# Getting started with Rails

## 6. Second model comments
ref. http://guides.rubyonrails.org/getting_started.html#adding-a-second-model
```
$ rails generate model Comment commenter:string body:text article:references
Running via Spring preloader in process 539
      invoke  active_record
      create    db/migrate/20160218075638_create_comments.rb
      create    app/models/comment.rb
      invoke    test_unit
      create      test/models/comment_test.rb
      create      test/fixtures/comments.yml

$ rake db:migrate
== 20160218075638 CreateComments: migrating ===================================
-- create_table(:comments)
   -> 0.0035s
== 20160218075638 CreateComments: migrated (0.0036s) ==========================

$
```
### 6.2 Associating Models
```
edit app/models/article.rb to add the other side of the association:
 add:  has_many :comments
```
### 6.3 Adding a Route for Comments
```
  resources :articles do
    resources :comments
  end
```
### 6.4 Generating a Controller
routes.rb
---------

```
Rails.application.routes.draw do
  resources :articles do
    resources :comments
end
```
get 'welcome/index'

```

$ rails generate controller Comments
Running via Spring preloader in process 1436
      create  app/controllers/comments_controller.rb
      invoke  erb
      create    app/views/comments
      invoke  test_unit
      create    test/controllers/comments_controller_test.rb
      invoke  helper
      create    app/helpers/comments_helper.rb
      invoke    test_unit
      invoke  assets
      invoke    coffee
      create      app/assets/javascripts/comments.coffee
      invoke    scss
      create      app/assets/stylesheets/comments.scss
$
Edit template for comments
app/views/articles/show.html.erb
* add create action to comments_controller.rb
* add commenting to app/views/articles/show.html.erb
```
