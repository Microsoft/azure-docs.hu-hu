---
title: Stream-adatok Stream Analyticsról Data Lake Storage Gen1ra – Azure
description: Megtudhatja, hogyan használhatja a Azure Data Lake Storage Gen1 kimenetként egy Azure Stream Analytics feladatokhoz egy egyszerű forgatókönyvvel, amely egy Azure Storage-blobból olvassa be az adatokat.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/30/2018
ms.author: twooley
ms.openlocfilehash: 4c289ecb1d8471a7b99f1d4c85a0163de4d0c593
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91576217"
---
# <a name="stream-data-from-azure-storage-blob-into-azure-data-lake-storage-gen1-using-azure-stream-analytics"></a>Adatok továbbítása Azure Storage Blobról Azure Data Lake Storage Gen1 a Azure Stream Analytics használatával
Ebből a cikkből megtudhatja, hogyan használhatja a Azure Data Lake Storage Gen1 kimenetként egy Azure Stream Analytics feladatokhoz. Ez a cikk egy egyszerű forgatókönyvet mutat be, amely beolvassa az adatokat egy Azure Storage-blobból (bemenet), és az adatokat a Data Lake Storage Gen1ba (output) írja.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Egy Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* **Azure Storage-fiók**. A fiókból BLOB-tárolót fog használni egy Stream Analytics-feladathoz tartozó adatok beviteléhez. Ebben az oktatóanyagban tegyük fel, hogy rendelkezik egy **storageforasa** nevű Storage-fiókkal és egy **storageforasacontainer** nevű fiókban található tárolóval. Miután létrehozta a tárolót, töltsön fel egy minta adatfájlt. 
  
* **Egy Data Lake Storage Gen1-fiók**. Kövesse a [Azure Data Lake Storage Gen1 használatának első lépései a Azure Portal használatával](data-lake-store-get-started-portal.md)című témakör utasításait. Tegyük fel, hogy van egy **myadlsg1** nevű Data Lake Storage Gen1 fiókja. 

## <a name="create-a-stream-analytics-job"></a>Stream Analytics-feladat létrehozása
Első lépésként hozzon létre egy Stream Analytics feladatot, amely tartalmaz egy bemeneti forrást és egy kimeneti célhelyet. Ebben az oktatóanyagban a forrás egy Azure Blob-tároló, és a cél Data Lake Storage Gen1.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. A bal oldali panelen kattintson a **stream Analytics feladatok** elemre, majd a **Hozzáadás** gombra.

    ![Stream Analytics-feladatok létrehozása](./media/data-lake-store-stream-analytics/create.job.png "Stream Analytics-feladat létrehozása")

    > [!NOTE]
    > Győződjön meg arról, hogy a feladatot ugyanabban a régióban hozza létre, mint a Storage-fiókot, vagy ha további költségekkel jár az adatok régiók közötti áthelyezéséhez.
    >

## <a name="create-a-blob-input-for-the-job"></a>BLOB-bemenet létrehozása a feladathoz

1. Nyissa meg a Stream Analytics-feladathoz tartozó lapot, a bal oldali panelen kattintson a **bemenetek** lapra, majd a **Hozzáadás** gombra.

    ![Képernyőkép a Stream Analytics Job panelről, a bemenetek lehetőséggel és az adatfolyam-bevitel hozzáadása lehetőséggel.](./media/data-lake-store-stream-analytics/create.input.1.png "Adjon hozzá egy bemenetet a feladathoz")

2. Az **új beviteli** panelen adja meg a következő értékeket.

    ![Képernyőfelvétel a blob Storage-ról – új bemeneti panel.](./media/data-lake-store-stream-analytics/create.input.2.png "Adjon hozzá egy bemenetet a feladathoz")

   * A **bemeneti alias** mezőben adjon meg egy egyedi nevet a bevitt adathoz.
   * A **forrás típusa** beállításnál válassza az **adatfolyam** lehetőséget.
   * A **forrás** mezőben válassza a **blob Storage** lehetőséget.
   * Az **előfizetés** mezőben válassza a **blob Storage használata a jelenlegi előfizetésből** lehetőséget.
   * A **Storage-fiók** területen válassza ki az előfeltételek részeként létrehozott Storage-fiókot. 
   * A **tároló** mezőben válassza ki azt a tárolót, amelyet a kiválasztott Storage-fiókban hozott létre.
   * Az **események szerializálási formátuma** beállításnál válassza a **CSV** lehetőséget.
   * A **határolójelek** beállításnál válassza a **Tab** lehetőséget.
   * A **kódoláshoz** válassza az **UTF-8** elemet.

     Kattintson a **Létrehozás** lehetőségre. A portál most hozzáadja a bemenetet, és teszteli a kapcsolódást.


## <a name="create-a-data-lake-storage-gen1-output-for-the-job"></a>Data Lake Storage Gen1 kimenet létrehozása a feladatokhoz

1. Nyissa meg az Stream Analytics feladatokhoz tartozó lapot, kattintson a **kimenetek** lapra, kattintson a **Hozzáadás** gombra, és válassza a **Data Lake Storage Gen1** lehetőséget.

    ![Képernyőkép a Stream Analytics feladatok panelről, a kimenetek lehetőséggel, a Hozzáadás lehetőséggel és az 1. generációs Data Lake Storage.](./media/data-lake-store-stream-analytics/create.output.1.png "Kimenet hozzáadása a feladatokhoz")

2. Az **új kimenet** panelen adja meg a következő értékeket.

    ![Képernyőkép az 1. generációs Data Lake Storage – új kimenet panel, az Engedélyezés lehetőséggel.](./media/data-lake-store-stream-analytics/create.output.2.png "Kimenet hozzáadása a feladatokhoz")

    * A **kimeneti alias** mezőben adja meg a feladatok kimenetének egyedi nevét. Ez az a lekérdezésekben használt felhasználóbarát név, amely a lekérdezés kimenetét erre a Data Lake Storage Gen1 fiókra irányítja.
    * A rendszer kérni fogja, hogy engedélyezze a hozzáférést a Data Lake Storage Gen1 fiókhoz. Kattintson az **Engedélyezés** gombra.

3. Az **új kimenet** panelen folytassa a következő értékek megadásával.

    ![Képernyőkép a Data Lake Storage Gen 1 – New output panelről.](./media/data-lake-store-stream-analytics/create.output.3.png "Kimenet hozzáadása a feladatokhoz")

   * A **fióknév** mezőben válassza ki azt a Data Lake Storage Gen1 fiókot, amelyet már létrehozott, ahol a feladatok kimenetét el szeretné juttatni.
   * Az **elérésiút-előtag mintája** mezőben adja meg a fájloknak a megadott Data Lake Storage Gen1 fiókban való írásához használt elérési utat.
   * Ha a Date (dátum) **formátumot** használta az előtag elérési útján, akkor kiválaszthatja azt a dátumformátumot, amelyben a fájlok vannak rendszerezve.
   * Ha az előtag elérési útján időtokent **használt, akkor** adja meg a fájlok rendszerezésének időformátumát.
   * Az **események szerializálási formátuma** beállításnál válassza a **CSV** lehetőséget.
   * A **határolójelek** beállításnál válassza a **Tab** lehetőséget.
   * A **kódoláshoz** válassza az **UTF-8** elemet.
    
     Kattintson a **Létrehozás** lehetőségre. A portál most hozzáadja a kimenetet, és teszteli a kapcsolódást.
    
## <a name="run-the-stream-analytics-job"></a>Stream Analytics-feladat futtatása

1. Stream Analytics feladatok futtatásához a **lekérdezés** lapon le kell futtatnia egy lekérdezést. Ebben az oktatóanyagban futtathatja a minta lekérdezést úgy, hogy lecseréli a helyőrzőket a feladathoz tartozó bemeneti és kimeneti Aliasokra, ahogy az alábbi képernyőfelvételen látható.

    ![Lekérdezés futtatása](./media/data-lake-store-stream-analytics/run.query.png "A lekérdezés futtatása")

2. Kattintson a képernyő felső részén található **Mentés** elemre, majd az **Áttekintés** lapon kattintson a **Start** gombra. A párbeszédpanelen válassza az **Egyéni idő** lehetőséget, majd állítsa be az aktuális dátumot és időpontot.

    ![Feladatütemezés beállítása](./media/data-lake-store-stream-analytics/run.query.2.png "Feladatütemezés beállítása")

    A feladatok elindításához kattintson a **Start** gombra. A feladatok elindításához akár pár percet is igénybe vehet.

3. Ha a feladatot úgy szeretné elindítani, hogy a blobból kiválassza az adatforrást, másolja a minta adatfájlt a blob-tárolóba. A [Azure Data Lake git-tárházból](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt)lekérhet egy minta adatfájlt. Ebben az oktatóanyagban másolja a **vehicle1_09142014.csv** fájlt. Az adatok blob-tárolóba való feltöltéséhez különböző ügyfeleket, például [Azure Storage Explorereket](https://storageexplorer.com/)használhat.

4. Az **Áttekintés** lap **figyelés** területén tekintse meg az adatok feldolgozásának módját.

    ![Feladat monitorozása](./media/data-lake-store-stream-analytics/run.query.3.png "Feladat monitorozása")

5. Végezetül Ellenőrizheti, hogy a feladatok kimeneti adatai elérhetők-e a Data Lake Storage Gen1 fiókban. 

    ![Kimenet ellenőrzése](./media/data-lake-store-stream-analytics/run.query.4.png "Kimenet ellenőrzése")

    A Adatkezelő ablaktáblán figyelje meg, hogy a kimenet a Data Lake Storage Gen1 output Settings () paraméterben megadott elérési út mappájába íródik `streamanalytics/job/output/{date}/{time}` .  

## <a name="see-also"></a>Lásd még
* [Data Lake Storage Gen1 használatára szolgáló HDInsight-fürt létrehozása](data-lake-store-hdinsight-hadoop-use-portal.md)
