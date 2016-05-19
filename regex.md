
## Using Lookahead

| Symbol 	| Definition    			| Description																			|
|-----------|---------------------------|---------------------------------------------------------------------------------------|
| (?=foo)	| Positive Lookahead	    | Asserts that what immediately follows the current position in the string is foo 		|
| (?!foo)	| Negative Lookahead   		| Asserts that what immediately follows the current position in the string is not foo 	|

_This is a summarized version from the [Mastering Lookahead and Lookbehind](http://www.rexegg.com/regex-lookarounds.html) article on rexegg.com._


Lookaheads in regex is a bit more advanced and can get easily confusing in the beginning, they are however useful for several purposes, including simple string validation. So if you have not used regex at all yet, I advise that you play around with the basics first.

The important thing to understand about lookaheads is that at the end of a lookahead, the regex engine has not changed its position in the string. This makes it possible to chain several lookaheads, one after the other without changing position.

This will all become clear in the following example where we will perform a simple password validation with just one line of regex, instead of many lines of javascript.

We will not be using Negative Lookahead in the example, however it behaves the same way as positive lookaheads do, just with logic being that you 'lookahead' for a pattern not being in the string.

_Note: whenever the [rexex style guide](http://www.rexegg.com/regex-style.html) is mentioned it is referring to the one from rexegg.com._

######Password requirements:

1. The Password must have between 6 and 10 word characters. `\w`
2. It has to contain at least one lowercase character. `[a-z]`
3. It has to contain **three** uppercase characters. `[A-Z]`
4. It has to contain at least one digit. `\d`


######Starting with the first requirement

A string made up of 6 to 10 characters can be written like this `^\w{6,10}$`. Start at the beginning of the string `^`, match any word character 6 to 10 times `\w{6,10}` and make sure your at the end of the string `$`.

Within a lookahead the pattern becomes `(?=^\w{6,10}$)`, we will however move the `^` to the beginning of the pattern in order not to duplicate it for every lookahead. It is important to realise that our lookaheads here will always look from left to right in the string, and by adding the `^` before the lookaheads we make sure that assertions are started at the very beginning of the string.

`^(?=\w{6,10}$)`

This expression validates that a string is 6 to 10 characters, it does however not match anything yet, we have only looked ahead and come back to the beginning of the string.

######Second Requirement

We now have to check whether the password contains a lowercase letter. The easy way to check this would be to use `(?=.*[a-z])`, this is however inefficient due to backtracking.

Instead we will use an expression that makes use of the principle of contrast recommended by the regex style guide. The expression looks like this `[^a-z]*[a-z]`,
`[^a-z]` is the counterclass to `[a-z]`. So the expression above is saying: match 0 or more non lowercase letters and one lowercase letter. The pattern becomes:

`^(?=\w{6,10}$)(?=[^a-z]*[a-z])`

######Third Requirement

The third requirement is similar to the second, however with the added difficulty of repeating the uppercase check 3 times.

We will do this using the quantifier `{3}`.
The lookahead will look like this: `(?=(?:[^A-Z]*[A-Z]){3})`.
_Note: `(:?)` means non capturing group, it is similar to `()`, just that it does not return the capture in the results._

So this lookahead will do the following three times: match zero or more characters that are not uppercase letters `[^A-Z]*`, then match one uppercase letter `[A-Z]`. The pattern becomes:

`^(?=\w{6,10}$)(?=[^a-z]*[a-z])(?=(?:[^A-Z]*[A-Z]){3})`

######Last Requirement

The last lookahead again uses the principle of contrast to check for 0 or more non digits `\D*` and one digit `\d`. `(?=\D*\d)` The pattern becomes:

`^(?=\w{6,10}$)(?=[^a-z]*[a-z])(?=(?:[^A-Z]*[A-Z]){3})(?=\D*\d)`

Now we have made sure the password is valid, and if that is all we wanted we can stop here. However, if we also wanted to match and return the string, we can easily do so now.

######Matching the string

A simple `.*` would suffice to capture the entire string which, as we have asserted with out lookups, matches all of our criteria. The pattern becomes: 

`^(?=\w{6,10}$)(?=[^a-z]*[a-z])(?=(?:[^A-Z]*[A-Z]){3})(?=\D*\d).*`

However, to make the pattern even more efficient we can use one of the patterns from the lookaheads to match the entire string. Which one does not matter as it can work with any, but the obvious one here would be `\w{6,10}$`, as it matches the entire string anyway. The pattern becomes: 

`^(?=[^a-z]*[a-z])(?=(?:[^A-Z]*[A-Z]){3})(?=\D*\d)\w{6,10}$`

This shows that when checking for n conditions we only need n-1 lookaheads at the most, often you can combine several conditions into a single lookahead.

One last thing to note is that, while the order of the lookaheads will not change the result, it is more efficient to use those lookaheads first that are most likely to fail. This makes use of the design to fail principle from the regex style guide.

######Example In javascript

```javascript
var valid = "VaLiD123";
var invalid = "invalidPassword";
var regex = /^(?=[^a-z]*[a-z])(?=(?:[^A-Z]*[A-Z]){3})(?=\D*\d)\w{6,10}$/;

console.log(regex.test(valid));
// Outputs: true
console.log(regex.test(invalid));
// Outputs: false

```


######The process

With `VaLiD123` as example, let see how the lookaheads work.

`^` as we know will tell the lookahead to start at the beginning of the string.

so  `VaLiD123`
	^










