<div align="center">

## Hiding Processes


</div>

### Description

The object of this short tutorial is to demonstrate how processes can be hidden from the windows, its taskbar, and its task manager. It also contains my code to hide from the task manager on 9x machines without crashing when run on NT, or XP.
 
### More Info
 


<span>             |<span>
---                |---
**Submitted On**   |
**By**             |[Exercia](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByAuthor/exercia.md)
**Level**          |Intermediate
**User Rating**    |4.5 (45 globes from 10 users)
**Compatibility**  |Delphi 5
**Category**       |[Miscellaneous](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByCategory/miscellaneous__7-1.md)
**World**          |[Delphi](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByWorld/delphi.md)
**Archive File**   |[](https://github.com/Planet-Source-Code/exercia-hiding-processes__7-764/archive/master.zip)





### Source Code

```
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.0 Transitional//EN">
<HTML><HEAD>
<META http-equiv=Content-Type content="text/html; charset=unicode">
<META content="MSHTML 6.00.2600.0" name=GENERATOR></HEAD>
<BODY>
<P><EM>[Introduction]<br></EM>
<br><FONT size=2><EM><FONT
size=3>      </FONT></EM>
	Normally I don't write tutorials or submit code. I'm no Jerome ;p But trying to find good resources for *truely* hiding programs from windows was such a task for me that I figured I would share what knowledge I've found. If you are interested in hiding your program from windows and the task list or just curious about the different ways you can do so, then this file is for you!<br></FONT>
<br>
<br><EM>[Hiding From Task Manager
#1]</EM>
 <br>
<br><FONT size=2><EM><FONT
size=3>      </FONT></EM>
	The main way that everyone is telling you to do this is using code like this:<br>
<br><FONT color=seagreen>
 const<br>
 RSPSIMPLESERVICE = 1;<br>
 RSPUNREGISTERSERVICE = 0;<br>
 function RegisterServiceProcess (dwProcessID, dwType: DWord) : DWord;<br>
 stdcall; external 'KERNEL32.DLL';</FONT>
  <br>
<br><FONT
color=seagreen>RegisterServiceProcess(GetCurrentProcessID,
RSPSIMPLESERVICE);</FONT></FONT></P>
<P><FONT size=2><EM><FONT size=3>     
</FONT></EM>
	Simple enough, right? It seems so. Unfortunately when you try using this code under any OS except for a 9x machine, it will crash the entire program. It crashed mine even before I ever called the function! When I found this out I was frustrated because I wanted my bot to work universally on all OS's. Because of this, I finally found a way to make it work and wrote universally compatible code. If your code is meant only for Windows 9x machines, then there would be nothing wrong with using the previous code. If not, read on..<br></FONT>
<br>
<br><EM>[Get Operating System]</EM>
 <br>
<br><FONT size=2><EM><FONT
size=3>      </FONT></EM>In order to make the code that
follows work, we must have a variable that will find the operating system. To do
this, I have found (and slightly modified) the following code:</FONT>
	        <br>
<br><FONT size=2><FONT
color=seagreen>
 var<br>
  // Global OS vars<br>
  VersionInfo: TOSVersionInfo;<br>
  Platform: string;<br>
  MajorVersion,MinorVersion,Build: DWORD;<br>
<br>
 procedure GetOSVersion;<br>
 begin<br>
  VersionInfo.dwOSVersionInfoSize := SizeOf(VersionInfo);<br>
  GetVersionEx(VersionInfo);<br>
<br>
  with VersionInfo do<br>
  begin<br>
  case dwPlatformId of<br>
  VER_PLATFORM_WIN32s  : Platform := '3.1';<br>
  VER_PLATFORM_WIN32_WINDOWS : Platform := '98';<br>
  VER_PLATFORM_WIN32_NT  :<br>
  begin<br>
   Case dwMajorVersion of<br>
   5 : Platform := '2000/NT';<br>
   else<br>
   Platform :=
    'NT';<br>
   end;<br>
   if
   dwBuildNumber > = 2500 then Platform := 'XP'<br>
  end;<br>
  end;<br>
<br>
  MajorVersion := dwMajorVersion;<br>
  MinorVersion := dwMinorVersion;<br>
  Build := dwBuildNumber;<br>
  end;<br>
 end;</FONT>
 <br></FONT>
<br>
<br><EM>
[Hiding From Task Manager #2]<br></EM>
<br><FONT size=2><EM><FONT
size=3>      </FONT></EM>
	Now that we have a function to check the OS version we can add my universally compatible code to hide from 9x machines. First we need to add the type TReg before your implementation:<br>
<br><FONT color=seagreen>
 type<br>
  TReg =
  function (dwProcessID, dwType: DWord) : DWord;  <br>
<br><FONT color=black><EM><FONT
size=3>      </FONT></EM>Now for the
code. In this example we will assume that the following code is put in a form's
FormCreate event. Because that's most likely where you will want to put
it:</FONT>
	        <br>
<br><FONT color=seagreen>
 var<br>
  RegisterServiceProcess: TReg;<br>
 begin<br>
<br>
 // Determine the operating system<br>
 GetOsVersion;<br>
<br>
 // Check to see if OS is 9x<br>
 if Platform = '98' then begin<br>
  Handle :=
  LoadLibrary('KERNEL32.DLL');<br>
  if
  Handle <> 0 then begin<br>
  @RegisterServiceProcess := GetProcAddress(Handle, 'RegisterServiceProcess');<br>
  if
  @RegisterServiceProcess <> nil then<br>
   RegisterServiceProcess(GetCurrentProcessID, RSPSIMPLESERVICE);<br>
  end;<br>
  FreeLibrary(Handle);<br>
 end;<br>
 end;</FONT>
 <br>
<br>
<br><FONT color=black><FONT size=3><EM>[Hiding From
NT]</EM></FONT>
 <br>
<br><EM><FONT
size=3>      </FONT></EM>
	This is a difficult task. NT boxes are not easily tricked. There is only one simple way that I've found to do this, and I believe it only works on NT 4.0. Go to your Project unit (IE. Project1) And find where it initializes and sets the application title. Replace it with this code:<br>
<br><FONT color=seagreen>
 Application.Initialize;<br>
 Application.Title:=
 '';<br></FONT>
<br><EM><FONT size=3>     
</FONT></EM>
	The version of NT that this works with (NT 4.0?) will not show your process in the manager because it displays processes by their titles, and your program is now running without one!<br>
<br>
<br></FONT><FONT color=black><EM><FONT
size=3>[Hiding from taskbar & windows]</FONT> <br></EM>
<br></FONT><FONT
size=2><FONT color=black><EM><FONT size=3>      </FONT></EM>
	The following is just a little piece of code I wrote to ensure that my form is unseen. It is commented so I will not explain it here:<br></FONT>
<br><FONT color=seagreen>
 procedure HideMe();<br>
 begin<br>
  // Make sure the form is out of sight<br>
  form1.Left := 99999;<br>
  form1.Top := 99999;<br>
<br>
  // Make form dissapear<br>
  form1.Visible := false;<br>
  Application.Minimize;<br>
<br>
  // Hide window entirely (dissapears from task bar!) <br>
  ShowWindow(Application.Handle, SW_HIDE);<br>
  SetWindowLong(Application.Handle, GWL_EXSTYLE,<br>
  GetWindowLong(Application.Handle, GWL_EXSTYLE)<br>
  or WS_EX_TOOLWINDOW );<br>
 end;</FONT>
 <br></FONT>
<br>
<br><FONT size=2><FONT
size=3 color=black><EM>[Note]</EM></FONT></FONT></P>
<P><FONT size=2><FONT color=black><EM><FONT size=3>      </FONT></EM>
	Some code used in this tutorial was based on other's code. Thanks to those who wrote the original code.<br>
<br><FONT
size=3><EM>[Conclusion]</EM></FONT>
<br>
<br><EM><FONT size=3>     
</FONT></EM>Hopefully
some of this has been helpful. I have no good HTML editor on this computer so
this isn't a nice flashy tutorial. Good luck in your programming! I always am
interested in hearing about new methods and techniques, so if you would like to
share some feel free to drop me a line. Also, if you have a question with
anything here or something else let me know and I'll do my best to help. Happy
c0d1ng!</FONT>
  <FONT color=mediumslateblue>
	                    ;)<br></FONT>
<br><FONT
color=tomato><EM>-NaHeMiA-</EM></FONT></FONT></P></FONT></FONT></BODY></HTML>
```

