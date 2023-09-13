---
layout: post
title: "iterator-remove"
date: 2023-09-13 10:25:06 +0800
comments: true
category: java
tag: [java]


---

# iterator-remove

在HashMap类中， 不管是 KeyIterator ValueIterator还是 EntryIterator 都是 extends HashIterator

所以remove方法都可以将其从map中删除。

```
package com.mouse.spel_demo;

import lombok.extern.slf4j.Slf4j;
import org.junit.jupiter.api.Test;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.expression.Expression;
import org.springframework.expression.spel.standard.SpelExpressionParser;
import org.springframework.expression.spel.support.StandardEvaluationContext;

import java.util.ArrayList;
import java.util.HashMap;
import java.util.Iterator;
import java.util.List;
import java.util.Map;

@Slf4j
@SpringBootTest
class MapIteratorTests {

    @Test
    void remove() {
        Map<String, Integer> map = new HashMap<>(){{
            put("a", 1);
            put("b", 2);
        }};
        Iterator<Map.Entry<String,Integer>> it = map.entrySet().iterator();
        while(it.hasNext()) {
            if (it.next().getKey().equals("a")) {
                it.remove();
            }
        }
        System.out.println(map);
    }


    @Test
    void removeValue() {
        Map<String, Integer> map = new HashMap<>(){{
            put("a", 1);
            put("b", 2);
        }};
        Iterator<Integer> it = map.values().iterator();
        while(it.hasNext()) {
            if (it.next().equals(1)) {
                it.remove();
            }
        }
        System.out.println(map);
    }
}

```





