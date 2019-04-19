# REST (Representational State Transfer)
Despite the intimidating name, Representational State Transfer (REST) is not as difficult as it first seems. REST refers to the way a well designed web service should work, by transferring states. Navigating a website is done by going between pages, and the page currently displayed is the current state, thus going to another page is transferring to another state. This style of developing a web service aims for fast performance, reusability, and maintainability by using components that can be improved on and changed without changing the entire system. Flask will be used as examples throughout this chapter, so Python will be required for testing purposes.

An example of a page being hosted on the root route "/", returning "Hello World" when requested:

```python
from flask import Flask
app = Flask(__name__)

@app.route("/")
def hello():
    return "Hello World!"
```

On the subject of routes and routing, building organized routes is important. For instance if you have a login page, you'll want the route to be something like "mywebsite/login" with "/login" being the route. Here are some tips for building a good URL:

- Make it human readable, use words that people will understand and that make sense to you.
- Make the routes distinct, this will reduce confusion as to what page is where.
- Plan it out beforehand. This is good advice for many things, but especially here where a website can have many pages.
- Don't use too many dynamic URLs, such as a code for a specific users info when it is not necessary, these can be mandatory, but when they are not try to avoid it.
- Use underscores and in general keep routes short, the longer the routes get the harder they are to read, and use underscores to separate words.

## REST Style
In order for a web service to be considered RESTful, it must follow 6 constraints:

### Client–server Architecture
This follows the Software Engineering principle of separation of concern, which is the practice of separating functionality of a program into pieces, every piece having one function, or concern. In terms of a web service this means separating the webserver and client interface into separate sections. This creates reuseability for the client side software, as well as extensibility for the server side. 

An example of a webserver, this returns html for the client to render when the base route "/" is accessed:

```python
@bp.route('/')
def index():
    """Show all the posts, most recent first."""
    db = get_db()
    posts = db.execute(
        'SELECT p.id, title, body, created, author_id, username'
        ' FROM post p JOIN user u ON p.author_id = u.id'
        ' ORDER BY created DESC'
    ).fetchall()
    """return html"""
    return render_template('blog/index.html', posts=posts)
```

### Statelessness
Statelessness means that no state information is held on the server side, only sent to the server with requests in order to serve them, or to a database from the server for persistent state information/authentication. This makes the server more independent of client code, meaning much less chance for failure if the client fails. State in this case refers to the state of the clients interface, the server is not concerned with that. Note that in the previous example the client sends no state data, the only data a client will send is to obtain what is needed from the server.

### Cacheability
REST web services must use caches when appropriate. Defining responses from a server as cacheable or not correctly can prevent clients from getting out of date or incorrect data, while also eliminating some interactions with the server side, as the data will already be on the client. This not only improves performance, but also takes away some dependance on a server. Here is an example of one way to cache in Python, using the shelve module linked to at the end of this chapter.

```python
import shelve

d = shelve.open(filename)  # open -- file may get suffix added by low-level
                           # library

d[key] = data              # store data at key (overwrites old data if
                           # using an existing key)
data = d[key]              # retrieve a COPY of data at key (raise KeyError
                           # if no such key)
```

It is a simple key value cache but it can save time when something stored on a database doesn't change often but is accessed frequently, a listener can be used to detect when this change happens and can then update the cache, making it effective once again.

### Layered System
A client should not be able to tell if it is connected to an end server or a middle man, and using an intermediate manager can add security and other features before it talks to the server. This requires that the client and server be very distinct, or in this case the interface and data access layer, because the data access layer can be comprised of more that a server.

### Client Side Scripts/Code
The ability to send executable code to a client to extend its functionality is also a requirement of REST.

### Uniform Interface
This is the main constraint of REST and separates the web service to allow for a request response to be developed independently from others.

#### Resource identification in requests
This means that the resource returned from a request is defined by the request itself, letting the server handle its data separately from what it returns, only concerned with returning the correct information based on the request.

#### Resource manipulation through representations
A client gets enough information from a request that it can manipulate the data on its end(update, delete).

#### Hyperlink connectivity web
A client should be able to navigate a website using the hyperlinks sent to it from the server, and should have access to all pages intended for a client once it has gotten to a page on the website.

# CRUD
A concept that goes along with REST is CRUD, which stands for create, read, update, and delete. These are the four functions needed for a persistent database, it must be able to create data, read its values, update those same values, and delete whatever data that may need to be deleted.

## CRUD and REST
CRUD relates to REST in the way that HTTP requests, which are the main way clients communicate with a server, can all fit into one of the CRUD functions.

- Create corresponds to POST requests, which are used to send data along with the request, typically to create something.
- Read corresponds to GET requests, which do not send info along, they simply request a URL for data, very much like a reading of data.
- Update corresponds to PUT requests, which send data along with the purpose of using it for updating existing data.
- Delete corresponds to DELETE requests, which are simple requests sending enough data to target something to delete.

# HTTP
HTTP requests are how applications communicate with a website, and they are composed of a few parts.

## Headers
Headers allow the client and the server to pass additional information with the request or the response, and are generally used for authentication or sending info for the server to use.

An example of an HTTP request sent using CURL with a header:
```
curl --header "Username: Jorgen233" www.google.com
```

## Payload
Payload is the actual data of the request when a POST request is sent, it can be in different formats but it is always necessary data.

An example of a payload for a request in Javascript:

```javascript
POST /some-path HTTP/1.1
Content-Type: application/json

{ "firstname" : "Jack", "lastname" : "Broncato" }
```

The payload is formatted in a JSON file format, with the two fields firstname and lastname, both containing their values

# References

- 1. Pallets, Davidism. “Pallets/Flask.” GitHub, Github, 13 Apr. 2018, github.com/pallets/flask/tree/1.0.2/examples/tutorial.
- 2. Reschke, Julian F. “HTTP/1.1, Part 3: Message Payload and Content Negotiation.” IETF Tools, 2011, tools.ietf.org/html/draft-ietf-httpbis-p3-payload-14#section-3.2.
- 3. Ronacher, Armin. “Welcome.” Welcome | Flask (A Python Microframework), Flask, 2019, flask.pocoo.org/.
- 4. “Shelve - Python Object Persistence¶.” Shelve - Python Object Persistence - Python 3.7.3 Documentation, 17 Apr. 2019, docs.python.org/3/library/shelve.html.
