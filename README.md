# ConvaiClient CDN Package

This repository contains the source code for the ConvaiClient package, which is hosted on a CDN for easy inclusion in web pages without the need for npm or other package managers.

## Description

ConvaiClient is a JavaScript library that provides an interface for interacting with the Convai AI platform. It allows you to send messages to the AI and receive responses, as well as configure various options and settings.

## Usage

To use the ConvaiClient package in your web application, simply include the following `<script>` tag in your HTML file:
```html
<script src="https://Conv-AI.github.io/web-sdk-cdn/convai-client.js"></script>

```
If the environment/platform only allows to work inside scripts, For example: Playcanvas.  You can use the following code to include the ConvaiClient package:
```javascript
var convaiScript = document.createElement('script');
var scriptLoaded = false;
convaiScript.src = "https://Conv-AI.github.io/web-sdk-cdn/convai-client.js";
document.getElementsByTagName('head')[0].appendChild(convaiScript);
var ConvaiNpc = pc.createScript('convaiNpc');
```

Once included, you can create a new instance of the ConvaiClient class and start using its methods:

```javascript

var convaiClient = null;
var userTextStream = "";
var npcTextStream = "";
var keyPressed = false;

/**
 * Initializes the ConvaiClient with an API key, a character ID, and flags for enabling the audio recorder and player. Call this first from Start()
 * Sets up several callbacks for handling different types of responses from the Convai Client.
 * @param {string} apiKey - The API key for the Convai service.
 * @param {string} characterId - The ID of the character to use in the Convai service.
 * @param {boolean} enableAudio - Flag to enable the audio player.
 * @param {string} sessionId - current conversation session. Can be used to retrieve chat history.
 * @param {boolean} [disableAudioGeneration=false] - Flag to disable audio generation. For chat only applications.
*/
function initializeConvaiClient () {
  convaiClient = new ConvaiClient({
    apiKey: <apiKey>,
    characterId: <characterId>,
    enableAudio: true,
    sessionId: <sessionId>,
    disableAudioGeneration: false,
  })

  convaiClient.setResponseCallback(function (response) {
    if(response.hasUserQuery){
        var transcript = response.getUserQuery();
        if(transcript){
        var isFinal = transcript?.getIsFinal();
        var transcriptText = transcript.getTextData();
        if(isFinal){
            userTextStream += " " + transcriptText;
        }
        userTextStream = transcriptText;
        }
    }

    if(response.hasAudioResponse()){
        var audioResponse = response.getAudioResponse();
        npcTextStream += " " + audioResponse.getTextData();
    }
  })

}

function handleKeyDown(e){
    if(convaiClient && e.keyCode === 84 && !keyPressed){
        e.stopPropagation();
        e.preventDefault();
        keyPressed = true;
        userTextStream = "";
        npcTextStream = "";
        convaiClient.startAudioChunk();
    }
}

function handleKeyUp(e){
    if(convaiClient && e.keyCode === 84 && keyPressed){
        e.preventDefault();
        keyPressed = false;
        convaiClient.endAudioChunk();
    }
}
window.addEventListener("keydown", handleKeyDown);
window.addEventListener("keyup", handleKeyUp)
```


Functions available in the ConvaiClient class include:
```javascript
/**
* Resets the conversation session. This will clear the conversation history and start a new session.
 */
convaiClient.resetSession();

/**
 * Set a callback function to handle responses.
 * @param {function} callback - The callback function will have response as an argument which will give you all the response data.
 */
convaiClient.setResponseCallback(callback);

/**
 * Set a callback function to handle errors.
 * @param {function} callback - The callback function to handle potential network errors.
 */

convaiClient.setErrorCallback(callback);

/**
* Send a text chunk to the convai client. This will generate a response which can be received in setResponseCallback.
 * @param {string} text - The text content.
 */
convaiclient.sendTextChunk(text:string);

/**
 * Start audio recording for audioQuery.
 */
convaiClient.startAudioChunk();


/**
 * End audio recording for audioQuery.
 */
convaiClient.endAudioChunk();

/**
 * Set a callback for audio play events.
 * @param {function} fn - The callback function is called when the npc audio starts playing.
 */
convaiClinet.onAudioPlay(fn: () => void);

/**
 * Set a callback for audio stop events.
 * @param {function} fn - The callback function is called when the npc audio stops playing.
 */ 
convaiClinet.onAudioStop(fn: () => void);


```



