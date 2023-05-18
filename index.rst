==========
Node & SQL
==========

Introduction
============

In this unit, you'll be working with JSON web tokens and localStorage 
to handle authentication and send secure requests. You'll also be 
learning more about Node and Express to create servers. The content 
will include sections about setting up APIs, what REST is, and how 
to implement middleware. To round out the backend content, you'll 
also be using Sequelize to interact with a database and bring that 
data into your project.

Concepts and Objectives
=======================

.. objectivesindex::

Project Overview
================

The app you'll be working on in this unit is called Social Mountain. It 
is a basic social media app that allows anyone to see public text posts. 
In order to create a post, users must log in. They can also see their 
own private posts when they are logged in. You'll get to practice 
authentication and build an entire server that interacts with your database.

MVP
===

Features
--------

- User can login and logout 

- User can create posts 

- User can edit and delete their own posts 

- User can view others' posts 

Code
----

- Uses React ``context``

- Uses ``JWT`` to authenticate users 

- Uses an ``epxress`` server 

- Uses ``Sequelize`` to connect to a database from the server 

- Contains models for ``Post`` and ``User`` 

- Uses middleware to authenticate protected endpoints 


.. warning:: Enroll in Courses

    **Before** you watch any content for this unit, ensure that you are 
    enrolled in the following course(s) on Udemy. Once you're enrolled, the links
    in the content sections of this page will take you to the correct sections 
    of the course. 
    
    - `One <https://specs.udemy.com/course/react-the-complete-guide-incl-redux>`_
    
    - `Two <https://specs.udemy.com/course/nodejs-the-complete-guide>`_


Authentication
==============

Objectives
----------

.. objectives::
    
    - Student can identify use cases for authentication
    
    - Student can explain how authentication works
    
    - Student can store and access information in local storage
    
    - Student can implement authentication and retrieve user information
    
    - Student can encrypt passwords

Summary
-------

Authentication is an important part of many apps. It allows us to customize 
content to different users. In these videos, you'll learn about handling 
authentication on the front end.

Content
-------

.. video:: Adding Authentication to React Apps
    :link: https://specs.udemy.com/course/react-the-complete-guide-incl-redux/learn/lecture/25889754?course_portion_id=220036&learning_path_id=3326410#overview




Project Part 1: Front-end Authentication with JWT
=================================================

Goals
-----

- Set up the Auth form 

- Set up Auth Context

Steps
-----

.. stepslist::

    .. step:: Download starter code and setup project

        .. download:: Starter
            :link: ../react-proj-4.zip

        #. Once you have download the starter code, open a terminal and ``cd`` into the starter folder. 

        #. Run ``npm i`` to install the needed packages. 



    .. step:: Auth Form Skeleton

        #. Explore the `components/Auth.js` file. This is the starting point for your app's authentication.  
        
        #. Open your app in the browser (run ``npm start`` if you haven't yet) and make sure that you're not getting any errors so far. Also submit the form to make sure that the handler function runs. 



    .. step:: Capture User Inputs

        #. Set appropriate ``type``, ``placeholder``, and ``value`` props on each ``input``. 

        #. Change the state of ``username`` when a user types in the first field. 

        #. Change the state of ``password`` when a user types in the second field.

        #. Change the state of ``register`` when a user clicks on the button at the bottom of the page. 

        #. Check on your app in the browser to make sure everything is working still (you can also add ``console.log`` s to check on your state values).

        .. togglereveal:: 

            .. code-block:: html
                    
                <input
                    type='text'
                    placeholder='username'
                    value={username}
                    onChange={e => setUsername(e.target.value)}
                    className='form-input'/>
                <input
                    type='password'
                    placeholder='password'
                    value={password}
                    onChange={e => setPassword(e.target.value)}
                    className='form-input'/>

                ... 

                <button className='form-btn'>Need to {register ? 'Login' : 'Sign Up'}?</button>
 


    .. step:: submitHandler Function

        Since you don't have a backend built out yet, you'll use a Devmountain API temporarily. This API was designed in the same way that you'll make yours later in this project. 

        The authentication API **expects to receive a username and password on the body of post requests**. It returns an object with user information and a JSON Web Token.

        The base URL is: `https://socialmtn.devmountain.com`

        #. Import ``axios`` at the top of the file. 
        
        #. In the ``submitHandler`` function, check if the user is registering or logging in. 
        
        #. If they are registering, use ``axios`` to send a ``POST`` request to the API at the ``/register`` endpoint. Don't forget to send a ``body`` object with the request.
        
        #. If they are logging in, use ``axios`` to send a ``POST`` request to the API at the ``/login`` endpoint. Don't forget to send a ``body`` object with the request.
        
        #. The ``axios`` requests will create ``promise``s which you can handle using a ``.then`` or using ``async`` & ``await``. Either way, when you get the response back from the API, ``console.log`` the data. You'll come back to this after you set up an authentication context provider. 
        
        #. You should also add a method for catching errors. There you can log errors, reset input field values, or anything else that would be helpful to you as you debug. (At the end of the project, there's also a challenge that will use this error catcher.) 

        .. togglereveal:: 

            .. code-block:: javascript 

                import axios from 'axios'

                ...   
                
                const submitHandler = e => {
                    e.preventDefault()
                
                    const body = {
                        username,
                        password
                    }
                
                    const url = 'https://socialmtn.devmountain.com'
                
                    axios.post(register ? `${url}/register` : `${url}/login`, body)
                        .then(({data}) => {
                            console.log('AFTER AUTH', data)
                        })
                        .catch(err => {
                            setPassword('')
                            setUsername('')
                        })
                }
        


    .. step::  Authentication Context Skeleton

        In this step, you should take time to read the following paragraphs as well as to explore the code given to you in `store/authContext.js`. 

        If you need a refresher on ``context``, head to the `React docs <https://reactjs.org/docs/context.html#reactcreatecontext>`_. 

        In short, context allows multiple components at different levels of our app access to the same information. For this application, we have 2 pieces of data to store and 2 functions that affect that data. As for the data, we'll need to store the user's JWT so that we can send it when we make additional requests. We'll also need their ``id`` for certain requests, so we'll store that as well. As for the functions, users will need to be able to login and logout, which will set or remove the token and id respectively. 

        Notice ``AuthContext`` near the top of the file. When you initialize that context, you have to pass in an object with the default values. These values will only be used if a ``Provider`` isn't found. That's why in the ``AuthContextProvider`` below, we are using local values for the context. This is the provider that we'll use in `index.js` to make sure that all the components in our app can access the values. 

        There are some helper functions included that should look familiar to you. They'll allow us to calculate the remaining time left on a token and access the ``localStorage`` respectively. The code at the top of the component checks for ``localStorage`` and sets it to state if available. That means that users could leave our app and come back and still be logged in until the token expires. 

        Look at the ``return`` of the ``AuthContextProvider`` component – it should return a component called ``AuthContext.Provider`` – this is available from the initial context we created earlier. By returning it from our custom component, we get all the functionality from the ``context`` API with our added state and functions. You'll also see that the returned component displays ``props.children``. This will allow the outer component, which is the one we'll use, to display whatever ``jsx`` is written inside it. We'll see in the next step that the child passed to our provider is our entire app!



    .. step:: Making Auth Context Available App-wide

        #. In `src/index.js`, import ``AuthContextProvider`` from the store. 
        
        #. Wrap your ``App`` component with the provider. (You can wrap it around ``BrowserRouter`` as well). 
        
        #. This will allow any component in your app to access the auth context with the ``useContext`` hook! 

        .. togglereveal:: 

            .. code-block:: javascript 

                import React from 'react'
                import ReactDOM from 'react-dom/client'
                import './index.css'
                import App from './App'
                import { BrowserRouter } from 'react-router-dom'
                import {AuthContextProvider} from './store/authContext'
                
                const root = ReactDOM.createRoot(document.getElementById('root'))
                
                root.render(
                <React.StrictMode>
                <AuthContextProvider>
                    <BrowserRouter>
                    <App />
                    </BrowserRouter>
                </AuthContextProvider>
                </React.StrictMode>
                )
        


    .. step:: Login using Context

        Let's think about what needs to happen when a user logs in. We'll need to store their ``token`` and ``userId``. And we'll also want to start a timer so that they're logged out when their token expires. This expiration is set by the backend and the time will be sent back with the response from the server. 
        We should also consider where this function will be called from and how it will get the data it needs. For our application, as with most, there is only one place to login from – the main authentication form. That form makes a request to the backend and then gets some information back as a response. That response data is what we want to store in our ``AuthContext``. We know (from step 4 and hopefully from your exploration) that we will get the needed information from that response. 

        This step of the project will use ``localStorage``. Head to `this link <https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage>`_ if you need a refresher on the topic. 

        #. Near the top of your `store/authContext.js` file, notice the variable named ``logoutTimer``. It will be used to keep track of how much time is left before the user's token expires. We'll change the value of this when users login and logout.
        
        #. In the ``AuthContextProvider`` component, find the function named ``login`` and change it so that it takes in a token, an expiration time, and a user id. 
        
        #. Inside the function, set the token and user id to their respective portions of state. 
        
        #. Additionally, save the token, expiration time, and the user id to ``localStorage``. 

        Next, we'll need to figure out how much time is remaining on the token.

        #. Inside the ``login`` function, create a variable called ``remainingTime`` and set it equal to the invocation of the ``calculateRemainingTime`` function passing in the expiration time that got passed into ``login``. 
        
        #. The last thing we'll do in the ``login`` function is to set the value of ``logoutTimer`` that we looked at at the beginning of this step. Go ahead and reassign the value of ``logoutTimer`` to the invocation of ``setTimeout`` (a built-in JavaScript function that expects a callback and a number of milliseconds; it will execute the callback when the provided milliseconds have passed) passing in ``logout`` and ``remainingTime``. Currently, our logout function doesn't do anything, we'll fix that in a couple of steps. 

        .. togglereveal:: 

            .. code-block:: javascript 

                let logoutTimer

                ...  
                
                const login = (token, exp, userId) => {
                setToken(token)
                setUserId(userId)
                localStorage.setItem('token', token)
                localStorage.setItem('exp', exp)
                
                const remainingTime = calculateRemainingTime(exp)
                
                logoutTimer = setTimeout(logout, remainingTime)
                }


    .. step:: Calling Login from the Auth Form

        Now that we have a way to store the user information in context, we need to actually call the ``login`` function. We'll need to set up `Auth.js` to be able to read the context we created and execute functions from it. 

        #. Open `Auth.js` and import ``useContext`` from React and the ``AuthContext`` from your store. 
        
        #. Inside the ``Auth`` component, create a variable named ``authCtx`` that stores the result of invoking ``useContext`` passing in ``AuthContext``. This will allow the ``Auth`` component to hook into the auth context!
        
        #. In the ``submitHandler`` function, after the user information comes back from the ``axios`` request, call ``authCtx.login`` passing in the token, expiration time, and user id from the response. 
        
        #. Test your code by trying to register a new user! Notice that ``login`` will get called whether a user is registering or logging in. This is convenient for users since they won't have to refill the login form immediately after registering. 

        .. togglereveal:: 

            .. code-block:: javascript 

                import {useState, useContext} from 'react'

                ...

                const authCtx = useContext(AuthContext)

                ... 

                authCtx.login(res.data.token, res.data.exp, res.data.userId)


    .. step:: Restrict Views in App.js

        Now that users have the ability to login, let's change some of our pages to only be viewable if you're logged in. 

        #. In `App.js`, import ``useContext`` from React and the ``AuthContext`` from your store. 

        #. Just like in the last step, inside the component, create a variable named ``authCtx`` that stores the result of invoking ``useContext`` passing in ``AuthContext``. This will allow the ``App`` component to hook into the auth context!

        #. In the ``Auth`` route's ``element`` prop, check if there is a ``token`` in context. If there isn't, send users to ``Auth``, if there is, use the ``Navigate`` component to send them to the home path. 

        #. Using a similar method (checking for the ``token`` and utilizing ``Navigate``), send unauthenticated users to the authentication form if they try to view the form or profile pages. 

        #. Test

        .. togglereveal:: 

            .. code-block:: javascript 

                import { useContext } from 'react'
                import { Routes, Route, Navigate } from 'react-router-dom'
                import './App.css'
                
                import Header from './components/Header'
                import Home from './components/Home'
                import Auth from './components/Auth'
                import Form from './components/Form'
                import Profile from './components/Profile'
                
                import AuthContext from './store/authContext'
                
                const App = () => {
                const authCtx = useContext(AuthContext)
                
                return (
                    <div className='app'>
                        <Header/>
                        <Routes>
                        <Route path='/' element={<Home/>}/>
                        <Route path='/auth' element={!authCtx.token ? <Auth/> : <Navigate to='/'/>}/>
                        <Route path='/form' element={authCtx.token ? <Form/> : <Navigate to='/auth'/>}/>
                        <Route path='/profile' element={authCtx.token ?<Profile/> : <Navigate to='/auth'/>}/>
                        <Route path='*' element={<Navigate to='/'/>}/>
                        </Routes>
                    </div>
                )
                }
                
                export default App
 


    .. step:: Challenge: Logout Option in Header

        Without looking at the solution code below, try to implement ``AuthContext`` in your ``Header`` component to show only the appropriate links for authenticated and unauthenticated users. Reference the previous two steps and the Udemy content to try and figure this out. Once you've given it your best shot, look at the solution below. It's fine if yours doesn't match exactly, but it should only show “Home” and “Login or Sign Up” to unauthenticated users. Authenticated users should see “Home”, “Profile”, “Add Post” and “Logout” in the header.

        .. togglereveal:: 

            .. code-block:: javascript 

                import {useContext} from 'react'
                import {Link} from 'react-router-dom'
                
                import AuthContext from '../store/authContext'
                import logo from '../assets/dm-logo-white.svg'
                
                const Header = () => {
                const authCtx = useContext(AuthContext)
                
                return (
                    <header className='header flex-row'>
                        <div className='flex-row'>
                            <img src={logo} alt='dm-logo' className='logo'/>
                            <h2>Social Mountain</h2>
                        </div>
                        <nav>
                            {
                                authCtx.token ? (
                                    <ul className='main-nav'>
                                        <li>
                                            <Link to='/'>Home</Link>
                                        </li>
                                        <li>
                                            <Link to='profile'>Profile</Link>
                                        </li>
                                        <li>
                                            <Link to='form'>Add Post</Link>
                                        </li>
                                        <li>
                                            <button className='logout-btn' onClick={() => authCtx.logout()}>Logout</button>
                                        </li>
                                    </ul>
                                ) : (
                                    <ul className='main-nav'>
                                        <li>
                                            <Link to='/'>Home</Link>
                                        </li>
                                        <li>
                                            <Link to='/auth'>Login or Sign Up</Link>
                                        </li>
                                    </ul>
                                )
                            }
                        </nav>
                    </header>
                )
                }
                
                export default Header


    .. step:: Logout Functionality

        The final step of setting up our context file is making a way for users to logout. We'll also round out the functionality of the logout timer. 

        #. In the ``logout`` function, set the token and userId stored on state to be ``null``. And remove the items we added to ``localStorage``. Our other components will be reading this information from the context, so once we remove it here, it won't be available anywhere else. 
        #. The function should also check if ``logoutTimer`` is still going, and if it is, we should call the ``clearTimeout`` function passing in the name of the timer. We have to check if the timer is still going since this function can be called when users choose to logout or when the timer runs out. We don't need to clear the timer if it's already run out. 
        #. 






Introduction to Node
====================

Objectives
----------

.. objectives::

    - Students can define what NodeJS is

    - Students can list pros and cons to NodeJS as a backend

    - Student can explain the node lifecycle and event loop

    - Student understands the request/response model in Node

    - Student understands Node Modules

Summary
-------

You scratched the surface of Node.js in Foundations - these videos will dive a 
little deeper into this amazing tool. 

Content
-------

.. video:: Introduction & Understanding the Basics 
    :link: https://specs.udemy.com/course/nodejs-the-complete-guide/learn/lecture/11561804?course_portion_id=220060&learning_path_id=3326410#overview




Working with Express
====================

Objectives
----------

.. objectives::

    - Student can write a functioning Express API

    - Student can request data from Express API

    - Student can display data from Express API

    - Student can understands and can implement middleware in Express

Summary
-------

In these videos, you'll learn more about debugging in Node and you'll learn more about Express servers. 

Content
-------

.. video:: Workflow, Debugging, & Express
    :link: https://specs.udemy.com/course/nodejs-the-complete-guide/learn/lecture/11561804?course_portion_id=220060&learning_path_id=3326410#overview



Project Part 2: Setting Up an Express Server
============================================

Goals
-----

- Set up the server directory 

- Set up the base server file 

Steps
-----

.. stepslist::
    
    .. step:: Initial Setup

        #. At the root of your project, create a folder called `server`. 
        
        #. Inside the folder, create an `index.js` file, a `controllers` folder, and a `middleware` folder. 
        
        #. At the root of your project, create a `.env` file and be sure to add it to your `.gitignore` before your next commit!
        
        #. Add ``PORT`` and ``SECRET`` variables to your `.env` - the post is where your server will run. The secret can be any value you like; it will be used to create the JWT.

    .. step:: Setup Controller Files

        First, let's set up a controller for authentication. Up until now, we've been using a Devmountain API, but eventually, we'll complete our own and it starts here!

        #. Create a file called `auth.js` in the `controllers` directory. 
        
        #. This file should export two functions - ``login`` and ``logout``, which for now can just log something to the console. 

        Now we also want to set up a posts controller - this is a social media app after all. This app will allow users to add and delete their own posts as well as change them from private to public. It also allows anyone on the website to view public posts. 

        #. Create a file called `posts.js` in the `controllers` directory.
        
        #. This file should export five functions - ``getAllPosts``, ``getCurrentUserPosts``, ``addPost``, ``editPost``, and ``deletePost``, which for now can just log something to the console. 

        .. togglereveal::

            .. code-block:: javascript
                
                // auth.js
                module.exports = {
                    register: (req, res) => {
                        console.log('register')
                    },
                    
                    login: (req, res) => {
                        console.log('login')
                    },
                }
                
                // posts.js
                module.exports = {
                    getAllPosts: (req, res) => {
                        console.log('get all posts')
                    },
                    
                    getCurrentUserPosts: (req, res) => {
                        console.log('current user posts')
                    },
                    
                    addPost: (req, res) => {
                        console.log('add post')
                    },
                    
                    editPost: (req, res) => {
                        console.log('edit post')
                    },
                    
                    deletePost: (req, res) => {
                        console.log('delete post')
                    }
                }

    .. step:: Middleware

        We will set up a middleware function that will run on every request that requires a user to be authenticated to run. If they don't pass this middleware, then they won't be able to make the request. 

        #. In the middleware folder, create a file called `isAuthenticated.js`. 
        
        #. Copy and paste the code below into your file. 
        
        #. Read through the code and **write at least 5 comments** explaining different parts of the code. 

        .. code-block:: javascript

                require('dotenv').config()
                const jwt = require('jsonwebtoken')
                const {SECRET} = process.env
                
                module.exports = {
                    isAuthenticated: (req, res, next) => {
                        const headerToken = req.get('Authorization')
                    
                        if (!headerToken) {
                            console.log('ERROR IN auth middleware')
                            res.sendStatus(401)
                        }
                    
                        let token
                    
                        try {
                            token = jwt.verify(headerToken, SECRET)
                        } catch (err) {
                            err.statusCode = 500
                            throw err
                        }
                    
                        if (!token) {
                            const error = new Error('Not authenticated.')
                            error.statusCode = 401
                            throw error
                        }
                    
                        next()
                    }
                }

    .. step:: Main Server File

        Imports
        +++++++

        Import the following:
        
        - ``dotenv`` (don't forget to call its ``config()`` method)
        
        - ``express``
        
        - ``cors``
        
        - ``PORT``
        
        - All the functions from `auth.js`
        
        - All the functions from `posts.js` 
        
        - Your middleware function 

        Setup App
        +++++++++

        Now that we are in the back end, ``app`` will refer to our server app. 
        
        #. Create a variable called ``app`` that is equal to express invoked. 
        
        #. At the bottom of the file, call ``app.listen`` passing in your port number and a callback that logs a success message to the console. 

        Endpoints
        +++++++++
        
        Recall that you created 7 functions in your controller folder. We'll need an endpoint for each of those functions. 
        
        - A ``post`` endpoint for registering at `/register`
        
        - A ``post`` endpoint for logging in at `/login`
        
        - A ``get`` endpoint for getting all the posts at `/posts`
        
        - A ``get`` endpoint for getting the current user's posts at `/userposts/:userId` 
        
        - A ``post`` endpoint for creating a post at `/posts`
        
        - A ``put`` endpoint for editing posts at `/posts/:id`
        
        - A ``delete`` endpoint for deleting posts at `/posts/:id` 

        Middleware
        ++++++++++

        We created one function that we need to run on requests that require authentication. Additionally, there are a couple of lines of code that you might have noticed missing from our server that are also middleware. 
        
        #. On the endpoints for posting, editing, or deleting a post, add the ``isAuthenticated`` middleware. 
        
        #. Above all of your endpoints, add ``app.use`` middleware for parsing your requests into JSON and using the ``cors`` package. ``app.use`` middleware will run on every endpoint defined below it in the file. 



        .. togglereveal::

            .. code-block:: javascript

                require('dotenv').config()
                
                const express = require('express')
                const cors = require('cors')
                
                const {PORT} = process.env
                const {getAllPosts, getCurrentUserPosts, addPost, editPost, deletePost} = require('./controllers/posts')
                const {register, login} = require('./controllers/auth')
                const {isAuthenticated} = require('./middleware/isAuthenticated')
                
                const app = express()
                
                app.use(express.json())
                app.use(cors())
                
                //AUTH
                app.post('/register', register)
                app.post('/login', login)
                
                // GET POSTS - no auth
                app.get('/posts', getAllPosts)
                
                // CRUD POSTS - auth required
                app.get('/userposts/:userId', getCurrentUserPosts)
                app.post('/posts', isAuthenticated, addPost)
                app.put('/posts/:id', isAuthenticated, editPost)
                app.delete('/posts/:id', isAuthenticated, deletePost)
                
                app.listen(PORT, () => console.log(`db sync successful & server running on port ${PORT}`))

    .. step:: Test

        #. Run your server using ``nodemon``.

        #. Open Postman and test your endpoints! You won't get any responses in Postman, but you should see ``console.log`` s in your terminal. 


Using Sequelize
===============

Objectives
----------

.. objectives::

    - Student can explain a relational database

    - Student can list some advantages and disadvantages of relational vs non relational databases

    - Student can create database tables

    - Student can add data to tables

    - Student can modify tables

    - Student can select data from a table that meets certain conditions

    - Student can describe and use aggregate functions

    - Student can select data from multiple tables using JOIN statements

Summary
-------

Sequelize not only provides a way for us to connect with databases, it's 
also a fully functional Object Relational Mapper (ORM). It allows us to 
use JavaScript to interact with our databases. In these videos, you'll 
review SQL concepts and learn about many of the helpful methods that 
Sequelize offers us. 

Content
-------

.. video:: SQL Introduction
    :link: https://specs.udemy.com/course/nodejs-the-complete-guide/learn/lecture/11738940?course_portion_id=220064&learning_path_id=3326410#overview

.. video:: Understanding Sequelize
    :link: https://specs.udemy.com/course/nodejs-the-complete-guide/learn/lecture/11738996?course_portion_id=220066&learning_path_id=3326410#overview





Project Part 3: Creating a Database
===================================

Goals
-----

- Set up a database using Sequelize

Steps
-----

.. stepslist::
    
    .. step:: Create a Database on Heroku

        #. Head to your Heroku account and create a database. 

        #. Copy the ``URI`` and add it to your `.env` file as ``CONNECTION_STRING``. 


    .. step:: Setup Sequelize Connection

        #. In your `server` folder, create a folder called `util` and in that folder, a file called `database.js` - this is where we'll connect to the database with Sequelize. 
        
        #. Import and configure ``dotenv`` at the top of the file. 
        
        #. Additionally, import the ``CONNECTION_STRING`` from your `.env` and the ``Sequelize`` class, which is the default export from the package. 
        
        #. Create a ``new`` instance of the ``Sequelize`` class, passing in the database connection string and a configuration object. You can call the instance ``sequelize`` with a lowercase “s”.
        
        #. The configuration object should have a ``dialect`` property whose value is “postgres” and a ``dialectOptions`` property whose value is an object. That object should contain a property called ``ssl`` whose value is also an object (so we've got an object in an object in an object). The innermost object should have a ``rejectUnauthorized`` property whose value is ``false``. This will make working with the connection in development easier. 
        
        #. Don't forget to export ``sequelize``. 

        .. togglereveal::

            .. code-block:: javascript

                require('dotenv').config()
                const {CONNECTION_STRING} = process.env
                const Sequelize = require('sequelize')
                
                const sequelize = new Sequelize(CONNECTION_STRING, {
                    dialect: 'postgres',
                    dialectOptions: {
                        ssl: {
                            rejectUnauthorized: false
                        }
                    }
                })
                
                module.exports = {
                    sequelize
                }


    .. step:: Create a User Model

        #. In your `server` folder, create a `models` folder and in that folder, create a `user.js` file. 
        
        #. The `user.js` file is where you'll define what your user objects should look like. Because we're using Sequelize, this is how we'll be defining our tables. Sequelize will take our models and set up the database for us. 
        
        #. In `user.js`, import ``DataTypes`` from sequelize. 
        
        #. Import your Sequelize connection, ``sequelize``, from the `util/database` file. 
        
        #. Set up a ``module.exports`` object with one property: ``User``. In the value of this property, we will call a function from Sequelize to set up what the user objects should look like. 
        
        #. The value of ``User`` should be set to ``sequelize.define`` passing in the string “user”, which is the name of the table, and an object with three properties: ``id``, ``username``, and ``hashedPass``. 
        
        #. The value of ``id`` should be a configuration object with ``type``, ``autoIncrement``, ``allowNull``, and ``primaryKey`` properties. The value of ``type`` should be ``DataTypes.INTEGER`` and the other three should be boolean values. Determine those values based on what you know about ids in databases. 
        
        #. The ``username`` and ``hashedPass`` properties should both be strings. Don't forget to use the Sequelize ``DataTypes`` to define those.

        .. togglereveal::

            .. code-block:: javascript

                const {DataTypes} = require('sequelize')
                
                const {sequelize} = require('../util/database')
                
                module.exports = {
                    User : sequelize.define('user', {
                        id: {
                            type: DataTypes.INTEGER,
                            autoIncrement: true,
                            allowNull: false,
                            primaryKey: true
                        },
                        username: DataTypes.STRING,
                        hashedPass: DataTypes.STRING
                    })
                }

    .. step:: Create a Post Model

            Using your ``User`` model as a guide, create a ``Post`` model in your `post.js` file. Make sure to include the following properties on your object:
            
            - ``id``
            
            - ``title``
            
            - ``content``
            
            - ``privateStatus``

        .. togglereveal::

            .. code-block:: javascript
                
                const {DataTypes} = require('sequelize')
                
                const {sequelize} = require('../util/database')
                
                module.exports = {
                    Post : sequelize.define('post', {
                        id: {
                            type: DataTypes.INTEGER,
                            autoIncrement: true,
                            allowNull: false,
                            primaryKey: true
                        },
                        title: DataTypes.STRING,
                        content: DataTypes.TEXT,
                        privateStatus: DataTypes.BOOLEAN
                    })
                }
 
    .. step:: Connect to the Database

        Now that we have the outline for the database, we want to actually have our server connect to it when the server starts up. We'll also set up the relationship between our ``User`` and ``Post`` models in this step. 

        #. In `index.js` in your server, import ``sequelize`` from `util/database` and each of your models from their respective files. 
        
        #. Somewhere above your endpoints and below the imports, let's set up the relationships for ``User`` and ``Post``. In our application, users can post as much as they would like and each post only has one author. Using Sequelize, we can define those relationships with the ``.hasMany`` and ``.belongsTo`` methods. 
        
        #. Now, at the bottom of your file, we'll use the ``.sync`` method to connect to our database. Call ``sequelize.sync``, which is an asynchronous function, so you'll want to follow that up with a ``.then``. Pass the ``.then`` a callback function and move your ``app.listen`` line inside that callback. This will make it so when you run your file with ``nodemon``, it will sync up with the database before the server starts up. 
        
        #. You can test this by running ``nodemon`` and then opening up SQL Tabs, Postico, or a similar app and connecting to your database. You can then view what tables are available - there won't be any data yet though. 

        .. togglereveal::

            .. code-block:: javascript
                
                const {sequelize} = require('./util/database')
                const {User} = require('./models/user')
                const {Post} = require('./models/post')
                
                ...
                
                User.hasMany(Post)
                Post.belongsTo(User)
                
                ...
                
                // the force: true is for development -- it DROPS tables!!!
                // you can use it if you like while you are building 
                // sequelize.sync({ force: true })
                sequelize.sync()
                .then(() => {
                    app.listen(PORT, () => console.log(`db sync successful & server running on port ${PORT}`))
                })
                .catch(err => console.log(err))
 



REST
====

Objectives
----------

.. objectives::

    - Student can define RESTful in the context of APIs

    - Student can knows what CRUD stands for

    - Student knows the four primary HTTP methods

    - Student can explain middleware

    - Student can build a server that listens for requests

    - Student can create endpoints that listen for the four main HTTP methods

Summary
-------

In these videos, you'll learn more about what makes an API RESTful and how to build one.

Content
-------

.. video:: Working with REST APIs - The Basics
    :link: https://specs.udemy.com/course/nodejs-the-complete-guide/learn/lecture/12087602?course_portion_id=220068&learning_path_id=3326410#overview

.. video:: Working with REST APIs - The Practical Application
    :link: https://specs.udemy.com/course/nodejs-the-complete-guide/learn/lecture/12097852?course_portion_id=220070&learning_path_id=3326410#overview




Project Part 4: Building Out Endpoint Methods
=============================================

Goals
-----

Add functionality to all of the authentication and post methods in the server 

Steps
-----

.. stepslist::
    
    .. step:: Register

        Right now, our authentication functions just log strings to the console. We are going to change these functions to use JSON web tokens and Sequelize methods to authenticate users.

        #. In `controllers/auth.js`, import and configure ``dotenv`` and bring in the ``SECRET`` variable from the `.env` file. 
        
        #. We're also going to need to import the ``User`` model, the ``bcryptjs`` package, and the ``jsonwebtoken`` package. 
        
        #. Before we dive into ``register``, create a function outside of the ``module.exports`` called ``createToken`` that takes in a ``username`` and an ``id``. 
        
        #. The ``createToken`` function should return ``jwt.sign`` passing in an object with ``username`` and ``id`` as the payload, the ``SECRET`` as the secretOrPrivateKey, and an options object that has an ``expiresIn`` property that's set to “2 days”. Reference this `README <https://github.com/auth0/node-jsonwebtoken#jwtsignpayload-secretorprivatekey-options-callback>`_ if you're having trouble with the ``.sign`` method. 

        In the ``register`` function, we are going to check to see if a user already exists with the username being sent. If they do, we'll send a message that we can't create the user. If there isn't anyone with that name yet, then we'll store the user in our database, create a token for them, and send the username, their id, their token, and its expiration back to the front end.

        #. Now in your ``register`` function, let's delete the ``console.log`` and replace it with a try catch. 
        
        #. Before we move on with the functionality, add a ``console.log`` to the catch block so that you can catch errors. 
        
        #. We're going to use the ``async/await`` keywords in these functions, go ahead and add the ``async`` keyword now to the beginning of your function definition, before the parameters. This will allow us to perform asynchronous actions in the function using the ``await`` keyword. 
        
        #. In the try block, destructure ``username`` and ``password`` off of ``req.body`` - this will make them easier to access for the rest of the function. 
        
        #. Then create a variable called ``foundUser`` that's set equal to ``await User.findOne({where: {username: username})`` which is using the asynchronous ``findOne`` method that's built into our Sequelize ``User`` model. We then are passing it an object that adds a ``WHERE`` clause to our query and looks for usernames matching the one coming from ``req.body``. 
        
        #. Next, write an ``if/else`` that checks if ``foundUser`` is true. If it is, that means we already have a user with that name in the database, so we'll want to send a 400 error and a message back to the front. 
        
        #. If it isn't true, then we'll do all the work of making a user! First, create a salt and a hashed password using methods from ``bcrypt``:

            .. code-block:: javascript
                
                const salt = bcrypt.genSaltSync(10)
                const hash = bcrypt.hashSync(password, salt)
        
        #. Next, create a new user using the ``.create`` method on our ``User`` model. The ``create`` method is asynchronous and expects an object with the properties defined on the model. So, create a variable called ``newUser`` that is equal to ``await User.create({username: username, hashedPass: hash})``. 
        
        #. Then, make a variable called ``token`` that is equal to the invocation of the ``createToken`` method, passing in ``newUser.dataValues.username`` and ``newUser.dataValues.id``. You can see the ``dataValues`` by console.logging ``newUser``. 
        
        #. We'll also need to make our own expiration time since the JWT ``sign`` method only returns the actual token. You can do that with this line: ``const exp = Date.now() + 1000 * 60 * 60 * 48``. This uses the JavaScript Date object and adds a number of milliseconds to it to equal two days, which is the expiry we assigned to the token earlier. 
        
        #. Finally, we can send an object to the front end using ``res.send``. The object should have four properties:
        ``username`` whose value should be ``newUser.dataValues.username``
        ``userId`` whose value should be ``newUser.dataValues.id``
        ``token`` whose value should be the ``token`` variable you created 
        ``exp`` whose value should be the ``exp`` variable you created

        .. note:: Confused? 

            If this is a little overwhelming, go back and read through your code line by line and explain out loud what is happening with each part. 

        .. togglereveal::

            .. code-block:: javascript

                register: async (req, res) => {
                    try {
                        const {username, password} = req.body
                        let foundUser = await User.findOne({where: {username}})
                        if (foundUser) {
                            res.status(400).send('cannot create user')
                        } else {
                            const salt = bcrypt.genSaltSync(10)
                            const hash = bcrypt.hashSync(password, salt)
                            const newUser = await User.create({username, hashedPass: hash})
                            const token = createToken(newUser.dataValues.username, newUser.dataValues.id)
                            console.log('TOOOOOOKEN', token)
                            const exp = Date.now() + 1000 * 60 * 60 * 48
                            res.status(200).send({
                                username: newUser.dataValues.username,
                                userId: newUser.dataValues.id,
                                token,
                                exp})
                        }
                    } catch (error) {
                        console.log('ERROR IN register')
                        console.log(error)
                        res.sendStatus(400)
                    }
                },


    .. step:: Login

        #. Set up the skeleton of your ``login`` function similarly to how we set up ``register``. Make it async, add a try catch, log something to the console in the ``catch`` block, destructure ``username`` and ``password``, create a ``foundUser`` variable, and an ``if/else`` that checks if ``foundUser`` is truthy. 
        
        #. This time though, since we are trying to log someone in, all the functionality will go in the ``if`` portion. First, we'll check to see if the username and password match what's stored in the database. We can do that with this line: ``const isAuthenticated = bcrypt.compareSync(password, foundUser.hashedPass)``. 
        
        #. Now we'll need an inner ``if/else`` that checks if ``isAuthenticated`` is truthy. 
        
        #. If it is, create a ``token`` and ``exp`` variables like we did in ``register`` and then send an object on the response with the same properties as in ``register``. 
        
        #. In both the inner and outer ``else`` blocks, send a message that says `cannot log in` or something similar.
        
        #. Test your login and register! (Make sure you're running both ``nodemon`` and ``npm start``).

        .. togglereveal::

            .. code-block:: javascript

                login: async (req, res) => {
                    try {
                        const {username, password} = req.body
                        let foundUser = await User.findOne({where: {username}})
                        if (foundUser) {
                            const isAuthenticated = bcrypt.compareSync(password, foundUser.hashedPass)
                
                            if (isAuthenticated) {
                                const token = createToken(foundUser.dataValues.username, foundUser.dataValues.id)
                                const exp = Date.now() + 1000 * 60 * 60 * 48
                                res.status(200).send({
                                    username: foundUser.dataValues.username,
                                    userId: foundUser.dataValues.id,
                                    token,
                                    exp
                                })
                            } else {
                                res.status(400).send('cannot log in')
                            }
                
                        } else {
                            res.status(400).send('cannot log in')
                        }
                    } catch (error) {
                        console.log('ERROR IN register')
                        console.log(error)
                        res.sendStatus(400)
                    }
                },

    .. step:: Add a Post

        Let's start off our post controller with adding a post. This will look similar to our register and login functions. 

        #. First, import your Post and User models at the top of the file.
        
        #. Make the ``addPost`` function ``async`` and add a ``try-catch`` statement inside.
        
        #. In the ``try`` block, destructure ``title``, ``content``, ``status``, and ``userId`` from ``req.body``.
        
        #. We'll be using the Sequelize ``create`` method again, but this time we don't need to save it to a variable; so, you can start your line off with ``await`` directly. Then call ``Post.create`` passing in an object with ``title``, ``content``, ``privateStatus``, and ``userId`` properties whose values are their corresponding destructured values from ``req.body``. 
        
        #. Last, send a 200 status back to the front end.
        
        #. In the ``catch`` block, you can log errors and send a 400 response back to the front end.

        Now, let's get the front end to work with this. The code has been provided for you. 

        #. Head to the `src/components/Form.js` file and delete all the code above line 6, uncomment the rest. 
        
        #. You can now test the form if you have ``nodemon`` and ``npm start`` running. However, right now, we won't be able to see posts anywhere on the front end, which we'll fix in the next step.

        .. togglereveal::

            .. code-block:: javascript

                addPost: async (req, res) => {
                    try {
                        const {title, content, status, userId} = req.body
                        await Post.create({title, content, privateStatus: status, userId})
                        res.sendStatus(200)
                    } catch (error) {
                        console.log('ERROR IN getCurrentUserPosts')
                        console.log(error)
                        res.sendStatus(400)
                    }
                },

    .. step:: Getting Posts

        Our app has 2 different endpoints for getting posts. One of them gets all users' public posts (it filters them on the front end so that you don't see your own posts) and the other gets all of the current user's posts (so you can see your own posts in your profile). 

        Here is code that you can use for the ``getAllPosts`` function:

        .. code-block:: javascript

            getAllPosts: async (req, res) => {
            try {
                const posts = await Post.findAll({
                    where: {privateStatus: false},
                    include: [{
                        model: User,
                        required: true,
                        attributes: [`username`]
                    }]
                })
                res.status(200).send(posts)
            } catch (error) {
                console.log('ERROR IN getAllPosts')
                console.log(error)
                res.sendStatus(400)
            }
        },
        
        Study the code above and figure out how it's working. Why is there a ``where`` clause? What does ``include`` do? You could even try testing this by taking out different parts and seeing what that affects.

        #. Once you've got an idea of what's happening, try to write the ``getCurrentUserPosts`` function. Remember: we want **all** of the user's posts, but none of anyone else's. 
        
        #. Head to the front end, delete all the active code in `/src/components/Home.js' and ‘/src/components/Profile.js`, and comment in all of the code below line 16.
        
        #. Now you should be able to test! You'll notice that the “delete” and “make public/private” buttons don't work yet. Let's finish up that functionality in the next steps. 

        .. togglereveal::

            .. code-block:: javascript

                getCurrentUserPosts: async (req, res) => {
                    try {
                        const {userId} = req.params
                        const posts = await Post.findAll({
                            where: {userId: userId},
                            include: [{
                                model: User,
                                required: true,
                                attributes: [`username`]
                            }]})
                        res.status(200).send(posts)
                    } catch (error) {
                        console.log('ERROR IN getCurrentUserPosts')
                        console.log(error)
                        res.sendStatus(400)
                    }
                },


    .. step:: Edit a Post

        The only thing that we'll allow users to update about their posts is whether it's private or public. With that in mind, read through the code in `Profile.js` that makes the edit request as well as the code in `server/index.js` that sets up the endpoint. What information does it look like you'll have access to in order to complete this request? 

        #. In the ``editPost`` function in the post controller, set up the function to be ``async`` and add a ``try-catch`` statement inside. 
        
        #. Get the post's ``id`` and ``status`` and store them in variables to use in your query to the database. 
        
        #. Call Sequelize's asynchronous ``update`` method passing in two objects: the first should be an object with the column to update and the value to update it to, the second object should contain a ``where`` clause that finds only the entry with the ``id`` of the post we're editing. 
        
        #. After the update has run, send a 200 status back to the front. 
        
        #. Set up the ``catch`` block. 
        
        #. Test the button from the profile page!

        .. togglereveal::

            .. code-block:: javascript

                editPost: async (req, res) => {
                    try {
                        const {id} = req.params
                        const {status} = req.body
                        await Post.update({privateStatus: status}, {
                            where: {id: +id}
                        })
                        res.sendStatus(200)
                    } catch (error) {
                        console.log('ERROR IN getCurrentUserPosts')
                        console.log(error)
                        res.sendStatus(400)
                    }
                },


    .. step:: Delete a Post

        Follow the same process we've been using to write the ``deletePost`` function. Recall that the Sequelize method for deleting is named ``.destroy``. 

        .. togglereveal::

            .. code-block:: javascript

                deletePost: async (req, res) => {
                    try {
                        const {id} = req.params
                        await Post.destroy({where: {id: +id}})
                        res.sendStatus(200)
                    } catch (error) {
                        console.log('ERROR IN getCurrentUserPosts')
                        console.log(error)
                        res.sendStatus(400)
                    }
                }





OPTIONAL Challenge - Notifications
==================================

Goals
-----

Set up a notification for users on unsuccessful register or login 

Steps
-----

.. stepslist::
    
    .. step:: Create a Notification

        In `Auth.js`, set up a notification that will run when the ``axios`` request catches an error. You can add text to the page, use the ``alert()`` function, create your own modal, or use a package from ``npm``. 


Solution
--------

You can download the solution for this project here:

.. download:: Solution Code
   :link: ../react-proj-4-solution.zip


Survey
======

Please complete `this survey <https://www.surveymonkey.com/r/F3WM7GD>`_ 
when you have finished this unit.