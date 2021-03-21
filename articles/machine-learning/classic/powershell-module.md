---
title: 'ML Studio (klasszikus): PowerShell-modulok – Azure'
description: A PowerShell használatával Azure Machine Learning Studio (klasszikus) munkaterületeket, kísérleteket, webszolgáltatásokat és egyebeket hozhat létre és kezelhet.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 04/25/2019
ms.openlocfilehash: 684299d61ba6e9e27e16a162c9f226a7ea3b5f58
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100518011"
---
# <a name="powershell-modules-for-azure-machine-learning-studio-classic"></a>PowerShell-modulok Azure Machine Learning Studio (klasszikus)

**a következőkre vonatkozik:** ![ A következőre vonatkozik:. ](../../../includes/media/aml-applies-to-skus/yes.png) A Machine Learning Studio (klasszikus) ![ nem vonatkozik a következőre:.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


A PowerShell-modulok használatával programozott módon kezelheti a Studio (klasszikus) erőforrásait és eszközeit, például munkaterületeket, adatkészleteket és webszolgáltatásokat.

A Studio (klasszikus) erőforrásait három PowerShell-modul használatával használhatja:

* [Azure PowerShell az](#az-rm) az 2018-ben megjelent, a AzureRM összes funkcióját tartalmazza, bár különböző parancsmag-nevekkel
* A 2016-ben kiadott [AzureRM](#az-rm) , a PowerShell helyébe
* A [Azure Machine learning PowerShell klasszikus](#classic) változata 2016-ben megjelent

Habár ezek a PowerShell-modulok hasonlóságot mutatnak, mindegyiket bizonyos forgatókönyvekhez tervezték. Ez a cikk a PowerShell-modulok közötti különbségeket ismerteti, és segít eldönteni, hogy melyiket kell választania.  

Az alábbi [támogatási táblázatban](#support-table) megtekintheti, hogy az egyes modulok milyen erőforrásokat támogatnak. 

## <a name="azure-powershell-az-and-azurerm"></a><a name="az-rm"></a> Azure PowerShell az és AzureRM

Az mostantól az Azure-hoz való interakcióhoz készült PowerShell-modul, amely a AzureRM összes korábbi funkcióját tartalmazza. A AzureRM továbbra is megkapja a hibajavításokat, de nem fog új parancsmagokat vagy szolgáltatásokat kapni.  Az és a AzureRM egyaránt felügyeli a **Azure Resource Manager** üzembe helyezési modell használatával üzembe helyezett megoldásokat. Ezek az erőforrások például a Studio (klasszikus) munkaterületek és a Studio (klasszikus) "új" webszolgáltatások. 

A klasszikus PowerShell-t az az vagy a AzureRM együtt is telepítheti, hogy az "új" és a "klasszikus" típusú erőforrásokra is vonatkozzon. Azonban az az és a AzureRM nem ajánlott egyszerre telepíteni. Az az és a AzureRM közötti döntéshez a Microsoft az az összes jövőbeli üzembe helyezését javasolja.  További információ az az és a AzureRM és az áttelepítési útvonalról a [Azure PowerShell](/powershell/azure/new-azureps-module-az)az az.

Az az használatával való ismerkedéshez kövesse az [Azure telepítési utasításait az az](/powershell/azure/install-az-ps).

## <a name="powershell-classic"></a><a name="classic"></a> Klasszikus PowerShell

A Studio (klasszikus) [PowerShell klasszikus modul](https://aka.ms/amlps) lehetővé teszi a **klasszikus üzemi modell** használatával üzembe helyezett erőforrások kezelését. Ezek az erőforrások a Studio (klasszikus) felhasználói eszközök, a "klasszikus" webszolgáltatások és a "klasszikus" webszolgáltatás-végpontok közé tartoznak.

A Microsoft azonban azt javasolja, hogy a Resource Manager-alapú üzemi modellt minden jövőbeli erőforráshoz használja az erőforrások üzembe helyezésének és kezelésének egyszerűsítése érdekében. Ha többet szeretne megtudni az üzembe helyezési modellekről, tekintse meg a Azure Resource Manager és a [klasszikus üzembe helyezési](../../azure-resource-manager/management/deployment-models.md) cikket.

A klasszikus PowerShell megkezdéséhez töltse le a [kiadási csomagot](https://github.com/hning86/azuremlps/releases) a githubról, és kövesse a [telepítésre vonatkozó utasításokat](https://github.com/hning86/azuremlps/blob/master/README.md). Az útmutató ismerteti, hogyan lehet feloldani a letöltött/kibontott DLL-t, majd importálni a PowerShell-környezetbe.

A klasszikus PowerShell-t az az vagy a AzureRM együtt is telepítheti, hogy az "új" és a "klasszikus" típusú erőforrásokra is vonatkozzon.

## <a name="powershell-support-table"></a><a name="support-table"></a> PowerShell-támogatási tábla


| Feladat | **Az** |  **Klasszikus PowerShell** |
| --- | --- | --- |
| Munkaterületek létrehozása/törlése | [Resource Manager-sablonok](./deploy-with-resource-manager-template.md) |  |
| Munkaterület-kötelezettségvállalási tervek kezelése | [Új – AzMlCommitmentPlan](/powershell/module/az.machinelearning/new-azmlcommitmentplan) | |
| Munkaterület-felhasználók kezelése |  | [Add-AmlWorkspaceUsers](https://github.com/hning86/azuremlps#add-amlworkspaceusers)|
| Webszolgáltatások kezelése | [Új – AzMlWebService](/powershell/module/az.machinelearning/new-azmlwebservice) <br>("új" webszolgáltatások)| [Új – AmlWebService](https://github.com/hning86/azuremlps#manage-classic-web-service) <br>("klasszikus" webszolgáltatások) |
| Webszolgáltatás-végpontok/kulcsok kezelése |  [Get-AzMlWebServiceKey](/powershell/module/az.machinelearning/get-azmlwebservicekey)|  [Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#manage-classic-web-servcie-endpoint)|
| Felhasználói adatkészletek/betanított modellek kezelése| | [Get-AmlDataset](https://github.com/hning86/azuremlps#manage-user-assets-dataset-trained-model-transform) |
| Felhasználói kísérletek kezelése |  | [Start – AmlExperiment](https://github.com/hning86/azuremlps#manage-experiment) |
| Egyéni modulok kezelése | | [Új – AmlCustomModule](https://github.com/hning86/azuremlps#manage-custom-module) |


## <a name="next-steps"></a>Következő lépések
Tanulmányozza a PowerShell-modul teljes dokumentációját:
* [Klasszikus PowerShell](https://aka.ms/amlps)
* [Azure PowerShell Az](/powershell/module/az.machinelearning/#machine_learning)