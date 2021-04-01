### Aggregation Pipeline 

**What is aggregation pipeline ?**

 Through Aggregation pipeline, we can pass the data and then  do many operations.
 
  ***For ex:*** in our data there could be many fields and we only want to display some of them. We can do calculations on the different items on the document. 
  
  ***For ex:*** there may be a certain smartphone category and it may have ratings. Using aggregation pipeline, we can calculate the average of the ratings and display themHow to use ?


 We can use it in DB or in our NODE APP  we can use Model and then  define as:

```javascript
TourModel.aggregate{[.......pipeline.......]}
```

We pass different pipeline methods as an array, so the filtering of the data goes as per the array.Different Pipeline methods we used:

1. **`$match`** : The match operator is used to filter the  document that matches the specific criteria. For ex:  we want ratings greater than 4.5 , we can do that using $match operator.
2. **`$group`**: The group operator groups the  elements that we specify, For ex: to user we want  to only display all details except the primary keys and other  in our database. We can group items we want to display and then put in aggregation pipeline.
3. **`$sort`**: As the name suggests, it sorts the elements in the document. We can sort in increasing or deceasing way. To sort in decreasing way use $sort:{-1} and 1 for increasing.
4. **`$unwind`**: What unwind does is that it takes each element and  returns one document each for array of element provided. The clear example as we saw in StackOverflow:
    
```
{
title : "this is my title" ,
author : "bob" ,
posted : new Date () ,
pageViews : 5 ,
tags : [ "fun" , "good" , "fun" ] ,
comments : [
             { author :"joe" , text : "this is cool" } ,
             { author :"sam" , text : "this is bad" }
],
other : { foo : 5 }}

```

What `$unwind` does is allow you to peel off a document for each element and returns that resulting document. To think of this in a classical approach, it would be the equivilent of "for each item in the tags array, return a document with only that item".Now, here we see the tags are in array form and they are "fun", "good" and "fun". So, We want to actually return one document for each tag. We can specify that as:

So, running the follow code would generate:
```
db.article.aggregate(
    { $project : {
        author : 1 ,
        title : 1 ,
        tags : 1
    }},
    { $unwind : "$tags" });
```
**Result:**
```
{
     "result" : [
             {
                     "_id" : ObjectId("4e6e4ef557b77501a49233f6"),
                     "title" : "this is my title",
                     "author" : "bob",
                     "tags" : "fun"
             },
             {
                     "_id" : ObjectId("4e6e4ef557b77501a49233f6"),
                     "title" : "this is my title",
                     "author" : "bob",
                     "tags" : "good"
             },
             {
                     "_id" : ObjectId("4e6e4ef557b77501a49233f6"),
                     "title" : "this is my title",
                     "author" : "bob",
                     "tags" : "fun"
             }
     ],
     "OK" : 1}
```

We use unwind - it deconstructs

#### Virtual Properties

They are like temporary fields that are not stored on the database but can be shown to the users each time they do some requests. For ex: we might want to show users the save percentage for each items if there is a discount. This kind of states are not stored in database but displayed as a GO when user gets something (   queries).

So, how do we do it ?

We define on our scheme that our virtuals to be true. In our schema, where we pass the first objet as the data , we can pass second object specifying our virtal properties to be true.On second parameter, we pass the below code as object.
```
{
        toJSON: { virtuals: true },
        toObject: { virtuals: true }
    }
```

We can specify our virtual properties as:

```
tourSchema.virtual('durationWeeks').get(function() {
    return this.duration / 7;
});

```


**NOTE:** The virtual properties cannot be queried. For ex: we cannot query our data such as " give me all data that have durationWeeks less than 3 or something like that".It is not possible.

#### Mongoose MIddleware (Document  Middleware)

**Note** (we've already done this - for using bcrypt our password ) 😉 

We have different kinds of middleware - `pre` and `post`. The pre will run just before saving the document to the collection while post will run after the completion of saving.
 
**Note:** In Document Middleware (**`this`**)  points to the current document (save ma document lai point garxa this keyword le).

However, in the case of Query Middleware, (**`this`**) keyword points to the current query that is being executed.

Similarly, (**`this`**)  in Aggregate Middlware points to the current agregate object.