---
title: Apache Hadoop-fürtök létrehozása sablonok használatával – Azure HDInsight
description: Ismerje meg, hogyan hozhat létre fürtöket a HDInsight Resource Manager-sablonok használatával
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-azurecli
ms.date: 04/07/2020
ms.openlocfilehash: 43f9736ce902d4c195d6b07cfdf7011ba9ca0c2c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101702742"
---
# <a name="create-apache-hadoop-clusters-in-hdinsight-by-using-resource-manager-templates"></a>Apache Hadoop-fürtök létrehozása a HDInsight-ben Resource Manager-sablonok használatával

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Ebből a cikkből megtudhatja, hogyan hozhat létre Azure HDInsight-fürtöket [Azure Resource Manager-sablonok](../azure-resource-manager/templates/deploy-powershell.md)használatával. A fürt többi létrehozási eszközének és szolgáltatásának megismeréséhez kattintson az oldal tetején található Tab választóra. Lásd még: [fürt létrehozási módszerei](hdinsight-hadoop-provision-linux-clusters.md#cluster-setup-methods).

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="resource-manager-templates"></a>Resource Manager-sablonok

A Resource Manager-sablonok segítségével egyszerűen létrehozható az alkalmazáshoz tartozó alábbi erőforrások egyetlen, koordinált műveletben:

* HDInsight-fürtök és azok függő erőforrásai (például az alapértelmezett Storage-fiók).
* Egyéb erőforrások (például Azure SQL Database az [Apache Sqoop](https://sqoop.apache.org/)használatához).

A sablonban megadhatja az alkalmazáshoz szükséges erőforrásokat. A különböző környezetek bemeneti értékeihez is megadhat központi telepítési paramétereket. A sablon JSON-ből és kifejezésekből áll, amelyek használatával értékeket hozhat létre a telepítéshez.

Az [Azure Gyorsindítás sablonjain](https://azure.microsoft.com/resources/templates/?term=hdinsight)HDInsight-mintákat talál. A platformfüggetlen [Visual Studio Code](https://code.visualstudio.com/#alt-downloads) -ot használhatja a [Resource Manager bővítménnyel](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) vagy egy szövegszerkesztővel, hogy a sablont egy fájlba mentse a munkaállomáson.

A Resource Manager-sablonokkal kapcsolatos további információkért tekintse meg a következő cikkeket és példákat:

* [Azure Resource Manager sablonok szerzője](../azure-resource-manager/templates/template-syntax.md)
* [Alkalmazás üzembe helyezése Azure Resource Manager-sablonokkal](../azure-resource-manager/templates/deploy-powershell.md)
* [Microsoft. HDInsight/fürtök](/azure/templates/microsoft.hdinsight/allversions) sablonjának referenciája
* [Azure Gyorsindítás sablonok](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Hdinsight&pageNumber=1&sort=Popular)

## <a name="generate-templates"></a>Sablonok előállítása

A Resource Manager lehetővé teszi, hogy különböző eszközök használatával exportáljon egy Resource Manager-sablont az előfizetése meglévő erőforrásaiból. Az így létrehozott sablon használatával megismerheti a sablonok szintaxisát, illetve igény szerint automatizálhatja a megoldás újbóli telepítését. További információ: [sablonok exportálása](../azure-resource-manager/templates/export-template-portal.md).

## <a name="deploy-using-the-portal"></a>Üzembe helyezés a portál használatával

A Azure Portal használatával Resource Manager-sablonokat is üzembe helyezhet. További információ: [erőforrások központi telepítése egyéni sablonból](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).

## <a name="deploy-using-powershell"></a>Üzembe helyezés a PowerShell használatával

Azure PowerShell használatával üzembe helyezhet egy Resource Manager-sablont. További információ: [erőforrások üzembe helyezése Resource Manager-sablonokkal és Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) , valamint [saját Resource Manager-sablon üzembe helyezése sas-jogkivonat és Azure PowerShell segítségével](../azure-resource-manager/templates/secure-template-with-sas-token.md).

## <a name="deploy-using-azure-cli"></a>Üzembe helyezés az Azure CLI-vel

Resource Manager-sablont az Azure CLI használatával helyezhet üzembe. További információ: [erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure CLI](../azure-resource-manager/templates/deploy-cli.md) -vel, valamint [saját Resource Manager-sablon üzembe helyezése sas-JOGKIVONAT és Azure CLI használatával](../azure-resource-manager/templates/secure-template-with-sas-token.md).

## <a name="deploy-using-the-rest-api"></a>Üzembe helyezés a REST API használatával

REST API használatával üzembe helyezhet egy Resource Manager-sablont. További információ: [erőforrások üzembe helyezése Resource Manager-sablonokkal és Resource Manager-Rest APIokkal](../azure-resource-manager/templates/deploy-rest.md).

## <a name="deploy-with-visual-studio"></a>Üzembe helyezés a Visual Studióval

 A Visual Studióval hozzon létre egy erőforráscsoport-projektet, és telepítse azt az Azure-ba a felhasználói felületen keresztül. Kiválaszthatja, hogy milyen típusú erőforrásokat kíván felvenni a projektbe. Ezeket az erőforrásokat a rendszer automatikusan hozzáadja a Resource Manager-sablonhoz. A projekt egy PowerShell-szkriptet is biztosít a sablon üzembe helyezéséhez.

A Visual Studio és az erőforráscsoportok együttes használatának bemutatása: Azure- [erőforráscsoportok létrehozása és üzembe helyezése a Visual Studióval](../azure-resource-manager/templates/create-visual-studio-deployment-project.md).

## <a name="troubleshoot"></a>Hibaelhárítás

Ha problémába ütközik a HDInsight-fürtök létrehozása során, tekintse meg [a hozzáférés-vezérlésre vonatkozó követelményeket](hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan hozhat létre HDInsight-fürtöt. További tudnivalókért olvassa el a következő cikket:

* További HDInsight kapcsolódó sablonok: [Azure Gyorsindítás sablonok](https://azure.microsoft.com/resources/templates/?term=hdinsight).
* Az erőforrások .NET-ügyfél-függvénytáron keresztüli üzembe helyezésére példa: [erőforrások telepítése .net-kódtárak és sablon használatával](/previous-versions/azure/virtual-machines/windows/csharp-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Az alkalmazások üzembe helyezésének részletes ismertetését lásd: az Azure- [ban előre definiált és üzembe helyezett szolgáltatások üzembe helyezése](../app-service/deploy-complex-application-predictably.md).
* Útmutató a megoldások különböző környezetekben történő telepítéséhez: [Fejlesztési és tesztelési környezetek a Microsoft Azure eszközben](../devtest-labs/devtest-lab-overview.md).
* A Azure Resource Manager sablon fejezeteiről a [sablonok készítése](../azure-resource-manager/templates/template-syntax.md)című témakörben olvashat bővebben.
* A Azure Resource Manager sablonban használható függvények listáját a [sablon függvények](../azure-resource-manager/templates/template-functions.md)című részben tekintheti meg.