# Getting Started w/ Express.js and Typescript

A simple hello world application using express.js with typescript meant to serve as a simple introduction to express.js.

## Table of Contents

 * Project Strcuture
 * Installing Dependancies  
 * Typescript Configuration    
 * Running the application

 If your are [`cloning`](https://github.com/crispycret/express-example) this repo from github the only thing you need to do is run `npm install` to install the dependancies and make sure you have `ts-node-dev` installed globally and start the application.
 
#### Install dependancies
 ```
 git clone https://github.com/crispycret/express-example.git
 cd express-example
 npm install
 npm install -g ts-node-dev
 ```

#### Start Application
 ```
 npm run dev
 # or
 npm run start
 ```

 `npm run dev` uses `ts-node-dev` and implements hot-reloading when detecting changes but has some limitations.
 `npm run start` classically compiles the typescript into javascript and runs the application and should be used if `npm run dev` is not working properly.


## Project Structure

The first thing we need to do is create the project stucture and initialize the project as a node application.

```
myapp/
├── build/ (generated)
│   └── server.js (generated)
├── src/
│   └──server.ts
├── package.json
├── tsconfig.json
```

Create the project directory.

```
mkdir myapp
cd myapp
mkdir src
```

Initialize the project directory as a node application.

```
npm init -y
```

This creates a `package.json` file, which is used to define dependancies, run scripts, identify the entry point of the package and more. The 'package.json file is considered the heart of a node application. The `-y` flag skips the initial `package.json` configuration and is optional.




## Installing Dependancies

Next install express.

```
npm i express
```

Afterwards, we can install the development dependecies that will allow the use of Typescript.

```
npm i -D typescript @types/node @types/express
```

The packages `@types/node` and `@types/express` give static typings for the node and express libraries.



## Typescript Configuration

Now that we have all the dependacies needed for the express application we can configure typescript to work for the application.
We have already installed type dependencies for node and express and now need to create a configuration file for typescript.
The easiest way to do this is with the `npx` tool.

```
npx tsc --init
```

This will generate a `tsconf.json` file that needs two modifications.

These modifications are to specify the entry point of the code and the output directory to store the compiled javascript.

Open `tsconfig.json`, uncomment and change the folowing lines to look like this.

```
"outDir": "./build",    /* Specify an output folder for all emitted files. */
"rootDir": "./src",     /* Specify the root folder within your source files. */
```

The application now supports typescript.



  
## Running the application


We are almost ready to run our express application, we just need to write some code and make a few changes to the `package.json` file

Let's start by creating our program entry file `src/server.ts` and writing a `hello world` application using `express`.

```
cd src
touch server.ts
```


This is what the `server.ts` file looks like.

```
import express, { Application, Request, Response } from 'express';
const app: Application = express();
const PORT: number = 3001;

app.use('/', (req: Request, res: Response): void => {
    res.send('Hello world!');
});

app.listen(PORT, (): void => {
    console.log('SERVER IS UP ON PORT:', PORT);
});
```


Now that we have some code written let's run the application.

Navigate back to the root directory of the project.

```
cd ..
```

Since we are using typescript we need to make a couple of changes and we will be good to go.

Inside the `package.json` file we need to change the entry point to our program.
By default the entry point will be to `index.js` but we need to change this to `src/server.ts`.

```
{
  ...
  "main": "src/server.ts",
  ...
}
```

Now we can build the application by compiling the typescript into javascript.

```
npx tsc
```

This commands compiles our project into the javascript `build/server.js`.

Now lets run the express app.

```
node ./build/server.js
```

Visit [http://localhost:3001/](http://localhost:3001/) in your browser to see the application working.


## Adding Scripts to package.json

While this is now working, the last act of compiling and running the application can become tedious.
Lets use the `package.json` scripts to make this process faster by adding the following lines.

```
  ...
  "scripts": {
    "start": "npx tsc && node ./build/server.js",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  ...
```

Now we can just use the command `npm run start` to replace using `npx tsc` and `node ./build/server.js`.

There is another change can make to speed up the development process and that is to enable hot-reloading to the degree that when changes are detected in the code the project is automatically re-compilied and started. [See this stackoverflow post for more info](https://stackoverflow.com/questions/37979489/how-to-watch-and-reload-ts-node-when-typescript-files-change).

A simple way to recompile and start a node application when changes are made is to use `ts-node-dev`.

Install `ts-node-dev` globally.

```
npm i -g ts-node-dev
```

Now instead of using `npm run start` to run the application you can use the command `ts-node-dev src/server.ts`.
It is also possible to create a package.json script to create a shorthand for this as well, the only caviate is that `ts-node-dev` has some limtations so it's best not to replace not a good idea to replace `npx tsc && node ./build/server.js` as the `npm run start` command.
Instead we can create another script command named `dev` or any other name you want that will be mapped to the `ts-node-dev` command.
This allows faster development faster using `npm run dev` but keeps `npm run start` around as a backup command if you find any troubles with `npm run dev`.
```
{
  "name": "myapp",
  "version": "1.0.0",
  "description": "",
  "main": "src/server.ts",
  "scripts": {
    "start": "npx tsc && node ./build/server.js",
    "dev": "ts-node-dev src/server.ts",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC",
  "dependencies": {
    "express": "^4.18.2"
  },
  "devDependencies": {
    "@types/express": "^4.17.17",
    "@types/node": "^18.14.6",
    "typescript": "^4.9.5"
  }
}
```