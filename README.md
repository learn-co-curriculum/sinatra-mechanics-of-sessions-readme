# Mechanics of A Session

## Objectives

1.  View session data in the browser with Developer Tools
2. Clear session cookies using Developer Tools
3. Set up a session on your servers
4. Explain that a session is just a hash that stores specific data


## Setting Up A Session

Before we dive into the mechanics of setting up a session, it's important to note that a session is basically just a hash that stores data on the server, and passes the data to the client as a cookie. You can access the data stored in the session in the same way you would any hash in Ruby.


And now to set up the session...

In Sinatra, you want to set up your session in your controller, `app.rb` by using Sinatra's `configure block` to enable sessions in your app:

```ruby
configure do
  enable :sessions
  set :session_secret, "secret"
end
```

The `configure` block above is part of a built-in settings that control whether features are enabled or not. In this case, we're enabling the sessions feature.

The first line of the configure block `enable: sessions`, turns sessions on. The next line `set :session_secret, "secret"` is an encryption key that will be used to create a `session_id`. A `session_id` is a unique string of letters and numbers that is unique per session and is stored in the browser cookie. You can actually set your session secret to anything that you want. Don't worry too much about understanding how the `session_id` works. It's just part of the mechanics of getting a secure private session working. You won't need to interact with it pretty much ever.

The `session_secret` is a pretty minimal security feature, but the basic idea is that setting your `:session_secret` to a word that other people don't know makes it harder for someone to create a fake session_id and hack into your site without signing up or signing in.

## Using Sessions

In order to keep track of a current user throughout a session, you would want to set up the `session` hash to store the `user_id` in the hash during a controller action. Because we haven't covered how to set up log in and log out, we can use the `session` hash to store the user's name:

```ruby

get '/hey' do 
  @session = session
end
```

In the example above, we have a route `/hey` that gets processed by a GET request. Because we enabled sessions in our app, every controller action has access to the `session` hash.

We stored our session hash in the instance variable `@session` so that our views will have access to the session data. In this case `@session` looks like this:

```ruby
@session = {
  "session_id"=>  
    "dd32f512ee239ad74aa6f10c8cad37ce28d6c6922eff252ed641b1017130fe22", 
  "csrf"=> "040e9777d4dfae03bb1e6498f2a75482", 
  "tracking"=>{ 
    "HTTP_USER_AGENT"=> "e193e9e937caa9a19ca483f046281aae77d2216b", 
    "HTTP_ACCEPT_LANGUAGE"=> "66eae971492938c2dcc2fb1ddc8d7ec3196037da"
  }
}
```

You can access information from the hash just like you would any hash. `@session["session_id"]` will return `"dd32f512ee239ad74aa6f10c8cad37ce28d6c6922eff252ed641b1017130fe22"`.

You can also modify and add data to the session hash by adding a key-value pair:

```ruby
get '/hey' do 
  session["name"] = "Victoria"
  @session = session
end
```


## Viewing Sessions

### Viewing in Developer Tools

Even though you set up a session on the server, you can view the contents of the session in the browser as a cookie using Developer Tools.

Visit any website that requires you to log in and make sure that you're logged in already before completing the next steps. We'll be using learn.co in this example.

Go ahead and open up the Developer Tools. In Chrome, you can do this by right clicking and selecting `Inspect Element`. Once the developer tools are open, click the `Resources`. On the right side, you'll see a section that says `Cookies` and it will bring up all the cookies loaded on the site. You can see that several of them say `Session`.

<img src="https://s3.amazonaws.com/learn-verified/browser-cookies.png">

### Viewing in Chrome Settings

You can see the details of these cookies in the Chrome browser by selecting the hamburger icon in the top right corner and selecting settings:

<img src="https://s3.amazonaws.com/learn-verified/chrome-settings.png">

Scroll all the way to the bottom the page and select the link `Show advanced settings...`. Next, scroll down till you see `Privacy` and select `Content settings...`:

<img src="https://s3.amazonaws.com/learn-verified/privacy-content-settings.png">

And last, select `All Cookies and Data`:

<img src="https://s3.amazonaws.com/learn-verified/all-cookies-data.png">

This will bring up a list of all the sites that your browser has stored cookies from. Search for `learn.co` and click to see a list of all the cookies Learn keeps. You should see one called `_feyonce_session`. That's the session cookie learn uses to keep track of a current user.

## Clearing Sessions

There are several ways to clear a session cookie. The first is to simply log out of the site. At that point, you automatically clear any browsing data. 

First, there is a a way to clear the cookies with the developer tools. Simply right click on a session and select either `Delete` or `Delete All from learn.co`

<img src="https://s3.amazonaws.com/learn-verified/clear-cookies.png">

As soon as you remove the cookies, you'll notice that more likely than not, you'll be logged out of the site.

Second, you can open an `Incognito Window` in Chrome which will not store any cookies. And Incognito Window allows you to browse without Chrome storing your browsing history and cookie store. You can open a browser in Incognito by selecting `File < New Incognito Window`. 

If you log into Learn.co in the Incognito Window, it will create the cookies needed to run that session, but will not have cached cookies. This can be helpful when debugging session and cookie issues.




