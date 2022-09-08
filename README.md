# Wallpaper-Engine-Tutorial-LayerInstantiation
Tutorial Code for Wallpaper Engine on how to create new Layer per script

How to instantiate / spawn / create new Layers / Objects per Script

Preface: This is a growing Tutorial. Feel free to leave feedback and I will update this Tutorial if needed. :)

1.0: Create an Object you want to duplicate. In this Chapter I will use Blender to make a 3D-Model, but you can take anything you want (skip to step 1.3). It doesn't matter if it's an image, text, solid or whatever. It only needs to be a "Layer".

1.1: BLENDER: Set Transform -> Location X to "1", Y to -1, Z to 1.

1.2: BLENDER: File -> Export -> Wavefront(.obj). I named it "cube.obj".

1.3: Back to Wallpaper Engine: Add Asset -> Model -> OK -> open up "cube.obj".

1.4: Set Name to "cube". Or whatever you like. 
Set Origin+Angles to all 0s. You can choose the Scale of the cube. 
I took 0.1 for x, y and z.

2.0: Add Asset -> Solid Layer (Resolution: 0x0, Background color: black) -> OK.

2.1: Name it whatever you want. 
This will be your Parent/Manager to control and instantiate your duplicates. 
I called it "Overseer". Set Origin, Angles and Scale to all 0s. (optional).

2.2: Click one of the gears in the Properties-tab and bind a script. 
It doesn't matter which gear you use. 
I chose the visibility script, because it's the first in the column. 
Delete everything except
[code]‘use strict';[/code].

2.3: Snippets -> insert Function -> init. 
This function is called once, when this Layer gets initialized, before the first "update". (more to update, later)

2.4: Add following line: [code]let bp= thisScene.getInitialLayerConfig("0");[/code]
"let bp" declares a new variable called "bp", which will stay in memory, until the function ends. 
"thisScene" is a global variable to get the Scene this object is currently in.
".getInitialLayerConfig()" is a function to read the saved configuration from "scene.txt" in your project-folder.
"0" The name between doubleqoutes needs to be identical with the name of the cube.

2.5: Add line: [code]shared = new Object({ blueprint: blueprint });[/code]
"shared" is a global variable, like thisScene. You have to initialize it with the contents you will use later on.
"= new Object()" initializes an Object. ()
"{ blueprint: bp}" content of the new Object. A variable "blueprint", which contains the blueprint (bp) from the last step.

2.6: Add line: [code]const TIME_BETWEEN_GEN = 0.2;[/code] 
(at the top of your script, right beneath "'use strict';"): 
Decide how much time you want between the creation of each cube. I chose 0.2 seconds. 
A constant can't be changed after decleration but the perfomance profits from shorter reading timings. The names of constants are usually written in CAPSLOCK with _ as seperator.

2.7: Add line: [code]var timeCounter = 0.0;[/code] 
(below the constant)
This variable will count the time between each frame.

2.8: Snippets -> insert Function -> update.
The update function is called once before every frame. Here is where the "moving" part happens.
But try to keep it short and simple here. Too much calculations in every frame, will result in lower FPS.

2.9: Add line: [code]timeCounter += engine.frametime;[/code]
"timeCounter" is the variable we declared at step 2.7.
"+=" adds the content of the right side of the expression, to the variable on the left side.
"engine" is the global object to address general information about the system of the wallpaper user and the running wallpaper.
".frametime" is the past time since the last frame. (with 30 FPS, the frametime between frames will be 1/30 = 0.03333 seconds.)
This line adds the time that has passed since the last frame, to the timeCounter variable.

2.10: Add line: [code]if (timeCounter >= TIME_BETWEEN_GEN) { }[/code]
"if () {}" If the content in the round brackets is TRUE, do whatever is in the curly brackets.
"timeCounter >= TIME_BETWEEN_GEN" check if the timeCounter is bigger or equal to the constant TIME_BETWEEN_GEN.
	
2.11: Add line: [code]let blueprint = shared.blueprint;[/code] (into the curly brackets of the if-statement)
"let blueprint" declares a new variable, called blueprint. You can name variables as you want.
"= shared.blueprint" sets the variable blueprint as a pointer (shortcut) to the blueprint-variable, which we made in the init() function. 

2.12: Add line: [code]thisScene.createLayer(blueprint);[/code]
"thisScene" is a global variable to get the Scene this object is currently in.
".createLayer()" is the function to create new Layer!
"(blueprint)" tells the createLayer()-function what we want to create/duplicate. In our case, the blueprint.
Note: All the variables we made until now were just pointer. You could write it as one line too, instead of declaring new variables to hold pointers. But this way it's more readable and will save the engine some searching-time.

2.13: Add line: [code]timeCounter = 0.0;[/code] 
Set the variable timeCounter back to 0.0, to restart counting from 0.

2.14: Lets test it and summarize what we have accomplished. Click "Compile" (if you made syntax errors, this will be marked now) and "OK", to close the editor. If you havn't activated "Show Stats" (the stats display in the right corner), then you should do it now, for further testing.
Press "Run Preview" to run your scripts. (note: your scripts won't show any effects, until you press this button) and watch "Draw calls" and "Primitives drawn".
If everything made correct, those numbers will grow every second.
Congrats! You are generating cubes! 
	
In line 3 the time between the creation of cubes, is declared as a constant. 
Then the initialization begins with finding the object named "0" and save it as a blueprint in the global object "shared" (which needs to be declared once and only once for all your scripts).
Each Frame, the update function gets called and the timeCounter rises for the amount of passed time, since the last frame.
If the time passed, which we declared as a constant, a new cube gets created and the timer is set back to 0.
	
Unfortunately all cubes will share the same origin (position): x = 0, y = 0, z = 0.
If you change the origin of the blueprint-cube "0", all generated cubes will spawn at the same position.
In the next steps, we will manipulate the blueprint in each loop, to place the cubes at random positions.

3.0: Select the cube "0" and press the gear-button at "Origin" and then "bind script". Again, delete everything except "'use strict';". Press compile and OK to close it.
This step was necessary to change the mode of this parameter in the way, wallpaper engine reads your project from your file and what you have to write in the future to adress the value of the origin-value.
To understand what happens in the background, open your project in the explorer and then open the "scene.json" file with Editor.
First section are the camera settings. Second section is for general settings, which you can find under "Scene options".
The third section "objects" is what we're looking for. It should contain 2 big objects with a lot of variables per object.
Search for the variable "name". One will have the name "Overseer" or the name you chose in step 2.1. The other will have the name "0". That's the cube.
Behind the scenes, the content of this object in this scene.json file, will be duplicated, with the script.
In the next steps we will manipulate this variables before duplicating it.
In this step we had to bind a script to the origin-parameter. 
This binding has changed how this object saves the "origin" parameter: in an additional object, marked with { }, having 2 additional child-variables: "script" and "value". 
Without script-binding, the scale-variable would look like "angles" or "scale", containing only one string of numbers "0.000 0.000 0.000".
Later, we will change the "value" variable in the "origin"-parameter with a script.	
Close the Editor, but you can open it anytime again for further orientation on how the variables are called.
Keep in mind that if you save this scene.json in the Editor, it will save the version you have open. 
If you change anything in the Wallpaper Editor, the changes won't be updated in the Editor version of scene.json. You could lose all changes, when saving the wrong version. 


3.1: Select the Overseer and open his script again. Add a new line at the bottom: "function randomOrigin() {}" (And press Enter between the curly braces).

3.2: Add those lines: [code]let x = Math.random();[/code]  [code]let y = Math.random();[/code]  [code]let z = Math.random();[/code]
Each declares the variable x and y and sets them to a random number between 0.0 and 1.0.

3.3: Add line: [code]return  x + " " + y + " " + z;[/code]
"return" indicates to return/place the result to where this function was called.
The rest of the function constructs a string in the format, which will be accepted by the engine: "x.xxxx x.xxxx x.xxxx".

3.4 Add line: [code]blueprint.origin.value = randomOrigin();[/code] right, after declaring the blueprint, in the if-statement.
This line sets the origin position to the result of randomOrigin(). 
Click the compile button, to check for syntax errors and run the preview.
You should now see the cubes spawning on different positions, filling up a cubic space. (between 0.0 and 1.0 on x, y and z)
To see what coordinates where choosen, add the line [code]console.log(blueprint.origin.value);[/code].
By running the preview, your console will be spammed with random coordinates, each time a cube gets created.
Delete the console.log() line to disable the message.

3.5 Add Asset -> Light -> OK
Select the light and move it's origin's Y to 2 and Z to around 1.5
Press Run Preview again. Now you can see the cubes better. :)

If you create new Layer, you have to destroy them too, in order to clean up. One way is to make an array in the Overseer script and add each cube to this array, when creating them. To delete them, you could iterate this array and destroy each cube. :)
Or the other way: Let the cubes destroy themself, after a while.

4.0 Open the origin-script of the cube and add a init function: Snippets -> Insert Function -> init.
Add following line:
[code]engine.setTimeout(function() { thisScene.destroyLayer(thisObject); }, 2000);[/code]

"engine" is the global object to address general information about the system of the wallpaper user and the running wallpaper.
".setTimeout()" is a function to let the engine wait for some time. Inside the brackets, there is another function, which will be called, when the time is over: .setTimeout(function, time-in-milliseconds)
A function without name, which gets called from within another function is called "anonymous function".
"thisScene" is again, the global object of the scene.
".destroyLayer()" destroys a Layer.
"thisObject" is a global object and a reference for the cube to itself.
"2000" is the time in milliseconds, until the anonymous function will be called.
Press Compile and Run the preview.
The wall of cubes should disapear after 2 seconds and free up the memory.

Congratulations! Now you know the basics of generating layers with a script! :)













