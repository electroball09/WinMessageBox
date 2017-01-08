# WinMessageBox
Wrapper for showing win32 message boxes  
[Image Example](http://i.imgur.com/jWD109u.png)

Please make sure to read the NOTES section at the bottom of the page.

For full MSDN documentation on message boxes, [see here](https://msdn.microsoft.com/en-us/library/ms645505)

##Usage
To use, simply call any of the following forms of `WinMessageBox.Show()` with any of the parameters described below
```C#
  - WinMessageBox.Show(string messageText)
  - WinMessageBox.Show(string messageText, string messageCaption)
  - WinMessageBox.Show(string messageText, WinMessageBoxFlags flags)
  - WinMessageBox.Show(string messageText, string messageCaption, WinMessageBoxFlags flags)
  - WinMessageBox.Show(string messageText, string messageCaption, WinMessageBoxFlags flags, bool captureFocus)
```  
See RETURNS below for information on getting the result of the interaction

####PARAMETERS
[Reference Image](http://i.imgur.com/QOxuvOt.png)  
1. `string messageText` - The text to display in the body of the message box.  Accepts common delimiters (e.g. "\n")  
2. `string messageCaption` - The text to display in the header of the message box  
3. `WinMessageBoxFlags flags` - Flags to modify the behavior and appearance of the message box. (see FLAGS below)  
4. `bool captureFocus` - Whether or not the user can switch the application to foreground while the message box is shown (see NOTES at the bottom of the page)  

####FLAGS
To use, simply bitwise OR any combination of the following types of flags:  
  1. `"btn" prefix` - Modifies the buttons shown on the message box.  Default is `btnOkay`  
    - `btnOkay` - [Image](http://i.imgur.com/SVAmG44.png)
    - `btnOkayCancel` - [Image](http://i.imgur.com/QJleSYG.png)
    - `btnAbortRetryIgnore` - [Image](http://i.imgur.com/9JoqU7N.png)
    - `btnYesNo` - [Image](http://i.imgur.com/SaLMxHm.png)
    - `btnYesNoCancel` - [Image](http://i.imgur.com/TaRSE0E.png)
    - `btnRetryCancel` - [Image](http://i.imgur.com/QOZu8eq.png)
    - `btnCancelTryContinue` - [Image](http://i.imgur.com/8dpV5fJ.png)
    - `btnHelp` - [Image](http://i.imgur.com/Zknzps1.png) (see NOTES at the bottom of the page)
  2. `"icon" prefix` - Modifies the icon shown next to the body of the message box.  Default is `iconNone`  
    - `iconNone` - [Does not display an icon](http://i.imgur.com/8CKbyOE.png) and has no chime
    - `iconExclamation / iconWarning` - [Displays yellow "!" icon](http://i.imgur.com/VnVNA8V.png) and plays the Windows "Exclamation" chime
    - `iconInformation / iconAsterisk` - [Displays blue "i" icon](http://i.imgur.com/0ZUuvZi.png) and plays the Windows "Exclamation" chime
    - `iconQuestion` - [Displays blue "?" icon](http://i.imgur.com/nOFAPsI.png) and has no chime
    - `iconStop / iconError / iconHand` - [Displays red "X" icon](http://i.imgur.com/vuxmnJA.png) and plays the Windows "Error" chime

  3. `"def" prefix` - Modifies the default selected button.  Default is `defButton1`
    - `defButton1` - First Button
    - `defButton2` - Second Button
    - `defButton3` - Third Button
    - `defButton4` - Fourth Button
  4. `"modal" prefix` - Specifies message box modality.  Default is `modalAppl`
    - `modalAppl` - The user must respond to the message box before any work can be resumed on the parent window
    - `modalSystem` - Same as `modalAppl` but the message box will always be on top
    - `modalTask` - The user must respond to the message box before any work can be resumed on the calling thread  

For instance, to display a message box that looks like [this](http://i.imgur.com/Du93wyx.png), you would write

```C#
  WinMessageBox.Show(
    "Hi, I'm a message",  
    "Caption",  
    WinMessageBoxFlags.btnOkayCancel | WinMessageBoxFlags.iconExclamation  
    );
```

NOTE:
  - **Using more than one of the same prefix flag will likely not have intended results**
    
####RETURNS

`Show()` returns a `WinMessageBoxResult` that is the result of the user interaction with the message box  
  - `Error` - An error occured within the Win32 call  
  - `Ok` - The user pressed the "Okay" button  
  - `Cancel` - The user pressed the "Cancel" button (see NOTE below)  
  - `Abort` - The user pressed the "Abort" button  
  - `Retry` - The user pressed the "Retry" button  
  - `Ignore` - The user pressed the "Ignore" button  
  - `Yes` - The user pressed the "Yes" button  
  - `No` - The user pressed the "No" button  
  - `TryAgain` - The user pressed the "Try Again" button  
  - `Continue` - The user pressed the "Continue" button  

For instance, to see whether a user [likes pumpkin pie or not](http://i.imgur.com/PQBfgHu.png), you could write this:  

```C#
  WinMessageBoxResult result = WinMessageBox.Show(
                                 "Do you like pumpkin pie?",  
                                 "Question", 
                                 WinMessageBoxFlags.btnYesNo | WinMessageBoxFlags.iconQuestion
                                 );
  if (result == WinMessageBoxResult.Yes)
      WinMessageBox.Show("That's great!");
```

NOTE:
  - When the message box has a "Cancel" button, it will return `WinMessageBoxResult.Cancel` if the user either:  
    - Presses the "Cancel" button
    - Presses their ESC key
    - Clicks the close button in the top right


##NOTES
  - **USING THIS WILL LOCK UP YOUR WINDOW, WHETHER IT'S IN THE EDITOR OR THE PLAYER, UNTIL THE MESSAGE BOX IS GONE**  
    - **ONLY USE THIS FOR CRITICAL INFORMATION**  
    - If you specify the `captureFocus` parameter to be true, the program will not permit you to switch the application to the foreground until the message box is gone.
      - If `captureFocus` is false, which it is by default, you will be able to bring the main window to the foreground, but **it will still be unresponsive until the message box is gone**  
    - The const `DEFAULT_CAPTURE_FOCUS`, which is `false` by default, is used to determine `captureFocus` on any of the methods that don't take the `captureFocus` parameter.
    - **There are a few $2 Unity-based message box systems on the [asset store](https://www.assetstore.unity3d.com/en/#!/search/page=1/sortby=relevance/query=MessageBox)**, if you want something that won't pause the application

####Using `btnHelp`
  - If you specify `WinMessageBoxFlags.btnHelp` as the button, you will need to handle a [`WM_HELP`](https://msdn.microsoft.com/en-us/library/bb774305) message from Windows if you want to do something when the user clicks "Help"
    - The user can still click "Okay" and it will return `WinMessageBoxResult.Ok`
