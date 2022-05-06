---
layout: post
title: "c++ 字符串替换"
date: 2015-06-26 22:25:06 +0800
comments: true
category: c++
tag: [c++]
---

{%  highlight c++ %}
#include<string>
#include<iostream>
using namespace std;
//第一种替换字符串的方法用replace()
void string_replace(string&s1,const string&s2,const string&s3)
{
	string::size_type pos=0;
	string::size_type a=s2.size();
	string::size_type b=s3.size();
	while((pos=s1.find(s2,pos))!=string::npos)
	{
		s1.replace(pos,a,s3);
		pos+=b;
	}
}
//第二种替换字符串的方法用erase()和insert()
void string_replace_2(string&s1,const string&s2,const string&s3)
{
	string::size_type pos=0;
	string::size_type a=s2.size();
	string::size_type b=s3.size();
	while((pos=s1.find(s2,pos))!=string::npos)
	{
		s1.erase(pos,a);
		s1.insert(pos,s3);
		pos+=b;
	}
}
{% endhighlight %}