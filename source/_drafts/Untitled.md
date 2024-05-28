title: 'Hexo+GitHub 搭建紀錄'
author: appleDog
tags: []
categories: []
date: 2024-05-28 10:03:00
---


### 選擇Hexo搭配GitHub的原因

由於之前沒有碰過git、課程上也沒有接觸過（之後會不會有也不知道）
雖然看了一些教學，不過技術面的東西果然還是要實作比較有感覺
加上課程裡包含Linux基礎操作，想說藉此機會多多練習相關的操作與設定

<!-- more -->


打算建個人網誌的時候各方爬文，需求很簡單

- 免費
- 自訂性高
- 可以顯示程式碼
- 目前能力足以操作
- 盡量避開現成社群平台

Hexo 最大的好處是中文化資料夠多，出什麼問題都還能靠爬文想辦法解決；github中文教學也不少，兩者並用順便學習，在架站和解決問題的過程也的確學到許多。

那麼就開始吧！

<br>

---

### 1. 申請GitHub帳號+開新專案

申請帳號網路上很多教學就不贅述，拿到熱騰騰的帳號後可以先設定for Hexo的repository

參考資料：
[連結待修改](https://google.com "游標顯示")


以上設定都還在滑鼠點點點可以做到的範疇，接下來要打開Terminal囉！


### 2. 安裝git及Hexo

因為第一次安裝不太熟悉，為免不知道動到什麼連原本上課的專案都出問題，所以另外開了一台主機操作。

作業系統是Linux+ubuntu，先把ftp架起來方便檔案傳輸。

[ftp server架設筆記-待補](https://google.com "游標顯示")


打開Terminal，先到你要放網誌資料的路徑下

```php
$ mkdir [路徑名稱]
$ cd [剛才新增的路徑]
```
<br>
以我的話是放到/var/www/blog 路徑裡，記得要確認路徑擁有者是自己！

~~最開始沒注意到後續一直跳出權限不足的問題，很煩~~

<br><br>

### 安裝 git

在終端機裡輸入

```php
// 安裝 git
$ sudo apt install git
```
跑完後就可以用git的相關功能啦！
接著綁定自己的github帳號

```php
 // 的Github用戶名
git config --global user.name

// 註冊GitHub的電子信箱
git config --global user.email 
```

由於github在2021年`停止使用帳號密碼認證`的服務，改用`ssh-key`
所以我們要在前置的時候先把ssh-key設定好，後續才能將網誌上傳到github上


```php
// 註冊GitHub的電子信箱
ssh-keygen -t rsa -C 
```
之後沒什麼特別要額外設定的東西就一直按enter讓他跑就好。
跑完後系統會自動生成一個 `id_rsa.pub` 檔案，裡面放的就是ssh-key相關的東西
路徑在 `~/使用者名稱/.ssh`（Windows的應該在c:/）

打開後複製所有內容，到[這裡](https://github.com/settings/keys)去設定ssh-key
title 我是留白，將剛才複製的內容貼到key裡面後儲存就行了。

到這邊git相關的設定就先告一段落囉！

參考資料
[chunhung.yu@hackmd](https://hackmd.io/@king87515/Sy16ckymU)

<br>


### 安裝Node,js

根據官網介紹，Hexo是以`nodejs`下去開發的靜態網頁建立工具，所以接下來就是安裝Node.js

首先進到Nodejs官網，**`不要按首頁的下載`**

請到上面的Download裡根據自己的作業系統需求安裝，
以我為例是選擇Linux+nvm，之後官方會顯示指令碼，照著貼上執行就好。

不過因為這是一台清潔溜溜什麼都沒有的主機，所以還要先安裝 Curl 組件，不然會無法執行。

```php
// 安裝 curl
$ sudo apt install curl
```
之後跟著官方指令安裝Nodejs，過程中會連同npm一起安裝
所以之後我們可以直接使用npm的相關指令。

> npm：搭配Node.js使用的套件管理工具，之後安裝套件需要用到

到這邊應該都安裝好了，可以用 `-v` 指令確認自己node、npm和git的版本。
有跑出版本號就是成功囉。

<br><br>

### 安裝 Hexo

<br>
同樣根據官網給的指令安裝

```php
// 安裝hexo-cli
$ npm install -g hexo-cli

// 初始化資料夾，這裡都是在/blog下執行故沒有指定路徑
// 如果需要的話可以在後面指定資料夾 ex. hexo init <資料夾名稱>
$ hexo init

// 安裝git部屬套件; 上傳到github會用到
$ npm install hexo-deployer-git
```
<br>
  
安裝完畢後，應該可以看到資料夾內多了很多東西。
貼官網的資料示意：
```php
.
├── _config.yml
├── package.json
├── scaffolds
├── source
|   ├── _drafts
|   └── _posts
└── themes
```

<br><br>

### 設定網頁配置
<br>
安裝完之後還有一些小設定要處理