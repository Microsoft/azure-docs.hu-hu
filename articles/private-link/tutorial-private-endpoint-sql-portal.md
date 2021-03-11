---
title: 'Oktatóanyag: Kapcsolódás Azure SQL Serverhez Azure-beli magánhálózati végpont használatával – portál'
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egy privát végponttal rendelkező Azure SQL Servert a Azure Portal használatával.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: tutorial
ms.date: 10/20/2020
ms.author: allensu
ms.custom: fasttrack-edit
ms.openlocfilehash: 8bb227bdc4bbcdf986c2ed64e1f89e1dfa289b83
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102554972"
---
# <a name="tutorial-connect-to-an-azure-sql-server-using-an-azure-private-endpoint---azure-portal"></a>Oktatóanyag: Kapcsolódás Azure SQL Serverhez Azure-beli magánhálózati végpont használatával – Azure Portal

Az Azure privát végpontja az Azure-beli privát kapcsolat alapvető építőeleme. Lehetővé teszi az Azure-erőforrások, például a virtuális gépek (VM-EK) számára, hogy magánjellegű módon kommunikáljanak a privát kapcsolati erőforrásokkal.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzon létre egy virtuális hálózatot és egy megerősített gazdagépet.
> * Virtuális gépet hoz létre.
> * Hozzon létre egy Azure SQL-kiszolgálót és egy magánhálózati végpontot.
> * Az SQL Server magánhálózati végpontjának kapcsolatának tesztelése.

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

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
    | Erőforráscsoport   | **CreateSQLEndpointTutorial kiválasztása – RG** |
    | **Példány adatai** |                                                                 |
    | Name             | **MyVNet** megadása                                    |
    | Region           | Válassza ki az **USA keleti** régióját |

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
    | Erőforráscsoport | **CreateSQLEndpointTutorial** kiválasztása |
    | **Példány adatai** |  |
    | Virtuális gép neve | **MyVM** megadása |
    | Region | Válassza ki az **USA keleti** régióját |
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

## <a name="create-an-azure-sql-server-and-private-endpoint"></a><a name ="create-a-private-endpoint"></a>Azure SQL Server-és magánhálózati végpont létrehozása

Ebben a szakaszban egy SQL Server-kiszolgálót fog létrehozni az Azure-ban. 

1. A Azure Portal képernyő bal felső részén válassza az **erőforrás létrehozása**  >  **adatbázisok**  >  **SQL Database** lehetőséget.

1. Az **SQL Database létrehozása** **alapismeretek** lapján adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | **Projekt részletei** | |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válassza a **CreateSQLEndpointTutorial** lehetőséget. Ezt az erőforráscsoportot az előző szakaszban hozta létre.|
    | **Adatbázis adatai** |  |
    | Adatbázis neve  | Adja meg a **mysqldatabase**. Ha ezt a nevet hozza, hozzon létre egy egyedi nevet. |
    | Kiszolgáló | Válassza az **Új létrehozása** lehetőséget. |

6. Az **új kiszolgáló** mezőbe írja be vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | Kiszolgálónév  | Adja meg a **portra beállított mysqlserver**. Ha ezt a nevet hozza, hozzon létre egy egyedi nevet.|
    | Kiszolgáló-rendszergazdai bejelentkezés | Adja meg a választott rendszergazda nevét. |
    | Jelszó | Adjon meg egy tetszőleges jelszót. A jelszónak legalább 8 karakter hosszúnak kell lennie, és meg kell felelnie a meghatározott követelményeknek. |
    | Hely | Válassza ki az **USA keleti** régióját |
    
7. Válassza az **OK** lehetőséget.

8. Válassza a **hálózatkezelés** fület, vagy kattintson a **Tovább: hálózatkezelés** gombra.

9. A **hálózatkezelés** lapon adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | **Hálózati kapcsolat** | |
    | Kapcsolati mód | Válasszon **privát végpontot**. |
   
10. Válassza a **+ privát végpont hozzáadása** **privát végpontok** lehetőséget.

11. A **privát végpont létrehozása** lapon adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válassza a **CreateSQLEndpointTutorial** lehetőséget. |
    | Hely | Válassza az **USA keleti régiója** lehetőséget. |
    | Name | Adja meg a **myPrivateSQLendpoint**. |
    | Célzott alerőforrás | Válassza a **SQLServer** lehetőséget. |
    | **Hálózat** |  |
    | Virtuális hálózat | Válassza a **myVNet** lehetőséget. |
    | Alhálózat | Válassza a **mySubnet** lehetőséget. |
    | **saját DNS integráció** | |
    | Integrálás saját DNS-zónával | Hagyja meg az alapértelmezett **Igen értéket**. |
    | Privát DNS-zóna | Hagyja meg az alapértelmezett **(új) privatelink.database.Windows.net**. |

12. Válassza az **OK** lehetőséget. 

13. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.

14. Válassza a **Létrehozás** lehetőséget.

## <a name="test-connectivity-to-private-endpoint"></a>A magánhálózati végponthoz való kapcsolódás tesztelése

Ebben a szakaszban az előző lépésben létrehozott virtuális gépet fogja használni az SQL Serverhez való kapcsolódáshoz a privát végponton keresztül.

1. Válassza az **erőforráscsoportok** lehetőséget a bal oldali navigációs ablaktáblán.

2. Válassza a **CreateSQLEndpointTutorial** lehetőséget.

3. Válassza a **myVM** lehetőséget.

4. A **myVM** áttekintés lapján válassza a **kapcsolat** , majd a **megerősített** lehetőséget.

5. Válassza a kék **használat Bastion** gombot.

6. Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót.

7. A kapcsolat után nyissa meg a Windows PowerShellt a kiszolgálón.

8. Írja be a következő szöveget: `nslookup <sqlserver-name>.database.windows.net`. A helyére írja **\<sqlserver-name>** be az előző lépésben létrehozott SQL Server nevét.  A következőhöz hasonló üzenet jelenik meg:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mysqlserver8675.privatelink.database.windows.net
    Address:  10.1.0.5
    Aliases:  mysqlserver8675.database.windows.net
    ```

    A rendszer a **10.1.0.5** magánhálózati IP-címét adja vissza az SQL-kiszolgáló nevéhez.  Ez a címe a korábban létrehozott virtuális hálózat alhálózatában található.


9. Telepítse a [SQL Server Management Studiot](/sql/ssms/download-sql-server-management-studio-ssms?preserve-view=true&view=sql-server-2017) a **myVM**.

10. Nyissa meg **SQL Server Management Studio**.

4. A **Kapcsolódás a kiszolgálóhoz** lapon adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | Server type (Kiszolgáló típusa) | Válassza a **Database Engine** (Adatbázismotor) lehetőséget.|
    | Kiszolgálónév | Adja meg a **\<sqlserver-name> . database.Windows.net** |
    | Hitelesítés | Válassza ki az **SQL Server-hitelesítés** lehetőséget. |
    | Felhasználónév | Adja meg a kiszolgáló létrehozásakor megadott felhasználónevet |
    | Jelszó | Adja meg a kiszolgáló létrehozásakor megadott jelszót |
    | Jelszó megjegyzése | Válassza az **Igen** lehetőséget. |

1. Válassza a **Kapcsolódás** lehetőséget.
2. A bal oldali menüben lévő adatbázisok tallózása.
3. Opcionálisan Információk létrehozása vagy lekérdezése a **mysqldatabase**.
4. A távoli asztali kapcsolat bezárásával **myVM**. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 
Ha végzett a magánhálózati végpont, az SQL Server és a virtuális gép használatával, törölje az erőforráscsoportot és az összes benne lévő erőforrást: 
1. Adja meg a **CreateSQLEndpointTutorial** a portál tetején található **keresőmezőbe** , és válassza a **CreateSQLEndpointTutorial** lehetőséget a keresési eredmények közül. 
2. Válassza az **Erőforráscsoport törlése** elemet. 
3. Írja be **a CreateSQLEndpointTutorial nevet az erőforráscsoport neveként** , majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban a következőket hozta létre:

* Virtuális hálózat és a megerősített gazdagép.
* Virtuális gép.
* Azure SQL Server magánhálózati végponttal.

A virtuális gépet arra használta, hogy biztonságosan tesztelje a kapcsolatot az SQL Serverhez a magánhálózati végponton keresztül.

A következő lépésként a webalkalmazás az Azure SQL Database architektúra-forgatókönyvhöz való **privát kapcsolattal** is érdekli, amely a virtuális hálózaton kívül egy webalkalmazást kapcsol össze egy adatbázis privát végpontján.
> [!div class="nextstepaction"]
> [Webalkalmazás az Azure SQL Database szolgáltatáshoz való privát kapcsolattal](/azure/architecture/example-scenario/private-web-app/private-web-app)