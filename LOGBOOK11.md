# Public-Key Infrastructure LAB

## Setup

Na fase inicial, adicionamos novas entradas aos hosts conhecidos pela máquina virtual e executamos os containers fornecidos no lab:

Deste formato:

```bash
sudo nano etc/hosts     # '10.9.0.80 www.bank32.com'

$ dcbuild             
$ dcup                  
```

## Task 1 -  Becoming a Certificate Authority (CA)

Copiamos o arquivo de configuração padrão de certificados (encontrado em /usr/lib/ssl/openssl.cnf) para o nosso diretório de trabalho. Prosseguimos com uma série de comandos para estabelecer o ambiente necessário para nossa CA pessoal:

```bash
mkdir ca
cd ca
mkdir certs
mkdir crl
mkdir newcerts
touch index.txt
echo "1000" >> serial
```

Depois fizemos setup da CA:

```bash
openssl req -x509 -newkey rsa:4096 -sha256 -days 3650 -keyout ca.key -out ca.crt
```

Durante o processo colocamos os seguintes dados:

```note
- passphrase (test)
- nome do país (PT)
- região (Maia)
- cidade (Maia)
- organização (FSI)
- secção (FEUP)
- nome (T04G07)
- email (test@fe.up.pt)
```

Para ver o conteúdo dos ficheiros gerados, decodificamos o certificado X509 e a chave RSA:

```bash
openssl x509 -in ca.crt -text -noout
openssl rsa -in ca.key -text -noout
```

Comprovamos que é self signed, uma vez que o campo <b>issuer</b> e <b>subject</b> são iguais.
```
Version: 3 (0x2)
Serial Number:
    ae:09:b9:46:7b:ff:3d:72:79:6a:0d:65:79:21:8f:af
Signature Algorithm: sha256WithRSAEncryption
Issuer: C = PT, ST = Maia, L = Maia, 0 = FSI, OU = FEUP, CN = T04G07, emailAddress: test@fe.up.pt.com
Validity
    Not Before: Dec 4 09:13:42 2022 GMT
    Not After : Dec 2 09:13:42 2032 GMT
Subject: C = PT, ST = Maia, L = Maia, 0 = FSI, OU = FEUP, CN = T04G07, emailAddress: test@fe.up.pt.com
Subject Public Key Info:
    Public Key Algorithm: rsaEncryption
```

No conteúdo do ficheiro gerado referente à criptografia conseguimos identificar o seguinte:
- os dois números primos (campo `prime1` e `prime2`);
- o *modulus* (campo `modulus`);
- os expoentes públicos e privados (campo `publicExponent` e `privateExponent`, respetivamente);
- o coeficiente (campo `coeficient`);

## Task 2 - Generating a Certificate Request for Your Web Server

Criamos a partir do seguinte comando certificado para o site `www.bank32.com`:

```bash
openssl req -newkey rsa:2048 -sha256 -keyout server.key -out server.csr -subj "/CN=www.bank32.com/O=Bank32 Inc./C=US"  passout pass:test -addext "subjectAltName = DNS:www.bank32.com, DNS:www.bank32A.com, DNS:www.bank32A.com"
```

Com isto obtivemos dois ficheiros: o RSA do site e o Certificado do site.


## Task 3 - Generating a Certificate for your server

De modo a criar um certificado para o nosso servidor www.bank32.com, executamos o seguinte comando:

```bash
$ openssl ca -config openssl.cnf -policy policy_anything -md sha256 -days 3650 -in server.csr -out server.crt -batch -cert ca.crt -keyfile ca.key
```

A identidade do nosso servidor é de facto um certificado para o www.bank32.com.

E agora, ao executar este código...

```bash
$ openssl x509 -in server.crt -text -noout
```

...de modo a vertificar que, ao descomentarmos a linha "copy_extensions = copy" do nosso ficheiro, mostra que o certificado cobre todos os nomes especificados que foram mencionados na tarefa anterior.


## Task 4 - Deploying Certificate in an Apache-Based HTTPS Website

Copiamos os ficheiros "server.ctf" e "server.key" para a pasta partilhada `/volumes` e mudamos os nomes para bank32, com a respetiva extensão. Modificamos o ficheiro "etc/apache2/sites-available/bank32_apache_ssl.conf" dentro do container, para que o certificado e chave usados sejam os da pasta partilhada:

```note
<VirtualHost *:443> 
    DocumentRoot /var/www/bank32
    ServerName www.bank32.com
    ServerAlias www.bank32A.com
    ServerAlias www.bank32B.com
    ServerAlias www.bank32W.com
    DirectoryIndex index.html
    SSLEngine On 
    SSLCertificateFile /volumes/bank32.crt
    SSLCertificateKeyFile /volumes/bank32.key
</VirtualHost>
```

Para iniciar o servidor Apache foi necessário primeiro abrir uma shell no container e inserir os seguintes comandos:

```bash
$ service apache2 start
```

Acessamos ao website https://bank32.com e notamos que a conexão era insegura (não criptografada). Então, para garantir a segurança da nossa conexão, incluímos o certificado CA que produzimos nas autoridades reconhecidas pelo navegador. Isso foi feito acessando about:preferences#privacy, selecionando Certificados -> Visualizar Certificados -> Autoridades -> Importar. Após este processo, constatamos que a conexão se tornou segura.

## Task 5 - Launching a Man-In-The-Middle Attack

A configuração do servidor foi modificada para agora apresentar o site `www.example.com` com as configurações anteriores. O ficheiro "etc/apache2/sites-available/bank32_apache_ssl.conf" ficou da seguinte forma:

```note
<VirtualHost *:443> 
    DocumentRoot /var/www/bank32
    ServerName www.bank32.com
    ServerAlias www.bank32A.com
    ServerAlias www.bank32B.com
    ServerAlias www.bank32W.com
    DirectoryIndex index.html
    SSLEngine On 
    SSLCertificateFile /volumes/bank32.crt
    SSLCertificateKeyFile /volumes/bank32.key
</VirtualHost>

<VirtualHost *:80> 
    DocumentRoot /var/www/bank32
    ServerName www.example.com
    DirectoryIndex index_red.html
</VirtualHost>
```

Alteramos igualmente o DNS do alvo, associando o hostname www.example.com ao IP do servidor web malicioso.

Ao reconstruír o servidor e ir ao site `www.example.com` verificamos que o browser alerta para um potencial risco.

Esta situação ocorre devido à discrepância no certificado utilizado, pois o nome de domínio não corresponde ao que está registrado no certificado do servidor.



## Task 6 - Launching a Man-In-The-Middle Attack with a Compromised CA

Considerando que o nosso CA está comprometido, este pode ser empregado na criação de certificados para um site malicioso. Neste caso, desejamos gerar um certificado para o site www.example.com, portanto, replicamos os passos da Tarefa 2:

```bash
$ openssl req -newkey rsa:2048 -sha256 -keyout example.key -out example.csr -subj "/CN=www.example.com/O=example Inc./C=US" -passout pass:test
$ openssl ca -config openssl.cnf -policy policy_anything -md sha256 -days 3650 -in example.csr -out example.crt -batch -cert ca.crt -keyfile ca.key
```

Depois modificamos o ficheiro de configuração do servidor `etc/apache2/sites-available/bank32_apache_ssl.conf` para utilizar os dois ficheiros originados: **example.csr** e **example.key**:

```note
<VirtualHost *:443> 
    DocumentRoot /var/www/bank32
    ServerName www.bank32.com
    ServerAlias www.bank32A.com
    ServerAlias www.bank32B.com
    ServerAlias www.bank32W.com
    DirectoryIndex index.html
    SSLEngine On 
    SSLCertificateFile /volumes/example.crt
    SSLCertificateKeyFile /volumes/example.key
</VirtualHost>

<VirtualHost *:80> 
    DocumentRoot /var/www/bank32
    ServerName www.bank32.com
    DirectoryIndex index_red.html
</VirtualHost>
```

Após reiniciar o servidor e ir a www.example.com, confirmámos que ligação já é segura.

