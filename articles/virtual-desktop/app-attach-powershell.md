---
title: Windows rendszerű virtuális asztali MSIX alkalmazás csatolja az előnézet PowerShellt – Azure
description: A Windows rendszerű virtuális asztali MSIX-alkalmazás csatlakoztatása a PowerShell használatával.
author: Heidilohr
ms.topic: how-to
ms.date: 12/14/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 1196982fedc7321805e36cceed27c90e43a6e705
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99558336"
---
# <a name="set-up-msix-app-attach-preview-using-powershell"></a>A MSIX-alkalmazás csatolásának (előzetes verzió) beállítása a PowerShell használatával

> [!IMPORTANT]
> A MSIX-alkalmazás csatolása jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A Azure Portal mellett manuálisan is beállíthatja a MSIX-alkalmazás csatolását (előzetes verzió) a PowerShell használatával. Ebből a cikkből megtudhatja, hogyan használhatja a PowerShellt a MSIX-alkalmazás csatolásának beállításához.

## <a name="requirements"></a>Követelmények

>[!IMPORTANT]
>Mielőtt elkezdené, győződjön meg róla, hogy kitölti és beküldi [ezt az űrlapot](https://aka.ms/enablemsixappattach) , hogy engedélyezze a MSIX-alkalmazás csatolását az előfizetésében. Ha nem rendelkezik jóváhagyott kéréssel, a MSIX-alkalmazás csatolása nem fog működni. A kérések jóváhagyása akár 24 óráig is eltarthat a munkaidőn belül. A kérés elfogadását és befejezését követően e-mailt fog kapni.

A MSIX-alkalmazás csatolásának konfigurálásához a következők szükségesek:

- Működő Windowsos virtuális asztali telepítés. A Windows rendszerű virtuális asztali gépek (klasszikus) központi telepítésének megismeréséhez tekintse meg [a bérlő létrehozása a Windows rendszerű virtuális asztalon](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md)című témakört. A Windows rendszerű virtuális asztali Azure Resource Manager integrációs szolgáltatással való üzembe helyezésével kapcsolatos további információkért lásd: [gazdagép-készlet létrehozása a Azure Portal](./create-host-pools-azure-marketplace.md).
- Egy Windows rendszerű virtuális asztali címkészlet, amely legalább egy aktív munkamenet-gazdagépet futtat.
- Ennek az alkalmazáskészletnek az ellenőrzési környezetben kell lennie.
- Egy asztali távoli alkalmazás csoportja.
- A MSIX-csomagoló eszköz.
- Egy MSIX csomagolt alkalmazás kibővült egy fájlmegosztásba feltöltött MSIX-rendszerképbe.
- Egy fájlmegosztás a Windows rendszerű virtuális asztali környezetben, ahol a MSIX-csomagot tárolja a rendszer.
- A MSIX-rendszerkép feltöltésének helyét a gazdagépen lévő összes virtuális gép (VM) számára is elérhetővé kell tenni. A felhasználóknak csak olvasási jogosultsággal kell rendelkezniük a rendszerkép eléréséhez.
- Töltse le és telepítse a PowerShell Core-t.
- Töltse le a nyilvános előzetes Azure PowerShell modult, és bontsa ki egy helyi mappába.
- Telepítse az Azure-modult a következő parancsmag futtatásával:

    ```powershell
    Install-Module -Name Az -Force
    ```

## <a name="sign-in-to-azure-and-import-the-module"></a>Jelentkezzen be az Azure-ba, és importálja a modult

Ha minden követelmény elkészült, nyissa meg a PowerShell Core-t egy rendszergazda jogú parancssorban, és futtassa a következő parancsmagot:

```powershell
Connect-AzAccount
```

A futtatása után hitelesítse a fiókját a hitelesítő adataival. Ebben az esetben előfordulhat, hogy az eszköz URL-címét vagy tokenjét kell megadnia.

## <a name="import-the-azwindowsvirtualdesktop-module"></a>Az az. WindowsVirtualDesktop modul importálása

A cikk utasításait az az. DesktopVirtualization modulnak kell követnie.

>[!NOTE]
>A nyilvános előzetes verzióban külön ZIP-fájlként adjuk meg a modult, amelyet manuálisan kell importálnia.

A Kezdés előtt a következő parancsmag futtatásával megállapíthatja, hogy az az. DesktopVirtualization modul már telepítve van-e a munkamenetben vagy a virtuális gépen:

```powershell
Get-Module | Where-Object { $_.Name -Like "desktopvirtualization" }
```

Ha a WAN használatával eltávolítja a modul egy meglévő példányát, és megkezdi az áttelepítést, futtassa a következő parancsmagot:

```powershell
Uninstall-Module Az.DesktopVirtualization
```

Ha a modul blokkolva van a virtuális gépen, futtassa ezt a parancsmagot a tiltás feloldásához:

```powershell
Unblock-File "<path>\Az.DesktopVirtualization.psm1"
```

Ezzel a törléssel elfogyott az idő a modul importálásához.

1. Futtassa a következő parancsmagot, majd nyomja le az **R** billentyűt, amikor a rendszer felszólítja, hogy fogadja el az egyéni kód futtatását.

   ```powershell
   Import-Module -Name "<path>\Az.DesktopVirtualization.psm1" -Verbose
   ```

2. Miután futtatta az importálási parancsmagot, ellenőrizze, hogy rendelkezik-e a MSIX-parancsmagokkal a következő parancsmag futtatásával:

   ```powershell
   Get-Command -Module Az.DesktopVirtualization | Where-Object { $_.Name -match "MSIX" }
   ```

   Ha a parancsmagok vannak, a kimenetnek a következőhöz hasonlóan kell kinéznie:

   ```powershell
   CommandType     Name                                               Version    Source

   -----------     ----                                               -------    ------

   Function        Expand-AzWvdMsixImage                              0.0        Az.DesktopVirtualization

   Function        Get-AzWvdMsixPackage                               0.0        Az.DesktopVirtualization

   Function        New-AzWvdMsixPackage                               0.0        Az.DesktopVirtualization

   Function        Remove-AzWvdMsixPackage                            0.0        Az.DesktopVirtualization

   Function        Update-AzWvdMsixPackage                            0.0        Az.DesktopVirtualization
   ```

   Ha nem látja ezt a kimenetet, zárjunk be minden PowerShell-és PowerShell Core-munkamenetet, és próbálkozzon újra.

## <a name="set-up-helper-variables"></a>Segítő változók beállítása

Miután importálta a modult, be kell állítania a segítő változókat. Az alábbi példák azt mutatják be, hogyan végezheti el az egyes műveleteket.

Az előfizetés-azonosító beszerzése:

```powershell
Get-AzContext -ListAvailable | fl
```

Egy Azure-bérlő és-előfizetés környezetének kiválasztása a következő névvel:

```powershell
$obj = Select-AzContext -Name "<Name>"
```

Az előfizetési változó beállítása:

```powershell
$subId = $obj.Subscription.Id
```

A munkaterület nevének beállítása:

```powershell
$ws = "<WorksSpaceName>"
```

Az alkalmazáskészlet nevének beállítása:

```powershell
$hp = "<HostPoolName>"
```

Az erőforráscsoport beállítása, amelyben a munkamenet-gazda virtuális gépek konfigurálva vannak:

```powershell
$rg = "<ResourceGroupName>"
```

Végül pedig ellenőrizze, hogy helyesen állította-e be az összes változót:

```powershell
Get-AzWvdWorkspace -Name $ws -ResourceGroupName $rg -SubscriptionId $subID
```

## <a name="add-an-msix-package-to-a-host-pool"></a>MSIX-csomag hozzáadása gazdagép-készlethez

Ha mindent beállított, itt az ideje, hogy hozzáadja a MSIX-csomagot egy gazdagép-készlethez. Ehhez először be kell szereznie az MSIX-rendszerkép UNC elérési útját.

Az UNC elérési úton futtassa ezt a parancsmagot a MSIX rendszerkép kibontásához:

```powershell
$obj = Expand-AzWvdMsixImage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subID -Uri <UNCPath>
```

Futtassa ezt a parancsmagot a MSIX-csomag a kívánt alkalmazáskészlethez való hozzáadásához:

```powershell
New-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId -PackageAlias $obj.PackageAlias -DisplayName <DisplayName> -ImagePath <UNCPath> -IsActive:$true
```

Ha elkészült, erősítse meg, hogy a csomag a következő parancsmaggal lett létrehozva:

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId | Where-Object {$_.PackageFamilyName -eq $obj.PackageFamilyName}
```

## <a name="remove-an-msix-package-from-a-host-pool"></a>MSIX-csomag eltávolítása egy gazdagépről

Csomag eltávolítása az állomásról:

Szerezze be a parancsmaggal rendelkező gazdagépekhez társított összes csomag listáját, majd keresse meg a kimenetben eltávolítani kívánt csomag nevét:

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId 
```

Azt is megteheti, hogy egy adott csomagot is kap a megjelenítendő neve alapján a következő parancsmaggal:

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId | Where-Object { $_.Name -like "Power" }
```

A csomag eltávolításához futtassa a következő parancsmagot:

```powershell
Remove-AzWvdMsixPackage -FullName $obj.PackageFullName -HostPoolName $hp -ResourceGroupName $rg
```

## <a name="publish-msix-apps-to-an-app-group"></a>MSIX-alkalmazások közzététele az alkalmazás-csoportokban

A szakasz utasításait csak akkor követheti el, ha az előző szakaszokban szereplő utasításokat követte. Ha van egy aktív munkamenet-gazdagéptel rendelkező gazdagépe, legalább egy asztali alkalmazáscsoport, és egy MSIX-csomagot adott hozzá a gazdagéphez, készen áll a folytatásra.

Ha közzé szeretne tenni egy alkalmazást a MSIX-csomagból egy alkalmazás-csoportba, meg kell találnia a nevét, majd ezt a nevet kell használnia a közzétételi parancsmagban.

Alkalmazás közzététele:

Futtassa ezt a parancsmagot az összes elérhető alkalmazás csoport listázásához:

```powershell
Get-AzWvdApplicationGroup -ResourceGroupName $rg -SubscriptionId $subId
```

Ha megtalálta annak az alkalmazás-csoportnak a nevét, amelyen közzé kívánja tenni az alkalmazásokat, használja a következő parancsmag nevét:

```powershell
$grName = "<AppGroupName>"
```

Végül közzé kell tennie az alkalmazást.

- A MSIX-alkalmazás asztali alkalmazás-csoportba való közzétételéhez futtassa a következő parancsmagot:

   ```powershell
   New-AzWvdApplication -ResourceGroupName $rg -SubscriptionId $subId -Name PowerBi -ApplicationType MsixApplication -ApplicationGroupName $grName -MsixPackageFamilyName $obj.PackageFamilyName -CommandLineSetting 0
   ```

- Az alkalmazás távoli alkalmazás-csoportba való közzétételéhez futtassa a következő parancsmagot:

   ```powershell
   New-AzWvdApplication -ResourceGroupName $rg -SubscriptionId $subId -Name PowerBi -ApplicationType MsixApplication -ApplicationGroupName $grName -MsixPackageFamilyName $obj.PackageFamilyName -CommandLineSetting 0 -MsixPackageApplicationId $obj.PackageApplication.AppId
   ```

>[!NOTE]
>Ha egy felhasználó egy távoli alkalmazás-csoporthoz és egy asztali alkalmazáscsoport ugyanahhoz a készlethez van hozzárendelve, amikor a felhasználó a távoli asztalhoz csatlakozik, akkor mindkét csoport MSIX-alkalmazásait fogja látni.

## <a name="next-steps"></a>Következő lépések

Kérje meg a közösségi kérdéseket a szolgáltatással kapcsolatban a [Windows rendszerű virtuális asztali TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

A Windows rendszerű virtuális asztalok visszajelzéseit a [Windows Virtual Desktop visszajelzési központja](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)is elhagyhatja.

Az alábbi cikkek hasznosnak bizonyulnak:

- [MSIX-alkalmazás szószedetének csatolása](app-attach-glossary.md)
- [MSIX-alkalmazás csatolása – GYIK](app-attach-faq.md)
