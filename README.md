# Opened captions server for google docs apps script

Work in progress. 

The aim is to make a system that takes in captions coming from [openedcaptions.com](https://openedcaptions.com) by Dan Schultz and adds them to a google doc in real time (or close enough) untill you stop it.

Issue: Google apps script can refresh like a cron job stile only every minute, so it might make it challenging to pull from the openedcaptions server through scokets connection. There is the need for an intermediate server to buffer the text (which is what this repo is doing). 

For the Google app script initially looked at [NPR one on github](https://github.com/nprapps/debates) from [their live fact checking debate](https://source.opennews.org/en-US/articles/how-npr-transcribes-and-fact-checks-debates-live).
Then tried to do a simplified version, which fetches text from a rest API end point and adds it to google doc every minute.
working version in[./google_app_script/main.gs](./google_app_script/main.gs)

## Opened Captions
Some more details on opened captions for context. 

>Opened Captions: Turning the spoken words on TV screens into streams of hackable data

- [source piece](https://source.opennews.org/en-US/articles/introducing-opened-captions)
- [niemanlab](http://www.niemanlab.org/2012/12/opened-captions-turning-the-spoken-words-on-tv-screens-into-streams-of-hackable-data/)

>The result: Opened Captions. It provides a real-time API for closed captions pulled from C-SPAN. The system makes it possible to code against what’s being said on TV right now, and by solving this one really tricky problem, it makes a broad range of applications possible.
https://openedcaptions.com/ 

- [opened-captions-example](https://github.com/slifty/opened-captions-example) 
- [opened-captions](https://github.com/slifty/opened-captions)
- [CSPan live stream](http://www.stream2watch.cc/live-television/united-states/c-span-live-stream) to check against captions.


## Setup and run 

`npm install` then `npm start` it gets the stream from opencaptions and store into `transcription.txt`. It also opens a server on port `5000`.

If you pass the querystring offset it offsets the string
enter with the browser to `localhost:5000` you get everything.
If you go to `localhost:5000?offset=100` you get the text starting from the 101 character from when you started the server.


## Connnect to google docs / Apps script
How would I connect this to a google doc?

So you can create your google apps script that just query this server every minute and append the data.

[as npr did](https://github.com/nprapps/debates/blob/master/google_apps_scripts/main.js) you run that time trigger which is like a cron that pulls this server we just created.

Here is an [easy way to run the google app script every minute](https://deveopers.google.com/apps-script/guides/triggers/installable#time-driven_triggers)

You can find  the google app script to add to the google doc in the [google_app_script](./google_app_script)

### intermedia server
For buffering the text. Run this server on a heroku or EC2 instance or something similar. 

This is needed because opencaptions uses websockets and google apps script don't. Otherwise it could all be run just inside of a google app script.

## testing app script using ngrok
[ngrok](https://ngrok.com/) description

>Don’t constantly redeploy your in-progress work to get feedback from clients. ngrok creates a secure public URL (https://yourapp.ngrok.io) to a local webserver on your machine. Iterate quickly with immediate feedback without interrupting flow.

To install

`npm install -g ngrok`

To run, start ngrok forwarding `ngrok http 5000`. 
This will give you a url like this [http://c8b8351d.ngrok.io/](http://c8b8351d.ngrok.io/)


## How to use 

A little fiddly to setup, but it works for now. 

- create a google doc, and add a scrip to it. [tools -> script editor]
- copy and paste [./google_app_script/main.gs](./google_app_script/main.gs) into the google app script. 

- Start local server for this reposetory using `npm start` from terminal
- start `ngrok` by typing `ngrok http 5000` and hit enter
- take URL provided by ngrok and add it to the google app script.  similar to `http://c3d6d9e8.ngrok.io`
- add this url to the google app script, line 17 `var openedCaptionsIntermediateEndPointEndServer = 'http://c3d6d9e8.ngrok.io';`
- add script global variable for offset as key and value of `0` to begin with under `file -> projec properties -> script properties.` (repeat this step if you need to reset the script).
- setup 1 minute triggered event in google app script  for `myFunction` under Resources -> All your treiggers. 
- Watch the text being updated in the google doc.




## Contributors

[Dan Z](https://github.com/impronunciable)

[Pietro](https://github.com/pietrop)