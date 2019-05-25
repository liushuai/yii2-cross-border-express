Cross Border Express for Yii2
================
跨境物流接口，目前只支持云途物流和三态速递

[![Latest Stable Version](https://poser.pugx.org/yiier/yii2-cross-border-express/v/stable)](https://packagist.org/packages/yiier/yii2-cross-border-express) 
[![Total Downloads](https://poser.pugx.org/yiier/yii2-cross-border-express/downloads)](https://packagist.org/packages/yiier/yii2-cross-border-express) 
[![Latest Unstable Version](https://poser.pugx.org/yiier/yii2-cross-border-express/v/unstable)](https://packagist.org/packages/yiier/yii2-cross-border-express) 
[![License](https://poser.pugx.org/yiier/yii2-cross-border-express/license)](https://packagist.org/packages/yiier/yii2-cross-border-express)


Installation
------------

The preferred way to install this extension is through [composer](http://getcomposer.org/download/).

Either run

```
php composer.phar require --prefer-dist yiier/yii2-cross-border-express "*"
```

or add

```
"yiier/yii2-cross-border-express": "*"
```

to the require section of your `composer.json` file.


Usage
-----


### 配置

```php
<?php

$config = [
    // HTTP 请求的超时时间（秒）
    'timeout' => 5.0,

    // 可用的平台配置
    'platforms' => [
       'santai' => [
            'appKey' => 'xxx',
            'token' => 'xxx',
            'userId' => 'xxx'
        ],
        'yuntu' => [
            'account' => 'xxx',
            'secret' => 'xxx',
        ],
    ],
];

$express = new \yiier\crossBorderExpress\Express($config, 'santai');
    
```


### 发货

```php
<?php
$expressOrder = new \yiier\crossBorderExpress\contracts\Order();
$expressOrder->customerOrderNo = 'xxx';
$expressOrder->transportCode = 'xxx';

$goods[] = new \yiier\crossBorderExpress\contracts\Goods();
$goods->description = 'xxx';
$goods->cnDescription = 'xxx';
$goods->quantity = 'xxx';
$goods->weight = 'xxx';
$goods->hsCode = 'xxx';
$goods->enMaterial = 'cotton';
$goods->cnMaterial = '棉';
$goods->worth = 1; // 1美元;
        
$expressOrder->goods = $goods;
$expressOrder->evaluate = 1; // 1美元
$expressOrder->taxesNumber = 'xxx'; // 税号
$expressOrder->isRemoteConfirm = 1; // 是否同意收偏远费
$expressOrder->isReturn = 1; // 是否退件
$expressOrder->withBattery = 0; // 是否带电池

$package = new \yiier\crossBorderExpress\contracts\Package();
$package->description = 'xxxx';
$package->quantity = 1;
$package->weight = 'xxx';
$package->declareWorth = 1; // 1美元
$expressOrder->package = $package;


$recipient = new \yiier\crossBorderExpress\contracts\Recipient();
$recipient->countryCode = 'xx';
$recipient->name = 'xx';
$recipient->address = 'xx';
$recipient->city = 'xx';
$recipient->state = 'xx';
$recipient->zip = 'xx';
$recipient->phone = 'xx';
$expressOrder->recipient = $recipient;

$shipper = new \yiier\crossBorderExpress\contracts\Shipper();
$shipper->countryCode = 'CN';
$shipper->name = 'xxx';
$shipper->company = 'xxx';
$shipper->address = 'xxx';
$shipper->city = 'xxx';
$shipper->state = 'xxx';
$shipper->zip = 'xx';
$shipper->phone = 'xxx';
$expressOrder->shipper = $shipper;
    
// 发货
$express->createOrder($expressOrder);
```


### 获取物流方式

```php
<?php
$countryCode = 'US';
$item = [];
if (!empty($transports = $express->getTransportsByCountryCode($countryCode))) {
    foreach ($transports as $transport) {
        // 带跟踪号的物流方式
        $item[$transport->code] = [
            'code' => $transport->code,
            'name' => $transport->cnName,
            'is_tracking_no' => (int)$transport->ifTracking,
            'data' => $transport->data,
        ];
    }
}
return $item;
```


### 获取打印地址

```php
<?php
$orderNumber = 'xxx';
$express->getPrintUrl($orderNumber);
```
