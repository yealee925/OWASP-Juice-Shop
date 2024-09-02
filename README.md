# OWASP-Juice-Shop

## Objective
The Detection Lab project aimed to establish a controlled environment for simulating and detecting cyber attacks. The primary focus was to ingest and analyze logs within a Security Information and Event Management (SIEM) system, generating test telemetry to mimic real-world attack scenarios. This hands-on experience was designed to deepen understanding of network security, attack patterns, and defensive strategies.

### Skills Learned
-

- Advanced understanding of SIEM concepts and practical application.
- Proficiency in analyzing and interpreting network logs.
- Ability to generate and recognize attack signatures and patterns.
- Enhanced knowledge of network protocols and security vulnerabilities.
- Development of critical thinking and problem-solving skills in cybersecurity.

### Tools Used
- Burp Suite to intercept and 

## Setup Burp Suite
1. If not already installed, download Burp Suite and the extension to configure the web browser to redirect traffic through Burp Suite. In this case, we will be using the Firefox browser, so ensure the FoxyProxy extension is installed
2. Click on the button on the top right of the Firefox browser which will open the options pop-up. Then in the pop-up, select the Options button which will open an Options browser

   ![image](https://github.com/user-attachments/assets/67886f81-1f4c-46c7-9b25-51caa77f7b6b)
3. Go to the Proxies tab and click the Add button to create a new proxy configuration. Add the proxy details by filling in the following values:
   - Title: Burp (or any preferred name)
   - Proxy IP: 127.0.0.1
   - Type: HTTP
   - Port: 8080

   ![image](https://github.com/user-attachments/assets/6b488748-1519-48a5-b64a-2051fe220cdb)
4. Click Save and select the FoxyProxy extension on the top right of the browser. Clicking the Burp configuration will redirect your browser traffic through 127.0.0.1:8080. Switch over to Burp Suite and in the Proxy tab, select Intercept is on which will intercept the traffic and the proxy will populate with the HTTP request 

![image](https://github.com/user-attachments/assets/3ab190fb-f81c-4453-9d40-1bfda3018f86)
   ![image](https://github.com/user-attachments/assets/086211d6-a276-4ec7-b962-1db4510a0c88)

Note: If you want to stop your browser from being intercepted and to be able to load freely, you can either:
  - In Burp Suite, turn off Intercept is on
  - In FoxyProxy, select Disable 

## Reconnaissance
- Web traffic interception isn't necessary in this section, so can disable Burp Suite or Foxyproxy for now
1. Open the Firefox browser and input the target system's IP address (10.10.5.171) into the address bar, which will take you to the OWASP Juice Shop site

   ![image](https://github.com/user-attachments/assets/bb7d126c-646d-4fda-9f26-e19a5fcdd27c)
2. Select products and click on Reviews which will show the user's email address. For instance, the admin's email address can easily be found (admin@juice-sh.op) as they had left reviews

   ![image](https://github.com/user-attachments/assets/e385a222-407b-4b78-b204-710a358ec74f)
3. Another email to keep note of can be found under the Green Smoothie (jim@juice-sh.op). Through a simple Google search ("replicator"), the reference they are referring to is shown to be from Star Trek 

   ![image](https://github.com/user-attachments/assets/ebc66f99-076d-45c9-b4a3-31e458fcb4d4)
   ![image](https://github.com/user-attachments/assets/96036f62-6737-41fc-aee5-8bb03e3ee77a)
4. Try using the search bar which is the magnifying glass at the top right corner. Search whatever you want, then click Enter. Pay attention to the URL which includes the item that was searched

   ![image](https://github.com/user-attachments/assets/4d694d75-7a7c-44a1-98b1-3d828bae4d3a)
   - The search parameter appears to be **q=**

## [A] SQL Injection Steps
1. Go to the Login page and enable traffic interception. Select Intercept is on in Burp Suite and ensure that FoxyProxy is enabled. Login by entering random letters, then switch to Burp Suite. Forward the packet until the results include the email and password you provided 

![image](https://github.com/user-attachments/assets/8c4f3498-2d79-49c3-a484-31883b4cd8c2)
   ![image](https://github.com/user-attachments/assets/c9f26fd4-01c0-4ed5-851e-635820957caf)

2. Change the email to: ' or 1=1--
   Then click Forward

   ![image](https://github.com/user-attachments/assets/ca7c091e-9ead-449c-9d1a-fa49dcc6666e)
   - ' is used to close the brackets in the SQL query
   - The **OR** allows either the previous SQL statement to work if it is true **or** the statement 1=1 that we had inputted which is always true
   - -- in SQL is used to comment out data so any restrictions on the login will no longer work as they are commented out
3. Switch back to the Firefox browser and see that you successfully logged into the admin account, then logout

   ![image](https://github.com/user-attachments/assets/65cbeb47-3e40-416b-acf4-cbc40beb6d99)
4. Now we'll try to log into the admin account with the admin's email. We still don't know the password so we'll use SQL injection to login. Ensure that Burp Suite & FoxyProxy are both enabled. Login by inputting **admin@juice-sh.op** in the email bar and put random letters into the password then select Enter. Switch over to Burp Suite and Foward the packet
     
   ![image](https://github.com/user-attachments/assets/40bf235c-e18a-4455-ab5e-884a65cbc5cd)
   ![image](https://github.com/user-attachments/assets/3fb68c1a-074b-4ad4-b748-98576d67c362)
5. Edit the email to: admin@juice-sh.op'--

   ![image](https://github.com/user-attachments/assets/6cede4c0-a951-4064-9ad1-f6404401f5a4)
   - The email address is valid, so don't need to include 1=1 as the email is already true (so 1=1 can be used when the email/username are not known)
6. Switch over to the Firefox browser to see that login was successful, then logout
   
   ![image](https://github.com/user-attachments/assets/a05b5d00-461f-4d26-802e-d62aac3f71a4)


## [B] Broken Authentication Steps
- We were able to use SQL injection to login to the Admin's account and now we will focus on using brute force in Burp Suite to figure out the password
7. Ensure both Burp Suite and FoxyProxy are both enabled. and capture a login request once more. Use the admin's email for the username (admin@juice-sh.op) and random letters for the password. Go back to Burp Suite, click on the Action tab, and select Send to Intruder

   ![image](https://github.com/user-attachments/assets/c7e3c73d-8215-41e2-a937-dd7e1d9144a3)

8. Switch from the Proxy tab to the Intruder tab. Select the Positions tab, then click the Clear § button

   ![image](https://github.com/user-attachments/assets/f6ef0111-972e-42b6-90bd-e5d93ab71707)
9. Change the password field to §§ inside the quotes using the Add § button

    ![image](https://github.com/user-attachments/assets/3826789d-5869-4fbb-b726-04978bf4bcb9)
10. Switch to the Payloads tab and use the best1050.txt from Seclists for the payload. This can be installed by opening a terminal and inputting **apt-get install seclists**. In the Payloads settings section, select Load...

    ![image](https://github.com/user-attachments/assets/868cef66-f0db-47e2-be8d-8ec7cf3cc37b)
    ![image](https://github.com/user-attachments/assets/c8080731-a30b-469d-beca-4e614bb1b1dd)
11. Load the list from: /usr/share/wordlists/SecLists/Passwords/Common-Credentials/best1050.txt
    After the file is loaded into Burp Suite, select Start attack 

    ![image](https://github.com/user-attachments/assets/ab6c2d24-50ff-4c2d-915f-aff40a5ba646)
12. The status code will indicate if the request was a success or a failure, so filter the requests by status code

    ![image](https://github.com/user-attachments/assets/aa9e3dc3-910c-44f6-806f-10a0c861e87e)
    - A status code of 200 is a successful request (which means it's the password) and a status code of 401 means a failed request
13. Close the window and Discard the attack since the password has been found. Switch over to the Firefox browser and login using the credentials that were found

    ![image](https://github.com/user-attachments/assets/772fbd48-80be-4fcb-99a5-dd230d07b004)
14. In a previous task there was a review with an email address of jim@juice-sh.op



## [C] Broken Access Control Steps

## [D] Cross-Site Scripting XSS Steps
