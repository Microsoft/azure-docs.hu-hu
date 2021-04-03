---
title: Útmutató Windows-sablonok számítógépének beállításához | Microsoft Docs
description: Általános lépések a Windows-sablonok számítógépének előkészítéséhez a labor Servicesben.  Ezek a lépések a Windows Update ütemtervének, a OneDrive telepítésének és az Office telepítésének beállítását foglalják magukban.
author: EMaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: 216dc843b31eac355e1d818014f3d70b2ef83132
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94647902"
---
# <a name="guide-to-setting-up-a-windows-template-machine-in-azure-lab-services"></a>Útmutató Windows-sablonok számítógépének beállításához Azure Lab Services

Ha Azure Lab Serviceshoz állít be egy Windows 10-es sablont, az alábbi ajánlott eljárásokat és tippeket érdemes figyelembe vennie. Az alábbi konfigurációs lépések mindegyike nem kötelező.  Ezek az előkészítő lépések azonban segíthetnek a tanulók hatékonyabbá tételében, az osztályok időkorlátjának minimalizálásában és a legújabb technológiák használatának biztosításában.

>[!IMPORTANT]
>Ez a cikk PowerShell-kódrészleteket tartalmaz a gépi sablon módosítási folyamatának egyszerűsítéséhez.  Az összes megjelenített PowerShell-parancsfájlhoz rendszergazdai jogosultságokkal kell futtatnia azokat a Windows PowerShellben. A Windows 10 rendszerben a gyors módja, hogy a jobb gombbal kattintson a Start menüre, és válassza a "Windows PowerShell (rendszergazda)" lehetőséget.

## <a name="install-and-configure-onedrive"></a>OneDrive telepítése és konfigurálása

Ha a virtuális gép alaphelyzetbe állítása esetén a tanulói adatok elvesznek, akkor azt javasoljuk, hogy a tanulók az adataikat a felhőbe állítsa vissza.  A Microsoft OneDrive segíthetnek a tanulóknak az adataik védelmében.  

### <a name="install-onedrive"></a>A OneDrive telepítése

A OneDrive manuális letöltéséhez és telepítéséhez tekintse meg a [OneDrive](https://onedrive.live.com/about/download/) vagy a [OneDrive for Business](https://onedrive.live.com/about/business/) letöltési lapját.

A következő PowerShell-parancsfájlt is használhatja.  A rendszer automatikusan letölti és telepíti a OneDrive legújabb verzióját.  Miután telepítette a OneDrive-ügyfelet, futtassa a telepítőt.  A példánkban a `/allUsers` kapcsoló használatával telepítjük a OneDrive a gépen lévő összes felhasználóra. A `/silent` kapcsolót a OneDrive csendes telepítésére is használjuk.

```powershell
Write-Host "Downloading OneDrive Client..."
$DownloadPath = "$env:USERPROFILE/Downloads/OneDriveSetup.exe"
if((Test-Path $DownloadPath) -eq $False )
{
    Write-Host "Downloading OneDrive..."
    $web = new-object System.Net.WebClient
    $web.DownloadFile("https://go.microsoft.com/fwlink/p/?LinkId=248256",$DownloadPath)
} else {
    Write-Host "OneDrive installer already exists at " $DownloadPath
}

Write-Host "Installing OneDrive..."
& $env:USERPROFILE/Downloads/OneDriveSetup.exe /allUsers /silent
```

### <a name="onedrive-customizations"></a>OneDrive testreszabása

Számos [testreszabási művelet hajtható végre a OneDrive](/onedrive/use-group-policy). Ismerkedjen meg a leggyakoribb testreszabásokkal.

#### <a name="silently-move-windows-known-folders-to-onedrive"></a>Windows ismert mappák csendes áthelyezése a OneDrive

A dokumentumokat, például a dokumentumokat, a letöltéseket és a képeket gyakran használják a tanulói fájlok tárolására. A mappák OneDrive való biztonsági mentésének biztosításához javasoljuk, hogy helyezze át ezeket a mappákat a OneDrive.

Ha olyan gépen van, amely nem Active Directory használ, a felhasználók manuálisan is áthelyezhetik ezeket a mappákat a OneDrive, ha a hitelesítésük a OneDrive történik.

1. Fájlkezelő megnyitása
2. Kattintson a jobb gombbal a dokumentumok, letöltések vagy képek mappára.
3. Lépjen a tulajdonságok > helyre.  Helyezze át a mappát egy új mappába a OneDrive könyvtárban.

Ha a virtuális gép csatlakoztatva van Active Directoryhoz, beállíthatja, hogy a sablon számítógépe automatikusan rákérdezzen a tanulók számára az ismert mappák OneDrive való áthelyezésére.  

Először le kell kérnie a szervezet AZONOSÍTÓját.  További útmutatásért lásd [a Microsoft 365 szervezet azonosítójának megkeresése](/onedrive/find-your-office-365-tenant-id)című témakört.  A szervezet AZONOSÍTÓját a következő PowerShell használatával is lekérheti.

```powershell
Install-Module MSOnline -Confirm
Connect-MsolService
$officeTenantID = Get-MSOLCompanyInformation |
    Select-Object -expand objectID |
    Select-Object -expand Guid
```

A szervezet AZONOSÍTÓjának megadását követően állítsa be a OneDrive-t, hogy az ismert mappákat a következő PowerShell használatával helyezze át a OneDrive.

```powershell
if ($officeTenantID -eq $null)
{
        Write-Error "Variable `$officeTenantId must be set to your Office Tenant Id before continuing."
}
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "KFMSilentOptIn" -Value $officeTenantID -PropertyType STRING
```

### <a name="use-onedrive-files-on-demand"></a>Igény szerinti OneDrive-fájlok használata

A tanulók több fájlt is tartalmazhatnak a OneDrive-fiókjaikban. A tárhelynek a gépen való megtakarítása és a letöltési idő csökkentése érdekében javasoljuk, hogy a Student OneDrive-fiókjában tárolt összes fájl igény szerinti legyen.  Az igény szerinti fájlok csak akkor tölthetők le, ha a felhasználó hozzáfér a fájlhoz.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive" -Force
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "FilesOnDemandEnabled" -Value "00000001" -PropertyType DWORD
```

### <a name="silently-sign-in-users-to-onedrive"></a>Felhasználók csendes bejelentkezésének OneDrive

A OneDrive beállítható úgy, hogy automatikusan bejelentkezzen a bejelentkezett felhasználó Windows-hitelesítő adataival.  Az automatikus bejelentkezés olyan osztályok esetében hasznos, ahol a tanuló az iskolai hitelesítő adataival jelentkezik be.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "SilentAccountConfig" -Value "00000001" -PropertyType DWORD
```

### <a name="disable-the-tutorial-that-appears-at-the-end-of-onedrive-setup"></a>Tiltsa le a OneDrive-telepítő végén megjelenő oktatóanyagot

Ezzel a beállítással megakadályozhatja, hogy az oktatóanyag a OneDrive-telepítő végén webböngészőben legyen elindítva.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive" -Force
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "DisableTutorial" -Value "00000001" -PropertyType DWORD -Force
```

### <a name="set-the-maximum-size-of-a-file-that-to-be-download-automatically"></a>Az automatikusan letölteni kívánt fájl maximális méretének beállítása

Ezt a beállítást a rendszer a felhasználók beavatkozás nélküli bejelentkezéséhez használja a OneDrive-szinkronizálási ügyfélhez a Windows hitelesítő adataival azokon az eszközökön, amelyeken az igény szerinti OneDrive-fájlok nincsenek engedélyezve. A rendszer a megadott küszöbértéknél nagyobb OneDrive rendelkező felhasználókat kéri a szinkronizálni kívánt mappák kiválasztására, mielőtt a OneDrive Sync ügyfél (OneDrive.exe) letölti a fájlokat.  A példánkban a "1111-2222-3333-4444" a szervezeti azonosító, a 0005000 pedig 5 GB-os küszöbértéket állít be.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive\DiskSpaceCheckThresholdMB"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive\DiskSpaceCheckThresholdMB"
    -Name "1111-2222-3333-4444" -Value "0005000" -PropertyType DWORD
```

## <a name="install-and-configure-microsoft-365"></a>Microsoft 365 telepítése és konfigurálása

### <a name="install-microsoft-365"></a>Microsoft 365 telepítése

Ha a sablon számítógépének szüksége van az Office-ra, javasoljuk, hogy az Office üzembe helyezését az Office [Deployment Tool (ODT)](https://www.microsoft.com/download/details.aspx?id=49117)használatával. Létre kell hoznia egy újrafelhasználható konfigurációs fájlt a [Microsoft 365 apps felügyeleti központban](https://config.office.com/) , amellyel kiválaszthatja, hogy melyik architektúra, milyen funkciókra lesz szüksége az Office-től, és milyen gyakran frissül.

1. Nyissa meg [Microsoft 365 alkalmazások felügyeleti központját](https://config.office.com/) , és töltse le a saját konfigurációs fájlját.
2. Töltse le az [Office üzembehelyezési eszközét](https://www.microsoft.com/download/details.aspx?id=49117).  A letöltött fájl lesz `setup.exe` .
3. `setup.exe /download configuration.xml`Az Office-összetevők letöltéséhez futtassa a parancsot.
4. `setup.exe /configure configuration.xml`Az Office-összetevők telepítéséhez futtassa a parancsot.

### <a name="change-the-microsoft-365-update-channel"></a>A Microsoft 365 frissítési csatorna módosítása

Az Office konfigurációs eszköz használatával beállíthatja, hogy az Office milyen gyakran kapjon frissítéseket. Ha azonban módosítania kell, hogy az Office milyen gyakran kapja meg a frissítéseket a telepítés után, módosíthatja a frissítési csatorna URL-címét. A frissítési csatorna URL-címei a [Microsoft 365 alkalmazások frissítési csatornájának módosítása a szervezet eszközeire](/deployoffice/change-update-channels)című részében találhatók. Az alábbi példa bemutatja, hogyan állíthatja be Microsoft 365 a havi frissítési csatorna használatára.

```powershell
# Update to the Microsoft 365 Monthly Channel
Set-ItemProperty
    -Path "HKLM:\SOFTWARE\Microsoft\Office\ClickToRun\Configuration\CDNBaseUrl"
    -Name "CDNBaseUrl"
    -Value "http://officecdn.microsoft.com/pr/492350f6-3a01-4f97-b9c0-c7c6ddf67d60"
```

## <a name="install-and-configure-updates"></a>Frissítések telepítése és konfigurálása

### <a name="install-the-latest-windows-updates"></a>A legújabb Windows-frissítések telepítése

Javasoljuk, hogy biztonsági okokból telepítse a legújabb Microsoft-frissítéseket a sablon számítógépre, mielőtt közzéteszi a virtuális gépet.  Azt is elkerülheti, hogy a tanulók ne tudják megszakítani a munkájukat, amikor a frissítések váratlan időpontokban futnak.

1. **Beállítások** elindítása a Start menüből
2. Kattintson a **frissítés** & Biztonság elemre.
3. Kattintson **a frissítések keresése** elemre.
4. A rendszer letölti és telepíti a frissítéseket.

A PowerShell használatával is frissítheti a sablon számítógépét.

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Confirm
Install-Module PSWindowsUpdate -Confirm
Install-WindowsUpdate -MicrosoftUpdate
Set-ExecutionPolicy default -Force
```

>[!NOTE]
>Egyes frissítések esetében előfordulhat, hogy a gépet újra kell indítani.  A rendszer felszólítja, ha újraindításra van szükség.

### <a name="install-the-latest-updates-for-microsoft-store-apps"></a>Microsoft Store alkalmazások legújabb frissítéseinek telepítése

Javasoljuk, hogy az összes Microsoft Store alkalmazást frissítse a legújabb verzióra.  Az alábbi útmutatást követve manuálisan frissítheti az alkalmazásokat a Microsoft Storeról.  

1. **Microsoft Store** alkalmazás elindítása.
2. Kattintson a felhasználói fénykép melletti ellipszisre (...) az alkalmazás felső sarkában.
3. Válassza a **Letöltés** és frissítések lehetőséget a legördülő menüből.
4. Kattintson a **frissítés beolvasása** gombra.

A PowerShell használatával is frissítheti Microsoft Store már telepített alkalmazásokat.

```powershell
(Get-WmiObject -Namespace "root\cimv2\mdm\dmmap" -Class "MDM_EnterpriseModernAppManagement_AppManagement01").UpdateScanMethod()
```

### <a name="stop-automatic-windows-updates"></a>Automatikus Windows-frissítések leállítása

Ha a Windowst a legújabb verzióra frissíti, érdemes lehet megszüntetni a Windows-frissítéseket.  Az automatikus frissítések megakadályozhatják az ütemezett osztályok időpontját.  Ha a tanfolyam már fut, gondolja át, hogy a tanulók manuálisan keresik-e meg a frissítéseket, vagy az ütemezett órákon kívüli időpontra állítanak be automatikus frissítéseket.  A Windows Update testreszabási lehetőségeivel kapcsolatos további információkért tekintse meg a [további Windows Update-beállítások kezelése](/windows/deployment/update/waas-wu-settings)című témakört.

Előfordulhat, hogy az automatikus Windows-frissítések a következő PowerShell-parancsfájl használatával leállnak.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\AU"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\AU"
    -Name "NoAutoUpdate" -Value "1" -PropertyType DWORD
```

## <a name="install-foreign-language-packs"></a>Idegen nyelvi csomagok telepítése

Ha a virtuális gépen további nyelvekre van szüksége, akkor a Microsoft Storeon keresztül adhatja hozzá őket.

1. Microsoft Store elindítása
2. A "nyelvi csomag" kifejezés keresése
3. Válassza ki a telepítendő nyelvet

Ha már bejelentkezett a sablon virtuális gépre, a "nyelvi csomag telepítése" parancsikon () használatával `ms-settings:regionlanguage?activationSource=SMC-IA-4027670` lépjen közvetlenül a megfelelő beállítások lapra.

## <a name="remove-unneeded-built-in-apps"></a>Szükségtelen beépített alkalmazások eltávolítása

A Windows 10 számos beépített alkalmazást tartalmaz, amelyek esetleg nem szükségesek az adott osztályhoz. A diákoknak szánt számítógépes rendszerkép leegyszerűsítése érdekében előfordulhat, hogy el szeretné távolítani néhány alkalmazást a sablon gépről.  A telepített alkalmazások listájának megtekintéséhez használja a PowerShell- `Get-AppxPackage` parancsmagot.  Az alábbi példa az összes eltávolítható alkalmazást megjeleníti.

```powershell
Get-AppxPackage | Where {$_.NonRemovable -eq $false} | select Name
```

Egy alkalmazás eltávolításához használja az Remove-Appx parancsmagot.  Az alábbi példa bemutatja, hogyan távolíthatja el az XBox-hoz kapcsolódó összes információt.

```powershell
Get-AppxPackage -Name *xbox* | foreach { if (-not $_.NonRemovable) { Remove-AppxPackage $_} }
```

## <a name="install-common-teaching-related-applications"></a>Közös tanítással kapcsolatos alkalmazások telepítése

Telepítsen más alkalmazásokat, amelyeket gyakran használnak a Windows áruházbeli alkalmazásban való tanításhoz. A javaslatok közé tartoznak például a [Microsoft faliújság-alkalmazás](https://www.microsoft.com/store/productId/9MSPC6MP8FM4), a [Microsoft Teams](https://www.microsoft.com/store/productId/9MSPC6MP8FM4)és a [Minecraft Education Edition](https://education.minecraft.net/). Ezeket az alkalmazásokat manuálisan kell telepíteni a Windows áruházon keresztül vagy a sablon virtuális gépén lévő saját webhelyein keresztül.

## <a name="conclusion"></a>Összegzés

Ez a cikk a Windows-sablonos virtuális gép hatékony osztályra történő előkészítésének opcionális lépéseit mutatja be.  A lépések közé tartozik például a OneDrive telepítése és a Microsoft 365 telepítése, a Windows frissítéseinek telepítése és a frissítések telepítése Microsoft Store alkalmazásokhoz.  Azt is ismertetjük, hogyan állíthatja be a frissítéseket egy olyan ütemtervre, amely az osztály számára a legjobban működik.  

## <a name="next-steps"></a>Következő lépések
Tekintse meg a Windows leállítási viselkedésének szabályozása a költségek kezeléséhez: [útmutató a Windows leállítási viselkedésének szabályozásához](how-to-windows-shutdown.md) .