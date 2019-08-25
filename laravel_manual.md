# Laravel project

```
composer create-project --prefer-dist laravel/laravel awesomeblog_app "5.7.*"
```

# Authentication

```
php artisan make:auth
```

# Mysql setting(docker)

`.env`

```
DB_CONNECTION=mysql
DB_HOST=mysql
DB_PORT=3306
DB_DATABASE=awesomeblog
DB_USERNAME=root
DB_PASSWORD=GlobalIT@123
```

# fontawesome
```
<link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.6.3/css/all.css">
```
ref: https://webdesign-trends.net/entry/8351

# Controller
```
php artisan make:controller UserController
php artisan make:controller LessonController
```

# Migration
```
php artisan make:migration create_followers_table --create=followers
php artisan make:migration create_lessons_table --create=lessons

rollback
php artisan migrate:rollback --step=1
```

# Add columns
```
php artisan make:migration add_column_in_users_table --table=users
php artisan make:migration add_column_in_uesr_taken_cources_table --table=userTakenCources

    public function up()
    {
        Schema::table('users', function (Blueprint $table) {
            $table->string('avatar')->after('name')->default('/images/default.jpg');
        });
    }
    public function down()
    {
        Schema::table('users', function (Blueprint $table) {
            $table->dropColumn('avatar');
        });
    }
```

# Model
```
php artisan make:model Lesson
```

# upload file

```
blade.php

<input type="file" name="avatar">

Controller

public function editStore($id)
    {
        $image = request()->file('avatar');

        $file = $image->getClientOriginalName();

        // original path is storage/app.
        // below path is storage/app/public/images
        // https://reffect.co.jp/laravel/how_to_upload_file_in_laravel
        $image->storeAs('public/images' , $file);

        $user = Auth::user()->update([
            'name' => request()->name,
            'email' => request()->email,
            'avatar' => '/storage/images/'.$file,
        ]);

        return redirect('home');
    }

migration

    public function up()
    {
        Schema::table('users', function (Blueprint $table) {
            $table->string('avatar')->after('name')->default('/images/default.jpg');
        });
    }

showpage

<div class="avatar">
    <div class="default">
            <img src="{{Auth::user()->avatar}}" style="width:100px;height:100px;">
    </div>
</div>

command

php artisan storage:link
storageに保存された画像がpublicにリンクされる

User.php
    protected $fillable = [
        'name', 'email', 'password',
        'name', 'email', 'password', 'avatar'
    ];
```

# seeder
```
php artisan make:seed UsersTableSeeder

/Users/konoken/e-learning-laravel/eLearning_app/database/seeds/UsersTableSeeder.php
public function run()
    {
        DB::table('users')->insert([
            ['name' => 'test1',
            'avatar' => '/images/default.jpg',
            'email' => 'test1@test.com',
            'password' => Hash::make('hogehoge'),
            'created_at' => date('Y-m-d H:i:s'),
            'updated_at' => date('Y-m-d H:i:s'),
            ],
            ['name' => 'test2',
            'avatar' => '/images/default.jpg',
            'email' => 'test2@test.com',
            'password' => Hash::make('hogehoge'),
            'created_at' => date('Y-m-d H:i:s'),
            'updated_at' => date('Y-m-d H:i:s'),
            ],
        ]);

/Users/konoken/e-learning-laravel/eLearning_app/database/seeds/DatabaseSeeder.php
public function run()
    {
        // $this->call(UsersTableSeeder::class);
        $this->call(UsersTableSeeder::class);
    }

insert data
php artisan db:seed
specific class
php artisan db:seed --class=UsersTableSeeder

insert data(init)
php artisan migrate:refresh --seed

ref
https://coinbaby8.com/laravel-seeder-factory-faker.html
```

自分以外を抜き出す
```
$users = User::where("id" , "!=" , Auth::user()->id)->paginate(3);
```

Questionテーブルにあるanswer_idとChoiceテーブルを紐付ける
```
Question.php
    public function answer() {
        return $this->belongsTo('App\Choice');
    }
LessonController.php
    $question->correct = $question->answer->choice;
```

forget

```
php artisan make:mail Mail

MAIL_DRIVER=smtp
MAIL_HOST=smtp.sendgrid.net
MAIL_PORT=25
MAIL_USERNAME=apikey
MAIL_PASSWORD=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
MAIL_ENCRYPTION=null
```

