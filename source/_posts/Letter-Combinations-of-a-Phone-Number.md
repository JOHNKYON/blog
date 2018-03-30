---
title: Leetcode 17.Letter Combinations of a Phone Number
date: 2018-03-30 23:17:13
categories:
- Leetcode
tags:
- Leetcode
- Algorithm
- Coding

---

Given a digit string, return all possible letter combinations that the number could represent.

A mapping of digit to letters (just like on the telephone buttons) is given below.
![](/images/leetcode17_1.png)

    Input:Digit string "23"
    Output: ["ad", "ae", "af", "bd", "be", "bf", "cd", "ce", "cf"].

**Note:**
Although the above answer is in lexicographical order, your answer could be in any order you want.

*java* 

    public List<String> letterCombinations(String digits) {
        LinkedList<String> ans = new LinkedList<>();
        if (0 == digits.length()) {
            return ans;
        }
        String[] mapping = new String[]{"0", "1", "abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz"};
        ans.add("");
        for (int i = 0; i < digits.length(); i++) {
            int x = Character.getNumericValue(digits.charAt(i));
            while (ans.peek().length() == i) {
                String temp = ans.remove();
                for (Character c : mapping[x].toCharArray()) {
                    ans.add(temp + c);
                }
            }
        }
        return ans;
    }

This alogirthm uses a queque to make sure every new character was added to the previous string, shown as below.

![](/images/leetcode17_2.jpg)