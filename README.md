# ssl-cert-commands
ssl-cert-commands


First, let's generate a key from the PFX file; this key is later used for p12 keystore.
```
openssl pkcs12 -in example.pfx -nocerts -out example.key  
Enter Import Password:
MAC verified OK
Enter PEM pass phrase:
Verifying - Enter PEM pass phrase:
```

As shown here, you will be asked for the password of the PFX file. Later, you will be asked to enter a PEM passphase. Let's, for example, use 123456 for everything here.

The second command is almost the same, but it is about nokey and a crt this time:

openssl pkcs12 -in example.pfx -clcerts -nokeys -out example.crt  
Enter Import Password:
MAC verified OK


Now, we have a key and and a crt file. The next step is to create a truststore, like so:

keytool -import -file example.crt -alias exampleCA -keystore truststore.jks
Enter keystore password:
Re-enter new password:
Owner: CN=.....
.......
Trust this certificate? [no]:  yes
Certificate was added to keystore


As you can see here, you just import this crt file into a JKS truststore and set the password. For the question: "Do you trust this certificate?" answer "yes," so it is then added in the truststore.

If you only need a truststore, you can stop here.

The last step is to create a keystore, like so:

openssl pkcs12 -export -in example.crt -inkey example.key -certfile example.crt -name "examplecert" -out keystore.p12
Enter pass phrase for example.key:
Enter Export Password:
Verifying - Enter Export Password:


This p12 keystore is enough in many cases. However, if you still need a JKS keystore, you need one additional command:

keytool -importkeystore -srckeystore keystore.p12 -srcstoretype pkcs12 -destkeystore keystore.jks -deststoretype JKS
Importing keystore keystore.p12 to keystore.jks...
Enter destination keystore password:
Re-enter new password:
Enter source keystore password:
Entry for alias examplecert successfully imported.
Import command completed:  1 entries successfully imported, 0 entries failed or cancelled
Warning:
The JKS keystore uses a proprietary format. It is recommended to migrate to PKCS12 which is an industry standard format using "keytool -importkeystore -srckeystore keystore.jks -destkeystore keystore.jks -deststoretype pkcs12".


That is all folks! Hope this helps, and please feel free to leave any questions or comments.

ls                                                                        
example.pfx  example.key            keystore.p12
example.crt  keystore.jks           truststore.jks



