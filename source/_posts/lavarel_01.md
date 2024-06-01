---
title: Lavarel - 後台管理系統 01.
date: 2024-05-29 08:39:11
tags: 
    - 職訓
    - Lavarel
categories: 
    - [職訓, Lavarel]
    - Lavarel
description:
---

### 前言

課程進度會用到之前的內容
有空再補上

> 代表個人筆記

<!-- more -->

#### 上半場：後台分離

`使用AdminLTE-3.2.0`

##### 1. 從老師那邊下載zip檔
> 網頁工程通常先開發後台管理再開發前台

##### 2. /resources/views->新增"/admin"->新增app、home、menu.blade.php檔
 >每個後台的網址都要做檢查，使用middleware，後面會提這是什麼

##### 3. 打開zip檔，複製index.html內容貼上app.blade
##### 4. 剪下`sidebar_Menu`貼到menu.blade檔案裡
> 將選單版面分離，也分離權限
不同權限登入會看見不同選單功能

+ app    -> 後台layout
+ menu   -> 選單
+ home   -> 首頁

##### 5. 將 `Main content` 剪下貼到 `home.blade` 裡
##### 6. 在原本sidebar的位置引入menu（需要路徑引入特定檔案內容）
```php
<!-- Sidebar Menu -->
@include("admin.menu")      // 引入檔案
<!-- /.sidebar-menu -->
```
##### 7. 在原本Main content的位置抓content內容（只要名稱能夠抓到就會引入，不用給路徑）
   
```php
<!-- Main content -->
@yield("content")
<!-- /.content -->
```

>目前這三個都是`網頁架構`，固定要顯示的網頁內容
如果頁面要抽換 `直接改home裡面的頁面顯示` 即可
所以`content`只會有一個在`home`裡（就是home的主架構），其他都是 `抽換其中內容` 而已
> + yield：動態內容
> + include：靜態內容
> + 參考資料 
[Laravel-yield的使用](https://medium.com/%E5%BF%AB%E6%A8%82%E5%AD%B8%E7%A8%8B%E5%BC%8F/laravel-yield%E7%9A%84%E4%BD%BF%E7%94%A8-5b9f85c01809)
[Laravel 入門：來認識 Blade 吧](https://simonallen.coderbridge.io/2020/09/24/laravel-blade-note/)

<br>


##### 8. 前面有放css和js檔案的話要記得改`app.blade`的引入路徑

##### 9. `home.blade`裡要繼承頁面
在最前方加入
```php
@extends("admin.app")   //繼承
@section("content")
<section>
    Main content貼上的內容
</section>
@endsection
```

##### 10. 指令處輸入：php artisan make:controller Admin/AdminController

```php
    // 新增以下方法
    // 將home.blade的內容讓view顯示
    class AdminController extends Controller{
        public function home(){
            return view("admin.home");
        }
    }
```

##### 11. 加入路由
路徑：`/route/web`
```php
Route::group(["prefix" => "admin"], function(){
    route::get("home",[AdminController::class, "home"]);
});
```
##### 12. 打開server到 `/admin/home` 確認網頁內容

---

#### 下半場：後台建置

##### 1. 到menu.blade把不會用到的東西刪掉
ex. ``` <li class="nav-item menu-open">```

##### 2. widget改成 `注意事項`（要連結的網頁名稱，之前做的那個）
##### 3. 指令處輸入：php artisan make:controller Admin/AdminNoticeController
> 新增 AdminNoticeController

<br>

##### 4. 新增方法

```php
class AdminNoticeController extends Controller
{   
    
    // 取得 "Notice" 資料表內容
    // 就是之前建的注意事項網頁
    public function list(){

        // get()與all() 取全部資料
        // 此處取 Notice 資料表
        $list = Notice::get();
        return view("admin.notice.list", compact("list"));

    }


    // 資料表輸入新增資料的內容
    public function add(){

        return view("admin.notice.add");

    }

    // 資料表新增資料
    public function insert(Request $req){

        $notice = new Notice();
        $notice->title = $req->title;
        $notice->save();

        Session::flash("message", "已新增");
        return redirect("/admin/notice/list");

    }

}
```
<br>

##### 5. 新增/notice/list.blade.php

```php

//頁面新增以下內容

@extends("admin.app")
@section("content")
<div class="container">
    <div class="row">
        <div class="cole-12">
            <table class="table table-border border border-dark">
                <thead>
                    <tr class="table table-warning">
                        <th class="col-1 text-center" style="vertical-align: middle;">
                            <input type="checkbox" class="form-check-input" id="all">
                        </th>
                        <th class="col-9 text-center">注意事項</th>
                        <th class="col-2text-center">修改</th>
                    </tr>
                </thead>
                <tbody>
                    @foreach($list as $data)
                    <tr>
                        <td class="col-1 text-center">
                            <input type="checkbox" name="id[]" class="form-check-input" value="{{ $data->id }}">
                        </td>
                        <td class="col-9 text-left">{{ $data->title }}</td>
                        <td class="col-9 text-left">
                            <a href="edit/{{ $data -> id }}" class="btn btn-success text-white">修改</a>
                        </td>
                    </tr>
                    @endforeach
                </tbody>
            </table>
        </div>
    </div>
</div>
@endsection
```
<br>

##### 7. 新增路由（/route/web）

```php

// 新增以下內容，連結notice功能

    Route::group(["prefix" => "admin/notice"], function(){
        Route::get("list",[AdminNoticeController::class, "list"]);
        Route::get("add",[AdminNoticeController::class, "add"]);
        Route::post("insert",[AdminNoticeController::class, "insert"]);
    });
```

> + 這時候到 server:8000/admin/notice/list 應該可以看到notice表格的內容
> + id 取名的時候要有唯一性，不可使用`id[]`陣列表示，但是可以``` {{ $data->id }}``` 抓內容作為id設定

<br>

##### 8. 到app.blade修改 Content Header (Page Header)

```php
    <h1 class="m-0">@yield("title")</h1>    // 本來應該是Dashbord
    </div><!-- /.col -->
    <div class="col-sm-6">
    <ol class="breadcrumb float-sm-right">
    <li class="breadcrumb-item"><a href="/admin/home">Home</a></li>     // 改連結
    <li class="breadcrumb-item active">@yield("title")</li>     // 忘了本來是啥，總之改掉
```

<br>

##### 9. list.blade 增加新增刪除按鈕

```php

        // 貼在div container->row->col-12 到<table>之前

        <div class="card">
            <div class="card-header">
                <div class="row">
                    <div class="col-1">
                        <a href="add" class="btn btn-primary">新增</a>
                    </div>
                    <div class="col-1">
                        <a href="javascript:doDelete()" class="btn btn-danger">刪除</a>
                    </div>
                </div>
            </div>
        </div>
```

<br>

##### 10. 新增 /admin/notice/add.blade.php 頁面
> 新增內容總要有`輸入`和`送出`的地方
就是這個頁面
!!! 這個版本沒有把 `bootstrap` 改成舊的 !!!
!!! 版型可能會不一樣 !!!

```php

// 排版可以自行調整，這版有點醜

@extends("admin.app")
@section("title","新增注意事項")
@section("content")
<div class="container">
    <div class="row">
        <div class="card col-12">
            <div class="card-header">
                <a href="list" class="btn btn-secondary">回上頁</a>
            </div>
        </div>
        <form method="post" action="insert">
            {{ csrf_field() }}
            <div class="row">
                <label class="col-form-lable col-3 text-right">標題</label>
                <div class="col-9">
                    <input type="text" class="form-control" name="title" require autofocus>
                </div>
            </div>
            <div class="card-body text-center mt-3">
                <button class="btn btn-primary" type="submit">確定</button>
            </div>
        </form>
    </div>
</div>
@endsection

```
<br>

##### 11. 已新增時跳出通知視窗
> 使用sweetalret2套件
> 有各種特效可以玩玩看

```php
// 於/admin/app.blade 內修改

<head>

    // 引入sweetalert2
    <link rel="stylesheet" href="/css/admin/sweetalert2.min.css">
    <script rel="stylesheet" src="/js/admin/sweetalert2.all.min.js"></script>

</head>
<body>

    // 新增迴圈, 使用sweetalert2

    @if(Session::has("message"))
        <script>
            swal.fire("{{ Session::get('message') }}");
        </script>
    @endif
</body>
```

<br>

##### 12. 於adminNoticeController 增加edit的方法

```php
public function edit(Request $req){

    $notice = Notice::find($req->id);

    return view("admin.notice.edit", compact("notice"));
}

public function update(Request $req){

    $notice = Notice::find($req->id);
    $notice->title = $req->title;
    $notice->save();    // 也可用 $notice->update();

    Session::flash("message", "已修改");
    return redirect("/admin/notice/list");
}
```
<br>

##### 13. 新增 /admin/notice/edit.blade 頁面
```php
@extends("admin.app")
@section("title","修改內容")
@section("content")
<div class="container">
    <div class="row">
        <div class="card col-12">
            <div class="card-header">
                <a href="../list" class="btn btn-secondary">回上頁</a>
            </div>
        </div>
        <form method="post" action="../update">
            <input type="hidden" name="id" value="{{ $notice->id }}">
            {{ csrf_field() }}
            <div class="row">
                <label class="col-form-lable col-3 text-right">標題</label>
                <div class="col-9">
                    <input type="text" class="form-control" name="title" value="{{ $notice->title }}" require autofocus>
                </div>
            </div>
            <div class="card-body text-center mt-3">
                <button class="btn btn-primary" type="submit">確定</button>
            </div>
        </form>
    </div>
</div>
@endsection
```