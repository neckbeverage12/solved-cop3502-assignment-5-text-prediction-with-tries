Download Link: https://assignmentchef.com/product/solved-cop3502-assignment-5-text-prediction-with-tries
<br>
In this programming assignment, you will gain experience with an advanced tree data structure that is used to store strings, and which allows for efficient insertion and lookup: a trie! You will then use this data structure to complete a text prediction task.

In this assignment, you will also learn how to process command line arguments with argc and argv and how to use valgrind to test your programs for memory leaks.

By completing this assignment and reflecting upon the awesomeness of tries, you will fortify your knowledge of algorithms and data structures and solidify your mastery of many C programming topics you have been practicing all semester: dynamic memory management, file I/O, processing command line arguments, dealing with structs and pointers to structs, and so much more.

In the end, you will have implemented a tremendously useful data structure that has many applications in text processing and corpus linguistics.

In this program, you are welcome to use any code I have given you so far in class, as long as you include a comment to give me credit (primarily so your submission doesn’t get flagged for plagiarism). The intellectually curious student will, of course, try to write the whole program from scratch.

<strong>Deliverables</strong>

TriePrediction.c

(<strong><em>Note!</em></strong> Capitalization of your filename matters!)

<h1>1. Overview: Tries</h1>

We have seen in class that the trie data structure can be used to store strings. It provides for efficient string insertion and lookup; insertion into a trie is O(<em>k</em>) (where <em>k</em> is the length of the string being inserted), and searching for a string is an O(<em>k</em>) operation (worst-case). In a trie, a node does not store the string it represents; rather, the <em>edges </em>taken to reach that node from the root indicate the string it represents. Each node contains a <em>flag </em>(or <em>count</em>) variable that indicates whether the string represented by that node has been inserted into the trie (or <em>how many times</em> it has been inserted). For example:

<strong>Figure 1:</strong>

This is a trie that codifies the words “and,” “cherry,” “chocolate,” “I,”

“like,” “really,” and “pie.” The strings “I,” “like,” and “pie” are represented (or counted) twice. All other strings are counted only once.

<h2>1.1.     TrieNode Struct (TriePrediction.h)</h2>

In this assignment, you will insert words from a <em>corpus</em> (that is, a body of text from an input file) into a trie. The struct you will use for your trie nodes is as follows:

typedef struct TrieNode

{

// number of times this string occurs in the corpus    int count;

// 26 TrieNode pointers, one for each letter of the alphabet    struct TrieNode *children[26];

// the co-occurrence subtrie for this string    struct TrieNode *subtrie; } TrieNode;

You must use this trie node struct, which is specified in TriePrediction.h without any modifications.

You should #include the header file from TriePrediction.c like so:

#include “TriePrediction.h”

Notice that the trie node, because it only has 26 children, represents strings in a case insensitive way (i.e., “apple” and “AppLE” are treated the same in this trie).

<h2>1.2.     Subtries: Contextual Co-occurrence and Predictive Text</h2>

Words that appear together in the same sentence are said to “co-occur.” In this program, we’ll be interested in contextual co-occurrence and predictive text – namely, given some word, <em>w</em>, what are all the words that we see immediately after <em>w</em> in the sentences in some corpus? Consider, for example, the following sentence:

<em>I like cherry pie, and I really like chocolate pie.</em>

In the example sentence, the word “I” is followed by the words “like” and “really”, the word “pie” is followed by the word “and”, and so on.

To track co-occurrence, for each word <em>w</em>, we’ll place each word that directly follows <em>w</em> into the subtrie of <em>w</em>. For example, if we place these terms (and their associated counts) into their own tries, those tries will look like this:

(<em>See following page for diagram.</em>)

<strong>Figure 2:</strong>

Co-occcurrence subtries for “I” (left) and “like” (right), based on the sentence, “I like cherry pie, and I really like chocolate pie.”

In Figure 2, the trie on the left is what we will call the <em>co-occurrence subtrie</em> for the word “I,” based on the example sentence above (<em>I like cherry pie, and I really like chocolate pie</em>). Its root should be stored in the <em>subtrie</em> pointer field of the node marked “I” in the original trie diagram in Figure 1 (see page 2 above).

Similarly, the trie on the right in Figure 2 is the co-occurrence subtrie for the word “like.” Its root should be stored in the <em>subtrie</em> pointer field of the node marked “like” in the original trie diagram in Figure 1 (see page 2, above).

<em>Within</em> these subtries, all <em>subtrie </em>pointers should be initialized to NULL, because we will NOT produce sub-subtries in this assignment!

<h1>2. Input Files and Output Format</h1>

<h2>2.1.     Command Line Arguments</h2>

Your program will take two command line arguments, specifying two files to be read at runtime:

./a.out corpus01.txt input01.txt

The first filename specifies a corpus that will be used to construct your trie and subtries. The second filename specifies an input file with strings to be processed based on the contents of your trie.

You can assume that I will <em>always </em>specify valid input files when I run your program.

It’s super easy to get command line arguments (like the string “corpus01.txt” in this example) into your program. You just have to change the function signature for main(). Whereas we have typically seen main() defined using int main(void), you will now use the following function signature instead:

int main(int argc, char **argv)

Within main(), argc is now an integer representing the number of command line arguments passed to the program (including the name of the executable itself). argv is an array of strings that stores all those command line arguments. argv[0] stores the name of the program being executed. Following is a simple program that print out all the command line arguments passed to a program. You don’t need this in your programming assignment; it’s just an example to show how argc and argv work.

<table width="638">

 <tbody>

  <tr>

   <td width="638">#include &lt;stdio.h&gt;</td>

  </tr>

  <tr>

   <td width="638"> </td>

  </tr>

  <tr>

   <td width="638">int main(int argc, char **argv)</td>

  </tr>

  <tr>

   <td width="638">{</td>

  </tr>

  <tr>

   <td width="638">int i;</td>

  </tr>

  <tr>

   <td width="638">for (i = 0; i &lt; argc; i++)</td>

  </tr>

  <tr>

   <td width="638">printf(“argv[%d]: %s
”, i, argv[i]);</td>

  </tr>

  <tr>

   <td width="638">return 0;</td>

  </tr>

  <tr>

   <td width="638">}</td>

  </tr>

 </tbody>

</table>

If we compiled this code into an executable file called a.out and ran it from the command line by typing ./a.out input01.txt lol hi, we would see the following output:

argv[0]: ./a.out argv[1]: input01.txt

argv[2]: lol argv[3]: hi




<h2>Passing Command Line Arguments in CodeBlocks</h2>

You can set up CodeBlocks to automatically pass command line arguments to your program every time you hit <em>Build and run (F9)</em> within the IDE. Simply go to <em>Project</em> → <em>Set programs’ arguments…</em>, and in the box labeled <em>Program arguments</em>, type your desired command line arguments.

For example, the following setup passes input01.txt lol hi as command line arguments to the program when compiled and run within CodeBlocks. (I don’t think you need to check off the box next to “<em>This target provides</em>….” CodeBlocks seems to take care of that automatically after you hit “OK.”)

If you compile your program like this in CodeBlocks and later run your executable from the command line, you will still need to supply fresh arguments at the command line. At any rate, don’t forget to test your code on Eustis before submitting, even if you do most of your development in CodeBlocks.

(<em>Continued on the following page.</em>)

<h2>Corpus File</h2>

<h3>2.3.1     Corpus File Format</h3>

The corpus file contains a series of sentences. Each line contains a single sentence with at least 1 word and no more than 30 words. Each word contains fewer than 1024 characters, some of which may be punctuation characters.

Each sentence will contain exactly one of the following punctuators, and it will occur at the very end of the sentence: period (‘.’), exclamation point (‘!’) or question mark (‘?’). Other punctuators may occur throughout the sentence, including (but not limited to) commas (‘,’), colons (‘:’), semicolons (‘;’), apostrophes (‘’’ and ‘‘’), quotation marks (‘“’ and ‘”’), and so on.

All words will have at least one alphabetic character (‘a’ through ‘z’ or ‘A’ through ‘Z’). Words will <em>not </em>contain any numeric characters (‘0’ through ‘9’). All punctuators should be stripped away from all words before entering them into the trie. So, for example, the word “don’t” will be entered into the trie as “dont”.

For example:

corpus01.txt:

I like cherry pie, and I really like chocolate pie.

corpus02.txt:

’tweren’t my fault, I swear!

And now we’re on to the second line of text.

<h3>2.3.2    Building the Main Trie</h3>

First and foremost, each word from the corpus file should be inserted into your trie. If a word occurs multiple times in the corpus, you should increment <em>count</em> variables accordingly. For example, the trie in Figure 1 (see page 2, above) corresponds to the text given in the corpus01.txt file above.

<h3>2.3.3     Building Subtries</h3>

For each sentence in the corpus, update the co-occurrence subtrie for each word in that sentence. The structure of the co-occurrence subtries is described above in Section 1.2, “Subtries: Contextual Cooccurrence and Predictive Text.”

If a string in the main trie is not followed by any other words in the corpus, its subtrie pointer should be

NULL.

<h2>Input File</h2>

The input file (the second filename specified as a command line argument) will contain any number of lines of text. Each line will correspond to one of the following three commands:

<strong>     Command:</strong> &lt;string&gt; <strong>     Description:</strong>

&lt;string&gt; is a string containing fewer than 1024 characters. The string will contain alphabetical characters only (‘A’ through ‘Z’ and/or ‘a’ through ‘z’), and it appears on a line by itself.

If &lt;string&gt; is in the trie, you should print the string, followed by a printout of its subtrie contents, using the output format shown in the sample outputs for this program. (Note that when printing a subtrie, the words in the subtrie are preceded by hyphens.)

If &lt;string&gt; is in the trie, but its subtrie is empty, you should print that string, followed on the next line by “(EMPTY)”.

If &lt;string&gt; is not in the trie at all, you should print that string, followed on the next line by

“(INVALID STRING)”.

<strong>Command:</strong> !

<strong>     Description:</strong>

The character ‘!’ will appear on a line by itself. When you encounter this command, you should print the trie using the output format shown in the sample outputs for this program. (When printing the main trie, there are no hyphens preceding the words on each line. See sample output files, or see the sample output below on pg. 6.)

<strong>Command:</strong> @ &lt;string&gt; &lt;n&gt;

<strong>     Description:</strong>

This is the text prediction command.

&lt;string&gt; is a string containing fewer than 1024 characters. The string will contain alphabetical characters only (‘A’ through ‘Z’ and ‘a’ through ‘z’). &lt;n&gt; is an integer.

When you encounter this command, you should print the following sequence of (<em>n</em> + 1) words:

<em>w</em><sub>0</sub> <em>w</em><sub>1</sub> <em>w</em><sub>2</sub> … <em>w</em><sub>n</sub>

where <em>w</em><sub>0</sub> is &lt;string&gt;, and for 1 ≤ <em>i</em> ≤ <em>n</em>, <em>w</em><sub>i</sub> is the word that most frequently follows word <em>w</em><sub>i-1</sub> in the corpus. Note that the words are separated by spaces, and there is never a space after the last word on one of these lines of output.




Furthermore, <em>w</em><sub>0</sub> is always capitalized in the output exactly as it was capitalized in the input file, but words <em>w</em><sub>1</sub> through <em>w</em><sub>n</sub> should appear in all lowercase.

If &lt;string&gt; does not appear in the trie, it should appear on this line of output by itself. If some <em>w</em><sub>i</sub> does not have any words in its subtrie, the sequence should terminate prematurely. Again, this line of output should <em>not</em> have a trailing space at the end of it, even if it terminates prematurely.

<h2>2.5.     Sample Input and Output Files</h2>

For example, the following corpus and input files would produce the following output:

corpus03.txt:

I like cherry pie and chocolate pie.

input03.txt:

<table width="599">

 <tbody>

  <tr>

   <td width="599">!chocolaTE apricot @ I 11@ chocolate 1@ persimmon 20</td>

  </tr>

 </tbody>

</table>

output03.txt:

<table width="599">

 <tbody>

  <tr>

   <td width="599">and (1)</td>

  </tr>

  <tr>

   <td width="599">cherry (1)</td>

  </tr>

  <tr>

   <td width="599">chocolate (1)</td>

  </tr>

  <tr>

   <td width="599">i (1)</td>

  </tr>

  <tr>

   <td width="599">like (1)</td>

  </tr>

  <tr>

   <td width="599">pie (2)</td>

  </tr>

  <tr>

   <td width="599">chocolaTE</td>

  </tr>

  <tr>

   <td width="599">– pie (1)</td>

  </tr>

  <tr>

   <td width="599">apricot</td>

  </tr>

  <tr>

   <td width="599">(INVALID STRING)</td>

  </tr>

  <tr>

   <td width="599">I like cherry pie and chocolate pie and chocolate pie and chocolate</td>

  </tr>

  <tr>

   <td width="599">chocolate pie</td>

  </tr>

  <tr>

   <td width="599">persimmon</td>

  </tr>

 </tbody>

</table>

(<em>Continued on the following page.</em>)

Note that a word might be in the trie but have an empty subtrie. Consider the following example:

corpus04.txt:

<table width="599">

 <tbody>

  <tr>

   <td width="599">Spin straw to gold.</td>

  </tr>

  <tr>

   <td width="599">Spin all night long.</td>

  </tr>

  <tr>

   <td width="599">Spin spin spin.</td>

  </tr>

  <tr>

   <td width="599">Spindle.</td>

  </tr>

 </tbody>

</table>

input04.txt:

spin spindle nikstlitslepmur

output04.txt:

spin

<ul>

 <li>all (1)</li>

 <li>spin (2)</li>

 <li>straw (1) spindle (EMPTY) nikstlitslepmur (INVALID STRING)</li>

</ul>

You must follow the output format above precisely. Be sure to consult the included text files for further examples.

I have included some functions that will help you print the contents of your trie(s) in the required format, because I think those functions are a bit too tricky to expect you to write them on your own. See printTrie.txt (attachment) for those functions. You’re welcome to copy and paste them into your TriePrediction.c source file if you want to use them, or you can write your own.

Also, studying and understanding those functions will serve as a launching point for you to write the other functions required to get this program working.

(<em>Continued on the following page.</em>)

<h1>3. Standard Tests and Unit Tests</h1>

With this assignment, I have included two kinds of test cases: standard test cases, which you can process from the command line, and unit test cases, which bypass your main() function and instead call functions I have written, which will in turn call individual functions from your code to see if they’re working as intended.

<h2>3.1.     Running Standard Test Cases</h2>

To run the standard test cases, you must compile your source code with test_launcher_std.c and then run your program from the command line like so:

<table width="638">

 <tbody>

  <tr>

   <td width="638">gcc TriePrediction.c test_launcher_std.c</td>

  </tr>

  <tr>

   <td width="638">./a.out corpus01.txt input01.txt</td>

  </tr>

 </tbody>

</table>

Compiling with test_launcher_std.c ensures that it is <em>your</em> main() function that executes when you run the program.

<h2>3.2.     Running Unit Test Cases</h2>

Unit tests are designed to override your main() function and directly call one or more functions in your code. This allows me to determine whether you implemented individual functions correctly.

To run unit test cases, you must compile your source code with test_launcher_unit.c, as well as the source file for the unit test you want to run. Afterwards, you can run the program without any command line arguments. For example, you can compile and run unit_test01.c like so:

<table width="638">

 <tbody>

  <tr>

   <td width="638">gcc TriePrediction.c test_launcher_unit.c unit_test01.c</td>

  </tr>

  <tr>

   <td width="638">./a.out</td>

  </tr>

 </tbody>

</table>

Compiling with test_launcher_unit.c essentially overrides your main() function and ensures that the unit test code you compiled into your program will be executed when you run the program.

<h1>4. Special Requirement: Memory Leaks and Valgrind</h1>

Part of the credit for this assignment will be awarded based on your ability to implement the program without any memory leaks. To test for memory leaks, you can use a program called valgrind, which is installed on Eustis.

To run your program through valgrind at the command line, compile your code with the -g flag, and then run valgrind, like so:

gcc TriePrediction.c test_launcher_std.c -g valgrind –leak-check=yes ./a.out corpus01.txt input01.txt

For help deciphering the output of valgrind, see: <a href="http://valgrind.org/docs/manual/quick-start.html">http://valgrind.org/docs/manual/quick-start.html</a>

Note that if you do not use fclose() to explicitly close all open files before your program terminates, valgrind might alert you that your program has a memory leak.

In the output of valgrind, the magic phrase you’re looking for to indicate that you have no memory leaks is:

All heap blocks were freed – no leaks are possible

<h1>5. Function Requirements</h1>

<h2>5.1.    Required Function</h2>

You have a fair amount of leeway with how to approach this assignment. There are nine required functions. How you structure the rest of your program is up to you.

int main(int argc, char **argv);

<strong>Description:</strong> You must write a main() function.

<strong>Returns:</strong> Your main() must return zero.

TrieNode *buildTrie(char *filename);

<strong>Description:</strong> filename is the name of a corpus text file to process. Open the file and create a trie (including all its appropriate subtries) as described above.

<strong>Returns:</strong> The root of the new trie.

<strong>Note:</strong> I did not include a unit test for this function, but I do intend to write one to check whether your trie has been constructed properly.

TrieNode *destroyTrie(TrieNode *root);

<strong>Description:</strong> Free all dynamically allocated memory associated with this trie.

<h2>Returns: NULL</h2>

TrieNode *getNode(TrieNode *root, char *str);

<strong>Description:</strong> Searches the trie for the specified string, str.

<strong>Returns:</strong> If the string is represented in the trie (with <em>count</em> ≥ 1), return a pointer to its terminal node (the last node in the sequence, which represents that string). Otherwise, return NULL.

void getMostFrequentWord(TrieNode *root, char *str);

<strong>Description:</strong> Searches the trie for the most frequently occurring word and copies it into the string variable passed to the function, str. If you are calling this function yourself, you should create the str string beforehand, and it should be (at least) long enough to hold the string that will be written to it. (For this, you can use MAX_CHARACTERS_PER_WORD from TriePrediction.h.) If I call this function manually when testing your code, I will ensure the string is created ahead of time and that it is long enough to hold the longest string in the trie. Note that there is no guarantee that str will contain the empty string when this function is first called; the string might contain garbage data.

If there are multiple strings in the trie that are tied for the most frequently occurring, populate str with the one that comes first in alphabetical order. If the trie is empty, set str to the empty string (“”).

<strong>Hint:</strong> You might find it easier to write helper functions that you call from within this function.

int containsWord(TrieNode *root, char *str);

<strong>Description:</strong> Searches the trie for the specified string, str.

<strong>Returns:</strong> If the string is represented in the trie (with <em>count</em> ≥ 1), return 1. Otherwise, return 0.

<strong>Note:</strong> You might find that you don’t need this function to build out the text prediction functionality of your code.

int prefixCount(TrieNode *root, char *str);

<strong>Description:</strong> Counts the number of strings in the trie (with <em>count</em> ≥ 1) that begin with the specified string, str. Note that if the specified string itself is contained within the trie, that string should be included in the count. If one of these strings occurs more than once, its entire <em>count</em> should be added to the return value.

<strong>Returns:</strong> The number of strings in the trie that begin with the specified string, str.

<strong>Note:</strong> You might find that you don’t need this function to build out the text prediction functionality of your code.

double difficultyRating(void);

<strong>Returns:</strong> A double indicating how difficult you found this assignment on a scale of 1.0 (ridiculously easy) through 5.0 (insanely difficult).

double hoursSpent(void);

<strong>Returns:</strong> An estimate (greater than zero) of the number of hours you spent on this assignment.

<h2>5.2.    Suggested Functions</h2>

These functions are not required, but I think they will simplify your task immensely if you implement them properly and call them when processing your corpus/input files. Think of these function descriptions as hints at how to proceed. If you want, you can even implement these functions with different parameters, return types, and so on.

TrieNode *createTrieNode(void);

<strong>Description:</strong> Dynamically allocate space for a new TrieNode struct. Initialize all the struct members appropriately.

<strong>Returns:</strong> A pointer to the new node.

<strong>Note:</strong> You should try to implement this yourself, but there’s a copy of this function in our notes in Webcourses, should you really need it.

void insertString(TrieNode *root, char *str);

<strong>Description:</strong> Inserts the string str into the trie. Since it has no return value, it assumes the root already exists (i.e., root is not NULL). If str is already represented in the trie, simply increment its count member.

<strong>Note:</strong> You should try to implement this yourself, but there’s a copy of this function in our notes in Webcourses, should you really need it.

void stripPunctuators(char *str);

<strong>Description:</strong> Takes a string, str, and removes all punctuation from the string. For example, if str contains the string “Hello!” when the function is called, then str should contain the string “Hello” when the function returns. When writing this function, you might find C’s built-in isalpha() function (from ctype.h) to be helpful.




<h1>Compilation and Testing (Code::Blocks)</h1>

<strong><em>Important Note!</em></strong> If you compile your project with Code::Blocks, you must import <em>either</em> test_launcher_std.c <em>or</em> test_launcher_unit.c in your project (but not both). If you are compiling a unit test, you must also import the unit test’s source file.

The key to getting your program to include a custom header file in Code::Blocks (or any IDE) is to create a project. Here are the step-by-step instructions for creating a project in Code::Blocks, importing TriePreidction.h and the TriePrediction.c file you’ve created (even if it’s just an empty file so far).

<ol>

 <li>Start Code::Blocks.</li>

 <li>Create a New Project (<em>File</em> → <em>New</em> → <em>Project</em>).</li>

 <li>Choose “Empty Project” and click “Go.”</li>

 <li>In the Project Wizard that opens, click “Next.”</li>

 <li>Input a title for your project (e.g., “TriePreidction”).</li>

 <li>Pause to reflect on life a bit. Isn’t this amazing?</li>

 <li>Choose a folder (e.g., Desktop) where Code::Blocks can create a subdirectory for the project.</li>

 <li>Click “Finish.”</li>

</ol>

Now you need to import your files. You have two options:

<ol>

 <li>Drag your source and header files into Code::Blocks. Then right click the tab for <strong><u>each</u></strong> file and choose “Add file to active project.”</li>

</ol>

– <em>or</em> –

<ol start="2">

 <li>Go to <em>Project</em> → <em>Add Files</em>…. Then browse to the directory with the source and header files you want to import. Select the files from the list (using CTRL-click to select multiple files). Click “Open.” In the dialog box that pops up, click “OK.”</li>

</ol>

You should now be good to go. Try to build and run the project (F9).

<strong><em>Note!</em></strong> Even if you develop your code with Code::Blocks on Windows, you ultimately have to transfer it to the Eustis server to compile and test it there. See the following page (Section 7, “Compilation and Testing (Linux/Mac Command Line)”) for instructions on command line compilation in Linux. See also: “Super Important: Running the test-all.sh Script.”

(<em>Continued on the following page.</em>)

<h1>Compilation and Testing (Linux/Mac Command Line)</h1>

To compile your code at the command line and then run it with a standard (corpus-based) test case:

gcc TriePrediction.c test_launcher_std.c ./a.out corpus01.txt input01.txt &gt; myoutput.txt diff myoutput.txt output01.txt

Of course, you should switch out <em>corpus01.txt </em>and <em>input01.txt</em> for other corpus and input files.

To compile and run a unit test case at the command line:

<table width="665">

 <tbody>

  <tr>

   <td width="665">gcc TriePrediction.c test_launcher_unit.c unit_test01.c</td>

  </tr>

  <tr>

   <td width="665">./a.out &gt; myoutput.txt</td>

  </tr>

  <tr>

   <td width="665">diff myoutput.txt unit_output01.txt</td>

  </tr>

 </tbody>

</table>

If the contents of myoutput.txt and unit_output01.txt are exactly the same, diff won’t have any output. It will just look like this:

<strong><a href="/cdn-cgi/l/email-protection" class="__cf_email__" data-cfemail="e59680848b969fa5809096918c96">[email protected]</a>:~$</strong> diff myoutput.txt unit_output01.txt <strong><a href="/cdn-cgi/l/email-protection" class="__cf_email__" data-cfemail="215244404f525b61445452554852">[email protected]</a>:~$</strong> _

If the files differ, it will spit out some information about the lines that aren’t the same. For example:

<strong><a href="/cdn-cgi/l/email-protection" class="__cf_email__" data-cfemail="99eafcf8f7eae3d9fceceaedf0ea">[email protected]</a>:~$</strong> diff myoutput.txt unit_output01.txt

1c1

&lt; fail whale &#x1f641;

—

&gt; Success! <strong><a href="/cdn-cgi/l/email-protection" class="__cf_email__" data-cfemail="bbc8dedad5c8c1fbdecec8cfd2c8">[email protected]</a>:~$</strong> _

<h2>7.1.     Super Important: Running the test-all.sh Script</h2>

To be absolutely certain that all your test cases are passing, you should place TriePrediction.c and TriePrediction.h in the test_cases directory included with this assignment and run the test-all.sh script, like so:

bash test-all.sh

<h1>Grading Criteria and Submission</h1>

<h2>8.1.    Deliverables</h2>

Submit a single source file, named TriePrediction.c, via Webcourses. The source file should contain definitions for the required function (listed above), as well as any auxiliary functions you decide to implement. Don’t forget to #include “TriePrediction.h” in your source code. Your program must compile on Eustis with the following:

gcc TriePrediction.c test_launcher_std.c

Be sure to include your name and NID in a comment at the top of your source file.

<h2>8.2.           Additional Restrictions: Use Tries; No Globals, Mid-Function Declarations, or System Calls</h2>

You must use tries to receive credit for this assignment. As always, you must also avoid the use of global variables, mid-function variable declarations, and system calls (such as system(“pause”)).


