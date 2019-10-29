[toc]

# Regular Expressions
正则表达式
## Introductions
Regular expressions are a powerful tool for various kinds of string manipulation.
They are a domain specific language (DSL) that is present as a library in most modern programming languages, not just Python.
They are useful for two main tasks:
- verifying that strings match a pattern (for instance, that a string has the format of an email address), 
- performing substitutions in a string (such as changing all American spellings to British ones).

Regular expressions in Python can be accessed using the **re** module, which is part of the standard library. 
After you've defined a regular expression, the re.match function can be used to determine whether **it matches at the beginning of a string**.
If it does, match returns an object representing the match, if not, it returns None.
To avoid any confusion while working with regular expressions, we would use raw strings as **r"expression"**.
Raw strings don't escape anything, which makes use of regular expressions easier.
Example:
```python
import re

pattern = r"spam"

if re.match(pattern, "spamspamspam"):
   print("Match")
else:
   print("No match")
```
Result:
```shell
Match
```
The function **re.search** finds a match of a pattern anywhere in the string.
The function **re.findall** returns a list of all substrings that match a pattern.
Example:
```python
import re

pattern = r"spam"

if re.match(pattern, "eggspamsausagespam"):
   print("Match")
else:
   print("No match")

if re.search(pattern, "eggspamsausagespam"):
   print("Match")
else:
   print("No match")
    
print(re.findall(pattern, "eggspamsausagespam"))
```
Result:
```shell
No match
Match
['spam', 'spam']
```

The regex search returns an object with several methods that give details about it. 
These methods include group which returns the string matched, start and end which return the start and ending positions of the first match, and span which returns the start and end positions of the first match as a tuple.
Example:
```python
import re

pattern = r"pam"

match = re.search(pattern, "eggspamsausage")
if match:
   print(match.group())
   print(match.start())
   print(match.end())
   print(match.span())
```
Result:
```shell
pam
4
7
(4, 7)
```

**sub** method replaces all occurrences of the pattern in string with repl, substituting all occurrences, unless count provided. This method returns the modified string.
Syntax:
```python
re.sub(pattern, repl, string, count=0)
```
Example:
```python
import re

str = "My name is David. Hi David."
pattern = r"David"
newstr = re.sub(pattern, "Amy", str)
print(newstr)
```
Result:
```shell
My name is Amy. Hi Amy.
```

## Metacharacters
元字符
**. (dot)** matches **any character**, other than a new line.
```python
import re

pattern = r"gr.y"

if re.match(pattern, "grey"):
   print("Match 1")

if re.match(pattern, "gray"):
   print("Match 2")

if re.match(pattern, "blue"):
   print("Match 3")
```
**^ and $** match the start and end of a string, respectively.
```python
import re

pattern = r"^gr.y$"

if re.match(pattern, "grey"):
   print("Match 1")

if re.match(pattern, "gray"):
   print("Match 2")

if re.match(pattern, "stingray"):
   print("Match 3")
```
The pattern "^gr.y$" means that the string should start with gr, then follow with any character, except a newline, and end with y.
result
```shell
Match 1
Match 2
```
## Character Classes
Character classes provide a way to match **only one** of a specific set of characters.
A character class is created by putting the characters it matches inside square brackets.
Example:
```python
import re

pattern = r"[aeiou]"

if re.search(pattern, "grey"):
   print("Match 1")

if re.search(pattern, "qwertyuiop"):
   print("Match 2")

if re.search(pattern, "rhythm myths"):
   print("Match 3")
```
The pattern [aeiou] in the search function matches all strings that contain any one of the characters defined.
Result:
```shell
Match 1
Match 2
```
Character classes can also match **ranges of characters**. 
Some examples:
The class **[a-z]** matches any lowercase alphabetic character.
The class **[G-P]** matches any uppercase character from G to P.
The class **[0-9]** matches any digit. 
Multiple ranges can be included in one class. For example, [A-Za-z] matches a letter of any case.
Example:
```python
import re

pattern = r"[A-Z][A-Z][0-9]"

if re.search(pattern, "LS8"):
   print("Match 1")

if re.search(pattern, "E3"):
   print("Match 2")

if re.search(pattern, "1ab"):
   print("Match 3")
```
Result:
```shell
Match 1
```
Place a **^** at the start of a character class to **invert** it. 
This causes it to match any character other than the ones included. 
Other metacharacters such as $ and ., have no meaning within character classes. 
The metacharacter ^ has no meaning unless it is the first character in a class.
```python
import re

pattern = r"[^A-Z]"

if re.search(pattern, "this is all quiet"):
   print("Match 1")

if re.search(pattern, "AbCdEfG123"):
   print("Match 2")

if re.search(pattern, "THISISALLSHOUTING"):
   print("Match 3")
```

The pattern [^A-Z] excludes **uppercase** strings.
Note, that the ^ should be inside the brackets to invert the character class.
Result:
```shell
Match 1
Match 2
```
## More Metacharacters
Some more metacharacters are **\*, +, ?, { and }** .
These specify numbers of repetitions. 
The metacharacter **\*** means "zero or more repetitions of the previous thing". It tries to match as many repetitions as possible. The "previous thing" can be a single character, a class, or a group of characters in parentheses.
Example:
```python
import re

pattern = r"egg(spam)*"

if re.match(pattern, "egg"):
   print("Match 1")

if re.match(pattern, "eggspamspamegg"):
   print("Match 2")

if re.match(pattern, "spam"):
   print("Match 3")
```
The example above matches strings that start with "egg" and follow with **zero or more** "spam"s.
Result:
```shell
Match 1
Match 2
```
The metacharacter **+** is very similar to **\***, except it means "**one or more** repetitions", as opposed to "zero or more repetitions".
```python
import re

pattern = r"g+"

if re.match(pattern, ""):
   print("Match 1")

if re.match(pattern, "gggggggggggggg"):
   print("Match 2")

if re.match(pattern, "abc"):
   print("Match 3")
```
To summarize:
\* matches 0 or more occurrences of the preceding expression.
\+ matches 1 or more occurrence of the preceding expression.
```shell
Match 2
```
The metacharacter ? means "zero or one repetitions".
Example:
```python
import re

pattern = r"ice(-)?cream"

if re.match(pattern, "ice-cream"):
   print("Match 1")

if re.match(pattern, "icecream"):
   print("Match 2")

if re.match(pattern, "sausages"):
   print("Match 3")

if re.match(pattern, "ice--ice"):
   print("Match 4")
```
Result:
```shell
Match 1
Match 2
```

**Curly braces** can be used to represent the number of repetitions between two numbers.The regex {x,y} means "between x and y repetitions of something". Hence {0,1} is the same thing as ?.If the first number is missing, it is taken to be zero. If the second number is missing, it is taken to be infinity.
Example:
```python
import re
pattern = r"9{1,3}$"
if re.match(pattern, "9"):
    print("Match 1")
if re.match(pattern, "999"):
    print("Match 2")
if re.match(pattern, "9999"):
    print("Match 3")
```
"9{1,3}$" matches string that have 1 to 3 nines.
Result:
```shell
Match 1
Match 2
```
## Groups
A group can be created by surrounding part of a regular expression with **parentheses**. This means that a group can be given as an argument to metacharacters such as * and ?.
```python
import re
pattern = r"egg(spam)*"
if re.match(pattern, "egg"):
    print("Match 1")
if re.match(pattern, "eggspamspamspamegg"):
    print("Match 2")
if re.match(pattern, "spam"):
    print("Match 3")
# Match 1
# Match 2
```
The content of groups in a match can be accessed using the group function.
A call of group(0) or group() returns the whole match.
A call of group(n), where n is greater than 0, returns the nth group from the left.
The method groups() returns all groups up from 1.
```python
import re

pattern = r"a(bc)(de)(f(g)h)i"

match = re.match(pattern, "abcdefghijklmnop")
if match:
   print(match.group())
   print(match.group(0))
   print(match.group(1))
   print(match.group(2))
   print(match.groups())
```
```shell
abcdefghi
abcdefghi
bc
de
('bc', 'de', 'fgh', 'g')
```
There are several kinds of special groups.
Two useful ones are **named groups** and **non-capturing groups**.
**Named groups** have the format **(?P<name>...)**, where name is the name of the group, and ... is the content. They behave exactly the same as normal groups, except they can be accessed bygroup(name) in addition to its number.
**Non-capturing groups** have the format **(?:...)**. They are not accessible by the group method, so they can be added to an existing regular expression without breaking the numbering.
Example:
```python
import re

pattern = r"(?P<first>abc)(?:def)(ghi)"

match = re.match(pattern, "abcdefghi")
if match:
   print(match.group("first"))
   print(match.groups())
```
Result:
```shell
abc
('abc', 'ghi')
```
Another important metacharacter is |.
This means "or", so red|blue matches either "red" or "blue".
```python
import re

pattern = r"gr(a|e)y"

match = re.match(pattern, "gray")
if match:
   print ("Match 1")

match = re.match(pattern, "grey")
if match:
   print ("Match 2")    

match = re.match(pattern, "griy")
if match:
    print ("Match 3")
# Match 1
# Match 2
```
## Special Sequences
There are various special sequences you can use in regular expressions. They are written as a backslash followed by another character. One useful special sequence is a backslash and a number between 1 and 99, e.g., \1 or \17. This matches the expression of the group of that number.

# Pythonicness & Packaging
## The Zen of Python
```python
import this
```
```shell
The Zen of Python, by Tim Peters

Beautiful is better than ugly.
Explicit is better than implicit.
Simple is better than complex.
Complex is better than complicated.
Flat is better than nested.
Sparse is better than dense.
Readability counts.
Special cases aren't special enough to break the rules.
Although practicality beats purity.
Errors should never pass silently.
Unless explicitly silenced.
In the face of ambiguity, refuse the temptation to guess.
There should be one-- and preferably only one --obvious way to do it.
Although that way may not be obvious at first unless you're Dutch.
Now is better than never.
Although never is often better than *right* now.
If the implementation is hard to explain, it's a bad idea.
If the implementation is easy to explain, it may be a good idea.
Namespaces are one honking great idea -- let's do more of those!
```

## PEP
Python Enhancement Proposals
PEP8

## Function Arguments
```python
def function(named_arg, *args):
    print(named_arg)
    print(args)
    
function(1, 2, 3, 4, 5)
```
Result:
```shell
1
(2, 3, 4, 5)
```

**default value**
```python
def function(x, y, food = "spam"):
    print(food)
    
function(1, 2)
function(3, 4, "egg")
```
Result:
```shell
spam
egg
```

**\*\*kwargs**
```python
def my_func(x, y=7, *args, **kwargs):
    print(x)
    print(y)
    print(args)
    print(kwargs)
    
my_func(2,3,4,5,6, a=7, b=8)
```
Result:
```shell
2
3
(4, 5, 6)
{'a': 7, 'b': 8}
```

## Tuple Unpacking
```python
numbers = (1, 2, 3)
a, b, c = numbers
print(a)
print(b)
print(c)
```

```python
a, b, *c, d = [1, 2, 3, 4, 5, 6, 7, 8, 9]
print(a)
print(b)
print(c)
print(d)
```
Result:
```shell
1
2
[3, 4, 5, 6, 7, 8]
9
```
## Ternary Operator
```python
a = 7
b = 1 if a >= 5 else 42
print(b)
```
Result:
```shell
1
```

## More on Else Statements