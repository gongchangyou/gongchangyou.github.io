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


关于wait
```php
class Promise {
	public function __construct(
        callable $waitFn = null,
        callable $cancelFn = null
    ) {
        $this->waitFn = $waitFn;
        $this->cancelFn = $cancelFn;
    }
}
```
```php
$promise = new Promise(function () use (&$promise) {
    $promise->resolve('foo');
});

// Calling wait will return the value of the promise.
echo $promise->wait(); // outputs "foo"
```

```php
$promise = new Promise(function () use (&$promise) {
    throw new \Exception('foo');
});

$promise->wait(); // throws the exception.
```

```php
$promise = new Promise(function () { die('this is not called!'); });
$promise->resolve('foo');
echo $promise->wait(); // outputs "foo"
```
```php
$promise = new Promise();
$promise->reject('foo');
$promise->wait();//throws the exception.
```


一个异步请求的例子
```php
use GuzzleHttp\Client;
use GuzzleHttp\Pool;
use GuzzleHttp\Promise\Promise;
$client = new Client();


$request = function ($total) {
    $uri = 'https://shanghai.anjuke.com/community/view/';
    for ($i=0; $i<$total; $i++) {
        yield new \GuzzleHttp\Psr7\Request('Get', $uri . $i);
    }
};

$pool = new Pool($client, $request(10), [
        'concurrency' => 5,
        'fulfilled' => function ($response, $index) {
            echo 'fulfilled ' . $index . "\n";
        },
        'rejected' => function ($reason, $index) {
            echo 'rejected ' . $index . "\n";
        },
    ]
);

$promise = $pool->promise();

$promise->wait();
```

![client]({{ site.baseurl}}/images/201809/client.png)

![pool]({{ site.baseurl}}/images/201809/pool.png)

![类图]({{ site.baseurl}}/images/201809/GuzzleHttp.png)

![pool->promise()时序图]({{ site.baseurl}}/images/201809/pool-promise.png)



wait时序图 

![wait时序图]({{ site.baseurl}}/images/201809/wait.png)

在FulfilledPromise和 RejectPromise中 then方法都会将成功或者失败回调加入到queue中，等待下一次run的时候执行这些task



疑问? 
![question]({{ site.baseurl}}/images/201809/promise_question.png)

![question]({{ site.baseurl}}/images/201809/addPending.png)
![question]({{ site.baseurl}}/images/201809/advanceIterator.png)
![question]({{ site.baseurl}}/images/201809/next.png)

最终会执行 CurlMultiHandler 的 addRequest 方法 将新的request加入到 _mh中


