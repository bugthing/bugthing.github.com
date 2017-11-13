---
layout: default
title: Check those SSL certs
---

I had to update some ssl certs using a cloud service UI and cli tool. I was not sure which
SSL certs I needed, so I checked the current config using these nifty command line tools, summerised like so:


    export QDOMAIN=google.com
    echo -n | openssl s_client -connect $QDOMAIN:443  | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > $QDOMAIN.cert
    openssl x509 -in $QDOMAIN.cert -text

Download the cert from the HTTPS server
---------------------------------------

This command uses the openssl tool to contact and negotiate with the SSL server behind the domain google.com. It then outputs
keys and other information pertaining to the ssl handshake that occured. Using `sed` we extract the certificate from the output
and write it to the file `google.cert`

    echo -n | openssl s_client -connect google.com:443  | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > google.cert

This talks directly to the server and there is not much to get in the way (e.g. browser cache) so is good to rely on as the truth.

Now we have a cert for a domain, we can compare with ssl cert for different domain and see if they are the same, for example `www.google.com`

Check the cert
--------------

Now we have `.cert` file we can use the openssl tool to extract easily read information, such as expiry date.

    openssl x509 -in google.cert -text

