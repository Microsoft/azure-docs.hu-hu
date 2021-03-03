---
title: Az Apache Cassandra és a Azure Cosmos DB Azure felügyelt példányai közötti különbségek Cassandra API
description: Ismerje meg az Azure felügyelt példányának az Apache Cassandra és a Azure Cosmos DB Cassandra API közötti különbségeit. Emellett megismerheti az egyes szolgáltatások előnyeit, és kiválaszthatja őket.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.openlocfilehash: 3050dda4b3c0e1a05d751a4f5969bba69ad0506d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101748759"
---
# <a name="differences-between-azure-managed-instance-for-apache-cassandra-preview-and-azure-cosmos-db-cassandra-api"></a>Az Apache Cassandra (előzetes verzió) és a Azure Cosmos DB Azure felügyelt példánya közötti különbségek Cassandra API 

Ebből a cikkből megtudhatja, hogyan különbözik az Azure felügyelt példányai az Apache Cassandra és a Azure Cosmos DB Cassandra API. Ez a cikk ajánlásokat tartalmaz a két szolgáltatás közötti választáshoz, vagy a saját Apache Cassandra-környezet üzemeltetéséhez.

> [!IMPORTANT]
> Az Apache Cassandra Azure felügyelt példánya jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="key-differences"></a>Fő eltérések

Az Apache Cassandra Azure felügyelt példánya automatizált üzembe helyezést, skálázást és műveleteket biztosít az Azure-beli nyílt forráskódú Apache Cassandra-példányok csomópont-állapotának fenntartásához. Lehetővé teszi a meglévő helyszíni vagy Felhőbeli saját Apache Cassandra-fürtök kapacitásának felskálázását is. A felügyelt Cassandra-adatközpontok a meglévő fürt ringbe való hozzáadásával méretezhetők.

A Azure Cosmos DB [Cassandra API](../cosmos-db/cassandra-introduction.md) a Microsoft globálisan elosztott, felhőalapú adatbázis-szolgáltatásának [Azure Cosmos db](../cosmos-db/index.yml)a kompatibilitási réteg. Ezeknek a szolgáltatásoknak az Azure-ban való kombinációja az Apache Cassandra széles választékát biztosítja az összetett hibrid felhőalapú környezetekben.

## <a name="how-to-choose"></a>Hogyan lehet választani?

A következő táblázat bemutatja a gyakori forgatókönyveket, a számítási feladatok követelményeit, valamint azokat a törekvéseket, amelyekben az egyes telepítési módszerek illeszkednek:

| |Önkiszolgáló Apache Cassandra helyszíni vagy Azure-beli | Azure felügyelt példány az Apache Cassandra-hoz | Azure Cosmos DB Cassandra API |
|---------|---------|---------|---------|
|**Központi telepítés típusa**| Az Apache Cassandra kiválóan testreszabható üzembe helyezése egyéni javításokkal vagy snitches. | Egy szabványos, nyílt forráskódú Apache Cassandra-alapú üzembe helyezést használhat egyéni kód nélkül. | Olyan platformmal rendelkezik, amely nem Apache Cassandra alatta van, de az minden nyílt forráskódú ügyfél-illesztőprogramnak megfelel a [huzal protokoll](../cosmos-db/cassandra-support.md) szintjén. |
| **Működési terhelés**| Meglévő Cassandra-szakértőkkel rendelkezik, akik üzembe helyezhetik, konfigurálhatják és kezelhetik a fürtöket.  | Csökkenteni szeretné az Apache Cassandra Node Health működési terhelését, de továbbra is kézben tarthatja a platform szintjének konfigurációit, például a replikálást és a konzisztenciát. | A felhőben egy teljes körűen felügyelt platform-szolgáltatásbeli adatbázis használatával szeretné megkerülni a működési terhelést. |
| **Operációsrendszer-követelmények**| Meg kell őriznie az egyéni vagy a virtuális gépek operációs rendszerének lemezképeit. | A vanília-rendszerképeket használhatja, de szeretné szabályozni a SKU-ket, a memóriát, a lemezeket és a IOPS. | Azt szeretné, hogy a kapacitás kiépítése egyszerűsíthető legyen, és egyetlen normalizált metrikaként legyen kifejezve, egy az egyhez kapcsolattal az átviteli sebességhez, például a [kérelmek egységei](../cosmos-db/request-units.md) Azure Cosmos db. |
| **Díjszabási modell**| Olyan felügyeleti szoftvereket kíván használni, mint a Datastax-eszközök, és a licencelési költségek is elégedettek. | A nyílt forráskódú licencelési és a virtuálisgép-példányokon alapuló díjszabást részesíti előnyben. | A Felhőbeli natív díjszabást szeretné használni, amely magában foglalja az [autoscale](../cosmos-db/manage-scale-cassandra.md#use-autoscale) és a [kiszolgáló](../cosmos-db/serverless.md) nélküli ajánlatokat is. |
| **Elemzés**| Az analitikai folyamatok kiépítésének teljes körű szabályozására van szükség, függetlenül attól, hogy milyen terheléssel kell kiépíteni és karbantartani őket. | Olyan felhőalapú analitikai szolgáltatásokat szeretne használni, mint a Azure Databricks. | Közel valós idejű hibrid tranzakciós elemzést szeretne a platformba beépített [Azure szinapszis-hivatkozással a Cosmos DBhoz](../cosmos-db/synapse-link.md). |
| **Számítási feladatok mintája**| A számítási feladat meglehetősen állandó állapotú, és nem igényel gyakran méretezési csomópontokat a fürtben. | A számítási feladat illékony, és képesnek kell lennie az adatközpont csomópontjainak vertikális felskálázására vagy skálázására, illetve az adatközpontok egyszerű hozzáadására vagy eltávolítására. | A számítási feladat gyakran ingadozik, és a lehető leggyorsabban és jelentős mértékben kell tudnia méretezni a méretezést és a méretezést. |
| **SLA-k**| Elégedett a SLA-kat a konzisztencia, az átviteli sebesség, a rendelkezésre állás és a vész-helyreállítási folyamat fenntartása érdekében. | Elégedett a SLA-kat a konzisztencia, az átviteli sebesség és a rendelkezésre állás terén, de segítségre van szüksége a biztonsági mentésekhez. | Teljes mértékben átfogó SLA-kat szeretne a konzisztencia, az átviteli sebesség, a rendelkezésre állás és a vész-helyreállítás érdekében. |

## <a name="next-steps"></a>Következő lépések

Ismerkedjen meg az egyik rövid útmutatóval:

* [Felügyelt példány-fürt létrehozása a Azure Portal](create-cluster-portal.md)