

##Introduction

In order to use Ephestos, Ephestos needs first to be installed



###1\.  How to install Ephestos

To use Ephestos , Ephestos must be installed both in Blender and in Pharo\.

Pharo can be found here

[http://pharo\.org/](http://pharo.org/)

Blender can be found here

[http://www\.blender\.org/](http://www.blender.org/)



####1\.1\.  Install Ephestos in Blender

Download Ephestos from its github repo here, clicking on the "download zip"

[https://github\.com/kilon/pyEphestos](https://github.com/kilon/pyEphestos)

Decompress the zip file and it should give you an "Ephestos" folder , if not rename the folder\. Copy paste the folder inside the folder that your Blender is putting addons\.

Open in Blender and go to File menu `>` User Preferences in addons tab, you will find Ephestos in Development tab\. Enable it\.

Now go to the 3d View window \(where your 3d objects are displayed\)  , then go to 3d view window and find Ephestos on the left panel and click "enable Ephestos"\. If the panel is closed then just press t shortcut to make it appear\.

<a name=""></a><figure><img src="figures/ephestos-addon-gui.png" width="40%"></img><figcaption>Ephestos addon GUI</figcaption></figure>

The Ephestos GUI should be display "Listening" meaning that the socket server is enabled and listening for any connection from Pharo\.



####1\.2\.  Install Ephestos in Pharo

To install Ephestos in Pharo just open a workspace and do this



<a name="installEphestos"></a>**Install Ephestos in Pharo
**

```smalltalk
Gofer new smalltalkhubUser: 'kilon' project: 'Ephestos' ; package: 'Ephestos'; load.
```



You don't have to enable Ephestos in Pharo because each command you send with it is actually a socket that opens a connection, sends a string with the python command and then closes it\.




###2\.  Using Ephestos

Ephestos can be used in two ways, one is passing python syntax directly to Blender and another is using Pharo syntax that is converted to python and passed to Blender to be executed\.



####2\.1\.  Using python syntax

So all you have to do is use the Ephestos sendMessage:  , passing the python command as a pharo string\.

First I advice you to resize your pharo window and blender window so they are side by side to see the result of your command because Ephestos is very fast\.

For example if you want to delete the default Cube or any object you selected do this in the workspace



<a name="deleteObjectSimple"></a>**Delete a selected Blender object
**

```smalltalk
Ephestos sendMessage: 'bpy.ops.object.delete()'.
```



if you want to add back a Cube do this



<a name="addCube"></a>**Add a Cube Mesh in Blender
**

```smalltalk
Ephestos sendMessage: 'bpy.ops.mesh.primitive_cube_add()'.
```



The string passed can be any kind of python command and not limited just to Blender Python API\.



####2\.2\.  Using the Python Parser

Also Ephestos comes with a Pharo parser, the parser takes normal pharo syntax and converts it to Python so you no longer need to use a string with python syntax for example the previous two command with the parser will be as following\.



<a name="deleteAddCube"></a>**Delete the cube and add it back using Pharo syntax
**

```smalltalk
EphCParser bpy ops object delete:'()';e.
EphCParser bpy ops mesh primitive_cube_add:'()';e.
```



Pharo will complain that bpy , ops etc do not exist \. Thats normal because those messages really dont exist \. A menu will pop up for each message that does not exist\. Accept the first top option for each message\. Next time you use the non existent message pharo should not complain\.

 I have hacked the object so any message that does not exist passes it as a python string using Ephestos sendMessage\. This mean you can also send invalid objests or even invalid python syntax\. If what you send causes a python error it will send the error back to Pharo and open the Pharo  debugger to show you exactly where the error is located\.

The e message marks the end of your command , its useful when then messages sends are more complex\. For example



<a name="changeMaterial"></a>**Change the diffuse color of the active material of object named Cube
**

```smalltalk
EphCParser bpy data objects: '["Cube"]'; active_material; diffuse_color:'=(1.0 , 0.0 , 0.0)' ; e.
```



This will simply change the diffuse\_color of the active material of the object named "Cube"\. Observe that we pass active\_material as ; active\_material ; this is normal Pharo syntax because of how Pharo prioritize messages\. You could also use parentheses



<a name="changeMaterialParen"></a>** Change diffuse color but this time using parentheses instead of cascade
**

```smalltalk
((EphCParser bpy data objects: '["Cube"]') active_material) diffuse_color:'=(1.0 , 0.0 , 0.0)' ; e.
```



but I prefer cascades since they are easier to type \. You can also use parentheses and cascades for every message including bpy and ops\.

But bpy and data do not need parentheses or cascades because they are unary messages and already Pharo knows that are separate messages\.

So as you see we use  Pharo syntax and yet we execute Python code\.

Actually Ephestos can be used to execute any regular Python code\. Making it possible to use any python library from Pharo and not just Blender\. I am creating also a standalone library called Atlas that will allow Pharo to use any kind of Python library without Blender\.

Next step is trying to wrap also more complex python syntax like Python classes and Python functions definitions\. I will also implement the ability to send multiple lines of python commands which is useful for things that have long definitions of classes like definitions of blender operators , blender panels etc\. Using however this existing syntax you can already access all blender properties and blender operators so Ephestos is already very powerful\.

I hope you found it easy enough to understand, if not , then you can ask any questions by sending me an email at kilon\.alios@gmail\.com
