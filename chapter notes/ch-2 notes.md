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