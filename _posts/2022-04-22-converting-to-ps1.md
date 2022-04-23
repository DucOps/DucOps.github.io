---
layout: post
title: Converting to Powershell
categories: [blog]
tags: [cmd, powershell, endpoint, Windows]
fullview: true
---

I've only done a bit of Powershell in the past. In fact, before my current job, the most PS I've ever used was some script I probably copied and pasted from Stackoverflow to add users into a distribution list in Office 365.

But now I think I can't avoid it.

I'm the main packager at my company. I don't have to do it, but there's not enough resources (or even experienced people) that can do it. Funnily enough, I was never taught how to package, I just winged it.
 
Over the years, I gotten pretty good. I'm not the best of course. There's probably some skilled guy who knows all the tricks. I'm still struggling to find a methodology to decide on which detection method to use, but I digress.
 
But I got good because well, I use a lot of *.bat (batch) scripting to do installs. Why batch? Because it's simple, lightweight and I've used it in the past as a young teen doing random godknowwhatfun with it. Again, not saying I'm the best, but I'm proficient enough to get an install down the line. I've seen some crazy scripts (Such as MCT: https://github.com/AveYo/MediaCreationTool.bat). If you know enough commands to not only manipulate the install, but the Windows environment, you're set. You can so almost anything. Almost.

I had a scenario where I needed to create shortcuts, and for the life of me, I couldn't do it in batch. So I learnt how to do it in PoweShell.

But when it came to deployment, it didn't work. Localized testing was fine, but Intune says it failed. After some fluffling about, turns out it has something to do with Powershell running execution in 32bit instead of 64bit so you need to define the absolute path to the Powershell executable, etc. That's annoying.

What's good practice though? I read somewhere that you shouldn't really be using batch to do deployments, and that you should do it in PS. Well why not? An MSP (prior to my time) write a Powershell script to change a setting in Windows (forgot which, it's late) and it would fail sometimes (weird). So I took it, re-wrote it in batch, and hasn't failed (yet). Went from like a 30-something line script, down to about 4-5 lines, including a line for REM.

I understand the need for consistency on approach, but if it works, let it work. Delivery over the method, right? There's a time and place for both, and I think they can both co-exist.

But as I document my code, and slowly try to transition intune into a CI/CD pipeline sort of deal, I'll probably end up converting any batch files I've created in the past into a Powershell script.

Just pains me to write this:

{% highlight yaml %}

$DataStamp = get-date -Format yyyyMMddTHHmmss
$logFile = '{0}-{1}.log' -f $file.fullname,$DataStamp
$MSIArguments = @(
    "/i"
    ('"{0}"' -f $file.fullname)
    "/qn"
    "/norestart"
    "/L*v"
    $logFile
)
Start-Process "msiexec.exe" -ArgumentList $MSIArguments -Wait -NoNewWindow 

Credits: https://powershellexplained.com/2016-10-21-powershell-installing-msi-files/

{% endhighlight %}

Over this:
{% highlight yaml %}

START /WAIT msiexec.exe /i "app.msi" /qn /norestart /L*v "C:\temp\applog.log"

{% endhighlight %}

Oh wait well... I can see why PS is more robust and feature rich... BUT I'M LAZY.

exit 0



