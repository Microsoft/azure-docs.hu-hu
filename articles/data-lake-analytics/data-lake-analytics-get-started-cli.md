---
title: '& lekérdezés létrehozása Azure Data Lake Analytics – Azure CLI'
description: Megtudhatja, hogyan hozhat létre Azure Data Lake Analytics fiókot az Azure parancssori felületén, és hogyan küldhet el egy U-SQL-feladatot.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 06/18/2017
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9123ca6a1bfa90737bb1ce83ee365d1ecf514e1f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92220992"
---
# <a name="get-started-with-azure-data-lake-analytics-using-azure-cli"></a>Az Azure Data Lake Analytics használatának első lépései az Azure parancssori felületével

[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Ez a cikk azt ismerteti, hogyan használható az Azure CLI parancssori felülete Azure Data Lake Analytics-fiókok létrehozására, a USQL feladatok elküldésére és a katalógusokra. A feladat tabulátorral elválasztott értékeket tartalmazó (TSV-) fájlt olvas be, majd konvertálja azt egy vesszővel elválasztott értékeket tartalmazó (CSV-) fájllá.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt nekilátna, a következőkre lesz szüksége:

* **Egy Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* Ehhez a cikkhez az Azure CLI 2,0-es vagy újabb verzióját kell futtatnia. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Bejelentkezés az Azure-előfizetésbe:

```azurecli
az login
```

Fel kell keresnie egy URL-címet, és ott meg kell adnia egy hitelesítő kódot.  Ezután kövesse az útmutatásokat a hitelesítő adatai megadásához.

Miután bejelentkezett, a login paranccsal listázhatja az előfizetéseit.

Egy adott előfizetés használata:

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-data-lake-analytics-account"></a>Data Lake Analytics-fiók létrehozása

A feladatok futtatásához rendelkeznie kell egy Data Lake Analytics-fiókkal. A Data Lake Analytics-fiók létrehozásához az alábbiakat kell megadnia:

* **Azure-erőforráscsoport**. A Data Lake Analytics-fiókot egy Azure-erőforráscsoportban kell létrehoznia. Az [Azure Resource Manager](../azure-resource-manager/management/overview.md) lehetővé teszi, hogy az alkalmazásában lévő erőforrásokat csoportként használja. Az alkalmazás összes erőforrását egyetlen, koordinált műveletben telepítheti, frissítheti vagy törölheti.  

Az előfizetés alá tartozó meglévő erőforráscsoportok megjelenítése:

```azurecli
az group list
```

Új erőforráscsoport létrehozása:

```azurecli
az group create --name "<Resource Group Name>" --location "<Azure Location>"
```

* **A Data Lake Analytics-fiók neve**. Minden Data Lake Analytics-fiók rendelkezik egy névvel.
* **Hely**. Használja az egyik, a Data Lake Analytics szolgáltatást támogató Azure-adatközpontot.
* **Alapértelmezett Data Lake Store fiók**: minden Data Lake Analytics fiókhoz tartozik egy alapértelmezett Data Lake Store fiók.

A meglévő Data Lake Store-fiók megjelenítése:

```azurecli
az dls account list
```

Új Data Lake Store-fiók létrehozása:

```azurecli
az dls account create --account "<Data Lake Store Account Name>" --resource-group "<Resource Group Name>"
```

Egy Data Lake Analytics-fiók létrehozásához használja a következő szintaxist:

```azurecli
az dla account create --account "<Data Lake Analytics Account Name>" --resource-group "<Resource Group Name>" --location "<Azure location>" --default-data-lake-store "<Default Data Lake Store Account Name>"
```

A fiók létrehozása után az alábbi parancsok használatával listázhatja a fiókokat és jelentheti meg azok részleteit:

```azurecli
az dla account list
az dla account show --account "<Data Lake Analytics Account Name>"
```

## <a name="upload-data-to-data-lake-store"></a>Adatok feltöltése a Data Lake Store-ba

Az alábbi oktatóanyagban keresési naplókat fog feldolgozni.  A keresési napló tárolható Data Lake-adattárban vagy Azure Blob Storage-ban.

Az Azure Portal felhasználói felületet biztosít bizonyos mintaadatfájlok alapértelmezett Data Lake Store-fiókba való másolásához. Ilyen adatfájl a keresési napló is. Az adatok alapértelmezett Data Lake Store-fiókba való feltöltéséhez lásd a [Forrásadatok előkészítése](data-lake-analytics-get-started-portal.md) című szakaszt.

Fájlok az Azure CLI használatával való feltöltéséhez használja az alábbi parancsokat:

```azurecli
az dls fs upload --account "<Data Lake Store Account Name>" --source-path "<Source File Path>" --destination-path "<Destination File Path>"
az dls fs list --account "<Data Lake Store Account Name>" --path "<Path>"
```

A Data Lake Analytics az Azure Blob Storage-hoz is rendelkezik hozzáféréssel.  A fájlok az Azure Blob Storage-ba történő feltöltéséhez lásd: [Using the Azure CLI with Azure Storage](../storage/blobs/storage-quickstart-blobs-cli.md) (Az Azure parancssori felület és az Azure Storage használata).

## <a name="submit-data-lake-analytics-jobs"></a>Data Lake Analytics-feladatok küldése

A Data Lake Analytics-feladatok nyelve a U-SQL. További információk a U-SQL-ről: [U-SQL nyelv – első lépések](data-lake-analytics-u-sql-get-started.md) és [U-SQL nyelvi referencia](/u-sql/).

### <a name="to-create-a-data-lake-analytics-job-script"></a>Data Lake Analytics-feladatparancsfájl létrehozása

Hozzon létre egy szövegfájlt az alábbi U-SQL-parancsfájl alapján, és mentse a szövegfájlt a munkaállomáson:

```usql
@a  =
    SELECT * FROM
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
```

Ez a U-SQL-parancsfájl beolvassa a forrásadatfájlt az **Extractors.Tsv()** segítségével, majd létrehoz egy csv-fájlt az **Outputters.Csv()** használatával.

Ne módosítsa az elérési utat, kivéve, ha átmásolja a forrásfájlt egy másik helyre.  A Data Lake Analytics létrehozza a kimeneti mappát, ha az még nem létezik.

Egyszerűbb relatív útvonalakat használni az alapértelmezett Data Lake Store-fiókokban tárolt fájlokhoz. De használhat abszolút elérési utakat is.  Például:

```usql
adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
```

A társított tárfiókokban lévő fájlok eléréséhez abszolút elérési utakat kell használnia.  A társított Azure Storage-fiókban tárolt fájlok szintaxisa:

```usql
wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv
```

> [!NOTE]
> A nyilvános blobokat tartalmazó Azure Blob-tárolók nem támogatottak.
> A nyilvános tárolókat tartalmazó Azure Blob-tárolók nem támogatottak.
>

### <a name="to-submit-jobs"></a>Feladatok elküldése

Feladatok elküldéséhez használja a következő szintaxist.

```azurecli
az dla job submit --account "<Data Lake Analytics Account Name>" --job-name "<Job Name>" --script "<Script Path and Name>"
```

Például:

```azurecli
az dla job submit --account "myadlaaccount" --job-name "myadlajob" --script @"C:\DLA\myscript.txt"
```

### <a name="to-list-jobs-and-show-job-details"></a>Feladatok listázása és részleteik megjelenítése

```azurecli
az dla job list --account "<Data Lake Analytics Account Name>"
az dla job show --account "<Data Lake Analytics Account Name>" --job-identity "<Job Id>"
```

### <a name="to-cancel-jobs"></a>Feladatok megszakítása

```azurecli
az dla job cancel --account "<Data Lake Analytics Account Name>" --job-identity "<Job Id>"
```

## <a name="retrieve-job-results"></a>Feladatok eredményeinek lekérése

A feladat befejezése után az alábbi parancsok segítségével listázhatja ki a kimeneti fájlokat, és töltheti le a fájlokat:

```azurecli
az dls fs list --account "<Data Lake Store Account Name>" --source-path "/Output" --destination-path "<Destination>"
az dls fs preview --account "<Data Lake Store Account Name>" --path "/Output/SearchLog-from-Data-Lake.csv"
az dls fs preview --account "<Data Lake Store Account Name>" --path "/Output/SearchLog-from-Data-Lake.csv" --length 128 --offset 0
az dls fs download --account "<Data Lake Store Account Name>" --source-path "/Output/SearchLog-from-Data-Lake.csv" --destination-path "<Destination Path and File Name>"
```

Például:

```azurecli
az dls fs download --account "myadlsaccount" --source-path "/Output/SearchLog-from-Data-Lake.csv" --destination-path "C:\DLA\myfile.csv"
```

## <a name="next-steps"></a>Következő lépések

* Az Data Lake Analytics Azure CLI-hivatkozási dokumentum megtekintéséhez lásd: [Data Lake Analytics](/cli/azure/dla).
* Az Data Lake Store Azure CLI-hivatkozási dokumentum megtekintéséhez lásd: [Data Lake Store](/cli/azure/dls).
* Egy összetettebb lekérdezés megtekintéséhez lásd: [Webhelynaplók elemzése az Azure Data Lake Analytics használatával](data-lake-analytics-analyze-weblogs.md).