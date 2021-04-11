---
title: Lassú vagy sikertelen feladat hibáinak megoldása az Azure HDInsight-fürtön
description: Lassú vagy sikertelen feladat diagnosztizálása és megoldása egy Azure HDInsight-fürtön.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: 660c80a471739f7dc0163e915f45e3a067e1e4b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104871962"
---
# <a name="troubleshoot-a-slow-or-failing-job-on-a-hdinsight-cluster"></a>Egy HDInsight-fürt lassú vagy meghibásodott feladatának hibaelhárítása

Ha egy alkalmazás egy HDInsight-fürtön lévő, lassú vagy hibás hibát okozó alkalmazást használ, több hibaelhárítási lehetőség is van. Ha a feladatok a vártnál hosszabb ideig tartanak, vagy általánosságban a lassú válaszadási idő látható, előfordulhat, hogy a fürtből a leállási hibák, például a fürt által futtatott szolgáltatások futnak. Ezeknek a lassulásoknak a leggyakoribb oka azonban nem elegendő skálázás. Amikor új HDInsight-fürtöt hoz létre, válassza ki a megfelelő [virtuálisgép-méreteket](hdinsight-supported-node-configuration.md).

Lassú vagy sikertelen fürtök diagnosztizálásához gyűjtsön információkat a környezet minden aspektusáról, például a társított Azure-szolgáltatásokról, a fürtkonfiguráció és a feladat-végrehajtási információkról. A hasznos diagnosztika a hiba állapotának egy másik fürtön való újbóli előállítására szolgál.

* 1. lépés: a probléma adatainak összegyűjtése.
* 2. lépés: a HDInsight-fürt környezetének ellenőrzése.
* 3. lépés: a fürt állapotának megtekintése.
* 4. lépés: a környezeti verem és a verziók áttekintése.
* 5. lépés: vizsgálja meg a fürt naplófájljait.
* 6. lépés: a konfigurációs beállítások megadása.
* 7. lépés: a hiba ismételt előállítása egy másik fürtön.

## <a name="step-1-gather-data-about-the-issue"></a>1. lépés: adatok összegyűjtése a hibával kapcsolatban

A HDInsight számos eszközt biztosít a fürtökkel kapcsolatos problémák azonosításához és elhárításához. A következő lépések végigvezetik az eszközökön, és javaslatokat nyújtanak a probléma megoldásához.

### <a name="identify-the-problem"></a>A probléma azonosítása

A probléma azonosításához vegye figyelembe a következő kérdéseket:

* Mit várhatok? Mi történt helyette?
* Mennyi ideig tart a folyamat futtatása? Mennyi ideig kell futtatni?
* Mindig lassan futnak a feladatok a fürtön? Gyorsabban futnak egy másik fürtön?
* Mikor történt a probléma első előfordulása? Milyen gyakran történt ez azóta?
* Bármi módosult a saját fürt konfigurációjában?

### <a name="cluster-details"></a>Fürt részletei

A fürt fontos adatai a következők:

* Fürt neve.
* Fürt régiója – a [régiók kiesésének](https://azure.microsoft.com/status/)keresése.
* HDInsight-fürt típusa és verziója.
* A fő-és munkavégző csomópontokhoz megadott HDInsight-példányok típusa és száma.

A Azure Portal megadhatja ezeket az információkat:

:::image type="content" source="./media/hdinsight-troubleshoot-failed-cluster/hdi-azure-portal-info.png" alt-text="HDInsight Azure Portal információk":::

Használhatja az [Azure CLI](/cli/azure/)-t is:

```azurecli
az hdinsight list --resource-group <ResourceGroup>
az hdinsight show --resource-group <ResourceGroup> --name <ClusterName>
```

Egy másik lehetőség a PowerShell használata. További információ:  [Apache Hadoop-fürtök kezelése a HDInsight-ben Azure PowerShell](hdinsight-administer-use-powershell.md).

## <a name="step-2-validate-the-hdinsight-cluster-environment"></a>2. lépés: a HDInsight-fürt környezetének ellenőrzése

Minden HDInsight-fürt különböző Azure-szolgáltatásokra támaszkodik, valamint olyan nyílt forráskódú szoftvereken, mint az Apache HBase és a Apache Spark. A HDInsight-fürtök más Azure-szolgáltatások, például Azure-beli virtuális hálózatok hívását is meghívhatják.  A fürt meghibásodását a fürtön futó szolgáltatások vagy külső szolgáltatások okozhatják.  A fürtszolgáltatás konfigurációjának módosítása azt is okozhatja, hogy a fürt meghibásodik.

### <a name="service-details"></a>Szolgáltatás részletei

* Keresse meg a nyílt forráskódú kódtár kiadási verzióit.
* Keresse meg az [Azure-szolgáltatások kimaradásait](https://azure.microsoft.com/status/).  
* Keresse meg az Azure-szolgáltatások használati korlátait. 
* Keresse meg az Azure Virtual Network alhálózati konfigurációt.  

### <a name="view-cluster-configuration-settings-with-the-ambari-ui"></a>A Ambari felhasználói felületén megtekintheti a fürt konfigurációs beállításait

Az Apache Ambari egy HDInsight-fürt felügyeletét és figyelését teszi lehetővé webes felhasználói felületen és REST API. A Ambari a Linux-alapú HDInsight-fürtökön található. Válassza ki a **fürt irányítópultjának** paneljét a Azure Portal HDInsight oldalon.  A **HDInsight-fürt irányítópultja** ablaktáblán kattintson a Ambari felhasználói felületének megnyitásához, és adja meg a fürt bejelentkezési hitelesítő adatait.  

:::image type="content" source="./media/hdinsight-troubleshoot-failed-cluster/apache-ambari-overview.png" alt-text="Apache Ambari-irányítópult – áttekintés":::

A szolgáltatási nézetek listájának megnyitásához válassza a **Ambari nézetek** elemet a Azure Portal oldalon.  Ez a lista a telepített könyvtáraktól függ. Például megtekintheti a FONALak üzenetsor-kezelőjét, a kaptár nézetet és a TEZ nézetet.  Válasszon ki egy szolgáltatási hivatkozást a konfiguráció és a szolgáltatás adatainak megtekintéséhez.

#### <a name="check-for-azure-service-outages"></a>Azure-szolgáltatás kimaradásainak ellenőrzése

A HDInsight számos Azure-szolgáltatásra támaszkodik. Virtuális kiszolgálókat futtat az Azure HDInsight, tárolja az Azure Blob Storage-ban vagy a Azure Data Lake Storageban tárolt adatfájlokat és parancsfájlokat, és indexeli a naplófájlokat az Azure Table Storage-ban. A szolgáltatásokkal kapcsolatos fennakadások, bár ritkán fordulnak elő, problémákat okozhatnak a HDInsight. Ha váratlan munkalassítás vagy meghibásodás történt a fürtben, tekintse meg az [Azure állapot-irányítópultját](https://azure.microsoft.com/status/). Az egyes szolgáltatások állapota régiónként van felsorolva. Tekintse meg a fürt régióját, valamint a kapcsolódó szolgáltatásokhoz tartozó régiókat is.

#### <a name="check-azure-service-usage-limits"></a>Az Azure-szolgáltatások használati korlátainak keresése

Ha nagyméretű fürtöt indít, vagy egyszerre sok fürtöt indított el, a fürt meghiúsulhat, ha túllépte az Azure-szolgáltatási korlátot. A szolgáltatás korlátai az Azure-előfizetéstől függően változnak. További információk: [Az Azure-előfizetések és -szolgáltatások korlátozásai, kvótái és megkötései](../azure-resource-manager/management/azure-subscription-service-limits.md).
Kérheti, hogy a Microsoft növelje a rendelkezésre álló HDInsight-erőforrások (például a virtuálisgép-magok és a virtuálisgép-példányok) számát a [Resource Manager Core kvóta növelésére vonatkozó kéréssel](../azure-portal/supportability/resource-manager-core-quotas-request.md).

#### <a name="check-the-release-version"></a>A kiadás verziójának keresése

Hasonlítsa össze a fürt verzióját a legújabb HDInsight-kiadással. Minden HDInsight-kiadás olyan javításokat tartalmaz, mint például az új alkalmazások, funkciók, javítások és hibajavítások. Előfordulhat, hogy a fürtöt érintő probléma megoldódott a legújabb verzióban. Ha lehetséges, futtassa újra a fürtöt a HDInsight legújabb verziójával és a társított kódtárak, például az Apache HBase, a Apache Spark és egyebek használatával.

#### <a name="restart-your-cluster-services"></a>A fürtszolgáltatások újraindítása

Ha a fürtben lassulást tapasztal, érdemes lehet a szolgáltatások újraindítását a Ambari felhasználói felületén vagy a klasszikus Azure CLI-n keresztül. Előfordulhat, hogy a fürt átmeneti hibákba ütközik, és az újraindítással a lehető leggyorsabb módszer a környezet stabilizálására és a teljesítmény javítására.

## <a name="step-3-view-your-clusters-health"></a>3. lépés: a fürt állapotának megtekintése

A HDInsight-fürtök a virtuálisgép-példányokon futó különböző típusú csomópontokból állnak. Az egyes csomópontok az erőforrás-éhezés, a hálózati kapcsolati problémák és a fürt lelassulását okozó egyéb problémák figyelésére használhatók. Minden fürt két fő csomópontot tartalmaz, és a legtöbb fürt típusa feldolgozói és peremhálózati csomópontok kombinációját tartalmazza. 

Az egyes fürtök típusait használó különböző csomópontok leírását lásd: [fürtök beállítása a HDInsight-ben Apache Hadoop, Apache Spark, Apache Kafka és sok más](hdinsight-hadoop-provision-linux-clusters.md).

Az alábbi szakaszok azt ismertetik, hogyan ellenőrizhető az egyes csomópontok és a teljes fürt állapota.

### <a name="get-a-snapshot-of-the-cluster-health-using-the-ambari-ui-dashboard"></a>Pillanatkép készítése a fürt állapotáról a Ambari felhasználói felületének irányítópultja segítségével

A [Ambari felhasználói felület irányítópultja](#view-cluster-configuration-settings-with-the-ambari-ui) ( `https://<clustername>.azurehdinsight.net` ) áttekintést nyújt a fürt állapotáról, például a rendelkezésre állásról, a memóriáról, a hálózatról és a CPU-használatról, a HDFS és így tovább. A Ambari hosts (gazdagépek) szakasza segítségével megtekintheti az erőforrásokat a gazdagép szintjén. A szolgáltatások leállítására és újraindítására is lehetőség van.

### <a name="check-your-webhcat-service"></a>A Webhcaten szolgáltatás ellenõrzése

A Apache Hive, Apache Pig vagy Apache Sqoop feladatok egyik gyakori forgatókönyve, hogy a hiba a [webhcaten](hdinsight-hadoop-templeton-webhcat-debug-errors.md) (vagy *Templeton*) szolgáltatás meghibásodása. A Webhcaten egy REST-felület a távoli feladatok végrehajtásához, mint például a kaptár, a Pig, a SCOOP és a MapReduce. A Webhcaten lefordítja a beküldési kérelmeket Apache Hadoop fonal-alkalmazásokba, és visszaadja a fonal alkalmazás állapotból származtatott állapotot.  A következő szakaszok az általános Webhcaten HTTP-állapotkódok leírását ismertetik.

#### <a name="badgateway-502-status-code"></a>BadGateway (502 állapotkód)

Ez a kód az átjáró-csomópontok általános üzenete, és a leggyakoribb meghibásodási állapotkódok. Ennek egyik lehetséges oka az, hogy az Webhcaten szolgáltatás leállt az aktív fő csomóponton. Ennek a lehetőségnek a megkereséséhez használja a következő CURL-parancsot:

```bash
curl -u admin:{HTTP PASSWD} https://{CLUSTERNAME}.azurehdinsight.net/templeton/v1/status?user.name=admin
```

A Ambari megjelenít egy riasztást, amely megjeleníti azokat a gazdagépeket, amelyeken a Webhcaten szolgáltatás le van kapcsolva. A Webhcaten szolgáltatás biztonsági mentését úgy teheti meg, hogy újraindítja a szolgáltatást a gazdagépén.

:::image type="content" source="./media/hdinsight-troubleshoot-failed-cluster/restart-webhcat-server.png" alt-text="Apache Ambari – Webhcaten-kiszolgáló újraindítása":::

Ha a Webhcaten-kiszolgáló továbbra sem jelenik meg, akkor ellenőrizze az operatív naplóban a hibaüzeneteket. További részletekért lásd a `stderr` `stdout` csomóponton hivatkozott és fájlokat.

#### <a name="webhcat-times-out"></a>Webhcaten időtúllépés

An méretű HDInsight átjáró időtúllépési válaszokat küld, amelyek két percnél hosszabb ideig tartanak vissza `502 BadGateway` . A Webhcaten lekérdezi a fonal-szolgáltatásokat a feladatok állapotára vonatkozóan, és ha a szál a válaszadáshoz több mint két percet vesz igénybe, akkor a kérés időtúllépést okozhat.

Ebben az esetben tekintse át a következő naplókat a `/var/log/webhcat` címtárban:

* a **webhcaten. log** a log4j napló, amelyre a kiszolgáló írja a naplókat
* a **webhcat-Console. log** a kiszolgáló StdOut-a indításakor
* a **webhcat-Console-error. log** a kiszolgálói folyamat stderr

> [!NOTE]  
> Mindegyiket `webhcat.log` naponta, a megnevezett fájlok létrehozásával összesítjük `webhcat.log.YYYY-MM-DD` . Válassza ki a vizsgálni kívánt időtartományhoz tartozó megfelelő fájlt.

A következő szakaszok a Webhcaten időtúllépésének lehetséges okait ismertetik.

##### <a name="webhcat-level-timeout"></a>Webhcaten-szint időtúllépése

Ha a Webhcaten terhelés alatt van, és több mint 10 nyitott szoftvercsatorna van, akkor hosszabb időt vesz igénybe az új szoftvercsatorna-kapcsolatok létrehozása, ami időtúllépést okozhat. A Webhcaten és onnan érkező hálózati kapcsolatok listázásához használja `netstat`  az aktuális aktív átjárócsomóponthoz:

```bash
netstat | grep 30111
```

30111 a Webhcaten port figyeli a következőt:. A megnyitott szoftvercsatornák száma nem lehet kisebb, mint 10.

Ha nincsenek nyitott szoftvercsatornák, az előző parancs nem eredményez eredményt. A következő paranccsal ellenőrizze, hogy az Templeton működik-e, és hogy figyeli-e a 30111-es portot:

```bash
netstat -l | grep 30111
```

##### <a name="yarn-level-timeout"></a>FONAL szintjének időtúllépése

A Templeton meghívja a FONALat a feladatok futtatásához, és a Templeton és a fonal közötti kommunikáció időtúllépést okozhat.

A fonal szintjén két típusú időkorlát létezik:

1. A FONALas feladatok elküldése elég hosszú időt vehet igénybe az időtúllépés miatt.

    Ha megnyitja a `/var/log/webhcat/webhcat.log` naplófájlt, és megkeresi a "várólistán lévő feladatokhoz" kifejezést, több olyan bejegyzést is láthat, ahol a végrehajtási idő túl hosszú (>2000 MS), és a várakozási idő növelését jelző bejegyzéseket jelenít meg.

    Az üzenetsor-kezelési feladatok továbbra is növekednek, mert az új feladatok elküldésének sebessége meghaladja a régi feladatok befejezési sebességét. Ha a szál memóriája 100%-ban használatban van, a *joblauncher-várólista* már nem tud az *alapértelmezett várólistából* felvenni a kapacitást. Ezért nem lehet több új feladatot fogadni a joblauncher-várólistába. Ez a viselkedés azt okozhatja, hogy a várakozási idő hosszabb és hosszabb lesz, ami időtúllépési hibát okoz, amelyet általában sok más is követ.

    Az alábbi képen a joblauncher-várólista a 714,4%-os túlhasználatú helyen látható. Ez akkor fogadható el, ha az alapértelmezett várólistában továbbra is szabad kapacitás áll rendelkezésre a kölcsönzéshez. Ha azonban a fürt teljes mértékben használatban van, és a szál memóriája 100%-os kapacitású, az új feladatoknak várniuk kell, ami végül időtúllépéseket okoz.

    :::image type="content" source="./media/hdinsight-troubleshoot-failed-cluster/hdi-job-launcher-queue.png" alt-text="HDInsight Job Launcher üzenetsor nézet":::

    A probléma kétféleképpen oldható meg: csökkentse a beküldött új feladatok sebességét, vagy növelje a régi feladatok felhasználásának sebességét a fürt skálázásával.

2. A FONALak feldolgozása hosszú időt is igénybe vehet, ami időtúllépést okozhat.

    * Az összes feladat listázása: ez egy időigényes hívás. Ez a hívás enumerálja az alkalmazásokat a fonal erőforráskezelő, és minden befejezett alkalmazás esetében lekéri az állapotot a fonal JobHistoryServer. Ha a feladatok száma nagyobb, a hívás időtúllépést okozhat.

    * Hét napnál régebbi feladatok listázása: a HDInsight fonal JobHistoryServer úgy van konfigurálva, hogy hét napig (érték) őrizze meg a Befejezett feladatok adatait `mapreduce.jobhistory.max-age-ms` . A kiürített feladatok számbavételére tett kísérlet időtúllépést eredményez.

A problémák diagnosztizálásához:

1. Az UTC-időtartomány meghatározása a hibák megoldásához
2. Válassza ki a megfelelő `webhcat.log` fájl (oka) t
3. A FIGYELMEZTETÉSi és a HIBAÜZENETek megtekintése ebben az időszakban

#### <a name="other-webhcat-failures"></a>Egyéb Webhcaten hibák

1. HTTP-állapotkód 500

    A legtöbb esetben, ahol a Webhcaten a 500-as értéket adja vissza, a hibaüzenet a hiba részleteit tartalmazza. Ellenkező esetben tekintse át `webhcat.log` a figyelmeztetési és a hibaüzeneteket.

2. Sikertelen feladatok

    Előfordulhatnak olyan esetek, amikor a Webhcaten folytatott interakciók sikeresek, de a feladatok meghiúsulnak.

    A Templeton a-ben gyűjti a Feladatkezelő kimenetét `stderr` `statusdir` , ami gyakran hasznos a hibaelhárításhoz. `stderr` a tényleges lekérdezés fonal-alkalmazási azonosítóját tartalmazza.

## <a name="step-4-review-the-environment-stack-and-versions"></a>4. lépés: a környezeti verem és a verziók áttekintése

A Ambari felhasználói felületi **verem és verziója** lapon információkat biztosít a fürtszolgáltatás-konfigurációról és a szolgáltatás korábbi verzióiról.  A Hadoop nem megfelelő verziója lehet a fürt meghibásodásának oka.  A Ambari felhasználói felületén válassza a **rendszergazda** menüt, majd a  **Stacks és a Versions** elemet.  A szolgáltatás verziószámával kapcsolatos információk megtekintéséhez válassza a **verziók** fület a lapon:

:::image type="content" source="./media/hdinsight-troubleshoot-failed-cluster/ambari-stack-versions.png" alt-text="Apache Ambari stack és verziók":::

## <a name="step-5-examine-the-log-files"></a>5. lépés: a naplófájlok vizsgálata

A HDInsight-fürtöt alkotó számos szolgáltatásból és összetevőből számos típusú napló jön létre. A [webhcaten-naplófájlok](#check-your-webhcat-service) korábban vannak leírva. A következő szakaszokban leírtak szerint számos más hasznos naplófájl is megvizsgálható a fürttel kapcsolatos problémák szűkítéséhez.

* A HDInsight-fürtök több csomópontból állnak, amelyek többsége elküldött feladatok futtatására szolgál. A feladatok párhuzamosan futnak, de a naplófájlok csak lineárisan jelenítik meg az eredményeket. A HDInsight új feladatokat hajt végre, és leállítja azokat, amelyek nem teljesítik az első lépéseket. A program minden tevékenységet naplóz a `stderr` és a `syslog` fájlok között.

* A parancsfájl műveleti naplófájljai hibákat vagy váratlan konfigurációs változásokat mutatnak a fürt létrehozási folyamata során.

* A Hadoop-naplók a hibákat tartalmazó lépés részeként indított Hadoop-feladatokat azonosítják.

### <a name="check-the-script-action-logs"></a>A parancsfájl műveleti naplóinak keresése

A HDInsight [parancsfájl-műveletek](hdinsight-hadoop-customize-cluster-linux.md) futtatása manuálisan, vagy ha meg van adva a fürtön. A parancsfájl-műveletek segítségével például további szoftvereket telepíthet a fürtre, vagy megváltoztathatja a konfigurációs beállításokat az alapértelmezett értékek alapján. A parancsfájl-műveleti naplók ellenőrzésével betekintést nyerhet a fürt beállítása és konfigurálása során felmerülő hibákba.  A parancsfájl-műveletek állapotát az Ambari felhasználói felületén található **Ops** gombra kattintva vagy az alapértelmezett Storage-fiókból való eléréssel tekintheti meg.

A parancsfájl műveleti naplói a `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE` címtárban találhatók.

### <a name="view-hdinsight-logs-using-ambari-quick-links"></a>HDInsight-naplók megtekintése Ambari-Gyorshivatkozások használatával

A HDInsight Ambari felhasználói felülete számos **gyors hivatkozás** szakaszt tartalmaz.  A HDInsight-fürt egy adott szolgáltatásához tartozó naplóbejegyzések eléréséhez nyissa meg a fürt Ambari felhasználói felületét, majd a bal oldali listából válassza ki a szolgáltatás hivatkozását. Válassza a **gyors hivatkozások** legördülő menüt, majd a HDInsight csomópontot, majd válassza ki a kapcsolódó naplóhoz tartozó hivatkozást.

A HDFS-naplók esetében például:

:::image type="content" source="./media/hdinsight-troubleshoot-failed-cluster/apache-ambari-quick-links.png" alt-text="Ambari gyors hivatkozásai":::

### <a name="view-hadoop-generated-log-files"></a>Hadoop által generált naplófájlok megtekintése

An méretű HDInsight a fürt az Azure-táblákba és az Azure Blob Storage-ba írt naplókat hoz létre. A fonal saját végrehajtási naplókat hoz létre. További információ: a [HDInsight-fürt naplóinak kezelése](hdinsight-log-management.md#access-the-hadoop-log-files).

### <a name="review-heap-dumps"></a>Halom-memóriaképek áttekintése

A kupac-memóriaképek az alkalmazás memóriájának pillanatképét tartalmazzák, beleértve a változók értékeit is, amelyek a futásidőben felmerülő problémák diagnosztizálásához hasznosak. További információkért lásd: [heap-memóriaképek engedélyezése Apache Hadoop-szolgáltatásokhoz Linux-alapú HDInsight](hdinsight-hadoop-collect-debug-heap-dump-linux.md).

## <a name="step-6-check-configuration-settings"></a>6. lépés: konfigurációs beállítások keresése

A HDInsight-fürtök előre konfigurálva vannak a kapcsolódó szolgáltatások (például a Hadoop, a kaptár, a HBase stb.) alapértelmezett beállításaival. A fürt típusától, a hardver konfigurációjától, a csomópontok számától, a futtatott feladatok típusaitól, valamint az Ön által használt adatoktól (és az adatok feldolgozásának módjától) függően előfordulhat, hogy optimalizálnia kell a konfigurációt.

A teljesítmény-konfigurációk legtöbb forgatókönyvhöz való optimalizálásával kapcsolatos részletes utasításokért lásd: a [fürtök konfigurációjának optimalizálása az Apache Ambari](hdinsight-changing-configs-via-ambari.md). A Spark használatakor lásd: [Apache Spark feladatok optimalizálása a teljesítményhez](spark/apache-spark-perf.md). 

## <a name="step-7-reproduce-the-failure-on-a-different-cluster"></a>7. lépés: a hiba ismételt előállítása egy másik fürtön

A fürtlemez forrásának diagnosztizálásához indítson el egy új fürtöt ugyanazzal a konfigurációval, majd küldje el újra a sikertelen feladatok lépéseit egyenként. A következő feldolgozás előtt vizsgálja meg az egyes lépések eredményeit. Ezzel a módszerrel lehetőség van egyetlen sikertelen lépés kijavítására és újrafuttatására. Ez a módszer azt is kihasználja, hogy csak egyszer töltse be a bemeneti adatokat.

1. Hozzon létre egy új tesztelési fürtöt ugyanazzal a konfigurációval, mint a meghibásodott fürt.
2. Küldje el az első feladatot a tesztelési fürtnek.
3. Ha a lépés befejezi a feldolgozást, keresse meg a hibákat a lépés naplófájljaiban. Kapcsolódjon a test-fürt fő csomópontjára, és tekintse meg a naplófájlokat. A lépés naplófájljai csak a lépés futtatása után jelennek meg egy ideig, Befejezve vagy sikertelenül.
4. Ha az első lépés sikeres volt, futtassa a következő lépést. Ha hiba történt, vizsgálja meg a hibákat a naplófájlokban. Ha a kód hibát jelzett, hajtsa végre a javítást, és futtassa újra a lépést.
5. Folytassa, amíg az összes lépés hiba nélkül fut.
6. Ha befejezte a tesztelési fürt hibakeresését, törölje azt.

## <a name="next-steps"></a>Következő lépések

* [HDInsight-fürtök kezelése az Apache Ambari webes felületével](hdinsight-hadoop-manage-ambari.md)
* [HDInsight-naplók elemzése](./hdinsight-troubleshoot-guide.md)
* [Hozzáférési Apache Hadoop a FONALas alkalmazások bejelentkezéséhez Linux-alapú HDInsight](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Halom-memóriaképek engedélyezése Apache Hadoop-szolgáltatásokhoz Linux-alapú HDInsight](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
* [Ismert problémák a Apache Spark-fürtön a HDInsight-on](./spark/apache-spark-known-issues.md)