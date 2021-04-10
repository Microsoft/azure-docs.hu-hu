---
title: 'Oktatóanyag: NAT-átjáró létrehozása – Azure Portal'
titlesuffix: Azure Virtual Network NAT
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre és érvényesítheti a NAT-átjárókat a Azure Portal használatával.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: nat
ms.topic: tutorial
ms.date: 03/02/2021
ms.custom: template-tutorial
ms.openlocfilehash: 82b5892b027627871e5492e3c6cd3776a923632b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102553442"
---
# <a name="tutorial-create-a-nat-gateway-using-the-azure-portal"></a>Oktatóanyag: NAT-átjáró létrehozása a Azure Portal használatával

Ez az oktatóanyag bemutatja, hogyan használhatja az Azure Virtual Network NAT szolgáltatást. Létre kell hoznia egy NAT-átjárót, amely kimenő kapcsolatot biztosít az Azure-beli virtuális gépek számára. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzon létre egy virtuális hálózatot.
> * Virtuális gépet hoz létre.
> * Hozzon létre egy NAT-átjárót, és társítsa a virtuális hálózathoz.
> * Kapcsolódjon a virtuális géphez, és ellenőrizze a NAT IP-címét.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="virtual-network"></a>Virtuális hálózat

Egy virtuális gép üzembe helyezése és a NAT-átjáró használata előtt létre kell hozni az erőforráscsoportot és a virtuális hálózatot.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Válassza ki a képernyő bal felső részén az **Erőforrás létrehozása > Hálózatkezelés > Virtuális hálózat** lehetőséget, vagy a keresőmezőben keressen rá a **virtuális hálózat** kifejezésre.

3. Válassza a **Létrehozás** lehetőséget.

4. A **virtuális hálózat létrehozása** területen adja meg vagy válassza ki ezt az információt az **alapok** lapon:

    | **Beállítás**          | **Érték**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projekt részletei**  |                                                                 |
    | Előfizetés     | Válassza ki az Azure-előfizetését                                  |
    | Erőforráscsoport   | Válassza az **Új létrehozása** lehetőséget. </br> Adja meg a **myResourceGroupNAT**. </br> Válassza az **OK** lehetőséget. |
    | **Példány adatai** |                                                                 |
    | Name             | **MyVNet** megadása                                    |
    | Region           | Select **(Európa) Nyugat-Európa** |

5. Válassza az **IP-címek** lapot, vagy válassza a **következő: IP-címek** gombot az oldal alján.

6. Az **IP-címek** lapon adja meg a következő adatokat:

    | Beállítás            | Érték                      |
    |--------------------|----------------------------|
    | IPv4-címtartomány | Adja meg a **10.1.0.0/16** értéket |

7. Válassza az **+ alhálózat hozzáadása** elemet. 

8. Az **alhálózat szerkesztése** területen adja meg a következő adatokat:

    | Beállítás            | Érték                      |
    |--------------------|----------------------------|
    | Alhálózat neve | **MySubnet** megadása |
    | Alhálózati címtartomány | Adja meg a **10.1.0.0/24** értéket |

9. Válassza a **Hozzáadás** lehetőséget.

10. Válassza a **Biztonság** fület.

11. A **BastionHost** területen válassza az **Engedélyezés** lehetőséget. Adja meg a következő adatokat:

    | Beállítás            | Érték                      |
    |--------------------|----------------------------|
    | Bástya neve | **MyBastionHost** megadása |
    | AzureBastionSubnet címterület | Adja meg a **10.1.1.0/24** értéket |
    | Nyilvános IP-cím | Válassza az **Új létrehozása** lehetőséget. </br> A **név** mezőbe írja be a következőt: **myBastionIP**. </br> Válassza az **OK** lehetőséget. |

12. Válassza a **felülvizsgálat + létrehozás** lapot, vagy kattintson a **felülvizsgálat + létrehozás** gombra.

13. Válassza a **Létrehozás** lehetőséget.

## <a name="nat-gateway"></a>NAT-átjáró

Használhat egy vagy több nyilvános IP-cím-erőforrást, nyilvános IP-előtagot vagy mindkettőt. Hozzáadunk egy nyilvános IP-erőforrást és egy NAT-átjáró erőforrást.

1. A képernyő bal felső részén válassza az **erőforrás létrehozása > hálózatkezelés > NAT-átjáró** vagy a **NAT-átjáró** keresése a keresőmezőbe elemet.

2. Válassza a **Létrehozás** lehetőséget. 

3. A **hálózati címfordítási (NAT-) átjáró létrehozása** területen adja meg vagy válassza ki az alábbi adatokat az **alapok** lapon:

    | **Beállítás**          | **Érték**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projekt részletei**  |                                                                 |
    | Előfizetés     | Válassza ki az Azure-előfizetését.                                  |
    | Erőforráscsoport   | Válassza a **myResourceGroupNAT** lehetőséget. |
    | **Példány adatai** |                                                                 |
    | Name             | **MyNATgateway** megadása                                    |
    | Region           | Select **(Európa) Nyugat-Európa**  |
    | Rendelkezésre állási zóna | Válassza a **Nincs** lehetőséget. |
    | Üresjárati időkorlát (perc) | Adja meg a **10** értéket. |

4. Válassza a **kimenő IP-cím** lapot, vagy válassza a **következő: kimenő IP-cím** gombot az oldal alján.

5. A **kimenő IP-cím** lapon adja meg vagy válassza ki a következő adatokat:

    | **Beállítás** | **Érték** |
    | ----------- | --------- |
    | Nyilvános IP-címek | Válassza **az új nyilvános IP-cím létrehozása** lehetőséget. </br> A **név** mezőben adja meg a **myPublicIP**. </br> Válassza az **OK** lehetőséget. |

6. Válassza az **alhálózat** fület, vagy válassza a **következő: alhálózat** gombot a lap alján.

7. Az **alhálózat** lapon válassza a **MyVNet** lehetőséget a **virtuális hálózat** legördülő menüben.

8. Jelölje be a **mySubnet** melletti jelölőnégyzetet.

9. Válassza a **felülvizsgálat + létrehozás** fület, vagy kattintson a lap alján található kék **Áttekintés + létrehozás** gombra.

10. Válassza a **Létrehozás** lehetőséget.
    
## <a name="virtual-machine"></a>Virtuális gép

Ebben a szakaszban létre fog hozni egy virtuális gépet a NAT-átjáró teszteléséhez és a kimenő kapcsolatok nyilvános IP-címének ellenőrzéséhez.

1. A portál bal felső részén válassza az **erőforrás létrehozása**  >  **számítási**  >  **virtuális gép** lehetőséget. 

2. A **virtuális gép létrehozása** lapon az **alapismeretek** lapon adja meg a következő információkat, vagy válassza ki az alábbi adatokat:

    | **Beállítás** | **Érték** |
    | ----------- | --------- |
    | **Projekt részletei** |   |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válassza a **myResourceGroupNAT** lehetőséget. |
    | **Példány adatai** |   |
    | Virtuális gép neve | Adja meg a **myVM**. |
    | Region | Válassza az **(Európa) Nyugat-Európa** lehetőséget. |
    | Rendelkezésre állási beállítások | Hagyja meg az alapértelmezett nem szükséges redundanciát. |
    | Kép | Válassza a **Windows Server 2019 Datacenter-Gen1** elemet. |
    | Méret | Válassza a **Standard_DS1_v2** lehetőséget. |
    | **Rendszergazdai fiók** |   |
    | Felhasználónév | Adja meg a virtuális gép felhasználónevét. |
    | Jelszó | Adjon meg egy jelszót. |
    | Jelszó megerősítése | Erősítse meg a jelszót. |
    | **Bejövő portok szabályai** |    |
    | Nyilvános bejövő portok | Válassza a **Nincs** lehetőséget. |

3. Válassza a **lemezek** fület, vagy válassza a **következő: lemezek** gombot az oldal alján.

4. Hagyja meg az alapértelmezett értéket a **lemezek** lapon.

5. Válassza a **hálózatkezelés** fület, vagy válassza a **következő: hálózatkezelés** gombot az oldal alján.

6. A **hálózatkezelés** lapon adja meg vagy válassza ki a következő adatokat:

    | **Beállítás** | **Érték** |
    | ----------- | --------- |
    | **Hálózati adapter** |   |
    | Virtuális hálózat | Válassza a **myVNet** lehetőséget. |
    | Alhálózat | Válassza a **mySubnet** lehetőséget. |
    | Nyilvános IP-cím | Válassza a **Nincs** lehetőséget. |
    | NIC hálózati biztonsági csoport | Válassza az **Alapszintű** lehetőséget. |
    | Nyilvános bejövő portok | Válassza a **Nincs** lehetőséget. |

7. Válassza a **felülvizsgálat + létrehozás** fület, vagy kattintson a lap alján található kék **Áttekintés + létrehozás** gombra.

8. Válassza a **Létrehozás** lehetőséget.

## <a name="test-nat-gateway"></a>NAT-átjáró tesztelése

Ebben a szakaszban teszteljük a NAT-átjárót. Először a NAT-átjáró nyilvános IP-címét fogjuk felderíteni. Ezután csatlakozik a teszt virtuális géphez, és ellenőrizze a kimenő kapcsolatot a NAT-átjárón keresztül.
    
1. Keresse meg a NAT-átjáró nyilvános IP-címét az **Áttekintés** képernyőn. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás** lehetőséget, majd válassza a **myPublicIP** lehetőséget.

2. Jegyezze fel a nyilvános IP-címet:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/find-public-ip.png" alt-text="A NAT-átjáró nyilvános IP-címének felderítése" border="true":::

3. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás** lehetőséget, majd az erőforrások listából válassza ki a **myVM** , amely a **myResourceGroupNAT** erőforráscsoporthoz található.

4. Az **Áttekintés** lapon válassza a **kapcsolat**, majd a **Bastion** lehetőséget.

5. Válassza a kék **használat Bastion** gombot.

6. Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót.

7. Nyissa meg az **Internet Explorert** a **myTestVM**.

8. Írja be **https://whatsmyip.com** a címsorba.

9. Ellenőrizze, hogy a megjelenő IP-cím megegyezik-e az előző lépésben feljegyzett NAT-átjáró címével:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/my-ip.png" alt-text="Külső kimenő IP-címet mutató Internet Explorer" border="true":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, törölje a virtuális hálózatot, a virtuális gépet és a NAT-átjárót a következő lépésekkel:

1. A bal oldali menüben válassza az **erőforráscsoportok** lehetőséget.

2. Válassza ki a **myResourceGroupNAT** erőforráscsoportot.

3. Válassza az **Erőforráscsoport törlése** elemet.

4. Adja meg a **myResourceGroupNAT** , és válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Az Azure Virtual Network NAT-ról további információt a következő témakörben talál:
> [!div class="nextstepaction"]
> [Virtual Network NAT áttekintése](nat-overview.md)
