Laravel RESTful API
=================
### 基本環境部屬
* create project
```
composer create-project laravel/laravel:^7 --prefer-dist [profect-name]
```

* careate database
```
CREATE DATABASE api_restful
```

* 修改`.env`DB設定
> 修改後再下`php artisan serve`否則會報錯

* 新增Model
```
php artisan make:model Member -m   #-m會自動產生migration
```

* 修改members migration
```
public function up()
    {
        Schema::create('members', function (Blueprint $table) {
            $table->increments('id');
            $table->string('name');
            $table->string('language');
            $table->timestamps();
        });
    }
```
migrate:
```
php artisan migrate
```

* 產生controller
```
php artisan make:controller APIController
```


### 修改路由與controller  
`routes/api.php`
```
Route::get('/member', 'APIController@getAllMembers');
Route::get('/member/{id}', 'APIController@getMember');
Route::post('/member', 'APIController@createMember');
Route::put('/member/{id}', 'APIController@updateMember');
Route::delete('/member/{id}', 'APIController@deleteMember');
```

`APIController.php`
```
public function getAllMembers()
{
    $members = Member::get()->toJson(JSON_PRETTY_PRINT);
    return response($members, 200);
}

public function getMember($id)
{
	$member = Member::where('id', $id)->first();
    if($member === null){
        return response([
            'message'=> 'member not found'
        ], 404);
    }
    
    return response($member, 200);
}

public function createMember(Request $request)
{
	$member = new Member();
    $member->name = $request->name;
    $member->language = $request->language;
    $member->save();

    return response()->json([
        'message'=> 'create member successfully'
    ], 201);
}

public function updateMember(Request $request, $id)
{
	$member = Member::where('id', $id)->first();
    if($member === null){
        return response([
            'message'=> 'member not found'
        ], 404);
    }

    $member->name = $request->name ?? $member->name;
    $member->language = $request->language ?? $member->language;
    $member->save();

    return response([
        'message'=> 'update member successfully'
    ], 200);
}

public function deleteMember($id)
{
	$member = Member::where('id', $id)->first();
    if($member === null){
        return response([
            'message'=> 'member not found'
        ], 404);
    }

    $member->delete();
    return response([
        'message'=> 'member deleted'
    ], 202);
}
```

> 注意測試put method時參數帶json格式`Content-Type: application/json`

Reference:
-----------------
這篇寫得相當完整而且簡單易懂:  
https://www.twilio.com/blog/building-and-consuming-a-restful-api-in-laravel-php
