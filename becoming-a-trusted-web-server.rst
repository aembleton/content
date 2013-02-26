Becoming a trusted web server.
##############################
:date: 2009-02-23 14:05
:author: Laurence Noton
:category: Security

My last security post was about how to become a Certificate Authority.
Now I want to describe how to set up secure communication to an Apache
web server.

The first thing you need to have done is ensure Apache was installed
with the correct SSL options:

-  –enable-proxy=shared : builds apache with the capability of acting as
   a reverse proxy and load balancer
-  --enable-ssl --with-ssl=/usr/local/ssl/install/openssl : builds
   apache with the SSL module, note you must have OpenSSL already
   installed

.. raw:: html

   </p>

Then install the latest Java version, the following commands were tested
with Java 1.6, check your install with:

java -version

Ok, after all that is set up the following steps need to be followed:

Create keystore for server (i.e myserver)

keytool -genkeypair -alias myserver\_key -keysize 1024 -keystore
myserver.jks -storepass progress -keypass progress

keytool -list -keystore myserver.jks -storepass progress

You now have a keystore for the server, the next step is to create a
Certificate Signing Request (csr):

keytool -certreq -alias myserver\_key -keypass progress -keystore
myserver.jks -storepass progress -file myserver.csr

more myserver.csr

Once you have the CSR you want this to be signed by your Certificate
Authority. Send you CSR to your CA asking them to sign it:

openssl x509 -req -in myserver.csr -CA internal-ca-soa.crt -CAkey
internal-ca-soa.key -out myserver.crt -days 356 -CAcreateserial
-CAserial internal-ca-soa.seq

more myserver.crt

Next up is to import the signed certificate and the public certificate
of the Certificate Authority back into your servers keystore.

keytool -importcert -alias internal-ca-soa\_crt -keypass progress -file
internal-ca-soa.crt -keystore myserver.jks -storepass progress

keytool -importcert -alias myserver\_key -keypass progress -file
myserver.crt -keystore myserver.jks -storepass progress

keytool -list -keystore myserver.jks -storepass progress

This is the main part now done. You technically have a signed
certificate. If you want to setup SSL in the likes of Tomcat then your
ready to follow their guidelines, as Tomcat will read directly from the
Java Keystore. However, this setup is not typical as you normally have
more than one appserver on a box with Apache acting as a reverse proxy.
Therefore it makes much more sense to install the SSL setup in Apache.

Once this has all been done we now need to export the certificate and
private key and convert into a format for Apache.

Extract Public Key

keytool -export -alias myserver\_key -keystore myserver.jks -file
exported-myserver.crt

openssl x509 -noout -text -in exported-myserver.crt -inform der

Convert Public Key into PEM format

openssl x509 -out exported-myserver-pem.crt -outform pem -in
exported-myserver.crt -inform der

Next up is to export the private key from the keystore. Java provides no
support for this so I'm making use of a Java library provided by
anandsekar
(http://www.anandsekar.com/wp-content/uploads/2006/01/ExportPrivateKey.zip)
- Big thumbs up for the people behind this nice little bit of code.

java -jar ExportPrivateKey.zip myserver.jks JKS progress myserver\_key
exported-myserver-pkcs8.key

openssl pkcs8 -inform PEM -nocrypt -in exported-myserver-pkcs8.key -out
exported-myserver.key

We now have the public certificate (exported-myserver-pem.crt) and
private key (exported-myserver.key) signed by our CA. These can be
combined, but I don't see the need for this really. We are now ready to
configure Apache!

Uncomment the following line in the httpd.conf:

Include conf/extra/httpd-ssl.conf

Change the following lines in the httpd-ssl.conf

SSLCertificateFile "/apps/sslenv/exported-myserver-pem.crt"

SSLCertificateKeyFile "/apps/sslenv/exported-myserver.key"

Restart Apache, and now if you go to any URL proxied by Apache using
HTTPS it will be secure. In this configuration you will still be able to
access the resources via straight HTTP. To force an HTTPS transport for
a URL requires a little more configuration which I will document later.
