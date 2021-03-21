---
title: Rövid útmutató – privát végpont létrehozása a Azure Portal használatával
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre egy privát végpontot a Azure Portal használatával.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 10/20/2020
ms.author: allensu
ms.openlocfilehash: 395912ae70c5a01bd7de9a80cf8a507dd516028e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96018069"
---
# <a name="quickstart-create-a-private-endpoint-using-the-azure-portal"></a>Rövid útmutató: privát végpont létrehozása a Azure Portal használatával

Ismerkedjen meg az Azure Private-hivatkozással egy privát végpont használatával, amellyel biztonságosan csatlakozhat egy Azure-webalkalmazáshoz.

Ebben a rövid útmutatóban létrehoz egy privát végpontot egy Azure-webalkalmazáshoz, és üzembe helyez egy virtuális gépet a magánhálózati kapcsolat teszteléséhez.  

A magánhálózati végpontok különböző típusú Azure-szolgáltatásokhoz, például az Azure SQL-hez és az Azure Storage-hoz is létrehozhatók.

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Azure-webalkalmazás az Azure **-** előfizetésében üzembe helyezett PremiumV2 vagy magasabb szintű app Service-csomaggal.  
    * További információkat és példákat a rövid útmutató [: ASP.net Core Webalkalmazás létrehozása az Azure-ban](../app-service/quickstart-dotnetcore.md)című témakörben talál. 
    * A webalkalmazások és végpontok létrehozásával kapcsolatos részletes oktatóanyagért lásd [: oktatóanyag: Kapcsolódás egy webalkalmazáshoz egy Azure Private-végpont használatával](tutorial-private-endpoint-webapp-portal.md).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-a-virtual-network-and-bastion-host"></a>Virtuális hálózat és megerősített gazdagép létrehozása

Ebben a szakaszban létrehoz egy virtuális hálózatot, alhálózatot és egy megerősített gazdagépet. 

A megerősített gazdagép a magánhálózati végpont teszteléséhez a virtuális géphez való biztonságos kapcsolódást fogja használni.

1. Válassza ki a képernyő bal felső részén az **Erőforrás létrehozása > Hálózatkezelés > Virtuális hálózat** lehetőséget, vagy a keresőmezőben keressen rá a **virtuális hálózat** kifejezésre.

2. A **virtuális hálózat létrehozása** területen adja meg vagy válassza ki ezt az információt az **alapok** lapon:

    | **Beállítás**          | **Érték**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projekt részletei**  |                                                                 |
    | Előfizetés     | Válassza ki az Azure-előfizetését                                  |
    | Erőforráscsoport   | **CreatePrivateEndpointQS kiválasztása – RG** |
    | **Példány adatai** |                                                                 |
    | Name             | **MyVNet** megadása                                    |
    | Region           | Válassza a **Nyugat-Európa** régiót.|

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
    | Erőforráscsoport | **CreatePrivateEndpointQS kiválasztása – RG** |
    | **Példány adatai** |  |
    | Virtuális gép neve | **MyVM** megadása |
    | Region | Válassza a **Nyugat-Európa** régiót. |
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

## <a name="create-a-private-endpoint"></a>Privát végpont létrehozása

Ebben a szakaszban létre fog hozni egy privát végpontot az előfeltételek szakaszban létrehozott webalkalmazáshoz.

1. A portálon a képernyő bal felső részén válassza az **erőforrás létrehozása**  >  **hálózati**  >  **privát hivatkozás** lehetőséget, vagy a keresőmezőbe írja be a **privát hivatkozás** kifejezést.

2. Válassza a **Létrehozás** lehetőséget.

3. A **privát kapcsolat központban** válassza a bal oldali menüben a **privát végpontok** lehetőséget.

4. A **privát végpontok** területen válassza a **+ Hozzáadás** lehetőséget.

5. A **privát végpont létrehozása** elem **alapjai** lapon adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | **Projekt részletei** | |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válassza a **CreatePrivateEndpointQS-RG** elemet. Ezt az erőforráscsoportot az előző szakaszban hozta létre.|
    | **Példány adatai** |  |
    | Name  | Adja meg a **myPrivateEndpoint** nevet. |
    | Region | Válassza a **Nyugat-Európa** régiót. |

6. Válassza ki az **erőforrás** lapot vagy a **következő: erőforrás** gombot az oldal alján.
    
7. Az **erőforrás** mezőben adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | Kapcsolati módszer | Válassza a **Kapcsolódás egy Azure-erőforráshoz a címtárban** lehetőséget. |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforrás típusa | Válassza a **Microsoft. Web/Sites** lehetőséget. |
    | Erőforrás | Válassza az **\<your-web-app-name>** lehetőséget. </br> Válassza ki az előfeltételekben létrehozott webalkalmazás nevét. |
    | Célzott alerőforrás | Válassza a **helyek** lehetőséget. |

8. Válassza a **konfiguráció** lapot vagy a **következő: konfiguráció** gombot a képernyő alján.

9. A **konfiguráció** területen adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | **Hálózat** |  |
    | Virtuális hálózat | Válassza a **myVNet** lehetőséget. |
    | Alhálózat | Válassza a **mySubnet** lehetőséget. |
    | **saját DNS integráció** |  |
    | Integrálás saját DNS-zónával | Hagyja meg az alapértelmezett **Igen értéket**. |
    | Előfizetés | Válassza ki előfizetését. |
    | Privát DNS-zónák | Hagyja meg az alapértelmezett **(új) privatelink.azurewebsites.net**.
    

13. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.

14. Válassza a **Létrehozás** lehetőséget.

## <a name="test-connectivity-to-private-endpoint"></a>A magánhálózati végponthoz való kapcsolódás tesztelése

Ebben a szakaszban az előző lépésben létrehozott virtuális gépet fogja használni a webalkalmazáshoz való kapcsolódáshoz a privát végponton keresztül.

1. Válassza az **erőforráscsoportok** lehetőséget a bal oldali navigációs ablaktáblán.

2. Válassza a **CreatePrivateEndpointQS-RG** elemet.

3. Válassza a **myVM** lehetőséget.

4. A **myVM** áttekintés lapján válassza a **kapcsolat** , majd a **megerősített** lehetőséget.

5. Válassza a kék **használat Bastion** gombot.

6. Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót.

7. A kapcsolat után nyissa meg a Windows PowerShellt a kiszolgálón.

8. Írja be a következő szöveget: `nslookup <your-webapp-name>.azurewebsites.net`. A helyére írja **\<your-webapp-name>** be az előző lépésekben létrehozott webalkalmazás nevét.  A következőhöz hasonló üzenet jelenik meg:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mywebapp8675.privatelink.azurewebsites.net
    Address:  10.1.0.5
    Aliases:  mywebapp8675.azurewebsites.net
    ```

    A rendszer a webalkalmazás neveként a **10.1.0.5** magánhálózati IP-címét adja vissza.  Ez a címe a korábban létrehozott virtuális hálózat alhálózatában található.

11. Nyissa meg az Internet Explorert a **myVM**-ben lévő megerősített kapcsolódásban.

12. Adja meg a webalkalmazás ( **https:// \<your-webapp-name> . azurewebsites.net**) URL-címét.

13. Ha még nem telepítette az alkalmazást, az alapértelmezett webalkalmazás-oldal jelenik meg:

    :::image type="content" source="./media/create-private-endpoint-portal/web-app-default-page.png" alt-text="Alapértelmezett webalkalmazás lap." border="true":::

18. A **myVM** létesített kapcsolatok lezárása.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, törölje a virtuális hálózatot, a virtuális gépet és a webalkalmazást a következő lépésekkel:

1. A bal oldali menüben válassza az **erőforráscsoportok** lehetőséget.

2. Válassza a **CreatePrivateEndpointQS-RG** elemet.

3. Válassza az **Erőforráscsoport törlése** elemet.

4. Írja be a **CreatePrivateEndpointQS-RG** **nevet az erőforráscsoport nevének megadása mezőbe**.

5. Válassza a **Törlés** elemet.


## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozta a következőket:

* Virtuális hálózat és a megerősített gazdagép.
* Virtuális gép.
* Privát végpont egy Azure-webalkalmazáshoz.

A virtuális gépet arra használta, hogy biztonságosan tesztelje a kapcsolatot a webalkalmazáshoz a privát végponton keresztül.



A privát végpontot támogató szolgáltatásokkal kapcsolatos további információkért lásd:
> [!div class="nextstepaction"]
> [Privát kapcsolat elérhetősége](private-link-overview.md#availability)
