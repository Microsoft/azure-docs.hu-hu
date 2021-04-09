---
title: Windows rendszerű virtuális asztali környezetbeli gazdagép-készlet létrehozása – Azure
description: A bérlői és a gazdagép-készletezési problémák elhárítása és megoldása a Windows rendszerű virtuális asztali környezet telepítése során.
author: Heidilohr
ms.topic: troubleshooting
ms.custom: references_regions
ms.date: 02/17/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: c31a32b32a685087c53198ec52af1188d0071cab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100652445"
---
# <a name="host-pool-creation"></a>Gazdagépkészlet létrehozása

>[!IMPORTANT]
>Ez a tartalom a Windows rendszerű virtuális asztali Azure Resource Manager Windows virtuális asztali objektumokkal vonatkozik. Ha a Windowsos virtuális asztalt (klasszikus) Azure Resource Manager objektumok nélkül használja, tekintse meg [ezt a cikket](./virtual-desktop-fall-2019/troubleshoot-set-up-issues-2019.md).

Ez a cikk a Windows rendszerű virtuális asztali bérlő és a kapcsolódó munkamenet-gazdagépek infrastruktúrájának kezdeti beállítása során felmerülő problémákat ismerteti.

## <a name="provide-feedback"></a>Visszajelzés küldése

Látogasson el a [Windows rendszerű virtuális asztali technikai Közösségbe](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) , és beszéljen a Windows Virtual Desktop szolgáltatásról a termék csapatával és az aktív közösség tagjaival.

## <a name="acquiring-the-windows-10-enterprise-multi-session-image"></a>A Windows 10 Enterprise több munkamenetet ábrázoló rendszerképének beszerzése

A Windows 10 Enterprise multi-session rendszerképek használatához nyissa meg az Azure Marketplace-t , válassza a  >  **Microsoft Windows 10** > és a [Windows 10 Enterprise multi-session, 1809-es verziójának](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsdesktop.windows-10?tab=PlansAndPrice)első lépései lehetőséget.

## <a name="issues-with-using-the-azure-portal-to-create-host-pools"></a>A gazdagépek létrehozásával kapcsolatos problémák a Azure Portal használatával

### <a name="error-create-a-free-account-appears-when-accessing-the-service"></a>Hiba: az "ingyenes fiók létrehozása" üzenet jelenik meg a szolgáltatás elérésekor

> [!div class="mx-imgBorder"]
> ![Az "ingyenes fiók létrehozása" üzenetet megjelenítő Azure Portal ábrázoló kép](media/create-new-account.png)

**OK**: nincsenek aktív előfizetések abban a fiókban, amelybe bejelentkezett az Azure-ba, vagy a fiók nem rendelkezik az előfizetések megtekintéséhez szükséges engedélyekkel.

**Javítás**: Jelentkezzen be az előfizetésbe, ahol a munkamenet-gazdagép virtuális gépei (VM-EK) egy olyan fiókkal telepíthetők, amely legalább közreműködői szintű hozzáféréssel rendelkezik.

### <a name="error-exceeding-quota-limit"></a>Hiba: "túllépte a kvóta korlátját"

Ha a művelet túllépi a kvóta korlátját, a következő műveletek közül választhat:

- Hozzon létre egy új címkészletet ugyanazzal a paraméterekkel, de kevesebb virtuális géppel és virtuálisgép-maggal.

- Nyissa meg a böngésző statusMessage mezőjében látható hivatkozást, hogy küldjön egy kérést az Azure-előfizetéshez tartozó kvóta növelésére a megadott virtuálisgép-SKU-ra vonatkozóan.

### <a name="error-cant-see-user-assignments-in-app-groups"></a>Hiba: a felhasználói hozzárendelések nem jelennek meg az alkalmazás csoportjaiban.

**OK**: Ez a hiba általában akkor fordul elő, ha áthelyezte az előfizetést 1 Azure Active Directory (ad) bérlőről egy másikra. Ha a régi hozzárendelések továbbra is a régi Azure AD-bérlőhöz vannak kötve, akkor a Azure Portal elvesznek.

**Javítás**: a felhasználókat újra hozzá kell rendelnie az alkalmazás-csoportokhoz.

### <a name="i-only-see-us-when-setting-the-location-for-my-service-objects"></a>Csak a szolgáltatási objektumok helyének beállításakor jelenik meg

**OK**: az Azure jelenleg nem támogatja ezt a régiót a Windows rendszerű virtuális asztali szolgáltatás számára. Ha szeretné megtudni, hogy mely földrajzi területek támogatottak, tekintse meg az [adathelyszíneket](data-locations.md). Ha a Windows rendszerű virtuális asztal támogatja a helyet, de továbbra sem jelenik meg, amikor egy helyet próbál meg kiválasztani, az azt jelenti, hogy az erőforrás-szolgáltató még nem frissült.

**Javítás**: a régiók legújabb listájának beszerzéséhez regisztrálja újra az erőforrás-szolgáltatót:

1. Lépjen az **előfizetések** elemre, és válassza ki a megfelelő előfizetést.
2. Válassza az **erőforrás-szolgáltató** elemet.
3. Válassza a **Microsoft. DesktopVirtualization** lehetőséget, majd a művelet menüben válassza az **újbóli regisztráció** lehetőséget.

Ha újra regisztrálja az erőforrás-szolgáltatót, nem jelenik meg konkrét felhasználói felületi visszajelzés vagy frissítési állapot. Az újbóli regisztrálási folyamat szintén nem zavarja a meglévő környezeteket.

## <a name="azure-resource-manager-template-errors"></a>Azure Resource Manager sablon hibái

A Azure Resource Manager-sablonok és a PowerShell DSC nem sikeres központi telepítésének hibakereséséhez kövesse az alábbi utasításokat.

1. Tekintse át az üzemelő példány hibáit a Azure Resource Manager használatával történő [központi telepítési műveletek megtekintésével](../azure-resource-manager/templates/deployment-history.md).
2. Ha nem találhatók hibák az üzemelő példányban, tekintse át a tevékenység naplójának hibáit a tevékenységek [megtekintése az erőforrásokon végzett naplózási műveleteknél](../azure-resource-manager/management/view-activity-logs.md).
3. A hiba észlelése után használja a hibaüzenetet és az erőforrásokat az [Azure telepítési hibáinak elhárításához Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md) a probléma megoldásához.
4. Törölje az előző központi telepítés során létrehozott erőforrásokat, majd próbálkozzon újra a sablon üzembe helyezésével.

### <a name="error-your-deployment-failedhostnamejoindomain"></a>Hiba: a telepítés nem sikerült.... \<hostname> /JoinDomain

> [!div class="mx-imgBorder"]
> ![A telepítés sikertelen képernyőkép.](media/failure-joindomain.png)

Nyers hiba – példa:

```Error
 {"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details.
 Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\": \"Failed\",\r\n \"error\":
 {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.
 \",\r\n \"details\": [\r\n {\r\n \"code\": \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has reported a failure when processing
 extension 'joindomain'. Error message: \\\"Exception(s) occurred while joining Domain 'diamondsg.onmicrosoft.com'\\\".\"\r\n }\r\n ]\r\n }\r\n}"}]}
```

**1. ok:** A virtuális gépek tartományhoz való csatlakoztatásához megadott hitelesítő adatok helytelenek.

**1. javítás:** Tekintse meg a "helytelen hitelesítő adatok" hibaüzenetet, ha a virtuális gépek nem csatlakoznak a tartományhoz a munkamenet-gazdagép virtuálisgép- [konfigurációjában](troubleshoot-vm-configuration.md).

**2. ok:** A tartománynév nem oldható fel.

**2. javítás:** Lásd a [következő hibát: a tartomány neve nem oldható](troubleshoot-vm-configuration.md#error-domain-name-doesnt-resolve) fel a munkamenet-gazdagép virtuálisgép- [konfigurációjában](troubleshoot-vm-configuration.md).

**3. ok:** A virtuális hálózat (VNET) DNS-konfigurációja **alapértelmezett** értékre van állítva.

A probléma megoldásához tegye a következőket:

1. Nyissa meg a Azure Portal, és lépjen a **Virtual Networks (virtuális hálózatok** ) lapra.
2. Keresse meg a VNET, majd válassza a **DNS-kiszolgálók** lehetőséget.
3. A DNS-kiszolgálók menünek a képernyő jobb oldalán kell megjelennie. Az adott menüben válassza az **Egyéni** lehetőséget.
4. Győződjön meg arról, hogy a DNS-kiszolgálók szerepelnek a tartományvezérlő vagy a Active Directory tartomány egyéni egyeztetése területén. Ha nem látja a DNS-kiszolgálót, akkor adja hozzá az értékét a **DNS-kiszolgáló hozzáadása** mezőben.

### <a name="error-your-deployment-failedunauthorized"></a>Hiba: Your deployment failed...\Unauthorized (Sikertelen üzembe helyezés...\Jogosulatlan)

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Unauthorized","message":"{\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Target\": null,\r\n \"Details\": [\r\n {\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n },\r\n {\r\n \"Code\": \"Unauthorized\"\r\n },\r\n {\r\n \"ErrorEntity\": {\r\n \"ExtendedCode\": \"52020\",\r\n \"MessageTemplate\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Parameters\": [\r\n \"default\"\r\n ],\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n }\r\n }\r\n ],\r\n \"Innererror\": null\r\n}"}]}
```

**OK:** Az Ön által használt előfizetés olyan típus, amely nem fér hozzá a szükséges funkciókhoz abban a régióban, ahol az ügyfél üzembe helyezését kísérli meg. Például az MSDN, az ingyenes vagy az oktatási előfizetések megjeleníthetik ezt a hibát.

**Javítás:** Módosítsa az előfizetés típusát vagy régióját olyanra, amely hozzáférhet a szükséges funkciókhoz.

### <a name="error-vmextensionprovisioningerror"></a>Hiba: VMExtensionProvisioningError

> [!div class="mx-imgBorder"]
> ![A központi telepítés képernyőképe sikertelen volt, mert a terminál kiépítési állapota nem sikerült.](media/failure-vmextensionprovisioning.png)

**1. ok:** Átmeneti hiba a Windows rendszerű virtuális asztali környezettel.

**2. ok:** Átmeneti hiba történt a kapcsolatban.

**Javítás:** A PowerShell használatával történő bejelentkezéssel ellenőrizze, hogy a Windows rendszerű virtuális asztali környezet kifogástalan állapotú-e. Fejezze be a virtuális gép regisztrációját manuálisan a [gazdagép létrehozása a PowerShell](create-host-pools-powershell.md)-lel.

### <a name="error-the-admin-username-specified-isnt-allowed"></a>Hiba: The Admin Username specified isn't allowed (A megadott rendszergazdai felhasználónév nem engedélyezett)

> [!div class="mx-imgBorder"]
> ![A központi telepítés képernyőképe nem sikerült, mert a megadott rendszergazda nem engedélyezett.](media/failure-username.png)

Nyers hiba – példa:

```Error
 { …{ "provisioningOperation":
 "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T20:53:18.904917Z", "duration": "PT3.0574505S", "trackingId":
 "1f460af8-34dd-4c03-9359-9ab249a1a005", "statusCode": "BadRequest", "statusMessage": { "error": { "code": "InvalidParameter", "message":
 "The Admin Username specified is not allowed.", "target": "adminUsername" } … }
```

**OK:** A megadott jelszó tiltott alkarakterláncokat tartalmaz (adminisztrátor, rendszergazda, gyökér).

**Javítás:** Frissítse a felhasználónevet, vagy használjon más felhasználókat.

### <a name="error-vm-has-reported-a-failure-when-processing-extension"></a>Hiba: VM has reported a failure when processing extension (A virtuális gép hibát jelentett egy bővítmény feldolgozásakor)

> [!div class="mx-imgBorder"]
> ![Az erőforrás-művelet képernyőképe a terminál kiépítési állapotával fejeződött be a telepítés során.](media/failure-processing.png)

Nyers hiba – példa:

```Error
{ … "code": "ResourceDeploymentFailure", "message":
 "The resource operation completed with terminal provisioning state 'Failed'.", "details": [ { "code":
 "VMExtensionProvisioningError", "message": "VM has reported a failure when processing extension 'dscextension'.
 Error message: \"DSC Configuration 'SessionHost' completed with error(s). Following are the first few:
 PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource functionality with error message:
 One or more errors occurred. The SendConfigurationApply function did not succeed.\"." } ] … }
```

**OK:** A PowerShell DSC bővítmény nem tudta beolvasni a rendszergazdai hozzáférést a virtuális gépen.

**Javítás:** Ellenőrizze, hogy a Felhasználónév és a jelszó rendszergazdai hozzáféréssel rendelkezik-e a virtuális gépen, majd futtassa újra a Azure Resource Manager sablont.

### <a name="error-deploymentfailed--powershell-dsc-configuration-firstsessionhost-completed-with-errors"></a>Hiba: DeploymentFailed – a (z) FirstSessionHost PowerShell DSC-konfiguráció hibával fejeződött be

> [!div class="mx-imgBorder"]
> ![A központi telepítés képernyőképe sikertelen, mert a PowerShell DSC-konfiguráció "FirstSessionHost" hibával fejeződött be.](media/failure-dsc.png)

Nyers hiba – példa:

```Error
{
    "code": "DeploymentFailed",
   "message": "At least one resource deployment operation failed. Please list
 deployment operations for details. 4 Please see https://aka.ms/arm-debug for usage details.",
 "details": [
         { "code": "Conflict",
         "message": "{\r\n \"status\": \"Failed\",\r\n \"error\": {\r\n \"code\":
         \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource
         operation completed with terminal provisioning state 'Failed'.\",\r\n
         \"details\": [\r\n {\r\n \"code\":
        \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has
              reported a failure when processing extension 'dscextension'.
              Error message: \\\"DSC Configuration 'FirstSessionHost'
              completed with error(s). Following are the first few:
              PowerShell DSC resource MSFT ScriptResource failed to
              execute Set-TargetResource functionality with error message:
              One or more errors occurred. The SendConfigurationApply
              function did not succeed.\\\".\"\r\n }\r\n ]\r\n }\r\n}"  }

```

**OK:** A PowerShell DSC bővítmény nem tudta beolvasni a rendszergazdai hozzáférést a virtuális gépen.

**Javítás:** Ellenőrizze, hogy a megadott Felhasználónév és jelszó rendszergazdai hozzáféréssel rendelkezik-e a virtuális gépen, majd futtassa újra a Azure Resource Manager sablont.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Hiba: DeploymentFailed – InvalidResourceReference

Nyers hiba – példa:

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation
failed. Please list deployment operations for details. Please see https://aka.ms/arm-
debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\":
\"Failed\",\r\n \"error\": {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n
\"message\": \"The resource operation completed with terminal provisioning state
'Failed'.\",\r\n \"details\": [\r\n {\r\n \"code\": \"DeploymentFailed\",\r\n
\"message\": \"At least one resource deployment operation failed. Please list
deployment operations for details. Please see https://aka.ms/arm-debug for usage
details.\",\r\n \"details\": [\r\n {\r\n \"code\": \"BadRequest\",\r\n \"message\":
\"{\\r\\n \\\"error\\\": {\\r\\n \\\"code\\\": \\\"InvalidResourceReference\\\",\\r\\n
\\\"message\\\": \\\"Resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/virtualNetworks/wvd-vnet/subnets/default
referenced by resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/networkInterfaces/erd. Please make sure that
the referenced resource exists, and that both resources are in the same
region.\\\",\\r\\n\\\"details\\\": []\\r\\n }\\r\\n}\"\r\n }\r\n ]\r\n }\r\n ]\r\n }\r\n}"}]}
```

**OK:** Az erőforráscsoport nevének egy részét a sablon által létrehozott egyes erőforrásokhoz használja a rendszer. A meglévő erőforrásoknak megfelelő név miatt a sablon egy másik csoportból is választhat egy meglévő erőforrást.

**Javítás:** Ha a Azure Resource Manager sablont a munkamenet-gazda virtuális gépek üzembe helyezéséhez futtatja, az első két karakternek egyedinek kell lennie az előfizetési erőforráscsoport neveként.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Hiba: DeploymentFailed – InvalidResourceReference

Nyers hiba – példa:

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation
failed. Please list deployment operations for details. Please see https://aka.ms/arm-
debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\":
\"Failed\",\r\n \"error\": {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n
\"message\": \"The resource operation completed with terminal provisioning state
'Failed'.\",\r\n \"details\": [\r\n {\r\n \"code\": \"DeploymentFailed\",\r\n
\"message\": \"At least one resource deployment operation failed. Please list
deployment operations for details. Please see https://aka.ms/arm-debug for usage
details.\",\r\n \"details\": [\r\n {\r\n \"code\": \"BadRequest\",\r\n \"message\":
\"{\\r\\n \\\"error\\\": {\\r\\n \\\"code\\\": \\\"InvalidResourceReference\\\",\\r\\n
\\\"message\\\": \\\"Resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/virtualNetworks/wvd-vnet/subnets/default
referenced by resource /subscriptions/EXAMPLE/resourceGroups/DEMO/providers/Microsoft.Network/networkInterfaces
/EXAMPLE was not found. Please make sure that the referenced resource exists, and that both
resources are in the same region.\\\",\\r\\n \\\"details\\\": []\\r\\n }\\r\\n}\"\r\n
}\r\n ]\r\n }\r\n ]\r\n }\r\n\
```

**OK:** Ez a hiba azért van, mert a Azure Resource Manager sablonnal létrehozott hálózati adapter neve megegyezik a VNET található másik hálózati adapter nevével.

**Javítás:** Használjon másik gazdagép-előtagot.

### <a name="error-deploymentfailed--error-downloading"></a>Hiba: DeploymentFailed – hiba a letöltéskor

Nyers hiba – példa:

```Error
\\\"The DSC Extension failed to execute: Error downloading
https://catalogartifact.azureedge.net/publicartifacts/rds.wvd-provision-host-pool-
2dec7a4d-006c-4cc0-965a-02bbe438d6ff-prod
/Artifacts/DSC/Configuration.zip after 29 attempts: The remote name could not be
resolved: 'catalogartifact.azureedge.net'.\\nMore information about the failure can
be found in the logs located under
'C:\\\\WindowsAzure\\\\Logs\\\\Plugins\\\\Microsoft.Powershell.DSC\\\\2.77.0.0' on
the VM.\\\"
```

**OK:** Ezt a hibát egy statikus útvonal, tűzfalszabály vagy NSG okozza, amely blokkolja a Azure Resource Manager sablonhoz kötött zip-fájl letöltését.

**Javítás:** Távolítsa el a statikus útvonal, a tűzfalszabály vagy a NSG blokkolását. Szükség esetén megnyithatja a Azure Resource Manager sablon JSON-fájlját egy szövegszerkesztőben, megtekintheti a zip-fájlra mutató hivatkozást, és letöltheti az erőforrást egy engedélyezett helyre.

### <a name="error-cant-delete-a-session-host-from-the-host-pool-after-deleting-the-vm"></a>Hiba: a virtuális gép törlése után nem lehet törölni egy munkamenet-gazdagépet a gazdagépről

**OK:** A virtuális gép törlése előtt törölnie kell a munkamenet-gazdagépet.

**Javítás:** Helyezze a munkamenet-gazdagépet kiürítési módba, jelentkezzen ki az összes felhasználót a munkamenet-gazdagépről, majd törölje a gazdagépet.

## <a name="next-steps"></a>Következő lépések

- A Windows rendszerű virtuális asztalok és a eszkalációs sávok hibaelhárításával kapcsolatban lásd: [Hibaelhárítás – áttekintés, visszajelzés és támogatás](troubleshoot-set-up-overview.md).
- A virtuális gép (VM) Windows rendszerű virtuális asztali gépen való konfigurálása során felmerülő problémák elhárításával kapcsolatban lásd: a [munkamenet-gazdagép virtuális gép konfigurálása](troubleshoot-vm-configuration.md).
- A Windows rendszerű virtuális asztali ügynökkel vagy a munkamenet-kapcsolattal kapcsolatos problémák elhárításához lásd: a [Windows rendszerű virtuális asztali ügynökkel kapcsolatos gyakori problémák elhárítása](troubleshoot-agent.md).
- A Windows rendszerű virtuális asztali ügyfélkapcsolatokkal kapcsolatos problémák elhárításához tekintse meg a [Windows rendszerű virtuális asztali szolgáltatások kapcsolatai](troubleshoot-service-connection.md)című témakört.
- Távoli asztal-ügyfelekkel kapcsolatos problémák elhárításához tekintse meg [a távoli asztal-ügyfél hibaelhárítása](troubleshoot-client.md) című témakört.
- A PowerShell és a Windows virtuális asztal használatával kapcsolatos problémák elhárításához tekintse meg a [Windows rendszerű virtuális asztali PowerShell](troubleshoot-powershell.md)című témakört.
- A szolgáltatással kapcsolatos további tudnivalókért tekintse meg a [Windows rendszerű virtuális asztali környezet](environment-setup.md)című témakört.
- A következő témakörben talál útmutatást a hibakereséshez [: oktatóanyag: Resource Manager-sablonok telepítésének hibája](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- További információ a naplózási műveletekről: [műveletek naplózása a Resource Managerrel](../azure-resource-manager/management/view-activity-logs.md).
- Az üzembe helyezés során felmerülő hibák meghatározásával kapcsolatos további tudnivalókért lásd: [telepítési műveletek megtekintése](../azure-resource-manager/templates/deployment-history.md).
