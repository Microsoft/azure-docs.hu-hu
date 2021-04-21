---
title: MsIX Windows Virtual Desktop alkalmazás powerShell-szkriptek csatolásának konfigurálása – Azure
description: PowerShell-szkriptek létrehozása az MSIX-alkalmazás csatolása Windows Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 04/13/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 43a8cb00804927784982999db13ee193c34f55ca
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835379"
---
# <a name="create-powershell-scripts-for-msix-app-attach"></a>PowerShell-szkriptek létrehozása MSIX-alkalmazás csatolása számára

Ez a témakör bemutatja, hogyan állíthat be PowerShell-szkripteket az MSIX-alkalmazások csatolása számára.

## <a name="install-certificates"></a>Tanúsítványok telepítése

Tanúsítványokat kell telepítenie a gazdagépkészlet összes olyan munkamenetgazdára, amely az MSIX-alkalmazáscsomagok csatolási csomagjait fogja tartalmazni.

Ha az alkalmazás olyan tanúsítványt használ, amely nem nyilvános, vagy öna aláírással rendelkezik, a telepítés a következő:

1. Kattintson a jobb gombbal a csomagra, és válassza a **Tulajdonságok lehetőséget.**
2. A megjelenő ablakban válassza a **Digitális aláírások** lapot. A listában csak egy elemnek kell lennie a lapon. Jelölje ki az elemet az elem kiemeléshez, majd válassza a **Részletek lehetőséget.**
3. Amikor megjelenik a digitális aláírás  részletei ablak, válassza az Általános lapot, majd a Tanúsítvány megtekintése **lehetőséget,** végül válassza a **Tanúsítvány telepítése lehetőséget.**
4. Amikor a telepítő megnyílik, válassza ki a **helyi** gépet tárolási helyként, majd válassza a Tovább **lehetőséget.**
5. Ha a telepítő rákérdez, hogy engedélyezi-e az alkalmazás számára az eszköz módosításait, válassza az **Igen lehetőséget.**
6. Válassza **Az összes tanúsítvány tárolása a következő tárolóban** lehetőséget, majd válassza a Tallózás **lehetőséget.**
7. Amikor megjelenik a Tanúsítványtároló kiválasztása ablak, válassza a **Megbízható személyek** lehetőséget, majd kattintson az **OK gombra.**
8. Válassza a **Tovább,** majd a **Befejezés lehetőséget.**

## <a name="enable-microsoft-hyper-v"></a>A Microsoft Hyper-V

Microsoft Hyper-V engedélyezni kell, mert a parancs a szakaszhoz szükséges, és a `Mount-VHD` `Dismount-VHD` destage-hez szükséges.

```powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
```

>[!NOTE]
>A módosításhoz újra kell indítania a virtuális gépet.

## <a name="prepare-powershell-scripts-for-msix-app-attach"></a>PowerShell-szkriptek előkészítése MSIX-alkalmazás csatolása számára

Az MSIX-alkalmazás csatolása négy különböző fázisból áll, amelyek a következő sorrendben hajthatóak végre:

1. Fázis
2. Regisztráció
3. Regisztrációjának deregációja
4. Destage (Destage)

Minden fázis egy PowerShell-szkriptet hoz létre. Az egyes fázisok mint szkriptei itt [érhetők el.](https://github.com/Azure/RDS-Templates/tree/master/msix-app-attach)

### <a name="stage-powershell-script"></a>PowerShell-szkript szakasza

A PowerShell-parancsfájlok frissítése előtt győződjön meg arról, hogy a kötet GUID-azonosítóját a vhD-ban is elérhető. A kötet GUID-azonosítóját a következővel lehet lehozni:

1.  Nyissa meg azt a hálózati megosztást, ahol a VHD található abban a virtuális gépen belül, ahol a szkriptet fogja futtatni.

2.  Kattintson a jobb gombbal a virtuális merevlemezre, és válassza a **Csatlakoztatás lehetőséget.** Ezzel csatlakoztatja a VHD-t egy meghajtóbetűjelhez.

3.  A VHD csatlakoztatása után **megnyílik Fájlkezelő** ablak. Rögzítse a szülőmappát, és frissítse **a $parentFolder** változót

    >[!NOTE]
    >Ha nem lát szülőmappát, az azt jelenti, hogy az MSIX nincs megfelelően kibontva. Próbálja újra az előző szakaszt, és próbálkozzon újra.

4.  Nyissa meg a szülőmappát. Ha megfelelően kibontva van, egy, a csomag nevével azonos nevű mappa jelenik meg. Frissítse a **$packageName** változót úgy, hogy megfeleljen a mappa nevének.

    Például: `VSCodeUserSetup-x64-1.38.1_1.38.1.0_x64__8wekyb3d8bbwe`.

5.  Nyisson meg egy parancssort, és írja be a **mountvol parancsot.** Ez a parancs megjeleníti a kötetek listáját és azok GUID azonosítóit. Másolja ki annak a kötetnek a GUID-azonosítóját, ahol a meghajtó betűjele megegyezik azzal a meghajtóval, amelyhez csatlakoztatta a VHD-t a 2. lépésben.

    Ha például ebben a példakimenetben a mountvol parancshoz csatlakoztatta a VHD-t a C meghajtóhoz, a fenti értéket kell `C:\` másolnia:

    ```cmd
    Possible values for VolumeName along with current mount points are:

    \\?\Volume{a12b3456-0000-0000-0000-10000000000}\
    *** NO MOUNT POINTS ***

    \\?\Volume{c78d9012-0000-0000-0000-20000000000}\
        E:\

    \\?\Volume{d34e5678-0000-0000-0000-30000000000}\
        C:\

    ```


6.  Frissítse a **$volumeGuid** változót az előbb kimáselt kötet GUID azonosítóját.

7. Nyisson meg egy rendszergazdai PowerShell-parancssort, és frissítse a következő PowerShell-szkriptet a környezetére vonatkozó változók használatával.

    ```powershell
    #MSIX app attach staging sample

    #region variables
    $vhdSrc="<path to vhd>"
    $packageName = "<package name>"
    $parentFolder = "<package parent folder>"
    $parentFolder = "\" + $parentFolder + "\"
    $volumeGuid = "<vol guid>"
    $msixJunction = "C:\temp\AppAttach\"
    #endregion

    #region mountvhd
    try
    {
          Mount-Diskimage -ImagePath $vhdSrc -NoDriveLetter -Access ReadOnly
          Write-Host ("Mounting of " + $vhdSrc + " was completed!") -BackgroundColor Green
    }
    catch
    {
          Write-Host ("Mounting of " + $vhdSrc + " has failed!") -BackgroundColor Red
    }
    #endregion

    #region makelink
    $msixDest = "\\?\Volume{" + $volumeGuid + "}\"
    if (!(Test-Path $msixJunction))
    {
         md $msixJunction
    }

    $msixJunction = $msixJunction + $packageName
    cmd.exe /c mklink /j $msixJunction $msixDest
    #endregion

    #region stage
    [Windows.Management.Deployment.PackageManager,Windows.Management.Deployment,ContentType=WindowsRuntime] | Out-Null
    Add-Type -AssemblyName System.Runtime.WindowsRuntime
    $asTask = ([System.WindowsRuntimeSystemExtensions].GetMethods() | Where { $_.ToString() -eq 'System.Threading.Tasks.Task`1[TResult] AsTask[TResult,TProgress](Windows.Foundation.IAsyncOperationWithProgress`2[TResult,TProgress])'})[0]
    $asTaskAsyncOperation = $asTask.MakeGenericMethod([Windows.Management.Deployment.DeploymentResult], [Windows.Management.Deployment.DeploymentProgress])
    $packageManager = [Windows.Management.Deployment.PackageManager]::new()
    $path = $msixJunction + $parentFolder + $packageName # needed if we do the pbisigned.vhd
    $path = ([System.Uri]$path).AbsoluteUri
    $asyncOperation = $packageManager.StagePackageAsync($path, $null, "StageInPlace")
    $task = $asTaskAsyncOperation.Invoke($null, @($asyncOperation))
    $task
    #endregion
    ```

### <a name="register-powershell-script"></a>PowerShell-szkript regisztrálása

A regisztrálási szkript futtatásához futtassa a következő PowerShell-parancsmagokat a környezetére vonatkozó értékekkel lecserélt helyőrző értékekkel.

```powershell
#MSIX app attach registration sample

#region variables
$packageName = "<package name>"
$path = "C:\Program Files\WindowsApps\" + $packageName + "\AppxManifest.xml"
#endregion

#region register
Add-AppxPackage -Path $path -DisableDevelopmentMode -Register
#endregion
```

### <a name="deregister-powershell-script"></a>PowerShell-szkript regisztrációjának deregációja

Ehhez a szkripthez cserélje  le a $packageName helyőrzőt a tesztelt csomag nevére.

```powershell
#MSIX app attach deregistration sample

#region variables
$packageName = "<package name>"
#endregion

#region deregister
Remove-AppxPackage -PreserveRoamableApplicationData $packageName
#endregion
```

### <a name="destage-powershell-script"></a>PowerShell-szkriptek destage (A PowerShell-parancsfájlok) telepítése

Ehhez a szkripthez cserélje  le a $packageName helyőrzőt a tesztelt csomag nevére. Éles környezetben a legjobb, ha leállításkor futtatja.

```powershell
#MSIX app attach de staging sample

$vhdSrc="<path to vhd>"

#region variables
$packageName = "<package name>"
$msixJunction = "C:\temp\AppAttach"
#endregion

#region deregister
Remove-AppxPackage -AllUsers -Package $packageName
Remove-Item "$msixJunction\$packageName" -Recurse -Force -Verbose
#endregion

#region Detach VHD
Dismount-DiskImage -ImagePath $vhdSrc -Confirm:$false
#endregion
```

## <a name="set-up-simulation-scripts-for-the-msix-app-attach-agent"></a>Szimulációs szkriptek beállítása az MSIX-alkalmazás csatolási ügynökéhez

A szkriptek létrehozása után a felhasználók manuálisan futtathatja őket, vagy beállíthatja őket úgy, hogy automatikusan indítási, bejelentkezési, kijelentkezés- és leállítási parancsfájlként fusson. Az ilyen típusú szkriptekkel kapcsolatos további információkért lásd: [Indítási,](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789196(v=ws.11)/)leállítási, bejelentkezési és kijelentkezés parancsfájlok használata a Csoportházirend.

Ezek az automatikus szkriptek az alkalmazás csatolási szkriptek egy-egy fázisát futtatják:

- Az indítási szkript futtatja a stage szkriptet.
- A bejelentkezési szkript futtatja a regisztrálási szkriptet.
- Az kijelentkezés szkript futtatja a regisztrációt megszűkentő szkriptet.
- A leállítási szkript futtatja a destage szkriptet.

## <a name="use-packages-offline"></a>Csomagok offline használata

Ha az [Microsoft Store Vállalatoknak-ból](https://businessstore.microsoft.com/) vagy a hálózat [Microsoft Store Oktatási Intézményeknek-ről](https://educationstore.microsoft.com/) származó csomagokat használ, vagy olyan eszközökön, amelyek nem csatlakoznak az internethez, be kell szereznie a csomaglicenceket az Microsoft Store-ból, és telepítenie kell őket az eszközére az alkalmazás sikeres futtatásához. Ha az eszköze online állapotban van, és tud csatlakozni a Microsoft Store Vállalatoknak-hoz, a szükséges licencek letöltése automatikusan megtörténik, de offline állapotban manuálisan kell beállítania a licenceket.

A licencfájlok telepítéséhez olyan PowerShell-szkriptet kell használnia, amely a WMI-hídszolgáltató MDM_EnterpriseModernAppManagement_StoreLicenses02_01 osztályát hívja meg.

Így állíthatja be a licenceket offline használatra:

1. Töltse le az alkalmazáscsomagot, a licenceket és a szükséges keretrendszereket a Microsoft Store Vállalatoknak. A kódolt és a kódolatlan licencfájlokra egyaránt szüksége lesz. Részletes letöltési utasításokat itt [talál.](/microsoft-store/distribute-offline-apps#download-an-offline-licensed-app)
2. Frissítse az alábbi változókat a szkriptben a 3. lépéshez:
      1. `$contentID` A a Nem kódolatlan licencfájl (.xml) ContentID értéke. A licencfájlt egy Ön által választott szövegszerkesztőben nyithatja meg.
      2. `$licenseBlob` A a kódolt licencfájlban (.bin) lévő licencblob teljes sztringe. A kódolt licencfájlt egy Ön által választott szövegszerkesztőben nyithatja meg.
3. Futtassa a következő szkriptet egy rendszergazdai PowerShell-parancssorból. Jó hely a licenctelepítés végrehajtásához az [](#stage-powershell-script) előkészítési szkript végén van, amely egy rendszergazdai parancssorból is futtatható.

```powershell
$namespaceName = "root\cimv2\mdm\dmmap"
$className = "MDM_EnterpriseModernAppManagement_StoreLicenses02_01"
$methodName = "AddLicenseMethod"
$parentID = "./Vendor/MSFT/EnterpriseModernAppManagement/AppLicenses/StoreLicenses"

#TODO - Update $contentID with the ContentID value from the unencoded license file (.xml)
$contentID = "{'ContentID'_in_unencoded_license_file}"

#TODO - Update $licenseBlob with the entire String in the encoded license file (.bin)
$licenseBlob = "{Entire_String_in_encoded_license_file}"

$session = New-CimSession

#The final string passed into the AddLicenseMethod should be of the form <License Content="encoded license blob" />
$licenseString = '<License Content='+ '"' + $licenseBlob +'"' + ' />'

$params = New-Object Microsoft.Management.Infrastructure.CimMethodParametersCollection
$param = [Microsoft.Management.Infrastructure.CimMethodParameter]::Create("param",$licenseString ,"String", "In")
$params.Add($param)


try
{
   $instance = New-CimInstance -Namespace $namespaceName -ClassName $className -Property @{ParentID=$parentID;InstanceID=$contentID}
   $session.InvokeMethod($namespaceName, $instance, $methodName, $params)

}
catch [Exception]
{
     write-host $_ | out-string
}
```

## <a name="next-steps"></a>Következő lépések

Ez a funkció jelenleg nem támogatott, de a [TechCo Windows Virtual Desktop kérdéseket tehet fel a közösségnek.](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)

Visszajelzést is küldhet a Windows Virtual Desktop a [Windows Virtual Desktop visszajelzési központban.](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)
