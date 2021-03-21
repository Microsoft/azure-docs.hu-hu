---
title: Azure Integration Runtime létrehozása a Azure Data Factory-ben
description: Megtudhatja, hogyan hozhat létre Azure Integration Runtime-t a Azure Data Factoryban, amely az adatmásolási és-átalakítási tevékenységek másolására szolgál.
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/09/2020
author: nabhishek
ms.author: abnarain
ms.openlocfilehash: ecf2a74a16234084fbac4d1c26157d1703b56a13
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100373062"
---
# <a name="how-to-create-and-configure-azure-integration-runtime"></a>Azure-beli integrációs modul létrehozása és konfigurálása
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A Integration Runtime (IR) a Azure Data Factory által használt számítási infrastruktúra, amely különböző hálózati környezetekben biztosít adatintegrációs képességeket. Az IR-vel kapcsolatos további információkért lásd: [Integration Runtime](concepts-integration-runtime.md).

A Azure IR teljes körűen felügyelt számítást biztosít az adatáthelyezés natív módon történő elvégzéséhez és az Adatátalakítási tevékenységek elküldéséhez a számítási szolgáltatások, például a HDInsight számára. Az Azure-környezetben üzemel, és támogatja a nyilvános hálózati környezetben lévő erőforrásokhoz való csatlakozást nyilvános elérhetőségű végpontokkal.

Ez a dokumentum bemutatja, hogyan hozhat létre és konfigurálhat Azure Integration Runtime. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-azure-ir"></a>Alapértelmezett Azure IR
Alapértelmezés szerint minden egyes adatfeldolgozó Azure IR rendelkezik a háttérben, amely támogatja a Felhőbeli adattárakon és a nyilvános hálózatban lévő számítási szolgáltatásokban végzett műveleteket. A Azure IR helye az autooldják. Ha a **connectvia tulajdonsággal** tulajdonság nincs megadva a társított szolgáltatás definíciójában, a rendszer az alapértelmezett Azure IR használja. Csak explicit módon kell létrehoznia egy Azure IR, ha explicit módon meg szeretné határozni az IR helyét, vagy ha azt szeretné, hogy gyakorlatilag a tevékenység-végrehajtásokat a különböző IRs felügyeleti célokra lehessen csoportosítani. 

## <a name="create-azure-ir"></a>Azure IR létrehozása

Azure IR létrehozásához és beállításához használhatja az alábbi eljárásokat.

### <a name="create-an-azure-ir-via-azure-powershell"></a>Azure IR létrehozása Azure PowerShell használatával
Integration Runtime a **set-AzDataFactoryV2IntegrationRuntime PowerShell-** parancsmag használatával hozható létre. Azure IR létrehozásához adja meg a nevet, a helyet és a típust a parancshoz. Az alábbi példa egy olyan Azure IR létrehozására szolgál, amelynek a helye "Nyugat-Európa":

```powershell
Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName "SampleV2DataFactory1" -Name "MySampleAzureIR" -ResourceGroupName "ADFV2SampleRG" -Type Managed -Location "West Europe"
```  
Azure IR esetén a típust **felügyelt** értékre kell beállítani. Nem kell megadnia a számítási adatokat, mert teljes mértékben felügyelt a felhőben. Azure-SSIS IR létrehozásához a számítási adatokat, például a csomópontok méretét és a csomópontok darabszámát kell megadni. További információ: [Azure-SSIS IR létrehozása és konfigurálása](create-azure-ssis-integration-runtime.md).

A Set-AzDataFactoryV2IntegrationRuntime PowerShell-parancsmag használatával meglévő Azure IR is konfigurálhat a hely módosításához. Az Azure IR helyével kapcsolatos további információkért lásd: az [Integration Runtime bemutatása](concepts-integration-runtime.md).

### <a name="create-an-azure-ir-via-azure-data-factory-ui"></a>Azure IR létrehozása Azure Data Factory felhasználói felületen
A következő lépések végrehajtásával hozhat létre Azure IR a Azure Data Factory felhasználói felületen.

1. Azure Data Factory felhasználói felületének első **lépések** oldalán válassza a [kezelés fület](./author-management-hub.md) a bal szélső ablaktáblán.

   ![A Kezdőlap kezelés gombja](media/doc-common-process/get-started-page-manage-button.png)

1. Válassza az **integrációs** modulok lehetőséget a bal oldali ablaktáblán, majd válassza az **+ új** lehetőséget.

   ![Képernyőfelvétel: az integrációs modulokat a bal oldali panelen és az + új gombon keresztül emeli ki.](media/doc-common-process/manage-new-integration-runtime.png)

1. Az **Integration Runtime telepítése** lapon válassza az **Azure, a saját** üzemeltetésű lehetőséget, majd kattintson a **Folytatás** gombra. 

1. A következő lapon válassza az **Azure** lehetőséget Azure IR létrehozásához, majd válassza a **Folytatás** lehetőséget.
   ![Integrációs modul létrehozása](media/create-azure-integration-runtime/new-azure-integration-runtime.png)

1. Adja meg a Azure IR nevét, majd válassza a **Létrehozás** lehetőséget.
   ![Azure IR létrehozása](media/create-azure-integration-runtime/create-azure-integration-runtime.png)

1. Ekkor megjelenik egy előugró értesítés, amikor a létrehozás befejeződik. Az **Integration Runtimes (integrációs** modulok) lapon győződjön meg arról, hogy az újonnan létrehozott IR megjelenik a listában.

## <a name="use-azure-ir"></a>Azure IR használata

Azure IR létrehozása után hivatkozhat rá a társított szolgáltatás definíciójában. Alább látható egy példa arra, hogyan hivatkozhat a fent létrehozott Azure Integration Runtime egy Azure Storage-beli társított szolgáltatásból:

```json
{
    "name": "MyStorageLinkedService",
    "properties": {
      "type": "AzureStorage",
      "typeProperties": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=myaccountname;AccountKey=..."
      },
      "connectVia": {
        "referenceName": "MySampleAzureIR",
        "type": "IntegrationRuntimeReference"
      }   
    }
}

```

## <a name="next-steps"></a>Következő lépések
Az integrációs modulok egyéb típusainak létrehozásáról a következő cikkekben talál további információt:

- [Saját üzemeltetésű integrációs modul létrehozása](create-self-hosted-integration-runtime.md)
- [Azure SSIS integrációs modul létrehozása](create-azure-ssis-integration-runtime.md)
