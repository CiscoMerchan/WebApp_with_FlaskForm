# Create a Login form with FlaskForm 


To follow with the [video](https://www.youtube.com/watch?v=eY7pSCeF_Rg) please install:
    
   *[Python 3.9 or later](https://www.python.org/downloads/)

   *git 

   *[Pycharm Community Edition 2022.2](https://www.jetbrains.com/pycharm/download/#section=windows)

References:

   * [Flask-WTF](https://flask-wtf.readthedocs.io/en/1.0.x/)
   
   * Jinja2 engine [https://jinja.palletsprojects.com/en/3.1.x/templates/#import-context-behavior](https://jinja.palletsprojects.com/en/3.1.x/templates/#import-context-behavior)

   **Learning objectives:**

  * Create a web application with login form using:
    * Flask 
    * FlaskForm class(with validators)
    * Template Inheritance with Jinja2 
    * flask_bootstrap


**Challenge 1: Create a route “/login” that when the user clicks on “Login” will open the ‘login.html’**

   * Create an app decorator route with “/login”

   * Create a login function to render the “login.html” template. 

   * Create a link between the login button in 'index.html' and login() function in 'main.py'
        - To scape the HTML syntax we use Jinja2, double curly **`{{ }}`** brackets allows us to evaluate an expression, variable or function call and print the 
          result into the template.
        - to create a link use [url_for()](https://tedboy.github.io/flask/generated/flask.url_for.html) that will map the ‘login’ function in 
        the _href=" "_ inside the anchor <a> tag that is in "index.html" , like this when the user clicks on Login button the user will be redirected to "login.html".

   - Refresh the browser and click on the Login button.     


#### Setting up Form


**Challenge 2:  In main.py create a Form with WTForm([FlaskForm](https://flask-wtf.readthedocs.io/en/0.15.x/form/))**


* Set in main.py flask_wtf and wtforms:
   
   ```
      from flask_wtf import FlaskForm
      from wtforms import StringField,SubmitField```


* To build a web form, create a subclass call LoginForm that inherits class FlaskForm base and define the form [fields](https://wtforms.readthedocs.io/en/3.0.x/fields/) in the form with 
  wtforms class variables :
  
   
    ```
       class LoginForm(FlaskForm):
         email = StringField(label='email')
         password = StringField(label='password')
         submit = SubmitField(label='Log')```
	

**By default, WTForm  protects all forms against [Cross-Site Request Forgery (CSRF)](https://owasp.org/www-community/attacks/csrf) attacks. To implement CSRF protection.**
    
   * Build a configuration value for protect the LoginForm from CSRF attacks 
	 

    `app = Flask(__name__)
     app.config['SECRET_KEY'] = 'Very secret string'`


**Challenge 3:  Render  the LoginForm on login.html template**

  * Instantiate LoginForm  in variable call 'form' to pass as an argument in render_template('login.html').
      
      ```
         @app.route("/login")
         def login():
           form = LoginForm()

           return render_template('login.html',form=form)```

  * Display the web-form in login.html using the 'form' variable 
    
          ```<form >
          {{ form.csrf_token }}
          {{ form.email.label}}<br>{{ form.email(size=20) }}<br>
          {{ form.password.label }}<br>{{ form.password(size=20) }}<br>
          {{ form.submit }}
          </form>``` 

#### Accessing Form Data


**HTTP Methods: To establish communication between the user and the servers, Flask routing decorator uses 
methods=[‘GET’,’POST’]. _GET_ methods are by default in @app.route(). For security reasons we do _POST_ requests when the
user  provides information to the server.**


**Challenge 4:  Add method POST**

  * Add method POST to @app.route(‘/login’)
       

     ```
        @app.route("/login", methods=['GET','POST'])
        def login():`

  * Add method POST to form tag in login.html file
      ```<form method="post">```

  
_**The Form is working but is not completely functional, the form still doesn't know if  the email input is really an
email (text contains @), the user password is visible in the entry field, the data in both fields are not required and
there is no minimum and maximum amount of characters for the password.**_


**Challenge 5: Add validators to the web-form to ensure that the entered data is correct**

   * Replace the [fields with the object class variables associated with the field](https://wtforms.readthedocs.io/en/2.3.x/fields/#wtforms.fields.StringField).
      
    
    ```from wtforms import SubmitField, PasswordField,EmailField```

   * Install [wtforms.validators](https://flask-wtf.readthedocs.io/en/1.0.x/quickstart/#validating-forms) library and give it an optional validator to ensure that the field is not submitted empty 
      and a length for the password.
   `from wtforms.validators import DataRequired, Length`

   * Make the necessary changes in LoginForm class
    

    ` class LoginForm(FlaskForm):
         email = EmailField(label='email', validators=[DataRequired()])
         password = PasswordField(label='password',
                                 validators=[DataRequired(),
                                 Length(min=8, max=20)])
         submit = SubmitField(label='Log')`


  - RUN the code to verify if the data entries are valid.

**Challenge 6: Print user email in the console.**

  * Establish a condition in the login function that if the data from the 'form'  is [validate_on_submit()](https://flask-wtf.readthedocs.io/en/0.15.x/quickstart/).printed
    the user email in the console. 

      
     ```
        @app.route("/login", methods=['GET','POST'])
        def login():
            form = LoginForm()
            if form.validate_on_submit():
                 print(form.email.data)

            return render_template('login.html',form=form)```

  * Add to  `<form>` the data from must be given action with an endpoint to redirect the data to the login function using Jinja2 {{ url_for() }}
    
    ` <form method="post" action="{{url_for('login')}}">`

   - RUN the code to verify if the user email is printed in the console.


**Now that we know that the LoginForm is receiving the data**


**Challenge 7: If user enter the predetermined email and password open “success.html”, else to “denied.html”**

   * If email: “admin@test.com” and password:”12345678”. Render 'success.html'.

   * Otherwise, render “denied.html” 

    `@app.route("/login", methods=['GET','POST'])
     def login():
            form = LoginForm()
            if form.validate_on_submit():
                if form.email.data == 'admin@email.com' 
                                       and form.password.data == '12345678':                
                return render_template('success.html')
                else:
                      return render_template('denied.html')
            return render_template('login.html',form=form)`

  
   - RUN the code to verify if the right email and password open 'success.html' and e if the user entries are different 
   from the predetermined condition  in the login function the 'denied.html' opens.

**Now the form is functional but will be nice to some styles on index.html and login.html  by using Bootstrap 
(Bootstrap is a web GUI framework. When Flask_bootstrap is initialised, a base template that includes all the Bootstrap
files are available in the app. This template takes advantage of Jinja2 template inheritance).**

First we have to look at [Template Inheritance with Jinja2](https://flask.palletsprojects.com/en/2.2.x/patterns/templateinheritance/)



**_Template Inheritance_** is one of the most powerful aspects of Jinja Templating. The idea behind template inheritance is 
somewhat similar to Object-Oriented Programming. We start by creating a base template which contains the HTML skeleton 
and markers that child template can override. The markers are created using the block statement. The child templates 
uses extends statement to inherit or extends the base templates. Lets the example from the 'base.html' file:

        `<!DOCTYPE html>
        <html lang="en">
        <head>
           <meta charset="UTF-8">
           <title>{% block title%}{% endblock %}</title>
        </head>
        <body>
               {% block content%}{% endblock %}
        </body>
        </html>`


   **So you can see in 'index.html' and 'login.html' file are already configured as child templates from 'base.html' file.
     E.g. 'index.html':**

    `
    {% extends 'base.html' %}

	{% block title %}Secrets{% endblock %}

	{% block content %}

    <div class="jumbotron">
	<div class="container">
		<h1>Welcome</h1>
		<p>Are you ready to discover my secret?</p>
		<a href="{{url_for('login')}}">
		<button class="btn btn-primary btn-lg" type="submit">Login</button>
		</a>
	</div>
    </div>
 	{% endblock %}`

 
 **Challenge 8: Render the app with Bootstrap**

 **Follow  [Flask-Bootstrap documentation  instructions](https://pythonhosted.org/Flask-Bootstrap/basic-usage.html#):** 

   * Install flask_bootstrap extension and import Bootstrap.
 
   * Initialise Bootstrap in the app : Bootstrap(app)

   * Extend bootstrap to index.html and login.html

   - Refresh the app in the browser and see the change on the presentation.

**Now, everything looks good but still one extra thing to take total advantage of Flask_Bootstrap in this app.**

_**For next time  rather than type the whole code inside a `'<form>'` tag, there is a [WTForms support in Flask_Bootstrap](https://pythonhosted.org/Flask-Bootstrap/forms.html) that just
with one line of code the LoginForm is rendered in the login.html template.**_


   **Challenge 9: Uncomment all the code inside `<form> </form>` and replaced the form with ‘wtf.quick_form’**

   * Import the bootstrap/wtf.html at the top of login.html
        `{% import "bootstrap/wtf.html" as wtf %}`

   * Below to uncomment `<form></form>` tag. Type: `{{ wtf.quick_form(form) }}`

   - Refresh the browser and see the result.


   **Conclusion**


You made a Flask application that has a web-form, using the Flask-WTF extension and the WTForms library. The form has several
types of fields to receive data from the user, validate it using special WTForms validators, dependent on the entered data
the user is directed to success.html template or denied.html template, to use flask_bootstrap extension for style the webapp
and use bootstrap as support to display the LoginForm with one line of code.