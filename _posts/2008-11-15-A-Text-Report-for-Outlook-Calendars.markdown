---
layout: post
title:  "A Text Report for Outlook Calendars"
date:   2008-11-15 06:00:00 -0400
categories: Programming
---
My prior firm created a great little application for Outlook and Exchange calendars,
here’s how it worked. People in the Systems Group entered their out-of-office time (vacation time,
sick days, work from home days, etc) into an Exchange Public Folder calendar called “Vacations.”
The application, which ran overnight sort of in a batch mode, would read the calendar and format a
text report suitable for reading in Outlook or on a Blackberry device. The report, as I remember it,
looked something like this:

```
This is formatCalendarReport.wsf running on 11/9/2008 11:06:30 AM...

        Calendar: Personal Folders/Calendar...
   Report period: 11/10/2008 through 11/13/2008 (4 days)...

Monday, 11/10/2008:
        +-------------------------------------------------------------+
         Subject: Sal - Vacation
            Body:
            From: 11/10/2008   To: 11/12/2008
        +-------------------------------------------------------------+

Tuesday, 11/11/2008:
        +-------------------------------------------------------------+
         Subject: Sal - Vacation
            Body:
            From: 11/10/2008   To: 11/12/2008
        +-------------------------------------------------------------+
         Subject: Jim - Working from home
            Body: Please contact me at 212.555.1212 as needed.
            From: 11/11/2008   To: 11/11/2008
        +-------------------------------------------------------------+

Wednesday, 11/12/2008:
        +-------------------------------------------------------------+
         Subject: Sal - Vacation
            Body:
            From: 11/10/2008   To: 11/12/2008
        +-------------------------------------------------------------+
         Subject: Rob - Cisco seminar
            Body: I will be out of the office in the afternoon.
            From: 11/12/2008 12:00:00 PM   To: 11/12/2008 5:00:00 PM
        +-------------------------------------------------------------+

Thursday, 11/13/2008:
        +-------------------------------------------------------------+
         No calendar events for today.
        +-------------------------------------------------------------+

This is formatCalendarReport.wsf ending on 11/9/2008 11:06:31 AM...
```

Now keep in mind that this calendar report is not something that Outlook or Exchange provides
as a standard feature, so a developer in the Systems Programming Group wrote a custom Windows
application to format calendar entries to be delivered by email. Shared Public Folder Calendars
were widely used by different groups and departments in the firm and people generally liked the
daily email format (rather than having to manually view several calendars every day in Outlook).

When I started a job at a new company I wasn’t really surprised to see Exchange Public Folder
calendars used for similar shared event scheduling to coordinate vacation time, software releases,
and on-call support. I quickly missed the convenience of receiving a daily email so I set out to
recreate the report in a script.

**A Script to Format Outlook Calendars**

*formatCalendarReport.wsf* is a Windows Scripting Host (WSH) application that uses the VBScript
programming language to access Exchange Public Folders and print calendar events in a format similar
to the listing above. The script can be run from the Windows CMD prompt using the WSH cscript command.
If you’re using a current Service Pack version of Windows XP you should find that WSH and the cscript
command are already installed on your system, so download the script to your computer and try to
produce usage instructions by running it with the /? parameter as follows:

```
C:\>cscript /nologo formatCalendarReport.wsf /?

Prints a report of events in an Outlook calendar for a period of time.

Usage: formatCalendarReport.wsf /calendar:value [/numdays:value]
          [/emptyevents[+|-]] [/emptybody[+|-]]

Options:
calendar    : pathname of the Outlook Calendar
numdays     : number of days to process in the report
emptyevents : include dates without events, default emptyevents+
emptybody   : include empty body fields, default emptybody+

Example:

   formatCalendarReport.wsf /calendar:"Personal Folders/Calendar"
      /numdays:14 /emptyevents+ /emptybody+
```

The WSH script and its output include a few enhancements compared to the version that I remember.
First, when the script runs it produces greeting and closing lines that show the start and end
times of the command:

```
This is formatCalendarReport.wsf running on 11/9/2008 11:06:30 AM...
...
This is formatCalendarReport.wsf ending on 11/9/2008 11:06:31 AM...
```

And the runtime parameters are printed at the top of the formatted report:

```
        Calendar: Personal Folders/Calendar...
   Report period: 11/11/2008 through 11/13/2008 (4 days)...
```

If there are no scheduled events for a given date, the report will include a message like this:

```
Thursday, 11/13/2008:
        +-------------------------------------------------------------+
         No calendar events for today.
        +-------------------------------------------------------------+
```

And if multiple events are scheduled, the subject, body, the start, and the end times of each
event are printed as follows:

```
Tuesday, 11/11/2008:
        +-------------------------------------------------------------+
         Subject: Sal - Vacation
            Body:
            From: 11/10/2008   To: 11/12/2008
        +-------------------------------------------------------------+
         Subject: Jim - Working from home
            Body: Please contact me at 212.555.1212 as needed.
            From: 11/11/2008   To: 11/11/2008
        +-------------------------------------------------------------+
```

Notice that calendar appointments which are entered as “All day events” show their start and
end date ranges without times (as in the two events immediately above). However, calendar appointments
which have specific start and end times (for example, events that are not defined as “All day events”)
show the time of day details as in this example:

```
+-------------------------------------------------------------+
 Subject: Rob - Cisco seminar
    Body: I will be out of the office in the afternoon.
    From: 11/12/2008 12:00:00 PM   To: 11/12/2008 5:00:00 PM
+-------------------------------------------------------------+
```

**An Example Command**

Here’s how to run it on your system. The script provides command line options to customize the format
of the report. Below is a runtime version of the command that uses all the currently supported options:

```
cscript /nologo formatCalendarReport.wsf
        /calendar:"Personal Folders\Calendar" /numdays:7
        /emptyevents- /emptybody-
```

`cscript` is a Windows command that invokes WSH to run a .wsf script, and the /nologo option tells WSH
to omit a banner message (which reduces some clutter from the output). formatCalendarReport.wsf is the
name of the script.

The Microsoft Exchange Calendar to read is specified by the /calendar option. The calendar is a
double-quoted pathname within the Outlook folder list. For example:

```
/calendar:"Personal Folders\Calendar"
```

selects a calendar under Outlook’s Personal Folders list. Alternatively, a calendar called Vacations
in the Exchange Public Folders list might be accessed like this:

```
/calendar:"Public Folders\All Public Folders\Vacations"
```

The *formatCalendarReport.wsf* script starts from the current date and (by default) creates a report
for the next 14 days. In the example above, the /numdays option is used to create a shorter report
for seven days (starting from today’s date).

If a day in the calendar doesn’t have any scheduled events, a line item is added to the report that
reads “No calendar events for today.” To exclude dates without events, use the /emptyevents- parameter.
(By default /emptyevents+ is implied.)

Similarly, a “Body:” line item is included by default, so add /emptybody- if you would like to exclude
body fields from the report that don’t contain any data.

**System Requirements for the Script**

The script requires Windows Scripting Host. If you are running a modern version of Windows
(Windows XP, Vista, or Windows Server 2003/2008) you’ll find WSH and the cscript command included
in your system. If you are running a different or older version of Windows, or if your system doesn’t
have the cscript command, check the [Microsoft Scripting FAQ][MicrosoftScriptingFAQURL] to determine
how to install WSH on your system.

The script also requires Outlook Redemption. Outlook Redemption facilitates access to Outlook and
Exchange data via scripting languages without the Windows security layer continually confirming access
through a pop-up window. Outlook Redemption can be downloaded from the following Web site:
[http://www.dimastr.com/redemption][OutlookRedemptionURL]. Follow the instructions in the readme.txt
file to install Redemption on your system to run the *formatCalendarReport.wsf* script.

A connection to Outlook (for a mailbox in Personal Folders) or an Exchange Server (for a mailbox stored
in Exchange) is required. The script connects using the privileges of current user. If you setup the
script to run as a Windows Scheduled Task remember to configure logon information (both username and
password) for the proper connection to your mailbox.

**Additional Comments and Follow-up**

A .txt filetype has been added to the name of the script to avoid problems with virus scanners or
firewalls when you download the file. Be sure to remove the .txt extension after the download completes
so you won’t have a problem running it on your computer.

*formatCalendarReport.wsf* will format a calendar into a text listing but it doesn’t email the report.
A follow-up post will demonstrate how a separate Windows tool can be used to email the completed report
to a list of addresses.

Please note that the script was only tested on a Windows XP system running Service Pack 3.

In the meantime, if you download the script and try it on your system, and please post a comment so we
can hear something about your experiences running it.

[MicrosoftScriptingFAQURL]: https://www.microsoft.com/technet/scriptcenter/scrptfaq.mspx
[OutlookRedemptionURL]: https://www.dimastr.com/redemption
