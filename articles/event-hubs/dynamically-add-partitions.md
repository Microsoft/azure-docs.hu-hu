---
title: Partíciók dinamikus hozzáadása egy eseményközponthoz a Azure Event Hubs
description: Ez a cikk bemutatja, hogyan adhat dinamikusan partíciókat egy eseményközponthoz a Azure Event Hubs.
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: aeeee1bcefe58b006dac0b6913aaa609cbeefb8c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775119"
---
# <a name="dynamically-add-partitions-to-an-event-hub-apache-kafka-topic-in-azure-event-hubs"></a>Partíciók dinamikus hozzáadása egy eseményközponthoz (Apache Kafka témakör) a Azure Event Hubs
Az Event Hubs üzenetstreamelést biztosít egy particionált felhasználói mintán keresztül, amelyben mindegyik felhasználó az üzenetstream csak egy adott részét, vagyis partícióját olvassa. Ez a minta biztosítja a horizontális skálázhatóságot az eseményfeldolgozáshoz, és egyéb, streamközpontú szolgáltatásokat is nyújt, amelyek az üzenetsorokban vagy témakörökben nem érhetők el. A partíció események egy rendezett sorozata az eseményközpontban. Ahogy újabb események érkeznek, a sorozat végére kerülnek. A partíciókról általánosságban a Partíciók oldalon [található további információ](event-hubs-scalability.md#partitions)

Az eseményközpont létrehozásakor megadhatja a partíciók számát. Bizonyos esetekben előfordulhat, hogy az eseményközpont létrehozása után partíciókat kell hozzáadnia. Ez a cikk azt ismerteti, hogyan adhat dinamikusan partíciókat egy meglévő eseményközponthoz. 

> [!IMPORTANT]
> A partíciók dinamikus kiegészítései  csak dedikált fürtökön Event Hubs érhetők el.

> [!NOTE]
> A Apache Kafka esetében az **eseményközpontok** **kafka-témakörre vannak leképezve.** További leképezések a Azure Event Hubs és a Apache Kafka között: [A Kafka és Event Hubs fogalmi leképezése](event-hubs-for-kafka-ecosystem-overview.md#kafka-and-event-hub-conceptual-mapping)


## <a name="update-the-partition-count"></a>A partíciók számának frissítése
Ez a szakasz bemutatja, hogyan frissítheti egy eseményközpont partíciószámát különböző módokon (PowerShell, parancssori felület stb.).

### <a name="powershell"></a>PowerShell
Egy [eseményközpontban a Set-AzureRmEventHub](/powershell/module/azurerm.eventhub/Set-AzureRmEventHub) PowerShell-paranccsal frissítheti a partíciókat. 

```azurepowershell-interactive
Set-AzureRmEventHub -ResourceGroupName MyResourceGroupName -Namespace MyNamespaceName -Name MyEventHubName -partitionCount 12
```

### <a name="cli"></a>parancssori felület
A [`az eventhubs eventhub update`](/cli/azure/eventhubs/eventhub#az_eventhubs_eventhub_update) CLI-paranccsal frissítheti a partíciókat egy eseményközpontban. 

```azurecli-interactive
az eventhubs eventhub update --resource-group MyResourceGroupName --namespace-name MyNamespaceName --name MyEventHubName --partition-count 12
```

### <a name="resource-manager-template"></a>Resource Manager-sablon
Frissítse a tulajdonság értékét a Resource Manager sablonban, majd az erőforrás frissítéséhez újra üzembe `partitionCount` kell azt használnia. 

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
Használja az `AlterTopics` API-t (például a **kafka-topics CLI-eszközzel)** a partíciók számának növeléséhez. Részletekért [lásd: Kafka-témakörök módosítása.](http://kafka.apache.org/documentation/#basic_ops_modify_topic) 

## <a name="event-hubs-clients"></a>Event Hubs ügyfelek
Nézzük meg, hogyan viselkednek Event Hubs ügyfelek, amikor a partíciók száma frissül egy eseményközpontban. 

Amikor partíciót ad hozzá egy meglévő even hubhoz, az eseményközpont-ügyfél kap egy üzenetet a szolgáltatástól, amely tájékoztatja az ügyfeleket arról, hogy az entitás metaadatai (az entitás az eseményközpont, a metaadatok pedig a partícióinformációk) `MessagingException` módosultak. Az ügyfelek automatikusan újra megnyitják az AMQP-hivatkozásokat, amelyek a módosított metaadat-információkat fogják kihozni. Az ügyfelek ezután a szokásos módon működnek.

### <a name="senderproducer-clients"></a>Küldő/előállító ügyfelek
Event Hubs küldő három lehetőséget kínál:

- **Partíció küldője** – Ebben a forgatókönyvben az ügyfelek közvetlenül egy partícióra küldik az eseményeket. Bár a partíciók azonosíthatók, és az események közvetlenül elküldhetőek nekik, ez a minta nem ajánlott. A partíciók hozzáadása nem befolyásolja ezt a forgatókönyvet. Javasoljuk, hogy indítsa újra az alkalmazásokat, hogy észlelni tudják az újonnan hozzáadott partíciókat. 
- **Partíciókulcs-küldő** – Ebben a forgatókönyvben az ügyfelek egy kulccsal küldik el az eseményeket, így az ehhez a kulcshoz tartozó összes esemény ugyanabban a partícióban lesz. Ebben az esetben a szolgáltatás a kulcsot és a megfelelő partícióhoz tartozó útvonalakat is elosztja. A partíciószám frissítése a kivonatolás módosítása miatt nem megfelelő sorrendben okozhatja a problémákat. Ezért ha fontos a sorrend, a partíciók számának növelése előtt győződjön meg arról, hogy az alkalmazás a meglévő partíciók összes eseményét használja.
- **Ciklikus** időkorrekezéses küldő (alapértelmezett) – Ebben a forgatókönyvben a Event Hubs szolgáltatás ciklikus időkorrekálással generálja az eseményeket a partíciók között, és terheléselosztási algoritmust is használ. Event Hubs szolgáltatás ismeri a partíciószám változásait, és másodpercek alatt elküldi őket az új partícióknak a partíciószám megváltoztatását követő másodpercekben.

### <a name="receiverconsumer-clients"></a>Fogadó/fogyasztói ügyfelek
Event Hubs közvetlen fogadókat és egy egyszerű fogyasztó kódtárat biztosít, az [Event Processor Host (régi SDK)](event-hubs-event-processor-host.md) vagy [event processor (új SDK) nevű kódtárat.](event-processor-balance-partition-load.md)

- **Közvetlen fogadók** – A közvetlen fogadók adott partíciókat figyelnek. A futásidejű működésüket ez nem befolyásolja, ha egy eseményközpont partícióit horizontálisan felskálcionálják. A közvetlen fogadókat használó alkalmazásnak kell felvennie az új partíciókat, és ennek megfelelően kell hozzárendelni a fogadókat.
- **Eseményfeldolgozó gazdagépe** – Ez az ügyfél nem frissíti automatikusan az entitás metaadatait. Így a partíciók számának növekedése nem fog növekedni. Egy eseményfeldolgozó-példány újralétrehozása egy entitás-metaadat-beolvasást okoz, amely pedig új blobokat hoz létre az újonnan hozzáadott partíciókhoz. A már meglévő blobokat ez nem érinti. Ajánlott az összes eseményfeldolgozó-példány újraindítása, hogy az összes példány értesüljön az újonnan hozzáadott partíciókról, és a terheléselosztás megfelelően legyen kezelve a fogyasztók között.

    Ha a .NET SDK[(WindowsAzure.ServiceBus)](https://www.nuget.org/packages/WindowsAzure.ServiceBus/)régi verzióját használja, az eseményfeldolgozó-gazdagép eltávolít egy meglévő ellenőrzőpontot újraindításkor, ha az ellenőrzőpont partíciószáma nem egyezik meg a szolgáltatásból lekért partíciók számával. Ez a viselkedés hatással lehet az alkalmazásra. 

## <a name="apache-kafka-clients"></a>Apache Kafka ügyfelek
Ez a szakasz azt ismerteti Apache Kafka hogyan viselkednek az Azure Event Hubs Kafka-végpontját Azure Event Hubs, amikor egy eseményközpont partíciószáma frissül. 

A Event Hubs protokollt Apache Kafka Kafka-ügyfelek eltérően viselkednek, mint az AMQP protokollt használni. A Kafka-ügyfelek ezredmásodpercek alatt frissítik a `metadata.max.age.ms` metaadataikat. Ezt az értéket az ügyfélkonfigurációkban adhatja meg. A `librdkafka` kódtárak is ugyanazt a konfigurációt használják. A metaadat-frissítések tájékoztatják az ügyfeleket a szolgáltatás változásairól, beleértve a partíciók számának növekedését is. A konfigurációk listájáért lásd a Apache Kafka [konfigurációit Event Hubs.](apache-kafka-configurations.md)

### <a name="senderproducer-clients"></a>Küldő/előállító ügyfelek
Az előállítók mindig megszabják, hogy a kérések küldése az előállított rekordok minden készletének partíciócélját tartalmazza. Így minden előállító particionálása ügyféloldalon történik, a közvetítő metaadatainak előállítói nézetével. Miután hozzáadta az új partíciókat az előállító metaadat-nézethez, elérhetők lesznek az előállítói kérések számára.

### <a name="consumerreceiver-clients"></a>Fogyasztói/fogadó ügyfelek
Amikor egy fogyasztói csoport tagja metaadat-frissítést hajt végre, és felveszi az újonnan létrehozott partíciókat, az a tag újraegyensúlyozást kezdeményez a csoportban. A felhasználói metaadatok ezután az összes csoporttag számára frissülnek, az új partíciókat pedig a kiosztott újraegyensúlyozási vezető rendeli hozzá.

## <a name="recommendations"></a>Javaslatok

- Ha partíciókulcsot használ az előállítói alkalmazásokkal, és a partíciók sorrendje a kulcs kivonatolásától függ, nem ajánlott a partíciók dinamikus hozzáadása. 

    > [!IMPORTANT]
    > Bár a meglévő adatok megőrzik a sorrendet, a partíciók kivonatolása megszakad a partíciószám partíciók összeadása miatti változását követően kivonatolt üzenetek esetén.
- A következő esetekben ajánlott partíciót hozzáadni egy meglévő témakörhez vagy eseményközpont-példányhoz:
    - Ha az események küldésének alapértelmezett módját használja
     - Kafka alapértelmezett particionálási stratégiák, például – Ragadós hozzárendelő stratégia


## <a name="next-steps"></a>Következő lépések
További információ a partíciókról: [Partíciók.](event-hubs-scalability.md#partitions)
