---
title: "HTMX: Simple AJAX"
date: 2026-02-10
draft: true
tags:
  - blog
  - python
  - learning
---
We all remember AJAX (Asynchronous JavaScript and XML). I've spent many hours and many days creating XMLHttpRequest objects and using jQuery's .get and .post methods to connect HTML to a PHP backend to achieve that true "no full page load" magic. That's still possible today... It's also even simpler with [HTMX](https://htmx.org), a small library that makes HTML dynamic. At its most basic, HTMX queries endpoints and updates HTML elements with the returned content (usually text or HTML). It supports GET and POST, but also PUT, DELETE, etc.

We will be building a small web service with a dynamic front-end powered by FastAPI and HTMX. Comments have been removed to keep it short, but the full version is available [here](https://github.com/mlfbvr/fastapi-htmx-project)

_Important note: This is a project for me to learn FastAPI and HTMX. Do not use this in production **as-is**. I cannot stress this enough._

### Starting up
_This article assumes a certain familiarity with HTML and  Python and that python is installed_

To start, we will create the basis of the project by running these commands.
We are using a python virtual env located in .venv, but it can be located anywhere.
We also activate the virtual env, install fastapi with a standard set of dependencies and create our `templates` directory

```
$ mkdir ~/fastapi_htmx
$ cd ~/fastapi_htmx
$ python3 -m venv .venv
$ source ~/fastapi_htmx/.venv/bin/activate
$ pip install "fastapi[standard]"
$ mkdir templates
```

Now that the directories and virtual environment are ready, let's open up our editor and create the basic files:
`main.py`
```python linenums="1"
from fastapi import FastAPI, Request
from fastapi.templating import Jinja2Templates

app = FastAPI()
templates = Jinja2Templates(directory="templates")


@app.get("/")
def read_root(request: Request):
    return templates.TemplateResponse("index.html", {"request": request})

```

We start by importing `FastAPI` and `Request` from `fastapi`, as well as `Jinja2Templates` from `fastapi.templating`, which will be used to render our templates. We also define the FastAPI `app` and the `templates` template repository

Our first `GET` route will return the rendered Jinja2 template `templates/index.html`

`templates/index.html`
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>FastAPI + HTMX!!!</title>
</head>
<body>
   <h1>FastAPI + HTMX!!!</h1> 
</body>
</html>
```

Once the template is created, we can start the fastapi server and view the results so far. 

```bash
$ fastapi dev
```

Open `http://127.0.0.1:8000` in your browser to see our page with a big "FastAPI + HTMX!!!"

### Next Step
At its most basic, HTMX works by retrieving data, whether in HTML or text, and swapping it with pre-existing HTML elements. For example, we can create a simple route in our backend that will, for this example, simulate a call to some external weather API and return a snippet of HTML with the conditions and the temperature.

To prepare, let's make the following changes to our `home` HTML template:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <script src="https://cdn.jsdelivr.net/npm/htmx.org@2.0.8/dist/htmx.min.js"></script>
    <title>FastAPI + HTMX!!!</title>
</head>
<body>
   <h1>FastAPI + HTMX!!!</h1>
   <h2>Current Weather Conditions</h2>
   <p id="loading" class="htmx-indicator">Loading...</p>
   <div id="weather" hx-get="/weather" hx-indicator="#loading" hx-trigger="load, every 5s"></div>
</body>
</html>
```

We added the following elements:
- a `<script>` tag that loads htmx version 2.0.8 from jsdeliver.net
- a `<h2>` header for the header conditions section
- a `<p>` element that will be our loading indicator (note that the class `htmx-indicator` is mandatory)
- a `<div>` that will contain the HTML returned by our API call (the weather information), with the following attributes:
	- `hx-get="/weather"` performs a `GET` request on the `/weather` endpoint
	- `hx-indicator="#loading"` identifies our loading indicator, the `<p>` with the id of `loading`
	- `hx-trigger="load, every 5s"` defines the trigger for the query. In this case we want it triggered when the element originally loads, then every 5 seconds.

Next thing we need to do is to create the template that will be rendered

`templates/weather.html`
```html
<p>Temperature: {{ weather.temperature }}°C</p>
<p>Conditions: {{ weather.conditions }}</p>
```

Since we only have a few templates, we can store them all together. For a more complicated site, these small templates can be stored in a sub-directory like `templates/partials` or `templates/snippets`

We also need to define our route to get the weather data from. To do that, we'll modify our FastAPI app and add the following code:

```python

@app.get("/weather/")
def read_weather(request: Request):
    from time import sleep
    from random import randint

    sleep(1)
    weather_data = {"temperature": randint(15, 30), "conditions": "Sunny"}
    return templates.TemplateResponse(
        "weather.html", {"request": request, "weather": weather_data}
    )
```

This route will receive the `GET` request, sleep for 1 second to simulate fetching the data from an external API, and return the simulated data with a temperature between 15 and 30 degrees and sunny conditions.

Back in our web browser, we should now see our "Current Weather Conditions" header, a random temperature that gets updated every 5 seconds, "Conditions: Sunny". You'll also see "Loading..." appear every 5 seconds and go away after 1 second.

### Completing the app
The next thing we need to know so we can begin really working with both FastAPI and HTMX is how to handle `POST` requests.

For this, we will need a form so let's implement a basic guest book that visitors to our app can sign. It will have one field, name, which is required.

Edit the `templates/index.html` file and add the following code to the bottom of the `<body>` tag:

```html
   <h2>Guest Book</h2>
   <div id="guestbook">No guests yet.</div>
   <form hx-post="/sign" hx-target="#guestbook">
       <input type="text" name="name" placeholder="Your name" required>
       <button type="submit">Sign Guest Book</button>
   </form>
```

This gives us a header, list of guests who have signed and a form for new guests to sign.

A few things to note:
- "No guests yet." is the default content for the `guestbook` div, meaning it will be displayed until someone signs our guest book
- The form uses different HTMX attributes:
	- `hx-post="/sign"` defines the action of the form as a `POST` to the `/sign` endpoint
	- `hx-target="#guestbook"` indicates the target where the contents will be swapped. In the previous example, `hx-target` was omitted, which made the action of retrieving the weather target the current element by default.


The route to sign the guestbook is pretty simple:

```python
signatures = [] # Simulates a persistent storage for signatures


# Use Pydantic to define a model for the form data
class FormData(BaseModel):
    name: str


@app.post("/sign")
def sign(request: Request, form_data: FormData = Form(...)):
    from datetime import datetime

    signature = {"name": form_data.name, "timestamp": datetime.now()}
    found = list(filter((lambda s: s["name"] == form_data.name), signatures))

    if not found:
        signatures.append(signature)

    return templates.TemplateResponse(
        "signatures.html", {"request": request, "signatures": signatures}
    )

```

We also need to import `Form` from `fastapi` by editing the first line of our application:
```python
from fastapi import FastAPI, Request, Form
```

The `signatures` list will act as our persistent storage and store the signatures.

The data submitted from the form is parsed and turned into our `form_data`. It's also automatically validated through the use of a Pydantic model.

Once the data is received, we create a `signature` dict with the name of the guest and a timestamp. We also validate that the guest has not signed the guestbook, using filter and a lambda function, before adding it to our `signatures` list.

The route then returns the parsed signatures template with the `signatures` list as its context, which HTMX then uses to re-render the guestbook signature list on our page

`templates/signatures.html`
```html
<ul>
{% for signature in signatures %}
    <li>{{ signature.name }} signed at {{ signature.timestamp }}</li>
{% endfor %}
</ul>
```

### Conclusion
As mentioned at the beginning, this is not a production-ready end-to-end application. A lot of things are missing, like proper error validation and other optimisations. But it still demonstrates the basic functionality of HTMX and also gives a short demonstration of what FastAPI can do.

For more information, I recommend the documentation of both projects, available at the links below:
- [https://fastapi.tiangolo.com/learn/](https://fastapi.tiangolo.com/learn/)
- [https://htmx.org/docs/](https://htmx.org/docs/)
