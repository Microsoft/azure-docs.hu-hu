---
title: Privát hivatkozás – Azure Portal – Azure Database for MySQL
description: Megtudhatja, hogyan konfigurálhatja a Azure Database for MySQLhoz tartozó magánhálózati hivatkozást Azure Portal
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 01/09/2020
ms.openlocfilehash: ce916336ea47cd223c10a8f664b2dc9806ed0a17
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2021
ms.locfileid: "99221026"
---
# <a name="create-and-manage-private-link-for-azure-database-for-mysql-using-portal"></a>Azure Database for MySQL privát hivatkozás létrehozása és kezelése a portál használatával

A privát végpont az Azure-beli privát kapcsolat alapvető építőeleme. Lehetővé teszi az Azure-erőforrások, például a Virtual Machines (VM-EK) számára, hogy magánjellegű módon kommunikáljanak a privát kapcsolati erőforrásokkal. Ebből a cikkből megtudhatja, hogyan hozhat létre virtuális gépet egy Azure-Virtual Networkban és egy Azure-beli privát végponttal rendelkező Azure Database for MySQL-kiszolgálón a Azure Portal használatával.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!NOTE]
> A privát hivatkozás funkció csak a általános célú vagy a memória optimalizált árképzési szintjein Azure Database for MySQL-kiszolgálókon érhető el. Győződjön meg arról, hogy az adatbázis-kiszolgáló ezen díjszabási szintek egyikében található.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba
Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="create-an-azure-vm"></a>Azure-beli virtuális gép létrehozása

Ebben a szakaszban létre fog hozni egy virtuális hálózatot és az alhálózatot, amely a privát kapcsolati erőforráshoz (az Azure-ban MySQL-kiszolgálóhoz) való hozzáféréshez használt virtuális GÉPET üzemelteti.

### <a name="create-the-virtual-network"></a>A virtuális hálózat létrehozása
Ebben a szakaszban létre fog hozni egy Virtual Network és egy alhálózatot, amely a privát kapcsolati erőforrás eléréséhez használt virtuális gépet üzemelteti.

1. A képernyő bal felső részén válassza az **erőforrás létrehozása**  >  **hálózatkezelés**  >  **virtuális hálózat** lehetőséget.
2. A **virtuális hálózat létrehozása** lapon adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | Név | Adja meg a *MyVirtualNetwork*. |
    | Címtér | Adja meg a *10.1.0.0/16* értéket. |
    | Előfizetés | Válassza ki előfizetését.|
    | Erőforráscsoport | Válassza az **új létrehozása** elemet, írja be a *myResourceGroup*, majd kattintson **az OK gombra**. |
    | Hely | Válassza a **Nyugat-Európa** régiót.|
    | Alhálózat – név | Adja meg a *mySubnet*. |
    | Alhálózat – címtartomány | Adja meg a *10.1.0.0/24* értéket. |
    |||
3. Hagyja a többi értéket alapértelmezettként, és válassza a **Létrehozás** lehetőséget.

### <a name="create-virtual-machine"></a>Virtuális gép létrehozása

1. A Azure Portal képernyő bal felső részén válassza az **erőforrás létrehozása**  >  **számítási**  >  **virtuális gép** lehetőséget.

2. A **virtuális gép létrehozása – alapismeretek** területen adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | **PROJEKT RÉSZLETEI** | |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válassza a **myResourceGroup** lehetőséget. Ezt az előző szakaszban hozta létre.  |
    | **PÉLDÁNY RÉSZLETEI** |  |
    | Virtuális gép neve | Adja meg a *myVm*. |
    | Régió | Válassza a **Nyugat-Európa** régiót. |
    | Rendelkezésre állási beállítások | Az alapértelmezett **infrastruktúra-redundancia megadása nem kötelező**. |
    | Kép | Válassza a **Windows Server 2019 Datacenter** lehetőséget. |
    | Méret | Hagyja meg az alapértelmezett **standard DS1 v2** értéket. |
    | **RENDSZERGAZDAFIÓK** |  |
    | Felhasználónév | Adja meg a választott felhasználónevet. |
    | Jelszó | Adjon meg egy tetszőleges jelszót. A jelszónak legalább 12 karakter hosszúnak kell lennie, és meg kell felelnie a [meghatározott összetettségi követelményeknek](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Jelszó megerősítése | Adja meg újra a jelszót. |
    | **BEJÖVŐPORT-SZABÁLYOK** |  |
    | Nyilvános bejövő portok | Hagyja meg az alapértelmezett **nincs** értéket. |
    | **MEGTAKARÍTÁSI LEHETŐSÉG** |  |
    | Már van Windows-licence? | Hagyja meg az alapértelmezett **nem** értéket. |
    |||

1. Válassza a **Tovább: lemezek** lehetőséget.

1. A **Virtuális gép létrehozása – Lemezek** lehetőségnél hagyja meg az alapértelmezett értékeket, és válassza a **Tovább: Hálózatkezelés** lehetőséget.

1. A **virtuálisgép-hálózat létrehozása** területen válassza ki ezt az információt:

    | Beállítás | Érték |
    | ------- | ----- |
    | Virtuális hálózat | Hagyja meg az alapértelmezett **MyVirtualNetwork**.  |
    | Címtér | Hagyja meg az alapértelmezett **10.1.0.0/24** értéket.|
    | Alhálózat | Hagyja meg az alapértelmezett **mySubnet (10.1.0.0/24)**.|
    | Nyilvános IP-cím | Hagyja meg az alapértelmezett **(új) myVm-IP-címet**. |
    | Nyilvános bejövő portok | Válassza a **kiválasztott portok engedélyezése** lehetőséget. |
    | Válassza ki a bejövő portokat | Válassza a **http** és az **RDP** lehetőséget.|
    |||


1. Válassza az **Áttekintés + létrehozás** lehetőséget. Az **Áttekintés és létrehozása** lapra kerül, ahol az Azure érvényesíti az Ön konfigurációját.

1. Amikor megjelenik a **Megfelelt az ellenőrzésen** üzenet, válassza a **Létrehozás** lehetőséget.

## <a name="create-an-azure-database-for-mysql"></a>Azure Database for MySQL létrehozása

Ebben a szakaszban egy Azure Database for MySQL-kiszolgálót fog létrehozni az Azure-ban. 

1. A Azure Portal képernyő bal felső részén válassza az **erőforrás létrehozása**  >  **adatbázisok**  >  **Azure Database for MySQL** lehetőséget.

1. A **Azure Database for MySQL** adja meg a következő információkat:

    | Beállítás | Érték |
    | ------- | ----- |
    | **Projekt részletei** | |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válassza a **myResourceGroup** lehetőséget. Ezt az előző szakaszban hozta létre.|
    | **Kiszolgáló adatai** |  |
    |Kiszolgálónév  | Adja meg a *myServer*. Ha ezt a nevet hozza, hozzon létre egy egyedi nevet.|
    | Rendszergazdai felhasználónév| Adja meg a választott rendszergazda nevét. |
    | Jelszó | Adjon meg egy tetszőleges jelszót. A jelszónak legalább 8 karakter hosszúnak kell lennie, és meg kell felelnie a meghatározott követelményeknek. |
    | Hely | Válassza ki azt az Azure-régiót, ahol a MySQL-kiszolgálót szeretné tárolni. |
    |Verzió  | Válassza ki a szükséges MySQL-kiszolgáló adatbázis-verzióját.|
    | Számítás és tárolás| Válassza ki a kiszolgálón a munkaterhelés alapján szükséges díjszabási szintet. |
    |||
 
7. Válassza az **OK** lehetőséget. 
8. Válassza az **Áttekintés + létrehozás** lehetőséget. Az **Áttekintés és létrehozása** lapra kerül, ahol az Azure érvényesíti az Ön konfigurációját. 
9. Amikor megjelenik az átadott üzenet ellenőrzése lehetőség, válassza a **Létrehozás** lehetőséget. 
10. Amikor megjelenik az átadott üzenet ellenőrzése lehetőség, válassza a létrehozás lehetőséget. 

> [!NOTE]
> Bizonyos esetekben a Azure Database for MySQL és a VNet különböző előfizetésekben találhatók. Ezekben az esetekben a következő konfigurációkat kell biztosítania:
> - Győződjön meg arról, hogy mindkét előfizetés regisztrálva van a **Microsoft. DBforMySQL** erőforrás-szolgáltatónál. További információ: [Resource-Manager-regisztráció][resource-manager-portal]

## <a name="create-a-private-endpoint"></a>Privát végpont létrehozása

Ebben a szakaszban létre fog hozni egy MySQL-kiszolgálót, és hozzá kell adnia egy privát végpontot. 

1. A Azure Portal képernyő bal felső részén válassza az **erőforrás létrehozása**  >  **hálózati**  >  **privát hivatkozás** lehetőséget.

2. A **Private link Centerben – áttekintés**, a **szolgáltatáshoz való magánhálózati kapcsolat** létrehozásához válassza az **Indítás** lehetőséget.

    :::image type="content" source="media/concepts-data-access-and-security-private-link/privatelink-overview.png" alt-text="Privát hivatkozás áttekintése":::

1. A **privát végpont létrehozása – alapismeretek** területen adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | **Projekt részletei** | |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válassza a **myResourceGroup** lehetőséget. Ezt az előző szakaszban hozta létre.|
    | **Példány részletei** |  |
    | Name | Adja meg a *myPrivateEndpoint* nevet. Ha ezt a nevet hozza, hozzon létre egy egyedi nevet. |
    |Régió|Válassza a **Nyugat-Európa** régiót.|
    |||

5. Válassza a **Tovább: erőforrás** elemet.
6. A **privát végpont létrehozása – erőforrás** területen adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    |Kapcsolati módszer  | Válassza a kapcsolódás egy Azure-erőforráshoz a címtárban lehetőséget.|
    | Előfizetés| Válassza ki előfizetését. |
    | Erőforrás típusa | Válassza a **Microsoft. DBforMySQL/kiszolgálók** lehetőséget. |
    | Erőforrás |*MyServer* kiválasztása|
    |Célzott alerőforrás |*Portra beállított mysqlserver* kiválasztása|
    |||
7. Válassza a **Tovább: konfigurálás** lehetőséget.
8. A **privát végpont létrehozása – konfiguráció** területen adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    |**HÁLÓZATKEZELÉS**| |
    | Virtuális hálózat| Válassza a *MyVirtualNetwork* lehetőséget. |
    | Alhálózat | Válassza a *mySubnet* lehetőséget. |
    |**PRIVÁT DNS-INTEGRÁCIÓ**||
    |Integrálás saját DNS-zónával |Válassza az **Igen** lehetőséget. |
    |Privát DNS-zóna |Válassza az *(új) privatelink. mysql. database. Azure. com* elemet |
    |||

    > [!Note] 
    > Használja az előre definiált saját DNS-zónát a szolgáltatáshoz, vagy adja meg a kívánt DNS-zóna nevét. A részletekért tekintse meg az [Azure-szolgáltatások DNS-zóna konfigurációját](../private-link/private-endpoint-dns.md) .

1. Válassza az **Áttekintés + létrehozás** lehetőséget. Az **Áttekintés és létrehozása** lapra kerül, ahol az Azure érvényesíti az Ön konfigurációját. 
2. Amikor megjelenik a **Megfelelt az ellenőrzésen** üzenet, válassza a **Létrehozás** lehetőséget. 

    :::image type="content" source="media/concepts-data-access-and-security-private-link/show-mysql-private-link.png" alt-text="Saját hivatkozás létrehozva":::

    > [!NOTE] 
    > Az ügyfél DNS-beállításában lévő teljes tartománynév nem oldható fel a magánhálózati IP-címekre konfigurálva. Az [itt](../dns/dns-operations-recordsets-portal.md)látható módon konfigurálnia kell egy DNS-zónát a beállított FQDN-hez.

## <a name="connect-to-a-vm-using-remote-desktop-rdp"></a>Kapcsolódás a virtuális géphez Távoli asztal (RDP) használatával


A **myVm** létrehozása után az alábbi módon csatlakozhat az internetről: 

1. A portál keresési sávjába írja be a *myVm* szöveget.

1. Kattintson a **Csatlakozás** gombra. A **Kapcsolódás** gombra kattintva megnyílik a **virtuális géphez való kapcsolódás** .

1. Válassza az **RDP-fájl letöltése** lehetőséget. Az Azure létrehoz egy RDP protokoll (*. rdp*) fájlt, és letölti a számítógépre.

1. Nyissa meg a *letöltött. rdp* fájlt.

    1. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget.

    1. Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót.

        > [!NOTE]
        > Előfordulhat, hogy a   >  virtuális gép létrehozásakor megadott hitelesítő adatok megadásához több választási lehetőséget kell választania **egy másik fiók használatával**.

1. Válassza az **OK** lehetőséget.

1. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Ha a tanúsítvány figyelmeztetést kap, válassza az **Igen** vagy a **Folytatás** lehetőséget.

1. Ha megjelenik a virtuális gép asztala, csökkentse a helyi asztalra való visszatérést.

## <a name="access-the-mysql-server-privately-from-the-vm"></a>A MySQL-kiszolgáló elérése a virtuális gépről

1. A *myVM* távoli asztal nyissa meg a PowerShellt.

2. Adja meg a értéket  `nslookup  myServer.privatelink.mysql.database.azure.com` . 

    Egy ehhez hasonló üzenet jelenik meg:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myServer.privatelink.mysql.database.azure.com
    Address:  10.1.3.4
    ```
    > [!NOTE]
    > Ha a nyilvános hozzáférés le van tiltva a tűzfal beállításai között Azure Database for MySQL-egyetlen kiszolgálón. Ezek a ping és Telnet tesztek a tűzfal beállításaitól függetlenül sikeresek lesznek. Ezek a tesztek biztosítják a hálózati kapcsolatot.

3. Tesztelje a MySQL-kiszolgáló magánhálózati kapcsolati kapcsolatát bármely elérhető ügyfél használatával. Az alábbi példában a [MySQL Workbench](https://dev.mysql.com/doc/workbench/en/wb-installing-windows.html) használatával végeztem el a műveletet.

4. Az **új kapcsolatok** területen adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | Server type (Kiszolgáló típusa)| Válassza a **MySQL** lehetőséget.|
    | Kiszolgálónév| *MyServer.privatelink.mysql.database.Azure.com* kiválasztása |
    | Felhasználónév | Adja meg a felhasználónevet, username@servername amelyet a MySQL-kiszolgáló létrehozásakor adott meg. |
    |Jelszó |Adja meg a MySQL-kiszolgáló létrehozásakor megadott jelszót. |
    |SSL|Válassza a **kötelező** lehetőséget.|
    ||

5. Válassza a Csatlakozás lehetőséget.

6. A bal oldali menüben lévő adatbázisok tallózása.

7. Opcionálisan Információk létrehozása vagy lekérdezése a MySQL-kiszolgálóról.

8. A távoli asztali kapcsolat bezárásával myVm.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha végzett a magánhálózati végpont, a MySQL-kiszolgáló és a virtuális gép használatával, törölje az erőforráscsoportot és az összes benne lévő erőforrást:

1. Adja meg a *myResourceGroup* a portál tetején található **keresőmezőbe** , és válassza a *myResourceGroup* lehetőséget a keresési eredmények közül.
2. Válassza az **Erőforráscsoport törlése** elemet.
3. Írja be **a myResourceGroup nevet az erőforráscsoport neveként** , majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebben az útmutatóban létrehozott egy virtuális GÉPET egy virtuális hálózaton, egy Azure Database for MySQLt és egy privát végpontot a privát eléréshez. Az internetről csatlakozik egy virtuális géphez, és biztonságosan kommunikál a MySQL-kiszolgálóval a privát hivatkozás használatával. További információ a privát végpontokról: [Mi az az Azure Private Endpoint](../private-link/private-endpoint-overview.md).

<!-- Link references, to text, Within this same GitHub repo. -->
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md