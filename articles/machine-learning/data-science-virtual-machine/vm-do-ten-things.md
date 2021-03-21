---
title: Az adatelemzés és a modell megismerése Windows rendszeren
titleSuffix: Azure Data Science Virtual Machine
description: Adatfelderítési és-modellezési feladatok végrehajtása a Windows Data Science Virtual Machineon.
services: machine-learning
ms.service: data-science-vm
ms.custom: devx-track-python
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: c44c40f2ddab53cf0beb5ecd48127d126fdb4333
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101657400"
---
# <a name="data-science-with-a-windows-data-science-virtual-machine"></a>Adatelemzés Windows Data Science Virtual Machine

A Windows Data Science Virtual Machine (DSVM) az adatelemzési és-modellezési feladatok elvégzésére szolgáló hatékony adattudományi fejlesztési környezet. A környezet már több népszerű adatelemző eszközzel is rendelkezik, amelyek megkönnyítik a helyszíni, Felhőbeli vagy hibrid üzembe helyezések elemzését. 

A DSVM szorosan együttműködik az Azure-szolgáltatásokkal. Képes olvasni és feldolgozni az Azure-ban már tárolt, az Azure Szinapszisban (korábban SQL DW), Azure Data Lake, Azure Storage vagy Azure Cosmos DB. Más elemzési eszközöket is használhat, például Azure Machine Learning.

Ebből a cikkből megtudhatja, hogyan használhatja a DSVM az adatelemzési feladatok elvégzésére és más Azure-szolgáltatásokkal való interakcióra. Íme néhány dolog, amit elvégezhet a DSVM:

- A Python 2, Jupyter Notebook a Python 3 és a Microsoft R használatával kísérletezhet a böngészőben tárolt adataival. (a Microsoft R az R nagyvállalati használatra kész, teljesítményre tervezett verziója.)
- Microsoft Machine Learning Server és Python használatával megismerheti az adatelemzést és a modellek helyi fejlesztését a DSVM.
- Az Azure-erőforrások felügyelete a Azure Portal vagy a PowerShell használatával.
- Kiterjesztheti tárhelyét, és megoszthatja a nagyméretű adatkészleteket/kódokat a teljes csapaton belül, ha létrehoz egy Azure Files-megosztást csatlakoztatott meghajtóként a DSVM.
- Ossza meg a kódot a csapatával a GitHub használatával. Az adattárhoz az előre telepített git-ügyfelek használatával férhet hozzá: git bash és git GUI.
- Az Azure-beli és az elemzési szolgáltatások, például az Azure Blob Storage, az Azure Cosmos DB, az Azure szinapszis (korábban SQL DW) és a Azure SQL Database elérése.
- Készítsen jelentéseket és irányítópultokat a DSVM előre telepített Power BI Desktop példányának használatával, és telepítse őket a felhőben.

- Telepítsen további eszközöket a virtuális gépre.   

> [!NOTE]
> A további használati díjak a jelen cikkben felsorolt adattárolási és elemzési szolgáltatások esetében érvényesek. Részletekért tekintse meg az [Azure díjszabását](https://azure.microsoft.com/pricing/) ismertető oldalt.
> 
> 

## <a name="prerequisites"></a>Előfeltételek

* Szüksége van egy Azure-előfizetésre. Regisztrálhat [az ingyenes próbaverzióra](https://azure.microsoft.com/free/).
* A Azure Portal Data Science Virtual Machine üzembe helyezésére vonatkozó utasítások a [virtuális gépek létrehozásakor](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016)érhetők el.


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]


## <a name="use-jupyter-notebooks"></a>A Jupyter-notebookok használata
A Jupyter Notebook egy böngészőalapú IDE-t biztosít az adatelemzéshez és a modellezéshez. Egy Jupyter Notebook a Python 2, a Python 3 vagy az R (nyílt forráskódú és Microsoft R Server) is használható.

A Jupyter Notebook elindításához kattintson a **Start** menüben vagy az asztalon található **Jupyter notebook** ikonra. A DSVM-parancssorban futtathatja azt a könyvtárat is, ```jupyter notebook``` ahol meglévő jegyzetfüzetekkel rendelkezik, vagy új jegyzetfüzeteket kíván létrehozni.  

A Jupyter megkezdése után navigáljon a `/notebooks` könyvtárba, például a DSVM előre csomagolt jegyzetfüzeteket. Ezután:

* Válassza ki a jegyzetfüzetet a kód megtekintéséhez.
* Futtassa az egyes cellákat a SHIFT + ENTER billentyűkombináció kiválasztásával.
* Futtassa a teljes jegyzetfüzetet a **cella** futtatása lehetőség kiválasztásával  >  .
* Hozzon létre egy új jegyzetfüzetet úgy, hogy kiválasztja a Jupyter ikont (a bal felső sarokban), majd kiválasztja az **új** gombot a jobb oldalon, majd kiválasztja a jegyzetfüzet nyelvét (más néven kerneleket).   

> [!NOTE]
> A Jupyter-ben jelenleg a Python 2,7, a Python 3,6, az R, a Julia és a PySpark kernelek támogatottak. Az R kernel a nyílt forráskódú R és a Microsoft R platformon egyaránt támogatja a programozást.   
> 
> 

A jegyzetfüzetben megtekintheti az adatait, felépítheti a modellt, és tesztelheti a modellt a választott könyvtárak használatával.

## <a name="explore-data-and-develop-models-with-microsoft-machine-learning-server"></a>Ismerje meg az adatgyűjtést és a modellek fejlesztését Microsoft Machine Learning Server
Használhatja az R és a Python nyelveket is, hogy az adatelemzést közvetlenül a DSVM végezze el.

Az R esetében használhat olyan IDE-t, mint a RStudio, amely a Start menüben vagy az asztalon található. Vagy használhatja az R Tools for Visual Studio alkalmazást. A Microsoft további könyvtárakat biztosított a nyílt forráskódú CRAN R-re vonatkozóan, hogy lehetővé tegye a skálázható elemzést, és hogy képes legyen elemezni a párhuzamosan tagolt elemzésekben engedélyezett memória méretét. 

A Python esetében használhat olyan IDE-t, mint a Visual Studio Community Edition, amelyen előre telepítve van a Python Tools for Visual Studio (PTVS) bővítmény. Alapértelmezés szerint csak a Python 3,6, a root Conda-környezet van konfigurálva a PTVS. A következő lépések elvégzésével engedélyezheti az anaconda Python 2,7-et:

1. Hozzon létre egyéni környezeteket az egyes verziókhoz az **eszközök**  >  **Python-eszközök**  >  **Python-környezetek** területen, majd válassza a **+ Custom** elemet a Visual Studio Community Edition kiadásban.
1. Adjon meg egy leírást, és állítsa be a környezeti előtag elérési útját **c:\anaconda\envs\python2** for anaconda Python 2,7.
1. A környezet mentéséhez válassza az **automatikus észlelés**  >  **alkalmazása** elemet.

A Python-környezetek létrehozásával kapcsolatos további információkért tekintse meg a [PTVS dokumentációját](/visualstudio/python/) .

Most, hogy létrehoz egy új Python-projektet. Nyissa meg a **fájl**  >  **új**  >  **projekt**  >  **Python** elemet, és válassza ki az Ön által felépített Python-alkalmazás típusát. Az aktuális projekt Python-környezetét a kívánt verzióra (Python 2,7 vagy 3,6) állíthatja be úgy, hogy a jobb gombbal a **Python-környezetek** lehetőségre kattint, majd kiválasztja a **Python-környezetek hozzáadása/eltávolítása** lehetőséget. A PTVS használatáról a [termékdokumentációban](/visualstudio/python/)talál további információt.



## <a name="manage-azure-resources"></a>Azure-erőforrások kezelése
A DSVM nem csupán azt teszi lehetővé, hogy az elemzési megoldást helyileg hozza létre a virtuális gépen. Emellett lehetővé teszi a szolgáltatások elérését az Azure Cloud platformon. Az Azure számos számítási, tárolási, adatelemzési és egyéb szolgáltatást biztosít, amelyeket felügyelhet és elérhet a DSVM.

Az Azure-előfizetések és a felhőalapú erőforrások felügyeletéhez két lehetőség közül választhat:
+ Használja a böngészőt, és lépjen a [Azure Portal](https://portal.azure.com).

+ PowerShell-parancsfájlok használata. Azure PowerShell futtatása az asztal egyik parancsikonjára vagy a **Start** menüből. A részletekért tekintse meg a [Microsoft Azure PowerShell dokumentációját](../../azure-resource-manager/management/manage-resources-powershell.md) . 

## <a name="extend-storage-by-using-shared-file-systems"></a>Tárterület kiterjesztése megosztott fájlrendszerek használatával
Az adatszakértők megoszthatnak nagy adatkészleteket, kódokat vagy más erőforrásokat a csapaton belül. A DSVM körülbelül 45 GB szabad területtel rendelkezik. A tárterület bővítéséhez Azure Files használhat, és csatlakoztathatja egy vagy több DSVM-példányhoz, vagy elérheti azt egy REST API keresztül. A [Azure Portal](../../virtual-machines/windows/attach-managed-disk-portal.md) is használhatja, vagy a [Azure PowerShell](../../virtual-machines/windows/attach-disk-ps.md) használatával további dedikált adatlemezeket adhat hozzá. 

> [!NOTE]
> A Azure Files-megosztás maximális mérete 5 TB. Az egyes fájlok maximális mérete 1 TB. 

Azure Files-megosztás létrehozásához használja a Azure PowerShell parancsfájlt:

```powershell
# Authenticate to Azure.
Connect-AzAccount
# Select your subscription
Get-AzSubscription –SubscriptionName "<your subscription name>" | Select-AzSubscription
# Create a new resource group.
New-AzResourceGroup -Name <dsvmdatarg>
# Create a new storage account. You can reuse existing storage account if you want.
New-AzStorageAccount -Name <mydatadisk> -ResourceGroupName <dsvmdatarg> -Location "<Azure Data Center Name For eg. South Central US>" -Type "Standard_LRS"
# Set your current working storage account
Set-AzCurrentStorageAccount –ResourceGroupName "<dsvmdatarg>" –StorageAccountName <mydatadisk>

# Create an Azure Files share
$s = New-AzStorageShare <<teamsharename>>
# Create a directory under the file share. You can give it any name
New-AzStorageDirectory -Share $s -Path <directory name>
# List the share to confirm that everything worked
Get-AzStorageFile -Share $s
```

Most, hogy létrehozott egy Azure Files-megosztást, csatlakoztathatja az Azure-ban található bármely virtuális géphez. Javasoljuk, hogy a virtuális gépet a Storage-fiókkal megegyező Azure-adatközpontba helyezze, hogy elkerülje a késést és az adatátviteli díjakat. Az Azure PowerShell alábbi parancsokkal csatlakoztathatja a meghajtót a DSVM:

```powershell
# Get the storage key of the storage account that has the Azure Files share from the Azure portal. Store it securely on the VM to avoid being prompted in the next command.
cmdkey /add:<<mydatadisk>>.file.core.windows.net /user:<<mydatadisk>> /pass:<storage key>

# Mount the Azure Files share as drive Z on the VM. You can choose another drive letter if you want.
net use z:  \\<mydatadisk>.file.core.windows.net\<<teamsharename>>
```

Most már elérheti a meghajtót, ahogy a virtuális gép bármely normál meghajtóján lenne.

## <a name="share-code-in-github"></a>Kód megosztása a GitHubban
A GitHub egy olyan kódrészlet, amelyben a fejlesztői közösség által megosztott technológiák használatával különböző eszközökhöz használhat programkódokat és forrásokat. A git technológiát használja a programkódok verzióinak nyomon követésére és tárolására. A GitHub egy olyan platform is, ahol saját tárházat hozhat létre a csapat megosztott kódjának és dokumentációjának tárolásához, a verziókövetés megvalósításához, valamint azt, hogy ki férhet hozzá a kód megtekintéséhez és használatához. 

A git használatával kapcsolatos további információkért látogasson el a [GitHub Súgó oldalaira](https://help.github.com/) . Használhatja a GitHubot a csapatával való együttműködésre, a Közösség által fejlesztett kód használatára, valamint a kód visszavonására a Közösséghez.

A DSVM a parancssorban és a grafikus felhasználói felületen is betöltődik a GitHub-tárház eléréséhez. A git és a GitHub működéséhez használt parancssori eszköz git bash néven is ismert. A Visual Studio telepítve van a DSVM, és a git-bővítmények. Ezen eszközök ikonjai a **Start** menüben és az asztalon találhatók.

Ha egy GitHub-tárházból szeretne kódot letölteni, használja a ```git clone``` parancsot. Például a Microsoft által az aktuális könyvtárba közzétett adatelemzési tárház letöltéséhez futtassa a következő parancsot a git Bashben:

```bash
git clone https://github.com/Azure/DataScienceVM.git
```

A Visual Studióban ugyanezt a klónozási műveletet végezheti el. Az alábbi képernyőfelvételen a git-és GitHub-eszközök a Visual Studióban való elérését mutatja be:

![A Visual Studio képernyőképe a megjelenő GitHub-kapcsolatban](./media/vm-do-ten-things/VSGit.png)

A git használatával kapcsolatban további információkat talál a GitHub-tárház használatáról a github.com elérhető erőforrásokból. A [Cheat Sheet](https://services.github.com/on-demand/downloads/github-git-cheat-sheet.pdf) hasznos hivatkozás.

## <a name="access-azure-data-and-analytics-services"></a>Hozzáférés az Azure-beli és az elemzési szolgáltatásokhoz
### <a name="azure-blob-storage"></a>Azure Blob Storage
Az Azure Blob Storage egy megbízható, gazdaságos felhőalapú tárolási szolgáltatás, amely nagy és kicsi adatmennyiséget biztosít. Ez a szakasz azt ismerteti, hogyan helyezhetők át az adattárolók a blob Storage-ba, és hogyan érhetik el az Azure-blobokban tárolt

#### <a name="prerequisites"></a>Előfeltételek

* Hozza létre az Azure Blob Storage-fiókot a [Azure Portal](https://portal.azure.com).

   ![Képernyőfelvétel a Storage-fiók létrehozási folyamatáról a Azure Portal](./media/vm-do-ten-things/create-azure-blob.png)

* Ellenőrizze, hogy a parancssori AzCopy eszköz előre van-e telepítve: ```C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy.exe``` . A azcopy.exet tartalmazó könyvtár már a PATH környezeti változóban van, így az eszköz futtatásakor nem lehet beírni a teljes parancs elérési útját. A AzCopy eszközzel kapcsolatos további információkért tekintse meg a [AzCopy dokumentációját](../../storage/common/storage-use-azcopy-v10.md).
* Indítsa el a Azure Storage Explorer eszközt. A  [Storage Explorer weboldaláról](https://storageexplorer.com/)tölthető le. 

   ![A Storage-fiók elérésének Azure Storage Explorer képernyőképe](./media/vm-do-ten-things/AzureStorageExplorer_v4.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azcopy"></a>Adatok áthelyezése egy virtuális gépről egy Azure-blobba: AzCopy

Ha az adatátvitelt a helyi fájlok és a blob Storage között szeretné áthelyezni, a parancssorban vagy a PowerShellben is használhatja a AzCopy-t:

```powershell
AzCopy /Source:C:\myfolder /Dest:https://<mystorageaccount>.blob.core.windows.net/<mycontainer> /DestKey:<storage account key> /Pattern:abc.txt
```

Cserélje le a **C:\MyFolder** -t a fájl tárolásának elérési útjára, a **mystorageaccount** a blob Storage-fiók nevével, a **mycontainer** és a **Storage-fiók kulcsát** a blob Storage-hozzáférési kulcsával. A Storage-fiók hitelesítő adatait a [Azure Portalban](https://portal.azure.com)találja.

Futtassa a AzCopy parancsot a PowerShellben vagy egy parancssorból. Íme néhány példa a AzCopy parancs használatára:

```powershell
# Copy *.sql from a local machine to an Azure blob
"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:"c:\Aaqs\Data Science Scripts" /Dest:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /DestKey:[ENTER STORAGE KEY] /S /Pattern:*.sql

# Copy back all files from an Azure blob container to a local machine

"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Dest:"c:\Aaqs\Data Science Scripts\temp" /Source:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /SourceKey:[ENTER STORAGE KEY] /S
```

Miután futtatta a AzCopy parancsot egy Azure-blobba való másolásra, a fájl megjelenik Azure Storage Explorerban.

![Képernyőfelvétel a Storage-fiókról, a feltöltött CSV-fájl megjelenítése](./media/vm-do-ten-things/AzCopy_run_finshed_Storage_Explorer_v3.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azure-storage-explorer"></a>Adatok áthelyezése egy virtuális gépről egy Azure-blobba: Azure Storage Explorer

Az adatok a virtuális gépen lévő helyi fájlból is tölthetők fel Azure Storage Explorer használatával:

* Az adatok tárolóba való feltöltéséhez válassza ki a tárolót, és kattintson a **feltöltés** gombra. ![ Képernyőkép a feltöltés gombról Azure Storage Explorer](./media/vm-do-ten-things/storage-accounts.png)
* A **fájlok** mező jobb oldalán válassza a három pontot (**..**.), válassza ki a fájlrendszerből feltölteni kívánt fájlokat, majd válassza a **feltöltés** lehetőséget a fájlok feltöltésének megkezdéséhez. ![ Képernyőfelvétel a fájlok feltöltése párbeszédpanelről](./media/vm-do-ten-things/upload-files-to-blob.png)

#### <a name="read-data-from-an-azure-blob-python-odbc"></a>Adatok beolvasása egy Azure-blobból: Python ODBC

Az adatok közvetlenül egy Jupyter Notebook vagy egy Python-programban lévő blobból való beolvasásához használhatja a BlobService könyvtárat.

Először importálja a szükséges csomagokat:

```python
import pandas as pd
from pandas import Series, DataFrame
import numpy as np
import matplotlib.pyplot as plt
from time import time
import pyodbc
import os
from azure.storage.blob import BlobService
import tables
import time
import zipfile
import random
```

Ezután dugja be a blob Storage-fiók hitelesítő adatait, és olvassa el az adatokat a blobból:

```python
CONTAINERNAME = 'xxx'
STORAGEACCOUNTNAME = 'xxxx'
STORAGEACCOUNTKEY = 'xxxxxxxxxxxxxxxx'
BLOBNAME = 'nyctaxidataset/nyctaxitrip/trip_data_1.csv'
localfilename = 'trip_data_1.csv'
LOCALDIRECTORY = os.getcwd()
LOCALFILE =  os.path.join(LOCALDIRECTORY, localfilename)

#download from blob
t1 = time.time()
blob_service = BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILE)
t2 = time.time()
print(("It takes %s seconds to download "+BLOBNAME) % (t2 - t1))

#unzip downloaded files if needed
#with zipfile.ZipFile(ZIPPEDLOCALFILE, "r") as z:
#    z.extractall(LOCALDIRECTORY)

df1 = pd.read_csv(LOCALFILE, header=0)
df1.columns = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime','passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude']
print 'the size of the data is: %d rows and  %d columns' % df1.shape
```

Az adatgyűjtés adatkeretként történik:

![Képernyőfelvétel az első 10 adatsorról](./media/vm-do-ten-things/IPNB_data_readin.png)


### <a name="azure-synapse-analytics-and-databases"></a>Azure szinapszis-elemzés és-adatbázisok
Az Azure szinapszis Analytics egy rugalmas adattárház-szolgáltatás, amely nagyvállalati szintű SQL Server felülettel rendelkezik.

Az Azure szinapszis Analytics kiépítéséhez kövesse az ebben a [cikkben](../../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md)található utasításokat. Az Azure szinapszis Analytics üzembe helyezése után [ezzel az útmutatóval](../team-data-science-process/sqldw-walkthrough.md) az adatok feltöltését, feltárását és modellezését végezheti el az Azure szinapszis Analytics szolgáltatásban található adatok használatával.

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB
Azure Cosmos DB egy NoSQL-adatbázis a felhőben. Használhatja a JSON-hoz hasonló dokumentumokkal, valamint a dokumentumok tárolására és lekérdezésére.

A következő előfeltételek végrehajtásával érheti el Azure Cosmos DB a DSVM:

1. A Azure Cosmos DB Python SDK már telepítve van a DSVM. A frissítéséhez futtassa a ```pip install pydocumentdb --upgrade``` parancsot a parancssorból.
2. Hozzon létre egy Azure Cosmos DB fiókot és adatbázist a [Azure Portal](https://portal.azure.com).
3. Töltse le a Azure Cosmos DB adatáttelepítési eszközt a [Microsoft letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=53595) , és bontsa ki a kívánt könyvtárat.
4. Egy [nyilvános blobban](https://data.humdata.org/dataset/a60ac839-920d-435a-bf7d-25855602699d/resource/7234d067-2d74-449a-9c61-22ae6d98d928/download/volcano.json) tárolt JSON-adatok (vulkáni adatok) importálása Azure Cosmos DBba az áttelepítési eszközhöz a következő parancs-paraméterekkel. (Használja dtui.exe azon a könyvtáron, amelyre a Azure Cosmos DB adatáttelepítési eszközt telepítette.) Adja meg a forrás és a cél helyét a következő paraméterekkel:
   
    `/s:JsonFile /s.Files:https://data.humdata.org/dataset/a60ac839-920d-435a-bf7d-25855602699d/resource/7234d067-2d74-449a-9c61-22ae6d98d928/download/volcano.json /t:DocumentDBBulk /t.ConnectionString:AccountEndpoint=https://[DocDBAccountName].documents.azure.com:443/;AccountKey=[[KEY];Database=volcano /t.Collection:volcano1`

Az adat importálása után nyissa meg a Jupyter, és nyissa meg a *DocumentDBSample* című jegyzetfüzetet. Python-kódot tartalmaz a Azure Cosmos DB eléréséhez, és elvégezheti az alapszintű lekérdezéseket. A Azure Cosmos DBról további információt a szolgáltatás [dokumentációs oldalának](../../cosmos-db/index.yml)webhelyén talál.

## <a name="use-power-bi-reports-and-dashboards"></a>Power BI jelentések és irányítópultok használata 
Megjelenítheti a vulkáni JSON-fájlt az előző Azure Cosmos DB például Power BI Desktop, hogy vizuális elemzéseket nyerjen az adatokból. A részletes lépések a [Power bi cikkben](../../cosmos-db/powerbi-visualize.md)találhatók. A magas szintű lépések:

1. Indítsa el a Power BI Desktopot, majd válassza az **Adatok beolvasása** lehetőséget. Az URL-címet a következőképpen adhatja meg: `https://cahandson.blob.core.windows.net/samples/volcano.json` .
2. Ekkor megjelenik a listaként importált JSON-rekordok listája. Alakítsa át a listát egy táblázatba, hogy Power BI képes legyen vele dolgozni.
4. Bontsa ki az oszlopokat a Expand (nyíl) ikon kiválasztásával.
5. Figyelje meg, hogy a hely egy **rekord** mező. Bontsa ki a rekordot, és csak a koordinátákat válassza ki. A **koordináta** egy lista oszlop.
6. Adjon hozzá egy új oszlopot a lista koordináta oszlopának egy vesszővel tagolt **LatLong** oszlopba való átalakításához. Összefűzi a két elemet a koordináta-lista mezőben a képlet használatával ```Text.From([coordinates]{1})&","&Text.From([coordinates]{0})``` .
7. Alakítsa át a **jogosultságszint-emelési** oszlopot decimális értékre, és válassza a **Bezárás** és **alkalmazás** gombokat.

A fenti lépések helyett illessze be a következő kódot. Az adatátalakítások lekérdezési nyelven való írásához a Power BI Speciális szerkesztőban használt lépéseket.

```pqfl
let
    Source = Json.Document(Web.Contents("https://cahandson.blob.core.windows.net/samples/volcano.json")),
    #"Converted to Table" = Table.FromList(Source, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
    #"Expanded Column1" = Table.ExpandRecordColumn(#"Converted to Table", "Column1", {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}, {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}),
    #"Expanded Location" = Table.ExpandRecordColumn(#"Expanded Column1", "Location", {"coordinates"}, {"coordinates"}),
    #"Added Custom" = Table.AddColumn(#"Expanded Location", "LatLong", each Text.From([coordinates]{1})&","&Text.From([coordinates]{0})),
    #"Changed Type" = Table.TransformColumnTypes(#"Added Custom",{{"Elevation", type number}})
in
    #"Changed Type"
```

Most már rendelkezik a Power BI adatmodellben lévő adataival. A Power BI Desktop példányának a következőképpen kell megjelennie:

![Power BI Desktop](./media/vm-do-ten-things/PowerBIVolcanoData.png)

Jelentéseket és vizualizációkat hozhat létre az adatmodell használatával. A következő cikkben ismertetett lépéseket követve hozhat létre egy jelentést: [Power bi](../../cosmos-db/powerbi-visualize.md#build-the-reports) .

## <a name="scale-the-dsvm-dynamically"></a>A DSVM dinamikus méretezése 
A DSVM vertikális fel-és leskálázásával is teljesítheti a projekt igényeit. Ha a virtuális gépet nem kell esti vagy hétvégeken használni, leállíthatja a virtuális gépet a [Azure Portalról](https://portal.azure.com).

> [!NOTE]
> Ha csak a virtuális gép operációs rendszerének leállítás gombját használja, a számítási költségeket számítjuk fel. Ehelyett fel kell szabadítania a DSVM a Azure Portal vagy Cloud Shell használatával.
> 
> 

Előfordulhat, hogy néhány nagy méretű elemzést kell kezelnie, és több PROCESSZORt, memóriát vagy lemezterületet kell használnia. Ha igen, a számítási és a költségvetési igényeknek megfelelő méretű virtuálisgép-méretek közül választhat a CPU-magok, a GPU-alapú példányok a Deep learning, a memória kapacitása és a lemez típusa (beleértve a SSD-meghajtókat is) tekintetében. A virtuális gépek teljes listája, valamint óradíjas számítási díjszabása az [Azure Virtual Machines díjszabási](https://azure.microsoft.com/pricing/details/virtual-machines/) oldalán érhető el.


## <a name="add-more-tools"></a>További eszközök hozzáadása
A DSVM beépített eszközök számos gyakori adatelemzési igényt tudnak kezelni. Ezzel időt takaríthat meg, mert nem kell egyenként telepítenie és konfigurálnia a környezeteket. Pénzt takarít meg, mivel csak a felhasznált erőforrásokért kell fizetnie.

A cikkben ismertetett egyéb Azure-adatelemzési és-elemzési szolgáltatások segítségével javíthatja az elemzési környezetét. Bizonyos esetekben további eszközökre lehet szükség, beleértve néhány saját partneri eszközt is. A virtuális gépen teljes körű rendszergazdai hozzáféréssel rendelkezik a szükséges új eszközök telepítéséhez. További csomagokat is telepíthet a Pythonban és az R-ben, amelyek nincsenek előre telepítve. A Python esetében használhatja a vagy a programot is ```conda``` ```pip``` . Az r esetében használhatja az ```install.packages()``` r-konzolt, vagy használhatja az ide-t, és válassza a **csomagok**  >  **telepítési csomagok** lehetőséget.

## <a name="deep-learning"></a>Deep learning

A keretrendszeren alapuló mintákon kívül olyan átfogó útmutatók is beszerezhetők, amelyek ellenőrzése megtörtént a DSVM. Ezek a forgatókönyvek megkönnyítik a mélyreható tanulási alkalmazások fejlesztését olyan tartományokban, mint például a képek és a szövegek és a nyelvek megismerése.   


- [Neurális hálózatok futtatása különböző keretrendszerekben](https://github.com/ilkarman/DeepLearningFrameworks): Ez a bemutató bemutatja, hogyan lehet áttelepíteni a kódot az egyik keretrendszerről a másikra. Azt is bemutatja, hogyan lehet összehasonlítani a modelleket és a futásidejű teljesítményt a keretrendszerek között. 

- [Útmutató a képeken belüli termékek észlelésére szolgáló teljes körű megoldás](https://github.com/Azure/cortana-intelligence-product-detection-from-images)létrehozásához: a képészlelés egy olyan technika, amely képes objektumokat megkeresni és osztályozni a képeken belül. Ez a technológia jelentős előnyökkel bír számos valós üzleti tartományban. A kiskereskedők például használhatják ezt a technikát annak meghatározására, hogy az ügyfél melyik terméket választotta fel a polcról. Ezek az információk segítenek a termékek leltározásának kezelésében. 

- [Mély tanulás a hanggal](/archive/blogs/machinelearning/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure): ez az oktatóanyag azt mutatja be, hogyan lehet betanítani a Hangesemények észlelésére szolgáló mélyreható tanulási modellt a [városi hangok adatkészletében](https://serv.cusp.nyu.edu/projects/urbansounddataset/urbansound8k.html). Emellett áttekintést nyújt a hangadatokkal való munkáról.

- [Szöveges dokumentumok besorolása](https://github.com/anargyri/lstm_han): Ez a bemutató azt mutatja be, hogyan lehet két neurális hálózati architektúrát létrehozni és betanítani: hierarchikus figyelmet a hálózatra és a hosszú távú memória-(LSTM-) hálózatra. Ezek a neurális hálózatok a kerasz API-t használják a mélyreható tanuláshoz a szöveges dokumentumok besorolásához. 

## <a name="summary"></a>Összefoglalás
Ez a cikk néhány olyan dolgot ismertetett, amelyet a Microsoft Data Science Virtual Machine végezhet el. Számos további dolgot tehet a DSVM hatékony elemzési környezetének elvégzéséhez.
