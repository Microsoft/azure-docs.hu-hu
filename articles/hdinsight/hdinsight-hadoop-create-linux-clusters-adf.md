---
title: 'Oktatóanyag: igény szerinti fürtök az Azure HDInsight-ban Data Factory'
description: Oktatóanyag – megtudhatja, hogyan hozhat létre igény szerinti Apache Hadoop-fürtöket a HDInsight-ben Azure Data Factory használatával.
ms.service: hdinsight
ms.topic: tutorial
ms.custom: seoapr2020
ms.date: 04/24/2020
ms.openlocfilehash: 762938ebb4785a54224771e96c5bca274721dc30
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98945973"
---
# <a name="tutorial-create-on-demand-apache-hadoop-clusters-in-hdinsight-using-azure-data-factory"></a>Oktatóanyag: igény szerinti Apache Hadoop-fürtök létrehozása a HDInsight-ben Azure Data Factory használatával

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre igény szerinti [Apache Hadoop](../hdinsight/hdinsight-overview.md#cluster-types-in-hdinsight) -fürtöt az Azure HDInsight Azure Data Factory használatával. Ezután a Azure Data Factoryban lévő adatfolyamatok használatával futtathatja a kaptár-feladatokat, és törölheti a fürtöt. Ebből az oktatóanyagból megtudhatja, hogyan `operationalize` hajthat végre egy Big Data-feladatot, ahol a fürt létrehozása, a feladatok futtatása és a fürt törlése ütemezhető.

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Azure-tárfiók létrehozása
> * Azure Data Factory tevékenység ismertetése
> * Adatelőállító létrehozása Azure Portal használatával
> * Társított szolgáltatások létrehozása
> * Folyamat létrehozása
> * Folyamat aktiválása
> * Folyamat monitorozása
> * Kimenet ellenőrzése

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* A PowerShell az [modul](/powershell/azure/) telepítve van.

* Egy Azure Active Directory egyszerű szolgáltatás. Miután létrehozta a szolgáltatásnevet, ügyeljen rá, hogy az **alkalmazás azonosítóját** és a **hitelesítési kulcsot** a csatolt cikkben található utasítások alapján kérje le. Ezekre az értékekre később szüksége lesz az oktatóanyagban. Győződjön meg arról is, hogy az egyszerű szolgáltatás tagja az előfizetés *közreműködői* szerepkörének, vagy az az erőforráscsoport, amelyben a fürt létrejött. A szükséges értékek lekérésére és a megfelelő szerepkörök hozzárendelésére vonatkozó utasításokért lásd: [Azure Active Directory egyszerű szolgáltatásnév létrehozása](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="create-preliminary-azure-objects"></a>Előzetes Azure-objektumok létrehozása

Ebben a szakaszban különféle objektumokat hoz létre, amelyeket az igény szerint létrehozott HDInsight-fürthöz fog használni. A létrehozott Storage-fiók tartalmazni fogja a minta HiveQL parancsfájlt, `partitionweblogs.hql` amely a fürtön futó példa Apache Hive-feladatok szimulálására használható.

Ez a szakasz egy Azure PowerShell parancsfájllal hozza létre a Storage-fiókot, és átmásolja a szükséges fájlokat a Storage-fiókon belül. A jelen szakaszban található Azure PowerShell-minta parancsfájl a következő feladatokat hajtja végre:

1. Bejelentkezik az Azure-ba.
2. Létrehoz egy Azure-erőforráscsoportot.
3. Létrehoz egy Azure Storage-fiókot.
4. BLOB-tároló létrehozása a Storage-fiókban
5. A HiveQL parancsfájl (**partitionweblogs. HQL**) másolása a blob-tárolóból. A szkript a következő címen érhető el: [https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql) . A minta parancsfájl már elérhető egy másik nyilvános blob-tárolóban. Az alábbi PowerShell-parancsfájl másolatot készít a fájlokról az általa létrehozott Azure Storage-fiókba.

### <a name="create-storage-account-and-copy-files"></a>Storage-fiók létrehozása és fájlok másolása

> [!IMPORTANT]  
> Adja meg az Azure-erőforráscsoport és a parancsfájl által létrehozandó Azure Storage-fiók nevét.
> Írja le az **erőforráscsoport nevét**, a **Storage-fiók nevét** és a Storage- **fiók kulcsát** , amelyet a parancsfájl kibont. Ezekre a következő szakaszban lesz szükség.

```powershell
$resourceGroupName = "<Azure Resource Group Name>"
$storageAccountName = "<Azure Storage Account Name>"
$location = "East US"

$sourceStorageAccountName = "hditutorialdata"  
$sourceContainerName = "adfv2hiveactivity"

$destStorageAccountName = $storageAccountName
$destContainerName = "adfgetstarted" # don't change this value.

####################################
# Connect to Azure
####################################
#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

#endregion

####################################
# Create a resource group, storage, and container
####################################

#region - create Azure resources
Write-Host "`nCreating resource group, storage account and blob container ..." -ForegroundColor Green

New-AzResourceGroup `
    -Name $resourceGroupName `
    -Location $location

New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $destStorageAccountName `
    -Kind StorageV2 `
    -Location $location `
    -SkuName Standard_LRS `
    -EnableHttpsTrafficOnly 1

$destStorageAccountKey = (Get-AzStorageAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $destStorageAccountName)[0].Value

$sourceContext = New-AzStorageContext `
    -StorageAccountName $sourceStorageAccountName `
    -Anonymous

$destContext = New-AzStorageContext `
    -StorageAccountName $destStorageAccountName `
    -StorageAccountKey $destStorageAccountKey

New-AzStorageContainer `
    -Name $destContainerName `
    -Context $destContext
#endregion

####################################
# Copy files
####################################
#region - copy files
Write-Host "`nCopying files ..." -ForegroundColor Green

$blobs = Get-AzStorageBlob `
    -Context $sourceContext `
    -Container $sourceContainerName `
    -Blob "hivescripts\hivescript.hql"

$blobs|Start-AzStorageBlobCopy `
    -DestContext $destContext `
    -DestContainer $destContainerName `
    -DestBlob "hivescripts\partitionweblogs.hql"

Write-Host "`nCopied files ..." -ForegroundColor Green
Get-AzStorageBlob `
    -Context $destContext `
    -Container $destContainerName
#endregion

Write-host "`nYou will use the following values:" -ForegroundColor Green
write-host "`nResource group name: $resourceGroupName"
Write-host "Storage Account Name: $destStorageAccountName"
write-host "Storage Account Key: $destStorageAccountKey"

Write-host "`nScript completed" -ForegroundColor Green
```

### <a name="verify-storage-account"></a>Storage-fiók ellenőrzése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A bal oldalon navigáljon az **összes szolgáltatás**  >  **általános**  >  **erőforráscsoporthoz**.
1. Válassza ki a PowerShell-parancsfájlban létrehozott erőforráscsoport-nevet. Ha túl sok erőforráscsoport van felsorolva, használja a szűrőt.
1. Az **Áttekintés** nézetből egyetlen erőforrás jelenik meg, hacsak nem osztja meg az erőforráscsoportot más projektekkel. Ez az erőforrás a korábban megadott névvel rendelkező Storage-fiók. Válassza ki a Storage-fiók nevét.
1. Válassza ki a **tárolók** csempét.
1. Válassza ki a **adfgetstarted** tárolót. Ekkor megjelenik egy nevű mappa **`hivescripts`** .
1. Nyissa meg a mappát, és győződjön meg róla, hogy tartalmazza a minta parancsfájlt, a **partitionweblogs. HQL** fájlt.

## <a name="understand-the-azure-data-factory-activity"></a>A Azure Data Factory tevékenység ismertetése

[Azure Data Factory](../data-factory/introduction.md) összehangolja és automatizálja az adatáthelyezést és-átalakítást. Azure Data Factory létrehozhat egy HDInsight Hadoop-fürtöt a bemeneti adatszelet feldolgozásához és a fürt törléséhez a feldolgozás befejezésekor.

Azure Data Factoryban egy adatfeldolgozó egy vagy több adatfolyamattal rendelkezhet. Az adatfolyamatok egy vagy több tevékenységgel rendelkeznek. A tevékenységek két típusa létezik:

* [Adatáthelyezési tevékenységek](../data-factory/copy-activity-overview.md). Az adatáthelyezési tevékenységekkel adatok helyezhetők át a forrás adattárból a célhely adattárba.
* [Az Adatátalakítási tevékenységek](../data-factory/transform-data.md). Az Adatátalakítási tevékenységek segítségével alakíthatja át vagy dolgozza fel az adatfeldolgozási tevékenységeket. A HDInsight kaptár tevékenység a Data Factory által támogatott átalakítási tevékenységek egyike. Ebben az oktatóanyagban a struktúra-átalakítási tevékenységet kell használnia.

Ebben a cikkben a struktúra tevékenységét konfigurálja egy igény szerinti HDInsight Hadoop-fürt létrehozásához. Amikor a tevékenység futtatja az adatfeldolgozást, a következő történik:

1. A rendszer automatikusan létrehoz egy An méretű HDInsight Hadoop-fürtöt a szelet feldolgozásához.

2. A bemeneti adatokat a rendszer egy HiveQL parancsfájl futtatásával dolgozza fel a fürtön. Ebben az oktatóanyagban a kaptár tevékenységhez társított HiveQL-szkript a következő műveleteket végzi el:

    * A meglévő táblát (*hivesampletable*) használja egy másik tábla **HiveSampleOut** létrehozásához.
    * Feltölti a **HiveSampleOut** táblát az eredeti *hivesampletable* megadott oszlopokkal.

3. A HDInsight Hadoop-fürt a feldolgozás befejeződése után törlődik, és a fürt üresjáratban van a beállított ideig (timeToLive-beállítás). Ha a következő adatszelet ebben a timeToLive üresjárati időben is elérhető, akkor a szelet feldolgozásához ugyanazt a fürtöt használja a rendszer.  

## <a name="create-a-data-factory"></a>Adat-előállító létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. A bal oldali menüben navigáljon az **`+ Create a resource`**  >  **elemzési**  >  **Data Factory** elemre.

    ![Azure Data Factory a portálon](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-azure-portal.png "Azure Data Factory a portálon")

3. Adja meg vagy válassza ki a következő értékeket az **új adatok gyári** csempéhez:

    |Tulajdonság  |Érték  |
    |---------|---------|
    |Név | Adja meg az adatelőállító nevét. A névnek globálisan egyedinek kell lennie.|
    |Verzió | Távozás: **v2**. |
    |Előfizetés | Válassza ki Azure-előfizetését. |
    |Erőforráscsoport | Válassza ki a PowerShell-parancsfájl használatával létrehozott erőforráscsoportot. |
    |Hely | A helyet a rendszer automatikusan a megadott helyre állítja be a korábban létrehozott erőforráscsoport létrehozásakor. Ebben az oktatóanyagban a hely az **USA keleti** régiójában van beállítva. |
    |GIT engedélyezése|Törölje a jelet a jelölőnégyzetből.|

    ![Azure Data Factory létrehozása a Azure Portal használatával](./media/hdinsight-hadoop-create-linux-clusters-adf/azure-portal-create-data-factory.png "Azure Data Factory létrehozása a Azure Portal használatával")

4. Válassza a **Létrehozás** lehetőséget. Az adatelőállítók létrehozása 2 – 4 perc közötti időt is igénybe vehet.

5. Miután létrehozta az adatelőállítót, az **üzembe helyezés sikeres** értesítés jelenik meg az **erőforrás keresése** gombbal.  Válassza az **erőforrás** megnyitása lehetőséget az Data Factory alapértelmezett nézetének megnyitásához.

6. Válassza a **létrehozás & a figyelő** lehetőséget a Azure Data Factory szerzői műveletek és figyelési portál elindításához.

    ![Azure Data Factory portál áttekintése](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-portal-overview.png "Azure Data Factory áttekintése")

## <a name="create-linked-services"></a>Társított szolgáltatások létrehozása

Ebben a szakaszban két társított szolgáltatást hoz létre az adatai-előállítóban.

* Egy **Azure Storage-beli társított szolgáltatást**, amely egy Azure Storage-fiókot társít az adat-előállítóhoz. Ezt a tárterületet csak az igény szerinti HDInsight-fürt használja. Emellett tartalmazza a fürtön futtatott struktúra-parancsfájlt is.
* **Igény szerinti HDInsight társított szolgáltatás**. A Azure Data Factory automatikusan létrehoz egy HDInsight-fürtöt, és futtatja a kaptár parancsfájlt. Ezt követően pedig törli a HDInsight-fürtöt, miután a fürt egy előre meghatározott ideig tétlen volt.

### <a name="create-an-azure-storage-linked-service"></a>Azure Storage-beli társított szolgáltatás létrehozása

1. Az **első lépések** oldal bal oldali paneljén válassza a **Szerző** ikont.

    ![Azure Data Factory társított szolgáltatás létrehozása](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-edit-tab.png "Azure Data Factory társított szolgáltatás létrehozása")

2. Válassza a **kapcsolatok** lehetőséget az ablak bal alsó sarkában, majd válassza az **+ új** lehetőséget.

    ![Kapcsolatok létrehozása Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-create-new-connection.png "Kapcsolatok létrehozása Azure Data Factory")

3. Az **új társított szolgáltatás** párbeszédpanelen válassza az **Azure Blob Storage** lehetőséget, majd kattintson a **Folytatás** gombra.

    ![Azure Storage-beli társított szolgáltatás létrehozása Data Factoryhoz](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-storage-linked-service.png "Azure Storage-beli társított szolgáltatás létrehozása Data Factoryhoz")

4. Adja meg a következő értékeket a Storage társított szolgáltatáshoz:

    |Tulajdonság |Érték |
    |---|---|
    |Név |Írja be a következő szöveget: `HDIStorageLinkedService`.|
    |Azure-előfizetés |Válassza ki az előfizetését a legördülő listából.|
    |Tárfiók neve |Válassza ki a PowerShell-parancsfájl részeként létrehozott Azure Storage-fiókot.|

    Válassza a **kapcsolatok tesztelése** és sikeres, majd a **Létrehozás** lehetőséget.

    ![Adja meg az Azure Storage társított szolgáltatás nevét](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-storage-linked-service-details.png "Adja meg az Azure Storage társított szolgáltatás nevét")

### <a name="create-an-on-demand-hdinsight-linked-service"></a>Igény szerinti HDInsight társított szolgáltatás létrehozása

1. Kattintson ismét az **+ Új** gombra egy további társított szolgáltatás létrehozásához.

2. Az **új társított szolgáltatás** ablakban válassza a **számítás** lapot.

3. Válassza az **Azure HDInsight** lehetőséget, majd kattintson a **Continue (folytatás**) gombra.

    ![HDInsight társított szolgáltatás létrehozása a Azure Data Factoryhoz](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-linked-service.png "HDInsight társított szolgáltatás létrehozása a Azure Data Factoryhoz")

4. Az **új társított szolgáltatás** ablakban adja meg a következő értékeket, és hagyja a többi értéket alapértelmezettként:

    | Tulajdonság | Érték |
    | --- | --- |
    | Név | Írja be a következő szöveget: `HDInsightLinkedService`.|
    | Típus | **Igény szerinti HDInsight** kiválasztása. |
    | Azure Storage társított szolgáltatás | Válassza a(z) `HDIStorageLinkedService` lehetőséget. |
    | Fürt típusa | **Hadoop** kiválasztása |
    | Élettartam | Adja meg azt az időtartamot, ameddig szeretné, hogy a HDInsight-fürt elérhető legyen az automatikus törlés előtt.|
    | Egyszerű szolgáltatásnév azonosítója | Adja meg az előfeltételek részeként létrehozott Azure Active Directory egyszerű szolgáltatásnév alkalmazás-AZONOSÍTÓját. |
    | Egyszerű szolgáltatásnév kulcsa | Adja meg a Azure Active Directory egyszerű szolgáltatásnév hitelesítési kulcsát. |
    | Fürt neve előtag | Adjon meg egy értéket, amely az összes, az adatelőállító által létrehozott fürt típusához előtaggal lesz ellátva. |
    |Előfizetés |Válassza ki az előfizetését a legördülő listából.|
    | Erőforráscsoport kiválasztása | Válassza ki a korábban használt PowerShell-parancsfájl részeként létrehozott erőforráscsoportot.|
    | Operációs rendszer típusa/fürt SSH-felhasználóneve | Írjon be egy SSH-felhasználónevet, általában `sshuser` . |
    | Operációs rendszer típusa/fürt SSH-jelszava | Adja meg az SSH-felhasználó jelszavát |
    | Operációs rendszer típusa/fürt felhasználóneve | Adja meg a fürthöz tartozó felhasználónevet, általában a nevet `admin` . |
    | Operációs rendszer típusa/fürt jelszava | Adja meg a fürt felhasználójának jelszavát. |

    Ezután kattintson a **Létrehozás** elemre.

    ![Adja meg a HDInsight társított szolgáltatás értékeit](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-linked-service-details.png "Adja meg a HDInsight társított szolgáltatás értékeit")

## <a name="create-a-pipeline"></a>Folyamat létrehozása

1. Válassza a **+** (plusz) gombot, majd válassza a **folyamat** elemet.

    ![Folyamat létrehozása Azure Data Factoryban](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-create-pipeline.png "Folyamat létrehozása Azure Data Factoryban")

1. A **tevékenységek** eszközkészletben bontsa ki a **HDInsight** csomópontot, és húzza a **kaptár** tevékenységet a folyamat tervező felületére. Az **általános** lapon adja meg a tevékenység nevét.

    ![Tevékenységek hozzáadása Data Factory folyamathoz](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-add-hive-pipeline.png "Tevékenységek hozzáadása Data Factory folyamathoz")

1. Győződjön meg arról, hogy a kaptár tevékenység van kiválasztva, majd válassza a **HDI-fürt** fület. A **HDInsight társított szolgáltatás** legördülő listájában válassza ki a korábban létrehozott társított szolgáltatást a HDInsight **HDInsightLinkedService**.

    ![HDInsight-fürt adatainak megadása a folyamathoz](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-hive-activity-select-hdinsight-linked-service.png "HDInsight-fürt adatainak megadása a folyamathoz")

1. Válassza a **szkript** fület, és végezze el a következő lépéseket:

    1. A **parancsfájlhoz társított szolgáltatás** esetében válassza a **HDIStorageLinkedService** lehetőséget a legördülő listából. Ez az érték a korábban létrehozott Storage társított szolgáltatás.

    1. A **fájl elérési útja** területen válassza a **Tallózás tároló** lehetőséget, és navigáljon arra a helyre, ahol a minta struktúra-parancsfájl elérhető. Ha korábban futtatta a PowerShell-parancsfájlt, ennek a helynek a következőnek kell lennie: `adfgetstarted/hivescripts/partitionweblogs.hql` .

        ![A folyamathoz tartozó struktúra-parancsfájl részleteinek megadása](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-provide-script-path.png "A folyamathoz tartozó struktúra-parancsfájl részleteinek megadása")

    1. A **speciális**  >  **Paraméterek** területen válassza a elemet **`Auto-fill from script`** . Ez a beállítás minden olyan paramétert megkeres a kaptár-parancsfájlban, amely futásidőben értéket igényel.

    1. Az **érték** szövegmezőben adja hozzá a meglévő mappát a formátum mezőben `wasbs://adfgetstarted@<StorageAccount>.blob.core.windows.net/outputfolder/` . Az elérési út megkülönbözteti a kis- és nagybetűket. Ez az elérési út a parancsfájl kimenetét fogja tárolni. A `wasbs` séma azért szükséges, mert a Storage-fiókok esetében alapértelmezés szerint engedélyezve van a biztonságos átvitel.

        ![Adja meg a kaptár parancsfájl paramétereit](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-provide-script-parameters.png "Adja meg a kaptár parancsfájl paramétereit")

1. A folyamat érvényesítéséhez kattintson az **Érvényesítés** elemre. Az **>>** érvényesítési ablak bezárásához kattintson a (jobbra mutató nyíl) gombra.

    ![A Azure Data Factory folyamat ellenőrzése](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-validate-all.png "A Azure Data Factory folyamat ellenőrzése")

1. Végül válassza az **összes közzététele** lehetőséget az összetevők közzétételéhez Azure Data Factory.

    ![A Azure Data Factory-folyamat közzététele](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-publish-pipeline.png "A Azure Data Factory-folyamat közzététele")

## <a name="trigger-a-pipeline"></a>Folyamat aktiválása

1. A tervező felületének eszköztárán válassza az **aktiválási**  >  **trigger hozzáadása most** lehetőséget.

    ![A Azure Data Factory folyamat elindítása](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-trigger-pipeline.png "A Azure Data Factory folyamat elindítása")

2. Kattintson az **OK gombra** az előugró oldali sávon.

## <a name="monitor-a-pipeline"></a>Folyamat monitorozása

1. Váltson a bal oldali **Monitorozás** lapra. Ekkor a folyamat futása megjelenik a **Pipeline Runs** (Folyamatfuttatások) listában. Figyelje meg a Futtatás állapotát az **állapot** oszlopban.

    ![A Azure Data Factory folyamat figyelése](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-monitor-pipeline.png "A Azure Data Factory folyamat figyelése")

1. Az állapot frissítéséhez kattintson a **Frissítés** gombra.

1. Azt is megteheti, hogy kijelöli a **tevékenység** futtatása ikont, hogy megjelenjen-e a folyamathoz társított tevékenység. Az alábbi képernyőképen csak egy tevékenység fut, mivel a létrehozott folyamatnak csak egy tevékenysége van. Ha vissza szeretne térni az előző nézetre, válassza a **folyamatok** lehetőséget az oldal tetején.

    ![A Azure Data Factoryi folyamat tevékenységének figyelése](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-monitor-pipeline-activity.png "A Azure Data Factoryi folyamat tevékenységének figyelése")

## <a name="verify-the-output"></a>Kimenet ellenőrzése

1. A kimenet ellenőrzéséhez a Azure Portal navigáljon az oktatóanyaghoz használt Storage-fiókhoz. A következő mappákat vagy tárolókat kell látnia:

    * Megjelenik egy **adfgerstarted/OutputFolder** , amely a folyamat részeként futtatott struktúra-parancsfájl kimenetét tartalmazza.

    * Megjelenik egy **adfhdidatafactory- \<linked-service-name> - \<timestamp>** tároló. Ez a tároló a folyamat futtatásának részeként létrehozott HDInsight-fürt alapértelmezett tárolási helye.

    * Megjelenik egy olyan **adfjobs** -tároló, amely tartalmazza a Azure Data Factory feladatok naplóit.  

        ![A Azure Data Factory folyamat kimenetének ellenőrzése](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-verify-output.png "A Azure Data Factory folyamat kimenetének ellenőrzése")

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az igény szerinti HDInsight-fürt létrehozásakor nem szükséges explicit módon törölni a HDInsight-fürtöt. A rendszer a folyamat létrehozásakor megadott konfiguráció alapján törli a fürtöt. Még a fürt törlése után is a fürthöz társított Storage-fiókok továbbra is fennállnak. Ez a viselkedés úgy van kialakítva, hogy ne legyenek érintetlenek az adatai. Ha azonban nem szeretné megőrizni az adatmegőrzést, törölheti a létrehozott Storage-fiókot.

Vagy törölheti az oktatóanyaghoz létrehozott teljes erőforráscsoportot is. Ez a folyamat törli a Storage-fiókot és a létrehozott Azure Data Factory.

### <a name="delete-the-resource-group"></a>Az erőforráscsoport törlése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza az **erőforráscsoportok** lehetőséget a bal oldali ablaktáblán.
1. Válassza ki a PowerShell-parancsfájlban létrehozott erőforráscsoport-nevet. Ha túl sok erőforráscsoport van felsorolva, használja a szűrőt. Ekkor megnyílik az erőforráscsoport.
1. Az **erőforrások** csempén az alapértelmezett Storage-fiókkal és az adatelőállítóval kell rendelkeznie, hacsak nem osztja meg az erőforráscsoportot más projektekkel.
1. Válassza az **Erőforráscsoport törlése** elemet. Ezzel törli a Storage-fiókot és a Storage-fiókban tárolt adatfájlokat.

    !["Azure Portal törlési erőforráscsoport"](./media/hdinsight-hadoop-create-linux-clusters-adf/delete-resource-group.png "Erőforráscsoport törlése")

1. Adja meg az erőforráscsoport nevét a törlés megerősítéséhez, majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan használhatja a Azure Data Factoryt igény szerinti HDInsight-fürt létrehozására, és Apache Hive feladatok futtatására. A következő cikkből megtudhatja, hogyan hozhat létre egyéni konfigurációval rendelkező HDInsight-fürtöket.

> [!div class="nextstepaction"]
> [Azure HDInsight-fürtök létrehozása egyéni konfigurációval](hdinsight-hadoop-provision-linux-clusters.md)