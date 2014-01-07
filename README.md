#Unroll-Me/derby

This is a fork of [codeparty/derby](https://github.com/codeparty/derby) maintained by [SLaks](http://slaks.net) to add features and bugfixes.

 > The Derby MVC framework makes it easy to write real-time, collaborative applications that run in both Node.js and browsers.

 > Derby includes a powerful data synchronization engine called Racer that automatically syncs data among browsers, servers, and a database. Models subscribe to changes on specific objects, enabling granular control of data propagation without defining channels. Racer supports offline usage and conflict resolution out of the box, which greatly simplifies writing multi-user applications.

 > Derby applications load immediately and can be indexed by search engines, because the same templates render on both server and client. In addition, templates define bindings, which instantly update the view when the model changes and vice versa. Derby makes it simple to write applications that load as fast as a search engine, are as interactive as a document editor, and work offline.

For more information, see the stock Derby documentation at http://derbyjs.com/

Issues and pull requests welcome.  
For support, see the [Google Group](https://groups.google.com/forum/?fromgroups#!forum/derbyjs).

[![Build
Status](https://secure.travis-ci.org/Unroll-Me/derby.png)](http://travis-ci.org/Unroll-Me/derby)

##Fork Usage

I have changed parts of how Racer and Derby interact with each-other, so Unroll-Me/Derby must be run with [SLaks/racer](https://github.com/SLaks/racer) (and vice-versa).  
To prevent mistakes (and to make it easier to use forks of my projects), I removed Derby's npm dependency on Racer, so Racer must always be installed separately.

To get started, delete `node_modules/derby` and (if present) `node_module/racer`, and run

```shell
npm install --save git://github.com/SLaks/racer.git
npm install --save git://github.com/Unroll-Me/derby.git
```

I also have a [fork of Tracks](https://github.com/Unroll-Me/tracks), with some minor bugfixes, but it can be used independently of these forks.

If you use npm link to develop on a project directly against your own clone of Derby, you will need to link or install Racer in the Derby clone instead of the project itself (since node will resolve packages relative to the symlink target, not your project)

#Changes from stock Derby

This fork contains the following changes (sorted by date descending; grouped by type).
Some of these changes have been pull-requested to stock Derby (not all of these pull requests have been accepted); others, particularly those that depend on Racer changes or involve more fundamental overhauls, have not.

##Breaking changes
Unroll-Me/derby currently has no breaking changes, other than changes involving Racer interaction (which, when used with SLaks/racer, don't break anything).

## Bugfixes
 - View function invocations can now contain strings with commas and parentheses (but not escape characters)
 - `this.model.req` now exists in view functions on static views.  
Note that `this.model` is still empty when rendering static views; to access model data, use `this.ctx` directly.
 - Models containing U+2028 LINE SEPARATOR characters are now serialized correctly

## New features
 - Static view helpers ([#225](https://github.com/codeparty/derby/pull/225))  
   You can now create [view helpers](http://derbyjs.com/#view_helper_functions) for use with [static pages](http://derbyjs.com/#static_pages).  
Such view helpers must be registered with every `staticPages` instance used to render static views that use them, in the same way that view helpers are registered with app views:  
```js
var staticPages = derby.createStatic(root);
staticPages.view.fn('name', function(...) { ... };
```

 - Precompiling static views  
You can now call `app.view.pack()` on static views too:  
```js
var staticPages = derby.createStatic(root);
staticPages.pack('folder/home', function(err, clientName) { ... };
```  
This will create a JSON file in `public/genpack` with the pre-compiled HTML and CSS markup for the view.  This allows Derby to skip the expensive compilation step, which would ordinarily happen the first time each view is loaded (per `staticPages` instance).  
Note that this only applies in production mode.
