# Midi-Parser
Using Python to transform MIDI files into CSV tables


## The Music21 Library

### Streams
Music21 stores data in **Streams**, which contain objects such as **Note**, **Chord** and **Percussion Chord**.
\
https://web.mit.edu/music21/doc/usersGuide/usersGuide_04_stream1.html#usersguide-04-stream1
https://web.mit.edu/music21/doc/usersGuide/usersGuide_06_stream2.html
\\
Objects stored in a Stream are generally spaced in time, with an offset from the beginning of the stream.

### Functions
Because streams are hierarchical objects which can contain other streams and a variety of subclasses, we need some functions to read them in depending on the information we're looking for:

**flatten()** - Creates a flat representation of a stream, including only elements that are not subclasses

**s.recurse()** - generator to iterate through all the stream layers
