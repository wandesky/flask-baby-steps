# Chapter Two Notes (Basic Applicatino Structure)
## Initialization
All Flask applications must create an application instance (which handles all requests from clients using WSGI protocol). The application instance is an object of class Flask and is created as follows:
```python
from flask import Flask
app = Flask(__name__)
```
Flask uses the (__name__) argument to determine the location of the application, which in turn allows it to locate other files that are part of the application, such as images and templates.
## Routes and View Functions
A route is the association between a URL and the function that handles it.
The app.route decorator is the most convenient way to define a route e.g.
```python
@app.route('/')
def index():
    return'<h1>Hello World!</h1>'
```
A common use of decorators in python is to
register functions as handler functions to be invoked when certain events occur. The index() function above is the handler for the application's root URL. An alternative to the app.route decorator is the app.add_url_rule()method shown below:
```python
def index():
    return '<h1>Hello World</h1>'

app.add_url_rule('/', 'index', index)
```
The index() function that handles application URLs is called the view function.
Flask can support URLs with dynamic components using the code below:
```python
@app.route('/user/<name>')
def user(name):
    return '<h1>Hello, {}!</h1>'.format(name)
```
Flask also supports routes of other types such as int, string, float and path e.g.
/user/<int:id> for integer types.
## A complete application
```python
from flask import Flask
app = Flask(__name__)

@app.route('/')
def index():
    return '<h1>Hello World!</h1>'
```
## Development Web Server
The web server is started by the ```flask run``` command. This looks for the name of the python script that contains the application instance in the FLASK_APP environment variable.
#### Linux
```python
export FLASK_APP=hello.py
flask run
```
#### Windows
```python
set FLASK_APP=hello.py
flask run
```
The default Flask web server should only be used for development and testing.

The Flask dev web server can also be started by invoking the app.run() method. Older Flask versions which lacked the flask command required the server to be started by running the application's main script:
```python
if __name__=='__main__':
    app.run()
```
The app.run() command can still be useful such as during unit testing.
## Dynamic Routes
```python
@app.route('/user/<name>')
def user(name):
    return '<h1>Hello, {}!</h1>'.format(name)
```
## Debug mode
In debug mode, two modules of the development server(reloader and debugger) are enabled by default.
The reloader restarts the server whenever a file is modified.
The debugger raises an unhandled exception in an interactive stack trace on the browser.
The debug mode is enabled by:
```export FLASK_DEBUG=1``` or in windows ```set FLASK_DEBUG=1```
In the case of app.run, the FLASK_APP and FLASK_DEBUG environments are not used, instead ```app.run(debug=true)```
## Command-Line Options
```flask --help``` or simply ```flask``` will list the options.
Demo: ```flask run --help```
The ```flask shell``` command starts python shell sesion in the context of the application (which is useful for running maintenance tasks or tests, or debug issue).
The ```flask run``` command runs the application with the development web server.
Running ```flask run --help``` will list other useful options such as ```--host``` argument that can set the network interface to listen to for connections from clients.
```flask run --host 0.0.0.0 ```
## The Request-Response Cycle
The request object encapsulates the HTTP request sent by the client.
Flask uses contexts to temporarily make certain objects globally accessible thereby avoding cluttering.
```python
from flask import request

@app.route('/')
def index():
    user_agent = request.headers.get("User-Agent")
    return '<p>Your browser is {}</p>'.format(user_agent)
```
Contexts enable Flask to make certain variables globally accessible to a thread without interfering with other threads.
Flask has two contexts: _application_ and _request_ context containing the following variables:
| Variable name  | Context   | Description  |
|---|---|---|
| current_app  |app.   |The application instance for the active application   |
| g  |app.   | an object that the application can use for temporary storage during the handling of a request. This variable is reset with each request.  |
|request   | request  |The request object, which encapsulates the contents of an HTTP request sent by the client.   |
|session   |request   |The user session, a dictionary that the application can use to store values that are 'remembered' between requests   |

### Request Dispatching
For each client request, the application needs to find out what view function to invoke to service it.
To view the URL map created for hello.py, the commands below can be used(remember to run the python terminal in venv):
```python
>>> from hello import app
>>> app.url_map
```
The output will be something similar to:
```
Map([<Rule '/' (HEAD, OPTIONS, GET) -> index>,
 <Rule '/static/<filename>' (HEAD, OPTIONS, GET) -> static>,
 <Rule '/user/<name>' (HEAD, OPTIONS, GET) -> user>])
```
The '/static/<filename>' is a special route added by Flask to give access to static files.
The(HEAD, OPTIONS, GET) elements are the request methods that are handled by the routes. Flask attaches methods to each route so that different request methods to the same URL can be handled by different view functions. The HEAD and OPTIONS methods are managed automatically by Flask.

### The Request Object
The request object contains all the information that the client included in the HTTP request. The table below shows some of the most common attributes and methods of the request object.
| Method  | Description   |
|---|---|
| form  |A dictionary with all the form fields submitted with the request   |
| args  |A dictionary with all the arguments passed in the query string of the URL   | 
| values   | A dictionary that combines the values in form and args  |
| cookies  | A dictionary with all the cookies included in the request   |
| get_json  | Returns a python dictionary with the parsed JSON included in the body of the request   |
| blueprint  | The name of the Flask blueprint that is handling the request   |
_ Consult the table on page 37 for more methods _

### Request Hooks
Request hooks provide the option to register common functions to be invoked before or after a request is dispatched. e.g. authenticating a user before making a request.
The request hooks are implemented as decorators. 4 hooks supported by Flask are:
#### before_request
Registers a function to run before each request
#### before_first_request
Registers function to run only before the first request is handled (e.g. when adding server initialization tasks)
#### after_request
Registers a function to run after each request, but only if no unhandled exception occurred.
#### teardown_request
Registers a function to run after each request, even if unhandled exceptions occurred.
It is common to share data between request hook functions and view functions is by using the g context global storage.

### Responses
The default HTTP response status code is 200. It is possible to respond with a different status code by passing it as a second return value after the response text e.g.
```python
@app.route('/')
def index():
    return '<h1>Bad request</h1>', 400
```
The request can also include a dictionary of headers added to the HTTP response as a third argument.
It is also possible to create a response object with all the arguments packed in it using the make_response() function:
```python
@app.route('/')
def index():
    response = make_response('<h1>This doc carries a cooke</h1>')
    response.set_cookie('answer', '42')
    return response
```
A table showing some of the attributes and methods of the response object.
| Attribute or Method  | Description   |
|---|---|
| status_code  |The numeric HTTP status code   |
| headers  |A dictionary-like object with all the headers that will be sent with the response   | 
| set_cookie()   | Adds a cookie to the response  |
| delete_cookie()  | Removes a cookie   |
| content_length  | The length of the response body   |
| content_type  | The media type of the response body   |
| set_data()  | Sets the response body as a string or bytes value   |
| get_data()  | Gets the response body   |

Redirect and Abort are special responses
#### Redirect
```python
from flask import redirect

@app.route('/')
def index():
    return redirect('http://www.sample.com')
```

#### Abort
```python
from flask import abort

@app.route('/user/<id>')
def get_user():
    user = load_user(id)
    if not user:
        abort(404)
    return '<h1>Hello, {}</h1>'.format(user.name)
```
Abort does not return control back to the function because it raises an exception.

## Flask Extensions
Flask is designed to be extended and important functionality  (e.g. db and user authentication) has been intentionally left out to give the programmer freedom to select a package, or write their own.