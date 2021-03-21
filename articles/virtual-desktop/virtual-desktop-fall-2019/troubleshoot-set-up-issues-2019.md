---
title: Windows rendszerű virtuális asztali (klasszikus) bérlői gazdagép-készlet létrehozása – Azure
description: A bérlői és a gazdagépi készletek problémáinak elhárítása és megoldása a Windows rendszerű virtuális asztali (klasszikus) bérlői környezet beállítása során.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8c6d26de62364b6aca671d1e4283a01c1b78c397
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95014832"
---
# <a name="tenant-and-host-pool-creation-in-windows-virtual-desktop-classic"></a>Bérlői és gazdagépi készlet létrehozása a Windows Virtual Desktopban (klasszikus)

>[!IMPORTANT]
>Ez a tartalom a Windows rendszerű virtuális asztali (klasszikus) verzióra vonatkozik, amely nem támogatja Azure Resource Manager Windows rendszerű virtuális asztali objektumokat. Ha Azure Resource Manager Windows rendszerű virtuális asztali objektumokat szeretne felügyelni, tekintse meg [ezt a cikket](../troubleshoot-set-up-issues.md).

Ez a cikk a Windows rendszerű virtuális asztali bérlő és a kapcsolódó munkamenet-gazdagépek infrastruktúrájának kezdeti beállítása során felmerülő problémákat ismerteti.

## <a name="provide-feedback"></a>Visszajelzés küldése

Látogasson el a [Windows rendszerű virtuális asztali technikai Közösségbe](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) , és beszéljen a Windows Virtual Desktop szolgáltatásról a termék csapatával és az aktív közösség tagjaival.

## <a name="acquiring-the-windows-10-enterprise-multi-session-image"></a>A Windows 10 Enterprise több munkamenetet ábrázoló rendszerképének beszerzése

A Windows 10 Enterprise multi-session rendszerképek használatához nyissa meg az Azure Marketplace-t, majd válassza az első **lépések**  >  **Microsoft Windows 10** > és [Windows 10 Enterprise for Virtual desktops, 1809-es verzió](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsdesktop.windows-10?tab=PlansAndPrice)lehetőséget.

> [!div class="mx-imgBorder"]
> ![Képernyőkép a Windows 10 Enterprise rendszerű virtuális asztalok, 1809-es verzió kiválasztásáról.](../media/AzureMarketPlace.png)

## <a name="creating-windows-virtual-desktop-tenant"></a>Windows rendszerű virtuális asztali bérlő létrehozása

Ez a szakasz a Windows rendszerű virtuális asztali bérlő létrehozásakor felmerülő esetleges problémákat ismerteti.

### <a name="error-aadsts650052-the-app-needs-access-to-a-service"></a>Hiba: a AADSTS650052 az alkalmazásnak hozzá kell férnie egy szolgáltatáshoz.

Nyers hiba – példa:

```Error
AADSTS650052 Message The app needs access to a service(\"{name}\") that your organization
\"{organization}\" has not subscribed to or enabled. Contact your IT Admin to review the
configuration of your service subscriptions.650052 Message The app needs access to a service
(\"{name}\") that your organization \"{organization}\" has not subscribed to or enabled.
Contact your IT Admin to review the configuration of your service subscriptions.
```

**OK:** Az Azure Active Directory-példányban nem adható meg a Windows rendszerű virtuális asztal.

**Javítás:** [kövesse ezt az útmutatót](./tenant-setup-azure-active-directory.md#grant-permissions-to-windows-virtual-desktop) a jóváhagyás megadásához.

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Hiba: The user isn't authorized to query the management service (A felhasználónak nincs jogosultsága a kezelési szolgáltatás lekérdezéséhez)

> [!div class="mx-imgBorder"]
> ![Képernyőkép a PowerShell-ablakról, amelyben a felhasználó nem rendelkezik jogosultsággal a kezelési szolgáltatás lekérdezéséhez.](../media/UserNotAuthorizedNewTenant.png)

Nyers hiba – példa:

```Error
   New-RdsTenant : User isn't authorized to query the management service.
   ActivityId: ad604c3a-85c6-4b41-9b81-5138162e5559
   Powershell commands to diagnose the failure:
   Get-RdsDiagnosticActivities -ActivityId ad604c3a-85c6-4b41-9b81-5138162e5559
   At line:1 char:1
   + New-RdsTenant -Name "testDesktopTenant" -AadTenantId "01234567-89ab-c ...
   + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
       + CategoryInfo          : FromStdErr: (Microsoft.RDInf...nt.NewRdsTenant:NewRdsTenant) [New-RdsTenant], RdsPowerSh
      ellException
       + FullyQualifiedErrorId : UnauthorizedAccess,Microsoft.RDInfra.RDPowershell.Tenant.NewRdsTenant
```

**OK:** A bejelentkezett felhasználó nem rendelte hozzá a TenantCreator szerepkört a Azure Active Directory.

**Javítás:** Kövesse a TenantCreator- [alkalmazás szerepkörének a Azure Active Directory-bérlőben lévő felhasználóhoz való hozzárendelésével kapcsolatos](tenant-setup-azure-active-directory.md#assign-the-tenantcreator-application-role)utasításokat. Az utasításokat követve egy felhasználó lesz hozzárendelve a TenantCreator szerepkörhöz.

> [!div class="mx-imgBorder"]
> ![Képernyőkép a hozzárendelt TenantCreator-szerepkörről.](../media/TenantCreatorRoleAssigned.png)

## <a name="creating-windows-virtual-desktop-session-host-vms"></a>Windows rendszerű virtuális asztali munkamenetgazda-alapú virtuális gépek létrehozása

A munkamenet-gazda virtuális gépek többféleképpen is létrehozhatók, de a Windows rendszerű virtuális asztali csapat csak az [Azure Marketplace](https://azuremarketplace.microsoft.com/) -ajánlattal kapcsolatos virtuális gépek üzembe helyezési problémáit támogatja. További információ: [a Windows virtuális asztal használatával kapcsolatos problémák – alkalmazáskészlet Azure Marketplace-ajánlatának kiépítése](#issues-using-windows-virtual-desktop--provision-a-host-pool-azure-marketplace-offering).

## <a name="issues-using-windows-virtual-desktop--provision-a-host-pool-azure-marketplace-offering"></a>Problémák a Windows virtuális asztal használatával – Azure Marketplace-ajánlat kiépítése

A Windows rendszerű virtuális asztal – alkalmazáskészlet sablonjának kiépítése az Azure piactéren érhető el.

### <a name="error-when-using-the-link-from-github-the-message-create-a-free-account-appears"></a>Hiba: a GitHubról való hivatkozás használatakor megjelenik az "ingyenes fiók létrehozása" üzenet.

> [!div class="mx-imgBorder"]
> ![Képernyőkép egy ingyenes fiók létrehozásához.](../media/be615904ace9832754f0669de28abd94.png)

**1. ok:** Nincsenek aktív előfizetések az Azure-ba való bejelentkezéshez, vagy a használt fiók nem rendelkezik engedéllyel az előfizetések megtekintéséhez.

**1. javítás:** Jelentkezzen be egy olyan fiókkal, amely közreműködői hozzáféréssel rendelkezik (legalább) ahhoz az előfizetéshez, amelyben a munkamenet-gazdagép virtuális gépei telepítve lesznek.

**2. ok:** A használt előfizetés egy Microsoft Cloud szolgáltatói (CSP-) bérlő részét képezi.

**2. javítás:** Nyissa meg a GitHub helyét az **új Windowsos virtuális asztali címkészlet létrehozásához és** kiépítéséhez, és kövesse az alábbi utasításokat:

1. Kattintson a jobb gombbal az **üzembe helyezés az Azure** -ba lehetőségre, és válassza a **hivatkozás címe másolása** lehetőséget.
2. Nyissa meg a **jegyzettömböt** , és illessze be a hivatkozást.
3. A # karakter előtt szúrja be a CSP végfelhasználói bérlői nevét.
4. Nyissa meg az új hivatkozást egy böngészőben, és a Azure Portal betölti a sablont.

    ```Example
    Example: https://portal.azure.com/<CSP end customer tenant name>
    #create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%
    2FRDS-Templates%2Fmaster%2Fwvd-templates%2FCreate%20and%20provision%20WVD%20host%20pool%2FmainTemplate.json
    ```

### <a name="error-you-receive-template-deployment-is-not-valid-error"></a>Hiba: "a sablon központi telepítése nem érvényes" hibaüzenet jelenik meg

> [!div class="mx-imgBorder"]
> ![Képernyőkép a "sablon központi telepítéséről... Érvénytelen "hiba](../media/troubleshooting-marketplace-validation-error-generic.png)

A művelet megkezdése előtt ellenőriznie kell a tevékenység naplóját, hogy megtekintse a sikertelen telepítés ellenőrzésének részletes hibáját.

A tevékenység naplójában lévő hiba megtekintéséhez:

1. Lépjen ki az Azure Marketplace aktuális üzembe helyezési ajánlatával.
2. A felső keresési sávban keresse meg és válassza ki a **műveletnapló** elemet.
3. Keressen egy olyan nevű tevékenységet, amelynek az **érvényesítése** **sikertelen** állapotú, és válassza ki a tevékenységet.

   > [!div class="mx-imgBorder"]
   > ![Képernyőkép az egyes * * * * * * * * * * * * * állapot ellenőrzése](../media/troubleshooting-marketplace-validation-error-activity-summary.png)

4. Válassza a JSON lehetőséget, majd görgessen le a képernyő aljáig egészen addig, amíg meg nem jelenik a "statusMessage" mező.

   > [!div class="mx-imgBorder"]
   > ![Képernyőkép a sikertelen tevékenységről, a JSON-szöveg statusMessage tulajdonsága körüli piros mezővel.](../media/troubleshooting-marketplace-validation-error-json-boxed.png)

Ha a műveleti sablon túllépi a kvóta korlátját, akkor a következő műveletek egyikét hajthatja végre a kijavításához:

 - Futtassa az Azure Marketplace-t az első alkalommal használt paraméterekkel, de ezúttal kevesebb virtuális GÉPET és virtuálisgép-magot használ.
 - Nyissa meg a böngésző **statusMessage** mezőjében látható hivatkozást, hogy küldjön egy kérést az Azure-előfizetéshez tartozó kvóta növelésére a megadott VIRTUÁLISGÉP-SKU-ra vonatkozóan.

## <a name="azure-resource-manager-template-and-powershell-desired-state-configuration-dsc-errors"></a>A Azure Resource Manager sablon és a PowerShell kívánt állapot-konfigurációs (DSC) hibái

A Azure Resource Manager-sablonok és a PowerShell DSC nem sikeres központi telepítésének hibakereséséhez kövesse az alábbi utasításokat.

1. Tekintse át az üzemelő példány hibáit a Azure Resource Manager használatával történő [központi telepítési műveletek megtekintésével](../../azure-resource-manager/templates/deployment-history.md).
2. Ha nem találhatók hibák az üzemelő példányban, tekintse át a tevékenység naplójának hibáit a tevékenységek [megtekintése az erőforrásokon végzett naplózási műveleteknél](../../azure-resource-manager/management/view-activity-logs.md).
3. A hiba észlelése után használja a hibaüzenetet és az erőforrásokat az [Azure telepítési hibáinak elhárításához Azure Resource Manager](../../azure-resource-manager/templates/common-deployment-errors.md) a probléma megoldásához.
4. Törölje az előző központi telepítés során létrehozott erőforrásokat, majd próbálkozzon újra a sablon üzembe helyezésével.

### <a name="error-your-deployment-failedhostnamejoindomain"></a>Hiba: a telepítés nem sikerült.... \<hostname> /JoinDomain

> [!div class="mx-imgBorder"]
> ![A telepítés sikertelen képernyőkép.](../media/e72df4d5c05d390620e07f0d7328d50f.png)

Nyers hiba – példa:

```Error
 {"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details.
 Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\": \"Failed\",\r\n \"error\":
 {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.
 \",\r\n \"details\": [\r\n {\r\n \"code\": \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has reported a failure when processing
 extension 'joindomain'. Error message: \\\"Exception(s) occurred while joining Domain 'diamondsg.onmicrosoft.com'\\\".\"\r\n }\r\n ]\r\n }\r\n}"}]}
```

**1. ok:** A virtuális gépek tartományhoz való csatlakoztatásához megadott hitelesítő adatok helytelenek.

**1. javítás:** Tekintse meg a "helytelen hitelesítő adatok" hibaüzenetet, ha a virtuális gépek nem csatlakoznak a tartományhoz a munkamenet-gazdagép virtuálisgép- [konfigurációjában](troubleshoot-vm-configuration-2019.md).

**2. ok:** A tartománynév nem oldható fel.

**2. javítás:** Lásd a [következő hibát: a tartomány neve nem oldható](troubleshoot-vm-configuration-2019.md#error-domain-name-doesnt-resolve) fel a munkamenet-gazdagép virtuálisgép- [konfigurációjában](troubleshoot-vm-configuration-2019.md).

**3. ok:** A virtuális hálózat (VNET) DNS-konfigurációja **alapértelmezett** értékre van állítva.

A probléma megoldásához tegye a következőket:

1. Nyissa meg az Azure Portalt, és lépjen a **Virtual Networks (virtuális hálózatok** ) lapra.
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
> ![A központi telepítés képernyőképe sikertelen volt, mert a terminál kiépítési állapota nem sikerült.](../media/7aaf15615309c18a984673be73ac969a.png)

**1. ok:** Átmeneti hiba a Windows rendszerű virtuális asztali környezettel.

**2. ok:** Átmeneti hiba történt a kapcsolatban.

**Javítás:** A PowerShell használatával történő bejelentkezéssel ellenőrizze, hogy a Windows rendszerű virtuális asztali környezet kifogástalan állapotú-e. Fejezze be a virtuális gép regisztrációját manuálisan a [gazdagép létrehozása a PowerShell](create-host-pools-powershell-2019.md)-lel.

### <a name="error-the-admin-username-specified-isnt-allowed"></a>Hiba: The Admin Username specified isn't allowed (A megadott rendszergazdai felhasználónév nem engedélyezett)

> [!div class="mx-imgBorder"]
> ![A központi telepítés képernyőképe nem sikerült, mert a megadott rendszergazda nem engedélyezett.](../media/f2b3d3700e9517463ef88fa41875bac9.png)

Nyers hiba – példa:

```Error
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostDesktop/providers/Microsoft.
  Resources/deployments/vmCreation-linkedTemplate/operations/EXAMPLE", "operationId": "EXAMPLE", "properties": { "provisioningOperation":
 "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T20:53:18.904917Z", "duration": "PT3.0574505S", "trackingId":
 "1f460af8-34dd-4c03-9359-9ab249a1a005", "statusCode": "BadRequest", "statusMessage": { "error": { "code": "InvalidParameter", "message":
 "The Admin Username specified is not allowed.", "target": "adminUsername" } }, "targetResource": { "id": "/subscriptions/EXAMPLE
 /resourceGroups/demoHostDesktop/providers/Microsoft.Compute/virtualMachines/demo", "resourceType": "Microsoft.Compute/virtualMachines", "resourceName": "demo" } }}
```

**OK:** A megadott jelszó tiltott alkarakterláncokat tartalmaz (adminisztrátor, rendszergazda, gyökér).

**Javítás:** Frissítse a felhasználónevet, vagy használjon más felhasználókat.

### <a name="error-vm-has-reported-a-failure-when-processing-extension"></a>Hiba: VM has reported a failure when processing extension (A virtuális gép hibát jelentett egy bővítmény feldolgozásakor)

> [!div class="mx-imgBorder"]
> ![Az erőforrás-művelet képernyőképe a terminál kiépítési állapotával fejeződött be a telepítés során.](../media/49c4a1836a55d91cd65125cf227f411f.png)

Nyers hiba – példa:

```Error
{ "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostD/providers/Microsoft.Resources/deployments/
 rds.wvd-provision-host-pool-20190129132410/operations/5A0757AC9E7205D2", "operationId": "5A0757AC9E7205D2", "properties":
 { "provisioningOperation": "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T21:43:05.1416423Z",
 "duration": "PT7M56.8150879S", "trackingId": "43c4f71f-557c-4abd-80c3-01f545375455", "statusCode": "Conflict",
 "statusMessage": { "status": "Failed", "error": { "code": "ResourceDeploymentFailure", "message":
 "The resource operation completed with terminal provisioning state 'Failed'.", "details": [ { "code":
 "VMExtensionProvisioningError", "message": "VM has reported a failure when processing extension 'dscextension'.
 Error message: \"DSC Configuration 'SessionHost' completed with error(s). Following are the first few:
 PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource functionality with error message:
 One or more errors occurred. The SendConfigurationApply function did not succeed.\"." } ] } }, "targetResource":
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostD/providers/Microsoft.
 Compute/virtualMachines/desktop-1/extensions/dscextension",
 "resourceType": "Microsoft.Compute/virtualMachines/extensions", "resourceName": "desktop-1/dscextension" } }}
```

**OK:** A PowerShell DSC bővítmény nem tudta beolvasni a rendszergazdai hozzáférést a virtuális gépen.

**Javítás:** Ellenőrizze, hogy a Felhasználónév és a jelszó rendszergazdai hozzáféréssel rendelkezik-e a virtuális gépen, majd futtassa újra a Azure Resource Manager sablont.

### <a name="error-deploymentfailed--powershell-dsc-configuration-firstsessionhost-completed-with-errors"></a>Hiba: DeploymentFailed – a (z) FirstSessionHost PowerShell DSC-konfiguráció hibával fejeződött be

> [!div class="mx-imgBorder"]
> ![A központi telepítés képernyőképe sikertelen, mert a PowerShell DSC-konfiguráció "FirstSessionHost" hibával fejeződött be.](../media/64870370bcbe1286906f34cf0a8646ab.png)

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

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Hiba: The user isn't authorized to query the management service (A felhasználónak nincs jogosultsága a kezelési szolgáltatás lekérdezéséhez)

Nyers hiba – példa:

```Error
"response": { "content": { "startTime": "2019-04-01T17:45:33.3454563+00:00", "endTime": "2019-04-01T17:48:52.4392099+00:00",
"status": "Failed", "error": { "code": "VMExtensionProvisioningError", "message": "VM has reported a failure when processing
extension 'dscextension'. Error message: \"DSC Configuration 'FirstSessionHost' completed with error(s).
Following are the first few: PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource
 functionality with error message: User is not authorized to query the management service.
\nActivityId: 1b4f2b37-59e9-411e-9d95-4f7ccd481233\nPowershell commands to diagnose the failure:
\nGet-RdsDiagnosticActivities -ActivityId 1b4f2b37-59e9-411e-9d95-4f7ccd481233\n
The SendConfigurationApply function did not succeed.\"." }, "name": "2c3272ec-d25b-47e5-8d70-a7493e9dc473" } } }}
```

**OK:** A megadott Windows virtuális asztali bérlői rendszergazda nem rendelkezik érvényes szerepkör-hozzárendeléssel.

**Javítás:** A Windows rendszerű virtuális asztali bérlőt létrehozó felhasználónak be kell jelentkeznie a Windows rendszerű virtuális asztali PowerShellbe, és hozzá kell rendelnie egy szerepkör-hozzárendelést a megkísérelt felhasználóhoz. Ha a GitHub Azure Resource Manager-sablon paramétereit futtatja, kövesse az alábbi utasításokat a PowerShell-parancsok használatával:

```PowerShell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
New-RdsRoleAssignment -TenantName <Windows Virtual Desktop tenant name> -RoleDefinitionName "RDS Contributor" -SignInName <UPN>
```

### <a name="error-user-requires-azure-ad-multi-factor-authentication-mfa"></a>Hiba: a felhasználónak Azure AD Multi-Factor Authentication (MFA) szükséges

> [!div class="mx-imgBorder"]
> ![A központi telepítés képernyőképe nem sikerült, mert nincs Multi-Factor Authentication (MFA)](../media/MFARequiredError.png)

Nyers hiba – példa:

```Error
"message": "{\r\n  \"status\": \"Failed\",\r\n  \"error\": {\r\n    \"code\": \"ResourceDeploymentFailure\",\r\n    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",\r\n    \"details\": [\r\n      {\r\n        \"code\": \"VMExtensionProvisioningError\",\r\n        \"message\": \"VM has reported a failure when processing extension 'dscextension'. Error message: \\\"DSC Configuration 'FirstSessionHost' completed with error(s). Following are the first few: PowerShell DSC resource MSFT_ScriptResource  failed to execute Set-TargetResource functionality with error message: One or more errors occurred.  The SendConfigurationApply function did not succeed.\\\".\"\r\n      }\r\n    ]\r\n  }\r\n}"
```

**OK:** A megadott Windows virtuális asztali bérlői rendszergazdának az Azure AD Multi-Factor Authentication (MFA) használatára van szüksége a bejelentkezéshez.

**Javítás:** Hozzon létre egy egyszerű szolgáltatásnevet, és rendeljen hozzá egy szerepkört a Windows rendszerű virtuális asztali bérlőhöz az [oktatóanyag: egyszerű szolgáltatások és szerepkör-hozzárendelések létrehozása a PowerShell](create-service-principal-role-powershell.md)használatával című témakör lépéseit követve. Miután meggyőződött arról, hogy be tud jelentkezni a Windows rendszerű virtuális asztalra az egyszerű szolgáltatással, futtassa újra az Azure Marketplace-ajánlatot vagy a GitHub-Azure Resource Manager sablont attól függően, hogy melyik módszert használja. A metódus helyes paramétereinek megadásához kövesse az alábbi utasításokat.

Ha az Azure Marketplace-ajánlatot futtatja, adja meg a következő paraméterek értékeit a Windows rendszerű virtuális asztal megfelelő hitelesítéséhez:

- Windows virtuális asztali bérlői RDS-tulajdonos: szolgáltatásnév
- Alkalmazás azonosítója: a létrehozott új egyszerű szolgáltatás alkalmazás-azonosítója
- Jelszó/Jelszó megerősítése: az egyszerű szolgáltatáshoz létrehozott jelszó titka
- Azure AD-bérlő azonosítója: az Ön által létrehozott szolgáltatásnév Azure AD-bérlői azonosítója

Ha a GitHub Azure Resource Manager sablont futtatja, adja meg a következő paraméterek értékeit a Windows rendszerű virtuális asztal megfelelő hitelesítéséhez:

- Bérlői rendszergazdai egyszerű felhasználónév (UPN) vagy alkalmazás azonosítója: a létrehozott új egyszerű szolgáltatás alkalmazás-azonosítója
- Bérlői rendszergazdai jelszó: az egyszerű szolgáltatáshoz létrehozott jelszó titkos kulcsa
- IsServicePrincipal: **true**
- AadTenantId: a létrehozott egyszerű szolgáltatás Azure AD-bérlői azonosítója

### <a name="error-vmsubnet-not-available-when-configuring-virtual-networks"></a>Hiba: a alhálózat virtuális hálózatok konfigurálásakor nem érhető el

**OK:** A WVD Marketplace sablonjában a felhasználói felület csak olyan alhálózatokat jelenít meg, amelyek legalább annyi IP-címmel rendelkeznek, mint a sablonban megadott virtuális gépek teljes száma. Az alhálózatban lévő elérhető IP-címek tényleges számának meg kell egyeznie a telepítendő új virtuális gépek számával, de az aktuális felhasználói felület nem számítható ki.

**Javítás:** Megadhat egy alhálózatot legalább annyi IP-címmel, amely a piactér felhasználói felületének használatával nem használja a hozzáadott virtuális gépek számát. Ez az alhálózat nevének megadásával végezhető el a "**existingSubnetName**" paraméterben, ha újból [üzembe helyez egy meglévő központi telepítést](expand-existing-host-pool-2019.md#redeploy-from-azure) , vagy [üzembe helyezi a GITHUBról a mögöttes ARM-sablonnal](create-host-pools-arm-template.md#run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool).

## <a name="next-steps"></a>Következő lépések

- A Windows rendszerű virtuális asztalok és a eszkalációs sávok hibaelhárításával kapcsolatban lásd: [Hibaelhárítás – áttekintés, visszajelzés és támogatás](troubleshoot-set-up-overview-2019.md).
- A virtuális gép (VM) Windows rendszerű virtuális asztali gépen való konfigurálása során felmerülő problémák elhárításával kapcsolatban lásd: a [munkamenet-gazdagép virtuális gép konfigurálása](troubleshoot-vm-configuration-2019.md).
- A Windows rendszerű virtuális asztali ügyfélkapcsolatokkal kapcsolatos problémák elhárításához tekintse meg a [Windows rendszerű virtuális asztali szolgáltatások kapcsolatai](troubleshoot-service-connection-2019.md)című témakört.
- Távoli asztal-ügyfelekkel kapcsolatos problémák elhárításához tekintse meg [a távoli asztal-ügyfél hibaelhárítása](../troubleshoot-client.md) című témakört.
- A PowerShell és a Windows virtuális asztal használatával kapcsolatos problémák elhárításához tekintse meg a [Windows rendszerű virtuális asztali PowerShell](troubleshoot-powershell-2019.md)című témakört.
- A szolgáltatással kapcsolatos további tudnivalókért tekintse meg a [Windows rendszerű virtuális asztali környezet](environment-setup-2019.md)című témakört.
- A következő témakörben talál útmutatást a hibakereséshez [: oktatóanyag: Resource Manager-sablonok telepítésének hibája](../../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- További információ a naplózási műveletekről: [műveletek naplózása a Resource Managerrel](../../azure-resource-manager/management/view-activity-logs.md).
- Az üzembe helyezés során felmerülő hibák meghatározásával kapcsolatos további tudnivalókért lásd: [telepítési műveletek megtekintése](../../azure-resource-manager/templates/deployment-history.md).