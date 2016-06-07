# Realtime Quick-Start

Developing a [Collaborative Real Time Editor](https://en.wikipedia.org/wiki/Collaborative_real-time_editor) is easier than ever!

You can find a list of useful repositories [here](./REPOSITORIES.md).

We're going to use some of them to produce a basic appliation that will familiarize you with the basics of writing software for real time collaboration.

This guide assumes you're using some kind of Unix-like operating system, Mac OSX or Linux-based distributions ought to work without problems.
Windows users may have to adapt instructions.

Let's get started!

### Setting up your Server

We'll be using **Chainpad-Server** as a back end for our app.

To get your server running, follow the [installation guide](https://github.com/xwiki-labs/chainpad-server#installation), and then launch your server using `node server.js`.

### Writing your App

We're going to build a **Realtime Guestbook**.
When somebody visits the page, they'll be prompted to input their name.
Once they've entered their name, it will be added to a list a of visitors.

The twist for this simple app is that the list of visitors is actually a realtime datastructure.
When a new visitor modifies the datastructure, other users' pages will update to reflect their changes.

If you look inside your server's `www/` directory, you'll find a `template/` directory, which contains two files:

* `index.html`
* `main.js`

Our application will use this same structure.


```BASH
# Navigate into the www/ directory, if you aren't already there
cd ./www/;

# Create a folder for your app
mkdir guestbook;

# Navigate into your new folder
cd guestbook;
```

#### Markup

Your next step is to create some basic HTML that you can view in your browser.

Using your favourite text editor, Open `index.html`, paste the following code into it, and save.

```HTML
<!DOCTYPE html>
<html>
<head>
    <meta content="text/html; charset=utf-8" http-equiv="content-type"/>
    <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
    <script data-main="main" src="/bower_components/requirejs/require.js"></script>
    <style>
        html, body{
            padding: 0px;
            margin: 0px;
            overflow: hidden;
            box-sizing: border-box;
            background-color: #e1e1e1;
        }
        body {
            width: 50vw;
            margin: auto;
        }
        h1, h2 { text-align: center; }
        pre { font-size:30px; }
    </style>
</head>
<body>
    <h1>Guestbook</h1>
    <h2>This page has been visited by:</h2>
    <pre id="visitors"></pre>

</body>
</html>
```

Assuming you're using the default configuration for your server, you should now be able to visit http://localhost:3001/guestbook/.

It should look something like this:

![](./assets/guestbook-initial.png)

#### Javascript

The rest of our example app will consist entirely of Javascript, in a single file called `main.js`, located in the same `guestbook/` directory.

Once again, open up your favourite text editor and paste the following template into `main.js`:

```javascript
define([
    /* DEPENDENCIES */
], function (/* MODULES */) {
    /* MAIN ROUTINE */

});
```

When you visit your web page, this module will be loaded by the script tag:

`<script data-main="main" src="/bower_components/requirejs/require.js"></script>`

`main.js` is a module which is compatible with _require.js_.

You can specify other modules as dependencies where it says `/* DEPENDENCIES */`.
They will be loaded and provided to your main program where it says `/* MODULES */`.

As you add to your script, you may want to reload your page and see what difference your changes have made.
Ideally you should have your console open so you notice if there are errors.

Let's start with dependencies...

```javascript
define([

    /*  our server provides a configuration API, from which we can determine
        the URL of the websocket we will use */
    '/api/config?cb=' + Math.random().toString(16).slice(2),

    /*  this library creates a collaborative object */
    '/bower_components/chainpad-listmap/chainpad-listmap.js',

    /*  provide some cryptographic functions so that we can 
        pass information through the server without it being readable */
    '/bower_components/chainpad-crypto/crypto.js',
    '/bower_components/jquery/dist/jquery.min.js',

/*  the modules we've specified will be passed to our main routine as
    function arguments, as you can see below.

    We don't need to do anything with jQuery, as it's loaded into
    the global scope as `$` */
], function (Config, Listmap, Crypto) {
    /* MAIN ROUTINE */

});
```

Now we can work on the main routine.
The rest of our application's code will go in the function body located below the dependencies:

To start, we want to prompt our users to provide their name.
We'll assign their input to the variable `userName`:

```javascript
], function (Config, Listmap, Crypto) {
    var userName = window.prompt("What is your name?");
```

Next we want to create our realtime object.

We need to specify a few configuration variables:

* the URL of the websocket it will use to communicate with other peers
* the channel id each peer will use to connect
* the encryption key
* an object of the type we'd like to use to collaborate
* our encryption module, which will encrypt messages before sending them to the server, and decrypt them when new messages are received

```javascript
    var rt = Listmap.create({
        websocketURL: Config.websocketURL,
        channel: "b87dff2e9a465f0e0ae36453d19b087c",
        cryptKey:"sksyaHv+OOlQumRmZrQU4f5N",
        data:{},
        crypto: Crypto
    });
```

Just like that, we have a realtime object.
We can add properties to it, and our changes will be replicated to our peers.

You may have noticed that we specified the channel and cryptKey as unchanging strings.
This is because we want all users to visit the same guestbook.

In most cases users will want to be able to join distinct channels, and invite friends or colleagues to join their channels.
As an app author you'll need to choose a User Interface that works for your goals.
You can prompt users to enter a channel and password, or infer both by parsing the [fragment identifier](https://en.wikipedia.org/wiki/Fragment_identifier) from the page's URL, as is done in [Cryptpad](https://cryptpad.fr).

Next you'll want to figure out what should happen when the object is updated.
In our case, we want to update the page to display a list of all the users who have visited the page.

We can make a function that takes an array and updates the DOM, then hook it into the object after.

```javascript
    var $userList = $('#visitors');

    var render = function (visitors) {
        $userList.text(visitors
            .map(function (name) {
                return '* ' + name;
            }).join('\n'));
    };
```

Now you just need to hook that into your realtime object.
It takes a little time for your object to download and reconstruct the history of changes.

It's best to wait until you've received the latest changes before making further modifications to the object.
You can do that by adding an 'onready' callback to the object.

```javascript
    var proxy = rt.proxy.on('ready', function () {
        console.log('ready!');

        // now that the object is ready, listen for further changes
        proxy.on('change', ['guestBook'], function (oldValue, newvalue, path) {
            console.log("A new user (%s) signed the guestbook", newValue);
            render(proxy.guestBook);
        }).on('disconnect', function () {
            // if the user loses their connection, inform them
            window.alert('Network connection lost!');
        });

        // initialize the guestbook, if it isn't already there
        if (!proxy.guestBook) { proxy.guestBook = []; }

        // do a little validation on your input, and

        if (typeof userName !== 'string') {
            render(proxy.guestBook);
            return;
        }

        userName = userName.trim();
        // add your name to the guestBook, if it isn't already there
        if (userName && proxy.guestBook.indexOf(userName) === -1) {
            proxy.guestBook.push(userName);
        }

        // display the current list (with your name)
        render(proxy.guestBook);
    });
```

And that's that!

Try visiting your page with two separate windows open.
Sign the guestbook with the first, then with the second, and watch as the first page updates to reflect the second page's changes are reflected through the content.

Realtime apps can be much more complicated, but that's a basic start.

To recap:

1. Launch your server
2. write some HTML that loads a main javascript file
3. create a main javascript file that loads dependencies
4. define functions to handle realtime events
5. create a realtime session and attach your functions to it

Before modifying your code any more, it's recommended that you back it up.
Then you can freely add and remove code to see what effect it has on your app's behaviour.

The complete source for the realtime-guestbook app is available [here](./examples/guestbook/).
