---
title: Gyakori üzembehelyezési hibák elhárítása
description: Ismerteti, hogyan háríthatja el azokat a gyakori hibákat, amikor erőforrásokat helyez üzembe az Azure-ban a Azure Resource Manager.
tags: top-support-issue
ms.topic: troubleshooting
ms.date: 01/20/2021
ms.openlocfilehash: 07c197f1b54522b96a3bfa2d6a5ce7b368be3b35
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789176"
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>Gyakori Azure-beli üzembe helyezési hibák elhárítása az Azure Resource Managerrel

Ez a cikk néhány gyakori Azure-beli üzembe helyezési hibát ismertet, és a hibák megoldásához nyújt információt. Ha nem találja az üzembehelyezési hiba hibakódját, tekintse meg [a hibakód megkeresését](#find-error-code) segítő témakört.

Ha hibakóddal kapcsolatos információkat keres, és ez a cikk nem tartalmazza ezt az információt, tudajuk meg velünk. A lap alján visszajelzést küldhet. A visszajelzéseket a GitHub Issues követi nyomon.

## <a name="error-codes"></a>Hibakódok

| Hibakód | Kockázatcsökkentés | További információ |
| ---------- | ---------- | ---------------- |
| AccountNameInvalid | Kövesse a tárfiókok elnevezési korlátozásait. | [Tárfiók nevének feloldása](error-storage-account-name.md) |
| AccountPropertyCannotBeSet | Ellenőrizze a tárfiók elérhető tulajdonságait. | [storageAccounts (tárfiókok)](/azure/templates/microsoft.storage/storageaccounts) |
| AllocationFailed (Foglalási költségek) | A fürt vagy régió nem rendelkezik elérhető erőforrásokkal, vagy nem támogatja a kért virtuálisgép-méretet. Próbálja újra a kérést később, vagy kérjen egy másik virtuálisgép-méretet. | [Kiépítési és foglalási problémák Linux](/troubleshoot/azure/virtual-machines/troubleshoot-deployment-new-vm-linux)rendszeren, Kiépítési és foglalási [problémák Windows](/troubleshoot/azure/virtual-machines/troubleshoot-deployment-new-vm-windows) rendszeren és Foglalási [hibák elhárítása](/troubleshoot/azure/virtual-machines/allocation-failure)|
| AnotherOperationInProgress (AnotherOperationInProgress) | Várjon, amíg az egyidejű művelet befejeződik. | |
| AuthorizationFailed (Hitelesítésifailed) | A fiók vagy a szolgáltatásnév nem rendelkezik elegendő hozzáféréssel az üzembe helyezés befejezéséhez. Ellenőrizze, hogy a fiók milyen szerepkörhöz tartozik, és hogy milyen hozzáféréssel rendelkezik az üzembe helyezési hatókörhöz.<br><br>Ez a hiba akkor jelenhet meg, ha nincs regisztrálva egy szükséges erőforrás-szolgáltató. | [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md)<br><br>[Regisztráció feloldása](error-register-resource-provider.md) |
| BadRequest (Rossz kérdés) | Olyan üzembe helyezési értékeket küldött, amelyek nem egyeznek a Resource Manager. A hibaelhárítással kapcsolatos segítségért tekintse meg a belső állapotüzenetet. | [Sablonreferenciák](/azure/templates/) és [támogatott helyek](resource-location.md) |
| Ütközés | Olyan műveletet kér, amely nem engedélyezett az erőforrás aktuális állapotában. A lemezek átméretezése például csak akkor engedélyezett, ha virtuális gépet hoz létre, vagy amikor felszabadítják a virtuális gépet. | |
| DeploymentActiveAndUneditable | Várjon, amíg az ebben az erőforráscsoportban való egyidejű üzembe helyezés befejeződik. | |
| DeploymentFailedCleanUp | Amikor teljes módban telepíti az üzembe helyezést, a rendszer törli a sablonban nem álló erőforrásokat. Ez a hiba akkor jelenik meg, ha nem rendelkezik megfelelő engedélyekkel a sablonon nem rendelkező összes erőforrás törléséhez. A hiba elkerülése érdekében módosítsa növekményesre az üzembe helyezési módot. | [Az Azure Resource Manager üzembe helyezési módjai](deployment-modes.md) |
| DeploymentNameInvalidCharacters | Az üzemelő példány neve csak betűket, számjegyeket, "-", "." vagy "_" betűt tartalmazhat. | |
| DeploymentNameLengthLimitExceeded | Az üzembe helyezési nevek legfeljebb 64 karakterből állhatnak.  | |
| DeploymentFailed (Üzemelő példány le van oltva) | A DeploymentFailed hiba egy általános hiba, amely nem tartalmazza a hiba megoldásához szükséges részleteket. A hiba részletei között keresse meg a hibakódot, amely további információkat tartalmaz. | [Hibakód megkeresve](#find-error-code) |
| DeploymentQuotaExceeded | Ha eléri az erőforráscsoportonkénti 800 üzemelő példány korlátját, törölje az üzemelő példányokat a már nem szükséges előzményekből. | [A 800-asnál nagyobb üzembe helyezési számnál nagyobb hiba elhárítása](deployment-quota-exceeded.md) |
| DeploymentJobSizeExceeded | A méret csökkentése érdekében egyszerűsítse a sablont. | [Sablonmérettel kapcsolatos hibák elhárítása](error-job-size-exceeded.md) |
| DnsRecordInUse | A DNS-rekord nevének egyedinek kell lennie. Adjon meg egy másik nevet. | |
| ImageNotFound | Ellenőrizze a virtuálisgép-rendszerkép beállításait. |  |
| InUseSubnetCannotBeDeleted | Ez a hiba akkor jelenhet meg, amikor frissíteni próbál egy erőforrást, és a kérés feldolgozása az erőforrás törlésével és létrehozásával történik. Ügyeljen arra, hogy az összes változatlan értéket adja meg. | [Erőforrás frissítése](/azure/architecture/guide/azure-resource-manager/advanced-templates/update-resource) |
| InvalidAuthenticationTokenTenant | Szerezze be a megfelelő bérlő hozzáférési jogkivonatát. A jogkivonatot csak attól a bérlőtől kaphatja meg, amelyhez a fiókja tartozik. | |
| InvalidContentLink | Valószínűleg olyan beágyazott sablonra próbált hivatkozni, amely nem érhető el. Ellenőrizze a beágyazott sablonhoz megadott URI-t. Ha a sablon létezik egy tárfiókban, győződjön meg arról, hogy az URI elérhető. Előfordulhat, hogy sas-jogkivonatot kell átadnia. Jelenleg nem lehet olyan sablonhoz hivatkozni, amely egy Azure Storage-tűzfal mögötti [tárfiókban található.](../../storage/common/storage-network-security.md) Fontolja meg a sablon áthelyezését egy másik adattárba, például a GitHubba. | [Hivatkozott sablonok](linked-templates.md) |
| InvalidDeploymentLocation | Az előfizetés szintjén való üzembe helyezéskor egy másik helyet adott meg egy korábban használt üzemelő példány nevéhez. | [Előfizetési szintű üzemelő példányok](deploy-to-subscription.md) |
| InvalidParameter (Érvénytelen paraméter) | Az erőforráshoz megadott értékek egyike nem egyezik a várt értékkel. Ez a hiba számos különböző feltétel eredménye lehet. Előfordulhat például, hogy egy jelszó nem elegendő, vagy a blob neve helytelen. A hibaüzenetnek jeleznie kell, hogy melyik értéket kell kijavítani. | |
| InvalidRequestContent | Az üzembe helyezési értékek lehetnek nem felismert értékek, vagy hiányoznak a szükséges értékek. Erősítse meg az erőforrástípus értékeit. | [Sablonreferencia](/azure/templates/) |
| InvalidRequestFormat | Engedélyezze a hibakeresési naplózást az üzembe helyezés futtatásakor, és ellenőrizze a kérés tartalmát. | [Naplózás hibakeresése](#enable-debug-logging) |
| InvalidResourceNamespace | Ellenőrizze a type tulajdonságban megadott **erőforrásnévteret.** | [Sablonreferencia](/azure/templates/) |
| InvalidResourceReference (Érvénytelen forrásreferencia) | Az erőforrás még nem létezik, vagy helytelenül hivatkozik rá. Ellenőrizze, hogy hozzá kell-e adni egy függőséget. Ellenőrizze, hogy a  referencia-függvény használata tartalmazza-e a forgatókönyvhöz szükséges paramétereket. | [Függőségek feloldása](error-not-found.md) |
| InvalidResourceType (Érvénytelen erőforrástípus) | Ellenőrizze a típus tulajdonságban megadott **erőforrástípust.** | [Sablonreferencia](/azure/templates/) |
| InvalidSubscriptionRegistrationState | Regisztrálja előfizetését az erőforrás-szolgáltatónál. | [Regisztráció feloldása](error-register-resource-provider.md) |
| InvalidTemplate | Ellenőrizze, hogy vannak-e hibák a sablon szintaxisában. | [Érvénytelen sablon feloldása](error-invalid-template.md) |
| InvalidTemplateCircularDependency | Távolítsa el a szükségtelen függőségeket. | [Körkörös függőségek feloldása](error-invalid-template.md#circular-dependency) |
| JobSizeExceeded | A méret csökkentése érdekében egyszerűsítse a sablont. | [Sablonmérettel kapcsolatos hibák elhárítása](error-job-size-exceeded.md) |
| LinkedAuthorizationFailed | Ellenőrizze, hogy a fiókja ugyanannak a bérlőnek a tagja-e, mint az az erőforráscsoport, amelyhez az üzembe helyezést telepíti. | |
| LinkedInvalidPropertyId | Az erőforrás erőforrás-azonosítója nem feloldható megfelelően. Ellenőrizze, hogy az erőforrás-azonosítóhoz minden szükséges értéket megadott-e, beleértve az előfizetés-azonosítót, az erőforráscsoport nevét, az erőforrástípust, a szülő-erőforrás nevét (ha szükséges) és az erőforrás nevét. | |
| LocationRequired (Hely) | Adja meg az erőforrás helyét. | [Hely beállítása](resource-location.md) |
| MismatchingResourceSegments | Győződjön meg arról, hogy a beágyazott erőforrás neve és típusa megfelelő számú szegmenst ad meg. | [Erőforrásszegmensek feloldása](error-invalid-template.md#incorrect-segment-lengths)
| MissingRegistrationForLocation | Ellenőrizze az erőforrás-szolgáltató regisztrációs állapotát és a támogatott helyeket. | [Regisztráció feloldása](error-register-resource-provider.md) |
| MissingSubscriptionRegistration | Regisztrálja előfizetését az erőforrás-szolgáltatónál. | [Regisztráció feloldása](error-register-resource-provider.md) |
| NoRegisteredProviderFound | Ellenőrizze az erőforrás-szolgáltató regisztrációs állapotát. | [Regisztráció feloldása](error-register-resource-provider.md) |
| NotFound | Előfordulhat, hogy egy függő erőforrást próbál meg egy szülőerőforrással párhuzamosan üzembe helyezni. Ellenőrizze, hogy hozzá kell-e adni függőséget. | [Függőségek feloldása](error-not-found.md) |
| OperationNotAllowed (Nem engedélyezett) | Az üzembe helyezés olyan műveletet kísérel meg, amely túllépi az előfizetés, az erőforráscsoport vagy a régió kvótáját. Ha lehetséges, módosítsa az üzemelő példányát, hogy a kvótákon belül maradjon. Ellenkező esetben fontolja meg a kvóták változásának kérelmezését. | [Kvóták feloldása](error-resource-quota.md) |
| ParentResourceNotFound | A gyermekerőforrások létrehozása előtt ellenőrizze, hogy létezik-e szülőerőforrás. | [Szülőerőforrás feloldása](error-parent-resource.md) |
| PasswordTooLong | Előfordulhat, hogy túl sok karakterből áll jelszót választott ki, vagy a jelszó értékét biztonságos sztringgé konvertálta, mielőtt paraméterként adta volna át. Ha a sablon biztonságos **sztringparamétert** tartalmaz, nem kell konvertálni az értéket biztonságos sztringgé. Adja meg a jelszó értékét szövegként. |  |
| PrivateIPAddressInReservedRange | A megadott IP-cím tartalmaz egy, az Azure által megkövetelt címtartományt. Módosítsa az IP-címet a fenntartott tartomány elkerüléséhez. | [IP-címek](../../virtual-network/public-ip-addresses.md) |
| PrivateIPAddressNotInSubnet | A megadott IP-cím az alhálózati tartományon kívül esik. Módosítsa az IP-címet úgy, hogy az alhálózat tartományán belülre essen. | [IP-címek](../../virtual-network/public-ip-addresses.md) |
| PropertyChangeNotAllowed (Tulajdonság nem engedélyezett) | Egyes tulajdonságok nem módosíthatók az üzembe helyezett erőforrásokon. Erőforrás frissítésekkor korlátozza a módosításokat az engedélyezett tulajdonságokra. | [Erőforrás frissítése](/azure/architecture/guide/azure-resource-manager/advanced-templates/update-resource) |
| RequestDisallowedByPolicy | Az előfizetés tartalmaz egy erőforrás-szabályzatot, amely megakadályozza az üzembe helyezés során végrehajtani próbált műveletet. Keresse meg a műveletet letiltó szabályzatot. Ha lehetséges, módosítsa az üzemelő példányát úgy, hogy megfeleljen a szabályzat korlátainak. | [Szabályzatok feloldása](error-policy-requestdisallowedbypolicy.md) |
| ReservedResourceName | Adjon meg egy erőforrásnevet, amely nem tartalmaz fenntartott nevet. | [Fenntartott erőforrások nevei](error-reserved-resource-name.md) |
| ResourceGroupBeingDeleted | Várjon, amíg a törlés befejeződik. | |
| ResourceGroupNotFound | Ellenőrizze az üzemelő példány célerőforrás-csoportjának nevét. A cél erőforráscsoportnak már léteznie kell az előfizetésében. Ellenőrizze az előfizetési környezetet. | [Azure CLI](/cli/azure/account?#az_account_set) [PowerShell](/powershell/module/Az.Accounts/Set-AzContext) |
| ResourceNotFound | Az üzemelő példány olyan erőforrásra hivatkozik, amely nem oldható fel. Ellenőrizze, hogy a **referencia-függvény** használata tartalmazza-e a forgatókönyvhöz szükséges paramétereket. | [Hivatkozások feloldása](error-not-found.md) |
| ResourceQuotaExceeded | Az üzemelő példány olyan erőforrásokat próbál létrehozni, amelyek túllépik az előfizetés, az erőforráscsoport vagy a régió kvótáját. Ha lehetséges, módosítsa az infrastruktúrát, hogy a kvótákon belül maradjon. Ellenkező esetben fontolja meg a kvóták változásának kérelmezését. | [Kvóták feloldása](error-resource-quota.md) |
| SkuNotAvailable | Válassza ki a kiválasztott helyhez elérhető termékváltozatot (például virtuálisgép-méretet). | [Termékváltozat feloldása](error-sku-not-available.md) |
| StorageAccountAlreadyExists | Adjon meg egy egyedi nevet a tárfióknak. | [Tárfiók nevének feloldása](error-storage-account-name.md)  |
| StorageAccountAlreadyTaken (TárfiókalreadyTaken) | Adjon meg egy egyedi nevet a tárfióknak. | [Tárfiók nevének feloldása](error-storage-account-name.md) |
| StorageAccountNotFound | Ellenőrizze a használni próbált tárfiók előfizetését, erőforráscsoportját és nevét. | |
| AlhálózatokNotInSameVnet | Egy virtuális gép csak egy virtuális hálózattal lehet. Több hálózati adapter üzembe helyezésekor győződjön meg arról, hogy ugyanannak a virtuális hálózatnak a tagja. | [Több NIC](../../virtual-machines/windows/multiple-nics.md) |
| SubscriptionNotFound | Az üzembe helyezéshez megadott előfizetés nem érhető el. Lehet, hogy az előfizetés azonosítója helytelen, a sablont üzembe helyező felhasználó nem rendelkezik megfelelő engedélyekkel az előfizetésben való üzembe helyezéshez, vagy az előfizetés azonosítója nem megfelelő formátumú. Ha beágyazott üzemelő példányokat használ a hatókörök közötti [üzembe helyezéshez,](./deploy-to-resource-group.md)adja meg az előfizetés GUID-azonosítóját. | |
| SubscriptionNotRegistered (Nem regisztrált előfizetés) | Erőforrás üzembe helyezésekor az erőforrás-szolgáltatónak regisztrálva kell lennie az előfizetésben. Ha üzembe helyezéshez Azure Resource Manager sablont használ, az erőforrás-szolgáltató automatikusan regisztrálva lesz az előfizetésben. Az automatikus regisztráció néha nem fejeződik be időben. Az időszakos hiba elkerülése érdekében az üzembe helyezés előtt regisztrálja az erőforrás-szolgáltatót. | [Regisztráció feloldása](error-register-resource-provider.md) |
| TemplateResourceCircularDependency | Távolítsa el a szükségtelen függőségeket. | [Körkörös függőségek feloldása](error-invalid-template.md#circular-dependency) |
| TooManyTargetResourceGroups | Egyetlen üzemelő példány erőforráscsoportszámának csökkentése. | [Hatókörök közötti üzembe helyezés](./deploy-to-resource-group.md) |

## <a name="find-error-code"></a>Hibakód megkerese

Kétféle hibáról kaphat értesítést:

* érvényesítési hiba
* üzembehelyezési hiba

Az érvényesítési hibák olyan forgatókönyvekből adódnak, amelyek az üzembe helyezés előtt határozhatók meg. Ezek közé tartoznak a sablon szintaxishibái, vagy az olyan erőforrások üzembe helyezése, amelyek túllépik az előfizetése kvótáit. Az üzembehelyezési hibák az üzembehelyezési folyamat során lépnek fel. Ezek közé tartozik például egy olyan erőforrás elérésére tett kísérlet, amelynek az üzembe helyezése párhuzamosan zajlik.

Mindkét típusú hiba az üzembe helyezés hibaelhárításához használható hibakódot ad vissza. Mindkét típusú hiba megjelenik a [tevékenységnaplóban](../management/view-activity-logs.md). Az érvényesítési hibák azonban nem jelennek meg az üzembe helyezési előzmények között, mert az üzembe helyezés el sem indult.

### <a name="validation-errors"></a>Érvényesítési hibák

Amikor a portálon keresztül végzi el az üzembe helyezést, az értékek megadása után jelenik meg az érvényesítési hiba.

![portálérvényesítési hiba megjelenítése](./media/common-deployment-errors/validation-error.png)

További információért válassza ki az üzenetet. Az alábbi képen egy **InvalidTemplateDeployment** hiba és egy üzenet látható, amely azt jelzi, hogy a szabályzat blokkolta az üzembe helyezést.

![érvényesítési adatok megjelenítése](./media/common-deployment-errors/validation-details.png)

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

További részleteket láthat az üzembe helyezésről. Válassza ki a beállítást, hogy többet tudjon meg a hibáról.

![sikertelen üzembe helyezés](./media/common-deployment-errors/deployment-failed.png)

Megjelenik a hibaüzenet és a hibakódok. Figyelje meg, hogy két hibakód látható. Az első hibakód (**DeploymentFailed**) egy általános hiba, amely nem adja meg a hiba megoldásához szükséges részleteket. A második hibakód (**StorageAccountNotFound**) megadja a szükséges részleteket.

![hiba részletei](./media/common-deployment-errors/error-details.png)

## <a name="enable-debug-logging"></a>Hibakeresési naplózás engedélyezése

Néha több információra van szüksége a kérésről és a válaszról, hogy megtudja, mi volt a hiba. Az üzembe helyezés során kérheti, hogy a rendszer további adatokat naplóz a telepítés során.

### <a name="powershell"></a>PowerShell

A PowerShellben állítsa a **DeploymentDebugLogLevel** paramétert Az összes, a ResponseContent vagy a RequestContent paraméterre.

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

Vagy a válasz tartalma a következővel:

```powershell
(Get-AzResourceGroupDeploymentOperation `
-DeploymentName exampledeployment `
-ResourceGroupName examplegroup).Properties.response `
| ConvertTo-Json
```

Ezek az információk segíthetnek megállapítani, hogy a sablonban helytelenül van-e beállítva egy érték.

### <a name="azure-cli"></a>Azure CLI

Az Azure CLI jelenleg nem támogatja a hibakeresési naplózás bekapcsolát, de lekérheti a hibakeresési naplózást.

Vizsgálja meg az üzembe helyezési műveleteket a következő paranccsal:

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

Bizonyos esetekben a sablon hibáinak elhárításának legegyszerűbb módja annak egyes részeinek tesztelése. Létrehozhat egy egyszerűsített sablont, amely lehetővé teszi, hogy arra a részre koncentráljon, amely szerinte a hibát okozza. Tegyük fel például, hogy hibaüzenetet kap egy erőforrásra való hivatkozáskor. A teljes sablon kezelése helyett hozzon létre egy sablont, amely visszaadja azt a részt, amely a problémát okozhatja. Segítségével meghatározhatja, hogy a megfelelő paramétereket adhatja-e meg, megfelelően használja a sablonfunkciókat, és le tudja-e kérni a várt erőforrást.

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

Vagy tegyük fel, hogy olyan üzembe helyezési hibákat kap, amelyek úgy vélik, hogy helytelenül beállított függőségekkel kapcsolatosak. Tesztelje a sablont egyszerűsített sablonokra való feltörése segítségével. Először hozzon létre egy sablont, amely csak egyetlen erőforrást helyez üzembe (például egy SQL Server). Ha biztos abban, hogy az erőforrás megfelelően van definiálva, adjon hozzá egy erőforrást, amely attól függ (például egy SQL Database). Ha helyesen definiálta ezt a két erőforrást, adjon hozzá további függő erőforrásokat (például naplózási szabályzatokat). Az egyes teszttelepítések között törölje az erőforráscsoportot, hogy biztosan megfelelően tesztelje a függőségeket.

## <a name="next-steps"></a>Következő lépések

* A hibaelhárítási oktatóanyag lépéseiért lásd: [Oktatóanyag: A Resource Manager üzembe helyezésének hibaelhárítása](template-tutorial-troubleshoot.md)
* További információ a naplózási műveletekről: [Naplózási műveletek a Resource Manager.](../management/view-activity-logs.md)
* Az üzembe helyezés során előforduló hibákat megállapító műveletekkel kapcsolatos információkért lásd: [Telepítési műveletek megtekintése.](deployment-history.md)