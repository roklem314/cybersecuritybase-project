# Cyber Security Base – Course Project I (F-Secure Cyber Security Base MOOC)
The project can be found here: https://github.com/roklem314/cybersecuritybase-project. 
In the beginning, clone the project to your local machine. 
This project uses "Starter Code" from here https://github.com/cybersecuritybase/cybersecuritybase-project, which is needed for course completion. 

Next, this document describes how the following flaws can be reviewed and reproduced by running the application locally.

FLAW 1: Insufficient Loggin & Monitoring

Any of the application auditable events, like successful or failed logins, have not been logged. This kind of behavior makes application monitoring and auditing very hard because there is no way to know if the site has been hacked and which are the most recent events if the attack/breach is noticed. Other non-intentional problems caused by bugs and misconfigurations are missed, and application debugging is almost impossible.

Steps to reproduce the problem:
1. When the application is running, its completion, failures, or other events can not be later recovered from logs.

How to Fix it: Spring Boot gives the option to write log files in addition to the console output. There is an option to set a logging.file.name or logging.file.path property (for example, in your application.properties). Also logging systems can have the logger levels set in the Spring Environment (for example, in application.properties) by using logging.level.<logger-name>=<level> where the level is one of TRACE, DEBUG, INFO, WARN, ERROR, FATAL, or OFF. 
Here is good examples of how this can be done: https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-logging

__________________________________________________


FLAW 2: Sensitive Data Exposure 

If the attacker re-enters a person's name on the event registration form on (form.html) it will show this person's address if that person has already registered.

Steps to reproduce problem:
1. Open the browser and go to http://localhost:XXXX, where XXXX is, for example, 8080(this depends on your local settings, but 8080 is the default).
3. Sign-in with username "ted" and password "ted" and then click the Login button.
4. In the "form" page, type in "ted" as name and "address" as address and press submit.
5. The message says, "user has been signed up to the event," and this indicating that the user has signed up to the event.
6. Then go back to address http://localhost:8080/form .
7. Insert "ted" as name and click the button submit
8. Ted's address will be shown on the message

How to Fix it: On the SignupController.java class beginning, there are two methods such as defaultMapping() and loadForm(). These methods must be added "HTTP session.invalidate();" to prevent this problem.

__________________________________________________

FLAW 3: Cross-Site Scripting(XSS)

On form.html attacker can insert malicious javascript through text fields and then gain information about users sing up informations.

Steps to reproduce problem:
1. Open browser and go to: http://localhost:8080
2. Sign in with username "ted" and password "ted" and then click Login button
3. In the form.html page type in "ted" as name and "<script>alert('error')</script>" as address and press Submit
4. Text "user has been signed up to the event" appears as a successful sign up to the event. Javascript is stored now as executable that will run when sent to the victim's browser
5. Go back to http://localhost:8080
6. If "ted" is still logged in, type in "ted" as name and anything you want as an address
7. This makes redirect to another page and browser runs the javascript that was previously stored
8. After javascript runs then message "user has already registered with address ." will be seen 

How to Fix it: All input parameter values should be validated before saving. This can be done using javascript to validate input before sending values on client side or server side inside of controller. Escaping untrusted HTTP request data based on the context in the HTML output (body, attribute, JavaScript, CSS, or URL) will resolve Reflected and Stored XSS vulnerabilities. 

__________________________________________________


FLAW 4: Broken Authentication and Broken Access Control

Password reset doesn’t use verification before resetting password. Attacker can get access to users account by reseting password because confirmation field where original password should be given can be leaved empty.

Steps to reproduce problem:
1. Open browser and go to: http://localhost:8080
2. Sign in with username "ted" and password "ted" and click Login button
3. Now on form.html page there can click text "Click to reset password" to reset password
4. On page reset.html enter new password and confirmation (can be leaved empty)
5. Click submit
6. Attacker gains access to users account 

How to Fix it: When performing password reset the  the original password check must be compulsory to make confirmation. User queries original password and application control  Original password has to be queried from the user and used for user verification before password reset.

__________________________________________________


FLAW 5: Security Misconfiguration

Application uses old version of the Spring Framework 1.4.2. Also there is disabled one of the security features that is set disabled. It gives attacker to way redirect browser requests to malicious web page when user is authenticating. 
Malicious web page using POST -parameters for reSet and send sensitive information to attacker.

Steps to reproduce problems:

1. First is on file pom.xml file in NetBeans, path …/cybersecuritybase-project/cybersecuritybase-project/pom.xml.
2. List says under the org.springframework.boot there is version number 1.4.2
        <parent>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-parent</artifactId>
            <version>1.4.2.RELEASE</version>
        </parent>

How to Fix it: Updating this version to latest working versions of Spring Framework and then re-building the application. Newest version of Spring Framework can be found here https://github.com/spring-projects/spring-framework/wiki/Spring-Framework-Versions.
3. Other problem is in class SecurityConfiguration.java where method http.csrf().disable() is set disabled(). 
How to Fix it: In class SecurityConfiguration.java there must be made little change to method http.csrf().disable() and change it to http.csrf() . After this Spring Framework will automatically add randomized anti-CSRF tokens into every HTTP request. 
