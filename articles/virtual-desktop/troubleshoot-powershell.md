---
title: Windows rendszerű virtuális asztali PowerShell – Azure
description: A PowerShell hibáinak elhárítása a Windows rendszerű virtuális asztali környezet beállításakor.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 06/05/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: cd02ee8ab794858566aeafa96fa78919be3b85a5
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100367622"
---
# <a name="windows-virtual-desktop-powershell"></a>Windows Virtual Desktop – PowerShell

>[!IMPORTANT]
>Ez a tartalom a Windows rendszerű virtuális asztali Azure Resource Manager Windows virtuális asztali objektumokkal vonatkozik. Ha a Windowsos virtuális asztalt (klasszikus) Azure Resource Manager objektumok nélkül használja, tekintse meg [ezt a cikket](./virtual-desktop-fall-2019/troubleshoot-powershell-2019.md).

Ez a cikk a PowerShell és a Windows rendszerű virtuális asztal használatával kapcsolatos hibák és problémák megoldására használható. Távoli asztali szolgáltatások PowerShell-lel kapcsolatos további információkért lásd: [Windows virtuális asztali PowerShell](/powershell/windows-virtual-desktop/overview).

## <a name="provide-feedback"></a>Visszajelzés küldése

Látogasson el a [Windows rendszerű virtuális asztali technikai Közösségbe](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) , és beszéljen a Windows Virtual Desktop szolgáltatásról a termék csapatával és az aktív közösség tagjaival.

## <a name="powershell-commands-used-during-windows-virtual-desktop-setup"></a>A Windows rendszerű virtuális asztali telepítés során használt PowerShell-parancsok

Ez a szakasz azokat a PowerShell-parancsokat sorolja fel, amelyek a Windows rendszerű virtuális asztali környezet beállításakor használatosak, és módot biztosít a használat közben felmerülő problémák megoldására.

### <a name="error-new-azroleassignment-the-provided-information-does-not-map-to-an-ad-object-id"></a>Hiba: New-AzRoleAssignment: a megadott információ nem AD-objektum-AZONOSÍTÓra van leképezve

```powershell
New-AzRoleAssignment -SignInName "admins@contoso.com" -RoleDefinitionName "Desktop Virtualization User" -ResourceName "0301HP-DAG" -ResourceGroupName 0301RG -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

**OK:** A *-SignInName* paraméter által megadott felhasználó nem található a Windows rendszerű virtuális asztali környezethez kötött Azure Active Directoryban.

**Javítás:** Győződjön meg arról, hogy az alábbi dolgok vannak.

- A felhasználónak szinkronizálva kell lennie Azure Active Directory.
- A felhasználó nem köthető a vállalatok közötti (B2C) vagy a vállalatközi (B2B) kereskedelemhez.
- A Windows rendszerű virtuális asztali környezetnek megfelelő Azure Active Directoryhoz kell kötnie.

### <a name="error-new-azroleassignment-the-client-with-object-id-does-not-have-authorization-to-perform-action-over-scope-code-authorizationfailed"></a>Hiba: New-AzRoleAssignment: "a (z) azonosítójú ügyfél nem rendelkezik a hatókörön belüli művelet végrehajtásához szükséges engedéllyel (kód: AuthorizationFailed)"

**1. ok:** A használt fiók nem rendelkezik tulajdonosi engedélyekkel az előfizetéshez.

**1. javítás:** A tulajdonosi engedélyekkel rendelkező felhasználónak végre kell hajtania a szerepkör-hozzárendelést. Azt is megteheti, hogy a felhasználót hozzá kell rendelnie a felhasználói hozzáférés rendszergazdai szerepkörhöz, hogy egy felhasználót rendeljen hozzá egy alkalmazás-csoportjához.

**2. ok:** A használt fiók tulajdonosi engedélyekkel rendelkezik, de nem része a környezet Azure Active Directoryjának, vagy nem rendelkezik engedéllyel a felhasználó helyét tartalmazó Azure Active Directory lekérdezéséhez.

**2. javítás:** Active Directory engedélyekkel rendelkező felhasználónak végre kell hajtania a szerepkör-hozzárendelést.

### <a name="error-new-azwvdhostpool----the-location-is-not-available-for-resource-type"></a>Hiba: New-AzWvdHostPool – a hely nem érhető el az erőforrás típusaként

```powershell
New-AzWvdHostPool_CreateExpanded: The provided location 'southeastasia' is not available for resource type 'Microsoft.DesktopVirtualization/hostpools'. List of available regions for the resource type is 'eastus,eastus2,westus,westus2,northcentralus,southcentralus,westcentralus,centralus'.
```

Ok: a Windows virtuális asztal támogatja a gazdagépek, az alkalmazáscsoport és a munkaterületek helyének kiválasztását a szolgáltatási metaadatok bizonyos helyszíneken való tárolásához. A lehetőségek csak a funkció elérhetőségére korlátozódnak. Ez a hiba azt jelenti, hogy a szolgáltatás nem érhető el a kiválasztott helyen.

Javítás: a hibaüzenetben a támogatott régiók listája jelenik meg. Ehelyett használja a támogatott régiók egyikét.

### <a name="error-new-azwvdapplicationgroup-must-be-in-same-location-as-host-pool"></a>Hiba: a New-AzWvdApplicationGroupnak a gazdagép-készlettel azonos helyen kell lennie

```powershell
New-AzWvdApplicationGroup_CreateExpanded: ActivityId: e5fe6c1d-5f2c-4db9-817d-e423b8b7d168 Error: ApplicationGroup must be in same location as associated HostPool
```

**OK:** A hely nem egyezik. Az összes gazdagép, alkalmazáscsoport és munkaterület rendelkezik egy hellyel a szolgáltatás metaadatainak tárolásához. Az egymáshoz társított összes objektumnak ugyanazon a helyen kell lennie. Ha például egy címkészlet található a-ben `eastus` , akkor a alkalmazásban is létre kell hoznia az alkalmazás-csoportokat `eastus` . Ha létrehoz egy munkaterületet, amely az alkalmazás-csoportokat regisztrálja, akkor a munkaterületnek is be kell jelentkeznie `eastus` .

**Javítás:** Kérje le azt a helyet, amelyben a gazdagép létre lett hozva, majd rendelje hozzá a létrehozandó alkalmazáscsoport ugyanahhoz a helyhez.

## <a name="next-steps"></a>Következő lépések

- A Windows rendszerű virtuális asztalok és a eszkalációs sávok hibaelhárításával kapcsolatban lásd: [Hibaelhárítás – áttekintés, visszajelzés és támogatás](troubleshoot-set-up-overview.md).
- A Windows rendszerű virtuális asztali környezet és a gazdagép-készletek beállítása során felmerülő problémák elhárításához tekintse meg a [környezet és az alkalmazáskészlet létrehozása](troubleshoot-set-up-issues.md)című témakört.
- A virtuális gép (VM) Windows rendszerű virtuális asztali gépen való konfigurálása során felmerülő problémák elhárításával kapcsolatban lásd: a [munkamenet-gazdagép virtuális gép konfigurálása](troubleshoot-vm-configuration.md).
- A Windows rendszerű virtuális asztali ügyfélkapcsolatokkal kapcsolatos problémák elhárításához tekintse meg a [Windows rendszerű virtuális asztali szolgáltatások kapcsolatai](troubleshoot-service-connection.md)című témakört.
- Távoli asztal-ügyfelekkel kapcsolatos problémák elhárításához tekintse meg [a távoli asztal-ügyfél hibaelhárítása](troubleshoot-client.md) című témakört.
- A szolgáltatással kapcsolatos további tudnivalókért tekintse meg a [Windows rendszerű virtuális asztali környezet](environment-setup.md)című témakört.
- További információ a naplózási műveletekről: [műveletek naplózása a Resource Managerrel](../azure-resource-manager/management/view-activity-logs.md).
- Az üzembe helyezés során felmerülő hibák meghatározásával kapcsolatos további tudnivalókért lásd: [telepítési műveletek megtekintése](../azure-resource-manager/templates/deployment-history.md).
