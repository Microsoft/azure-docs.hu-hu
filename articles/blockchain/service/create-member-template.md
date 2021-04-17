---
title: Hozzon létre Azure Blockchain Service tag a sablon Azure Resource Manager használatával
description: Megtudhatja, hogyan hozhat létre Azure Blockchain Service tagokat a Azure Resource Manager használatával.
services: azure-resource-manager
ms.date: 09/16/2020
ms.topic: quickstart
ms.service: azure-resource-manager
ms.custom:
- subject-armqs
- references_regions
- mode-arm
ms.openlocfilehash: c49e45ae84d58d62460d493887e2a4e78e40ba32
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536302"
---
# <a name="quickstart-create-an-azure-blockchain-service-member-using-an-arm-template"></a>Rövid útmutató: Azure Blockchain Service tag létrehozása ARM-sablonnal

Ebben a rövid útmutatóban egy új blokklánctagot és konzorciumot helyez üzembe a Azure Blockchain Service egy Azure Resource Manager (ARM-sablon) használatával. A Azure Blockchain Service tagja egy blokklánccsomópont egy privát konzorciumi blokklánchálózatban. Tagok kiépítésekor konzorciumi hálózatot hozhat létre vagy csatlakozhat hozzájuk. Egy konzorciumi hálózatnak legalább egy tagra van szüksége. A résztvevők számára szükséges blokklánctagok száma a forgatókönyvtől függ. A konzorciumi résztvevők egy vagy több blokklánctaggal is lehetnek, vagy megoszthatnak tagokat más résztvevőkkel. További információ a consortia projektről: [Azure Blockchain Service konzorcium.](consortium.md)

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-blockchain-asaservice%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/201-blockchain-asaservice/) közül származik.

:::code language="json" source="~/quickstart-templates/201-blockchain-asaservice/azuredeploy.json":::

A sablonban definiált Azure-erőforrások:

* [**Microsoft.Blockchain/blockchainMembers**](/azure/templates/microsoft.blockchain/blockchainmembers)

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Kattintson az alábbi hivatkozásra az Azure-ba való bejelentkezéshez és egy sablon megnyitásához.

    [![Üzembe helyezés az Azure-ban](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-blockchain-asaservice%2Fazuredeploy.json)

1. Adja meg a Azure Blockchain Service beállításait.

    Beállítás | Leírás
    --------|------------
    Előfizetés | Válassza ki a szolgáltatáshoz használni kívánt Azure-előfizetést. Ha több előfizetéssel rendelkezik, válassza ki azt az előfizetést, amely részeként fizet az erőforrásért.
    Erőforráscsoport | Hozzon létre egy új erőforráscsoport-nevet, vagy válasszon ki egy meglévőt az előfizetéséből.
    Region | Válasszon ki egy régiót az erőforráscsoport létrehozásához. A konzorcium minden tagjának ugyanazon a helyen kell lennie. Az üzembe helyezéshez elérhető helyek: *westeurope, eastus, southeastasia, westeurope, northeurope, westus2* és *japaneast.* Előfordulhat, hogy egyes régiókban a funkciók nem érhetők el. Azure Blockchain Data Manager azure-régiókban érhető el: az USA keleti régiója és Nyugat-Európa.
    Bc-tag neve | Válasszon egy egyedi nevet a Azure Blockchain Service tagnak. A blokklánctag neve csak kisbetűket és számokat tartalmazhat. Az első karakternek betűnek kell lennie. Az érték 2–20 karakter hosszúságú lehet.
    Konzorcium neve | Adjon meg egy egyedi nevet. További információ a consortia projektről: Azure Blockchain Service [konzorcium.](consortium.md)
    Tag jelszava | A tag alapértelmezett tranzakciós csomópontjának jelszava. Alapszintű hitelesítéshez használja a jelszót, amikor a blokklánctag alapértelmezett tranzakciós csomópontjának nyilvános végpontjára csatlakozik.
    Konzorciumkezelési fiók jelszava | A konzorciumfiók jelszava a tag számára létrehozott Ethereum-fiók titkos kulcsának titkosítására használatos. A konzorciumkezeléshez használatos.
    Termékváltozat szint | Az új szolgáltatás tarifacsomagja. A Standard **és** az **Alapszintű csomagok közül** választhat. Az *Alapszintű csomag* fejlesztési, tesztelési és koncepció igazolási célokra használható. Éles *környezetben való* üzembe helyezéshez használja a Standard szintet. Akkor is használja a *Standard* szintet, ha Blockchain Data Manager nagy mennyiségű privát tranzakciót küld. A tarifacsomag alapszintű és standard közötti módosítása a tag létrehozása után nem támogatott.
    Termékváltozat neve | Az új szolgáltatás csomópont-konfigurációja és költsége. Használja **a B0-t** az Alapszintű, **az S0-t pedig** a Standardhoz.
    Hely | Válasszon egy helyet a tag létrehozásához. Alapértelmezés szerint az erőforráscsoport helye lesz `[resourceGroup().location]` használatban. A konzorcium minden tagjának ugyanazon a helyen kell lennie. Az üzembe helyezéshez elérhető helyek: *westeurope, eastus, southeastasia, westeurope, northeurope, westus2* és *japaneast.* Előfordulhat, hogy egyes régiókban a funkciók nem érhetők el. Azure Blockchain Data Manager azure-régiókban érhető el: az USA keleti régiója és Nyugat-Európa.

1. Válassza **az Áttekintés + létrehozás lehetőséget** a sablon ellenőrzéséhez és üzembe helyezéséhez.

  A Azure Portal itt a sablon üzembe helyezésére használható. Használhatja az Azure PowerShell, az Azure CLI-t és a REST API. További információ az egyéb üzembe helyezési módszerekről: [Sablonok üzembe helyezése.](../../azure-resource-manager/templates/deploy-powershell.md)

## <a name="review-deployed-resources"></a>Az üzembe helyezett erőforrások áttekintése

Az üzembe helyezett Azure Portal megtekintéséhez használhatja a Azure Blockchain Service adatait. A portálon a saját tagját tartalmazó erőforráscsoportot Azure Blockchain Service meg. Válassza ki a létrehozott blokklánctagot.

![Az üzembe Azure Blockchain tag áttekintésének részletei a Azure Portal](./media/create-member-template/deployed-member.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Használhatja a következő rövid útmutatóhoz vagy oktatóanyaghoz létrehozott blokklánctagot. Ha már nincs rá szükség, törölheti az erőforrásokat a rövid útmutatóhoz létrehozott erőforráscsoport törlésével.

Az erőforráscsoport törlése:

1. A Azure Portal navigáljon az **Erőforráscsoport** elemre a bal oldali navigációs panelen, és válassza ki a törölni kívánt erőforráscsoportot.
2. Válassza az **Erőforráscsoport törlése** elemet. Ellenőrizze a törlést az erőforráscsoport nevének megadásával, majd válassza a **Törlés lehetőséget.**

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban üzembe helyezett egy Azure Blockchain Service egy új konzorciumot. Próbálja ki a következő rövid útmutatót, hogy az Ethereumhoz Azure Blockchain fejlesztői készlettel csatoljon egy Azure Blockchain Service taghoz.

> [!div class="nextstepaction"]
> [Csatlakozás Visual Studio kód használatával Azure Blockchain Service](connect-vscode.md)
