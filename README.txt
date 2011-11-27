This an example using picketlink to provide Single Sign One for two applications.


***************************** Example Components ***********************************

picket-idp: WAR application that acts as a Identity Provider. Must be deployed in a JBoss AS with the follows requirements:

 * use ports-default
 
 * profile lib with the follow libs:
    picketlink-bindings-2.0.1.final.jar
    picketlink-bindings-jboss-2.0.1.final.jar
    picketlink-fed-2.0.1.final.jar
    picketlink-trust-jbossws-2.0.1.final.jar
 
 * include the follow application policy on conf/login-config.xml:
   <application-policy name="picket-idp">
    <authentication>
      <login-module code="org.jboss.security.auth.spi.UsersRolesLoginModule"
        flag="required">
        <module-option name="usersProperties">props/idp-users.properties</module-option>
        <module-option name="rolesProperties">props/idp-roles.properties</module-option>
      </login-module>
    </authentication>
  </application-policy>

 * create the conf/props/idp-users.properties:
  rafael=rafael
  chies=chies

 * create the conf/props/idp-roles.properties:
  rafael=manager
  chies=consultant




picket-sp-one: WAR application that acts as a service provider. Must be deployed in a JBoss AS with follows requirements:
 
 * use ports-01

 * profile lib with the follow libs:
    picketlink-bindings-2.0.1.final.jar
    picketlink-bindings-jboss-2.0.1.final.jar
    picketlink-fed-2.0.1.final.jar
    picketlink-trust-jbossws-2.0.1.final.jar

 * include the follow application policy on conf/login-config.xml:
  <application-policy name = "picket-sp">
    <authentication>
      <login-module code = "org.picketlink.identity.federation.bindings.jboss.auth.SAML2LoginModule"/>
    </authentication>
  </application-policy> 

picket-spw=two: other WAR application that acts as a service provider.

 * use ports-02

 * profile lib with the follow libs:
    picketlink-bindings-2.0.1.final.jar
    picketlink-bindings-jboss-2.0.1.final.jar
    picketlink-fed-2.0.1.final.jar
    picketlink-trust-jbossws-2.0.1.final.jar

 * include the follow application policy on conf/login-config.xml:
  <application-policy name = "picket-sp">
    <authentication>
      <login-module code = "org.picketlink.identity.federation.bindings.jboss.auth.SAML2LoginModule"/>
    </authentication>
  </application-policy>



************************************** How test the use case ? *******************************************

 - start all three jboss instances: picket-idp, picket-sp-one e picket-sp-two.
 - try access http://localhost:8180/picket-sp-one
 - picket-sp-one will redirect the authentication to picket-idp.
 - provide the login info: user=rafael | passwd=rafael
 - you should be authorized to access http://localhost:8180/picket-sp-one/index.html

 - now, access http://localhost:8280/picket-sp-two
 - you should be authorized to access index.html without providing login info, because the authentication has already been done with picket-sp-one app.
 - It's all done, SSO !!
 

