Chapter 11: Text Editors
------------------------

[11.0: Introduction/ Learning Objectives](#110-introduction-learning-objectives)  
[11.1: Basic Editors: nano and gedit](#111-basic-editors:-nano-and-gedit)  
[11.2: More Advanced Editors: vi and emacs](#102-more-advanced-editors-vi-and-emacs)  
[11.3: Summary](#113-summary)  
  
### 11.0: Introduction/ Learning Objectives
----
By the end of this chapter, you should be familiar with:
* How to create and edit files using the available Linux text editors.
* **nano**, a simple text-based editor.
* **gedit**, a simple graphical editor.
* **vi** and **emacs**, two advanced editors with both text-based and graphical interfaces.

### 11.1 Basic Editors: nano and gedit
----
#### Overview of Text Editors in Linux

#### Creating Files Without Using an Editor
Two ways:  
1. **echo** repeatedly  
   * `$ echo line one > myfile`  
   * `$ echo line two >> myfile`  
   * `$ echo line three >> myfile`  
2. **cat** with redirection  
   * `$ cat << EOF > myfile`  
   * `> line one`  
   * `> line two`  
   * `> line three`  
   * `> EOF`  
   * `$`  

#### nano and gedit
* **nano** is easy
* **gedit** is part of the **GNOME** desktop system
* **kwrite** is associated with **KDE**

#### nano

#### gedit
* far more capable than Notepad
* very configurable
* lots of plugins

#### Lab 11.1a (Lab 1): Using nano
1. Start nano by typing nano myfile.txt.
2. Add your name in the first line of the file.
3. Add the date in the last line of the file.
4. Close the file.  

Okay  

#### Lab 11.1b (Lab 2): Using gedit
1. Start gedit by typing gedit myfile.txt.
2. Add your street address in the second line of the file.
3. Add the name of your city on the last line of the file.
4. Close the file.  

Okay  

### 11.2 More Advanced Editors: vi and emacs
----
#### vi and emacs  
* steep learning curve but more efficient once learned  

#### Introduction to vi
* learn it
* GNOME extends it with **gvim** and KDE offers **kvim**

#### vimtutor
* `$ vimtutor` launches a short but comprehensive tutorial!

#### Modes in vi
* keep track of your modes!  

Mode    | Feature
----    | -------
Command | By default, vi starts in Command mode. Each key is an editor command. Keyboard strokes are interpreted as commands that can modify file contents.  
Insert  | Type `i` to switch to Insert mode from Command mode. Insert mode is used to enter (insert) text into a file. Insert mode is indicated by an “-- INSERT --” indicator at the bottom of the screen. Press `Esc` to exit Insert mode and return to Command mode.
Line    | Type `:` to switch to the Line mode from Command mode. Each key is an external command, including operations such as writing the file contents to disk or exiting. Uses line editing commands inherited from older line editors. Most of these commands are actually no longer used. Some line editing commands are very powerful. Press Esc to exit Line mode and return to Command mode.

#### Working with Files in vi
* press `ENTER` after each of these commands  

Command      | Usage
-------      | -----
vi myfile    | Start the vi editor and edit the myfile file
vi -r myfile | Start vi and edit myfile in recovery mode from a system crash
:r file2     | Read in file2 and insert at current position
:w           | Write to the file
:w myfile    | Write out the file to myfile
:w! file2    | Overwrite file2
:x or :wq    | Exit vi and write out modified file
:q           | Quit vi
:q!          | Quit vi even though modifications have not been saved

#### Changing Cursor Positions in vi
* press `ENTER` after line mode commands  

Key                 | Usage
---                 | -----
arrow keys          | To move up, down, left and right
j or <ret>          | To move one line down
k                   | To move one line up
h or Backspace      | To move one character left
l or Space          | To move one character right
0                   | To move to beginning of line
$                   | To move to end of line
w                   | To move to beginning of next word
:0 or 1G            | To move to beginning of file
:n or nG            | To move to line n
:$ or G             | To move to last line in file
CTRL-F or Page Down | To move forward one page
CTRL-B or Page Up   | To move backward one page
^l                  | To refresh and center screen

#### Using Modes and Cursor Movements in vi
(video)  

#### Searching for Text in vi

Command  | Usage
-------  | -----
/pattern | Search forward for pattern
?pattern | Search backward for pattern

Key | Usage
--- | -----
n   | Move to next occurrence of search pattern
N   | Move to previous occurrence of search pattern

#### Working with Text in vi

Key 	   | Usage
---        | -----
a          | Append text after cursor; stop upon Escape key
A          | Append text at end of current line; stop upon Escape key
i          | Insert text before cursor; stop upon Escape key
I          | Insert text at beginning of current line; stop upon Escape key
o          | Start a new line below current line, insert text there; stop upon Escape key
O          | Start a new line above current line, insert text there; stop upon Escape key
r          | Replace character at current position
R          | Replace text starting with current position; stop upon Escape key
x          | Delete character at current position
Nx         | Delete N characters, starting at current position
dw         | Delete the word at the current position
D          | Delete the rest of the current line
dd         | Delete the current line
Ndd or dNd | Delete N lines
u          | Undo the previous operation
yy         | Yank (copy) the current line and put it in buffer
Nyy or yNy | Yank (copy) N lines and put it in buffer
p          | Paste at the current position the yanked line or lines from the buffer.

#### Using External Commands, Saving, and Closing in the vi Editor
(video)

#### Using External Commands  
* typing  `: sh` opens an external command shell  
* typing `: ! ` executes a command from within vi; example:  
   * `: ! wc %` runs `wc` (word count); `%` represents current file  
   * best for non-interactive commands  

#### Introduction to emacs

#### Working with emacs

Key           | Usage
---           | -----
emacs myfile  | Start emacs and edit myfile
CTRL-x i      | Insert prompted for file at current position
CTRL-x s      | Save all files
CTRL-x CTRL-w | Write to the file giving a new name when prompted
CTRL-x CTRL-s | Saves the current file 
CTRL-x CTRL-c | Exit after being prompted to save any modified files
CTRL-h t      | Opens help and then emacs tutorial

#### Changing Cursor Positions in emacs

Key                 | Usage
---                 | -----
arrow keys          | Use the arrow keys for up, down, left and right
CTRL-n              | One line down
CTRL-p              | One line up
CTRL-f              | One character forward/right
CTRL-b              | One character back/left
CTRL-a              | Move to beginning of line
CTRL-e              | Move to end of line
Meta-f              | Move to beginning of next word
Meta-b              | Move back to beginning of preceding word
Meta-<              | Move to beginning of file
Meta-g-g-n          | Move to line n (can also use 'Esc-x Goto-line n')
Meta->              | Move to end of file
CTRL-v or Page Down | Move forward one page
Meta-v or Page Up   | Move backward one page
CTRL-l              | Refresh and center screen

#### Searching for Text in emacs

Key    | Usage
---    | -----
CTRL-s | Search forward for prompted pattern, or for next pattern
CTRL-r | Search backwards for prompted pattern, or for next pattern

#### Working with Text in emacs

Key                     | Usage
---                     | -----
CTRL-o                  | Insert a blank line
CTRL-d                  | Delete character at current position
CTRL-k                  | Delete the rest of the current line
CTRL-_                  | Undo the previous operation
CTRL- (space or CTRL-@) | Mark the beginning of the selected region. The end will be at the cursor position
CTRL-w                  | Delete the current marked text and write it to the buffer
CTRL-y                  | Insert at current cursor location whatever was most recently deleted

#### Lab 11.2a (Lab 3): vi and emacs Tutorials
1. Familiarize yourself with these tutorials  

Okay

#### Lab 11.2b (Lab 4): Working with vi
1. Practice vi since it is on every Linux system and because it's harder than emacs.
#### Navigating in vi  
1. Assuming **myfile** does not exist, create it and open it by typing:  
     `student:/tmp> vi myfile`  
2. You now have a mostly blank screen with your cursor on the top line. You are initially in command mode.  
3. Type **a** to append and put you into insert mode.  
4. Type the sentence  
     `The quick brown fox jumped over the lazy dog.`  
     If you make a mistake in typing, remember that you can backspace to fix it.  
5. Hit the **ESC** key. Your cursor should be on the period at the end of the sentence. You are back in command mode at this point.  
6. Type a again, and then hit **Enter**. You should be on a new line under the one you just typed.  
7. Type the sentence:  
     `Nobody expects the Spanish Inquisition!`  
     Hit **Enter** key after typing the **!** character.  
8. At the beginning of the third line, type the sentence:  
     `This is the third line.`  
     Hit the **ESC** key. You are now back in command mode.  
9. Now let's just move around in this file. Your cursor should be positioned over the period at the end of the third line you just typed. Hit the **h** key three times. Hit the **k** key once. Which letter is your cursor over? Which word are you in?  
     (Your cursor should be over the **S** in the word **Spanish**.)  
10. Hit **h** four times and then hit **j**. Now which letter is under your cursor? Which word are you in?  
     (Your cursor should be over the **r** in the word **third**.)  
11. Hit **k** twice and then **l** three times. Which letter is your cursor over? In which word?  
     (Your cursor should be over the **x** in the word **fox**.)  
12. Now hit **w** eight times. What do you notice Which letter is your cursor over? Which word is your cursor over?  
     (You should notice that you are now skipping across words. In fact, eight taps on **w** will take you from the first line to the second line! Your cursor should now be over the **e** in the word **expects** on the second line.)  
13. Hit **k**, followed by the **$** key. Now hit **b** twice. Which letter is your cursor over and in which word?  
     (Your cursor should be over the **l** in the word **lazy**)  
14. You should be getting the feel for moving around character-by-character, line-by-line and word-by-word, both forward and backward.  
     Now hit the **0** (zero) key followed by two **w** keys and three **l** keys. Which letter is your cursor over and in which word are you?  
     (Your cursor should be over the **w** in the word **brown**.  
15. Now hit **e** three times. Which letter is your cursor over and in which word?  
     (You should be over the **d** in the word **jumped**.)  
16. Save the file using the `:w` command. You will use this file in the next exercise. Finally quit **vi** by typing the `:q` command. You can also combine these commands by typing `:wq` if you prefer.  
Hopefully, this practice gets you used to how you can move your cursor around in a file. After awhile, these movement keys will become second-nature.  
  
#### Editing in vi  
Being comfortable moving around within a file is important, but actually editing the text in the file is the whole point of using an editor.  
1. We will edit the file you created in the previous exercise; i.e., do  
     `student:/tmp> vi myfile`  
2. Search for the word **the**, by hitting **/** followed by **the**, and then hit **Enter**  
     Your cursor should jump to the word **the** in the first line between the words **over** and **azy**.  
3. Now search for the next occurrence of **the**. Hit the **n** key. Your cursor should have jumped to the word **the** between **expects** and **Spanish** on the second line.  
4. Let's change this word from **the** to **a**. With your cursor positioned over the t in the word **the**, type **cw** to change the word. Did you notice that the word **the** disappeared?  
     You are now in insert mode, so simply type the letter **a** at this point. If you wanted to type more characters here, you can. For example, you could have changed the word **the** to **a bright and shiny** if you wanted to. When you are finished with changing the word **the** to the word **a**, type the **ESC** key to exit from insert mode back to command mode.  
5. Hit the **L** (**shift+l**) key to move to the last line on the screen. For this short file, that also happens to be the last line. Type **dd**. What happens?  
     (The last line should now be deleted.)  
6. Hit the **H** (**shift+h**) key to move to the first line on the screen. Again, for this short file, that also happens to be the first line of the file. Type **"1P** (double-quote, **1** and **P**). What happens?  
     You have put the contents of the last deleted item above the current line. In this case, you put the former third line above the first line. Your file should now look like this:  
     `This is the third line.
     The quick brown fox jumped over the lazy dog.
     Nobody expects a Spanish Inquisition!`  
7. Move your cursor to the word **third** using any method you prefer. Remember, you can do this several ways. You can move one character at a time, skip a word at a time, or search for the word. Use the **cw** command to change the word **third** to the word **first**. Remember to hit the **ESC** key when you are finished typing the word **first** in order to exit insert mode.  
8. Hit the **L** key to move down to the last line on the screen. Let's yank this into a named buffer; let's use the named buffer **c**. So, to yank this whole line into **c**, we can type **"cY**. Is the line still there?  
     After yanking the last line into the **c** named buffer, the last line is still present. Yanking a line just copies the line.)  
9. Hit the **H** key to move to the top line on the screen. Let's put the contents of the c named buffer after our first line. Type **"cp** to put the contents of the **c** named buffer after the current line.  
     After putting the yanked line after the first line, your file should look like:  
     `This is the third line.
     Nobody expects a Spanish Inquisition!
     The quick brown fox jumped over the lazy dog.
     Nobody expects a Spanish Inquisition!`  
10. Let's try the substitute command to change all occurrences of a word to something else. For emphasis, let's change all instances of the word **the** to all capitals, as in **THE**. To do this, we type the command `:%s/the/THE/g`. Do you notice the changes?  
      After executing the substitute command, your file should look like:  
      `This is THE third line.
      Nobody expects a Spanish Inquisition!
      The quick brown fox jumped over THE lazy dog.
      Nobody expects a Spanish Inquisition!`  
      Note that the first **The** at the beginning of line 3 was not changed because it did not match **the** (all lower-case) in our substitute command.  
11. On second thought, use of **THE** in all capital letters looks like I am yelling at you. Let's undo that last change. How Type the **u** key! (For undo.) Did you notice that both occurrences of the change were undone by this one command The **u** key will undo the last change even if that change affected several places in the file.  
12. Finally, let's improve the file's format by running it through the **fmt** command. Type `%!fmt`. How does it look?  
      (Your formatted file should look similar to:  
      `This is the first line. Nobody expects a Spanish Inquisition!
      The quick brown fox jumped over the lazy dog.  Nobody expects a
      Spanish Inquisition!`)  
13. Exit **vi**. You can either save your changes by writing the file and then quitting (`:wq`), or quit without saving (`:q!`).  
  
#### External Commands in vi
The following situation, where you may lose quite a bit of work before it is saved, is likely to come up sooner or later.  
1. Change the permissions on the file you have been editing to read-only, and open it for editing by doing:  
     `student:/tmp> chmod 444 myfile`  
     `student:/tmp> vi myfile`  
2. Delete the first line in the file, by typing `dd`. You may see a brief error message, but the line will be deleted.  
3. Now let's try and save the modified file. Type `:w`. What happens?  You can't save this file, can you? It is read-only! You should have gotten an error message at the bottom line of the screen telling you that the file is readonly.  
4. Perhaps you made lots of changes to this file in a long editing session. Is all your hard work lost? No!  
5. You have a couple of alternatives. The first one is that you can go out to the shell from your editor, change the permissions on the file, exit the shell and be returned to your editing session to save your file.  
     Do this by typing `:sh`. This should give you a shell prompt. Now type the command:  
     `student:/tmp> chmod 644 myfile`  
     and then exit the shell with:  
     `student:/tmp> exit`  
     You should be returned to your vi editing session. You typically see an error message telling you that the file has changed. Type O (shift+o) for OK.  
     Now type `:w!` (note the added !). This file should be saved now!  
6. Another thing you could do is to write the contents of the file to a new file. The `:w` command can take an argument as the file name you want to save the file as. For example, you could type `:w new_myfile` to save all your changes to the file `new_myfile`.  
     It is good to know these techniques because this will likely come up sometime while you are working with Linux. Play around with both techniques and become comfortable with both.  
  
### 11.3: Summary
----
You have completed this chapter. Let’s summarize the key concepts covered:  
* **Text editors** (rather than word processing programs) are used quite often in Linux, for tasks such as creating or modifying system configuration files, writing scripts, developing source code, etc.
* **nano** is an easy-to-use text-based editor that utilizes on-screen prompts.
* **gedit** is a graphical editor, very similar to Notepad in Windows.
* The **vi** editor is available on all Linux systems and is very widely used. Graphical extension versions of **vi** are widely available as well.
* **emacs** is available on all Linux systems as a popular alternative to **vi**. **emacs** can support both a graphical user interface and a text mode interface.
* To access the **vi** tutorial, type **vimtutor** at a command line window.
* To access the **emacs** tutorial type **Ctl-h** and then **t** from within emacs.
* vi has three modes: **Command**, **Insert**, and **Line**; **emacs** has only one, but requires use of special keys, such as **Control** and **Escape**.
* Both editors use various combinations of keystrokes to accomplish tasks; the learning curve to master these can be long, but once mastered using either editor is extremely efficient.
  
  
COMMANDS:  
11.1  
`$ echo line one > myfile`  
`$ echo line two >> myfile`  
`$ echo line three >> myfile`  
`$ cat << EOF > myfile`  
`> line one`  
`> line two`  
`> line three`  
`> EOF`  
11.2  
from within vi:
`: sh`  
`: ! wc %`  
`$ `  


