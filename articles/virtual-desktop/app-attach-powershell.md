---
title: Windows Virtual Desktop MSIX-alkalmazás csatlakoztatása a PowerShellhez – Azure
description: MSIX-alkalmazás csatolásának beállítása Windows Virtual Desktop PowerShell használatával.
author: Heidilohr
ms.topic: how-to
ms.date: 04/13/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: f44cbf3764063c511c896f11bb7ebfaae2973f0c
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365399"
---
# <a name="set-up-msix-app-attach-using-powershell"></a>MSIX-alkalmazás csatolásának beállítása a PowerShell használatával

A Azure Portal manuálisan is beállíthatja az MSIX-alkalmazás csatolását a PowerShell-sel. Ez a cikk bemutatja, hogyan állíthatja be az MSIX-alkalmazás csatolását a PowerShell használatával.

## <a name="requirements"></a>Követelmények

>[!IMPORTANT]
>Mielőtt hozzá is kezd, töltse ki [](https://aka.ms/enablemsixappattach) és küldje el ezt az űrlapot, hogy engedélyezze az MSIX-alkalmazás csatolását az előfizetésben. Ha nem rendelkezik jóváhagyott kérelemvel, az MSIX-alkalmazás csatolása nem fog működni. A kérelmek jóváhagyása a munkanapok során akár 24 órát is igénybe vehet. E-mailt fog kapni, ha a kérést elfogadták és befejezték.

Az MSIX-alkalmazás csatolásának konfiguráláshoz a következőre lesz szüksége:

- Egy működő Windows Virtual Desktop üzembe helyezéshez. A (klasszikus) Windows Virtual Desktop bérlők üzembe helyezésének elsajátításért lásd: Bérlő létrehozása [a Windows Virtual Desktop.](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md) A gazdagépkészletek Windows Virtual Desktop telepítésével kapcsolatos Azure Resource Manager lásd: Gazdagépkészlet létrehozása [a Azure Portal.](./create-host-pools-azure-marketplace.md)
- Egy Windows Virtual Desktop gazdagépkészlet legalább egy aktív munkamenetgazdával.
- Ennek a gazdagépkészletnek az érvényesítési környezetben kell lennie.
- Távoli asztali alkalmazáscsoport.
- Az MSIX csomagolási eszköz.
- MsIX-csomagba csomagolt alkalmazás egy fájlmegosztásba feltöltött MSIX-rendszerképbe kibontva.
- A fájlmegosztás a Windows Virtual Desktop, ahol az MSIX-csomagot tárolni fogja.
- Annak a fájlmegosztásnak, ahová az MSIX-rendszerképet feltöltötte, elérhetőnek kell lennie a gazdagépkészlet összes virtuális gépe (VM) számára is. A felhasználóknak csak olvasási engedélyekkel kell rendelkezniük a rendszerkép eléréséhez.
- Töltse le és telepítse PowerShell Core.
- Töltse le a nyilvános előzetes Azure PowerShell modult, és bontsa ki egy helyi mappára.
- Telepítse az Azure-modult a következő parancsmag futtatásával:

    ```powershell
    Install-Module -Name Az -Force
    ```

## <a name="sign-in-to-azure-and-import-the-module"></a>Jelentkezzen be az Azure-ba, és importálja a modult

Ha minden követelmény készen áll, nyissa meg a PowerShell Core-t egy rendszergazda jogú parancssorban, és futtassa a következő parancsmagot:

```powershell
Connect-AzAccount
```

A futtatás után hitelesítse a fiókját a hitelesítő adataival. Ebben az esetben a rendszer az eszköz URL-címét vagy jogkivonatát kéri.

## <a name="import-the-azwindowsvirtualdesktop-module"></a>Az Az.WindowsVirtualDesktop modul importálása

Az Ebben a cikkben található utasításoknak megfelelően az Az.DesktopVirtualization modulra lesz szüksége.

>[!NOTE]
>A nyilvános előzetes verzióhoz a modult külön ZIP-fájlokként biztosítjuk, amelyek manuálisan importálhatóak.

Mielőtt elkezdené, a következő parancsmag futtatásával láthatja, hogy az Az.DesktopVirtualization modul már telepítve van-e a munkameneten vagy virtuális gépen:

```powershell
Get-Module | Where-Object { $_.Name -Like "desktopvirtualization" }
```

Ha el kell távolítania a modul egy meglévő példányát, és elölről kell kezdenie, futtassa a következő parancsmagot:

```powershell
Uninstall-Module Az.DesktopVirtualization
```

Ha a modul le van tiltva a virtuális gépen, futtassa ezt a parancsmagot a tiltás feloldásához:

```powershell
Unblock-File "<path>\Az.DesktopVirtualization.psm1"
```

A tisztítás után ideje importálni a modult.

1. Futtassa a következő parancsmagot, majd nyomja le az **R** billentyűt, amikor a rendszer kéri, hogy egyeztesen az egyéni kód futtatásával.

   ```powershell
   Import-Module -Name "<path>\Az.DesktopVirtualization.psm1" -Verbose
   ```

2. Az importálási parancsmag futtatása után a következő parancsmag futtatásával ellenőrizze, hogy az tartalmazza-e az MSIX-parancsmagokat:

   ```powershell
   Get-Command -Module Az.DesktopVirtualization | Where-Object { $_.Name -match "MSIX" }
   ```

   Ha a parancsmagok vannak, a kimenetnek így kell kinéznie:

   ```powershell
   CommandType     Name                                               Version    Source

   -----------     ----                                               -------    ------

   Function        Expand-AzWvdMsixImage                              0.0        Az.DesktopVirtualization

   Function        Get-AzWvdMsixPackage                               0.0        Az.DesktopVirtualization

   Function        New-AzWvdMsixPackage                               0.0        Az.DesktopVirtualization

   Function        Remove-AzWvdMsixPackage                            0.0        Az.DesktopVirtualization

   Function        Update-AzWvdMsixPackage                            0.0        Az.DesktopVirtualization
   ```

   Ha nem látja ezt a kimenetet, zárja be az összes PowerShellt, és PowerShell Core munkameneteket, és próbálkozzon újra.

## <a name="set-up-helper-variables"></a>Segítő változók beállítása

A modul importálása után be kell állítania a segítő változókat. Az alábbi példák bemutatják, hogyan kell mindegyiket megtenni.

Az előfizetés azonosítójának lekért száma:

```powershell
Get-AzContext -ListAvailable | fl
```

Azure-bérlő és -előfizetés környezetének kiválasztása névvel:

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

A gazdagépkészlet nevének beállítása:

```powershell
$hp = "<HostPoolName>"
```

Az erőforráscsoport beállítása, amelyben a munkamenetgazda virtuális gépei konfigurálva vannak:

```powershell
$rg = "<ResourceGroupName>"
```

Végül pedig ellenőrizze, hogy helyesen beállította-e az összes változót:

```powershell
Get-AzWvdWorkspace -Name $ws -ResourceGroupName $rg -SubscriptionId $subID
```

## <a name="add-an-msix-package-to-a-host-pool"></a>MSIX-csomag hozzáadása gazdagépkészlethez

Miután mindent beállított, itt az ideje, hogy hozzáadja az MSIX-csomagot egy gazdagépkészlethez. Ezt először az MSIX-rendszerkép UNC elérési útjának be kell szereznie.

Az UNC elérési út használatával futtassa ezt a parancsmagot az MSIX-rendszerkép kibontásához:

```powershell
$obj = Expand-AzWvdMsixImage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subID -Uri <UNCPath>
```

A parancsmag futtatásával adja hozzá az MSIX-csomagot a kívánt gazdagépkészlethez:

```powershell
New-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId -PackageAlias $obj.PackageAlias -DisplayName <DisplayName> -ImagePath <UNCPath> -IsActive:$true
```

Ha végzett, a következő parancsmaggal ellenőrizze, hogy a csomag létrejött-e:

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId | Where-Object {$_.PackageFamilyName -eq $obj.PackageFamilyName}
```

## <a name="remove-an-msix-package-from-a-host-pool"></a>MSIX-csomag eltávolítása gazdagépkészletből

Csomag eltávolítása gazdagépkészletből:

Szerezze be a parancsmaggal a gazdagépkészlethez társított összes csomag listáját, majd keresse meg az eltávolítani kívánt csomag nevét a kimenetben:

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId 
```

A következő parancsmaggal le is kaphat egy adott csomagot a megjelenített neve alapján:

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId | Where-Object { $_.Name -like "Power" }
```

A csomag eltávolításához futtassa a következő parancsmagot:

```powershell
Remove-AzWvdMsixPackage -FullName $obj.PackageFullName -HostPoolName $hp -ResourceGroupName $rg
```

## <a name="publish-msix-apps-to-an-app-group"></a>MSIX-alkalmazások közzététele alkalmazáscsoportban

Csak akkor kövesse az ebben a szakaszban található utasításokat, ha befejezte az előző szakaszokban található utasításokat. Ha aktív munkamenetgazdával, legalább egy asztali alkalmazáscsoporttal rendelkezik egy gazdagépkészletben, és MSIX-csomagot adott hozzá a gazdagépkészlethez, készen áll az indulásra.

Ha az MSIX-csomagból közzétenné az alkalmazást egy alkalmazáscsoportban, meg kell találnia a nevét, majd ezt a nevet kell használnia a közzétételi parancsmagban.

Alkalmazás közzététele:

Futtassa ezt a parancsmagot az összes elérhető alkalmazáscsoport listához:

```powershell
Get-AzWvdApplicationGroup -ResourceGroupName $rg -SubscriptionId $subId
```

Ha megtalálta annak az alkalmazáscsoportnak a nevét, amelybe alkalmazásokat szeretne közzétenni, használja a nevét ebben a parancsmagban:

```powershell
$grName = "<AppGroupName>"
```

Végül közzé kell tennie az alkalmazást.

- Az MSIX-alkalmazás asztali alkalmazáscsoportban való közzétételéhez futtassa a következő parancsmagot:

   ```powershell
   New-AzWvdApplication -ResourceGroupName $rg -SubscriptionId $subId -Name PowerBi -ApplicationType MsixApplication -ApplicationGroupName $grName -MsixPackageFamilyName $obj.PackageFamilyName -CommandLineSetting 0
   ```

- Ha az alkalmazást egy távoli alkalmazáscsoportban is közzéteheti, futtassa inkább a következő parancsmagot:

   ```powershell
   New-AzWvdApplication -ResourceGroupName $rg -SubscriptionId $subId -Name PowerBi -ApplicationType MsixApplication -ApplicationGroupName $grName -MsixPackageFamilyName $obj.PackageFamilyName -CommandLineSetting 0 -MsixPackageApplicationId $obj.PackageApplication.AppId
   ```

>[!NOTE]
>Ha egy felhasználó egy távoli alkalmazáscsoporthoz és egy asztali alkalmazáscsoporthoz is hozzá van rendelve ugyanabban a gazdagépkészletben, amikor a felhasználó a távoli asztalhoz csatlakozik, mindkét csoport MSIX-alkalmazásait láthatja.

## <a name="next-steps"></a>Következő lépések

A funkcióval kapcsolatos kérdéseit a [TechCo Windows Virtual Desktop fel.](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)

Visszajelzést is küldhet a Windows Virtual Desktop a [Windows Virtual Desktop visszajelzési központban.](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)

Íme néhány további cikk, amelyek hasznosak lehetnek:

- [MSIX-alkalmazás csatolása – szószedet](app-attach-glossary.md)
- [MSIX-alkalmazás csatolása – GYIK](app-attach-faq.md)
