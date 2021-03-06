# Getting started with Rails
Ref.: http://guides.rubyonrails.org/getting_started.html#adding-a-second-model

###3.2 Creating the Blog Application
```
$ rails new blog
$ cd blog
```
## 5 Getting Up and Running
```
add the article resource to the config/routes.rb as follows:
Rails.application.routes.draw do
  resources :articles
  root 'welcome#index'
end
```
## 6 Second model comments

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
== 20160218075638 CreateComments: migrating ========
-- create_table(:comments)
   -> 0.0035s
== 20160218075638 CreateComments: migrated (0.0036s) ==========

$
```
### 6.2 Associating Models
```
 edit app/models/article.rb to add the other side of the association:
 add:  has_many :comments
```
### 6.3 Adding a Route for Comments

<code>config/routes.rb</code>:
```
Rails.application.routes.draw do
  resources :articles do
    resources :comments
end

get 'welcome/index'

```

### 6.4 Generating a Controller

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
- add create action to comments_controller.rb
- add commenting to app/views/articles/show.html.erb
```
## 7 Refactoring
take a look at the app/views/articles/show.html.erb template. 
It is getting long and awkward. 
We can use partials to clean it up.
### 7.1 Rendering Partial Collections
Create the file app/views/comments/_comment.html.erb and put the following into it:
```
<p>
  <strong>Commenter:</strong>
  <%= comment.commenter %>
</p>

<p>
  <strong>Comment:</strong>
  <%= comment.body %>
</p>
```

### 7.2 Rendering a Partial Form

Let us also move that new comment section out to its own partial.
 Again, you create a file app/views/comments/_form.html.erb
  containing:
```
  <%= form_for([@article, @article.comments.build]) do |f| %>
  <p>
    <%= f.label :commenter %><br>
    <%= f.text_field :commenter %>
  </p>
  <p>
    <%= f.label :body %><br>
    <%= f.text_area :body %>
  </p>
  <p>
    <%= f.submit %>
  </p>
<% end %>
```
Then you make the app/views/articles/show.html.erb
 look like the following:
```
<p>
  <strong>Title:</strong>
  <%= @article.title %>
</p>
 
<p>
  <strong>Text:</strong>
  <%= @article.text %>
</p>

<h2>Comments</h2>
<%= render @article.comments %>
 
<h2>Add a comment:</h2>
 <%= render 'comments/form' %>
 
<%= link_to 'Edit', edit_article_path(@article) %> |
<%= link_to 'Back', articles_path %>
```
## 8 Deleting Comments
We need to implement a link of some sort in the view and
 a destroy action in the CommentsController. 
 So, let's add the delete link in the
  app/views/comments/_comment.html.erb partial:
 ```
 <p>
  <%= link_to 'Destroy Comment', [comment.article, comment],
               method: :delete,
               data: { confirm: 'Are you sure?' } %>
</p>
```
<strong>Add a destroy action to</strong>
<br>
 our controller (app/controllers/comments_controller.rb):
 ```
 def destroy
    @article = Article.find(params[:article_id])
    @comment = @article.comments.find(params[:id])
    @comment.destroy
    redirect_to article_path(@article)
  end
 ```
 ## 8.1 Deleting Associated Objects
 If you delete an article, its associated comments will
  also need to be deleted, otherwise they would simply
   occupy space in the database.
   Rails allows you to use the dependent option of an
     association to achieve this. Modify the Article model,
 <code> app/models/article.rb</code>, as follows:
 ```
 class Article < ActiveRecord::Base
   has_many :<strong>comments</strong>, dependent: <strong>:destroy</strong>
   validates :title, presence: true,
                    length: { minimum: 5 }
 end
 ```
## 9 Security
### 9.1 Basic Authentication
If you were to publish your blog online, anyone
 would be able to add, edit and delete articles or delete comments.

Rails provides a very simple HTTP authentication system that will
 work nicely in this situation.

In the ArticlesController we need to have a way to block access
 to the various actions if the person is not authenticated.
 Here we can use the Rails http_basic_authenticate_with method,
 which allows access to the requested action if that method allows it.

To use the authentication system, we specify it at the top of our
 ArticlesController in <code>app/controllers/articles_controller.rb.</code>
 We want the user to be authenticated on every action 
 except index:
 ```
 class ArticlesController < ApplicationController
 
  http_basic_authenticate_with name: "dhh", password: "secret", except: [:index, :show]
 
  def index
    @articles = Article.all
  end
 
 ```
 We also want to allow only authenticated users to delete comments,
  so in the CommentsController
  (<code>app/controllers/comments_controller.rb</code>) we write:
```
class CommentsController < ApplicationController
 
  http_basic_authenticate_with name: "dhh", password: "secret", only: :destroy
 
  def create
    @article = Article.find(params[:article_id])
    # ...
  end
```
