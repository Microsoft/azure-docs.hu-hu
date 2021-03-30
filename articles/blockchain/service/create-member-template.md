---
title: Azure Blockchain-szolgáltatásbeli tag létrehozása Azure Resource Manager sablon használatával
description: Ismerje meg, hogyan hozhat létre egy Azure Blockchain-szolgáltatási tagot Azure Resource Manager sablon használatával.
services: azure-resource-manager
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs, references_regions
ms.date: 09/16/2020
ms.openlocfilehash: e9893336f2e6633519853aceecc945ee6bf0bf4b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91292761"
---
# <a name="quickstart-create-an-azure-blockchain-service-member-using-an-arm-template"></a>Rövid útmutató: Azure Blockchain-szolgáltatásbeli tag létrehozása ARM-sablonnal

Ebben a rövid útmutatóban egy új blockchain-tagot és-konzorciumot helyez üzembe az Azure Blockchain Service-ben egy Azure Resource Manager-sablon (ARM-sablon) használatával. Az Azure Blockchain szolgáltatás tagja egy Blockchain-csomópont egy privát konzorcium Blockchain-hálózatában. Egy tag kiépítés esetén létrehozhat vagy csatlakozhat konzorciumi hálózathoz. Legalább egy tagnak szüksége van egy konzorciumi hálózatra. A résztvevők által igényelt blockchain-tagok száma a forgatókönyvtől függ. A konzorcium résztvevői rendelkezhetnek egy vagy több blockchain-taggal, vagy megoszthatnak más résztvevőkkel rendelkező tagokat is. A konzorciumokkal kapcsolatos további információkért lásd: [Azure Blockchain Service Consortium](consortium.md).

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-blockchain-asaservice%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/201-blockchain-asaservice/) közül származik.

:::code language="json" source="~/quickstart-templates/201-blockchain-asaservice/azuredeploy.json":::

A sablonban definiált Azure-erőforrások:

* [**Microsoft. Blockchain/blockchainMembers**](/azure/templates/microsoft.blockchain/blockchainmembers)

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Kattintson az alábbi hivatkozásra az Azure-ba való bejelentkezéshez és egy sablon megnyitásához.

    [![Üzembe helyezés az Azure-ban](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-blockchain-asaservice%2Fazuredeploy.json)

1. Az Azure Blockchain Service-tag beállításainak megadása.

    Beállítás | Leírás
    --------|------------
    Előfizetés | Válassza ki a szolgáltatásához használni kívánt Azure-előfizetést. Ha több előfizetéssel rendelkezik, válassza ki azt az előfizetést, amely részeként fizet az erőforrásért.
    Erőforráscsoport | Hozzon létre egy új erőforráscsoport-nevet, vagy válasszon ki egy meglévőt az előfizetésből.
    Region | Válasszon régiót az erőforráscsoport létrehozásához. A konzorcium összes tagjának ugyanazon a helyen kell lennie. A központi telepítés elérhető helyei a következők: *westeurope, eastus, southeastasia, westeurope, northeurope, westus2* és *japaneast*. Előfordulhat, hogy egyes régiókban nem érhetők el szolgáltatások. Az Azure Blockchain Data Manager a következő Azure-régiókban érhető el: USA keleti régiója és Nyugat-Európa.
    BC-tag neve | Válasszon egyedi nevet az Azure Blockchain szolgáltatás tagjának. A blockchain-tag neve csak kisbetűket és számokat tartalmazhat. Az első karakternek betűnek kell lennie. Az értéknek 2 – 20 karakter hosszúnak kell lennie.
    Konzorcium neve | Adjon meg egy egyedi nevet. A konzorciumokkal kapcsolatos további információkért lásd: [Azure Blockchain Service Consortium](consortium.md).
    Tag jelszava | A tag alapértelmezett tranzakciós csomópontjának jelszava. Az alapszintű hitelesítéshez használja a jelszót az blockchain-tag alapértelmezett tranzakciós csomópontjának nyilvános végponthoz való csatlakozáskor.
    Konzorcium-felügyeleti fiók jelszava | A konzorciumi fiók jelszava a tag számára létrehozott Ethereum-fiók titkos kulcsának titkosítására szolgál. A konzorciumok felügyeletére szolgál.
    SKU-rétegek | Az új szolgáltatás díjszabási szintje. A **standard** és az **alapszintű** csomagok közül választhat. A fogalmak fejlesztéséhez, teszteléséhez és bizonyításához *használja az alapszintű* csomagot. Használja a *standard* szintű üzemi szintű üzembe helyezést. A *standard* szintet is használhatja, ha Blockchain Data Manager használ, vagy nagy mennyiségű privát tranzakciót küld. Az alapszintű és a standard szintű díjszabás a tag létrehozása után történő módosítása nem támogatott.
    SKU neve | A csomópont-konfiguráció és az új szolgáltatás díja. Az alapszintű és a **S0** **B0** használata a standard szintű használatra.
    Hely | Válasszon egy helyet a tag létrehozásához. Alapértelmezés szerint az erőforráscsoport helye van használatban `[resourceGroup().location]` . A konzorcium összes tagjának ugyanazon a helyen kell lennie. A központi telepítés elérhető helyei a következők: *westeurope, eastus, southeastasia, westeurope, northeurope, westus2* és *japaneast*. Előfordulhat, hogy egyes régiókban nem érhetők el szolgáltatások. Az Azure Blockchain Data Manager a következő Azure-régiókban érhető el: USA keleti régiója és Nyugat-Európa.

1. Válassza a **felülvizsgálat + létrehozás** lehetőséget a sablon ellenőrzéséhez és üzembe helyezéséhez.

  A Azure Portal a sablon üzembe helyezéséhez használja a rendszer. Használhatja a Azure PowerShell, az Azure CLI és a REST API is. További információ az üzembe helyezési módszerekről: [sablonok üzembe helyezése](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

A Azure Portal használatával megtekintheti az üzembe helyezett Azure Blockchain-szolgáltatás tagjainak részleteit. A portálon nyissa meg az Azure Blockchain-szolgáltatás tagját tartalmazó erőforráscsoportot. Válassza ki a létrehozott blockchain-tagot.

![Üzembe helyezett Azure Blockchain-tagok áttekintése – részletek a Azure Portal](./media/create-member-template/deployed-member.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Használhatja a következő rövid útmutatóhoz vagy oktatóanyaghoz létrehozott blockchain tagot. Ha már nincs rá szükség, törölheti az erőforrásokat a gyors útmutatóhoz létrehozott erőforráscsoport törlésével.

Az erőforráscsoport törlése:

1. A Azure Portalban navigáljon az **erőforráscsoporthoz** a bal oldali navigációs ablaktáblán, és válassza ki a törölni kívánt erőforráscsoportot.
2. Válassza az **Erőforráscsoport törlése** elemet. A törlés ellenőrzéséhez írja be az erőforráscsoport nevét, és válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban üzembe helyezett egy Azure Blockchain-szolgáltatási tagot és egy új konzorciumot. Próbálja ki a következő rövid útmutatót a Ethereum készült Azure Blockchain Development Kit használatával az Azure Blockchain-szolgáltatáshoz való csatlakoztatáshoz.

> [!div class="nextstepaction"]
> [A Visual Studio Code használata az Azure Blockchain szolgáltatáshoz való kapcsolódáshoz](connect-vscode.md)