# Introduction to Model Associations

##Overview of the project

I want to create a way of retrieving, storing, and manipulating information in an application I'm going to build called -- _**sailsPeople**_.  Initially, the application will have **Users** (e.g. people that will use the application to track their sales activity), **Profiles** (e.g. information realating to and describing a **User**), **Leads** (e.g.) prospective people user's want to sell products are services, and **SailsTeams** (e.g. groups of users that work together). 

I need some way of organizing **_Users_**, **_Profiles_**, **_Leads_**, and **_SailsTeams_** in my application and each of these is usually referred to as a **Data Model**.

### Getting ready for the project
Create a sails project using:

`sails new sailsPeople`

Sails creates the project using the following folder structure:

```javascript
sailsPeople
       |_api
       |_assets
       |_config
       |_node_modules
       |_tasks
       |_views                
.gitignore
.sailsrc
app.js
Gruntfile.js
package.json
README.md                
```

##What are _data_ models?
The term [**model**]() is one of those over-loaded words that can cause a bunch of confusion.  Therefore, I want to provide some context for how I'm using the term _model_ in this project.  For my purposes, a **model** is a representation of a set of structured data, usually corresponding with a table in a SQL database or a collection in databases like MongoDB, Redis, or Riak.

This _model_ can be considered a **back-end** _model_ because it resides on the server, although it can be accessed via any _Client_ that can make a request via **http** and/or **sockets**.  For context, _models_ fit here in the overall Sails framework.

<img src="http://i.imgur.com/Nci0TlT.jpg" />

###When do I use models?
Models are useful any time I want to store, retrieve, update, and/or delete data in my app.  For instance, my app might need to save _User_ data entered into a web form.  The data will passed into to a _model_ and ultimately stored on the server.   The distinction between the **model** and the underlying place it's stored is important because with Sails once I define the **model** I have a common set of methods to interact with the data.  Therefore, regardless of whether I'm using an SQL database (e.g. mySQL, PostgreSQL), a schema-less database (e.g. MongoDB, Redis, or Riak) or combination of databases, Sails removes the need to address each of the database's unique api.

I can simply configure my **models**, choose an [**Adapter**]() via a [**Connection**](), and rely upon [**Model**]() methods to access and manipulate my data across various databases.

<img src="http://i.imgur.com/0z4A2Ok.jpg" />
 
### Defining _models_

>**Task:** Configure a model to hold my _User_ data.

A _model_ is defined by group of [attributes]() that describe a particular _thing_ or _entity_.

For example, in this project I want to create a `User` model that consists of a single attribute:  `name`.  The _model_ will ultimately consist of other attributes (e.g. email address, title, phone number, etc), however, for the purpose of understanding how _models_ work and interact I'll limit the attributes to simply the user's name. 

I'll open up my editor and create a file named `User.js` and save it in the `sailsPeople/api/models/` folder.  In Sails, I can define a **User** model as a javascript object:

```javascript
{
  attributes: {
    name: {
      type: 'string'
    }
  }
}
```

When I start my app using `sails lift`, Sails will look for models in `/sailsPeople/api/models`.  Finding my newly created `User.js` file, Sails will connect to the database where my _User_ data is stored and provide an inteface for me to interact with it.  By default, Sails stores data in a temporary file on my hard disk, but I can change this to MySQL, PostgreSQL, MongoDB, Redis, or CouchDB at any time down the road.

I'm going to do something similar with my second model called **Profile**.  In my app, a **Profile** contains attributes that describe a particular **User**.

To create this second model I'll cheat a little bit.  The Sails command-line tool ships with a few simple [generators]() for quickly generating common code files.  This keeps me from having to manually create new files and put together the boilerplate code every time I want to add something new to my app.

To define my **Profile** _model_, I'll open the command line and enter:

 ```sh
 $ sails generate api profile
 ```
 
When I open `sailsPeople/api/models/Profile.js`, an empty model has been created for me.  For now, I just want to track two pieces of information for my _Profile_ -- `aboutMe` which not surprisingly will be a description of a _User_ and `gender`.   


```javascript
attributes: {
  aboutMe: {
    type: 'string'
  },
  gender: {
    type: 'string'
  }
}
```

At this point, I have defined and configured two _models_:

<img src="http://i.imgur.com/NyYnaQe.jpg" />


### Models, Records, and Collections

Throughout this project I'm going to differentiate between a **model**, a **record** and a **collection**.  

A **model** is a _definition_ of a thing (e.g. User.js): 

```javascript
attributes: {
  name: {
    type: 'string'
  }
}
```

An **record** is _one_ of those things:

```json
{
"name": "Nikola Tesla",
"createdAt": "2014-04-14T17:18:49.439Z",
"updatedAt": "2014-04-14T17:18:49.439Z",
"id": 1
}
```

 A **collection** is a _group_ of **records**:

 ```json
[  {
  "name": "Nikola Tesla",
  "createdAt": "2014-04-14T17:18:49.439Z",
  "updatedAt": "2014-04-14T17:18:49.439Z",
  "id": 1
},
{
  "name": "Neal Stephenson",
  "createdAt": "2014-04-14T20:47:01.703Z",
  "updatedAt": "2014-04-14T20:47:01.703Z",
  "id": 2
}  ]
```

> Don't confuse my use of the word collection with a collection in MongoDB or a Backbone collection, although they share some similar qualities they are not the same thing.

So with my User _model_, I can create, update, find and destroy a new _Record_ in any number of different places in Sails:

- from the [`sails console`]()
- by requesting one of my [blueprint routes]()
- from a [lifecycle callback]() defined in the User model itself
- from a custom action in a [controller]()

Although, I'll use a few different methods from the list above to work with data throughout this project, I'm concentrating on Associations and **not** _Model_ methods.  For more information on _Model_ methods and how to use them, see the reference section under [**Models**]().   

I need to create some _Users_ and _Profiles_ so I'll start with using some of the **blueprint routes**.  If you'd like a more detailed explanation of blueprint routing, particularly the **shortcut blueprint routes** below, check out: [How do blueprint routes work in sails?](http://irlnathan.github.io/sailscasts/blog/2014/01/17/sailscasts-answers-ep8-how-do-blueprint-actions-and-blueprint-routes-work-in-sails/) 

### Records: _creating_ some Users and Profiles
>**Task:** Create a couple of _**Users**_ and what will be their associated _**Profiles**_. 

I'll lift my app using `sails lift`.

I'll create my first _User_ by opening my browser and entering the following URL: [`http://localhost:1337/user/create?name=Nikola Tesla`]()

####How does this work?

I'm using the _blueprint api_ through the **shortcut blueprint _routes_** which allow me to access blueprint CRUD _model_ methods from the browser.  These **routes* are extremely helpful during development, however, can and should be [**disabled**]() during production.

- My browser is sending a GET request to my running Sails app at [localhost:1337]()
- That request matches up to a **shortcut blueprint** route, [/user/create]()
- The shortcut blueprint triggers an action, `create()` defined in `api/controllers/UserController.js`.
- **Note:** I haven't defined a custom `create` action, so the implicit [**blueprint action**]() will be run in its place.
- This inspects the [parameters]() I passed in (`?name=Nikola Tesla`) and passes them to [`User.create()`]().
- This creates a _Record_ using my User _model_ for a new user named "Nikola Tesla". 

After creating the user _Nikola Tesla_, Sails returns the following json:

```javascript
{
name: "Nikola Tesla",
createdAt: "2014-04-14T17:18:49.439Z",
updatedAt: "2014-04-14T17:18:49.439Z",
id: 1
}
```

>**Note:**
> 
> By default, Sails adds `id`, `createdAt`, and `updatedAt` attributes without me having to explicitly define them in the model.  This behavior [is configurable]().

Next, I'm going to create an additional _User_ with the name `Neal Stephenson`.  I'll visit exactly the same URL as before, but change out the `name` parameter: [`http://localhost:1337/user/create?name=Neal Stephenson`]()

So now I have two _Records_ of my User _model_:

<table>
<tr>
  <th align="center" colspan="4">Instances of User Model</th>
</tr>
<tr>
  <th align="center">id</th>
  <th align="center">name</th>
  <th align="center">createdAt</th>
  <th align="center">updatedAt</th>
</tr>
<tr>
  <td align="center">1</td>
  <td align="center">'Nikola Tesla'</td>
  <td align="center">'2014-04-14T17:18:49.439Z'</td>
  <td align="center">'2014-04-14T17:18:49.439Z'</td>
  </tr>
  <tr>
  <td align="center">2</td>
  <td align="center">'Neal Stephenson'</td>
  <td align="center">'2014-04-14T20:47:01.703Z'</td>
  <td align="center">'2014-04-14T20:47:01.703Z'</td>
  </tr>
</table>

Next I'll create two Profiles `/profile/create?aboutMe=This is stuff about me&gender=male`.

<table>
<tr>
  <th align="center" colspan="5">Instances of Profile Model</th>
</tr>
<tr>
  <th align="center">id</th>
  <th align="center">aboutMe</th>
  <th align="center">gender</th>
  <th align="center">createdAt</th>
  <th align="center">updatedAt</th>
</tr>
<tr>
  <td align="center">1</td>
  <td align="center">I was born on July 10 1856 in Serbia. I'm an inventor, electrical engineer, mechanical engineer, physicist, and futurist best known for my contributions to the design of the modern alternating current (AC) electricity supply system.</td>
  <td align="center">male</td>
  <td align="center">'2014-04-14T20:47:25.571Z'</td>
  <td align="center">'2014-04-14T20:47:25.571Z'</td>
  </tr>
  <td align="center">2</td>
  <td align="center">I'm an American author and game designer known for my work in speculative fiction. My novels have been variously categorized as science fiction, historical fiction, cyberpunk, and 'postcyberpunk.</td>
  <td align="center">male</td>
  <td align="center">'2014-04-14T20:47:25.571Z'</td>
  <td align="center">'2014-04-14T20:47:25.571Z'</td>
  </tr>
</table>


### Records: _finding_ Users and Profiles
>**Task:** Get a list of _User_ and _Profile_ Records

So now that I have some Users and Profiles, let's talk about how to retrieve them.


Again, using the blueprint API, I can access my Users and Profiles without writing any code.  I simply open up my browser and visit: [`http://localhost:1337/user`]()
(remember: this means my browser is sending a GET request to Sails at the `/user` path)

Sails returns the following json:

```json
[  {
  "name": "Nikola Tesla",
  "createdAt": "2014-04-14T17:18:49.439Z",
  "updatedAt": "2014-04-14T17:18:49.439Z",
  "id": 1
},
{
  "name": "Neal Stephenson",
  "createdAt": "2014-04-14T20:47:01.703Z",
  "updatedAt": "2014-04-14T20:47:01.703Z",
  "id": 2
}  ]
```

Accessing stored Profiles from the browser is just as easy via [`http://localhost:1337/profile`]().  As before, Sails returns the following json:


```json
[{
  "aboutMe": "I was born on July 10 1856 in Serbia. I'm an inventor, electrical engineer, mechanical engineer, physicist, and futurist best known for my contributions to the design of the modern alternating current (AC) electricity supply system.",
  "gender": "male",
  "id": 1,
  "createdAt": "2014-04-24T22:26:54.000Z",
  "updatedAt": "2014-04-24T22:26:54.000Z"
},
{
  "aboutMe": "I'm an American author and game designer known for my work in speculative fiction. My novels have been variously categorized as science fiction, historical fiction, cyberpunk, and 'postcyberpunk.'",
  "gender": "male",
  "id": 2,
  "createdAt": "2014-04-24T22:26:54.000Z",
  "updatedAt": "2014-04-24T22:26:54.000Z"
}]
```

With our _models_ defined, _attributes_ configured, and some _records_ created, it's time to address associations.

<!--                       -->
<!-- Start of Associations -->
<!--                       -->

##Why do I use _Associations_?
**Associations** provide a way to relate models so that finding, creating, updating, and destroying _Records_ requires less programming.  _Associations_ require some initial configuration, however, once configured, allow me to find and change records easily.

##One-Way Associations: _Configuring_, _Finding_, _Creating_, _Updating_ and _Removing_ Associations

###Configuring the _Model_: a _one-way association_
>**Task:** Configure an association where each _User_ can be associated with one _Profile_

Let's say I want to be able to find a particular _User_, and also the _Profile_ she is associated with.  I could manage the relationship manually: i.e. find the User, find the Profile, then format the result. However, it would be a lot nicer to look up both a User and its associated Profile in one action.

To accomplish this, I'll configure the User _model_ with a new attribute called **Profile**-- but this time, instead of specifying a primitive type like "string" or "integer", I'll configure the Profile _**attribute**_ as an association.  In addition to the _association attribute_, I can configure its _association **type**_ (e.g. a _model_, representing one associated record or a _collection_, representing one to multiple records), and the _association **model**_ (e.g. the _model_ where the associated _Records_ will come from).

<img src="http://i.imgur.com/DOZDdoG.jpg" />
>**Note:** It can be confusing that the _association type_, model or collection, uses the word model.  Is this use of the word _model_ associated with say, the _User_ model?  **No.** The _association type_ is simply letting Sails know whether the association is looking for no more than one _Record_ in the case of _model_ or in the case of _collections_ one or more _Records_. 

The Profile _**association** attribute_ represents a one-way relationship, with a single _Record_, between the User _model_ and the Profile _model_.  I could name this attribute 'foo', however, since it relates to the **Profile** _model_ I'm going to stay consistent and name the _**association** attribute_ -- Profile. What this means is that I can now associate the user, _Nikola Tesla_, with his _Profile_. More importantly, based upon this association I can access and manipulate Nikola Tesla's _Profile_ at the same time I'm accessing his _User_ Record. 

Sails abstracts away a lot of the complexity of associations for me, and it supports both "schema-ful" and "schema-less" databases. The underlying implementation of associations is slightly different in each case, but since it can be helpful to examine what's going on under the covers, I'll provide a quick overview using terminology from the classic relational model.
	
Each _Record_ contains a _**Primary Key**_ which is an attribute whose value is guaranteed to be unique between _Records_.  For example, the default _**Primary Key**_ in MongoDB would look something like `_id: ObjectId("52752fd8a61584b30c000001")` versus PostgreSQL which would look something like `id: 1`.  

In this project, I have a _Record_ -- _Nikola Tesla_ with an **id:** of 1.  The _id:_ **is** the _Primary Key_ and this _primary key_ becomes very useful when I want to associate _Nikola Tesla_ with his _Profile_. 

<img src="http://i.imgur.com/59L86a7.jpg" />

It really helps to see this change before and after the association is made.

<img src="http://i.imgur.com/Jw5s6ke.jpg?1" />

Before I make the association, _Nikola Tesla_ and his _Profile_ exist independently of each other.  However, a relationship will be created when I update Nikola's Profile _association attribute_ with the _Primary Key_ of his Profile _Record_.

### Updating a _Record's_ association: a one-way association

>**Task:** Create an association between _Nikola Tesla_ and his _Profile_.  

First I'll open the [Sails console]() by running the following command in my terminal:

```sh
$ sails console
```

Now, at the `sails> ` prompt, I'll enter:

```javascript
sails> User.update({id: 1}).set({profile: 1}).exec(function(err, user){console.log(user); });
```

That's hard to read so let's look at the same code formatted in a more readable style:

```javascript
User.update({id: 1})
  .set({profile: 1})
  .exec(function(err, user){
    console.log(user);
  });
```

####How does this work?

1. I pass the `.update` method, the _primary key_ of the user I want to update, in this case id: 1 (better known as _Nikola Tesla_).
2. Next, I set the Profile _association attribute_ (profile: ) to the _primary key_ of Nikola's Profile (1).

So now that I've placed the _primary key_  as the value of the **Profile** _association attribute_ in the Nikola's _Record_, sails can deduce that **the profile** is associated with **Nikola Tesla**.  

<img src="http://i.imgur.com/Rm2bI5y.jpg" />

As you might expect, the **Profile** _attribute_ in each user _Record_ is now capable of referring to a particular instance of the **Profile** _model_.  It's important to point out that the **Profile** _attribute_ doesn't necessarily have to be set-- a user might not have a Profile!  Equally important is that at this point a profile _Record_ can belong to more than one User.

So now that I've created the association, what can I do with it?

### Finding associated _Records_: using "populate" in a _one-way association_
>**Task:** Find _Nikola Tesla's_ profile using the _.populate()_ method.


<img src="http://i.imgur.com/59sfv4c.jpg?1" />

There are several methods I'll be using to **populate** (i.e. look up), **add**, and **remove** associated _Records_. Here is a general description of each method:


<table>
<tr>
  <th align="center" colspan="2">Association Methods</th>
</tr>
<tr>
  <th align="center">method</th>
  <th align="center">description</th>
</tr>
 <tr>
  <td align="center">`user.populate()`</td>
  <td align="left">This method will return all of the <i>Records</i> associated with <i>association attribute</i>.  For example, in a callback from the .find() method where the user is Nikola Tesla, user.populate('profile') will return Nikola Tesla's Profile <i>Record</i>.
 </tr>
<tr>
  <td align="center">`user.profile.add()`</td>
  <td align="left">This method will make the necessary association of one <i>Record</i> to another <i>Record</i>. For example in a callback from the .find() method where the user is Nikola Tesla, user.profile.add(1), will add a Profile <i>Record</i> with the <i>primary id</i> of 1 to the <i>association</i> attribute of Nikola Tesla.  <b><i>Note:</i></b> I will use the user.save() method to save the changes I made. </td>
 </tr>
 <tr>
  <td align="center">`user.profile.remove()`</td>
  <td align="left">This method will remove the necessary association of one <i>Record</i> to another <i>Record</i>. For example in a callback from the .find() method where the user is Nikola Tesla, user.profile.remove(1), will remove a Profile <i>Record</i> with the <i>primary id</i> of 1 to the <i>association</i> attribute of Nikola Tesla.  <b><i>Note:</i></b> I will use the user.save() method to save the changes I made.</td>
 </tr>
</table>


<!-- I got to about here and had to stop ~mike -->

The first of these methods is `.populate` which I can use to have sails return all _Records_ of an association.  

First I'll open the [Sails console]() by running the following command in my terminal:

```sh
$ sails console
```

Now, at the `sails> ` prompt, I'll enter:

```javascript
sails> User.findOne(1).populate('profile').exec(function(err,user){ console.log(user); });
```

That's hard to read so let's look at the same code formatted in a more readable style:

```javascript
User.findOne(1)
  .populate('profile')
  .exec(function(err,user){
    console.log(user); 
  });
```

####How does this work?

1. I pass the `.findOne` method, the primary key of the user I want to find, in this case id: 1 (better known as Nikola Tesa).
2. I then chain `.populate` to look up the `profile` _association attribute_. 
3. Sails returns the following json:

```javascript
{ 
  profile: 
   { aboutMe: 'I was born on July 10 1856 in Serbia.  I\'m an inventor, electrical engineer, mechanical engineer, physicist, and futurist best known for my contributions to the design of the modern alternating current (AC) electricity supply system.',
     gender: 'male',
     id: 1,
     createdAt: Thu Apr 24 2014 17:26:54 GMT-0500 (CDT),
     updatedAt: Thu Apr 24 2014 17:26:54 GMT-0500 (CDT),
     owner: null },
  name: 'Nikola Tesla',
  id: 1,
  createdAt: Thu Apr 24 2014 17:26:54 GMT-0500 (CDT),
  updatedAt: Fri Apr 25 2014 18:39:25 GMT-0500 (CDT) 
}
```
 
###Creating a **new** _Profile_ and associating it with an existing _User_
>**Task:** Create a new _Profile_ for _Neal Stephenson_.

<img src=http://i.imgur.com/BDVwKao.jpg?1 />

First I'll open the [Sails console]() by running the following command in my terminal:

```sh
$ sails console
```

Now, at the `sails> ` prompt, I'll enter:


```javascript
sails> Profile.create({aboutMe: "I'm an American author and game designer known for my work in speculative fiction. My novels have been variously categorized as science fiction, historical fiction, cyberpunk, and 'postcyberpunk.", gender: "male"}).exec(function(err, profile) { User.findOne(2).exec(function(err, user) {user.profile = profile.id; user.save(function(err){}); }); });
```

Here is the same code but in a more readable format:

```javascript
Profile.create({aboutMe: "I'm an American author and game designer known for my work in speculative fiction. My novels have been variously categorized as science fiction, historical fiction, cyberpunk, and 'postcyberpunk.", 
  gender: "male"})
  .exec(function(err, profile) { 
    User.findOne(2).exec(function(err, user) {
      user.profile = profile.id; 
      user.save(function(err){}); 
    }); 
  });
```

####How does this work?

1. First I create the **new** _Profile_ using the `.create` method and pass in two attributes _aboutMe_ and _gender_.
2. Next, I find the _User_ **Neal Stephenson** who I want to assocate with the new _Profile_ via the `.findOne` method and passing in **Neal Stephenson's** _Primary Key_ of `1`.
3. Next, I asign the Profile _attribute_ of **Neal Stepheson** with the Profile `id:` returned from step one using `user.profile = profile.id;`
4. Finally, I save the model instance of **Neal Stephenson** by using the `.save` method.

### Creating a **new** _User_ and associating it with an existing _Profile_
>**Task:** Create a new user _Aimee Mann_ and associate it with her _Profile_.

<img src=http://i.imgur.com/KWmQ4WM.jpg/>

I could use the same strategy I used in the previous example, however, because the User _model_ is aware of the Profile _model_ through its association, I can also use an additional, less verbose, approach.

>**Note:** I previously created the following Profile _Record_ for this example:

```json
{ 
  "aboutMe": "I'm an American rock singer-songwriter, guitarist and bassist. In the 1980s, I sang in the Boston new wave band 'Til Tuesday until I left to begin a solo career in the early 1990s.",
  "gender": "female",
  "id": "3",
  "createdAt": "Sat Apr 26 2014 22:22:56 GMT-0500 (CDT)",
  "updatedAt": "Sat Apr 26 2014 22:22:56 GMT-0500 (CDT)"" 
}
```


First I'll open the [Sails console]() by running the following command in my terminal:

```sh
$ sails console
```

Now, at the `sails> ` prompt, I'll enter:


```javascript
sails> User.create({name: 'Aimee Mann', profile: 3}).exec(function(err, user { console.log(user); });
```

Here is the same code but in a more readable format:

```javascript
User.create({name: 'Aimee Mann', profile: 3})
  .exec(function(err, user {
    console.log(user);
  });
```

####How does this work?

I create the **new** _User_ using the `.create` method and pass in Aimee Mann's name along with the _primary key_ of the Profile I want to associate with Aimee (e.g. profile: 3).


### Removing the association between a _User_ and a _Profile_: a _one-way associations_
>**Task:** Remove the association between _Neal Stephenson_ and his _Profile_.

<img src="http://i.imgur.com/4MqMR3v.jpg?1" />

I can remove Neal Stephenson's association with its Profile by assigning `null` to Neal's Profile _attribute_.

First I'll open the [Sails console]() by running the following command in my terminal:

```sh
$ sails console
```

Now, at the `sails> ` prompt, I'll enter:


```javascript
sails> User.update(2).set({profile: null}).exec(console.log);
```

####How does this work?

1. First I pass the _primary key_ of Neal Stephenson (2) to the `.update()` method.
2. Next, I'll set the Profile _association attribute_ to null.

<!--                       -->
<!--  Two-way Associations -->
<!--                       -->

##Two-way Associations: Configuring, Finding, Creating, Updating and Removing 

So far, I've been using a one-way association between the User and Profile _models_. Nikola Tesla is aware of his _Profile_ **record**, however, his Profile is unaware of Nikola Tesla.  This is easy to fix. I'll configure another one-way association in the opposite direction, this time between the Profile and User _models_.  

**Since I already explained how to use a one-way association, aren't two-way associations the same thing?**  Well, yes and no.  Yes, in that the configuration of the _Model_ is very similar, however, the way I use the create, update, and remove methods are different and worthy of some examples.

###Configuring the _Model_: a _two-way association_
>**Task:** Create a new association between the _Profile_ model and the _User_ model.

<img src="http://i.imgur.com/8VPMYjA.jpg" />

Similar to how I configured the User _model_, I can configure the Profile _model_ with a new _**association** attribute_ called **Owner**.  The User _**association** attribute_ represents a one-way relationship between the Profile _model_ and the User _model_.  That is, Nikola Tesla's Profile _record_ is now "aware" of Nikola.

<img src ="http://i.imgur.com/AJkVupC.jpg" />


### Finding associated _Records_: using "populate" in a _two-way association_
>**Task:** Find the Profile _record_ of _Neal Stephenson_ using the **Profile** _association attribute_ and find the _Neal Stephenson_ using the **Owner** _association attribute_.

Not surprisingly finding a User or Profile record is identical to the one-way association.  Where things get interesting is in Creating, Updating, and Deleting Associations.

<img src="http://i.imgur.com/ur9r0XE.jpg?2" />

### Create a new _User_ and _Profile_ Record while adding associations at the same time
>**Task:** Create a new _User_, Boris Karloff, and his _Profile_ and associate each of them.

<img src="http://i.imgur.com/VzznCRP.jpg?1" />

First I'll open the [Sails console]() by running the following command in my terminal:

```sh
$ sails console
```

Now, at the `sails> ` prompt, I'll enter:


```javascript
sails> User.create({name: 'Boris Karloff', profile: {aboutMe: 'I am an actor born in 1887 best known for my portrayal of Frankenstein's monster and the voice of the animated classic 'The Grinch that stole Christimas', gender: 'male'}}).exec(function(err, user) {Profile.update(user.profile).set({owner: user.id}).exec(console.log); });
```

Here is the same code but in a more readable format:

```javascript
User.create({name: 'Boris Karloff', profile: {aboutMe: 'I am an actor born in 1887 best known for my portrayal of Frankenstein's monster and the voice of the animated classic 'The Grinch that stole Christimas." , gender: 'male'}})
  .exec(function(err, user) {
    Profile.update(user.profile)
      .set({owner: user.id})
        .exec(console.log); 
  });
```

####How does this work?

1. First I create the user _Boris Karloff_ by passing the `name` of the new User I'm creating.
2. I also pass in Boris Karloff's _association attribute_ --**profile** as an object that contains the attributes of the Profile -- `aboutMe` and `gender`. 
3. Next, I update Boris's Profile in the callback using `user.profile` as the _primary id_ of the Profile I want to update.
4. Finally, I set the Profile's _association attribute_ -- `owner` and pass in Boris Karloff's _primary key_ using `user.id`.


###Updating a Record's association: a two-way association.
>**Task:** Associate _Charles Ponzi's_ with his _Profile_.

<img src="http://i.imgur.com/BjN9M44.jpg" />

First I'll open the [Sails console]() by running the following command in my terminal:

```sh
$ sails console
```

Now, at the `sails> ` prompt, I'll enter:


```javascript
sails> User.update(5, { profile: { id: 5, owner: 5 } }).exec(console.log)
```

####How does this work?

1. First I'll pass in Charles Ponzi's _primary key_ (5) to the `.update()` method. 
2. Next, I'll pass in Ponzi's _association attribute_ -- **profile** as a _**nested**_ object containing the _primary key_ of Ponzi's Profile (id: 5) as well as the _primary key_ of Ponzi himself (owner: 5).

### Removing the association between a _User_ and a _Profile_: a _two-way association_
>**Task:** Removing _Charles Ponzi's_ user **record** with his _Profile_.

<img src="http://i.imgur.com/pLtQHjb.jpg" />

First I'll open the [Sails console]() by running the following command in my terminal:

```sh
$ sails console
```

Now, at the `sails> ` prompt, I'll enter:

```javascript
sails> User.update({id: 5}, {profile: {id: 5, owner: null}}).exec(function(err, user) { user[0].profile = null; user[0].save(); });
```

Here is the same code but in a more readable format:

```javascript
User.update({id: 5}, {profile: {id: 5, owner: null}})
  .exec(function(err, user) { 
    user[0].profile = null; 
    user[0].save(); 
  });
```

####How does this work?

1. First I'll pass in Charles Ponzi's _primary key_ (5) to the `.update()` method. 
2. Next, I'll pass in Ponzi's _association attribute_ --**profile** as a _**nested**_ object containing the _primary key_ of Ponzi's Profile (id: 5) as well as assigning the **owner** _association attribute_ to null. 

>**Note:** It is very important that when passing a nested object in the `.update` method that I specify a _primary key_ of what I want to update.  If I don't pass the _primary key_, the `.update` method will create a new object.

###Validating Two-way Associations
Even though I've set-up a two-way association between the _User_ and _Profile_ as well as the _Profile_ and _User_, there is nothing that will prevent Charles Ponzi's Profile _record_ from being associated with both Charles Ponzi and Nikola Tesla.  Given Ponzi's past, I don't think Nikola Tesla would appreciate that.  But as with most things there's a way to prevent that behavior.

Because Associations are _attributes_ Sails validations apply.

###Unqiue Validation: Enforcing a one-to-one association
**Task:** Prevent more than one _User_ record being associated with a particular _Profile_.

In order to prevent more than one User being associated with a particular Profile, I'll make a slight configuration change in my User and Profile _models_.

<img src="http://i.imgur.com/FyDmr8K.jpg" />

By adding the **unique** _validation attribute_, only one Profile _record_ may be associated with a particular User _record_.  If I attempt to associate a Profile _record_ to more than one User _record_, I receive the following error:

```javascript
{
  error: "E_UNKNOWN",
  summary: "Encountered an unexpected error",
  status: 500,
  raw: "error: duplicate key value violates unique constraint "user_profile_key""
}
```

<!--                           -->
<!--  One to Many Associations -->
<!--                           -->

##One to Many Associations: Configuring, Finding, Creating, Updating and Removing 

Up to this point, the associations I've used have been limited to single _Records_.  For example, Nikola Tesla can have only one Profile.  **But what if I need to associate more than one _Record_ with Nikola Tesla?** In this section I'm going to explore associating multiple _Records_ together.  The first of these associations is a one-to-many relationship like:

- _a band_ has one to many _members_
- _a mother_ can have one to many _children_
- _a person_ can manage one to many _projects_

For my **sailsPeople** _project_ I'm going to add a new _model_ called **Lead**.  Each _User_ can have many _Leads_.  Setting up this association is simple.

###Configuring the Model: a one-to-many association
>**Task:** Create a _model_ called _Lead_ with a `name` _attribute_. Configure a **_Leads_** _association attribute_ for the _User_ model.

<img src="http://i.imgur.com/L8JZ4wt.jpg?1" />

To define my **Lead** _model_, I'll open the command line and enter:

 ```sh
 $ sails generate api lead
 ```
 
When I open `sailsPeople/api/models/Lead.js`, an empty model has been created for me.  For now, I just want to track the name of the lead so I'll add the attribute `name`.   


```javascript
attributes: {
  name: 'string'
}
```

 I'll open up the User _model_ located in the `sailsPeople/api/models/User.js` file and add a **new** _association attribute_ called **Leads**-- to represent a particular user _Record's_ _Leads_.  This time, however, instead of specifying an _association type_ of **model**, I'll use a _association type_ of **collection**. 

>**Note:** A **collection** is different than a **model** _association type_ in that a _**collection**_ can have one or more _Records_.

The User _model_ should now look like this:


```javascript
attributes: {
  name: {
    type: 'string'
  },
  profile: {
    model: 'profile'
    unique: true
  },
  leads: {
    collection: ‘lead’
  }  
}
```

Once again sails abstracts the complexity away from me, however, I find it helpful to dig a bit deeper to see what's going on behind the scenes.  My **sailsPeople** project now has four models -- User, Profile and Lead.  But wait, what's the fourth model?  First, since we're not going to be discussing the Profile _model_ in this section I'll concentrate on the two _models_ that are going to share the association -- _User_ and _Lead_.  The fourth model is something called a **Join** or **Junction** model.  This _model_ will be the bridge between the _User_ and the _Lead_ and will contain an array of _primary keys_ of each associated _Record_.

<img src="http://i.imgur.com/fSCIWrJ.jpg?1" />

###Creating a new _Lead_ and adding an association with an existing _User_

>**Task:** Create a _Lead_ with the name _George Jetson_ and associate him with _Nikola Tesla_. 

<img src="http://i.imgur.com/Yc2scFO.jpg" />

First I'll open the [Sails console]() by running the following command in my terminal:

```sh
$ sails console
```

Now, at the `sails> ` prompt, I'll enter:

```javascript
sails> User.findOne(1).exec(function(err,user){ user.leads.add({name: 'George Jetson'}); user.save(); });
```

Here is the same code but in a more readable format:

```javascript
User.findOne(1)
  .exec(function(err,user){ 
    user.leads.add({name: 'George Jetson'}); 
    user.save();
  });
```

####How does this work?

1. First I find _Nikola Tesla_ by passing in his _primary key_ (1) into the `.findOne()` method.
2. Next, using dot notation (user.leads) I add the lead _George Jetson_ by passing in an object `{name: 'George Jetson'}`as an argument to the `.add()` method.  This has the effect of creating the _Lead_ and associating the _primary key_ of George Jetson with Nikola Tesla's **leads** _association attribute_.
3. Finally, I save _Nikola Tesla_ using the `.save()` method.

> **Note:** Using the method just explained, I've created additional _Leads_ -- Hero Protagonist, Guy Montag, Mustapha Mond, and Winston Smith, now associated with _Nikola Tesla_.

###Finding _associated_ Records: using "populate" in a one-to-many association
>**Task:** Find all of the _Lead_ **records** associated with _Nikola Tesla_.

<img src="http://i.imgur.com/KdodJm5.jpg" />

First I'll open the [Sails console]() by running the following command in my terminal:

```sh
$ sails console
```

Now, at the `sails> ` prompt, I'll enter:

```javascript
sails> User.findOne(1).populate("leads").exec(console.log);
```

####How does this work?

1. First I pass in _Nikola Tesla's_ _primary key_ (1) to the `.findOne()` method.
2. I then chain `.populate()` passing in the **leads** _association attribute_.

Sails returns the following json object:

```javascript

 [ { leads: 
     [ { name: 'Geore Jetson',
         id: 1,
         createdAt: Thu May 01 2014 14:32:23 GMT-0500 (CDT),
         updatedAt: Thu May 01 2014 14:32:23 GMT-0500 (CDT) } ],
       { name: 'Hero Protagonist',
         id: 2,
         createdAt: Thu May 01 2014 14:23:10 GMT-0500 (CDT),
         updatedAt: Thu May 01 2014 14:23:10 GMT-0500 (CDT) },
       { name: 'Guy Montag',
         id: 3,
         createdAt: Thu May 01 2014 14:23:21 GMT-0500 (CDT),
         updatedAt: Thu May 01 2014 14:23:21 GMT-0500 (CDT) },
       { name: 'Mustapha Mond',
         id: 4,
         createdAt: Thu May 01 2014 14:23:32 GMT-0500 (CDT),
         updatedAt: Thu May 01 2014 14:23:32 GMT-0500 (CDT) },
       { name: 'Winston Smith',
         id: 5,
         createdAt: Thu May 01 2014 14:23:36 GMT-0500 (CDT),
         updatedAt: Thu May 01 2014 14:23:36 GMT-0500 (CDT) },
           profile: null,
    name: 'Nikola Tesla',
    createdAt: Thu May 01 2014 13:48:02 GMT-0500 (CDT),
    updatedAt: Thu May 01 2014 14:32:23 GMT-0500 (CDT),
    id: 1 } ]

```

###Removing the association between a User and a Lead: one-to-many associations

>**Task:** Remove the association between _Nikola Tesla_ and _Guy Montag_.

<img src="http://i.imgur.com/BziXZ4j.jpg" />

First I'll open the [Sails console]() by running the following command in my terminal:

```sh
$ sails console
```

Now, at the `sails> ` prompt, I'll enter:

```javascript
sails> User.findOne(1).exec(function(err, user) { user.leads.remove(3); user.save(); });
```

Here is the same code but in a more readable format:

```javascript
User.findOne(1)
  .exec(function(err, user) { 
    user.leads.remove(3); 
    user.save(); 
  });
```

####How does this work?

1. First I find _Nikola Tesla_ by passing in his _primary key_ (1) into the `.findOne()` method.
2. Next, using dot notation (user.leads) I'll remove _Guy Montag_ as a lead of Nikola Tesla by passing in Guy's  _primary key_ (3) as an argument to the `.remove()` method.
3. Finally, I'll save the User -- _Nikola Tesla_ using the `.save()` method.

<!--                            -->
<!--  Many to Many Associations -->
<!--                            -->

##Many to Many Associations: Configuring, Finding, Creating, Updating and Removing 

In addition to associating one _Record_ to many other _Records_, sails also provides for many-to-many relationships like:

- a student takes many classes; a class has many students.
- an order can have many items -- an item can be in many orders
- a doctor, sees many patients; a patient sees many doctors.

For my **sailsPeople** _project_ I'm going to add a new _model_ called **sailsTeam**.  Each _User_ can have many _sailsTeams_ and each _sailsTeam_ can have many _Users_. 

###Configuring the Model: a many-to-many association
>**Task:** Configure the _User_ model so that each user _record_ can be assocated with one or more sailsTeam _records_ through an _association attribute_ called **sailsTeams**. The _association type_ will be collection.  Configure the _sailsTeam_ model so that each sailsTeam _record_ can be associated with one or more user _records_ through an _association attribute_ called **members**.  The _association type_ will be collection. 

I'll create _model_ called -- **SailsTeam** to track the sales teams.  Within this _model_ I'll create a new _association attribute_ called-- **members** to represent the _Users_ that comprise the sales team.  Finally, I'll configure the _User_ model with a new _association attribute_ called-- **sailsTeams** to represent which (if any) _sailsTeam_ the _User_ belongs.  

<img src="http://i.imgur.com/wpBg8Dv.jpg" />

Although sails abstracts the complexity away from me, I find it helpful to dig deeper in order to make a more informed decision of how I configure my associations.  The number of models comprising this many-to-many relationship might be surprising.  In this configuration we have two join or junctions models -- one for the relationship betwen _Users_ to _SailsTeam_ and between _SailsTeam_ to _Users_.  In a bit, I'll show how I can make one slight change to the configuration so that only one join/junction model is necessary, however, could there ever be a situation where I want a many-to-many association where my _Models_ don't know about each other?

Suppose I have an application where a _user_ can "friend" another _user_ and/or designate that _user_ as an "enemy".  In this application I don't want one _user_ to know that another _user_ has made them an enemy.  Therefore, I'll maintain the updates to the associations for each _model_.

> **Note:** Creating and Removing associations are identical to one-to-many associations.

<img src="http://i.imgur.com/PYhNdrm.jpg?1" />

###Configuring the Model: a many-to-many association with _via_

>**Task:** Configure the _User_ and _sailsTeam_ models so that changing the association in one _model_ syncs with the other _model_.

So far, I'm responsible for keeping the _User_ and _sailsTeam_ models in sync.  Instead, when a _User_ is added or removed from the _sailsTeam_ or vice versa I want each _model_ to be updated with the change.  To accomplish this I'll use a new attribute to associations called **_via_**.  Currently, my _SailsTeam_ model doesn't know about the existence of the _User_ model. Using _via_ indicates which attribute will be synced up between a given _model_ on a change.  So from the perspective of the _User_ model, when _Nikola Tesla_ is added to the _sailsTeam_, I want the **members** _association attribute_ to be updated as well.   

<img src="http://i.imgur.com/Y6hBzEW.jpg" />

Once again, my sailsProject has three models -- the User _model_, the sailsTeam _model_, and the join/junction model that bridges the other two models together.  Now both the User and sailsTeam _models_ are aware of each other.  Therefore, associations that are added or removed occur on both models simultaneously.

<img src="http://i.imgur.com/QSt7Bve.jpg?1" />

###Creating a new sailsTeam and add an association with an existing User

>**Task**: Create a new _sailsTeam_ with the name **Mid-West** and make _Nikola Tesla_ a member.

> **Note:** I created two sailsTeams prior to this example.

<img src="http://i.imgur.com/oDtwB95.jpg" />

First I'll open the [Sails console]() by running the following command in my terminal:

```sh
$ sails console
```

Now, at the `sails> ` prompt, I'll enter:

```javascript
sails> User.findOne(1).exec(function(err, user) { user.sailsTeams.add({name: 'Mid-West'}); user.save(); });
```

Here is the same code but in a more readable format:

```javascript
User.findOne(1)
  .exec(function(err, user) { 
    user.sailsTeams.add({name: 'Mid-West'}); 
    user.save(); 
  });
```

####How does this work?

1. First I find _Nikola Tesla_ by passing in his _primary key_ (1) into the `.findOne()` method. 
2. Next, using dot notation (user.sailsTeams) I'll add a new sailsTeam by passing in a "nested" object `{name: 'Mid-West'}` as an argument to the `.add()` method.  Since I haven't provided a primary id (e.g. id: 10), Sails will automatically create the sailsTeam and associate Nikola Tesla with it. 
3. Finally, I'll save the User -- _Nikola Tesla_ using the `.save()` method.

###Removing an association between an existing sailsTeam and an existing User
>**Task:** Remove _Nikola Tesla_ from the _Mid-West_ sailsTeeam.

<img src="http://i.imgur.com/lk2E6Kp.jpg" />

First I'll open the [Sails console]() by running the following command in my terminal:

```sh
$ sails console
```

Now, at the `sails> ` prompt, I'll enter:

```javascript
sails> User.findOne(1).exec(function(err, user) { user.sailsTeams.remove(3); user.save(); });
```

Here is the same code but in a more readable format:

```javascript
User.findOne(1)
  .exec(function(err, user) { 
    user.sailsTeams.remove(3); 
    user.save(); 
  });
```

####How does this work?

1. First I find _Nikola Tesla_ by passing in his _primary key_ (1) into the `.findOne()` method.
2. Next, using dot notation (user.sailsTeams) I'll remove the _Mid-West_ sailsTeam by passing in that sailsTeam's _primary key_ (3) as an argument to the `.remove()` method.
3. Finally, I'll save the User -- _Nikola Tesla_ using the `.save()` method.

##Combining a one-to-many association with a one-way association using **via**

Salespeople don't want to fight over leads.  Therefore I want to limit the associations so that a user can have multiple leads but only one user can be assocated with a particular lead at one time.

###Using **via** to limit a lead _Record_ to one user _Record_
>**Task:** Configure the _Lead_ model with a new **owner** _association attribute_, using the **model** _association type_, with the _associated model_ as **user**.  Configure the _User_ model's **leads** _association attribute_ with the **via** _attribute_ using **owner**.

First, I'll configure the **Lead** model found in the `/sailsPeople/api/models/Lead.js` file with a new _association attribute_ called **owner**. 

>**Note:** If you already have Records from your Lead _model_, these Records will be deleted the next time you start sails using `sails lift` or `sails console` as part of the auto-migration of Sails.


<img src="http://i.imgur.com/LYWBFQS.jpg" />

Next, I'll configure the **User** model found in the `/sailsPeople/api/models/User.js` file with the **via** _attribute_ and reference the **owner** _association attribute_ of the Lead _model_.

<img src="http://i.imgur.com/k22k0LM.jpg" />

**So what have I accomplished?**  By syncing the one to many relationship between user and lead with the one-way relationship between lead and user I've guaranteed that a lead can only be "owned" by one user.  I'll go through this step-by-step.

In my **sailsPeople** project I've created the following leads and users:

<img src="http://i.imgur.com/wFBlEGr.jpg?1" />

Let's see what happens when I associate _Nikola Tesla_ with _George Jetson_:

<img src="http://i.imgur.com/YgBjZsc.jpg" />

Not surprisingly, the _primary key_ of Nikola Tesa is added to the **owner** _association attribute_ of George Jetson.  Next, I'm going to associate the remainder of the leads to Nikola Tesla using the same process ending up with the following:

<img src="http://i.imgur.com/W7fvaLE.jpg?1" />

So what will happen if I associate the user _Neal Stephenson_ with the lead _George Jetson_?

<img src="http://i.imgur.com/LgF0rUK.jpg" />

Because I've sync'd these two models using the **via** attribute, Sails has added the appropriate back-end configuration so that whether I add an association from the User _model_ or the _Lead_ model only one user will be associated with a particular lead.  

>**Note:** To prevent a user other than _Nikola Tesla_ from changing a lead association, I could create a policy where only _users_ that have an association with a lead, have the ablity to change the association. See the [**policies**]() reference guide for information on how to configure policies.

