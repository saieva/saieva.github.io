---
layout: post
title:  "Move Messages to Folders with Outlook VBA"
date:   2010-03-27 06:00:00 -0400
categories: Programming
---
Let’s say your email management approach is to save important messages to a folder other than your Outlook Inbox.
You might save messages from outside vendors to a folder named “Vendor Documents” or messages related to
corporate policy to a “Policies” folder. Here’s an Outlook VBA macro that helps to file those messages from your
Inbox in a single click:

```
Sub MoveToFolder(folderName)

 mailboxNameString = "Mailbox - Firstname Lastname"

 Dim olApp As New Outlook.Application
 Dim olNameSpace As Outlook.NameSpace
 Dim olCurrExplorer As Outlook.Explorer
 Dim olCurrSelection As Outlook.Selection

 Dim olDestFolder As Outlook.MAPIFolder
 Dim olCurrMailItem As MailItem
 Dim m As Integer

 Set olNameSpace = olApp.GetNamespace("MAPI")
 Set olCurrExplorer = olApp.ActiveExplorer
 Set olCurrSelection = olCurrExplorer.Selection

 Set olDestFolder = olNameSpace.Folders(mailboxNameString).Folders(folderName)

 For m = 1 To  olCurrSelection.Count
    Set olCurrMailItem = olCurrSelection.Item(m)
    Debug.Print "[" & Date & " " & Time & "] moving #" & m & _
                ": folder = " & folderName & _
                "; subject = " & olCurrMailItem.Subject & "..."
    olCurrMailItem.Move olDestFolder
 Next m

End Sub
```

Some customizations are necessary to make this all work for your email system.
First, copy-and-paste the program to the Visual Basic Editor in Outlook (which you can open with Alt-F11).
You’ll need to create a new VB Module (use the Insert->Module menu choice) to hold the macro if you don’t
already have custom Outlook macros defined.

Second, the `mailboxNameString` variable needs to be edited for the name of your Exchange mailbox;
you can usually find the name of your mailbox on the left side of the Outlook window at the top, it starts
with “Mailbox” like in the example above.

Third, you’ll need to add other VBA macros which call the `MoveToFolder()` macro with the correct folder name
passed as  a parameter. For example, we might create two additional macros one each for the Vendor Documents
and the Policies folders; the macro for Vendor Documents might be called `MTV()` and the Policies folder macro `MTP()`:

```
Sub MTV()
 MoveToFolder ("Vendor Documents")
End Sub

Sub MTP()
 MoveToFolder ("Policies")
End Sub
```

(Note, the destination folders should already exist in your Outlook folder list.)

The last step is to assign the macros to Outlook toolbar buttons so you can move the selected messages in
your Inbox to the appropriate folders with a click. If you’re managing multiple destination folders, and you
created multiple macros (one for each folder), you can also create a new toolbar to organize all your
`MoveToFolder()` macros. Here’s a Microsoft Knowledge Base article that describes how to assign Outlook VBA
macros to toolbar buttons: [How to Assign a Macro to a Toolbar Button][MicrosoftKBURL].

The `MoveToFolder()` macro will work on a single selected message, or a group of messages selected in a sequential
range (with a Shift-Click), or a non-sequential range (with Ctrl-Clicks). Also, an activity message will be
written to the VBA Editor’s Immediate Window (which is opened with Ctrl-G) so you can go-back and view
`MoveToFolder()` activity. A total win for email convenience, give it a try.

[MicrosoftKBURL]: https://support.microsoft.com/kb/292797
