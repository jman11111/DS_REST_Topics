# REST (Representational State Transfer)
Despite the intimidating acronym representing this concept, it is not as difficult as it first seems. Representational State Transfer refers to the way a well designed web service should work, by transferring states. Navigating a website is done by going between pages, and the page currently diplayed is the current state, thus going to another page is transferring to another state. This style of making a web service aims to attain fast performance, reuseability, and maintainability when developing by using components that can be improved on and changed without changing the entire system. For examples of the concepts flask will be used, so python will be required to test examples.

an example of a page being hosted on the root route "/", returning "Hello World" when requested:

```python
from flask import Flask
app = Flask(__name__)

@app.route("/")
def hello():
    return "Hello World!"
```

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
Statelessness means that no state information is held on the server side, only sent to the server with requests in order to serve them, or to a database from the server for persistent state information/authentication. This makes the server more indepedant of client code, meaning much less chance for failure if the client fails. State in this case refers to the state of the clients interface, the server is not concerned with that. Note that in the previous example the client sends no state data, the only data a client will send is to obtain whats needed from the server.

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

It is a simple key value cache but it can save time when something stored on a database doesnt change often but is accessed frequently, a listener can be used to detect when this change happens and can then update the cache, making it effective once again.

### Layered System
A client should not be able to tell if it is connected to an end server or a middle man, and using an intermediate manager can add security and other features before it talks to the server.

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

# References

- <https://docs.python.org/3/library/shelve.html>
- <https://github.com/pallets/flask/tree/1.0.2/examples/tutorial>
- <http://flask.pocoo.org/>
