# Vim Commands
This isn't going to be a guide like the others, this is a reference page of various vim commands that serve specific purposes. I will be coming back to add to this over time. This page will still teach you some vim, but you should really learn vim from vimtutor. If you have vim installed on your system, just run the following command in your terminal and an interactive tutorial will start:
> vimtutor

### Modes
An important thing I will likely keep bringing up is what mode you are in to perform a certain action. You can only run certain commands and perform certain tasks in a specific mode, so I want to get this out of the way to prevent any confusion.

The program, vim, starts in what is called **normal mode**, where you can only read the document, not insert text. Nearly every key serves a purpose in vim, and in normal mode, pressing these keys runs commands. While in normal mode, you can press the hjkl keys to move around the document. You can press backspace to move backwards on the current line. You could even press ```:``` to enter command mode.

Command mode allows you to run macros, replacements, modifiers, etc. on your document. 

Visual mode allows you to select text. You press ```v``` to enter visual mode, and you use the cursor keys to highlight which text you want to focus on.

Insert mode is entered by pressing the ```insert``` or ```i``` key. In this mode, you can edit text. You're basically in a word processor state.

Replace mode is insert mode, but everything you type replaces wherever your cursor sits at in the buffer. Enter replace mode by pressing ```insert``` twice.

Now you should know what I mean whenever I mention these modes.


### Removing a set of characters
Let's say you want to remove the first character, or the first five characters of each line in vim. You would need to use a regular expression like so:
```:%s/^.\{0,5\}//g```

Let's break this down:

```%``` is for a range. A range permits a command to be applied to a group of lines in the current buffer.

The s in %s means substitution, as we are going to be replacing characters. In this case, replacing them with nothing.

After the ```%s```, there is a forwardslash ```/```, and then you'll notice another forwardslash ```/``` after the last brace ```}```, and then another foward slash. The text between the first two forwardslashes, which is ```^.\{0,5\}```, is what we are trying to match in our expression. We are looking for the first five characters of every line, and then between the second and third forwardslashes is what we want to replace the first five characters with. In this case, it's nothing, so there is just nothing between the second and third forwardslash. If I wanted to replace the first five characters with a single astiresk ```*```, my whole expression would be ```:%s/^.\{0,5\}/*/g``` intead. Lastly, what comes after the last forwardslash is your specifier for the document. You can remove the ```g``` after the last forwardslash to affect only the line your cursor is on, or you can target the whole document with a ```/g```. More on that in a bit.

```^``` is the beginning of the line. It's not the first character, but it means we're going to look for something from the start of the line/lines. If it were ```$```, it would be the end of the line. If we were to run ```:%s/^.//g``` we would remove the first character at the beginning of each line. If we were to run ```:%s/$.//g``` we would remove the first character of the last word at the end of every line. We could also rewrite ```:%s/^.\{0,5\}//g``` as just ```:%s/^......//g```, but this is a very primitive way to get the job done.

```.``` is any character, and it doesn't matter what that character is. There will be more on this later, as there are many uses for ```.``` in other contexts.

```\``` is just so we can use an escape character. When we use this in vim commands or regular expressions, it means we're not looking for a character, we're trying to do something else like find a range of characters with braces in this case.

```\{0,5\}``` as a whole means characters 0 to 5 are what we are looking for. The 0th character is really nothing. It's just good practice to start from 0, but you could honestly change our overall expressing to ```:%s/^.\{1,5\}//g```. If I had the word #Server on the current line of my document, and I ran ```:%s/^.\{1,1\}//``` I would literally just be deleting the # from #Server. Again, remove the first character is done quicker with ```:%s/^.//``` on the current line, and add a g at the end to remove the first character on all lines. Play with these concepts yourself in vim, and remember that pressing the u key in normal mode undoes actions if you want to keep playing around with expressions and starting over without retyping things.

Lastly, the ```//g```. As I mentioned above, the text between ```//``` is what we're going to use to replace what comes earlier. You could put anything in there, but in our use case we just want to remove the characters. It's still called a replace, but we're just replacing with nothing. And again, the ```/g``` means every line on the file. Without ```/g```, you just affect the line your cursor is currently on.


