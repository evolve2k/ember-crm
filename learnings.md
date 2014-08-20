Here I document my learnings on how an ember rails app fits together.

# app/assets/javascripts/application.js.coffee
window.App = Ember.Application.create();          # we now have an 'App' javascript (Ember) Object

The base object is Ember.Object. All of the other objects in Ember extend Ember.Object.

Most of the time you will be using an object that extends Ember.Object 
like Ember.Controller or Ember.View, but you can also use Ember.Object itself. 
Ember.Route

One common use for Ember.Object is to create service objects that handle some specific logic.

How to make an ember object
user

user.get('firstName') is 'Sam'

user.toString() #=> <Ember.Object:ember{objectId}>     # user.class

App.User = Ember.Object.extend()
user = App.User.create()             # User extends off App


Properties
----------
App.User = Ember.Object.extend
  isHuman: true
  temperature: 98.6
  favoriteDirector: 'Tarantino'

isHuman, temperature, and favoriteDirector would now be accessible with .get.

user = App.User.create()
user.get('isHuman') #=> true
user.get('favoriteDirector') #=> "Tarantino"
user.get('temperature') #=> 98.6

App.User = Ember.Object.extend
  fullName: ( ->
    @get('firstName') + ' ' + @get('lastName')
  ).property('firstName', 'lastName')

Functions
---------
App.User = Ember.Object.extend
  showMessage: (message) -> alert(message)
  showName: -> @showMessage(@get('fullName'))

Observers
---------
App.User = Ember.Object.extend
  weightChanged: ( ->
    alert('yikes') if @get('weight') > 400
  ).observes('weight')

Extend existing objects
-----------------------
App.Animal = Ember.Object.extend
  likesFood: true
App.Human = App.Animal.extend()
human = App.Human.create()
human.get('likesFood') #=> true

Init
----
App.Human = Ember.Object.extend
  init: -> alert("I think, therefore I am")


Reopening Objects
-----------------
App.Human = Ember.Object.extend()

App.Human.reopen
  name: 'Señor Bacon'

francis = App.Human.create()
francis.get('name') #=> 'Señor Bacon'


Equiv of class level methods
-----------------------------
App.Human = Ember.Object.extend()
App.Human.reopenClass
  sayUncle: -> alert("uncle")

ROUTES
------
APP.router.map

# app/assets/javascripts/router.js.coffee
# This is full of magic

App.Router.map ->
  @resource 'users'
  @route 'user.new', path: '/users/new'
  @resource 'user', path: '/users/:id', ->
    @route 'edit'

Ember Object Flow (Railsy like magic)
-------------------------------------
Route -> Controller -> View -> Template

# app/assets/javascripts/router.js.coffee
App.Router.map ->
  @route 'about'

# creates..
AboutRoute, AboutController, AboutView, and look for a template named about.js.emblem (or about.hbs if you’re using Handlebars). 

FLOW
----
When you go to the about route, which would be http://localhost:3000/#/about, all of these console logs will get called, in this order:

# app/assets/javascripts/routes/about.js.coffee
App.AboutRoute = Ember.Route.extend
  init: ->
    @_super()
    console.log 'route called'

# app/assets/javascripts/controllers/about.js.coffee
App.AboutController = Ember.Controller.extend
  init: ->
    @_super()
    console.log 'controller called'


# app/assets/javascripts/views/about.js.coffee
App.AboutView = Ember.View.extend
  init: ->
    @_super()
    console.log "view called"

# app/assets/javascripts/templates/about.js.emblem
  h1 Template rendered!

EMBER ROUTE
-----------


In Ember every template is backed by a model. And when the model changes the data shown via the template autoatically updates also.
Jeff: Ember does not create a model it looks for one.

The Route specifies which model a template is backed by.


Action helpers send event requests to the controller if the action is undertaken

An Ember Controller is an object that stores application state and responds to events from your templates.

A partial helper does nothing but help break up your templates (into separate files) if they get a bit big & unweildy.

LiveReload
