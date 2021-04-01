---
title: Ismerkedés a Azure Data Lake Storage Gen1-PowerShell használatával | Microsoft Docs
description: A Azure PowerShell használatával hozzon létre egy Azure Data Lake Storage Gen1 fiókot, és hajtson végre alapszintű műveleteket.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 06/27/2018
ms.author: twooley
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 485b14f85d68290c5447c885b9bc4974318f7952
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92103730"
---
# <a name="get-started-with-azure-data-lake-storage-gen1-using-azure-powershell"></a>Ismerkedés a Azure Data Lake Storage Gen1 használatával Azure PowerShell

> [!div class="op_single_selector"]
> * [Portál](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Azure CLI](data-lake-store-get-started-cli-2.0.md)
>
>

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Megtudhatja, hogyan hozhat létre Azure PowerShell egy Azure Data Lake Storage Gen1-fiók létrehozásához, és hogyan végezhet el olyan alapvető műveleteket, mint például a mappák létrehozása, az adatfájlok feltöltése és letöltése, a fiók törlése stb. További információ a Data Lake Storage Gen1ről: [Data Lake Storage Gen1 áttekintése](data-lake-store-overview.md).

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Egy Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* Az **Azure PowerShell 1.0-s vagy újabb verziója**. Lásd: [How to install and configure Azure PowerShell](/powershell/azure/) (Az Azure PowerShell telepítése és konfigurálása).

## <a name="authentication"></a>Hitelesítés

Ez a cikk egy egyszerűbb hitelesítési módszert használ a Data Lake Storage Gen1, ahol a rendszer felszólítja az Azure-fiók hitelesítő adatainak megadására. A fiók és a fájlrendszer Data Lake Storage Gen1 hozzáférési szintjét a bejelentkezett felhasználó hozzáférési szintje szabályozza. Vannak azonban más megközelítések a hitelesítéshez Data Lake Storage Gen1, amelyek végfelhasználói hitelesítés vagy szolgáltatások közötti hitelesítés. A hitelesítéssel kapcsolatban a [Végfelhasználói hitelesítés](data-lake-store-end-user-authenticate-using-active-directory.md) vagy a [Szolgáltatások közötti hitelesítés](./data-lake-store-service-to-service-authenticate-using-active-directory.md) című témakörben talál útmutatást és további tudnivalókat.

## <a name="create-a-data-lake-storage-gen1-account"></a>Data Lake Storage Gen1-fiók létrehozása

1. Nyisson meg egy új Windows PowerShell-ablakot az asztalon. Adja meg a következő kódrészletet az Azure-fiókba való bejelentkezéshez, az előfizetés beállításához és a Data Lake Storage Gen1-szolgáltató regisztrálásához. Amikor a rendszer kéri, hogy jelentkezzen be, győződjön meg arról, hogy az egyik előfizetés-rendszergazda/tulajdonos:

    ```PowerShell
    # Log in to your Azure account
    Connect-AzAccount

    # List all the subscriptions associated to your account
    Get-AzSubscription

    # Select a subscription
    Set-AzContext -SubscriptionId <subscription ID>

    # Register for Azure Data Lake Storage Gen1
    Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"
    ```

1. Egy Data Lake Storage Gen1 fiók egy Azure-erőforráscsoporthoz van társítva. Első lépésként hozzon létre egy erőforráscsoportot.

    ```PowerShell
    $resourceGroupName = "<your new resource group name>"
    New-AzResourceGroup -Name $resourceGroupName -Location "East US 2"
    ```

    ![Azure-erőforráscsoport létrehozása](./media/data-lake-store-get-started-powershell/ADL.PS.CreateResourceGroup.png "Azure-erőforráscsoport létrehozása")

1. Hozzon létre egy Data Lake Storage Gen1 fiókot. A megadott név csak kisbetűket és számokat tartalmazhat.

    ```PowerShell
    $dataLakeStorageGen1Name = "<your new Data Lake Storage Gen1 account name>"
    New-AzDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStorageGen1Name -Location "East US 2"
    ```

    ![Data Lake Storage Gen1-fiók létrehozása](./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "Data Lake Storage Gen1-fiók létrehozása")

1. Ellenőrizze, hogy a fiók létrehozása sikeres volt-e.

    ```PowerShell
    Test-AzDataLakeStoreAccount -Name $dataLakeStorageGen1Name
    ```

    A parancsmag kimeneti értéke **True** (Igaz) kell, hogy legyen.

## <a name="create-directory-structures"></a>Címtár-struktúrák létrehozása

Az adatkezeléshez és az adattároláshoz a Data Lake Storage Gen1 fiókjában hozhat létre címtárakat.

1. Adjon meg egy gyökérkönyvtárat.

    ```PowerShell
    $myrootdir = "/"
    ```

1. Hozzon létre egy új könyvtárat **mynewdirectory** néven a megadott gyökérkönyvtárban.

    ```PowerShell
    New-AzDataLakeStoreItem -Folder -AccountName $dataLakeStorageGen1Name -Path $myrootdir/mynewdirectory
    ```

1. Ellenőrizze, hogy az új könyvtár létrehozása sikeres volt-e.

    ```PowerShell
    Get-AzDataLakeStoreChildItem -AccountName $dataLakeStorageGen1Name -Path $myrootdir
    ```

    A következő képernyőképen láthatóhoz hasonló kimenetnek kell megjelennie:

    ![A könyvtár ellenőrzése](./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "A könyvtár ellenőrzése")

## <a name="upload-data"></a>Adatok feltöltése

Az adatok feltölthetők közvetlenül a legfelső szintű Data Lake Storage Gen1ba, vagy a fiókon belül létrehozott könyvtárba. A jelen szakaszban szereplő kódrészletek bemutatják, hogyan tölthet fel néhány adatot az előző szakaszban létrehozott könyvtárba (**mynewdirectory**).

Ha feltölthető mintaadatokra van szüksége, használhatja az [Azure Data Lake Git-tárában](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData) található **Ambulance Data** mappát. Töltse le a fájlt, és tárolja a számítógépén egy helyi könyvtárban (pl. C:\sampledata).

```PowerShell
Import-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
   -Path "C:\sampledata\vehicle1_09142014.csv" `
   -Destination $myrootdir\mynewdirectory\vehicle1_09142014.csv
```

## <a name="rename-download-and-delete-data"></a>Az adatátnevezés, letöltése és törlése

Fájlok átnevezéséhez használja a következő parancsot:

```PowerShell
Move-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
    -Path $myrootdir\mynewdirectory\vehicle1_09142014.csv `
    -Destination $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv
```

Fájlok letöltéséhez használja a következő parancsot:

```PowerShell
Export-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
    -Path $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv `
    -Destination "C:\sampledata\vehicle1_09142014_Copy.csv"
```

Fájlok törléséhez használja a következő parancsot:

```PowerShell
Remove-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
    -Paths $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv
```

Ha a rendszer rákérdez, írja be az **Y** karaktert az elem törléséhez. Ha több fájlt kíván törölni, megadhatja az összes elérési utat, vesszővel elválasztva.

```PowerShell
Remove-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
    -Paths $myrootdir\mynewdirectory\vehicle1_09142014.csv, $myrootdir\mynewdirectoryvehicle1_09142014_Copy.csv
```

## <a name="delete-your-account"></a>Fiók törlése

A következő parancs használatával törölheti Data Lake Storage Gen1 fiókját.

```PowerShell
Remove-AzDataLakeStoreAccount -Name $dataLakeStorageGen1Name
```

Ha a rendszer rákérdez, írja be az **Y** karaktert a fiók törléséhez.

## <a name="next-steps"></a>Következő lépések

* [Teljesítmény-finomhangolási útmutató a PowerShell és a Azure Data Lake Storage Gen1 használatával](data-lake-store-performance-tuning-powershell.md)
* [Azure Data Lake Storage Gen1 használata big data követelményekhez](data-lake-store-data-scenarios.md)
* [Az adatok védelme az 1. generációs Data Lake Storage-ban](data-lake-store-secure-data.md)
* [Azure Data Lake Analytics használata a Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Az Azure HDInsight használata Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)