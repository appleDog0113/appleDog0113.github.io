---
title: Lavarel - 後台管理系統
date: 2024-05-29 08:39:11
tags:
---

### 前言

課程進度會用到之前的內容
有空再補上

只是個人上課筆記，排版有跑掉有空再整理

<!-- more -->

#### 上半場：後台分離

`使用AdminLTE-3.2.0`

1. 從老師那邊下載zip檔
    > 網頁工程通常先開發後台管理再開發前台

2. /resources/views->新增"/admin"->新增app、home、menu.blade.php檔
     >每個後台的網址都要做檢查，使用middleware，後面會提這是什麼

3. 打開zip檔，複製index.html內容貼上app.blade
4. 剪下`sidebar_Menu`貼到menu.blade檔案裡
   > 將選單版面分離，也分離權限
   不同權限登入會看見不同選單功能

   + app    -> 後台layout
   + menu   -> 選單
   + home   -> 首頁
  <br>
5. 將 `Main content` 剪下貼到 `home.blade` 裡
6. 在原本sidebar的位置引入menu（需要路徑引入特定檔案內容）
   ```php
    <!-- Sidebar Menu -->
    @include("admin.menu")      // 引入檔案
    <!-- /.sidebar-menu -->
   ```
7. 在原本Main content的位置抓content內容（只要名稱能夠抓到就會引入，不用給路徑）
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
   > + 參考資料 [Laravel-yield的使用](https://medium.com/%E5%BF%AB%E6%A8%82%E5%AD%B8%E7%A8%8B%E5%BC%8F/laravel-yield%E7%9A%84%E4%BD%BF%E7%94%A8-5b9f85c01809)

   <br>


8. 前面有放css和js檔案的話要記得改`app.blade`的引入路徑

9. `home.blade`裡要繼承頁面
    在最前方加入
    ```php
    @extends("admin.app")   //繼承
    @section("content")
    <section>
        Main content貼上的內容
    </section>
    @endsection
    ```

10. 指令處輸入：php artisan make:controller Admin/AdminController

    ```php
        // 新增以下方法
        // 將home.blade的內容讓view顯示
        class AdminController extends Controller{
            public function home(){
                return view("admin.home");
            }
        }
    ```

11. 加入路由
    `/route/web`
    ```php
    Route::group(["prefix" => "admin"], function(){
        route::get("home",[AdminController::class, "home"]);
    });
    ```
12. 打開server到 `/admin/home` 確認網頁內容

---

#### 下半場：後台管理

1. 到menu.blade把不會用到的東西刪掉
    ex. ``` <li class="nav-item menu-open">```

2. widget改成 `注意事項`（要連結的網頁名稱，之前做的那個）
3. 指令處輸入：php artisan make:controller Admin/AdminNoticeController
    > 新增 AdminNoticeController

<br>

4. 新增方法

    ```php
    class AdminNoticeController extends Controller
    {   
        
        // 取得 "Notice" 資料表內容
        // 就是之前建的注意事項網頁
        public function list(){

            // get()與all() 取全部資料
            // 此處取 Notice 資料表
            $list = Notice::get();
            return view("admin.notice,list", compact("list"));

        }


        // 資料表輸入新增資料的內容
        public function add(){

            return view("admin.notice,add");

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

<br><br>

5. 新增/notice/list.blade.php
   <br>

    ```php

    //頁面新增以下內容

    @extends("admin.app")
    @section("content")
    <div class="row">
        <div class="cole-12">
            <table class="table table-border border border-dark">
                <thead>
                <th>
                    <td class="text-center">
                        <input type="checkbox" class="form-check-input" id="all">
                    </td>
                    <td class="text-left">注意事項</td>
                </th>
                </thead>
                <tbody>
                    @foreach($list as $data)
                    <tr>
                        <td>
                            <input type="checkbox" name="id[]" class="form-check-input" value="{{ $data->id }}">
                        </td>
                        <td>{{ $data->title }}</td>
                    </tr>
                    @endforeach
                </tbody>
            </table>
        </div>
    </div>
    @endsection
    ```
<br><br>

6. 新增路由（/route/web）


