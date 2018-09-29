---
layout: post
title: "GuzzleHttp"
date: 2018-09-29 10:25:06 +0800
comments: true
category: php
tag: [php]
---

```php
use GuzzleHttp\Promise\Promise;

$promise = new Promise();
$promise->then(
    // $onFulfilled
    function ($value) {
        echo 'The promise was fulfilled.';
    },
    // $onRejected
    function ($reason) {
        echo 'The promise was rejected.';
    }
);
```

```php
use GuzzleHttp\Promise\Promise;

$promise = new Promise();
$promise
    ->then(function ($value) {
        // Return a value and don't break the chain
        return "Hello, " . $value; //创建一个新的promise实例，并且为之注册下一个then中的成功和失败的回调函数
    })
    // This then is executed after the first then and receives the value
    // returned from the first then.
    ->then(function ($value) {
        echo $value;
    });

// Resolving the promise triggers the $onFulfilled callbacks and outputs
// "Hello, reader".
$promise->resolve('reader.');
```

```php
$promise = new Promise();
$nextPromise = new Promise();

$promise
    ->then(function ($value) use ($nextPromise) {
        echo $value;
        return $nextPromise;
    })
    ->then(function ($value) {
        echo $value;
    });

// Triggers the first callback and outputs "A"
$promise->resolve('A');
// Triggers the second callback and outputs "B"
$nextPromise->resolve('B');
```