
## Using Lookahead

| Symbol 	| Definition    			| Description																			|
|-----------|---------------------------|---------------------------------------------------------------------------------------|
| (?=foo)	| Lookahead	  				| Asserts that what immediately follows the current position in the string is foo 		|
| (?!foo)	| Negative Lookahead   		| Asserts that what immediately follows the current position in the string is not foo 	|

Lookaheads in regex can get easily confusing in the beginning, they are however useful for several purposes, including simple string validation.

The important thing to understand about lookaheads is that at the end of a lookahead, the regex engine has not changed its position in the string. This makes it possible to chain several lookaheads, one after the other without changing position.

This will all become clear in the following example where we will perform a simple password validation with just one line of regex, instead of many lines of javascript.

### Password requirements:

1. The Password must have between 6 and 10 word characters. `\w`
2. It has to contain at least one lowercase character. `[a-z]`
3. It has to contain three uppercase characters. `[A-Z]`
4. It has to contain at least one digit. `\d`


### Starting with the first requirement

A string made up of 6 to 10 characters can be written like this `^\w{6,10}$`.

Within a lookahead the pattern becomes `(?=^\w{6,10}$)` we will however move the `^` to the beginning of the pattern in order not to duplicate it for every lookahead.
`^(?=\w{6,10}$)`

This expression validates that a string is 6 to 12 characters, however does not match anything yet, we have only looked ahead and come back to the beginning of the string.

### Second Requirement

We now have to check wether the password contains a lowercase letter. The easy way to check this would be to use `(?=.*[a-z])`, this is however inefficient due to backtracking (a bit too advanced for now).

Instead we will use an expression that makes use of the principle of contrast recommended by the regex style guide. The expression looks like this `[^a-z]*[a-z]`.






