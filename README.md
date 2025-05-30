# Powershell-Scripts
Windows IT Automation


1. Disk Space Monitoring Script (Cross-Platform)

```powershell
# Check all drives and alert if free space is less than 15%
$threshold = 15
$drives = Get-PSDrive -PSProvider 'FileSystem'

foreach ($drive in $drives) {
    if ($drive.Size -gt 0 -and $drive.Used -gt 0) {
        $used = ($drive.Used / $drive.Size) * 100
        $free = 100 - $used

        if ($free -lt $threshold) {
            Write-Output "⚠️ Drive $($drive.Name) low on space: $([math]::Round($free, 2))% free"
        } else {
            Write-Output "✅ Drive $($drive.Name): $([math]::Round($free, 2))% free"
        }
    } else {
        Write-Output "⏩ Skipping $($drive.Name): No size or usage data available"
    }
}
```

---

2. Active Directory: Create a New User (Windows Only)

```powershell
# Requires Active Directory module and domain access
Import-Module ActiveDirectory

$first = "John"
$last = "Doe"
$userName = "$first.$last"
$ou = "OU=Users,DC=domain,DC=local"

New-ADUser -Name "$first $last" `
           -SamAccountName $userName `
           -UserPrincipalName "$userName@domain.local" `
           -AccountPassword (ConvertTo-SecureString "Pass@123" -AsPlainText -Force) `
           -Enabled $true -Path $ou
```

---

🛠️ 3. Patch Status Report Across Multiple Servers

```powershell
# Fetches list of installed patches and exports to CSV
$servers = Get-Content "servers.txt"

Invoke-Command -ComputerName $servers -ScriptBlock {
    Get-HotFix | Sort-Object InstalledOn | Select-Object Description, HotFixID, InstalledOn
} | Export-Csv "PatchReport.csv" -NoTypeInformation
```

---

4. Send Email Alert (Disk, Patch, or Any Failure)

```powershell
# Replace SMTP settings with your own
$subject = "Disk Space Alert"
$body = "Drive C: has less than 10% free space"
Send-MailMessage -To "admin@domain.com" `
                 -From "alerts@domain.com" `
                 -Subject $subject `
                 -Body $body `
                 -SmtpServer "smtp.domain.com"
```

---

5. Scheduled Task Creation Script

```powershell
# Create a task to run your PowerShell script daily
$action = New-ScheduledTaskAction -Execute 'PowerShell.exe' -Argument '-File "C:\Scripts\Monitor.ps1"'
$trigger = New-ScheduledTaskTrigger -Daily -At 8am
Register-ScheduledTask -Action $action -Trigger $trigger -TaskName "Disk Monitor" -Description "Monitor Disk Space Daily"
```

---

# 6. Basic PowerShell GUI (Button Trigger Example)

```powershell
Add-Type -AssemblyName System.Windows.Forms
$form = New-Object system.Windows.Forms.Form
$form.Text = "Infra Toolkit"
$form.Width = 400
$form.Height = 200

$button = New-Object System.Windows.Forms.Button
$button.Text = "Check Disk Space"
$button.Width = 150
$button.Top = 50
$button.Left = 100

$button.Add_Click({
    $output = Get-PSDrive -PSProvider 'FileSystem' | Out-String
    [System.Windows.Forms.MessageBox]::Show($output)
})

$form.Controls.Add($button)
$form.ShowDialog()
```

---

# 7. Azure Automation Runbook Example (Cloud Execution)

```powershell
# Sample runbook to restart a service on Azure VM
Get-Service -Name "wuauserv" | Restart-Service
```

---

# 8. Ansible Playbook Example for Windows Updates

```yaml
# Run from Ansible host with WinRM enabled on the target Windows machine
- name: Apply Windows Updates
  hosts: windows
  tasks:
    - name: Install Security Updates
      win_updates:
        category_names:
          - SecurityUpdates
        reboot: yes
```

---

# 9. System Info Report

```powershell
# Export system info to a file
Get-ComputerInfo | Out-File "SystemInfo.txt"
```

---
