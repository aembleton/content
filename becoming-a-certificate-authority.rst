Becoming a Certificate Authority.
#################################
:date: 2009-01-07 05:27
:author: Laurence Noton
:category: Security

A few times I have been asked to ensure the security of data being
transported within a private network. The requirement for this has
typically been a long the lines of compliancy to security standards such
as PCI DSS to simply needing to follow good practice and to minimize
risk.

Many businesses handle sensitive data and just ensuring that the
contents of this data is secure between the external client and the SSL
termination at the the firewalls doesn't mean that the data should flow
between internal servers unsecured. There is an element of risk
internally that must be managed.

There are many ways to secure communication, but the one that most
people are aware of, and visibly see, is SSL. It is worth noting that
this is quite a heavy weight solution in comparison to message level
security where individual pieces of information can be secured.

In this blog I want to talk about how to set up an internal Certificate
Authority to manage the internal security data. Why become a CA for
internal security? It's cheap, and provides the same level of required
security that a heavy weight CA can provide. Of course I wouldn't
suggest you use this CA as a signer for public facing websites.

The first thing that you need to do is ensure you have the necessary ssl
software. OpenSSL, is a free open source solution to our SSL needs. So
download that and following the installation guide provided by OpenSSL.
It's worth noting that I have only performed these steps on linux
environment.

Then run the following commands:

openssl genrsa -out internal-ca-soa.key -des 1024

more internal-ca-soa.key

You will be asked for a password and also information regarding the
attributes to be set in on the key. It's a good idea to use a broad
general name such as SOA\_Infra, as you can use this for internal
server-server as well as client-server encryption.

Create CA self-signed public certificate:

openssl req -new -key internal-ca-soa.key -x509 -days 3650 -out
internal-ca-soa.crt

openssl x509 -in internal-ca-soa.crt -noout -text

And that's is. You can now sign certificate signing requests using your
private key (internal-ca-soa.key) and self-signed cert
(internal-ca-soa.crt)

internal-ca-soa.crt can now be rolled out internally into the trusted
certificate store on your business machines. When users connect to an
internal site which supplies a certificate signed by internal-ca-soa.crt
they will know it's safe and the browser will display the pad-lock.

Next time I'll talk about how, as a client, I can become signed by this
certificate to show people I can be trusted, and of course to ensure all
communication is encrypted.
