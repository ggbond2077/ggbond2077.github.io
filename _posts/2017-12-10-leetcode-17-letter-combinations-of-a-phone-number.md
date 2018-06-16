---
layout: post
title: leetcode-17-letter-combinations-of-a-phone-number
categories: [题目] 
description: leetcode-17-letter-combinations-of-a-phone-number
keywords: algorithm
---


**给定一个数字字符串，找出这个字符串对应的字母串组合,这是一个简单题，练习一下linkedlist作为队列的用法**

*   题目链接： [https://leetcode.com/problems/letter-combinations-of-a-phone-number](https://leetcode.com/problems/letter-combinations-of-a-phone-number)

* * *

> Given a digit string, return all possible letter combinations that the number could represent.  
> A mapping of digit to letters (just like on the telephone buttons) is given below.  
> Input:Digit string "23" Output: \["ad", "ae", "af", "bd", "be", "bf", "cd", "ce", "cf"\] Note: Although the above answer is in lexicographical order, your answer could be in any order you want.

* * *

*   数字与字母的对应关系同电话键盘，2-->abc 3-->def...

保存对应关系，遍历数字串，在结果集中追加当前数字对应字母. 我怎么没想到用队列...

*  我的代码

```java
    class Solution {
        public List<String> letterCombinations(String digits) {
    
        List<String> res = new ArrayList<String>();
        if(digits==null || "".equals(digits))
            return res;
        String dic [][] = {
            {},
            {},
            {"a","b","c"},
            {"d","e","f"},
            {"g","h","i"},
            {"j","k","l"},
            {"m","n","o"},
            {"p","q","r","s"},
            {"t","u","v"},
            {"w","x","y","z"}
        };
        int len = digits.length();
        int t = (int)(digits.charAt(0)-'0');
    
        for(int i = 0; i < dic[t].length; i++){
            res.add(dic[t][i]);
        }
    
        for(int j = 1; j < len; j++){
            int n = (int)(digits.charAt(j)-'0');
            List<String> tempres = new ArrayList<String>();
            for(int i= 0; i < dic[n].length; i++){
            for(String s :res){
                tempres.add(s+dic[n][i]);
            }
            }
            res = tempres;
            tempres = null;
        }
        return res;
        }
    }
```

*   题解代码

```java
            public List<String> letterCombinations(String digits) {
            LinkedList<String> ans = new LinkedList<String>();
            String[] mapping = new String[] {"0", "1", "abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz"};
            ans.add("");
            for(int i =0; i<digits.length();i++){
                int x = Character.getNumericValue(digits.charAt(i));
                while(ans.peek().length()==i){
                    String t = ans.remove();
                    for(char s : mapping[x].toCharArray())
                        ans.add(t+s);
                }
            }
            return ans;
        }
```
