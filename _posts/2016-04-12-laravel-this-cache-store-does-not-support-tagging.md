---
layout: post
title: "Laravel - This Cache Store Does Not Support Tagging"
categories: code php laravel
---

Oops! Long story short, that's one of the really common errors during our development in Laravel. Accroding to the documentation [here](https://laravel.com/docs/5.1/cache#cache-tags):

> Note: Cache tags are not supported when using the file or database cache drivers. Furthermore, when using multiple tags with caches that are stored "forever", performance will be best with a driver such as memcached, which automatically purges stale records.

Just to change our cache driver from `CACHE_DRIVER=file` in `.env` to `CACHE_DRIVER=array`.

Save it and refresh the page, it works again.
