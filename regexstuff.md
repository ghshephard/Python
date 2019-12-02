# Regex Studies:


### Back Reference to capturing group that matches nothing:

    (re?)txt\1


Back Reference to a capturing group that does not participate in match:

    (re)?txt\1


Placing the ? inside the () allows a match of nothing. 

    Searching on: test(,?)\1 line
    test line ==> test line

Placing the ? outside the () results in a failure to match. 

    Searching on: test(,)?\1 line
    test line ==> *No Match*

The key concept here is that a back reference to a capture group that *did not participate* in the match
- ? outside () - results in a failure to match, whereas a backreference to a capture group that matches nothing,  

### Positive Lookahead

    re_1(?=re_2)

 re_2 is *not* matched, rather it is used to see whether a match is possible by "looking ahead" in the line to see if something
 is present.

	Searching on: ^.*?t(?=.*gordon)
	first line of the night for jim ==> *No Match*
	best, line of the night for gordon ==> best
	last fine - always gordon ==> last

