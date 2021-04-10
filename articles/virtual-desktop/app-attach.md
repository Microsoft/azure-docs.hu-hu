---
title: A Windows rendszerű virtuális asztali MSIX alkalmazás konfigurálása PowerShell-parancsfájlok csatolása – Azure
description: PowerShell-parancsfájlok létrehozása a Windows rendszerű virtuális asztali MSIX alkalmazáshoz.
author: Heidilohr
ms.topic: how-to
ms.date: 12/14/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 224f2e773ecd42dcbdd356531d9ce94636de002f
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448270"
---
# <a name="create-powershell-scripts-for-msix-app-attach-preview"></a>PowerShell-parancsfájlok létrehozása MSIX-alkalmazás csatolásához (előzetes verzió)

> [!IMPORTANT]
> A MSIX-alkalmazás csatolása jelenleg nyilvános előzetes verzióban érhető el.
> Ezt az előzetes verziót szolgáltatói szerződés nélkül biztosítjuk, és nem javasoljuk, hogy éles számítási feladatokhoz használja azt. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ebből a témakörből megtudhatja, hogyan állíthat be PowerShell-szkripteket a MSIX alkalmazáshoz.

>[!IMPORTANT]
>Mielőtt elkezdené, győződjön meg róla, hogy kitölti és beküldi [ezt az űrlapot](https://aka.ms/enablemsixappattach) , hogy engedélyezze a MSIX-alkalmazás csatolását az előfizetésében. Ha nem rendelkezik jóváhagyott kéréssel, a MSIX-alkalmazás csatolása nem fog működni. A kérések jóváhagyása akár 24 óráig is eltarthat a munkaidőn belül. A kérés elfogadását és befejezését követően e-mailt fog kapni.

## <a name="install-certificates"></a>Tanúsítványok telepítése

Telepítenie kell a tanúsítványokat az összes olyan munkamenet-gazdagépre, amely a MSIX-alkalmazáshoz tartozó csomagokat fogja üzemeltetni.

Ha az alkalmazás olyan tanúsítványt használ, amely nem nyilvánosan megbízható vagy önaláírt, a következő módon telepítheti:

1. Kattintson a jobb gombbal a csomagra, és válassza a **Tulajdonságok** lehetőséget.
2. A megjelenő ablakban válassza a **digitális aláírások** fület. A lapon csak egy elemnek kell szerepelnie a listán, ahogy az az alábbi képen is látható. Válassza ki az elemet az elem kiemeléséhez, majd válassza a **részletek** lehetőséget.
3. Amikor megjelenik a digitális aláírás részletei ablak, válassza az **általános** lapot, majd válassza a **Tanúsítvány megtekintése** lehetőséget, majd válassza a **tanúsítvány telepítése** lehetőséget.
4. A telepítő megnyitásakor válassza a **helyi gép** lehetőséget a tárolási helyként, majd kattintson a **tovább** gombra.
5. Ha a telepítő megkérdezi, hogy engedélyezni szeretné-e az alkalmazásnak az eszköz módosítását, válassza az **Igen** lehetőséget.
6. Válassza **a minden tanúsítvány tárolása a következő tárolóban** lehetőséget, majd válassza a **Tallózás** lehetőséget.
7. Amikor megjelenik a tanúsítványtároló kiválasztása ablak, válassza a **Megbízható személyek** lehetőséget, majd kattintson **az OK gombra**.
8. Válassza a **tovább** , majd a **Befejezés** lehetőséget.

## <a name="enable-microsoft-hyper-v"></a>Microsoft Hyper-V engedélyezése

A Microsoft Hyper-Vt engedélyezni kell, mert a parancs végrehajtásához szükség van a művelet elvégzéséhez `Mount-VHD` `Dismount-VHD` .

```powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
```

>[!NOTE]
>Ehhez a változáshoz újra kell indítani a virtuális gépet.

## <a name="prepare-powershell-scripts-for-msix-app-attach"></a>PowerShell-parancsfájlok előkészítése MSIX-alkalmazás csatolásához

A MSIX-alkalmazás csatolása négy különböző fázist tartalmaz, amelyeket a következő sorrendben kell végrehajtani:

1. Fázis
2. Regisztráció
3. Képes
4. Kilépés

Minden fázis létrehoz egy PowerShell-szkriptet. Az egyes fázisok mintául szolgáló parancsfájljai [itt](https://github.com/Azure/RDS-Templates/tree/master/msix-app-attach)érhetők el.

### <a name="stage-powershell-script"></a>Szakasz PowerShell-parancsfájl

A PowerShell-parancsfájlok frissítése előtt ellenőrizze, hogy rendelkezik-e a kötet GUID azonosítóval a VHD-ben. A kötet GUID-azonosítójának lekérése:

1.  Nyissa meg azt a hálózati megosztást, ahol a VHD abban a virtuális gépen található, amelyen futtatni fogja a parancsfájlt.

2.  Kattintson a jobb gombbal a virtuális merevlemezre, és válassza a **Csatlakoztatás** lehetőséget. Ezzel csatlakoztatja a virtuális merevlemezt egy meghajtóbetűjelhez.

3.  Miután csatlakoztatta a VHD-t, megnyílik a **fájlkezelő** ablak. A szülőmappa rögzítése és a **$parentFolder** változó frissítése

    >[!NOTE]
    >Ha nem lát szülő mappát, az azt jelenti, hogy a MSIX nem lett megfelelően kibontva. Ismételje meg az előző szakaszt, és próbálkozzon újra.

4.  Nyissa meg a szülőmappa mappát. Ha a megfelelően ki van bontva, egy olyan mappa jelenik meg, amelynek a neve megegyezik a csomag nevével. Frissítse a **$packageName** változót, hogy megegyezzen a mappa nevével.

    Például: `VSCodeUserSetup-x64-1.38.1_1.38.1.0_x64__8wekyb3d8bbwe`.

5.  Nyisson meg egy parancssort, és adja meg a **mountvol** parancsot. A parancs megjeleníti a kötetek listáját és a GUID azonosítókat. Másolja ki annak a kötetnek a GUID azonosítóját, amelyben a meghajtóbetűjel megegyezik azzal a meghajtóval, amellyel a virtuális merevlemezt csatlakoztatta a 2. lépésben.

    Például a mountvol parancs kimenetében, ha a virtuális merevlemezt a C meghajtóhoz csatlakoztatta, a fenti értéket át kell másolnia `C:\` :

    ```cmd
    Possible values for VolumeName along with current mount points are:

    \\?\Volume{a12b3456-0000-0000-0000-10000000000}\
    *** NO MOUNT POINTS ***

    \\?\Volume{c78d9012-0000-0000-0000-20000000000}\
        E:\

    \\?\Volume{d34e5678-0000-0000-0000-30000000000}\
        C:\

    ```


6.  Frissítse a **$volumeGuid** változót az imént másolt kötet GUID azonosítóval.

7. Nyisson meg egy rendszergazdai PowerShell-parancssort, és frissítse a következő PowerShell-parancsfájlt a környezetre vonatkozó változókkal.

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

### <a name="register-powershell-script"></a>PowerShell-parancsfájl regisztrálása

A regisztrálási parancsfájl futtatásához futtassa a következő PowerShell-parancsmagokat a helyőrző értékekkel, amelyek az adott környezetre érvényes értékekkel lesznek lecserélve.

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

### <a name="deregister-powershell-script"></a>PowerShell-parancsfájl regisztrációjának törlése

Ehhez a parancsfájlhoz cserélje le a **$packageName** helyőrzőjét a tesztelni kívánt csomag nevére.

```powershell
#MSIX app attach deregistration sample

#region variables
$packageName = "<package name>"
#endregion

#region deregister
Remove-AppxPackage -PreserveRoamableApplicationData $packageName
#endregion
```

### <a name="destage-powershell-script"></a>PowerShell-parancsfájl lefázisa

Ehhez a parancsfájlhoz cserélje le a **$packageName** helyőrzőjét a tesztelni kívánt csomag nevére. Éles környezetben a legjobb megoldás a leállításkor.

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

## <a name="set-up-simulation-scripts-for-the-msix-app-attach-agent"></a>Szimulációs parancsfájlok beállítása a MSIX alkalmazáshoz csatolható ügynökhöz

A parancsfájlok létrehozása után a felhasználók manuálisan futtathatják azokat, vagy beállíthatja, hogy a rendszer automatikusan futtassa az indítási, bejelentkezési, kijelentkezési és leállítási parancsfájlok futtatását. Az ilyen típusú parancsfájlokkal kapcsolatos további információkért lásd: [indítási, leállítási, bejelentkezési és kijelentkezési parancsfájlok használata Csoportházirendban](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789196(v=ws.11)/).

Ezen automatikus parancsfájlok mindegyike az alkalmazás egy fázisát futtatja a parancsfájlok csatolásához:

- Az indítási parancsfájl futtatja a szakasz parancsfájlját.
- A bejelentkezési parancsfájl a regisztrálási parancsfájlt futtatja.
- A kijelentkezési parancsfájl a deregister szkriptet futtatja.
- A leállítási parancsfájl futtatja a kilépési parancsfájlt.

## <a name="use-packages-offline"></a>Csomagok használata kapcsolat nélküli üzemmódban

Ha olyan csomagokat használ a [Microsoft Store for Business](https://businessstore.microsoft.com/) vagy a [Microsoft Store for Education](https://educationstore.microsoft.com/) szolgáltatásban a hálózaton vagy az internethez nem csatlakozó eszközökön, le kell kérnie a csomag licenceit a Microsoft Store, és telepítenie kell őket az eszközre az alkalmazás sikeres futtatásához. Ha az eszköz online állapotban van, és kapcsolódni tud a vállalati Microsoft Storehoz, a szükséges licenceket automatikusan le kell tölteni, de ha offline állapotban van, manuálisan kell beállítania a licenceket.

A licencfájl telepítéséhez egy PowerShell-szkriptet kell használnia, amely meghívja a MDM_EnterpriseModernAppManagement_StoreLicenses02_01 osztályt a WMI Bridge-szolgáltatóban.

A következő módon állíthatja be a licenceket offline használatra:

1. Töltse le az alkalmazáscsomag, a licencek és a szükséges keretrendszereket a vállalati Microsoft Store. A kódolt és a titkosítatlan licencek is szükségesek. Részletes letöltési utasítások [itt](/microsoft-store/distribute-offline-apps#download-an-offline-licensed-app)találhatók.
2. Frissítse a következő változókat a 3. lépéshez tartozó parancsfájlban:
      1. `$contentID` a ContentID értéke a nem kódolt licencfájl (. xml). A licencfájl megnyitható egy tetszőleges szövegszerkesztőben.
      2. `$licenseBlob` a a titkosított licencfájl (. bin) teljes karakterlánca a licenc blobjának. A kódolt licencfájl megnyitható egy tetszőleges szövegszerkesztőben.
3. Futtassa a következő parancsfájlt egy rendszergazdai PowerShell-parancssorból. A licencek telepítésének megfelelő helye az [átmeneti parancsfájl](#stage-powershell-script) végén található, amelyet rendszergazdai parancssorból is futtatni kell.

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

Ez a funkció jelenleg nem támogatott, de kérdéseket tehet fel a Közösségnek a [Windows rendszerű virtuális asztali TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

A Windows rendszerű virtuális asztalok visszajelzéseit a [Windows Virtual Desktop visszajelzési központja](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)is elhagyhatja.
