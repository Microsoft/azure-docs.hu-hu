---
title: A vállalati biztonság áttekintése az Azure HDInsight
description: Ismerje meg a vállalati biztonság Azure HDInsight való biztosításának különböző módszereit.
ms.service: hdinsight
ms.topic: overview
ms.custom: seoapr2020
ms.date: 08/24/2020
ms.openlocfilehash: f4fa1e64e00f2ae027d80960072da7d73d3a89cb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98946844"
---
# <a name="overview-of-enterprise-security-in-azure-hdinsight"></a>A vállalati biztonság áttekintése az Azure HDInsight

Az Azure HDInsight számos módszert kínál a nagyvállalati biztonsági igények kielégítésére. A megoldások többsége alapértelmezés szerint nem aktiválódik. Ez a rugalmasság lehetővé teszi, hogy kiválassza a legfontosabb biztonsági funkciókat, és segít elkerülni a nem kívánt szolgáltatások kifizetését. Ez a rugalmasság azt is jelenti, hogy az Ön felelőssége, hogy a megfelelő megoldások engedélyezve legyenek a telepítéshez és a környezethez.

Ez a cikk a biztonsági megoldásokat úgy tekinti meg, hogy a biztonsági megoldásokat négy hagyományos biztonsági pillérre osztja: biztonsági, hitelesítési, engedélyezési és titkosítási szinten.

A cikk emellett bemutatja az **Azure HDInsight Enterprise Security Package (ESP)**, amely Active Directory-alapú hitelesítést, többfelhasználós támogatást és szerepköralapú hozzáférés-vezérlést biztosít a HDInsight-fürtökhöz.

## <a name="enterprise-security-pillars"></a>Vállalati biztonsági oszlopok

A nagyvállalati biztonság egyik módja a biztonsági megoldások felosztása négy fő csoportra a vezérlés típusa alapján. Ezeket a csoportokat biztonsági pilléreknek is nevezik, és a következők: peremhálózat-biztonság, hitelesítés, engedélyezés és titkosítás.

### <a name="perimeter-security"></a>Szegélyhálózati biztonság

A HDInsight [virtuális hálózatokon](../hdinsight-plan-virtual-network-deployment.md)keresztül érhető el. A vállalati rendszergazda létrehozhat egy fürtöt egy virtuális hálózaton (VNET) belül, és hálózati biztonsági csoportok (NSG-EK) használatával korlátozhatja a virtuális hálózathoz való hozzáférést. Csak a bejövő NSG-szabályokban engedélyezett IP-címek kommunikálhatnak a HDInsight-fürttel. Ez a konfiguráció peremhálózati biztonságot nyújt.

Egy VNET telepített összes fürthöz tartozik egy privát végpont is. A végpont feloldja a VNET belüli magánhálózati IP-címet a fürt átjáróinak való magánhálózati HTTP-hozzáféréshez.

### <a name="authentication"></a>Hitelesítés

A HDInsight [Enterprise Security Package](apache-domain-joined-architecture.md) Active Directory-alapú hitelesítést, többfelhasználós támogatást és szerepköralapú hozzáférés-vezérlést biztosít. A Active Directory integrációja [Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md)használatával érhető el. Ezekkel a képességekkel létrehozhat egy Active Directory tartományhoz csatlakoztatott HDInsight-fürtöt. Ezután konfigurálja azon alkalmazottak listáját, akik hitelesíteni tudják a fürtöt.

Ezzel a beállítással a vállalati alkalmazottak tartományi hitelesítő adataikkal jelentkezhetnek be a fürt csomópontjaira. A tartományi hitelesítő adataikat is használhatják a más jóváhagyott végpontokkal való hitelesítéshez. Mint az Apache Ambari views, az ODBC, a JDBC, a PowerShell és a REST API-k, hogy együttműködjön a fürttel.

### <a name="authorization"></a>Engedélyezés

Az ajánlott eljárás a legtöbb vállalatnál azt biztosítja, hogy nem minden alkalmazott teljes hozzáféréssel rendelkezik az összes vállalati erőforráshoz. Hasonlóképpen a rendszergazda szerepköralapú hozzáférés-vezérlési házirendeket is meghatározhat a fürterőforrások számára. Ez a művelet csak az ESP-fürtökben érhető el.

A Hadoop-rendszergazda konfigurálhatja a szerepköralapú hozzáférés-vezérlést (RBAC). A konfigurációk [az Apache](apache-domain-joined-run-hive.md) [HBase](apache-domain-joined-run-hbase.md)és a [Kafka](apache-domain-joined-run-kafka.md) -t az Apache Ranger beépülő modulokkal védik. A RBAC-házirendek konfigurálása lehetővé teszi, hogy a szervezet egyik szerepkörével társítsa az engedélyeket. Ez az absztrakciós réteg megkönnyíti annak biztosítását, hogy az emberek csak a munkahelyi feladataik elvégzéséhez szükséges engedélyeket használják. A Ranger lehetővé teszi az alkalmazottak és a hozzáférés-vezérlési házirendek által végzett módosítások naplózását is.

Például, a rendszergazda konfigurálhatja az [Apache Ranger](https://ranger.apache.org/) keretrendszert, hogy beállítsa a Hive hozzáférés-vezérlés házirendjét. Ez a funkció biztosítja a sor szintű és az oszlop szintű szűrést (adatmaszkolás). És szűri a bizalmas adatokat a jogosulatlan felhasználóktól.

### <a name="auditing"></a>Naplózás

A fürterőforrás-hozzáférés naplózása szükséges az erőforrások jogosulatlan vagy véletlen hozzáférésének nyomon követéséhez. Ez olyan fontos, mint a fürt erőforrásainak jogosulatlan hozzáférés elleni védelme.

A rendszergazda megtekintheti és jelentheti az összes hozzáférést a HDInsight-fürt erőforrásaihoz és az adatforrásokhoz. A rendszergazda megtekintheti és jelentheti a hozzáférés-vezérlési szabályzatok módosításait.

Az Apache Ranger és a Ambari-naplók eléréséhez, valamint az SSH-hozzáférési naplókhoz [engedélyezze Azure monitor](../hdinsight-hadoop-oms-log-analytics-tutorial.md#cluster-auditing) és tekintse meg a naplózási rekordokat biztosító táblákat.

### <a name="encryption"></a>Titkosítás

Az adatok védelme fontos a szervezeti biztonsági és megfelelőségi követelmények teljesítéséhez. A jogosulatlan alkalmazottaktól származó adatokhoz való hozzáférés korlátozásával együtt érdemes titkosítani.

A HDInsight támogatja az inaktív adatok titkosítását a platform által felügyelt és az [ügyfél által felügyelt kulcsokkal](../disk-encryption.md)együtt. A forgalomban lévő adattitkosítás a TLS és az IPSec protokollal is kezelhető. További információkért lásd: az [Azure HDInsight történő átvitel titkosítása](encryption-in-transit.md) .

### <a name="compliance"></a>Megfelelőség

Az Azure megfelelőségi ajánlatai különféle típusú biztosítékokon alapulnak, beleértve a formális tanúsítványokat is. Továbbá tanúsítványok, érvényesítések és engedélyek. Független harmadik féltől származó könyvvizsgáló cégek által készített értékelések. A Microsoft által készített szerződéses módosítások, önértékelések és ügyfél-útmutató dokumentumok. A HDInsight vonatkozó megfelelőségi információkért tekintse meg a [Microsoft adatvédelmi központot](https://www.microsoft.com/trust-center) , és [tekintse át Microsoft Azure megfelelőségét](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942).

## <a name="shared-responsibility-model"></a>Megosztott felelősség modell

Az alábbi ábra összefoglalja a főbb rendszerbiztonsági területeket és az egyes eszközökön elérhető biztonsági megoldásokat. Emellett kiemeli, hogy mely biztonsági területek felelnek meg az ügyfélnek. A HDInsight pedig a szolgáltató feladata.

![HDInsight – megosztott felelősségi diagram](./media/hdinsight-security-overview/hdinsight-shared-responsibility.png)

Az alábbi táblázat a biztonsági megoldások egyes típusaihoz kapcsolódó erőforrásokra mutató hivatkozásokat tartalmaz.

| Biztonsági térség | Elérhető megoldások | Felelős fél |
|---|---|---|
| Adathozzáférés biztonsága | Hozzáférés-vezérlési listák konfigurálása a Azure Data Lake Storage Gen1 és a Gen2 [hozzáférés-vezérlési listáihoz](../../storage/blobs/data-lake-storage-access-control.md)  | Ügyfél |
|  | Engedélyezze a ["biztonságos átvitel szükséges"](../../storage/common/storage-require-secure-transfer.md) tulajdonságot a Storage-fiókokon. | Ügyfél |
|  | [Azure Storage-tűzfalak](../../storage/common/storage-network-security.md) és virtuális hálózatok konfigurálása | Ügyfél |
|  | [Azure Virtual Network szolgáltatásbeli végpontok](../../virtual-network/virtual-network-service-endpoints-overview.md) konfigurálása a Cosmos db és az [Azure SQL db](../../azure-sql/database/vnet-service-endpoint-rule-overview.md) -hez | Ügyfél |
|  | Győződjön meg arról, hogy a [titkosítás az átvitelben](./encryption-in-transit.md) funkció engedélyezve van a TLS és az IPSec használatára a fürtön belüli kommunikációhoz. | Ügyfél |
|  | [Ügyfél által felügyelt kulcsok](../../storage/common/customer-managed-keys-configure-key-vault.md) konfigurálása az Azure Storage encryption szolgáltatáshoz | Ügyfél |
|  | Az Azure-támogatás által az adataihoz való hozzáférés vezérlése az [ügyfél kulcstárolójának](../../security/fundamentals/customer-lockbox-overview.md) használatával | Ügyfél |
| Alkalmazás-és middleware-biztonság | Integráció a HRE-DS-sel és az [ESP konfigurálása](apache-domain-joined-configure-using-azure-adds.md) vagy [a hib használata a OAuth-hitelesítéshez](identity-broker.md)| Ügyfél |
|  | [Apache Ranger-engedélyezési](apache-domain-joined-run-hive.md) házirendek konfigurálása | Ügyfél |
|  | [Azure monitor naplók](../hdinsight-hadoop-oms-log-analytics-tutorial.md) használata | Ügyfél |
| Operációs rendszer biztonsága | Fürtök létrehozása a legújabb biztonságos alapképpel | Ügyfél |
|  | Az [operációs rendszer javításának](../hdinsight-os-patching.md) rendszeres időközönkénti ellenőrzése | Ügyfél |
|  | [CMK-lemezek titkosításának biztosítása a virtuális gépek számára](../disk-encryption.md) | Ügyfél |
| Hálózati biztonság | [Virtuális hálózat](../hdinsight-plan-virtual-network-deployment.md) konfigurálása |
|  | A [bejövő hálózati biztonsági csoport (NSG) szabályainak](../control-network-traffic.md) vagy [privát hivatkozásának](../hdinsight-private-link.md) konfigurálása | Ügyfél |
|  | A [kimenő forgalom korlátozásának](../hdinsight-restrict-outbound-traffic.md) konfigurálása a tűzfallal | Ügyfél |
|  | [Az IPSec-titkosítás konfigurálása a fürtcsomópontok közötti átvitelben](encryption-in-transit.md) | Ügyfél |
| Virtualizált infrastruktúra | N/A | HDInsight (felhőalapú szolgáltató) |
| Fizikai infrastruktúra biztonsága | N/A | HDInsight (felhőalapú szolgáltató) |

## <a name="next-steps"></a>Következő lépések

* [Az ESP-vel rendelkező HDInsight-fürtök tervezése](apache-domain-joined-architecture.md)
* [HDInsight-fürtök beállítása az ESP-vel](./apache-domain-joined-configure-using-azure-adds.md)
* [HDInsight-fürtök az ESP-vel való kezelése](apache-domain-joined-manage.md)