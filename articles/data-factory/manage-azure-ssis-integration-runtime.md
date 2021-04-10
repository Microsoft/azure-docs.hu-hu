---
title: Az Azure-SSIS Integration Runtime újrakonfigurálása
description: Megtudhatja, hogyan konfigurálhat egy Azure-SSIS integrációs modult Azure Data Factory a már üzembe helyezése után.
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/03/2020
author: swinarko
ms.author: sawinark
ms.openlocfilehash: 724411dc12654aec1614230c943923062b334cd2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100370682"
---
# <a name="reconfigure-the-azure-ssis-integration-runtime"></a>Az Azure-SSIS Integration Runtime újrakonfigurálása

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ez a cikk azt ismerteti, hogyan lehet újrakonfigurálni egy meglévő Azure-SSIS integrációs modult. Az Azure-SSIS Integration Runtime (IR) Azure Data Factory-ben való létrehozásáról az [Azure-SSIS Integration Runtime létrehozása](create-azure-ssis-integration-runtime.md)című témakörben olvashat.  

## <a name="data-factory-ui"></a>A Data Factory felhasználói felülete 
A Azure-SSIS IR leállításához, szerkesztéséhez/újrakonfigurálásához vagy törléséhez Data Factory felhasználói felületet használhat. 

1. Nyissa meg Data Factory felhasználói felületét úgy, hogy kijelöli az adatelőállító kezdőlapjának **szerző & figyelése** csempéjét.
2. Válassza a **felügyeleti központ** **Kezdőlap**, **Szerkesztés** és **figyelés** csomópontokat a **kapcsolatok** ablaktábla megjelenítéséhez.

### <a name="to-reconfigure-an-azure-ssis-ir"></a>Azure-SSIS IR újrakonfigurálása
A **Manage** hub **kapcsolatok** ablaktábláján váltson az **Integration Runtimes (integrációs** modulok) lapra, és válassza a **frissítés** lehetőséget. 

   ![Kapcsolatok ablaktábla](./media/tutorial-create-azure-ssis-runtime-portal/connections-pane.png)

   A Azure-SSIS IR szerkesztéséhez/újrakonfigurálásához válassza ki a nevét. Kiválaszthatja a megfelelő gombokat a Azure-SSIS IR figyelésére/indítására/leállítására/törlésére, automatikusan létrehoz egy ADF-folyamatot a SSIS-Azure-SSIS IR csomag végrehajtásával, és megtekintheti a Azure-SSIS IR JSON-kódját/hasznos adatait.  A Azure-SSIS IR szerkesztése/törlése csak a leállítása után hajtható végre.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az Azure-SSIS integrációs modul egy példányának üzembe helyezése és elindítása után a `Stop`  -  `Set`  -  `Start` PowerShell-parancsmagok sorozatának egymás utáni futtatásával újrakonfigurálhatja. A következő PowerShell-szkript például az Azure-SSIS Integration Runtime-példányhoz lefoglalt csomópontok számát öt értékre módosítja.

### <a name="reconfigure-an-azure-ssis-ir"></a>Azure-SSIS IR újrakonfigurálása

1. Először állítsa le az Azure-SSIS integrációs modult a [stop-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/stop-Azdatafactoryv2integrationruntime) parancsmag használatával. Ez a parancs kibocsátja az összes csomópontját, és leállítja a számlázást.

   ```powershell
   Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 
   ```
2. Ezután konfigurálja újra a Azure-SSIS IR a [set-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/set-Azdatafactoryv2integrationruntime) parancsmag használatával. Az alábbi példában szereplő parancs egy Azure-SSIS integrációs modult öt csomópontra méretezi.

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -NodeCount 5
   ```  
3. Ezután indítsa el az Azure-SSIS integrációs modult a [Start-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/start-Azdatafactoryv2integrationruntime) parancsmag használatával. Ez a parancs lefoglalja az összes csomópontját a SSIS-csomagok futtatásához.   

   ```powershell
   Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName
   ```

### <a name="delete-an-azure-ssis-ir"></a>Azure-SSIS IR törlése
1. Először sorolja fel az összes meglévő Azure SSIS IRs-t az adat-előállítóban.

   ```powershell
   Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName -Status
   ```
2. Ezt követően állítsa le az összes meglévő Azure SSIS IRs-t az adatai-előállítóban.

   ```powershell
   Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
   ```
3. Ezután távolítsa el az összes meglévő Azure SSIS IRs-t az adatai-előállítóban egyenként.

   ```powershell
   Remove-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
   ```
4. Végül távolítsa el az adatgyárat.

   ```powershell
   Remove-AzDataFactoryV2 -Name $DataFactoryName -ResourceGroupName $ResourceGroupName -Force
   ```
5. Ha létrehozott egy új erőforráscsoportot, távolítsa el az erőforráscsoportot.

   ```powershell
   Remove-AzResourceGroup -Name $ResourceGroupName -Force 
   ```

## <a name="next-steps"></a>Következő lépések
Az Azure-SSIS futtatókörnyezettel kapcsolatos további információkért tekintse meg a következő témaköröket: 

- [Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Ez a cikk az integrációs modulokkal kapcsolatos általános információkat tartalmaz, beleértve a Azure-SSIS IR. 
- [Oktatóanyag: SSIS-csomagok üzembe helyezése az Azure-ban](./tutorial-deploy-ssis-packages-azure.md). Ez a cikk részletes útmutatást nyújt egy Azure-SSIS IR létrehozásához, és a Azure SQL Database használatával futtatja az SSIS-katalógust. 
- [Útmutató: Azure-SSIS integrációs modul létrehozása](create-azure-ssis-integration-runtime.md). Ez a cikk az oktatóanyagon alapul, és útmutatást nyújt az Azure SQL felügyelt példányának használatáról és az IR virtuális hálózathoz való csatlakoztatásáról. 
- [Azure-SSIS integrációs modul csatlakoztatása virtuális hálózathoz](join-azure-ssis-integration-runtime-virtual-network.md). Ez a cikk egy Azure-SSIS integrációs modul Azure virtuális hálózathoz való csatlakoztatásával kapcsolatos elméleti információkat tartalmaz. Azt is ismerteti, hogyan használható az Azure Portal a virtuális hálózat oly módon való konfigurálására, hogy az Azure-SSIS integrációs modul csatlakozhasson ahhoz. 
- [Azure-SSIS integrációs modul monitorozása](monitor-integration-runtime.md#azure-ssis-integration-runtime). Ez a cikk bemutatja, hogyan kérhet le információkat egy Azure-SSIS integrációs modulról, és ismerteti a visszaadott információkban található állapotok leírását.