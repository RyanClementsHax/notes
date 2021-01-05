# Web Request

## Get request piped into a for each
```powershell
$baseUrl = "example.com/thing"

(Invoke-RestMethod -Uri "$baseUrl/some_path").id -split '\s+' | ForEach-Object {  
  Invoke-WebRequest -Uri "$baseUrl/some_other_path" `
    -Method "DELETE" `
    -ContentType "application/json-patch+json" `
    -Body ([System.Text.Encoding]::UTF8.GetBytes("{$([char]10)  `"id`": `"$_`",$([char]10)  `"userName`": `"me`"$([char]10)}"))
}
```