# Compiling CoffeeScript from Sublime Text 2
A few days ago I configured Sublime Text 2 editor to use three different hotkeys for compiling and running CoffeeScript files. I am new to Sublime so I consider somewhat of an achievement that I was able to pull this off with help from the interwebz and a bit of luck.

## Run a CoffeeScript file in the Sublime console

The first hotkey that I configured runs a CoffeeScript file and displays its output to the Sublime’s console window. This is useful when I want to quickly run a small CoffeeScript program that outputs to the console. The following screenshot shows this:

![Sublime Text Console](https://hectorcorrea.com/images/sublimetextconsole.png)

To wire up this hotkey I created a new *Build System* for CoffeeScript files. You can do this via the Tools + Build System + New Build System menu option. Copy the following code inside the editor and save it as CoffeeScriptRun.sublime-build in the default directory (in my Mac the path was `~/Library/Application Support/Sublime Text 2/Packages/User`)

For Mac

```code
{
    "cmd": ["coffee", "$file"],
    "selector" : "source.coffee",
    "path" : "/usr/local/bin"
}
```

For Windows

```code
{
    "cmd": ["coffee", "$file"],
    "selector" : "source.coffee"
}
```

From now on, when I hit **command+B** (?+B) while editing a CoffeeScript file, Sublime automatically calls the CoffeeScript compiler with the current opened file. Command+B is the default key in Sublime Text to run the build command. You can also run this command from the menu by going to Tools + Build. The output of the CoffeeScript will be displayed in the Sublime Text console at the bottom of the screen.


## Generate the JavaScript file for a CoffeeScript file

The second command that I configured was to re-generate the JavaScript file associated with a CoffeeScript file. 

One easy way to do this is to create another build file like I did above but use a slightly different command to tell the CoffeeScript compiler to produce a JavaScript file. The disadvantage of this approach is that then I have to toggle between build modes via the Tools + Build System and then use command+B to produce the JavaScript file. This is just too many steps for something that I need to do quite often.

What I really wanted was a hotkey to re-generate the JavaScript file in one shot. To do this I had to create a new key binding (Sublime Text wording for hotkey) by going to the Sublime Text 2 + Preferences + Key bindings - User and entering the following text:

```code
[
    { "keys": ["super+j"], "command": "coffeescript_to_javascript" }
]
```

This tells Sublime to wire the **command+J** key to a command called “coffeescript_to_javascript”. The next step is to create the code associated with “coffeescript_to_javascript”. To do this I went to Tools + New Plugin... menu and replaced the default code with the following:

```code
import sublime, sublime_plugin

class CoffeescriptToJavascript(sublime_plugin.TextCommand):
  def run(self, edit):
    self.view.window().run_command('exec', {'cmd': ["coffee", "-c", self.view.file_name()]})
```

Two things to notice in this code. One is that the name of the class (CoffeescriptToJavascript) is the CamelCase version of the name of the command (“coffeescript_to_javascript”) that I defined in the key bindings. This is very important as Sublime will automatically look for a class with the CamelCase version for the value defined in the key bindings.

I wish I could remember where I stole the idea for this code, I know it was somewhere in the [Sublime message board](http://www.sublimetext.com/forum/) but I didn’t save the link.

The second thing to notice is that the run command in this code is calling the CoffeeScript compiler (coffee) and passing both the `-c` flag to produce the JavaScript and the name of the file to compile (which will be the name of the current file being edited.) 

I saved this plugin file as `CoffeeScriptCompile.py` in the same location where you saved the build file that we created before. I restarted Sublime to make sure the new plugin was loaded.

From now on, when I use **command+J** Sublime runs `coffee -c somefile.coffee` and generates the corresponding `somefile.js` file. This command does not output the result to the console, though. 


## Outputting the JavaScript code to the Sublime Console

Lastly, using the steps defined in the previous section I created another plugin to output the JavaScript to Sublime’s console rather than to a file. This is very useful when I want to dig and see how a particular CoffeeScript feature is translated to its JavaScript equivalent. 

I wired this command to the **command+M** key and pass the `-p` flag to the CoffeeScript compiler to get the JavaScript to the standard output.