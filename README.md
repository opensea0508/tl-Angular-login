
## Goal of this project
When I started with programming (even with Angular), I noticed I had to look up a lot of things. Things like
- how to create a login page?
- how to handle authentication?
- how to fetch items from a serve?
- how to create/read/update/delete items(s)?
- routing?
- component structure?

And you can find a lot of articles about it on the web. But it was mostly fragmented. For routing I had to go article x, for authentication to article y. Nowadays I know all these things by hearth, but there are still new (Angular) developers that are facing the same problem that I once did. 

So I created this "tiny" project to demonstrate some basics design patterns.

## What's in this project?
- Bootstrap 4 (https://getbootstrap.com/)
- Fontawesome 5 (https://fontawesome.com/)
- Angular 6 (https://angular.io/)
- A basic authentication service to handle the login/logout/check authentication token
- A simple secured dashboard
- A simple login page (including error handling)
- A fake backend service to handle the api request
- A service to inject the authentication token on each request to the api endpoint
- Angular Routing system
- Some RxJS function (http://reactivex.io/) 
- Feature "Items"   : Add / Delete / Edit items using CRUD (Create Read Update Delete) methods

## Install and run this project
- Install NodeJS (https://nodejs.org)
- Install Angular CLI (https://cli.angular.io/)
- Download or clone this repository on your computer
- Open your termninal and navigate to the project root folder and enter :  ``` npm install ```
- Next to build and start the project : ``` npm start ```
- Open your browser and navigate to http://localhost:4200/

## Testing
There are 2 tests :
- Karma / Jasmine tests : ``` ng test ```
- Protractor e2e tests : ``` ng e2e ```

## Build and run production
- ``` ng build --prod ``` 
- ``` http-server ./dist/angular-dashboard-starterkit -p 8888 ``` 

The production build will replace the environment.ts (environment.prod.ts) and the fakebackend.ts (fakebackend.prod.ts)  file. 

## Build staging
For staging there are 2 flavours:
- Build without mocks: ``` ng build -c=staging ``` 
- Build with mocks: ``` ng build -c=staging-mocks``` 

The environment.ts contens file will be replaced with the environment.staging.ts file.

## Serving
- Default (with mocks) : ``` ng serve ``` 
- Without mocks : ``` ng serve --c no-mocks``` 
- Without mocks, different api url : ``` ng serve --c api``` 


## The authentication pattern
So what's the authentication pattern/lifecycle?

- Login (validate the user on the serve)
- Check the auhtentication status of the user
- Logout

Sounds easy right? But a lot goes into this logic. And a lot of this logic goes behind the scenes. Let's dive into it.

## Login flow
So for mortal people, they only see the login page, fill in the details and press submit. But what actually happens behind the screen? These are (in short) the steps/workflow:

- The application sends the login data to the server.
- Server checks if the user is valid.
- If the user is valid, the server will generate a token. This token is associated to the user, so every time an api request is made with that token, the server knows wich user it is.
- The server will return (or not) the token (and depending on your backend service also some basic information about the user)
- When the application recieves the token, the token will be locally stored and the user get's redirected to the next page (usually a home or dashboard page).
- If there is a invalid login, the application needs to handle the error (for example display an error text)

## Validate token flow
Each time you start the application, it checks if there was a token stored. The token is just a string generated by the server. This token can be anything ofcourse. So we need to check if the token is still valid. The basic flow is this:

- The application checks if a token is stored.
- If there's a token stored, a api rquest to the server is made to validate the token.
- If everything in the above steps is ok, then the application can just continue it's normal flow.
- If there was an error of any kind, the logout process is started and the user will be presented with the login screen.

If you want, you could also validate the token, each time a secured page is called by the application. Do keep in mind that this also requires new api requests to the server. For my example project, I've choosen to only validate when you start the application.

## Logout flow
When the logout process is started (started by the user or by the application), the following wil happen:

- An api request is made to the server with the stored token.
- The server can remove the token, so this token cannot be used anymore.
- The application will delete all stored data related to authentication (for example the token, user meta data).
- The application will send a signal that a logout is issued so other components/services etc can act on it.
- The user is redirected to the login page

## Angular specifics
So now we got the the basics covered. Let's see some Angular specific logic choices I made for :

- Login 
- Public and secure layout pages
- Logout
- Fake backend
- Token intercept

## Login
So I've made an authentication service to handle the login. Normally I would create a service function with a http request and subscribe to it. I think most Angular developers can relate to this. But in this example I've changed it a little bit (because I can). 


I came across this post about converting the subscribe to a promise with the help of the async-await feature : https://medium.com/@balramchavan/using-async-await-feature-in-angular-587dd56fdc77. The idea is to make the http request, but the result is then converted to a promise. The authors statement is that it's not logical to subscribe or stream when you only do the request once. The login part is a one time only. You don't login on each page. You only login again when you're logged out again. So I decided to give this method a go. The downside of this technique is you can't use the pipe() method.

Also to simulate a real backend server, I included a fake backend server. The fake backend server will catch the api login request and then will return either a valid login or a failed login (it's random). This is a way to test the server response and what the user sees when the login was failed.

## Public and secure layout pages
I wanted to have 2 completely layouts for the login page (a public page) and secured pages. In order to do that I have setup an UI module wich holds the secured template layout. The app.component.html will server either the just the public page (just normal router link) or the LayoutComponent (the secured layout page) based on the authentication status. In the routing file a secure page is marked with the canActivate key with is mapped to a guard.

## Logout
I've setup the most top level logout in the app.component.ts. For me this is the logical place because it's the most top level component. Ofcourse other components can subscribe to the logout event to do some garbage collection.

## Fake backend server
To acutally test the login/logout/validate token, I've setup a fake backend using the HttpInterceptor ( https://angular.io/api/common/http/HttpInterceptor). For those who don't know what this is, this a way to interecpt all incomming and outgoing requests and (if you want) alter the request. 

## Token intercept
So after a succesfull login, a token is generated by the server and the application will store the token locally in the browser. So now each api quest to the server must have the token to validate the user. The token is placed in the http headers (Autentication Bearer) on each request. So you could do this hard coded with each request, but this can also be automated. Using the HttpInterceptor again, we can actually listen when an api request is made to the server and then the token is automaticly added to the http headers.

## Feature "Items"
What's a secured dashboard if you don't have any content to show? In this template I've included a basic feature with Items with CRUD. Some features/implementations are:
- Create an item
- Read an item
- Update an item
- Delete an item
- Most common component structure





