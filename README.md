# web-practice
# -1 == 0xFFFFFFFF in DWord == 100% DPI scaling
# 0 = default setting of the screen (can be 125%!)
# 1 = default settings + 1
# 2 = default settings + 2
$dpiValue = -1

$activeMonitorsRegPath = "HKCU:\Control Panel\Desktop\PerMonitorSettings"
$genericMonitorsList = Get-ChildItem HKLM:\System\CurrentControlSet\Control\GraphicsDrivers\ScaleFactors

Write-Host( [string]::Format("Found {0} ScaleFactors monitors",$genericMonitorsList.Length));

foreach ($genericMonitor in $genericMonitorsList){

    $tempRegPath = $activeMonitorsRegPath + '\' + $genericMonitor.PsChildname;

    # if registry KEY already exists
	if (Test-Path -Path $tempRegPath) {
	
        Write-Host('Updating value for monitor - ' + $genericMonitor.PsChildname)

		# update existing-item DPI's value
		Set-ItemProperty -Path $tempRegPath -Name 'DpiValue' -Value $dpiValue –Force 
		
	} else {

        Write-Host('Creating new key and value for monitor - ' + $genericMonitor.PsChildname)

    	# create new key under PerMonitorSettings
		New-Item -Path $activeMonitorsRegPath -Name $genericMonitor.PsChildname –Force | Out-Null
		
		# create new value
		New-ItemProperty  -Path $tempRegPath -Name 'DpiValue' -PropertyType DWord -Value $dpiValue –Force  | Out-Null
	}
}

$genericMonitorsList.Close();
$genericMonitorsList = $null;
