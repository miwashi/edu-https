# edu-https

```bash
cd ~
cd ws
rm -rf https-test #Om den finns
mkdir https-test
cd https-test
npm init -y
mkdir src
touch ./src/app.js
touch ./src/service.js
npm pkg set scripts.start="node ./src/service.js"
npm pkg set scripts.dev="node --watch ./src/service.js"
npm pkg set scripts.test="jest"
npm install express
echo "node_modules" > .gitignore
git init
git add .
git commit -m "Initial commit"
```

# Create certificates
```bash
mkdir ./certs
openssl req -nodes -new -x509 -keyout server.key -out ./certs/server.cert -days 365
```

## service.js <heredoc

```js
cat > ./src/service.js << 'EOF'
const https = require('https');
const fs = require('fs');
const app = require('./app');

const options = {
    key: fs.readFileSync('./certs/server.key'), // Path to your private key
    cert: fs.readFileSync('./certs/server.cert') // Path to your certificate
};

// Set the port for the server
const PORT = 4433;

// Create and start the HTTPS server
https.createServer(options, app).listen(PORT, () => {
    console.log(`HTTPS Server is running on https://localhost:${PORT}`);
});
EOF
```

## app.js <heredoc

```js
const express = require('express');
const app = express();

// Middleware for parsing JSON and handling basic requests
app.use(express.json());

// Define a simple route
app.get('/', (req, res) => {
    res.send('Hello, World!');
});

// Additional routes can go here
// Example: Another route
app.get('/api', (req, res) => {
    res.json({ message: 'Welcome to the API!' });
});

module.exports = app;
```


