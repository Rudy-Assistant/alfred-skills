---
name: hotkey-creator
description: |
  Create custom keyboard shortcuts using AutoHotkey v2 scripts. Hotkeys: app launchers, text expansion, window management (move/resize/snap), clipboard enhancement, quick actions (screenshot, open terminal). Generate .ahk files and set scripts to run on startup. Includes a starter collection of proven hotkeys for common workflows.
---

# Hotkey Creator â Keyboard Shortcuts for Everything

Automate your workflow with custom keyboard shortcuts. AutoHotkey v2 lets you trigger scripts, launch apps, expand text snippets, move windows, and more â all from a single keypress.

## Why AutoHotkey?

- **App launchers**: Win+A = open Audacity, Win+V = open Visual Studio Code
- **Text expansion**: Type `;;email` â expands to your full email address
- **Window management**: Win+Shift+Left = snap window to left half, Win+Shift+Right = right half
- **Quick actions**: Win+Shift+S = screenshot region (faster than the default)
- **System control**: Custom mute/unmute, volume shortcuts, toggle wireless
- **Mouse enhancement**: Remap mouse buttons, change cursor speed on the fly

## Setup

### 1. Install AutoHotkey v2

Download from https://www.autohotkey.com/ (v2.0 or later, not v1).

Or via PowerShell (admin):
```powershell
winget install AutoHotkey.AutoHotkey
```

### 2. Create Your Script

Create a file named `MyHotkeys.ahk` in a safe location like:
```
C:\Users\ccimi\Scripts\MyHotkeys.ahk
```

Paste one of the recipes below into the file.

### 3. Run the Script

Double-click the `.ahk` file to start it. A tray icon appears. Right-click the tray icon â "Edit This Script" to make changes.

### 4. Run on Startup (Optional)

To auto-start your hotkeys every time you log in:

**Method A: Task Scheduler**
Create a scheduled task (see task-scheduler skill):
```powershell
$scriptPath = "C:\Users\ccimi\Scripts\MyHotkeys.ahk"
$trigger = New-ScheduledTaskTrigger -AtLogOn
$action = New-ScheduledTaskAction -Execute "C:\Program Files\AutoHotkey\AutoHotkey.exe" -Argument "`"$scriptPath`""
$principal = New-ScheduledTaskPrincipal -UserID (whoami) -LogonType Interactive
Register-ScheduledTask -TaskName "AutoHotkeys" -Trigger $trigger -Action $action -Principal $principal -Force
```

**Method B: Startup Folder**
Create a shortcut to your `.ahk` file and place it in:
```
C:\Users\ccimi\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup\
```

**Method C: Registry (admin)**
```powershell
$scriptPath = "C:\Users\ccimi\Scripts\MyHotkeys.ahk"
$regPath = "HKCU:\Software\Microsoft\Windows\CurrentVersion\Run"
New-ItemProperty -Path $regPath -Name "AutoHotkeys" -Value "`"C:\Program Files\AutoHotkey\AutoHotkey.exe`" `"$scriptPath`"" -Force
```

## Hotkey Syntax (AutoHotkey v2)

```
Key combinations:
#   = Windows key (Win)
^   = Ctrl
+   = Shift
!   = Alt
&   = Custom combination (e.g., F1 & F2 = F1 pressed then F2)

Examples:
#a          = Win+A
#!a         = Win+Alt+A
^+a         = Ctrl+Shift+A
!+F2        = Alt+Shift+F2
```

Function syntax:
```autohotkey
#a::       ; Win+A
{
    Run "notepad.exe"
}

#e::       ; Win+E
{
    Send "hello@example.com"
}
```

## Starter Hotkey Collection

Here's a ready-to-use script with common hotkeys:

```autohotkey
; MyHotkeys.ahk - Starter collection

; ââ App Launchers ââ

#e::       ; Win+E = open File Explorer
{
    Run "explorer.exe"
}

#c::       ; Win+C = open VS Code
{
    Run "code.exe"
}

#p::       ; Win+P = open Notepad
{
    Run "notepad.exe"
}

#t::       ; Win+T = open Terminal
{
    Run "powershell.exe"
}

#b::       ; Win+B = open browser
{
    Run "msedge.exe"
}

; ââ Text Expansion (Snippets) ââ

::;;email::
{
    Send "ccimi@example.com"
}

::;;phone::
{
    Send "555-0123"
}

::;;date::
{
    Send Format(A_Now, "yyyy-MM-dd")
}

::;;time::
{
    Send Format(A_Now, "HH:mm:ss")
}

::;;sig::
{
    Send "Best regards,{Enter}John Doe"
}

; ââ Window Management ââ

#!Left::   ; Win+Alt+Left = snap to left half
{
    ActiveWindow := WinGetID("A")
    WinGetPos(, , &ScreenWidth, &ScreenHeight, "A")
    WinMove(0, 0, ScreenWidth // 2, ScreenHeight, ActiveWindow)
}

#!Right::  ; Win+Alt+Right = snap to right half
{
    ActiveWindow := WinGetID("A")
    WinGetPos(, , &ScreenWidth, &ScreenHeight, "A")
    WinMove(ScreenWidth // 2, 0, ScreenWidth // 2, ScreenHeight, ActiveWindow)
}

#!Up::     ; Win+Alt+Up = maximize
{
    WinMaximize("A")
}

#!Down::   ; Win+Alt+Down = restore/minimize
{
    WinRestore("A")
}

; ââ Quick Actions ââ

#!s::      ; Win+Alt+S = screenshot region
{
    Run "snippingtool.exe"
}

#!v::      ; Win+Alt+V = paste from clipboard as plain text
{
    text := A_Clipboard
    A_Clipboard := ""
    Sleep 100
    Send text
}

#!l::      ; Win+Alt+L = lock screen
{
    DllCall("LockWorkStation")
}

; ââ Volume & Media ââ

F24 & Up::     ; F24+Up = volume up
{
    SoundSetVolume("+5")
}

F24 & Down::   ; F24+Down = volume down
{
    SoundSetVolume("-5")
}

F24 & Space::  ; F24+Space = play/pause
{
    Send "{Media_Play_Pause}"
}

; ââ System Control ââ

#!m::      ; Win+Alt+M = toggle mute
{
    SoundSetMute(-1)  ; Toggle mute
}

#!r::      ; Win+Alt+R = restart explorer (fixes frozen taskbar)
{
    Run 'powershell.exe -Command "Get-Process explorer | Stop-Process; Start-Process explorer.exe"'
}

; ââ Reload hotkeys ââ

^!r::      ; Ctrl+Alt+R = reload this script (for debugging)
{
    Reload
}
```

## Recipes by Category

### App Launchers

```autohotkey
; Win+A = Audacity
#a::
{
    Run "C:\Program Files\Audacity\Audacity.exe"
}

; Win+G = GIMP
#g::
{
    Run "C:\Program Files\GIMP 2\bin\gimp-2.10.exe"
}

; Win+S = Sublime Text (if installed)
#s::
{
    Run "C:\Program Files\Sublime Text\sublime_text.exe"
}

; Win+D = DevTools (Chrome)
#d::
{
    Send "^+i"  ; Opens DevTools in active Chrome window
}
```

### Text Expansion (Snippets)

```autohotkey
; Common responses
::;;yes::
{
    Send "Yes, I can do that."
}

::;;no::
{
    Send "No, I'm not available."
}

::;;fyi::
{
    Send "FYI: "
}

; Code snippets
::;;pyheader::
{
    Send "#!/usr/bin/env python3{Enter}"""" + Chr(34) + """Docstring here.""" + Chr(34) + "{Enter}" + Chr(34) + Chr(34) + Chr(34) + "{Enter}{Enter}"
}

; Credentials (use with caution - for test accounts only)
::;;testpass::
{
    Send "TestPassword123!"
}
```

### Window Management

```autohotkey
; Snap to corners
#!1::      ; Win+Alt+1 = top-left quarter
{
    WinMove(0, 0, A_ScreenWidth // 2, A_ScreenHeight // 2, "A")
}

#!2::      ; Win+Alt+2 = top-right quarter
{
    WinMove(A_ScreenWidth // 2, 0, A_ScreenWidth // 2, A_ScreenHeight // 2, "A")
}

#!3::      ; Win+Alt+3 = bottom-left quarter
{
    WinMove(0, A_ScreenHeight // 2, A_ScreenWidth // 2, A_ScreenHeight // 2, "A")
}

#!4::      ; Win+Alt+4 = bottom-right quarter
{
    WinMove(A_ScreenWidth // 2, A_ScreenHeight // 2, A_ScreenWidth // 2, A_ScreenHeight // 2, "A")
}

; Resize by 10%
^+Right::  ; Ctrl+Shift+Right = grow right
{
    WinGetPos(&x, &y, &w, &h, "A")
    WinMove(x, y, w + 50, h, "A")
}

^+Left::   ; Ctrl+Shift+Left = shrink right
{
    WinGetPos(&x, &y, &w, &h, "A")
    WinMove(x, y, w - 50, h, "A")
}

; Always on top toggle
^!t::      ; Ctrl+Alt+T = toggle always-on-top
{
    WinSetAlwaysOnTop(-1, "A")
}
```

### Clipboard Enhancement

```autohotkey
; Paste without formatting
^!v::
{
    text := A_Clipboard
    A_Clipboard := ""
    Sleep 100
    Send(text)
}

; Copy + show confirmation
^c::
{
    Send "^c"
    Sleep 100
    ToolTip("Copied!")
    SetTimer(ToolTip, 1000)
}

; Copy current time to clipboard
!+t::
{
    A_Clipboard := Format(A_Now, "yyyy-MM-dd HH:mm:ss")
    ToolTip("Copied: " . A_Clipboard)
    SetTimer(ToolTip, 2000)
}
```

### Quick Screenshots

```autohotkey
; Win+Shift+S = region screenshot (system default)
#!s::
{
    Run "snippingtool.exe"
}

; Win+Shift+W = fullscreen screenshot (save to Desktop)
#!w::
{
    filename := "C:\Users\ccimi\Desktop\screenshot_" . Format(A_Now, "yyyy-MM-dd_HH-mm-ss") . ".png"
    DllCall("PrintWindow", "Ptr", -1, "Ptr", CreateDC(), "Int", 0)  ; Simplified
    ToolTip("Screenshot saved: " . filename)
    SetTimer(ToolTip, 2000)
}
```

## Debugging

**Test a single hotkey:**
1. Create a new `.ahk` file with just that hotkey
2. Double-click to run it
3. Press the hotkey and check if it works

**View AutoHotkey error log:**
Right-click the tray icon â "Show Error". This opens the error output window.

**Reload the script without restarting:**
Add `Ctrl+Alt+R` hotkey (see starter collection) and press it after editing.

**Hotkey not working?**
- Check for conflicting hotkeys (Windows, other apps)
- Try a different key combination
- Verify the syntax (colons, braces, semicolons)
- Make sure the `.ahk` file is actually running (check tray icon)

## Safety Notes

- **Passwords in scripts**: Never store real passwords in a text hotkey. Use test accounts only.
- **Text expansion collisions**: `::email::` will auto-expand even in the middle of words. Use longer triggers like `::;;email::`.
- **Window management**: Some apps don't respond well to programmatic window moves. Test on the target app first.
- **System hotkeys**: Some Win+ combinations are reserved by Windows. If a hotkey doesn't work, try a different key.
- **Admin scripts**: If you need a hotkey to run as admin, the entire `AutoHotkey.exe` must be run as admin.

## Resources

- AutoHotkey v2 docs: https://www.autohotkey.com/docs/v2/
- Built-in functions: `Run`, `Send`, `WinMove`, `WinMaximize`, `DllCall`, `ToolTip`
- Debug: Use `ToolTip()` to display temporary on-screen messages
