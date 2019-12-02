Regex Studies:


Back Reference to capturing group that matches nothing:

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