# Action Cable
Implement Action Cable in a simple working Rails app



> **3 steps :** <br>
> 1 - Create a channel to handle Websockets connections on the server side<br>
> 2 - Update the controller action(s) to broadcast changes to the channel (instead of redirecting or rendering)<br>
> 3 - Make a JS program to interact with the page on the client side (using data sent on the channel)

<br>

## Channel

Generate a new channel 

```shell
$ rails generate channel Foobar
```
This generate (among others) : *app/channels/foobar_channel.rb*. Set the stream(s) here :
```ruby
def subscribed
    stream_from "streamname"
end
```
*(As many streams as needed can be added, each one being streamed to all or just a group of users).*

Link the main server of the app with the Action Cable server. In the *config/routes.rb* file, add :
```ruby
mount ActionCable.server, at: '/cable'
```
<br>

## Controller

Add the server method .broadcast to the controller to set the data **hash** that will be streamed

```ruby
ActionCable.server.broadcast 'streamname',
                                data_key1: info1,
                                data_key2: info2,
                                ...
```
***- DRY -*** <br>
If the content added with Action Cable is identical to existing views/partials, it's better to directly stream a partial :
```ruby
ActionCable.server.broadcast 'streamname',
                                data_key1: render(partial: 'partial_name', locals: { baz_variable_needed: baz_variale_passed }),
                                ...
```
***- Notes -*** 
- If the data is submitted via a form, change the form to submit a remote request ( *data-remote="true"* in the *form* tag)
- Update *app/channels/application_cable/connection.rb* if a [login protection is needed at Action Cable level](https://www.learnenough.com/action-cable-tutorial#sec-login_protection)
<br>

## Client side JavaScript - Channel subscription
The file *app/assets/javascripts/channel/foobar.coffee* receives the data broadcasted on the Foobar channel.
```coffee
received: (data) ->
    functionUsingDataReceived(data.data_key1, data.data_key2)
    ...
```
Instead of CoffeeScript, plain vanilla JS can alternatively be used in *app/assets/javascripts/channel/foobar.js*
```javascript
received: function(data) {
    functionUsingDataReceived(data.data_key1, data.data_key2);
    ...
```
<br>

## Deploy to production (on Heroku)
- Add Redis Gem
- Include redistogo addon
- Update production URL in *config/cable.yml*
- Configure the production environment in *config/environments/production.rb*