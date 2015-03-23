---
layout: post
title: Understanding Action Mailer
---

When building a web application you should think about all the interactions/events that comprise your site. Your goal is generally to have a high user retention rate. As a developper/website owner, you want your users to interact as much as possible on your website without feeling the need to leave or use other third party services.

Let's take a simple (but important) example: <strong>Mailing</strong>
Imagine for a second that you are a user of a perticular website and you need to send an email to the technical team about a bug or something (who cares... at this point you just want to contact them).
It would be kind of (super) ennoying to copy and paste the websites contact email into your own personal email service (Gmail, Outlook, etc...). Again, don't forget that your goal is to <strong>KEEP</strong> the user on your website. You don't want them to open up Gmail... No! What you need is a simple way for users to contact you directly via the website.

Today I am going to show you how you can implement such feature using Action Mailer in your Ruby on Rails web app. Go take a look at the <a href="http://guides.rubyonrails.org/action_mailer_basics.html">Action Mailer Basics</a> documentation to familiarize yourself with the different features it provides.


<h1>User Authentification</h1>

Let's take this step by step. If you are already using the <a href="https://github.com/plataformatec/devise"> Devise Gem</a> to authentificate users, skip this part and go right to the <strong>User Mailer</strong> section.


----<strong>Step 1</strong>

In you `Gemfile`:

```Ruby
gem 'devise'
```

Then run `bundle install`. After your done run `rails generate devise:install` which generate an initializer that will have all the devise configuration you need along with descriptions on how to use them(`config/initializers/devise.rb`).

----<strong>Step 2</strong>

Now you'll want to set up your user model. That's just one simple command :)

```Ruby
rails generate devise User
```
 This generator will also configure your 7 RESTFUL routes so that it point to the Devise controller. Go take a look: `config/routes.rb`.

And now, of course, run `rake db:migrate` to add the new users table to your `schema`!
Voila! you now have access to super usefull helper methods and call backs:

`before_action :authenticate_user!` ---> In your controller (assuming your devise model is User). This will set a before_action that will ask for user authentification before an action. You can put it in any controller you want. For example I set up mine in my `welcomeController` so that a user will be ask to authentificate him/herself at the `welcome#index` route.

`user_signed_in?` --> check if a user is signed in...

Ex:

```Ruby
 	<% if user_signed_in? %>
        <li><%= link_to "My Pets", "/users/my_pets", :method => :get, class: "active" %><span class="sr-only">(current)</span></a></li>
      </ul>
        <ul class="nav navbar-nav navbar-right">
          <li><%= link_to "Sign out", destroy_user_session_path, :method => :delete, class: "active" %></li>
      <%else%>
         <li><%= link_to('Login', new_user_session_path)%></li>
        <li><%= link_to('Sign Up', new_user_registration_path)%></li>
  <%end%>
```

`current_user` --> Helps you get info from the current user.

Ex:

```Ruby
<div c<a href="https://github.com/plataformatec/devise#controller-filters-and-helpers" lass="form-group">
      <div class="col-lg-10">
        <input type="hidden" class="form-control" id="inputEmail" name="email[user_email]" value="<%= current_user.email %>">
      </div>
    </div>
```
Here I created an input field where the value is the current user's email. You'll in a bit how this can quite usefull (hint: `input type="hidden"`).


There are a bunch of other helpers but for the purpose of this blog post i'll leave them aside. Feel free to take a look at them <a href="https://github.com/plataformatec/devise#controller-filters-and-helpers">here</a>.


<h1>User Mailer</h1>



Now that we have our users set up, we can easily implement a mailing feature. I will now show you how you can enable your users to send emails right from your web app. Once you have a good understanding of how it works, you'll know how to send emails to who ever you want!

----<strong>Step 1</strong>

Generating a Mailer:

```Ruby
bin/rails generate mailer UserMailer
create  app/mailers/user_mailer.rb
create  app/mailers/application_mailer.rb
invoke  erb
create    app/views/user_mailer
create    app/views/layouts/mailer.text.erb
create    app/views/layouts/mailer.html.erb
invoke  test_unit
create    test/mailers/user_mailer_test.rb
create    test/mailers/previews/user_mailer_preview.rb
```
 As you can see, you can generate mailers just like you do for other generators. Mailers are similar to controllers in that you get a mailer, a directory for views, and a test.

 Ok, now lets tackle this from the buttom up. First we need to set up our routes so that a user can 'POST' an email to a `create` action in the Mailers Controller. This `create` method will take the params the user passed into the form and utilise it to send the email with the appropriate content.

 In `config/routes.rb`

 ```Ruby
post '/mailers/create' => 'mailers#create'
```

```Ruby
<form action="/mailers/create" method="post" class="form-horizontal" id= "contact-form">
  <fieldset>
    <legend>Adopt Me!</legend>
    <div class="form-group">
      <div class="col-lg-10">
        <input type="hidden" class="form-control" id="inputEmail" name="email[shelter_email]" value="<%= @pet.shelter.email %>">
        <input type="hidden" class="form-control" id="inputEmail" name="email[pet_name]" value="<%= @pet.name %>">
      </div>
    </div>
    <div class="form-group">
      <div class="col-lg-10">
        <%if user_signed_in?%>
        <input type="hidden" class="form-control" id="inputEmail" name="email[user_email]" value="<%= current_user.email %>">
        <%end%>
      </div>
    </div>
    <div class="form-group">
      <label for="textArea" class="col-lg-2 control-label">Message</label>
      <div class="col-lg-10">
        <textarea class="form-control" rows="3" id="textArea" name="email[content]">Hello, <%= @pet.shelter.name %> I am interested in adopting <%= @pet.name %></textarea>
      </div>
    </div>

    <div class="form-group">
      <div class="col-lg-10 col-lg-offset-2">
        <button type="reset" class="btn btn-default cancel-btn">Cancel</button>
        <!-- only if user signed in, else alert -->
        <button type="Email" class="btn btn-warning">Submit</button>
      </div>
    </div>
  </fieldset>
</form>
```
What's going on here... For the purpose of this blog post, we are dealing with users who want to contact a shelter right from the pets show page. Some of our fields here are hidden (`type="hidden"`). Why is that?
Well because the user is sending an email to a perticular shelter from the pet show page, we do not need to display the shelter's email. We have already associated pets with shelters where pets `belong_to` a shelter and each shelter, well, has a contact email.
Here, the only thing we want the user to fill in is the actual content of the email.

```Ruby
<textarea class="form-control" rows="3" id="textArea" name="email[content]">Hello, <%= @pet.shelter.name %> I am interested in adopting <%= @pet.name %></textarea>
```
We are simply prefilling the 	`textarea` with relevant information so as to make it easier for the user.

Awesome! look at our super params:

```Ruby
{"email"=>
  {"shelter_email"=>"pennycat45@hotmail.com",
   "pet_name"=>"Alice",
   "user_email"=>"cghazanfar10@gmail.com",
   "content"=>
    "Hello, Lindas Feral Cat Assistance I am interested in adopting Alice"},
 "controller"=>"mailers",
 "action"=>"create"}
```
Perfect! Next step:

Let's set up our model:

```Ruby
class Mailer < ActionMailer::Base
	default :from     => 'example@email.com'

		def contact_shelter(shelter_email, content, subject, user_email)
			@shelter_email =  shelter_email
			@content = content
			@subject = subject
			@user_email = user_email
			mail(to: shelter_email,
		   		 body: content,
		   		 subject: subject,
		   		 reply_to: user_email)
		end

end
```
This is pretty straighforward. We have a `contact_shelter` method that takes in 4 arguments which are the values of the params passed into the form.

Jumping to our `MailersController`:

```Ruby
class MailersController < ApplicationController

	def create
	 	@user_email = params[:email][:user_email]
	  @shelter_email = params[:email][:shelter_email]
	  @content = params[:email][:content]
	  @pet_name = params[:email][:pet_name]
	  @subject = "Interested in adopting #{@pet_name}"
	  Mailer.contact_shelter(@shelter_email, @content, @subject, @user_email).deliver
	  flash.now[:notice] = 'Message sent'
	  redirect_to my_pets_path
	end

end
```
Here we are creating the params that will be passed as arguments into the `contact_shelter` method.
This one line here `Mailer.contact_shelter(@shelter_email, @content, @subject, @user_email).deliver` is taking care of the email delivery.

YES!! we are almost done.
Last but not least we need to set up the right email protocol
In the `config/environments/development.rb` configure your mailer so that it uses the smtp protocol:

```Ruby
  config.action_mailer.default_url_options = { host: 'localhost', port: 3000 }

  config.action_mailer.delivery_method = :smtp
  config.action_mailer.delivery_method = :smtp
  config.action_mailer.smtp_settings =  {
    :address        => "smtp.gmail.com",
    :port       =>  587,
    :domain       =>  'localhost',
    :user_name      => 'xxxxxx',
    :password     =>  'xxxxxx',
    :authentication   => 'plain',
    :enable_starttls_auto   => true
}
```

You now need to create the view for your email. Create a folder under views called `mailer`. In that folder create a file that is the same name of your method in your mailer model: in this case `contact_shelter.html.erb`.
This is how I set up my mailer view:

```Ruby
<!DOCTYPE html>
<html>
  <head>
    <meta content='text/html; charset=UTF-8' http-equiv='Content-Type' />
  </head>
  <body>
    <h1><%= @shelter_email %></h1>

    <p>
    <%= @content %>
    </p>

    <p>Thanks!</p>
    <p><%= @user_email %></p>
  </body>
</html>
```

So the action here is this:
--User fills in the content of the email.
--Submits the email
--email is sent to the shelters email
--Shelters can directly reply to the user

TAADAAA! Don't forget to check out <a href="http://guides.rubyonrails.org/action_mailer_basics.html">Action Mailer Basics</a> documentation if you are confused by what I am writting (It's pretty clear in my head but it might not reflect well on paper...)

Love you all and <3 Ruby

Cheers!


