---
title: Windows rendszerű virtuális asztali (klasszikus) szolgáltatás egyszerű szerepkör-hozzárendelése – Azure
description: Egyszerű szolgáltatások létrehozása és szerepkörök kiosztása a PowerShell használatával a Windows Virtual Desktopban (klasszikus).
author: Heidilohr
ms.topic: tutorial
ms.date: 05/27/2020
ms.author: helohr
ms.custom: devx-track-azurepowershell
manager: femila
ms.openlocfilehash: 535ee2dace742a57877c9673e77bee64fd81c456
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445040"
---
# <a name="tutorial-create-service-principals-and-role-assignments-with-powershell-in-windows-virtual-desktop-classic"></a>Oktatóanyag: egyszerű szolgáltatások és szerepkör-hozzárendelések létrehozása a PowerShell-lel a Windows Virtual Desktopban (klasszikus)

>[!IMPORTANT]
>Ez a tartalom a Windows rendszerű virtuális asztali (klasszikus) verzióra vonatkozik, amely nem támogatja Azure Resource Manager Windows rendszerű virtuális asztali objektumokat.

Az egyszerű szolgáltatások olyan identitások, amelyeket a Azure Active Directory létrehozhat a szerepkörök és engedélyek adott célra való hozzárendeléséhez. A Windows virtuális asztal szolgáltatásban létrehozhat egy egyszerű szolgáltatásnevet a következőhöz:

- Adott Windowsos virtuális asztali felügyeleti feladatok automatizálása.
- Használjon hitelesítő adatokat az MFA-kötelező felhasználók helyett, amikor a Windows rendszerű virtuális asztali számítógépeken bármilyen Azure Resource Manager sablont futtat.

Az oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Egyszerű szolgáltatásnév létrehozása Azure Active Directoryban.
> * Szerepkör-hozzárendelés létrehozása a Windows rendszerű virtuális asztalon.
> * Jelentkezzen be a Windows rendszerű virtuális asztalra az egyszerű szolgáltatásnév használatával.

## <a name="prerequisites"></a>Előfeltételek

Az egyszerű szolgáltatások és a szerepkör-hozzárendelések létrehozása előtt három dolgot kell tennie:

1. Telepítse a AzureAD modult. A modul telepítéséhez futtassa a PowerShellt rendszergazdaként, és futtassa a következő parancsmagot:

    ```powershell
    Install-Module AzureAD
    ```

2. [Töltse le és importálja a Windows rendszerű virtuális asztali PowerShell-modult](/powershell/windows-virtual-desktop/overview/).

3. Kövesse a cikkben szereplő összes útmutatást ugyanabban a PowerShell-munkamenetben. Előfordulhat, hogy a folyamat nem működik, ha megszakítja a PowerShell-munkamenetet, ha bezárja az ablakot, és később újra megnyitja.

## <a name="create-a-service-principal-in-azure-active-directory"></a>Egyszerű szolgáltatás létrehozása az Azure Active Directory-ban

Miután teljesítette az előfeltételeket a PowerShell-munkamenetben, futtassa a következő PowerShell-parancsmagokat egy több-bérlős szolgáltatásnév létrehozásához az Azure-ban.

```powershell
Import-Module AzureAD
$aadContext = Connect-AzureAD
$svcPrincipal = New-AzureADApplication -AvailableToOtherTenants $true -DisplayName "Windows Virtual Desktop Svc Principal"
$svcPrincipalCreds = New-AzureADApplicationPasswordCredential -ObjectId $svcPrincipal.ObjectId
```
## <a name="view-your-credentials-in-powershell"></a>Hitelesítő adatok megtekintése a PowerShellben

Mielőtt létrehozza a szerepkör-hozzárendelést a szolgáltatásnév számára, tekintse meg a hitelesítő adatait, és jegyezze fel őket későbbi használatra. A jelszó különösen azért fontos, mert a PowerShell-munkamenet lezárása után nem fogja tudni lekérni azt.

A következő három hitelesítő adatot kell leírnia, és a futtatni kívánt parancsmagokat kell lekérnie:

- Jelszó:

    ```powershell
    $svcPrincipalCreds.Value
    ```

- Bérlő azonosítója:

    ```powershell
    $aadContext.TenantId.Guid
    ```

- Alkalmazásazonosító:

    ```powershell
    $svcPrincipal.AppId
    ```

## <a name="create-a-role-assignment-in-windows-virtual-desktop"></a>Szerepkör-hozzárendelés létrehozása a Windows rendszerű virtuális asztalon

Ezután létre kell hoznia egy szerepkör-hozzárendelést, hogy az egyszerű szolgáltatásnév be tudja jelentkezni a Windows rendszerű virtuális asztalra. Győződjön meg arról, hogy olyan fiókkal jelentkezik be, amely jogosult szerepkör-hozzárendelések létrehozására.

Először [töltse le és importálja a](/powershell/windows-virtual-desktop/overview/) PowerShell-munkamenetben használni kívánt Windows virtuális asztali PowerShell-modult, ha még nem tette meg.

Futtassa a következő PowerShell-parancsmagokat a Windows rendszerű virtuális asztalhoz való kapcsolódáshoz és a bérlők megjelenítéséhez.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Get-RdsTenant
```

Amikor megkeresi a bérlő nevét ahhoz a bérlőhöz, amelyhez szerepkör-hozzárendelést kíván létrehozni, használja a következő parancsmagot a névvel:

```powershell
$myTenantName = "<Windows Virtual Desktop Tenant Name>"
New-RdsRoleAssignment -RoleDefinitionName "RDS Owner" -ApplicationId $svcPrincipal.AppId -TenantName $myTenantName
```

## <a name="sign-in-with-the-service-principal"></a>Bejelentkezés az egyszerű szolgáltatással

Miután létrehozta az egyszerű szolgáltatáshoz tartozó szerepkör-hozzárendelést, győződjön meg arról, hogy a szolgáltatásnév a következő parancsmag futtatásával tud bejelentkezni a Windows rendszerű virtuális asztalra:

```powershell
$creds = New-Object System.Management.Automation.PSCredential($svcPrincipal.AppId, (ConvertTo-SecureString $svcPrincipalCreds.Value -AsPlainText -Force))
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com" -Credential $creds -ServicePrincipal -AadTenantId $aadContext.TenantId.Guid
```

Miután bejelentkezett, győződjön meg róla, hogy minden működik, ha tesztel néhány Windowsos virtuális asztali PowerShell-parancsmagot az egyszerű szolgáltatással.

## <a name="next-steps"></a>Következő lépések

Miután létrehozta a szolgáltatásnevet, és hozzárendelte azt egy szerepkörhöz a Windows rendszerű virtuális asztali bérlőben, a használatával létrehozhat egy gazdagépet. Ha többet szeretne megtudni a gazdagép-készletekről, folytassa a gazdagépek Windows rendszerű virtuális asztali gépen való létrehozásának oktatóanyagával.

 > [!div class="nextstepaction"]
 > [Gazdagépcsoport létrehozása az Azure Marketplace-en](create-host-pools-azure-marketplace-2019.md)
