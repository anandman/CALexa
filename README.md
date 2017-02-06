#CALexa - An Alexa Skill for accessing CalDAV Calendars  

Currently Alexa comes with a built-in google calender. If you're devoted to privacy principles and running your own CalDav-Server such as [Baikal](http://sabre.io/), but still go along with latest technology trends, you may not come around the amazon Alexa natural language assistent. This skill gives you the opportunity to easily host your own CalDav skill, e.g. using [Nginx](https://nginx.org) on a [Raspberry Pi](http://www.raspberrypi.org), to make your Calendar transparently accessible for Alexa.

## Technical Aspects
This skill uses [Flask-Ask](https://flask-ask.readthedocs.io), a Python micro-framework that simplifies developing Alexa skills. Great work [John Wheeler](https://twitter.com/johnwheeler_)! With a little knowledge of Python and Flask it is very simple to extend.

## Running the CALexa Skill on a Raspberry Pi

If you haven't setup your own CalDAV server, I recommend looking at [Jan Karres' Blog](https://jankarres.de/2014/01/raspberry-pi-baikal-caldav-und-carddav-server-installieren/).

### Nginx Configuration
> **Note:**
> - If you're not confident using Nginx see the [beginners guide](http://nginx.org/en/docs/beginners_guide.html)!
> - In order to be able to interact with Amazon Alexa, your endpoint either has to provide a certificate from a trusted certificate authority or you have to upload a self-signed certificate in X.509 format.

1) Add the following entry to your Nginx configuration (you may find it at /etc/nginx/sites-available/default):
```nginx
location /calexa {
rewrite ^/calexa/?(.*)$ /$1 break;
    proxy_pass http://localhost:5000;
    client_max_body_size 0;

    proxy_connect_timeout  36000s;
    proxy_read_timeout  36000s;
    proxy_send_timeout  36000s;
}
```
2) Restart Nginx.

3) Start the CALexa skill as well:
```sh
python /yourpathtocalexa/calexa.py
```
Your service will now be available via https://yourdomain/calexa/


## Configuration <i class="icon-cog"></i>

#### Intent Schema


```
{
  "intents": [
    {
      "intent": "GetTodayEventsIntent"
    },
    {
      "intent": "AMAZON.HelpIntent"
    },
     {
      "intent": "AMAZON.StopIntent"
    },
     {
      "intent": "AMAZON.CancelIntent"
    }
  ]
}
```

#### Sample Utterances:
```
GetTodayEventsIntent Was ist auf dem Kalender
GetTodayEventsIntent Was ist heute auf dem Kalender
GetTodayEventsIntent Gib mir die heutigen Ereignisse
GetTodayEventsIntent Gib mir die heutigen Termine
GetTodayEventsIntent Gib mir die Termine für heute
GetTodayEventsIntent Gib mir die Ereignisse für heute
GetTodayEventsIntent Was habe ich heute für Termine
GetTodayEventsIntent Was habe ich heute für Ereignisse
```


## Interaction Example

```sequence
User->Alexa: Alexa, was ist heute auf dem Kalender?
Note right of Alexa: Alexa does NLP
Alexa--> Skill: GetTodayEventsIntent
Note right of Skill: Skill accesses CalDAV server
Skill-->Alexa:  String(Es sind folgende Termine auf dem Kalender ...)
Note right of Alexa: Alexa synthesises NL from string
Alexa-->User:  Es sind folgende Termine auf dem Kalender ...


```
> **Note:**

> - CALexa is still in **beta** state
> - Thank you @ [Stackedit](https://stackedit.io) to make this markdown such beautiful!
