---
title: Azure HDInsight – gyakori kérdések
description: Gyakori kérdések a HDInsight
keywords: gyakori kérdések, GYIK
author: Ramakoni1
ms.author: ramakoni
ms.service: hdinsight
ms.custom: hdinsightactive,seoapr2020
ms.topic: conceptual
ms.date: 11/20/2019
ms.openlocfilehash: 84124f33f6aa02f63c0c47a24bd7d2a71ced2d11
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101699300"
---
# <a name="azure-hdinsight-frequently-asked-questions"></a>Azure HDInsight: Gyakori kérdések

Ez a cikk az [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/)futtatásával kapcsolatos leggyakoribb kérdésekre ad választ.

## <a name="creating-or-deleting-hdinsight-clusters"></a>HDInsight-fürtök létrehozása vagy törlése

### <a name="how-do-i-provision-an-hdinsight-cluster"></a>Hogyan kiépíteni egy HDInsight-fürtöt?

A HDInsight-fürtök típusai és a létesítési módszerek áttekintését lásd: [fürtök beállítása a HDInsight-ben Apache Hadoop, Apache Spark, Apache Kafka](./hdinsight-hadoop-provision-linux-clusters.md)stb.

### <a name="how-do-i-delete-an-existing-hdinsight-cluster"></a>Hogyan törölni egy meglévő HDInsight-fürtöt?

Ha többet szeretne megtudni a fürt törléséről, ha már nincs használatban, tekintse meg a [HDInsight-fürt törlése](hdinsight-delete-cluster.md)című részt.

Próbáljon meg legalább 30 – 60 percet hagyni a létrehozási és törlési műveletek között. Ellenkező esetben a művelet sikertelen lehet a következő hibaüzenettel:

``Conflict (HTTP Status Code: 409) error when attempting to delete a cluster immediately after creation of a cluster. If you encounter this error, wait until the newly created cluster is in operational state before attempting to delete it.``

### <a name="how-do-i-select-the-correct-number-of-cores-or-nodes-for-my-workload"></a>Hogyan válassza ki a számítási feladathoz tartozó magok vagy csomópontok megfelelő számát?

A magok és egyéb konfigurációs lehetőségek megfelelő száma különböző tényezőktől függ.

További információ: HDInsight- [fürtök kapacitásának megtervezése](./hdinsight-capacity-planning.md).

### <a name="what-are-the-various-types-of-nodes-in-an-hdinsight-cluster"></a>Milyen típusú csomópontok vannak egy HDInsight-fürtben?

Lásd: [erőforrástípusok az Azure HDInsight-fürtökben](hdinsight-virtual-network-architecture.md#resource-types-in-azure-hdinsight-clusters).

### <a name="what-are-the-best-practices-for-creating-large-hdinsight-clusters"></a>Mik az ajánlott eljárások nagyméretű HDInsight-fürtök létrehozásához?

1. Javasoljuk, hogy az HDInsight-fürtöket [Egyéni AMBARI adatbázissal](./hdinsight-custom-ambari-db.md) állítsa be a fürt méretezhetőségének javítása érdekében.
2. A [Azure Data Lake Storage Gen2](./hdinsight-hadoop-use-data-lake-storage-gen2.md) használatával létrehozhat HDInsight-fürtöket, hogy kihasználhassa a nagyobb sávszélességet és a Azure Data Lake Storage Gen2 egyéb teljesítmény-jellemzőit.
3. A átjárócsomópontokkal elég nagynak kell lennie ahhoz, hogy több, ezeken a csomópontokon futó főkiszolgálót is el lehessen fogadni.
4. Bizonyos számítási feladatokhoz, mint például az interaktív lekérdezés, nagyobb Zookeeper-csomópontokra is szüksége lesz. Legalább 8 magos virtuális gépet célszerű figyelembe venni.
5. A kaptár és a Spark esetében használjon [külső Hive-metaadattár](./hdinsight-use-external-metadata-stores.md).

## <a name="individual-components"></a>Különálló összetevők

### <a name="can-i-install-additional-components-on-my-cluster"></a>Telepíthetek további összetevőket a fürtön?

Igen. További összetevők telepítéséhez vagy a fürtkonfiguráció testreszabásához használja a következőt:

- Parancsfájlok létrehozása a létrehozás során vagy után. A szkripteket parancsfájl- [művelettel](./hdinsight-hadoop-customize-cluster-linux.md)hívja meg a rendszer. A parancsfájl-művelet a Azure Portal, HDInsight Windows PowerShell-parancsmagok vagy a HDInsight .NET SDK használatával használható konfigurációs lehetőség. Ezt a konfigurációs lehetőséget a Azure Portal, a HDInsight Windows PowerShell-parancsmagok vagy a HDInsight .NET SDK használatával lehet használni.

- [HDInsight](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/) az alkalmazások telepítéséhez.

A támogatott összetevők listáját lásd: [Mik a Apache Hadoop összetevők és verziók érhetők el a HDInsight-ben?](./hdinsight-component-versioning.md)

### <a name="can-i-upgrade-the-individual-components-that-are-pre-installed-on-the-cluster"></a>Frissíthetem a fürtön előre telepített egyes összetevőket?

Ha olyan beépített összetevőket vagy alkalmazásokat frissít, amelyek előre telepítve vannak a fürtön, a Microsoft nem támogatja az eredményül kapott konfigurációt. Ezeket a rendszerkonfigurációkat a Microsoft nem tesztelte. Próbálja meg a HDInsight-fürt egy másik verzióját használni, amelyen már telepítve van az összetevő frissített verziója.

Például a struktúra frissítése önálló összetevőként nem támogatott. A HDInsight egy felügyelt szolgáltatás, és számos szolgáltatás integrálva van a Ambari-kiszolgálóval és teszteléssel. A struktúra saját maga általi frissítése a más összetevők indexelt bináris fájljait is megváltoztatja, és az összetevő-integrációs problémákat okoz a fürtön.

### <a name="can-spark-and-kafka-run-on-the-same-hdinsight-cluster"></a>A Spark és a Kafka ugyanazon a HDInsight-fürtön fut?

Nem, nem lehet futtatni Apache Kafka és Apache Spark ugyanazon a HDInsight-fürtön. Hozzon létre külön fürtöket a Kafka és a Spark számára az erőforrás-tartalommal kapcsolatos problémák elkerülése érdekében.

### <a name="how-do-i-change-timezone-in-ambari"></a>Hogyan változás időzónája a Ambari-ben?

1. Nyissa meg a Ambari webes felhasználói felületét `https://CLUSTERNAME.azurehdinsight.net` , ahol a CLUSTERNAME a fürt neve.
2. A jobb felső sarokban válassza a rendszergazda elemet | Beállítások. 

   ![Ambari-beállítások](media/hdinsight-faq/ambari-settings.png)

3. A felhasználói beállítások ablakban válassza ki az új időzónát az időzóna legördülő listából, majd kattintson a Mentés gombra.

   ![Ambari-felhasználói beállítások](media/hdinsight-faq/ambari-user-settings.png)

## <a name="metastore"></a>Metaadattár

### <a name="how-can-i-migrate-from-the-existing-metastore-to-azure-sql-database"></a>Hogyan válthatok át a meglévő metaadattár a Azure SQL Database? 

Ha SQL Serverról Azure SQL Databasera kíván áttérni, tekintse meg a következőt [: oktatóanyag: SQL Server áttelepítése önálló adatbázisba vagy készletezett adatbázisba Azure SQL Database offline módban a DMS használatával](../dms/tutorial-sql-server-to-azure-sql.md).

### <a name="is-the-hive-metastore-deleted-when-the-cluster-is-deleted"></a>Törlődik a Hive-metaadattár a fürt törlésekor?

Ez attól függ, hogy a fürt milyen metaadattár használatára van konfigurálva.

Alapértelmezett metaadattár esetén: az alapértelmezett metaadattár a fürt életciklusának részét képezi. Amikor töröl egy fürtöt, a rendszer a megfelelő metaadattár és metaadatokat is törli.

Egyéni metaadattár esetén: a metaadattár életciklusa nem kötődik a fürt életciklusához. Így a fürtöket a metaadatok elvesztése nélkül hozhatja létre és törölheti. Az olyan metaadatok, mint például a struktúra sémái a HDInsight-fürt törlése és újbóli létrehozása után is megmaradnak.

További információ: [külső metaadat-tárolók használata az Azure HDInsight-ben](hdinsight-use-external-metadata-stores.md).

### <a name="does-migrating-a-hive-metastore-also-migrate-the-default-policies-of-the-ranger-database"></a>Áttelepíti a Hive-metaadattár a Ranger-adatbázis alapértelmezett házirendjeit is?

Nem, a házirend-definíció a Ranger-adatbázisban található, így a Ranger-adatbázis migrálása áttelepíti a szabályzatot.

### <a name="can-you-migrate-a-hive-metastore-from-an-enterprise-security-package-esp-cluster-to-a-non-esp-cluster-and-the-other-way-around"></a>Áttelepíthet egy Hive-metaadattárt egy Enterprise Security Package (ESP) fürtről egy nem ESP-fürtre, és fordítva?

Igen, áttelepíthet egy Hive-metaadattár ESP-ről egy nem ESP-fürtre.

### <a name="how-can-i-estimate-the-size-of-a-hive-metastore-database"></a>Hogyan lehet megbecsülni egy Hive-metaadattár adatbázis méretét?

Hive-metaadattár a rendszer a kaptár-kiszolgáló által használt adatforrások metaadatainak tárolására szolgál. A méretre vonatkozó követelmények a kaptár-adatforrások számától és összetettségtől függenek. Ezeket az elemeket nem lehet előre megbecsülni. Az [Hive-metaadattár útmutatóban](hdinsight-use-external-metadata-stores.md#hive-metastore-guidelines)leírtaknak megfelelően az S2-es szintű használatot is elvégezheti. A szinten 50 DTU és 250 GB tárhelyet biztosít, és ha szűk keresztmetszetet lát, az adatbázis vertikális felskálázása.

### <a name="do-you-support-any-other-database-other-than-azure-sql-database-as-an-external-metastore"></a>Más adatbázisokat is támogat, mint a külső metaadattár Azure SQL Database?

Nem, a Microsoft csak a külső egyéni metaadattár Azure SQL Database támogatja.

### <a name="can-i-share-a-metastore-across-multiple-clusters"></a>Megoszthatok egy metaadattár több fürt között?

Igen, az egyéni metaadattár több fürtön is megoszthatja, ha a HDInsight azonos verzióját használják.

## <a name="connectivity-and-virtual-networks"></a>Kapcsolat és virtuális hálózatok  

### <a name="what-are-the-implications-of-blocking-ports-22-and-23-on-my-network"></a>Mik a hálózatán a 22-es és a 23-as portok blokkolásának következményei?

Ha letiltja a 22-es és a 23-as portot, nem fog SSH-hozzáféréssel rendelkezni a fürthöz. Ezeket a portokat a HDInsight szolgáltatás nem használja.

További információ a következő dokumentumokban található:

- [A HDInsight Apache Hadoop Services által használt portok](./hdinsight-hadoop-port-settings-for-services.md)

- [Biztonságos bejövő forgalom HDInsight-fürtökhöz privát végponttal rendelkező virtuális hálózaton](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)

- [HDInsight kezelése – IP-címek](./hdinsight-management-ip-addresses.md)

### <a name="can-i-deploy-an-additional-virtual-machine-within-the-same-subnet-as-an-hdinsight-cluster"></a>Telepíthetek egy további virtuális gépet ugyanazon az alhálózaton belül, mint HDInsight-fürtöt?

Igen, egy további virtuális gépet is telepíthet a HDInsight-fürttel azonos alhálózaton belül. A következő konfigurációk lehetségesek:

- Edge-csomópontok: hozzáadhat egy másik peremhálózati csomópontot a fürthöz, a következő témakörben leírtak szerint: az [üres peremhálózati csomópontok használata Apache Hadoop fürtökön a HDInsight-ben](hdinsight-apps-use-edge-node.md).

- Önálló csomópontok: hozzáadhat egy önálló virtuális gépet ugyanahhoz az alhálózathoz, és elérheti a fürtöt a virtuális gépről a privát végpont használatával `https://<CLUSTERNAME>-int.azurehdinsight.net` . További információ: a [hálózati forgalom szabályozása](./control-network-traffic.md).

### <a name="should-i-store-data-on-the-local-disk-of-an-edge-node"></a>Egy peremhálózati csomópont helyi lemezén kell tárolni az adattárolást?

Nem, a helyi lemezen tárolt adattárolás nem jó ötlet. Ha a csomópont meghibásodik, a helyileg tárolt összes adat el fog veszni. Azt javasoljuk, hogy Azure Data Lake Storage Gen2 vagy Azure Blob Storage-ban tárolja az adattárolást, vagy egy Azure Files-megosztást csatlakoztat az adattároláshoz.


### <a name="can-i-add-an-existing-hdinsight-cluster-to-another-virtual-network"></a>Hozzáadhatok egy meglévő HDInsight-fürtöt egy másik virtuális hálózathoz?

Nem. A virtuális hálózatot a kiépítés időpontjában kell megadni. Ha nincs megadva virtuális hálózat a kiépítés során, a központi telepítés olyan belső hálózatot hoz létre, amely kívülről nem érhető el. További információ: [HDInsight hozzáadása meglévő virtuális hálózathoz](hdinsight-plan-virtual-network-deployment.md#existingvnet).

## <a name="security-and-certificates"></a>Biztonság és tanúsítványok

### <a name="what-are-the-recommendations-for-malware-protection-on-azure-hdinsight-clusters"></a>Mik a kártevők elleni védelemre vonatkozó javaslatok az Azure HDInsight-fürtökön?

További információ a kártevők elleni védelemről: a [Microsoft antimalware for Azure Cloud Services és Virtual Machines](../security/fundamentals/antimalware.md).

### <a name="how-do-i-create-a-keytab-for-an-hdinsight-esp-cluster"></a>Hogyan hozzon létre egy keytab kiterjesztésű egy HDInsight ESP-fürthöz?

Hozzon létre egy Kerberos-keytab kiterjesztésű a tartomány felhasználónevéhez. Ezt a keytab kiterjesztésű később is használhatja a távoli tartományhoz csatlakoztatott fürtök hitelesítéséhez jelszó megadása nélkül. A tartománynév nagybetűs:

```shell
ktutil
ktutil: addent -password -p <username>@<DOMAIN.COM> -k 1 -e RC4-HMAC
Password for <username>@<DOMAIN.COM>: <password>
ktutil: wkt <username>.keytab
ktutil: q
```

### <a name="can-i-use-an-existing-azure-active-directory-tenant-to-create-an-hdinsight-cluster-that-has-the-esp"></a>Használhatok meglévő Azure Active Directory bérlőt egy ESP-vel rendelkező HDInsight-fürt létrehozásához?

A Azure Active Directory Domain Services (Azure AD DS) engedélyezése előtt hozzon létre egy HDInsight-fürtöt ESP használatával. A nyílt forráskódú Hadoop a Kerberos hitelesítésre támaszkodik (a OAuth szemben).

A virtuális gépek tartományhoz való csatlakoztatásához tartományvezérlő szükséges. Az Azure AD DS a felügyelt tartományvezérlő, és Azure Active Directory kiterjesztésének minősül. Az Azure AD DS biztosítja az összes Kerberos-követelményt, hogy felügyelt módon hozza létre a biztonságos Hadoop-fürtöt. A HDInsight felügyelt szolgáltatásként integrálható az Azure AD DS a biztonság biztosításához.

### <a name="can-i-use-a-self-signed-certificate-in-an-aad-ds-secure-ldap-setup-and-provision-an-esp-cluster"></a>Használhatok önaláírt tanúsítványt egy HRE-DS Secure LDAP-beállításban, és kiépíteni egy ESP-fürtöt?

A hitelesítésszolgáltató által kiállított tanúsítvány használata ajánlott. Az ESP esetében azonban az önaláírt tanúsítványok használata is támogatott. További információkért lásd:

- [Az Active Directory Domain Services engedélyezése](domain-joined/apache-domain-joined-configure-using-azure-adds.md#enable-azure-ad-ds)

- [Oktatóanyag: biztonságos LDAP konfigurálása Azure Active Directory Domain Services felügyelt tartományhoz](../active-directory-domain-services/tutorial-configure-ldaps.md)

### <a name="how-can-i-pull-login-activity-shown-in-ranger"></a>Hogyan hívhatom le a Rangerben látható bejelentkezési tevékenységet?

A naplózási követelményekhez a Microsoft javasolja, hogy engedélyezze az Azure Monitor-naplókat a [HDInsight-fürtök figyelése Azure monitor naplók használatával](./hdinsight-hadoop-oms-log-analytics-tutorial.md)című témakörben leírtak

### <a name="can-i-disable-clamscan-on-my-cluster"></a>Le lehet tiltani `Clamscan` a fürtön?

`Clamscan` a a HDInsight-fürtön futó víruskereső szoftver, amelyet az Azure Security (azsecd) használ a fürtök vírusfertőzés elleni védelme érdekében. A Microsoft nyomatékosan javasolja, hogy a felhasználók ne végezzenek változást az alapértelmezett `Clamscan` konfigurációban.

Ez a folyamat nem zavarja vagy nem hajtja végre a más folyamatokból származó ciklusokat. Ez mindig más folyamatnak fog történni. A CPU-tüskéket `Clamscan` csak akkor kell látni, ha a rendszer üresjáratban van.  

Olyan helyzetekben, amelyekben meg kell határoznia az ütemtervet, a következő lépéseket hajthatja végre:

1. Tiltsa le az automatikus végrehajtást a következő parancs használatával:
   
  sudo `usr/local/bin/azsecd config -s clamav -d Disabled` sudo szolgáltatás azsecd újraindítása 
   
1. Adjon hozzá egy cron-feladatot, amely a következő parancsot futtatja root-ként:
   
   `/usr/local/bin/azsecd manual -s clamav`

A cron-feladatok beállításával és futtatásával kapcsolatos további információkért lásd: [Hogyan cron-feladatok beállítása](https://askubuntu.com/questions/2368/how-do-i-set-up-a-cron-job)?

### <a name="why-is-llap-available-on-spark-esp-clusters"></a>Miért érhető el a LLAP a Spark ESP-fürtökön?
A LLAP biztonsági okokból (Apache Ranger) van engedélyezve, nem pedig a teljesítmény. Használjon nagyobb csomópontos virtuális gépeket a LLAP erőforrás-használatának (például minimális D13V2) kielégítéséhez. 

### <a name="how-can-i-add-additional-aad-groups-after-creating-an-esp-cluster"></a>Hogyan adhatok hozzá további HRE-csoportokat az ESP-fürt létrehozása után?
A cél kétféleképpen valósítható meg: 1 – újból létrehozhatja a fürtöt, és hozzáadhatja a további csoportot a fürt létrehozásakor. Ha hatókörön belüli szinkronizálást használ a HRE-DS-ben, győződjön meg arról, hogy a B csoport szerepel a hatókörön belüli szinkronizálásban.
2 – a csoport hozzáadása az ESP-fürt létrehozásához használt előző csoport beágyazott alcsoportjaként. Ha például egy csoporttal hozta létre az ESP-fürtöt `A` , később a csoportba `B` beágyazott alcsoportként, `A` majd körülbelül egy óra elteltével szinkronizálja, és automatikusan elérhetővé válik a fürtben. 

## <a name="storage"></a>Tárolás

### <a name="can-i-add-an-azure-data-lake-storage-gen2-to-an-existing-hdinsight-cluster-as-an-additional-storage-account"></a>Hozzáadhatok Azure Data Lake Storage Gen2 egy meglévő HDInsight-fürthöz további Storage-fiókként?

Nem, jelenleg nem lehet Azure Data Lake Storage Gen2 Storage-fiókot hozzáadni egy olyan fürthöz, amely elsődleges tárolóként rendelkezik blob Storage-tárolóval. További információ: a [tárolási lehetőségek összehasonlítása](hdinsight-hadoop-compare-storage-options.md).

### <a name="how-can-i-find-the-currently-linked-service-principal-for-a-data-lake-storage-account"></a>Hogyan találhatom meg a jelenleg társított egyszerű szolgáltatást egy Data Lake Storage-fiókhoz?

A beállításokat a fürt tulajdonságai között, a Azure Portalban találhatja meg **Data Lake Storage Gen1 hozzáférés** lehetőségnél. További információ: a [fürt beállításának ellenőrzése](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md#verify-cluster-set-up).
 
### <a name="how-can-i-calculate-the-usage-of-storage-accounts-and-blob-containers-for-my-hdinsight-clusters"></a>Hogyan számítható ki a Storage-fiókok és a blob-tárolók használata a HDInsight-fürtökhöz?

Hajtsa végre az alábbi műveletek egyikét:

- [A PowerShell használata](../storage/scripts/storage-blobs-container-calculate-size-powershell.md)

- A/User/Hive/. méretének megkeresése ** A HDInsight-fürt Kuka/mappája a következő parancssor használatával:
  
  `hdfs dfs -du -h /user/hive/.Trash/`

### <a name="how-can-i-set-up-auditing-for-my-blob-storage-account"></a>Hogyan állíthatok be naplózást a blob Storage-fiókomhoz?

A blob Storage-fiókok naplózásához a [Azure Portal a Storage-fiók figyelése](../storage/common/manage-storage-analytics-logs.md)című eljárással konfigurálhatja a figyelést. Egy HDFS-naplózási napló csak a helyi HDFS fájlrendszer (hdfs://mycluster) naplózási információit tartalmazza.  Nem tartalmazza a távoli tárterületen végzett műveleteket.

### <a name="how-can-i-transfer-files-between-a-blob-container-and-an-hdinsight-head-node"></a>Hogyan vihetők át fájlok egy blob-tároló és egy HDInsight-csomópont között?

Futtasson egy, az alábbi rendszerhéj-parancsfájlhoz hasonló parancsfájlt a fő csomóponton:

```shell
for i in cat filenames.txt
do
   hadoop fs -get $i <local destination>
done
```
 
> [!NOTE]
> A fájl *filenames.txt* a blob-tárolókban található fájlok abszolút elérési útját fogja tartalmazni.
 
### <a name="are-there-any-ranger-plugins-for-storage"></a>Vannak-e a Storage-beli Ranger-beépülő modulok?

Jelenleg nem létezik Ranger beépülő modul a blob Storage-hoz és Azure Data Lake Storage Gen1 vagy Gen2. Az ESP-fürtök esetében Azure Data Lake Storaget kell használnia. A HDFS-eszközök használatával a fájlrendszer szintjén legalább a részletes kiőrlési engedélyeket manuálisan is beállíthatja. Emellett a Azure Data Lake Storage használatakor az ESP-fürtök a fájlrendszer-hozzáférés-vezérlést Azure Active Directory használatával hajtják végre a fürt szintjén. 

Az adathozzáférési szabályzatokat a Azure Storage Explorer használatával rendelheti hozzá a felhasználók biztonsági csoportjaihoz. További információkért lásd:

- [Hogyan az Azure AD-felhasználók számára a kaptár vagy más szolgáltatások használatával történő adatlekérdezésre vonatkozó engedélyeket Data Lake Storage Gen2.](hdinsight-hadoop-use-data-lake-storage-gen2.md#how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services)
- [Fájl-és könyvtár-szintű engedélyek beállítása Azure Storage Explorer és Azure Data Lake Storage Gen2 használatával](../storage/blobs/data-lake-storage-explorer.md)

### <a name="can-i-increase-hdfs-storage-on-a-cluster-without-increasing-the-disk-size-of-worker-nodes"></a>Megnövelhető a fürt HDFS-tárolója a munkavégző csomópontok lemezének méretének növelése nélkül?

Nem. A munkavégző csomópontok lemezének mérete nem növelhető. Így az egyetlen lehetőség a lemez méretének növelésére, hogy eldobja a fürtöt, és újra létrehozza azt nagyobb feldolgozó virtuális gépekkel. Ne használja a HDFS-t a HDInsight-adatai tárolásához, mert a fürt törlése után az adatai törlődnek. Ehelyett tárolja adatait az Azure-ban. A fürt méretezése további kapacitást is hozzáadhat a HDInsight-fürthöz.

## <a name="edge-nodes"></a>Határcsomópontok

### <a name="can-i-add-an-edge-node-after-the-cluster-has-been-created"></a>Hozzá lehet adni egy Edge-csomópontot a fürt létrehozása után?

Lásd: [üres peremhálózati csomópontok használata Apache Hadoop fürtökön a HDInsight-ben](hdinsight-apps-use-edge-node.md).

### <a name="how-can-i-connect-to-an-edge-node"></a>Hogyan lehet csatlakozni egy peremhálózati csomóponthoz?

Az Edge-csomópont létrehozása után az SSH használatával csatlakozhat a 22-es porthoz. A peremhálózati csomópont nevét a fürt portálján találja. A nevek általában *-ED* végződéssel rendelkeznek.

### <a name="why-are-persisted-scripts-not-running-automatically-on-newly-created-edge-nodes"></a>Miért nem futnak automatikusan az újonnan létrehozott Edge-csomópontokon a megőrzött parancsfájlok?

A megőrzött parancsfájlok segítségével testre szabhatja a fürthöz a skálázási műveletek révén hozzáadott új munkavégző csomópontokat. A megőrzött parancsfájlok nem vonatkoznak az Edge-csomópontokra.

## <a name="rest-api"></a>REST API

### <a name="what-are-the-rest-api-calls-to-pull-a-tez-query-view-from-the-cluster"></a>Mik a REST API hívások a TEZ lekérdezési nézetének lekéréséhez a fürtből?

A szükséges információk JSON formátumban való lekéréséhez a következő REST-végpontok használhatók. A kérések létrehozásához használja az alapszintű hitelesítési fejléceket.

- `Tez Query View`: *https: \/ / \<cluster name> . azurehdinsight.net/ws/v1/Timeline/HIVE_QUERY_ID/*
- `Tez Dag View`: *https: \/ / \<cluster name> . azurehdinsight.net/ws/v1/Timeline/TEZ_DAG_ID/*

### <a name="how-do-i-retrieve-the-configuration-details-from-hdi-cluster-by-using-an-azure-active-directory-user"></a>Hogyan lekérni a konfigurációs adatokat a HDI-fürtből egy Azure Active Directory felhasználó használatával?

Ha a HRE-felhasználóval egyeztetni szeretné a megfelelő hitelesítési jogkivonatokat, az átjárót a következő formátumban kell megtennie:

* https:// `<cluster dnsname>` . azurehdinsight.NET/API/v1/Clusters/testclusterdem/stack_versions/1/repository_versions/1 

### <a name="how-do-i-use-ambari-restful-api-to-monitor-yarn-performance"></a>Hogyan a Ambari REST-API-t használja a fonal teljesítményének figyeléséhez?

Ha a cURL parancsot ugyanabban a virtuális hálózatban vagy egy társ virtuális hálózaton hívja meg, a parancs a következőket teszi:

```curl
curl -u <cluster login username> -sS -G
http://<headnodehost>:8080/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
```
 
Ha a parancsot a virtuális hálózaton kívülről vagy egy nem társ virtuális hálózatról hívja meg, a parancs formátuma a következő:

- Nem ESP-fürt esetén:
  
  ```curl
  curl -u <cluster login username> -sS -G 
  https://<ClusterName>.azurehdinsight.net/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
  ```

- ESP-fürt esetén:
  
  ```curl
  curl -u <cluster login username>-sS -G 
  https://<ClusterName>.azurehdinsight.net/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
  ```

> [!NOTE]
> A curl kérni fogja a jelszó megadását. Érvényes jelszót kell megadnia a fürt bejelentkezési felhasználónevéhez.

## <a name="billing"></a>Számlázás

### <a name="how-much-does-it-cost-to-deploy-an-hdinsight-cluster"></a>Mennyibe kerül a HDInsight-fürt üzembe helyezése?

A számlázással kapcsolatos díjszabásról és gyakori kérdésekért lásd az [Azure HDInsight díjszabását](https://azure.microsoft.com/pricing/details/hdinsight/) ismertető oldalt.

### <a name="when-does-hdinsight-billing-start--stop"></a>Mikor kezdődik a HDInsight-számlázás & leállítása?

A HDInsight-fürt számlázása a fürt létrehozásakor kezdődik és a fürt törlésekor fejeződik be. A számlázás percenként történik.

### <a name="how-do-i-cancel-my-subscription"></a>Hogyan lemondani az előfizetést?

További információ az előfizetés lemondásáról: [Az Azure-előfizetés megszakítása](../cost-management-billing/manage/cancel-azure-subscription.md).

### <a name="for-pay-as-you-go-subscriptions-what-happens-after-i-cancel-my-subscription"></a>Az utólagos elszámolású előfizetések esetén mi történik az előfizetés megszakítása után?

Az előfizetéssel kapcsolatos további információkért lásd: [Mi történik az előfizetés megszakítása után?](../cost-management-billing/manage/cancel-azure-subscription.md)

## <a name="hive"></a>Hive

### <a name="why-does-the-hive-version-appear-as-121000-instead-of-21-in-the-ambari-ui-even-though-im-running-an-hdinsight-36-cluster"></a>Miért jelenik meg a struktúra verziója 2,1 helyett 1.2.1000-ként a Ambari felhasználói felületen, még ha HDInsight 3,6-fürtöt futtatok?

Bár a Ambari felhasználói felületén csak 1,2 jelenik meg, a HDInsight 3,6 a kaptár 1,2 és a kaptár 2,1-t is tartalmazza.

## <a name="other-faq"></a>Egyéb gyakori kérdések

### <a name="what-does-hdinsight-offer-for-real-time-stream-processing-capabilities"></a>Mit kínál a HDInsight a valós idejű adatfolyam-feldolgozási képességekhez?

További információ az adatfolyam-feldolgozás integrációs képességeiről: [stream Processing Technology kiválasztása az Azure-ban](/azure/architecture/data-guide/technology-choices/stream-processing).

### <a name="is-there-a-way-to-dynamically-kill-the-head-node-of-the-cluster-when-the-cluster-is-idle-for-a-specific-period"></a>Van lehetőség a fürt fő csomópontjának dinamikus leölésére, ha a fürt egy adott időszakra tétlen?

Ez a művelet nem hajtható végre HDInsight-fürtökkel. Ezeket a forgatókönyveket Azure Data Factory is használhatja.

### <a name="what-compliance-offerings-does-hdinsight-offer"></a>Milyen megfelelőségi ajánlatokat kínál a HDInsight?

A megfelelőségi információkat a [Microsoft adatvédelmi központjának](https://www.microsoft.com/trust-center) és a [Microsoft Azure megfelelőségének áttekintése](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)című témakörben tekintheti meg.