# Automating-Tasks-In-Active-Directory
One of the most powerful aspects about Powershell is how it is able to interact with Active Directory. In this module I am going to focusing on creating and removing user accounts, but be aware there are over 20 commands covering different aspects of Active Directory.

<h2>Technologies Used</h2>

- Windows Server 2022 Version 21H2
- Powershell 

<h2>GetADUser Script</h2>
  Before beginning, I encourage making this script as it will make checking to see if a user was successfully added to Active Directory much faster then manually checking. For personal testing this script is handy, but in an enterprise situation it doesn't have a great usecase as it is very limited. What it does is pull any User that has a samAccountName, which is the User Logon Property. Every User account has this value so it pulls every name in the Domain. 

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
