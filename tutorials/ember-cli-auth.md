# Ember-CLI Authenticated app

Lets show a bit more complicated of an app that uses Ember-CLI and github authentication. A lot of this tutorial may not make sense at first, but if I've sold you on Ember I'm sure you'll read more into [Ember.js](http://emberjs.com/) and [Ember-CLI](http://www.ember-cli.com/). Much of the authentication comes from a previous tutorial I've written which you can view [here](http://www.asciifarm.com/EmberAuthTutorial/)

## Prerequisites
- [Git](http://git-scm.com/downloads)
- [Node.js](https://nodejs.org/)
- [Bower](http://bower.io/) `npm install -g bower`
- [Ember-CLI](http://www.ember-cli.com/) `npm install -g ember-cli`

## Tutorial
- Change directory to your development folder, ex `cd ~/Develop`
- Initialize a new app `ember new myWebApp`
- Change directory to your new app `cd myWebApp`
- Fix package.json (will be fixed with ember-cli 0.2.1)
```javascript
//--- /package.json
"ember-cli": "ember-cli/ember-cli",
```
- Now run `npm install` this will take a while
- Get some dependencies
```
ember install:addon ember-cli-simple-auth
ember install:addon ember-cli-simple-auth-torii
npm install --save-dev torii
```
- Setup our basic app structure
```
ember g route application
ember g route protected
ember g route login
ember g controller login
```
- Add the mixins for logging in and blocking unauthenticated users
```javascript
//--- /app/routes/application.js
import Ember from 'ember';
import ApplicationRouteMixin from 'simple-auth/mixins/application-route-mixin';

export default Ember.Route.extend(ApplicationRouteMixin, {});
```
```javascript
//--- /app/routes/protected.js
import Ember from 'ember';
import AuthenticatedRouteMixin from 'simple-auth/mixins/authenticated-route-mixin';

export default Ember.Route.extend(AuthenticatedRouteMixin, {});
```
- Add login actions and setup the controller
```javascript
//--- /app/controllers/login.js
import Ember from 'ember';
import LoginControllerMixin from 'simple-auth/mixins/login-controller-mixin';

export default Ember.Controller.extend(LoginControllerMixin, {
  authenticator: 'authenticator:torii'
});
```
```javascript
//--- /app/routes/login.js
import Ember from 'ember';

export default Ember.Route.extend({
	actions: {
		githubLogin: function() {
			this.get('session').authenticate('simple-auth-authenticator:torii', 'github-oauth2');
			return;
		}
	}
});
```
- Update the templates
```html
<!-- app/templates/application.hbs -->
<h2 id="title">Welcome to Bluemix Ember-CLI Authentication Demo!</h2>

<ul>
	<li>
		{{#if session.isAuthenticated}}
			<a {{action 'invalidateSession'}}>Logout</a>
		{{else}}
			{{#link-to 'login'}}Login{{/link-to}}
		{{/if}}
	</li>
	<li>{{#link-to 'protected'}}Protected Page{{/link-to}}</li>
</ul>

{{outlet}}
```
```html
<!-- app/templates/login.hbs -->
<h3>Login Route</h3>
<button {{action 'githubLogin'}}>Login with Github</button>
```
```html
<!-- app/templates/protected.hbs -->
<h3>Protected Route</h3>

Hello Authenticated user!

<br><br>

You shouldn't see this unless logged in
```
- Setup Github OAuth. Go [here](https://github.com/settings/applications) and `Register new application`, for now use a Homepage URL and Authorization callback URL of `http://localhost:4200`
- Update config/environment.js
```js
//--- config/environment.js
ENV['torii'] = {
  providers: {
    'github-oauth2': {
      apiKey: 'Client ID from Github',
      scope: 'user',
      redirectUri: 'http://localhost:4200'
    }
  }
};
```
- Test by running `ember server`

## Deploy to bluemix
- In the bluemix web interface create a new Node.js application
- Don't initialize the app with starter code, but rather download the starter code from the CF command line interface tab.
- Copy app.js and manifest.yml to the root directory of your ember project
- Modify package.json to include express
```json
"dependencies": {
    "express": "3.4.7",
    "torii": "^0.2.3"
}
```
- Modify app.js to get rid of jade and use ember-cli's build directory
```javascript
// setup middleware
var app = express();
app.use(app.router);
app.use(express.errorHandler());
app.use(express.static(__dirname + '/dist')); //This changes to /dist where ember-cli builds to

// YOU CAN REMOVE THE BELOW LINES, left in to show difference

// app.set('view engine', 'jade');
// app.set('views', __dirname + '/views'); //optional since express defaults to CWD/views

// render index page
// app.get('/', function(req, res){
// 	res.render('index');
// });
```
- We now have to change our `Authorization callback URL` on our [Github app page](https://github.com/settings/applications/) to match the bluemix url
- We also have to update `redirectUri` to match in config/environment.js. Don't forget to add `http://` at the beginning!
- Build the project `ember build`
- Push to bluemix (you can also get the command from the CF tab in the bluemix interface)
```bash
cf push ember-cli-auth-test
```

## Notes
- Don't `ADD GIT` in the bluemix web interface, it won't work and may give you unintended results
