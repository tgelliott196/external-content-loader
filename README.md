# Define the root folder to start the search
$rootFolder = "C:\"

# Function to check if the current user has write access to a folder
function Test-WriteAccess {
    param (
        [string]$folderPath
    )
    try {
        # Attempt to create a temporary file in the folder
        $tempFile = [System.IO.Path]::Combine($folderPath, [System.IO.Path]::GetRandomFileName())
        $fileStream = [System.IO.File]::Create($tempFile)
        $fileStream.Close()
        # Delete the temporary file
        Remove-Item -Path $tempFile -Force
        return $true
    } catch {
        return $false
    }
}

# Function to recursively check folders
function Get-WritableFolders {
    param (
        [string]$path
    )
    # Get all directories in the current path
    $directories = Get-ChildItem -Path $path -Directory -Recurse -ErrorAction SilentlyContinue
    foreach ($dir in $directories) {
        if (Test-WriteAccess -folderPath $dir.FullName) {
            Write-Output $dir.FullName
        }
    }
}

# Run the function starting from the root folder
Get-WritableFolders -path $rootFolder
