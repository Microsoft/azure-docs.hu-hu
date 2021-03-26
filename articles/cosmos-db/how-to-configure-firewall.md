---
title: IP-tűzfal konfigurálása a Azure Cosmos DB-fiókhoz
description: Ismerje meg, hogyan konfigurálhatja az IP-hozzáférés-vezérlési házirendeket az Azure Cosmos-fiókok tűzfal általi támogatásához.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 03/03/2021
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: b94b30851a5206c2183d999a3c024351cf415c90
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105568239"
---
# <a name="configure-ip-firewall-in-azure-cosmos-db"></a>IP-tűzfal konfigurálása Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

A fiókban tárolt adatokat a Azure Cosmos DB támogatja egy olyan titkos alapú engedélyezési modellt, amely erős kivonatoló alapú üzenethitelesítő kód (HMAC) használ. Emellett a Azure Cosmos DB támogatja az IP-alapú hozzáférés-vezérlést a bejövő tűzfalak támogatásához. Ez a modell hasonló a hagyományos adatbázisrendszer tűzfalszabályok esetében, és további biztonsági szintet biztosít a fiókja számára. A tűzfalakkal beállíthatja, hogy az Azure Cosmos-fiók csak jóváhagyott gépekből és/vagy felhőalapú szolgáltatásokból legyen elérhető. Az Azure Cosmos-adatbázisban tárolt adatoknak ezen jóváhagyott készletekből és szolgáltatásokból való elérése továbbra is megköveteli, hogy a hívó érvényes engedélyezési jogkivonatot jelentsen.

## <a name="ip-access-control"></a><a id="ip-access-control-overview"></a>IP-hozzáférés-vezérlés

Alapértelmezés szerint az Azure Cosmos-fiók elérhető az internetről, feltéve, hogy a kérést érvényes engedélyezési jogkivonat kíséri. Az IP-házirend alapú hozzáférés-vezérlés konfigurálásához a felhasználónak meg kell adnia az IP-címek vagy az IP-címtartományok készletét a CIDR (osztály nélküli Inter-Domain útválasztás) képernyőn, hogy az ügyfél IP-címeinek engedélyezett listája szerepeljen egy adott Azure Cosmos-fiók eléréséhez. A konfiguráció alkalmazása után az ezen engedélyezett listán kívüli gépekről származó kérelmek 403 (tiltott) választ kapnak. Ha IP-tűzfalat használ, javasoljuk, hogy engedélyezze a Azure Portal számára a fiók elérését. Hozzáférés szükséges az adatkezelő használatának engedélyezéséhez, valamint a Azure Portalban megjelenített fiók metrikáinak lekéréséhez. Az adatkezelő használatakor a Azure Portal hozzáférésének engedélyezése mellett a tűzfal beállításait is frissítenie kell, hogy az aktuális IP-címet hozzáadja a tűzfalszabályok számára. Vegye figyelembe, hogy a tűzfal módosításai akár 15 percet is igénybe vehetnek, és a tűzfal inkonzisztens viselkedést mutat ebben az időszakban.

Az IP-alapú tűzfalat az alhálózat és a VNET hozzáférés-vezérlés használatával kombinálhatja. Ezek kombinálásával korlátozhatja a hozzáférést bármely olyan forráshoz, amely nyilvános IP-címmel és/vagy a VNET-n belül egy adott alhálózattal rendelkezik. Az alhálózati és a VNET hozzáférés-vezérlés használatáról további információt a [virtuális hálózatok Azure Cosmos db erőforrásainak elérését](./how-to-configure-vnet-service-endpoint.md)ismertető témakörben talál.

Összefoglalva, az engedélyezési jogkivonat mindig szükséges az Azure Cosmos-fiók eléréséhez. Ha nincs beállítva az IP-tűzfal és a VNET Access Control listája (ACL), az Azure Cosmos-fiók az engedélyezési jogkivonattal érhető el. Miután az IP-tűzfal vagy a VNET ACL-je vagy mindkettő be van állítva az Azure Cosmos-fiókon, csak a megadott forrásokból (és az engedélyezési jogkivonattal) származó kérelmek érvényes válaszokat kapnak. 

Az Azure Cosmos DB-fiókban tárolt adatait IP-tűzfalak használatával is biztonságossá teheti. A Azure Cosmos DB támogatja az IP-alapú hozzáférés-vezérlést a bejövő tűzfalak támogatásához. A következő módszerek egyikével állíthatja be az IP-tűzfalat a Azure Cosmos DB fiókban:

* Az Azure Portalról
* Deklaratívan az Azure Resource Manager-sablon használatával
* Programozott módon az Azure CLI-n vagy Azure PowerShellon keresztül, a **ipRangeFilter** tulajdonság frissítésével

## <a name="configure-an-ip-firewall-by-using-the-azure-portal"></a><a id="configure-ip-policy"></a> IP-tűzfal konfigurálása a Azure Portal használatával

A Azure Portal IP-hozzáférés-vezérlési szabályzatának beállításához nyissa meg a Azure Cosmos DB fiók lapot, és válassza a **tűzfal és virtuális hálózatok** lehetőséget a navigációs menüben. Módosítsa az értékről a **kiválasztott hálózatokra** **való hozzáférés engedélyezése lehetőséget** , majd válassza a **Mentés** lehetőséget.

![Képernyőfelvétel: a tűzfal oldalának megnyitása a Azure Portal](./media/how-to-configure-firewall/azure-portal-firewall.png)

Ha az IP-hozzáférés-vezérlés be van kapcsolva, az Azure Portal lehetővé teszi az IP-címek, IP-címtartományok és kapcsolók megadását. A kapcsolók lehetővé teszik a hozzáférést más Azure-szolgáltatásokhoz és a Azure Portalhoz. A kapcsolókról a következő fejezetekben olvashat bővebben.

> [!NOTE]
> Miután engedélyezte a Azure Cosmos DB-fiókjához tartozó IP-hozzáférés-vezérlési házirendet, a rendszer elutasítja a Azure Cosmos DB fiókjának az IP-címtartományok engedélyezett listáját nem tartalmazó összes kérését. A hozzáférés-vezérlés integritásának biztosítása érdekében a portálon található Azure Cosmos DB-erőforrások böngészése is le van tiltva.

### <a name="allow-requests-from-the-azure-portal"></a>Kérések engedélyezése a Azure Portal

Ha programozott módon engedélyezi az IP-hozzáférés-vezérlési házirendet, a hozzáférés fenntartásához hozzá kell adnia a Azure Portal IP-címét a **ipRangeFilter** tulajdonsághoz. A portál IP-címei a következők:

|Region|IP-cím|
|------|----------|
|Németország|51.4.229.218|
|Kína|139.217.8.252|
|US Gov|52.244.48.71|
|Minden más régió|104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26|

Az alábbi képernyőképen látható módon engedélyezheti a kérelmeket a Azure Portal eléréséhez: **hozzáférés engedélyezése Azure Portal** lehetőség kiválasztásával.

![A Azure Portal-hozzáférés engedélyezését bemutató képernyőkép](./media/how-to-configure-firewall/enable-azure-portal.png)

### <a name="allow-requests-from-global-azure-datacenters-or-other-sources-within-azure"></a>Globális Azure-adatközpontokból vagy egyéb Azure-beli forrásokból érkező kérések engedélyezése

Ha a Azure Cosmos DB fiókját olyan szolgáltatásokból éri el, amelyek nem biztosítanak statikus IP-címet (például Azure Stream Analytics és Azure Functions), akkor továbbra is használhatja az IP-tűzfalat a hozzáférés korlátozására. Az Azure-ban lévő más forrásokból is engedélyezheti a hozzáférést az **Azure-adatközpontok lehetőségből származó kapcsolatok fogadása** lehetőség kiválasztásával, az alábbi képernyőképen látható módon:

![Az Azure-adatközpontok kapcsolatainak fogadását bemutató képernyőkép](./media/how-to-configure-firewall/enable-azure-services.png)

Ha engedélyezi ezt a beállítást, a rendszer hozzáadja az IP-címet `0.0.0.0` az engedélyezett IP-címek listájához. Az `0.0.0.0` IP-cím korlátozza a kérelmeket az Azure Datacenter IP-címtartomány Azure Cosmos db-fiókjára. A beállítás semmilyen más IP-tartomány számára nem engedélyezi a hozzáférést az Azure Cosmos DB-fiókhoz.

> [!NOTE]
> Ez a beállítás úgy konfigurálja a tűzfalat, hogy engedélyezze az Azure-ból érkező összes kérést, beleértve az Azure-ban üzembe helyezett más ügyfelek előfizetésének kéréseit is. A beállítás által megengedett IP-címek listája széles körű, ezért korlátozza a tűzfalszabályok hatékonyságát. Ezt a lehetőséget csak akkor használja, ha a kérések nem a virtuális hálózatok statikus IP-címeiről vagy alhálózatáról származnak. Ha ezt a beállítást választja, a rendszer automatikusan engedélyezi a hozzáférést a Azure Portal, mert az Azure Portal üzembe helyezése az Azure-ban történik.

### <a name="requests-from-your-current-ip"></a>Az aktuális IP-címről érkező kérések

A fejlesztés egyszerűsítése érdekében a Azure Portal segíti az ügyfélszámítógép IP-címének azonosítását és hozzáadását az engedélyezett listához. A gépet futtató alkalmazások hozzáférhetnek a Azure Cosmos DB-fiókjához.

A portál automatikusan észleli az ügyfél IP-címét. Lehet, hogy a gép ügyfél IP-címe vagy a hálózati átjáró IP-címe. Mielőtt éles környezetben üzembe helyezi a számítási feladatokat, ügyeljen rá, hogy távolítsa el ezt az IP-címet.

Ha a jelenlegi IP-címet az IP-címek listájához szeretné felvenni, válassza a **jelenlegi IP-cím hozzáadása** lehetőséget. Kattintson a **Mentés** gombra.

:::image type="content" source="./media/how-to-configure-firewall/enable-current-ip.png" alt-text="Képernyőfelvétel: a tűzfalbeállítások konfigurálása az aktuális IP-címhez":::

### <a name="requests-from-cloud-services"></a>A Cloud servicestől érkező kérések

Az Azure-ban a Cloud Services gyakori módszer a közepes szintű szolgáltatás-logika Azure Cosmos DB használatával történő üzemeltetésére. Ha engedélyezni szeretné a Azure Cosmos DB fiókjához való hozzáférést egy felhőalapú szolgáltatásból, hozzá kell adnia a felhőalapú szolgáltatás nyilvános IP-címét a Azure Cosmos DB fiókjához társított IP-címek engedélyezett listájához az [IP-hozzáférés-vezérlési házirend konfigurálásával](#configure-ip-policy). Ez biztosítja, hogy a Cloud Services összes szerepkör-példánya hozzáférhessen a Azure Cosmos DB-fiókjához.

A felhőalapú szolgáltatások IP-címeit a Azure Portal az alábbi képernyőképen látható módon kérheti le:

:::image type="content" source="./media/how-to-configure-firewall/public-ip-addresses.png" alt-text="Képernyőfelvétel: a Azure Portalban megjelenített felhőalapú szolgáltatás nyilvános IP-címének megjelenítése":::

Ha szerepkör-példányok hozzáadásával bővíti a felhőalapú szolgáltatást, akkor ezek az új példányok automatikusan hozzáférhetnek a Azure Cosmos DB fiókhoz, mert ugyanahhoz a felhőalapú szolgáltatáshoz tartoznak.

### <a name="requests-from-virtual-machines"></a>Virtuális gépek kérései

A [virtuális gépeket](https://azure.microsoft.com/services/virtual-machines/) vagy virtuálisgép- [méretezési csoportokat](../virtual-machine-scale-sets/overview.md) is használhat a közepes szintű szolgáltatások üzemeltetéséhez Azure Cosmos db használatával. Ha úgy szeretné konfigurálni a Cosmos DB-fiókot, hogy az lehetővé tegye a virtuális gépekről való hozzáférést, a virtuális gép és/vagy virtuálisgép-méretezési csoport nyilvános IP-címét a Azure Cosmos DB-fiókhoz tartozó [IP-hozzáférés-vezérlési házirend konfigurálásával](#configure-ip-policy)kell konfigurálnia.

A Azure Portal lévő virtuális gépek IP-címeit az alábbi képernyőképen látható módon kérheti le:

:::image type="content" source="./media/how-to-configure-firewall/public-ip-addresses-dns.png" alt-text="A Azure Portalban megjelenített virtuális gép nyilvános IP-címét bemutató képernyőfelvétel":::

Amikor virtuálisgép-példányokat vesz fel a csoportba, a automatikusan hozzáférést kap a Azure Cosmos DB fiókjához.

### <a name="requests-from-the-internet"></a>Az internetről érkező kérések

Amikor az interneten található számítógépről fér hozzá a Azure Cosmos DB-fiókjához, a számítógép ügyfél IP-címét vagy IP-címét hozzá kell adni a fiókja IP-címeinek engedélyezett listájához.

### <a name="add-outbound-rules-to-the-firewall"></a>Kimenő szabályok hozzáadása a tűzfalhoz

A tűzfal beállításaihoz hozzáadandó kimenő IP-címtartományok aktuális listájának eléréséhez tekintse meg az [Azure IP-címtartományok és a szolgáltatási címkék letöltése](https://www.microsoft.com/download/details.aspx?id=56519)című témakört.

A lista automatizálásához tekintse meg [a Service tag Discovery API (nyilvános előzetes verzió) használatát](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview)ismertető témakört.

## <a name="configure-an-ip-firewall-by-using-a-resource-manager-template"></a><a id="configure-ip-firewall-arm"></a>IP-tűzfal konfigurálása Resource Manager-sablon használatával

A Azure Cosmos DB-fiók hozzáférés-vezérlésének konfigurálásához győződjön meg arról, hogy a Resource Manager-sablon a **ipRules** tulajdonságot az engedélyezett IP-címtartományok tömbje alapján határozza meg. Ha az IP-tűzfalat már telepített Cosmos-fiókra konfigurálja, győződjön meg arról, hogy a `locations` tömb megfelel a jelenleg telepített rendszernek. A `locations` tömb és egyéb tulajdonságok nem módosíthatók egyszerre. További információ és példák a Azure Resource Manager-sablonokra Azure Cosmos DB megtekintéséhez: [Azure Resource Manager sablonok Azure Cosmos db](./templates-samples-sql.md)

> [!IMPORTANT]
> A **ipRules** tulajdonság a 2020-04-01-es API-verzióval lett bevezetve. A korábbi verziók Ehelyett egy **ipRangeFilter** tulajdonságot tettek elérhetővé, amely a vesszővel tagolt IP-címek listája.

Az alábbi példa bemutatja, hogyan jelennek meg a **ipRules** tulajdonság az API 2020-04-01-es vagy újabb verziójában:

```json
{
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "name": "[variables('accountName')]",
  "apiVersion": "2020-04-01",
  "location": "[parameters('location')]",
  "kind": "GlobalDocumentDB",
  "properties": {
    "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
    "locations": "[variables('locations')]",
    "databaseAccountOfferType": "Standard",
    "enableAutomaticFailover": "[parameters('automaticFailover')]",
    "ipRules": [
      {
        "ipAddressOrRange": "40.76.54.131"
      },
      {
        "ipAddressOrRange": "52.176.6.30"
      },
      {
        "ipAddressOrRange": "52.169.50.45"
      },
      {
        "ipAddressOrRange": "52.187.184.26"
      }
    ]
  }
}
```

Ugyanez a példa a 2020-04-01 előtti API-verzióra:

```json
{
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "name": "[variables('accountName')]",
  "apiVersion": "2019-08-01",
  "location": "[parameters('location')]",
  "kind": "GlobalDocumentDB",
  "properties": {
    "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
    "locations": "[variables('locations')]",
    "databaseAccountOfferType": "Standard",
    "enableAutomaticFailover": "[parameters('automaticFailover')]",
    "ipRangeFilter":"40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26"
  }
}
```

## <a name="configure-an-ip-access-control-policy-by-using-the-azure-cli"></a><a id="configure-ip-firewall-cli"></a>IP-hozzáférés-vezérlési szabályzat konfigurálása az Azure CLI használatával

Az alábbi parancs bemutatja, hogyan hozhat létre egy olyan Azure Cosmos DB fiókot, amely IP-hozzáférés-vezérléssel rendelkezik:

```azurecli-interactive
# Create a Cosmos DB account with default values and IP Firewall enabled
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
ipRangeFilter='192.168.221.17,183.240.196.255,40.76.54.131'

# Make sure there are no spaces in the comma-delimited list of IP addresses or CIDR ranges.
az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --locations regionName='East US 2' failoverPriority=1 isZoneRedundant=False \
    --ip-range-filter $ipRangeFilter
```

## <a name="configure-an-ip-access-control-policy-by-using-powershell"></a><a id="configure-ip-firewall-ps"></a>IP-hozzáférés-vezérlési házirend konfigurálása a PowerShell használatával

Az alábbi szkript bemutatja, hogyan hozhat létre egy Azure Cosmos DB-fiókot IP-hozzáférés-vezérléssel:

```azurepowershell-interactive
# Create a Cosmos DB account with default values and IP Firewall enabled
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$ipRules = @("192.168.221.17","183.240.196.255","40.76.54.131")

$locations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0; "isZoneRedundant"=False },
    @{ "locationName"="East US 2"; "failoverPriority"=1, "isZoneRedundant"=False }
)

# Make sure there are no spaces in the comma-delimited list of IP addresses or CIDR ranges.
$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "ipRules"=$ipRules
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2020-04-01" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

## <a name="troubleshoot-issues-with-an-ip-access-control-policy"></a><a id="troubleshoot-ip-firewall"></a>IP-hozzáférés-vezérlési házirenddel kapcsolatos problémák elhárítása

Az IP-hozzáférés-vezérlési házirenddel kapcsolatos problémákat a következő beállításokkal lehet elhárítani:

### <a name="azure-portal"></a>Azure Portal

Ha engedélyezi a Azure Cosmos DB-fiókjához tartozó IP-hozzáférés-vezérlési házirendet, a fiókra irányuló összes kérést letiltja az IP-címtartományok engedélyezési listáján kívüli gépekről. A portál adatsík műveleteinek, például a tárolók és a dokumentumok lekérdezésének engedélyezéséhez explicit módon engedélyeznie kell Azure Portal hozzáférését a portál **tűzfal** paneljének használatával.

### <a name="sdks"></a>SDK-k

Ha olyan gépekről próbál meg hozzáférni Azure Cosmos DB erőforrásokhoz, amelyek nem szerepelnek az engedélyezési listán, a rendszer általános **403 Tiltott** választ ad vissza további részletek nélkül. Ellenőrizze az engedélyezett IP-listát a fiókjához, és győződjön meg arról, hogy a megfelelő házirend-konfigurációt alkalmazza a rendszer a Azure Cosmos DB-fiókjára.

### <a name="source-ips-in-blocked-requests"></a>A letiltott kérelmekben szereplő forrás IP-címek

Engedélyezze a diagnosztikai naplózást a Azure Cosmos DB-fiókjában. Ezek a naplók minden kérelmet és választ jelenítenek meg. A tűzfalral kapcsolatos üzenetek naplózása 403 visszatérési kóddal történik. Az üzenetek szűrésével megtekintheti a letiltott kérelmek forrás IP-címeit. Lásd: [Azure Cosmos db diagnosztikai naplózás](./monitor-cosmos-db.md).

### <a name="requests-from-a-subnet-with-a-service-endpoint-for-azure-cosmos-db-enabled"></a>Az alhálózaton lévő, Azure Cosmos DB engedélyezett szolgáltatás-végponttal rendelkező alhálózat kérései

A virtuális hálózat azon alhálózatai által küldött kérelmek, amelyek szolgáltatás-végpontja Azure Cosmos DB engedélyezve, a virtuális hálózatot és az alhálózati identitást Azure Cosmos DB fiókoknak küldi el. Ezek a kérések nem rendelkeznek a forrás nyilvános IP-címével, ezért az IP-szűrők elutasítja őket. Ha engedélyezni szeretné a hozzáférést a virtuális hálózatokban lévő adott alhálózatokról, vegyen fel egy hozzáférés-vezérlési listát a [virtuális hálózat és az alhálózat-alapú hozzáférés konfigurálása a Azure Cosmos db-fiókhoz című témakörben](how-to-configure-vnet-service-endpoint.md)leírtak szerint. A tűzfalszabályok alkalmazása akár 15 percet is igénybe vehet, és a tűzfal nem konzisztens viselkedést mutat ezen időszak alatt.

### <a name="private-ip-addresses-in-list-of-allowed-addresses"></a>Magánhálózati IP-címek az engedélyezett címek listájában

Egy Azure Cosmos-fiók létrehozása vagy frissítése a privát IP-címeket tartalmazó engedélyezett címek listájával sikertelen lesz. Győződjön meg arról, hogy nincs megadva magánhálózati IP-cím a listában.

## <a name="next-steps"></a>Következő lépések

A Azure Cosmos DB-fiókhoz tartozó virtuális hálózati szolgáltatás végpontjának konfigurálásához tekintse meg a következő cikkeket:

* [Virtuális hálózati és alhálózat-hozzáférés-vezérlés a Azure Cosmos DB-fiókhoz](how-to-configure-vnet-service-endpoint.md)
* [A virtuális hálózat és az alhálózat-alapú hozzáférés konfigurálása a Azure Cosmos DB-fiókhoz](how-to-configure-vnet-service-endpoint.md)