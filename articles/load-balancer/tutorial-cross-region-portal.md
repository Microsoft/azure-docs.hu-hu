---
title: 'Oktatóanyag: Régiók közötti terheléselosztás létrehozása a Azure Portal'
titleSuffix: Azure Load Balancer
description: Az oktatóanyag első lépésekben régiók közötti virtuális gépeket Azure Load Balancer üzembe a Azure Portal.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 02/24/2021
ms.openlocfilehash: 16320021ede4a4e285c4e1973c166d2cdf643c4a
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529527"
---
# <a name="tutorial-create-a-cross-region-azure-load-balancer-using-the-azure-portal"></a>Oktatóanyag: Régiók közötti Azure Load Balancer létrehozása a Azure Portal

A régiók közötti terheléselosztás biztosítja, hogy a szolgáltatás globálisan több Azure-régióban is elérhető. Ha az egyik régió meghibásodik, a forgalom a legközelebbi megfelelő állapotú regionális terheléselosztáshoz lesz irányítva.  

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Régiók közötti terheléselosztás létrehozása.
> * Hozzon létre egy két regionális terheléselosztást tartalmazó háttérkészletet.
> * Hozzon létre egy terheléselosztó-szabályt.
> * A terheléselosztás tesztelése.

Ha nem rendelkezik Azure-előfizetéssel, kezdés előtt hozzon létre egy [ingyenes](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) fiókot.

> [!IMPORTANT]
> A régiók közötti Azure Load Balancer jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés.
- Két **standard** termékváltozat az Azure Load Balancer két különböző Azure-régióban üzembe helyezett háttérkészletekkel.
    - További információ regionális standard terheléselosztás létrehozásáról és virtuális gépek háttérkészletek számára való létrehozásáról: Rövid [útmutató:](quickstart-load-balancer-standard-public-portal.md)Nyilvános terheléselosztás létrehozása a virtuális gépek terheléselosztása az Azure Portal.
        - Minden régióban fűzheti hozzá a terheléselosztások, virtuális gépek és egyéb erőforrások nevét az **-R1 és** **-R2 névvel.** 

## <a name="sign-in-to-azure-portal"></a>Bejelentkezés az Azure portálra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="create-cross-region-load-balancer"></a>Régiók közötti terheléselosztás létrehozása

Ebben a szakaszban egy régiók közötti terheléselosztást és nyilvános IP-címet fog létrehozni.

1. Válassza az **Erőforrás létrehozása** lehetőséget. 
2. A keresőmezőbe írja be a **Következőt: Load Balancer.** A **keresési eredmények között válassza a** Terheléselosztás lehetőséget.
3. A **Terheléselosztás lapon** válassza a Létrehozás **lehetőséget.**
4. A **Terheléselosztás létrehozása** lap Alapvető beállítások lapján **adja** meg vagy válassza ki a következő adatokat: 

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | Előfizetés               | Válassza ki előfizetését.    |    
    | Erőforráscsoport         | Válassza **az Új létrehozása lehetőséget,** és írja be a **CreateCRLBTutorial-rg** szöveget a szövegmezőbe.|
    | Name                   | Adja meg **a myLoadBalancer-CR adhatja meg**                                   |
    | Region         | Válassza **az (USA) USA nyugati régiója lehetőséget.**                                        |
    | Típus          | Válassza a **Nyilvános** lehetőséget.                                        |
    | Termékváltozat           | Hagyja meg az alapértelmezett **Standard értéket.** |
    | Szint           | Válassza a **Globális lehetőséget** |
    | Nyilvános IP-cím | Válassza az **Új létrehozása** lehetőséget.|
    | Nyilvános IP-cím | Írja **be a myPublicIP-CR** szöveget a szövegmezőbe.|
    | Útválasztási beállítás| Válassza **a Microsoft Network lehetőséget.** </br> Az útválasztási beállításokkal kapcsolatos további információkért lásd: [Mi az útválasztási beállítás (előzetes verzió)?](../virtual-network/routing-preference-overview.md). |

    > [!NOTE]
    > A régiók közötti terheléselosztás csak a következő otthoni régiókban helyezhető üzembe: **USA 2. keleti régiója, USA nyugati régiója, Nyugat-Európa, Délkelet-Ázsia, USA középső régiója, Észak-Európa, Kelet-Ázsia.** További információ: **https://aka.ms/homeregionforglb**.


3. Fogadja el az alapértelmezett értékeket a többi beállításnál, majd válassza az **Áttekintés + létrehozás lehetőséget.**

4. Az Áttekintés **+ létrehozás lapon** válassza a Létrehozás **lehetőséget.**   

    :::image type="content" source="./media/tutorial-cross-region-portal/create-cross-region.png" alt-text="Régiók közötti terheléselosztás létrehozása" border="true":::

## <a name="create-backend-pool"></a>Háttérkészlet létrehozása

Ebben a szakaszban két regionális standard terheléselosztást fog hozzáadni a régiók közötti terheléselosztás háttérkészletében.

> [!IMPORTANT]
> A lépések befejezéséhez győződjön meg arról, hogy két, háttérkészletekkel bíró regionális terheléselosztást helyezett üzembe az előfizetésében.  További információkért lásd: Rövid **[útmutató:](quickstart-load-balancer-standard-public-portal.md)** Nyilvános terheléselosztás létrehozása a virtuális gépek terhelésének az Azure Portal.

Hozza létre a **myBackendPool-CR háttércímkészletet,** hogy tartalmazza a regionális standard terheléselosztásokat.

1. A **bal oldali menüben** válassza a Minden szolgáltatás, majd a Minden erőforrás **lehetőséget,** végül pedig a **myLoadBalancer-CR** elemet az erőforrások listájából.

2. A **Beállítások alatt** válassza a **Háttérkészletek** lehetőséget, majd a Hozzáadás **lehetőséget.**

3. A **Háttérkészlet hozzáadása** lapon a név mezőbe írja be a **myBackendPool-CR nevet.**

4. Válassza a **Hozzáadás** lehetőséget.

4. Válassza **a myBackendPool-CR lehetőséget.**

5. A **Terheléselosztások alatt** válassza a Load Balancer (Terheléselosztás) alatt **található letöltőmezőt.**

5. Válassza **a myLoadBalancer-R1** lehetőséget, vagy a terheléselosztás nevét az 1. régióban.

6. Válassza az Előtere IP-konfigurációja alatti **lekért mezőt.** Válassza **a LoadBalancerFrontEnd lehetőséget.**

7. Ismételje meg a 4–6. lépést a **myLoadBalancer-R2 hozzáadásához.**

8. Válassza a **Hozzáadás** lehetőséget.

    :::image type="content" source="./media/tutorial-cross-region-portal/add-to-backendpool.png" alt-text="Regionális terheléselosztások hozzáadása a háttérkészlethez" border="true":::

## <a name="create-a-load-balancer-rule"></a>Terheléselosztási szabály létrehozása

Ebben a szakaszban egy terheléselosztási szabályt fog létrehozni:

* A **neve myHTTPRule.**
* A **LoadBalancerFrontEnd nevű előtérben.**
* A **80-as porton figyel.**
* Az elosztott terhelésű forgalmat a **myBackendPool-CR** nevű háttérhez irányítja a **80-as porton.**

    > [!NOTE]
    > Az előtereportnak egyeznie kell a háttérporttal és a háttérkészlet regionális terheléselosztási portjának előterével.

1. A **bal oldali menüben** válassza a Minden szolgáltatás, majd a Minden erőforrás **lehetőséget,** végül pedig a **myLoadBalancer-CR** elemet az erőforrások listájából.

2. A **Beállítások alatt** válassza a **Terheléselosztási szabályok lehetőséget,** majd a Hozzáadás **lehetőséget.**

3. Az alábbi értékekkel konfigurálhatja a terheléselosztási szabályt:
    
    | Beállítás | Érték |
    | ------- | ----- |
    | Név | Írja be **a myHTTPRule adatokat.** |
    | IP-verzió | Válassza az **IPv4 lehetőséget** |
    | Előtere IP-címe | Válassza **a LoadBalancerFrontEnd lehetőséget** |
    | Protokoll | Válassza a **TCP** lehetőséget. |
    | Port | Adja meg **a 80-ast.**|
    | Háttérport | Adja meg **a 80-ast.** |
    | A háttérkészlet | Válassza **a myBackendPool lehetőséget.**|
    | Üresjárati időkorlát (perc) | Húzza a csúszkát **15-re.** |
    | TCP alaphelyzetbe állítása | Válassza az **Engedélyezve** lehetőséget. |

4. Hagyja meg a többi alapértelmezett értéket, majd kattintson az **OK gombra.**

    :::image type="content" source="./media/tutorial-cross-region-portal/create-lb-rule.png" alt-text="Terheléselosztási szabály létrehozása" border="true":::

## <a name="test-the-load-balancer"></a>A terheléselosztó tesztelése

Ebben a szakaszban a régiók közötti terheléselosztást fogja tesztelni. Egy webböngészőben fog csatlakozni a nyilvános IP-címhez.  Leállítja az egyik regionális terheléselosztási háttérkészlet virtuális gépeit, és megfigyeli a feladatátvételt.

1. Keresse meg a terheléselosztás nyilvános IP-címét az **Áttekintés képernyőn.** A **bal oldali menüben** válassza a Minden szolgáltatás, majd a **Minden** erőforrás lehetőséget, végül pedig a **myPublicIP-CR elemet.**

2. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába. Az IIS-webkiszolgáló alapértelmezett oldala jelenik meg a böngészőben.

    :::image type="content" source="./media/tutorial-cross-region-portal/test-cr-lb-1.png" alt-text="Terheléselosztó tesztelése" border="true":::

3. Állítsa le az egyik regionális terheléselosztás háttérkészletében található virtuális gépeket.

4. Frissítse a webböngészőt, és figyelje meg a kapcsolat feladatátvételét a másik regionális terheléseltöltővel.

    :::image type="content" source="./media/tutorial-cross-region-portal/test-cr-lb-2.png" alt-text="Terheléselosztás tesztelése feladatátvétel után" border="true":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, a terheléselosztót és az összes kapcsolódó erőforrást. 

Válassza ki az erőforrásokat tartalmazó **CreateCRLBTutorial-rg** erőforráscsoportot, majd válassza a **Törlés lehetőséget.**

## <a name="next-steps"></a>További lépések

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

* Létrehozott egy régiók közötti terheléselosztást.
* Regionális terheléselosztások hozzáadva a régiók közötti terheléselosztás háttérkészletében.
* Létrehozott egy terheléselosztási szabályt.
* Tesztelte a terheléseltöltőt.

További információ a régiók közötti terheléselosztásról:
> [!div class="nextstepaction"]
> [Régiók közötti terheléselelosztás (előzetes verzió)](cross-region-overview.md)
