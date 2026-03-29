---
name: file-organizer
description: |
  Auto-sort files into folders by type, date, project, or custom rules. Generate PowerShell scripts with preview mode to show what would happen before executing. Support recursive sorting, collision handling (rename vs skip vs overwrite), and undo operations.
---

# File Organizer â Automated File Sorting

Automatically organize files into logical folder structures based on type, creation/modification date, file size, custom patterns, or project rules. Each operation generates a preview showing exactly what will happen before any files are moved.

## Choosing Your Strategy

### By File Type
Group files into folders like `Images\`, `Documents\`, `Videos\`, `Archives\` based on extension.

### By Date
Organize into `2026\03\28\`, `2026-Q1\`, or `2026-03 March\` based on file creation/modification date.

### By Project
Move files into project folders: `ProjectA\`, `ProjectB\`, `ProjectC\` based on filename patterns.

### By Size
Sort into `Small (<1MB)`, `Medium (1-100MB)`, `Large (>100MB)` folders.

### By Custom Rules
Use regex patterns or filename conditions to classify files.

## Common Recipes

### Recipe 1: Sort Downloads by File Type

```powershell
# Sort Downloads folder by file type into organized subfolders
# Run in PowerShell: right-click Start â Terminal

$sourcePath = "C:\Users\ccimi\Downloads"
$preview = $true  # Set to $false to execute

$extensionMap = @{
    'Images' = @('.jpg', '.jpeg', '.png', '.gif', '.bmp', '.webp', '.heic')
    'Documents' = @('.pdf', '.doc', '.docx', '.xls', '.xlsx', '.txt', '.pptx')
    'Videos' = @('.mp4', '.mkv', '.avi', '.mov', '.wmv', '.flv')
    'Audio' = @('.mp3', '.wav', '.aac', '.flac', '.m4a', '.wma')
    'Archives' = @('.zip', '.7z', '.rar', '.tar', '.gz')
    'Executables' = @('.exe', '.msi', '.bat', '.ps1')
}

Write-Host "Scanning $sourcePath..." -ForegroundColor Cyan
$files = Get-ChildItem $sourcePath -File -Depth 1

if ($files.Count -eq 0) {
    Write-Host "No files found." -ForegroundColor Yellow
    exit 0
}

$moves = @()
foreach ($file in $files) {
    $ext = $file.Extension.ToLower()
    $category = "Other"

    foreach ($cat in $extensionMap.Keys) {
        if ($ext -in $extensionMap[$cat]) {
            $category = $cat
            break
        }
    }

    $destFolder = Join-Path $sourcePath $category
    $destPath = Join-Path $destFolder $file.Name

    if ($destPath -ne $file.FullName) {
        $moves += [PSCustomObject]@{
            "File" = $file.Name
            "From" = "Downloads"
            "To" = $category
            "Size" = [math]::Round($file.Length / 1MB, 2)
        }
    }
}

Write-Host "`nPreview: $($moves.Count) files to organize" -ForegroundColor Cyan
$moves | Format-Table File, From, To, Size -AutoSize

if ($preview) {
    Write-Host "`nPreview mode enabled. Set `$preview = `$false to execute." -ForegroundColor Yellow
    exit 0
}

$confirm = Read-Host "`nProceed? (yes/no)"
if ($confirm -notin @("yes", "y")) {
    Write-Host "Cancelled." -ForegroundColor Yellow
    exit 0
}

foreach ($move in $moves) {
    try {
        $category = $move.To
        $destFolder = Join-Path $sourcePath $category
        New-Item -ItemType Directory -Path $destFolder -Force | Out-Null
        Move-Item -Path (Join-Path $sourcePath $move.File) -Destination (Join-Path $destFolder $move.File) -Force
        Write-Host "Moved: $($move.File) â $category" -ForegroundColor Green
    } catch {
        Write-Host "Failed: $($move.File) - $_" -ForegroundColor Red
    }
}

Write-Host "`nDone." -ForegroundColor Green
Get-ChildItem $sourcePath -Directory | ForEach-Object {
    $count = (Get-ChildItem $_.FullName -File).Count
    Write-Host "$($_.Name): $count files" -ForegroundColor Cyan
}
```

### Recipe 2: Organize Photos by Date Taken

```powershell
# Group photos into YYYY\MM-MonthName\ structure by EXIF date
# Requires: Get-Item with -File, filters on image extensions

$sourcePath = "C:\Users\ccimi\Pictures\Unsorted"
$destPath = "C:\Users\ccimi\Pictures\Organized"
$preview = $true

Add-Type -AssemblyName System.Drawing

$imageFiles = Get-ChildItem $sourcePath -File -Include *.jpg, *.jpeg, *.png | Where-Object { $_.Length -gt 0 }
Write-Host "Found $($imageFiles.Count) image files." -ForegroundColor Cyan

$moves = @()
foreach ($img in $imageFiles) {
    $img.Refresh()
    $createdDate = $img.CreationTime

    $yearFolder = $createdDate.Year.ToString()
    $monthFolder = $createdDate.ToString("MM-MMMM")
    $targetFolder = Join-Path $destPath $yearFolder $monthFolder

    $moves += [PSCustomObject]@{
        File = $img.Name
        "Created" = $createdDate.ToString("yyyy-MM-dd")
        "Target" = Join-Path $yearFolder $monthFolder
    }
}

$moves | Format-Table File, Created, Target -AutoSize
Write-Host "`nWill organize into: $destPath" -ForegroundColor Cyan

if ($preview) {
    Write-Host "Preview mode. Set `$preview = `$false to execute." -ForegroundColor Yellow
    exit 0
}

$confirm = Read-Host "Proceed? (yes/no)"
if ($confirm -notin @("yes", "y")) { exit 0 }

foreach ($move in $moves) {
    try {
        $parts = $move.Target -split '\\'
        $targetFolder = Join-Path $destPath @parts
        New-Item -ItemType Directory -Path $targetFolder -Force | Out-Null
        Move-Item (Join-Path $sourcePath $move.File) (Join-Path $targetFolder $move.File) -Force
        Write-Host "â $($move.File)" -ForegroundColor Green
    } catch {
        Write-Host "ERROR: $($move.File)" -ForegroundColor Red
    }
}

Write-Host "`nOrganized into: $destPath" -ForegroundColor Green
```

### Recipe 3: Recursive Sort by File Type (Subdirectories)

```powershell
# Sort ALL files in a tree (including subdirectories) by extension
# Moves files into Extension-named folders at the root

$rootPath = "C:\Users\ccimi\Documents\ProjectFiles"
$preview = $true

$allFiles = Get-ChildItem $rootPath -File -Recurse
Write-Host "Found $($allFiles.Count) files in $rootPath (recursive)." -ForegroundColor Cyan

$moves = @()
foreach ($file in $allFiles) {
    $ext = if ($file.Extension) { $file.Extension.Substring(1).ToUpper() } else { "NoExt" }
    $extFolder = Join-Path $rootPath $ext

    if ($extFolder -ne (Split-Path $file.FullName)) {
        $moves += [PSCustomObject]@{
            File = $file.Name
            Extension = $ext
            OldPath = Split-Path $file.FullName
        }
    }
}

Write-Host "`nMoving $($moves.Count) files into extension folders." -ForegroundColor Cyan
$moves | Group-Object Extension | ForEach-Object {
    Write-Host "$($_.Name): $($_.Count) files" -ForegroundColor Cyan
}

if ($preview) {
    Write-Host "`nPreview mode. Set `$preview = `$false to execute." -ForegroundColor Yellow
    exit 0
}

foreach ($move in $moves) {
    try {
        $extFolder = Join-Path $rootPath $move.Extension
        New-Item -ItemType Directory -Path $extFolder -Force | Out-Null
        Move-Item -Path (Join-Path $move.OldPath $move.File) -Destination (Join-Path $extFolder $move.File) -Force
    } catch {
        Write-Host "Failed: $($move.File)" -ForegroundColor Red
    }
}

Write-Host "Done." -ForegroundColor Green
```

## Collision Handling

### Rename on Conflict
If a file with the same name already exists in the destination, rename it:

```powershell
$destPath = Join-Path $folder $file.Name
if (Test-Path $destPath) {
    $name = [System.IO.Path]::GetFileNameWithoutExtension($file.Name)
    $ext = $file.Extension
    $newName = "$name-$(Get-Random).Copy$ext"
    $destPath = Join-Path $folder $newName
}
```

### Skip on Conflict
Leave existing files, skip the new one.

### Overwrite
Replace the existing file (use only when certain).

## Undo Operations

Generate a reverse-move script to restore original structure:

```powershell
# After running an organize script, save file manifest to restore later
$manifest = @()
foreach ($move in $moves) {
    $manifest += "$($move.File)|$($move.OldPath)|$($move.NewPath)"
}
$manifest | Out-File "undo-organize.txt"

# To undo: read manifest and reverse the moves
Get-Content "undo-organize.txt" | ForEach-Object {
    $file, $from, $to = $_ -split '\|'
    Move-Item (Join-Path $to $file) (Join-Path $from $file) -Force
}
```

## Safety

- **Always enable preview mode first** â inspect the planned moves before executing
- **Test on a small sample** before organizing an entire folder tree
- **Back up critical files** before large reorganizations
- **Collision handling**: Choose rename for safety, overwrite only when duplicate content is confirmed
- **Recursive operations**: Be cautious with `-Recurse` on large directory trees â can take time and create deep nesting

