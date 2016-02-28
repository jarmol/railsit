1. rails new MessageApp
2. gems: bundle install
3. rails generate model Message
Jarmo-MacBook-Air:MessengerApp railam$ rails generate model Message
Running via Spring preloader in process 898
      invoke  active_record
      create    db/migrate/20160215155850_create_messages.rb
      create    app/models/message.rb
      invoke    test_unit
      create      test/models/message_test.rb
      create      test/fixtures/messages.yml
Jarmo-MacBook-Air:MessengerApp railam$ 
4. Open the migration file in db/migrate/ for the messages table. 
The name of the migration file starts with the timestamp of when it was created. 
Inside the change method, add this line as line 4: 
   t.text :content
5. rake db:migrate
Jarmo-MacBook-Air:MessengerApp railam$ rake db:migrate
== 20160215155850 CreateMessages: migrating ===================================
-- create_table(:messages)
   -> 0.0015s
== 20160215155850 CreateMessages: migrated (0.0015s) ==========================

Jarmo-MacBook-Air:MessengerApp railam$ 
6. rake db:seed


    The change method tells Rails what change to make to the database. Here it uses the create_table method to create a new table in the database for storing messages.
    Inside create_table, we added t.text :content. This will create a text column called content in the messages tables.
    The final line t.timestamps is a Rails command that creates two more columns in the messages table called created_at and updated_at. These columns are automatically set when a message is created and updated.

7. Controller Messages
rails generate controller Messages
Jarmo-MacBook-Air:MessengerApp railam$ rails generate controller Messages
Running via Spring preloader in process 967
      create  app/controllers/messages_controller.rb
      invoke  erb
      create    app/views/messages
      invoke  test_unit
      create    test/controllers/messages_controller_test.rb
      invoke  helper
      create    app/helpers/messages_helper.rb
      invoke    test_unit
      invoke  assets
      invoke    coffee
      create      app/assets/javascripts/messages.coffee
      invoke    scss
      create      app/assets/stylesheets/messages.scss
Jarmo-MacBook-Air:MessengerApp railam$ 

8. In the routes file, create a route that maps the URL /messages to the Messages controller's index action.
lisää tiedostoon config/router.rb
  get '/messages' => 'messages#index'
9. Messages controller (app/controllers/messages_controller.rb), add an index action:
def index 
  @messages = Message.all 
end

10. nano app/views/messages/index.html.erb
The file index.html.erb is a web template. Web templates are HTML files that contain variables and control flow statements.
11. define methods and add routes for new and create
12. Then in the Messages controller below the new action, add a private method named message_params.
-------
Summary
When you visit http://localhost:8000/messages/new to create a new message, it triggers the first turn of the request/response cycle:

    The browser makes a HTTP GET request for the URL /messages/new.
    The Rails router maps this URL to the Messages controller's new action. The new action creates a new Message object @message and passes it on to the view in app/views/messages/new.html.erb.
    In the view, form_for creates a form with the fields of the @message object.

Then when you fill out the form and press Create, it triggers the second turn of the request/response cycle:

    The browser sends the data to the Rails app via an HTTP POST request to the URL /messages.
    This time, the Rails router maps this URL to the create action.
    The create action uses the message_params method to safely collect data from the form and update the database.

Here we used link_to to create a link to /messages/new. Instead of hardcoding <a> elements, we can use link_to to generate links:

    the first parameter is the link text
    the second parameter is the URL

    A model represents a table in the database.
    A migration is a way to update the database with a new table, or changes to an existing table.
    Rails provides seven standard controller actions for doing common things such as display and create data
    Data can be displayed in the view using ERB web templating.
    Data can be saved into the database using a web form.

------
