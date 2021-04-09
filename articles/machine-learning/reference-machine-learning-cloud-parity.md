---
title: Paritás a nyilvános és a szuverén régiók között
titleSuffix: Azure Machine Learning
description: Ez a cikk a nyilvános felhő és a Azure Government, az Azure Germany és az Azure China 21Vianet régiói közötti szolgáltatási paritást sorolja fel.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: larryfr
ms.author: andzha
author: Anurzeuii
ms.date: 12/21/2020
ms.custom: references_regions
ms.openlocfilehash: 88240f9b46997d11f1e7c2d93fa880b004615a11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "97725020"
---
# <a name="azure-machine-learning-sovereign-cloud-parity"></a>Azure Machine Learning szuverén felhő paritása

Ismerje meg, hogy milyen Azure Machine Learning funkciók érhetők el a szuverén Felhőbeli régiókban. 

A globális Azure-régiók listájában több "szuverén" régió is található, amelyek adott piacokat szolgálnak ki. Például a Azure Government és az Azure China 21Vianet régiói. Jelenleg Azure Machine Learning üzembe helyezése a következő szuverén Felhőbeli régiókba történik:

* Azure Government az **USA – Arizona** és **USA – Virginia** régiókat.
* Azure China 21Vianet-régió, **Kína – Kelet – 2**.

> [!TIP]
> A szuverén és nem szuverén régiók közötti különbségtételhez ez a cikk a __nyilvános felhő__ kifejezését fogja használni a nem szuverén régiókra való hivatkozáshoz.

Célunk, hogy a nyilvános felhő és a szuverén régiók között maximális paritást biztosítson. Ezekben a régiókban minden Azure Machine Learning funkció elérhető lesz a nyilvános felhőben (általános rendelkezésre állás) számított **30 napon** belül. Ezen régiókban lehetőség van az előzetes verziójú funkciók számának kiválasztására is. Az alábbiakban a szuverén és a nyilvános felhők közötti jelenlegi paritásbeli különbségek láthatók.

## <a name="azure-government"></a>Azure Government 

| Szolgáltatás | Nyilvános felhő állapota  | US-Virginia | US-Arizona| 
|----------------------------------------------------------------------------|:----------------------:|:--------------------:|:-------------:|
| **Automatizált gépi tanulás** | | | |
| Kísérletek létrehozása és futtatása jegyzetfüzetekben                                    | FE                   | IGEN                | IGEN         |
| Kísérletek létrehozása és futtatása a Studio webes felületén                        | Nyilvános előzetes verzió       | IGEN                | IGEN         |
| Piacvezető előrejelzési képességek                                  | FE                   | IGEN                | IGEN         |
| A Deep learning és az egyéb fejlett tanulók támogatása                      | FE                   | IGEN                | IGEN         |
| Nagy mennyiségű adat támogatása (legfeljebb 100 GB)                                          | Nyilvános előzetes verzió       | IGEN                | IGEN         |
| Azure Databricks integráció                                              | FE                   | NO                 | NO          |
| SQL-, CosmosDB-és HDInsight-integrációk                                   | FE                   | IGEN                | IGEN         |
| **Machine Learning folyamatok** |   |  | | 
| Folyamatok létrehozása, futtatása és közzététele az Azure ML SDK használatával                   | FE                   | IGEN                | IGEN         |
| Folyamat-végpontok létrehozása az Azure ML SDK használatával                           | FE                   | IGEN                | IGEN         |
| Folyamatok ütemezett futtatásának létrehozása, szerkesztése és törlése az Azure ML SDK használatával | FE                   | IGEN               | IGEN        |
| A folyamat futtatási részleteinek megtekintése a Studióban                                        | FE                   | IGEN                | IGEN         |
| Folyamatok létrehozása, futtatása, megjelenítése és közzététele az Azure ML Designerben          | FE      | IGEN                | IGEN         |
| Azure Databricks integráció az ML-folyamattal                             | FE                   | NO                 | NO          |
| Folyamat-végpontok létrehozása az Azure ML Designerben                             | FE      | IGEN                | IGEN         |
| **Integrált jegyzetfüzetek** |   |  | | 
| Munkaterület-jegyzetfüzet és fájlmegosztás                                        | FE                   | IGEN                | IGEN         |
| R-és Python-támogatás                                                       | FE                   | IGEN                | IGEN         |
| Virtuális hálózatok támogatása                                                    | Nyilvános előzetes verzió       | NO                 | NO          |
| **Számítási példány** |   |  | | 
| Felügyelt számítási példányok integrált jegyzetfüzetekhez                         | FE                   | IGEN                | IGEN         |
| Jupyter, JupyterLab-integráció                                            | FE                   | IGEN                | IGEN         |
| Virtual Network (VNet) támogatása                                             | Nyilvános előzetes verzió       | IGEN                | IGEN         |
| **SDK-támogatás** |  |  | | 
| R SDK-támogatás                                                              | Nyilvános előzetes verzió       | IGEN                | IGEN         |
| Python SDK-támogatás                                                         | FE                   | IGEN                | IGEN         |
| **Biztonság** |   | | | 
| Virtual Network (VNet) képzés támogatása                                | FE                   | IGEN                | IGEN         |
| Virtual Network (VNet) a következtetések támogatása                               | FE                   | IGEN                | IGEN         |
| Pontozási végpont hitelesítése                                            | Nyilvános előzetes verzió       | IGEN                | IGEN         |
| Munkahelyi privát hivatkozás                                                     | Nyilvános előzetes verzió       | NO                 | NO          |
| ACI a VNet mögött                                                            | Nyilvános előzetes verzió       | NO                 | NO          |
| VNet mögötti ACR                                                            | Nyilvános előzetes verzió       | NO                 | NO          |
| Az AK-fürt magánhálózati IP-címe                                                  | Nyilvános előzetes verzió       | NO                 | NO          |
| **Számítás** |   | | |
| Kvótakezelés a munkaterületek között                                         | FE                   | IGEN                | IGEN         |
| **A gépi tanulásra vonatkozó tudnivalók** |   | | |
| Adatkészletek és adattárolók létrehozása, megtekintése és szerkesztése az SDK-ból                  | FE                   | IGEN                | IGEN         |
| Adatkészletek és adattárolók létrehozása, megtekintése és szerkesztése a felhasználói felületen                   | FE                   | IGEN                | IGEN         |
| Adatkészlet-drift figyelők megtekintése, szerkesztése vagy törlése az SDK-ból                   | Nyilvános előzetes verzió       | IGEN                | IGEN         |
| Adatkészlet-drift figyelők megtekintése, szerkesztése vagy törlése a felhasználói felületen                    | Nyilvános előzetes verzió       | IGEN                | IGEN         |
| **Gépi tanulás életciklusa** |   | | |
| Modell profilkészítése                                                            | FE                   | IGEN                | RÉSZLEGES     |
| Az Azure DevOps bővítmény a Machine Learning & a Azure ML CLI         | FE                   | IGEN                | IGEN         |
| FPGA-alapú Hardware Accelerated Models                                     | FE                   | NO                 | NO          |
| Visual Studio Code-integráció                                             | Nyilvános előzetes verzió       | NO                 | NO          |
| Event Grid-integráció                                                     | Nyilvános előzetes verzió       | NO                 | NO          |
| Azure Stream Analytics integrálása Azure Machine Learning               | Nyilvános előzetes verzió       | NO                 | NO          |
| **Címkézés** |   | | |
| Projekt címkézése felügyeleti portál                                        | FE                   | IGEN                | IGEN         |
| Labeler-portál                                                            | FE                   | IGEN                | IGEN         |
| Címkézés a privát munkaerő használatával                                          | FE                   | IGEN                | IGEN         |
| ML támogatott címkézés (képbesorolás és objektumok észlelése)           | Nyilvános előzetes verzió       | IGEN                | IGEN         |
| **Felelősségteljes ML** |   | | |
| A felhasználói felület ismertetése                                                       | Nyilvános előzetes verzió       | NO                 | NO          |
| Differenciált adatvédelmi SmartNoise eszközkészlet                                    | OSS                  | NO                 | NO          |
| egyéni címkék a Azure Machine Learningban az adatlapok megvalósításához              | FE                   | NO                 | NO          |
| Méltányos AzureML-integráció                                               | Nyilvános előzetes verzió       | NO                 | NO          |
| Értelmező SDK                                                      | FE                   | IGEN                | IGEN         |
| **Képzés** |   | | |
| Kísérletezési naplók folyamatos átvitele                                              | FE                   | IGEN                | IGEN         |
| Megerősítő tanulás                                                     | Nyilvános előzetes verzió       | NO                 | NO          |
| Kísérletezés felhasználói felülete                                                         | FE                   | IGEN                | IGEN         |
| .NET-integráció ML.NET 1,0                                                | FE                   | IGEN                | IGEN         |
| **Következtetésekhez** |   | | |
| A Batch-következtetések                                                          | FE                   | IGEN                | IGEN         |
| Data Box Edge FPGA                                                    | Nyilvános előzetes verzió       | NO                 | NO          |
| **Egyéb** |   | | |
| Open Datasets                                                              | Nyilvános előzetes verzió       | IGEN                | IGEN         |
| Egyéni Cognitive Search                                                    | Nyilvános előzetes verzió       | IGEN                | IGEN         |
| Számos modell                                                                | Nyilvános előzetes verzió       | NO                 | NO          |


### <a name="azure-government-scenarios"></a>Azure Government forgatókönyvek

| Eset                                                    | US-Virginia | US-Arizona| Korlátozások  |
|----------------------------------------------------------------------------|:----------------------:|:--------------------:|-------------|
| **Általános biztonsági beállítások** |   | | |
| Privát hálózati kommunikáció a szolgáltatások között                                     | NO | NO | Jelenleg nincs privát hivatkozás | 
| Internet-hozzáférés (bejövő és kimenő) és adott VNet letiltása/szabályozása | RÉSZLEGES| RÉSZLEGES   | Az VNet mögötti ACR nem érhető el a Azure Government-Double Check on ACI-ban | 
| Az összes kapcsolódó erőforrás/szolgáltatás elhelyezése  | IGEN | IGEN |  |
| Titkosítás a nyugalmi állapotban és az átvitel során.                                                 | IGEN | IGEN |  |
| Gyökér-és SSH-hozzáférés a számítási erőforrásokhoz.                                          | IGEN | IGEN |  |
| Az üzembe helyezett rendszerek (példányok, végpontok stb.) biztonságának fenntartása, beleértve az Endpoint Protectiont, a javítást és a naplózást |  RÉSZLEGES|  RÉSZLEGES |A VNet mögötti ACI és a privát végpont jelenleg nem érhető el |                                  
| Az ACI/AK-integráció használatának szabályozása (Letiltás/korlátozás/korlátozás)                    | RÉSZLEGES| RÉSZLEGES |A VNet mögötti ACI és a privát végpont jelenleg nem érhető el|
| Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) – egyéni szerepkörök létrehozása                           | IGEN | IGEN |  |
| A ML szolgáltatás által használt ACR-rendszerképekhez való hozzáférés szabályozása (Azure által biztosított/karbantartott, illetve egyéni)  |RÉSZLEGES|  RÉSZLEGES | A magánhálózati végpontok és a VNet mögötti ACR nem támogatott Azure Government |
| **Általános Machine Learning szolgáltatás használata** |  | | |
| Fejlesztési környezet kialakítása modell létrehozásához, a modell betanításához, végpontként való üzemeltetéséhez, és egy WebApp használatával     | IGEN | IGEN |  |
| Adatok lekérése a ADLS (Data Lake Storage)                                 |IGEN | IGEN |  |
| Adatok lekérése az Azure-ból Blob Storage                                       |IGEN | IGEN |  |



### <a name="additional-azure-government-limitations"></a>További Azure Government korlátozások

* Azure Machine Learning számítási példányok esetében a 24 óránál hosszabb ideig tartó tokenek frissítése nem érhető el Azure Governmentban.
* A Model profilkészítés nem támogatja a 4 CPU-t a US-Arizona régióban.   
* Előfordulhat, hogy a minta jegyzetfüzetek nem működnek Azure Governmentban, ha nyilvános adatelérésre van szükségük.
* IP-címek: a [VNet és a kényszerített bújtatási](how-to-secure-training-vnet.md#forced-tunneling) utasításokban használt CLI-parancs nem ad vissza IP-tartományokat. Ehelyett használja az [Azure IP-címtartományok és a szolgáltatás címkéit Azure Government](https://www.microsoft.com/download/details.aspx?id=57063) .
* Az ütemezett folyamatok esetében blob-alapú trigger mechanizmust is biztosítunk. Ez a mechanizmus CMK-munkaterületek esetén nem támogatott. A CMK-munkaterületek blob-alapú triggerének engedélyezéséhez további telepítést kell végrehajtania. További információ: [gépi tanulási folyamat futtatásának elindítása logikai alkalmazásból](how-to-trigger-published-pipeline.md).
* Tűzfalak: Azure Government-régió használatakor adja hozzá a következő további gazdagépeket a tűzfal-beállításhoz:

    * Arizona használata esetén: `usgovarizona.api.ml.azure.us`
    * A Virginia használatára: `usgovvirginia.api.ml.azure.us`
    * Mindkettő esetében: `graph.windows.net` 


## <a name="azure-china-21vianet"></a>Azure China 21Vianet 

| Szolgáltatás                                       | Nyilvános felhő állapota | CH-Kelet – 2 | CH-Észak-3 |
|----------------------------------------------------------------------------|:------------------:|:--------------------:|:-------------:|
| **Automatizált gépi tanulás** |    | | |
| Kísérletek létrehozása és futtatása jegyzetfüzetekben                                    | FE               | IGEN       | N/A        |
| Kísérletek létrehozása és futtatása a Studio webes felületén                        | Nyilvános előzetes verzió   | IGEN       | N/A        |
| Piacvezető előrejelzési képességek                                  | FE               | IGEN       | N/A        |
| A Deep learning és az egyéb fejlett tanulók támogatása                      | FE               | IGEN       | N/A        |
| Nagy mennyiségű adat támogatása (legfeljebb 100 GB)                                          | Nyilvános előzetes verzió   | IGEN       | N/A        |
| Azure Databricks integráció                                              | FE               | NO        | N/A        |
| SQL-, CosmosDB-és HDInsight-integrációk                                   | FE               | IGEN       | N/A        |
| **Machine Learning folyamatok** |    | | |
| Folyamatok létrehozása, futtatása és közzététele az Azure ML SDK használatával                   | FE               | IGEN       | N/A        |
| Folyamat-végpontok létrehozása az Azure ML SDK használatával                           | FE               | IGEN       | N/A        |
| Folyamatok ütemezett futtatásának létrehozása, szerkesztése és törlése az Azure ML SDK használatával | FE               | IGEN       | N/A        |
| A folyamat futtatási részleteinek megtekintése a Studióban                                        | FE               | IGEN       | N/A        |
| Folyamatok létrehozása, futtatása, megjelenítése és közzététele az Azure ML Designerben          | FE  | IGEN       | N/A        |
| Azure Databricks integráció az ML-folyamattal                             | FE               | NO        | N/A        |
| Folyamat-végpontok létrehozása az Azure ML Designerben                             | FE   | IGEN       | N/A        |
| **Integrált jegyzetfüzetek** |   | | |
| Munkaterület-jegyzetfüzet és fájlmegosztás                                        | FE               | IGEN       | N/A        |
| R-és Python-támogatás                                                       | FE               | IGEN       | N/A        |
| Virtuális hálózatok támogatása                                                    | Nyilvános előzetes verzió   | NO        | N/A        |
| **Számítási példány** |    | | |
| Felügyelt számítási példányok integrált jegyzetfüzetekhez                         | FE               | NO        | N/A        |
| Jupyter, JupyterLab-integráció                                            | FE               | IGEN       | N/A        |
| Virtual Network (VNet) támogatása                                             | Nyilvános előzetes verzió   | IGEN       | N/A        |
| **SDK-támogatás** |    | | |
| R SDK-támogatás                                                              | Nyilvános előzetes verzió   | IGEN       | N/A        |
| Python SDK-támogatás                                                         | FE               | IGEN       | N/A        |
| **Biztonság** |   | | |
| Virtual Network (VNet) képzés támogatása                                | FE               | IGEN       | N/A        |
| Virtual Network (VNet) a következtetések támogatása                               | FE               | IGEN       | N/A        |
| Pontozási végpont hitelesítése                                            | Nyilvános előzetes verzió   | IGEN       | N/A        |
| Munkahelyi privát hivatkozás                                                     | Nyilvános előzetes verzió   | NO        | N/A        |
| ACI a VNet mögött                                                            | Nyilvános előzetes verzió   | NO        | N/A        |
| VNet mögötti ACR                                                            | Nyilvános előzetes verzió   | NO        | N/A        |
| Az AK-fürt magánhálózati IP-címe                                                  | Nyilvános előzetes verzió   | NO        | N/A        |
| **Számítás** |   | | |
| Kvótakezelés a munkaterületek között                                         | FE               | IGEN       | N/A        |
| **A gépi tanulásra vonatkozó tudnivalók** | | | |
| Adatkészletek és adattárolók létrehozása, megtekintése és szerkesztése az SDK-ból                  | FE               | IGEN       | N/A        |
| Adatkészletek és adattárolók létrehozása, megtekintése és szerkesztése a felhasználói felületen                   | FE               | IGEN       | N/A        |
| Adatkészlet-drift figyelők megtekintése, szerkesztése vagy törlése az SDK-ból                   | Nyilvános előzetes verzió   | IGEN       | N/A        |
| Adatkészlet-drift figyelők megtekintése, szerkesztése vagy törlése a felhasználói felületen                    | Nyilvános előzetes verzió   | IGEN       | N/A        |
| **Gépi tanulás életciklusa** |    | | |
| Modell profilkészítése                                                            | FE               | RÉSZLEGES   | N/A        |
| Az Azure DevOps bővítmény a Machine Learning & a Azure ML CLI         | FE               | IGEN       | N/A        |
| FPGA-alapú Hardware Accelerated Models                                     | FE               | NO        | N/A        |
| Visual Studio Code-integráció                                             | Nyilvános előzetes verzió   | NO        | N/A        |
| Event Grid-integráció                                                     | Nyilvános előzetes verzió   | IGEN       | N/A        |
| Azure Stream Analytics integrálása Azure Machine Learning               | Nyilvános előzetes verzió   | NO        | N/A        |
| **Címkézés** |    | | |
| Projekt címkézése felügyeleti portál                                        | FE               | IGEN       | N/A        |
| Labeler-portál                                                            | FE               | IGEN       | N/A        |
| Címkézés a privát munkaerő használatával                                          | FE               | IGEN       | N/A        |
| ML támogatott címkézés (képbesorolás és objektumok észlelése)           | Nyilvános előzetes verzió   | IGEN       | N/A        |
| **Felelősségteljes ML** |    | | |
| A felhasználói felület ismertetése                                                       | Nyilvános előzetes verzió   | NO        | N/A        |
| Differenciált adatvédelmi SmartNoise eszközkészlet                                    | OSS              | NO        | N/A        |
| egyéni címkék a Azure Machine Learningban az adatlapok megvalósításához              | FE               | NO        | N/A        |
| Méltányos AzureML-integráció                                               | Nyilvános előzetes verzió   | NO        | N/A        |
| Értelmező SDK                                                      | FE               | IGEN       | N/A        |
| **Képzés** |    | | |
| Kísérletezési naplók folyamatos átvitele                                              | FE               | IGEN       | N/A        |
| Megerősítő tanulás                                                     | Nyilvános előzetes verzió   | NO        | N/A        |
| Kísérletezés felhasználói felülete                                                         | FE               | IGEN       | N/A        |
| .NET-integráció ML.NET 1,0                                                | FE               | IGEN       | N/A        |
| **Következtetésekhez** |   | | |
| A Batch-következtetések                                                          | FE               | IGEN       | N/A        |
| Data Box Edge FPGA                                                    | Nyilvános előzetes verzió   | NO        | N/A        |
| **Egyéb** |    | | |
| Open Datasets                                                              | Nyilvános előzetes verzió   | IGEN       | N/A        |
| Egyéni Cognitive Search                                                    | Nyilvános előzetes verzió   | IGEN       | N/A        |
| Számos modell                                                                | Nyilvános előzetes verzió   | NO        | N/A        |



### <a name="additional-azure-china-limitations"></a>További Azure China-korlátozások

* Az Azure China korlátozott VM SKU-val rendelkezik, különösen a GPU SKU esetében. Csak NCv3 család (V100).
* REST API végpontok eltérnek a globális Azure-tól. Az alábbi táblázat segítségével megkeresheti az Azure China-régiók REST API végpontját:

    | REST-végpont                 | Globális Azure                                 | China-Government                           |
    |------------------|--------------------------------------------|--------------------------------------------|
    | Felügyeleti sík | `https://management.azure.com/`              | `https://management.chinacloudapi.cn/`       |
    | Adatsík       | `https://{location}.experiments.azureml.net` | `https://{location}.experiments.ml.azure.cn` |
    | Azure Active Directory              | `https://login.microsoftonline.com`          | `https://login.chinacloudapi.cn`             |

* Előfordulhat, hogy a minta jegyzetfüzet nem működik, ha a nyilvános adathoz való hozzáférésre van szükség.
* IP-címtartományok: az [VNet kényszerített bújtatási](how-to-secure-training-vnet.md#forced-tunneling) utasításban használt CLI-parancs nem ad vissza IP-tartományokat. Ehelyett használja az [Azure IP-címtartományok és a szolgáltatás címkéit az Azure China-](https://www.microsoft.com//download/details.aspx?id=57062) hoz.
* Azure Machine Learning a számítási példányok előzetes verziója nem támogatott olyan munkaterületen, amelyben a magánhálózati kapcsolat már engedélyezve van, de a CI a szolgáltatás bővítésének következő üzembe helyezése során is támogatott lesz az összes pénzmosás-régióra.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni arról, hogy az Azure Machine learning mely régiókban érhető el, tekintse meg a [termékek régiónként](https://azure.microsoft.com/global-infrastructure/services/)című témakört.
