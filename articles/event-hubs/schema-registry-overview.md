---
title: Azure Schema Registry az Event Hubsban (előzetes verzió)
description: Ez a cikk áttekintést nyújt az Azure Event Hubs (előzetes verzió) által támogatott séma-beállításjegyzékről.
ms.topic: overview
ms.date: 09/22/2020
ms.custom: references_regions
ms.openlocfilehash: a876651b76aa259754623854b8fc4a7c6c8a939e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92330495"
---
# <a name="azure-schema-registry-in-event-hubs-preview"></a>Azure Schema Registry az Event Hubsban (előzetes verzió)
Számos esemény-adatfolyam és üzenetküldési forgatókönyv esetén az esemény vagy az üzenet tartalma strukturált adatokat tartalmaz, amelyek szerializálása vagy deszerializálása séma-vezérelt formátum, például Apache Avro használatával történik. A küldők és a fogadók is ellenőrizhetik az adatok integritását egy séma-dokumentummal, mint a JSON-sémával. A séma által vezérelt formátumok esetében a séma elérhetővé tétele az üzenet fogyasztói számára előfeltétel, hogy a fogyasztó képes legyen deszerializálni az adatmennyiséget. 

Az **Azure Schema Registry** a Event Hubs egyik funkciója, amely egy központi tárházat biztosít a séma dokumentumaihoz az eseményvezérelt és üzenetküldési központú alkalmazásokhoz. Rugalmasságot biztosít a termelő és a fogyasztói alkalmazások számára az adatcseréhez anélkül, hogy a sémákat a közöttük lévő séma kezelése és megosztása, valamint a különböző díjszabások alapján kellene fejlődni. A séma beállításjegyzéke egyszerű irányítási keretrendszert is biztosít az újrafelhasználható sémák számára, és meghatározza a sémák közötti kapcsolatot egy csoportosítási összeállítás (Schema groups) segítségével.

> [!NOTE]
> - A **séma-beállításjegyzék** szolgáltatás jelenleg **előzetes** verzióban érhető el, és éles számítási feladatokhoz nem ajánlott.
> - A szolgáltatás csak **standard** és **dedikált** szinten érhető el, nem az alapszintű **csomagokban** .

A sémák által vezérelt szerializálási keretrendszerek, például az Apache Avro, a externalizing-szerializálási metaadatok a megosztott sémákban is segíthetnek, ha jelentősen csökkentik az egyes adatkészletekben található, és a címkével ellátott, címkézett formátumokkal (például JSON) ellátott üzenetek és mezőnevek. Az eseményeken és az eseményvezérelt infrastruktúrán belül tárolt sémák biztosítják, hogy a szerializáláshoz és a szerializáláshoz szükséges metaadatok mindig elérhetők legyenek, és a sémák nem lehetnek rossz helyre helyezve. 

## <a name="event-hubs-namespace"></a>Event Hubs-névtér
Egy Event Hubs névtér mostantól képes a séma-csoportok futtatására az Event hubok (vagy Kafka-témakörök) mellett. A séma beállításjegyzékét üzemelteti, és több sémával is rendelkezhet. Annak ellenére, hogy az Azure Event Hubsban üzemel, a séma beállításjegyzéke univerzálisan használható az összes Azure Messaging Services és bármely más üzenet-vagy Event Broker számára. Mindegyik séma-csoport egy külön biztonságos tárház a sémák készletéhez. A csoportok egy adott alkalmazással vagy szervezeti egységgel igazíthatók. 

## <a name="schema-groups"></a>Sémakezelő csoportok
A séma-csoport hasonló sémák logikai csoportja az üzleti feltételek alapján. A séma-csoport egy séma több verzióját is megtarthatja. A sémák kompatibilitási kényszerítési beállítása segíthet biztosítani, hogy az újabb séma-verziók visszafelé kompatibilisek legyenek.

A csoportosítási mechanizmus által kiszabott biztonsági határ segít biztosítani, hogy a kereskedelmi titkok véletlenül ne szivárognak ki a metaadatokon olyan helyzetekben, amikor a névtér több partner között van megosztva. Azt is lehetővé teszi, hogy az alkalmazások tulajdonosai a sémákat a többi olyan alkalmazástól függetlenül kezeljék, amelyek ugyanazt a névteret használják.


## <a name="schemas"></a>Sémák
A sémák határozzák meg a termelők és a fogyasztók közötti szerződést. Egy Event Hubs séma beállításjegyzékében definiált séma segít a szerződésnek az események adatain kívüli kezelésében, így nem távolítja el a hasznos adatok terhelését. A sémának van neve, típusa (példa: rekord, tömb stb.), kompatibilitási mód (nincs, továbbítás, visszafelé, teljes) és szerializálási típus (csak Avro). Egy séma több verzióját is létrehozhatja, és lekérheti és használhatja a séma egy adott verzióját. 

## <a name="client-sdks"></a>Ügyfél-SDK-k
A következő kódtárak egyikét használhatja egy Avro-szerializáló használatával, amellyel szerializálhatja és deszerializálhatja a séma beállításjegyzék-sémájának azonosítóit és a Avro-kódolású adatokat tartalmazó hasznos adatokat.

- [.NET – Microsoft. Azure. reSchemaRegistry. ApacheAvro](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/schemaregistry/Microsoft.Azure.Data.SchemaRegistry.ApacheAvro)
- [Java – Azure-schemaregistry – Avro](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/schemaregistry/azure-data-schemaregistry-avro/)
- [Python – Azure-schemaregistry – avroserializer](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/schemaregistry/azure-schemaregistry-avroserializer)
- [JavaScript @azure/schema-registry-avro](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/schemaregistry/schema-registry-avro)
- [Apache Kafka](https://github.com/Azure/azure-schema-registry-for-kafka/) – az Azure Schema Registry által támogatott Kafka-integrált Apache Avro-szerializálók és deszerializálók futtatása. A Java-ügyfél Apache Kafka ügyfél-szerializáló az Azure sémakezelő beállításjegyzékben bármilyen Apache Kafka-forgatókönyvben és bármely Apache Kafka®-alapú üzembe helyezéssel vagy felhőalapú szolgáltatásban használható. 

Az alábbi ábrán a séma beállításjegyzékének Event Hubs: 

:::image type="content" source="./media/schema-registry-overview/flow-diagram.png" alt-text="Folyamatábra":::

## <a name="standard-vs-dedicated-limits"></a>Standard és dedikált korlátok
A határértékek (például a névtérben lévő séma-csoportok száma) és a Event Hubs standard és dedikált szintjei esetében különböznek a [séma beállításjegyzékének korlátai](../azure-resource-manager/management/azure-subscription-service-limits.md#schema-registry-limitations) című témakörben.

## <a name="azure-role-based-access-control"></a>Azure-beli szerepköralapú hozzáférés-vezérlés
Ha programozott módon fér hozzá a séma beállításjegyzékéhez, regisztrálnia kell egy alkalmazást Azure Active Directory (Azure AD) szolgáltatásban, és hozzá kell adnia az alkalmazás rendszerbiztonsági tag az egyik Azure szerepköralapú hozzáférés-vezérlési (Azure RBAC) szerepkörhöz:

| Szerepkör | Leírás | 
| ---- | ----------- | 
| Tulajdonos | Séma-beállításjegyzékbeli csoportok és sémák olvasása, írása és törlése. |
| Közreműködő | Séma-beállításjegyzékbeli csoportok és sémák olvasása, írása és törlése. |
| [Séma beállításjegyzék-olvasója (előzetes verzió)](../role-based-access-control/built-in-roles.md#schema-registry-reader-preview) | Séma-beállításjegyzékbeli csoportok és sémák olvasása és listázása. |
| [Séma beállításjegyzékbeli közreműködői (előzetes verzió)](../role-based-access-control/built-in-roles.md#schema-registry-reader-preview) | Séma-beállításjegyzékbeli csoportok és sémák olvasása, írása és törlése. |

Az alkalmazások a Azure Portal használatával történő regisztrálásával kapcsolatos utasításokért lásd: [alkalmazás regisztrálása az Azure ad](../active-directory/develop/quickstart-register-app.md)-ben. Jegyezze fel az ügyfél-azonosítót (az alkalmazás AZONOSÍTÓját), a bérlő AZONOSÍTÓját és a kódban használandó titkot. 

## <a name="next-steps"></a>Következő lépések

- Ha meg szeretné tudni, hogyan hozhat létre egy séma-beállításjegyzéket a Azure Portal használatával, tekintse meg a [Event Hubs séma-beállításjegyzék létrehozása a Azure Portal használatával](create-schema-registry.md)című témakört.
- Tekintse meg az alábbi, **Avro ügyféloldali függvénytár** -mintákat.
    - [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/schemaregistry/Microsoft.Azure.Data.SchemaRegistry.ApacheAvro/tests/Samples)
    - [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/schemaregistry/azure-data-schemaregistry-avro/src/samples)
    - [JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/schemaregistry/schema-registry-avro/samples )
    - [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/schemaregistry/azure-schemaregistry-avroserializer/samples )
    - [Kafka Avro-integráció az Azure Schema registryben](https://github.com/Azure/azure-schema-registry-for-kafka/tree/master/csharp/avro/samples)
