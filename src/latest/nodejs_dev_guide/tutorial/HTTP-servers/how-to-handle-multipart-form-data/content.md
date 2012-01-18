### Handling Multi-Part Form Data

Handling form data and file uploads properly is an important and complex problem in HTTP servers.  Doing it by hand would involve parsing streaming binary data, writing it to the file system, parsing out additional form data, and several other complex concerns. Luckily, only a very few people will need to worry about it on that deep level. 

Felix Geisendorfer, one of the Node.js core committers, wrote a library called [`node-formidable`](https://github.com/felixge/node-formidable) that handles all the hard parts for you. With its friendly API, you can be parsing forms and receiving file uploads in no time.

This example is taken directly from the `node-formidable` GitHub page, with some additional explanation added.

    var formidable = require('formidable'),
    http = require('http'),
    util = require('util');

    http.createServer(function(req, res) {
      
      // This if statement is here to catch form submissions, 
      // and initiate multipart form data parsing.     
      if (req.url == '/upload' && req.method.toLowerCase() == 'post') {
      
        // Instantiate a new formidable form for processing.
        var form = new formidable.IncomingForm();
        
        // form.parse() analyzes the incoming stream data, 
        // picking apart the different fields and files for you.
        form.parse(req, function(err, fields, files) {
          if (err) {
            // Check for and handle any errors here.
            console.error(err.message);
            return;
          }

          res.writeHead(200, {'content-type': 'text/plain'});
          res.write('received upload:\n\n');
                    
          // This last line responds to the form submission 
          // with a list of the parsed data and files.
          res.end(util.inspect({fields: fields, files: files}));
        });

        return;
      }

      // If this is a regular request, and not a form submission, then send the form
      res.writeHead(200, {'content-type': 'text/html'});
      res.end(
        '<form action="/upload" enctype="multipart/form-data" method="post">'+
        '<input type="text" name="title"><br>'+
        '<input type="file" name="upload" multiple="multiple"><br>'+
        '<input type="submit" value="Upload">'+
        '</form>'
      );
    }).listen(8080);

Using `node-formidable` is definitely the simplest solution, and it is a battle-hardened, production-ready library. Let userland solve problems like this for you, so that you can get back to writing the rest of your code!