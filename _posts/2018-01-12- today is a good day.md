---
layout:     post
title:      爬虫日记2-下载图片
subtitle:  
date:       2018-01-12
author:     Jasmine
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - python
---


上一篇文章，我有了自己的第一条爬虫。就像打怪升级一样，爬虫也会慢慢地成长，学到新本领。今天，我的爬虫就学会了爬取“美图”。这个功能也很有用，可以爬取网上的壁纸，美女图片，正太，帅哥偶像，都是妥妥的。好啦，开始我们的“爬图”之旅吧。

动手写代码之前，我觉得还是要先理一下思路，而不是一开始就噼里啪啦写代码，这是一大禁忌。
##### 目标：爬取网上的图片
* 首先，我们要知道去哪里找到图片，也就是URL是什么。
具体来说，今天我就想爬取一下国家地理中文网上的图片。所以我的爬虫开始的起点是“www.nationalgeographic.com.cn”。可以使用requests库打开网页,并下载网页内容：
```
import requests
URL = "http://www.nationalgeographic.com.cn/index.php?m=content&c=index&a=lists&catid=596"
html = requests.get(URL).text
```

* 解析网页内容
这里我们又要利用这个神奇的工具BeautifulSoup了。它可以把下载下来的网页做成一碗美味的"汤汁"：
 ```
soup = BeautifulSoup(html,'lxml')
```
可是虽然我们把网页做成汤汁了，但是我们要找的图片在这碗汤的哪里呢？为了快速找到我们的照片，我们可以打开浏览器，查看网页的源码，就像这样：
![屏幕快照](http://upload-images.jianshu.io/upload_images/2730963-a23f4e68d400c8cf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![屏幕快照.png](http://upload-images.jianshu.io/upload_images/2730963-a331a4a527a174e2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)具体来说，下载图片的链接在这个标签里：![屏幕快照.png](http://upload-images.jianshu.io/upload_images/2730963-3c90f4ef27dd2181.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)是在<dl class = "show-list-dl aside-box">这一标签下的img标签里。知道大概的位置，我们就可以用正则表达式和BeautifulSoup来找到这个链接：
```
img_ul = soup.find_all('dl',{'class':'show-list-dl aside-box'})
for ul in img_ul:
    imgs = ul.find_all('img',{'src':re.compile("^http://")})
    for img in imgs:
        url = img['src']
```
* 下载图片
找到url以后，就可以利用request的get方法下载图片了，我们这里采用了分块下载的方法，也就是说，下载一部分内容，保存一部分内容：
```
       r = requests.get(url, stream=True)
        image_name = url.split('/')[-1]
        with open('./img/%s' % image_name, 'wb') as f:
            for chunk in r.iter_content(chunk_size=128):
                f.write(chunk)
        print('Saved %s' % image_name)
```
然后运行就可以得到图片了：
![屏幕快照 .png](http://upload-images.jianshu.io/upload_images/2730963-a47c9a0de659be83.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)![屏幕快照 .png](http://upload-images.jianshu.io/upload_images/2730963-57314726b92eaba1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

希望大家都可以爬到好看的图～完整代码见[我的github](https://github.com/GreenGitHuber/Web/blob/master/crawler/ex_down_image.py)咯 :D




