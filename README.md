# AR tourist guide
### KEA 5. semester Final Project. By Alexander Kellberg, Kristian Skovlund Rasmussen, Stefanos Agelastos.
### Exam evaluation: excellent

## About the project
### Initial concept:
As an internship project, I worked in a multidisciplinary team of 2 multimedia designers and 2 developers. We developed an AR solution for this local guided-tours company.
Our goal was to "Bring Stories Alive" as well as to add a competitive advantage to the company's SoMe presence. 
 
[![Watch the video](https://raw.githubusercontent.com/stefanosAgelastos/gps-tourist-app/master/docs/youtube.png)](https://youtu.be/Lbq94Ef9qJY?t=5).
  
### First working prototype:
Together with two colleagues we developed an augmented reality android tourist guide and a supporting administration system. 
The user harvests GPS and AR technologies through their phone and consumes content relevant to their location. 
The system administrator can add new locations and feed the application with new content.
![overview](https://raw.githubusercontent.com/stefanosAgelastos/gps-tourist-app/master/docs/overview.png) 
  
### About the process:
We combined Scrum and Design thinking. Both methodologies are iterative and incremental by nature, so they combine well:
![overview](https://raw.githubusercontent.com/stefanosAgelastos/gps-tourist-app/master/docs/process.png) 
  
## Built With
The admin webapp is based on [MERN](http://mern.io), a scaffolding tool which makes it easy to build isomorphic apps using Mongo, Express, React and NodeJS. Read the [Documentation](http://mern.io/documentation.html).
The android app was made on the [Unity](https://unity.com/) real-time 3d development platform and [Vuforia](https://www.vuforia.com/) library, which provided web, gps and augmented reality capabilitities.
Currently the solution has the following architecture:
![solution](https://raw.githubusercontent.com/stefanosAgelastos/gps-tourist-app/master/docs/solution.png) 
  
## Getting Started

These instructions will get you a copy of the project up and running on your local machine for development and testing purposes. See deployment for notes on how to deploy the project on a live system.

### Prerequisites

What things you need to install the software and how to install them
**Note : Please make sure your MongoDB is running.** For MongoDB installation guide see [this](https://docs.mongodb.org/v3.0/installation/). Also `npm6` is required to install dependencies properly.

## Available Commands

1. `npm run start` - starts the development server with hot reloading enabled

2. `npm run bs` - bundles the code and starts the production server

3. `npm run test` - start the test runner

4. `npm run watch:test` - start the test runner with watch mode

5. `npm run cover` - generates test coverage report

6. `npm run lint` - runs linter to check for lint errors

## File Structure

### Webpack Configs

MERN uses Webpack for bundling modules. There are four types of Webpack configs provided `webpack.config.dev.js` (for development), `webpack.config.prod.js` (for production), `webpack.config.server.js` (for bundling server in production) and `webpack.config.babel.js` (for [babel-plugin-webpack-loaders](https://github.com/istarkov/babel-plugin-webpack-loaders) for server rendering of assets included through webpack).

The Webpack configuration is minimal and beginner-friendly. You can customise and add more features to it for production build.

### Server

MERN uses express web framework. Our app sits in server.js where we check for NODE_ENV.

If NODE_ENV is development, we apply Webpack middlewares for bundling and Hot Module Replacement.

#### Server Side Rendering

We use React Router's match function for handling all page requests so that browser history works.

All the routes are defined in `client/routes.js`. React Router renders components according to route requested.

```js
// Server Side Rendering based on routes matched by React-router.
app.use((req, res) => {
    match({
        routes,
        location: req.url
    }, (err, redirectLocation, renderProps) => {
        if (err) {
            return res.status(500).end('Internal server error');
        }

        if (!renderProps) {
            return res.status(404).end('Not found!');
        }

        const initialState = {
            posts: [],
            post: {}
        };

        const store = configureStore(initialState);

        fetchComponentData(store.dispatch, renderProps.components, renderProps.params).then(() => {
            const initialView = renderToString(
                <Provider store = {store} >
                  <RouterContext {...renderProps}/>
                </Provider>
            );

            const finalState = store.getState();

            res.status(200).end(renderFullPage(initialView, finalState));
        }).catch(() => {
            res.end(renderFullPage('Error', {}));
        });
    });
});
```

`match` takes two parameters, first is an object that contains routes, location and history and second is a callback function which is called when routes have been matched to a location.

If there's an error in matching we return 500 status code, if no matches are found we return 404 status code. If a match is found then, we need to create a new Redux Store instance.

**Note:** A new Redux Store has populated afresh on every request.

`fetchComponentData` is the essential function. It takes three params: first is a dispatch function of Redux store, the second is an array of components that should be rendered in current route and third is the route params. `fetchComponentData` collects all the needs (need is an array of actions that are required to be dispatched before rendering the component) of components in the current route. It returns a promise when all the required actions are dispatched. We render the page and send data to the client for client-side rendering in `window.__INITIAL_STATE__`.

### Client

Client directory contains all the shared components, routes, modules.

#### components
This folder contains all the common components which are used throughout the project.

#### index.js
Index.js simply does client side rendering using the data provided from `window.__INITIAL_STATE__`.

#### modules
Modules are the way of organising different domain-specific modules in the project. A typical module contains the following
```
.
└── Post
    ├── __tests__                    // all the tests for this module goes here
    |   ├── components               // Sub components of this module
    |   |   ├── Post.spec.js
    |   |   ├── PostList.spec.js
    |   |   ├── PostItem.spec.js
    |   |   └── PostImage.spec.js
    |   ├── pages
    |   |   ├── PostPage.spec.js
    |   |   └── PostViewPage.spec.js
    |   ├── PostReducer.spec.js
    |   └── PostActions.spec.js
    ├── components                   // Sub components of this module
    |   ├── Post.js
    |   ├── PostList.js
    |   ├── PostItem.js
    |   └── PostImage.js
    ├── pages                        // React Router Pages from this module
    |   ├── PostPage
    |   |   ├── PostPage.js
    |   |   └── PostPage.css
    |   └── PostViewPage
    |       ├── PostViewPage.js
    |       └── PostViewPage.css
    ├── PostReducer.js
    └── PostActions.js
```

## Misc

### Importing Assets
Assets can be kept where you want and can be imported into your js files or css files. Those fill be served by webpack in development mode and copied to the dist folder during production.

### ES6 support
We use babel to transpile code in both server and client with `stage-0` plugin. So, you can use both ES6 and experimental ES7 features.

### Docker
There are docker configurations for both development and production.

To run docker for development:
```sh
docker-compose build # re-run after changing dependencies
docker-compose up
```
or, if you want to override the web port:
```sh
WEB_PORT=<your_custom_port> docker-compose up
```

To run docker for production:
```sh
docker-compose -f docker-compose-production.yml up --build
```

To reset the database:
```sh
docker-compose down --volumes
```

### Make your MERN
In this version, we enabled the `mern-cli` to clone not only this project but also the variants of `mern-starter` like one project with MaterialUI or JWT auth. To make your version of MERN, follow these steps

1. Clone this project
    ```sh
    git clone https://github.com/Hashnode/mern-starter
    ```

2. Make your changes. Add a package, add authentication, modify the file structure, replace Redux with MobX or anything else.

3. In this version, we also added code generators. Blueprints for those generators are located at `config/blueprints`, and config is located at `mern.json`. Make sure to edit them if necessary after your made modifications in the previous step. There is a section below which explains how to modify generators.

4. Next clone `mern-cli` project
    ```sh
    git clone https://github.com/Hashnode/mern-cli
    ```

5. Add your project details to `variants.json` in the cloned project and send a pull request.

### Modifying Generators

#### mern.json
It contains a blueprints array. Each object in it is the config for a generator. A blueprint config contains the name, description, usage, and files array. An example blueprint config
```json
{
  "name": "dumb-s",
  "description": "Generates a dumb react component in shared components",
  "usage": "dumb-s [component-name]",
  "files": [
    {
      "blueprint-path": "config/blueprints/dumb-component.ejs",
      "target-path": "client/components/<%= helpers.capitalize(name) %>.js"
    }
  ]
}
```

A file object contains

1. `blueprint-path` - location of the blueprint file

2. `target-path` - location where the file should be generated

3. `parent-path` - optional parameter, used if you want to generate the file inside an already existing folder in your project.

Also, `target-path` supports [ejs](https://github.com/mde/ejs) and the following variables will be passed while rendering,

1. `name` - `<component-name>` input from user

2. `parent` - in particular special cases where you need to generate files inside an already existing folder, you can obtain this parent variable from the user. A config using that will look like,
    ```json
    {
      "name": "dumb-m",
      "description": "Generates a dumb react component in a module directory",
      "usage": "dumb-m <module-name>/<component-name>",
      "files": [
        {
          "blueprint-path": "config/blueprints/dumb-component.ejs",
          "parent-path": "client/modules/<%= helpers.capitalize(parent) %>",
          "target-path": "components/<%= helpers.capitalize(name) %>/<%= helpers.capitalize(name) %>.js"
        }
      ]
    }
    ```
    Here, notice the usage. In `<module-name>/<component-name>`, `<module-name>` will be passed as `parent` and `<component-name>` will be passed as `<name>`.

3. `helpers` - an helper object is passed which include common utility functions. For now, it contains `capitalize`. If you want to add more, send a PR to [mern-cli](https://github.com/Hashnode/mern-cli).

#### Blueprint files
Blueprints are basically [ejs](https://github.com/mde/ejs) templates which are rendered with the same three variables (`name`, optional `parent` and `helpers` object) as above.

### Caveats

#### FOUC (Flash of Unstyled Content)
To make the hot reloading of CSS work, we are not extracting CSS in development. Ideally, during server rendering, we will be extracting CSS, and we will get a .css file, and we can use it in the html template. That's what we are doing in production.

In development, after all scripts get loaded, react loads the CSS as BLOBs. That's why there is a second of FOUC in development.

#### Client and Server Markup Mismatch
This warning is visible only on development and totally harmless. This occurs to hash difference in `react-router`. To solve it, react router docs asks you to use `match` function. If we use `match`, `react-hot-reloader` stops working.

## License
MERN is released under the [MIT License](http://www.opensource.org/licenses/MIT).
