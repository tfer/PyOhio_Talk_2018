# There are a few basic syntax rules you should know before diving into it

* SRL is case insensitive. Thus, LITERALLY "test" is exactly the same as literally "test". But please beware, that everything inside a string is in fact case sensitive. LITERALLY "TEST" does NOT equal literally "test".
* The comma separating statements is completely optional and has no effect whatsoever. In fact, it gets removed before interpreting. But since it helps the human eye to distinct between different statements, it's allowed.
* Strings are interpreted as literal characters and will be escaped. They can either be defined using 'single' or "double" quotation marks. Escaping them using a backslash is possible as well.
* Parentheses should only be used when building a sub-query, for example while using a capture or non-capture group to, for example, apply a quantifier to multiple items.
* Comments are currently not supported and may be implemented in the future.

## Frame[^Called 'Character' in the official Doc]

A frame is anything that can match a tile in our model.  An SRL statement to describe one, or a sequence of frames takes the following form:

```srl-BNF
<character-set-name> [specification] [quantifier] [anchor]
```

As you can see, the \<character-set-name> almost always come first, (there can be another '[anchor]' before it). They start a new statement, and everything that follows defines or refines the  frame(s) it introduces. Some \<character-set-name>'s allow a specification.  For example 'LETTER', allows you to specify a span of allowed letters, e.g.: 'from a to f'.

Every frame or frame sequence can be quantified. You may want to match exactly four letters from a to f. This would match abcd, but not abcg. You can do that by supplying 'exactly 4 times' as a quantifier:

```srl
letter from a to f exactly 4 times
```

Note: this adds 4 frames to our regex, four copies of 'letter from a to f'


Okay, let's dive into the different \<character-set-name>'s. Below, you can find a list of all available \<character-set-name> along with an example query.

### LITERALLY
```srl
literally "string"
```

The 'literally' <character-set-name> allows you build a sequence of frames up with one statement.  It passes a string to the query that will be interpreted as exactly what you've requested. Nothing else will match besides your string. Any special character will automatically be escaped.

#### Example query:
```srl
literally "sample"
```

### ONE OF

example format:
```srl-BNF
one of "characters"
```

So 'literally', (above), comes in handy if the string is known. But if there is a unknown string which may only contain certain characters, using ONE OF makes much more sense. This will match one of the supplied characters.

#### Example query:
```srl
one of "a%1"
```

### LETTER & UPPERCASE LETTER

format:

```srl-BNF
letter [from a to z]
```

This will help you to match a letter between a specific span, if the exact word isn't known. If you know you're expecting an letter, then go for it. If not supplying anything, a normal letter between a and z will be matched. Of course, you can define a span, using the from x to y syntax.

Please note, that this will only match one letter. If you expect more than one letter, use a quantifier.

>Note: LETTER would be called an alphabetic character in computer science class.

#### Example query:

```srl
letter from a to f
uppercase letter
```

or use in this format:

```srl-BNF
uppercase letter [from A to Z]
```

This of course behaves just like the normal letter, with the only difference, that uppercase letter only matches letters that are written in uppercase. Of course, if the case insensitive flag is applied to the query, these two act completely the same.

#### Example query:

```srl
uppercase letter from A to F
```

### ANY CHARACTER

format:

```srl-BNF
any character
```

Just like a letter, any character matches anything between A to Z, plus 0 to 9 and _, -case insensitive. This way you can validate if someone for example entered a valid username.

>In many computer languages, including Python, these are the characters from which you can form valid identifers.

#### Example query:

```srl
starts with any character once or more, must end
```

>Note: this example shows an anchor in front.

### NO CHARACTER

```srl-BNF
no character
```

The inverse to the any character-character is no character. This will match everything except a to z, A to Z, 0 to 9 and _.

Example query:

```srl
starts with no character once or more, must end
```

### DIGIT or NUMBER

format:

```srl-BNF
digit [from 0 to 9]
```

When expecting a digit, but not a specific one, this comes in handy. Each digit matches only one digit, meaning you can only match digit from 0 to 9, but multiple times using a quantifier. Obviously, limiting the digit isn't a problem either. So if you're searching for a number from 5 to 7, go for it!

>Note: number is an alias for digit.

#### Example query:

```srl
starts with digit from 5 to 7 exactly 2 times, must end
```

### ANYTHING

format:

```srl-BNF
anything
```

Any character whatsoever. Well.. except for line breaks. This will match any character, except new lines. And, of course, only once. So don't forget to apply a quantifier, if necessary.

#### Example query:

```srl
anything
```

### NEW LINE

format;

```srl-BNF
new line
```

Match a new line. Forgive us, if we can't provide an example for that one, but you can check it out on the builder.


### WHITESPACE and NO WHITESPACE

```srl_BNF 
[no] whitespace
```

This matches any whitespace character. This includes a space, tab or new line. If using no whitespace everything except a whitespace character will match.

#### Example query:

```srl
whitespace
```

### TAB

```srl-BNF
tab
```

If you want to match tabs, but no other whitespace characters, this might be for you. It will only match the tab character, and nothing else.

#### Example query:

```snl_BNF
tab
```

### backslash

```srl
backslash
```

Matching a backslash with literally would work, but requires escaping, since the backslash is the escaping character. Thus, you'd have to use literally "\\" to match one backslash. Or you could just write backslash.

#### Example query:

```srl
backslash
```

### RAW

format:

```srl
raw "expression"
```

Sometimes, you may want to enforce a specific part of a regular expression. You can do this by using raw. This will append the given string without escaping it.

#### Example query:

literally "an", whitespace, raw "[a-zA-Z]"

__====================================================__

## Quantifiers

Quantifiers are probably one of the most important things here. If you've specified a character or a group in your query and now want to multiply it, you don't have to copy and paste all of it. Just tell them how many copies to allow.

Oh, and don't be confused. Sometimes, you may find that these quantifiers don't match with the tinkered example. That's okay, since we're not forcing the string to start or end. Thus, even if only parts of that string are matching, the expression will be valid.

Remember: You can execute every Python Cell in this notebook by clicking it, and then pressing shift-enter !

___

### EXACTLY

__format:__

```srl-BNF
exactly <x> times
```

You're sure. You don't guess, you dictate. exactly 4 times. Not more, not less. The statement before has to match exactly x times.

>Note: since exactly x times is pretty much to write, short terms exist.  Instead of `exactly 1 time`, you can write `once`, and for 2, write `twice`

__format:__

```srl-BNF
digit exactly 3 times, letter twice
between <x> and <y> times
```

For a specific number of repetitions between a span of \<x\> to \<y\>, you may use this quantifier. It will make sure the previous character exists between x and y times.

>Note: since between x and y times is pretty much to write, you can get rid of the times: `between 1 and 5`

__Example query:__

```srl
Example query:
starts with digit between 3 and 5 times, letter twice
optional
optional
```

### OPTIONAL

You can't always be sure that something exists. Sometimes it's okay if something is missing. In that case, the `optional` quantifier comes in handy. It will match the sub-query, if it's there, and ignore it, if it's missing.

__Example query:__

```srl
digit optional, letter twice
```

### ONCE/NEVER OR MORE

__format:__

```srl-BNF
once or more
never or more
```

If something has to exist at least once, or never, but if it does, then it may exist multiple times, the quantifiers once or more and never or more will do the job.

__Example query:__

```srl
starts with letter once or more, must end
```

### AT LEAST X TIMES

__format:__

```BNF
at least <x> times
```

Something may exist in an infinite length, but must exist at least x times.

__Example query:__

```srl
letter at least 10 times
```

__===========================================================__

## Groups

Groups are a powerful tool of regular expressions.  With them, you can capture matches, join and/or summarize them.

To make things easier for you, think of groups as sub-queries. Everything in between a group could be a standalone expression which will later be combined.

Each group allows you to form a sub-query either using parentheses, or just a literal string by using quotes instead.

### CAPTURE ... AS

__format:__

```BNF
capture (condition) [as "name"]
```

To go beyond simply validating input, a capture group comes in handy. You can capture any condition and return it by the engine. This helps you to filter inputs and only get the parts you care about.



If you're trying to get more than one match, capture names are useful, too. This is completely optional, but you can supply a name for a capture group using the `as "name"` syntax.

__Example query:__

```srl
capture (anything once or more) as "first", literally " - ", capture "second part" as "second"
```

### ANY OF

__format:__

```srl-BNF
any of (<condition>)
```

If you're not exactly sure which part of the condition will match, use any of. Every statement you supply in that sub-query, could be a match.

As you can see, you can feel free to nest multiple groups and even parentheses. 

In the example for `CAPTURE ...` repeated here:

```srl
capture (anything once or more) as "first", literally " - ", capture "second part" as "second"
```



>Note: `either of` is a synonym of `any of` .

__Query Example:__

```srl-BNF
capture (any of (literally "sample", (digit once or more)))
```

If you would have removed the parentheses around the digit once or more, the expression would be invalid, since you can't match either a digit, or "once or more".

___

### until

__format:__

```srl-BNF
until (<condition>)
```

Sometimes you want to match or capture a specific expression until some other condition is met. This can be achieved using the `until` group.

In the example below, we'll provide a string as a condition. However, this would work as well using a more complex expression, just like above.

__Example query:__

```srl
begin with capture (anything once or more) until "m"
```
