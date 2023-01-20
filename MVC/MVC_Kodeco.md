https://www.kodeco.com/1000705-model-view-controller-mvc-in-ios-a-modern-approach

The Model (M)
The model layer encompasses your app’s data. 

Network code: 
You preferably only use a single class for network communication across your entire app. 
It makes it easy to abstract concepts common to all networking requests like HTTP headers, response and error-handling and more.

Persistence code: 
You use this when persisting data to a database, Core Data or storing data on a device.

Parsing code: 
You should include objects that parse network responses in the model layer. 
For example, in Swift model objects, you can use JSON encoding/decoding to handle parsing. 
This way, everything is self-contained and your network layer doesn’t have to know the details of all your model objects in order to parse them. 
Business and parsing logic is all self-contained within the models.

Managers and abstraction layers/classes: 
Everyone uses them, everyone needs them, nobody knows what to call them or where they belong. 
It’s normal to have the typical “manager” objects that often act as the glue between other classes. 
These can also be wrappers around lower-level, more robust API: a keychain wrapper on iOS, a class to help with notifications or a model to help you work with HealthKit.

Data sources and delegates: 
Something that may be less common is developers relying on model objects to be the data source or delegate of other components such as table or collection views. 
It’s common to see these implemented in the controller layer even when there’s a lot of custom logic that’s best kept compartmentalized.

Constants: 
It’s good practice to have a file with constants. 
Consider putting these within a structure of structures or variables. 
That way, you can reuse storyboard names, date formatters, colors, etc.

Helpers and extensions: 
In your projects, you will often add methods to extend the capabilities of strings, collections, etc. 
These too can be considered part of the model.
There are more classes and objects you could include, but these seem to be the most common. 
Again the goal is not to focus too much on the semantics of what is or isn’t a model. 
Rather, it is to have a solid foundation upon which to get your work done.

The View (V)
When users interacts with your app, they are interacting with the view layer. 
It should not contain any business logic.

Typical code smells found in this layer manifest in different ways but boil down to include anything unrelated to UI in your view layer. 
This can include network calls, business logic, manipulating models and so on.

Use the following as a checklist when inspecting your view layer:

Does it interact with the model layer?
Does it contain any business logic?
Does it try to do anything not related to UI?

Of course, these rules aren’t written in stone and sometimes you’ll need to bend them. 
Nonetheless, it’s important to keep them in mind.



The Controller (C)
The controller will use all the elements in your model layer to define the flow of information in your app.

Usually, you’ll see classes from this layer deciding things like:

What should I access first: the persistence or the network?
How often should I refresh the app?
What should the next screen be and in which circumstances?
If the app goes to the background, what should I tidy up?
The user tapped on a cell; what should I do next?
