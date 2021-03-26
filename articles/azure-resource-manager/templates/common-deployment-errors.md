---
title: Gyakori üzembehelyezési hibák elhárítása
description: Ismerteti, Hogyan oldhatók fel a gyakori hibák, amikor erőforrásokat helyez üzembe az Azure-ban Azure Resource Manager használatával.
tags: top-support-issue
ms.topic: troubleshooting
ms.date: 01/20/2021
ms.openlocfilehash: 40e6317a1d879704ef00e928a971ae08fc9e0f72
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105564347"
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>Gyakori Azure-beli üzembe helyezési hibák elhárítása az Azure Resource Managerrel

Ez a cikk néhány gyakori Azure-telepítési hibát ismertet, és információt nyújt a hibák megoldásához. Ha nem találja az üzembehelyezési hiba hibakódját, tekintse meg [a hibakód megkeresését](#find-error-code) segítő témakört.

Ha egy hibakódra vonatkozó információt keres, és ez a cikk nem tartalmaz információt, tudassa velünk. A lap alján elhagyhatja a visszajelzést. A visszajelzéseket a GitHub-problémák követik nyomon.

## <a name="error-codes"></a>Hibakódok

| Hibakód | Kockázatcsökkentés | További információ |
| ---------- | ---------- | ---------------- |
| AccountNameInvalid | Kövesse a Storage-fiókok elnevezési korlátozásait. | [A Storage-fiók nevének feloldása](error-storage-account-name.md) |
| AccountPropertyCannotBeSet | Keresse meg a rendelkezésre álló Storage-fiók tulajdonságait. | [storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| AllocationFailed | A fürt vagy a régió nem rendelkezik elérhető erőforrásokkal, vagy nem támogatja a kért virtuálisgép-méretet. Ismételje meg a kérést később, vagy igényeljen egy másik virtuálisgép-méretet. | A Linux, a [kiépítési és a foglalási problémák](/troubleshoot/azure/virtual-machines/troubleshoot-deployment-new-vm-windows) kiosztása [és lefoglalása](/troubleshoot/azure/virtual-machines/troubleshoot-deployment-new-vm-linux)a Windows rendszerhez és a foglalási [hibák elhárítása](/troubleshoot/azure/virtual-machines/allocation-failure)|
| AnotherOperationInProgress | Várjon, amíg az egyidejű művelet befejeződik. | |
| AuthorizationFailed | A fiók vagy az egyszerű szolgáltatásnév nem rendelkezik megfelelő hozzáféréssel az üzemelő példány befejezéséhez. Győződjön meg arról, hogy a fiókja a szerepkörhöz tartozik, valamint a központi telepítési hatókörhöz való hozzáférése.<br><br>Ez a hiba akkor jelenhet meg, ha egy szükséges erőforrás-szolgáltató nincs regisztrálva. | [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md)<br><br>[Regisztráció feloldása](error-register-resource-provider.md) |
| BadRequest | Olyan központi telepítési értékeket küldtünk, amelyek nem egyeznek a Resource Manager által várttal. A hibaelhárítással kapcsolatos segítségért olvassa el a belső állapotjelző üzenetet. | A [sablon referenciája](/azure/templates/) és a [támogatott helyszínek](resource-location.md) |
| Ütközés | Olyan műveletet kér, amely nem engedélyezett az erőforrás jelenlegi állapotában. Például a lemezek átméretezése csak a virtuális gép létrehozásakor vagy a virtuális gép kiosztása esetén engedélyezett. | |
| DeploymentActiveAndUneditable | Várjon, amíg a rendszer végrehajtja az adott erőforráscsoport egyidejű üzembe helyezését. | |
| DeploymentFailedCleanUp | Ha a üzembe helyezése teljes módban történik, a sablonban nem szereplő erőforrások törlődnek. Ez a hibaüzenet akkor jelenik meg, ha nem rendelkezik megfelelő engedélyekkel a sablonban nem szereplő összes erőforrás törléséhez. A hiba elkerüléséhez módosítsa a központi telepítési módot növekményes értékre. | [Az Azure Resource Manager üzembe helyezési módjai](deployment-modes.md) |
| DeploymentNameInvalidCharacters | A központi telepítés neve csak betűt, számot, "-", "." vagy "_" karaktert tartalmazhat. | |
| DeploymentNameLengthLimitExceeded | A központi telepítési nevek legfeljebb 64 karakterből állhatnak.  | |
| DeploymentFailed | A DeploymentFailed hiba általános hiba, amely nem adja meg a hiba megoldásához szükséges adatokat. Tekintse meg a hiba részletes adatait, amely további információkat tartalmaz. | [Hibakód keresése](#find-error-code) |
| DeploymentQuotaExceeded | Ha eléri a 800-es üzemelő példányok számát egy erőforráscsoport esetében, törölje a már nem szükséges előzményekből származó központi telepítéseket. | [Hiba elhárítása, ha a központi telepítés száma meghaladja a 800](deployment-quota-exceeded.md) |
| DeploymentJobSizeExceeded | Egyszerűsítse a sablont a méret csökkentése érdekében. | [Sablon méretével kapcsolatos hibák elhárítása](error-job-size-exceeded.md) |
| DnsRecordInUse | A DNS-rekord nevének egyedinek kell lennie. Adjon meg másik nevet. | |
| ImageNotFound | A VM-rendszerkép beállításainak megtekintése. |  |
| InUseSubnetCannotBeDeleted | Ez a hiba akkor fordulhat elő, ha egy erőforrást próbál frissíteni, és az erőforrás törlésével és létrehozásával dolgozza fel a kérést. Győződjön meg arról, hogy az összes változatlan értéket meg kell adni. | [Erőforrás frissítése](/azure/architecture/guide/azure-resource-manager/advanced-templates/update-resource) |
| InvalidAuthenticationTokenTenant | Szerezze be a megfelelő bérlő hozzáférési jogkivonatát. Csak azon bérlőtől kérheti le a jogkivonatot, amelyhez a fiók tartozik. | |
| InvalidContentLink | Valószínűleg megpróbált olyan beágyazott sablonhoz kapcsolni, amely nem érhető el. Ellenőrizze a beágyazott sablonhoz megadott URI-t. Ha a sablon létezik egy Storage-fiókban, győződjön meg arról, hogy az URI elérhető. Lehetséges, hogy egy SAS-tokent kell átadnia. Jelenleg nem lehet olyan sablonhoz kapcsolódni, amely egy [Azure Storage-tűzfal](../../storage/common/storage-network-security.md)mögötti Storage-fiókban található. Vegye fontolóra a sablon áthelyezését egy másik adattárba, például a GitHubra. | [Hivatkozott sablonok](linked-templates.md) |
| InvalidDeploymentLocation | Az előfizetés szintjén történő üzembe helyezéskor egy másik helyet adott meg a korábban használt központi telepítési névnek. | [Előfizetés szintű központi telepítések](deploy-to-subscription.md) |
| InvalidParameter | Az adott erőforráshoz megadott értékek egyike nem felel meg a várt értéknek. Ez a hiba számos különböző körülménytől járhat. Előfordulhat például, hogy a jelszó nem elegendő, vagy a blob neve helytelen. A hibaüzenetnek jeleznie kell, hogy melyik értéket kell kijavítani. | |
| InvalidRequestContent | A központi telepítési értékekben szerepelnek a nem felismerhető értékek, vagy hiányoznak a szükséges értékek. Erősítse meg az erőforrástípus értékeit. | [Sablonreferencia](/azure/templates/) |
| InvalidRequestFormat | A központi telepítés futtatásakor engedélyezze a hibakeresési naplózást, és ellenőrizze a kérelem tartalmát. | [Hibakeresési naplózás](#enable-debug-logging) |
| InvalidResourceNamespace | Tekintse meg a **Type (típus** ) tulajdonságban megadott erőforrás-névteret. | [Sablonreferencia](/azure/templates/) |
| InvalidResourceReference | Az erőforrás vagy még nem létezik, vagy helytelenül hivatkozik rá. Győződjön meg róla, hogy hozzá kell adnia egy függőséget. Ellenőrizze, hogy a **hivatkozási** függvény használata tartalmazza-e a forgatókönyv szükséges paramétereit. | [Függőségek feloldása](error-not-found.md) |
| InvalidResourceType | Tekintse meg a **Type (típus** ) tulajdonságban megadott erőforrástípust. | [Sablonreferencia](/azure/templates/) |
| InvalidSubscriptionRegistrationState | Regisztrálja az előfizetését az erőforrás-szolgáltatónál. | [Regisztráció feloldása](error-register-resource-provider.md) |
| InvalidTemplate | A hibákért keresse meg a sablon szintaxisát. | [Érvénytelen sablon feloldása](error-invalid-template.md) |
| InvalidTemplateCircularDependency | Felesleges függőségek eltávolítása. | [Körkörös függőségek feloldása](error-invalid-template.md#circular-dependency) |
| JobSizeExceeded | Egyszerűsítse a sablont a méret csökkentése érdekében. | [Sablon méretével kapcsolatos hibák elhárítása](error-job-size-exceeded.md) |
| LinkedAuthorizationFailed | Ellenőrizze, hogy a fiókja ugyanahhoz a bérlőhöz tartozik-e, mint a központilag telepíteni kívánt erőforráscsoport. | |
| LinkedInvalidPropertyId | Egy erőforrás erőforrás-azonosítója nem oldja meg megfelelően a megoldást. Győződjön meg arról, hogy az erőforrás-AZONOSÍTÓhoz szükséges összes értéket megadja, beleértve az előfizetés-azonosítót, az erőforráscsoport nevét, az erőforrás típusát, a szülő erőforrás nevét (ha szükséges) és az erőforrás nevét. | |
| LocationRequired | Adja meg az erőforrás helyét. | [Hely beállítása](resource-location.md) |
| MismatchingResourceSegments | Győződjön meg arról, hogy a beágyazott erőforrás megfelelő számú szegmenst tartalmaz a név és a típus mezőben. | [Erőforrás-szegmensek feloldása](error-invalid-template.md#incorrect-segment-lengths)
| MissingRegistrationForLocation | Az erőforrás-szolgáltató regisztrációs állapotának és a támogatott helyeinek az ellenőrzését. | [Regisztráció feloldása](error-register-resource-provider.md) |
| MissingSubscriptionRegistration | Regisztrálja az előfizetését az erőforrás-szolgáltatónál. | [Regisztráció feloldása](error-register-resource-provider.md) |
| NoRegisteredProviderFound | Az erőforrás-szolgáltató regisztrációs állapotának keresése. | [Regisztráció feloldása](error-register-resource-provider.md) |
| NotFound | Előfordulhat, hogy egy szülő erőforrással párhuzamosan kell telepítenie egy függő erőforrást. Ellenőrizze, hogy hozzá kell-e adnia egy függőséget. | [Függőségek feloldása](error-not-found.md) |
| OperationNotAllowed | Az üzemelő példány olyan műveletet próbál végrehajtani, amely meghaladja az előfizetés, az erőforráscsoport vagy a régió kvótáját. Ha lehetséges, módosítsa úgy a központi telepítést, hogy a kvótán belül maradjon. Ellenkező esetben érdemes lehet módosítani a kvótákat. | [Kvóták feloldása](error-resource-quota.md) |
| ParentResourceNotFound | A gyermek erőforrások létrehozása előtt győződjön meg arról, hogy a szülő erőforrás létezik. | [Szülő erőforrás feloldása](error-parent-resource.md) |
| PasswordTooLong | Előfordulhat, hogy túl sok karakterből álló jelszót jelölt ki, vagy a jelszó értékét egy biztonságos karakterláncra konvertálta, mielőtt paraméterként átadná. Ha a sablon tartalmaz egy **biztonságos karakterlánc** -paramétert, akkor nem szükséges az érték konvertálása biztonságos karakterlánccá. Adja meg a jelszó értékét szövegként. |  |
| PrivateIPAddressInReservedRange | A megadott IP-cím tartalmazza az Azure által igényelt címtartományt. Módosítsa az IP-címet a fenntartott tartomány elkerüléséhez. | [IP-címek](../../virtual-network/public-ip-addresses.md) |
| PrivateIPAddressNotInSubnet | A megadott IP-cím az alhálózat tartományán kívül esik. Módosítsa az IP-címet az alhálózati tartományba eső értékre. | [IP-címek](../../virtual-network/public-ip-addresses.md) |
| PropertyChangeNotAllowed | Egyes tulajdonságok nem módosíthatók központilag telepített erőforrásokon. Egy erőforrás frissítésekor korlátozza az engedélyezett tulajdonságok módosításait. | [Erőforrás frissítése](/azure/architecture/guide/azure-resource-manager/advanced-templates/update-resource) |
| RequestDisallowedByPolicy | Az előfizetés tartalmaz egy erőforrás-szabályzatot, amely megakadályozza az üzembe helyezés során végrehajtani kívánt műveleteket. Keresse meg a műveletet blokkoló házirendet. Ha lehetséges, módosítsa az üzemelő példányt, hogy megfeleljen a szabályzat korlátainak. | [Szabályzatok feloldása](error-policy-requestdisallowedbypolicy.md) |
| ReservedResourceName | Adja meg a fenntartott nevet nem tartalmazó erőforrás nevét. | [Fenntartott erőforrások nevei](error-reserved-resource-name.md) |
| ResourceGroupBeingDeleted | Várjon, amíg a törlés befejeződik. | |
| ResourceGroupNotFound | A központi telepítéshez tartozó cél erőforráscsoport nevének megadásához. A célként megadott erőforráscsoport már léteznie kell az előfizetésben. Az előfizetési környezet ellenõrzése. | [Azure CLI](/cli/azure/account?#az-account-set) [PowerShell](/powershell/module/Az.Accounts/Set-AzContext) |
| ResourceNotFound | Az üzemelő példány olyan erőforrásra hivatkozik, amely nem oldható fel. Ellenőrizze, hogy a **hivatkozási** függvény használata tartalmazza-e a forgatókönyvhöz szükséges paramétereket. | [Hivatkozások feloldása](error-not-found.md) |
| ResourceQuotaExceeded | A központi telepítés olyan erőforrásokat próbál létrehozni, amelyek túllépik az előfizetés, az erőforráscsoport vagy a régió kvótáját. Ha lehetséges, módosítsa az infrastruktúrát, hogy a kvótán belül maradjon. Ellenkező esetben érdemes lehet módosítani a kvótákat. | [Kvóták feloldása](error-resource-quota.md) |
| SkuNotAvailable | Válassza ki a kiválasztott helyhez elérhető SKU-t (például a virtuális gép méretét). | [SKU feloldása](error-sku-not-available.md) |
| StorageAccountAlreadyExists | Adjon meg egy egyedi nevet a tárfióknak. | [A Storage-fiók nevének feloldása](error-storage-account-name.md)  |
| StorageAccountAlreadyTaken | Adjon meg egy egyedi nevet a tárfióknak. | [A Storage-fiók nevének feloldása](error-storage-account-name.md) |
| StorageAccountNotFound | Tekintse meg az előfizetést, az erőforráscsoportot és a használni kívánt Storage-fiók nevét. | |
| SubnetsNotInSameVnet | A virtuális gépeknek csak egy virtuális hálózata lehet. Több hálózati adapter telepítésekor győződjön meg arról, hogy ugyanahhoz a virtuális hálózathoz tartoznak. | [Több hálózati adapter](../../virtual-machines/windows/multiple-nics.md) |
| SubscriptionNotFound | Nem érhető el a központi telepítéshez megadott előfizetés. Lehet, hogy az előfizetés-azonosító helytelen, a sablont telepítő felhasználó nem rendelkezik megfelelő engedélyekkel az előfizetéshez való üzembe helyezéshez, vagy az előfizetés-azonosító formátuma helytelen. Ha beágyazott központi telepítéseket használ a [hatókörök közötti központi](./deploy-to-resource-group.md)telepítéshez, adja meg az előfizetés GUID azonosítóját. | |
| SubscriptionNotRegistered | Erőforrás telepítésekor az erőforrás-szolgáltatót regisztrálni kell az előfizetéséhez. Ha Azure Resource Manager sablont használ a központi telepítéshez, a rendszer automatikusan regisztrálja az erőforrás-szolgáltatót az előfizetésben. Előfordulhat, hogy az automatikus regisztráció nem fejeződött be időben. Az időszakos hiba elkerüléséhez regisztrálja az erőforrás-szolgáltatót az üzembe helyezés előtt. | [Regisztráció feloldása](error-register-resource-provider.md) |
| TemplateResourceCircularDependency | Felesleges függőségek eltávolítása. | [Körkörös függőségek feloldása](error-invalid-template.md#circular-dependency) |
| TooManyTargetResourceGroups | Az erőforráscsoportok számának csökkentése egyetlen központi telepítéshez. | [Több hatókörű központi telepítés](./deploy-to-resource-group.md) |

## <a name="find-error-code"></a>Hibakód keresése

Kétféle hibáról kaphat értesítést:

* érvényesítési hiba
* üzembehelyezési hiba

Az érvényesítési hibák olyan forgatókönyvekből adódnak, amelyek az üzembe helyezés előtt határozhatók meg. Ezek közé tartoznak a sablon szintaxishibái, vagy az olyan erőforrások üzembe helyezése, amelyek túllépik az előfizetése kvótáit. Az üzembehelyezési hibák az üzembehelyezési folyamat során lépnek fel. Ezek közé tartozik például egy olyan erőforrás elérésére tett kísérlet, amelynek az üzembe helyezése párhuzamosan zajlik.

Mindkét típusú hiba az üzembe helyezés hibaelhárításához használható hibakódot ad vissza. Mindkét típusú hiba megjelenik a [tevékenységnaplóban](../management/view-activity-logs.md). Az érvényesítési hibák azonban nem jelennek meg az üzembe helyezési előzmények között, mert az üzembe helyezés el sem indult.

### <a name="validation-errors"></a>Érvényesítési hibák

Amikor a portálon keresztül végzi el az üzembe helyezést, az értékek megadása után jelenik meg az érvényesítési hiba.

![portál-érvényesítési hiba megjelenítése](./media/common-deployment-errors/validation-error.png)

További információért válassza ki az üzenetet. Az alábbi képen egy **InvalidTemplateDeployment** -hibaüzenet és egy olyan üzenet jelenik meg, amely a házirend letiltott telepítését jelzi.

![érvényesítés részleteinek megjelenítése](./media/common-deployment-errors/validation-details.png)

### <a name="deployment-errors"></a>Telepítési hibák

Ha a művelet megfelel az ellenőrzés követelményeinek, de az üzembe helyezés során meghiúsul, akkor üzembehelyezési hiba jelenik meg.

Az üzembehelyezési hibakódok és üzenetek megtekintése a PowerShell-lel:

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -DeploymentName exampledeployment -ResourceGroupName examplegroup).Properties.statusMessage
```

Az üzembehelyezési hibakódok és üzenetek megtekintése az Azure CLI-vel:

```azurecli-interactive
az deployment operation group list --name exampledeployment -g examplegroup --query "[*].properties.statusMessage"
```

A portálon válassza ki az értesítést.

![értesítési hiba](./media/common-deployment-errors/notification.png)

További részleteket az üzemelő példányról talál. Válassza ki a beállítást, hogy többet tudjon meg a hibáról.

![sikertelen telepítés](./media/common-deployment-errors/deployment-failed.png)

Megjelenik a hibaüzenet és a hibakódok. Figyelje meg, hogy két hibakód látható. Az első hibakód (**DeploymentFailed**) egy általános hiba, amely nem adja meg a hiba megoldásához szükséges részleteket. A második hibakód (**StorageAccountNotFound**) megadja a szükséges részleteket.

![hiba részletei](./media/common-deployment-errors/error-details.png)

## <a name="enable-debug-logging"></a>Hibakeresési naplózás engedélyezése

Időnként további információra van szüksége a kérésről és a válaszról, hogy megtudja, mi volt a baj. Az üzembe helyezés során kérheti, hogy a rendszer az üzembe helyezés során további információkat naplózza.

### <a name="powershell"></a>PowerShell

A PowerShellben állítsa a **DeploymentDebugLogLevel** paramétert az all, a ResponseContent vagy a RequestContent értékre.

```powershell
New-AzResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName examplegroup `
  -TemplateFile c:\Azure\Templates\storage.json `
  -DeploymentDebugLogLevel All
```

Vizsgálja meg a kérelem tartalmát a következő parancsmaggal:

```powershell
(Get-AzResourceGroupDeploymentOperation `
-DeploymentName exampledeployment `
-ResourceGroupName examplegroup).Properties.request `
| ConvertTo-Json
```

Vagy a válasz tartalma:

```powershell
(Get-AzResourceGroupDeploymentOperation `
-DeploymentName exampledeployment `
-ResourceGroupName examplegroup).Properties.response `
| ConvertTo-Json
```

Ez az információ segít megállapítani, hogy a sablon egyik értéke helytelenül van-e beállítva.

### <a name="azure-cli"></a>Azure CLI

Az Azure CLI jelenleg nem támogatja a hibakeresési naplózás bekapcsolását, de a hibakeresési naplózást is beolvashatja.

Vizsgálja meg a telepítési műveleteket a következő paranccsal:

```azurecli
az deployment operation group list \
  --resource-group examplegroup \
  --name exampledeployment
```

Vizsgálja meg a kérelem tartalmát a következő paranccsal:

```azurecli
az deployment operation group list \
  --name exampledeployment \
  -g examplegroup \
  --query [].properties.request
```

Vizsgálja meg a válasz tartalmát a következő paranccsal:

```azurecli
az deployment operation group list \
  --name exampledeployment \
  -g examplegroup \
  --query [].properties.response
```

### <a name="nested-template"></a>Beágyazott sablon

Beágyazott sablon hibakeresési információinak naplózásához használja a **debugSetting** elemet.

```json
{
  "type": "Microsoft.Resources/deployments",
  "apiVersion": "2020-10-01",
  "name": "nestedTemplate",
  "properties": {
    "mode": "Incremental",
    "templateLink": {
      "uri": "{template-uri}",
      "contentVersion": "1.0.0.0"
    },
    "debugSetting": {
       "detailLevel": "requestContent, responseContent"
    }
  }
}
```

## <a name="create-a-troubleshooting-template"></a>Hibaelhárítási sablon létrehozása

Bizonyos esetekben a sablon egy részének teszteléséhez a legegyszerűbb módszer a sablonok hibakeresése. Létrehozhat egy egyszerűsített sablont, amely lehetővé teszi, hogy a hibát okozó részre koncentráljon. Tegyük fel például, hogy hibaüzenetet kap, amikor egy erőforrásra hivatkozik. Ahelyett, hogy egy teljes sablont kellene használnia, hozzon létre egy sablont, amely visszaadja a problémát okozó részt. Segít eldönteni, hogy a megfelelő paramétereket használja-e, és a sablon megfelelően működik-e, és beolvassa a várt erőforrást.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  "storageName": {
    "type": "string"
  },
  "storageResourceGroup": {
    "type": "string"
  }
  },
  "variables": {},
  "resources": [],
  "outputs": {
  "exampleOutput": {
    "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageName')), '2016-05-01')]",
    "type" : "object"
  }
  }
}
```

Vagy tegyük fel, hogy olyan központi telepítési hibákat észlel, amelyeket úgy gondol, hogy a nem megfelelően beállított függőségekhez kapcsolódik. A sablon teszteléséhez bontsa ki az egyszerűsített sablonokat. Először hozzon létre egy sablont, amely csak egyetlen erőforrást telepít (például egy SQL Server). Ha biztos benne, hogy megfelelően definiálta az erőforrást, adjon hozzá egy erőforrást, amely attól függ (például egy SQL Database). Ha a két erőforrás megfelelően van definiálva, vegyen fel más függő erőforrásokat (például naplózási házirendeket). Az egyes tesztelési környezetek között törölje az erőforráscsoportot, hogy ellenőrizze a függőségek megfelelő tesztelését.

## <a name="next-steps"></a>Következő lépések

* A hibaelhárítással kapcsolatos oktatóanyagért lásd [: oktatóanyag: Resource Manager-sablonok központi telepítésének hibaelhárítása](template-tutorial-troubleshoot.md)
* További információ a naplózási műveletekről: [műveletek naplózása a Resource Managerrel](../management/view-activity-logs.md).
* Az üzembe helyezés során felmerülő hibák meghatározásával kapcsolatos további tudnivalókért lásd: [telepítési műveletek megtekintése](deployment-history.md).