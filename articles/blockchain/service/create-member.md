---
title: Azure Blockchain szolgáltatásbeli tag létrehozása – Azure Portal
description: Hozzon létre egy Azure Blockchain-szolgáltatási tagot egy Blockchain Consortium számára a Azure Portal használatával.
ms.date: 07/16/2020
ms.topic: quickstart
ms.reviewer: ravastra
ms.custom: references_regions
ms.openlocfilehash: 8a7f5aaea56f34e8107664ab786a14b59cd1cb7d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91292728"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-the-azure-portal"></a>Rövid útmutató: Azure Blockchain Service Blockchain-tag létrehozása a Azure Portal használatával

Ebben a rövid útmutatóban egy új blockchain-tagot és-konzorciumot helyez üzembe az Azure Blockchain Service-ben a Azure Portal használatával.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Nincsenek.

## <a name="create-a-blockchain-member"></a>Blockchain-tag létrehozása

Az Azure Blockchain szolgáltatás tagja egy Blockchain-csomópont egy privát konzorcium Blockchain-hálózatában. Egy tag kiépítés esetén létrehozhat vagy csatlakozhat konzorciumi hálózathoz. Legalább egy tagnak szüksége van egy konzorciumi hálózatra. A résztvevők által igényelt blockchain-tagok száma a forgatókönyvtől függ. A konzorcium résztvevői rendelkezhetnek egy vagy több blockchain-taggal, vagy megoszthatnak más résztvevőkkel rendelkező tagokat is. A konzorciumokkal kapcsolatos további információkért lásd: [Azure Blockchain Service Consortium](consortium.md).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.
1. Válassza a **Blockchain**  >  **Azure Blockchain szolgáltatás (előzetes verzió)** lehetőséget.

    ![Szolgáltatás létrehozása](./media/create-member/create-member.png)

    Beállítás | Leírás
    --------|------------
    Előfizetés | Válassza ki a szolgáltatásához használni kívánt Azure-előfizetést. Ha több előfizetéssel rendelkezik, válassza ki azt az előfizetést, amely részeként fizet az erőforrásért.
    Erőforráscsoport | Hozzon létre egy új erőforráscsoport-nevet, vagy válasszon ki egy meglévőt az előfizetésből.
    Region | Válasszon ki egy régiót a tag létrehozásához. A konzorcium összes tagjának ugyanazon a helyen kell lennie. Előfordulhat, hogy egyes régiókban nem érhetők el szolgáltatások. Az Azure Blockchain Data Manager a következő Azure-régiókban érhető el: USA keleti régiója és Nyugat-Európa.
    Protokoll | Jelenleg az Azure Blockchain Service előzetes verziója támogatja a kvórum protokollt.
    Konzorcium | Új konzorcium esetén adjon meg egy egyedi nevet. Ha a konzorciumot egy meghívás használatával csatlakoztatja, válassza ki azt a konzorciumot, amelyhez csatlakozik. A konzorciumokkal kapcsolatos további információkért lásd: [Azure Blockchain Service Consortium](consortium.md).
    Name | Válasszon egyedi nevet az Azure Blockchain szolgáltatás tagjának. A blockchain-tag neve csak kisbetűket és számokat tartalmazhat. Az első karakternek betűnek kell lennie. Az értéknek 2 – 20 karakter hosszúnak kell lennie.
    Tag fiókjának jelszava | A tag fiók jelszava a tag számára létrehozott Ethereum-fiók titkos kulcsának titkosítására szolgál. A tag fiókja és a fiók jelszava a konzorciumok kezeléséhez.
    Díjszabás | A csomópont-konfiguráció és az új szolgáltatás díja. Válassza a **módosítás** hivatkozást a **standard** **és az alapszintű** csomagok választásához. A fogalmak fejlesztéséhez, teszteléséhez és bizonyításához *használja az alapszintű* csomagot. Használja a *standard* szintű üzemi szintű üzembe helyezést. A *standard* szintet is használhatja, ha Blockchain Data Manager használ, vagy nagy mennyiségű privát tranzakciót küld. Az alapszintű és a standard szintű díjszabás a tag létrehozása után történő módosítása nem támogatott.
    Csomópont jelszava | A tag alapértelmezett tranzakciós csomópontjának jelszava. Az alapszintű hitelesítéshez használja a jelszót az blockchain-tag alapértelmezett tranzakciós csomópontjának nyilvános végponthoz való csatlakozáskor.

1. Válassza a **felülvizsgálat + létrehozás** lehetőséget a beállítások érvényesítéséhez. Válassza a **Létrehozás** lehetőséget a szolgáltatás kiépítéséhez. A kiépítés körülbelül 10 percet vesz igénybe.
1. A telepítési folyamat figyeléséhez kattintson az **értesítések** elemre az eszköztáron.
1. Az üzembe helyezés után navigáljon a blockchain-taghoz.

Az **Áttekintés** lehetőség kiválasztásával megtekintheti a szolgáltatás alapvető adatait, beleértve a RootContract-címeket és a tagsági fiókot is.

![Blockchain-tagok áttekintése](./media/create-member/overview.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A következő rövid útmutatóhoz vagy oktatóanyaghoz létrehozott tagot használhatja. Ha már nincs rá szükség, törölheti az erőforrásokat a gyors útmutatóhoz `myResourceGroup` létrehozott erőforráscsoport törlésével.

Az erőforráscsoport törlése:

1. A Azure Portalban navigáljon az **erőforráscsoporthoz** a bal oldali navigációs ablaktáblán, és válassza ki a törölni kívánt erőforráscsoportot.
2. Válassza az **Erőforráscsoport törlése** elemet. A törlés ellenőrzéséhez írja be az erőforráscsoport nevét, és válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban üzembe helyezett egy Azure Blockchain-szolgáltatási tagot és egy új konzorciumot. Próbálja ki a következő rövid útmutatót a Ethereum készült Azure Blockchain Development Kit használatával az Azure Blockchain-szolgáltatáshoz való csatlakoztatáshoz.

> [!div class="nextstepaction"]
> [A Visual Studio Code használata az Azure Blockchain szolgáltatáshoz való kapcsolódáshoz](connect-vscode.md)
