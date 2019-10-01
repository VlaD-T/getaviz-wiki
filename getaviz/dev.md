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

## UI
So far so good.  

