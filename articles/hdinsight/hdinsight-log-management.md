---
title: HDInsight-fürt naplófájljainak kezelése – Azure HDInsight
description: Határozza meg a HDInsight-tevékenység naplófájljainak típusait, méretét és adatmegőrzési szabályzatait.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 02/05/2020
ms.openlocfilehash: 20a7a7392070a6f888add630398516b2f872cd7d
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104865485"
---
# <a name="manage-logs-for-an-hdinsight-cluster"></a>HDInsight-fürt naplóinak kezelése

An méretű HDInsight-fürt számos naplófájlt hoz létre. Például a Apache Hadoop és a kapcsolódó szolgáltatások, például a Apache Spark, részletes feladatok végrehajtására szolgáló naplókat készítenek. A naplófájlok kezelése egy kifogástalan HDInsight-fürt fenntartásának részét képezi. A napló archiválására vonatkozó szabályozási követelmények is megadhatók.  A naplófájlok száma és mérete miatt a naplózási tároló és az archiválás optimalizálása segít a szolgáltatás költséghatékonyságának kezelésében.

A HDInsight-fürtök kezelése magában foglalja a fürt környezetének összes aspektusával kapcsolatos információk megőrzését. Ez az információ magában foglalja az összes kapcsolódó Azure-szolgáltatás naplóját, a fürtkonfiguráció, a feladatok végrehajtási adatait, a hibás állapotokat és az egyéb adatokat, ha szükséges.

A HDInsight naplózásának jellemző lépései a következők:

* 1. lépés: a naplózási adatmegőrzési szabályzatok meghatározása
* 2. lépés: a fürtszolgáltatás verzióinak kezelése – konfigurációs naplók
* 3. lépés: a fürt feladatok végrehajtási naplófájljainak kezelése
* 4. lépés: a naplózási kötetek tárolási méretének és költségeinek előrejelzése
* 5. lépés: a naplófájlok archiválási házirendjeinek és folyamatainak meghatározása

## <a name="step-1-determine-log-retention-policies"></a>1. lépés: a naplózási adatmegőrzési szabályzatok meghatározása

A HDInsight-naplózási kezelési stratégia létrehozásának első lépése az üzleti forgatókönyvekkel és a feladatok végrehajtásával kapcsolatos előzmények tárolási követelményeivel kapcsolatos információk összegyűjtése.

### <a name="cluster-details"></a>Fürt részletei

A következő fürt adatai hasznosak lehetnek az információk gyűjtéséhez a naplózási kezelési stratégiában. Gyűjtse össze ezeket az információkat az adott Azure-fiókban létrehozott összes HDInsight-fürtből.

* Fürt neve
* A fürt régiója és az Azure rendelkezésre állási zónája
* A fürt állapota, beleértve az utolsó állapot változásának részleteit is
* A Master, a Core és a Task csomópontokhoz megadott HDInsight-példányok típusa és száma

A legfelső szintű információk többségét a Azure Portal használatával érheti el.  Azt is megteheti, hogy az [Azure CLI](/cli/azure/) -vel is beolvassa a HDInsight-fürt (ek) adatait:

```azurecli
az hdinsight list --resource-group <ResourceGroup>
az hdinsight show --resource-group <ResourceGroup> --name <ClusterName>
```

Ezeket az információkat a PowerShell használatával is megtekintheti.  További információ: az [Apache Hadoop-fürtök kezelése a HDInsight-ben a Azure PowerShell használatával](hdinsight-administer-use-powershell.md).

### <a name="understand-the-workloads-running-on-your-clusters"></a>A fürtökön futó munkaterhelések ismertetése

Fontos, hogy megismerje a HDInsight-fürt (ek) on futó számítási feladatok típusát az egyes típusokhoz tartozó megfelelő naplózási stratégiák kialakításához.

* A számítási feladatok (például a fejlesztés vagy a tesztelés) vagy a termelési minőség?
* Milyen gyakran fut a termelési minőségű számítási feladatok?
* Az erőforrás-igényes és/vagy hosszan futó számítási feladatok bármelyike?
* A munkaterhelések bármelyike a Hadoop-szolgáltatások összetett készletét használja, amelyekhez többféle típusú napló készül?
* A munkaterhelések bármelyike rendelkezik-e a szabályozás végrehajtásával kapcsolatos követelményekkel?

### <a name="example-log-retention-patterns-and-practices"></a>Példa naplózási adatmegőrzési mintákra és eljárásokra

* Vegye fontolóra az adatbányászati nyomon követés fenntartását úgy, hogy egy azonosítót ad hozzá minden egyes naplóbejegyzés vagy más módszerekkel. Ez lehetővé teszi az adat és a művelet eredeti forrásának nyomon követését, valamint az egyes fázisokban lévő adat követését az egységesség és az érvényesség megismerése érdekében.

* Gondolja át, hogyan gyűjthet naplókat a fürtből vagy több fürtből, és hogyan oszthatja azokat olyan célokra, mint a naplózás, a figyelés, a tervezés és a riasztás. Használhat egyéni megoldást is a naplófájlok rendszeres eléréséhez és letöltéséhez, valamint az irányítópultok megjelenítéséhez és elemzéséhez. További képességeket is hozzáadhat a riasztásokhoz a biztonság vagy a hibák észlelése céljából. Ezeket a segédprogramokat a PowerShell, a HDInsight SDK-k vagy a klasszikus Azure üzemi modellhez hozzáférő kód segítségével hozhatja létre.

* Érdemes megfontolni, hogy a figyelési megoldás vagy szolgáltatás hasznos előnyt jelentene-e. A Microsoft System Center egy [HDInsight felügyeleti csomagot](https://systemcenter.wiki/?Get_ManagementPackBundle=Microsoft.HDInsight.mpb&FileMD5=10C7D975C6096FFAA22C84626D211259)biztosít. A naplók gyűjtésére és központosítására olyan külső gyártótól származó eszközöket is használhat, mint az Apache Chukwa és a ganglionok. Számos vállalat kínál szolgáltatásokat a Hadoop-alapú big data megoldások figyelésére, például: középpont, CompuWare APM, Sematext SPM és Zettaset Orchestrator.

## <a name="step-2-manage-cluster-service-versions-and-view-logs"></a>2. lépés: a fürtszolgáltatás verzióinak kezelése és a naplók megtekintése

Egy tipikus HDInsight-fürt számos szolgáltatást és nyílt forráskódú szoftvercsomagot használ (például Apache HBase, Apache Spark stb.). Bizonyos munkaterhelések, például a bioinformatika esetében előfordulhat, hogy a feladat-végrehajtási naplók mellett meg kell őriznie a szolgáltatás konfigurációs naplójának előzményeit is.

### <a name="view-cluster-configuration-settings-with-the-ambari-ui"></a>A Ambari felhasználói felületén megtekintheti a fürt konfigurációs beállításait

Az Apache Ambari egy webes felhasználói felületet és egy REST API biztosít a HDInsight-fürtök felügyeletéhez, konfigurálásához és figyeléséhez. A Ambari a Linux-alapú HDInsight-fürtökön található. A Azure Portal HDInsight lapon válassza ki a **fürt** irányítópultja ablaktáblát a **fürt irányítópultok** hivatkozás oldalának megnyitásához.  Ezután válassza a **HDInsight-fürt irányítópultja** panelt a Ambari felhasználói felületének megnyitásához.  A rendszer a fürt bejelentkezési hitelesítő adatainak megadását kéri.

A szolgáltatási nézetek listájának megnyitásához válassza a **Ambari nézetek** panelt a HDInsight Azure Portal lapján.  Ez a lista attól függően változik, hogy milyen könyvtárakat telepített.  Például megtekintheti a FONALak üzenetsor-kezelőjét, a kaptár nézetet és a TEZ nézetet.  A konfigurálási és a szolgáltatási információk megtekintéséhez válassza a bármely szolgáltatás hivatkozását.  A Ambari felhasználói felületi **verem és verziója** lapon információk szerepelnek a fürtszolgáltatás konfigurációjának és a szolgáltatás verziójának előzményeiről. Ha a Ambari felhasználói felületének ezen szakaszára szeretne navigálni, válassza a **rendszergazda** menüt, majd a **Stacks és a Versions** elemet.  A **verziók** lapon tekintheti meg a szolgáltatás verziószámával kapcsolatos információkat.

:::image type="content" source="./media/hdinsight-log-management/ambari-stack-versions.png" alt-text="Apache Ambari-rendszergazdai verem és verziók":::

A Ambari felhasználói felületének használatával letöltheti a fürt egy adott gazdagépén (vagy csomópontján) futó bármely (vagy az összes) szolgáltatás konfigurációját.  Válassza a **gazdagépek** menüt, majd a kívánt gazdagépre mutató hivatkozást. A gazdagép lapján válassza a **gazdagép műveletek** gombot, majd **töltse le az ügyfél konfigurációit**.

:::image type="content" source="./media/hdinsight-log-management/download-client-configs.png" alt-text="Apache Ambari letöltési gazdagép-ügyfél konfigurációja":::

### <a name="view-the-script-action-logs"></a>A parancsfájl műveleti naplóinak megtekintése

A HDInsight [parancsfájlokat](hdinsight-hadoop-customize-cluster-linux.md) futtatnak a fürtön, manuálisan, vagy ha meg van adva. A parancsfájl-műveletek segítségével például további szoftvereket telepíthet a fürtre, vagy megváltoztathatja a konfigurációs beállításokat az alapértelmezett értékek alapján. A parancsfájl-műveleti naplók betekintést nyújthatnak a fürt telepítése során felmerülő hibákba, valamint a konfigurációs beállítások olyan módosításaira is, amelyek befolyásolhatják a fürt teljesítményét és rendelkezésre állását.  Egy parancsfájl-művelet állapotának megtekintéséhez válassza a Ambari felhasználói felületén az **Ops** gombot, vagy az alapértelmezett Storage-fiókban nyissa meg az eseménynaplókat. A tárolási naplók a következő címen érhetők el: `/STORAGE_ACCOUNT_NAME/DEFAULT_CONTAINER_NAME/custom-scriptaction-logs/CLUSTER_NAME/DATE` .

### <a name="view-ambari-alerts-status-logs"></a>Ambari riasztások állapotának naplófájljainak megtekintése

Az Apache Ambari a riasztási állapot módosításait írja be `ambari-alerts.log` . A teljes elérési út: `/var/log/ambari-server/ambari-alerts.log` . A napló hibakeresésének engedélyezéséhez módosítsa a tulajdonságot a Change (módosítás) elemre a `/etc/ambari-server/conf/log4j.properties.` `# Log alert state changes` következőből:

```
log4j.logger.alerts=INFO,alerts

to

log4j.logger.alerts=DEBUG,alerts
```

## <a name="step-3-manage-the-cluster-job-execution-log-files"></a>3. lépés: a fürt feladatok végrehajtási naplófájljainak kezelése

A következő lépés a feladatok végrehajtási naplófájljainak áttekintése a különböző szolgáltatásokhoz.  A szolgáltatások lehetnek például az Apache HBase, a Apache Spark és sok más. A Hadoop-fürtök nagy mennyiségű részletes naplót készítenek, így a hasznos naplók (és amelyek nem) időigényesek lehetnek.  A naplófájlok kezeléséhez fontos a naplózási rendszer ismertetése.  A következő képen egy példa naplófájl található.

:::image type="content" source="./media/hdinsight-log-management/hdi-log-file-example.png" alt-text="HDInsight példa naplófájl minta kimenete":::

### <a name="access-the-hadoop-log-files"></a>A Hadoop naplófájlok elérése

A HDInsight a fürt fájlrendszerében és az Azure Storage-ban is tárolja a naplófájlokat. Megvizsgálhatja a fürt naplófájljait úgy, hogy megnyit egy [SSH](hdinsight-hadoop-linux-use-ssh-unix.md) -csatlakozást a fürthöz, és megkeresi a fájlrendszert, vagy a Hadoop fonal állapota portál használatával a távoli fő csomópont-kiszolgálón. Az Azure Storage-ban található naplófájlokat bármely olyan eszközzel megvizsgálhatja, amely hozzáférhet az Azure Storage-ból, és letöltheti azokat. Ilyenek például a [AzCopy](../storage/common/storage-use-azcopy-v10.md), a [CloudXplorer](https://clumsyleaf.com/products/cloudxplorer)és a Visual Studio Server Explorer. A PowerShellt és az Azure Storage ügyféloldali kódtárait, illetve az Azure .NET SDK-kat is használhatja az Azure Blob Storage-ban tárolt adateléréshez.

A Hadoop *a feladatok munkáját* a fürt különböző csomópontjain futtatja. A HDInsight megkezdheti a spekulációs feladatok kísérleteit, és leállíthatja az összes olyan feladatot, amely nem fejeződött be először. Ez jelentős tevékenységet generál, amely a vezérlőre, a stderr és a syslog-naplófájlokra van naplózva menet közben. Emellett egyszerre több feladat-kísérlet is fut, de a naplófájl csak lineárisan jeleníti meg az eredményeket.

#### <a name="hdinsight-logs-written-to-azure-blob-storage"></a>Az Azure Blob Storage-ba írt HDInsight-naplók

A HDInsight-fürtök úgy vannak konfigurálva, hogy az Azure PowerShell-parancsmagokkal vagy a .NET Job beküldési API-kkal elküldött feladatokhoz a feladatokat egy Azure Blob Storage-fiókba írják.  Ha az SSH-n keresztül küld feladatokat a fürthöz, a rendszer az előző szakaszban leírtak szerint tárolja a végrehajtási naplózási adatokat az Azure-táblákban.

A HDInsight által generált alapvető naplófájlok mellett a telepített szolgáltatások, például a FONALak is a feladat-végrehajtási naplófájlokat generálják.  A naplófájlok száma és típusa a telepített szolgáltatástól függ.  A gyakori szolgáltatások az Apache HBase, a Apache Spark és így tovább.  Vizsgálja meg az egyes szolgáltatások feladatütemezés-végrehajtási fájljait, hogy megértse a fürtön elérhető teljes naplózási fájlokat.  Minden szolgáltatás saját, egyedi naplózási módszerekkel és a naplófájlok tárolására szolgáló helyekkel rendelkezik.  Például a leggyakoribb szolgáltatás-naplófájlok (a FONALból) elérésének részleteit a következő szakaszban tárgyaljuk.

### <a name="hdinsight-logs-generated-by-yarn"></a>A FONALak által generált HDInsight-naplók

A fonal összesítései a feldolgozó csomóponton lévő összes tárolóban bejelentkeznek, és a naplókat munkavégző csomópontként egy összesített naplófájlként tárolja. Ezt a naplót az alkalmazás befejeződése után az alapértelmezett fájlrendszer tárolja. Az alkalmazás több száz vagy akár több ezer tárolót is használhat, de az egyetlen feldolgozó csomóponton futó összes tároló naplóit mindig egyetlen fájlba összesíti a rendszer. Az alkalmazás egy munkavégző csomóponton csak egy naplót használ. A naplózási összesítés alapértelmezés szerint engedélyezve van a 3,0-es és újabb verziójú HDInsight-fürtökön. Az összesített naplók a fürt alapértelmezett tárolójában találhatók.

```
/app-logs/<user>/logs/<applicationId>
```

Az összesített naplók nem olvashatók közvetlenül, mivel a tároló által indexelt TFile bináris formátumban vannak írva. A erőforráskezelő-naplók vagy a CLI-eszközök segítségével egyszerű szövegként tekintheti meg ezeket a naplókat alkalmazások vagy tárolók esetében.

#### <a name="yarn-cli-tools"></a>FONAL CLI-eszközök

A fonal CLI-eszközeinek használatához először az SSH használatával kell csatlakoznia a HDInsight-fürthöz. A `<applicationId>` `<user-who-started-the-application>` `<containerId>` parancsok futtatásakor a,, és `<worker-node-address>` adatokat kell megadni. A naplókat egyszerű szövegként tekintheti meg az alábbi parancsok egyikével:

```bash
yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>
```

#### <a name="yarn-resourcemanager-ui"></a>FONAL erőforráskezelő felhasználói felülete

A fonal erőforráskezelő felhasználói felülete a fürt fő csomópontján fut, és a Ambari webes felhasználói felületén keresztül érhető el. A következő lépések végrehajtásával tekintheti meg a FONALak naplóit:

1. Egy webböngészőben nyissa meg a `https://CLUSTERNAME.azurehdinsight.net` címet. Cserélje le a CLUSTERNAME kifejezést a HDInsight-fürt nevére.
2. A bal oldali szolgáltatások listájából válassza a fonal lehetőséget.
3. A rövid hivatkozások legördülő listából válassza ki az egyik fürtcsomópont-csomópontot, majd válassza a **erőforráskezelő-naplók** lehetőséget. Megjelenik a FONALak naplóira mutató hivatkozások listája.

## <a name="step-4-forecast-log-volume-storage-sizes-and-costs"></a>4. lépés: a naplózási kötetek tárolási méretének és költségeinek előrejelzése

Az előző lépések elvégzése után meg kell ismernie a HDInsight-fürt (ek) által gyártott naplófájlok típusait és köteteit.

Ezt követően elemezze a naplófájlok adatmennyiségét a kulcstároló tárolási helyein egy ideig. Például elemezheti a kötetet és a növekedést a 30-60-90 napos időszakokban.  Rögzítse ezeket az információkat egy táblázatba, vagy használjon más eszközöket, például a Visual studiót, az Azure Storage Explorer vagy az Excel Power Query. ```

Most már elegendő információval rendelkezik a naplók felügyeleti stratégiájának létrehozásához.  A számolótábla (vagy a választott eszköz) használatával előre jelezheti a naplózási méret növekedését és a log Storage Azure-szolgáltatás költségeit.  Tekintse meg a naplókat, amelyeket meg szeretne vizsgálni.  Most már megadhatja a naplófájlok jövőbeli költségét, miután meghatározta, hogy mely naplófájlokat lehet törölni (ha vannak ilyenek), és hogy mely naplókat kell megőrizni és archiválni a kevésbé költséges Azure Storage-ba.

## <a name="step-5-determine-log-archive-policies-and-processes"></a>5. lépés: a naplófájlok archiválási házirendjeinek és folyamatainak meghatározása

Miután meghatározta, hogy mely naplófájlok törölhetők, a naplózási paramétereket számos Hadoop-szolgáltatásban módosíthatja, hogy a naplófájlok automatikusan törlődjenek a megadott időszak után.

Bizonyos naplófájlok esetében alacsonyabb árú naplófájl-archiválási módszert használhat. Azure Resource Manager tevékenységi naplók esetében ezt a megközelítést a Azure Portal használatával is megismerheti.  Állítsa be a Resource Manager-naplók archiválását úgy, hogy kiválasztja a HDInsight-példányhoz tartozó Azure Portalban található **műveletnapló** hivatkozást.  A tevékenység naplójának keresése lap tetején válassza az **Exportálás** menüpontot az **exportálási tevékenység naplója** panel megnyitásához.  Töltse ki az előfizetést, a régiót, a Storage-fiókba való exportálást, valamint azt, hogy hány napig tart a naplók megőrzése. Ugyanezen az ablaktáblán azt is jelezheti, hogy az egy Event hub-ba exportálható-e.

:::image type="content" source="./media/hdinsight-log-management/hdi-export-log-files.png" alt-text="Azure Portal exportálási tevékenység naplójának előzetes verziója":::

Azt is megteheti, hogy parancsfájl-archiválást végez a PowerShell használatával.  Példa a PowerShell-parancsfájlra: [Azure Automation naplók archiválása az Azure-ba blob Storage](https://gallery.technet.microsoft.com/scriptcenter/Archive-Azure-Automation-898a1aa8).

### <a name="accessing-azure-storage-metrics"></a>Azure Storage-metrikák elérése

Az Azure Storage beállítható úgy, hogy naplózza a tárolási műveleteket és a hozzáférést. Ezeket a részletes naplókat használhatja a kapacitás monitorozásához és megtervezéséhez, valamint a tárolási kérelmek naplózásához. A naplózott adatok a késés részleteit tartalmazzák, és lehetővé teszik a megoldások teljesítményének figyelését és finomhangolását.
A .NET SDK for Hadoop használatával megvizsgálhatja az Azure Storage-hoz generált naplófájlokat, amelyek egy HDInsight-fürthöz tartozó adattárolót tárolnak.

### <a name="control-the-size-and-number-of-backup-indexes-for-old-log-files"></a>A régi naplófájlok biztonsági mentési indexek méretének és számának szabályozása

A megőrzött naplófájlok méretének és számának szabályozásához állítsa be a következő tulajdonságokat `RollingFileAppender` :

* `maxFileSize` annak a fájlnak a kritikus mérete, amely felett a fájl hengerelt. Az alapértelmezett érték 10 MB.
* `maxBackupIndex` a létrehozandó biztonságimásolat-fájlok számát adja meg, alapértelmezés szerint 1.

### <a name="other-log-management-techniques"></a>Egyéb naplózási kezelési technikák

A szabad lemezterület elkerülése érdekében egyes operációs rendszer-eszközök, például a [logrotate](https://linux.die.net/man/8/logrotate) használatával kezelhetik a naplófájlok kezelését. Beállíthatja `logrotate` , hogy naponta fusson, tömörítse a naplófájlokat, és távolítsa el a régieket. A módszer a követelményektől függ, például attól, hogy mennyi ideig tart a naplófájlok megőrzése a helyi csomópontokon.  

Azt is megtekintheti, hogy a HIBAKERESÉSi naplózás engedélyezve van-e egy vagy több szolgáltatáshoz, ami jelentősen növeli a kimeneti napló méretét.  

Ha a naplókat az összes csomópontról egy központi helyre szeretné gyűjteni, létrehozhat egy adatfolyamot, például az összes naplóbejegyzés betöltését a Solr-be.

## <a name="next-steps"></a>Következő lépések

* [A HDInsight figyelési és naplózási gyakorlata](/previous-versions/msp-n-p/dn749790(v=pandp.10))
* [A FONALas alkalmazások naplóihoz való hozzáférés Apache Hadoop Linux-alapú HDInsight](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [A naplófájlok méretének szabályozása a különböző Apache Hadoop-összetevőkhöz](https://community.hortonworks.com/articles/8882/how-to-control-size-of-log-files-for-various-hdp-c.html)
