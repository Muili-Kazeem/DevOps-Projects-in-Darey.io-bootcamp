# MERN STACK APPLICATION IMPLEMNETATION IN AWS

## Preparing Prerequisites
  * Set and spin up a new EC2 instance of t2.micro family with Ubuntu Server 20.04 LTS(HVM) image
  * Connected through SSH from my windows terminal into the remote AWS VM instance

## STEP 1: Backend Configuration
  * Updated and upgraded the ubuntu package manager on the VM instance using `sudo apt update` and `sudo apt upgrade`
  * Got the location of Node.js software from Ubuntu repositories with `curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -`
  * Installed NodeJS with `sudo apt-get install -y nodejs`
  * Verified the version of node and npm installed `node -v` `npm -v`
  * Created project directory called To-do and navigated into it `mkdir Todo` `cd Todo`
  * Initialized the project folder with npm `npm init`
   ![Screenshot (176)](https://user-images.githubusercontent.com/69347415/193704282-60988b6c-18a5-437d-8acd-43fd20fceb41.png)
   ![Screenshot (178)](https://user-images.githubusercontent.com/69347415/193704399-345c2b2d-77f8-41bc-aaab-660fc91056f7.png)
   ![Screenshot (181)](https://user-images.githubusercontent.com/69347415/193704417-03d0ec55-3006-45d5-9cff-d8d34245a17d.png)
   ![Screenshot (182)](https://user-images.githubusercontent.com/69347415/193704502-c5e9df55-7526-41a1-a99f-50e80a5aa042.png)
   ![Screenshot (183)](https://user-images.githubusercontent.com/69347415/193704529-f2d81a8b-e3ef-4470-992a-1d610440a82b.png)

---
  * ### INSTALL EXPRESSJS
      * Install express using npm `npm install express`
      * Created an index.js file `touch index.js`
      * Installed dotenv module `npm install dotenv`
      * opened index.js with vim editor `vim index.js`
      * populated it with the server code seen below
         ```
         const express = require('express');
         require('dotenv').config();

         const app = express();

         const port = process.env.PORT || 5000;

         app.use((req, res, next) => {
         res.header("Access-Control-Allow-Origin", "\*");
         res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept");
         next();
         });

         app.use((req, res, next) => {
         res.send('Welcome to Express');
         });

         app.listen(port, () => {
         console.log(`Server running on port ${port}`)
         });
         ```
       * ran the index.js script with node `node index.js`
       * Served the server and created an inbound rule to open TCP port 5000 on the VM instance to allow inbound traffic
       * Accessed the server's public IP address on my web browser
       * Back to my window terminal that SSH into the VM instance, created and navigated into a routes directory `mkdir routes` `cd routes`
       * created an api.js script and opened it in vim editor `touch api.js` `vim api.js`
       * Populated the script with the code below
          ```
          const express = require ('express');
          const router = express.Router();

          router.get('/todos', (req, res, next) => {

          });

          router.post('/todos', (req, res, next) => {

          });

          router.delete('/todos/:id', (req, res, next) => {

          })

          module.exports = router;
          ```
         ![Screenshot (184)](https://user-images.githubusercontent.com/69347415/193721673-267a481c-1a89-4888-9904-da7e5bc9c923.png)
![Screenshot (185)](https://user-images.githubusercontent.com/69347415/193721698-97b1cfa4-4612-4b4d-bc36-0cc8659e5924.png)
![Screenshot (186)](https://user-images.githubusercontent.com/69347415/193721730-f464e299-0694-49c2-9a77-1c949b879a38.png)
![Screenshot (188)](https://user-images.githubusercontent.com/69347415/193721779-ced60acb-c1ec-4ee1-8aea-353d74a9a9fb.png)
![Screenshot (189)](https://user-images.githubusercontent.com/69347415/193721847-2479f297-f281-4692-947f-93a8a5701ed5.png)

   * ### MODELS
       * Installed Mongoose `npm install mongoose`
       * Created and navigated into a models directory `mkdir models && cd models`
       * Created a todo.js script inside the models directory and opened it with vim `touch todo.js && vim todo.js`
       * input the code below in todo.js script
         ```
         const mongoose = require('mongoose');
         const Schema = mongoose.Schema;

         //create schema for todo
         const TodoSchema = new Schema({
         action: {
         type: String,
         required: [true, 'The todo text field is required']
         }
         })

         //create model for todo
         const Todo = mongoose.model('todo', TodoSchema);

         module.exports = Todo;
         ```
      *  Navigated to Routes directory and opened api.js with vim and populated it with the script below
        ```
        const express = require ('express');
        const router = express.Router();
        const Todo = require('./models/todo');

        router.get('/todos', (req, res, next) => {

        //this will return all the data, exposing only the id and action field to the client
        Todo.find({}, 'action')
        .then(data => res.json(data))
        .catch(next)
        });

        router.post('/todos', (req, res, next) => {
        if(req.body.action){
        Todo.create(req.body)
        .then(data => res.json(data))
        .catch(next)
        }else {
        res.json({
        error: "The input field is empty"
        })
        }
        });

        router.delete('/todos/:id', (req, res, next) => {
        Todo.findOneAndDelete({"_id": req.params.id})
        .then(data => res.json(data))
        .catch(next)
        })

        module.exports = router;
        ```
        ![Screenshot (190)](https://user-images.githubusercontent.com/69347415/193722137-5bcfca3d-6f6c-463b-95ce-a913d63076c6.png)
        ![Screenshot (191)](https://user-images.githubusercontent.com/69347415/193722217-32103b66-74ac-4ee3-8f5b-657882d4703c.png)
        ![Screenshot (192)](https://user-images.githubusercontent.com/69347415/193722314-60e72f66-7225-4887-88af-232e918ad25f.png)
        ![Screenshot (194)](https://user-images.githubusercontent.com/69347415/193722817-dd6087c2-da0f-4b4c-94a8-6e3a9e95c994.png)

        
  * ### MONGODB DATABASE
  * Heads up that I faced a lot of blockers in this section. A whole lot of blockers but they will be evident in the screenshots previewed below
      * Signed up for a MongoDB account and created a cluster of database with AWS as the cloud provider
      * In the Todo directory, I created and opened a .env file `touch .env && vim .env`
      * Assigned the connection string of my database to a DB variable and exited the .env file
      * Opened the index.js file, erased the script and updated it with the code below
          ```
           const express = require('express');
           const bodyParser = require('body-parser');
           const mongoose = require('mongoose');
           const routes = require('./routes/api');
           const path = require('path');
           require('dotenv').config();

           const app = express();

           const port = process.env.PORT || 5000;

           //connect to the database
           mongoose.connect(process.env.DB, { useNewUrlParser: true, useUnifiedTopology: true })
           .then(() => console.log(`Database connected successfully`))
           .catch(err => console.log(err));

           //since mongoose promise is depreciated, we overide it with node's promise
           mongoose.Promise = global.Promise;

           app.use((req, res, next) => {
           res.header("Access-Control-Allow-Origin", "\*");
           res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept");
           next();
           });

           app.use(bodyParser.json());

           app.use('/api', routes);
           app.use((err, req, res, next) => {
           console.log(err);
           next();
           });

           app.listen(port, () => {
           console.log(`Server running on port ${port}`)
           });
          ```
       * Started the server and opened the server on my web browser `node index.js`
       * Installed Postman and tested for the APIs created in the api.js file
