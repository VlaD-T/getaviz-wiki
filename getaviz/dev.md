# Developement
---
I'm working on windows and some problems are just because of it.
<br/>
In Getaviz there are Generator and UI parts. So if you want to change UI, you have to work with **UI** forlder and if you want to edit the generator, go to the **generator2** folder.  

## Generator

### How can I see my changes?
Currently there is no output in the console from the generator. BUT, what you have is a log file, which you can find at your **`rep clone\generator2\logs\jetty.log`**. 

To write something in that log file, use simple method:
```java
log.info("Your text")
```

!!! After you did some changes, don't forget to repack the project. 

### Repack the project
Open command line and go to the **`generator2\org.getaviz.generator`** folder.
Run: 
```java
mvn package
```

**Important** 
If you try to rebuild the project with the followign command, than changes won't be applied. So use "mvn package". 
```java
docker-compose build --no-cache backend
```

## UI
### How to create a controller?
To create a controller you simply have to create a js script. 
The important question is, how to bind it. So, the steps are as follows: 

* Create a js script in scripts folder;
* Create a function that will hold your controller. It may look like this: 

```javascript
var yourNewController = (function () {

    let controllerConfig = {
    };

    function initialize(setupConfig) {
        application.transferConfigParams(setupConfig, controllerConfig);
    };

    async function asyncFunctionExample(pyaload) {
        return console.log(payload);
    };

    return {
        initialize: initialize,
        asyncFunctionExample: asyncFunctionExample
    };
})();
```

* Afterwards you have to bind your script to the index.php file. Find the section there, where all scripts are added and add your own controller. 
* 1. If your controller is **global**, then add it in **html part of index.php** file. 
* 2. If your controller is build for **specific framework** (like A-Frame), then add it in **php part of **index.php file.
* Now, go to the "setup" folder in your UI root folder and find the setup which is currently used by you. Add your controller there. (Actually in any place. It depends on what your controller does and if it must be displayed somewhere).


### Setting the default parameters
If you want to add some configs, there are 2 files you have to make changes to:
1. Your controller file. Add some properties to your `controllerConfig` object. This will be your default configuration. 
2. Setup file. Go to **setups** folder and find the setup file you are using. Find the place where your controller is added and add parameters to it. 
It may look like this:
```javascript
...
{ 	name: 	"relationConnectorController",
    fixPositionY : false,
    showInnerRelations : true,
    sourceStartAtParentBorder : false,
    targetEndAtParentBorder : false,
    sourceStartAtBorder: true,
    targetEndAtBorder: true,
    createEndpoints : true
},
...
```
After this your default controller configuration will be overwritten after controller initialization. 
If your configurations is not overwritten, check the `Application.js` file. Make sure, that your controller configs and methods are called after the **application** was initialized. 
```javascript
...
//initialize application
application.initialize();
...
```


### How to react on events from other controllers? 
Almost every controller publishes some events. 
Take as an example **relationTransparencyController** and look at the **initialize** function. 

```javascript
function initialize(setupConfig) {
    application.transferConfigParams(setupConfig, controllerConfig);
    events.selected.on.subscribe(onRelationsChanged);
}
```

Take a look at the second line `events.selected.on.subscribe(onRelationsChanged);`. Here we subscribe this controller to the `selected.on` event. After this event is triggered, the `onRelationsChanged` function will be called. So each and every controller, which is subscribed to this event will fire some function. 

### How to fire/publish some event?
To fire some event, so that other controllers may react on that, you have to use `publish` method. 
Here are three different examples:

```javascript
events.filtered.on.publish(applicationEvent);
events.filtered.off.publish(applicationEvent);
events.selected.on.publish(applicationEvent);
```
<br/>

What is it for `applicationEvent`? This is the payload which you want to pass with the event. Take a look at the **packageExplorerController**.
```javascript
function zTreeOnClick(treeEvent, treeId, treeNode) {
    var applicationEvent = {
        sender: packageExplorerController,
        entities: [model.getEntityById(treeNode.id)]
    };
    events.selected.on.publish(applicationEvent);
}
```
<br/>

Another example, from the same controller:
```javascript
function zTreeOnCheck(event, treeId, treeNode) {
    var nodes = tree.getChangeCheckedNodes();
    
    var entities = [];
    nodes.forEach(function(node){
        node.checkedOld = node.checked; //fix zTree bug on getChangeCheckedNodes	
        entities.push(model.getEntityById(node.id));
    });
                            
    var applicationEvent = {			
        sender: 	packageExplorerController,
        entities:	entities
    };
    
    if (!treeNode.checked){
        events.filtered.on.publish(applicationEvent);
    } else {
        events.filtered.off.publish(applicationEvent);
    }		
}
```

### Where to find all the events?
The file you need is nested in **Scripts** folder, in **UI** and is called **Model.js**. 
There is also another file called **Events.js**, but it's not what you need. Event.js file has the logic, how to process the events.

So, we need **Model.js**, let's find a place with `states`.
```javascript
//states
const states = {
    selected 		: { name: "selected" },
    marked 			: { name: "marked" },
    hovered 		: { name: "hovered" },
    filtered 		: { name: "filtered" },
    tmpFiltered     : { name: "tmpFiltered"},
    added			: { name: "added" },
    componentSelected : { name: "componentSelected" },
    antipattern     : { name: "antipattern" },
    versionSelected : { name: "versionSelected" },
    loaded			: { name: "loaded" }
};
```
These states are exactly the events, which can be published and triggered. 

### How to create a new event?
In case you want to create a new event, just add a new state in **Model.js** file, in the `states` section. 
```javascript
//states
const states = {
    ...
    myNewState		: { name: "myNewState" }
};
```
After this you can publish and trigger this event in controllers, with:
```javascript
events.myNewState.off.publish(functionWhichMustBeCalled);
```
