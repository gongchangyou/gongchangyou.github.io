---
layout: post
title: "c++链表翻转"
date: 2015-07-31 22:25:06 +0800
comments: true
category: c++
tag: [c++]
---


{%  highlight c++ %}
template<typename _RandomAccessIterator>
void __reverse(_RandomAccessIterator __first, _RandomAccessIterator __last, random_access_iterator_tag)
{
	if (__first == __last)
		return;
	-–__last;
	while (__first < __last)
	{
		std::iter_swap(__first, __last);
		++__first;
		--__last;
	}
}
{% endhighlight %}

```
 ListNode* ReverseList(ListNode* pHead) {
        if(pHead==nullptr)
            return nullptr;
        ListNode* temp=nullptr;
        ListNode* cur=pHead;
        ListNode* node=nullptr;
        while(cur!=nullptr)
        {
            node=cur->next;
            cur->next=temp;
            temp=cur;
            cur=node; 
        }
        return temp;
    }

```