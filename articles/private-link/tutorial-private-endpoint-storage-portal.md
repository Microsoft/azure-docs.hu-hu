---
title: 'Oktatóanyag: Kapcsolódás egy Storage-fiókhoz egy Azure Private-végpont használatával'
titleSuffix: Azure Private Link
description: Ismerkedjen meg ezzel az Oktatóanyaggal az Azure Private Endpoint használatával, hogy magánjellegű módon kapcsolódjon a Storage-fiókhoz.
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: tutorial
ms.date: 9/25/2020
ms.openlocfilehash: 64856d0c9a06f57eb25a0cbc9279d1c09992f0d3
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2020
ms.locfileid: "92147599"
---
# <a name="tutorial-connect-to-a-storage-account-using-an-azure-private-endpoint"></a>Oktatóanyag: Kapcsolódás egy Storage-fiókhoz egy Azure Private-végpont használatával

Az Azure privát végpontja az Azure-beli privát kapcsolat alapvető építőeleme. Lehetővé teszi az Azure-erőforrások, például a virtuális gépek (VM-EK) számára, hogy magánjellegű módon kommunikáljanak a privát kapcsolati erőforrásokkal.

Az oktatóanyag a következőket ismerteti:

> [!div class="checklist"]
> * Hozzon létre egy virtuális hálózatot és egy megerősített gazdagépet.
> * Virtuális gépet hoz létre.
> * Hozzon létre egy privát végponttal rendelkező Storage-fiókot.
> * A Storage-fiók magánhálózati végpontjának kapcsolatának tesztelése.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="create-a-virtual-network-and-bastion-host"></a>Virtuális hálózat és megerősített gazdagép létrehozása

Ebben a szakaszban létrehoz egy virtuális hálózatot, alhálózatot és egy megerősített gazdagépet. 

A megerősített gazdagép a magánhálózati végpont teszteléséhez a virtuális géphez való biztonságos kapcsolódást fogja használni.

1. Válassza ki a képernyő bal felső részén az **Erőforrás létrehozása > Hálózatkezelés > Virtuális hálózat** lehetőséget, vagy a keresőmezőben keressen rá a **virtuális hálózat** kifejezésre.

2. A **virtuális hálózat létrehozása**területen adja meg vagy válassza ki ezt az információt az **alapok** lapon:

    | **Beállítás**          | **Érték**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projekt részletei**  |                                                                 |
    | Előfizetés     | Válassza ki az Azure-előfizetését                                  |
    | Erőforráscsoport   | **MyResourceGroup** kiválasztása |
    | **Példány adatai** |                                                                 |
    | Name             | **MyVNet** megadása                                    |
    | Régió           | Válassza ki az **USA keleti** régióját |

3. Válassza az **IP-címek** lapot, vagy válassza a **következő: IP-címek** gombot az oldal alján.

4. Az **IP-címek** lapon adja meg a következő adatokat:

    | Beállítás            | Érték                      |
    |--------------------|----------------------------|
    | IPv4-címtartomány | Adja meg a **10.1.0.0/16** értéket |

5. Az **alhálózat neve**alatt válassza ki az **alapértelmezett**szót.

6. Az **alhálózat szerkesztése**területen adja meg a következő adatokat:

    | Beállítás            | Érték                      |
    |--------------------|----------------------------|
    | Alhálózat neve | **MySubnet** megadása |
    | Alhálózati címtartomány | Adja meg a **10.1.0.0/24** értéket |

7. Kattintson a **Mentés** gombra.

8. Válassza a **Biztonság** fület.

9. A **BastionHost**területen válassza az **Engedélyezés**lehetőséget. Adja meg a következő adatokat:

    | Beállítás            | Érték                      |
    |--------------------|----------------------------|
    | Bástya neve | **MyBastionHost** megadása |
    | AzureBastionSubnet címterület | Adja meg a **10.1.1.0/24** értéket |
    | Nyilvános IP-cím | Válassza az **Új létrehozása** lehetőséget. </br> A **név**mezőbe írja be a következőt: **myBastionIP**. </br> Válassza az **OK** lehetőséget. |


8. Válassza a **felülvizsgálat + létrehozás** lapot, vagy kattintson a **felülvizsgálat + létrehozás** gombra.

9. Kattintson a **Létrehozás** gombra.

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Ebben a szakaszban létre fog hozni egy virtuális gépet, amely a privát végpont tesztelésére szolgál.


1. A portál bal felső részén válassza az **erőforrás létrehozása**  >  **számítási**  >  **virtuális gép** vagy a keresés a **virtuális gépen** elemet a keresőmezőbe.
   
2. A **virtuális gép létrehozása**területen írja be vagy válassza ki az értékeket az **alapok** lapon:

    | Beállítás | Érték                                          |
    |-----------------------|----------------------------------|
    | **Projekt részletei** |  |
    | Előfizetés | Válassza ki az Azure-előfizetését |
    | Erőforráscsoport | **MyResourceGroup** kiválasztása |
    | **Példány adatai** |  |
    | Virtuális gép neve | **MyVM** megadása |
    | Régió | Válassza ki az **USA keleti** régióját |
    | Rendelkezésre állási beállítások | Válassza az **infrastruktúra-redundancia nem szükséges** lehetőséget |
    | Kép | Válassza a **Windows Server 2019 Datacenter – Gen1** elemet. |
    | Azure Spot-példány | Válassza a **nem** lehetőséget |
    | Méret | A virtuális gép méretének kiválasztása vagy az alapértelmezett beállítás megadása |
    | **Rendszergazdai fiók** |  |
    | Felhasználónév | Adjon meg egy felhasználónevet |
    | Jelszó | Adja meg a jelszót |
    | Jelszó megerősítése | Jelszó újbóli megadása |

3. Válassza a **hálózatkezelés** lapot, vagy válassza a **Tovább: lemezek**, majd a **Tovább: hálózatkezelés**lehetőséget.
  
4. A hálózatkezelés lapon válassza ki vagy írja be a következőket:

    | Beállítás | Érték |
    |-|-|
    | **Hálózati adapter** |  |
    | Virtuális hálózat | **myVNet** |
    | Alhálózat | **mySubnet** |
    | Nyilvános IP-cím | Válassza a **Nincs** lehetőséget. |
    | NIC hálózati biztonsági csoport | **Basic**|
    | Nyilvános bejövő portok | Válassza a **Nincs** lehetőséget. |
   
5. Válassza a **Felülvizsgálat + létrehozás** lehetőséget. 
  
6. Tekintse át a beállításokat, majd kattintson a **Létrehozás**gombra.

## <a name="create-storage-account-with-a-private-endpoint"></a>Privát végponttal rendelkező Storage-fiók létrehozása

Ebben a szakaszban létrehoz egy Storage-fiókot, és konfigurálja a magánhálózati végpontot.

1. A bal oldali menüben válassza az **erőforrás létrehozása**  >  **Storage**  >  **Storage-fiók**lehetőséget, vagy keressen rá a **Storage-fiókra** a keresőmezőbe.

2. A **Storage-fiók létrehozása** **alapismeretek** lapján adja meg vagy válassza ki a következő adatokat:

    | Beállítás | Érték                                          |
    |-----------------------|----------------------------------|
    | **Projekt részletei** |  |
    | Előfizetés | Válassza ki az Azure-előfizetését |
    | Erőforráscsoport | **MyResourceGroup** kiválasztása |
    | **Példány adatai** |  |
    | Tárfiók neve | Adja meg a **mystorageaccount**. Ha a név nem érhető el, adjon meg egy egyedi nevet. |
    | Hely | Válassza ki az **USA keleti** régióját |
    | Teljesítmény | Hagyja meg az alapértelmezett **standardot** |
    | Fiók altípusa | Hagyja meg az alapértelmezett **tárolót (általános célú v2)** |
    | Replikáció| Hagyja meg az alapértelmezett **olvasási hozzáférésű geo-redundáns tárterületet (ra-GRS)** |
   
3. Válassza a **hálózatkezelés** fület, vagy kattintson a **Tovább: hálózatkezelés** gombra.

4. A **hálózatkezelés** lap **kapcsolati módszer** területén válassza a **privát végpont**elemet.

5. A **privát végponton**válassza a **+ Hozzáadás**lehetőséget.

6. A **privát végpont létrehozása** területen adja meg vagy válassza ki a következő adatokat:

    | Beállítás | Érték                                          |
    |-----------------------|----------------------------------|
    | Előfizetés | Válassza ki az Azure-előfizetését |
    | Erőforráscsoport | **MyResourceGroup** kiválasztása |
    | Hely | Válassza ki az **USA keleti** régióját |
    | Név | **MyPrivateEndpoint** megadása |
    | Tárolási alerőforrás | Hagyja meg az alapértelmezett **blobot** |
    | **Hálózat** |  |
    | Virtuális hálózat | **MyVNet** kiválasztása |
    | Alhálózat | Válassza a **mySubnet** lehetőséget. |
    | **saját DNS integráció** |
    | Integrálás saját DNS-zónával | Hagyja meg az alapértelmezett **Igen értéket** |
    | Privát DNS-zóna | Hagyja meg az alapértelmezett (új) privatelink.blob.core.windows.net |

7. Válassza az **OK** lehetőséget.

8. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.

9. Válassza a **Létrehozás** lehetőséget.

10. Válassza az **erőforráscsoportok** lehetőséget a bal oldali navigációs ablaktáblán.

11. Válassza a **myResourceGroup** lehetőséget.

12. Válassza ki az előző lépésekben létrehozott Storage-fiókot.

13. A Storage-fiók **Beállítások** szakaszában válassza a **hozzáférési kulcsok**elemet.

14. Válassza a másolás lehetőséget a **key1**tartozó **kapcsolatok karakterláncában** .

## <a name="test-connectivity-to-private-endpoint"></a>A magánhálózati végponthoz való kapcsolódás tesztelése

Ebben a szakaszban az előző lépésben létrehozott virtuális gépet fogja használni, hogy a magánhálózati végponton keresztül csatlakozhasson a Storage-fiókhoz.

1. Válassza az **erőforráscsoportok** lehetőséget a bal oldali navigációs ablaktáblán.

2. Válassza a **myResourceGroup** lehetőséget.

3. Válassza a **myVM**lehetőséget.

4. A **myVM**áttekintés lapján válassza a **kapcsolat** , majd a **megerősített**lehetőséget.

5. Válassza a kék **használat Bastion** gombot.

6. Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót.

7. A kapcsolat után nyissa meg a Windows PowerShellt a kiszolgálón.

8. Írja be a következő szöveget: `nslookup <storage-account-name>.blob.core.windows.net`. Cserélje le az **\<storage-account-name>** nevet az előző lépésekben létrehozott Storage-fiók nevére.  A következőhöz hasonló üzenet jelenik meg:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mystorageaccount8675.privatelink.blob.core.windows.net
    Address:  10.1.0.5
    Aliases:  mystorageaccount8675.blob.core.windows.net
    ```

    A rendszer a **10.1.0.5** magánhálózati IP-címét adja vissza a Storage-fiók nevéhez.  Ez a címe a korábban létrehozott virtuális hálózat alhálózatában található.

9. Telepítse a [Microsoft Azure Storage Explorert](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows) a virtuális gépre.

10. A **Microsoft Azure Storage Explorer** telepítése után válassza a **Befejezés** lehetőséget.  Az alkalmazás megnyitásához hagyja bejelölve a jelölőnégyzetet.

11. A **Kapcsolódás az Azure Storage-hoz** képernyőn válassza **a kapcsolati karakterlánc használata**lehetőséget.

12. Kattintson a **Tovább** gombra.

13. Adja meg a Storage-fiók nevét a **megjelenítendő név**előző lépéseiből.

14. Illessze be a mezőbe a **kapcsolatok karakterlánca**alatt található mezőben az előző lépésekben másolt Storage-fiókból.

15. Kattintson a **Tovább** gombra.

16. Ellenőrizze, hogy helyesek-e a beállítások a **kapcsolatok összegzésében**.  

17. Kattintson a **Csatlakozás** gombra.

18. A **myVM**létesített kapcsolatok lezárása.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, törölje a virtuális hálózatot, a virtuális gépet és a Storage-fiókot a következő lépésekkel:

1. A bal oldali menüben válassza az **erőforráscsoportok**lehetőséget.

2. Válassza a **myResourceGroup** lehetőséget.

3. Válassza az **Erőforráscsoport törlése** elemet.

4. Írja be a **myResourceGroup** **nevet az erőforráscsoport neve mezőbe**.

5. Válassza a **Törlés** elemet.

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan hozhat létre privát hivatkozási szolgáltatást:
> [!div class="nextstepaction"]
> [Privát kapcsolati szolgáltatás létrehozása](create-private-link-service-portal.md)
