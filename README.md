<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>Group Policy Management and Inspecting Event Viewer logs (Azure)</h1>
This tutorial outlines the how to do group policy management in Active Directory and briefly touching on Event Viewer within Azure Virtual Machines.<br />


<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- Event Viewer

<h2>Operating Systems Used </h2>

- Windows Server 2022, at least 2vCPUs, 8GB RAM
- Windows 10 Pro (21H2), at least 2vCPUs, 8GB RAM

<h2>High-Level Deployment and Configuration Steps</h2>

- Create some users within the domain
- Configure group policy account lockout settings
- Enabling and disabling accounts
- Observing logs

<h2>Deployment and Configuration Steps</h2>

<p>
We can start off by creating some users within DC-1. Go to Active Directory Users and Computers or search dsa.msc in the search bar, right-click on the _EMPLOYEES OU -> New -> Users. Then you can proceed to fill in
  any username and password. Once you get a few users we can begin to configure the group policy. 

Search gpmc.msc for Group policy management console in the search bar. 
</p>
<br />

<p>
<img src="https://i.imgur.com/PEIyob3.png" height="80%" width="80%" alt="gpmc"/>
  <img src="https://i.imgur.com/81VHTlK.png" height="80%" width="80%" alt="gpmc landing page"/>
</p>
<p>
We can either edit an existing group policy object (GPO) or create a new one. In this case, we'll edit the existing Default Domain Policy. Click on this then go to Computer Configuration -> Policies -> Windows
  Settings -> Security Settings -> Account Policies -> Account Lockout Policy.
</p>
<br />

<p>
<img src="https://i.imgur.com/ZpzKsdO.png" height="80%" width="80%" alt="account lockout policies"/>
</p>
<p>
Some defintions:
<p><b>Account lockout duration</b>: how long an account will be locked out for</p>
<p><b>Account lockout threshold</b>: how many times incorrect credentials can be entered in before being locked out</p>
<p><b>Allow administrator account lockout</b>: can apply lockout policy to the built-in administrator of the domain</p>
<p><b>Reset account lockout counter after</b>: how long it takes for the threshold counter to reset after an incorrect input</p>
<p>In this case we can set the lockout duration to 30min, threshold to 5 attempts, leave the administrator account lockout as not defined and the reset duration to 10min. 
<p>Before we try to lock ourselves out of an account, we need to update the group policy in Client-1 or the client machine. We can do this by logging as an admin user into Client-1, going to the command line or
Powershell as an administrator and running the command gpupdate /force.</p>

<img src="https://i.imgur.com/6H16mI6.png" height="80%" width="80%" alt="gpudpate force"/>

 <p> This will now update the group policy to include our changes to the account lockout policies. Now let's see what happens when we try to login incorrectly too many times. Pick any random user that was 
   created before and log into Client-1. If you tried to enter incorrect credentials more than 5 times, this should appear.</p>
</p>
<br />

<p>
  <img src="https://i.imgur.com/GYBk3gd.png" height="80%" width="80%" alt="account lockout banner"/>
</p>
<p>
We can try and unlock this account as an admin. Go back to DC-1 as an admin user and go Active Directory Users and Computers. It may be hard to find exactly which user was locked out, so an easy way to find them is
  by right-clicking on mydomain.com -> Find...
</p>
<br />
<p>
  <img src="https://i.imgur.com/2WswnMk.png" height="80%" width="80%" alt="find locked out user"/>
</p>
<p>
Now once we access their account, you check the unlock account box and hit OK. This will unlock the user's account and allow them to sign in again. We can also reset a user's account from the find menu as well.
  Simply right-click on the user's name and click on Reset password. This will also us to unlock the account at the same time if we haven't done so already.
</p>
<br />
<p>
  <img src="https://i.imgur.com/hwrC6Iu.png" height="50%" width="50%" alt="unlock account"/>
  <img src="https://i.imgur.com/ukCyFsK.png" height="50%" width="50%" alt="reset password"/>

</p>
<p>
In the same drop down for resetting a password is an option to disable an account. We can simply click on this to disable a user's account and re-enable it via the same dropdown menu. The black down arrow in the
  icon shows that the account has been disabled. There's normally a good reason for accounts being disabled such as an employee leaving the company or a compromised account. 
</p>
<br />

<p>
  <img src="https://i.imgur.com/3cTif50.png" height="80%" width="80%" alt="disabled account"/>
</p>
<p>
Finally, we have a look at Event Viewer. Certain logs, like Security logs, may only be accessed by admins so we'll need to login Client-1 as an admin. Search eventvwr.msc in the search bar and enter to access
  Event Viewer. This program holds all of the logs within the Windows operating system. Click on Windows logs in the left pane, then Security. We can then click Filter current log on the right pane then type in 
  4624,4625 in the Event ID. Event IDs are numbers assigned to certain types of events within the system. 4624 is for a successful logon and 4625  is for a failed logon. Doing that we should get this.
  
</p>
<br />
<p>
  <img src="https://i.imgur.com/P6OWqwX.png" height="80%" width="80%" alt="filter current log"/>
  <img src="https://i.imgur.com/eKiWQJy.png" height="80%" width="80%" alt="observing logs in event viewer"/>
</p>
<p>
We can see our 5 failed logon attempts in a row. Afterwards, we can unlock the account and then logon again as a regular user.
</p>
