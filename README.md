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
npm pkg set scripts.start="concurrently \"npm run server1\" \"npm run server2\""
npm pkg set scripts.server1="node ./src/service.js"
npm pkg set scripts.server2="node ./src/secure_service.js"
npm pkg set scripts.server1:watch="node --watch ./src/service.js"
npm pkg set scripts.server2:watch="node --watch ./src/secure_service.js"
npm pkg set scripts.dev="concurrently \"npm run server1:watch\" \"npm run server2:watch\""
npm pkg set scripts.test="jest"
npm install express
npm install --save-dev concurrently
echo "node_modules" > .gitignore
git init
git add .
git commit -m "Initial commit"
```

# Create certificates

Common name is the domain name and in our case it is localhost!

```bash
mkdir ./certs
openssl req -nodes -new -x509 -keyout ./certs/server.key -out ./certs/server.cert -days 365

# If you don't want to answer questions
openssl req \
  -x509 -nodes -days 365 \        # Make a self-signed cert (valid 1 year)
  -newkey rsa:2048 \              # Generate new 2048-bit RSA key
  -keyout server.key \            # Save private key to server.key
  -out server.cert \              # Output cert to server.cert
  -subj "/C=SE/ST=Stockholm/L=Stockholm/O=Jensen/OU=YH/CN=localhost/emailAddress=owner@example.com"
```

## service.js <heredoc

```js
cat > ./src/service.js << 'EOF'
const https = require('https');
const fs = require('fs');
const app = require('./app');
const PORT = process.env.PORT || 443;

const tlsOptions = {
    key: fs.readFileSync('./certs/server.key'),
    cert: fs.readFileSync('./certs/server.cert')
};

https.createServer(tlsOptions, app).listen(PORT, () => {
    console.log(`HTTPS Server is running on https://localhost:${PORT}`);
});
EOF
```

## secure_service.js <heredoc

```js
cat > ./src/service.js << 'EOF'
const https = require('https');
const fs = require('fs');
const app = require('./app');
const PORT = process.env.PORT || 443;

const tlsOptions = {
    key: fs.readFileSync('./certs/server.key'),
    cert: fs.readFileSync('./certs/server.cert')
};

https.createServer(tlsOptions, app).listen(PORT, () => {
    console.log(`HTTPS Server is running on https://localhost:${PORT}`);
});
EOF
```

## app.js <heredoc

```js
cat > ./src/app.js << 'EOF'
const express = require('express');
const app = express();
app.use(express.json());
/*
app.get('*', (req, res, next) => {
    if (!req.secure) {
        const securePort = process.env.SECURE_PORT || 443;
        const host = req.hostname;
        const url = `https://${host}:${securePort}${req.originalUrl}`;
        console.log(`Redirecting to: ${url}`);
        res.redirect(url);
    }
    next();
});
*/
app.get('/', (req, res) => {
    if (req.secure) {
        res.send('Hello, Secure World!');
    } else {
        res.send('Hello, Insecure World!');
    }
});
module.exports = app;

EOF
```


