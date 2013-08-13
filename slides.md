# Iris

### Idea to Implementation

Presented by [Max Beatty]("http://maxbeatty.com")

###### CONFIDENTIAL



## What is Iris?

Iris is a full-stack app built with [Backbone.js](http://backbonejs.org) on the client which RESTfully talks to a [Node.js](http://nodejs.org) server that's able to call [our service layer](https://github.com/isocket/jservices).


## Where?

Seemslessly embedded inside

- [isocket.com](http://www.isocket.com)
- [buyads.com](http://www.buyads.com)

_Users have no idea they are using a separate technology stack._


## How?

A single JavaScript include inside of `coreapp` loads the Backbone app using [RequireJS](http://requirejs.org/)

```
use_javascript(
  "/i/components/requirejs/require.js",
  '',
  array("data-main" => "/i/main")
);
```


## Why?

0. Prince
1. <s>`coreapp`</s>
2. Less work to build from scratch
3. Easier service-layer interactions


## Who?

- [Gui](https://github.com/guimonz), [Chris](https://github.com/cbaclig), and [Max](https://github.com/maxbeatty) owned the decision to move further away from `coreapp`.

- [Max](https://github.com/maxbeatty) lead the architecting of the Node.js-backed Backbone app with inspiration from [ƒovea](https://github.com/isocket/fovea) and [BuyAds Pro](https://github.com/isocket/buyadspro)

<p class="fragment">[Everyone](https://github.com/isocket/iris/graphs/contributors) but [Brian](https://github.com/brianstarke) has contributed</p>


## When?

- Initial commit: May 21, 2013

- Production Launch: July 15, 2013

- Shipped in less than **2 months**

- Averaging 15 commits per day



## Architecture

100% [CoffeeScript](http://coffeescript.org/)

### Client

RequireJS

Backbone.js

### Server

Node.js

Express

More to come from [Ryan](https://github.com/ryanbarry)



## Workflow

<p class="fragment">1. User loads page including `iris` snippet</p>
<p class="fragment">2. Remotely fetches data from `iris` server</p>
<p class="fragment">3. Renders view client-side with Underscore templates in Backbone views</p>
<p class="fragment">4. Interactions with models and collections are synced to server</p>



## Sample View

```coffeescript
define [
  'jquery'
  'underscore'
  'backbone'
  'text!tpl/user/_info.html'
], ($, _, Backbone, tpl) ->

  class User extends Backbone.View
    template: _.template tpl

    className: 'participant clear-both'

    initialize: ->
      @model.bind 'change', @render, @ # triggered after fetch

    render: ->
      @$el.html @template @model.toJSON()
```



## Promises

Promises are objects which represent the pending result of an asynchronous operation

jQuery 1.5 added `Deferred` which implements Promises


## Example Deferred

```coffeescript
seller = $.Deferred (deferred) => @getSeller options.seller, deferred
```

```coffeescript
getSeller: (sellerIdentity, deferred) ->
  if sellerIdentity.userUid?
    seller = new User uid: sellerIdentity.userUid
    sellerBlock = new UserView model: seller
  else
    seller = new Group uid: sellerIdentity.groupUid
    sellerBlock = new GroupView model: seller

  seller.fetch
    error:
      deferred.fail "error fetching user"

    success: (model, response, options) ->
      deferred.resolve sellerBlock.el.outerHTML
```


## $.when

> Provides a way to execute callback functions based on one or more objects, usually Deferred objects that represent asynchronous events.

```coffeescript
$.when( $.ajax("/api") )
.done(
  (data, status, jqxhr) ->
    console.log arguments
)
```


## Multiple Deferreds

Control flow by waiting for both to finish

```coffeescript
seller = $.Deferred (deferred) => @getSeller options.seller, deferred

buyer = $.Deferred (deferred) => @getBuyer options, deferred

$.when(seller, buyer)
.done(
  (sellerResponse, buyerResponse) =>
    @render sellerResponse, buyerResponse[0], buyerResponse[1]
    options.deferred.resolve @el.outerHTML
)
.fail @failedFetch
```


# Questions?

Feedback?