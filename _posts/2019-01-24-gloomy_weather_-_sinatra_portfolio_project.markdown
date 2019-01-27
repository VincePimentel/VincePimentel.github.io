---
layout: post
title:      "Gloomy Weather - Sinatra Portfolio Project"
date:       2019-01-24 00:12:35 -0500
permalink:  gloomy_weather_-_sinatra_portfolio_project
---

Whenever I need to focus, relax and/or block out my noisy neighbor, I usually open up a website or app that generates ambient background noise. There's just this calm and soothing feeling listening to raindrops hitting the roof or ground, and thunder roaring so I thought to myself, why not create my own version?

**Gloomy Weather** generates background ambient sounds ranging from soothing rain, rumbling and crashing thunder, moving train, chirping birds, waves by the beach and many more. You can mix and match various sounds and audio levels to get the ideal combination to help you relax, sleep, focus or just to drown out bothersome noise from your environment.

### Demo and Repo

Check out the demo: [https://gloomy-weather.herokuapp.com](https://gloomy-weather.herokuapp.com).

Check out the repo: [https://github.com/VincePimentel/gloomy-weather](https://github.com/VincePimentel/gloomy-weather).

## Front-End

![Gloomy Weather Homepage](https://i.imgur.com/QMkNBZD.png)

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

You can mix and match various elements and audio levels to get the ideal combination to help you relax, sleep, focus or just to drown out bothersome noise from your environment and save them as presets for later listening.

## Back-End
### MODELS AND ASSOCIATIONS

![](https://i.imgur.com/wY0hKjJ.png)

The app has two models - User and Preset.
* A user has many presets
* User has a username and password
* A preset belongs to a user
* Preset has a title, description, user_id and elements with integer values which are essentially their volume levels

```
ActiveRecord::Schema.define(version: 2019_01_16_002015) do

  create_table "presets", force: :cascade do |t|
    t.integer "user_id"
    t.string "title", default: ""
    t.string "description", default: ""
    t.integer "rain", default: 0
    t.integer "thunder", default: 0
    t.integer "beach", default: 0
    t.integer "river", default: 0
    t.integer "garden", default: 0
    t.integer "fire", default: 0
    t.integer "bird", default: 0
    t.integer "night", default: 0
    t.integer "train", default: 0
    t.integer "cafe", default: 0
    t.integer "womb", default: 0
    t.integer "brown", default: 0
  end

  create_table "users", force: :cascade do |t|
    t.string "username"
    t.string "password_digest"
  end

end
```

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

### CONTROLLERS AND ROUTES

### Users Controller

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

If `@user.valid?`, as in it meets the requirements I have set (`presence: true` and `length { minimum: 3}`), then their data was valid and was successfully entered into the database. They will then be redirected to their own page. If not, then pass the `@user` instance to the registration form.

### VIEWS

### Registration

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

### RESOURCES

### Javascript

I also had to write some basic scripts to allow the user to interact with the play, pause, reset buttons and adjust the volume of each of the elements.

### Bootstrap, FontAwesome and Material.io

For everything that you see, I have used [Bootstrap](https://getbootstrap.com/) and [FontAwesome](https://fontawesome.com/) to make it a lot prettier than what I could make by myself! Most of the sounds came from [Freesound](https://freesound.org/).

Thank you for reading and I hope this app can be of use for you one day!


