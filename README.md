# ParentBot

[![Codacy Badge](https://www.codacy.com/project/badge/0bebac574a7040179d83778a4dd18aa9)](https://www.codacy.com/app/dragonbanshee/node-steam-parentbot)

A module that provides a simple base class for a Steam bot that can be easily overwritten and customized.

I made this (mainly for myself) because I was getting tired of writing the same code over and over again that does the same things. This is a way to eliminate the copying and pasting of code, while still allowing you to fully customize it.

# Installation
First you will need to install [node.js](http://nodejs.org) if you haven't already. <b>This will only work with Node.js v0.12.x</b>.

Once you have node and npm installed, type this command in shell, cmd, powershell, etc:
```javascript
npm install steam-parentbot
```

Once it's installed, you will need to create your config file, you may use the one in the examples folder, or you can create one anywhere. It must follow the same structure as the one in example.js.

# Options
To initialize the bot, you must use `var Bot = new ChildBot(username, password)`, but you may also add an optional `options` object. This object can contain the following (and any others if you are adding to the bot):
```javascript
//Without options
var Bot = new ChildBot(username, password);

//With options
var Bot = new ChildBot(username, password, {
	apikey: '1234567890', //steam api key, will be registered automatically if one isn't supplied
	sentryfile: 'username.sentry', //sentry file that stores steamguard info, defaults to username.sentry
	logfile: 'username.log', //filename to log stuff to, defaults to username.log
	guardCode: 'XXXXX' //steam guard code, only needed if you get error 63 when logging in, can remove after sentry is generated
});

```

# Default methods and handlers
The base class ParentBot comes with a lot of built in methods and listeners. You can add your own, and edit the built in ones right in your config file.

The event handlers are:
```javascript
_onError() //steamClient.on('error')
_onConnected() //steamClient.on('connected')
_onLogOnResponse(response) //steamClient.on('logOnResponse')
_onLoggedOff(eresult) //steamClient.on('loggedOff')
_onDebug() //steamClient.on('debug')

_onUpdateMachineAuth(response, callback) //steamUser.on('updateMachineAuth')

_onFriendMsg(steamID, message, type, chatter) //steamFriends.on('friendMsg')
_onFriend(steamID, relationship) //steamFriends.on('friend')
```

The two default methods are
```javascript
connect() //steamClient.connect()
logOn() //steamClient.logOn()
```

This module does not come with trade offers or trading involved, but like usual, you may add it to your config file. Check out example.js for an example of this.

# Overwriting built in methods and handlers
This module was designed to provide a base class, as well as allow the user full customization. The module comes with lots of listeners built in already (the only ones you probably want to overwrite are _onFriend and _onFriendMsg since they don't really contain anything useful except for telling users that the bot isn't set up correctly (unless thats what you want).

There are already some built in instances of libraries and things that you can use in your config file. You can access all of these by doing `Bot.property.method`. For a list of methods, please visit the links for each property below. List of properties you can use:
- steamClient - an instance of [Steam.SteamClient()](https://github.com/seishun/node-steam#steamclient)
- steamUser - an instance of [Steam.SteamUser(steamClient)](https://github.com/seishun/node-steam/tree/master/lib/handlers/user#steamuser)
- steamFriends - an instance of [Steam.SteamFriends(steamClient)](https://github.com/seishun/node-steam/tree/master/lib/handlers/friends#steamfriends)
- steamTrading - an instance of [Steam.SteamTrading(steamClient)](https://github.com/seishun/node-steam/tree/master/lib/handlers/trading#steamtrading)
- logger - an instance of [Winston.Logger](https://github.com/winstonjs/winston)
- steamWebLogon - an instance of [SteamWebLogon](https://github.com/Alex7Kom/node-steam-weblogon) (use this for logging into Steam web, replacement for steamClient.on('webSessionID');
- steamTrade - an instance of [SteamTrade](https://github.com/seishun/node-steam-trade)
- offers - an instance of [SteamTradeOffers](https://github.com/Alex7Kom/node-steam-tradeoffers)

To overwrite a default handler (the ones with a _ in front), do this in your config file, assuming that `ChildBot` is the child of `ParentBot` (this example will show how to change _onFriend):
```javascript
var admins = ['76561198091343023'];
var Bot = new ChildBot('username', 'password');

ChildBot.prototype._onFriend = function(steamID, relationship) {
    if(relationship === Steam.EFriendRelationship.RequestRecipient) {
        if(admins.indexOf(steamID) !== -1) {
            Bot.steamFriends.addFriend(steamID);
        }
        else {
            Bot.logger.warn('Someone who isn\'t an admin tried to add me, denying...');
            Bot.steamFriends.removeFriend(steamID);
        }
    }
}
```

This example overwrites the _onFriend handler to create a custom friend handler that only accepts friend requests from admins. _onFriend is the function that is passed to `this.steamFriends.on('friend', function(steamID, relationship) { that._onFriend(steamID, relationship); });`, so there is no need to add a new listener, you can just modify the function.

If you want to create your own listener from an external module (this works the same way for modules that are already in use since I didn't create all listeners, like tradeOffers), you would do it like this
```javascript
var MySQL = require('mysql');

// inherit from parentbot, see [example.js](https://github.com/dragonbanshee/node-steam-parentbot/blob/master/examples/example.js) for how to do this

var Bot = new ChildBot('username', 'password');

Bot.connection = MySQL.creatConnection({
	host: 'localhost',
	user: 'root',
	password: 'password'
});

Bot.connection.on('error', function(e) {
	Bot.logger.error(e);
});
```

That is how you add your own listeners to your bot from external libraries.

# Help
__This repository is beginner friendly__. If you have a problem, no matter how simple it is, PLEASE open an issue, and either I or other users will try to answer it as quickly as possible. If you need help with something that is really complex or would take a long time, you can [add me on steam](http://steamcommunity.com/id/dragonbanshee).

# Contributors
__Pull requests are welcome!__ If you found a bug and fixed it, send a pull request. If you think that you added something useful, send a pull request. Please try to follow the existing style though. I wrote [this guide](https://github.com/efreak/node-steam-chat-bot/wiki/contributing), and I think it still applies here.

Feel free to add your name and github link here if you contributed. Also add what you did to contribute.
- <a href="https://github.com/dragonbanshee">dragonbanshee (project creator)</url>
