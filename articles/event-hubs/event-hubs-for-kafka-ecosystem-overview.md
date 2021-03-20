---
title: Az Event hub használata Apache Kafka alkalmazásból – Azure Event Hubs | Microsoft Docs
description: Ez a cikk az Azure Event Hubs Apache Kafka támogatásáról nyújt információt.
ms.topic: article
ms.date: 09/25/2020
ms.openlocfilehash: b0f0da76bba68f8a66695700d530e871cbd35e3c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97861340"
---
# <a name="use-azure-event-hubs-from-apache-kafka-applications"></a>Az Azure Event Hubs használata Apache Kafka alkalmazásokból
A Event Hubs egy olyan végpontot biztosít, amely kompatibilis a Apache Kafka® gyártóval és a fogyasztói API-kkal, amelyeket a legtöbb meglévő Apache Kafka ügyfélalkalmazás használhat a saját Apache Kafka-fürt futtatására. A Event Hubs a Apache Kafka gyártói és fogyasztói API-ügyfelét támogatja a 1,0-es és újabb verziókban.

> [!VIDEO https://www.youtube.com/embed/UE1WgB96_fc]

## <a name="what-does-event-hubs-for-kafka-provide"></a>Mire szolgál a Kafka Event Hubs?

A (z) Apache Kafka funkció Event Hubs az Azure-beli Event Hubs, amely a Apache Kafka Server 1,0-es vagy újabb verzióira épülő Apache Kafka ügyfelekkel kompatibilis protokoll, és támogatja a Event Hubs-hoz való olvasást és írást Apache Kafka, amelyek egyenértékűek témakörökkel. 

Gyakran használhatja a Event Hubs Kafka-végpontot az alkalmazásaiban programkód módosítása nélkül, a meglévő Kafka-beállításhoz képest, és csak a konfigurációt módosítja: frissítse a konfigurációban található kapcsolati karakterláncot, hogy az Event hub által közzétett Kafka-végpontra mutasson a Kafka-fürtre való Rámutatás helyett. Ezután elindíthatja a folyamatos átviteli eseményeket a Kafka protokollt használó alkalmazásokból Event Hubsba. 

A Kafka és a Event Hubs fogalmi szempontból nagyon hasonlítanak egymásra: ezek mind a streaming-adattovábbításra készült particionált naplók, így az ügyfél vezérli, hogy a megőrzött napló melyik részét szeretné olvasni. A következő táblázat a Kafka és a Event Hubs közötti fogalmakat térképezi fel.

### <a name="kafka-and-event-hub-conceptual-mapping"></a>A Kafka és az Event hub fogalmi leképezése

| Kafka-koncepció | Event Hubs koncepció|
| --- | --- |
| Fürt | Névtér |
| Témakör | Eseményközpont |
| Partíció | Partíció|
| Fogyasztói csoport | Fogyasztói csoport |
| Eltolás | Eltolás|

### <a name="key-differences-between-apache-kafka-and-event-hubs"></a>Apache Kafka és Event Hubs közötti fő különbségek

A [Apache Kafka](https://kafka.apache.org/) olyan szoftver, amelyet általában telepítenie kell, és működnie kell, Event Hubs egy teljes körűen felügyelt, felhőalapú szolgáltatás. Nincsenek a felügyelni és figyelni kívánt kiszolgálók, lemezek vagy hálózatok, és nincs szükség a közvetítők megfontolni vagy konfigurálására. Létre kell hoznia egy névteret, amely egy teljes tartománynévvel rendelkező végpont, majd az adott névtéren belül hozza létre Event Hubs (témaköröket). 

További információ a Event Hubs és a névterekről: [Event Hubs szolgáltatások](event-hubs-features.md#namespace). Felhőalapú szolgáltatásként Event Hubs a végpontként egyetlen stabil virtuális IP-címet használ, így az ügyfeleknek nem kell tudniuk a fürtön belüli közvetítőket vagy gépeket. Annak ellenére, hogy Event Hubs implementálja ugyanazt a protokollt, ez a különbség azt jelenti, hogy az összes partícióhoz tartozó Kafka-forgalom előre irányítva van ezen az egy végponton keresztül, és nem szükséges tűzfal-hozzáférést biztosítani a fürt összes brókeréhez.   

A Event Hubs méretezését a megvásárolt átviteli egységek száma vezérli, az egyes átviteli egységek pedig másodpercenként 1 MB-nyi vagy 1000 esemény másodpercenkénti számát, a kimenő forgalomban pedig kétszer ezt a kötetet. A Event Hubs automatikusan méretezheti az átviteli egységeket, amikor eléri az átviteli sebességet, ha az [automatikus](event-hubs-auto-inflate.md) feltöltés funkciót használja; Ez a funkció együttműködik a Apache Kafka protokoll támogatásával is.  

### <a name="is-apache-kafka-the-right-solution-for-your-workload"></a>Apache Kafka a megfelelő megoldás a munkaterheléshez?

Az Apache Kafkat használó alkalmazások létrehozásával azt is érdemes megérteni, hogy az Azure Event Hubs egy olyan szolgáltatási flotta részét képezi, amely magában foglalja a [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)és a [Azure Event Gridt](../event-grid/overview.md)is. 

Habár a Apache Kafka kereskedelmi forgalmazásának egyes szolgáltatói azt sugallják, hogy Apache Kafka az összes üzenetkezelési platformra vonatkozó igényt kielégít, a valóság az, hogy a Apache Kafka nem implementálja például a [versengő fogyasztói](/azure/architecture/patterns/competing-consumers) várólista mintáját, a nem támogatja a  [Közzététel-előfizetést](/azure/architecture/patterns/publisher-subscriber) olyan szinten, amely lehetővé teszi, hogy az előfizetők a nem egyszerű eltolástól eltérő, kiszolgáló által kiértékelt szabályok alapján hozzáférjenek a bejövő üzenetekhez, és nincs lehetőség az üzenet által kezdeményezett feladatok életciklusának nyomon követésére, illetve a sidelining hibás üzenetek küldésére egy kézbesítetlen levelekbe.

Ha meg szeretné ismerni a minták közötti különbséget, valamint azt, hogy melyik minta a legmegfelelőbb a szolgáltatáshoz, tekintse át az Azure-útmutató [aszinkron üzenetkezelési lehetőségeit](/azure/architecture/guide/technology-choices/messaging) . Apache Kafka felhasználóként az is előfordulhat, hogy a Kafka-vel eddig felismert kommunikációs útvonalakat sokkal kevesebb alapvető összetettséggel és még nagyobb teljesítményű képességekkel lehet megvalósítani, Event Grid vagy Service Bus használatával. 

Ha olyan Apache Kafka-funkciókra van szüksége, amelyek nem érhetők el az Event Hubson Apache Kafka interfészhez, vagy ha a megvalósítási minta meghaladja a [Event Hubs kvótákat](event-hubs-quotas.md), akkor futtathatja a [natív Apache Kafka-fürtöt is az Azure HDInsight-ben](../hdinsight/kafka/apache-kafka-introduction.md).  

## <a name="security-and-authentication"></a>Biztonság és hitelesítés
Minden alkalommal, amikor eseményeket tesz közzé vagy használ fel egy Event Hubs a Kafka számára, az ügyfél megpróbál hozzáférni a Event Hubs erőforrásokhoz. Biztosítani szeretné, hogy az erőforrások elérhetők legyenek egy jogosult entitás használatával. Ha Apache Kafka protokollt használ az ügyfelekkel, beállíthatja a konfigurációt a hitelesítéshez és a titkosításhoz a SASL mechanizmusok használatával. A Event Hubs for Kafka használatához TLS-titkosításra van szükség (mivel az Event Hubs TLS-alapú adatforgalom titkosítva van). Ezt megteheti a konfigurációs fájlban lévő SASL_SSL beállítás megadásával. 

Az Azure Event Hubs több lehetőséget kínál a biztonságos erőforrásokhoz való hozzáférés engedélyezésére. 

- OAuth 2.0
- Közös hozzáférésű jogosultságkód (SAS)

#### <a name="oauth-20"></a>OAuth 2.0
A Event Hubs a Azure Active Directory (Azure AD) szolgáltatással integrálódik, amely egy **OAuth 2,0** -kompatibilis központi engedélyezési kiszolgálót biztosít. Az Azure AD-vel az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) segítségével részletesen megérthető engedélyeket biztosíthat az ügyfél-identitásoknak. Ezt a funkciót a Kafka-ügyfelekkel a mechanizmus protokoll-és **OAUTHBEARER** **SASL_SSL** megadásával veheti igénybe. Az Azure-szerepkörökkel és a hozzáférés-kezelés szintjeivel kapcsolatos részletekért lásd: [hozzáférés engedélyezése az Azure ad-vel](authorize-access-azure-active-directory.md).

```properties
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=OAUTHBEARER
sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
```

#### <a name="shared-access-signature-sas"></a>Közös hozzáférésű jogosultságkód (SAS)
A Event Hubs a **közös hozzáférésű aláírásokat (SAS)** is biztosítja a Event Hubs számára a Kafka-erőforrásokhoz való delegált hozzáféréshez. Az OAuth 2,0 token-alapú mechanizmus használatával történő hozzáférés engedélyezése a biztonsági és a könnyű használatot lehetővé teszi az SAS használatával. A beépített szerepkörök kihasználhatják az ACL-alapú hitelesítés szükségességét is, amelyet a felhasználónak kell karbantartani és felügyelni. Ezt a funkciót a Kafka-ügyfelekkel úgy használhatja, hogy megadta **SASL_SSL** a protokollhoz, és **egyszerű** a mechanizmus számára. 

```properties
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

> [!IMPORTANT]
> Cserélje le a helyére `{YOUR.EVENTHUBS.CONNECTION.STRING}` a Event Hubs névtérhez tartozó kapcsolatok karakterláncát. A kapcsolatok karakterláncának beolvasásával kapcsolatos utasításokért lásd: [Event Hubs-kapcsolatok karakterláncának beolvasása](event-hubs-get-connection-string.md). Íme egy példa a konfigurációra: `sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";`

> [!NOTE]
> A Kafka-ügyfelek SAS-hitelesítésének használatakor a rendszer a létrehozott kapcsolatokat nem bontja le, amikor a SAS-kulcsot újragenerálták. 


#### <a name="samples"></a>Példák 
Az Event hub létrehozásával **és a sas** vagy OAuth használatával való elérésével kapcsolatos részletes utasításokért lásd: gyors útmutató [: adatstream Event Hubs a Kafka protokoll használatával](event-hubs-quickstart-kafka-enabled-event-hubs.md).

A OAuth és az Event Hubs for Kafka használatát bemutató **példákért** lásd: [minták a githubon](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth).

## <a name="other-event-hubs-features"></a>Egyéb Event Hubs funkciók 

A Apache Kafka funkció Event Hubs az Azure Event Hubsban egyidejűleg elérhető három protokoll egyike, amely kiegészíti a HTTP-t és a AMQP. Ezeket a protokollokat bármilyen módon megírhatja, és beolvashatja bármely másikkal, így a jelenlegi Apache Kafka-gyártók továbbra is végezhetik a közzétételt a Apache Kafkaon keresztül, de az olvasónak a Event Hubs AMQP felületével (például Azure Stream Analytics vagy Azure Functions) való natív integráció előnyeit is kihasználhatja. Az Azure-Event Hubs az AMQP-alapú útválasztási hálózatokra is könnyedén integrálható célként, és az adatApache Kafka-integráción keresztül is beolvashatja azokat.  

Emellett Event Hubs olyan funkciókat is, mint például a [Capture](event-hubs-capture-overview.md), amely rendkívül költséghatékony, hosszú távú archiválást tesz lehetővé az Azure-Blob Storage és Azure Data Lake Storageon keresztül, és a [geo-vész-helyreállítás](event-hubs-geo-dr.md) a Kafka-szolgáltatás Event Hubs is működik.

## <a name="apache-kafka-feature-differences"></a>Apache Kafka szolgáltatásbeli különbségek 

A Apache Kafka Event Hubs célja, hogy hozzáférést biztosítson az Azure Event hub képességeihez a Apache Kafka API-ba zárolt alkalmazásokhoz, és máskülönben egy Apache Kafka-fürtnek kell lennie. 

A [fentiekben](#is-apache-kafka-the-right-solution-for-your-workload)leírtaknak megfelelően az Azure Messaging-flotta bőséges és robusztus lefedettséget biztosít számos üzenetkezelési forgatókönyv esetén, és bár a következő funkciók jelenleg nem támogatottak a Apache Kafka API támogatásának Event Hubs "támogatása mellett, akkor a kívánt képesség és a kívánt funkció elérhetővé tételével.

### <a name="transactions"></a>Tranzakciók

A [Azure Service Bus](../service-bus-messaging/service-bus-transactions.md) robusztus tranzakciós támogatással rendelkezik, amely lehetővé teszi az üzenetek és munkamenetek fogadását és rendezését, miközben a tranzakció konzisztencia-védelme alatt több célcsoportnak küldi el az üzenetek feldolgozásának eredményét. A szolgáltatáskészlet nem csak az egyes üzenetek pontosan egyszeri feldolgozását teszi lehetővé az egyes sorozatokban, de elkerülhető annak a kockázata is, hogy egy másik fogyasztó véletlenül újra feldolgozza ugyanezeket az üzeneteket, mivel ez a helyzet Apache Kafka. Service Bus a tranzakciós üzenetek számítási feladataihoz ajánlott szolgáltatás.

### <a name="compression"></a>Tömörítés

A Apache Kafka ügyféloldali [tömörítési](https://cwiki.apache.org/confluence/display/KAFKA/Compression) funkciója több üzenet egy kötegét tömöríti egyetlen üzenetbe a gyártó oldalán, és kibontja a köteget a fogyasztói oldalon. A Apache Kafka Broker speciális üzenetként kezeli a köteget.

Ez a funkció alapvetően az Azure Event Hubs multi-Protocol modellje, amely lehetővé teszi, hogy az üzenetek még a kötegekben is elküldött üzeneteket a közvetítőtől és bármely protokollon keresztül egyénileg lehessen lekérni. 

Az Event hub-események hasznos adatai egy bájtos adatfolyamok, és a tartalom tömöríthető az Ön által választott algoritmussal. Az Apache Avro kódolási formátuma natív módon támogatja a tömörítést.

### <a name="log-compaction"></a>Naplózási tömörítés

A Apache Kafka log tömörítés egy olyan szolgáltatás, amely lehetővé teszi az egyes kulcsok összes rekordjának kivonását egy partícióból, ami gyakorlatilag egy Apache Kafka témakört egy olyan kulcs-érték tárolóba kapcsol, amelyben az utolsó hozzáadott érték felülbírálja az előzőt. Ezt a funkciót jelenleg nem az Azure Event Hubs implementálja. Az adatbázis-szolgáltatások, például a [Azure Cosmos db](../cosmos-db/introduction.md)sokkal jobb támogatást nyújt a kulcs-érték tárolási mintához, akár gyakori frissítésekkel is. További részletekért tekintse meg a Event Hubs összevonási útmutatójának [log vetületét](event-hubs-federation-overview.md#log-projections) ismertető témakört. 

### <a name="kafka-streams"></a>Kafka streamek

A Kafka Streams egy olyan ügyféloldali kódtár a stream analyticshez, amely a Apache Kafka nyílt forráskódú projekt részét képezi, de az Apache Kafka Event stream brokertől eltér. 

Az Azure Event Hubs ügyfeleinek leggyakoribb oka a Kafka-streamek támogatása, mivel érdeklik a "ksqlDB" termék. a "ksqlDB" olyan szabadalmaztatott megosztott forrású projekt, amely [olyan licenccel](https://github.com/confluentinc/ksql/blob/master/LICENSE) rendelkezik, amelynek nincs olyan gyártója, amely a szolgáltatásként nyújtott szoftvereket, platform-szolgáltatásokat, infrastruktúra-szolgáltatást vagy más hasonló online szolgáltatások, amelyek az Fluent termékekkel vagy szolgáltatásokkal versenyeznek, "ksqlDB" támogatást használhatnak vagy kínálnak. Ha ksqlDB-t használ, akkor vagy önállóan kell futtatnia a Kafka-t, vagy az Fluent Felhőbeli ajánlatokat kell használnia. A licencelési feltételek olyan Azure-ügyfelekre is hatással lehetnek, akik a licenc által kizárt célra nyújtanak szolgáltatásokat.

Az önálló és ksqlDB nélkül a Kafka-adatfolyamok kevesebb képességgel rendelkeznek, mint számos alternatív keretrendszer és szolgáltatás, amelyek közül a legtöbb beépített SQL-interfészt tartalmaz, amelyek mindegyike az Azure Event Hubs-nal integrálható ma:

- [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md)
- [Azure szinapszis Analytics (Event Hubs Capture használatával)](../event-grid/event-grid-event-hubs-integration.md)
- [Azure Databricks](/azure/databricks/scenarios/databricks-stream-from-eventhubs)
- [Apache Samza](https://samza.apache.org/learn/documentation/latest/connectors/eventhubs)
- [Apache Storm](event-hubs-storm-getstarted-receive.md)
- [Apache Spark](event-hubs-kafka-spark-tutorial.md)
- [Apache flink](event-hubs-kafka-flink-tutorial.md)
- [Befolyó streamek](event-hubs-kafka-akka-streams-tutorial.md)

A felsorolt szolgáltatások és keretrendszerek általában a különböző forrásokból származó, az adaptereken keresztüli adatstreameket és hivatkozási adatok beolvasását teszik lehetővé. A Kafka-adatfolyamok csak a Apache Kafkaból szerzik be az adatait, és az elemzési projektek ezért Apache Kafkaba vannak zárva. Más forrásokból származó adatok használatához először importálnia kell az Apache Kafkaba a Kafka csatlakozási keretrendszerével.

Ha az Azure-beli Kafka Streams keretrendszert kell használnia, [Apache Kafka a HDInsight-on](../hdinsight/kafka/apache-kafka-introduction.md) keresztül biztosítjuk Önnek ezt a lehetőséget. A Apache Kafka on HDInsight teljes körűen szabályozhatja Apache Kafka összes konfigurációs aspektusát, miközben az Azure platform különböző szempontjaival teljes mértékben integrálva van, a hiba/frissítés tartományból a hálózat elkülönítésére az integráció figyelése érdekében. 

## <a name="next-steps"></a>Következő lépések
Ez a cikk a Kafka-Event Hubs bevezetését ismertette. További információ: [Apache Kafka fejlesztői útmutató az Azure Event Hubshoz](apache-kafka-developer-guide.md).