# API Gateway Client Certificate verify at node Service running on EC2

This Repository show how you can verify API Gateway Certificate on EC2 Instance. Kindly follow below steps,

1.] Create Client certification in API Gateway:

![APIGW](https://github.com/yash-sonani/APIGatewayClientCertificateVerifyOnEC2/blob/master/APIGW-Client%20Certificate.png)

2.] Copy certificate and make clientca.pem file.

3.] launching new Instance and Connect instance with SSH.

4.] Install your server application or follow the below to install node-JS:

```
curl --silent --location https://rpm.nodesource.com/setup_8.x | sudo bash -
sudo yum -y install nodejs

```

![Install-Node-1](https://github.com/yash-sonani/APIGatewayClientCertificateVerifyOnEC2/blob/master/Node-Instalation-1.png)

![Install-Node-2](https://github.com/yash-sonani/APIGatewayClientCertificateVerifyOnEC2/blob/master/Node-Instalation-2.png)

5.] Installation of certBot (For create cretificate):

```
wget https://dl.eff.org/certbot-auto
chmod a+x ./certbot-auto
./certbot-auto certonly --standalone -d api.test.com   [api.test.com is domain name]
sudo ./certbot-auto -d api.test.com --manual --preferred-challenges dns certonly   
```

![Install-certBot-1](https://github.com/yash-sonani/APIGatewayClientCertificateVerifyOnEC2/blob/master/certBot-Installation-1.png)

![Install-certBot-2](https://github.com/yash-sonani/APIGatewayClientCertificateVerifyOnEC2/blob/master/certBot-Installation-2.png)

![Install-certBot-3](https://github.com/yash-sonani/APIGatewayClientCertificateVerifyOnEC2/blob/master/certBot-Installation-3.png)

6.] Certificate(fullchain.pem) and key(privkey.pem) generated on path

```
/etc/letsencrypt/live/test.com/
```

7.] Create Node Service to verify the certificate:

```js
var https = require('https');
var fs = require('fs');

var options = {
  // Server SSL private key and certificate
  key: fs.readFileSync('/etc/letsencrypt/live/test.com/privkey.pem'),
  cert: fs.readFileSync('/etc/letsencrypt/live/test.com/fullchain.pem'),
  // issuer/CA certificate against which the client certificate will be
  // validated. A certificate that is not signed by a provided CA will be
  // rejected at the protocol layer.
  ca: fs.readFileSync('cert/clientca.pem'),
  // request a certificate, but don't necessarily reject connections from
  // clients providing an untrusted or no certificate. This lets us protect only
  // certain routes, or send a helpful error message to unauthenticated clients.
  requestCert: true,
  rejectUnauthorized: true
};

var a = https.createServer(options, function (req, res) {
  res.writeHead(200);
  res.end("hello world\n\nthe trust is mutual\n");
}).listen(443);
```

