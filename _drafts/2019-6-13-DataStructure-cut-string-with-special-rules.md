---
layout: post
title: leetcode10|未通过|正则表达式
categories: [leetcode]
---

* 这段记录是在做leetcode正则表达是截取字符串时遇到的问题
{%highlight ruby%}


	/*如这一段代码就是按照相应的规则裁剪字符串，并将裁剪后的字符串信息放入到新的容器中
	 *例如输入的字符串是"aab\*c\*d\*e\*b\*cdf\*e"
	 *aa是不可变的字母段:所以char = a int = 2 bool = 0//定长
	 *b后面跟的是\*所以是变长的字母段，最小长度为0：所以char = b int = 0 bool = 1//边长
	 *
	 *
	 *
	 */
	
	//最初始代码：代码对现在访问的字符的下一个进行与判断，复杂，而且这段代码还是没完成的。

	vector<pair<pair<char, int>, bool>>p_split2_three;
	int i = 0;
	p_split2_three.push_back(make_pair(make_pair(p[0], 1), 0));
	i = 1;
	for (i; i < p.length(); i++) {
		
		if (p[i] == start && p[i+1] != '*') {
			count++;
			if (i+1 == p.length()-1) {
				p_split2_three.push_back(make_pair(make_pair(start, count), 0));
			}
		}
		else if(p[i] != start && p[i+1] != '*'){
			p_split2_three.push_back(make_pair(make_pair(p[i],count),0));
			start = p[i];
			count = 0;



		}
		else if (p[i] == start && p[i+1] == '*') {
			if(count>0)
				p_split2_three.push_back(make_pair(make_pair(p[i], count), 0));
			p_split2_three.push_back(make_pair(make_pair(p[i], 0), 1));
			if (i + 2 < p_length) {
				start = p[i + 2];
				i += 1;
			}
			count = 0;
		
		}
		else if (p[i] != start && p[i+1] == '*') {
			if(count>0)
				p_split2_three.push_back(make_pair(make_pair(start, count), 0));
			p_split2_three.push_back(make_pair(make_pair(p[i], 0), 1));
			if (i + 2 < p_length) {
				start = p[i + 2];
				i += 1;
			}
			count = 0;
		
		}
	}
	//新代码：对输入的字符一共分为三类，每进入到一类的时候进行对应的处理。核心是从当前的字母出发，处理当前字母和前面的字母。而不是去预判当前字母的下一个，这样增加了判断的复杂性。

	int i = 0;
	p_split2_three.push_back(make_pair(make_pair(p[0], 1), 0));
	i = 1;
	for (i; i < p.length(); i++) {
		if (p[i] == p_split2_three.back().first.first) {
			//如果是重复的字母
			p_split2_three.back().first.second++;
		}
		else if (p[i] == '*') {
			if (!p_split2_three.back().second) {
				//遇到新的字母将前一个结构体变成可变长的字母序列
				p_split2_three.back().second = 1;
				p_split2_three.back().first.second--;//如果是aa*second就是1若是ba*形式second就是0
			}
		}
		else {
				//如果两个字母不相等，新建一个对象
				p_split2_three.push_back(make_pair(make_pair(p[i], 1), 0));
			}
	}


{%endhighlight%}