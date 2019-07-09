---
title: Az Azure HDInsight-fürtök testreszabása szkriptműveletek használatával
description: Linux-alapú HDInsight-fürtök a Parancsfájlműveletek segítségével egyéni összetevőket adhat. A szkriptműveletek olyan Bash-szkriptek, amelyek segítségével a fürtkonfiguráció egyéni beállítását, vagy adjon hozzá további szolgáltatásokat és segédprogramok, mint például a Hue, Solr, vagy az R.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: 41a57d1ad5d216797fc60ea13acff346734fdef8
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67433634"
---
# <a name="customize-azure-hdinsight-clusters-by-using-script-actions"></a>Az Azure HDInsight-fürtök testreszabása szkriptműveletek használatával

Az Azure HDInsight nevű konfigurációs módszert biztosít a **szkriptműveletek** , amely meghívja az egyéni szkriptek a fürt testreszabása. Ezek a parancsfájlok segítségével további összetevők és konfigurációs beállításokat módosítaná. A szkriptműveletek használható során vagy a fürt létrehozása után.

A szkriptműveletek HDInsight alkalmazásként is az Azure piactéren tehetők közzé. További információ a HDInsight-alkalmazások: [egy HDInsight-alkalmazás közzététele az Azure Marketplace-en](hdinsight-apps-publish-applications.md).

## <a name="permissions"></a>Engedélyek

Egy tartományhoz csatlakoztatott HDInsight-fürtöt a két Apache Ambari az engedélyeket, amelyek szükségesek, ha a parancsfájl-műveletek használata a fürt van:

* **AZ AMBARI. FUTTATÁS\_EGYÉNI\_PARANCS**. Az Ambari rendszergazdai szerepkör alapértelmezés szerint rendelkezik ezzel az engedéllyel.
* **A FÜRT. FUTTATÁS\_EGYÉNI\_PARANCS**. A HDInsight fürt rendszergazdája és az Ambari rendszergazda alapértelmezés szerint rendelkezik ezzel az engedéllyel.

Tartományhoz csatlakoztatott HDInsight-engedélyek való használatáról további információkért lásd: [kezelése a HDInsight-fürt a vállalati biztonsági csomag](./domain-joined/apache-domain-joined-manage.md).

## <a name="access-control"></a>Hozzáférés-vezérlés

Ha nem a rendszergazda vagy az Azure-előfizetés tulajdonosa, a fióknak rendelkeznie kell legalább közreműködői hozzáférés az erőforráscsoporthoz, amelyet a HDInsight-fürt található.

Ha hoz létre egy HDInsight-fürtöt, rendelkező személy legalább közreműködői hozzáféréssel az Azure-előfizetés korábban regisztrálnia kellett az HDInsight-szolgáltatót. A szolgáltató regisztrálása akkor történik meg, ha egy olyan felhasználó, aki Közreműködői hozzáféréssel rendelkezik az előfizetéshez, először hoz létre erőforrást az előfizetésben. Azt is el lehet végezni anélkül, hogy egy erőforrás létrehozását, ha Ön [regisztrálja a szolgáltatót REST használatával](https://msdn.microsoft.com/library/azure/dn790548.aspx).

További információ a hozzáférés-kezelés használatával:

* [Bevezetés a hozzáférés-kezelés Azure Portalon történő használatába](../role-based-access-control/overview.md)
* [Az Azure-előfizetések erőforrásaihoz való hozzáférés kezelése szerepkör-hozzárendelésekkel](../role-based-access-control/role-assignments-portal.md)

## <a name="understand-script-actions"></a>A szkriptműveletek ismertetése

Szkriptműveletet egy HDInsight-fürt csomópontjain futó Bash-szkript. Jellemzők és szkriptműveletek funkciói a következők:

* A HDInsight-fürt által elérhető, egy URI-t kell tárolni. A lehetséges tárolási helyek a következők:
    
    * A normál fürtök:
    
      * ADLS Gen1: HDInsight Data Lake Storage elérésére használja a szolgáltatásnév a parancsfájl olvasási hozzáféréssel kell rendelkeznie. Az URI-t a Data Lake Storage Gen1 tárolt parancsfájlokat formátuma `adl://DATALAKESTOREACCOUNTNAME.azuredatalakestore.net/path_to_file`.
      
      * A blobok Azure Storage-fiók, amely kisebb, mint az elsődleges vagy a további tárfiókot a HDInsight-fürt. HDInsight hozzáférést kap mindkét említett típusú tárfiókok fürt létrehozása során.

        > [!IMPORTANT]  
        > Forgassa el a tárfiók hívóbetűjét, a az Azure Storage-fiók, az ezt követő Parancsfájlműveletek az ott tárolt sikertelen parancsfájlokkal fog okozni.

      * Egy nyilvános fájlmegosztás n keresztül elérhető szolgáltatások http:// elérési utak. Példák az Azure Blob, GitHub, onedrive vállalati verzió.

        Például az URI-k, lásd: [parancsfájl művelet parancsfájlpéldákat](#example-script-action-scripts).

     * ESP-fürtök:
         
         * A wasb: / / vagy a wasbs: / / vagy http [s] :// URI-k használata támogatott.
            
* Korlátozhatja az egyes csomóponttípusok futtatásához. Például a fő csomópontok vagy a munkavégző csomópontok.

* Megőrzött vagy alkalmi is lehet.

    A megőrzött szkriptek segítségével testre szabhatja a fürthöz méretezés műveletek során hozzáadott új munkavégző csomópontok. Egy megőrzött parancsfájl is vonatkozhatnak módosításokat egy másik csomópont típusa a méretezési műveletek esetén. Ilyen például, egy fő csomópontot.

  > [!IMPORTANT]  
  > Megőrzött Parancsfájlműveletek egyedi névvel kell rendelkeznie.

    Ad hoc parancsfájlok nem megőrzött. A feldolgozó csomópontokat hozzáadni a fürthöz, a szkript futtatása után nem alkalmazza őket. Ezután egy ad-hoc parancsfájl egy megőrzött parancsfájl előléptetése, vagy egy ad-hoc parancsfájl egy megőrzött parancsfájl lefokozása.

  > [!IMPORTANT]  
  > A fürt létrehozásakor használt szkriptműveletek automatikusan megmaradnak.
  >
  > Nem állandó parancsprogramokat, amelyek nem sikerül, akkor is, ha Ön kifejezetten azt jelzik, hogy azok kell lennie.

* Elfogadhatja a parancsfájl végrehajtása során használt paraméterek.

* Gyökér szintű jogosultsággal futtassa a fürtcsomópontokon.

* Az Azure Portalon, az Azure PowerShell, az Azure klasszikus parancssori felület vagy a HDInsight .NET SDK használható.

A fürt adott időpontig futtatott minden parancsfájl előzményeket. Az előzmények nyújt segítséget, amikor meg kell keresnie egy parancsfájl azonosítója előléptetés vagy a lefokozási műveletekhez.

> [!IMPORTANT]  
> Nincs automatikus mód szkriptműveletet által végzett módosítások visszavonása. Vagy manuálisan a változtatásokat, vagy adjon meg egy parancsfájlt, amely megfordítja őket.

### <a name="script-action-in-the-cluster-creation-process"></a>A fürtlétrehozás a parancsfájlművelet

A fürt létrehozásakor használt szkriptműveletek némileg eltérnek szkriptműveletek futtathat egy meglévő fürtön:

* A parancsfájlt a rendszer automatikusan megőrzi.

* A parancsprogram hibát okozhat a Fürtlétrehozási folyamat sikertelen lesz.

A következő ábra szemlélteti a létrehozási folyamat során parancsfájlművelet futtatásakor:

![HDInsight-fürt testreszabása és -szakaszok a fürt létrehozásakor][img-hdi-cluster-states]

A szkript futtatása, amíg megtörténik a HDInsight konfigurálása folyamatban van. A parancsfájl párhuzamosan fut a megadott csomópontok a fürtben. A csomópontok-gyökér szintű jogosultság fut.

> [!NOTE]  
> Például a szolgáltatások, többek között az Apache Hadoop-hez kapcsolódó szolgáltatások indítása és leállítása műveleteket hajthat végre. Ha leállítja a szolgáltatások, ügyeljen arra, hogy az Ambari szolgáltatás és más Hadoop-hez kapcsolódó szolgáltatások futnak a parancsfájl befejezése előtt. Ezek a szolgáltatások állapotának és a fürt állapotának sikeresen meghatározása, létrehozása közben van szükség.


Fürt létrehozása során egyszerre használhatja sok parancsfájlműveletekkel. Ezeket a parancsfájlokat a megadott is sorrendben kerül meghívásra.

> [!IMPORTANT]  
> Szkriptműveletek Befejezés 60 perc, vagy időtúllépés. Fürt üzembe helyezésekor, a parancsfájl futtatása egy időben alkalmazza a többi beállítási és konfigurációs folyamat. Verseny a erőforrások – például CPU-idő vagy a hálózati sávszélesség előfordulhat, hogy a parancsfájl, a fejlesztési környezet mint befejezése hosszabb időt vesz igénybe.
>
> A parancsfájl futtatásához szükséges idő minimalizálása érdekében ne fordítása a forrásból származó alkalmazások letöltése és egyéb feladatokhoz. Üzembehelyezésnél fordítson elő az alkalmazásokat, és tárolja a bináris fájlt az Azure Storage-ban.


### <a name="script-action-on-a-running-cluster"></a>Futó fürtökön parancsfájlműveletekkel

Hiba történt a parancsfájl egy már fut a fürtön futnak automatikusan nem okozza a fürt módosítása sikertelen állapotba. A szkript befejezése után a fürt egy működőképes állapotba térjen vissza.

> [!IMPORTANT]  
> Akkor is, ha a fürt rendelkezik egy működőképes állapotba, előfordulhat, hogy a hibás parancsfájl megszakítják dolog. A parancsfájl például esetleg törli a fürt számára szükséges fájlokat.
>
> Parancsfájlok műveletek gyökérszintű jogosultságokkal futtassa. Győződjön meg arról, hogy megértette a parancsfájl funkciója, a fürthöz való alkalmazása előtt.

Amikor alkalmazza a parancsfájl fürtre, a fürt állapota **futó** való **elfogadva**. Állapotúra változik majd **HDInsight konfigurációs** , és végül térjen **futó** sikeres parancsprogramok. A szkript állapotát a parancsfájlművelet előzményeinek kerül. Ezt az információt arra kéri, hogy a parancsfájl sikeres vagy sikertelen volt. Ha például a `Get-AzHDInsightScriptActionHistory` PowerShell-parancsmagot egy parancsfájl állapotát jeleníti meg. Ez hasonló információt ad vissza a következő szöveget:

    ScriptExecutionId : 635918532516474303
    StartTime         : 8/14/2017 7:40:55 PM
    EndTime           : 8/14/2017 7:41:05 PM
    Status            : Succeeded

> [!IMPORTANT]  
> Ha módosítja a fürt felhasználójának, a rendszergazda, a jelszót, és a fürt létrehozása után, a futtatásához a fürtön szkriptműveletek meghiúsulhat. Ha bármely megőrzött Parancsfájlműveletek adott cél feldolgozó csomóponttal rendelkezik, ezek a szkriptek meghiúsulhat, ha a fürt.

## <a name="example-script-action-scripts"></a>Példa parancsfájl művelet parancsfájlok

Parancsprogram-művelet parancsfájlok keresztül az alábbi segédprogramokat használhatja:

* Az Azure Portal
* Azure PowerShell
* Az Azure klasszikus parancssori felület
* An HDInsight .NET SDK

HDInsight parancsprogramokkal történő telepítését a következő összetevőket a HDInsight-fürtökön biztosítja:

| Name (Név) | Szkript |
| --- | --- |
| Azure Storage-fiók hozzáadása |`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`. Lásd: [további tárfiókok hozzáadása a HDInsight](hdinsight-hadoop-add-storage.md). |
| A Hue telepítése |`https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh`. Lásd: [telepítése és használata a Hue HDInsight hadoop-fürtök](hdinsight-hadoop-hue-linux.md). |
| A Giraph telepítése |`https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh`. Lásd: [Apache Giraph telepítése HDInsight hadoop-fürtök](hdinsight-hadoop-giraph-install-linux.md). |
| Az előzetes betöltés Hive-kódtárak |`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh`. Lásd: [egyéni Apache Hive-kódtárak hozzáadása a HDInsight-fürt létrehozásakor](hdinsight-hadoop-add-hive-libraries.md). |

## <a name="use-a-script-action-during-cluster-creation"></a>Fürt létrehozása során egy parancsfájlművelettel

Ez a szakasz ismerteti a különböző módokon használhatja a Parancsfájlműveletek egy HDInsight-fürt létrehozásakor.

### <a name="use-a-script-action-during-cluster-creation-from-the-azure-portal"></a>Használjon szkriptműveletet az Azure Portalról a fürt létrehozásakor

1. Indítsa el a fürt létrehozása [fürtök beállítása a HDInsight az Apache Hadoop, az Apache Spark, az Apache Kafka és további](hdinsight-hadoop-provision-linux-clusters.md). Fürt létrehozása során, a kiszolgálófarmban egy __fürt összegzése__ lapot. Az a __fürt összegzése__ lapon válassza ki a __szerkesztése__ hozzárendeli a __speciális beállítások__.

    ![Speciális beállítások hivatkozása](./media/hdinsight-hadoop-customize-cluster-linux/advanced-settings-link.png)

3. Az a __speciális beállítások__ szakaszban jelölje be __Szkriptműveletek__. Az a __Szkriptműveletek__ szakaszban jelölje be __+ új küldés__.

    ![Egy új szkriptművelet elküldéséhez](./media/hdinsight-hadoop-customize-cluster-linux/add-script-action.png)

4. Használja a __válassza ki a parancsprogramot__ bejegyzésben válassza ki az előzetesen parancsfájlt. Egyéni parancsfájl használata, válassza ki a __egyéni__. Adja meg a __neve__ és __Bash parancsfájl URI azonosítója__ a parancsfájlt.

    ![Parancsprogram hozzáadásához válassza ki a parancsfájl formájában](./media/hdinsight-hadoop-customize-cluster-linux/select-script.png)

    A következő táblázat ismerteti az elemeket az űrlapon:

    | Tulajdonság | Érték |
    | --- | --- |
    | Szkript kiválasztása | A saját parancsfájl használatára, válassza ki a __egyéni__. Ellenkező esetben válassza a megadott szkriptek közül. |
    | Name (Név) |Adja meg a parancsfájlművelet nevét. |
    | Bash parancsfájl URI azonosítója |Adja meg a szkript URI azonosítója. |
    | Head/Worker vagy ZooKeeper |Adja meg a csomópontok, amelyen a szkript fut: **Head**, **Worker**, vagy **ZooKeeper**. |
    | Paraméterek |Adja meg a paramétereket, ha a parancsfájl által igényelt. |

    Használja a __parancsfájlműveletet__ bejegyzést, győződjön meg arról, hogy érvényesek-e a parancsfájl méretezési műveletek során.

5. Válassza ki __létrehozás__ a parancsfájl mentéséhez. Ezt követően használhatja __+ új küldés__ , adja meg egy másik szkriptet.

    ![Több parancsprogram-művelet](./media/hdinsight-hadoop-customize-cluster-linux/multiple-scripts.png)

    Hozzáadás a parancsfájlok elkészült, válassza ki a __válassza ki__ gombra, majd a __tovább__ gombra kattintva visszatérhet a __fürt összegzése__ szakaszban.

3. A fürt létrehozásához válassza __létrehozás__ származó a __fürt összegzése__ kiválasztása.

### <a name="use-a-script-action-from-azure-resource-manager-templates"></a>Használjon szkriptműveletet az Azure Resource Manager-sablonok

A szkriptműveletek használható az Azure Resource Manager-sablonok. Egy vonatkozó példáért lásd: [HDInsight Linux-fürt létrehozása és futtatása szkriptműveletet](https://azure.microsoft.com/resources/templates/hdinsight-linux-run-script-action/).

Ebben a példában a parancsfájlművelet egészül ki az alábbi kód használatával:

```json
"scriptActions": [
    {
        "name": "setenvironmentvariable",
        "uri": "[parameters('scriptActionUri')]",
        "parameters": "headnode"
    }
]
```

További információ a sablon üzembe helyezése:

* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)

* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli)

### <a name="use-a-script-action-during-cluster-creation-from-azure-powershell"></a>Használjon szkriptműveletet az Azure PowerShell a fürt létrehozásakor

Ebben a szakaszban használhatja a [Add-AzHDInsightScriptAction](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightscriptaction) parancsmag segítségével szabhatja testre a fürthöz-szkriptek meghívása. Mielőtt elkezdené, győződjön meg arról, telepítése és konfigurálása az Azure PowerShell-lel. A PowerShell-parancsok használatához van szüksége a [AZ modul](https://docs.microsoft.com/powershell/azure/overview).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az alábbi parancsfájl bemutatja, hogyan szkriptműveletet alkalmazni, amikor a fürt létrehozása a PowerShell használatával:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=5-90)]

Mielőtt a fürt létrehozása több percet is igénybe vehet.

### <a name="use-a-script-action-during-cluster-creation-from-the-hdinsight-net-sdk"></a>A HDInsight .NET SDK-t a fürt létrehozása során egy parancsfájlművelettel

A HDInsight .NET SDK-t biztosít, amelyek megkönnyítik a HDInsight használata a .NET-alkalmazásból klienskódtárak. A kód minta: [létrehozása Linux-alapú fürtök a HDInsight .NET SDK használatával](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action).

## <a name="apply-a-script-action-to-a-running-cluster"></a>Egy futó fürt szkriptműveletet vonatkozik

Ez a szakasz ismerteti, hogyan parancsfájl műveleteket alkalmazhat a futó fürt.

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-portal"></a>Egy futó fürt szkriptműveletet alkalmazni az Azure Portalról

Nyissa meg a [az Azure portal](https://portal.azure.com):

1. A bal oldali menüben válassza ki a **minden szolgáltatás**.

1. A **ANALYTICS**válassza **HDInsight-fürtök**.

1. Válassza ki a fürtöt a listából, amely az alapértelmezett nézetének megnyitása.

1. Az alapértelmezett nézet a csoportban **beállítások**válassza **Szkriptműveletek**.

1. A felső részén a **Szkriptműveletek** lapon jelölje be **+ új küldés**.

    ![Parancsfájl hozzáadása egy futó fürt](./media/hdinsight-hadoop-customize-cluster-linux/add-script-running-cluster.png)

4. Használja a __válassza ki a parancsprogramot__ bejegyzésben válassza ki az előzetesen parancsfájlt. Egyéni parancsfájl használata, válassza ki a __egyéni__. Adja meg a __neve__ és __Bash parancsfájl URI azonosítója__ a parancsfájlt.

    ![Parancsprogram hozzáadásához válassza ki a parancsfájl formájában](./media/hdinsight-hadoop-customize-cluster-linux/select-script.png)

    A következő táblázat ismerteti az elemeket az űrlapon:

    | Tulajdonság | Érték |
    | --- | --- |
    | Szkript kiválasztása | A saját parancsfájl használatára, válassza ki a __egyéni__. Ellenkező esetben válassza ki a megadott parancsfájlt. |
    | Name (Név) |Adja meg a parancsfájlművelet nevét. |
    | Bash parancsfájl URI azonosítója |Adja meg a szkript URI azonosítója. |
    | A fő/feldolgozó vagy Zookeeper |Adja meg a csomópontok, amelyen a szkript fut: **A fő**, **feldolgozó**, vagy **ZooKeeper**. |
    | Paraméterek |Adja meg a paramétereket, ha a parancsfájl által igényelt. |

    Használja a __parancsfájlműveletet__ bejegyzést, hogy a parancsfájl a méretezési műveletek során mikor lesz alkalmazva.

5. Végül válassza ki a **létrehozás** gombra kattintva a alkalmazni a parancsfájl a fürthöz.

### <a name="apply-a-script-action-to-a-running-cluster-from-azure-powershell"></a>Egy futó fürt szkriptműveletet alkalmazni az Azure PowerShell-lel

A PowerShell-parancsok használatához van szüksége a [AZ modul](https://docs.microsoft.com/powershell/azure/overview).

Az alábbi példa bemutatja, hogyan szkriptműveletet alkalmazandó futó fürt:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=105-117)]

A művelet befejezése után az alábbi szöveghez hasonló információt kap:

    OperationState  : Succeeded
    ErrorMessage    :
    Name            : Giraph
    Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
    Parameters      :
    NodeTypes       : {HeadNode, WorkerNode}

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-cli"></a>Egy futó fürt szkriptműveletet alkalmazni az Azure parancssori felületen

Mielőtt elkezdené, győződjön meg arról, telepítése és konfigurálása az Azure parancssori felület. További információkért lásd: [a klasszikus Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-classic-cli?view=azure-cli-latest).

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

1. Váltson Azure Resource Manager módra:

    ```bash
    azure config mode arm
    ```

2. Hitelesítés az Azure-előfizetéshez:

    ```bash
    azure login
    ```

3. Egy futó fürt szkriptműveletet vonatkoznak:

    ```bash
    azure hdinsight script-action create <clustername> -g <resourcegroupname> -n <scriptname> -u <scriptURI> -t <nodetypes>
    ```

    Ha kihagyja ezt a parancsot a paramétereket, kér őket. Ha a parancsfájl adja meg a `-u` fogad el paramétereket, használatával megadhatja azokat a `-p` paraméter.

    Érvényes csomópont-típusok a következők `headnode`, `workernode`, és `zookeeper`. Ha a parancsfájl több csomóponttípusok kell alkalmazni, adja meg pontosvesszővel elválasztva típusok `;`. Például: `-n headnode;workernode`.

    A parancsfájl megőrizni, adjon hozzá `--persistOnSuccess`. Emellett megőrizheti a parancsfájl később használatával `azure hdinsight script-action persisted set`.

    A feladat befejezése után megjelenik a következő szöveg hasonló kimenet:

        info:    Executing command hdinsight script-action create
        + Executing Script Action on HDInsight cluster
        data:    Operation Info
        data:    ---------------
        data:    Operation status:
        data:    Operation ID:  b707b10e-e633-45c0-baa9-8aed3d348c13
        info:    hdinsight script-action create command OK

### <a name="apply-a-script-action-to-a-running-cluster-by-using-rest-api"></a>Egy futó fürt szkriptműveletet alkalmazása REST API használatával

Lásd: [REST API-t az Azure HDInsight-fürt](https://msdn.microsoft.com/library/azure/mt668441.aspx).

### <a name="apply-a-script-action-to-a-running-cluster-from-the-hdinsight-net-sdk"></a>Egy futó fürt a HDInsight .NET SDK-ból szkriptműveletet vonatkozik

A .NET SDK használatával történő parancsfájlok alkalmazni a fürt egy példa: [a alkalmazni egy futó Linux-alapú HDInsight-fürtön hajtja Szkriptműveletet](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

## <a name="view-history-and-promote-and-demote-script-actions"></a>Előzmények megtekintése és előléptetése és lefokozása Parancsfájlműveletek

### <a name="the-azure-portal"></a>Az Azure Portal

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A bal oldali menüben válassza ki a **minden szolgáltatás**.

1. A **ANALYTICS**válassza **HDInsight-fürtök**.

1. Válassza ki a fürtöt a listából, amely az alapértelmezett nézetének megnyitása.

1. Az alapértelmezett nézet a csoportban **beállítások**válassza **Szkriptműveletek**.

4. Ehhez a fürthöz-szkriptek előzményeit jeleníti meg a parancsfájl műveletek szakaszban. Ezen információk közé tartozik a megőrzött szkriptek listáját. Az alábbi képernyőképen látható, hogy a Solr szkript futtatása a fürtön. A képernyőfelvételen bármely a megőrzött szkriptek nem jelenik meg.

    ![A Parancsfájlműveletek](./media/hdinsight-hadoop-customize-cluster-linux/script-action-history.png)

5. Válassza ki a parancsfájl megjelenítése az előzményekben tekintheti át a **tulajdonságok** Ez a szkript a következő szakaszban. A képernyő tetején futtassa újra a parancsfájlt, vagy léptetheti elő.

    ![Szkriptműveletek tulajdonságai](./media/hdinsight-hadoop-customize-cluster-linux/promote-script-actions.png)

6. Választhatja a három pontra **...** , jobb oldalán a parancsfájl műveletek szakaszban lévő bejegyzéseket műveletek végrehajtásához.

    ![A szkriptműveletek, három pontot](./media/hdinsight-hadoop-customize-cluster-linux/deletepromoted.png)

### <a name="azure-powershell"></a>Azure PowerShell

| A parancsmag | Függvény |
| --- | --- |
| `Get-AzHDInsightPersistedScriptAction` |Megőrzött Parancsfájlműveletek információkat lekérni. |
| `Get-AzHDInsightScriptActionHistory` |A fürt vagy egy adott parancsfájl részleteinek alkalmazott szkriptműveletek előzményeit lekéréséhez. |
| `Set-AzHDInsightPersistedScriptAction` |Megőrzött parancsfájlművelet-ad-hoc parancsfájlművelet előléptetése. |
| `Remove-AzHDInsightPersistedScriptAction` |Ad-hoc művelet egy megőrzött parancsfájlművelet lefokozása. |

> [!IMPORTANT]  
> `Remove-AzHDInsightPersistedScriptAction` a parancsfájl által végrehajtott műveletek nem vonható vissza. Ez a parancsmag csak a megőrzött jelző távolítja el.

Az alábbi példa parancsfájl mutatja be, a parancsmagokkal előléptetése és lefokozása majd a parancsfájlt.

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=123-140)]

### <a name="the-azure-classic-cli"></a>Az Azure klasszikus parancssori felület

| A parancsmag | Függvény |
| --- | --- |
| `azure hdinsight script-action persisted list <clustername>` |Megőrzött Parancsfájlműveletek listájának lekéréséhez. |
| `azure hdinsight script-action persisted show <clustername> <scriptname>` |Egy adott megőrzött parancsfájlművelet információkat lekérni. |
| `azure hdinsight script-action history list <clustername>` |A fürt a alkalmazni szkriptműveletek előzményeit lekéréséhez. |
| `azure hdinsight script-action history show <clustername> <scriptname>` |Egy adott szkriptművelet információkat lekérni. |
| `azure hdinsight script action persisted set <clustername> <scriptexecutionid>` |Megőrzött parancsfájlművelet-ad-hoc parancsfájlművelet előléptetése. |
| `azure hdinsight script-action persisted delete <clustername> <scriptname>` |Ad-hoc művelet egy megőrzött parancsfájlművelet lefokozása. |

> [!IMPORTANT]  
> `azure hdinsight script-action persisted delete` a parancsfájl által végrehajtott műveletek nem vonható vissza. Ez a parancsmag csak a megőrzött jelző távolítja el.

### <a name="the-hdinsight-net-sdk"></a>The HDInsight .NET SDK

A .NET SDK használatával parancsfájl előzményeinek lekérése a fürt egy példát, előléptetése vagy parancsfájlok lefokozása, lásd: [ a alkalmazni egy futó Linux-alapú HDInsight-fürtön hajtja Szkriptműveletet](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

> [!NOTE]  
> Ebben a példában is bemutatja, hogyan telepíthet HDInsight alkalmazásokat a .NET SDK-val.

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>A HDInsight-fürtökön használt nyílt forráskódú szoftverek támogatása

A Microsoft Azure HDInsight szolgáltatást használ az ökoszisztéma formátumú körül Apache Hadoop nyílt forráskódú technológiák. A Microsoft Azure nyílt forráskódú technológiák egy általános szintű támogatást biztosít. További információkért lásd: a **támogatás hatóköre** szakaszában [Azure támogatás – gyakori kérdések](https://azure.microsoft.com/support/faq/). A HDInsight szolgáltatás egy további szintű támogatást biztosít a beépített összetevők.

Kétféle típusú nyílt forráskódú összetevők a HDInsight szolgáltatásban érhetők el:

* **Beépített összetevők**. Ezeket az összetevőket a HDInsight-fürtökön az előtelepített, és adja meg a fürt fő funkciói. A következő összetevők ebbe a kategóriába tartoznak:

  * [Az Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) erőforrás-kezelő.
  * A Hive query language [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual).
  * [Az Apache Mahout](https://mahout.apache.org/). 
    
    Kiszolgálófürt-összetevők teljes listája megtalálható [Mik azok az Apache Hadoop-összetevők és verziók a HDInsight?](hdinsight-component-versioning.md)

* **Egyéni összetevők**. A fürt felhasználóként telepítse, vagy használja a számítási feladatok valamelyik összetevő a Közösségben elérhető vagy Ön által létrehozott.

> [!WARNING]  
> A HDInsight-fürthöz megadott összetevők teljes mértékben támogatottak. Support segít elkülöníteni, és ezeket az összetevőket kapcsolatos problémák megoldásához.
>
> Egyéni összetevők segítséget a probléma további hibaelhárításához üzletileg ésszerű támogatást kapnak. Lehet, hogy a probléma megoldásához Support. Vagy előfordulhat, hogy megadását is végezhetnek elérhető csatornák a nyílt forráskódú technológiák, ahol található részletes szakértelmét, hogy a technológiát. Számos közösségi helyek is használható. Példa [HDInsight az MSDN-fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight) és [Stack Overflow](https://stackoverflow.com). 
>
> Apache projektek is rendelkeznek projekt helyek a [Apache webhely](https://apache.org). Például [Hadoop](https://hadoop.apache.org/).

A HDInsight szolgáltatás használatához az egyéni összetevők több módot nyújt. Az azonos szintű támogatást vonatkozik, függetlenül attól, hogy hogyan összetevő használt vagy a fürtön telepíteni. Az alábbi lista ismerteti a leggyakoribb módja, hogy a HDInsight-fürtökön használják egyéni összetevők:

1. **Feladat küldése**. Hadoop és egyéb feladatok végrehajtása, vagy használjon egyéni összetevők küldheti el a fürtöt.

2. **Fürt testreszabása**. Fürt létrehozása során további beállításokat és egyéni, a fürtcsomópontokon telepített összetevőket is megadhat.

3. **A minták**. Népszerű egyéni összetevők a Microsofttól és előfordulhat, hogy adja meg ezeket az összetevőket hogyan használható a HDInsight-fürtökön mintáit. Ezek a minták támogatás nélkül állnak rendelkezésre.

## <a name="troubleshooting"></a>Hibaelhárítás

Az Ambari webes felhasználói felület segítségével szkriptműveletek által naplózott adatok megtekintéséhez. Ha a parancsfájl futása sikertelen, a fürt létrehozásakor, a naplók is elérhetők a fürthöz társított alapértelmezett tárfiókban. Ez a szakasz információt nyújt az a naplók begyűjtéséről mindkét ezek a beállítások használatával.

### <a name="the-apache-ambari-web-ui"></a>Az Apache Ambari webes Felülettel

1. A böngészőben nyissa meg https://CLUSTERNAME.azurehdinsight.net. Cserélje le a **CLUSTERNAME** kifejezést a HDInsight-fürt nevére.

    Amikor a rendszer kéri, adja meg a rendszergazdai fiók nevét, **rendszergazdai**, és a fürthöz tartozó jelszót. Előfordulhat, hogy a rendszergazdai hitelesítő adatok webes űrlapon való újbóli.

2. Az oldal tetején lévő sávon válassza a **ops** bejegyzés. Listáját jeleníti meg a jelenlegi és korábbi műveletek ambarival a fürtön végzett.

    ![Az Ambari webes felhasználói felület sávon kicserélhet ops kiválasztva](./media/hdinsight-hadoop-customize-cluster-linux/ambari-nav.png)

3. Keresse meg a bejegyzéseket, amelyek **futtatása\_customscriptaction** a a **műveletek** oszlop. Ezek a bejegyzések a Parancsfájlműveletek futásakor jönnek létre.

    ![Képernyőkép a műveletek](./media/hdinsight-hadoop-customize-cluster-linux/ambariscriptaction.png)

    Megtekintéséhez a **STDOUT** és **STDERR** kimeneti, válassza ki a **run\customscriptaction** bejegyzést, és jelenítse meg a hivatkozások le. Ez a kimenet jön létre a parancsfájl futtatásakor, és előfordulhat, hogy hasznos információkat.

### <a name="access-logs-from-the-default-storage-account"></a>Az alapértelmezett tárfiókot a naplók elérése

Ha fürt létrehozása egy parancsfájl hiba miatt nem sikerül, a naplók a fürt tárfiókban őrzi meg.

* A storage-naplók esetén érhető el `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE`.

    ![Képernyőkép a műveletek](./media/hdinsight-hadoop-customize-cluster-linux/script_action_logs_in_storage.png)

    Ebben a könyvtárban, a naplók vannak rendszerezve külön-külön az **átjárócsomópontjával**, **munkavégző csomópont**, és **zookeeper-csomópont**. Lásd az alábbi példákat:

    * **Átjárócsomópont**: `<uniqueidentifier>AmbariDb-hn0-<generated_value>.cloudapp.net`

    * **Munkavégző csomópont**: `<uniqueidentifier>AmbariDb-wn0-<generated_value>.cloudapp.net`

    * **Zookeeper-csomópont**: `<uniqueidentifier>AmbariDb-zk0-<generated_value>.cloudapp.net`

* Az összes **stdout** és **stderr** a megfelelő gazdagép töltenek fel a tárfiók. Van egy **kimeneti -\*.txt** és **hibák –\*.txt** minden parancsfájlművelethez. A **kimeneti-*.txt** fájlt a parancsfájl a gazdagépen futó az URI-t információt tartalmaz. A következő szöveg egy példa ezt az információt:

        'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'

* Akkor lehet, hogy ismételten parancsfájl művelet fürt létrehozása ezzel a névvel. Ebben az esetben, hogy megkülönböztethesse a megfelelő naplók alapján a **dátum** mappa neve. Például a fürt mappastruktúra **sajátfürt**létrehozott különböző időpontokban, a következő naplóbejegyzéseket hasonlóan jelenik meg:

    `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04` `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* Ha egy parancsfájl művelet fürtöt hoz létre ugyanazzal a névvel ugyanarra a napra, az egyedi előtag segítségével azonosíthatja a megfelelő naplófájlok helyét.

* Ha 12:00-kor, éjfélkor, közel-fürtöt hoz létre, lehetséges, hogy a naplófájlok span két napon keresztül. Ebben az esetben két különböző mappája ugyanazon a fürtön a láthatja.

* Naplófájlok feltöltése az alapértelmezett tároló akár öt percet, különösen nagyméretű fürtök esetén is igénybe vehet. Tehát ha azt szeretné, a naplók eléréséhez, nem közvetlenül a fürt törlésekor szkriptműveletet meghibásodásakor.

### <a name="ambari-watchdog"></a>Ambari-figyelő

> [!WARNING]  
> Ne módosítsa a jelszót az Ambari figyelő, hdinsightwatchdog, a Linux-alapú HDInsight-fürtön. Ez a fiók jelszavának módosítása működésképtelenné válik új Parancsfájlműveletek futtatására a HDInsight-fürtön.

### <a name="cant-import-name-blobservice"></a>BlobService neve nem lehet importálni.

__A jelenség__. A parancsprogram-művelet sikertelen lesz. A következő hibához hasonló szöveg megtekintheti a művelet az Ambari jeleníti meg:

```
Traceback (most recent call list):
  File "/var/lib/ambari-agent/cache/custom_actions/scripts/run_customscriptaction.py", line 21, in <module>
    from azure.storage.blob import BlobService
ImportError: cannot import name BlobService
```

__OK__. Ez a hiba akkor fordul elő, ha a Python Azure Storage ügyfelet frissít, amely a HDInsight-fürt foglalt. HDInsight az Azure Storage-kliens 0.20.0 vár.

__Feloldási__. Ez a hiba elhárításához manuálisan létesíthetnek az egyes fürtcsomópontokhoz `ssh`. Futtassa a következő paranccsal telepítse újra a helyes tárfiók-ügyfél verziója:

```bash
sudo pip install azure-storage==0.20.0
```

Csatlakozás a fürthöz az ssh-val kapcsolatos információkért lásd: [HDInsight (az Apache Hadoop) SSH használatával csatlakozhat](hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="history-doesnt-show-the-scripts-used-during-cluster-creation"></a>Előzmények nem jelenik meg a fürt létrehozásakor használt parancsfájlokra

A fürt létrejött, 2016. március 15. előtt nem láthatja a parancsfájlművelet előzményeinek bejegyzés. A fürt átméretezése hatására a szkripteket a parancsfájlművelet előzményeinek jelennek meg.

Ez alól két kivétel van:

* A fürt létrejött, 2015. szeptember 1. előtt. Dátum, amikor szkriptműveletek jelentek meg. Tetszőleges dátum előtt létrehozott fürt nem használt szkriptműveletek a fürtlétrehozáshoz.

* A fürt létrehozásakor használt több parancsprogram-művelet. Vagy több parancsfájl több parancsfájlt vagy a ugyanazzal a névvel, ugyanilyen URI, de különböző paraméterek ugyanazt a nevet is használ. Ezekben az esetekben a következő hibaüzenetet kapja:

    Nincs új Parancsfájlműveletek ütköző parancsfájl nevét a meglévő szkriptekre miatt futtathatja a fürtön. Fürt létrehozásakor megadott parancsfájl nevének valamennyi egyedinek kell lennie. Meglévő parancsfájlok a átméretezése.

## <a name="next-steps"></a>További lépések

* [Parancsfájl művelet-parancsfájlok fejlesztése a HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Telepítse, és az Apache Giraph használata a HDInsight-fürtökön](hdinsight-hadoop-giraph-install-linux.md)
* [Adjon hozzá további tárterületet HDInsight-fürthöz](hdinsight-hadoop-add-storage.md)

[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/HDI-Cluster-state.png "Fürt létrehozása során szakaszai"
