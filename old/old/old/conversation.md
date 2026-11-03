❯ ok so basically the deal is i can understand the core then use it to build    
  bridge and with that ill be say in a good position eg. to understand say a    
  language like python along with some concenineves using those just core and   
  bridge                                                                        
  
⏺ Yes, that's the deal. With the core and the bridge, every part of Python
  already has a place:

  def, lambda                  functions
  tuples, classes, match       data and patterns
  if / else                    cases
  while, for                   a definition that uses itself
  x = x + 1                    state: the hidden memory
  raise, try / except          a request, and a handler that never goes on
  yield, generators            a request, and a handler that goes on later (the
  for loop)
  objects                      data holding functions that share a memory
  TypeError                    "stuck", found while running instead of before
  The rest is conveniences. A list comprehension is map, and a decorator is a
  function that takes a function. Each one is a shorter way to write something
  already in the table. 

  So learning Python becomes reading it through the map: for each feature, ask
  which row it is and what's hidden. The same works for any other language.

✻ Cogitated for 13s · done 17:58

❯ great so its just a matter of writing the core / bridge in a clean simple way 
  thats really it                                                               
  
⏺ Yes, that's really it. The core and the bridge, written cleanly and simply, in
  the order of understanding. Around them go two small pieces: a short chapter
  on conveniences, and the base as one late chapter of "why".

  Half of it already exists. thebook.md covers the core in that style, with
  every example checked. What's missing is the bridge, state and effects,
  explained the same way: starting from what it's for, with examples that mean
  something. Its exact rules are already in summary.md.

  Whenever you want, I can write that chapter into thebook.md.
