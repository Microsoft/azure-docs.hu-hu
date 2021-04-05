---
title: 'Oktatóanyag: Kapcsolódás egy webalkalmazáshoz egy Azure Private-végpont használatával'
titleSuffix: Azure Private Link
description: Ismerkedjen meg ezzel az Oktatóanyaggal az Azure Private Endpoint használatával a webapphoz való kapcsolódáshoz.
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: tutorial
ms.date: 10/19/2020
ms.openlocfilehash: 502c48a92f5b41c4434d03139335a0ce05fa451f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92896973"
---
# <a name="tutorial-connect-to-a-web-app-using-an-azure-private-endpoint"></a>Oktatóanyag: Csatlakozás webalkalmazáshoz egy Azure-beli privát végpont segítségével

Az Azure privát végpontja az Azure-beli privát kapcsolat alapvető építőeleme. Lehetővé teszi az Azure-erőforrások, például a virtuális gépek (VM-EK) számára, hogy magánjellegű módon kommunikáljanak a privát kapcsolati erőforrásokkal.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzon létre egy virtuális hálózatot és egy megerősített gazdagépet.
> * Virtuális gépet hoz létre.
> * Hozzon létre egy webappot.
> * Hozzon létre egy privát végpontot.
> * A Web App Private-végponthoz való kapcsolódás tesztelése.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!Note]
> A privát végpontok nyilvános régiókban érhetők el a PremiumV2, a PremiumV3 Windows Web Apps, a Linux Web Apps és a Azure Functions Premium csomag (más néven a rugalmas Prémium csomag) számára. 

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="create-a-virtual-network-and-bastion-host"></a>Virtuális hálózat és megerősített gazdagép létrehozása

Ebben a szakaszban létrehoz egy virtuális hálózatot, alhálózatot és egy megerősített gazdagépet. 

A megerősített gazdagép a magánhálózati végpont teszteléséhez a virtuális géphez való biztonságos kapcsolódást fogja használni.

1. Válassza ki a képernyő bal felső részén az **Erőforrás létrehozása > Hálózatkezelés > Virtuális hálózat** lehetőséget, vagy a keresőmezőben keressen rá a **virtuális hálózat** kifejezésre.

2. A **virtuális hálózat létrehozása** területen adja meg vagy válassza ki ezt az információt az **alapok** lapon:

    | **Beállítás**          | **Érték**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projekt részletei**  |                                                                 |
    | Előfizetés     | Válassza ki az Azure-előfizetését                                  |
    | Erőforráscsoport   | **MyResourceGroup** kiválasztása |
    | **Példány adatai** |                                                                 |
    | Name             | **MyVNet** megadása                                    |
    | Region           | **Nyugat-Európa** kiválasztása |

3. Válassza az **IP-címek** lapot, vagy válassza a **következő: IP-címek** gombot az oldal alján.

4. Az **IP-címek** lapon adja meg a következő adatokat:

    | Beállítás            | Érték                      |
    |--------------------|----------------------------|
    | IPv4-címtartomány | Adja meg a **10.1.0.0/16** értéket |

5. Az **alhálózat neve** alatt válassza ki az **alapértelmezett** szót.

6. Az **alhálózat szerkesztése** területen adja meg a következő adatokat:

    | Beállítás            | Érték                      |
    |--------------------|----------------------------|
    | Alhálózat neve | **MySubnet** megadása |
    | Alhálózati címtartomány | Adja meg a **10.1.0.0/24** értéket |

7. Kattintson a **Mentés** gombra.

8. Válassza a **Biztonság** fület.

9. A **BastionHost** területen válassza az **Engedélyezés** lehetőséget. Adja meg a következő adatokat:

    | Beállítás            | Érték                      |
    |--------------------|----------------------------|
    | Bástya neve | **MyBastionHost** megadása |
    | AzureBastionSubnet címterület | Adja meg a **10.1.1.0/24** értéket |
    | Nyilvános IP-cím | Válassza az **Új létrehozása** lehetőséget. </br> A **név** mezőbe írja be a következőt: **myBastionIP**. </br> Válassza az **OK** lehetőséget. |


8. Válassza a **felülvizsgálat + létrehozás** lapot, vagy kattintson a **felülvizsgálat + létrehozás** gombra.

9. Válassza a **Létrehozás** lehetőséget.

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Ebben a szakaszban létre fog hozni egy virtuális gépet, amely a privát végpont tesztelésére szolgál.


1. A portál bal felső részén válassza az **erőforrás létrehozása**  >  **számítási**  >  **virtuális gép** vagy a keresés a **virtuális gépen** elemet a keresőmezőbe.
   
2. A **virtuális gép létrehozása** területen írja be vagy válassza ki az értékeket az **alapok** lapon:

    | Beállítás | Érték                                          |
    |-----------------------|----------------------------------|
    | **Projekt részletei** |  |
    | Előfizetés | Válassza ki az Azure-előfizetését |
    | Erőforráscsoport | **MyResourceGroup** kiválasztása |
    | **Példány adatai** |  |
    | Virtuális gép neve | **MyVM** megadása |
    | Region | **Nyugat-Európa** kiválasztása |
    | Rendelkezésre állási beállítások | Válassza az **infrastruktúra-redundancia nem szükséges** lehetőséget |
    | Kép | Válassza a **Windows Server 2019 Datacenter – Gen1** elemet. |
    | Azure Spot-példány | Válassza a **nem** lehetőséget |
    | Méret | A virtuális gép méretének kiválasztása vagy az alapértelmezett beállítás megadása |
    | **Rendszergazdai fiók** |  |
    | Felhasználónév | Adjon meg egy felhasználónevet |
    | Jelszó | Adja meg a jelszót |
    | Jelszó megerősítése | Jelszó újbóli megadása |

3. Válassza a **hálózatkezelés** lapot, vagy válassza a **Tovább: lemezek**, majd a **Tovább: hálózatkezelés** lehetőséget.
  
4. A hálózatkezelés lapon válassza ki vagy írja be a következőket:

    | Beállítás | Érték |
    |-|-|
    | **Hálózati adapter** |  |
    | Virtuális hálózat | **myVNet** |
    | Alhálózat | **mySubnet** |
    | Nyilvános IP-cím | Válassza a **Nincs** lehetőséget. |
    | NIC hálózati biztonsági csoport | **Basic**|
    | Nyilvános bejövő portok | Válassza a **Nincs** lehetőséget. |
   
5. Válassza az **Áttekintés + létrehozás** lehetőséget. 
  
6. Tekintse át a beállításokat, majd kattintson a **Létrehozás** gombra.

## <a name="create-web-app"></a>Webalkalmazás létrehozása

Ebben a szakaszban egy webalkalmazást fog létrehozni.

1. A bal oldali menüben válassza az **erőforrás létrehozása**  >  **tároló**  >  **webalkalmazás** lehetőséget, vagy keressen rá a **webalkalmazás** kifejezésre a keresőmezőbe.

2. A **create Web App** ( **alapismeretek** ) lapon adja meg vagy válassza ki a következő adatokat:

    | Beállítás | Érték                                          |
    |-----------------------|----------------------------------|
    | **Projekt részletei** |  |
    | Előfizetés | Válassza ki az Azure-előfizetését |
    | Erőforráscsoport | **MyResourceGroup** kiválasztása |
    | **Példány adatai** |  |
    | Name | Adja meg a **mywebapp**. Ha a név nem érhető el, adjon meg egy egyedi nevet. |
    | Közzététel | Válassza a **Kód** lehetőséget. |
    | Futtatókörnyezet verme | Válassza a **.net Core 3,1 (LTS)** elemet. |
    | Operációs rendszer | Válassza a **Windows** lehetőséget. |
    | Region | **Nyugat-Európa** kiválasztása |
    | **App Service-csomag** |  |
    | Windows-csomag (Nyugat-Európa) | Válassza az **Új létrehozása** lehetőséget. </br> Adja meg a **MyServicePlan** **nevet**. |
    | Termékváltozat és méret | Válassza a **Méret módosítása** lehetőséget. </br> Válassza a **P2V2** lehetőséget a **spec Picker** képernyőn. </br> Kattintson az **Alkalmaz** gombra. |
   
3. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.

4. Válassza a **Létrehozás** lehetőséget.

    :::image type="content" source="./media/tutorial-private-endpoint-webapp-portal/create-web-app.png" alt-text="A webalkalmazás létrehozása Azure Portalban – alapismeretek lap." border="true":::

## <a name="create-private-endpoint"></a>Privát végpont létrehozása

1. A bal oldali menüben válassza a **minden erőforrás**  >  **mywebapp** vagy a létrehozás során kiválasztott nevet.

2. A webalkalmazás áttekintése lapon válassza a **Beállítások**  >  **hálózatkezelés** lehetőséget.

3. A **hálózatkezelés** területen válassza **a privát végponti kapcsolatok konfigurálása** lehetőséget.

4. Válassza a **+ Hozzáadás** lehetőséget a **privát végpont kapcsolatai** képernyőn.

5. Adja meg vagy válassza ki az alábbi adatokat a **privát végpont hozzáadása** képernyőn:

    | Beállítás | Érték |
    | ------- | ----- |
    | Név | Adja meg a **mywebappendpoint**. |
    | Előfizetés | Válassza ki előfizetését. |
    | Virtuális hálózat | Válassza a **myVNet** lehetőséget. |
    | Alhálózat | Válassza a **mySubnet** lehetőséget. |
    | Integrálás saját DNS-zónával | Válassza az **Igen** lehetőséget. |

6. Válassza az **OK** lehetőséget.
    

## <a name="test-connectivity-to-private-endpoint"></a>A magánhálózati végponthoz való kapcsolódás tesztelése

Ebben a szakaszban az előző lépésben létrehozott virtuális gépet fogja használni a webalkalmazáshoz való kapcsolódáshoz a privát végponton keresztül.

1. Válassza az **erőforráscsoportok** lehetőséget a bal oldali navigációs ablaktáblán.

2. Válassza a **myResourceGroup** lehetőséget.

3. Válassza a **myVM** lehetőséget.

4. A **myVM** áttekintés lapján válassza a **kapcsolat** , majd a **megerősített** lehetőséget.

5. Válassza a kék **használat Bastion** gombot.

6. Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót.

7. A kapcsolat után nyissa meg a Windows PowerShellt a kiszolgálón.

8. Írja be a következő szöveget: `nslookup <webapp-name>.azurewebsites.net`. A helyére írja **\<webapp-name>** be az előző lépésekben létrehozott webalkalmazás nevét.  A következőhöz hasonló üzenet jelenik meg:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mywebapp8675.privatelink.azurewebsites.net
    Address:  10.1.0.5
    Aliases:  mywebapp8675.azurewebsites.net
    ```

    A rendszer a webalkalmazás neveként a **10.1.0.5** magánhálózati IP-címét adja vissza.  Ez a címe a korábban létrehozott virtuális hálózat alhálózatában található.

9. Nyisson meg egy webböngészőt a helyi számítógépen, és adja meg a webalkalmazás külső URL-címét, a **https:// \<webapp-name> . azurewebsites.net**.

10. Győződjön meg arról, hogy a rendszer **403** lapot kap. Ez az oldal azt jelzi, hogy a webalkalmazás kívülről nem érhető el.

    :::image type="content" source="./media/tutorial-private-endpoint-webapp-portal/web-app-ext-403.png" alt-text="403 oldal a külső webalkalmazások címeihez." border="true":::

11. Nyissa meg az Internet Explorert a **myVM**-ben lévő megerősített kapcsolódásban.

12. Adja meg a webalkalmazás ( **https:// \<webapp-name> . azurewebsites.net**) URL-címét.

13. Ellenőrizze, hogy a webalkalmazás alapértelmezett lapja jelenik-e meg.

    :::image type="content" source="./media/tutorial-private-endpoint-webapp-portal/web-app-default-page.png" alt-text="Alapértelmezett webalkalmazás lap." border="true":::

18. A **myVM** létesített kapcsolatok lezárása.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, törölje a virtuális hálózatot, a virtuális gépet és a webalkalmazást a következő lépésekkel:

1. A bal oldali menüben válassza az **erőforráscsoportok** lehetőséget.

2. Válassza a **myResourceGroup** lehetőséget.

3. Válassza az **Erőforráscsoport törlése** elemet.

4. Írja be a **myResourceGroup** **nevet az erőforráscsoport neve mezőbe**.

5. Válassza a **Törlés** elemet.

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan hozhat létre privát hivatkozási szolgáltatást:
> [!div class="nextstepaction"]
> [Privát kapcsolati szolgáltatás létrehozása](create-private-link-service-portal.md)
