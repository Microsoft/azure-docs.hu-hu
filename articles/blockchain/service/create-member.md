---
title: Új Azure Blockchain Service létrehozása – Azure Portal
description: Hozzon létre egy Azure Blockchain Service egy blokklánc konzorciumhoz a Azure Portal.
ms.reviewer: ravastra
ms.date: 07/16/2020
ms.topic: quickstart
ms.custom:
- references_regions
- mode-portal
ms.openlocfilehash: 9fe8b7856a99930c16b4173e432fa0b30642582c
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536202"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-the-azure-portal"></a>Gyorsútmutató: Azure Blockchain Service-blokklánctag létrehozása az Azure Portal használatával

Ebben a rövid útmutatóban egy új blokklánctagot és konzorciumot helyez üzembe a Azure Blockchain Service a Azure Portal.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Nincsenek.

## <a name="create-a-blockchain-member"></a>Blokklánctag létrehozása

A Azure Blockchain Service tagja egy blokklánccsomópont egy privát konzorciumi blokklánchálózatban. Tagok kiépítésekor konzorciumi hálózatot hozhat létre vagy csatlakozhat hozzájuk. Egy konzorciumhálózatnak legalább egy tagra van szüksége. A résztvevők számára szükséges blokklánctagok száma a forgatókönyvtől függ. A konzorciumi résztvevők egy vagy több blokklánctaggal is lehetnek, vagy megoszthatnak tagokat más résztvevőkkel. További információ a konzorciumról: Azure Blockchain Service [konzorcium.](consortium.md)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.
1. Válassza **a Blockchain**  >  **Azure Blockchain Service (előzetes verzió) lehetőséget.**

    ![Szolgáltatás létrehozása](./media/create-member/create-member.png)

    Beállítás | Leírás
    --------|------------
    Előfizetés | Válassza ki a szolgáltatáshoz használni kívánt Azure-előfizetést. Ha több előfizetéssel rendelkezik, válassza ki azt az előfizetést, amely részeként fizet az erőforrásért.
    Erőforráscsoport | Hozzon létre egy új erőforráscsoport-nevet, vagy válasszon ki egy meglévőt az előfizetéséből.
    Region | Válasszon ki egy régiót a tag létrehozásához. A konzorcium minden tagjának ugyanazon a helyen kell lennie. Előfordulhat, hogy egyes régiókban a funkciók nem érhetők el. Azure Blockchain Data Manager azure-régiókban érhető el: az USA keleti régiója és Nyugat-Európa.
    Protokoll | Jelenleg a Azure Blockchain Service előzetes verziója támogatja a Kvórum protokollt.
    Konzorcium | Új konzorciumhoz adjon meg egy egyedi nevet. Ha meghíváson keresztül csatlakozik egy konzorciumhoz, válassza ki a konzorciumot, amelyhez csatlakozik. További információ a consortia projektről: [Azure Blockchain Service konzorcium.](consortium.md)
    Name | Válasszon egyedi nevet a Azure Blockchain Service tagnak. A blokklánctag neve csak kisbetűket és számokat tartalmazhat. Az első karakternek betűnek kell lennie. Az érték 2–20 karakter hosszúságú lehet.
    Tagfiók jelszava | A tagfiók jelszava a tag számára létrehozott Ethereum-fiók titkos kulcsának titkosítására használatos. A konzorciumkezeléshez a tagfiókot és a tagfiók jelszavát használhatja.
    Díjszabás | Az új szolgáltatás csomópont-konfigurációja és költsége. A Change **(Módosítás)** hivatkozásra kattintva választhat **a Standard és** az **Alapszintű** csomagok közül. Az *Alapszintű csomag* fejlesztési, tesztelési és koncepció igazolási célokra használható. Éles *környezetben való* üzembe helyezéshez használja a Standard szintet. Akkor is használja a *Standard* szintet, ha Blockchain Data Manager nagy mennyiségű privát tranzakciót küld. A tarifacsomag alapszintű és standard közötti módosítása a tag létrehozása után nem támogatott.
    Csomópont jelszava | A tag alapértelmezett tranzakciós csomópontjának jelszava. Alapszintű hitelesítéshez használja a jelszót, amikor a blokklánctag alapértelmezett tranzakciós csomópontjának nyilvános végpontjára csatlakozik.

1. A **beállítások érvényesítéséhez válassza** az Áttekintés + létrehozás lehetőséget. A **szolgáltatás létrehozásához** válassza a Létrehozás lehetőséget. A kiépítés körülbelül 10 percet vesz igénybe.
1. Válassza **az eszköztár** Értesítések gombját az üzembe helyezési folyamat figyelése érdekében.
1. Az üzembe helyezés után keresse meg a blokklánctagot.

Válassza **az Áttekintés** lehetőséget, és megtekintheti a szolgáltatás alapvető adatait, beleértve a RootContract-címet és a tagfiókot.

![Blokklánctagok áttekintése](./media/create-member/overview.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Használhatja a következő rövid útmutatóhoz vagy oktatóanyaghoz létrehozott tagot. Ha már nincs rá szükség, törölheti az erőforrásokat a rövid útmutatóhoz `myResourceGroup` létrehozott erőforráscsoport törlésével.

Az erőforráscsoport törlése:

1. A Azure Portal lépjen az Erőforráscsoport elemre **a** bal oldali navigációs panelen, és válassza ki a törölni kívánt erőforráscsoportot.
2. Válassza az **Erőforráscsoport törlése** elemet. Ellenőrizze a törlést az erőforráscsoport nevének megadásával, majd válassza a **Törlés lehetőséget.**

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban üzembe helyezett egy Azure Blockchain Service egy új konzorciumot. Próbálja ki a következő rövid útmutatót, hogy az Ethereumhoz Azure Blockchain fejlesztői készlettel csatoljon egy Azure Blockchain Service taghoz.

> [!div class="nextstepaction"]
> [Csatlakozás Visual Studio kód használatával Azure Blockchain Service](connect-vscode.md)
