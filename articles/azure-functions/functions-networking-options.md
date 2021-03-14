---
title: Az Azure Functions hálózatkezelési lehetőségei
description: A Azure Functionsben elérhető összes hálózati beállítás áttekintése.
author: cachai2
ms.topic: conceptual
ms.date: 1/21/2021
ms.author: cachai
ms.openlocfilehash: 0267184a921c92c3dc092908a09467ef3a090175
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2021
ms.locfileid: "103463034"
---
# <a name="azure-functions-networking-options"></a>Az Azure Functions hálózatkezelési lehetőségei

Ez a cikk a Azure Functions üzemeltetési lehetőségei között elérhető hálózati szolgáltatásokat ismerteti. A következő hálózatkezelési lehetőségek lehetővé teszi az erőforrások elérését internetről átirányítható címek használata nélkül, vagy a Function app-hozzáférés korlátozására.

Az üzemeltetési modellek különböző szintű hálózati elkülönítéssel rendelkeznek. A megfelelő beállítás kiválasztásával teljesítheti a hálózat elkülönítési követelményeit.

A Function apps több módon is üzemeltethető:

* Több-bérlős infrastruktúrán futó tervezési lehetőségek közül választhat, a virtuális hálózati kapcsolat és a méretezési lehetőségek különböző szintjeivel:
    * A [fogyasztási terv](consumption-plan.md) dinamikusan méretezi a terhelést, és minimális hálózati elkülönítési lehetőségeket kínál.
    * A [Prémium csomag](functions-premium-plan.md) emellett dinamikusan méretezi a hálózatot, és átfogóbb hálózati elkülönítést biztosít.
    * Az Azure [app Service-csomag](dedicated-plan.md) rögzített skálán működik, és a prémium csomaghoz hasonló hálózati elkülönítést biztosít.
* A függvényeket futtathatja egy [app Service Environmentban](../app-service/environment/intro.md). Ez a metódus üzembe helyezi a függvényt a virtuális hálózatban, és teljes hálózati vezérlést és elkülönítést biztosít.

## <a name="matrix-of-networking-features"></a>Hálózati szolgáltatások mátrixa

[!INCLUDE [functions-networking-features](../../includes/functions-networking-features.md)]

## <a name="inbound-access-restrictions"></a>Bejövő hozzáférési korlátozások

A hozzáférési korlátozások használatával megadhatja az alkalmazáshoz engedélyezett vagy megtagadott IP-címek prioritással rendezett listáját. A lista tartalmazhat IPv4-és IPv6-címeket, illetve adott virtuális hálózati alhálózatokat a [szolgáltatási végpontok](#use-service-endpoints)használatával. Ha egy vagy több bejegyzés van, akkor a lista végén egy implicit "elutasítás all" érték szerepel. Az IP-korlátozások az összes funkció-üzemeltetési lehetőséggel működnek.

A hozzáférési korlátozások a [prémium](functions-premium-plan.md), a [felhasználás](consumption-plan.md)és a [app Service](dedicated-plan.md)esetében érhetők el.

> [!NOTE]
> A hálózati korlátozásokkal csak a virtuális hálózatról telepítheti a szolgáltatást, vagy ha az Ön által használt gép IP-címét a biztonságos címzettek listáján lévő Azure Portal eléréséhez helyezi el. Azonban továbbra is kezelheti a függvényt a portál használatával.

További információ: [Azure app Service statikus hozzáférési korlátozások](../app-service/app-service-ip-restrictions.md).

### <a name="use-service-endpoints"></a>Szolgáltatásvégpontok használata

A szolgáltatási végpontok használatával korlátozhatja a hozzáférést a kiválasztott Azure-beli virtuális hálózati alhálózatokhoz. Egy adott alhálózathoz való hozzáférés korlátozásához hozzon létre egy korlátozási szabályt egy **Virtual Network** típussal. Ezután kiválaszthatja azt az előfizetést, virtuális hálózatot és alhálózatot, amely számára engedélyezni vagy megtagadni kívánja a hozzáférést. 

Ha a szolgáltatási végpontok még nincsenek engedélyezve a Microsoft. Web-ben a kiválasztott alhálózathoz, akkor automatikusan engedélyezve lesznek, kivéve, ha bejelöli a **hiányzó Microsoft. Web Service-végpontok figyelmen kívül hagyása** jelölőnégyzetet. A forgatókönyv, ahol előfordulhat, hogy engedélyezni szeretné a szolgáltatási végpontokat az alkalmazáson, de az alhálózat nem függ elsősorban attól, hogy rendelkezik-e az alhálózaton való engedélyezéséhez szükséges engedélyekkel. 

Ha valaki másnak kell engedélyeznie a szolgáltatási végpontokat az alhálózaton, jelölje be a **hiányzó Microsoft. webszolgáltatás-végpontok figyelmen kívül hagyása** jelölőnégyzetet. Az alkalmazás a szolgáltatás-végpontok számára lesz konfigurálva, hogy az alhálózaton később engedélyezzék őket. 

![Képernyőfelvétel: az "IP-korlátozás hozzáadása" panel a kiválasztott Virtual Network típussal.](../app-service/media/app-service-ip-restrictions/access-restrictions-vnet-add.png)

A szolgáltatási végpontok nem használhatók a App Service Environmenton futó alkalmazásokhoz való hozzáférés korlátozására. Ha az alkalmazás egy App Service Environmentban van, akkor az IP-hozzáférési szabályok alkalmazásával szabályozhatja a hozzáférését. 

A szolgáltatási végpontok beállításával kapcsolatos további információkért lásd: [Azure functions privát hely elérésének létrehozása](functions-create-private-site-access.md).

## <a name="private-endpoint-connections"></a>Magánhálózati végpontok kapcsolatai

[!INCLUDE [functions-private-site-access](../../includes/functions-private-site-access.md)]

Ha más olyan szolgáltatásokat szeretne meghívni, amelyek privát végponti kapcsolatban állnak, például a Storage vagy a Service Bus szolgáltatással, konfigurálja úgy az alkalmazást, hogy kimenő hívásokat hajtson végre a [privát végpontok számára](#private-endpoints).

## <a name="virtual-network-integration"></a>Virtuális hálózat integrációja

A virtuális hálózat integrációja lehetővé teszi, hogy a Function alkalmazás hozzáférjen a virtuális hálózaton belüli erőforrásokhoz.
A Azure Functions kétféle virtuális hálózati integrációt támogat:

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

A Azure Functions virtuális hálózati integrációja a megosztott infrastruktúrát App Service Web Apps használatával használja. Ha többet szeretne megtudni a virtuális hálózatok integrációjának két típusáról, tekintse meg a következőt:

* [Regionális virtuális hálózat integrációja](../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)
* [Átjáró – szükséges virtuális hálózati integráció](../app-service/web-sites-integrate-with-vnet.md#gateway-required-vnet-integration)

A virtuális hálózati integráció beállításával kapcsolatos további információkért lásd: [Function App-alkalmazás integrálása Azure-beli virtuális hálózattal](functions-create-vnet.md).

## <a name="regional-virtual-network-integration"></a>Regionális virtuális hálózat integrációja

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

## <a name="connect-to-service-endpoint-secured-resources"></a>Kapcsolódás a szolgáltatás végpontjának biztonságos erőforrásaihoz

A magasabb szintű biztonság érdekében a szolgáltatás-végpontok segítségével számos Azure-szolgáltatást korlátozhat egy virtuális hálózatra. Ezután integrálnia kell a Function alkalmazást az adott virtuális hálózattal az erőforrás eléréséhez. Ezt a konfigurációt minden olyan csomag támogatja, amely támogatja a virtuális hálózatok integrálását.

További információ: [Virtual Network szolgáltatás-végpontok](../virtual-network/virtual-network-service-endpoints-overview.md).

## <a name="restrict-your-storage-account-to-a-virtual-network"></a>A Storage-fiók korlátozása virtuális hálózatra 

Egy Function-alkalmazás létrehozásakor létre kell hoznia egy általános célú Azure Storage-fiókot, amely támogatja a blobot, a várólistát és a Table Storage-t. Ezt a Storage-fiókot lecserélheti egy, a szolgáltatási végpontokkal vagy privát végponttal védett tárolóval. Ez a funkció jelenleg az összes olyan Windows Virtual Network támogatott SKU-t támogatja, amely tartalmazza a standard és a prémium szintet, kivéve a Flex-bélyegzőket, amelyekben a virtuális hálózatok csak prémium SKU-hoz érhetők el. Egy privát hálózatra korlátozódó Storage-fiókkal rendelkező függvény beállítása:

1. Hozzon létre egy olyan függvényt, amely nem rendelkezik engedélyezett szolgáltatási végpontokkal.
1. Konfigurálja a függvényt a virtuális hálózathoz való kapcsolódáshoz.
1. Hozzon létre vagy konfiguráljon egy másik Storage-fiókot.  Ez lesz az a Storage-fiók, amelyet a szolgáltatási végpontok biztosítanak, és összekapcsolhatjuk a funkciót.
1. [Hozzon létre egy fájlmegosztást](../storage/files/storage-how-to-create-file-share.md#create-file-share) a biztonságos Storage-fiókban.
1. Engedélyezze a szolgáltatási végpontokat vagy a magánhálózati végpontot a Storage-fiókhoz.  
    * Privát végponti kapcsolatok használata esetén a Storage-fióknak szüksége lesz egy privát végpontra a `file` és az `blob` alerőforrásokhoz.  Ha bizonyos képességeket (például Durable Functions) használ, `queue` `table` egy privát végponti kapcsolaton keresztül is szüksége lesz rá, és elérhetővé válik.
    * Ha szolgáltatási végpontokat használ, engedélyezze a Function apps számára dedikált alhálózatot a Storage-fiókokhoz.
1. Másolja a fájl és a blob tartalmát a Function app Storage-fiókból a biztonságos Storage-fiókba és a fájlmegosztásba.
1. Másolja ki a Storage-fiókhoz tartozó kapcsolatok karakterláncát.
1. Frissítse az **alkalmazás beállításait** a Function alkalmazás **konfigurációjában** a következőre:
    - `AzureWebJobsStorage` a biztonságos Storage-fiókhoz tartozó kapcsolódási karakterláncra.
    - `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` a biztonságos Storage-fiókhoz tartozó kapcsolódási karakterláncra.
    - `WEBSITE_CONTENTSHARE` a biztonságos Storage-fiókban létrehozott fájlmegosztás nevével.
    - Hozzon létre egy új beállítást a név és a érték megadásával `WEBSITE_CONTENTOVERVNET` `1` .
    - Ha a Storage-fiók magánhálózati végponti kapcsolatokat használ, ellenőrizze vagy adja hozzá a következő beállításokat
        - `WEBSITE_VNET_ROUTE_ALL` értékkel `1` .
        - `WEBSITE_DNS_SERVER` értéke `168.63.129.16` 
1. Mentse az alkalmazás beállításait.  

A Function alkalmazás újraindul, és mostantól egy biztonságos Storage-fiókhoz fog csatlakozni.

## <a name="use-key-vault-references"></a>Key Vault-referenciák használata

Azure Key Vault hivatkozásokat használhat a Azure Functions alkalmazásban található Azure Key Vault titkok használatára a kód módosítása nélkül. Az Azure Key Vault a titkos kódok központi kezelését biztosító szolgáltatás, a hozzáférési szabályzatok és naplóelőzmények teljes szabályozásával.

Jelenleg [Key Vault hivatkozások](../app-service/app-service-key-vault-references.md) nem fognak működni, ha a kulcstartó biztonságos a szolgáltatási végpontokkal. A Virtual Network Integration használatával a Key vaulthoz való csatlakozáshoz meg kell hívnia Key Vault az alkalmazás kódjában.

## <a name="virtual-network-triggers-non-http"></a>Virtuális hálózati eseményindítók (nem HTTP)

Jelenleg a nem HTTP-trigger függvények a következő két módszer egyikével használhatók a virtuális hálózaton belül:

+ Futtassa a Function alkalmazást egy prémium szintű csomagban, és engedélyezze a virtuális hálózatok triggerének támogatását.
+ A Function alkalmazást App Service csomagban vagy App Service Environment futtathatja.

### <a name="premium-plan-with-virtual-network-triggers"></a>Prémium csomag virtuális hálózati eseményindítókkal

A Prémium csomag futtatásakor a nem HTTP-trigger függvények a virtuális hálózaton belül futó szolgáltatásokhoz is csatlakoztathatók. Ehhez engedélyeznie kell a Virtual Network trigger támogatását a Function alkalmazás számára. A **futtatókörnyezet-méretezés figyelése** beállítás a [Azure Portal](https://portal.azure.com) **konfigurációs**  >  **függvény futtatókörnyezetének beállításai** területen található.

:::image type="content" source="media/functions-networking-options/virtual-network-trigger-toggle.png" alt-text="VNETToggle":::

A virtuális hálózati eseményindítókat a következő Azure CLI-parancs használatával is engedélyezheti:

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.functionsRuntimeScaleMonitoringEnabled=1 --resource-type Microsoft.Web/sites
```

> [!TIP]
> Előfordulhat, hogy a virtuális hálózati eseményindítók engedélyezése hatással lehet az alkalmazás teljesítményére, mivel a App Service-csomag példányainak figyelniük kell az eseményindítókat a skálázási időpontok meghatározásához. Ez a hatás valószínűleg nagyon kicsi.

A virtuális hálózati eseményindítók a functions futtatókörnyezet 2. x vagy újabb verziójában támogatottak. A következő nem HTTP típusú triggerek támogatottak:

| Mellék | Minimális verzió |
|-----------|---------| 
|[Microsoft. Azure. webjobs. Extensions. Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/) | 3.0.10 vagy újabb |
|[Microsoft. Azure. webjobs. Extensions. EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs)| 4.1.0 vagy újabb|
|[Microsoft. Azure. webjobs. Extensions. ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus)| 3.2.0 vagy újabb verzió|
|[Microsoft. Azure. webjobs. Extensions. CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB)| 3.0.5 vagy újabb|
|[Microsoft. Azure. webjobs. Extensions. DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask)| 2.0.0 vagy újabb|

> [!IMPORTANT]
> Ha engedélyezi a virtuális hálózat triggerének támogatását, csak az előző táblázatban látható trigger-típusok dinamikusan méretezhetők az alkalmazással. Továbbra is használhatja azokat az eseményindítókat, amelyek nem szerepelnek a táblázatban, de nem méretezhetők az előre bemelegítő példányok száma után. Az eseményindítók teljes listájáért lásd: [triggerek és kötések](./functions-triggers-bindings.md#supported-bindings).

### <a name="app-service-plan-and-app-service-environment-with-virtual-network-triggers"></a>App Service csomag és App Service Environment virtuális hálózati eseményindítókkal

Ha a Function alkalmazás egy App Service vagy egy App Service Environment fut, akkor a nem HTTP-trigger függvényeket is használhatja. Ahhoz, hogy a függvények helyesen legyenek kiváltva, csatlakoznia kell egy olyan virtuális hálózathoz, amely hozzáfér az trigger-kapcsolatban definiált erőforráshoz.

Tegyük fel például, hogy úgy szeretné konfigurálni a Azure Cosmos DBt, hogy csak egy virtuális hálózatról fogadja a forgalmat. Ebben az esetben telepítenie kell a Function alkalmazást egy olyan App Service tervbe, amely virtuális hálózati integrációt biztosít a virtuális hálózattal. Az integráció lehetővé teszi, hogy az adott Azure Cosmos DB erőforrás aktiválja a függvényt.

## <a name="hybrid-connections"></a>Hibrid kapcsolatok

[Hibrid kapcsolatok](../azure-relay/relay-hybrid-connections-protocol.md) az Azure Relay szolgáltatása, amellyel más hálózatokban is elérheti az alkalmazás erőforrásait. Hozzáférést biztosít az alkalmazástól az alkalmazások végpontja számára. Nem használhatja az alkalmazást az alkalmazás elérésére. A Hibrid kapcsolatok a Windows rendszeren futó függvények számára is elérhető a használati tervben.

A Azure Functionsban használt módon minden hibrid csatlakozás egyetlen TCP-gazdagéphez és porthoz kapcsolódik. Ez azt jelenti, hogy a hibrid kapcsolatok végpontja bármely operációs rendszeren és alkalmazáson is lehet, ha a TCP-figyelési porthoz fér hozzá. A Hibrid kapcsolatok funkció nem tudja, vagy érdekli az alkalmazás protokollja, vagy hogy mi az, amit elér. Csak hálózati hozzáférést biztosít.

További információt a [Hibrid kapcsolatok app Service dokumentációjában](../app-service/app-service-hybrid-connections.md)talál. Ugyanezek a konfigurációs lépések támogatják a Azure Functions.

>[!IMPORTANT]
> Hibrid kapcsolatok csak Windows-csomagok esetén támogatott. A Linux nem támogatott.

## <a name="outbound-ip-restrictions"></a>Kimenő IP-korlátozások

A kimenő IP-korlátozások prémium csomagokban, App Service csomagban vagy App Service Environment érhetők el. Konfigurálhatja a kimenő korlátozásokat arra a virtuális hálózatra, amelyen a App Service Environment telepítve van.

Ha egy prémium szintű csomagban vagy egy virtuális hálózattal rendelkező App Service tervben integrál egy függvényt, az alkalmazás alapértelmezés szerint továbbra is elvégezheti a kimenő hívásokat az internetre. Az Alkalmazásbeállítás hozzáadásával `WEBSITE_VNET_ROUTE_ALL=1` kényszeríti az összes kimenő forgalom küldését a virtuális hálózatba, ahol a hálózati biztonsági csoportra vonatkozó szabályok a forgalom korlátozására használhatók.

## <a name="automation"></a>Automation
A következő API-k lehetővé teszik a regionális virtuális hálózati integrációk programozott kezelését:

+ **Azure CLI**: [`az functionapp vnet-integration`](/cli/azure/functionapp/vnet-integration) egy regionális virtuális hálózati integráció hozzáadásához, listázásához vagy eltávolításához használja a parancsokat.  
+ **ARM-sablonok**: a regionális virtuális hálózatok integrációja Azure Resource Manager sablon használatával engedélyezhető. Teljes példaként tekintse meg [ezt a functions gyorsindító sablont](https://azure.microsoft.com/resources/templates/101-function-premium-vnet-integration/).

## <a name="troubleshooting"></a>Hibaelhárítás

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="next-steps"></a>Következő lépések

További információ a hálózatkezelésről és a Azure Functions:

* [Kövesse a virtuális hálózatok integrálásának első lépéseit ismertető oktatóanyagot](./functions-create-vnet.md)
* [A functions hálózatkezeléssel kapcsolatos gyakori kérdések](./functions-networking-faq.md)
* [További információ a App Service/functions Virtual Network Integration szolgáltatásáról](../app-service/web-sites-integrate-with-vnet.md)
* [További információ az Azure-beli virtuális hálózatokról](../virtual-network/virtual-networks-overview.md)
* [További hálózatkezelési funkciók és vezérlés App Service környezetekkel](../app-service/environment/intro.md)
* [Helyi erőforrásokhoz való kapcsolódás tűzfal módosítása nélkül Hibrid kapcsolatok használatával](../app-service/app-service-hybrid-connections.md)
