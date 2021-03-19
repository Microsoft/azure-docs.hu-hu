---
title: Alkalmazások használata az Azure Blockchain Workbenchben
description: Oktatóanyag az alkalmazás-szerződések használatáról az Azure Blockchain Workbench előzetes verziójában.
ms.date: 10/14/2019
ms.topic: tutorial
ms.reviewer: brendal
ms.openlocfilehash: 5761bf1294691c2d50e0e389fe69ec286df4a06c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "74324042"
---
# <a name="tutorial-using-applications-in-azure-blockchain-workbench"></a>Oktatóanyag: alkalmazások használata az Azure Blockchain Workbenchben

A Blockchain Workbench használatával létrehozhat és alkalmazhat műveleteket a szerződéseken. Megtekintheti a szerződés részleteit, például az állapot és a tranzakciók előzményeit is.

A következőket fogja megtanulni:

> [!div class="checklist"]
> * Új szerződés létrehozása
> * Művelet elvégzése egy szerződésen

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Egy Blockchain Workbench üzembe helyezése. További információ: az [Azure Blockchain Workbench üzembe](deploy.md) helyezése az üzembe helyezés részleteiről
* Egy üzembe helyezett blockchain-alkalmazás a Blockchain Workbenchben. Lásd: [blockchain-alkalmazás létrehozása az Azure Blockchain workbenchben](create-app.md)

[Nyissa meg a Blockchain Workbench](deploy.md#blockchain-workbench-web-url) alkalmazást a böngészőben.

![Blockchain Workbench](./media/use/workbench.png)

A Blockchain Workbench tagjaként kell bejelentkeznie. Ha nincsenek felsorolt alkalmazások, akkor Ön a Blockchain Workbench tagja, de nem tagja egyetlen alkalmazásnak sem. A Blockchain Workbench rendszergazdája tagokat rendelhet az alkalmazásokhoz.

## <a name="create-new-contract"></a>Új szerződés létrehozása

Új szerződés létrehozásához a szerződés **kezdeményezője** megadott tagnak kell lennie. A szerződéshez tartozó alkalmazás-szerepköröket és kezdeményezőket meghatározó információk: [munkafolyamatok a konfiguráció áttekintésében](configuration.md#workflows). További információ a tagok alkalmazási szerepkörökhöz való hozzárendeléséről: [tag hozzáadása az alkalmazáshoz](manage-users.md#add-member-to-application).

1. A Blockchain Workbench alkalmazás szakaszban válassza ki a létrehozni kívánt szerződést tartalmazó alkalmazás csempéjét. Megjelenik az aktív szerződések listája.

2. Új szerződés létrehozásához válassza az **új szerződés** lehetőséget.

    ![Új szerződés gomb](./media/use/contract-list.png)

3. Megjelenik az **új szerződés** panel. Megadhatja a kezdeti paraméterek értékeit. Válassza a **Létrehozás** lehetőséget.

    ![Új szerződés panel](./media/use/new-contract.png)

    Az újonnan létrehozott szerződés a többi aktív szerződéssel együtt jelenik meg a listában.

    ![Aktív szerződések listája](./media/use/active-contracts.png)

## <a name="take-action-on-contract"></a>Szerződésen alapuló művelet elvégzése

Attól függően, hogy a szerződés milyen állapotban van, a tagok elvégezhetik a szerződés következő állapotára való áttérést. A műveletek egy adott [állapotban](configuration.md#states)lévő [átmenetként](configuration.md#transitions) vannak meghatározva. Az átálláshoz engedélyezett alkalmazás-vagy példány-szerepkörhöz tartozó tagok is elvégezhetik a műveletet. 

1. A Blockchain Workbench alkalmazás szakaszban válassza ki azt az alkalmazás-csempét, amely tartalmazza a szerződést a művelet végrehajtásához.
2. Válassza ki a szerződést a listában. A szerződés részletei különböző fejezetekben jelennek meg. 

    ![Szerződés részletei](./media/use/contract-details.png)

    | Section  | Description  |
    |---------|---------|
    | Állapot | A szerződés szakaszain belüli aktuális folyamat felsorolása |
    | Részletek | A szerződés aktuális értékei |
    | Művelet | Az utolsó művelet részletei |
    | Tevékenység | A szerződés tranzakciós előzményei |
    
3. A **művelet** szakaszban válassza a **művelet elvégzése** lehetőséget.

4. A szerződés aktuális állapotával kapcsolatos részletek a panelen jelennek meg. Válassza ki a legördülő listából azt a műveletet, amelyet el szeretne végezni. 

    ![Művelet kiválasztása](./media/use/choose-action.png)

5. A művelet elindításához válassza a **művelet elvégzése** lehetőséget.
6. Ha paraméterekre van szükség a művelethez, határozza meg a művelet értékeit.

    ![Művelet elvégzése](./media/use/take-action.png)

7. A művelet végrehajtásához válassza a **művelet elvégzése** lehetőséget.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench-alkalmazás verziószámozása](version-app.md)
