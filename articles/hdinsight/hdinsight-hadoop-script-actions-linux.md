---
title: Parancsfájl-műveletek fejlesztése az Azure HDInsight-fürtök testreszabásához
description: Ismerje meg, hogyan szabhatók testre a HDInsight-fürtök a bash-parancsfájlok használatával. A parancsfájl-műveletek lehetővé teszik parancsfájlok futtatását a fürt létrehozása során vagy után a fürt konfigurációs beállításainak módosításához vagy további szoftverek telepítéséhez.
ms.service: hdinsight
ms.topic: how-to
ms.date: 11/28/2019
ms.openlocfilehash: b6705728fddc9a5a3c9cb8eb2f1811412fb3a290
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98945473"
---
# <a name="script-action-development-with-hdinsight"></a>Parancsfájl-műveletek fejlesztése a HDInsight

Megtudhatja, hogyan szabhatja testre a HDInsight-fürtöt bash-parancsfájlok használatával. A parancsfájlok műveletei lehetővé teszik a HDInsight testreszabását a fürt létrehozása során vagy után.

## <a name="what-are-script-actions"></a>A parancsfájlok műveletei

A parancsfájl-műveletek olyan bash-parancsfájlok, amelyeket az Azure futtat a fürtcsomópontok között a konfiguráció módosításához vagy a szoftverek telepítéséhez. A parancsfájl művelete gyökérként fut, és teljes hozzáférési jogosultságokat biztosít a fürtcsomópontok számára.

A parancsfájlok műveletei a következő módszerekkel alkalmazhatók:

| Ezzel a módszerrel alkalmazhat egy parancsfájlt... | Fürt létrehozása közben... | Futó fürtön... |
| --- |:---:|:---:|
| Azure Portal |✓ |✓ |
| Azure PowerShell |✓ |✓ |
| Azure klasszikus parancssori felület |&nbsp; |✓ |
| HDInsight .NET SDK |✓ |✓ |
| Azure Resource Manager-sablon |✓ |&nbsp; |

A parancsfájl-műveletek alkalmazásával kapcsolatos további információkért lásd: HDInsight- [fürtök testreszabása parancsfájl-műveletek használatával](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="best-practices-for-script-development"></a><a name="bestPracticeScripting"></a>Ajánlott eljárások a parancsfájlok fejlesztéséhez

Ha egyéni szkriptet fejleszt ki egy HDInsight-fürthöz, az ajánlott eljárások többek között a következők:

* [A Apache Hadoop verziójának megcélzása](#bPS1)
* [Az operációs rendszer verziójának megcélzása](#bps10)
* [Stabil hivatkozások biztosítása a parancsfájl erőforrásaihoz](#bPS2)
* [Előre lefordított erőforrások használata](#bPS4)
* [Győződjön meg arról, hogy a fürt testreszabási parancsfájlja idempotens](#bPS3)
* [A fürt architektúrájának magas rendelkezésre állásának biztosítása](#bPS5)
* [Az egyéni összetevők konfigurálása az Azure Blob Storage használatára](#bPS6)
* [Adatok írása az STDOUT-ba és a STDERR](#bPS7)
* [Fájlok mentése ASCII-ként LF-sorok végződésével](#bps8)
* [Újrapróbálkozási logika használata az átmeneti hibákból való helyreállításhoz](#bps9)

> [!IMPORTANT]  
> A parancsfájl-műveletek végrehajtásának 60 percen belül kell lennie, vagy a folyamat meghiúsul. A csomópont-kiépítés során a parancsfájl egyidejűleg fut más beállítási és konfigurációs folyamatokkal. Az erőforrások, például a CPU-idő vagy a hálózati sávszélesség versenye miatt előfordulhat, hogy a szkript hosszabb ideig tart, mint a fejlesztési környezetben.

### <a name="target-the-apache-hadoop-version"></a><a name="bPS1"></a>A Apache Hadoop verziójának megcélzása

A HDInsight különböző verziói a Hadoop Services és az összetevők különböző verzióit telepítették. Ha a parancsfájl egy szolgáltatás vagy összetevő egy adott verzióját várja, akkor a szkriptet csak a szükséges összetevőket tartalmazó HDInsight-verzióval kell használnia. A HDInsight-ben található összetevő-verziókkal kapcsolatos információkat a [HDInsight-összetevő verziószámozási](hdinsight-component-versioning.md) dokumentuma alapján találhatja meg.

### <a name="checking-the-operating-system-version"></a>Az operációs rendszer verziójának ellenőrzése

A HDInsight különböző verziói az Ubuntu adott verzióira támaszkodnak. A parancsfájlban esetlegesen szükséges operációsrendszer-verziók között eltérések lehetnek. Előfordulhat például, hogy olyan bináris fájlt kell telepítenie, amely az Ubuntu-verzióhoz van kötve.

Az operációs rendszer verziószámának vizsgálatához használja a következőt: `lsb_release` . A következő parancsfájl például bemutatja, hogyan hivatkozhat egy adott tar-fájlra az operációs rendszer verziójától függően:

```bash
OS_VERSION=$(lsb_release -sr)
if [[ $OS_VERSION == 14* ]]; then
    echo "OS version is $OS_VERSION. Using hue-binaries-14-04."
    HUE_TARFILE=hue-binaries-14-04.tgz
elif [[ $OS_VERSION == 16* ]]; then
    echo "OS version is $OS_VERSION. Using hue-binaries-16-04."
    HUE_TARFILE=hue-binaries-16-04.tgz
fi
```

### <a name="target-the-operating-system-version"></a><a name="bps10"></a> Az operációs rendszer verziójának megcélzása

A HDInsight a Ubuntu Linux eloszláson alapul. A HDInsight különböző verziói az Ubuntu különböző verzióira támaszkodnak, ami megváltoztathatja a szkript működésének módját. A HDInsight 3,4-es és korábbi verziói például az üzembe helyezést használó Ubuntu-verziókon alapulnak. A 3,5-es és újabb verziók a rendszer által használt Ubuntu 16,04-es verzión alapulnak. A rendszer és a Kiindulás különböző parancsokra támaszkodik, így a parancsfájlt úgy kell megírni, hogy mindkettővel működjön.

A HDInsight 3,4 és a 3,5 egy másik fontos különbség, hogy `JAVA_HOME` most már a Java 8-ra mutat. A következő kód azt mutatja be, hogyan állapítható meg, hogy a parancsfájl fut-e Ubuntu 14 vagy 16 rendszeren:

```bash
OS_VERSION=$(lsb_release -sr)
if [[ $OS_VERSION == 14* ]]; then
    echo "OS version is $OS_VERSION. Using hue-binaries-14-04."
    HUE_TARFILE=hue-binaries-14-04.tgz
elif [[ $OS_VERSION == 16* ]]; then
    echo "OS version is $OS_VERSION. Using hue-binaries-16-04."
    HUE_TARFILE=hue-binaries-16-04.tgz
fi
...
if [[ $OS_VERSION == 16* ]]; then
    echo "Using systemd configuration"
    systemctl daemon-reload
    systemctl stop webwasb.service    
    systemctl start webwasb.service
else
    echo "Using upstart configuration"
    initctl reload-configuration
    stop webwasb
    start webwasb
fi
...
if [[ $OS_VERSION == 14* ]]; then
    export JAVA_HOME=/usr/lib/jvm/java-7-openjdk-amd64
elif [[ $OS_VERSION == 16* ]]; then
    export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
fi
```

A kódrészleteket tartalmazó teljes parancsfájlt a következő helyen találja: https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh .

A HDInsight által használt Ubuntu-verzió esetében tekintse meg a [HDInsight-összetevő verziószámát](hdinsight-component-versioning.md) ismertető dokumentumot.

A rendszerszintű és a kezdeti lépések közötti különbségek megismeréséhez tekintse meg a [rendszerindító felhasználók számára](https://wiki.ubuntu.com/SystemdForUpstartUsers)című témakört.

### <a name="provide-stable-links-to-script-resources"></a><a name="bPS2"></a>Stabil hivatkozások biztosítása a parancsfájl erőforrásaihoz

A parancsfájlnak és a kapcsolódó erőforrásoknak elérhetőnek kell maradniuk a fürt teljes élettartama alatt. Ezek az erőforrások akkor szükségesek, ha új csomópontokat adnak hozzá a fürthöz a skálázási műveletek során.

Az ajánlott eljárás az, hogy minden Azure Storage-fiókban letöltse és archiválja az előfizetését.

> [!IMPORTANT]  
> A használt Storage-fióknak az alapértelmezett Storage-fióknak kell lennie a fürthöz vagy egy nyilvános, írásvédett tárolóhoz bármely más Storage-fiókban.

A Microsoft által biztosított mintákat például a [https://hdiconfigactions.blob.core.windows.net/](https://hdiconfigactions.blob.core.windows.net/) Storage-fiók tárolja. Ez a hely egy nyilvános, csak olvasható tároló, amelyet a HDInsight csapat tart fenn.

### <a name="use-pre-compiled-resources"></a><a name="bPS4"></a>Előre lefordított erőforrások használata

A parancsfájl futtatásához szükséges idő csökkentése érdekében kerülje az erőforrások forráskódból való fordítására szolgáló műveleteket. Például előre lefordíthatja az erőforrásokat, és tárolhatja őket egy Azure Storage-fiók blobjában ugyanabban az adatközpontban, mint a HDInsight.

### <a name="ensure-that-the-cluster-customization-script-is-idempotent"></a><a name="bPS3"></a>Győződjön meg arról, hogy a fürt testreszabási parancsfájlja idempotens

A szkripteknek idempotens kell lenniük. Ha a parancsfájl többször is fut, minden alkalommal ugyanazt az állapotot kell visszaadnia a fürtnek.

Például egy parancsfájl, amely módosítja a konfigurációs fájlokat, ne adjon hozzá duplikált bejegyzéseket, ha többször is futott.

### <a name="ensure-high-availability-of-the-cluster-architecture"></a><a name="bPS5"></a>A fürt architektúrájának magas rendelkezésre állásának biztosítása

A Linux-alapú HDInsight-fürtök két, a fürtön belül aktív fő csomópontot biztosítanak, a parancsfájlok műveletei pedig mindkét csomóponton futnak. Ha a telepített összetevők csak egy fő csomópontot várnak, ne telepítse az összetevőket mindkét fő csomópontra.

> [!IMPORTANT]  
> A HDInsight részeként biztosított szolgáltatások úgy vannak kialakítva, hogy szükség szerint feladatátvételt végezzenek a két fő csomópont között. Ez a funkció nem terjeszthető ki a parancsfájlok műveletein keresztül telepített egyéni összetevőkre. Ha magas rendelkezésre állásra van szüksége az egyéni összetevőkhöz, saját feladatátvételi mechanizmust kell megvalósítani.

### <a name="configure-the-custom-components-to-use-azure-blob-storage"></a><a name="bPS6"></a>Az egyéni összetevők konfigurálása az Azure Blob Storage használatára

A fürtön telepített összetevőkhöz a Apache Hadoop elosztott fájlrendszer (HDFS) tárolót használó alapértelmezett konfiguráció is tartozhat. A HDInsight az Azure Storage-t vagy a Data Lake Storage használja alapértelmezett tárolóként. Mindkettő olyan HDFS-kompatibilis fájlrendszert biztosít, amely akkor is megőrzi az adattárolást, ha a fürtöt törölték. Előfordulhat, hogy a HDFS helyett a WASB vagy az ADL használatára telepített összetevőket kell konfigurálnia.

A legtöbb művelet esetében nem kell megadnia a fájlrendszert. A következő példa például a Hadoop-Common. jar fájlt másolja a helyi fájlrendszerből a fürt tárterületére:

```bash
hdfs dfs -put /usr/hdp/current/hadoop-client/hadoop-common.jar /example/jars/
```

Ebben a példában a `hdfs` parancs transzparens módon használja az alapértelmezett fürtöt tárolót. Egyes műveletek esetében előfordulhat, hogy meg kell adnia az URI-t. Azure Data Lake Storage Gen1 például a `adl:///example/jars` `abfs:///example/jars` Data Lake Storage Gen2 vagy `wasb:///example/jars` Az Azure Storage esetében.

### <a name="write-information-to-stdout-and-stderr"></a><a name="bPS7"></a>Adatok írása az STDOUT-ba és a STDERR

A HDInsight az STDOUT-ra és a STDERR-re írt parancsfájl-kimenetet naplózza. Ezeket az információkat a Ambari webes felhasználói felületének használatával tekintheti meg.

> [!NOTE]  
> Az Apache Ambari csak akkor érhető el, ha a fürt létrehozása sikeres volt. Ha parancsfájl-műveletet használ a fürt létrehozása során, és a létrehozás meghiúsul, tekintse meg a [parancsfájl-műveletek hibakeresése](./troubleshoot-script-action.md) a naplózott adatok elérésének egyéb módjaihoz című témakört.

A legtöbb segédprogram és telepítési csomag már adatokat ír az STDOUT-ba és a STDERR-be, azonban érdemes lehet további naplózást hozzáadni. Ha szöveget szeretne a STDOUT-ba küldeni, használja a következőt: `echo` . Például:

```bash
echo "Getting ready to install Foo"
```

Alapértelmezés szerint `echo` a karakterláncot a következőre küldi: StdOut. A STDERR való közvetlen hozzáadáshoz adja hozzá a következőt: `>&2` `echo` . Például:

```bash
>&2 echo "An error occurred installing Foo"
```

Ez Ehelyett a STDOUT-ba írt adatokat STDERR (2) átirányítja. Az i/o-átirányítás részletes ismertetését lásd: [https://www.tldp.org/LDP/abs/html/io-redirection.html](https://www.tldp.org/LDP/abs/html/io-redirection.html) .

A parancsfájl-műveletek által naplózott információk megtekintésével kapcsolatos további információkért lásd: [parancsfájl-műveletek hibakeresése](./troubleshoot-script-action.md).

### <a name="save-files-as-ascii-with-lf-line-endings"></a><a name="bps8"></a> Fájlok mentése ASCII-ként LF-sorok végződésével

A bash-parancsfájlokat ASCII formátumban kell tárolni, az LF által leállított vonalakkal. Az UTF-8-ként tárolt fájlok vagy a CRLF használata, mert a sorok vége meghiúsulhat a következő hiba miatt:

```
$'\r': command not found
line 1: #!/usr/bin/env: No such file or directory
```

### <a name="use-retry-logic-to-recover-from-transient-errors"></a><a name="bps9"></a> Újrapróbálkozási logika használata az átmeneti hibákból való helyreállításhoz

Fájlok letöltésekor, az apt-get használatával vagy az interneten keresztül adatokat továbbító egyéb műveletekkel a csomagok telepítése során előfordulhat, hogy a művelet átmeneti hálózati hibák miatt meghiúsul. Előfordulhat például, hogy a távoli erőforrás, amellyel kommunikál, a biztonsági mentési csomópontra történő feladatátvétel folyamatában lehet.

Annak érdekében, hogy a parancsfájl rugalmas legyen az átmeneti hibákkal, megvalósíthatja az újrapróbálkozási logikát. Az alábbi függvény az újrapróbálkozási logika megvalósítását mutatja be. A művelet végrehajtása előtt háromszor újrapróbálkozik a művelettel.

```bash
#retry
MAXATTEMPTS=3

retry() {
    local -r CMD="$@"
    local -i ATTMEPTNUM=1
    local -i RETRYINTERVAL=2

    until $CMD
    do
        if (( ATTMEPTNUM == MAXATTEMPTS ))
        then
                echo "Attempt $ATTMEPTNUM failed. no more attempts left."
                return 1
        else
                echo "Attempt $ATTMEPTNUM failed! Retrying in $RETRYINTERVAL seconds..."
                sleep $(( RETRYINTERVAL ))
                ATTMEPTNUM=$ATTMEPTNUM+1
        fi
    done
}
```

Az alábbi példák bemutatják, hogyan használhatja ezt a függvényt.

```bash
retry ls -ltr foo

retry wget -O ./tmpfile.sh https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
```

## <a name="helper-methods-for-custom-scripts"></a><a name="helpermethods"></a>Az egyéni parancsfájlok segítő módszerei

A parancsfájl-művelet segítő módszerei olyan segédprogramok, amelyeket használhat egyéni parancsfájlok írásakor. Ezeket a metódusokat a [https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh) parancsfájl tárolja. A következő paranccsal töltheti le és használhatja őket a parancsfájl részeként:

```bash
# Import the helper method module.
wget -O /tmp/HDInsightUtilities-v01.sh -q https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh && source /tmp/HDInsightUtilities-v01.sh && rm -f /tmp/HDInsightUtilities-v01.sh
```

A parancsfájlban a következő segítők használhatók:

| Segítő használata | Leírás |
| --- | --- |
| `download_file SOURCEURL DESTFILEPATH [OVERWRITE]` |Letölt egy fájlt a forrás URI-ból a megadott elérési útra. Alapértelmezés szerint nem írja felül a meglévő fájlt. |
| `untar_file TARFILE DESTDIR` |Kibont egy tar-fájlt (a használatával `-xf` ) a cél könyvtárába. |
| `test_is_headnode` |Ha a parancsfájl egy fürtcsomópont-csomóponton futott, adja vissza az 1; értéket. Ellenkező esetben 0. |
| `test_is_datanode` |Ha az aktuális csomópont egy adat (feldolgozó) csomópont, egy 1; értéket ad vissza. Ellenkező esetben 0. |
| `test_is_first_datanode` |Ha az aktuális csomópont az első adat (Worker) csomópont (workernode0), egy 1 értéket ad vissza. Ellenkező esetben 0. |
| `get_headnodes` |A fürt átjárócsomópontokkal teljes tartománynevét adja vissza. A nevek vesszővel tagoltak. Hiba esetén üres karakterláncot ad vissza. |
| `get_primary_headnode` |Lekéri az elsődleges átjárócsomóponthoz teljes tartománynevét. Hiba esetén üres karakterláncot ad vissza. |
| `get_secondary_headnode` |Lekéri a másodlagos átjárócsomóponthoz teljes tartománynevét. Hiba esetén üres karakterláncot ad vissza. |
| `get_primary_headnode_number` |Lekéri az elsődleges átjárócsomóponthoz numerikus utótagját. Hiba esetén üres karakterláncot ad vissza. |
| `get_secondary_headnode_number` |Lekéri a másodlagos átjárócsomóponthoz numerikus utótagját. Hiba esetén üres karakterláncot ad vissza. |

## <a name="common-usage-patterns"></a><a name="commonusage"></a>Gyakori használati minták

Ez a szakasz útmutatást nyújt néhány olyan általános használati minta megvalósításához, amelyeket a saját egyéni szkriptének írásakor futtathat.

### <a name="passing-parameters-to-a-script"></a>Paraméterek átadása egy parancsfájlnak

Bizonyos esetekben előfordulhat, hogy a szkript paramétereket kér. Előfordulhat például, hogy a Ambari REST API használatakor szükség van a fürt rendszergazdai jelszavára.

A parancsfájlnak átadott paraméterek *pozicionális paraméterekként* ismertek, és az `$1` első paraméterhez, a másodikhoz és a (z) rendszerhez vannak rendelve `$2` . `$0` a parancsfájl nevét tartalmazza.

A parancsfájlnak átadott értékeket paraméterként aposztrófok (') közé kell foglalni. Így biztosítható, hogy az átadott érték literálként legyen kezelve.

### <a name="setting-environment-variables"></a>Környezeti változók beállítása

A környezeti változók beállítását a következő utasítás hajtja végre:

```bash
VARIABLENAME=value
```

Az előző példában a a `VARIABLENAME` változó neve. A változó eléréséhez használja a következőt: `$VARIABLENAME` . Ha például egy, a jelszó nevű környezeti változóban egy pozíciós paraméter által megadott értéket szeretne hozzárendelni, használja a következő utasítást:

```bash
PASSWORD=$1
```

Ezt követően az adatokhoz való további hozzáférés is használható `$PASSWORD` .

A parancsfájlban beállított környezeti változók csak a parancsfájl hatókörén belül találhatók. Bizonyos esetekben előfordulhat, hogy olyan rendszerszintű környezeti változókat kell hozzáadnia, amelyek a szkript befejeződése után is megmaradnak. Rendszerszintű környezeti változók hozzáadásához adja hozzá a változót a következőhöz: `/etc/environment` . Az alábbi utasítás például a következőt adja meg `HADOOP_CONF_DIR` :

```bash
echo "HADOOP_CONF_DIR=/etc/hadoop/conf" | sudo tee -a /etc/environment
```

### <a name="access-to-locations-where-the-custom-scripts-are-stored"></a>Hozzáférés az egyéni parancsfájlokat tároló helyekhez

A fürtök testreszabásához használt parancsfájlokat a következő helyekre kell tárolni:

* A fürthöz társított __Azure Storage-fiók__ .

* A fürthöz társított __további Storage-fiók__ .

* __Nyilvánosan olvasható URI__. Például egy URL-cím a OneDrive-on, a Dropboxban vagy más file hosting szolgáltatásban tárolt adatelérési ponthoz.

* Egy __Azure Data Lake Storage fiók__ , amely a HDInsight-fürthöz van társítva. A Azure Data Lake Storage és a HDInsight használatával kapcsolatos további információkért lásd [: gyors útmutató: fürtök beállítása a HDInsight-ben](./hdinsight-hadoop-provision-linux-clusters.md).

    > [!NOTE]  
    > Az egyszerű szolgáltatásnak a HDInsight való Data Lake Storage eléréséhez olvasási hozzáféréssel kell rendelkeznie a parancsfájlhoz.

A parancsfájl által használt erőforrásoknak is nyilvánosan elérhetőnek kell lenniük.

Ha egy Azure Storage-fiókban tárolja a fájlokat, vagy a Azure Data Lake Storage gyors hozzáférést biztosít az Azure-hálózaton belül.

> [!NOTE]  
> A parancsfájlra való hivatkozáshoz használt URI-formátum a használt szolgáltatástól függően eltérő. A HDInsight-fürthöz társított Storage-fiókok esetében használja a vagy a t `wasb://` `wasbs://` . Nyilvánosan olvasható URI-k esetén használja a következőt: `http://` vagy `https://` . Data Lake Storage esetén használja a következőt: `adl://` .

## <a name="checklist-for-deploying-a-script-action"></a><a name="deployScript"></a>Ellenőrzőlista parancsfájl-művelet telepítéséhez

A következő lépésekkel végezheti el a parancsfájlok üzembe helyezésének előkészítését:

* Helyezze az egyéni parancsfájlokat tartalmazó fájlokat olyan helyre, amelyet a fürtcsomópontok az üzembe helyezés során elérhetővé tesznek. Például a fürt alapértelmezett tárolója. A fájlok tárolhatók nyilvánosan olvasható üzemeltetési szolgáltatásokban is.
* Ellenőrizze, hogy a parancsfájl idempotens van-e. Ez lehetővé teszi, hogy a parancsfájl többször is végrehajtva legyen ugyanazon a csomóponton.
* Egy ideiglenes könyvtár/tmp megtarthatja a parancsfájlok által használt letöltött fájlokat, majd a parancsfájlok végrehajtása után megtisztíthatja őket.
* Ha az operációsrendszer-szintű beállítások vagy az Hadoop szolgáltatás konfigurációs fájljai módosulnak, érdemes újraindítani a HDInsight-szolgáltatásokat.

## <a name="how-to-run-a-script-action"></a><a name="runScriptAction"></a>Parancsfájl-művelet futtatása

A HDInsight-fürtöket a következő módszerekkel testreszabhatja parancsfájl-műveletek használatával:

* Azure Portal
* Azure PowerShell
* Azure Resource Manager-sablonok
* A HDInsight .NET SDK-t.

További információ az egyes módszerek használatáról: a [parancsfájl-művelet használata](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="custom-script-samples"></a><a name="sampleScripts"></a>Egyéni parancsfájlok mintái

A Microsoft példákat biztosít a HDInsight-fürtön található összetevők telepítéséhez. Lásd: [a Hue telepítése és használata HDInsight-fürtökön](hdinsight-hadoop-hue-linux.md) példaként szolgáló parancsfájl-művelet.

## <a name="troubleshooting"></a>Hibaelhárítás

A fejlesztett parancsfájlok használatakor a következő hibák merülhetnek fel:

**Hiba**: `$'\r': command not found` . Időnként ezt követi `syntax error: unexpected end of file` .

*OK*: Ez a hiba akkor következik be, amikor egy parancsfájl SORAI a CRLF-sel végződik. A UNIX rendszerű rendszerek csak a TT-t várnak a sor végéig.

Ez a probléma leggyakrabban akkor fordul elő, ha a parancsfájlt Windows-környezetben készíti el, mivel a CRLF egy, a Windows számos szövegszerkesztője számára végződő közös vonal.

*Megoldás*: Ha a szövegszerkesztőben lehetőség van, válassza a Unix-formátum vagy a LF lehetőséget a sor befejezéséhez. A következő parancsokat is használhatja egy UNIX rendszeren a CRLF egy LF-re való módosításához:

> [!NOTE]  
> A következő parancsok nagyjából egyenértékűek, ha módosítani kell a CRLF sort a LF értékre. Válasszon egyet a rendszeren elérhető segédprogramok alapján.

| Parancs | Jegyzetek |
| --- | --- |
| `unix2dos -b INFILE` |Az eredeti fájl biztonsági mentése a-val történik. BAK bővítmény |
| `tr -d '\r' < INFILE > OUTFILE` |A fájl verziószáma csak LF végződéssel rendelkező verziót tartalmaz |
| `perl -pi -e 's/\r\n/\n/g' INFILE` | Közvetlenül módosítja a fájlt |
| ```sed 's/$'"/`echo \\\r`/" INFILE > OUTFILE``` |A fájl egy olyan verziót tartalmaz, amely csak LF végződéssel rendelkezik. |

**Hiba**: `line 1: #!/usr/bin/env: No such file or directory` .

*OK*: Ez a hiba akkor fordul elő, ha a parancsfájl UTF-8-as, bájtos rendelési JELLEL (BOM) lett mentve.

*Megoldás*: mentse a fájlt ASCII-ként, vagy egy ANYAGJEGYZÉK nélküli UTF-8-ként. Az alábbi parancsot Linux vagy UNIX rendszerű rendszeren is használhatja az ANYAGJEGYZÉK nélküli fájl létrehozásához:

```bash
awk 'NR==1{sub(/^\xef\xbb\xbf/,"")}{print}' INFILE > OUTFILE
```

Cserélje le az helyére az `INFILE` anyagjegyzéket tartalmazó fájlt. `OUTFILE` egy új fájlnévnek kell lennie, amely az AJ nélkül tartalmazza a parancsfájlt.

## <a name="next-steps"></a><a name="seeAlso"></a>Következő lépések

* Megtudhatja, hogyan [szabhatja testre a HDInsight-fürtöket parancsfájl-művelet használatával](hdinsight-hadoop-customize-cluster-linux.md)
* A [HDInsight .net SDK-referenciával](/dotnet/api/overview/azure/hdinsight) többet tudhat meg a HDInsight-t kezelő .NET-alkalmazások létrehozásáról
* A [HDInsight REST API](/rest/api/hdinsight/) segítségével megtudhatja, hogyan használható a REST a felügyeleti műveletek végrehajtásához a HDInsight-fürtökön.