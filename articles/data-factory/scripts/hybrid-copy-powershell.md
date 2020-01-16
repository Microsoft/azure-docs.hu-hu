---
title: Adatok másolása a helyszínről az Azure-ba a PowerShell használatával
description: Ez a PowerShell-parancsfájl egy helyszíni SQL Server adatbázisból egy másik Azure-Blob Storageba másolja az adatait.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.author: jingwang
author: linda33wj
manager: shwang
ms.custom: seo-lt-2019
ms.date: 10/31/2017
ms.openlocfilehash: 10555defc4888af66bb88d19190b6543aa8ae0c9
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75974694"
---
# <a name="use-powershell-to-create-a-data-factory-pipeline-to-copy-data-from-on-premises-to-azure"></a>A PowerShell használata a helyszíni adatok Azure-ba való másolásához szükséges adatfeldolgozó-folyamat létrehozásához

Ez a PowerShell-szkript létrehoz egy folyamatot a Azure Data Factoryban, amely egy helyszíni SQL Server-adatbázisból egy Azure-Blob Storage másolja az adatokból.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Előfeltételek

- **Egy SQL Server**. Ebben a példában egy helyszíni SQL Server-adatbázist használ **forrásként** szolgáló adattárként.
- **Azure Storage-fiók** Ebben a példában az Azure Blob Storage-t **célként/fogadó** adattárként kell használni. Ha még nem rendelkezik Azure Storage-fiókkal, a létrehozás folyamatáért lásd a [tárfiók létrehozását](../../storage/common/storage-account-create.md) ismertető cikket.
- **Saját üzemeltetésű integrációs**modul. Töltse le az MSI-fájlt a [letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=39717) , és futtassa a saját üzemeltetésű integrációs modul telepítéséhez a gépen.  

### <a name="create-sample-database-in-sql-server"></a>Mintaadatbázis létrehozása a SQL Serverban
1. A helyszíni SQL Server adatbázisban hozzon létre egy **EMP** nevű táblát a következő SQL-parancsfájl használatával:

   ```sql   
     CREATE TABLE dbo.emp
     (
         ID int IDENTITY(1,1) NOT NULL,
         FirstName varchar(50),
         LastName varchar(50),
         CONSTRAINT PK_emp PRIMARY KEY (ID)
     )
     GO
   ```

2. Helyezzen be néhány mintaadatok a táblába:

   ```sql
     INSERT INTO emp VALUES ('John', 'Doe')
     INSERT INTO emp VALUES ('Jane', 'Doe')
   ```

## <a name="sample-script"></a>Példaszkript

> [!IMPORTANT]
> Ez a szkript olyan JSON-fájlokat hoz létre, amelyek meghatározzák Data Factory entitásokat (társított szolgáltatás, adatkészlet és folyamat) a merevlemezen a c:\ mappa.

[!code-powershell[main](../../../powershell_scripts/data-factory/copy-from-onprem-sql-server-to-azure-blob/copy-from-onprem-sql-server-to-azure-blob.ps1 "Copy from on-premises SQL Server -> Azure Blob Storage")]


## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A minta parancsfájl futtatása után a következő paranccsal távolíthatja el az erőforráscsoportot és az ahhoz társított összes erőforrást:

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```
A következő parancs futtatásával távolíthatja el az adatgyárat az erőforráscsoporthoz:

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja:

| Parancs | Megjegyzések |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | Adat-előállító létrehozása |
| [Új – AzDataFactoryV2LinkedServiceEncryptCredential](/powershell/module/az.datafactory/new-Azdatafactoryv2linkedserviceencryptedcredential) | Titkosítja a hitelesítő adatokat egy társított szolgáltatásban, és létrehoz egy új társított szolgáltatás-definíciót a titkosított hitelesítő adatokkal.
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/Set-Azdatafactoryv2linkedservice) | Létrehoz egy társított szolgáltatást az adatelőállítóban. A társított szolgáltatások adattárakhoz vagy számítási feladatokhoz kapcsolódnak egy adatgyárban. |
| [Set-AzDataFactoryV2Dataset](/powershell/module/az.datafactory/Set-Azdatafactoryv2dataset) | Létrehoz egy adatkészletet az adat-előállítóban. Az adatkészlet egy folyamat egy tevékenységének bemenetét/kimenetét jelöli. |
| [Set-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Set-Azdatafactoryv2pipeline) | Folyamat létrehozása az adatelőállítóban. Egy folyamat egy vagy több olyan tevékenységet tartalmaz, amely egy bizonyos műveletet hajt végre. Ebben a folyamatban a másolási tevékenységek az egyik helyről egy másik helyre másolják az adatok egy Azure-Blob Storage. |
| [Meghívás – AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Invoke-Azdatafactoryv2pipeline) | Létrehoz egy futtatást a folyamathoz. Más szóval a folyamat futtatása folyamatban van. |
| [Get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-Azdatafactoryv2activityrun) | A folyamaton belüli tevékenység futtatásának (tevékenység futtatása) részleteinek beolvasása.
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |
|||

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/).

További Azure Data Factory PowerShell-szkriptek is találhatók a [Azure Data Factory PowerShell-mintákban](../samples-powershell.md).
