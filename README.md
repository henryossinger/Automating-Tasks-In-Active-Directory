# Automating-User-Creation-In-Active-Directory
One of the most powerful aspects about Powershell is how it is able to interact with Active Directory. In this module I am going to be focusing on creating and removing user accounts, and have a script to automate the creation of multiple user accounts at the end.

<h2>Technologies Used</h2>

- Windows Server 2022 Version 21H2
- Powershell
- Active Directory

<h2>GetADUser Script</h2>
Before beginning, I encourage making this script as it will make checking to see if a user was successfully added to Active Directory much faster then manually checking. For personal testing this script is handy, but in an enterprise situation it doesn't have a great usecase as it is very limited. What it does is pull any User that has a samAccountName, which is the User Logon Property. Every User account has this value so it pulls every name in the Domain. 

<p>
  
To make the script I put it into Powershell ISE and saved the file to my accounts directory, if you save it elsewhere you will have to specify the path Powershell needs to look at to execute the file. Syntax will be posted below: 

- Syntax: Get-ADUser -Filter * -samAccountName | select samAccountName. Executing files is done with ./file-name-here

<img src="https://imgur.com/cSCCLla.png" height="50%" width="80%" alt="Disk Sanitization Steps"/>

<h2>Adding and Removing Users</h2>
Let's start with the basics that will build into us being able to create a script for adding users. Adding and removing users in Powershell is very easy, and is done with the commands: New-ADUser and Remove-ADUser. For each of these commands, you only need to specify a name. When creating a new user, there are some default perameters that are set because we are not giving Powershell any info as to how we want this account configured: 

- Account is created in "Users" container
- Account is disabled
- Account is a member of the Domain Users group
- No password is set
- User must reset the password at the first logon

Keep in mind that because the default is to disable the account, this account is not usable after being created. 

Below I have creating and removing a user, and using our script as mentioned earlier to show that the account was successfully created. 

<img src="https://imgur.com/oQNdzqc.png" height="50%" width="80%" alt="Disk Sanitization Steps"/>

<h2>Creating "good" User Accounts</h2>
Now that we know how to create very basic accounts, let's use some of the parameters available with the New-ADUser command to create an account that actually works. At the very least a user account should have a password, be enabled, and be put into the correct OU for organization. To do this we are going to need to use the parameters below:

```powershell
-Name "Firstname Lastname" -GivenName "Firstname" -surname "Lastname" -samAccountName "F.Lastname" 
-AccountPassowrd (Read-Host -AsSecureString "Password-Goes-Here" -Force) -ChangePasswordAtLogon $True
-Path "OU=(OU-Goes-Here),DC=(Domain-Name-Here),DC=com 
```
Below is an image using the above parameters in Powershell, and verifying that the new account is created successfully by using our Active Directory Users script. 

<img src="https://imgur.com/GlMGl1g.png" height="50%" width="80%" alt="Disk Sanitization Steps"/>

When trying to login with this newly created account on a domain joined workstation, the user will be prompted to set a new password, and after doing so will successfully sign in and be able to use the workstation. We can see that all of our parameters we set worked correctly. A similar scenario to the onboarding process of a new hire at an organization. 

<img src="https://imgur.com/LcFZOTd.png" height="50%" width="80%" alt="Disk Sanitization Steps"/>

<h2>Automating the Process of Creating User Accounts</h2>
Creating User accounts in Powershell is not only quicker but less of a headache then using the GUI (In my opinion). The real strength of using Powershell with Active Directory is the ability to create multiple user accounts within a very small amount of time. Powershell gives the ability for us to write a script that we can ues anytime to create however many accounts we need. 

<p>

We have already used the commands and parameters needed to make this script, the only difference will be adding variables so that we can use different values for each user we are creating. The simplest way to do this is to put the data we need for our new Users into a CSV file, then have Powershell pull that data and input it into the New-ADUser command.

<p>

Note: I spent hours trying to find out what was wrong with my script, just to figure out Powershell was having trouble parsing Microsoft Excel even though it was in CSV format. Use a text editor for storing the user data, and make sure when saving that it is saved as UTF-8. Powershell can be extremely picky when it comes to reading data out of a file. I used Sublime and it worked immediately after switching off of Excel. 

<img src="https://imgur.com/a9NVcOI.png" height="50%" width="80%" alt="Disk Sanitization Steps"/>

Now that we have our data, let's create a script to add all of these Users to our domain. The one I have below is extremely simple, but it gets the job done.

```powershell
Import-Module ActiveDirectory

#Need to specificy the path of the CSV file Powershell needs to pull data from
$AD_Users = Import-CSV -Path C:\Users\Administrator\NewUsersList

#Assigning variables to the data in the CSV file
foreach ($User in $AD_Users){
  $Username = $User.username
  $Firstname = $User.firstname
  $Lastname = $User.lastname
  $Password = $User.Password 
  $OU = $User.ou

    #Outputs the Username of the account being created, nice feature that is also good for debugging
    Write-Host "Creating User: $Username"

    #Assigning the variables to each parameter 
    New-ADUser `
     -SamAccountName = $Username `
     -UserPrincipalName = "$Username@test-domain1.com" `
     -Name = "$Firstname $Lastname" `
     -GivenName $Firstname `
     -Surname = $Lastname `
     -Enabled $True `
     -DisplayName "$Firstname, $Lastname" `
     -Path $OU `
     -ChangePasswordAtLogon $True `
     -AccountPassword (ConvertTo-SecureString $Password -AsPlainText -Force)

}
```

Anyone who is decently versed in Powershell can look at this script and know exactly what is going on. Running the script and checking the GUI will ensure that it is working correctly and all user accounts are created within the proper OU's. I ran our ADUsers script before and after the newly created BulkUsers script to provide a better visual. 

<img src="https://imgur.com/4E1vqPp.png" height="50%" width="80%" alt="Disk Sanitization Steps"/> 
<img src="https://imgur.com/UUs04gq.png" height="50%" width="40%" alt="Disk Sanitization Steps"/> <img src="https://imgur.com/SMeLNl6.png" height="50%" width="40%" alt="Disk Sanitization Steps"/>

As you can see all our users accounts have been successfully created, and have been placed in the correct OU. With this format we can now add any additional variables we might need, add new parameters, and more! Now when we are tasked with adding X amount of users to our domain we can make the process take seconds and save ourselves a headache. 
