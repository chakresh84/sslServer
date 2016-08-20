--To check tomcat 
https://localhost:8443/

-- To call restful service
https://localhost:8443/RestfulServer/countries

----------------------------------------------------------------------------------
--Configuration

1. Generate Keys - Run below command from command promnt. Make sure JAVA_HOME is set.

keytool -genkey -alias mycert -keyalg RSA -keystore mykeystore

When you type the command above, it will ask you some questions. First, it will ask you to create a password (My password is “password“):

loiane:bin loiane$ keytool -genkey -alias tomcat -keyalg RSA
Enter keystore password:  password
Re-enter new password: password
What is your first and last name?
  [Unknown]:  Loiane Groner
What is the name of your organizational unit?
  [Unknown]:  home
What is the name of your organization?
  [Unknown]:  home
What is the name of your City or Locality?
  [Unknown]:  Sao Paulo
What is the name of your State or Province?
  [Unknown]:  SP
What is the two-letter country code for this unit?
  [Unknown]:  BR
Is CN=Loiane Groner, OU=home, O=home, L=Sao Paulo, ST=SP, C=BR correct?
  [no]:  yes
 
Enter key password for
    (RETURN if same as keystore password):  password
Re-enter new password: password


To Verify the cert info

keytool -v -list -keystore mykeystore

When you enter below command, you will se below output. Please note that you need to same password which you have used while creation cert.

Enter keystore password:

Keystore type: JKS
Keystore provider: SUN

Your keystore contains 1 entry

Alias name: mkyong
Creation date: 20 Aug, 2016
Entry type: PrivateKeyEntry
Certificate chain length: 1
Certificate[1]:
Owner: CN=localhost, OU=test, O=test, L=test, ST=test, C=Unknown
Issuer: CN=localhost, OU=test, O=test, L=test, ST=test, C=Unknown

------------------------------------------------------------------------------------
2. Server.xml (Tomcat file)

Put config like. Please note, we need to provide the ketstore file information here

<Connector
           protocol="org.apache.coyote.http11.Http11NioProtocol"
           port="8443" maxThreads="200"
           scheme="https" secure="true" SSLEnabled="true"
           keystoreFile="G:/software/apache-tomcat-7.0.70/webapps/mykeystore" keystorePass="password"
           clientAuth="false" sslProtocol="TLS"/>

Sample file is included in the project for reference.

---------------------------------------------------------------------------------

3. Web.xml

If we enable the changes in the server.xml for ssl, user will be able to access both ( ssl and non ssl) urls.

To force your web application to work with ONLY SSL, you simply need to add the following code to your web.xml file (before web-app tag ends):

	
<security-constraint>
    <web-resource-collection>
        <web-resource-name>RestfulServer</web-resource-name>
        <url-pattern>/*</url-pattern>
    </web-resource-collection>
    <user-data-constraint>
        <transport-guarantee>CONFIDENTIAL</transport-guarantee>
    </user-data-constraint>
</security-constraint>

The url pattern is set to /* so any page/resource from your application is secure (it can be only accessed with https). The transport-guarantee tag is set to CONFIDENTIAL to make sure your app will work on SSL.

If you want to turn off the SSL, you don’t need to delete the code above from web.xml, simply change CONFIDENTIAL to NONE.

------------------------------------------------------------------------------------------
