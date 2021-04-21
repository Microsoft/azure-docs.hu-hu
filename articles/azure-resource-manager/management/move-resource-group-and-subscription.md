---
title: Erőforrások áthelyezése új előfizetésbe vagy erőforráscsoportba
description: 'A Azure Resource Manager erőforrások új erőforráscsoportba vagy előfizetésbe való áthelyezéséhez használja a következőt: .'
ms.topic: conceptual
ms.date: 04/16/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7a50ecc6081f8fa7c7600ddf1f98a95eceafa73b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784622"
---
# <a name="move-resources-to-a-new-resource-group-or-subscription"></a>Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe

Ez a cikk bemutatja, hogyan helyezze át az Azure-erőforrásokat egy másik Azure-előfizetésbe vagy egy másik erőforráscsoportba ugyanabban az előfizetésben. Az erőforrások áthelyezéséhez az Azure Portal, az Azure PowerShell, az Azure CLI vagy a REST API használható.

A forráscsoport és a célcsoport is zárolva van az áthelyezési művelet során. Az írási és törlési műveletek le vannak tiltva az erőforráscsoportokon, amíg az áthelyezés be nem fejeződik. Ez a zárolás azt jelenti, hogy nem adhat hozzá, frissíthet vagy törölhet erőforrásokat az erőforráscsoportokban. Ez nem jelenti azt, hogy az erőforrások le vannak fagyva. Ha például áthelyez egy Azure SQL logikai kiszolgálót és annak adatbázisait egy új erőforráscsoportba vagy előfizetésbe, az adatbázisokat kezelő alkalmazások nem tapasztalnak állásidőt. Továbbra is olvashatnak és írhatnak az adatbázisokba. A zárolás legfeljebb négy óráig is tart, de a legtöbb lépés sokkal rövidebb idő alatt befejeződik.

Az erőforrás az áthelyezése során csak egy új erőforráscsoportba vagy előfizetésbe kerül. Az erőforrás helyét az áthelyezési művelet nem módosítja.

## <a name="changed-resource-id"></a>Erőforrás-azonosító módosult

Amikor áthelyez egy erőforrást, módosítja annak erőforrás-azonosítóját. Az erőforrás-azonosító szabványos formátuma `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}` a következő: . Amikor áthelyez egy erőforrást egy új erőforráscsoportba vagy előfizetésbe, egy vagy több értéket módosít az elérési úton.

Ha bárhol használja az erőforrás-azonosítót, módosítania kell ezt az értéket. Ha például egy erőforrás-azonosítóra hivatkozó egyéni irányítópultja van a portálon, frissítenie kell ezt az értéket. [](../../azure-portal/quickstart-portal-dashboard-azure-cli.md) Keresse meg az új erőforrás-azonosítóhoz frissíteni szükséges szkripteket és sablonokat.

## <a name="checklist-before-moving-resources"></a>Ellenőrzőlista az erőforrások áthelyezése előtt

Néhány fontos lépést végre kell hajtani az erőforrások áthelyezése előtt. Ezen feltételek ellenőrzésével a hibák elkerülhetőek.

1. Az áthelyezni kívánt erőforrásoknak támogatniuk kell az áthelyezési műveletet. Az áthelyezést támogató erőforrások listáját lásd: Erőforrások [áthelyezési műveleteinek támogatása.](move-support-resources.md)

1. Egyes szolgáltatásokra konkrét korlátozások vagy követelmények vonatkoznak az erőforrások áthelyezésekor. Ha a következő szolgáltatások bármelyikét átköltözti, a áthelyezés előtt tekintse át ezt az útmutatót.

   * Ha az erőforrásokat Azure Stack Hub, nem tudja áthelyezni az erőforrásokat a csoportok között.
   * [App Services útmutató az áthelyezéshez](./move-limitations/app-service-move-limitations.md)
   * [Útmutató az Azure DevOps Services áthelyezési útmutatóihoz](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
   * [Útmutató a klasszikus üzembe helyezési](./move-limitations/classic-model-move-limitations.md) modell áthelyezéséhez – Klasszikus számítás, Klasszikus tárolás, Klasszikus virtuális hálózatok és Cloud Services
   * [Hálózati áthelyezési útmutató](./move-limitations/networking-move-limitations.md)
   * [Helyreállítási szolgáltatások áthelyezési útmutatója](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
   * [Virtual Machines útmutató az áthelyezéshez](./move-limitations/virtual-machines-move-limitations.md)
   * Azure-előfizetés új felügyeleti csoportba való áthelyezéséhez lásd: [Előfizetések áthelyezése.](../../governance/management-groups/manage.md#move-subscriptions)

1. Ha olyan erőforrást költöztet, amely közvetlenül az erőforráshoz (vagy egy gyermekerőforráshoz) rendelt Azure-szerepkört rendelkezik, a szerepkör-hozzárendelés nem lesz áthelyezve, és árva lesz. Az áthelyezés után újra létre kell hoznia a szerepkör-hozzárendelést. Végül a rendszer automatikusan eltávolítja az árva szerepkör-hozzárendelést, de javasoljuk, hogy az áthelyezés előtt távolítsa el a szerepkör-hozzárendelést.

    A szerepkör-hozzárendelések kezeléséhez további információt az Azure-beli szerepkör-hozzárendelések felsorolását és az [Azure-szerepkörök](../../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-at-a-scope) [hozzárendelését (](../../role-based-access-control/role-assignments-portal.md)).

1. A forrás- és cél-előfizetésnek aktívnak kell lennie. Ha problémája van egy letiltott fiók engedélyezésével, hozzon létre egy Azure-támogatás [kérést.](../../azure-portal/supportability/how-to-create-azure-support-request.md) Válassza **az Előfizetés-kezelés** lehetőséget a probléma típusaként.

1. A forrás- és cél-előfizetésnek ugyanabban a bérlőben [Azure Active Directory lennie.](../../active-directory/develop/quickstart-create-new-tenant.md) Annak ellenőrzéséhez, hogy mindkét előfizetés azonos bérlőazonosítóval rendelkezik-e, használja Azure PowerShell Azure CLI-t.

   A Azure PowerShell használja a következőt:

   ```azurepowershell-interactive
   (Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
   (Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
   ```

   Azure CLI esetén használja az alábbi parancsot:

   ```azurecli-interactive
   az account show --subscription <your-source-subscription> --query tenantId
   az account show --subscription <your-destination-subscription> --query tenantId
   ```

   Ha a forrás- és cél-előfizetés bérlői számai nem azonosak, a következő módszerekkel egyezteti össze a bérlői adatokat:

   * [Azure-előfizetés tulajdonjogának átruházása másik fiókra](../../cost-management-billing/manage/billing-subscription-transfer.md)
   * [Azure-előfizetés társítása vagy hozzáadása az Azure Active Directoryhoz](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

1. A cél előfizetést regisztrálni kell az áthelyezett erőforrás erőforrás-szolgáltatóján. Ha nem, hibaüzenetet kap, amely szerint az előfizetés nincs regisztrálva **a erőforrástípushoz.** Ez a hiba akkor jelenhet meg, ha egy erőforrást új előfizetésbe költöztet, de az előfizetést még soha nem használták ezzel az erőforrástípussal.

   A PowerShellben a következő parancsokkal lekérte a regisztrációs állapotot:

   ```azurepowershell-interactive
   Set-AzContext -Subscription <destination-subscription-name-or-id>
   Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
   ```

   Erőforrás-szolgáltató regisztráláshoz használja a következőt:

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
   ```

   Az Azure CLI-hez használja a következő parancsokat a regisztráció állapotának lekért állapotához:

   ```azurecli-interactive
   az account set -s <destination-subscription-name-or-id>
   az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
   ```

   Erőforrás-szolgáltató regisztráláshoz használja a következőt:

   ```azurecli-interactive
   az provider register --namespace Microsoft.Batch
   ```

1. Az erőforrásokat átköltöző fióknak legalább az alábbi engedélyekkel kell rendelkeznie:

   * **Microsoft.Resources/subscriptions/resourceGroups/moveResources/action** a forrás erőforráscsoporton.
   * **Microsoft.Resources/subscriptions/resourceGroups/write** a cél erőforráscsoporton.

1. Az erőforrások áthelyezése előtt ellenőrizze annak az előfizetésnek az előfizetési kvótáját, amelybe az erőforrásokat átköltözteti. Ha az erőforrások áthelyezésével az előfizetés túllépi a korlátot, át kell vizsgálnia, hogy kérheti-e a kvóta emelését. A korlátok listájáért és a növekedés kérésének részleteiért lásd: Azure-előfizetések és -szolgáltatások [korlátai, kvótái és megkötései.](../../azure-resource-manager/management/azure-subscription-service-limits.md)

1. **Az előfizetések közötti áthelyezéshez az erőforrásnak és annak függő erőforrásainak ugyanabban az erőforráscsoportban kell lennie, és együtt kell áthelyezni őket.** Például egy felügyelt lemezekkel rendelkezik virtuális gép esetén a virtuális gépet és a felügyelt lemezeket együtt kell áthelyezni más függő erőforrásokkal együtt.

   Ha egy erőforrást új előfizetésbe költöztet, ellenőrizze, hogy az erőforrás rendelkezik-e függő erőforrásokkal, és hogy ugyanabban az erőforráscsoportban találhatók-e. Ha az erőforrások nem ugyanabban az erőforráscsoportban vannak, ellenőrizze, hogy az erőforrások kombinálhatók-e ugyanabban az erőforráscsoportban. Ha igen, helyezze ezeket az erőforrásokat ugyanoda az erőforráscsoportba egy áthelyezési művelettel az erőforráscsoportok között.

   További információ: [Előfizetések közötti áthelyezés forgatókönyve.](#scenario-for-move-across-subscriptions)

## <a name="scenario-for-move-across-subscriptions"></a>Forgatókönyv az előfizetések közötti áthelyezéshez

Az erőforrások egyik előfizetésből a másikba való áthelyezése három lépésből áll:

![előfizetések közötti áthelyezési forgatókönyv](./media/move-resource-group-and-subscription/cross-subscription-move-scenario.png)

Szemléltetés céljából csak egy függő erőforrásunk van.

* 1. lépés: Ha a függő erőforrások különböző erőforráscsoportok között vannak elosztva, először helyezze át őket egy erőforráscsoportba.
* 2. lépés: Helyezze át az erőforrást és a függő erőforrásokat a forrás-előfizetésből a cél-előfizetésbe.
* 3. lépés: Szükség esetén újra eloszthatja a függő erőforrásokat a cél-előfizetésen belüli különböző erőforráscsoportokba.

## <a name="validate-move"></a>Áthelyezés ellenőrzése

Az [áthelyezés érvényesítése művelettel](/rest/api/resources/resources/moveresources) anélkül tesztelheti az áthelyezési forgatókönyvet, hogy ténylegesen áthelyezi az erőforrásokat. Ezzel a művelettel ellenőrizheti, hogy az áthelyezés sikeres lesz-e. Az áthelyezési kérelem küldésekor a rendszer automatikusan behívja az érvényesítést. Ezt a műveletet csak akkor használja, ha az eredményeket előre meg kell tudni tudni. A művelet futtatásához a következőre lesz szüksége:

* a forrás erőforráscsoport neve
* a cél erőforráscsoport erőforrás-azonosítója
* az áthelyezni szükséges erőforrások erőforrás-azonosítója
* a [fiók hozzáférési jogkivonata](/rest/api/azure/#acquire-an-access-token)

Küldje el a következő kérést:

```HTTP
POST https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<source-group>/validateMoveResources?api-version=2019-05-10
Authorization: Bearer <access-token>
Content-type: application/json
```

A kérelem törzsével:

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

Ha a kérés megfelelően van formázva, a művelet a következőt adja vissza:

```HTTP
Response Code: 202
cache-control: no-cache
pragma: no-cache
expires: -1
location: https://management.azure.com/subscriptions/<subscription-id>/operationresults/<operation-id>?api-version=2018-02-01
retry-after: 15
...
```

A 202-es állapotkód azt jelzi, hogy az érvényesítési kérelem elfogadva lett, de még nem határozott meg, hogy az áthelyezési művelet sikeres lesz-e. Az érték tartalmaz egy URL-címet, amely a hosszú ideig futó művelet `location` állapotának ellenőrzéséhez használható.

Az állapot ellenőrzéshez küldje el a következő kérést:

```HTTP
GET <location-url>
Authorization: Bearer <access-token>
```

Amíg a művelet még fut, továbbra is megkapja a 202-es állapotkódot. Az újra próbálkozás előtt várja meg az értékben jelzett `retry-after` másodpercek számát. Ha az áthelyezési művelet sikeresen érvényesítve van, a 204-es állapotkódot kapja. Ha az áthelyezés érvényesítése sikertelen, a következő hibaüzenet jelenik meg:

```json
{"error":{"code":"ResourceMoveProviderValidationFailed","message":"<message>"...}}
```

## <a name="use-the-portal"></a>A portál használata

Az erőforrások áthelyezéshez válassza ki az erőforrásokat tartalmazó erőforráscsoportot.

Az erőforráscsoport megtekintésekor az áthelyezési lehetőség le van tiltva.

:::image type="content" source="./media/move-resource-group-and-subscription/move-first-view.png" alt-text="áthelyezési lehetőség letiltva":::

Az áthelyezési lehetőség engedélyezéséhez válassza ki az áthelyezni kívánt erőforrásokat. Az összes erőforrás kiválasztásához jelölje be a lista tetején található jelölőnégyzetet. Vagy válassza ki egyenként az erőforrásokat. Az erőforrások kiválasztása után az áthelyezési lehetőség engedélyezve lesz.

:::image type="content" source="./media/move-resource-group-and-subscription/select-resources.png" alt-text="erőforrások kiválasztása":::

Válassza az **Áthelyezés** gombot.

:::image type="content" source="./media/move-resource-group-and-subscription/move-options.png" alt-text="áthelyezési beállítások":::

Ez a gomb három lehetőséget kínál:

* Lépjen egy új erőforráscsoportba.
* Lépjen egy új előfizetésre.
* Lépjen egy új régióba. A régiók közötti váltáshoz lásd: Erőforrások áthelyezése régiók [között (erőforráscsoportból).](../../resource-mover/move-region-within-resource-group.md?toc=/azure/azure-resource-manager/management/toc.json)

Válassza ki, hogy az erőforrásokat új erőforráscsoportba vagy új előfizetésbe költözteti-e.

Válassza ki a célként kiválasztott erőforráscsoportot. Nyugtázza, hogy frissítenie kell az erőforrások szkripteket, majd kattintson az **OK gombra.** Ha az új előfizetésre való áthelyezést választotta, akkor a cél-előfizetést is ki kell választania.

:::image type="content" source="./media/move-resource-group-and-subscription/move-destination.png" alt-text="cél kiválasztása":::

Miután érvényesíti az erőforrások áthelyezésének érvényességét, megjelenik egy értesítés arról, hogy az áthelyezési művelet fut.

:::image type="content" source="./media/move-resource-group-and-subscription/move-notification.png" alt-text="Értesítés":::

Ha befejeződött, értesítést kap az eredményről.

## <a name="use-azure-powershell"></a>Azure PowerShell használatával

A meglévő erőforrások másik erőforráscsoportba vagy előfizetésbe való áthelyezéséhez használja a [Move-AzResource parancsot.](/powershell/module/az.resources/move-azresource) Az alábbi példa bemutatja, hogyan lehet több erőforrást áthelyezni egy új erőforráscsoportba.

```azurepowershell-interactive
$webapp = Get-AzResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

Új előfizetésre való áthelyezéshez a paraméter értékét kell `DestinationSubscriptionId` tartalmaznia.

## <a name="use-azure-cli"></a>Az Azure parancssori felület használatával

A meglévő erőforrások másik erőforráscsoportba vagy előfizetésbe való áthelyezéséhez használja [az az resource move](/cli/azure/resource#az_resource_move) parancsot. Adja meg az áthelyezni szükséges erőforrások erőforrás-adatokat. Az alábbi példa bemutatja, hogyan lehet több erőforrást áthelyezni egy új erőforráscsoportba. A paraméterben adja meg az áthelyezni szükséges erőforrás-adatok szóközrel `--ids` elválasztott listáját.

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

Új előfizetésre való áthelyezéshez adja meg a `--destination-subscription-id` paramétert.

## <a name="use-rest-api"></a>A REST API használata

A meglévő erőforrások másik erőforráscsoportba vagy előfizetésbe való áthelyezéséhez használja az [Erőforrások áthelyezése](/rest/api/resources/resources/moveresources) műveletet.

```HTTP
POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version}
```

A kérelem törzsében meg kell adnia a célként megadott erőforráscsoportot és az áthelyezni kívánt erőforrásokat.

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

## <a name="frequently-asked-questions"></a>Gyakori kérdések

**Kérdés: Az erőforrás-áthelyezési műveletem, amely általában néhány percet vesz igénybe, már majdnem egy órája fut. Valami nem megfelelő?**

Az erőforrások mozgatása egy összetett művelet, amely különböző fázisokkal rendelkezik. Nem csak az áthelyezni próbált erőforrás erőforrás-szolgáltatóját is magában foglalhatja. Az erőforrás-szolgáltatók közötti függőségek miatt a Azure Resource Manager 4 órát is lehetővé tesz a művelet befejezéséhez. Ez az időszak lehetőséget biztosít az erőforrás-szolgáltatók számára az átmeneti problémák helyreállítására. Ha az áthelyezési kérelem a négyórás időszakon belül van, a művelet továbbra is megpróbálja végrehajtani a műveletet, és továbbra is sikeres lehet. A forrás- és célerőforráscsoportok ez idő alatt zárolva vannak a konzisztencia-problémák elkerülése érdekében.

**Kérdés: Miért van az erőforráscsoportom négy órán keresztül zárolva az erőforrás áthelyezése során?**

Egy áthelyezési kérelem legfeljebb négy óra alatt teljesíthető. Az erőforrások áthelyezésének megakadályozása érdekében a forrás és a cél erőforráscsoport is zárolva lesz az erőforrás áthelyezése során.

Az áthelyezési kérések két fázisból állnak. Az első fázisban az erőforrást áthelyezik. A második fázisban az értesítéseket az áthelyezni szükséges erőforrás-szolgáltatóknak küldi el a rendszer. Egy erőforráscsoport a teljes négy órára zárolható, ha egy erőforrás-szolgáltató egyik fázisban sem működik. Az engedélyezett idő alatt a Resource Manager újra a sikertelen lépést.

Ha egy erőforrást nem lehet négy órán belül áthelyezni, a Resource Manager feloldja mindkét erőforráscsoportot. A sikeresen áthelyezte erőforrások a cél erőforráscsoportban vannak. A nem áthelyezni nem sikerült erőforrások a forrás erőforráscsoportban maradnak.

**Kérdés: Milyen következményekkel jár a forrás- és célerőforrás-csoportok zárolása az erőforrás áthelyezése során?**

A zárolás megakadályozza, hogy törölje bármelyik erőforráscsoportot, új erőforrást hozzon létre az erőforráscsoportban, vagy törölje az áthelyezésben részt vevő összes erőforrást.

Az alábbi képen egy hibaüzenet látható a Azure Portal amikor egy felhasználó törölni próbál egy folyamatban lévő áthelyezés részét képezi erőforráscsoportot.

![Törlésre tett hibaüzenet áthelyezése](./media/move-resource-group-and-subscription/move-error-delete.png)

**Kérdés: Mit jelent a "MissingMoveDependentResources" hibakód?**

Egy erőforrás áthelyezésekor a függő erőforrásoknak léteznie kell a cél erőforráscsoportban vagy előfizetésben, vagy szerepelniük kell az áthelyezési kérelemben. A MissingMoveDependentResources hibakód akkor jelenik meg, ha egy függő erőforrás nem felel meg ennek a követelménynek. A hibaüzenet tartalmazza az áthelyezési kérelembe foglalni szükséges függő erőforrás részleteit.

Egy virtuális gép áthelyezésének például hét erőforrástípus három különböző erőforrás-szolgáltatóval való áthelyezését követelheti meg. Ezek az erőforrás-szolgáltatók és -típusok a következőek:

* Microsoft.Compute

  * virtualMachines
  * Lemezek
* Microsoft.Network
  * networkInterfaces
  * publicIPAddresses
  * networkSecurityGroups
  * virtualNetworks
* Microsoft.Storage
  * storageAccounts (tárfiókok)

Egy másik gyakori példa egy virtuális hálózat áthelyezésével. Előfordulhat, hogy több, a virtuális hálózathoz társított erőforrást is át kell helyezzen. Az áthelyezési kérelemhez szükség lehet nyilvános IP-címek, útvonaltáblák, virtuális hálózati átjárók, hálózati biztonsági csoportok stb. áthelyezésére.

**Kérdés: Miért nem tudok áthelyezni egyes erőforrásokat az Azure-ban?**

Jelenleg nem minden erőforrás áthelyezése Azure-támogatás át. Az áthelyezést támogató erőforrások listáját lásd: Áthelyezési [műveletek támogatása erőforrásokhoz.](move-support-resources.md)

**Kérdés: Hány erőforrást lehet áthelyezni egyetlen műveletben?**

Ha lehetséges, a nagyméretű áthelyezéseket külön áthelyezési műveletekre kell feltörése. Resource Manager azonnal hibát ad vissza, ha egyetlen műveletben több mint 800 erőforrás van. A 800-asnál kevesebb erőforrás áthelyezésével azonban időkorrel is meghiúsulhat.

**Kérdés: Mit jelent az a hiba, hogy egy erőforrás nem sikeres állapotban van?**

Ha olyan hibaüzenetet kap, amely azt jelzi, hogy egy erőforrás nem mozgatható, mert nem sikeres állapotban van, akkor az valójában egy függő erőforrás lehet, amely blokkolja az áthelyezést. A hibakód általában **MoveCannotProceedWithResourcesNotInSucceededState.**

Ha a forrás vagy cél erőforráscsoport virtuális hálózatot tartalmaz, a rendszer az áthelyezés során ellenőrzi a virtuális hálózat összes függő erőforrásának az államát. Az ellenőrzés közvetlenül és közvetve a virtuális hálózattól függő erőforrásokat is magában foglalja. Ha ezen erőforrások bármelyike meghiúsult állapotban van, az áthelyezés le lesz tiltva. Ha például egy virtuális hálózatot használó virtuális gép meghibásodott, az áthelyezés le lesz tiltva. Az áthelyezés akkor is blokkolva van, ha a virtuális gép nem az áthelyezni egyik erőforrás sem, és nem az áthelyezés egyik erőforráscsoportja sem.

Ha ezt a hibaüzenetet kapja, két lehetőség közül választhat. Helyezze át az erőforrásokat egy olyan erőforráscsoportba, amely nem rendelkezik virtuális hálózattal, vagy lépjen kapcsolatba az [ügyfélszolgálattal.](../../azure-portal/supportability/how-to-create-azure-support-request.md)

## <a name="next-steps"></a>Következő lépések

Az áthelyezést támogató erőforrások listáját lásd: Erőforrások [áthelyezési műveleteinek támogatása.](move-support-resources.md)
