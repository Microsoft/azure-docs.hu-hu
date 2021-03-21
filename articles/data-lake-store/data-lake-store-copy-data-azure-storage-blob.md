---
title: Adatok másolása az Azure Storage-blobokból a Data Lake Storage Gen1ba
description: Adatok másolása az Azure Storage-Blobokból a AdlCopy eszközzel Azure Data Lake Storage Gen1
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 84ee65b05af4393f49696875bda41df39e283d5d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "85980089"
---
# <a name="copy-data-from-azure-storage-blobs-to-azure-data-lake-storage-gen1"></a>Adatok másolása az Azure Storage-Blobokból a Azure Data Lake Storage Gen1ba

> [!div class="op_single_selector"]
> * [A DistCp használata](data-lake-store-copy-data-wasb-distcp.md)
> * [Az AdlCopy használata](data-lake-store-copy-data-azure-storage-blob.md)
>
>

A Data Lake Storage Gen1 parancssori eszközt, [AdlCopy](https://www.microsoft.com/download/details.aspx?id=50358), amely az alábbi forrásokból származó adatok másolására szolgál:

* Az Azure Storage-blobokból a Data Lake Storage Gen1ba. Nem használhatja a AdlCopy az adatok Data Lake Storage Gen1ból az Azure Storage-blobba való másolásához.
* Két Data Lake Storage Gen1 fiók között.

Emellett a AdlCopy eszközt két különböző módban is használhatja:

* **Önálló**, ahol az eszköz Data Lake Storage Gen1 erőforrásokat használ a feladat elvégzéséhez.
* **Data Lake Analytics-fiók használatával**, ahol a Data Lake Analytics-fiókhoz rendelt egységek a másolási művelet végrehajtásához használatosak. Érdemes ezt a beállítást használni, ha kiszámítható módon szeretné végrehajtani a másolási feladatokat.

## <a name="prerequisites"></a>Előfeltételek

A cikk elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Egy Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* Az **Azure Storage Blobok** tárolója néhány adattal.
* **Egy Data Lake Storage Gen1-fiók**. A létrehozásával kapcsolatos útmutatásért tekintse meg az Ismerkedés [a Azure Data Lake Storage Gen1rel](data-lake-store-get-started-portal.md) című témakört.
* **Data Lake Analytics fiók (nem kötelező)** – tekintse meg az [Ismerkedés a Azure Data Lake Analyticsvel](../data-lake-analytics/data-lake-analytics-get-started-portal.md) című témakört, amely útmutatást nyújt Data Lake Analytics fiók létrehozásához.
* **AdlCopy eszköz**. Telepítse a [AdlCopy eszközt](https://www.microsoft.com/download/details.aspx?id=50358).

## <a name="syntax-of-the-adlcopy-tool"></a>A AdlCopy eszköz szintaxisa

Használja a következő szintaxist a AdlCopy eszközzel való együttműködéshez

```console
AdlCopy /Source <Blob or Data Lake Storage Gen1 source> /Dest <Data Lake Storage Gen1 destination> /SourceKey <Key for Blob account> /Account <Data Lake Analytics account> /Units <Number of Analytics units> /Pattern
```

A szintaxisban található paramétereket alább találja:

| Beállítás | Leírás |
| --- | --- |
| Forrás |Megadja a forrásadatok helyét az Azure Storage-blobban. A forrás lehet blob tároló, blob vagy más Data Lake Storage Gen1 fiók. |
| Dest |Megadja a másolandó Data Lake Storage Gen1 célhelyet. |
| SourceKey |Megadja az Azure Storage-blob forrásának tárolási hozzáférési kulcsát. Erre csak akkor van szükség, ha a forrás blob-tároló vagy blob. |
| Fiók |Nem **kötelező**. Akkor használja, ha Azure Data Lake Analytics fiókot szeretne használni a másolási feladatok futtatásához. Ha a szintaxisban a/Account kapcsolót használja, de nem ad meg Data Lake Analytics fiókot, a AdlCopy egy alapértelmezett fiókot használ a feladatok futtatásához. Ha ezt a kapcsolót használja, a forrás (Azure Storage Blob) és a cél (Azure Data Lake Storage Gen1) adatforrásként hozzá kell adnia a Data Lake Analytics-fiókhoz. |
| Egység |A másolási feladatokhoz használandó Data Lake Analytics egységek számát adja meg. Ez a beállítás kötelező, ha a **/Account** kapcsolóval megadja a Data Lake Analytics fiókot. |
| Mintázat |Egy olyan regex-mintát ad meg, amely jelzi, hogy mely Blobok vagy fájlok másolhatók. A AdlCopy megkülönbözteti a kis-és nagybetűket. Az alapértelmezett minta, ha nincs megadva mintázat, az összes elem másolása. Több fájl mintázatának meghatározása nem támogatott. |

## <a name="use-adlcopy-as-standalone-to-copy-data-from-an-azure-storage-blob"></a>Adatok másolása Azure Storage-blobból a AdlCopy (önálló) használatával

1. Nyisson meg egy parancssort, és navigáljon ahhoz a könyvtárhoz, ahol a AdlCopy telepítve van, jellemzően `%HOMEPATH%\Documents\adlcopy` .
1. Futtassa a következő parancsot egy adott blobnak a forrás tárolójából egy Data Lake Storage Gen1 mappába való másolásához:

    ```console
    AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>
    ```

    Például:

    ```console
    AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log /dest swebhdfs://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==
    ```

    >[!NOTE]
    >A fenti szintaxis a Data Lake Storage Gen1-fiók egyik mappájába másolandó fájlt adja meg. A AdlCopy eszköz létrehoz egy mappát, ha a megadott mappanév nem létezik.

    A rendszer kérni fogja, hogy adja meg az Azure-előfizetéshez tartozó hitelesítő adatokat, amelyben a Data Lake Storage Gen1 fiókja van. A következőhöz hasonló kimenet jelenik meg:

    ```output
    Initializing Copy.
    Copy Started.
    100% data copied.
    Finishing Copy.
    Copy Completed. 1 file copied.
    ```

1. Az összes blobot átmásolhatja az egyik tárolóból a Data Lake Storage Gen1-fiókba a következő parancs használatával:

    ```console
    AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/ /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>  
    ```      

    Például:

    ```console
    AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest adl://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==
    ```

### <a name="performance-considerations"></a>A teljesítménnyel kapcsolatos megfontolások

Ha Azure Blob Storage-fiókból másol másolást, a blob Storage-oldalon történő másolás során lehet szabályozni. Ez csökkenti a másolási feladatok teljesítményét. Ha többet szeretne megtudni az Azure-Blob Storage korlátairól, tekintse meg az Azure Storage korlátozásait az [Azure-előfizetés és a szolgáltatás korlátainál](../azure-resource-manager/management/azure-subscription-service-limits.md).

## <a name="use-adlcopy-as-standalone-to-copy-data-from-another-data-lake-storage-gen1-account"></a>Adatok másolása egy másik Data Lake Storage Gen1 fiókból a AdlCopy (önálló) használatával

A AdlCopy segítségével két Data Lake Storage Gen1 fiók közötti Adatmásolást is használhat.

1. Nyisson meg egy parancssort, és navigáljon ahhoz a könyvtárhoz, ahol a AdlCopy telepítve van, jellemzően `%HOMEPATH%\Documents\adlcopy` .
1. Futtassa a következő parancsot egy adott fájl egy Data Lake Storage Gen1-fiókból egy másikba való másolásához.

    ```console
    AdlCopy /Source adl://<source_adlsg1_account>.azuredatalakestore.net/<path_to_file> /dest adl://<dest_adlsg1_account>.azuredatalakestore.net/<path>/
    ```

    Például:

    ```console
    AdlCopy /Source adl://mydatastorage.azuredatalakestore.net/mynewfolder/909f2b.log /dest adl://mynewdatalakestorage.azuredatalakestore.net/mynewfolder/
    ```

   > [!NOTE]
   > A fenti szintaxis megadja a másolandó fájlt a cél Data Lake Storage Gen1 fiók egyik mappájába. A AdlCopy eszköz létrehoz egy mappát, ha a megadott mappanév nem létezik.
   >
   >

    A rendszer kérni fogja, hogy adja meg az Azure-előfizetéshez tartozó hitelesítő adatokat, amelyben a Data Lake Storage Gen1 fiókja van. A következőhöz hasonló kimenet jelenik meg:

    ```output
    Initializing Copy.
    Copy Started.|
    100% data copied.
    Finishing Copy.
    Copy Completed. 1 file copied.
    ```
1. A következő parancs a forrás Data Lake Storage Gen1 fiókjában lévő összes fájlt átmásolja a cél Data Lake Storage Gen1 fiók egyik mappájába.

    ```console
    AdlCopy /Source adl://mydatastorage.azuredatalakestore.net/mynewfolder/ /dest adl://mynewdatalakestorage.azuredatalakestore.net/mynewfolder/
    ```

### <a name="performance-considerations"></a>A teljesítménnyel kapcsolatos megfontolások

Ha önálló eszközként használja a AdlCopy-t, a másolás megosztott, Azure által felügyelt erőforrásokon fut. Az ebben a környezetben felhasználható teljesítmény a rendszerterheléstől és a rendelkezésre álló erőforrástól függ. Ez a mód a legjobb megoldás, ha alkalmi alapon kis átvitelre van szükség. A AdlCopy önálló eszközként való használatakor nem kell paramétereket hangolni.

## <a name="use-adlcopy-with-data-lake-analytics-account-to-copy-data"></a>Az AdlCopy használata (Data Lake Analytics fiókkal) az adatmásoláshoz

A Data Lake Analytics-fiók használatával futtathatja a AdlCopy feladatot az adatok Azure Storage-blobokból Data Lake Storage Gen1ba való másolásához is. Ez a beállítás általában akkor használható, ha az áthelyezni kívánt adatok gigabájt és terabájt közötti tartományban vannak, és jobb és kiszámítható teljesítményt kívánnak.

Ha Data Lake Analytics-fiókját szeretné használni a AdlCopy egy Azure Storage Blobból való másoláshoz, a forrást (Azure Storage Blob) hozzá kell adni adatforrásként a Data Lake Analytics-fiókjához. További adatforrások Data Lake Analytics-fiókhoz való hozzáadásával kapcsolatos útmutatásért lásd: [Data Lake Analytics fiók adatforrásainak kezelése](../data-lake-analytics/data-lake-analytics-manage-use-portal.md#manage-data-sources).

> [!NOTE]
> Ha Azure Data Lake Storage Gen1-fiókból másol forrást Data Lake Analytics-fiókkal, akkor nem kell hozzárendelni a Data Lake Storage Gen1 fiókot a Data Lake Analytics fiókhoz. A forrás-és a Data Lake Analytics fiókhoz való hozzárendelésének követelménye csak akkor szükséges, ha a forrás egy Azure Storage-fiók.
>
>

Futtassa az alábbi parancsot egy Azure Storage-blobból egy Data Lake Storage Gen1-fiókba való másoláshoz Data Lake Analytics fiók használatával:

```console
AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Account <data_lake_analytics_account> /Units <number_of_data_lake_analytics_units_to_be_used>
```

Például:

```console
AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest swebhdfs://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Account mydatalakeanalyticaccount /Units 2
```

Hasonlóképpen, futtassa a következő parancsot a forrás Data Lake Storage Gen1 fiókjában lévő összes fájl másolásához a cél Data Lake Storage Gen1 fiókban Data Lake Analytics fiók használatával:

```console
AdlCopy /Source adl://mysourcedatalakestorage.azuredatalakestore.net/mynewfolder/ /dest adl://mydestdatastorage.azuredatalakestore.net/mynewfolder/ /Account mydatalakeanalyticaccount /Units 2
```

### <a name="performance-considerations"></a>A teljesítménnyel kapcsolatos megfontolások

Az adatok terabájtos tartományba másolásakor a AdlCopy használata a saját Azure Data Lake Analytics fiókkal jobb és kiszámítható teljesítményt nyújt. A beállítható paraméter a másolási feladatokhoz használandó Azure Data Lake Analytics egységek száma. Az egységek számának növelésével növelheti a másolási feladatok teljesítményét. Minden másolandó fájl legfeljebb egy egységet használhat. Több egység megadásával a másolt fájlok száma nem növeli a teljesítményt.

## <a name="use-adlcopy-to-copy-data-using-pattern-matching"></a>Az AdlCopy használata az adatmásolási minta egyeztetésével

Ebből a szakaszból megtudhatja, hogyan használhatja a AdlCopy-t a forrásokból származó adatok másolásához (az alábbi példában a Azure Storage Blob) egy célként megadott Data Lake Storage Gen1-fiókra. Az alábbi lépéseket követve például a. csv kiterjesztésű összes fájlt átmásolhatja a forrás blobból a célhelyre.

1. Nyisson meg egy parancssort, és navigáljon ahhoz a könyvtárhoz, ahol a AdlCopy telepítve van, jellemzően `%HOMEPATH%\Documents\adlcopy` .
1. A következő parancs futtatásával másolja át a *. csv kiterjesztésű fájlokat egy adott blobból a forrás tárolójából egy Data Lake Storage Gen1 mappába:

    ```console
    AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Pattern *.csv
    ```

    Például:

    ```console
    AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/FoodInspectionData/ /dest adl://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Pattern *.csv
    ```

## <a name="billing"></a>Számlázás

* Ha a AdlCopy eszközt önállóként használja, akkor az adatáthelyezés költségeiért díjat számítunk fel, ha a forrásként szolgáló Azure Storage-fiók nem ugyanabban a régióban található, mint a Data Lake Storage Gen1 fiók.
* Ha a AdlCopy eszközt használja a Data Lake Analytics-fiókkal, akkor a standard [Data Lake Analytics számlázási díjszabás](https://azure.microsoft.com/pricing/details/data-lake-analytics/) érvényes.

## <a name="considerations-for-using-adlcopy"></a>A AdlCopy használatának szempontjai

* A AdlCopy (1.0.5 verzió) támogatja az adatok másolását olyan forrásokból, amelyek több mint több ezer fájlhoz és mappához tartoznak. Ha azonban a nagyméretű adathalmazok másolásával kapcsolatos problémák merülnek fel, a fájlokat és mappákat különböző almappákba terjesztheti, és az almappák elérési útját használhatja forrásként.

## <a name="performance-considerations-for-using-adlcopy"></a>A AdlCopy használatának teljesítményével kapcsolatos szempontok

A AdlCopy több ezer fájlt és mappát tartalmazó adatok másolását támogatja. Ha azonban a nagyméretű adathalmazok másolásával kapcsolatos problémák merülnek fel, a fájlokat és mappákat kisebb almappákba terjesztheti. A AdlCopy ad hoc példányokhoz készült. Ha ismétlődő alapon kísérli meg az adatok másolását, érdemes megfontolnia [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) használatát, amely teljes körű felügyeletet biztosít a másolási műveletek köré.

## <a name="release-notes"></a>Kibocsátási megjegyzések

* 1.0.13 – Ha ugyanarra a Azure Data Lake Storage Gen1 fiókba másol adatokat több adlcopy-parancs között, akkor nem kell újra megadnia a hitelesítő adatait az egyes futtatásokhoz. A Adlcopy mostantól több futtatás során gyorsítótárazza ezeket az információkat.

## <a name="next-steps"></a>Következő lépések

* [Az adatok védelme az 1. generációs Data Lake Storage-ban](data-lake-store-secure-data.md)
* [Azure Data Lake Analytics használata a Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Az Azure HDInsight használata Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
