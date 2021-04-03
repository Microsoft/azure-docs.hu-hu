---
title: A MetaMask összekötése egy Azure Blockchain Service Network szolgáltatással
description: Kapcsolódjon az Azure Blockchain Service networkhez a MetaMask használatával, és helyezzen üzembe egy intelligens szerződést.
ms.date: 09/12/2019
ms.topic: quickstart
ms.reviewer: janders
ms.openlocfilehash: 4a45e02a861ff20a4dc774668a4e008f9b42aeea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "90530437"
---
# <a name="quickstart-use-metamask-to-connect-and-deploy-a-smart-contract"></a>Gyors útmutató: az MetaMask használata intelligens szerződéshez való kapcsolódáshoz és üzembe helyezéshez

Ebben a rövid útmutatóban a MetaMask használatával csatlakozhat egy Azure Blockchain Service networkhez, és a Remix használatával üzembe helyezhet egy intelligens szerződést. Az Metamask egy böngésző-bővítmény, amely egy éteri pénztárcát kezel, és intelligens szerződéses műveleteket hajt végre.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Teljes rövid útmutató [: blockchain-tag létrehozása a Azure Portal vagy a](create-member.md) gyors útmutató [: Azure blockchain Service Blockchain-tag létrehozása az Azure CLI használatával](create-member-cli.md)
* A [MetaMask-tallózó bővítmény](https://metamask.io) telepítése
* MetaMask- [mobiltárca](https://metamask.zendesk.com/hc/en-us/articles/360015488971-New-to-MetaMask-Learn-How-to-Setup-MetaMask-the-First-Time) létrehozása

## <a name="get-endpoint-address"></a>Végponti címek beolvasása

A Blockchain hálózathoz való kapcsolódáshoz az Azure Blockchain szolgáltatás végpontjának címe szükséges. A végpont címe és elérési kulcsa a Azure Portal.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Navigáljon az Azure Blockchain-szolgáltatás tagjához.
1. Válassza a **tranzakciós csomópontok** és az alapértelmezett tranzakció csomópontja hivatkozást.

    ![Alapértelmezett tranzakciós csomópont kiválasztása](./media/connect-metamask/transaction-nodes.png)

1. Válassza a **kapcsolati karakterláncok > hozzáférési kulcsok** elemet.
1. Másolja a végponti címeket a https-ről **(1. hozzáférési kulcs)**.

    ![Kapcsolati sztring](./media/connect-metamask/connection-string.png)

## <a name="connect-metamask"></a>MetaMask összekötése

1. Nyissa meg a MetaMask böngésző bővítményét, és jelentkezzen be.
1. A hálózat legördülő menüben válassza az  **Egyéni RPC** lehetőséget.

    ![Egyéni RPC](./media/connect-metamask/custom-rpc.png)

1. Az **új hálózat > új RPC URL-** cím elemnél illessze be a fent lemásolt végpont-címet.
1. Kattintson a **Mentés** gombra.

    Ha a kapcsolatok sikeresek voltak, a magánhálózat a hálózat legördülő menüben jelenik meg.

    ![Új hálózat](./media/connect-metamask/new-network.png)

## <a name="deploy-smart-contract"></a>Intelligens szerződés üzembe helyezése

A Remix egy böngészőalapú szilárdtest-fejlesztési környezet. A MetaMask és a Remix együttes használatával az intelligens szerződéseket üzembe helyezheti, és műveleteket hajthat végre.

1. A böngészőjében lépjen az `https://remix.ethereum.org` helyre.
1. Válassza az **új fájl** elemet a **Kezdőlap** lap **fájl** területén.

    Nevezze el az új fájlt `simple.sol` .

    ![Fájl létrehozása](./media/connect-metamask/create-file.png)

    Válassza az **OK** lehetőséget.
1. A Remix Editorban illessze be a következő **egyszerű intelligens szerződés** kódját.

    ```solidity
    pragma solidity ^0.5.0;
             
    contract simple {
        uint balance;
                 
        constructor() public{
            balance = 0;
        }
                 
        function add(uint _num) public {
            balance += _num;
        }
                 
        function get() public view returns (uint){
            return balance;
        }
    }
    ```

    Az **egyszerű szerződés** deklarál egy **Balance** nevű állapotjelző változót. Két függvény van definiálva. A **Hozzáadás** függvény egy számot ad hozzá az **egyenleghez**. A **Get** függvény az **Egyenleg** értékét adja vissza.
1. A szerződés fordításához először válassza a szilárdtest-fordító ablaktáblát, majd válassza a  **Simple. Sol fordítása** lehetőséget.

    ![A képernyőfelvétel egy lefordított szerződést mutat be.](./media/connect-metamask/compile.png)

1. Válassza a **központi telepítés & Futtatás** ablaktáblát, majd állítsa be a **környezetet** a **Web3** beadásához, hogy a blockchain-tag MetaMask-kapcsolaton keresztül kapcsolódjon.

    ![Futtatás lap](./media/connect-metamask/injected-web3.png)

1. Válassza ki az **egyszerű** szerződést, majd **telepítse** a t.

    ![A képernyőfelvételen megtekintheti az üzembe helyezést és a tranzakciók futtatását a kiválasztott szerződéssel, majd a kiválasztott elemet.](./media/connect-metamask/deploy.png)


1. A MetaMask-értesítés figyelmezteti, ha nem rendelkezik elegendő összeggel a tranzakció végrehajtásához.

    Nyilvános blockchain-hálózat esetén a tranzakciós díj megfizetéséhez éterre van szükség. Mivel ez egy konzorciumban található magánhálózat, a gáz árát nullára állíthatja.

1.  Válassza ki a **gáz díjat > a > speciális szerkesztése** lehetőséget, állítsa a **gázárak** 0-ra.

    ![Gázárak](./media/connect-metamask/gas-price.png)

    Kattintson a **Mentés** gombra.

1. Válassza a **megerősítés** lehetőséget az intelligens szerződés blockchain való telepítéséhez.
1. A **központilag telepített szerződések** szakaszban bontsa ki az **egyszerű** szerződést.

    ![Központilag telepített szerződés](./media/connect-metamask/deployed-contract.png)

    Két művelet, **Hozzáadás** és **lekérés**, leképezés a szerződésben meghatározott funkciókra.

1. Ha blockchain szeretne **hozzáadni** egy tranzakciót, adjon meg egy hozzáadandó számot, majd válassza a **Hozzáadás** lehetőséget. A (z) Remix: "gáz-becslési hiba" üzenet jelenik meg. "a tranzakciót olyan privát blockchain küldi, amely nem igényel gázt." Válassza a **tranzakció küldése** lehetőséget a tranzakció kényszerítéséhez.
1. A szerződés központi telepítésekor a MetaMask-értesítés figyelmezteti, ha nem áll rendelkezésre elegendő összeg a tranzakció végrehajtásához.

    Mivel ez egy konzorcium privát hálózata, a gáz árát nulla értékre állíthatja.

1. Válassza a **gáz díja > a > speciális szerkesztése** lehetőséget, állítsa a **gáz árát** 0 értékre, majd válassza a **Mentés** lehetőséget.
1. Válassza a **megerősítés** lehetőséget a tranzakció blockchain való elküldéséhez.
1. Válassza a **Get** művelet lehetőséget. Ez a csomópont-adat lekérdezési hívása. Nincs szükség tranzakcióra.

A Remix hibakeresési panelje a blockchain tranzakcióinak részleteit jeleníti meg:

![Hibakeresési előzmények](./media/connect-metamask/debug.png)

Megtekintheti az **egyszerű** szerződések létrehozását, a tranzakciót **egyszerűre. adja hozzá**, és hívja a **Simple. Get**.

A MetaMask lévő tranzakciók előzményeinek megtekintéséhez nyissa meg a MetaMask böngésző bővítményét, és tekintse meg az **Előzmények** szakaszt a központilag telepített szerződés és tranzakciók naplójához.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban a MetaMask böngésző bővítményét használta egy Azure Blockchain szolgáltatás-tranzakciós csomóponthoz való kapcsolódáshoz, egy intelligens szerződés üzembe helyezéséhez és egy tranzakció elküldéséhez a Blockchain. Próbálja ki a következő oktatóanyagot az Azure Blockchain Development Kit for Ethereum és a szarvasgomba használatával egy intelligens szerződési funkció egy tranzakción keresztül történő létrehozásához, létrehozásához, üzembe helyezéséhez és végrehajtásához.

> [!div class="nextstepaction"]
> [Intelligens szerződések létrehozása, készítése és üzembe helyezése az Azure Blockchain Service-ben](send-transaction.md)