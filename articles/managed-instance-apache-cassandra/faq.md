---
title: Gyakori kérdések az Azure felügyelt példányáról az Apache Cassandra Azure Portal
description: Gyakran ismételt kérdések az Azure felügyelt példányáról az Apache Cassandra-vel. Ez a cikk a felügyelt példányok, az előnyök, az átviteli sebességi korlátok, a támogatott régiók és egyéb konfigurációs adatok használatára vonatkozó kérdéseket tárgyalja.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.openlocfilehash: 1ba2b7d648c86912118b83a566bf2eb0800baee2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101748698"
---
# <a name="frequently-asked-questions-about-azure-managed-instance-for-apache-cassandra-preview"></a>Gyakran ismételt kérdések az Azure felügyelt példányáról az Apache Cassandra (előzetes verzió) szolgáltatásban

Ez a cikk az Azure felügyelt példányának Apache Cassandra-vel kapcsolatos gyakori kérdéseit tárgyalja. Megismerheti a felügyelt példányok használatát, előnyeit, átviteli korlátait, a támogatott régiókat és a konfiguráció részleteit.

> [!IMPORTANT]
> Az Apache Cassandra Azure felügyelt példánya jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="general-faq"></a>Gyakori kérdések – általános

### <a name="what-are-the-benefits-azure-managed-instance-for-apache-cassandra"></a>Milyen előnyökkel jár az Azure felügyelt példánya az Apache Cassandra esetében?

Az Apache Cassandra-adatbázis a megfelelő választás, ha skálázhatóságra és magas rendelkezésre állásra van szükség a teljesítmény veszélyeztetése nélkül. Kiváló platformot biztosít a kritikus fontosságú adathoz, mivel a lineáris skálázhatóság és a bizonyított hibatűrés az áru hardverén vagy a felhő infrastruktúráján alapul. Az Apache Cassandra Azure felügyelt példánya olyan szolgáltatás, amely az Azure-ban üzembe helyezett, nyílt forráskódú Apache Cassandra-adatközpontok példányait kezeli.

Akár teljes egészében a felhőben, akár a hibrid felhő és a helyszíni fürt részeként is használható. Ez a szolgáltatás nagyszerű választás, ha részletes konfigurálást és vezérlést szeretne a nyílt forráskódú Apache Cassandra-ban, karbantartási terhelés nélkül.

### <a name="why-should-i-use-this-service-instead-of-azure-cosmos-db-cassandra-api"></a>Miért érdemes ezt a szolgáltatást Azure Cosmos DB Cassandra API helyett használni?

Az Apache Cassandra Azure felügyelt példányát a Azure Cosmos DB csapat továbbítja. Ez egy önálló felügyelt szolgáltatás a nyílt forráskódú Apache Cassandra-adatközpontok és-fürtök üzembe helyezéséhez, karbantartásához és méretezéséhez. A [Azure Cosmos DB Cassandra API](../cosmos-db/cassandra-introduction.md) másrészt egy platform-szolgáltatás, amely együttműködési réteget biztosít az Apache Cassandra Wire protokollhoz. Ha az elvárás, hogy a platform ugyanúgy viselkedjen, mint bármely Apache Cassandra-fürt, válassza a felügyelt példány szolgáltatást. További információ: az [Azure felügyelt példánya az Apache Cassandra Vs Azure Cosmos DB Cassandra API](compare-cosmosdb-managed-instance.md) cikkben.

### <a name="is-azure-managed-instance-for-apache-cassandra-dependent-on-azure-cosmos-db"></a>Az Azure felügyelt példánya az Apache Cassandra Azure Cosmos DB függ?

Nem, az Apache Cassandra és a Azure Cosmos DB háttérrendszer esetében nem létezik architektúra-függőség az Azure felügyelt példányai között. 

#### <a name="can-i-deploy-azure-managed-instance-for-apache-cassandra-in-any-region"></a>Telepíthetem az Azure felügyelt példányát Apache Cassandra-ra bármely régióban?

A felügyelt példány az előzetes verzióban korlátozott számú régióban lesz elérhető.

### <a name="what-are-the-storage-and-throughput-limits-of-azure-managed-instance-for-apache-cassandra"></a>Milyen tárolási és átviteli sebességre vonatkozó korlátozások vonatkoznak az Azure felügyelt példányára az Apache Cassandra esetében?

Ezek a korlátozások a kiválasztott virtuális gépektől függenek.

### <a name="what-is-the-cost-of-azure-managed-instance-for-apache-cassandra"></a>Mennyibe kerül az Apache Cassandra Azure felügyelt példányának díja?

A felügyelt példányokra vonatkozó díjak a mögöttes virtuális gép költségein alapulnak, kis jelöléssel. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/managed-instance-apache-cassandra/) olvasható.

### <a name="can-i-use-yaml-file-settings-to-configure-behavior"></a>Használhatom a YAML-fájlok beállításait a viselkedés konfigurálásához?

Igen, a YAML ágyazható be egy Azure Resource Manager sablon központi telepítésének részeként.

### <a name="how-can-i-monitor-infrastructure-along-with-throughput"></a>Hogyan figyelhető meg az infrastruktúra az átviteli sebesség mellett?

A [Prometheus](https://prometheus.io/docs/introduction/overview/) -kiszolgáló üzemelteti a tevékenységek figyelését a fürtön, és egy végpontot tesz elérhetővé. Ez 10 percet vagy 10 GB-nyi adatmennyiséget tart fenn (amelyek közül a küszöbérték eléri az elsőt). A figyelés használatához létre kell hoznia egy [összevonást](https://prometheus.io/docs/prometheus/latest/federation/) és egy megfelelő irányítópult-eszközt, például a Grafana-t.

### <a name="does-azure-managed-instance-for-apache-cassandra-provide-full-backups"></a>Az Azure felügyelt példánya az Apache Cassandra teljes biztonsági mentést biztosít?

Igen, teljes biztonsági mentést biztosít az Azure Storage-hoz, és új fürtre állítja vissza

### <a name="how-can-i-migrate-data-from-my-existing-apache-cassandra-cluster-to-azure-managed-instance-for-apache-cassandra"></a>Hogyan telepíthetek át egy meglévő Apache Cassandra-fürt adatait az Azure felügyelt példányára az Apache Cassandra használatával?

Az Apache Cassandra Azure felügyelt példánya az Apache Cassandra összes funkcióját támogatja az adatközpontok közötti adatreplikálásra és adatátvitelre.

### <a name="can-i-pair-an-on-premises-apache-cassandra-cluster-with-the-azure-managed-instance-for-apache-cassandra"></a>Egy helyszíni Apache Cassandra-fürtöt is párosítható az Apache Cassandra Azure felügyelt példányával?

Igen, konfigurálhat hibrid fürtöt az Azure Virtual Network a szolgáltatás által központilag telepített adatközpontok használatával. A felügyelt példányok adatközpontjai az ugyanazon a fürtön belül található helyszíni adatközpontokkal kommunikálhatnak.

### <a name="where-can-i-give-feedback-on-azure-managed-instance-for-apache-cassandra-features"></a>Hol küldhetek visszajelzést az Azure felügyelt példányáról az Apache Cassandra funkciói számára?

A "Managed Apache Cassandra" kategória használatával visszajelzést adhat a [felhasználói](https://feedback.azure.com/forums/263030-azure-cosmos-db?category_id=398548) visszajelzések segítségével.

Ha a fiókjával van probléma, nyújtson be [támogatási kérelmet](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) az Azure Portalon.

## <a name="deployment-specific-faq"></a>Üzembe helyezésre vonatkozó gyakori kérdések

### <a name="will-the-managed-instance-support-node-addition-cluster-status-and-node-status-commands"></a>A felügyelt példány támogatja a csomópontok hozzáadását, a fürt állapotát és a csomópont állapotára vonatkozó parancsokat?

A *csak olvasható* Nodetool parancsok, például az `status` Azure CLI-n keresztül érhetők el. Azonban az olyan műveletek, mint a *csomópontok hozzáadása* , nem érhetők el, mert a felügyelt példányban lévő csomópontok állapotát felügyeljük. A hibrid módban csatlakozhat a fürthöz a *Nodetool* használatával. A Nodetool használata azonban nem ajánlott, mivel a fürt destabilizálhatja a fürtöt. Emellett érvénytelenítheti a fürtben található felügyelt példány-adatközpontok állapotával kapcsolatos, a termelési támogatásra vonatkozó SLA-t is.

### <a name="what-happens-with-various-settings-for-table-metadata"></a>Mi történik a táblázat metaadatainak különböző beállításaival?

A táblázat metaadatainak beállításai, például a Bloom Filter, a gyorsítótárazás, a javítási esély, a gc_grace és a tömörítési memtable_flush_period teljes mértékben támogatottak, ugyanúgy, mint bármely saját üzemeltetésű Apache Cassandra-környezettel.

## <a name="next-steps"></a>Következő lépések

A más API-kkal kapcsolatos gyakori kérdések megismeréséhez lásd:

* [Felügyelt példány-fürt létrehozása a Azure Portal](create-cluster-portal.md)
* [Felügyelt Apache Spark-fürt üzembe helyezése Azure Databricks](deploy-cluster-databricks.md)
* [Azure felügyelt példány kezelése Apache Cassandra-erőforrásokhoz az Azure CLI használatával](manage-resources-cli.md)