# **[Node.js](https://nodejs.org/en/)**

- ### Node.js is a javaScript runtime, which uses '**_V8_**' engine based on C++ to compile and execute js files right on your computer or server.

### Installing Node

    Download and install node.js from the official node.js website.

- For Running Js file in node js use
  ```javascript
    $   node filename
  ```

### Global Context Object

- similar to 'window' in browser except there's no window here
- Has no DOM support
  ```javascript
  console.log(global);
  ```

### Getting current dir

- \_\_dirname
  ```javascript
  console.log(__dirname); //Prints path of working directory
  ```
- \_\_filename
  ```javascript
  console.log(__filename); //Prints path of working file
  ```

### Importing and exporting modules

- To **Import**

  - 1. Single Value
       ```javascript
       const varName = require("./filename");
       ```
    - Accessing Values
      ```javascript
      console.log(varName);
      ```
  - 2. Multiple Value

       ```javascript
       const { property1, property2 } = require("./filename");

       //Property name should be same as property exported
       ```

    - Accessing Values
      ```javascript
      console.log(property1, property2);
      ```

- To **Export**

  - 1. Single Value

       ```javascript
       // at the end of file

       module.exports = value-to-export;
       ```

  - 2. Multiple Values

       ```javascript
       // at the end of file

       module.exports = {
         property1: property1,
         property2, //If property name and prperty value are same then only property name can be written.
       };
       ```

### Built in modules

- **_os_** module
  - Contains info about current operating system
  ```javascript
  const os = require("os");
  console.log(os); //shows the properties of module
  console.log(os.platform());
  ```
- **_fs_** module (File System)

  - Helps to read, write or delete file using node.

  ```javascript
  const fs = require("fs");
  ```

  - Reading file
    ```javascript
    //This is an asynchronous fn (has a callback function)
    fs.readFile('./[path-to-file]/filename.txt', (err, data) => {
        if(err){
            console.log(err);
        }
        console.log(data.toString());
    };
    ```
  - Writing file

    ```javascript
    //This is an asynchronous fn (has a callback function)
    fs.writeFile('./[path-to-file]/filename.txt', 'new file content', () => {
        console.log('File was written!');
    };
    ```

    - It just creates a new file if the specified file doesn't exist.

  - Directories

    - Helps to create/delete directories

    ```javascript
    //To check if the folder already exists or not

    if (!fs.existsSync("./[path]/foldername")) {
      //This is an asynchronous fn (has a callback function)
      // To create a new folder
      fs.mkdir("./[path]/foldername", (err) => {
        if (err) {
          console.log(err);
        }
        console.log("Folder created!");
      });
    } else {
      fs.rmdir("./[path]/foldername", (err) => {
        if (err) {
          console.log(err);
        }
        console.log("Folder Deleted!");
      });
    }
    ```

  - Delete File
    - Helps in deleting files
    ```javascript
    if (fs.existsSync("./[path]/filename.txt")) {
      fs.unlink("./[path]/filename.txt", (err) => {
        if (err) {
          console.log(err);
        }
        console.log("File Deleted!");
      });
    }
    ```
  - **Streams & Buffer**

    - Used when dealing with huge chunk of data by replacing traditional read/write functions

    - Reading stream

      ```js
      const readStream = fs.createReadStream("./[path]/filename.txt", {
        encoding: "utf-8",
      });

      // encoding 'utf-8' converts the buffer data directly to strings and hence there's  no need to use chunk.toString()

      readStream.on("data", (chunk) => {
        console.log("NEW CHUNK!");
        console.log(chunk); //prints buffer data (already coverted to readable format due to encoding)

        //console.log(chunk.toString()); //buffer data to strings
      });
      ```

    - Write stream

      ```js
      const writeStream = fs.createWriteStream("./[path]/filename.txt");
      writeStream.write(" Your text goes here!!! "); //writes the text to the file
      ```

      - Sending from **readable** to **writable**

        - Method 1

          ```js
          const readStream = fs.createReadStream("./[path]/filename.txt", {
            encoding: "utf-8",
          });

          const writeStream = fs.createWriteStream("./[path]/filename.txt");

          readStream.on("data", (chunk) => {
            console.log("NEW CHUNK!");
            console.log(chunk); //prints buffer data (already coverted to readable format due to encoding)

            //Writing 'chunk' data to file
            writeStream.write("\n---New Chunk---");
            writeStream.write(chunk);
          });
          ```

        - Method 2
          - Piping
            ```js
            readStream.pipe(writeStream);
            ```

  - **_http_** module

    - _Helps in creating a server_

    ```js
    const http = require("http");

    //Creating a server
    const server = http.createServer((req, res) => {
      console.log("request made");
      console.log(req);
      console.log(req.url, req.method);

      //set headers content type
      res.setHeader("Content-Type", "text/plain"); //can send 'html/text' too

      res.write("Hello Website!"); //writes data to the website
      res.end();
    });

    //Listening to the request made by server
    server.listen(3000, "localhost", () => {
      console.log("Listening for requests on port 3000");
    });
    ```

    - Now run the file and open **_localhost:3000_** in browser

### Exercise 1

- Create a server that routes through home and about section of a website and displays Error if page is not available. (Typically stored in _'server.js'_ file)

  - Importing Modules
    ```js
    const http = require("http");
    const fs = require("fs");
    ```
  - Creating a server

    ```js
    const server = http.createServer((req, res) => {
      console.log("request made");
      console.log(req.url, req.method);

      //set headers content type
      res.setHeader("Content-Type", "text/html");

      //Constructing path as per the request
      let path = "./views"; //All HTML  files were placed inside 'views' folder
      // Requesting URL from the address bar of browser
      switch (req.url) {
        case "/":
          path += "/index.html"; //If homepage -->  'index.html'
          res.statusCode = 200; //Changes the status code to Success
          break;
        case "/about":
          path += "/about.html"; //about section
          res.statusCode = 200; //Success
          break;
        // re-directing 'about-me' to 'about'
        case "/about-me":
          res.statusCode = 301; //Re-directed
          res.setHeader("Location", "/about"); //sets header location to '/about' and opens about section
          res.end();
          break;
        //For everything else show error 404
        default:
          path += "/404.html";
          res.statusCode = 404; //Not Found
          break;
      }

      //Sending an HTML file as a response
      fs.readFile(path, (err, data) => {
        if (err) {
          console.log(err);
          res.end();
        } else {
          res.write(data); //ProTip: Since we're sending only data from a single source, we can skip this line and pass the data to the res.end() as function argument
          //res.end(data);
          res.end();
        }
      });
    });
    ```

  - Listening to the request made by server
    ```js
    server.listen(3000, "localhost", () => {
      console.log("Listening for requests on port 3000");
    });
    ```

### **[NPM](https://www.npmjs.com)**

- Short for **_'Node Package Manager'_**.
- Tool to install and unistall Node packages using terminal
- **Initialisation of project**

  - A project can be initialised with the following command which creates _'package.json'_ file which has the metadata of the project like dependencies and stuff.
    ```js
    $  npm init
    ```
  - The _'package-lock.json'_ file contains the versions of installed dependencies (all the packages that are installed locally)

- **Installing Packges**

  - _Globally_
    - When installed globally, package can be used for any node project on the computer and is not limited to just current project
      ```js
      $  npm install -g packageName
      ```
    - **_'-g'_** flag here is for global
  - _Locally_

    - When installed locally, package can be used only for the project where it is installed.

      ```js
      // For Older Node.js version
      $   npm i --save packageName

      // For latest Node.js version
      $   npm i packageName
      ```

    - _Note_ : In the newer Node.js versions **'--save'** flag is **optional** as a local copy is created anyways.
    - **_' --save '_** flag here is for local
    - **_' i '_** is short for install

- **Uploading Node Projects on Github**
  - The **_'node \_ modules'_** folder which contains all the local dependencies of the project can have indefinitely large size and hence, it is included inside **_.gitignore_** file.
    - Also,the folder on local machine can be deleted ( _if not required or if it's consuming too much space_ ).
  - Here, **_'package.json'_** file of the project comes to the rescue.
    - Since, the json file contains the list of all the dependencies of project, one can simply run **_'npm install'_** command inside project folder to download the **_'node \_ modules'_** folder back with dependencies.

### [EXPRESS](https://expressjs.com/)

- Node Framework that help in managing server stuff in an elegent way.
- Makes code easier to read.
- Status code is automatically set for most of the cases.
- content-type is automatically set

#### **Creating an Express App**

- Typically the file is named as **_'app.js'_**.
* This is **Express** version of **EXERCISE 1**

* Importing express module
  ```js
  const express = require("express");
  ```
* Express app
  ```js
  const app = express();
  ```
* Listening for requests
  ```js
  app.listen(3000, () => {
    console.log("Listening for requests on port 3000");
  });
  ```
* GET Method Routes

  ```js
  app.get("/", (req, res) => {
    //res.send("<h1>Hello Express</h1>"); //To send a single thing as a response

    res.sendFile("./views/index.html", { root: __dirname }); //root of the dir should be specified
  });

  app.get("/about", (req, res) => {
    res.sendFile("./views/about.html", { root: __dirname });
  });
  ```

* Redirecting routes

  ```js
  //Redirecting '/about-us' to '/about'

  app.get("/about-us", (req, res) => {
    res.redirect("/about");
  });
  ```

* Error 404

  ```js
  app.use((req, res) => {
    res.status(404).sendFile("./views/404.html", { root: __dirname });
  });

  // The 'use'  function sends response to every single request that it encounters

  // And since  the program  executes from top to bottom this  particular 'use' function should be kept at bottom

  // Otherwise, it'll be fired even for valid URLs

  // Status code should be manually set in this case
  ```

### VIEW ENGINES

- Helps in injecting ***Dynamic*** content in a webpage
- There are many view engines. Here, we'll be using **[EJS](https://ejs.co/)** i.e. Embedded Javascript Templating.
- Ejs blends good with express

#### **Registering View Engine in Express**

```js
// Importing express module
const express = require("express");

// Creating express app
const app = express();

// Registering View Engine
app.set("view engine", "ejs");

// Note:  By default ejs engine looks for ejs files inside 'views' folder so we don't have to specify the location of the folder
// But if ejs files are in some other folder, the location should be specified as follows

// app.set('views', 'foldername');
```
