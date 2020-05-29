---
description: How to implement a new voice application for Pia in Javascript
---

# Create the stub files

A voice app, or app, or "skill" allows you to teach Pia new voice commands and react to them. This document is intended for software developers that want to implement a new skill in JavaScript.

## Motivation

So, you want to add new skills to Pia and write a voice app? That's great! We're very happy for your contributions to make Pia more versatile in situations and in tasks where you want her to help you. Once your app is working well and robust, please make a Pull Request to contribute it to Pia's skill set.

## Requirements

We're going to assume you've done the basics:

1. Checked out the Pia source code with git clone
2. Have Pia running from that source
3. Know modern ES6 JavaScript with async/await

## Create a stub

Your app will live in a subdirectory of `app/`. Find a nice, descriptive name for it.

Create the directory.

```text
cd app/
mkdir myapp.js
cd myapp/
touch myapp.js README.md intents.en.json
```

Obviously, adapt `myapp` to your app name. Do the same in all other examples below.

The main entry file **must** have the same name as the directory name. Otherwise, your app will not load.

Now, let's add the app stub files.

### App class stub

Open the file `myappname.js`and adapt it to your app name:

```text
import { JSONApp } from '../../baseapp/JSONApp.js';

export default class MyApp extends JSONApp {
  constructor() {
    super("myapp");
  }

  async load(lang) {
    await super.load(lang);
  }

  /**
   * Command
   * @param args {}
   * @param client {ClientAPI}
   */
  async personDetails(args, client)
  {
    throw this.error("not-implemented");
    return this.getResponse("person-details", { name, city });
  }
}
```

Please replace `myapp` with the directory name and `MyApp` with a class name.

The default export allows Pia to find your app class and load it.

### README

Open `README.md`and add a short and a long description of your application.

The first 1-2 lines should describe it in a very brief way. Then, make an empty line, and describe it in more detail. Include which uses cases and purposes you want your app to fulfill for the user, from an end user perspective. Also decribe on a very high level which technologies or third party applications or libraries it uses.

For example:

```text
Plays local music stored on the local disk.

Uses MPD <https://www.musicpd.org> for the
music archive management, playlist, and playback.

MPD needs to be running.
The server name is configured in config under `mpd`.
```

Describe your app in a similar format.

This is a good moment for explicitly think about a feature list, and to write it down, before you start coding. This will guide you during your later development, and help you to focus on the original goal.

Use Markdown `#` headings and nested `*` lists to format it. Please clearly distinguish between working features and intended features.

However, there is no point in listing available user voice commands, because they will be spelled out in the intents JSON file.

### Intents JSON

Open file `intents.en.json`and fill it out:

```text
{
    "interactionModel": {
        "languageModel": {
            "invocationName": "myapp",
            "intents": [
                {
                    "name": "PersonDetails",
                    "samples": [
                      "Who is {Person}?",
                      "Do you know {Person}?",
                      "Tell me about {Person}"
                    ],
                    "slots": [
                        {
                            "name": "Person",
                            "type": "Person"
                        }
                    ]
                },
                {
                    "name": "AddAppointment",
                    "samples": [
                      "(Add|Make an appointment with {Person} on {Time}"
                    ],
                    "slots": [
                        {
                            "name": "Time",
                            "type": "Pia.DateTime"
                        },
                        {
                            "name": "Person",
                            "type": "Person"
                        }
                    ]
                }
            ],
            "types": [
                {
                    "name": "Person",
                    "basetype": "Pia.List"
                }
            ],
            "responses": {
              "person-details": "%name% lives in %city%",
              "not-implemented": [
                "I have not learned this yet",
                "Somebody please tell me?"
              ]
            }
        }
    }
}
```

You need to replace `myapp` with your app name. Leave the rest as examples for you for now. We will adapt it later.

## Execution model

Before you start writing code, know that built-in apps are going to run in the same process as Pia, and are shipped with every instance of Pia. So, please make sure that your code is of good quality.

A crash in your app, or even just an uncaught exception from an async function, will lead to all of Pia crashing, and that's pretty bad. Also pay attention for leaks, as your app needs to run for months, without using more RAM. Likewise, do not waste computing resources, esp. unnecessary or large or slow network requests, nor CPU time. Remember that your app will run on a Raspberry Pi or an Android phone, not your desktop development machine.

{% hint style="info" %}
Tip: Due to a [bug in node.js import\(\) loader](https://github.com/nodejs/node/issues/32177), syntax errors in your JavaScript files will have no stack. For debugging, you can add this hardcoded import to `baseapp/loader/BuiltinAppsLoader.js`. But do not commit this change. Non-syntax errors get a proper stack.

```text
`import MyApp from '../../app/myapp/myapp.js';`
```
{% endhint %}

