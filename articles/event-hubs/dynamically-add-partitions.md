---
title: Partíciók dinamikus hozzáadása az Azure-beli Event hub-Event Hubs
description: Ebből a cikkből megtudhatja, hogyan adhat hozzá dinamikusan partíciókat az Azure Event Hubs egy Event hub-hoz.
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: e6efdc7bab309f825032555c97f1e1128f5addd6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98625265"
---
# <a name="dynamically-add-partitions-to-an-event-hub-apache-kafka-topic-in-azure-event-hubs"></a>Partíciók dinamikus hozzáadása egy Event hub-hoz (Apache Kafka témakörhöz) az Azure-ban Event Hubs
Az Event Hubs üzenetstreamelést biztosít egy particionált felhasználói mintán keresztül, amelyben mindegyik felhasználó az üzenetstream csak egy adott részét, vagyis partícióját olvassa. Ez a minta biztosítja a horizontális skálázhatóságot az eseményfeldolgozáshoz, és egyéb, streamközpontú szolgáltatásokat is nyújt, amelyek az üzenetsorokban vagy témakörökben nem érhetők el. A partíció események egy rendezett sorozata az eseményközpontban. Ahogy újabb események érkeznek, a rendszer hozzáadja a sorozatot a végéhez. A partíciókkal kapcsolatos további információkért lásd: [partíciók](event-hubs-scalability.md#partitions)

Az Event hub létrehozásakor megadhatja a partíciók számát. Bizonyos esetekben előfordulhat, hogy az Event hub létrehozása után partíciót kell hozzáadnia. Ez a cikk azt ismerteti, hogyan lehet dinamikusan hozzáadni a partíciókat egy meglévő Event hubhoz. 

> [!IMPORTANT]
> A partíciók dinamikus hozzáadása csak **dedikált** Event Hubs fürtökön érhető el.

> [!NOTE]
> Apache Kafka ügyfelek esetében az **Event hub** egy **Kafka-témakörre** mutat. Az Azure Event Hubs és Apache Kafka közötti további leképezéseket lásd: [Kafka-és Event Hubs fogalmi leképezés](event-hubs-for-kafka-ecosystem-overview.md#kafka-and-event-hub-conceptual-mapping)


## <a name="update-the-partition-count"></a>A partíciók számának frissítése
Ebből a szakaszból megtudhatja, hogyan frissítheti az Event hub partícióinak számát különböző módokon (PowerShell, CLI stb.).

### <a name="powershell"></a>PowerShell
Az Event hub partícióinak frissítéséhez használja a [set-AzureRmEventHub PowerShell-](/powershell/module/azurerm.eventhub/Set-AzureRmEventHub) parancsot. 

```azurepowershell-interactive
Set-AzureRmEventHub -ResourceGroupName MyResourceGroupName -Namespace MyNamespaceName -Name MyEventHubName -partitionCount 12
```

### <a name="cli"></a>parancssori felület
Az [`az eventhubs eventhub update`](/cli/azure/eventhubs/eventhub#az-eventhubs-eventhub-update) Event hub partícióinak frissítéséhez használja a CLI-parancsot. 

```azurecli-interactive
az eventhubs eventhub update --resource-group MyResourceGroupName --namespace-name MyNamespaceName --name MyEventHubName --partition-count 12
```

### <a name="resource-manager-template"></a>Resource Manager-sablon
Frissítse a tulajdonság értékét `partitionCount` a Resource Manager-sablonban, és telepítse újra a sablont az erőforrás frissítéséhez. 

```json
    {
        "apiVersion": "2017-04-01",
        "type": "Microsoft.EventHub/namespaces/eventhubs",
        "name": "[concat(parameters('namespaceName'), '/', parameters('eventHubName'))]",
        "location": "[parameters('location')]",
        "dependsOn": [
            "[resourceId('Microsoft.EventHub/namespaces', parameters('namespaceName'))]"
        ],
        "properties": {
            "messageRetentionInDays": 7,
            "partitionCount": 12
        }
    }
```

### <a name="apache-kafka"></a>Apache Kafka
A `AlterTopics` partíciók számának növeléséhez használja az API-t (például a **Kafka-témák CLI-** eszközön keresztül). Részletekért lásd: [Kafka-témakörök módosítása](http://kafka.apache.org/documentation/#basic_ops_modify_topic). 

## <a name="event-hubs-clients"></a>Ügyfelek Event Hubs
Nézzük meg, hogyan viselkedjenek Event Hubs-ügyfelek, amikor a partíciók száma frissül az Event hub-ban. 

Ha meglévő páros hubhoz ad hozzá partíciót, az Event hub-ügyfél megkapja a szolgáltatást, `MessagingException` amely tájékoztatja az entitások metaadatait (az entitás az Event hub és a metaadatok a partíciós adatok). Az ügyfelek automatikusan újra megnyitják a AMQP-hivatkozásokat, amelyek ezután felveszik a módosított metaadat-információkat. Az ügyfelek ezután rendesen működnek.

### <a name="senderproducer-clients"></a>Küldő/gyártói ügyfelek
A Event Hubs három feladói lehetőséget biztosít:

- **Partíció küldője** – ebben az esetben az ügyfelek közvetlenül egy partícióba küldik az eseményeket. Bár a partíciók azonosíthatók, és az események közvetlenül is elküldhetők számukra, nem ajánlott ezt a mintát. A partíciók hozzáadása nem befolyásolja ezt a forgatókönyvet. Javasoljuk, hogy indítsa újra az alkalmazásokat, hogy azok képesek legyenek az újonnan hozzáadott partíciók észlelésére. 
- **Partíciós kulcs küldője** – ebben a forgatókönyvben az ügyfelek az eseményeket egy kulccsal küldi el, hogy az adott kulcshoz tartozó összes esemény ugyanabban a partícióban legyen. Ebben az esetben a szolgáltatás a kulcsot és az útvonalakat a megfelelő partícióra írja. A partíciók számának frissítése a kivonatoló változás miatt nem okozhatja a megrendelési problémákat. Tehát, ha érdekel a rendezés, győződjön meg arról, hogy az alkalmazás a partíciók számának megnövekedése előtt felhasználja a meglévő partíciók összes eseményét.
- **Ciklikus multiplexelés (alapértelmezett)** – ebben a forgatókönyvben a Event Hubsi szolgáltatás kerekíti az eseményeket a partíciók között, és terheléselosztási algoritmust is használ. Event Hubs szolgáltatás tisztában van a partíciók számának változásaival, és a partíciók számának megváltoztatását másodpercek alatt elküldi az új partícióknak.

### <a name="receiverconsumer-clients"></a>Fogadó/fogyasztói ügyfelek
A Event Hubs közvetlen fogadókat és egyszerű fogyasztói kódtárat biztosít az [Event Processor Host (régi SDK)](event-hubs-event-processor-host.md)  vagy az [Event Processor (új SDK)](event-processor-balance-partition-load.md)néven.

- **Közvetlen fogadók** – a közvetlen fogadók egy adott partícióra figyeljenek. A futásidejű viselkedés nem befolyásolja a partíciók kiskálázását az Event hub esetében. A közvetlen fogadókat használó alkalmazásnak ügyelnie kell az új partíciók feladására, és ennek megfelelően kell hozzárendelni a fogadókat.
- **Event Processor Host** – ez az ügyfél nem frissíti automatikusan az entitás metaadatait. Ezért nem kell a partíciók számának növekedését felvennie. Egy esemény-feldolgozó példány újbóli létrehozásakor az entitás metaadatainak beolvasása történik, ami viszont új blobokat hoz létre az újonnan hozzáadott partíciókhoz. A meglévő Blobok nem lesznek érintettek. Az összes Event Processor-példány újraindítása ajánlott annak biztosítására, hogy az összes példány tisztában legyen az újonnan hozzáadott partíciókkal, és a terheléselosztás megfelelően legyen kezelve a felhasználók körében.

    Ha a .NET SDK ([WindowsAzure. ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/)) régi verzióját használja, akkor az eseménykezelő egy meglévő ellenőrzőpontot távolít el az újraindításkor, ha az ellenőrzőpontban lévő partíciók száma nem egyezik meg a szolgáltatásból beolvasott partíciók számával. Ez a viselkedés hatással lehet az alkalmazásra. 

## <a name="apache-kafka-clients"></a>Ügyfelek Apache Kafka
Ez a szakasz azt ismerteti, hogy az Event Hubs Azure-beli Kafka-végpontot használó Apache Kafka ügyfelek hogyan viselkedjenek a partíciók számának frissítésekor az Event hub esetében. 

A Apache Kafka protokollal Event Hubst használó Kafka-ügyfelek az AMQP protokollt használó Event hub-ügyfelektől eltérően viselkednek. A Kafka-ügyfelek minden ezredmásodperc után frissítik a metaadatokat `metadata.max.age.ms` . Ezt az értéket kell megadnia az ügyfél konfigurációjában. A `librdkafka` kódtárak is ugyanazt a konfigurációt használják. A metaadatok frissítései tájékoztatják az ügyfeleket a szolgáltatás változásairól, beleértve a partíciók számát. A konfigurációk listáját itt tekintheti meg: [Apache Kafka konfigurációk Event Hubshoz](apache-kafka-configurations.md).

### <a name="senderproducer-clients"></a>Küldő/gyártói ügyfelek
A termelők mindig azt diktálják, hogy a küldési kérelmek tartalmazzák a létrehozott rekordok partíciójának célhelyét. Így az összes termék particionálása az ügyfélen történik, és a gyártó a bróker metaadatait tekinti át. Miután hozzáadta az új partíciókat a gyártó metaadat-nézetéhez, elérhetővé válnak a termelői kérelmek esetében.

### <a name="consumerreceiver-clients"></a>Fogyasztói/fogadó ügyfelek
Amikor egy fogyasztói csoport tagja elvégez egy metaadat-frissítést, és felveszi az újonnan létrehozott partíciókat, a tag egy csoport újraelosztását kezdeményezi. A fogyasztói metaadatok ezután frissülnek az összes csoporttagok számára, és az új partíciókat a kiosztott egyensúlyi vezető fogja hozzárendelni.

## <a name="recommendations"></a>Javaslatok

- Ha a gyártó alkalmazásaihoz használ partíciós kulcsot, és a kulcs kivonatolása függ a partíciók sorrendjének biztosításához, akkor a partíciók dinamikus hozzáadása nem ajánlott. 

    > [!IMPORTANT]
    > Míg a meglévő adatok megőrzik a sorrendet, a partíciók kivonatolása megszakad, ha a partíciók hozzáadását követően a partíciók száma megváltozik.
- A következő esetekben javasolt a partíció hozzáadása meglévő témakörhöz vagy Event hub-példányhoz:
    - Ha az alapértelmezett módszert használja az események küldéséhez
     - Kafka alapértelmezett particionálási stratégiák, példa – Sticky Assigner stratégia


## <a name="next-steps"></a>Következő lépések
További információ a partíciókon: [partíciók](event-hubs-scalability.md#partitions).

