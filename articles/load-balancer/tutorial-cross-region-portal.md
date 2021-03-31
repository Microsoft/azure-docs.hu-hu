---
title: 'Oktatóanyag: régiók közötti terheléselosztó létrehozása a Azure Portal használatával'
titleSuffix: Azure Load Balancer
description: Ismerkedjen meg az Oktatóanyaggal, és telepítsen egy régiók közötti Azure Load Balancer a Azure Portal.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 02/24/2021
ms.openlocfilehash: c16123fae63b89eff57b5c91864d9a947e01b386
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104576917"
---
# <a name="tutorial-create-a-cross-region-azure-load-balancer-using-the-azure-portal"></a>Oktatóanyag: régiók közötti Azure Load Balancer létrehozása a Azure Portal használatával

A régiók közötti terheléselosztó biztosítja, hogy a szolgáltatások globálisan elérhetők legyenek több Azure-régió között. Ha az egyik régió meghibásodik, a rendszer átirányítja a forgalmat a legközelebbi, legközelebb egészséges regionális Load balancerbe.  

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozza létre a régiók közötti Load balancert.
> * Hozzon létre két regionális terheléselosztó-t tartalmazó háttér-készletet.
> * Hozzon létre egy terheléselosztó-szabályt.
> * A terheléselosztó tesztelése.

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

> [!IMPORTANT]
> A régiók közötti Azure Load Balancer jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés.
- Két különböző Azure-régióban üzembe helyezett backend-készletekkel rendelkező **standard** SKU Azure Load Balancer.
    - A regionális standard Load Balancer és a backend-készletek virtuális gépei létrehozásával kapcsolatos információkért lásd [: rövid útmutató: nyilvános terheléselosztó létrehozása a virtuális gépek terheléselosztásához a Azure Portal használatával](quickstart-load-balancer-standard-public-portal.md).
        - Fűzze hozzá az egyes régiókban található terheléselosztó, virtuális gépek és egyéb erőforrások nevét az **-R1** és **-R2** értékkel. 

## <a name="sign-in-to-azure-portal"></a>Bejelentkezés az Azure portálra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="create-cross-region-load-balancer"></a>Régiók közötti Load Balancer létrehozása

Ebben a szakaszban egy régiók közötti terheléselosztó és egy nyilvános IP-cím jön létre.

1. Válassza az **Erőforrás létrehozása** lehetőséget. 
2. A keresőmezőbe írja be a **Load Balancer** kifejezést. Válassza a **Load Balancer** elemet a keresési eredmények között.
3. A **terheléselosztó** lapon válassza a **Létrehozás** lehetőséget.
4. A **Load Balancer létrehozása** lap **alapok** lapján adja meg a következő információkat, vagy válassza ki az alábbi adatokat: 

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | Előfizetés               | Válassza ki előfizetését.    |    
    | Erőforráscsoport         | Válassza az **új létrehozása** elemet, és írja be a **CreateCRLBTutorial-RG** karakterláncot a szövegmezőbe.|
    | Name                   | Adja meg a **myLoadBalancer-CR** értéket                                   |
    | Region         | Válassza ki az USA **nyugati** régióját.                                        |
    | Típus          | Válassza a **Nyilvános** lehetőséget.                                        |
    | Termékváltozat           | Hagyja meg az alapértelmezett **standard** értéket. |
    | Szint           | **Globális** kiválasztása |
    | Nyilvános IP-cím | Válassza az **Új létrehozása** lehetőséget.|
    | Nyilvános IP-cím | Írja be a **myPublicIP-CR** karakterláncot a szövegmezőbe.|
    | Útválasztási beállítás| Válassza a **Microsoft Network** lehetőséget. </br> Az útválasztási beállításokkal kapcsolatos további információkért lásd: [Mi az útválasztási preferencia (előzetes verzió)?](../virtual-network/routing-preference-overview.md). |

    > [!NOTE]
    > A régiók közötti Load-Balancer csak a következő otthoni régiókban telepíthető: USA 2. keleti régiója, USA nyugati régiója, **Nyugat-Európa, Délkelet-Ázsia, USA középső régiója, Észak-Európa, Kelet-Ázsia**. További információ: **https://aka.ms/homeregionforglb**.


3. Fogadja el az alapértelmezett értékeket a többi beállításnál, majd válassza a **felülvizsgálat + létrehozás** elemet.

4. A **felülvizsgálat + létrehozás** lapon válassza a **Létrehozás** lehetőséget.   

    :::image type="content" source="./media/tutorial-cross-region-portal/create-cross-region.png" alt-text="Régiók közötti Load Balancer létrehozása" border="true":::

## <a name="create-backend-pool"></a>Háttérkészlet létrehozása

Ebben a szakaszban két regionális standard Load balancert fog hozzáadni a régiók közötti terheléselosztó háttér-készletéhez.

> [!IMPORTANT]
> A lépések elvégzéséhez győződjön meg arról, hogy az előfizetésében két regionális terheléselosztó van telepítve a háttér-készletek használatával.  További információ: gyors üzembe helyezés – **[nyilvános terheléselosztó létrehozása a virtuális gépek terheléselosztásához a Azure Portal használatával](quickstart-load-balancer-standard-public-portal.md)**.

Hozza létre a **myBackendPool-CR háttér-** címkészlet, hogy tartalmazza a regionális standard Load balancert.

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás** lehetőséget, majd az erőforrások listából válassza a **myLoadBalancer-CR** lehetőséget.

2. A **Beállítások** területen válassza a **háttér-készletek**, majd a **Hozzáadás** lehetőséget.

3. A **háttérbeli készlet hozzáadása** lapon a név mezőbe írja be a következőt: **myBackendPool-CR**.

4. Válassza a **Hozzáadás** lehetőséget.

4. Válassza a **myBackendPool-CR** elemet.

5. A **terheléselosztó területen válassza** a legördülő listát a **Load Balancer** alatt.

5. Válassza a **myLoadBalancer-R1** vagy a terheléselosztó nevét az 1. régióban.

6. Válassza a legördülő listát az előtér **-IP-konfiguráció** területen. Válassza a **LoadBalancerFrontEnd** lehetőséget.

7. Ismételje meg a 4-6 lépést a **myLoadBalancer-R2** hozzáadásához.

8. Válassza a **Hozzáadás** lehetőséget.

    :::image type="content" source="./media/tutorial-cross-region-portal/add-to-backendpool.png" alt-text="Regionális terheléselosztó hozzáadása a háttérkészletek-hez" border="true":::

## <a name="create-a-health-probe"></a>Állapotminta létrehozása

Ebben a szakaszban egy állapot-mintavételt hoz létre a terheléselosztási szabály létrehozásához:

* Elnevezett **myHealthProbe**.
* **TCP** protokoll.
* **5** másodperces intervallum.
* **Két** hiba nem kifogástalan állapotú küszöbértéke.

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás** lehetőséget, majd az erőforrások listából válassza a **myLoadBalancer-CR** lehetőséget.

2. A **Beállítások** területen válassza az **Állapotadat-mintavételek** lehetőséget.

3. Az állapot-mintavétel konfigurálásához használja ezeket az értékeket:

    | Beállítás | Érték |
    | ------- | ----- |
    | Név | Adja meg a **myHealthProbe**. |
    | Protokoll | Válassza a **TCP** lehetőséget. |
    | Port | Adja meg a **80** értéket. |
    | Időköz | Adja meg az **5** értéket. |
    | Nem kifogástalan állapot küszöbértéke | Adja meg a **2** értéket. |

4. Válassza az **OK** lehetőséget.

    > [!NOTE]
    > A több régióból álló Load Balancer beépített állapotú mintavételt tartalmaz. Ez a mintavétel a terheléselosztási szabály létrehozásához használható helyőrző.  További információ: **[a régiók közötti Load Balancer korlátai](cross-region-overview.md#limitations)**.

## <a name="create-a-load-balancer-rule"></a>Terheléselosztási szabály létrehozása

Ebben a szakaszban egy terheléselosztó-szabályt fog létrehozni:

* Elnevezett **: myhttprule**.
* A **LoadBalancerFrontEnd** nevű előtérben.
* Figyelés a **80-es porton**.
* Az elosztott terhelésű forgalmat a 80-es **porton** **myBackendPool-CR** nevű háttérre irányítja.

    > [!NOTE]
    > A frontend-portnak meg kell egyeznie a háttér-porttal és a háttér-készletben lévő regionális terheléselosztó előtérbeli portjával.

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás** lehetőséget, majd az erőforrások listából válassza a **myLoadBalancer-CR** lehetőséget.

2. A **Beállítások** területen válassza a **terheléselosztási szabályok** elemet, majd kattintson a **Hozzáadás** gombra.

3. Használja ezeket az értékeket a terheléselosztási szabály konfigurálásához:
    
    | Beállítás | Érték |
    | ------- | ----- |
    | Név | Adja meg a **: myhttprule**. |
    | IP-verzió | **IPv4** kiválasztása |
    | Előtérbeli IP-cím | **LoadBalancerFrontEnd** kiválasztása |
    | Protokoll | Válassza a **TCP** lehetőséget. |
    | Port | Adja meg a **80** értéket.|
    | Háttérport | Adja meg a **80** értéket. |
    | A háttérkészlet | Válassza a **myBackendPool** lehetőséget.|
    | Állapotadat-mintavétel | Válassza a **myHealthProbe** lehetőséget. |
    | Üresjárati időkorlát (perc) | Mozgassa a csúszkát **15**-re. |
    | TCP alaphelyzetbe állítása | Válassza az **Engedélyezve** lehetőséget. |

4. Hagyja meg a többi alapértelmezett beállítást, majd kattintson az **OK gombra**.

    :::image type="content" source="./media/tutorial-cross-region-portal/create-lb-rule.png" alt-text="Terheléselosztó szabály létrehozása" border="true":::

## <a name="test-the-load-balancer"></a>A terheléselosztó tesztelése

Ebben a szakaszban tesztelni fogja a régiók közötti Load balancert. A nyilvános IP-címhez egy böngészőben fog csatlakozni.  A virtuális gépeket a regionális terheléselosztó backend-készletek egyikében állíthatja le, és megfigyelheti a feladatátvételt.

1. A terheléselosztó nyilvános IP-címének megkeresése az **Áttekintés** képernyőn. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás** lehetőséget, majd a **myPublicIP-CR** elemet.

2. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába. Az IIS-webkiszolgáló alapértelmezett oldala jelenik meg a böngészőben.

    :::image type="content" source="./media/tutorial-cross-region-portal/test-cr-lb-1.png" alt-text="Terheléselosztó tesztelése" border="true":::

3. Állítsa le a virtuális gépeket az egyik regionális terheléselosztó háttér-készletében.

4. Frissítse a webböngészőt, és figyelje meg a másik regionális terheléselosztó kapcsolatának feladatátvételét.

    :::image type="content" source="./media/tutorial-cross-region-portal/test-cr-lb-2.png" alt-text="Terheléselosztó tesztelése a feladatátvétel után" border="true":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, a terheléselosztót és az összes kapcsolódó erőforrást. 

Ehhez válassza ki az erőforrásokat tartalmazó **CreateCRLBTutorial-RG** erőforráscsoportot, majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>További lépések

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

* Létrehozta a régiók közötti Load balancert.
* Regionális terheléselosztó hozzáadása a régiók közötti terheléselosztó háttér-készletéhez.
* Létrehozott egy terheléselosztási szabályt.
* Tesztelte a terheléselosztó.

A régiók közötti terheléselosztó további információit a következő témakörben talál:
> [!div class="nextstepaction"]
> [Régiók közötti Load Balancer (előzetes verzió)](cross-region-overview.md)
