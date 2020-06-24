---
description: Declarative description of your voice app
---

# Intents JSON

The intents JSON contains a high level description of your voice app, which functions it supports, what the user says to invoke them, which parameters they have, and the responses. It describes your app from a user perspective.

This information is used by Pia core to know what your application can do, when to invoke your application, and how. It also contains the translations of your app's input and output into various languages.

When you develop your application, this should be the first file that you write, before you start writing any code, immediately after writing the README and TODO files. Likewise, it should be the first code file that another developer reads when he starts to look at your application's code. 

Please take a look at the examples in [Pia source code](https://github.com/benbucksch/pia/tree/master/app/), in `apps/*/intents.en.json`. 

TODO Describe all sections in detail.

Currently, all data is underneath `interactionModel.languageModel`.

`invocationName`contains the application ID. It is used for internal purposes only, but must be unique. It should not be a UUID, but an English name.

## Intents

## Responses

`responses`contains an object map, whereas the property name is the string ID, which you pass into `this.getResponse(id)` or `this.error(id)` and the value is either a string or an array of strings. If it's an array of strings, a random entry will be chosen. This avoids too much annoying repetition and makes Pia nicer to listen to.









