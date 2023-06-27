# How Parula works

This section explains the basic concepts on which Parula is based. They determine the APIs, so they are good to understand before you start developing.

## Components

1. Audio input from microphones
2. Wake word
   1. Recognizes e.g. "Hey Parula". That allows the device to know that the user speaks to Parula. The main purpose is to avoids false triggers during a conversation between humans.
   2. Silence detection tells that the command finished.
3. Speech recognition
   1. Converts recorded audio into plain text.
4. Intent parser
   1. Matches what the user said with the known commands and known values.
   2. Selects the most probable match, using scoring
5. Voice app
   1. This is equivalent to an application for Linux or Android, and a skill in other voice assistants.
   2. Consists out of Intents, which are high-level functions of the app.
   3. Each Intent has a list of invokation commands, like "play song". Each command can have variables, in this case for the song title.
   4.  The app processes the input and returns a response in plain text form or as object.
6. Speech generation
   1. Converts plain text into a spoken words, i.e. a voice.
7. Audio output
8. Possibly visual output with UI

In Parula, parts 1, 2, 7 and 8 are what we call "client" or "device", where as Parts 3, 4 and 6 are Parula "core". Part 5, the voice apps, may be either be built-in or created by third parties. I.e. you :\).

\(There is only one Parula core in your house, but there can be many Parula "devices" that are connected to the same core. That allows them to work in a coordinated fashion and all use the same data. E.g. you have smart speakers in your living room and your bed room, a Parula app for your desktop computer, and a small water proof device in your bath room. But they all use the same data. -- The federation feature is not implemented yet.\)

## Known vocabulary

While speech recognition might have a reasonably high detection rate for simple words like "play" in a sentence, there is little chance for it to correctly understand all words of a song title or even an artist name. Esp. names will most likely be misunderstood. If we do a simple string matching, character by character, it will likely not find the right results.

If the user says "Play Chopin", a generic speech recognition might recognize "Play shoe pan" or "Play shoe pain". If we search a music library for "shoe pain", we will not find "Chopin". So, we need additional logic to do the matching, not only for commands, but also for the values, like song titles, artists, movie names, or locations and city names. Types like Dates and Times also get special treatment, to recognize "tomorrow at 2 PM".



## Intents are functions

Voice apps do not need to worry about the recognition of what was said. Parula takes care of all that. Voice apps can concentrate on their domain, on what they actually do. The voice app only implements normal functions in your programming language. They are called Intents. They get parameters, which can be words, or entire objects that you specify, and they can return objects as result. These objects have types, and Parula understands the type of the objects.

That allows the system to build a coherent whole, even across applications. For example, one voice app can return a contact person, whereas another voice app places the call. Or, one application allows you to find a restaurant, another allows you to see its rating, and a third app shows you how to navigate there. Parula defines common base types - in this case, a Person, or a Location, or more specifically a business with a Location. These types can be extended dynamically, and there will be a system for apps to agree on common types, called ontology.

## Context

Parula can understand pronouns like "it" and "this artist". It finds what the user refers to, and calls the voice app with the matching object. While doing so, it considers the type of the object, so you will not get a book as argument for a "play song".

The following commands work:

1. "Play Return to sender"
   1. -&gt; Plays the song "Return to sender" by "Elvis Presley"
2. "Play more songs by this artist"
   1. -&gt; Plays all songs by "Elvis Presley" in the library, starting with a random song

Or simple conversations like:

1. Where is Austria?
2. What is its population?
3. Tell me more about it.

## 



