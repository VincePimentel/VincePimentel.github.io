---
layout: post
title:      "Gloomy Weather - Sinatra Portfolio Project"
date:       2019-01-24 00:12:35 -0500
permalink:  gloomy_weather_-_sinatra_portfolio_project
---

Whenever I need to focus, relax and/or block out my noisy neighbor, I usually open up a website or app that generates ambient background noise. There's just this calm and soothing feeling listening to raindrops hitting the roof or ground, and thunder roaring so I thought to myself, why not create my own version?

## Front-End


![Gloomy Weather Homepage](https://i.imgur.com/r8FDaND.png)
*If you are reading this before I update the icons, please excuse the placeholder poop icon!*


### Controls
![](https://i.imgur.com/SGmHZH6.png)

I wanted my web app to have simple controls:
```
 Left click: increase volume
Right click: decrease volume
       Save: save current combination as a preset
 Pause/Play: toggle playing and pausing of all sounds
      Reset: zero out and stop all sounds
```

### Presets
![](https://i.imgur.com/cLr836K.png)

Presets are saved combination of elements and volume levels by a user. Being a registered user allows you to save, edit and delete your own presets for later listening. It also allows you to view, listen and copy presets made by other users.

## Back-End
### Models and Association

![](https://i.imgur.com/wY0hKjJ.png)

The app has two models - User and Preset.
* A user has many presets
* User has a username and password
* A preset belongs to a user
* Preset has a title, description, user_id and elements with integer values (volume level)

Since users will be registering, logging in and be allowed to later change their username and/or password, I need to set up my User model to help with validations. It would also be helpful to show feedback to the user when something goes wrong when trying to register or edit their account information. So I will need to set limitations on how short a username or password would be and such.

At the early stages of developing the app, I had to manually write my own validations (they were not pretty!). But later, I found out that you could use macros, methods and helpers from ActiveRecord!

For example, if I have my User model like this:

```
class User < ActiveRecord::Base
  validates :username, presence: true, length: { minimum: 3 }
  validates :password, presence: true, length: { minimum: 6 }
end
```

By having the `presence` helper, the app can make sure that users registering or editing their username or password can't have blank/empty ones.

And the best part is that by using the methods `errors` and `full_messages` on instances of Users that you call `save` or `update` on, they are going to return an array of error messages that can be used to display to the user without even having to manually write them.

Let's say a user submits a form with the following params: `{ username: "DJ", password: "12345" }`

```
user = User.create(params)
user.valid? # => false
user.errors.full_messages
 # = > ["Username is too short (minimum is 3 characters)", "Password is too short (minimum is 6 characters)"]
 ```
 
This can then be used in the controllers and views to validate data and display error messages easily and dynamically.


### Controllers and Routes

#### Users Controller

Continuing from above, my POST /users route (after submitting in the registration page) looks like this:

```
post "/users" do
  @user = User.create(params)

  if @user.valid?
    session[:user_id] = @user.id

    redirect "/users/#{@user.slug}"
  else
    erb :"/registrations/form"
  end
end
```

If `@user.valid?` then they'll be redirected to their own page. If not then pass `@user` to the registration form.

### Views

#### Registration

Still with our "DJ" example from above, I can then display the errors on the registration page by iterating through the array given by the `error.full_messages` methods.

Under the username input field, I have written the following to display only messages concerned with `username`.

```
<div class="invalid-feedback alert alert-danger" role="alert">
  <ul>
  <% @user.errors[:username].each do |message| %>
		
    <li><%= message %>.</li>
				
  <% end %>
  </ul>
</div>
```

![](https://i.imgur.com/1BRTnEA.png)

#### Javascript

I also had to write some basic scripts to allow the user to interact with the play, pause, reset buttons and adjust the volume of each of the elements.

#### Bootstrap, FontAwesome and Material.io

For everything that you see, I have used [Bootstrap](https://getbootstrap.com/), [FontAwesome](https://fontawesome.com/) and [Material.io](https://material.io/) to make it a lot prettier than what I could make by myself!



Thank you for reading!


