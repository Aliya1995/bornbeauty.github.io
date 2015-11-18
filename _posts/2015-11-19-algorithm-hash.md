---
layout: post
title: 数组中只出现一次的数字
category: 技术
tags: 算法
description: 一类问题

---

> 在牛客上看到了很多这种题目 今天总结一下吧

[题目](http://www.nowcoder.com/practice/e02fdb54d7524710a7d664d082bb7811?rp=2&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)很简单:

>题目描述
一个整型数组里除了两个数字之外，其他的数字都出现了两次。请写程序找出这两个只出现一次的数字。
>
时间限制：1秒空间限制：32768K
通过比例：21.19%
最佳记录：0 ms|8552K

	//num1,num2分别为长度为1的数组。传出参数
	//将num1[0],num2[0]设置为返回结果
	import java.util.HashMap;
	import java.util.Map;
	public class Solution {
		public void FindNumsAppearOnce(int[] array, int num1[], int num2[]) {
			if (array.length == 0 || array.length == 1) {
				num2[0] = num1[0] = 0;
			}
			HashMap<Integer, Integer> map = new HashMap<>();
			for (int i = 0; i < array.length; i++) {
				if (map.containsKey(Integer.valueOf(array[i]))) {
					map.put(Integer.valueOf(array[i]), new Integer(2));
				} else {
					map.put(Integer.valueOf(array[i]), new Integer(1));
				}
			}
			boolean isOne = true;
			for (Map.Entry<Integer, Integer> m : map.entrySet()) {
				if (m.getValue().intValue() == 1) {
					if (isOne) {
						num1[0] = m.getKey().intValue();
						isOne = false;
					} else {
						num2[0] = m.getKey().intValue();
					}
				}
			}
		}
	}

