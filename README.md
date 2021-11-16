# Text Processing Recipes Linux.
Linux text processing reference & recipes. 
Featuring: `vim, tr, cat, tac, sort, shuf, seq, pr, paste, fmt, cut, nl, split, csplit, sed, awk, grep` and regex.

# TABLE OF CONTENTS
* [Basic Regex](#basic-regex)
* [General Tools & Recipes](#general-tools)
* [grep](#grep)
* [sed](#sed)
* [awk](#awk)
* [vim-tips](#vim)

# Recommended books (not written by me)
[Definitive Guide to sed - by Daniel Goldman](https://amzn.to/2YJETKI)
<br>
[Sed & Awk - Dale Dougherty & Arnold Robbins](https://amzn.to/3nd0BjE)
<br>
[Effective awk programming - by Arnold Robbins](https://amzn.to/30xk6Lf)


# Basic Regex.

#### Literal. Matches 'hello'
`hello`

#### Any single character.
`.`

#### Two any single characters
`..`

#### Multiple specific characters. 'a' or 'b' or 'c' 
`[abc]`

#### Character range. All characters between a-z.
`[a-z]` 

#### Multiple ranges.All between a-z and A-Z.
`[a-zA-Z]`

#### Ranges + extra characters. Chars from 1-9 and 'a','b','c'
`[1-9abc]`

#### Negated chars. Any char that is NOT 'a', 'b', or 'c'
`[^abc]` 

#### Negated char ranges. Any char NOT between a-z.
`[^a-z]`

#### * multiplier. Any char zero or more times. Works on the preceding ITEM.
`.*`

#### * multiplier again. Char 'a', followed by char 'b' (preceding item) zero or more times. This is how all multipliers work.
`ab*`

#### ? multiplier. Any char zero or one times.
`.?`

#### + multiplier. Any char one or more times.
`.+`

#### Numbered multiplier. Character 'l' five times. ('lllll')
`l{5}`

#### Numbered range multiplier. Char 'a' between 1-5 times ('a', 'aa','aaa', etc)
`a{1-5}`

#### Open ended numbered range multiplier. Char 'a' at least 2 times. 
`a{2,}` 

#### Greedy matching (default). From 'hello you loving kool kaizer king' will match 'llo you loving kool kaizer k'
`l.*k`

#### Lazy matching. From 'hello you loving kool kaizer king' will match 'llo you loving k' and 'l k'. In most cases this is the desired behaviour.
`l.*?k`



#### Shorthand char classes:
```
\s - whitespace (space, newline, tab, etc)
\S - oposito of \s (not whitespace)
\d - digit (0-9)
\D - not digit
\w - word char ([a-zA-Z0-9_])
\W - not word char
```

#### ^ beginning of line. Beginning of line followed by 'h', 'e','l','l' and 'o'
`^hello` 

#### $ end of line. 'b','y','e' followed by end of line.
`bye$` 

#### \b - beginning or end of word. Will match ' FOO' at end of line but not 'BARFOO' at end of line. 
`\bFOO$`


#### Group in parenthesis. Back reference it by '\1'. 'foo' is group 1. Matches 'foo bar foo' (foo, followed by bar followed by first group which is 'foo')
`(foo) bar \1`

#### Alternation. 'foo' or 'bar'
`foo|bar` 



# General Tools

#### Replace ':' with tabs in the output 
`cat /etc/passwd | tr ':' '\t'`

#### Delete unwanted chars ('#')
`echo "hello # world" | tr -d '#'`

#### replace all but the alphanumeric chars with `_`
`cat main.py | tr -c '[:alnum:]\n' '_'
```
___usr_bin_python3
from_termcolor_import_colored__cprint
import_sys
for_i_in_range_0_10__
____print_str_i__end___r__
```

#### Convert all to uppercase:
`cat main.py | tr 'a-z' 'A-Z'`

#### Convert all to lowercase:
`cat main.py | tr 'A-Z' 'a-z'`


#### Swap uppercase/lowercase
```
tr 'a-zA-Z' 'A-Za-z' <file.txt
```


#### Replace all digits with empty space. 
`cat main.py | tr '0-9' ' '`


#### Replace all punctuation with empty space. Braces, parenthesis, etc. 
`cat main.py | tr '[:punct:]' ' '`


#### Remove repeated spaces (keep just a single space between words)
`cat file | tr -s ' '`


#### Sort using human-numeric-sort (will order: '1,10,15,100' ad not '1,10,100,15')
`sort -h file`

#### Sort and reverse
`sort -r file`

#### Random sort (shufle lines)
`sort -R file`

#### Sort in place (OVERWRITE file with sorted lines). Don't print anything on stdout.
`sort file -o file`

#### Sort and remove duplicate lines
`sort -u file`

#### Get N random lines from file
`shuf -n2 file`

#### Get N random lines from file, with repetition allowed (one line may be present multiple times)
`shuf -rn2 file `	

#### Generate 3 rand numbers between 1 and 20 (no repetition)
`shuf -n3 -i 1-20`


#### Generate 3 rand numbers between 1-20 with repetition
`shuf -rn3 -i 1-20`


#### Generate 3 rand negative numbers between -10 and 0 with repetition ('-r' is for repetition. remove it for no repetition)
`seq -10 0 | shuf -rn3`


#### Generate 3 rand floats (0.1 step) between 0-5. Add '-' for repetition ('-rn3')
`seq 0 0.1 5 | shuf -n3`


#### Generate 3 rand numbers and sort them (numerical sort)
`shuf -n3 -i 0-100 | sort -n`


#### Get 2 random words from word list provided after '-e'
`shuf -n2 -e red blue green orange`

#### Return two random file names from current dir
`shuf -n2 -e * `

#### Generate random IP. (replace newlines with dots using 'tr')
`shuf -n4 -i 0-255 | tr '\n' '.'`

#### Generate a sequenco of numbers 1 to 10
`seq 10`

#### Generate sequence of numbers between 5-15
`seq 5 15`

#### Generate sequence of numbers between 10-20, skipping every second number (10,12,14,etc)
`seq 10 2 20`

#### Generate a descending sequence of numbers from 10 to 0
`seq 10 -1 0`

#### Generate a sequence of numbers separated by space
`seq -s' ' 10`

#### Generate sequence of numbers and pad them with 0 (01,02,etc)
`seq -w 10`

#### Generate sequence of numbers with custom formatting (using printf formatting)
`seq -f'%.5f' 10`

#### Double space between lines (if no empty lines add one. If 2 empty lines - make them 4)
`pr -dt file`

#### Gen numbers and put them on 3 columns, separated by tabs:
`seq 9 | pr -3ts`
<br>
```
1	4	7
2	5	8
3	6	9
```

#### Gen numbers, put them on 3 cols, separated by commas:
`seq 9 | pr -3ts, `
<br>
```
1,4,7
2,5,8
3,6,9
```

#### Gen numbers, put them on 3 cols ACROSS (from left to right, not top to bottom), separated by commas, :
`seq 9 | pr -3ats, `
```
1,2,3
4,5,6
7,8,9
```

#### Put the contents of files each on a column, separated by tabs. Works well with files with short lines.
`pr -mts file1 file2`
```	
10	100
100	One thousand
20	2000
25	2,5
one	one and tow
	extra
	extra2
	extra3
```

#### Generate rand nums from: 1-10, 1-100. Put them on columns separated by tabs.
`pr -mts <(shuf -n10 -i 1-10) <(shuf -n10 -i 1-100)`
<br>
```

2	62
10	88
4	76
6	96
5	35
1	39
7	64
3	1
9	48
8	53
```

#### Generate 100 sequential numbers. Put them on 5 columns separated by tabs ('t' stands for no header (no extra whitespace at the bottom usually)
`seq 100 | pr -5t`
<br>
```

1	      21	    41		  61		81
2	      22	    42		  62		82
3	      23	    43		  63		83
4	      24	    44		  64		84
5	      25	    45		  65		85
6	      26	    46		  66		86
7	      27	    47		  67		87
8	      28	    48		  68		88
9	      29	    49		  69		89
10	      30	    50		  70		90
11	      31	    51		  71		91
12	      32	    52		  72		92
13	      33	    53		  73		93
14	      34	    54		  74		94
15	      35	    55		  75		95
16	      36	    56		  76		96
17	      37	    57		  77		97
18	      38	    58		  78		98
19	      39	    59		  79		99
20	      40	    60		  80		100
```

#### Another way to put contents of a file on columns (across), separated by tabs. One column for one dash (3 cols total)
```
paste - - - <file 
```

#### Another to generate a sequence of nums on 3 colums with custom separator
`seq 20 | paste - - - -d,`
```

1,2,3
4,5,6
7,8,9
10,11,12
13,14,15
16,17,18
19,20,
```

#### Put all lines on a single line, separated by custom delimiter (comma). Each file lines go on a separate line (output is 3 lines)
`paste -sd, file1 file2 file3`


#### Break lines at max width. Break if possible at spaces (don't put '-s' if you want the breaks to occur wherever). Will cut words if they are more than max width ('-w')
`paste -sw5 file`
```
Lorem
 
ipsum
 
dolor
 
amet.
 I 
am 
first
.
What 
a 
story
. I 
am 
secon
d.
The 
End.
```
#### Break lines at max width. Will keep words even if they exceed max width.
`fmt -w3 file`
```

Lorem
ipsum
dolor
amet.
I
am
first.
What
a
story.
I
am
second.
The
End.
```

#### Show file contents with line numbering
`cat -n file`
```
     1	Lorem ipsum dolor amet. I am first.
     2	What a story. I am second.
     3	The End.

```

#### Show file contents. Squeeze empty lines (to at most one line)
`cat -s file`

#### Show file contents. Number only non-empty lines
`cat -b file`


#### Display file lines in reverse
`tac file`


#### Reverse text.
`echo "hello" | rev`
```
olleh
```

#### Display first 5 lines
`head -n 5 file`

#### Display all except last N lines
`head -n -5 file`


#### Display a column (the second). Use custom separator.
`echo "hello world of linux" | cut -f2 -d' '`
```

world
```

#### Display a column range. Use custom separator.
`echo "hello world of linux" | cut -f2-4 -d' '`
```
world of linux
```

#### Display column range (from beginning to third). (use -f3- to display from third to end)
`echo "hello world of linux" | cut -f-3 -d' '`
```
hello world of
```

#### Number lines. Increase count by 3 at each line (1  line1, 4  line2, etc)
`nl -i3 file`


#### Number lines. Custom string after line number.
`nl -s'--' file`
```

     1--Lorem ipsum dolor amet. I am first.
     2--What a story. I am second.
     3--Before empty lines.
        
        
     4--After empty lines
     5--The End.
        
```

#### Number lines. Start counting from 5.
`nl -v5 file`

#### Split file into smaller files. Each smaller file has at most 100 lines. (WARNING - it may create lots of files in dir where cmd is run) 
`split -l10 file`


#### Generate 100 rand nums. Put first 10 in one file, second 10 in another file, etc. File names start with 'x' - eg 'xai'. 
`seq 100 | split -l10`

#### Split file in smaller files by byte count. (eg: 'xai' has content 'abcde', 'xaj' has 'fghij', etc). Each smaller file has 5 bytes at most. 
`split -b5 file`

#### Split file into 3 (approximatively) equal chunks. WILL SPLIT LINES.
`split -n3 file`


#### Split file into 3 (approx.) equal chunks. Don't split lines (in the middle). 
`split -nl/3 file`


#### Split file into 8 and display 4th chunk on stdout. Will cut lines. No files are generated.
`split -n4/8 file`


#### Split file into 8 and display 4th chunk on stdout. Lines are kept intact. No files generated.
`split -nl/4/8 file`


#### Split file into 3 chunks. Don't split lines. Use numeric suffixes for generated files ('x01, x02,etc')
`split -dnl/3 file`

#### Split file into 3 chunks. Use custom prefix for generated filenames ('sub_aa,sub_bb')
`split -nl/3 file sub_`

#### Split file into 3 cunks. Use custom prefix AND numeric suffixes ('sub_00, sub_01')
`split -dnl/3 file sub_`


#### Split file at 5th line into 2 subfiles. Subfile 1 ('xx00')wil have 4 lines. Subfile 2 ('xx01') will have the rest.
`csplit file 5`

#### Split file at line matching regex into 2 subfiles. Line matching regex will be in second file.
`csplit file '/my line/' `

# sed


#### Print one line
`sed -n '10p' myfile.txt` 

#### Do replacement on all lines except line 5
`sed '5!/s/foo/bar/' file.txt`

#### Do replacement on lines matching regex (eg: lines starting with 'hello')
`sed '/^hello/ s/h/H/' file.txt ` 



#### Do replacement from line 5 to end of file
`sed '5,$ s/foo/bar/' file.txt `


#### Delete empty files
`sed  '/^$/d' file`



#### Print lines between two regex matches
`sed -nE '/^foo/,/^bar/p' file.txt`

#### Use custom delimiters to make it easy for some strings that contain slashes
`sed 's_/bin/bash_/bin/sh_' file.txt ` 

#### Custom delimiters for regex address combined with the classical delimiter for substitute command (you could also use there a custom delimiter). Useful for paths.
`sed '\_/bin/bash_s/grep/egrep/' file.txt`
* or using the same delimiter for clarity `sed '\_/bin/bash_s_grep_egrep_' file.txt`

#### Insert a space between lowercase/Uppercase characters using & (which represents the regex match)
`sed 's/[a-zA-Z]/& /g' file.txt `

#### Keep the first word of every line (where word is defined by alphanumeric chars + underscores for simplicity sake)
`sed -E 's_[a-zA-Z0-9_]+.*_\1_' file.txt `


#### Switch the first two words 
`sed -E 's_([a-zA-Z0-9_]*) ([a-zA-Z0-9_]*)_\2 \1_' f1`


#### Remove duplicate words separated by a single space (but not triplicate)
`sed -E 's_([a-zA-Z0-9_]+) \1_\1_ig' f1`

#### Search and replace for pattern, write just the lines with the replacements in a new file
`sed  's_foo_bar_w replaced.txt' file.txt  `

#### Multiple replacements
`sed -e 's_foo_bar_' -e 's_hello_HELLO_' file.txt `

#### Multiple replacements by using a sed script
```
#!/usr/bin/sed -f
s/a/A/
s/foo/BAR/
s/hello/HELLO/
```
* Make executable with `chmod +x myscript.sed`, call with `./myscript.sed myfile.txt`


#### Multiple commands using the ; operator which in theory concatenates commands (WARNING! It won't work as expected with certain commands such as 'r' or 'w'. Use a sed script instead OR put the command dealing with filenames last). Print line 10 and insert before line 5. 
`sed '10p;5i\"INSERTED BEFORE LINE 5" file.txt ` 


#### Remove comments between lines starting with these two keywords. Empty lines will be put there instead
`sed -E '/start/,/end/ s/#.*//' file.txt `

#### Delete comments starting with # (no empty lines left behind)
`sed -E '/^#/d' f1`

#### Insert an empty line after pattern  (after each line containing comment in this case)
`sed '/^#/G' file.txt `


#### View lines minus lines between line starting with pattern and end of file 
`sed  '/start/,$ d' file.txt `

#### View lines except lines between line starting with pattern and line ending with pattern
`sed -rn '/start/,/end/ !p' file.txt `

#### Print until you encounter pattern then quit
`sed  '/start/q' file.txt `

#### Insert contents of file after a certain line
`sed  '5 r newfile.txt' file.txt `

#### Append text after lines containing regex (AFTER FOO)
`sed '/foo/a\AFTER FOO' file.txt `

#### Insert text after lines containing regex (BEFORE FOO)
`sed '/foo/i\BEFORE FOO' file.txt `

#### Change line containing regex match
`sed '/foo/c\FOO IS CHANGED' file.txt `

#### Nested sed ranges with inversion. Between lines 1,100 apply actions where the pattern DOESN'T match.
```
#!/usr/bin/sed -f
1,100 {
	/foo/ !{
		s_hello_HELLOOOOWORLD_
		s_yes_YES_
	}
}

```


#### Use nested addresses with change, insert and append to modify: the line before match, the line with match, the line after match.
```
#!/usr/bin/sed -f
/^#/ {
i\
#BEFFORE ORIGINAL COMMENt
a\
#AFTER ORIGINAL COMMENT
c\
# ORIGINAL COMMENT IS NOW THIS LINE
}

```

#### Insert new line before the first comment, after the first comment put in the contents of file and quit immediately afterwards
```
#!/usr/bin/sed -f
/^#/ {
i\#BEFORE COMMENT
r myotherfile.txt
q
}
```

#### Transform text 
`sed 'y/abc/ABC/' file.txt `


#### Copy all the comments (starting with #) to a new file
`sed -E '/^#/w comments.txt' file.txt `

#### Print every second line (substitute ~3 for third line, etc)
`sed -n '1~2p' file.txt `

#### Edit file in place but also create a backup
`sed -i.bak 's/hello/HELLO/' file.txt `

#### Append two extra lines after regex match
 `sed -E '/^#/G G' file.txt ` 

# grep

#### Search for match (the string 'hello') in file (called generically 'file'). Display every line that matches pattern (in this case every line containing 'hello')
`grep hello file`

#### Search for match in file and use quotes on the pattern. Not required unless you have special chars that are expanded by the shell. (in this case not required)
`grep 'hello' file`

#### Search for a match in multiple files
`grep hello file1 file2`

#### Search for match in all files in current dir (will show a warning if dirs are present too)
`grep hello *`

#### Search for a match in all files in curent dir. Don't show errors if dirs are present. (grep treats dirs just as ordinary files and tries to "read" them). '-s' is for silent. Will also skip errors regarding nonexistent files.
`grep -s hello *`

#### Search for a match in all files than end with '.py'
`grep hello *.py`

#### Search for match in all files in current dir. Suppress warning if dirs are present. (it searches for 'hello' in all files. 'skip' is an action passed to '-d'). Show warnings about unexisting files.
`grep -d skip hello *`


#### Case insensitive
`grep -i Hello file` 

#### Invert search
`grep -v hello file`

#### Combine options. Case insensitive AND invert search
`grep -iv Hello file`


#### Use regex. (search for either 'year' or 'Year')
`grep '[Yy]ear' file`


#### Use basic regex (default). Match literal 'years+' in string. ('?+{|()' have no special meaning). Don't match 'years', 'yearss', 'yearsss', etc.
`grep 'years+' file`

#### Use extendend regex. Match 'years', 'yearss', 'yearsss', etc. ('+' means one or more of the chars before it, in this case an 's'). '?+{|()' have special meaning.
`grep -E 'years+' file`

#### Same as above (extended regex)
`egrep 'years+' file`


#### Match whole words. Will match ' year ' but not 'goodyear'
`grep -w year file`

#### Match whole lines. Will match 'year' (where 'year' is the single word on a line. Won't match 'one year', 'goodyear'.
`grep -x year file`

#### Treat the search pattern literally, not as a regex. Will match the literal '[Yy]ear' but won't match 'year'.
`grep -F '[Yy]ear' file`

#### Search for multiple patterns. Match both 'year' and 'hello'.
`grep -e hello -e year file`

#### Read search patterns from a file. Each pattern on a new line. Match all found patterns. 'patterns.txt' can have 'word' on one line, '[Yy]ear' on the second, etc.
`grep -f patterns.txt file`

#### Read search patterns from file AND from text passed to option '-e'. Match all found patterns.
`grep -f patterns.txt -e '[Ee]xtra' file`


#### Count matching lines for pattern (NOT matching patterns). Display a number - how many lines matched. 
`grep -c hello file`

#### Count matching lines for every file except dirs (supressed with '-s'). Display how mayn lines matched (for every file). Will show multiple files with 0 or more matches. 
`grep -sc hello *`


#### Print ONLY file names where match found (don't print the actual matches).
`grep -l hello *.txt`


#### Print ONLY file names where match NOT found.
`grep -L hello *.txt`


#### Search for pattern only whithin the first Nth lines. (only in the first 10 lines in the example)
`grep -m 10 hello file`

#### Search for pattern whithin Nth lines for every file in current dir. Skip dirs. Note how we concatenate '-m' and '10'. We could've alse written them with a space, like '-m 10'
`grep -sm10 hello *`


#### Print only the matched parts, without the surrounding text. Example will print 'year', 'Year', 'YEAR', etc - each an a new line
`grep -o [Yy]ear file`


#### Supress error messages about files not existing.
`grep -s hello file nonexisting_file`

#### Print filename before each match. Eg: 'file:goodyear' (default when multiple files are searched).
`grep -H year file`


#### Supress printing filenames before each match (even if multiple files are searched)
`grep -h year file file2`

#### Add line number before each output line (Eg: '1:goodyear')
`grep -n year file`

#### Print both line number and file name (eg: 'file:3:goodyear'). '-H' will force to display filename even if just one file (by default not shown). '-s' suppress dir missing warns.
`grep -nHs year *`

#### Also print N trailing lines AFTER matched line. (show N lines AFTER the matched line)
`grep -A 2 year file`

#### Also print N trailing lines BEFORE matched line. (show N lines BEFORE the matched line)
`grep -B 2 year file`

#### Print 2 lines before and 4 lines after matched line.
`grep -B2 -A4 hello file`


#### Also print N lines BEFORE and N lines AFTER matched line (eg: 2 before and 2 after)
`grep -C 2 year file`

#### Force process binary files. Without this you'll get 'grep: /usr/bin/pamon: binary file matches'. (search for string 'au' in binary file)
`grep -a au /usr/bin/pamon`

#### Exclude files that match this pattern. (eg: don't search .py or .c files)
`grep --exclude=*.py --exclude=*.c year *`

#### Include files that match this pattern. Use in conjuction with --exclude. (exclude all .py files and then include only 'main.py' in the search)
`grep --exclude=*.py --include=main.py year *`


#### Search recursively in dir (go as deep as possible, searching for files). DON'T follow symlinks. No warning about searching dirs shown.
`grep -r hello`

#### Exclude dirs from searching. Useful when using '-r' to skip certain dirs, such as '.git'
`grep hello -r --exclude-dir='.git'`

#### Seach recursively in dir. If simlynk encountered, follow it and search the file pointed by the symlink. 
`grep -R hello`

#### Print total byte count before matched lines. First line (from file, not matched line) has a count of '0'. Eg: line 1 - '0:abc', line 2 '4:def'. It shows 4 because it has counted 4 bytes until now ('abc' + newline from the first file)
`grep -b hello file`

#### Search for 'hello' in files that might start with the '-' character. Without the '--' a file like '-myfile' won't be searched. WARNING - having such a file in your dir will BREAK "normal" grep functioning (eg: `grep hello *` WON'T SHOW all 'hello' lines from files. Reason is that when it encounters file '-x' it treats it as an option since it expands the `*` wildcard)
`grep -- hello *`

#### Sausage options 1. Search in binary files (text too but friendly toward binaries). Print byte count (or offset as grep calls it), force filename, ignorecase, show match only, also show line count, search recursively in this dir. Output is like 'hau/f:15:193:hello'
` grep -abHionr hello`.


# awk


#### Prerequisites
* Make sure you have 'gawk' installed. It has more features than the usual default 'mawk'. You can see what implementation af awk you're using by typing `man awk` and looking at the header. If you have 'mawk' just install awk with `sudo apt-get install gawk`

#### Intro
* Awk operates on records and fields. A record is by default a line. A field is a "word" by default. You can change how a record/field is defined by changing the field separator var (FS) and the record separator (RS).
* You give awk some files on which to operate. Awk starts reading records (lines) from these files one by one. It splits each record (line) into fields (words). 
* In your awk program you have code that will look like this  `/bilbo/ {print $0}`. `/bilbo/` is a pattern. `{print $0}` is an action (inside the curly braces). 
* On each record (line) that matches pattern (`/bilbo/`) execute actions inside curly braces (`{print $0}`). 
* The special variable `$0` represents the whole record (line). `print` prints.


#### Note about lines/records fields/words
* Usually you'll want to keep working with records which are "lines". That is - strings delimited by newlines. I'll also be referring to records as lines to make things simpler. 
* You can change how awk interprets records. Then records will be other things which are not "lines" (like strings separated by commas for example).  
* The same goes for fields. Fields are strings separated by space by default. You can change this of course to have the separator be a comma or something else.
* Because I just told you you'll know that a record is a line by default. But you could change it to something else (by changing the RS - the Record Separator). You also know that a field is a word (separated by space) by default - but you could change it by changing the FS - Field Separator. 


#### How to call awk
* You call it like `awk '{print $0}' file1 file2`
* In the examples below I sometimes show the text inside '', not the full blown bash command.
* If there are multiple files you have to use an awk script. Make a new file and put inside it:
```
#!/usr/bin/awk -f
BEGIN {print "BEGINNING"}
/Gollum/ {print "I like it raw and riggling"}
```
* Name it something like `myscript.awk` and make it executable with `chmod +x myscript.awk`
* Call it with `./myscript.awk file1 file2`


#### Simple pattern

* So you can read this awk program `/bilbo/ {print $0}` as: 
>> for each record (line) in all the files passed to awk
>>> if record (line) matches `/bilbo/` pattern
>>>> print the whole record (line)

#### Field
* How about `/bilbo/ {print $1}`? `$1` represents the first field (word) from the current record (line). `$2` is second field (word) and so on. 
* You can read `/bilbo/ {print $1}` as:
>> for each record (line) in all the files passed to awk
>>> if record (line) matches `/bilbo/` pattern
>>>> print the first field (word) from the record (line)


#### Pattern AND Pattern
* Patterns can be more complex. Check this out `/bilbo/&&/frodo/{print "my precious"}`
* You can read this as:
>> On each record (line) that matches `/bilbo/` AND `/frodo/`
>>> print the string "my precious"

#### Pattern OR Pattern
* Check this other pattern out `/bilbo/||/frodo/{print "Is it you mister Frodo?"}`
* You can read this as:
>> On each record (line) that matches `/bilbo/` OR `/frodo/`
>>> print the string "Is it you mister Frodo?"



#### NOT Pattern
* What about `! /frodo/ { print "Pohtatoes" }`? (note the extra spaces put there for clarity. You could also eliminate them to save typing)
* Read it as: 
>> On each record (line) that DOESN'T match `/frodo/`
>>> Print "Pohtatoes"

#### IF Pattern present then check for Pattern, ELSE check for Pattern
* Here's a more complex example `/frodo/ ? /ring/ : /orcs/{ print "Either frodo with the ring, or the orcs" }`
* `a?b:c` is a ternary operator. It reads: if a then do b, else do c.
* Read it as:
>> Read record.
>>> If it matches `/frodo/`
>>>> Does it also match `/ring/`? If yes then print "Either frodo with the ring, or the orcs"
>>> If it doesn't match `/frodo/`
>>>> Does it match `/orcs/`? If yes then print "Either frodo with the ring, or the orcs"
* The actions are executed if: a record (line) contains either ("frodo" and the "ring") OR (no "frodo" and "ring") but "orcs". 


#### Pattern Range
* How about this one? `/Shire/ , /Osgiliath/ { print $0 }`?
* The comma separated regex expressions are a "pattern range". 
* Read this as:
>> Execute command for each record (line) 
>>> Between the record (line) that matches `/Shire/` (including that record (line))
>>> And record (line) that matches `/Osgiliath/` (including that record(line))
* If you have a file that looks like:
```
What is it mister Frodo? 
Do you miss the Shire?
I miss the shire too.
This Osgiliath is to drab for me. 
Too many orcs.
```
* The above command will simply print the records(lines):
```
Do you miss the Shire?
I miss the shire too.
This Osgiliath is to drab for me.
```


#### BEGIN Pattern
* You'll be interested in this one. `BEGIN {print "And so it begins"}`
* BEGIN is a special pattern, triggered right at the beginning, before any input from files is read.
* NOTE - BEGIN will execute it's command even if no files are passed
* It reads:
>> Before any input is read
>>> Print "And so it beggins"

#### END Pattern
* If something begins, it has to end, right? `END {print "There and back, by Bilbo Baggins"}`
* END is triggered when awk has finished reading input from all files. Thus it needs files passed to awk in ordered to be triggered
* It reads:
>> After all input was read
>>> Print "There and back, by Bilbo Baggins"


#### BEGINFILE, ENDFILE Patterns
* If you pass multiple files to awk - it will treat them as being one contiguous file. But what if you want to call some commands when beginning to read input from a file? You use BEGINFILE and ENDFILE respectively.
* `BEGINFILE {print "A new chapter is beginning mister Frodo"}`
* It reads:
>> Before input is read from a file
>>> print "A new chapter is beginning mister Frodo"

#### NO Pattern
* This a simple one. `{print $1}`. 
* When no pattern is provided, just the command in curly braces, it is executed for all records (lines).
* This reads
>> For all records (lines):
>>> print the first word

#### Conditional Pattern
* `NF>4{print}`
* The pattern is `NF>4`. If true then exec commands inside curly braces.
* NF is the Number of Fields(words). If the current record (line) has more than 4 fields (words), print the record (line)



#### About commands
* All commands MUST be inside curly braces. You can't just call `print $0`. You have to put that command inside curly braces - `{print $0}`


#### Variables
* Awk has some built in variables. They are written in uppercase. 
* They can be useful in a variety of cases. 
* `FILENAME` is the name of the current file being processed. 
* `BEGINFILE {print "we are beginning to process " FILENAME}` will print the string followed by the name of the file when awk begin processing file. 
* `NR` is the Record Number (or Number Record if you'd like). In simple terms it's the record (line) count. If awk is processing line number 5 then NR is 5. 
* Let's say you have a 10 line file and a 5 line file. You pass both files to awk. Awk finishes the first 10 lines and is now at line 3 from the second file. How much will the NR be? 
* You might be tempted to say 3 - but it's 13. NR stands for ALL the records (lines) that awk processes, not records (lines) belonging to file.
* If you want to refer to record (line) count for file you would use FNR. F - from File. File Number Record. 
* In the case above NR would be 13 but FNR would be 3. 

#### More Variables
* Here's a list of the most important variables with a short description
* `FILENAME` - name of file
* `FNR` - File Number Record (input record number for current file -  according to official docs). File record (line) count.
* `FS` - Field separator (word separator if you'd like). Space by default. 
* `IGNORECASE` - if set to 1 case is ignored. Very important. `/frodo/` would match `Have you seen my old ring Frodo?` if IGNORECASE is 1. If set to 0 it would not. Use it like this:
```
BEGIN {IGNORECASE=1}
/frodo/ {print "do you remember the taste of strawberries Frodo?"}
```
* `NF` - number of fields (words) in the current record (line). You could use this to count words for example. 
* `NR` - Number Record. Global record (line) count if you will.
* `RS` - Record Separator. A newline by default.


#### Programming intro
* Awk is a full blown programming language. It has all the operators & syntax you would expect from a "regular" programming language such as C or python. 
* This is taken directly from the manual
```
 Operators
       The operators in AWK, in order of decreasing precedence, are:

       (...)       Grouping

       $           Field reference.

       ++ --       Increment and decrement, both prefix and postfix.

       ^           Exponentiation (** may also be used, and **= for the assignment operator).

       + - !       Unary plus, unary minus, and logical negation.

       * / %       Multiplication, division, and modulus.

       + -         Addition and subtraction.

       space       String concatenation.

       |   |&      Piped I/O for getline, print, and printf.

       < > <= >= == !=
                   The regular relational operators.

       ~ !~        Regular expression match, negated match.  NOTE: Do not use a constant regular expression (/foo/) on the left-hand side of a ~ or !~.  Only use one on the right-hand side.  The expression
                   /foo/ ~ exp has the same meaning as (($0 ~ /foo/) ~ exp).  This is usually not what you want.

  &&          Logical AND.

       ||          Logical OR.

       ?:          The  C  conditional  expression.  This has the form expr1 ? expr2 : expr3.  If expr1 is true, the value of the expression is expr2, otherwise it is expr3.  Only one of expr2 and expr3 is
                   evaluated.

       = += -= *= /= %= ^=
                   Assignment.  Both absolute assignment (var = value) and operator-assignment (the other forms) are supported.

   Control Statements
       The control statements are as follows:

              if (condition) statement [ else statement ]
              while (condition) statement
              do statement while (condition)
              for (expr1; expr2; expr3) statement
              for (var in array) statement
              break
              continue
              delete array[index]
              delete array
              exit [ expression ]
              { statements }
              switch (expression) {
              case value|regex : statement
  ...
              [ default: statement ]
              }

```


#### Programming usage
* Here's how you could use the "programming" part of awk:
```
#!/usr/bin/awk -f
BEGIN {
	IGNORECASE=1
	hobitses=0
}
/fellowship/ {
	if (index($0,"samwise") >0 ) {
		hobitses+=1
		print "Hurry up hobitses"
	}
}
END {
	print "Found a total of " hobitses " hobitses"
}
```

* Let's break this down.
* You see that this is an awk script because of the shebang (the line beginning with `#!/...`)
* Before reading any input set the built in var IGNORECASE to 1. "frodo" will match "Frodo", "FRODO", "frodo", etc.
* We also set a custom variable for our own usage and set its initial value to 0 (hobitses)
* Check all records (lines) and on those that match `/fellowship/` execute the following:
>> Check if we have the string "samwise" inside the record (line). Index is a built in function. It takes two strings. If the second string is contained within the first it will return a value bigger than 0. If the second string is not present in the first return 0.
>> If index() returns a value bigger than 0 ("samwise" was found inside the current record (line)) do the following:
>>> increase hobitses by 1
>>> print a message
* After processing all the records print a message with value of our custom variable "hobitses".


#### Options
* You can pass options to awk. Here are some useful ones:
* `-f` read awk source file. `awk -f source.awk file.txt`. You put all awk code in the source.awk file. NOTE - don't put the shebang (`#!/usr/bin/awk -f`)
* `-F` - field separator. Use to define "words". For example if you have a .csv you could make the separator a comma. Like this: `awk -F, '{print $2} file.txt ' - will print the second "word" separated by comma.
* `-v` assign a variable. Eg: `awk -v count=0 '/bilbo/{count+=1;print "Found another one. Now count is " count}' f1`. init count to 0. On records matching `/bilbo/` increment count by 1. Print the message.
* `-e` - execute commands. Use multiple awk commands. Eg: `awk -e 'BEGIN {IGNORECASE=1}' -e '/bilbo/{"Found him"}'`


#### String concatenation
* `print "hello" "world"` will output "helloworld". No space between.
* `print "hello","world"` will output "hello world". A space between by using a comma in print.

#### System commands
* You can execute system commands like so `awk '{system("ls "$1" -la")}' file.txt `.
* Let's break it down. We start by calling the system function on every record (line). We pass an argument built dynamically. "ls " followed by the first field (word) followed by " -l". If the field was "myfile.txt" the command would be "ls myfile.txt -la"
* note the spaces at the end in "ls ". If you don't put the space the command would look like "lsmyfile.txt -la" which won't work obviously. 
* It will output something like:
```
-rw-rw-r-- 1 me me 0 Nov 14 17:40 f1
-rw-rw-r-- 1 me me 59 Nov 14 17:41 f2
-rw-rw-r-- 1 me me 20 Nov 12 15:42 col1
```
* Basically we run `ls` on the first word of every file passed to awk.


#### Writing dynamically to files
* If you find a certain pattern you might like to write that to a file (with some extra info maybe).
* `awk '/Bilbo/{print "I found him. First word is " $1 >> "appended.txt"}' file.txt `. It will append the print message (followed by first field(word)) to the file appended.txt
* Use '>' instead of '>' to overwrite the file (instead of appending)


#### System commands with stdin
* You can pipe with print into certain system commands. Here's an example `awk '{print "file.txt" | "xargs -n1 ls -l"}'`
* "file.txt" is piped into the command "xargs -n1 ls -l". Similar to `echo "file.txt" | xargs -n ls -l`
* The advantage is that you can pass "dynamic" arguments. Certain fields (words) for example.
* The output looks like:
```
-rw-rw-r-- 1 me me 0 Nov 14 17:40 file.txt
```

#### Getline example
* `"date" | getline cur_date` - run "date", store into variable cur_date. This a simple use for getline.
* Here's a cooler one `awk '{"du "$0" |cut -f1" |getline cur_size;print "for file " $0 " size is " cur_size}' filenames.txt`
* Let's break it down. "du "$0" |cut -f" will look like "du myfile.txt | cut -f1". This linux command will output the size of the file in kb.
* We pass this value to the variable "cur_size". 
* We use a semicolon to separate commands. Next we print the value of the variable "cur_size"


#### $ - the positional variable
* $ is not a "normal" variable but rather a function triggered by the dolar sign (according to grymoire awk tut)
* `X=; print $X;` means `print $1`. 
* You can do more fancy stuff with this. `'{print $(NF-1)}' - NF is the number of fields (words) in the record (line). `$NF` would be the last field (word) (if there are 5 fields NF is 5. $NF will point to the 5th field (word))

#### Modify the positional variable
* You can modify a certain field (word) and print the record (line) containing that modified field.
* `echo "Meat's back on the menu" | awk '{$5="NO_MENUS_IN_ORC_LAND";print}'` will output `Meat's back on the NO_MENUS_IN_ORC_LAND`
* We assign a value to field number 5. Then we print the record (print with no arguments prints the current record, just like `$print $0`)


#### Selective .csv column print
* You have a .csv in the form:
```
city,area,population
LA,400,100
Miami,500,101,
Buenos Aires,800,102
```
* As you can see you have 3 columns. You would like to print the 1st and 3rd column only.
* This should do the trick `awk -F, '{print $1,$3}' cities.csv`
* We set a custom field (word) separator with `-F,`.
* Note the comma between the positional arguments. IF you remember `{print "a" "b"}` will output "ab". You need comma to separate by space, `{print "a","b"}`. 
* You can get fancy and skip the header row of the csv with `awk -F, '{if (NR>1) print $1,$3}' cities.csv `. If Number Record is bigger than 1 (not the first) only then print. 

#### Custom field separator with OFS
* `{print "a", "b"}` will output "a b". When a comma is present awk uses the output field separator (OFS) which is a space by default.
* You can change OFS to something else, like `::` for example. `{OFS="::";print "a", "b"}` will output `a::b`


#### Mix with command line text
* Let's say you have a file produced by the `ls` command, like this:
```
drwxr-xr-x  2 root root        69632 Nov 13 19:21 .
drwxr-xr-x 16 root root         4096 Nov  9 07:35 ..
-rwxr-xr-x  1 root root        59888 Dec  5  2020 [
-rwxr-xr-x  1 root root        18456 Feb  7  2021 411toppm
-rwxr-xr-x  1 root root           39 Aug 15  2020 7z
-rwxr-xr-x  1 root root           40 Aug 15  2020 7za
-rwxr-xr-x  1 root root           40 Aug 15  2020 7zr
-rwxr-xr-x  1 root root        35344 Jul  1 00:42 aa-enabled
-rwxr-xr-x  1 root root        35344 Jul  1 00:42 aa-exec
```
* you want to print the name of the executable but only if it's smaller than 50 bytes. Use this `awk '{if ($5<50) print $9}' bin_10 `
* If the fifth field (containing size in bytes) is smaller than 50 print the 9th field (name of executable)

#### Math on text.
* This is one of the cool things about awk. You can take some text, perform all kinds of programming magic on it, spit it out nice and modified. Usually you need to write a lot of boilerplate if you're using a general scripting language like python. 
* Let's take the cities .csv again:
```
city,area,population
LA,400,100
Miami,500,101,
Buenos Aires,800,102
```
* Check out this script.
```
#!/usr/bin/awk -f
BEGIN {
	total=0
	FS=","
}
{
	if (FNR>1) {
		real_pop=$3 * 1000
		total+=real_pop
		print "Real population of", $1, "is" ,real_pop
	}
}

END {
	
	print "Total Population:", total
}
```

* The output will be:
```
Real population of LA is 100000
Real population of Miami is 101000
Real population of Buenos Aires is 102000
Total Population: 303000
```
* By now you should understand what this does. We start by creating a variable and setting it to 0. We set the Field separator to a comma because we have a csv.
* If the File Number Record is bigger than 1 do stuff (note how we use FNR, not NR. That's because we want to skip the header of every file, not just the first file).
* We do some math, addition/multiplication. Finally we print the total. This works with multiple csv files. 
* With a bit of care we could cram it on 1/2 lines in the terminal. 


#### Fancy line numbers
* Look at this beaut: `awk '{print "(" NR ")" ,$0}' f1`
* It will print the line number in parenthesis followed by the actual line (from a file that doesn't have line numbering). Something like :
```
(1) line one
(2) line two
```
* Carefully study the spaces and commas from print. We know that by not using a command strings will be concatenated without any separator in between. So `"(" NR ")"` will output something like `(9)`. We could even dispense with the spaces in the command (`"("NR")"`) but I've kept them because they make things clearer. Next we print the actual line. Note the comma. It means awk will put a space (OFS) between the parenthesised line number and the actual line.


#### Print words by their number 
* It's time to get fancy and change Field Separator and Record separator.
* By default RS is a newline. What happens if you change it into an empty string? 
* Vim  will load all the file in memory and treat it as a single record. 
* FS (Field Separator)remains the same. But since the whole file it's treated as a single record the positional variable will refer to word number in FILE (not in record/line).
* Sounds complicated? It's not. You have a file like this:
```
first second  third
fourth fifth sixth
seventh eight
```
* This is the awk script.
```
#!/usr/bin/awk -f
BEGIN { RS="" }
{
		print $1, $8
}
```
* It will output `first eight`. 
* Look at the script again. If RS would be a newline `print $1, $8` would print something like:
```
first 
fourth
seventh
```
* It prints the first field on each record but since records are lines it can't find the eight field (word). 
* But when the whole file is treated as a single record the positional variable ($) refers to field number in whole file. 
* You can put this on a one liner and use it daily: `awk 'BEGIN{RS=""}{print $1020}' file.txt `


#### Pass stdin to awk (and show nicely formatted size of files)
* `ls -lh | awk '{print $9,"has size of",$5}'`
* Generate text with `ls -lh` that looks something like:
```
-rwxr-xr-x  1 root root           39 Aug 15  2020 7z
-rwxr-xr-x  1 root root           40 Aug 15  2020 7za
-rwxr-xr-x  1 root root           40 Aug 15  2020 7zr
```
* pipe it to awk. Print field $9 (filename) and field $5 (size)



#### Pass both stdin and file to awk
* `echo "Coming from stdin" | awk "{print}" file.txt -`
* In the above command awk first processes file.txt then stdin (passed by using the final dash)


#### Check if text coming from stdin or file
* `{ if (FILENAME!="-") print $0 }` it's the script.
* When you pass it both stdin and files  `echo "Coming from stdin" | awk "{print}" file.txt -` awk won't process text from stdin.


#### Arrays intro
* `{myarr=["one","two","three"]}` - WON'T work, even though this is the syntax used by many languages to declare arrays
* Use instead something like `{myarr[0]="one";myarr[1]="two"}` 
* Arrays are associative. You associate an index with a value. Like a dictionary.
```
#!/usr/bin/awk -f
{
		myarr["hobits"]="hobitses"
		print(myarr["hobits"])
}
```
* As you can see you can use strings and numbers as an index (as a key). In the end though array subscripts are always strings. 
* That is `{print myarr[5.1]}` will check for an '5.1' string index. 

#### Store lines in array
```
#!/usr/bin/awk -f
/bilbo/ {
		myarr[NR]=$0
}
END{
		for (i in myarr){
					print "subscript is",i
							print myarr[i]
								}
}
```
* For each record (line) that matches `/bilbo/` store the record(line) in 'myarr', using the current NR (Number Record - or current line count) as a subscript/index

* At the end enumerate over indexes in my arr (NOT over elements) with a 'for..in' loop. 
* It will print something like:
```
subscript is 3
a story by frodo and bilbo
subscript is 13
bilbo again and frodo
```
* Note how this array is not contiguous. It is sparse. The indexes between 0-3 and 3-13 don't exist and are not assigned automatically. Kinda make sense since awk uses strings and NOT integers as indexes/subscripts.


#### Delete array elems
* `{delete myarr[1]}` will delete element at index '1' (where '1' is a string). 
* `{delete myarr}` - will delete ALL elements of the array


#### Array index concatenation
```
#!/usr/bin/awk -f
BEGIN{
	arr[1,2]="one"
	arr["abc","bcd"]="two"
	arr["abc",1]="three"
	arr["foo" "bar"]="four"
	arr["bar" "foo"]="five"
	for (i in arr){
		print "at index",i,"value is",arr[i]
	}
}
```
* Will output:
```
at index abc1 value is three
at index abcbcd value is two
at index foobar value is four
at index barfoo value is five
at index 12 value is one
```
* Awk treats integers as strings when used as array indexes. Then it concatenates them if you pass multiple values, either separated by space, comma or not separated at all. There is no space between these concatenated values.

* Another thing to note is that the 'for..in' loop doesn't enumerate elems in the order they were added. 


#### Ordered array indexes
```
#!/usr/bin/awk -f
BEGIN{
	i=0
	arr[""]=0
}
/bilbo/{
	arr[i++]=$0
}
END{
	for (j=0;j<i;j++){
		print "at index",j,"value is",arr[j]
	}
}
```
* Will output:
```
at index 0 value is a story by frodo and bilbo with ring
at index 1 value is bilbo again and frodo and orcs
```
* We start by declaring a variable 'i' and assign a value zero. Then we initialize an empty array (using the empty string to declare a dummy zero value). We could skip the array initialization step and the output would be the same - BUT the code is clearer this way.
* For every record that matches `/bilbo/` store the line. Use current value of 'i' as index and increment it afterwards. 
* At the end use a for loop to enumerate. 'i' now has a value of the array size. We use the new variable 'j' to enumerate and check against 'i' which is array size.

#### The power of printf
`{printf("%d is nice but %.2f is better",1,2)}`
* will output:
`1 is nice but 2.00 is better`
* printf is "formatted" print (thus the extra f). It uses escape sequences and format specifiers that should be familiar to most programmers.
* `%d` is a decimal. `%f` is a float. `%.2f` - float with 2 digits. 
* Here are all the format specifiers (taken from awk man):
```
The printf Statement
       The  AWK  versions of the printf statement and sprintf() function (see
       below) accept the following conversion specification formats:

       %a, %A  A floating point number  of  the  form  [-]0xh.hhhhp+-dd  (C99
               hexadecimal floating point format).  For %A, uppercase letters
               are used instead of lowercase ones.

       %c      A single character.  If the argument used for %c  is  numeric,
               it  is treated as a character and printed.  Otherwise, the ar‐
               gument is assumed to be a string, and the only first character
               of that string is printed.

       %d, %i  A decimal number (the integer part).

       %e, %E  A  floating  point number of the form [-]d.dddddde[+-]dd.  The
               %E format uses E instead of e.

       %f, %F  A floating point number of the  form  [-]ddd.dddddd.   If  the
               system  library  supports it, %F is available as well. This is
               like %f, but uses capital letters for special “not  a  number”
               and “infinity” values. If %F is not available, gawk uses %f.

       %g, %G  Use %e or %f conversion, whichever is shorter, with nonsignif‐
               icant zeros suppressed.  The %G format uses %E instead of %e.

       %o      An unsigned octal number (also an integer).

       %u      An unsigned decimal number (again, an integer).

       %s      A character string.

       %x, %X  An unsigned hexadecimal number (an integer).   The  %X  format
               uses ABCDEF instead of abcdef.

       %%      A single % character; no argument is converted.

       Optional,  additional parameters may lie between the % and the control
       letter:

       count$ Use the count'th argument at  this  point  in  the  formatting.
              This is called a positional specifier and is intended primarily
              for use in translated versions of format strings,  not  in  the
              original text of an AWK program.  It is a gawk extension.

       -      The expression should be left-justified within its field.

       space  For  numeric  conversions, prefix positive values with a space,
              and negative values with a minus sign.

       +      The plus sign, used before the width modifier (see below), says
              to  always  supply  a sign for numeric conversions, even if the
              data to be formatted is positive.  The +  overrides  the  space
              modifier.

  #      Use  an  “alternate form” for certain control letters.  For %o,
              supply a leading zero.  For %x, and %X, supply a leading 0x  or
              0X for a nonzero result.  For %e, %E, %f and %F, the result al‐
              ways contains a decimal point.  For %g, and %G, trailing  zeros
              are not removed from the result.

       0      A  leading  0  (zero)  acts  as  a flag, indicating that output
              should be padded with zeroes instead of spaces.   This  applies
              only  to the numeric output formats.  This flag only has an ef‐
              fect when the field  width  is  wider  than  the  value  to  be
              printed.

       '      A  single quote character instructs gawk to insert the locale's
              thousands-separator character into decimal numbers, and to also
              use  the  locale's  decimal point character with floating point
              formats.  This requires correct locale support in the C library
              and in the definition of the current locale.

       width  The  field  should  be padded to this width.  The field is nor‐
              mally padded with spaces.  With the 0 flag, it is  padded  with
              zeroes.

       .prec  A  number  that  specifies  the precision to use when printing.
              For the %e, %E, %f and %F, formats, this specifies  the  number
              of  digits  you want printed to the right of the decimal point.
              For the %g, and %G formats, it specifies the maximum number  of
              significant  digits.   For  the %d, %i, %o, %u, %x, and %X for‐
              mats, it specifies the minimum number of digits to print.   For
              the  %s  format,  it specifies the maximum number of characters
              from the string that should be printed.
```


#### Selective file processing
* You can skip processing certain files using a combo of FILENAME and nextfile command
```
#!/usr/bin/awk -f
{
	if (FILENAME=="skip.txt" || FILENAME=="skip2.txt"){
		nextfile
	}
	print $0
}
```
* 'nextfile' will start processing the next file immediately.


#### Skip records (lines) based on a certain condition
* ` awk '{if (NF>4) next; print}' file.txt`
* If NF (Number of Fields) is bigger than 4 stop processing current input record and get the "next" one. Else print the record.
* This will in effect print lines that have 4 or less fields (words)

#### Some math funcs
```
{
	print "log",log($1),$2
	print "col",cos($1),$2
	print "sin",sin($1),$2
	print "rand",rand()
}
```
* Pretty self explanatory math funcs. rand() will print a rand float between 0 and 1.

#### Print some random integers (1000 rand ints between 0-100)
```
#!/usr/bin/awk -f
BEGIN {
for (i=0; i<1000; i++) printf("%d\n",rand()*100)
}
```
* We create random ints between 1 and 100 by multiplying the rand float (between 0-1) with 100 and cutting off the digits by using a `%d` (decimal) in printf, which treats any numbers as ints (an cuts the digits)

#### String funcs - index
```
#!/usr/bin/awk -f
{
	i=index($0,"bilbo")
	print $0
	if (i>0) print ">>>> INDEX IS",i
}
```
* the 'index()' func searches for the index  of the second string ("bilbo") in the first string (the current record/line). If none found it returns 0.

#### String funcs - length
* very basic but very much needed functionality - the length of a string.
* `awk '{print "[",$0,"]","has length of",length($0)}' file.txt` outputs something like:
```
[ Hello world world ] has length of 17
[ hello andback again Again ] has length of 25
[ a story by frodo and bilbo with ring ] has length of 36
```

#### String funcs - split
* Split a string into parts based on a separator (regex or string).
```
#!/usr/bin/awk -f
BEGIN {
	mystring="The best | leaf in the Shire, isn't it?"
	n=split(mystring,array," ")
	for (i in array) print array[i]
	print "TOTAL splits:",n
}
```
* will output:
```
The
best
leaf
in
the
Shire,
isn't
it?
TOTAL splits: 8
```
* First parameter is the string. The second parameter is an array in which to store the split parts of the string. We can pass an uninitialized variable directly. The last param is the separator. 
* The function returns the number of splits (in the 'n') variable. It returns the split string components in the array "array" passed as argument. 


#### String funcs - substr
```
#!/usr/bin/awk -f
BEGIN {
	mystring="lotr is cool"
	n=substr(mystring,1,3)
	print n
}

```
* will output:
`lot`
* substring returns a portion of the string. First arg is the string. Second is the index from where to start cutting. The indexing begins from 1, NOT 0. So first char is at index 1. The final (optional) arg is the length of the cut. 
* Read it as: get 3 chars from the first char (including the first char). 
* If you don't pass the third arg (length) it will cut until the end of string.
* Use it in a one-liner to print the first X chars of every line ` awk '{print substr($0,1,10)}' file.txt`


#### String funcs - gensub
```
#!/usr/bin/awk -f
BEGIN {
	mystring="Run Halifax. Show us the meaning of haste."
	res=gensub(/[Hh]ali/,"Shadow","g",mystring)
	print res
}
```
* will output:
` Run Shadowfax. Show us the meaning of haste.`
* first arg is the regex to search for. Second is the replacement string. Third is a flag. It can be "g" for global or an integer. If it's "g" it replaces all occurrences of regex in mystring. If it's for example 2 it will replace only the second occurrence of regex. The last arg is the target string (where to do search & replace)
* gensub will return a new string, leaving the original intact. 
* If you look at it you'll notice that it's similar to sed's substitute: `s/[Hh]ali/Shadow/g`
* If the last (target) string not supplied, use $0 (the current record)



#### String func - gsub
* gsub is similar to gensub but has some diffs. It doesn't take the flag argument and replaces globally by default. It also performs search & replace directly on the target string. It returns the number of substitutions (whereas gensub() returns the modified string)
* This make it very useful for modifying records nicely and rapidly. 
* `awk '{gsub(/bilbo/,"MASTER BILBO");print}' file.txt` will search for `/bilbo/` on the current record (line) and replace it with "MASTER BILBO". 


#### String func - sub
* Just like gsub() but only replaces the FIRST occurrence.
* `awk '{sub(/bilbo/,"MASTER BILBO");print}' file.txt` will search for `/bilbo/` on the current record (line) and replace it with "MASTER BILBO", just the FIRST occurrence on the record


#### String func - match
* Returns 0 if a match is not found, otherwise the starting index for the match (index STARTING AT 1, not 0)
* Look at this:
```
#!/usr/bin/awk -f
{
	i=match($0,/bilbo/)
	if (i>0){
		res=substr($0,i,5)
		print res
	}
}

```
* It returns the index for `/bilbo/` in current record. If bigger than 0 then get a substring (from 'i' to 'i'+5)


#### String func - tolower
* `echo "My Precious!" | awk '{print tolower($0)}'` 
* Will output `my precious!`

#### String func - toupper
* `echo "My Precious!" | awk '{print toupper($0)}'` 
* Will output `MY PRECIOUS!`

#### String func - asort
* Sorting an array of numbers or string might come in hand.
```
#!/usr/bin/awk -f
BEGIN {
	for (i=0;i<5;i++){
		arr[i]=rand()*100
		print arr[i]
	}
	print ">> SORTED"
	asort(arr)
	for (i in arr)print arr[i]
}

```
* will output:
```
92.4046
59.3909
30.6394
57.8941
74.0133
>> SORTED
30.6394
57.8941
59.3909
74.0133
92.4046
```

#### Time func - strftime
* ` awk 'BEGIN {print strftime("%a %b %e %H:%M:%S %Z %Y")}' will output `Sun Nov 14 15:09:56 EET 2021`
* Here's the format specifiers (taken from grymoire.com). (The man has some format specifiers but it's not this complete)
```
%a	The locale's abbreviated weekday name
%A	The locale's full weekday name
%b	The locale's abbreviated month name
%B	The locale's full month name
%c	The locale's "appropriate" date and time representation
%d	The day of the month as a decimal number (01--31)
%H	The hour (24-hour clock) as a decimal number (00--23)
%I	The hour (12-hour clock) as a decimal number (01--12)
%j	The day of the year as a decimal number (001--366)
%m	The month as a decimal number (01--12)
%M	The minute as a decimal number (00--59)
%p	The locale's equivalent of the AM/PM
%S	The second as a decimal number (00--61).
%U	The week number of the year (Sunday is first day of week)
%w	The weekday as a decimal number (0--6). Sunday is day 0
%W	The week number of the year (Monday is first day of week)
%x	The locale's "appropriate" date representation
%X	The locale's "appropriate" time representation
%y	The year without century as a decimal number (00--99)
%Y	The year with century as a decimal number
%Z	The time zone name or abbreviation
%%	A literal %.
```


#### Extract the inverse of a regex match
* When you use match() func and get an actual match you'll modify RSTART and RLENGTH. These are 2 built in awk vars that signify the start and the length of the match.
* You can use these vars to extract the inverse of a regex match. This might come handy in certain advanced scenarios. 
```
#!/usr/bin/awk -f
{
	reg="[Bb]ilbo"
	if (match($0,reg)){
		bef=substr($0,1,RSTART-1)
		aft=substr($0,RSTART+RLENGTH)
		pat=substr($0,RSTART,RLENGTH)
		print bef,"|",pat,"|",aft
	}
	else print $0
}

```
* This will output something like:
```
Hello world world
hello andback again Again
a story by frodo and  | bilbo |  with ring with bilbo
 | Bilbo |  baggins baggins baggins
```
* We start by checking for a match. If a match exists commands inside if will run (since it will return a value greater than 0).
* 'bef' is the substring before the match. We cut it from position beginning (string indexing starts at 1) up to the start of the match (to RSTART).
* 'aft' is the substring after the match. We cut it from the end of the match up to the end. Note how we use "RSTART+RLENGTH" to calculate the first char AFTER the end of the match. This it's a bit confusing with indexing starting from 1. If you're used to 0 indexing you would want to add a 1 ("RSTART+RLENGTH+1") but it's not needed here because indexing starts at 1.
* Note the comments starting with `#`  

#### Put all lines on one line
* Check out this wacky oneliner `awk '{a=a $0}END{print a}' file.txt`
* It will concatenate all lines on a single line (without any separators between lines). 
* Here's how it works. We start by declaring a variable a. Simply using it will make it uninitialized the first time awk encounters the variable name. Uninitialized variables have a value of "". You can use whatever var name you like, I used "a" because it's short and simple.
* On every record (line) add to "a" previous value of "a" + the current record (line). Awk concatenates values simply by putting them one after another. The line above would even work even without any spaces - eg `{a=a$0}` but I kept the space for clarity.
* After all input records are done processing print the final value of "a". "a" now contains all lines concatenated together.
* Feel free to add some separators between lines for clarity, like: `awk '{a=a"|"$0}END{print a}' file.txt`



#### User declared funcs
```

#!/usr/bin/awk -f

# Declare custom func outside
function throw_ring(who){
	if (who=="gollum"){
		return 0
	}
	else if (who=="frodo"){
		return 1
	}
}

# On all records matching /ring/
/ring/{
# Find gollum or frodo
if (match($0,"gollum")){
	was_thrown=throw_ring("gollum")
	#use ternary if/else. If was_thrown is true (or bigger than 0) return "THROWN". Else return "NOT THROWN"
	print "the  ring throw status is ", was_thrown?"THROWN":"NOT THROWN"
}

if (match($0,"frodo")){
	was_thrown=throw_ring("frodo")
	print "the  ring throw status is ", was_thrown?"THROWN":"NOT THROWN"
}

}

```
* This is a rather lange script but simple when you break it into pieces.
* Start by declaring a custom "throw_ring" function.
* on records that match `/ring/` exec the following operations:
* check if that record (which contains `/ring/`) contains "gollum". If yes get "thrown_status" value. 
* Use the ternary operator to return either "THROWN" or "NOT_THROWN". 
* Do the same for "frodo"
* The output will look like: `the  ring throw status is  THROWN`

#### More about conditional patterns
* Conditional patterns are powerful and succint. We can use them to check for certain conditions before executing any commands inside curly braces.
* `NF>4{print}` is equivalent to `{if(NF>4)print}`. As you can see the first one is clearer and shorter. It uses a conditional pattern. If true, exec commands inside curly braces.
* The second runs for every record and inside curly braces use a conditional 'if'. 
* We can use fairly advanced conditional patterns, using parenthesis and &&, || and even functions. 
* `awk 'match($0,/bilbo/){print}` - if we find `/bilbo/` inside current record print. Obviously we could've dispensed with the match and simply used a regular pattern. 
* But the power comes from combination with other tests. `awk '(NF>6)&& match($0,/bilbo/) {print}' file.txt`. If Number of Fields is bigger than 6 AND we have `/bilbo/` inside current record, print.
* We can replace match with the match operator - `~`. Like this: `NF>6 && $0~/bilbo {print}`. If Number of Fields bigger than 6 and we have a match of `/bilbo/` inside current record, print.


#### Advanced conditional patterns
* Here's a mind blowing one: `awk 'NF==3,/bilbo/{print}' f1`. The comma signifies a range (as for pattern range). But we use a conditional pattern as the start of the range and a regex as the end of the range. Pretty powerful stuff. 
* The line above will print before the first record where Number of Fields is 3 and up to (and including) the last record containing `/bilbo/`
* Here's a simpler but highly useful oneliner `awk 'NF!=0{print} file.txt`. If Number of Fields different than 0, print. It will skip printing empty lines.
* This one will make you oogle. `awk 'rand()>0.5{print}' file.txt`. It will print about half lines from 'file.txt', depending on the result of 'rand()'.


#### Print the first Nth lines of every file
* `awk 'FNR<=5{print}' f1 f2 f3`
* The line above will print the first 5 lines of every file passed to awk (without any separation between them). It uses a conditional pattern. If File Number Record smaller or equal to 5, print. 


#### Print until you encounter this pattern. Then move to next file.
* `awk '{if ($0~/bilbo/) nextfile;print}' f1 f2`.
* Print records (lines) from current file until you encounter `/bilbo/` in current record (line). Then move to the next file (skipping the rest of the current file).
* Do the same for the rest of the files.
* It will NOT print the line containing `/bilbo/`. 


#### Print every Nth line of file
* `awk '{getline;print}' file.txt` - print every second line of the file. 
* Why? For every record start with `getline`. This command sets '$0' from the next input record - it basically reads the next line into '$0'. Print then prints the newly updated '$0'
* `awk '{getline;getline;print}' file.txt` - print every third line of the file. We consume two lines then print the third one. And so on. Add more `getline` to increase `Nth` printing.

# Vim

### HOW TO READ SHORTCUTS:



`<Esc>` - Escape. Usually you should press the key, not type it. When you see `ii<Esc>` you type ii and then Escape, not 'ii<Esc>` literally.




`<C-g>` - means Ctrl+G




`g<C-g>` - means press g then press Ctrl+g




`<S-g>` - means press SHift+g




`<C-Z>` - means Ctrl+Shift+Z (Shift is not shown but implied since we have an uppercase Z)




`<zZ>` - z followed by Shift+Z





`<C-m><CR>` - type Ctrl+M followed by Enter. `<CR>` is Carriage Return (another word for Enter key)







### Find files and send them to vim.
```find ~ -size +10M | vim -```



* find files and open the results in vim. While in vim you can press `gf` to open a file (from the results provided by find) in vim. 
- HINT: if your file has spaces vim will complain that the file is not in the path. This happens because it truncates the file when a space appears, thus trying to reach the file at an erroneous path. So instead use `<S-v>` to select the hole line and then press `gf` 
- HINT2 - you can use any other command that outputs to stdout and send it to vim. 
- HINT3 - you can open the file under cursor in a new window with `<C-w>f`



### Encrypt
* You decide to write your secret journal using vim. Trouble is - anyone can read your unencrypted text files. Or can they? By using `vim -x mysecretfile.txt` you use encryption on file. You'll be asked for a key (password) to encrypt/decrypt your file. Warning - if you forget your key bye bye file. 
- HINT - you can also encrypt a file after you open it by typing `:X`




### Macros
* I'm still amazed by how many vim users totally ignore the power of register playback. That is - you record your actions into a register and play those actions back. It's a bit weird to wrap your head around it but once you do - you'll be amazed. 
Here's how it works
- press `qa` to start recording your actions into register `a`
- Perform some actions that you want repeated. Here's a very useful example for coders - custom line by line editing. Let's say that you need to go line by line, search for the word "foo" and place it in tags (<foo>) and place those tags again on the same line at the end of the line.
- This is a pretty complex scenario. Doing it by hand for thousands of lines it's PITA (Pain In The A*s). What can you do? Write an awk script? A Python script? NOOOOO. You use vim register playback.
- So you start your action by implementing a movement that will go to the next line or the next pattern. This is very important because it will make the playback "smart", as it will go on the next line or pattern and do the work.
- In your case you would start by typing  `/foo` and pressing `<CR>` (Enter). This takes care of always finding the next pattern and working on it.
- No do your desired operations. `i<<Esc>ea>` which will insert the bracket, then to normal mode, go te end of the word, append the closing bracket. 
- next you copy the whole bracketed foo by pressing `ya>` (yank all in between brackets) followed by `$p` (go to end of the line and paste)
- Your work is done. You press `q` to stop recording. 
- time for PAYBACK ...ah, I meant PLAYBACK. 
- you press `@a` to playback the contents of register `a` (into which you recorded your actions). You watch amazed how vim repeats "smartly" the operations you just performed previously. 
- Now you can press `@@` to repeat the previous playback as many times as you want. Or you could press `10@@` to repeat the playback for 10 times. Or if you want this applied to your whole file type `:% norm @a` - which means - apply the macro to all lines.






### Open files in own tab/win
* Your girlfriend's father comes to visit you. You want to impress him by opening a bunch of text files, each in it's own window in vim (and show him that you're very smart and tech savvy)
- You write `vim -o f1 f2 f3 f4` to open the specified files with windows split horizontally. use `vim -O f1 f2 f3` to split them vertically. If you have lots of files and you dont want a hundred narrow windows use `vim -o5 *.txt` to open a max of 5 windows (horizontally stacked). 
- You could instead open them in tabs by using the same syntax `vim -p f1 f2` or `vim -p5 *.txt`



### Info
* The `<C-g>` magic - it displays  file name and position. `g<C-g>` will display number of words, num of lines and other useful stats.



### Enter special chars
* Enter digraphs (speciar chars). Use `<C-k>` in Insert mode and type the digraph code. For example you can enter the pound sign using 'Pd' (£). Type `:h  digraph-table` for more info. To just view the codes type `:dig`



### Quickly write buffer to disk
* `ZZ`  - write the buffer to disk if modified and exit. I bet you didn't knew that - but if you did BRAVO, you know how to quit Vim! 



### Sort
* `:%!sort -u` - sort all lines and remove duplicates. `%` refers to the range and it means all the lines. You could use `1,5` for example and it would only sort lines 1 to 5. The `!` in this context means - execute an external shell command (in our case sort) with the range as stdin, when you're done put back in place the stdout from that command.




### Paste register
* While in insert mode press `<C-r>` followed by a registry, such as `a` or `b` or `@` or `:` or `+`. eg: `<C-r>a`. This will insert the contents of the registry at cursor.



### Insert mode navig
* While in insert mode press `<C-h>` to backspace a char, `<C-w>` to delete a word backwards, `<C-u>` to delete until the beginning of the line (all without leaving Insert mode)



### Autocomplete
* Did you knew you also have autocomplete by default? While in insert mode press `<C-n>` or `<C-p>` to cycle through possible autocompletions. 




### How to quit Vim
* There are lots of famous memes about people not knowing how to quit Vim. Here's a little secret - you can quit the current buffer(if you opened just a single file) without writting to disk by pressing `ZQ`.





### Repeat Insert
* Everyone knows that you can insert a character repeatedly using a command such as `20i-<Esc>` - which will insert 20 '-'. Or you could do something like `10aHello World Of Vim.<Esc>`. This will do 10 appends of the phrase 'Hello World Of Vim'



### On the fly computations
* You're deeply immersed into writting some cool code for your amazing program. Suddenly you have a hankering for some on the fly computations. You want to ... no, you **need** to calculate 42 multiplied by 42. Well damn. You have to stop your editing, pull up the calculator, do the calculations, copy them, go back to editing, and paste them. PHEW!!!
- Too much work.
- Here's a simpler solution. While in Insert mode press `<C-r>=`. You will see an equal sign appearing in the bottom of the window. Enter your math formula - eg: `42*42` and press enter. The result will be magically inserted before cursor while still keeping you in insert mode. (the result is 1764 if you were curious).
- Note of warning, certain operations such as `2^10` or `2**10` which you would expect to raise 2 to power of 10 don't work in that syntax. You'll have to use function instead, the `pow(2,10)` will raise 2 to power of 10.
- You can see more info by checking the help `:h expression`



### History of past searches
* You search for various keywords in your code. After a while you want to search for some of those keywords again but you don't remember them exactly. No worries. Just press `q/` and you'll open a small window at the bottom containing your searches. Navigate with `jk` and press <CR> (Enter) to repeat the selected search. Or just close that window with `:q`


### Useful selection of g commands
* `gf` open file under cursor
* `g;`traverse forward through the change list (for example after you edit in Insert mode)
* `g,`traverse backward through the change list
* `gi` go back to last insert position and enter insert mode, from wherever you are
* `gf` go to file under cursor (or select the whole line with `<C-v>` if the path has spaces
* `ga` - show char codes for char under cursor
* `g&` repeat last `:s` on all lines (last replace command)
* `gv` repeat last visual selection.


### Paste word under cursor
* You're in command  mode, trying to write a complex search & replace command. You want to search for the word "Supercalifragilisticexpialidocious" and replace it with "Super". Your fingers go on strike before you even start. What to do?. Well, assuming your cursor is on the said word you can paste that word in  mode by typing `<C-r><C-w>`. This will paste the word under the cursor. 
- this is also useful when you have words with tricky spelling.


### Create and use abbreviation.
* You're documenting your code. It's a mess. You need to use separators to make things clearer. You decide to use a long string, like `#--------------------------------------------------`. Typing it every time it's cumbersome and you will probably not get the amount of dashes right every time. 
What to do?
You could yank it in a register and paste it. But I think there's another, more flexible way.
You use abbreviations. Type:




`iab sep #-----------------------------------------------<CR>`




now in Insert mode type `sep` and press `Tab`. Your long separator is inserted. By using `:iab` you only create abbreviations for Insert mode and keep the other modes clean.

- HINT - to clear abbreviations type `:abc`



### List words under cursor.
* You're hard at work debugging your code. You left clues in form of comments containing the word "debug" - eg "#debug this or you'll get fired". You're somewher in the middle of the file but how many more lines containing the word "debug" are there?
- with the cursor over the word "debug" (or any other word) from Normal mode type `]I`. A list will appear at the bottom containing all lines containing the word under cursor, starting from the current line position. If you want the search to start from the beginning of the file (instead of curent line) type `[I`
- you can use this cool function for quickly displaying lines containing error codes, certain strings, etc. 


### Pattern searching tricks
* `/debug/1` - goes to the line below the line containing the pattern "debug". use another number for a negative offset or a larger offset. eg: `/debug/-4` -  4 lines above the line containing the debug pattern.
* `/debug/e+1` - go to +1 char after the end of pattern "debug". eg: "debug3" will put the cursor on "3"
* `/debug/b+3` - go to +3 chars after the beginning of pattern debug. In this case the cursor will be on "u" (not 'b' because counting starts at 0, where 0 char is the first char, in this case 'd')
* If later you decide you need another offset use `//e+10` - where 'e+10' is your new offset but using the old seach pattern.


### Use expression register to store and iterate
* You have a piece of code with the following word repeated 10 times: `var a1=42`. You want to have `var a1=42`, `var a2=42`, etc until `var a10=42`. How could you do that? By hand? NOOO.
* First set a variable to 0. `:let i=0`
* Now start to record a macro `qa` (record in register "a")
* Find your desired value using `/\va1/e`. The `/e` tells vim to place the cursor at the end of the patter, in this case on the digit `1`. Note that we're using `/\v` to signal vim to use 'very magic' (which means we don't have to escape certain special chars)
* Now for the magic. Start by incrementing the variable i. `:let i += 1` (spaces are important)
* press `s` to enter insert mode and delete the digit `1` at the same time.
* And now insert the value of `i` using the expression register. `<C-r>=i` (which will just echo the value of i and place it at cursor)
* Stop recording with `q`
* Apply the macro to all lines using `% norm @a` (on all lines eecute macro stored in 'a' register)
* DONE.


### Save and load your sessions.
* You are immersed into a highly complex editing session. You have log files, scripts, documentation and other files, all open in windows and tabs. Suddenly there's a computer crash. You open vim again but now you have to re-open all those files by hand (and place them in their various locations and windows). That sucks. 
* Instead you can simply save your session. Type `:mks mysession` (where myssesion is your session file) to save your session to a file. Next time you boot up vim use `vim -S mysession` to load your session.
* Or do it directly from vim. `:so mysession`



### Go back in history.
* You edit a bunch of files. After a couple of months you need to change  them again. Trouble is, you don't remember exactly what files you edited. 
* Type `:ol` and vim will show you your history (of file edits).
* To open one of these files type `:bro :ol` and choose a number coresponding to the desired file.



###  Go back to the line you last edited
* You start editing a file. Suddenly the phone calls. Your cat has won the lottery. After you collect the prize and buy the cat some toys you come back. You want to resume your work but you don't remember the exact line you were in. The file name it's also vague. What do you do?
* You type "`0" (backtick followed by zero). Vim will open the last edited file on the exact line you left it.



### Terminal inside vim
* You have 2 scripts and a config file open in separate windows. It's all good. But you need to run some quick commands in the terminal. Oh no. You don't want to leave the warm bossom of Vim. What to do?
* You could press `<C-z>` and put Vim in background (which will open a terminal). Then you could type `fg` in terminal to get back to vim.
* Or you could type `:sh` to open a shell temporarily and hide vim. When you're done you could press `exit` and get back to vim.
* But all these options aren't exactly what you want. You want the terminal, true, but you also need to view and edit your files **at the same time**.
* You type `:ter` and a terminal opens alongside your open windows. Yay! When you're done just type `exit` in the terminal.


###  Persistent undo
* Wouldn't it be cool to be able to undo your changes **after** you reopen your file? In vim this is quite easy to do. 
* Start by creating a dir where you want your undo info to go. Here's a good choice `~/.vim/undo-dir`
* Add the following two lines to your `.vimrc` (located usually at `~/.vimrc`:
```
set undodir=~/.vim/undo-dir
set undofile
```

### Load the previous  command
* You type a complex  command. Later you want that command again. One option would be to use the arrow keys and cycle through history. That works but takes your hands from the home row position. 
* Another way to load last  command is `:<C-r>:`. That is: start by tyying colon, press Ctrl+R and type colon again. Colon is a register storing your last  command. Ctrl+R outputs the contetns of a register.


### Append your registers.
* You open two important  documents. From one you start yanking important lines in a register. You go to the second document and paste but realize you missed a couple of lines. Damn. Now you have to yank all the lines and the ones you missed.
* Or do you? Instead you can just append to the register in which you yanked. Simply yank in the uppercase version of the register and you will append (eg: if you yanked in `a` now you append by yanking in `A`


### Range shortcut
* Press `5:` and vim will type for you in the  command line `:.,.+4` 


### Visual Block Syntax
* If you've used Vim for a while you know you can edit multiple lines at a time. Use Visual Block `<C-v>`, use a motion `3j`, insert some text `IHelloWorld` and press `<Esc>`. BAM! Your inserted text is typed on all lines. 
* But here's the real trick which few people know. Let's say that your vertical line spans some short lines. Eg:
```
I'm a long line, very vory long, YEAH.
Shory.
I'm a long line, very vory long, YEAH.
```
* You start the visual block let' say on the word 'YEAH' and it spans the whole 3 lines. If you use `I` and insert text the short line will remain empty (where it was empty space it'll still be empty space). But if you use `A` (append) then you'll also type text in the empty line. Pretty neat, eh? 
* In order to remember think that `A` Append always writes text. 


### Quickly run an external command
* It's cool to be able to run external terminal commands with `:%!sort` which passes all lines to sort and writes back the output. But there's a neat trick that will save you some time:
* `!5G` - will start writting an  command that looks like `:.,+4!`. You then will type your desired program `sort` and press enter. You start with `!` and then specify a motion from the current line `5G`. You can use other ranges, like `!4j` which will sort the following 4 lines from your current line.
* An even faster shortcut is `!!`. This will pass the current line to the external program (and replace it with the output). A known use case of this is to quickly insert a timestamp into your document `!!date`


### Replace with confirmation
* You have document. In it you have a variable called "money". You want to rename it to "cash" but only for certain cases. In that case you should type: `:%s/\v<money>/cash/gc`.  This long winded command uses very magic `\v` so that you won't have to escape `<>`. Those chars mark the beginning and end of a word. We use them because you presumably want to replace only the instance of "money" and not "zamoney" or "moneys".  Finally the `c` flag will prompt a confirmaton dialogue each time an entry is found.
* HINT - use `<C-e>` and `<C-y>` to scroll up and down so you can better see the context during the replace confirm dialogue.


### Documentation at your fingertips
* You're editing a bash file. You read the output from `find` and for each result you want to run an external script. You decide to use `xargs` instead of `-exec {} \;`. But as you write your command you find that you're rusty on xargs arguments. What to do?
* You could put vim in foreground `<C-z>`, `man xargs`, `fg`. Or you could open a terminal inside vim with `:ter`, check the docs then close the window with `<C-w><C-c>`. 
* But there are 2 better ways to do this.
* Just press `K` (Shift+k) on the keyword you're interested in and Vim will open the relevant man page. If it's another type of file (not bash) vim will also try to find the help for that particular keyword. For example you could put your cursor on `pow()` in a python file, type `K` and vim will show you the docs. Pretty cool, eh? One keystroke and the docs are open! AWESOME!
* But there's more. There is a filetype plugin which gives you an enhanced version of man. First install it with `:runtime! ftplugin/man.vim` (put it in your .vimrc if you want it open at startup).
* Now type `:Man xargs`. Vim will open the manual page inside vim in a separate window. That's cool and it save you the hassle of opening a terminal by hand and typing man inside it. 
* That's cool but it's just half of the gooodness. The other half is this. Inside this man window put your curosr on a keyword. Now press either `\K` (backward slash followed by Shift+k) or `<C-]>`. If there exists a man for that keyword it will open inside the same page. Like a browser. THis functionality is NOT present in the default man pages. You can navigate back and forth with `<C-o>`, `<C-i>`
* And for a last tip - you can use `\K` inside vim too on a keyword. Then vim will open the new and improved man page instead of the default documentation (whith opens with `K`)
* BUT NOTE! the `:Man` command works only for man pages for linux, not other programming languages. That is if you press `\K` on 'pow' inside a python file you will get the man page for 'POW(3)' (linux programmer manual) and not python.


### Input/output with external commands.
1. `:!date` - execute date command and print the results 
2. `:r !date` - execute date command and append the output after range last line. If no range provided use current line. Thus the results from date will be appended after the current line in our case.
3. `:w !date` - send all the lines as stdin to date command. Print the results (no insertion into current buffer)
4. `:1,3!date` send lines 1 to 3 as stdin to date command. Replace the lines in range (1,3) with output from date.



### Put each word on a new line
* Put all words from a line on a new line. eg: "hello world of vim" will be: "hello\n world\n of\n vim\n". How? With `:.!xargs -n1`. It says: send current line as stdin to xargs. You, xargs, split the line into args separated by blank space and `echo` at most one word. Then put the xargs output in place of the line. Since the output from xargs contains newlines this creates extra lines.
* You could also use a range, to put each word on the lines in range on a new line with `:1,5!xargs -n1`
* NOTE - xargs separates words by spaces. Vim has a default different way of separating words which may or may not corespond with xargs. (see `:h isk`


### Buffer delete
* As you know buffers are usually contents of files loaded in memory, with extra info attached to them (marks, registers, etc). 
* Let's say you decide to delete some buffers with `:bdel myverylongfile`. 5 minutes later you need again the buffer you deleted. Darn. What to do? You could try and remember the filename and reopen it. But what if there were lots of buffers you deleted and they havo complicated names?
* Vim it's here to help you. Just type `:ls!` to show all buffers, even [u]nlisted ones. You'll see your deleted buffer listed (they'll have a 'u' indicator in front of them for unlisted). Turns out deleting a buffer makes it unlisted. 
* If you really, really want to delete a buffer use `:bwipe`. 


### Autocomplete with files/lines
* You're editing a file. You have separator comments for sections, like `#DEBUG---------------------`. At some time you would like to insert that line into your current text. You could use a register but what if you have lots of types of separator comments (DEBUG, WIP, STABLE,etc)? Here's a better way:
* Type `<C-x><C-l>` (Ctrl+x, Crl+l). The autocomplete will show you whole lines and you can choose your separator comment line. (in Insert mode)
* <C-x><C-f>  will show you autocomplete options from the files in current directory. Quite cool.  (in Insert mode)
* After the autocomplete list appears you can cycle through it with `<C-n>` or `<C-p>`. 
* But here's an even crazier thingie - you can start typing an existing path and Vim will try and autocomplete it. Eg: you have typed `/` and are still in Insert mode. Type `<C-x>,<C-f>` and a list of dirs under root dir will appear. Cycle through them with `<C-n>` or `<C-p>`. If you want to go deeper you can't press Space because that will just enter that dir/file and a space afterwards. INstead press `<C-x>,<C-f>` and you will have inserted that dir/file name and will go one level deeper (to the next forward  slash) where you can cycle through the next options.
* You can use the above feature in combination with `gf` or `<C-w>f`  to open the files under cursor in vim. Very flexible.


### Repeat your insert
* You start editing a file. You type rather long text containing a copyright comment. You enter Normal mode, you jump to the end of the file and see that you will have to enter the same line again (for some reason). 
* Enter insert mode and press `<C-a>` - it'll insert the text you previously typed in Insert mode. 

### Better wrap
* After you set up the wrap option `:se wrap` you notice that words may be cut of in the middle at end of screen. Like so:
```
hello wo
rld of v
im
```
* Use `:se linebreak` and have the words not cut of in the middle:
```
hello 
world of 
vim
```

### Indent in Insert mode
* You're probably using the indent operators in Normal mode (to shift your lines to the rigt/left an amount of space). `>>` and `<<` will shift left/right a line. `>` works with other motion and text objects: `>ap`, `>3j`, etc. (`gg>G` will shift right all lines in a file)
* But if you're in Insert mode it can be a pain to switch to Normal and back again.
* It turns out you don't have to. You can use `<C-d>` and `<C-t>` to shift left/right right from Insert mode.


### Smart folding
* Folding is great. Trouble is - it sucks doing it by hand. Wouldn't it be great of Vim automatically created folds based on the indent level? That would work nicely for most programming languages.
* `:se foldmethod=indent` (instead of manual) and vim will automaticall fold based on 'shiftwidth' indents (if you change this setting in the middle of editing folding won't behave as expected)
* Now use the usual shortcuts to manipulate folds (`<C-z>,<C-o>,<C-m>,<C-r>,etc`)


### Increment numbers
* You're editing a file where you have years as dates. eg: "in 1825 the world...after 1841 something...". You receive a call from your editor/boss and he tells you that the dates are wrong. All years should be modified by adding 153 years to them. 
* Damn. You're in no mood for mental math juggle. And you have lots of years. What to do?
* Put your cursor over a year. Type `153<C-a>` (153 followed by Ctrl+a). Magically the year will be incremented by 153. Pretty cool. But you don't want to do this for all the years - you have hundreds of such dates. 
* Use a macro. `ggqa`. Go to top of file, start recording. `/\<\d\d\d\d\>` and press Enter (to find years). `153<C-a>` (153 followed by Ctrl+a). `q` to stop recording. `:%norm @a` to apply this macro to all lines.


### Copy a protected file and edit it immediately.
* You need to take a look and modify the dmesg file. Trouble is you can't if you started vim as a regular user - dmesg is owned by root. You could exit vim, make a copy, come back and re-open. 
* Here's a one liner that will save you the hassle: `:exec '!cp /var/log/dmesg .' | e dmesg`. First it executes the copy command. The pipe operator (vertical bar) chains commands in vim  mode. Immediately after a edit command is issued.


### Comment a whole file (or a portion)
* You open a long python script. You run it but you get errors. Darn. You decide to debug it by commenting it completely and uncomment sections as you see fit. 
* Commeting by hand it's troublesome - there are 1520 lines. You could try some fancy stuff with a Visual Block and insert but that might not be the best way.
* Instead you do `:%norm i#`. Kaboom - your whole script is commented out. Not smartly mind you (existing comments will be double commented) but it works nicely for your purposes. You can do the same for a .c file (or another file that supports these types of comments) with `:%norm i//`. You tell vim to execute a normal command across all lines (go to first char in line, even if it's empty and insert a comment)
* If you want to comment a section just use  range `:1,50norm i#`. If you have a visual selection and you type the colon you'll be presented with `:'<,'>` which means a range between the start and the end of the visual selection. Then you just type `norm i#` after the already present command.
* Using the same logic you could append semicolons to all lines in a file (or a range). Check it out: `%norm A;` - on all lines execute normal command A (which appends after the last char) and type a semicolon.

### Keep temp  commands in registers
* You have a particularly useful  command which you use repeatedly. It is: `:-1,+1norm i#` - it comments out the current line, the line above and the line below. As you work you want to execute this command, now an then. But you don't like typing it out every time.
* So instead you type the command in the text, just without the semicolon, on a new line: 
```
#bla bla
-1,+1norm i#
```
* Put your cursor on the line where the command is and delete it in a register, say 'w'. `"wdd` (in register w store this line and delete it from text)
* Now your register has your  command. But how to run it? 
* Easy. Type `:@w` - where 'w' is your register. Press enter and you'll execute this command. 
* Here's the explanation. When typing  commands a `@` sign in front of a letter signifies a register. the `@w` signifies the `w` register. When you type this special variable in  command window it just reads the contents of this register and executes it (after you press Enter).  
* Fun fact. You can repeat your last  command with `:@:`. The register `:` stores the last command. `@:` is used to signify this register while entering a command (and pressing Enter afterwards so that the command executes)




### Open extra files with terminal commands like find
* When you open vim with some files as arguments they are stored in `:args`. args are different from buffers. Buffers are all the buffers opened in memory and not necesarilly files. Args are the actual file args passed at startup. If you change the args the buffers stay untouched and viceversa. 
* Think about `:args` as a temporary list of files which you can modify to your desire. You navigate files in args with `:next` and `:prev` (whereas you navigate buffers with `:bn` `:bp` `:bf` `:bl`). 
* After you load vim and start editing some file you realize you have to also view some log files. What to do? 
* One possible way is to create a new buffer with `:new`. Afterwards put into this buffer all the results from find with `:r !find /var/log -size +1M`. All the results will be placed in the new buffer, each file path on a new line. Just press `gf` on a line and vim will open the file in a new window (and buffer). That's one approach.
* Another is to just run 
```
:args `find /var/log -size +1M -name '*.py' \|\| true`
```
(OR)
```
:args `find /var/log -size +1M -name '*.py' \| xargs -n1`
```
* Now if you type `:args` you'll see it is populated by all the results from find. Now you can naviate quickly throug these files. 
* Each method has drawbacks. Using this last approach you load all the results at once but the command is more tricky to type and your buffers may be overloaded. Using the first approach on the other hand will require you to go back to that new buffer and `gf` on each file you want opened. Choose what suits you best.
* For more see `:h backtick-expansion`



### Format with care
* You probably now about formatting with `gq`. You do things like `gggqG` to forma the whole file.
* But `gq` will also move the cursor to the motion end range (eg; end of file). In order to format but keep the cursor where it is you use `gw` - just in the same way. Like `gggwG`.


###


### patterns
* You're editing some source code left to you by your predecesor. It's messy. That fellow used to abbreviate troublesome variables with 'dbg_', like 'dbg_count', 'dbg_class', etc. Just how many of these variables are there? 
* You can search for them with the substitute command. Shocking, I know. Check it out: `:%s/<dbg_//gni`.
* This means: on all lines search for `<dbg_` (word start followed by 'dbg_'), replace with nothing (you could put a '&' but it's not needed), and use the following flags: g for global, i for case insensitive and n for no substitution. The 'n' flag is the trick here. It just prints how many matches there are without actually performing substitution.


### Quickly edit and reload your .vimrc
* You decide to make Vim behave exactly how you want it. You'll need to edit your .vimrc but that's alright. 
* After you open vim you can open your .vimrc with `:e $MYVIMRC`. Easy!  
* You've done your modification but now you want to see the effects. Just type `:so %` (source all lines) with .vimrc open and vim will source the new .vimrc.



