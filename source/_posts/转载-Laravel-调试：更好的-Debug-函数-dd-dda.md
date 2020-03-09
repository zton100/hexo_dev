---
title: 'Laravel调试:更好的 Debug 函数 dd => dda'
categories: 笔记
tags:
  - laravel
  - php
top_img: /img/thumb-1920-712781.jpg
abbrlink: 142aefee
date: 2020-03-08 16:24:57
cover: 'https://ss1.bdstatic.com/70cFuXSh_Q1YnxGkpoWK1HF6hhy/it/u=3979453105,1717514132&fm=26&gp=0.jpg'
---

# Laravel 调试：更好的 Debug 函数 dd => dda

`dd` 函数用来看变量类型很有帮助，但是有时候我们要查看 Eloquent 数据时，输出是这样的：

```php
Book {#399 ▼
  #fillable: array:10 [▶]
  #connection: null
  #table: null
  #primaryKey: "id"
  #keyType: "int"
  #perPage: 15
  +incrementing: true
  +timestamps: true
  #attributes: array:13 [▶]
  #original: array:13 [▶]
  #relations: []
  #hidden: []
  #visible: []
  #appends: []
  #guarded: array:1 [▶]
  #dates: []
  #dateFormat: null
  #casts: []
  #touches: []
  #observables: []
  #with: []
  +exists: true
  +wasRecentlyCreated: false
}
```

使用以下函数 `dda` ：

```php
function dda($model)
{
    if (method_exists($model, 'toArray')) {
        dd($model->toArray());
    } else {
        dd($model);
    }
}
```

就能得到我们想要的：

```php
array:13 [▼
  "id" => 1
  "name" => "name"
  "user_id" => 1
  "created_at" => "2017-04-18 06:48:42"
  "updated_at" => "2017-04-18 06:48:42"
]
```

> `dda` 等于： dd to array



> 本文为转载至[Laravelchina](https://learnku.com/laravel)的Wiki 文章，原文地址: https://learnku.com/laravel/wikis/15900