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

Steps to reproduce the problem:
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

On the form page (form.html), the attacker can insert malicious javascript through text fields and then gain information about users' sing-up information such as address.

Steps to reproduce the problem:
1. Open the browser and go the address http://localhost:8080 .
2. Sign in with username "ted" and password "ted" and then click the Login button.
3. In the form page (form.html) type "ted" as username and "<script>alert('error')</script>" as address and press Submit button.
4. Then text "user has been signed up to the event" appears as a successful sign up to the event. Now inserted javascript is stored now as executable that will run when sent to the victim's browser.
5. Go back to the address http://localhost:8080 .
6. If "ted" is still logged in, type in "ted" as username and anything you want as an address.
7. This makes a redirect to another page, and the browser runs the javascript that was previously stored.
8. After javascript runs, then the message "user has already registered with the address ." will be seen on the page.

How to Fix it: All input parameter values on the form page (form.html) should be validated before saving. This can be done using javascript, which validates input before sending values on the client-side or server-side inside the controller. By escaping untrusted HTTP request data based on the HTML output context (body, attribute, JavaScript, CSS, or URL), it will resolve Reflected and Stored XSS vulnerabilities.

__________________________________________________


FLAW 4: Broken Authentication and Broken Access Control

Password reset does not use verification before resetting the password. The attacker can access users' accounts by resetting the password because the confirmation field where the original password should be given can be left empty.

Steps to reproduce the problem:
1. Open the browser and go to address http://localhost:8080 .
2. Sign in with username "ted" and password "ted" and then click the Login button.
3. Now, on the form page (form.html), there is a clickable text "Click to reset password" that will perform the password reset event.
4. On the same page, now enter a new password and confirmation (this can be left empty).
5. Then Click submit button.
6. Attacker gains access to the user "ted" account.

How to Fix it: When performing a password reset to the original password, a confirmation check must be compulsory to all users. The user must provide an original password, and the application control action must verify this is indeed the original password before the user can successfully perform a password reset.

__________________________________________________


FLAW 5: Security Misconfiguration

The application uses an old version of the Spring Framework 1.4.2. Also, there is on disabled state one of the security features which gives attacker to way redirect browser requests to the malicious web page when the user is authenticating. 
Malicious web page using POST -parameters for "reSet" and send sensitive information to attacker-controlled domain.

Steps to reproduce the problem:
1. At first, there is the file pom.xml in NetBeans, path …/cybersecuritybase-project/cybersecuritybase-project/pom.xml.
2. Open this file where is configuration for to org.springframework.boot which version number  is 1.4.2 .
        <parent>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-parent</artifactId>
            <version>1.4.2.RELEASE</version>
        </parent>
        
How to Fix it:  There is needed updating to this version to the latest working versions of Spring Framework and then re-building the application. The newest version of Spring Framework can be found here https://github.com/spring-projects/spring-framework/wiki/Spring-Framework-Versions but check its working version to your needs.

3. Other problem is in class SecurityConfiguration.java where method http.csrf().disable() is set to disabled() state.
How to Fix it: In the class SecurityConfiguration.java there must be made little change to method http.csrf().disable() and change it to http.csrf() . After this, Spring Framework will automatically add randomized anti-CSRF tokens into every HTTP request.
