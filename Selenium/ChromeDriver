 This script determines the Google Chrome version and downloads and installs the required Chrome Driver

# configuration
Set-Location -Path $PSScriptRoot
$updatelog = 'chromedriver_update.log'

# Get the installed version of Google Chrome
$chrome_info = get-wmiobject Win32_Product | Where-Object {$_.Name -eq 'Google Chrome'} | Select-Object -First 1
if (-not $chrome_info) {
    'Could not retrieve Google Chrome version. Is it installed?' | Out-File $updatelog -Append
    Exit-PSSession
}
$major_chrome_version = $chrome_info.Version.Split('.')[0]
'Current major version of Google Chrome is {0}' -f $major_chrome_version | Out-File $updatelog -Append

# Determine the ChromeDriver version advised for this major version of Chrome
$cd_version_checkurl = 'https://googlechromelabs.github.io/chrome-for-testing/LATEST_RELEASE_{0}' -f $major_chrome_version
$cd_version = (Invoke-WebRequest -Uri $cd_version_checkurl).ParsedHtml.Body.innerHTML

# Download the required version (zip file)
$cd_zipfile = 'chromedriver_win32.zip'
$cd_executable = 'chromedriver.exe'
$cd_url = 'https://chromedriver.storage.googleapis.com/{0}/{1}' -f $cd_version, $cd_zipfile
Invoke-WebRequest -Uri $cd_url -OutFile $cd_zipfile

# replace chromedriver
if ( -not (Test-Path $cd_zipfile -PathType Leaf)) {
    'Could not retrieve Chrome Driver version {0} from {1}' -f $cd_version, $cd_url | Out-File $updatelog -Append
}
else {
    if ((Test-Path $cd_executable -PathType Leaf)) {
        Remove-Item -Path $cd_executable
    }

    Expand-Archive -Path $cd_zipfile -DestinationPath .
    Remove-Item -Path $cd_zipfile
   
    'Successfully updated the Chrome Driver to version {0}' -f $cd_version | Out-File $updatelog -Append
