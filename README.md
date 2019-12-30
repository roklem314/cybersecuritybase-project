# Cyber Security Base – Course Project I
Project files are here: https://github.com/roklem314/cybersecuritybase-project. 
Clone the project to your own local machine. Project use Starter Code from here https://github.com/cybersecuritybase/cybersecuritybase-project. 
Following flaws can be reviewed and reproduced by running application on port 8080.

FLAW 1: Cross-Site Request Forgery (CSRF)

Attacker can redirect browser requests to malicious web page when user is authenticating to malicious website because application has CSRF flaw. Malicious web page using POST -parameters for reSet.

Steps to reproduce:
1. Open browser and go to: http://localhost:8080
2. Sign in with username "ted" and password "ted" and then click Login button
3. In the form.html page type in "ted" as name and "address" as address and press submit
4. Create a web page malicious.html and write inside of html body the following form
	<form action="http://localhost:8080/reSet" method="POST">
	<input type="password" name="password" value="changed"/>
	<input type="password" name="confirm" value="changed"/>
	</form>
	<body onload="document.forms[0].submit()">
5. Writing the location of malicious.html file into browser's url bar and tab enter
6. Web page malicious.html will post the data that  needed for changing password. Now attacker has gained access to user account.

How to Fix it: In class SecurityConfiguration.java there must be made little change: http.csrf().disable() to http.csrf() . After this Spring Framework will automatically add randomized anti-CSRF tokens into every HTTP request.

__________________________________________________

FLAW 2: Sensitive Data Exposure 

Re-entering person's name for event registration will show if that person has already registered and show his/her address.
Resending form with POST -parameters in ZAP produces duplicate -message no matter which user created original registration.

Steps to reproduce:
1. Open browser and go to: http://localhost:8080
3. Sign in with username "ted" and password "ted" and then click Login button
4. In the "form" page type in "ted" as name and "address" as address and press submit
5. a message says "user has been signed up to the event" and thats indicating user has signed up to the event
6. go back to http://localhost:8080/form
7. insert "ted" as name and click submit
8. ted’s address will be shown on the message 

How to Fix it: SignupController.java class beginning there is methods defaultMapping() and loadForm(). These methods must be added "httpSession.invalidate();" to prevent this problem.

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

How to Fix it: All input parameter values should be validated before saving. This can be done using javascript to validate input before sending values on client side or server side inside of controller.

__________________________________________________


FLAW 4: Broken Authentication

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

Application uses old version of the Spring Framework 1.4.2. 

Steps to reproduce:
1. Open the pom.xml file in NetBeans, path …/cybersecuritybase-project/cybersecuritybase-project/pom.xml.
2. List says under the org.springframework.boot there is version number 1.4.2
        <parent>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-parent</artifactId>
            <version>1.4.2.RELEASE</version>
        </parent>

How to Fix it: Updating this version to latest working versions of Spring Framework and then re-building the application. 

