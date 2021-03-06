FoURLth
=======

*FoURLth* stands for *URLs in forth*.  Forth is an early, third-generation programming language 
created by Charles Moore in the early 1970s on an IBM 1130.  He thought of it as more of a fourth-generation
language and would have named it "fourth" but for the fact that file names on the 1130 could only be
five characters long at the time.   Hence, *forth*.

Forth makes heavy and more or less explicit use of a stack to maintain execution state.   Because of its
stack-orientedness, *RPN* (Reverse Polish Notation) works naturally for writing forth programs. 

This module works in a python *pyramid* environment using traversal to decode a URL into a forth program.
Whereas an actual forth program that multiplies two numbers and then squares them might look like this:

```forth

 12 27 * dup *

```

The equivalent foURLth "program" would look just like a URL: ``http://fourlth.com/12/27/*/dup/*``

Interaction with Pyramid
------------------------

The *FourlthInterpreter* class is designed to work like a pyramid traversal *resource*, so it
has a ``__getitem__`` method, as well as other methods that can be invoked once the "program" has
been decoded and is ready to run.  

The default view simply returns a JSON object that contains the word ``result`` associated with
whatever the top of the stack was at the end of execution.  If the stack is empty, this value will be ``null``.
There are two other view callables included for demonstration and debugging purposes.

The pyramid traversal algorithim will scan the ``PATH_INFO`` portion of this URL, calling the
resource's ``__getitem__`` method to look up each element.  However, rather than returning the result of
a lookup, the element is incorporated into the "program" being pseudo-compiled and the resource itself
gets returned again.    

Where new words are being defined, the returned resource is actually a new *FourlthInterpreter* instance
that will be embedded in the current one.   More or less the same scheme is used for IF-ELSE-THEN and
LOOP constructions.

Why Bother?
-----------

The idea was to come up with a way to create highly-customized, server-side functions, safely, that could
be rapidly engineered and deployed in web applications.  Specifically, I wanted to have a simple query
engine that could access gene expression data (microarray or RNA seq) and metadata that are part of a larger,
web-based application.   The idea was to be able to, for example, do a search for genes by symbol, then find out
which, if any, datasets had expression information for those genes.   Such a query might look like:

```http

 http://fourlth.net/Gata5/Myb/genesearch/ANY/datasetsearch/metadata

```

which might then return

```javascript

 { 'genes': ['Gata5', 'Muyb'], 'datasets': ['hiltonlab' { 'celltypes:' [...] }, ...] }

```

This JSON object could then be used by client-side logic to populate the web, dynamically.

An addition to building in querying capability, I've also built in several analytical tools.
One could, theoretically, also incorporate an *R* interpreter via the *rpy2* module, but
that necessarily bloats the size of the server-side portion of the application.  Care should be taken 
when doing this.

 
Nick Seidenman <nick@seidenman.net>

