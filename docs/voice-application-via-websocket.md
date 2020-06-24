---
description: >-
  How to implement a voice app in another process and connecting it to Pia core
  using WebSockets
---

# Voice application via WebSocket

There are a few reasons why you might want to implement your application in a different OS process and not as built-in app for Pia:

* You are integration voice control into an existing GUI application, e.g. a GNOME, KDE or Windows application.
* Your application is inherently heavy, because it's loading lots of data, or using large dependency libraries.
* The lifetime of your app needs to be different from Pia core, for whatever reason.
* You absolutely cannot implement the app in JavaScript, but you are forced to implement it in another programming language.

For such cases, you can run your application in a different OS process and connect it to Pia using WebSockets.

## JavaScript

If you're implementing your out-of-process application in JavaScript, please use the base classes that are implemented in Pia.

Install, on command line:

```text
yarn add pia
yarn add pia-ws-app
```

For startup:

```text
import MyApp from './MyApp.js';
import WSAppServer from 'pia-ws-app';

(async () => {
  try {
    let apps = [ new MyApp() ];
    await new WSAppServer(apps).start();
  } catch (ex) {
    console.error(ex);
  }
})();

```

For your app implementation:

```text
import { JSONApp } from 'pia/baseapp/JSONApp.js';
import { getConfig } from 'pia/util/config.js';

export default class MyApp extends JSONApp {
  constructor() {
    super("myapp", ".");
  }

  async load(lang) {
    await super.load(lang);
    let config = getConfig().calendar;
    ...
  }

  /**
   * Intent news, as defined by intents.en.json
   * @param args {
   *   NumberOfResults {Integer}
   * }
   * @param client {ClientAPI}
   * @returns {string} what to say to the end user
   */
  async news(args, client) {
    // client.lang contains the language, but this.getResponse() and this.error() pick the right language for you
    if (args.numberOfResults > 20) {
      throw this.error("too-much"); // translates and throws Error
    }
    let latestNews = ...
    let newsString = latestNews.slice(0, arg.NumberOfResults).join(". ");
    return this.getResponse("news-prefix", { // translates "The latest news are:"
      news: newsString,
    });
  },
}
```

The rest of the voice application implementation is identical to [built-in apps](develop/app/).

## Wire protocol

If you need to implement a voice application in another programming language, and there is no library already available, this section describes the wire protocol, so that you can implement it. It should be simple enough to implement it in a day.

The protocol is based on JSON and function call semantics. For that purpose, it uses a very small layer, with call ID, path, arguments and results.

A call looks like this:

```text
{
  "id": 0,
  "path": "calendar/NextEvent",
  "arg": {
    "args": {
      "numberOfEvents": 1,
    },
    "lang": "en"
  }
}
```

and the response looks like this:

```text
{
  id: 0,
  success: true,
  result: {
    responseText: "Your next appointment is in 2 weeks from now: Pia meeting with Ben Bucksch"
  }
}

```

or in the error case:

```text
{
  id: 0,
  success: false,
  message: "Your calendar is not yet set up. You can do that by going to ...",
  code: "not-configured"
}

```

### Call

Call direction: When then user issues a voice command, Pia core will call your app via this protocol, and your app responds. But during initial registration and for notifications, your app calls Pia core.

The `id` must be different for every call on this WebSocket. It is set by the caller and echoed back in the response.

The `path` is like the path in HTTP URLs. It identifies the intent to be called and contains the app ID + `/` + the intent ID of your app.

The language of the user is in `arg.lang` and contains a 2-letter ISO 631-1 code.

The arguments to your intent are in `arg.args`and will be sent as you defined them in your intents JSON. `NamedValues` will be sent as you defined them during load \(TODO implement the protocol for `NamedValues`\).

### Response

You return your responses, in success and error cases, by sending a message over the WebSocket with JSON as shown in the examples above. The `id` needs to be identical to the call you received. The `responseText` and error `message` need to be translated into the user language, as specified in `arg.lang`during the call. They may contain values already embedded in the string. The error `code` must be language independent and not contain values.

### Registration

At startup, you need to call Pia core and tell it about your app. This happens with a call with path `registerApp`, which you issue right after opening the WebSocket. The argument is the [Intents JSON](develop/app/intents-json.md) file. E.g.:

```text
{
  "id": 0,
  "path": "registerApp",
  "arg": {
    "interactionModel": {
      "languageModel": {
        "invocationName": "myapp",
        "intents": [
         {
           "name": "news",
           "samples": [
             "Tell me what is new",
             "What are the news?",
             ...

```

whereas `invocationName` contains your app ID, and `intents.name`is the intent ID and function name. For a detailed description, see [Intents JSON](develop/app/intents-json.md).

When the user says "What are the news", Pia core will send call function with `path: "myapp/news"`, according to the protocol above, as a message on your WebSocket connection.



