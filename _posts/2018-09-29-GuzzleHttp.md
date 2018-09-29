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

