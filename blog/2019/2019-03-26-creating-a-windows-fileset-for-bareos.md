---
title: Creating a Windows Fileset for Bareos
author: svennd

date: 2019-03-26T14:06:19+00:00
url: /creating-a-windows-fileset-for-bareos/
thumbnail: /img/2020/05/moritz-kindler-pPxUuFZ4arQ-unsplash-1-scaled.jpg
categories:
  - SysAdmin
  - windows
tags:
  - bareos

---
Bareos uses _FileSets_ to decide what to backup and what not to backup. While the documentation is extensive, the approach is mostly include everything and exclude parts (include all, exclude after).  While this is the way average people want to backup. If you work around non-IT minded people, you know that they will store EVERYTHING, EVERYWHERE and mix downloadable data with self created data. So the "include all, exclude after" method would make backups explode in size, with allot of unnecessary data.

So my idea is to exclude everything and include parts I know are relevant to backup. Inevitably this will make for allot exceptions to the rule, but with 
that is easier then including everything and finding a way to keep performance up. (exclude all, include some)

I want to have multiple small jobs that finish "quickly" on the client. Using this approach I can do multiple backups per day on specific data without generating an extreme load on client machines. The "backup everything" jobs can then be run on a lower frequency. As the frequently changed data is backed up already.

Since most of the work is in Microsoft Office applications, I started there. Now we have allot of scientific data floating around, this data however, is backed up on result servers and don't belong in the client desktop backups. This is another reason to go with exclude everything, include what you need; (although nobody ever got fired for making a backup extra...)

### Creating a new FileSet

Let's start by creating a new FileSet, you can name it whatever, but best to name it something you can recognize.

<pre>nano /etc/bareos/bareos-dir.d/fileset/win_office.conf</pre>

A "template" would look like this :

<pre>FileSet {
  # required name
  Name = "win_office"

  # volume shadow copy service
  # this is windows specific
  Enable VSS = yes
  
  # include
  Include {

    # include from this directory
    # 
    File = "C:/Users"

    Options {
      # config
      Signature = MD5
      IgnoreCase = yes
      noatime = yes

      # Word, Excel, Powerpoint
      WildFile = "*.doc"
      WildFile = "*.docx"
      WildFile = "*.xls"
      WildFile = "*.xlsx"
      WildFile = "*.ppt"
      WildFile = "*.pptx"

      # open office
      WildFile = "*.odt"
      WildFile = "*.ods"
      WildFile = "*.odp"

      # pdf
      WildFile = "*.pdf"
    }	

    Options {
      # all files not in include
      RegExFile = ".*"
      Exclude = yes
    }
  }
}</pre>

### Test the FileSet

Now before we deploy this FileSet, you can test this on a client to see what exactly gets backed up (dry-run); The easiest way is to create a Job Definition and Job :

<pre>nano /etc/bareos/bareos-dir.d/jobdefs/BackupWindowsOffice.conf</pre>

<pre>JobDefs {
  # name (required)
  Name = "BackupWindowsOffice"
  
  # type can be backup/restore/verify
  Type = Backup
  
  # the default level bareos will try
  # can also be Full/Differential(since last full)/Incremental(since last incremental)
  Level = Incremental
  
  # the default client, to be overwritten by the job.conf
  Client = bareos-fd
  
  # the fileset we just created
  FileSet = "win_office"
  
  # the schedule
  Schedule = "Nightly"
  
  # where to store it
  Storage = File
  
  # the message reporting
  Messages = Standard
  
  # the pool where to store it
  Pool = Incremental
  
  # the higher the value priority the lower it will be dropped in the queue
  # so for important jobs priority=1 will run first
  Priority = 10
  
  # the bootstrap file keeps a "log" of all the backups, and gets rewritten every time a 
  # full backup is made, it can be used during recovery
  Write Bootstrap = "/var/lib/bareos/%c.bsr"
  
  # in case these value's get overwritten
  # define where would be a good pool to write 
  # note that full backup will be used atleast once because no full
  # backup will exist
  Full Backup Pool = Full
  Differential Backup Pool = Differential
  Incremental Backup Pool = Incremental
}</pre>

Then the Job :

<pre>nano /etc/bareos/bareos-dir.d/job/fileset_test.conf</pre>

<pre>Job {
  # required
  Name = "svennd-office"
  
  # the default settings
  JobDefs = "BackupWindowsOffice"
  
  # overwrite the client here
  Client = "svennd"
}</pre>

Obviously we are not going to wait until the nightly is ran; open bconsole and run :

<pre>Enter a period to cancel a command.
*estimate job=svennd-office listing</pre>

To see what files are scheduled for backup; The backup set I used as template is useful, but includes way to much. So you can change it, run a reload and test again using estimate :

<pre>2000 OK estimate files=7,741 bytes=141,521,221
You have messages.
*reload
reloaded
*estimate job=svennd-office listing</pre>

Until you have exactly what you need.

### Current Windows FileSet

I have Windows 7 and Windows 10 users to backup. While generally that does not make much difference for Bareos, the Windows 7 users, have specifically a funny directory structure :

  * C:/users/username is moved to D:/users/username, but C:/users still exists ...
  * D:/users/username/documents is moved to D:/documents

Bareos will only issue a warning if <code class="EnlighterJSRAW" data-enlighter-language="null">File = "D:/Users"</code> is not found, so you could only use a single config files, but I dislike ignoring warnings, so I made a difference between windows 7 and windows 10 FileSets;

Below you will find my current FileSets, since I'm still playing around with these sets, they will most likely still change. Note that you don't have to generate a separate config file for every _FileSet {} . _So I think its a good idea to combine similar configs.

All these are found:

<pre>/etc/bareos/bareos-dir.d/fileset/</pre>

**win_images.conf** : images on windows 7 + 10, images are badly compressible, so don't spend time on these.

<pre># windows 7 & windows 10 images

FileSet {
  Name = "Win7_images"
  
  # volume shadow copy service
  Enable VSS = yes
  Include {
  
    # location
    File = "D:/Users"
  
  Options {
    # config
    Signature = MD5
    IgnoreCase = yes
    noatime = yes
    
    # images
    WildFile = "*.jpg"
    WildFile = "*.gif"
    WildFile = "*.tif"
    WildFile = "*.png"
  }	

  # exclude everything else
    Options {
    
    # all files not in include
    RegExFile = ".*"
    
    # default user profiles
    WildDir = "[C-D]:/Users/All Users/*"
    WildDir = "[C-D]:/Users/Default/*"
    
    # explicit don't backup
    WildDir = "[C-D]:/Users/*/AppData"
    WildDir = "[C-D]:/Users/*/Music"
    WildDir = "[C-D]:/Users/*/Videos"
    WildDir = "[C-D]:/Users/*/Searches"
    WildDir = "[C-D]:/Users/*/Saved Games"
    WildDir = "[C-D]:/Users/*/Links"
  
    # application specific
    WildDir = "[C-D]:/Users/*/MicrosoftEdgeBackups"
    WildDir = "[C-D]:/Users/*/Documents/R"
    WildDir = "*.svn/*"
    WildDir = "*.git/*"
    WildDir = "*.metadata/*"
    WildDir = "*cache*"
    WildDir = "*temp*"
    
    # share services
    WildDir = "*iCloudDrive*"
    WildDir = "*OneDrive*"
    WildDir = "*stack*"
    
    # windows specific
    WildDir = "*RECYCLE.BIN*"
    WildDir = "[C-D]:/System Volume Information"
    
    Exclude = yes
  }
  }
}

FileSet {
  Name = "Win10_images"
  
  # volume shadow copy service
  Enable VSS = yes
  Include {
  
  # location
    File = "C:/Users"
  
  Options {
    # config
    Signature = MD5
    IgnoreCase = yes
    noatime = yes
    
    # images
    WildFile = "*.jpg"
    WildFile = "*.gif"
    WildFile = "*.tif"
    WildFile = "*.png"
  }	

  # exclude everything else
    Options {
    
    # all files not in include
    RegExFile = ".*"
    
    # default user profiles
    WildDir = "[C-D]:/Users/All Users/*"
    WildDir = "[C-D]:/Users/Default/*"
    
    # explicit don't backup
    WildDir = "[C-D]:/Users/*/AppData"
    WildDir = "[C-D]:/Users/*/Music"
    WildDir = "[C-D]:/Users/*/Videos"
    WildDir = "[C-D]:/Users/*/Searches"
    WildDir = "[C-D]:/Users/*/Saved Games"
    WildDir = "[C-D]:/Users/*/Links"
  
    # application specific
    WildDir = "[C-D]:/Users/*/MicrosoftEdgeBackups"
    WildDir = "[C-D]:/Users/*/Documents/R"
    WildDir = "*.svn/*"
    WildDir = "*.git/*"
    WildDir = "*.metadata/*"
    WildDir = "*cache*"
    WildDir = "*temp*"
    
    # share services
    WildDir = "*iCloudDrive*"
    WildDir = "*OneDrive*"
    WildDir = "*stack*"
    
    # windows specific
    WildDir = "*RECYCLE.BIN*"
    WildDir = "[C-D]:/System Volume Information"
    
    Exclude = yes
  }
  }
}</pre>

**win_office.conf**

<pre># all office files in users (c:/ and d:/)
# for win 7		= D
# for win 10 	= C 


FileSet {
  Name = "Win7_office"
  
  # volume shadow copy service
  Enable VSS = yes
  Include {
  
  # location
    File = "D:/Users"
    File = "D:/My Documents"
  
  Options {
    # config
    Signature = MD5
    compression = LZ4
    IgnoreCase = yes
    noatime = yes
    
    # Word
    WildFile = "*.doc"
    WildFile = "*.dot"
    WildFile = "*.docx"
    WildFile = "*.docm"

    # Excel
    WildFile = "*.xls"
    WildFile = "*.xlt"
    WildFile = "*.xlsx"
    WildFile = "*.xlsm"
    WildFile = "*.xltx"
    WildFile = "*.xltm"

    # Powerpoint
    WildFile = "*.ppt"
    WildFile = "*.pot"
    WildFile = "*.pps"
    WildFile = "*.pptx"
    WildFile = "*.pptm"
    WildFile = "*.ppsx"
    WildFile = "*.ppsm"
    WildFile = "*.sldx"

    # access
    WildFile = "*.accdb"
    WildFile = "*.mdb"
    WildFile = "*.accde"
    WildFile = "*.accdt"
    WildFile = "*.accdr"

    # publisher
    WildFile = "*.pub"

    # open office
    WildFile = "*.odt"
    WildFile = "*.ods"
    WildFile = "*.odp"

    # pdf
    WildFile = "*.pdf"
    
    # flat text / code
    WildFile = "*.xml"
    WildFile = "*.log"
    WildFile = "*.rtf"
    WildFile = "*.tex"
    WildFile = "*.sql"
    WildFile = "*.txt"
    WildFile = "*.tsv"
    WildFile = "*.csv"
    WildFile = "*.php"
    WildFile = "*.sh"
    WildFile = "*.py"
    WildFile = "*.r"
    WildFile = "*.rProj"
    WildFile = "*.js"
    WildFile = "*.html"
    WildFile = "*.css"
    WildFile = "*.htm"
  }	

  # exclude everything else
    Options {
    
    # all files not in include
    RegExFile = ".*"
    
    # default user profiles
    WildDir = "[C-D]:/Users/All Users/*"
    WildDir = "[C-D]:/Users/Default/*"
    
    # explicit don't backup
    WildDir = "[C-D]:/Users/*/AppData"
    WildDir = "[C-D]:/Users/*/Music"
    WildDir = "[C-D]:/Users/*/Videos"
    WildDir = "[C-D]:/Users/*/Searches"
    WildDir = "[C-D]:/Users/*/Saved Games"
    WildDir = "[C-D]:/Users/*/Favorites"
    WildDir = "[C-D]:/Users/*/Links"
  
    # application specific
    WildDir = "[C-D]:/Users/*/MicrosoftEdgeBackups"
    WildDir = "[C-D]:/Users/*/Documents/R"
    WildDir = "*iCloudDrive*"
    WildDir = "*.svn/*"
    WildDir = "*.git/*"
    WildDir = "*.metadata/*"
    WildDir = "*cache*"
    WildDir = "*temp*"
    WildDir = "*OneDrive*"
    WildDir = "*RECYCLE.BIN*"
    WildDir = "[C-D]:/System Volume Information"
    Exclude = yes
  }
   
  }
}

FileSet {
  Name = "Win10_office"
  
  # volume shadow copy service
  Enable VSS = yes
  Include {
  
  # location
    File = "C:/Users"
  
  Options {
    # config
    Signature = MD5
    compression = LZ4
    IgnoreCase = yes
    noatime = yes
    
    # Word
    WildFile = "*.doc"
    WildFile = "*.dot"
    WildFile = "*.docx"
    WildFile = "*.docm"

    # Excel
    WildFile = "*.xls"
    WildFile = "*.xlt"
    WildFile = "*.xlsx"
    WildFile = "*.xlsm"
    WildFile = "*.xltx"
    WildFile = "*.xltm"

    # Powerpoint
    WildFile = "*.ppt"
    WildFile = "*.pot"
    WildFile = "*.pps"
    WildFile = "*.pptx"
    WildFile = "*.pptm"
    WildFile = "*.ppsx"
    WildFile = "*.ppsm"
    WildFile = "*.sldx"

    # access
    WildFile = "*.accdb"
    WildFile = "*.mdb"
    WildFile = "*.accde"
    WildFile = "*.accdt"
    WildFile = "*.accdr"

    # publisher
    WildFile = "*.pub"

    # open office
    WildFile = "*.odt"
    WildFile = "*.ods"
    WildFile = "*.odp"

    # pdf
    WildFile = "*.pdf"
    
    # flat text / code
    WildFile = "*.xml"
    WildFile = "*.log"
    WildFile = "*.rtf"
    WildFile = "*.tex"
    WildFile = "*.sql"
    WildFile = "*.txt"
    WildFile = "*.tsv"
    WildFile = "*.csv"
    WildFile = "*.php"
    WildFile = "*.sh"
    WildFile = "*.py"
    WildFile = "*.r"
    WildFile = "*.rProj"
    WildFile = "*.js"
    WildFile = "*.html"
    WildFile = "*.css"
    WildFile = "*.htm"
  }	

  # exclude everything else
    Options {
    
    # all files not in include
    RegExFile = ".*"
    
    # default user profiles
    WildDir = "[C-D]:/Users/All Users/*"
    WildDir = "[C-D]:/Users/Default/*"
    
    # explicit don't backup
    WildDir = "[C-D]:/Users/*/AppData"
    WildDir = "[C-D]:/Users/*/Music"
    WildDir = "[C-D]:/Users/*/Videos"
    WildDir = "[C-D]:/Users/*/Searches"
    WildDir = "[C-D]:/Users/*/Saved Games"
    WildDir = "[C-D]:/Users/*/Favorites"
    WildDir = "[C-D]:/Users/*/Links"
  
    # application specific
    WildDir = "[C-D]:/Users/*/MicrosoftEdgeBackups"
    WildDir = "[C-D]:/Users/*/Documents/R"
    WildDir = "*iCloudDrive*"
    WildDir = "*.svn/*"
    WildDir = "*.git/*"
    WildDir = "*.metadata/*"
    WildDir = "*cache*"
    WildDir = "*temp*"
    WildDir = "*OneDrive*"
    WildDir = "*RECYCLE.BIN*"
    WildDir = "[C-D]:/System Volume Information"
    Exclude = yes
  }
   
  }
}</pre>
