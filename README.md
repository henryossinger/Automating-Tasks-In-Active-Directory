# Automating-Tasks-In-Active-Directory
One of the most powerful aspects about Powershell is how it is able to interact with Active Directory. In this module I am going to focusing on creating and removing user accounts, but be aware there are over 20 commands covering different aspects of Active Directory.

<h2>Technologies Used</h2>

- Windows Server 2022 Version 21H2
- Powershell 

<h2>GetADUser Script</h2>
Before beginning, I encourage making this script as it will make checking to see if a user was successfully added to Active Directory much faster then manually checking. For personal testing this script is handy, but in an enterprise situation it doesn't have a great usecase as it is very limited. What it does is pull any User that has a samAccountName, which is the User Logon Property. Every User account has this value so it pulls every name in the Domain. 

To make the script I put it into Powershell ISE and saved the file to my accounts directory, if you save it elsewhere you will have to specify the path Powershell needs to look at to execute the file. Syntax will be posted below: 

- Syntax: Get-ADUser -Filter * -samAccountName | select samAccountName 

<img src="https://imgur.com/cSCCLla.png" height="50%" width="80%" alt="Disk Sanitization Steps"/>
