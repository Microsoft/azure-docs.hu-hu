---
title: Azure Data Lake Storage Gen1 fiók kezelése – Azure CLI
description: Az Azure CLI használatával hozzon létre egy Data Lake Storage Gen1 fiókot, és hajtson végre alapszintű műveleteket.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: de98e25cf5703a43282e551a0eda20d7767c6ce8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92103628"
---
# <a name="get-started-with-azure-data-lake-storage-gen1-using-the-azure-cli"></a>A Azure Data Lake Storage Gen1 használatának első lépései az Azure CLI-vel

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

> [!div class="op_single_selector"]
> * [Portál](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Azure CLI](data-lake-store-get-started-cli-2.0.md)
>
> 

Megtudhatja, hogyan hozhat létre Azure Data Lake Storage Gen1 fiókot az Azure CLI használatával, és hogyan végezhet el olyan alapvető műveleteket, mint például a mappák létrehozása, az adatfájlok feltöltése és letöltése, a fiók törlése stb. További információ a Data Lake Storage Gen1ről: [Data Lake Storage Gen1 áttekintése](data-lake-store-overview.md).

Az Azure CLI az Azure parancssori felülete, amely Azure-erőforrások kezelésére szolgál. A szolgáltatás macOS, Linux és Windows rendszereken használható. További információ: [Az Azure CLI áttekintése](/cli/azure). A parancsok és a szintaxis teljes listájának megjelenítéséhez tekintse meg a [Azure Data Lake Storage GEN1 CLI-referenciát](/cli/azure/dls) is.


## <a name="prerequisites"></a>Előfeltételek
A cikk elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Egy Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* **Azure CLI** – lásd: az [Azure CLI telepítése](/cli/azure/install-azure-cli) utasításokhoz.

## <a name="authentication"></a>Hitelesítés

Ez a cikk egy egyszerűbb hitelesítési módszert használ a Data Lake Storage Gen1, ahol végfelhasználói felhasználóként jelentkezik be. Ezt követően a Data Lake Storage Gen1 fiók és fájlrendszer hozzáférési szintjét a bejelentkezett felhasználó hozzáférési szintje szabályozza. Vannak azonban más megközelítések is, amelyek a hitelesítéshez Data Lake Storage Gen1, amelyek **végfelhasználói hitelesítés** vagy **szolgáltatások közötti hitelesítés**. A hitelesítéssel kapcsolatban a [Végfelhasználói hitelesítés](data-lake-store-end-user-authenticate-using-active-directory.md) vagy a [Szolgáltatások közötti hitelesítés](./data-lake-store-service-to-service-authenticate-using-active-directory.md) című témakörben talál útmutatást és további tudnivalókat.


## <a name="log-in-to-your-azure-subscription"></a>Bejelentkezés az Azure-előfizetésbe

1. Jelentkezzen be az Azure-előfizetésébe.

    ```azurecli
    az login
    ```

    Kap egy kódot a következő lépésben való használatra. Egy webböngészőben nyissa meg a https://aka.ms/devicelogin oldalt, és gépelje be a kódot a hitelesítéshez. A rendszer kéri a hitelesítési adatokkal való bejelentkezést.

2. Bejelentkezés után az ablakban megjelenő listában találhatók a fiókhoz társított Azure-előfizetések. Az alábbi paranccsal használhat egy adott előfizetést.
   
    ```azurecli
    az account set --subscription <subscription id> 
    ```

## <a name="create-an-azure-data-lake-storage-gen1-account"></a>Azure Data Lake Storage Gen1 fiók létrehozása

1. Új erőforráscsoport létrehozása. Az alábbi parancsban adja meg a használni kívánt paraméterértékeket. Ha a hely neve tartalmaz szóközöket, használjon idézőjeleket. Például: „USA 2. keleti régiója”. 
   
    ```azurecli
    az group create --location "East US 2" --name myresourcegroup
    ```

2. Hozza létre a Data Lake Storage Gen1 fiókot.
   
    ```azurecli
    az dls account create --account mydatalakestoragegen1 --resource-group myresourcegroup
    ```

## <a name="create-folders-in-a-data-lake-storage-gen1-account"></a>Mappák létrehozása Data Lake Storage Gen1 fiókban

Az adatkezeléshez és az adattároláshoz a Azure Data Lake Storage Gen1 fiók alatt hozhat létre mappákat. A következő parancs használatával hozzon létre egy **mynewfolder** nevű mappát a Data Lake Storage Gen1 fiók gyökerében.

```azurecli
az dls fs create --account mydatalakestoragegen1 --path /mynewfolder --folder
```

> [!NOTE]
> A `--folder` paraméter gondoskodik arról, hogy a parancs egy mappát hozzon létre. Ha ez a paraméter nincs jelen, a parancs egy mynewfolder nevű üres fájlt hoz létre a Data Lake Storage Gen1 fiók gyökerében.
> 
>

## <a name="upload-data-to-a-data-lake-storage-gen1-account"></a>Adatok feltöltése Data Lake Storage Gen1-fiókba

Az adatok feltölthetők közvetlenül a legfelső szintű vagy a fiókon belül létrehozott mappába Data Lake Storage Gen1. Az alábbi kódtöredékek bemutatják, hogyan tölthet fel néhány adatot az előző szakaszban létrehozott mappába (**mynewfolder**).

Ha feltölthető mintaadatokra van szüksége, használhatja az [Azure Data Lake Git-tárában](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData) található **Ambulance Data** mappát. Töltse le a fájlt, és tárolja a számítógépén egy helyi könyvtárban (pl. C:\sampledata).

```azurecli
az dls fs upload --account mydatalakestoragegen1 --source-path "C:\SampleData\AmbulanceData\vehicle1_09142014.csv" --destination-path "/mynewfolder/vehicle1_09142014.csv"
```

> [!NOTE]
> Célként adja meg a teljes elérési utat, beleértve a fájlnevet is.
> 
>


## <a name="list-files-in-a-data-lake-storage-gen1-account"></a>Data Lake Storage Gen1-fiókban található fájlok listázása

A következő parancs használatával listázhatja a Data Lake Storage Gen1 fiókban található fájlokat.

```azurecli
az dls fs list --account mydatalakestoragegen1 --path /mynewfolder
```

A kimenet az alábbihoz hasonló lesz:

```output
[
    {
        "accessTime": 1491323529542,
        "aclBit": false,
        "blockSize": 268435456,
        "group": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
        "length": 1589881,
        "modificationTime": 1491323531638,
        "msExpirationTime": 0,
        "name": "mynewfolder/vehicle1_09142014.csv",
        "owner": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
        "pathSuffix": "vehicle1_09142014.csv",
        "permission": "770",
        "replication": 1,
        "type": "FILE"
    }
]
```

## <a name="rename-download-and-delete-data-from-a-data-lake-storage-gen1-account"></a>Adatok átnevezése, letöltése és törlése Data Lake Storage Gen1 fiókból 

* **Fájlok átnevezéséhez** használja az alábbi parancsot:
  
    ```azurecli
    az dls fs move --account mydatalakestoragegen1 --source-path /mynewfolder/vehicle1_09142014.csv --destination-path /mynewfolder/vehicle1_09142014_copy.csv
    ```

* **Fájlok letöltéséhez** használja az alábbi parancsot: Ügyeljen arra, hogy a megadott cél elérési útja egy létező hely legyen.
  
    ```azurecli     
    az dls fs download --account mydatalakestoragegen1 --source-path /mynewfolder/vehicle1_09142014_copy.csv --destination-path "C:\mysampledata\vehicle1_09142014_copy.csv"
    ```

    > [!NOTE]
    > A parancs létrehozza a célmappát, ha az nem létezik.
    > 
    >

* **Fájlok törléséhez** használja az alábbi parancsot:
  
    ```azurecli
    az dls fs delete --account mydatalakestoragegen1 --path /mynewfolder/vehicle1_09142014_copy.csv
    ```

    Ha egyetlen paranccsal szeretné törölni a **mynewfolder** nevű mappát és a **vehicle1_09142014_copy.csv** nevű fájlt, használja a --recurse paramétert

    ```azurecli
    az dls fs delete --account mydatalakestoragegen1 --path /mynewfolder --recurse
    ```

## <a name="work-with-permissions-and-acls-for-a-data-lake-storage-gen1-account"></a>Data Lake Storage Gen1-fiók engedélyeinek és hozzáférés-vezérlési listáinak használata

Ebből a szakaszból megtudhatja, hogyan kezelheti az ACL-eket és az engedélyeket az Azure CLI használatával. Az ACL-ek Azure Data Lake Storage Gen1-ben való megvalósításának részletes ismertetését lásd: [Azure Data Lake Storage Gen1 hozzáférés-vezérlése](data-lake-store-access-control.md).

* **Egy fájl/mappa tulajdonosának frissítését** az alábbi paranccsal végezheti el:

    ```azurecli
    az dls fs access set-owner --account mydatalakestoragegen1 --path /mynewfolder/vehicle1_09142014.csv --group 80a3ed5f-959e-4696-ba3c-d3c8b2db6766 --owner 6361e05d-c381-4275-a932-5535806bb323
    ```

* **Egy fájl/mappa engedélyeinek frissítését** az alábbi paranccsal végezheti el:

    ```azurecli
    az dls fs access set-permission --account mydatalakestoragegen1 --path /mynewfolder/vehicle1_09142014.csv --permission 777
    ```
    
* **Adott elérési úthoz tartozó hozzáférés-vezérlési listák beolvasását** az alábbi paranccsal végezheti el:

    ```azurecli
    az dls fs access show --account mydatalakestoragegen1 --path /mynewfolder/vehicle1_09142014.csv
    ```

    A kimenet az alábbihoz hasonló lesz:

    ```output
    {
        "entries": [
        "user::rwx",
        "group::rwx",
        "other::---"
        ],
        "group": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
        "owner": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
        "permission": "770",
        "stickyBit": false
    }
    ```

* **Egy hozzáférés-vezérlési listához tartozó bejegyzés beállítását** az alábbi paranccsal végezheti el:

    ```azurecli
    az dls fs access set-entry --account mydatalakestoragegen1 --path /mynewfolder --acl-spec user:6360e05d-c381-4275-a932-5535806bb323:-w-
    ```

* **Egy hozzáférés-vezérlési listához tartozó bejegyzés eltávolítását** az alábbi paranccsal végezheti el:

    ```azurecli
    az dls fs access remove-entry --account mydatalakestoragegen1 --path /mynewfolder --acl-spec user:6360e05d-c381-4275-a932-5535806bb323
    ```

* **Egy alapértelmezett teljes hozzáférés-vezérlési lista eltávolítását** az alábbi paranccsal végezheti el:

    ```azurecli
    az dls fs access remove-all --account mydatalakestoragegen1 --path /mynewfolder --default-acl
    ```

* **Egy nem alapértelmezett teljes hozzáférés-vezérlési lista eltávolítását** az alábbi paranccsal végezheti el:

    ```azurecli
    az dls fs access remove-all --account mydatalakestoragegen1 --path /mynewfolder
    ```
    
## <a name="delete-a-data-lake-storage-gen1-account"></a>Data Lake Storage Gen1 fiók törlése
Data Lake Storage Gen1 fiók törléséhez használja a következő parancsot.

```azurecli
az dls account delete --account mydatalakestoragegen1
```

Ha a rendszer rákérdez, írja be az **Y** karaktert a fiók törléséhez.

## <a name="next-steps"></a>Következő lépések
* [Azure Data Lake Storage Gen1 használata big data követelményekhez](data-lake-store-data-scenarios.md) 
* [Az adatok védelme az 1. generációs Data Lake Storage-ban](data-lake-store-secure-data.md)
* [Azure Data Lake Analytics használata a Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Az Azure HDInsight használata Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)