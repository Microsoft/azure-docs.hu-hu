---
title: Mi az Azure Event Grid?
description: Eseményazonosító küldése a forrástól a kezelőknek Azure Event Grid használatával. Hozzon létre eseményvezérelt alkalmazásokat, és integrálja az Azure-szolgáltatásokat.
ms.topic: overview
ms.date: 01/28/2021
ms.openlocfilehash: e53665c88c3860d37b3512c6498ab626b02a6400
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99055134"
---
# <a name="what-is-azure-event-grid"></a>Mi az Azure Event Grid?

Az Azure Event Griddel könnyen létrehozhat eseményalapú architektúrával rendelkező alkalmazásokat. Először válassza ki azt az Azure-erőforrást, amelyhez elő szeretne fizetni, majd adja meg az eseménykezelőt vagy a webhook-végpontot az esemény elküldéséhez. Az Event Grid beépített támogatást nyújt az Azure-szolgáltatásokból, például a tárolóblobokból és erőforráscsoportokból érkező eseményekhez. Az Event Grid egyéni témakörök használatával a saját események használatát is támogatja. 

Szűrők használatával a meghatározott eseményeket különböző végpontokra, csoportos küldéssel több végpontra is irányíthatja, és ellenőrizheti az események megbízható kézbesítését.

Azure Event Grid üzembe helyezése a rendelkezésre állás maximalizálása érdekében a több tartalék tartomány minden régiójában, valamint a rendelkezésre állási zónák között (az azokat támogató régiókban) való natív elosztással. A Event Grid által támogatott régiók listáját itt tekintheti meg: [régiókban elérhető termékek](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all).

Ez a cikk az Azure Event Gridről nyújt áttekintést. Az Event Grid használatának első lépései: [Egyéni események létrehozása és átirányítása az Azure Event Griddel](custom-event-quickstart.md). 

:::image type="content" source="./media/overview/functional-model.png" alt-text="A források és a kezelők Event Grid modellje" lightbox="./media/overview/functional-model-big.png":::

Ez a rendszerkép azt mutatja be, hogy Event Grid hogyan csatlakozik a forrásokhoz és a kezelőhöz, és nem a támogatott integrációk átfogó listája.

## <a name="event-sources"></a>Eseményforrások

Jelenleg az alábbi Azure-szolgáltatások támogatják az események küldését a Event Gridba. A listában szereplő forrásokkal kapcsolatos további információkért válassza ki a hivatkozást.

- [Azure App Configuration](event-schema-app-configuration.md)
- [Azure Blob Storage](event-schema-blob-storage.md)
- [Azure Communication Services](event-schema-communication-services.md) 
- [Azure Container Registry](event-schema-container-registry.md)
- [Azure Event Hubs](event-schema-event-hubs.md)
- [Azure IoT Hub](event-schema-iot-hub.md)
- [Azure Key Vault](event-schema-key-vault.md)
- [Azure Machine Learning](event-schema-machine-learning.md)
- [Azure Maps](event-schema-azure-maps.md)
- [Azure Media Services](event-schema-media-services.md)
- [Azure-erőforráscsoportok](event-schema-resource-groups.md)
- [Azure Service Bus](event-schema-service-bus.md)
- [Azure SignalR](event-schema-azure-signalr.md)
- [Azure-előfizetések](event-schema-subscriptions.md)
- [Azure Cache for Redis](event-schema-azure-cache.md)

## <a name="event-handlers"></a>Eseménykezelők

Az egyes kezelők képességeire és a kapcsolódó cikkekre vonatkozó részletekért tekintse meg az [eseménykezelőket](event-handlers.md). Jelenleg az alábbi Azure-szolgáltatások támogatják az Event Gridből származó események kezelését: 

* [Azure Automation](handler-webhooks.md#azure-automation)
* [Azure Functions](handler-functions.md)
* [Event Hubs](handler-event-hubs.md)
* [Továbbító Hibrid kapcsolatok](handler-relay-hybrid-connections.md)
* [Logic Apps](handler-webhooks.md#logic-apps)
* [Power automatizálás (korábbi nevén Microsoft Flow)](https://preview.flow.microsoft.com/connectors/shared_azureeventgrid/azure-event-grid/)
* [Szolgáltatásbusz](handler-service-bus.md)
* [Queue Storage](handler-storage-queues.md)
* [Webhookok](handler-webhooks.md)

## <a name="concepts"></a>Alapelvek

Az Azure Event Grid öt fontos alapfogalma:

* **Események** – Mi történt.
* **Eseményforrások** – Ahol az esemény történt.
* **Témakörök** – A végpont, amelyre a közzétevők az eseményeket küldik.
* **Esemény-előfizetések** – az események továbbítására szolgáló végpont vagy beépített mechanizmus, esetenként több kezelőhöz. Az előfizetéseket a kezelők is használják a bejövő események intelligens szűrésére.
* **Eseménykezelők** – Az eseményre reagáló alkalmazás vagy szolgáltatás.

További információ ezekről a fogalmakról: [Az Azure Event Grid alapfogalmai](concepts.md).

## <a name="capabilities"></a>Képességek

Az Azure Event Grid néhány főbb jellemzője:

* **Egyszerűség** – Egy kattintással irányíthatja az eseményeket az Azure-erőforrásából bármely eseménykezelőre vagy végpontra.
* **Speciális szűrés** – az eseménytípus vagy az esemény-közzétételi útvonal szűrése annak biztosításához, hogy az eseménykezelők csak a releváns eseményeket kapják meg.
* **Kipróbálhatja** , hogy több végpontot is felfizessen ugyanarra az eseményre, hogy a lehető legtöbb helyre küldje el az esemény másolatait.
* **Megbízhatóság** – 24 órás újrapróbálkozás exponenciális leállítási az események kézbesítésének biztosításához.
* **Eseményenkénti fizetés** – Csak az Event Grid használatának mennyisége alapján kell fizetnie.
* **Nagy átviteli sebesség** – nagy mennyiségű számítási feladat létrehozása Event Gridon.
* **Beépített események** – Az erőforrások által meghatározott, beépített eseményekkel gyorsan munkához láthat.
* **Egyéni események** – az Event Grid segítségével átirányíthatja, szűrheti és megbízhatóan kézbesítheti az egyéni eseményeket az alkalmazásban.

Az Event Grid, az Event Hubs és a Service Bus összehasonlítása: [Az üzenetkézbesítő Azure-szolgáltatás kiválasztása](compare-messaging-services.md).

## <a name="what-can-i-do-with-event-grid"></a>Mire használhatom az Event Gridet?

Azure Event Grid számos olyan funkciót kínál, amelyek jelentősen javítják a kiszolgáló nélküli, az Ops Automation és az [integrációs](https://azure.com/integration) munkákat: 

### <a name="serverless-application-architectures"></a>Kiszolgáló nélküli alkalmazásarchitektúrák

![Kiszolgáló nélküli alkalmazás architektúrája](./media/overview/serverless_web_app.png)

Az Event Grid összeköti az adatforrásokat az eseménykezelőkkel. Például a Event Grid használatával aktiválhat egy kiszolgáló nélküli függvényt, amely elemzi a lemezképeket a blob Storage-tárolóhoz való hozzáadáskor. 

### <a name="ops-automation"></a>Műveletek automatizálása

![Műveletek automatizálása](./media/overview/Ops_automation.png)

Az Event Griddel felgyorsíthatja az automatizálást és egyszerűsítheti a szabályzatok kikényszerítését. A Event Grid használatával például értesítheti Azure Automation, ha létrejön egy virtuális gép vagy adatbázis az Azure SQL-ben. Az események használatával automatikusan ellenőrizhető, hogy a szolgáltatás konfigurációja megfelelő-e, a metaadatokat az Operations eszközökre, a virtuális gépek címkézésére vagy a fájl munkaelemeire tegye.

### <a name="application-integration"></a>Alkalmazásintegráció

![Alkalmazás-integráció az Azure-nal](./media/overview/app_integration.png)

Az Event Grid más szolgáltatásokkal kapcsolja össze alkalmazását. Létrehozhat például egy egyéni témakört, hogy az alkalmazása eseményadatait az Event Gridbe küldje, és kihasználja az általa kínált megbízható kézbesítést, fejlett átirányítást és a közvetlen Azure-integrációt. Vagy használhatja a Event Gridt is Logic Apps az adatfeldolgozást bárhol, kód írása nélkül. 

## <a name="how-much-does-event-grid-cost"></a>Mennyibe kerül az Event Grid?

Az Azure Event Grid árképzése eseményenkénti fizetésen alapul, tehát a fizetés a használat alapján történik. A havi első 100 000 művelet ingyenes. A műveletek a következőképpen vannak meghatározva: események bejövő forgalma, előfizetési kézbesítési kísérletek, kezelési hívások és tárgyutótag szerinti szűrés. A részleteket lásd az [árképzést ismertető oldalon](https://azure.microsoft.com/pricing/details/event-grid/).

## <a name="next-steps"></a>Következő lépések

* [Storage Blob-események útválasztása](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)  
  Az Event Grid használatával válaszolhat a Storage Blob-eseményekre.
* [Egyéni események: létrehozás és előfizetés](custom-event-quickstart.md)  
  Lásson azonnal munkához, és küldje el saját egyéni eseményeit egy végpontra az Azure Event Grid rövid útmutatója alapján.
* [A Logic Apps használata eseménykezelőként](monitor-virtual-machine-changes-event-grid-logic-app.md)  
  Ez az oktatóanyag bemutatja egy olyan alkalmazás létrehozását a Logic Appsszel, amely az Event Grid által leküldött eseményekre reagál.
* [Big Data típusú adatok streamelése adattárházba](event-grid-event-hubs-integration.md)  
  Egy oktatóanyag, amely az adatok Event Hubsból az Azure szinapszis Analyticsbe való továbbításához Azure Functions használ.
* [Event Grid REST API-referencia](/rest/api/eventgrid)  
  Az esemény-előfizetések, az Útválasztás és a szűrés kezelésére szolgáló hivatkozási tartalmat tartalmaz.