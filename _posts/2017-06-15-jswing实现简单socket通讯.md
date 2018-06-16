---
layout: post
title: jswing实现简单socket通讯
categories: [技术] 
description: jswing实现简单socket通讯
keywords: java
---


**实现功能：**
---------

1.  登录   2.注册   3.文本和表情发送   4看在线好友及好友上线下线通知

 

图：

![youchat1](https://www.ggbond.cc/wp-content/uploads/2017/06/char01.png)                                             ![youchat2](https://www.ggbond.cc/wp-content/uploads/2017/06/chat02.png)

![youchat3](https://www.ggbond.cc/wp-content/uploads/2017/06/chat03.png)

一、登录注册

    这部分比较简单，就插入数据和查询密码.  数据库用的mysql5.6  建了个简单的用户表users 只有username password两个字段

二、文本发送 客户端连接服务器

     public void connectToSever() {
            try {
                Socket sock = new Socket("127.0.0.1", 4242);
                out = new ObjectOutputStream(sock.getOutputStream());
                in = new ObjectInputStream(sock.getInputStream());
                Thread remote = new Thread(new RemoteReader());
                remote.start();
                sendUauslMessage(1, userName, "online");  //发送上线消息
            } catch (Exception ex) {
                System.out.println("连接不上服务器，你可以一个人玩");
            }
        }
    

客户端接收消息线程

       public class RemoteReader implements Runnable {
            Object obj = null;
            public void run() {
                try {
                    while ((obj = in.readObject()) != null) {
                        String MessageReceiv = (String) obj;
                        Client.this.handleReceivMessage(MessageReceiv);  //处理接收到的消息
                        isSend = false; // 判断是自己发送还是来自服务器的消息，为了填消息颜色显示的坑
                    }
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
        }
    

  首先，每当客户端发起一个连接，服务器将连接相关的信息（这里保存的输出流对象，也可保存每一个连接的soket对象）保存到一个list内，便于后面使用   

        public void go() {
            clientOutputStreams = new ArrayList();  //用于保存连接的输出流对象
            clients = new ArrayList<String>();      //保存连接的客户端用户名信息，方便反馈在线人数
            try {
                ServerSocket serverSock = new ServerSocket(4242);
                while (true) {
                    Socket clientSocket = serverSock.accept();  
                    ObjectOutputStream out = new ObjectOutputStream(
                    clientSocket.getOutputStream());
                    clientOutputStreams.add(out);
                    
                    out.writeObject(getOnlineString());    //这个是反馈在线信息
                    Thread t = new Thread(new ClientHandler(clientSocket));
                    t.start();
                    System.out.println("got a connection");
                }
            } catch (Exception ex) {
                ex.printStackTrace();
            }
    

客户端将字符串对象通过输出流将数据交给socket发送到服务器，服务器接收消息 调用tellEveryone 将收到的对象用过保存的输出流对象 发送给所有在线的客户，就实现了群聊消息。

        public void tellEveryone(Object one) {
            Iterator it = clientOutputStreams.iterator();
            while (it.hasNext()) {
                try {
                    ObjectOutputStream out = (ObjectOutputStream) it.next();
                    out.writeObject(one);
                } catch (Exception ex) {
                    ex.printStackTrace();
                }
            }
        }
    

三、表情处理

发送表情也是发送字符串，在客户端通过一定规则将表情转换成字符串发送出去， 收到来自服务器的消息后，根据规则还原成表情即可这里我是参照这篇博客做的 [http://blog.csdn.net/yangxingzou/article/details/6888222](http://blog.csdn.net/yangxingzou/article/details/6888222) 先继承ImageIcon 写一个ChatPic类给每个表情图标添加一个id属性。 再创建一个PicInfo类，一个PicInfo对象包含一个id属性和一个表情相对于文本的位置信息接下来就是定义规则将图片转化成字符串发送出去。 假设现在要发送这样一条消息：        这是一条包含文本和![](http://www.ctguggbond.com/wp-content/plugins/kindeditor-for-wordpress/plugins/emoticons/images/0.gif)表情的![](http://www.ctguggbond.com/wp-content/plugins/kindeditor-for-wordpress/plugins/emoticons/images/15.gif)消息 假设两个表情id分别为 01，02 对应图片保存的文件名， 在这里pos位置为 9和13（0开始向后数）。 转化后的字符串为:  这是一条包含文本和表情的消息*01&9+02&13 *号分隔不同的消息，+号分隔不同的表情，这样客户端收到消息之后，用StringTokennizer分解，取出还原相应信息即可。    客户端发送消息：

    //这里前面新增了个flag标记信息，0表示发送消息 1表示上线通知 2表示下线通知 3 表示为服务器发送的更新在线好友列表通知 
     public boolean sendUauslMessage(int flag, String userName, String message) {
            try {
                out.writeObject(flag + "*" + userName + "*" + message);                                 
             } catch (Exception ex) {                                          
    
                ex.printStackTrace();
                return false;
            }
            return true;
        }
    

客户端处理接收到的消息：

    public void handleReceivMessage(String MessageReceiv) {
                if (isSend)   //如果是自己发送的消息，就直接将输入框信息放到显示框即可，不用处理来自服务器端的消息
                return;
            String[] strs = MessageReceiv.split("[*]");
            int msgType; // msgType 0 表示消息 1 表示上线 2表示下线 3
            if (strs.length >= 3) {
                msgType = Integer.parseInt(strs[0]);
            } else {
                return;
            }
            String uname = strs[1];
            String message = strs[2];
            if (strs.length > 3) {
                for (int i = 3; i < strs.length; i++) {
                    message = message + "*" + strs[i];
                }
            }
            if (msgType == 0)
                addRecMsg(uname, message);
            else if (msgType == 1) {
                if (uname.equals(userName))
                    return;
                infolabel.setText(uname + "上线了！！！");
                listModel.addElement(uname);
            } else if (msgType == 2) {
                infolabel.setText(uname + "下线了！！！");
                listVector.remove(uname);
                listModel.removeElement(uname);
            } else if (msgType == 3) {
                for (int i = 2; i < strs.length; i++) {
                    listModel.addElement(strs[i]);
                }
            }
        }
    

四、好友上线下线  

客户端连接后会立即调用上面的sendUauslMessage方法发送一条1标记的信息（包含了用户名）给服务器，服务器端将这个用户添加到一个List的对象clients内 当一个客户端关闭窗口时则会发送一个2标记的信息给服务器，服务器就从clients中remove这个客户  当以上两个操作之后都会再次将1，2标记的信息转发给所有在线客户端通知某某上线。 每当有一个客户连接上服务器的同时，服务器将当前List的对象clients内保存的所有在线用户转换成3标记的字符串（转换规则类似表情转换以*号分隔每个用户名） 调用上面的tellEveryone发送给所有客户，让客户端刷新在线好友列表

服务器处理来自客户端的消息：

            private void handleMessage(Object o1) {
                // TODO Auto-generated method stub
                String s = (String) o1;
                String[] strs = s.split("[*]");
                int msgType;
                if (strs.length >= 3) {
                    msgType = Integer.parseInt(strs[0]);
                } else {
                    return;
                }
                String uname = strs[1];
    
                String message = strs[2];
    
                    if (msgType == 1) {
                    clients.add(uname);
                } else if (msgType == 2) {
                    clients.remove(uname);
                }
            }
        }
    

以上. 源码：[https://github.com/ctguggbond/youchat](https://github.com/ctguggbond/youchat)

.............................
