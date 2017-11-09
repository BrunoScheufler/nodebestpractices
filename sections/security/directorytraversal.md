# Securing your web application against directory traversal attacks

<br/><br/>


### One Paragraph Explainer

Directory traversal attacks (also known as path traversal attacks) aim to access files and directories stored outside of the accessible area. When building a web application and serving static files or interacting with user submitted file paths you have to make sure that requests reaching out of your intended file-system boundaries are rejected. Frameworks like express and the built-in static middleware are already configured to reject requests reaching out of the configured boundaries and can be used as an easy option to protect your application against this attack.

<br/><br/>


### Code Example – vulnerable Node.js http file serving

```javascript
const http = require('http');
const url = require('url');
const fs = require('fs');
const path = require('path');
const server = http.createServer();

server.on('request', (request, response) => {
    // some parsing for the given route
    const REQUEST_URL = url.parse(request.url, true);
    const ROUTE = REQUEST_URL.pathname.split('/');

    switch (ROUTE.splice(0, 2).pop()) {
        case 'assets':
            /*
                this is kept as simple as possible but Node.js will open a ReadableStream
                for the given route starting in the assets folder as the base directory
                returning any file for the given path - also files outside of the assets directory
                in the case of a user simply putting the 
            */
            response.writeHead(200);
            fs.createReadStream(path.join(__dirname, 'assets', ...ROUTE)).pipe(response);
            break;
        default:
            response.writeHead(404);
            response.end();
        break;
    }
});
 
<br/><br/>
