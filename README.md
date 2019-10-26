
<p align="center"><img align="center" style="width:320px" src="https://firebasestorage.googleapis.com/v0/b/illuday.appspot.com/o/Logo-FINAL-60fps.gif?alt=media&token=9564b776-d191-4cb2-9823-63093d2dbe25"/></p>
<p align="center"><img align="center" style="width:200px" src="https://firebasestorage.googleapis.com/v0/b/illuday.appspot.com/o/typo-01.svg?alt=media&token=a13e0fb9-54d9-40e1-8f98-eed082718fcc"/></p>
<br><br>

> FireQL is a GraphQL connector for Firestore (Firebase database). This repository offer a boilerplate to auto-host a GraphQL server on your Firebase Project (hosting part) connecting to Firestore on the same project.

> **At the moment, use this repository at your own risk, I can't assure the continuity of this project. It'll depends of the popularity of it.**

TO REMOVE : (functions/package.json - functions/graphql/* - functions/index.js - .gitignore - README.md)


# <p align="center">Summary</p>

- **[#](#1) Getting started** – *Create project, environment, clone repository, initialize, run playground*
- **[#](#2) Create a first type**
- **[#](#3) Add documents** – *Adding a mutation to our schema, to our resolvers, execute it*
- **[#](#4) Get documents** – *Adding a query to our schema, to our resolvers, execute it*
- **[#](#5) Update documents** – *Adding a mutation to our schema, to our resolvers, execute it*
- **[#](#6) Remove documents** – *Adding a mutation to our schema, to our resolvers, execute it*
- **[#](#7) Working with relations** – *Updating our type, adding*

<br/><p align="center"><img align="center" src="https://firebasestorage.googleapis.com/v0/b/illuday.appspot.com/o/badge.png?alt=media&token=47b4fb96-6b8d-44b1-848d-0b1c143203db"/></p>


## <a name="1"></a>Getting started

### Create your Firebase project & your Firestore database
***Note:** Free projects (spark) works with FireQL!*

No specifical needs here. Just be sure to create a **Firestore database** and not a realtime one.

### Prepare your environment

Execute these commands in order to install firebase CLI and to login with your Firebase account (Obviously, you need one). Just follow steps.

```sh
$ npm install -g firebase-tools
$ firebase login
```

### Clone this repository

Git clone it or just download zip.
```sh
$ git clone https://github.com/Illuday/fireQL.git
```

### Init Firebase project

Initialise the firebase project : 

```sh
$ firebase init
# Select "Functions" & "Hosting"
# Choose your previously created project
# Use Javascript
# Say "no" to ESlint (You'll be able to install it later.)
# Don't override index.js & packages.json
# Say Yes to dependencies
# Use public directory
# Don't create SPA
# Done!
```
### Open GraphQL playground with function emulating

```sh
 firebase emulators:start
```

***Note:** On some systems, emulators doesn't seams to work using this last command. You can downgrade firebase-tools to **6.8.0**, then run :*



```sh
 firebase serve
```

Access your playground on:
>http://localhost:5001/YOU_PROJECT_ID/us-central1/api (Given in the console)

Playground is running! **You have to copy/paste your api link (url above) in the upper field inside the playground in order to make it work**.

### Open GraphQL playground on Firebase Hosting

```sh
 firebase deploy
```

Then go to Firebase console, section "Functions", you should find your url: 

> https://us-central1-YOU_PROJECT_ID.cloudfunctions.net/api

Playground is running! **You have to copy/paste your api link (url above) in the upper field inside the playground in order to make it work**.

### Deploy for production

Todo.

<br/><p align="center"><img align="center" src="https://firebasestorage.googleapis.com/v0/b/illuday.appspot.com/o/badge.png?alt=media&token=47b4fb96-6b8d-44b1-848d-0b1c143203db"/></p>

## <a name="2"></a>Create a first type

***For non GQL user:** A **type** is a collection / table in your firestore database.*

To create a type, just add it in the schema. You can add as much type you need. **We'll come back on relations later**.

```javascript
const schema = gql`
  type Artist {
    id: ID
    name: String!
    age: Int!
  }
`
``` 
###### <p align="center">functions/graphql/schema.js</p><br>

<p align="center"><img align="center" src="https://firebasestorage.googleapis.com/v0/b/illuday.appspot.com/o/badge.png?alt=media&token=47b4fb96-6b8d-44b1-848d-0b1c143203db"/></p>

## <a name="3"></a>Add document to our type

***For non GQL user:** Compare to a restAPI, a resolver is a GraphQL "route", a mutation will represent a put/patch route with parameters.*

### 1 - Adding the mutation to our schema</span>

```javascript
const schema = gql`
  type Mutation {
    addArtist(name: String!, age: Int!): Artist
  }
`
```
###### <p align="center">functions/graphql/schema.js</p>

### 2 - Adding the mutation to our resolvers</span>

FireQL is my magical library to connect our graphQL server to our firestore. FireQL.add() will automatically add the new artist to our firestore collection "artists".

```javascript
const resolverFunctions = {
  Query: {},
  Mutation: {
    addArtist: (parent, document) => fireQL.add({ collectionName: 'artists', document }),
  },
};
```
###### <p align="center">functions/graphql/resolvers.js</p>

### 3 - Executing mutation

Go to your GQL playground and execute your mutation. There, we want to add an artist named "illuday", and get his id and his name (probably illuday...).

***For non GQL user:** GraphQL allows you to get only fields you want as result of any queries or mutations.*

#### <p align="center">ADDING AN ARTIST</p>

```javascript
mutation {
  addArtist (name: "illuday", age: "28") {
    id
    name
  }
}
```
###### <p align="center">Mutation - Playground</p>

<center>:arrow_down:</center><br>

```json
{
  "data": {
    "addArtist": {
      "id": "BsuNNpRQqFbgWME1RIZ4",
      "name": "illuday"
    }
  }
}
```
###### <p align="center">Mutation result - Playground</p>

In firestore, you can see that you have your document, added to artists collection with "illuday" as name and 28 as age.

***Note:** We havn't age in result because we didn't ask for it.*

Magic.

#### We'll come back later on adding, with more powerful add!

<br/><p align="center"><img align="center" src="https://firebasestorage.googleapis.com/v0/b/illuday.appspot.com/o/badge.png?alt=media&token=47b4fb96-6b8d-44b1-848d-0b1c143203db"/></p>

## <a name="4"></a>Get documents

### 1 - Adding the query to our schema</span>

```javascript
const schema = gql`
  type Query {
    getArtists(where: WhereInput): [Artist]
  }
`
```
###### <p align="center">functions/graphql/schema.js</p>

**The result value of this query is [Artist], it'll return an Array of Artist type.**

**WhereInput** is an **helper** that provide the **structure for querying firestore**. The object needed here is:

```javascript
{
  field: 'nameOfYourField'
  operator: 'enum: EQ (==), GT (>), GTE (>=), LE (<), LTE (<=), INARRAY'
  value: { // One of
    intValue: intValue
    stringValue: stringValue
  }
}
```

***Note:** This helper is already provide in your schema from this repository.*

### 2 - Adding the query to our resolvers</span>

FireQL.get() will automatically get artists from our firestore collection "artists".

```javascript
const resolverFunctions = {
  Query: {
    getArtists: (parent, { where }) => FireQL.get({ collectionName: 'artists', where }),
  },
  Mutation: {
    ...
  },
};
```

###### <p align="center">functions/graphql/resolvers.js</p>

### 3 - Executing query

Before executing this query, I seed my database to have more artists.

- illuday: 28y/o
- Anna Dittmann: 26y/o
- Ilya Kuvshinov: 26y/o
- Shayline: 27y/o

Let's make some tries in our GraphQL playground.

***Note:** I named my queries (in playground) to be able to save them all.* 

#### <p align="center">GET ALL ARTISTS</p>

```javascript
query getAllArtists {
  getArtists {
    id
    name
    age
  }
}
```
<center>:arrow_down:</center><br>

```javascript
{
  "data": {
    "getArtists": [
      {
        "id": "GF0ihzKePxeKZMRTjY7A",
        "name": "illuday",
        "age": 28
      },
      {
        "id": "NVLWsTYEq6GgqvoCvU6W",
        "name": "Shayline",
        "age": 27
      },
      {
        "id": "TgI9PYG4p7OKzrOBmzmD",
        "name": "Anna Dittmann",
        "age": 26
      },
      {
        "id": "mPXsd1tkYRfSxN0UW1aQ",
        "name": "Ilya Kuvshinov",
        "age": 29
      }
    ]
  }
}
```

<hr>

#### <p align="center">GET ARTIST BY ID</p>

```javascript
query getArtistById {
  getArtists (where: { field: "id", value: { stringValue: "TgI9PYG4p7OKzrOBmzmD" } }){
    id
    name
    age
  }
}
```

<center>:arrow_down:</center><br>

```javascript
{
  "data": {
    "getArtists": [
      {
        "id": "TgI9PYG4p7OKzrOBmzmD",
        "name": "Anna Dittmann",
        "age": 26
      }
    ]
  }
}
```
<hr>

#### <p align="center">GET ARTISTS BY AGE</p>

```javascript
query getArtistsByAge {
  getArtists (where: { field: "age", operator: LT, value: { intValue: 28 } }){
    name
    age
  }
}
```

<center>:arrow_down:</center><br>

```javascript
{
  "data": {
    "getArtists": [
      {
        "name": "Anna Dittmann",
        "age": 26
      },
      {
        "name": "Shayline",
        "age": 27
      }
    ]
  }
}
```

<br/><p align="center"><img align="center" src="https://firebasestorage.googleapis.com/v0/b/illuday.appspot.com/o/badge.png?alt=media&token=47b4fb96-6b8d-44b1-848d-0b1c143203db"/></p>
