---
title: 'Data Factory oktatóanyag: az első adatfolyamat '
description: Ez a Azure Data Factory oktatóanyag azt mutatja be, hogyan hozhat létre és ütemezhet olyan adatelőállítót, amely egy Hadoop-fürtön található kaptár-parancsfájl használatával dolgozza fel az adatfeldolgozást.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2018
ms.openlocfilehash: 7f1de53e20614ca66c91735ce462da5a194d1836
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100377227"
---
# <a name="tutorial-build-your-first-pipeline-to-transform-data-using-hadoop-cluster"></a>Oktatóanyag: az első folyamat létrehozása az Hadoop-fürtön keresztüli adatátalakításhoz
> [!div class="op_single_selector"]
> * [Áttekintés és előfeltételek](data-factory-build-your-first-pipeline.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Resource Manager-sablon](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)


> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse meg [az adat-előállító Azure Data Factoryvel való létrehozását ismertető rövid útmutatót](../quickstart-create-data-factory-dot-net.md).

Ebben az oktatóanyagban létrehozhatja az első Azure-beli adatfeldolgozót egy adatfolyamattal. A folyamat egy Azure HDInsight (Hadoop) fürtön futó struktúra-parancsfájl futtatásával átalakítja a bemeneti adatokat a kimeneti adatok előállításához.  

Ez a cikk az oktatóanyag áttekintését és előfeltételeit ismerteti. Az előfeltételek elvégzése után az oktatóanyag az alábbi eszközök/SDK-k egyikével végezhető el: Visual Studio, PowerShell, Resource Manager-sablon, REST API. A cikk végén található legördülő listában válassza ki az egyik lehetőséget, hogy elvégezze az oktatóanyagot az alábbi lehetőségek egyikének használatával.    

## <a name="tutorial-overview"></a>Az oktatóanyag áttekintése
Az oktatóanyagban az alábbi lépéseket fogja végrehajtani:

1. Hozzon létre egy **adatelőállítót**. Az adatfeldolgozók egy vagy több adatfolyamatot is tartalmazhatnak, amelyek áthelyezik és átalakítják az adatátvitelt.

    Ebben az oktatóanyagban egy folyamatot hoz létre az adatelőállítóban.
2. **Folyamat** létrehozása. Egy folyamat egy vagy több tevékenységgel rendelkezhet (például: másolási tevékenység, HDInsight struktúra tevékenység). Ez a példa a HDInsight-struktúra tevékenységét használja, amely egy HDInsight Hadoop-fürtön futtatja a kaptár-parancsfájlt. A szkript először létrehoz egy táblázatot, amely az Azure Blob Storage-ban tárolt nyers webnapló-adatokra hivatkozik, majd a nyers adatok év és hónap szerint particionálva.

    Ebben az oktatóanyagban a folyamat a kaptár tevékenységgel alakítja át az adatátalakítást egy Azure HDInsight Hadoop fürtön futó kaptár-lekérdezés futtatásával.
3. **Társított szolgáltatások** létrehozása. Egy társított szolgáltatás létrehozásával összekapcsolhatja az adattárakat vagy a számítási szolgáltatásokat az adatok gyárával. Az adattárak, például az Azure Storage a folyamatban lévő tevékenységek bemeneti/kimeneti adatait tárolják. Olyan számítási szolgáltatás, mint például a HDInsight Hadoop, az adatok átalakítását végzi.

    Ebben az oktatóanyagban két társított szolgáltatást hoz létre: az **Azure Storage** -t és az **Azure HDInsight**-t. Az Azure Storage társított szolgáltatás egy Azure Storage-fiókot társít, amely a bemeneti/kimeneti adatokat tárolja az adat-előállítóban. Az Azure HDInsight társított szolgáltatás egy Azure HDInsight-fürtöt társít, amely az adatátalakításra szolgál az adatgyárban.
3. Bemeneti és kimeneti **adatkészletek** létrehozása. A bemeneti adatkészletek a folyamattevékenységek bemenetét jelölik, a kimeneti adatkészletek pedig a tevékenységek kimeneteit.

    Ebben az oktatóanyagban a bemeneti és kimeneti adatkészletek a bemeneti és kimeneti adatok helyét adják meg az Azure Blob Storageban. Az Azure Storage társított szolgáltatás határozza meg, hogy melyik Azure Storage-fiókot használja a rendszer. A bemeneti adatkészlet meghatározza a bemeneti fájlok helyét, és a kimeneti adatkészlet meghatározza, hogy a kimeneti fájlok hol vannak elhelyezve.


A Azure Data Factory részletes áttekintéséhez tekintse [meg a Azure Data Factory bevezetését](data-factory-introduction.md) ismertető cikket.

Itt látható az oktatóanyagban felépített mintaadatok-előállító **diagram nézete** . A **MyFirstPipeline** egy kaptár típusú tevékenységgel rendelkezik, amely a **AzureBlobInput** adatkészletet bemenetként használja, és **AzureBlobOutput** -adatkészletet állít elő kimenetként.

![Diagram nézet Data Factory oktatóanyagban](media/data-factory-build-your-first-pipeline/data-factory-tutorial-diagram-view.png)


Ebben az oktatóanyagban a **adfgetstarted** Azure Blob-tároló **inputdata** mappájában egy input. log nevű fájl található. A naplófájl három hónapból álló bejegyzéseket tartalmaz: január, February és március 2016. Itt láthatók a bemeneti fájlban lévő minden hónaphoz tartozó mintavételi sorok.

```
2016-01-01,02:01:09,SAMPLEWEBSITE,GET,/blogposts/mvc4/step2.png,X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,53175,871
2016-02-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
2016-03-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
```

Ha a fájlt a folyamat HDInsight-struktúra tevékenységgel dolgozza fel, a tevékenység a HDInsight-fürtön futtat egy kaptár-parancsfájlt, amely a bemeneti adatokat évente és havonta particionálja. A szkript három kimeneti mappát hoz létre, amelyek minden hónapból származó bejegyzéseket tartalmaznak.  

```
adfgetstarted/partitioneddata/year=2016/month=1/000000_0
adfgetstarted/partitioneddata/year=2016/month=2/000000_0
adfgetstarted/partitioneddata/year=2016/month=3/000000_0
```

A fent látható minta sorokból az első (2016-01-01) a hónap = 1 mappában található 000000_0-fájlba íródik. Hasonlóképpen, a másodikat a hónap = 2 mappában található fájlba írja a rendszer, a harmadik pedig a hónap = 3 mappában található fájlba.  

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag megkezdése előtt a következő előfeltételeket kell megadnia:

1. **Azure-előfizetés** – ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy ingyenes próbaverziós fiókot. Az ingyenes [próbaverziós](https://azure.microsoft.com/pricing/free-trial/) fiók beszerzésével kapcsolatos további információkért tekintse meg az ingyenes próbaverziót ismertető cikket.
2. **Azure Storage** – Azure Storage-fiókot használ az ebben az oktatóanyagban tárolt adattároláshoz. Ha nem rendelkezik Azure Storage-fiókkal, tekintse meg a [Storage-fiók létrehozása](../../storage/common/storage-account-create.md) című cikket. A Storage-fiók létrehozása után jegyezze fel a **fiók nevét** és a **hozzáférési kulcsot**. További információ a Storage-fiók hozzáférési kulcsainak beolvasásáról: a [Storage-fiók hozzáférési kulcsainak kezelése](../../storage/common/storage-account-keys-manage.md).
3. Töltse le és tekintse át a következő helyen található kaptár-lekérdezési fájlt (**HQL**): [https://adftutorialfiles.blob.core.windows.net/hivetutorial/partitionweblogs.hql](https://adftutorialfiles.blob.core.windows.net/hivetutorial/partitionweblogs.hql) . Ez a lekérdezés átalakítja a bemeneti adatokat a kimeneti adatok előállításához.
4. Töltse le és tekintse át a következő helyen található minta bemeneti fájlt (**input. log**): [https://adftutorialfiles.blob.core.windows.net/hivetutorial/input.log](https://adftutorialfiles.blob.core.windows.net/hivetutorial/input.log)
5. Hozzon létre egy **adfgetstarted** nevű BLOB-tárolót az Azure-Blob Storage.
6. Töltse fel a **partitionweblogs. HQL** fájlt a **adfgetstarted** tároló **parancsfájl** mappájába. Használjon olyan eszközöket, mint például a [Microsoft Azure Storage Explorer](https://storageexplorer.com/).
7. Töltse fel a **bemeneti. log** fájlt a **adfgetstarted** tároló **inputdata** mappájába.

Az előfeltételek elvégzése után válassza az alábbi eszközök/SDK-k egyikét az oktatóanyag elvégzéséhez:

- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Resource Manager-sablon](data-factory-build-your-first-pipeline-using-arm.md)
- [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)

A Visual Studio grafikus felhasználói felületet biztosít az adatüzemek létrehozásához. Míg a PowerShell, a Resource Manager-sablon és a REST API lehetőségek lehetővé teszik az adatüzemek létrehozásához a parancsfájlkezelést és a programozási módszert.

> [!NOTE]
> Az oktatóanyagban található adatfolyamat átalakítja a bemeneti adatokat, hogy ezzel kimeneti adatokat hozzon létre. A forrásadattár adatait nem másolja egy céladattárba. Az adatok Azure Data Factory használatával történő másolásának útmutatásáért olvassa el [az adatok Blob Storage-ból SQL Database-be történő másolását ismertető oktatóanyagot](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
>
> Összefűzhet két tevékenységet (vagyis egymás után futtathatja őket), ha az egyik tevékenység kimeneti adatkészletét a másik tevékenység bemeneti adatkészleteként állítja be. Lásd [a Data Factorybeli ütemezést és végrehajtást](data-factory-scheduling-and-execution.md) ismertető cikket.
