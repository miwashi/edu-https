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

Common name is the domain name and in our case it is localhost!

```bash
mkdir ./certs
openssl req -nodes -new -x509 -keyout ./certs/server.key -out ./certs/server.cert -days 365
```

## service.js <heredoc

```js
cat > ./src/service.js << 'EOF'
const https = require('https');
const fs = require('fs');
const app = require('./app');
const PORT = = process.env.PORT || 443;

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
app.get('/', (req, res) => {
    res.send('Hello, Secure World!');
});
module.exports = app;
EOF
```


