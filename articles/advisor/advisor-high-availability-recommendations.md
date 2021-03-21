---
title: Az Advisor-alkalmazás megbízhatóságának javítása
description: A Azure Advisor használatával biztosíthatja és javíthatja a megbízhatóságot az üzleti szempontból kritikus fontosságú Azure-környezetekben.
ms.topic: article
ms.date: 09/27/2020
ms.openlocfilehash: c96b47f1ee145129f4c14c6646f93abeb8a5aac9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100579974"
---
# <a name="improve-the-reliability-of-your-application-by-using-azure-advisor"></a>Az alkalmazás megbízhatóságának javítása Azure Advisor használatával

Azure Advisor segít az üzleti szempontból kritikus fontosságú alkalmazások folytonosságának biztosításában és tökéletesítésében. Az Advisor megbízhatósági javaslatai az Advisor irányítópultjának **megbízhatóság** lapján olvashatók.

## <a name="check-the-version-of-your-check-point-network-virtual-appliance-image"></a>A hálózati virtuális berendezés rendszerképének verziószámának keresése

Az Advisor képes megállapítani, hogy a virtuális gép fut-e a "a" hálózati kapcsolat elvesztése a platform karbantartási műveletei során. Az Advisor javaslata segítséget nyújt a probléma megoldására szolgáló rendszerkép újabb verziójára való frissítéshez. Ez az ellenőrzés biztosítja az üzletmenet folytonosságát a jobb hálózati kapcsolaton keresztül.

## <a name="ensure-application-gateway-fault-tolerance"></a>Az Application Gateway hibatűrésének biztosítása

Ez a javaslat biztosítja az alkalmazás-átjárók által működtetett, kritikus fontosságú alkalmazások üzletmenet-folytonosságát. Az Advisor a hibatűréshez nem konfigurált Application Gateway-példányokat azonosítja. Ezután az elvégezhető szervizelési műveleteket javasolja. Az Advisor egy közepes vagy nagyméretű egypéldányos Application Gateway-átjárót azonosít, és legalább egy példány hozzáadását javasolja. Emellett azonosítja az Egypéldányos és a többpéldányos kisméretű Application Gateway átjárókat is, és azt ajánlja, hogy közepes vagy nagy SKU-ba migrálja őket. Az Advisor javasolja ezeket a műveleteket, hogy az Application Gateway-példányok úgy legyenek konfigurálva, hogy megfeleljenek az adott erőforrásokra vonatkozó SLA-követelményeknek.

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>Virtuális gépek adatainak védelme véletlen törléssel

A virtuális gép biztonsági mentésének beállítása biztosítja az üzleti szempontból kritikus fontosságú adatmennyiség rendelkezésre állását, és védelmet nyújt a véletlen törlés vagy sérülés ellen. Az Advisor azonosítja azokat a virtuális gépeket, amelyeken nincs engedélyezve a biztonsági mentés, és a biztonsági mentést javasolja 

## <a name="ensure-you-have-access-to-azure-experts-when-you-need-it"></a>Szükség esetén ellenőrizze, hogy van-e hozzáférése az Azure-szakértőkhöz

Ha üzleti szempontból kritikus fontosságú számítási feladatokat futtat, fontos, hogy a szükséges technikai támogatáshoz hozzáférjen. Az Advisor olyan potenciális üzleti szempontból kritikus előfizetéseket azonosít, amelyek nem rendelkeznek technikai támogatással a támogatási csomagokban. Javasoljuk, hogy frissítsen egy olyan lehetőségre, amely technikai támogatást is tartalmaz.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-problems-affect-you"></a>Azure Service Health riasztások létrehozása, amelyekről értesítést kap, ha az Azure-problémák érintik Önt

Javasoljuk, hogy Azure Service Health riasztásokat állítson be, hogy értesítést kapjon, ha az Azure-szolgáltatással kapcsolatos problémák érintik Önt. A [Azure Service Health](https://azure.microsoft.com/features/service-health/) egy ingyenes szolgáltatás, amely személyre szabott útmutatást és támogatást nyújt, ha az Azure-szolgáltatással kapcsolatos problémát érint. Az Advisor olyan előfizetéseket azonosít, amelyek nem rendelkeznek konfigurált riasztásokkal, és azt javasolja, hogy konfigurálja azokat.

## <a name="configure-traffic-manager-endpoints-for-resiliency"></a>Traffic Manager végpontok konfigurálása a rugalmasság érdekében

Az Azure Traffic Manager-profilok több végponttal is rendelkeznek magasabb rendelkezésre állással, ha bármelyik végpont meghibásodik. A végpontok különböző régiókban való elhelyezése tovább javítja a szolgáltatás megbízhatóságát. Az Advisor olyan Traffic Manger-profilokat azonosít, ahol csak egy végpont található, és legalább egy végpont hozzáadását javasolja egy másik régióban.

Ha egy Traffic Manager-profilban lévő összes végpont ugyanazon a régióban található, a más régiókban lévő felhasználók kapcsolati késéseket tapasztalhatnak. A végpontok egy másik régióba való felvétele vagy áthelyezése javítja a teljes teljesítményt, és jobb rendelkezésre állást biztosít, ha az egyik régióban lévő összes végpont meghibásodik. Az Advisor azonosítja Traffic Manager a közelségi útválasztáshoz konfigurált profilokat, ahol az összes végpont ugyanabban a régióban található. Azt javasolja, hogy egy végpontot egy másik Azure-régióhoz adjon hozzá vagy helyezzen át.

Ha egy Traffic Manager-profil földrajzi útválasztásra van konfigurálva, a rendszer a forgalmat a meghatározott régiók alapján irányítja a végpontokhoz. Ha egy régió meghibásodik, nincs előre definiált feladatátvétel. Ha van olyan végpontja, ahol a regionális csoportosítás az **összes (világ)** értékre van konfigurálva, akkor elkerülhető a forgalom eldobása és a szolgáltatás rendelkezésre állásának javítása. Az Advisor azonosítja a földrajzi útválasztáshoz konfigurált Traffic Manager-profilokat, ahol nincs olyan végpont konfigurálva, amelynek a regionális csoportosítása az **összes (világ)**. Javasolja a konfiguráció módosítását, hogy az **összes végpont (világ)** legyen.

## <a name="use-soft-delete-on-your-azure-storage-account-to-save-and-recover-data-after-accidental-overwrite-or-deletion"></a>Az adatok mentése és helyreállítása a véletlen felülírás vagy törlés után a Soft delete használatával az Azure Storage-fiókban

Engedélyezze a helyreállítható [törlést](../storage/blobs/soft-delete-blob-overview.md) a Storage-fiókban, hogy a törölt Blobok a véglegesen törölt állapotba kerüljenek a végleges törlés helyett. Az adatok felülírásakor a rendszer létrehoz egy helyreállítható módon törölt pillanatképet a felülírt adatok állapotának mentéséhez. A Soft delete lehetővé teszi a helyreállítást véletlen törlések vagy felülírások esetén. Az Advisor olyan Azure Storage-fiókokat azonosít, amelyeken nincs engedélyezve a helyreállított törlés, és azt javasolja, hogy engedélyezze azt.

## <a name="configure-your-vpn-gateway-to-active-active-for-connection-resiliency"></a>VPN-átjáró konfigurálása aktív-aktív kapcsolati rugalmasságra

Az aktív-aktív konfigurációban a VPN-átjárók mindkét példánya S2S VPN-alagutat hoz létre a helyszíni VPN-eszközhöz. Ha egy tervezett karbantartási esemény vagy nem tervezett esemény egy átjáró-példányra esik, a rendszer automatikusan átvált a másik aktív IPsec-alagútra. Azure Advisor azonosítja azokat a VPN-átjárókat, amelyek nincsenek aktív-aktívként konfigurálva, és azt javasolja, hogy magas rendelkezésre álláshoz konfigurálja azokat.

## <a name="use-production-vpn-gateways-to-run-your-production-workloads"></a>Éles környezetben működő VPN-átjárók használata az éles számítási feladatok futtatásához

Azure Advisor ellenőrzi az alapszintű SKU-t használó VPN-átjárókat, és azt javasolja, hogy használjon éles SKU-t. Az alapszintű SKU fejlesztéshez és teszteléshez lett tervezve. Termelési SKU-ajánlat:
- További alagutak. 
- BGP-támogatás. 
- Aktív-aktív konfigurációs beállítások. 
- Egyéni IPSec/IKE-házirend. 
- Nagyobb stabilitás és rendelkezésre állás.

## <a name="ensure-virtual-machine-fault-tolerance-temporarily-disabled"></a>A virtuális gép hibatűrésének biztosítása (átmenetileg letiltva)

Ha redundanciát szeretne biztosítani az alkalmazás számára, javasoljuk, hogy legalább két virtuális gépet egy rendelkezésre állási csoportba csoportosítson. Az Advisor azonosítja azokat a virtuális gépeket, amelyek nem részei egy rendelkezésre állási csoportnak, és azt ajánlja, hogy helyezze át őket egy Ez a konfiguráció biztosítja, hogy a tervezett vagy nem tervezett karbantartás során legalább egy virtuális gép elérhető legyen, és megfelel az Azure-beli virtuális gépek SLA-nak. Dönthet úgy is, hogy létrehoz egy rendelkezésre állási készletet a virtuális géphez, vagy hozzáadja a virtuális gépet egy meglévő rendelkezésre állási csoporthoz.

> [!NOTE]
> Ha úgy dönt, hogy létrehoz egy rendelkezésre állási készletet, legalább egy virtuális gépet hozzá kell adnia. Javasoljuk, hogy egy rendelkezésre állási csoportban két vagy több virtuális gépet csoportosítson, hogy legalább egy gép elérhető legyen a leállás során.

## <a name="ensure-availability-set-fault-tolerance-temporarily-disabled"></a>Rendelkezésre állási csoport hibatűrésének biztosítása (átmenetileg letiltva)

Ha redundanciát szeretne biztosítani az alkalmazás számára, javasoljuk, hogy legalább két virtuális gépet egy rendelkezésre állási csoportba csoportosítson. Az Advisor egyetlen virtuális gépet tartalmazó rendelkezésre állási csoportokat azonosít, és egy vagy több virtuális gép hozzáadását javasolja. Ez a konfiguráció biztosítja, hogy a tervezett vagy nem tervezett karbantartás során legalább egy virtuális gép elérhető legyen, és megfelel az Azure-beli virtuális gépek SLA-nak. Dönthet úgy, hogy létrehoz egy virtuális gépet, vagy egy meglévő virtuális gépet ad hozzá a rendelkezésre állási csoporthoz.  

## <a name="use-managed-disks-to-improve-data-reliability-temporarily-disabled"></a>Felügyelt lemezek használata az adatmegbízhatóság javítására (átmenetileg letiltva)

Azok a virtuális gépek, amelyek olyan rendelkezésre állási csoportban találhatók, amelyekben a Storage-fiókok vagy a tárolási méretezési egységek osztoznak, nem állnak rugalmasan az egyazon tárolási skálázási egységekkel kapcsolatos hibákig. Az Advisor azonosítja ezeket a rendelkezésre állási csoportokat, és javasolja az Azure Managed Disks szolgáltatásba való áttelepítést. Ez a Migrálás biztosítja, hogy a rendelkezésre állási csoportba tartozó virtuális gépek lemezei elég elszigeteltek legyenek, hogy elkerülje az egyetlen meghibásodási pontot. 

## <a name="repair-invalid-log-alert-rules"></a>Érvénytelen naplózási riasztási szabályok javítása

Azure Advisor észleli azokat a naplózási riasztási szabályokat, amelyekben érvénytelen lekérdezések vannak megadva a feltételek szakaszban. Azure Monitor naplózási riasztási szabályok lekérdezéseket futtatnak a megadott gyakorisággal és riasztásokkal az eredmények alapján. A lekérdezések idővel érvénytelenné válhatnak a hivatkozott erőforrások, táblák vagy parancsok változásai miatt. Az Advisor a riasztási lekérdezések helyesbítéseit javasolja, hogy megakadályozza a szabályok automatikus letiltását és a figyelési lefedettség biztosítását. További információ: [riasztási szabályok hibaelhárítása](../azure-monitor/alerts/alerts-troubleshoot-log.md#query-used-in-a-log-alert-isnt-valid)

## <a name="configure-consistent-indexing-mode-on-your-azure-cosmos-db-collection"></a>Konzisztens indexelési mód konfigurálása a Azure Cosmos DB-gyűjteményen

Azure Cosmos DB tárolók lusta indexelési móddal való konfigurálása hatással lehet a lekérdezési eredmények frissességére. Az Advisor észleli az így konfigurált tárolókat, és azt javasolja, hogy konzisztens módba váltson. [További információ a szabályzatok indexeléséről a Azure Cosmos DB.](../cosmos-db/how-to-manage-indexing-policy.md)

## <a name="configure-your-azure-cosmos-db-containers-with-a-partition-key"></a>Azure Cosmos DB-tárolók konfigurálása partíciókulccsal

Azure Advisor azonosítja Azure Cosmos DB nem particionált gyűjteményeket, amelyek megközelítik a kiépített tárolási kvótát. Azt javasolja, hogy ezeket a gyűjteményeket új gyűjteményekbe telepítse át egy partíciós kulcs-definícióval, hogy a szolgáltatás automatikusan bővítse őket. [További információ a partíciós kulcs kiválasztásáról.](../cosmos-db/partitioning-overview.md)

## <a name="upgrade-your-azure-cosmos-db-net-sdk-to-the-latest-version-from-nuget"></a>A Azure Cosmos DB .NET SDK frissítése a NuGet legújabb verziójára

Azure Advisor azonosítja Azure Cosmos DB a .NET SDK régi verzióit használó fiókokat. Azt javasolja, hogy a legújabb verzióra, a teljesítménnyel kapcsolatos fejlesztésekre és a funkciókra vonatkozó funkciókra frissítsen a NuGet legújabb verziójára. [További információ a Azure Cosmos DB .NET SDK-ról.](../cosmos-db/sql-api-sdk-dotnet-standard.md)

## <a name="upgrade-your-azure-cosmos-db-java-sdk-to-the-latest-version-from-maven"></a>Az Azure Cosmos DB Java SDK frissítése a legújabb verzióra a Mavenről

Azure Advisor azonosítja Azure Cosmos DB a Java SDK régi verzióit használó fiókokat. Azt javasolja, hogy a Maven legújabb verziójára frissítsen a legújabb javítások, a teljesítménnyel kapcsolatos fejlesztések és a funkciók funkcióival. [További információ a Azure Cosmos DB Java SDK-ról.](../cosmos-db/sql-api-sdk-java-v4.md)

## <a name="upgrade-your-azure-cosmos-db-spark-connector-to-the-latest-version-from-maven"></a>A Azure Cosmos DB Spark-összekötő frissítése a Maven legújabb verziójára

Azure Advisor azonosítja Azure Cosmos DB a Azure Cosmos DB Spark-összekötő régi verzióit használó fiókokat. Azt javasolja, hogy a Maven legújabb verziójára frissítsen a legújabb javítások, a teljesítménnyel kapcsolatos fejlesztések és a funkciók funkcióival. [További információ a Azure Cosmos DB Spark-összekötőről.](../cosmos-db/spark-connector.md)

## <a name="consider-moving-to-kafka-21-on-hdinsight-40"></a>Érdemes áthelyezni a Kafka 2,1-et a HDInsight 4,0

2020. július 1-től nem fog tudni új Kafka-fürtöket létrehozni a Kafka 1,1 használatával az Azure HDInsight 4,0-on. A meglévő fürtök a jelenlegi állapotukban futnak tovább, Microsoft-támogatás nélkül. A potenciális rendszer/támogatás megszakításának elkerülése érdekében érdemes lehet a HDInsight 2020 4,0-re való áttérést a Kafka 2,1-re.

## <a name="consider-upgrading-older-spark-versions-in-hdinsight-spark-clusters"></a>Érdemes lehet frissíteni a régebbi Spark-verziókat a HDInsight Spark-fürtökben

2020. július 1-től nem hozhatók létre új Spark-fürtök a Spark 2,1 vagy 2,2 használatával a HDInsight 3,6-ben. Nem fog tudni új Spark-fürtöket létrehozni a Spark 2,3 a HDInsight 4,0 használatával. A meglévő fürtök a jelenlegi állapotukban futnak tovább, Microsoft-támogatás nélkül. 

## <a name="enable-virtual-machine-replication"></a>Virtuális gépek replikálásának engedélyezése
Azok a virtuális gépek, amelyeken nincs engedélyezve a replikáció egy másik régióban, nem rugalmasak a regionális kimaradások terén. A virtuális gépek replikálása csökkenti az Azure-régiók leállása során fellépő hátrányos üzleti hatásokat. Az Advisor észleli azokat a virtuális gépeket, amelyeken nincs engedélyezve a replikáció, és azt javasolja, hogy engedélyezze. Ha engedélyezi a replikációt, a virtuális gépek gyorsan üzembe helyezhetők egy távoli Azure-régióban. [További információ a virtuális gépek replikálásáról.](../site-recovery/azure-to-azure-quickstart.md)

## <a name="upgrade-to-the-latest-version-of-the-azure-connected-machine-agent"></a>Frissítés az Azure Connected Machine ügynök legfrissebb verziójára
Az [Azure-beli csatlakoztatott gépi ügynök](../azure-arc/servers/manage-agent.md) rendszeresen frissül a hibajavítások, a stabilitási fejlesztések és az új funkciók segítségével. Azonosítjuk azokat az erőforrásokat, amelyek nem működnek a Machine Agent legújabb verziójával, és ez az Advisor javaslat arra utal, hogy az ügynököt az Azure-beli legjobb felület legújabb verziójára frissítse.

## <a name="do-not-override-hostname-to-ensure-website-integrity"></a>A webhelyintegritás biztosítása érdekében ne írja felül a gazdagépnevet
Az Advisor azt ajánlja, hogy a Application Gateway konfigurálásakor ne felülbírálja az állomásnév felülbírálását. Ha az Application Gateway előtere esetében más tartomány van megadva, mint amit a háttérrendszer eléréséhez használ, előfordulhat, hogy a cookie-k vagy az átirányítási URL-címek hibásak lesznek. Vegye figyelembe, hogy nem minden esetben ez a helyzet, és egyes háttérrendszertípusok (például a REST API-k) általában kevésbé érzékenyek erre. Győződjön meg arról, hogy a háttérrendszer képes kezelni ezt a problémát, vagy frissítse az Application Gateway konfigurációját úgy, hogy a gazdagépnevet ne kelljen felülírni a háttérrendszer esetében. App Service használatával való használat esetén csatoljon egy egyéni tartománynevet a webalkalmazáshoz, és ne használja a *. azurewebsites.net állomásnevet a háttér felé.* [További információ az egyéni tartományról](../application-gateway/troubleshoot-app-service-redirection-app-service-url.md).

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>Magas rendelkezésre állási javaslatok elérése az Advisorban

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), majd nyissa meg az [Advisor alkalmazást](https://aka.ms/azureadvisordashboard).

2.  Az Advisor irányítópultján válassza a **magas rendelkezésre állás** lapot.

## <a name="next-steps"></a>Következő lépések

Az Advisor ajánlásaival kapcsolatos további információkért lásd:
* [Az Advisor bemutatása](advisor-overview.md)
* [Bevezetés az Advisor használatába](advisor-get-started.md)
* [Advisor-pontszám](azure-advisor-score.md)
* [Az Advisor költségekkel kapcsolatos javaslatai](advisor-cost-recommendations.md)
* [Az Advisor teljesítményével kapcsolatos javaslatok](advisor-performance-recommendations.md)
* [Advisor biztonsági javaslatok](advisor-security-recommendations.md)
* [Advisor működési kiválósági javaslatok](advisor-operational-excellence-recommendations.md)