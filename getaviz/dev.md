# Developement
---
I'm working on windows and some problems are just because of it.
<br>
In Getaviz there is Generator and UI part. So if you want to change UI, you have to work with **UI** forlder and if you want to edit the generator, go to the **generator2** folder.  

## Generator

### How do I see my changes?
Currently there is no output in the console from the generator. BUT, what you have is a log file, which you can find at your **`rep clone\generator2\logs\jetty.log`**. 

To write something in that log file, use simple method:
```java
log.info("Your text")
```

!!! After you did some changes, don't forget to repack the project. 

### Repack the project
Open command line and go to the `**generator2\org.getaviz.generator**` folder.
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
* Now, go to the "setup" folder in your UI root folder and find the setup which is currently used by you. Add your controller there. (Actually in any place. It depends on what your controller does and if it must be displayed somewhere).

