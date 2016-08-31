
QlikSense Automated Task Failure Email Alerts
=============================================

![](https://github.com/NickAkincilar/QlikSense-Task-Failure-Email-Alerts/blob/master/images/UI.jpg)![](https://github.com/NickAkincilar/QlikSense-Task-Failure-Email-Alerts/blob/master/images/Email2.jpg)

### Release Notes

- 8/31/2016 Version 1.2 is released. 
    - Added **RESET HISTORY** button on the GUI to easily reset the 24 hour wait period for previously sent notifications.
    - Changed **FAILED TASK** logic filter so it ignores **DISABLED TASKS**. Previously it would sent emails even if the task was disabled.

[Download Ver 1.2!](https://github.com/NickAkincilar/QlikSense-Task-Failure-Email-Alerts/blob/master/QlikSense-Task-Failure-Email-Alerts/QlikSense_Email_Alert_installer_ver12.zip)

This is an automated QlikSense task failure email alert tool. It is
designed to run unattended like a windows service to send emails if a
failure is detected in any of the tasks. It uses repository(qmc) api
calls to query only the failed tasks and emails notices if any found.
Each email is recorded in a log file so it does not bombard users with
multiple emails for the same task. Instead it will wait for 24 hours
before sending another alert per each failed task if an other email was
sent prior. It uses direct REST calls instead of using wrappers so it
should be backwards compatible within reason.

The main program file **QlikSenseEmailAdmin.exe** has 3 modes of
operation.

1.  Running it without any command line arguments will cause the
    configuration screen to be displayed, allowing you to configure
    the process.

2.  Running it with "**-a"** argument such as **QlikSenseEmailAdmin.exe
    -a** will run it as an unattended program where it will use the
    configuration settings stored in the windows registry. These
    settings are maintained by the configuration UI screen.

3.  Running it with the following arguments will cause it to ignore the
    registry settings and run as unattended program using the specified
    parameters in the argument itself

Example: 

**QlikSenseEmailAdmin.exe** **-proxy:**https://localhost **-wait:**60000 **-smtp\_server:**smtp.gmail.com **-smtp\_port:**587 **-smtp\_enableSSL:**Y **-stmp\_user:**YourEmail@gmail.com **-smtp\_pw:**YourEmailPassword** -smtp\_from:**YourEmail@gmail.com
**-smtp\_to:**UserToBeNotified@YourCompany.com

### **SETUP & CONFIGURATION**

Once installed. setup is an easy 3 step process. Run the app as an
Admin, Configure and test Qliksense and SMTP servers, click to create a
scheduled event. Below are detailed instructions on this process:

1.  Download the latest version located at the bottom of the page. Unzip
    it and run **Setup.exe**

2.  Once the installation is complete. It will create a new program
    group called “**QlikSense Email Alerts**”

> ![](https://github.com/NickAkincilar/QlikSense-Task-Failure-Email-Alerts/blob/master/images/ProgramMenu.jpg)
>
> 3\. Right Click on **QlikSense Email Alert** and choose **Run as
> Administrator  (*Run as Admin is required to be able to save your
> changes*)**
>
> ![](https://github.com/NickAkincilar/QlikSense-Task-Failure-Email-Alerts/blob/master/images/ProgramsAdmin.jpg)
>
> 4\. Configuring the Alerting System is done in 3 steps.
>
>  
>
> **a. Setup Qliksense Server URL** & Click **Test Connection**. This
> will test the URL by requesting a task list from QlikSense server
>
> **b. Setup SMTP Email Server Settings** & Click **Test Email**. If
> successful, you will get a message box indicating the result and
> should receive a test email almost immediately,  (*if using GMAIL,
> gmail account settings needs be modified to allow 3^rd^ party smtp
> connections*.* Instructions for changing gmail settings is located
> below this article*. *Also depending on the email server, if the email
> address is not the username, smtp\_user and smtp\_from fields could be
> different*) 
>
> **c. Setup TASK SCHEDULER** & click **ADD/UPDATE SCHEDULER** 

-   1.  -   Add the domain or local **username & password** that will
            perform the task. (*Username needs to be in Domain\\Username
            format*. *User has to have security rights within QMC to be
            able to query tasks.)*

        -   Choose the **time interval** in minutes. * ( This is how
            often windows scheduler will query for failed tasks.*)

        -   Click on **Add/Update Scheduler** to create a Windows
            Scheduler Task item which will execute at the specified
            time interval. This will create a new windows scheduler task
            called "**QlikSense Email Alert - (Auto)**" located in
            **Control Panel\\Admin Tools\\Task Scheduler. (***More
            advanced time or execution related changes can be made to
            this task manually after it has been created***)** 

> ![](https://github.com/NickAkincilar/QlikSense-Task-Failure-Email-Alerts/blob/master/images/Task Scheduler.jpg)![](https://github.com/NickAkincilar/QlikSense-Task-Failure-Email-Alerts/blob/master/images/TaskUI.jpg)
>
> 5\. Test the functionality. For this you have two options: 

-   -   Within windows scheduler interface right click on **QlikSense
        Email Alert - (Auto)** task and choose **Run**.

    -   OR wait for the specified time interval and see if you get
        an email.  

**Debugging**: You can check
the **C:\\Users\\&lt;username&gt;\\AppData\\Roaming\\QlikSenseEmailAdmin\\History\\**
folder for debug details to see if it ran at all and to see if there are
any errors.

### ADDITIONAL INFORMATION

You can manually trigger the task (and disable it if not needed) or it
will start at the specified time once a day and run for 24 hours every X
minutes.  It will get a list of all failed tasks every X minutes. if it
finds a failed task that it has not sent an email within the last 24
hours, it will trigger an email alert. (*Each task failure alert is
emailed once a day regardless of how many times it fails during the day.
X minutes determines the max time interval between the actual failure
and the first email notice *)  If setup as following these directions,
 It will run like a service so the user does not have to be logged in to
the machine.

 

If everything is configured properly and you have failed tasks, you
should start getting alerts like this (*sorry no attached log files as
of now*) .

![](https://github.com/NickAkincilar/QlikSense-Task-Failure-Email-Alerts/blob/master/images/Email2.jpg)


 

### **APPLICATION FOLDERS & DATA**

-   -   Application is installed in to “**C:\\Program
        Files\\QlikSenseEmailAlert\\**” folder by default.

    -   Following files & folders ( log & History) are stored in
        “**C:\\Users\\&lt;username&gt;\\AppData\\Roaming\\QlikSenseEmailAdmin**\\”

        -   **Log.txt** has a list of previously sent emails per task to
            track what was previously sent so it only sends a single
            email alert per task per 24 hour period.

        -   **\\History\\** folder has debug logs of each run within the
            last 2 days. Files older then 2 days are
            deleted automatically.

    -   Configuration settings are stored in registry in the following
        location: **HKEY\_LOCAL\_MACHINE\\Software\\Qliksense\_Email\_Alert** 


 

### **CHANGING GMAIL SETTINGS **

If you are planning to use Gmail SMTP server to send emails then you
need to follow these instructions to allow 3rd party apps to use your
gmail account.

1.  Go to the ["Less secure apps"
    section](http://www.google.com/settings/security/lesssecureapps) in
    My Account.

2.  Next to "Access for less secure apps," select **Turn on**. (**Note
    to Google Apps users:** This setting is hidden if your administrator
    has locked less secure app account access.)




