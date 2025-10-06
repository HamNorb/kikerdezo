cls
[Console]::OutputEncoding = [System.Text.Encoding]::UTF8
$OutputEncoding = [System.Text.Encoding]::UTF8
# Szolista (Nemet - Magyar)
$engedelyezettValaszok = "szavak", "számok", "szamok", "Szavak", "Számok", "Szamok"
$ervenyesBemenet = $false
$valasz = ""

do {
    # 1. Bekérés (Read-Host)
    $valasz = Read-Host "Kérem adja meg, hogy 'szavak' vagy 'számok'"

    # 2. Ellenőrzés (a -in operátorral, ami a tömbben keres)
    # A -notin/in operátor alapértelmezetten NEM érzékeny a kis- és nagybetűkre!
    if ($valasz -in $engedelyezettValaszok) {
        $ervenyesBemenet = $true
    }
    else {
        Write-Warning "Érvénytelen bemenet. Kérem csak a 'szavak' vagy 'számok' szót adja meg."
    }

} while ($ervenyesBemenet -eq $false)

# A hurok után a $valasz változóban már a helyes, ellenőrzött válasz van.
Write-Host "A választásod: $valasz"
if (($valasz -eq "szavak") -or ($valasz -eq "Szavak")){
    $szavak = Get-Content -Path ".\szavak.txt" -Encoding UTF8
}
if (($valasz -eq "számok") -or ($valasz -eq "szamok") -or ($valasz -eq "Számok") -or ($valasz -eq "Szamok")){
    $szavak = Get-Content -Path ".\szamok.txt" -Encoding UTF8
}

function Normalize-String {
    param (
        [string]$InputString
    )
    # Hozzáadja az ékezetes magyar és német karaktereket, és lecseréli őket.
    # A 
    $normalizedString = $InputString.ToLower().Replace("á", "a").Replace("é", "e").Replace("í", "i").Replace("ó", "o").Replace("ö", "o").Replace("ő", "o").Replace("ú", "u").Replace("ü", "u").Replace("ű", "u").Replace("ä", "a").Replace("ö", "o").Replace("ü", "u")
    # Eltávolítja az összes ékezetet, ami a Normalize-rel kezelhető
    #$normalizedString = [string]::new($normalizedString.Normalize([System.Text.NormalizationForm]::FormD).Where({ $_ -ge ' ' -and $_ -le '~' }))
    return $normalizedString
}

$szamlalo = 0
$helyes = 0
$hibas = 0
cls
# Vegtelen ciklus a kilepesig
foreach ($szo in ($szavak | Get-Random -Count $szavak.Count)) {
 $reszek = $szo -split " - "
 $nemet = $reszek[0]
 $magyar = $reszek[1]

# Veletlenszeruen kerdezzuk magyarul vagy nemetul
if ((Get-Random -Minimum 0 -Maximum 2) -eq 0) {
 $szamlalo++
 # Kerdes nemetul
 $valasz = Read-Host "Mi a nemet megfeleloje: '$nemet'?"

 $normalizedValasz = Normalize-String -InputString $valasz
 $normalizedMagyar = Normalize-String -InputString $magyar

 if ($normalizedValasz.Trim().ToLower() -eq $normalizedMagyar.ToLower()) {
  Write-Host "✅ Helyes!" -ForegroundColor Green
  $helyes++
 } else {
  Write-Host "❌ Hibas! A helyes valasz: $magyar" -ForegroundColor Red
  $hibas++
 }
 if ($szamlalo%10 -eq 0)
 {
  Start-Sleep 1
  cls
  Write-Host "Helyes valaszok: $helyes" -ForegroundColor Green
  Write-Host "Hibas valaszok: $hibas" -ForegroundColor Red

 }
} else {
 # Kerdes magyarul
 $szamlalo++
 $valasz = Read-Host "Mi a magyar megfeleloje: '$magyar'?"

 $normalizedValasz = Normalize-String -InputString $valasz
 $normalizedNemet = Normalize-String -InputString $nemet

 if ($normalizedValasz.Trim().ToLower() -eq $normalizedNemet.ToLower()) {
  Write-Host "✅ Helyes!" -ForegroundColor Green
  $helyes++
 } else {
  Write-Host "❌ Hibas! A helyes valasz: $nemet" -ForegroundColor Red
  $hibas++
 }
 if ($szamlalo%10 -eq 0)
 {
  Start-Sleep 1
  cls
  Write-Host "Helyes valaszok: $helyes" -ForegroundColor Green
  Write-Host "Hibas valaszok: $hibas" -ForegroundColor Red
 }

}

 Start-Sleep -Seconds 1
}

# Eredmeny
Write-Host "`n📊 Eredmeny:"
Write-Host "Helyes valaszok: $helyes" -ForegroundColor Green
Write-Host "Hibas valaszok: $hibas" -ForegroundColor Red

Write-Host ("")
Write-Host -NoNewLine 'Press any key to continue...';
$null = $Host.UI.RawUI.ReadKey('NoEcho,IncludeKeyDown');
