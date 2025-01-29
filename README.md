<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Root CA, Sub CA, and Web Server Setup</title>
    <style>
        body { font-family: Arial, sans-serif; margin: 20px; padding: 20px; background-color: #f4f4f4; }
        h1, h2 { color: #333; }
        pre { background: #fff; padding: 10px; border-radius: 5px; overflow-x: auto; }
        code { color: #d63384; }
    </style>
</head>
<body>
    <h1>Root CA, Sub CA, and Web Server Setup</h1>

    <h2>Machine 1: Root CA</h2>
    <pre>
1. Install OpenSSL:
   <code>sudo apt install openssl</code>

2. Set Hostname:
   <code>sudo hostnamectl set-hostname rootca.shuharilab.com</code>

3. Edit /etc/hosts:
   <code>
   127.0.1.1 rootca.shuharilab.com rootca
   192.168.80.110 rootca.shuharilab.com rootca
   192.168.80.132 subca.shuharilab.com subca
   192.168.80.131 www.shuharilab.com www
   </code>

4. Create CA Directories:
   <code>mkdir -p /home/shuhari/ca/{certs,crl,newcerts,private,subca/csr,subca/certs}</code>

5. Assign Permissions:
   <code>chmod 700 /home/shuhari/ca/private</code>

6. Create necessary files:
   <code>
   touch index.txt index.txt.attr
   sudo echo 1000 | sudo tee serial
   sudo echo 1000 | sudo tee crlnumber
   </code>

7. Configure rootca.cnf and Generate Root CA Certificate:
   <code>
   sudo nano /home/shuhari/ca/rootca.cnf
   openssl genrsa -aes256 -out private/ca.key.pem 4096
   chmod 400 private/ca.key.pem
   openssl req -config rootca.cnf -key private/ca.key.pem -new -x509 -days 7300 -sha256 -extensions v3_ca -out certs/ca.cert.pem
   sudo chmod 444 certs/ca.cert.pem
   </code>
    </pre>

    <h2>Machine 2: Sub CA</h2>
    <pre>
1. Install OpenSSL:
   <code>sudo apt install openssl -y</code>

2. Create Sub CA Directories:
   <code>sudo mkdir -p /home/shuhari/subca/{certs,crl,newcerts,private,csr}</code>

3. Assign Permissions:
   <code>sudo chmod 700 /home/shuhari/subca/private</code>

4. Create necessary files:
   <code>
   sudo touch index.txt index.txt.attr
   sudo echo 1000 |sudo tee serial
   sudo echo 1000 |sudo tee crlnumber
   </code>

5. Configure subca.cnf and Generate Sub CA Certificate:
   <code>
   sudo nano subca.cnf
   sudo openssl genrsa -aes256 -out private/subca.key.pem 4096
   sudo chmod 400 private/subca.key.pem
   sudo openssl req -config subca.cnf -new -sha256 -key private/subca.key.pem -out csr/subca.csr.pem
   </code>
    </pre>

    <h2>Machine 3: Web Server</h2>
    <pre>
1. Install OpenSSL and Apache2:
   <code>sudo apt install openssl apache2 -y</code>

2. Create SSL Directory:
   <code>sudo mkdir -p /etc/apache2/ssl</code>

3. Assign Permissions:
   <code>sudo chmod 700 /etc/apache2/ssl</code>

4. Generate SSL Keys and CSR:
   <code>
   sudo openssl genrsa -out /etc/apache2/ssl/www.shuhari.local.key.pem 2048
   sudo openssl req -new -sha256 -key /etc/apache2/ssl/www.shuhari.local.key.pem -out /etc/apache2/ssl/www.shuhari.local.csr.pem
   </code>

5. Enable SSL Module in Apache:
   <code>sudo a2enmod ssl</code>
    </pre>
</body>
</html>
