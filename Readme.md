The Form.io Command Line Interface
=================================
This project is the command line interface for Form.io, which allows you to quickly bootstrap full working projects as
well as interface with the Form.io API.

Installation
-------------------
Installation is easy... Simply type the following in your command line.

```
npm install -g formio-cli
```

Commands
-------------

### Bootstrap

   ```
   formio bootstrap [GitHub Project]
   ```

   You can bootstrap any Form.io application within GitHub easily with our one line bootstrap command. First find a
   repository that you wish to bootstrap.  Here are a few...

    - https://github.com/formio/formio-app-todo
    - https://github.com/formio/formio-app-movie
    - https://github.com/formio/formio-app-formio
    - https://github.com/formio/formio-app-salesquote

   Example: If you wish to bootstrap the ToDo application, simply type the following in the command line.

   ```
   formio bootstrap formio/formio-app-todo
   ```

   This will ***download***, ***extract***, ***create***, ***configure*** and ***serve*** your application in one command!
   
### Migrate

   ```
   formio migrate <source> [<transformer>] <destination> --src-key [SOURCE_API_KEY] --dst-key [DESTINATION_API_KEY]
   ```

   The migrate command allows you to migrate submission data from one source to another using a simple command. You can either migrate data from a CSV into a form, or from a form into another form. This works by taking the data from ```<source>```, sending it through a middleware function called ```<transformer>``` (which you optionally provide) that transforms the data into the correct format, and then saving that data as a submission into the ```<destination>``` form. If you are migrating data from one form to the same form within two different projects, you do not need to use the ```<transformer>``` and your command would be as follows.
   
   ```
   formio migrate <source> <destination> --src-key [SOURCE_API_KEY] --dst-key [DESTINATION_API_KEY]
   ```
   
   As an example, if you wish to move submission data from one form in a project to your remotely deployed project. You can use the following command.
   
   ```
   formio migrate https://myproject.form.io/myform https://formio.mydomain.com/myproject/myform --src-key abc1234 --dst-key cde2468
   ```
  Where you would replace the domains of your from and to, but also need to replace the ```src-key``` and ```dst-key``` with the API Keys from the from project and API key of your destination project respectively.
  
#### Migrating from CSV
In many cases, you may wish to migrate data from a local CSV file into a project submission table. This requires the transform middleware where you will map the columns of your CSV file into the Submission data going into Form.io.

   Example: Let's suppose you have the following CSV file of data.
   
   ***import.csv***
   ```
   First Name, Last Name, Email
   Joe, Smith, joe@example.com
   Jane, Thompson, jane@example.com
   Terry, Jones, terry@example.com
   ```
   And now you wish to import all of that data into a form. You can create the transform file like the following.
   
   ***transform.js***
   ```
   var header = true;
   module.exports = function(record, next) {
     if (header) {
       // Ignore the header row.
       header = false;
       return next();
     }
     next(null, {
       data: {
         firstName: record[0],
         lastName: record[1],
         email: record[2]
       }
     });
   };
   ```
   
   This transform middleware file can be a complete Node.js middleware method and works asynchronously so if you need to perform asynchronous behavior, you can do that by only calling the ```next``` function when the record is ready.
   
   You can now migrate that data into your form with the following command.
   
   ```
   formio migrate import.csv transform.js https://myproject.form.io/myform --key [YOUR_API_KEY]
   ```  

### Deploy
    
   ```
   formio deploy [src] [dst]
   ```
   
   You can deploy a project on a paid plan on form.io to a hosted server with this command. Specify the source and destination servers and the project will be created or updated on the destination server.
   
   Examples:
   
   ```
   // A project without a server is implied from https://form.io
   formio deploy myproject http://myproject.localhost:3000
   
   // Projects can be specified with a subdomain.
   formio deploy https://myproject.form.io http://myproject.localhost:3000
   
   // Projects can also be referred to with their project id which will need to be looked up.
   formio deploy https://form.io/project/{projectId} http://localhost:3000/project/{projectId}
   ```
   
   Each server will require authentication so you will be asked twice, once for the source and once for the destination. These can also be specified with --src-username, --src-password, --dst-username, --dst-password.

### Serve

   ```
   formio serve [directory]
   ```

   This command will serve a directory (to localhost) that has already been boostrapped.

 - ###Copy

    ```
    formio copy form [src] [dest]
    ```

    This command will copy the components of a form into another form. **This will overwrite all components within the destination form if that form exists**.
    You can also chain together multiple source forms which will aggregate the components of those forms into the destination form.

    Examples:

    ```
    // Copy a form from one project to another.
    formio copy form https://myapp.form.io/myform https://myotherapp.form.io/myform

    // Aggregate multiple forms into the same form.
    formio copy form https://myapp.form.io/form1,https://myapp.form.io/form2 https://myapp.form.io/allforms
    ```
