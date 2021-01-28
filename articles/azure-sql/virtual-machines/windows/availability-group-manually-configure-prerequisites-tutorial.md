---
title: 'Oktatóanyag: rendelkezésre állási csoport előfeltételei'
description: Ez az oktatóanyag bemutatja, hogyan konfigurálhatja az Azure Virtual Machines SQL Server always on rendelkezésre állási csoport létrehozásához szükséges előfeltételeket.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: c492db4c-3faa-4645-849f-5a1a663be55a
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/29/2018
ms.author: mathoma
ms.custom: seo-lt-2019
ms.openlocfilehash: 869c4ac5cde7d1e50be0f2f738d8a0ce6de5e625
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98951716"
---
# <a name="tutorial-prerequisites-for-creating-availability-groups-on-sql-server-on-azure-virtual-machines"></a>Oktatóanyag: rendelkezésre állási csoportok létrehozásának előfeltételei az Azure-beli SQL Serveron Virtual Machines

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Ez az oktatóanyag bemutatja, hogyan végezheti el az [Azure Virtual Machines (VM) SQL Server always on rendelkezésre állási csoport](availability-group-manually-configure-tutorial.md)létrehozásához szükséges előfeltételeket. Az előfeltételek teljesítése után egy tartományvezérlő, két SQL Server virtuális gép és egy tanúsító kiszolgáló található egyetlen erőforráscsoporthoz.

Habár ez a cikk manuálisan konfigurálja a rendelkezésre állási csoport környezetét, ez a [Azure Portal](availability-group-azure-portal-configure.md), a [PowerShell vagy az Azure CLI](availability-group-az-commandline-configure.md), illetve az Azure-gyorsindítási [sablonok](availability-group-quickstart-template-configure.md) használatával is lehetséges. 

**Becsült idő**: az előfeltételek teljesítéséhez több óráig is eltarthat. Az idő nagy részében a virtuális gépek létrehozása történik.

Az alábbi ábra az oktatóanyagban felépített tudnivalókat mutatja be.

![Rendelkezésre állási csoport](./media/availability-group-manually-configure-prerequisites-tutorial-/00-EndstateSampleNoELB.png)

## <a name="review-availability-group-documentation"></a>A rendelkezésre állási csoport dokumentációjának áttekintése

Ez az oktatóanyag feltételezi, hogy rendelkezik a SQL Server always on rendelkezésre állási csoportokkal kapcsolatos alapvető ismeretekkel. Ha még nem ismeri ezt a technológiát, tekintse [meg az Always On rendelkezésre állási csoportok áttekintése (SQL Server)](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)című témakört.


## <a name="create-an-azure-account"></a>Azure-fiók létrehozása

Rendelkeznie kell Azure-fiókkal. [Megnyithat egy ingyenes Azure-fiókot](https://signup.azure.com/signup?offer=ms-azr-0044p&appId=102&ref=azureplat-generic) , vagy [aktiválhatja a Visual Studio előfizetői előnyeit](/visualstudio/subscriptions/subscriber-benefits).

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Ezzel a beállítással **+** új objektumot hozhat létre a portálon.

   ![Új objektum](./media/availability-group-manually-configure-prerequisites-tutorial-/01-portalplus.png)

3. Írja be az **erőforráscsoport** kifejezést a **piactér** keresési ablakába.

   ![Erőforráscsoport](./media/availability-group-manually-configure-prerequisites-tutorial-/01-resourcegroupsymbol.png)

4. Válassza az **erőforráscsoport** lehetőséget.
5. Válassza a **Létrehozás** lehetőséget.
6. Az **erőforráscsoport neve** alatt adja meg az erőforráscsoport nevét. Írja be például az **SQL-ha-RG** értéket.
7. Ha több Azure-előfizetéssel rendelkezik, ellenőrizze, hogy az előfizetés az Azure-előfizetés, amelyben létre kívánja hozni a rendelkezésre állási csoportot.
8. Válasszon ki egy helyet. A hely az az Azure-régió, ahol létre szeretné hozni a rendelkezésre állási csoportot. Ez a cikk egy Azure-beli hely összes erőforrását felépíti.
9. Ellenőrizze, hogy be van-e jelölve **a rögzítés az irányítópulton** jelölőnégyzet. Ez a választható beállítás a Azure Portal irányítópulton helyezi el az erőforráscsoport parancsikonját.

   ![Erőforráscsoport-parancsikon a Azure Portal](./media/availability-group-manually-configure-prerequisites-tutorial-/01-resourcegroup.png)

10. Válassza a **Létrehozás** lehetőséget az erőforráscsoport létrehozásához.

Az Azure létrehozza az erőforráscsoportot, és a portálon egy parancsikont helyez el az erőforráscsoporthoz.

## <a name="create-the-network-and-subnets"></a>A hálózat és az alhálózatok létrehozása

A következő lépés a hálózatok és alhálózatok létrehozása az Azure-erőforráscsoporthoz.

A megoldás egy virtuális hálózatot használ két alhálózattal. A [virtuális hálózat áttekintése](../../../virtual-network/virtual-networks-overview.md) további információkat nyújt az Azure-beli hálózatokról.

A virtuális hálózat létrehozása a Azure Portalban:

1. Az erőforráscsoporthoz válassza a **+ Hozzáadás** lehetőséget. 

   ![Új tétel](./media/availability-group-manually-configure-prerequisites-tutorial-/02-newiteminrg.png)
2. Keressen rá a **virtuális hálózatra**.

     ![Virtuális hálózat keresése](./media/availability-group-manually-configure-prerequisites-tutorial-/04-findvirtualnetwork.png)
3. Válassza a **virtuális hálózat** lehetőséget.
4. A **virtuális hálózaton** válassza ki a **Resource Manager** -alapú üzemi modellt, majd válassza a **Létrehozás** lehetőséget.

    A következő táblázat a virtuális hálózat beállításait mutatja be:

   | **Mező** | Érték |
   | --- | --- |
   | **Név** |autoHAVNET |
   | **Címtér** |10.0.0.0/24 |
   | **Alhálózat neve** |Rendszergazda |
   | **Alhálózati címtartomány** |10.0.0.0/29 |
   | **Előfizetés** |Itt adhatja meg a használni kívánt előfizetést. Az **előfizetés** üres, ha csak egy előfizetéssel rendelkezik. |
   | **Erőforráscsoport** |Válassza a **meglévő használata** lehetőséget, és válassza ki az erőforráscsoport nevét. |
   | **Hely** |Itt adhatja meg az Azure helyét. |

   A Címterület és az alhálózati címtartomány eltérő lehet a táblából. Az előfizetéstől függően a portálon elérhető címtartomány és a hozzá tartozó alhálózat-címtartomány is megjelenik. Ha nem áll rendelkezésre elegendő címtartomány, használjon másik előfizetést.

   A példa az alhálózati név **rendszergazdáját** használja. Ez az alhálózat a tartományvezérlők számára érhető el.

5. Válassza a **Létrehozás** lehetőséget.

   ![A virtuális hálózat konfigurálása](./media/availability-group-manually-configure-prerequisites-tutorial-/06-configurevirtualnetwork.png)

Az Azure visszatér a portál irányítópultra, és értesítést küld az új hálózat létrehozásakor.

### <a name="create-a-second-subnet"></a>Második alhálózat létrehozása

Az új virtuális hálózat egy **rendszergazda** nevű alhálózattal rendelkezik. A tartományvezérlők ezt az alhálózatot használják. A SQL Server virtuális gépek egy másik, **SQL** nevű alhálózatot használnak. Az alhálózat konfigurálása:

1. Az irányítópulton válassza ki a létrehozott erőforráscsoportot ( **SQL-ha-RG**). Keresse meg a hálózatot az **erőforrás csoportban az erőforráscsoport területen.**

    Ha az **SQL-ha-RG** nem látható, keresse meg az **erőforráscsoportok** kiválasztásával és az erőforráscsoport neve alapján történő szűréssel.

2. Válassza az **autoHAVNET** elemet az erőforrások listájában. 
3. A **autoHAVNET** virtuális hálózat **Beállítások** területén válassza az **alhálózatok** lehetőséget.

    Jegyezze fel a már létrehozott alhálózatot.

   ![Jegyezze fel a már létrehozott alhálózatot](./media/availability-group-manually-configure-prerequisites-tutorial-/07-addsubnet.png)

5. Második alhálózat létrehozásához válassza a **+ alhálózat** lehetőséget.
6. Az **alhálózat hozzáadása** területen konfigurálja az alhálózatot úgy, hogy beírja a **sqlsubnet** **nevet**. Az Azure automatikusan megadja a **címtartomány érvényes tartományát**. Győződjön meg arról, hogy ez a címtartomány legalább 10 címmel rendelkezik. Éles környezetben több címet is igényelhet.
7. Válassza az **OK** lehetőséget.

    ![Alhálózat konfigurálása](./media/availability-group-manually-configure-prerequisites-tutorial-/08-configuresubnet.png)

A következő táblázat összefoglalja a hálózati konfiguráció beállításait:

| **Mező** | Érték |
| --- | --- |
| **Név** |**autoHAVNET** |
| **Címtér** |Ez az érték az előfizetés elérhető címeitől függ. Egy tipikus érték a 10.0.0.0/16. |
| **Alhálózat neve** |**rendszergazda** |
| **Alhálózati címtartomány** |Ez az érték az előfizetés elérhető címeitől függ. Egy tipikus érték a 10.0.0.0/24. |
| **Alhálózat neve** |**sqlsubnet** |
| **Alhálózati címtartomány** |Ez az érték az előfizetés elérhető címeitől függ. Egy tipikus érték a 10.0.1.0/24. |
| **Előfizetés** |Itt adhatja meg a használni kívánt előfizetést. |
| **Erőforráscsoport** |**SQL-HA-RG** |
| **Hely** |Ugyanazt a helyet kell megadnia, amelyet az erőforráscsoport számára választott. |

## <a name="create-availability-sets"></a>Rendelkezésre állási csoportok létrehozása

A virtuális gépek létrehozása előtt létre kell hoznia a rendelkezésre állási csoportokat. A rendelkezésre állási csoportok csökkentik a tervezett vagy nem tervezett karbantartási események leállását. Az Azure-beli rendelkezésre állási csoport az erőforrások olyan logikai csoportja, amelyet az Azure fizikai tartalék tartományokon és frissítési tartományokon helyez el. A tartalék tartomány biztosítja, hogy a rendelkezésre állási csoport tagjai külön energiaellátási és hálózati erőforrásokkal rendelkezzenek. A frissítési tartomány biztosítja, hogy a rendelkezésre állási csoport tagjai ne álljanak le egyszerre karbantartásra. További információk: [Virtuális gépek rendelkezésre állásának kezelése](../../../virtual-machines/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Két rendelkezésre állási csoportra van szüksége. Az egyik a tartományvezérlők számára. A második a SQL Server virtuális gépek esetében.

Rendelkezésre állási csoport létrehozásához nyissa meg az erőforráscsoportot, és válassza a **Hozzáadás** lehetőséget. Az eredmények szűréséhez írja **be a rendelkezésre állási csoport** kifejezést. Válassza az eredmények között a **rendelkezésre állási csoport** lehetőséget, majd válassza a **Létrehozás** lehetőséget.

Konfigurálja a két rendelkezésre állási csoportot a következő táblázatban szereplő paramétereknek megfelelően:

| **Mező** | Tartományvezérlő rendelkezésre állási készlete | Rendelkezésre állási csoport SQL Server |
| --- | --- | --- |
| **Név** |adavailabilityset |sqlavailabilityset |
| **Erőforráscsoport** |SQL-HA-RG |SQL-HA-RG |
| **Tartalék tartományok** |3 |3 |
| **Frissítési tartományok** |5 |3 |

A rendelkezésre állási csoportok létrehozása után térjen vissza az erőforráscsoporthoz a Azure Portal.

## <a name="create-domain-controllers"></a>Tartományvezérlők létrehozása

Miután létrehozta a hálózatot, az alhálózatokat és a rendelkezésre állási csoportokat, készen áll a tartományvezérlők virtuális gépei létrehozására.

### <a name="create-virtual-machines-for-the-domain-controllers"></a>Virtuális gépek létrehozása a tartományvezérlők számára

A tartományvezérlők létrehozásához és konfigurálásához térjen vissza az **SQL-ha-RG** erőforráscsoporthoz.

1. Válassza a **Hozzáadás** elemet. 
2. Írja be a **Windows Server 2016 datacentert**.
3. Válassza a **Windows Server 2016 Datacenter** lehetőséget. A **Windows Server 2016 Datacenter** rendszerben ellenőrizze, hogy a telepítési modell **Resource Manager**-e, majd válassza a **Létrehozás** lehetőséget. 

Két virtuális gép létrehozásához ismételje meg a fenti lépéseket. Nevezze el a két virtuális gépet:

* ad-Primary-DC
* ad-másodlagos-tartományvezérlő

  > [!NOTE]
  > Az **ad-másodlagos-tartományvezérlő** virtuális gép nem kötelező, hogy magas rendelkezésre állást biztosítson Active Directory Domain Services számára.
  >

A következő táblázat a két gép beállításait mutatja be:

| **Mező** | Érték |
| --- | --- |
| **Név** |Első tartományvezérlő: *ad-Primary-DC*.</br>Második tartományvezérlő *ad-másodlagos-tartományvezérlő*. |
| **Virtuális merevlemez típusa** |SSD |
| **Felhasználónév** |Rdfe |
| **Jelszó** |Contoso! 0000 |
| **Előfizetés** |*Az előfizetése* |
| **Erőforráscsoport** |SQL-HA-RG |
| **Hely** |*Tartózkodási hely* |
| **Méret** |DS1_V2 |
| **Storage** | **Felügyelt lemezek használata**  -  **Igen** |
| **Virtuális hálózat** |autoHAVNET |
| **Alhálózat** |felügyelet |
| **Nyilvános IP-cím** |*A virtuális géppel megegyező név* |
| **Hálózati biztonsági csoport** |*A virtuális géppel megegyező név* |
| **Rendelkezésre állási csoport** |adavailabilityset </br>Tartalék **tartományok**: 2 </br>**Frissítési tartományok**: 2|
| **Diagnosztika** |Engedélyezve |
| **Diagnosztikai Storage-fiók** |*Automatikusan létrehozva* |

   >[!IMPORTANT]
   >A virtuális gépeket csak akkor helyezheti üzembe a rendelkezésre állási csoportba, ha létrehozza azt. A rendelkezésre állási csoport a virtuális gép létrehozása után nem módosítható. Lásd: [virtuális gépek rendelkezésre állásának kezelése](../../../virtual-machines/manage-availability.md).

Az Azure létrehozza a virtuális gépeket.

A virtuális gépek létrehozása után konfigurálja a tartományvezérlőt.

### <a name="configure-the-domain-controller"></a>A tartományvezérlő konfigurálása

A következő lépésekben konfigurálja az **ad-Primary-DC** gépet a Corp.contoso.com tartományvezérlőként.

1. A portálon nyissa meg az **SQL-ha-RG** erőforráscsoportot, és válassza ki az **ad-Primary-DC** gépet. Az **ad-Primary-DC-** ben válassza a **Kapcsolódás** lehetőséget egy RDP-fájl megnyitásához a távoli asztal eléréséhez.

    ![Csatlakozás virtuális géphez](./media/availability-group-manually-configure-prerequisites-tutorial-/20-connectrdp.png)

2. Jelentkezzen be a konfigurált rendszergazdai fiókjával (**\DomainAdmin**) és jelszavával (**contoso! 0000**).
3. Alapértelmezés szerint a **Kiszolgálókezelő** irányítópultjának kell megjelennie.
4. Válassza a **szerepkörök és szolgáltatások hozzáadása** hivatkozást az irányítópulton.

    ![Kiszolgálókezelő – Szerepkörök hozzáadása](./media/availability-group-manually-configure-prerequisites-tutorial-/22-addfeatures.png)

5. Kattintson a **tovább** gombra, amíg el nem jut a **kiszolgálói szerepkörök** szakaszhoz.
6. Válassza ki a **Active Directory Domain Services** és a **DNS-kiszolgálói** szerepköröket. Ha a rendszer kéri, adja meg a szerepkörökhöz szükséges további szolgáltatásokat.

   > [!NOTE]
   > A Windows arra figyelmeztet, hogy nincs statikus IP-cím. Ha teszteli a konfigurációt, válassza a **Folytatás** lehetőséget. Éles környezetekben állítsa az IP-címet statikusra a Azure Portalban, vagy a [PowerShell használatával állítsa be a tartományvezérlő számítógépének statikus IP-címét](/previous-versions/azure/virtual-network/virtual-networks-reserved-private-ip).
   >

    ![Szerepkörök hozzáadása párbeszédpanel](./media/availability-group-manually-configure-prerequisites-tutorial-/23-addroles.png)

7. Kattintson a **Tovább gombra** , amíg el nem éri a **megerősítő** szakaszt. Jelölje be a **célkiszolgáló automatikus újraindítása, ha szükséges** jelölőnégyzetet.
8. Válassza a **Telepítés** gombot.
9. A szolgáltatások telepítésének befejezését követően térjen vissza a **Kiszolgálókezelő** irányítópultra.
10. Válassza az új **AD DS** lehetőséget a bal oldali ablaktáblán.
11. A sárga figyelmeztető sávban válassza a **továbbiak** hivatkozást.

    ![AD DS párbeszédpanel a DNS-kiszolgáló virtuális gépén](./media/availability-group-manually-configure-prerequisites-tutorial-/24-addsmore.png)
    
12. A **minden kiszolgáló feladat részletei** párbeszédpanel **művelet** oszlopában válassza a **kiszolgáló előléptetése tartományvezérlővé** lehetőséget.
13. A **Active Directory Domain Services konfigurációs varázslóban** a következő értékeket használja:

    | **Oldal** | Beállítás |
    | --- | --- |
    | **Központi telepítés konfigurálása** |**Új erdő hozzáadása**<br/> **Gyökértartomány neve** = Corp.contoso.com |
    | **Tartományvezérlő beállításai** |**Címtárszolgáltatások helyreállító módjának jelszava** = contoso! 0000<br/>**Jelszó megerősítése** = contoso! 0000 |

14. A **tovább** gombra kattintva megtekintheti a varázsló többi lapját. Az **Előfeltételek ellenőrzése** lapon győződjön meg arról, hogy a következő üzenet jelenik meg: az **összes előfeltétel-ellenőrzés sikeresen átadva**. Áttekintheti a vonatkozó figyelmeztető üzeneteket, de lehetséges, hogy folytathatja a telepítést.
15. Válassza a **Telepítés** gombot. Az **ad-Primary-DC** virtuális gép automatikusan újraindul.

### <a name="note-the-ip-address-of-the-primary-domain-controller"></a>Jegyezze fel az elsődleges tartományvezérlő IP-címét.

Használja a DNS elsődleges tartományvezérlőjét. Jegyezze fel az elsődleges tartományvezérlő IP-címét.

Az elsődleges tartományvezérlő IP-címének lekérésének egyik módja a Azure Portalon keresztül történik.

1. A Azure Portal nyissa meg az erőforráscsoportot.

2. Válassza ki az elsődleges tartományvezérlőt.

3. Az elsődleges tartományvezérlőn válassza a **hálózati adapterek** lehetőséget.

![Hálózati adapterek](./media/availability-group-manually-configure-prerequisites-tutorial-/25-primarydcip.png)

Jegyezze fel a kiszolgáló magánhálózati IP-címét.

### <a name="configure-the-virtual-network-dns"></a>A virtuális hálózat DNS-beli konfigurálása

Miután létrehozta az első tartományvezérlőt, és engedélyezte a DNS-t az első kiszolgálón, konfigurálja a virtuális hálózatot a DNS-kiszolgáló használatára.

1. A Azure Portal válassza a virtuális hálózat lehetőséget.

2. A **Beállítások** területen válassza a **DNS-kiszolgáló** lehetőséget.

3. Válassza az **Egyéni** lehetőséget, majd írja be az elsődleges tartományvezérlő magánhálózati IP-címét.

4. Kattintson a **Mentés** gombra.

### <a name="configure-the-second-domain-controller"></a>A második tartományvezérlő konfigurálása

Az elsődleges tartományvezérlő újraindítása után beállíthatja a második tartományvezérlőt. Ez a választható lépés a magas rendelkezésre állás. A második tartományvezérlő konfigurálásához kövesse az alábbi lépéseket:

1. A portálon nyissa meg az **SQL-ha-RG** erőforráscsoportot, és válassza ki az **ad-szekunder-DC** gépet. Az **ad-másodlagos-tartományvezérlőn** válassza a **Kapcsolódás** lehetőséget egy RDP-fájl megnyitásához a távoli asztal eléréséhez.
2. Jelentkezzen be a virtuális gépre a beállított rendszergazdai fiók (**BUILTIN\DomainAdmin**) és a jelszó (**contoso! 0000**) használatával.
3. Módosítsa az előnyben részesített DNS-kiszolgáló címeit a tartományvezérlő címeként.
4. A **hálózati és megosztási központban** válassza ki a hálózati adaptert.

   ![Hálózati adapter](./media/availability-group-manually-configure-prerequisites-tutorial-/26-networkinterface.png)

5. Válassza ki a **Tulajdonságok** elemet.
6. Válassza a **Internet Protocol 4-es verzió (TCP/IPv4)** lehetőséget, majd válassza a **Tulajdonságok** lehetőséget.
7. Válassza **a következő DNS-kiszolgáló címek használata** lehetőséget, majd adja meg az elsődleges tartományvezérlő címét az **ELŐNYben részesített DNS-kiszolgálón**.
8. Kattintson **az OK gombra**, majd a **Bezárás** gombra a módosítások elvégzéséhez. Most már tud csatlakozni a virtuális géphez a **Corp.contoso.com**.

   >[!IMPORTANT]
   >Ha a DNS-beállítás módosítása után elveszíti a távoli asztal kapcsolatát, lépjen a Azure Portal, és indítsa újra a virtuális gépet.

9. A Távoli asztalról a másodlagos tartományvezérlőre nyissa meg a **Kiszolgálókezelő irányítópultját**.
10. Válassza a **szerepkörök és szolgáltatások hozzáadása** hivatkozást az irányítópulton.

    ![Kiszolgálókezelő – Szerepkörök hozzáadása](./media/availability-group-manually-configure-prerequisites-tutorial-/22-addfeatures.png)
11. Kattintson a **tovább** gombra, amíg el nem jut a **kiszolgálói szerepkörök** szakaszhoz.
12. Válassza ki a **Active Directory Domain Services** és a **DNS-kiszolgálói** szerepköröket. Ha a rendszer kéri, adja meg a szerepkörökhöz szükséges további szolgáltatásokat.
13. A szolgáltatások telepítésének befejezését követően térjen vissza a **Kiszolgálókezelő** irányítópultra.
14. Válassza az új **AD DS** lehetőséget a bal oldali ablaktáblán.
15. A sárga figyelmeztető sávban válassza a **továbbiak** hivatkozást.
16. A **minden kiszolgáló feladat részletei** párbeszédpanel **művelet** oszlopában válassza a **kiszolgáló előléptetése tartományvezérlővé** lehetőséget.
17. A **központi telepítés konfigurálása** területen válassza **a tartományvezérlő hozzáadása meglévő tartományhoz** lehetőséget.

    ![Központi telepítés konfigurálása](./media/availability-group-manually-configure-prerequisites-tutorial-/28-deploymentconfig.png)

18. Kattintson a **Kiválasztás** elemre.
19. Kapcsolódjon a rendszergazdai fiók használatával (**Corp. CONTOSO. COM\domainadmin**) és jelszó (**contoso! 0000**).
20. A **tartomány kiválasztása az erdőből** területen válassza ki a tartományt, majd kattintson **az OK gombra**.
21. A **tartományvezérlő beállításainál** használja az alapértelmezett értékeket, és állítsa be a Címtárszolgáltatások helyreállító módjának jelszavát.

    >[!NOTE]
    >Előfordulhat, hogy a **DNS-beállítások** lap figyelmezteti, hogy nem hozható létre delegálás ehhez a DNS-kiszolgálóhoz. Ezt a figyelmeztetést nem éles környezetben is figyelmen kívül hagyhatja.
    >

22. Kattintson a **Tovább gombra** , amíg a párbeszédpanel el nem éri az **Előfeltételek** ellenőrzését. Ezután válassza a **telepítés** lehetőséget.

Miután a kiszolgáló befejezte a konfigurációs módosításokat, indítsa újra a kiszolgálót.

### <a name="add-the-private-ip-address-to-the-second-domain-controller-to-the-vpn-dns-server"></a>Adja hozzá a magánhálózati IP-címet a második tartományvezérlőhöz a VPN DNS-kiszolgálóhoz

A Azure Portal virtuális hálózat területén módosítsa a DNS-kiszolgálót úgy, hogy az tartalmazza a másodlagos tartományvezérlő IP-címét. Ezzel a beállítással engedélyezhető a DNS-szolgáltatás redundancia.

### <a name="configure-the-domain-accounts"></a><a name="DomainAccounts"></a> A tartományi fiókok konfigurálása

A következő lépésekben konfigurálja a Active Directory fiókokat. A következő táblázat a fiókokat mutatja be:

| |Telepítési fiók<br/> |SQLServer – 0 <br/>SQL Server és SQL Agent-szolgáltatásfiók |SQLServer – 1<br/>SQL Server és SQL Agent-szolgáltatásfiók
| --- | --- | --- | ---
|**Keresztnév** |Telepítés |SQLSvc1 | SQLSvc2
|**Felhasználói SamAccountName** |Telepítés |SQLSvc1 | SQLSvc2

Az egyes fiókok létrehozásához kövesse az alábbi lépéseket.

1. Jelentkezzen be az **ad-Primary-DC** gépre.
2. A **Kiszolgálókezelőben** válassza az **eszközök**, majd a **Active Directory felügyeleti központ** lehetőséget.   
3. Válassza a **Corp (helyi)** elemet a bal oldali ablaktáblán.
4. A jobb oldali **feladatok** ablaktáblán válassza az **új**, majd a **felhasználó** lehetőséget.

   ![Active Directory felügyeleti központ](./media/availability-group-manually-configure-prerequisites-tutorial-/29-addcnewuser.png)

   >[!TIP]
   >Minden fiókhoz állítson be egy összetett jelszót.<br/> Nem éles környezetekben állítsa be a felhasználói fiókot, hogy soha ne járjon le.
   >

5. A felhasználó létrehozásához kattintson **az OK gombra** .
6. Ismételje meg az előző lépéseket mindhárom fiók esetében.

### <a name="grant-the-required-permissions-to-the-installation-account"></a>A szükséges engedélyek megadása a telepítési fiók számára

1. A **Active Directory felügyeleti központ** válassza a **Corp (helyi)** elemet a bal oldali ablaktáblán. Ezután a jobb oldali **feladatok** ablaktáblán válassza a **Tulajdonságok** lehetőséget.

    ![CORP-felhasználó tulajdonságai](./media/availability-group-manually-configure-prerequisites-tutorial-/31-addcproperties.png)

2. Válassza a **bővítmények** lehetőséget, majd a **Biztonság** lapon kattintson a **speciális** gombra.
3. A **Corp speciális biztonsági beállításai** párbeszédpanelen válassza a **Hozzáadás** lehetőséget.
4. Kattintson **a rendszerbiztonsági tag kiválasztása** elemre, keresse meg a **CORP\Install**, majd válassza **az OK** gombot.
5. Jelölje be az **összes tulajdonság olvasása** jelölőnégyzetet.

6. Jelölje be a **számítógép-objektumok létrehozása** jelölőnégyzetet.

     ![Corp felhasználói engedélyek](./media/availability-group-manually-configure-prerequisites-tutorial-/33-addpermissions.png)

7. Válassza az **OK**, majd újra az **OK** elemet. A **Corp** Properties ablak bezárásához.

Most, hogy befejezte a Active Directory és a felhasználói objektumok konfigurálását, hozzon létre két SQL Server virtuális gépet és egy tanúsító kiszolgáló virtuális gépet. Ezután csatlakozzon mindhárom tartományhoz.

## <a name="create-sql-server-vms"></a>SQL Server virtuális gépek létrehozása

Hozzon létre három további virtuális gépet. A megoldáshoz két virtuális gép szükséges SQL Server példányokkal. Egy harmadik virtuális gép tanúsító fog működni. A Windows Server 2016 [Felhőbeli tanúsító](/windows-server/failover-clustering/deploy-cloud-witness)is használhat. A korábbi operációs rendszerekkel való konzisztencia azonban ez a cikk egy tanúsító virtuális gépet használ.  

Az alábbi tervezési döntések meghozatala előtt tekintse át a következő lépéseket.

* **Storage – Azure Managed Disks**

   A virtuális gép tárolójában használja az Azure Managed Disks. A Microsoft a SQL Server virtuális gépek Managed Disksét javasolja. A Managed Disks szolgáltatás a háttérben kezeli a tárterületet. Emellett ha ugyanabban a rendelkezésre állási csoportban több, a Managed Diskset használó virtuális gép található, az Azure elosztja a tárolási erőforrásokat, hogy megfelelő redundanciát biztosítson. További információkért lásd az [Azure Managed Disks áttekintését](../../../virtual-machines/managed-disks-overview.md). A felügyelt lemezekkel kapcsolatos részletekért tekintse meg a [rendelkezésre állási csoportokban található virtuális gépek Managed Disks használatát](../../../virtual-machines/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set)ismertető témakört.

* **Hálózat – magánhálózati IP-címek az éles környezetben**

   A virtuális gépek esetében ez az oktatóanyag nyilvános IP-címeket használ. A nyilvános IP-cím lehetővé teszi, hogy a távoli kapcsolat közvetlenül a virtuális géphez legyen elérhető az interneten keresztül, és egyszerűbbé teszi a konfigurációs lépéseket. Éles környezetekben a Microsoft csak a magánhálózati IP-címeket javasolja, hogy csökkentse a SQL Server példány virtuálisgép-erőforrásának sebezhetőségi lábnyomát.

* **Hálózat – kiszolgálónként egyetlen NIC-t javasoljon** 

Egyetlen NIC-kiszolgálót (fürtcsomópont) és egyetlen alhálózatot használjon. Az Azure hálózatkezelésének fizikai redundancia van, így a további hálózati adapterek és alhálózatok szükségtelenek az Azure-beli virtuális gépek vendég fürtjében. A fürt ellenőrzési jelentése figyelmezteti, hogy a csomópontok csak egyetlen hálózaton érhetők el. Ezt a figyelmeztetést figyelmen kívül hagyhatja az Azure-beli virtuális gépek vendég feladatátvevő fürtökön.

### <a name="create-and-configure-the-sql-server-vms"></a>A SQL Server virtuális gépek létrehozása és konfigurálása

Ezután hozzon létre három virtuális gépet – két SQL Server virtuális gépet és egy virtuális gépet egy további fürtcsomópont számára. Az egyes virtuális gépek létrehozásához térjen vissza az **SQL-ha-RG** erőforráscsoporthoz, majd válassza a **Hozzáadás** lehetőséget. Keresse meg a megfelelő gyűjtemény elemet, válassza a **virtuális gép** lehetőséget, majd válassza **a** katalógusból lehetőséget. A következő táblázatban található információk segítségével hozhatja létre a virtuális gépeket:


| Oldal | VM1 | VM2 | VM3 |
| --- | --- | --- | --- |
| Válassza ki a megfelelő gyűjtemény elemet |**Windows Server 2016 Datacenter** |**SQL Server 2016 SP1 Enterprise Windows Server 2016 rendszeren** |**SQL Server 2016 SP1 Enterprise Windows Server 2016 rendszeren** |
| A virtuális gép konfigurációjának **alapjai** |**Név** = fürt – FSW<br/>**Felhasználónév** = rdfe<br/>**Password** = contoso! 0000<br/>**Előfizetés** = az előfizetése<br/>**Erőforráscsoport** = SQL-ha-RG<br/>**Location** = az Azure-beli helye |**Név** = SQLServer-0<br/>**Felhasználónév** = rdfe<br/>**Password** = contoso! 0000<br/>**Előfizetés** = az előfizetése<br/>**Erőforráscsoport** = SQL-ha-RG<br/>**Location** = az Azure-beli helye |**Név** = SQLServer-1<br/>**Felhasználónév** = rdfe<br/>**Password** = contoso! 0000<br/>**Előfizetés** = az előfizetése<br/>**Erőforráscsoport** = SQL-ha-RG<br/>**Location** = az Azure-beli helye |
| Virtuális gép konfigurációjának **mérete** |**Size** = DS1 \_ v2 (1 VCPU, 3,5 GB) |**Size** = DS2 \_ v2 (2 VCPU, 7 GB)</br>A méretnek támogatnia kell az SSD-tárolót (prémium szintű lemezes támogatás). )) |**Size** = DS2 \_ v2 (2 VCPU, 7 GB) |
| Virtuális gép konfigurációs **beállításai** |**Storage**: felügyelt lemezek használata.<br/>**Virtuális hálózat** = autoHAVNET<br/>**Alhálózat** = sqlsubnet (10.1.1.0/24)<br/>A **nyilvános IP-cím** automatikusan létrejön.<br/>**Hálózati biztonsági csoport** = nincs<br/>**Figyelési diagnosztika** = engedélyezve<br/>**Diagnosztikai Storage-fiók** = automatikusan létrehozott Storage-fiók használata<br/>**Rendelkezésre állási csoport** = sqlAvailabilitySet<br/> |**Storage**: felügyelt lemezek használata.<br/>**Virtuális hálózat** = autoHAVNET<br/>**Alhálózat** = sqlsubnet (10.1.1.0/24)<br/>A **nyilvános IP-cím** automatikusan létrejön.<br/>**Hálózati biztonsági csoport** = nincs<br/>**Figyelési diagnosztika** = engedélyezve<br/>**Diagnosztikai Storage-fiók** = automatikusan létrehozott Storage-fiók használata<br/>**Rendelkezésre állási csoport** = sqlAvailabilitySet<br/> |**Storage**: felügyelt lemezek használata.<br/>**Virtuális hálózat** = autoHAVNET<br/>**Alhálózat** = sqlsubnet (10.1.1.0/24)<br/>A **nyilvános IP-cím** automatikusan létrejön.<br/>**Hálózati biztonsági csoport** = nincs<br/>**Figyelési diagnosztika** = engedélyezve<br/>**Diagnosztikai Storage-fiók** = automatikusan létrehozott Storage-fiók használata<br/>**Rendelkezésre állási csoport** = sqlAvailabilitySet<br/> |
| A virtuális gép konfigurációjának **SQL Server beállításai** |Nem alkalmazható |**SQL-kapcsolat** = Private (Virtual Networkon belül)<br/>**Port** = 1433<br/>**SQL-hitelesítés** = letiltás<br/>**Tárolási konfiguráció** = általános<br/>**Automatikus javítás** = vasárnap 2:00-kor<br/>**Automatikus biztonsági mentés** = letiltva</br>**Azure Key Vault integráció** = letiltva |**SQL-kapcsolat** = Private (Virtual Networkon belül)<br/>**Port** = 1433<br/>**SQL-hitelesítés** = letiltás<br/>**Tárolási konfiguráció** = általános<br/>**Automatikus javítás** = vasárnap 2:00-kor<br/>**Automatikus biztonsági mentés** = letiltva</br>**Azure Key Vault integráció** = letiltva |

<br/>

> [!NOTE]
> Az itt javasolt gépi méretek a rendelkezésre állási csoportok tesztelésére szolgálnak az Azure Virtual Machinesban. Az éles számítási feladatokhoz szükséges legjobb teljesítmény érdekében tekintse meg az [Virtual Machines Azure-beli SQL Server teljesítményének](performance-guidelines-best-practices.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)SQL Server a gépek méretének és konfigurálásának ajánlott eljárásai című témakört.
>

A három virtuális gép teljes kiépítés után csatlakoztatnia kell őket a **Corp.contoso.com** tartományhoz, és a CORP\Install rendszergazdai jogosultságokat kell biztosítania a gépekhez.

### <a name="join-the-servers-to-the-domain"></a><a name="joinDomain"></a>A kiszolgálók csatlakoztatása a tartományhoz

Most már csatlakoztathatja a virtuális gépeket a **Corp.contoso.com**-hez. Hajtsa végre a következő lépéseket a SQL Server virtuális gépek és a tanúsító fájlmegosztás kiszolgálója között:

1. Távolról csatlakozhat a virtuális géphez a **BUILTIN\DomainAdmin** használatával.
2. A **Kiszolgálókezelőben** válassza a **helyi kiszolgáló** lehetőséget.
3. Válassza ki a **munkacsoport** hivatkozást.
4. A **számítógép neve** szakaszban válassza a **módosítás** lehetőséget.
5. Jelölje be a **tartomány** jelölőnégyzetet, és írja be a **Corp.contoso.com** szöveget a szövegmezőbe. Válassza az **OK** lehetőséget.
6. A **Windows biztonsági** előugró ablakban határozza meg az alapértelmezett tartományi rendszergazdai fiók (**CORP\DomainAdmin**) és a jelszó (**contoso! 0000**) hitelesítő adatait.
7. Amikor megjelenik az "üdvözli a corp.contoso.com tartomány" üzenet, kattintson az **OK gombra**.
8. Válassza a **Bezárás** lehetőséget, majd az előugró ablakban válassza az **Újraindítás most** lehetőséget.

## <a name="add-accounts"></a>Fiókok hozzáadása

Adja hozzá a telepítési fiókot rendszergazdaként az egyes virtuális gépeken, adjon engedélyt a telepítési fióknak és a helyi fiókoknak SQL Serveron belül, és frissítse a SQL Server szolgáltatásfiókot. 

### <a name="add-the-corpinstall-user-as-an-administrator-on-each-cluster-vm"></a>A Corp\Install-felhasználó hozzáadása rendszergazdaként az egyes fürtökön futó virtuális gépeken

Miután minden virtuális gép újraindult a tartomány tagjaként, adja hozzá a **CORP\Install** -t a helyi Rendszergazdák csoport tagjaként.

1. Várjon, amíg a virtuális gép újraindul, majd indítsa el újra az RDP-fájlt az elsődleges tartományvezérlőről, hogy bejelentkezzen a **SQLServer-0** értékre a **CORP\DomainAdmin** -fiók használatával.

   >[!TIP]
   >Győződjön meg arról, hogy a tartományi rendszergazda fiókkal jelentkezik be. Az előző lépésekben a beépített rendszergazdai fiókot használta. Most, hogy a kiszolgáló a tartományban van, használja a tartományi fiókot. Az RDP-munkamenetben adja meg a *tartomány* \\ *felhasználónevét*.
   >

2. A **Kiszolgálókezelőben** válassza az **eszközök**, majd a számítógép- **kezelés** lehetőséget.
3. A **Számítógép-kezelés** ablakban bontsa ki a **helyi felhasználók és csoportok** csomópontot, majd válassza a **csoportok** lehetőséget.
4. Kattintson duplán a **rendszergazdák** csoportra.
5. A **rendszergazdák tulajdonságai** párbeszédpanelen kattintson a **Hozzáadás** gombra.
6. Adja meg a felhasználó **CORP\Install**, majd kattintson **az OK gombra**.
7. A **rendszergazda tulajdonságok** párbeszédpanel bezárásához kattintson **az OK gombra** .
8. Ismételje meg az előző lépéseket a **SQLServer-1** és a **cluster-FSW**.


### <a name="create-a-sign-in-on-each-sql-server-vm-for-the-installation-account"></a>Bejelentkezés létrehozása minden SQL Server VM a telepítési fiókhoz

A rendelkezésre állási csoport konfigurálásához használja a telepítési fiókot (CORP\install). Ennek a fióknak a **sysadmin (rendszergazda** ) rögzített kiszolgálói szerepkör tagjának kell lennie minden SQL Server VMon. A következő lépésekkel hozzon létre egy bejelentkezési fiókot a telepítési fiókhoz:

1. Kapcsolódjon a kiszolgálóhoz a RDP protokollon (RDP) keresztül a *\<MachineName\> \DomainAdmin* fiók használatával.

1. Nyissa meg SQL Server Management Studio, és kapcsolódjon a SQL Server helyi példányához.

1. A **Object Explorer** területen válassza a **Biztonság** elemet.

1. Kattintson a jobb gombbal a **bejelentkezések** elemre. Válassza az **új bejelentkezés** lehetőséget.

1. A **Bejelentkezés – új** területen válassza a **Keresés** lehetőséget.

1. Válasszon **helyet**.

1. Adja meg a tartományi rendszergazda hálózati hitelesítő adatait.

1. Használja a telepítési fiókot (CORP\install).

1. A bejelentkezést úgy állítsa be, hogy a sysadmin ( **rendszergazda** ) rögzített kiszolgálói szerepkör tagja legyen.

1. Válassza az **OK** lehetőséget.

Ismételje meg a fenti lépéseket a másik SQL Server VM.

### <a name="configure-system-account-permissions"></a>Rendszerfiók engedélyeinek konfigurálása

Hozzon létre egy fiókot a rendszerfiókhoz, és adja meg a megfelelő engedélyeket, hajtsa végre az alábbi lépéseket minden SQL Server példányon:

1. Hozzon létre egy fiókot az `[NT AUTHORITY\SYSTEM]` egyes SQL Server-példányokhoz. A következő szkript hozza létre ezt a fiókot:

   ```sql
   USE [master]
   GO
   CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS WITH DEFAULT_DATABASE=[master]
   GO 
   ```

1. Adja meg a következő engedélyeket az `[NT AUTHORITY\SYSTEM]` egyes SQL Server példányokon:

   - `ALTER ANY AVAILABILITY GROUP`
   - `CONNECT SQL`
   - `VIEW SERVER STATE`

   A következő parancsfájl megadja ezeket az engedélyeket:

   ```sql
   GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM]
   GO
   GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM]
   GO
   GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM]
   GO 
   ```

### <a name="set-the-sql-server-service-accounts"></a><a name="setServiceAccount"></a>A SQL Server-szolgáltatásfiókok beállítása

Az egyes SQL Server VMeken állítsa be a SQL Server szolgáltatásfiókot. A tartományi fiókok konfigurálásakor létrehozott fiókokat használja.

1. Nyissa meg az **SQL Server Configuration Manager** eszközt.
2. Kattintson a jobb gombbal a SQL Server szolgáltatásra, majd válassza a **Tulajdonságok parancsot**.
3. Állítsa be a fiókot és a jelszót.
4. Ismételje meg ezeket a lépéseket a többi SQL Server VM.  

SQL Server rendelkezésre állási csoportok esetében minden SQL Server VM tartományi fiókként kell futnia.

## <a name="add-failover-clustering-features-to-both-sql-server-vms"></a>Feladatátvételi fürtszolgáltatási funkciók hozzáadása SQL Server virtuális gépekhez

Feladatátvételi fürtszolgáltatási funkciók hozzáadásához hajtsa végre a következő lépéseket mindkét SQL Server virtuális gépen:

1. Kapcsolódjon a SQL Server virtuális géphez a RDP protokoll (RDP) segítségével a *CORP\install* fiók használatával. Nyissa meg a **Kiszolgálókezelő irányítópultját**.
2. Válassza a **szerepkörök és szolgáltatások hozzáadása** hivatkozást az irányítópulton.

    ![Kiszolgálókezelő – Szerepkörök hozzáadása](./media/availability-group-manually-configure-prerequisites-tutorial-/22-addfeatures.png)

3. Kattintson a **tovább** gombra, amíg el nem jut a **kiszolgálói funkciók** szakaszhoz.
4. A **szolgáltatások** területen válassza a **feladatátvételi fürtszolgáltatás** lehetőséget.
5. Adja hozzá a további szükséges szolgáltatásokat.
6. A szolgáltatások hozzáadásához válassza a **telepítés** lehetőséget.

Ismételje meg a lépéseket a többi SQL Server VMon.

  >[!NOTE]
  > Ez a lépés, valamint a SQL Server virtuális gépek a feladatátvevő fürthöz való tényleges csatlakoztatása mostantól automatizálható az [Azure SQL VM CLI](./availability-group-az-commandline-configure.md) -vel és az Azure-beli [Gyorsindítás sablonokkal](availability-group-quickstart-template-configure.md).
  >

### <a name="tuning-failover-cluster-network-thresholds"></a>Feladatátvevő fürt hálózati küszöbértékének finomhangolása

Ha SQL Server rendelkezésre állási csoportokkal rendelkező Azure-beli virtuális gépeken futtatja a Windows feladatátvevő fürt csomópontjait, módosítsa a fürt beállítását egy nyugodtabb figyelési állapotra.  Ez sokkal stabilabb és megbízhatóbb lesz a fürt számára.  További részletekért lásd: [IaaS a feladatátvételi fürt hálózati küszöbértékek SQL Server-hangolásával](/windows-server/troubleshoot/iaas-sql-failover-cluster).


## <a name="configure-the-firewall-on-each-sql-server-vm"></a><a name="endpoint-firewall"></a> A tűzfal konfigurálása minden SQL Server VM

A megoldáshoz a következő TCP-portokat kell megnyitni a tűzfalon:

- **SQL Server VM**: a SQL Server alapértelmezett példányának 1433-as portja.
- **Azure Load Balancer** -mintavétel: Bármely elérhető port. A példák gyakran használják a 59999-ot.
- **Adatbázis-tükrözési végpont:** Bármely elérhető port. A példák gyakran használják a 5022-ot.

A tűzfal portjait mindkét SQL Server virtuális gépen meg kell nyitni.

A portok megnyitásának módszere a használt tűzfal megoldástól függ. A következő szakasz azt ismerteti, hogyan nyithatók meg a portok a Windows tűzfalban. Nyissa meg a szükséges portokat az egyes SQL Server virtuális gépeken.

### <a name="open-a-tcp-port-in-the-firewall"></a>TCP-port megnyitása a tűzfalon

1. Az első SQL Server **kezdőképernyőn** indítsa el a **fokozott biztonságú Windows tűzfalat**.
2. A bal oldali ablaktáblán válassza a **Bejövő szabályok** lehetőséget. A jobb oldali ablaktáblán válassza az **új szabály** lehetőséget.
3. A **szabálytípus** mezőben válassza a **port** lehetőséget.
4. A port mezőben adja meg a **TCP** értéket, és írja be a megfelelő portszámokat. Lásd a következő példát:

   ![SQL tűzfal](./media/availability-group-manually-configure-prerequisites-tutorial-/35-tcpports.png)

5. Kattintson a **Tovább** gombra.
6. A **művelet** lapon tartsa be **a kijelölt kapcsolat lehetőséget** , majd kattintson a **tovább** gombra.
7. A **profil** lapon fogadja el az alapértelmezett beállításokat, majd kattintson a **tovább** gombra.
8. A **név** lapon adja meg a szabály nevét (például az **Azure LB**-mintavételt) a **név** szövegmezőben, majd válassza a **Befejezés** lehetőséget.

Ismételje meg ezeket a lépéseket a második SQL Server VM.


## <a name="next-steps"></a>Következő lépések

* [SQL Server always on rendelkezésre állási csoport létrehozása az Azure-ban Virtual Machines](availability-group-manually-configure-tutorial.md)
