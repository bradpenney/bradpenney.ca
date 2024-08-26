---
title: "PowerShell For All"
date: 2023-01-13
slug: "powershellforall"
description: "PowerShell For All"
keywords: ["powershell", "scripting"]
draft: false
#tags: ["powershell", "scripting"]
math: false
toc: true
---

For years, Linux users have sneered at Windows and its lack of a kernel-wrapping shell that power users can employ to really control their operating system.  Sure, command prompt (`cmd`) was always available, but it is a feeble imitation of more powerful *NIX shells like BASH and ZSH.  Microsoft's answer: PowerShell - a truly capable shell to rival *NIX shells (even if its syntax is a bit odd!).  So, if you're interested in becoming a Windows power user, or dipping your toes into shell scripting (or programming in general), or, like me, you come from a *NIX world and can't stand clicking checkboxes, PowerShell is a great tool to add to your quiver.

### Installing PowerShell

Here's the best part for Windows users - **its already installed in Windows 10 and 11**.  Even for standard users, not just administrators.  Simply search for "PowerShell ISE" in your Start Menu, and you'll be greeted with a program that looks like this:

![Windows PowerShell](/devops/powershell/powershell_ISE.png)

That's it - you're ready to program with PowerShell!

If you're a \*NIX nut like me, it is possible to install PowerShell on Linux.  This is useful for troubleshooting PowerShell scripts, especially if you are storing them in a remote repository and you want to fix something while using a Linux laptop.  The steps, as [provided by Microsoft](https://learn.microsoft.com/en-us/powershell/scripting/install/install-ubuntu?view=powershell-7.3),  are pretty simple:

``` bash
# Update the list of packages
sudo apt-get update
# Install pre-requisite packages.
sudo apt-get install -y wget apt-transport-https software-properties-common
# Download the Microsoft repository GPG keys
wget -q "https://packages.microsoft.com/config/ubuntu/$(lsb_release -rs)/packages-microsoft-prod.deb"
# Register the Microsoft repository GPG keys
sudo dpkg -i packages-microsoft-prod.deb
# Update the list of packages after we added packages.microsoft.com
sudo apt-get update
# Install PowerShell
sudo apt-get install -y powershell
# Start PowerShell
pwsh
```
The install dialogue looks like this:

![Windows PowerShell on Linux](/devops/powershell/installPowerShell_Linux.png)

As an aside, I also tried installing the `snap` version of PowerShell but had no luck - I didn't really investigate so it could easily be user error!

### Why Script in Powershell?

Scripting in PowerShell unlocks power user control of Windows.  Its a great way to automate tedious tasks and it introduces the deeper world of scripting.

**Scenario: You are required to backup your files every day to a company network drive.**  This is tedious at best, and nearly impossible to remember when you're ready to shut down that work laptop and move to your next activity.  

**Solution:** Write a PowerShell script that does it for you!  Here's a little script I use at work to simply make a `.zip` file of my user directory and store it on the network drive:

``` powershell
# Get Timestamp
$date = Get-Date -UFormat "%Y%m%d"

# Clean Downloads Directory
Get-ChildItem -Path C:\Users\<userName>\Downloads\ -Include *.* -File -Recursive | foreach { $_.Delete()}

# Create a .zip of today's backup
Compress-Archive -Path C:\Users\<userName> -DestinationPAth Y:\Documents\Daily_Documents_Backup_$date.zip -Update

# Clean up backups more than 10 days old (goes back 1 year)
for ($num = 10 ; $num -le 365 ; $num++){
  $Temp = (Get-date).AddDays(-$num)
  $fileToDelete = "Y:\Documents\Daily_Documents_Backup_"+(Get-Date $Temp -UFormat "%Y%m%d")+".zip"
  if (Test-Path $fileToDelete) {
    echo("Found " + $fileToDelete + ", removing now...")
    Remove-Item $fileToDelete  
  }
}
```

Now, simply run the script using PowerShell ISE (or even via PowerShell command line if you prefer!)  Your files get backed up safely to the network drive in case your laptop decides its had enough of this world.  For bonus points, kick off the script using Windows Task Scheduler - so you can literally "set-it-and-forget-it".  Note - if you're doing this at work, your company may have locked down Windows Task Scheduler so it might not be an option for you.

### PowerShell Resources

While the value here is obvious, clearly the little script above uses some programming concepts such as a `for loop` which isn't going to be obvious if you're new to scripting /programming.  If you want to dive deep into PowerShell, a great place to start is [Learn PowerShell from TutorialsPoint](https://www.tutorialspoint.com/powershell/index.htm).  As an experienced shell scripter, I opted for *PowerShell Cookbook* by Lee Holmes, which got me up and running in no time.  Highly recommended desktop reference! 

![PowerShell Cookbook](/devops/powershell/powerShell_Cookbook.jpg)

In addition, naturally, there are thousands of web pages dedicated to PowerShell syntax and errors - simply input any error you receive into [your favourite search engine](https://duckduckgo.com/) and you'll be greeted with an array of (possibly bewildering, usually helpful) resources.

### Conclusion

The use cases for PowerShell are numerous - not the least of which is **learning to better control the technology you use**.  Explore your world - give PowerShell a whirl!
