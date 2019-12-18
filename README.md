# Mechanics of Sessions

## Objectives

1. View session data in the browser with Developer Tools
2. Clear session cookies using Developer Tools
3. Set up a session on your servers
4. Explain that a session is just a hash that stores specific data


## Setting Up A Session

Before we dive into the mechanics of setting up a session, it's important to note that a session is basically just a hash that stores data on the server and passes that data to the client as a cookie. You can access the data stored in the session in the same way you would any hash in Ruby.

And now to set up the session...

In Sinatra, we enable sessions within the controller (e.g., `app.rb`) by adding two lines in the `configure` block:

```ruby
configure do
  enable :sessions
  set :session_secret, "secret"
end
```

> **IMPORTANT**: You should **never** set your `session_secret` by hand, and especially not to something so trivially
> simple as `"secret"`! We are doing this for the sake of demonstration this _one_ time. You are advised to learn more
> about how to secure your sessions by following the [Using Sessions][secsin] documentation at the Sinatra home.


The `configure` block above is a part of built-in settings that control whether features are enabled or not. In this case, we're enabling the sessions feature.

The first line of the configure block, `enable :sessions`, turns sessions on. The next line, `set :session_secret, "secret"`, is an encryption key that will be used to create a `session_id`. A `session_id` is a string of letters and numbers that is unique to a given user's session and is stored in the browser cookie. You can actually set your `session_secret` to anything that you want. Don't worry too much about understanding how the `session_id` works. It's just part of the mechanics behind getting a secure private session working. You probably won't ever need to interact with it.

`session_secret` is a pretty minimal security feature, but the basic idea is that setting your `:session_secret` to a word that other people don't know makes it harder for someone to create a fake session_id and hack into your site without signing up or signing in.

## Using Sessions

In order to keep track of a current user throughout a session, we need to set up the `session` hash to store the `user_id` in the hash during a controller action. Since we haven't covered how to set up logins and logouts, we can simply use the `session` hash to store the user's name:

```ruby
get '/hey' do 
  @session = session
end
```

In the example above, we have a route, `/hey`, that gets processed by a `GET` request. Because we enabled sessions in our app, every controller action has access to the `session` hash.

We stored the `session` hash in the instance variable `@session` so that our views will have access to the session data. In this case, `@session` now looks like this:

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

You can also modify and add data to the `session` hash by adding a key-value pair:

```ruby
get '/hey' do 
  session["name"] = "Victoria"
  @session = session
end
```


## Viewing Sessions

### Viewing in Developer Tools

Even though sessions are created on the server, you can view the contents of the `session` hash as a cookie in your browser using Developer Tools.

Visit any website that requires a login, and make sure that you're already logged in before completing the next steps. We'll be using [Learn.co](https://learn.co) in this example.

Go ahead and open up your browser's Developer Tools. In Chrome, you can do this by right-clicking and selecting `Inspect`. Once the Developer Tools are open, click on the `Application` tab. On the left side of the tools, open up the `Cookies` dropdown, click the `learn.co` cookie, and it will bring up all of the cookies loaded on the site. You can see that several of them say `Session` in the `Expires / Max-Age` column.

![View cookies in Developer Tools](https://s3.amazonaws.com/learn-verified/browser-cookies.png)

### Viewing in Chrome Settings

You can also see the details of these cookies in the Chrome browser by selecting the hamburger icon in the top right corner and navigating to `Settings`:

![Settings](https://s3.amazonaws.com/learn-verified/chrome-settings.png)

Scroll all the way to the bottom the page and click on the `Show advanced settings...` link. Next, scroll down until you see `Privacy`, and then click the `Content settings...` button:

![Content settings...](https://s3.amazonaws.com/learn-verified/privacy-content-settings.png)

Finally, click the `All cookies and site data...` button:

![All cookies and site data...](https://s3.amazonaws.com/learn-verified/all-cookies-data.png)

This will bring up a list of all of the sites that your browser has stored cookies from. Search for `learn.co` and click to see a list of all the cookies that Learn sets. You should see one called `_feyonce_session`. That's the session cookie that Learn uses to keep track of the current user (in this case, you).

## Clearing Sessions

There are several ways to clear a session cookie. The first is to simply log out of the site. That ends the current session, at which point all session cookies are automatically cleared.

You can also clear session cookies from the Developer Tools interface. Simply right-click on a session and select either `Delete` or `Clear All from "learn.co"`.

![Clear cookies](https://s3.amazonaws.com/learn-verified/clear-cookies.png)

If you remove the session cookies and refresh the page, you'll notice that you've been logged out of the site.

Chrome also comes bundled with a special 'Incognito' mode that doesn't persist _any_ cookies –– session or otherwise –– beyond the scope of a given browsing session. If you log in to Learn from an Incognito window, Chrome will create the cookies needed to run that session, but it will not create any long-term, cached cookies. Because of this functionality, Incognito mode is often helpful while debugging session and cookie issues.

## Securing Your Session

As we mentioned at the beginning, you should not define `session_secret` as we do in this lab. The most
secure method is to use a secure number generator to generate a secret and to share that secret, via
environment variables in the shell, to Sinatra. This is covered in [their documentation][secsin].


<p class='util--hide'>View <a href='https://learn.co/lessons/sinatra-mechanics-of-sessions-readme'>Mechanics of Sessions</a> on Learn.co and start learning to code for free.</p>
