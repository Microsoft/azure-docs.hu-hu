---
title: 'Oktatóanyag: SQL Server always on rendelkezésre állási csoport konfigurálása'
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre SQL Server always on rendelkezésre állási csoportot az Azure Virtual Machines-on.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: 08a00342-fee2-4afe-8824-0db1ed4b8fca
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/30/2018
ms.author: mathoma
ms.custom: seo-lt-2019
ms.openlocfilehash: feab48f32396bcc89621433930c9a9f4689d8286
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97355443"
---
# <a name="tutorial-manually-configure-an-availability-group-sql-server-on-azure-vms"></a>Oktatóanyag: rendelkezésre állási csoport manuális konfigurálása (SQL Server Azure-beli virtuális gépeken)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Ez az oktatóanyag bemutatja, hogyan hozhat létre always on rendelkezésre állási csoportot az Azure Virtual Machines (VM) SQL Serverához. A teljes oktatóanyag egy rendelkezésre állási csoportot hoz létre két SQL Server adatbázis-replikával.

Habár ez a cikk manuálisan konfigurálja a rendelkezésre állási csoport környezetét, ez a [Azure Portal](availability-group-azure-portal-configure.md), a [PowerShell vagy az Azure CLI](availability-group-az-commandline-configure.md), illetve az Azure-gyorsindítási [sablonok](availability-group-quickstart-template-configure.md) használatával is lehetséges. 


**Becsült időtartam**: az [Előfeltételek](availability-group-manually-configure-prerequisites-tutorial.md) teljesítése után körülbelül 30 percet vesz igénybe.


## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag feltételezi, hogy rendelkezik SQL Server always on rendelkezésre állási csoportokkal kapcsolatos alapvető ismeretekkel. Ha további információra van szüksége, tekintse [meg az Always On rendelkezésre állási csoportok áttekintése (SQL Server)](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)című témakört.

Az oktatóanyag megkezdése előtt be kell [fejeznie az Always On rendelkezésre állási csoportok létrehozásához szükséges előfeltételeket az Azure Virtual Machinesban](availability-group-manually-configure-prerequisites-tutorial.md). Ha ezek az előfeltételek már befejeződtek, ugorjon a [fürt létrehozása](#CreateCluster)lehetőségre.

A következő táblázat felsorolja azokat az előfeltételeket, amelyeket el kell végeznie az oktatóanyag megkezdése előtt:

| Követelmény |Leírás |
|----- |----- |----- |
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **Két SQL Server példány** | – Azure-beli rendelkezésre állási csoport <br/> -Egyetlen tartományban <br/> -A feladatátvételi fürtszolgáltatás telepített funkciója |
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **Windows Server** | Fájlmegosztás a tanúsító fürthöz |  
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **SQL Server szolgáltatásfiók** | Tartományi fiók |
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **SQL Server Agent szolgáltatásfiók** | Tartományi fiók |  
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **A tűzfal portjai nyitva vannak** | -SQL Server: **1433** az alapértelmezett példányhoz <br/> – Adatbázis-tükrözési végpont: **5022** vagy bármely elérhető port <br/> -Rendelkezésre állási csoport terheléselosztó IP-címe állapotának mintavétele: **59999** vagy bármely elérhető port <br/> – A fürt alapvető terheléselosztó IP-címe állapotának mintavétele: **58888** vagy bármely elérhető port |
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **Feladatátvételi fürtszolgáltatás hozzáadása** | Mindkét SQL Server példánynak szüksége van erre a szolgáltatásra |
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **Telepítési tartományi fiók** | -Helyi rendszergazda az egyes SQL Servereken <br/> -SQL Server sysadmin (rendszergazda) rögzített kiszolgálói szerepkör tagja SQL Server egyes példányaihoz  |

>[!NOTE]
> Az oktatóanyagban ismertetett lépések közül sokat automatizálhat a [Azure Portal](availability-group-azure-portal-configure.md), [a PowerShell és az az az CLI](./availability-group-az-commandline-configure.md) és az [Azure gyorsindítási sablonok](availability-group-quickstart-template-configure.md)használatával.


<!--**Procedure**: *This is the first "step". Make titles H2's and short and clear – H2's appear in the right pane on the web page and are important for navigation.*-->

<a name="CreateCluster"></a>

## <a name="create-the-cluster"></a>A fürt létrehozása

Az előfeltételek befejezését követően az első lépés egy Windows Server feladatátvevő fürt létrehozása, amely két SQL-kiszolgálót és egy tanúsító kiszolgálót tartalmaz.

1. A RDP protokoll (RDP) használatával csatlakozzon az első SQL Serverhoz. Olyan tartományi fiókot használjon, amely az SQL-kiszolgálók és a tanúsító kiszolgáló rendszergazdája.

   >[!TIP]
   >Ha követte az [előfeltételeket tartalmazó dokumentumot](availability-group-manually-configure-prerequisites-tutorial.md), létrehozott egy **CORP\Install** nevű fiókot. Használja ezt a fiókot.

2. A **Kiszolgálókezelő** irányítópultján válassza az **eszközök**, majd a **Feladatátvevőfürt-kezelő** lehetőséget.
3. A bal oldali ablaktáblán kattintson a jobb gombbal a **Feladatátvevőfürt-kezelő** elemre, majd válassza **a fürt létrehozása** lehetőséget.

   ![Fürt létrehozása](./media/availability-group-manually-configure-tutorial/40-createcluster.png)

4. A fürt létrehozása varázslóban hozzon létre egy egycsomópontos fürtöt a következő táblázatban található beállításokkal:

   | Oldal | Beállítások |
   | --- | --- |
   | Előkészületek |Alapértelmezett beállítások használata |
   | Kiszolgálók kiválasztása |A Kiszolgálónév mezőbe írja be az első SQL Server nevet **, majd válassza** a **Hozzáadás** lehetőséget. |
   | Érvényesítési figyelmeztetés |Válassza a **nem lehetőséget. nem igényelem a Microsoft támogatását ehhez a fürthöz, ezért nem kívánja futtatni az ellenőrző teszteket. A Tovább gombra kattintva folytassa a fürt létrehozását**. |
   | Hozzáférési pont a fürt felügyeletéhez |Adja meg a fürt nevét, például **SQLAGCluster1** a **fürt nevében**.|
   | Visszaigazolás |Csak akkor használja az alapértelmezett értékeket, ha tárolóhelyeket használ. Lásd a táblázatot követő megjegyzést. |

### <a name="set-the-windows-server-failover-cluster-ip-address"></a>A Windows Server feladatátvételi fürt IP-címének beállítása

  > [!NOTE]
  > A Windows Server 2019 rendszeren a fürt egy **elosztott kiszolgálónevet** hoz létre a **fürt hálózati neve** helyett. Ha a Windows Server 2019-et használja, ugorja át az oktatóanyagban szereplő, a fürt alapvető nevére vonatkozó lépéseket. Fürt hálózati nevét a [PowerShell](failover-cluster-instance-storage-spaces-direct-manually-configure.md#create-failover-cluster)használatával hozhatja létre. További információért tekintse át a következőt [: fürt hálózati objektuma](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97) . 

1. A **Feladatátvevőfürt-kezelőban** görgessen le a **fürt alapvető erőforrásai** elemre, és bontsa ki a fürt részleteit. A **hibás** állapotú **nevet** és az **IP-cím** erőforrásait is látnia kell. Az IP-cím erőforrás nem hozható online állapotba, mert a fürt ugyanahhoz az IP-címhez van hozzárendelve, mint maga a gép, ezért ez egy duplikált cím.

2. Kattintson a jobb gombbal a sikertelen **IP-cím** erőforrásra, majd válassza a **Tulajdonságok parancsot**.

   ![Fürt tulajdonságai](./media/availability-group-manually-configure-tutorial/42_IPProperties.png)

3. Válassza a **statikus IP-cím** lehetőséget, és adjon meg egy elérhető címet a virtuális gépekkel azonos alhálózaton.

4. A **fürt alapvető erőforrásai** szakaszban kattintson a jobb gombbal a fürt nevére, majd válassza az **online állapotba hozás** lehetőséget. Várjon, amíg mindkét erőforrás online állapotú. Amikor a fürt neve erőforrás online állapotba kerül, frissíti a tartományvezérlő (DC) kiszolgálót egy új Active Directory (AD) számítógépfiók megadásával. Ezt az AD-fiókot használhatja a rendelkezésre állási csoport fürtözött szolgáltatásának későbbi futtatásához.

### <a name="add-the-other-sql-server-to-cluster"></a><a name="addNode"></a>A másik SQL Server hozzáadása a fürthöz

Adja hozzá a másik SQL Server a fürthöz.

1. A böngésző fáján kattintson a jobb gombbal a fürtre, és válassza a **Csomópont hozzáadása** lehetőséget.

    ![Csomópont hozzáadása a fürthöz](./media/availability-group-manually-configure-tutorial/44-addnode.png)

1. A **Csomópont hozzáadása varázslóban** válassza a **tovább** lehetőséget. A **kiszolgálók kiválasztása** lapon adja hozzá a második SQL Server. A Kiszolgálónév mezőbe írja be **a kiszolgáló nevét, majd** válassza a **Hozzáadás** lehetőséget. Ha elkészült, kattintson a **Tovább gombra**.

1. Az **érvényesítési figyelmeztetés** lapon válassza a **nem** lehetőséget (éles környezetben végezze el az ellenőrzési teszteket). Ezután válassza a **tovább** lehetőséget.

8. Ha tárolóhelyeket használ a **megerősítő** lapon, törölje a jelet az összes megfelelő **tároló hozzáadása a fürthöz** feliratú jelölőnégyzetből.

   ![Csomópont-megerősítés hozzáadása](./media/availability-group-manually-configure-tutorial/46-addnodeconfirmation.png)

   >[!WARNING]
   >Ha tárolóhelyeket használ, és nem törli **az összes megfelelő tároló hozzáadása a fürthöz** lehetőséget, a Windows leválasztja a virtuális lemezeket a fürtözési folyamat során. Ennek eredményeképpen azok nem jelennek meg a Lemezkezelésben vagy az Intézőben, amíg a tárolóhelyek el nem távolítják a fürtöt, és a PowerShell használatával újra hozzá vannak rendelve. A tárolóhelyek több lemezt is csoportosítanak a Storage-készletekbe. További információ: [tárolóhelyek](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831739(v=ws.11)).
   >

1. Kattintson a **Tovább** gombra.

1. Válassza a **Befejezés** gombot.

   Feladatátvevőfürt-kezelő azt mutatja, hogy a fürt új csomóponttal rendelkezik, és a **csomópontok** tárolóban listázza azt.

10. Jelentkezzen ki a távoli asztali munkamenetből.

### <a name="add-a-cluster-quorum-file-share"></a>Fürt Kvórumának fájlmegosztás hozzáadása

Ebben a példában a Windows-fürt fájlmegosztást használ a fürt Kvórumának létrehozásához. Ez az oktatóanyag egy csomópont-és fájlmegosztás-többségi kvórumot használ. További információ: [A feladatátvevő fürtök kvórumkonfigurációinak ismertetése](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731739(v=ws.11)).

1. Kapcsolódjon a tanúsító fájlmegosztás-tagkiszolgáló távoli asztali munkamenettel.

1. A **Kiszolgálókezelőben** válassza az **eszközök** elemet. Nyissa meg a **Számítógép-kezelés** programot.

1. Válassza a **megosztott mappák** lehetőséget.

1. Kattintson a jobb gombbal a **megosztások** elemre, és válassza az **új megosztás...** lehetőséget.

   ![Kattintson a jobb gombbal a Megosztások elemre, és válassza az új megosztás](./media/availability-group-manually-configure-tutorial/48-newshare.png)

   Megosztás létrehozásához használja **a megosztott mappa létrehozása varázslót** .

1. A **mappa elérési útja** lapon válassza a **Tallózás** lehetőséget, és keresse meg vagy hozza létre a megosztott mappa elérési útját. Kattintson a **Tovább** gombra.

1. A **név, leírás és beállítások területen** ellenőrizze a megosztás nevét és elérési útját. Kattintson a **Tovább** gombra.

1. A **megosztott mappa engedélyeinek** beállítása **testreszabja az engedélyeket**. Válassza az **Egyéni...** lehetőséget.

1. Az **engedélyek testreszabása** lapon válassza a **Hozzáadás...** lehetőséget.

1. Győződjön meg arról, hogy a fürt létrehozásához használt fiók teljes hozzáféréssel rendelkezik.

   ![Győződjön meg arról, hogy a fürt létrehozásához használt fiók teljes körű vezérléssel rendelkezik](./media/availability-group-manually-configure-tutorial/50-filesharepermissions.png)

1. Válassza az **OK** lehetőséget.

1. A **megosztott mappák engedélyei** területen válassza a **Befejezés** lehetőséget. Kattintson a **Befejezés gombra** .  

1. Kijelentkezés a kiszolgálóról

### <a name="configure-the-cluster-quorum"></a>A fürtkvórum konfigurálása

Ezután állítsa be a fürt kvórumát.

1. Kapcsolódjon az első fürtcsomópont-csomóponthoz a távoli asztal használatával.

1. **Feladatátvevőfürt-kezelő** kattintson a jobb gombbal a fürtre, mutasson a **További műveletek** pontra, majd válassza a **fürt kvórum beállításainak konfigurálása..**. lehetőséget.

   ![Válassza a fürt kvórum beállításainak konfigurálása lehetőséget.](./media/availability-group-manually-configure-tutorial/52-configurequorum.png)

1. A **fürt Kvórumának konfigurálása varázslóban** kattintson a **Tovább gombra**.

1. A **kvórum konfigurációjának kiválasztása beállításnál** válassza **a tanúsító kvórum kijelölése** lehetőséget, majd kattintson a **Tovább gombra**.

1. A **kvórum tanúsító kijelölése** lapon válassza **a tanúsító fájlmegosztás konfigurálása** lehetőséget.

   >[!TIP]
   >A Windows Server 2016 támogatja a Felhőbeli tanúkat. Ha ezt a típusú tanúsító választja, nincs szükség a tanúsító fájlmegosztás megosztására. További információ: [Felhőbeli tanúsító üzembe helyezése feladatátvevő fürtön](/windows-server/failover-clustering/deploy-cloud-witness). Ez az oktatóanyag egy tanúsító fájlmegosztást használ, amelyet a korábbi operációs rendszerek támogatnak.
   >

1. A **tanúsító fájlmegosztás konfigurálása** területen adja meg a létrehozott megosztás elérési útját. Kattintson a **Tovább** gombra.

1. Ellenőrizze a beállításokat a **megerősítéshez**. Kattintson a **Tovább** gombra.

1. Válassza a **Befejezés** gombot.

A fürt alapvető erőforrásai egy tanúsító fájlmegosztás használatára vannak konfigurálva.

## <a name="enable-availability-groups"></a>Rendelkezésre állási csoportok engedélyezése

Ezután engedélyezze az **AlAlwaysOnon rendelkezésre állási csoportok** funkciót. Hajtsa végre ezeket a lépéseket mindkét SQL Serveren.

1. A **kezdőképernyőn** indítsa el a **SQL Server konfigurációkezelő**.
2. A böngésző fáján válassza a **SQL Server szolgáltatások** elemet, majd kattintson a jobb gombbal a **SQL Server (MSSQLSERVER)** szolgáltatásra, és válassza a **Tulajdonságok** lehetőséget.
3. Válassza a **AlwaysOn magas rendelkezésre állása** lapot, majd válassza az **alAlwaysOnon rendelkezésre állási csoportok engedélyezése** lehetőséget az alábbiak szerint:

    ![AlAlwaysOnon rendelkezésre állási csoportok engedélyezése](./media/availability-group-manually-configure-tutorial/54-enableAlwaysOn.png)

4. Kattintson az **Alkalmaz** gombra. A felugró párbeszédpanelen kattintson az **OK gombra** .

5. Indítsa újra az SQL Server szolgáltatást.

Ismételje meg ezeket a lépéseket a többi SQL Server.

<!-----------------
## <a name="endpoint-firewall"></a>Open firewall for the database mirroring endpoint

Each instance of SQL Server that participates in an availability group requires a database mirroring endpoint. This endpoint is a TCP port for the instance of SQL Server that is used to synchronize the database replicas in the availability groups on that instance.

On both SQL Servers, open the firewall for the TCP port for the database mirroring endpoint.

1. On the first SQL Server **Start** screen, launch **Windows Firewall with Advanced Security**.
2. In the left pane, select **Inbound Rules**. On the right pane, select **New Rule**.
3. For **Rule Type**, choose **Port**.
1. For the port, specify TCP and choose an unused TCP port number. For example, type *5022* and select **Next**.

   >[!NOTE]
   >For this example, we're using TCP port 5022. You can use any available port.

5. In the **Action** page, keep **Allow the connection** selected and select **Next**.
6. In the **Profile** page, accept the default settings and select **Next**.
7. In the **Name** page, specify a rule name, such as **Default Instance Mirroring Endpoint** in the **Name** text box, then select **Finish**.

Repeat these steps on the second SQL Server.
-------------------------->

## <a name="create-a-database-on-the-first-sql-server"></a>Adatbázis létrehozása az első SQL Server

1. Indítsa el az RDP-fájlt az első SQL Server egy olyan tartományi fiókkal, amely tagja a sysadmin rögzített kiszolgálói szerepkörnek.
1. Nyissa meg SQL Server Management Studio és kapcsolódjon az első SQL Serverhoz.
7. **Object Explorer** kattintson a jobb gombbal az **adatbázisok** elemre, és válassza az **új adatbázis** lehetőséget.
8. Az **adatbázis neve** mezőbe írja be a **MyDB1** nevet, majd kattintson **az OK gombra**.

### <a name="create-a-backup-share"></a><a name="backupshare"></a> Biztonsági másolat megosztásának létrehozása

1. A **Kiszolgálókezelő** első SQL Server válassza az **eszközök** elemet. Nyissa meg a **Számítógép-kezelés** programot.

1. Válassza a **megosztott mappák** lehetőséget.

1. Kattintson a jobb gombbal a **megosztások** elemre, és válassza az **új megosztás...** lehetőséget.

   ![Új megosztás kiválasztása](./media/availability-group-manually-configure-tutorial/48-newshare.png)

   Megosztás létrehozásához használja **a megosztott mappa létrehozása varázslót** .

1. A **mappa elérési útja** lapon válassza a **Tallózás** lehetőséget, és keresse meg vagy hozza létre az adatbázis biztonsági másolatának megosztott mappájának elérési útját. Kattintson a **Tovább** gombra.

1. A **név, leírás és beállítások területen** ellenőrizze a megosztás nevét és elérési útját. Kattintson a **Tovább** gombra.

1. A **megosztott mappa engedélyeinek** beállítása **testreszabja az engedélyeket**. Válassza az **Egyéni...** lehetőséget.

1. Az **engedélyek testreszabása** lapon válassza a **Hozzáadás...** lehetőséget.

1. Győződjön meg arról, hogy mindkét kiszolgáló SQL Server és SQL Server Agent szolgáltatás fiókja teljes hozzáféréssel rendelkezik.

   ![Győződjön meg arról, hogy mindkét kiszolgáló SQL Server és SQL Server Agent szolgáltatás fiókja teljes hozzáféréssel rendelkezik.](./media/availability-group-manually-configure-tutorial/68-backupsharepermission.png)

1. Válassza az **OK** lehetőséget.

1. A **megosztott mappák engedélyei** területen válassza a **Befejezés** lehetőséget. Kattintson a **Befejezés gombra** .  

### <a name="take-a-full-backup-of-the-database"></a>Készítsen teljes biztonsági másolatot az adatbázisról

A naplózási lánc inicializálásához biztonsági mentést kell készítenie az új adatbázisról. Ha nem készít biztonsági másolatot az új adatbázisról, azt nem lehet felvenni egy rendelkezésre állási csoportba.

1. A **Object Explorerban** kattintson a jobb gombbal az adatbázisra, mutasson a **feladatok...** lehetőségre, és válassza a **biztonsági mentés** lehetőséget.

1. Az **OK** gombra kattintva teljes biztonsági mentést készíthet az alapértelmezett biztonsági mentési helyre.

## <a name="create-the-availability-group"></a>A rendelkezésre állási csoport létrehozása

Most már készen áll a rendelkezésre állási csoport konfigurálására a következő lépések végrehajtásával:

* Hozzon létre egy adatbázist az első SQL Server.
* Készítsen mind a teljes biztonsági mentést, mind a tranzakciónapló biztonsági mentését az adatbázisból.
* Állítsa vissza a teljes és a naplóbeli biztonsági mentést a második SQL Serverba a nem **helyreállítási** lehetőséggel.
* Hozza létre a rendelkezésre állási csoportot (**AG1**) szinkron véglegesítés, automatikus feladatátvétel és olvasható másodlagos replikák létrehozásával.

### <a name="create-the-availability-group"></a>A rendelkezésre állási csoport létrehozása:

1. Távoli asztali munkamenetben az első SQL Server. A SSMS **Object Explorer** kattintson a jobb gombbal a **AlwaysOn magas rendelkezésre állása** elemre, és válassza az **új rendelkezésre állási csoport varázslót**.

    ![Új rendelkezésre állási csoport elindítása varázsló](./media/availability-group-manually-configure-tutorial/56-newagwiz.png)

2. A **Bevezetés** lapon válassza a **tovább** lehetőséget. A rendelkezésre **állási csoport nevének megadása** lapon adja meg a rendelkezésre állási **csoport nevét a** rendelkezésre állási csoport nevében. Például: **AG1**. Kattintson a **Tovább** gombra.

    ![Új rendelkezésre állási csoport varázsló, rendelkezésre állási csoport nevének megadása](./media/availability-group-manually-configure-tutorial/58-newagname.png)

3. Az **adatbázisok kiválasztása** lapon válassza ki az adatbázist, majd válassza a **tovább** lehetőséget.

   >[!NOTE]
   >Az adatbázis megfelel a rendelkezésre állási csoport előfeltételeinek, mert legalább egy teljes biztonsági mentést végzett a kívánt elsődleges replikán.
   >

   ![Új rendelkezésre állási csoport varázsló, adatbázisok kiválasztása](./media/availability-group-manually-configure-tutorial/60-newagselectdatabase.png)

4. A **replikák megadása** lapon válassza a **replika hozzáadása** elemet.

   ![Új rendelkezésre állási csoport varázsló, replikák megadása](./media/availability-group-manually-configure-tutorial/62-newagaddreplica.png)

5. Megjelenik a **Kapcsolódás a kiszolgálóhoz** párbeszédpanel. Írja be a **kiszolgáló neve** mezőbe a második kiszolgáló nevét. Válassza a **Kapcsolódás** lehetőséget.

   A **replikák megadására** szolgáló lapon a második kiszolgáló jelenik meg a **rendelkezésre állási replikák** területen. Konfigurálja a replikákat az alábbiak szerint.

   ![Új rendelkezésre állási csoport varázsló, replikák meghatározása (kész)](./media/availability-group-manually-configure-tutorial/64-newagreplica.png)

6. A **végpontok** lehetőség kiválasztásával megtekintheti a rendelkezésre állási csoport adatbázis-tükrözési végpontját. Használja ugyanazt a portot, amelyet a [Tűzfalszabály adatbázis-tükrözési végpontokhoz való](availability-group-manually-configure-prerequisites-tutorial.md#endpoint-firewall)beállításakor használt.

    ![Új rendelkezésre állási csoport varázsló, válassza a kezdeti adatszinkronizálás lehetőséget.](./media/availability-group-manually-configure-tutorial/66-endpoint.png)

8. A **kezdeti adatszinkronizálás kiválasztása** lapon válassza a **teljes** lehetőséget, és adjon meg egy megosztott hálózati helyet. A helyhez használja a [létrehozott biztonsági mentési megosztást](#backupshare). A példában **\\ \\<először SQL Server \> \backup \\**. Kattintson a **Tovább** gombra.

   >[!NOTE]
   >A teljes szinkronizálás teljes biztonsági mentést készít az adatbázisról SQL Server első példányán, és visszaállítja a második példányra. Nagyméretű adatbázisok esetén a teljes szinkronizálás nem ajánlott, mert hosszú időt is igénybe vehet. Ezt az időt manuálisan is elvégezheti, ha kézzel készít biztonsági másolatot az adatbázisról, és visszaállítja azt `NO RECOVERY` . Ha az adatbázis már vissza van állítva a `NO RECOVERY` második SQL Server a rendelkezésre állási csoport konfigurálása előtt, válassza a **csak csatlakozás** lehetőséget. Ha a rendelkezésre állási csoport konfigurálása után szeretné a biztonsági mentést készíteni, válassza a **kezdeti adatszinkronizálás kihagyása** lehetőséget.
   >

   ![Válassza ki a kezdeti adatszinkronizálás kihagyása lehetőséget.](./media/availability-group-manually-configure-tutorial/70-datasynchronization.png)

9. Az **Érvényesítés** lapon válassza a **tovább** lehetőséget. Ennek a lapnak a következő képhez hasonlóan kell kinéznie:

    ![Új rendelkezésre állási csoport varázsló, ellenőrzés](./media/availability-group-manually-configure-tutorial/72-validation.png)

    >[!NOTE]
    >A figyelő konfigurációja figyelmeztetést kapott, mert még nem konfigurálta a rendelkezésre állási csoport figyelőjét. Figyelmen kívül hagyhatja ezt a figyelmeztetést, mert az Azure Virtual Machines szolgáltatásban az Azure Load Balancer létrehozása után létrehozhatja a figyelőt.

10. Az **Összefoglalás** lapon válassza a **Befejezés** lehetőséget, majd várjon, amíg a varázsló konfigurálja az új rendelkezésre állási csoportot. A **folyamat** lapon **További részleteket** is megadhat a részletes folyamat megtekintéséhez. A varázsló befejezése után vizsgálja meg az **eredmények** lapot annak ellenőrzéséhez, hogy a rendelkezésre állási csoport sikeresen létrejött-e.

     ![Új rendelkezésre állási csoport varázsló, eredmények](./media/availability-group-manually-configure-tutorial/74-results.png)

11. A varázslóból való kilépéshez kattintson a **Bezárás** gombra.

### <a name="check-the-availability-group"></a>A rendelkezésre állási csoport keresése

1. **Object Explorer** bontsa ki a **AlwaysOn magas rendelkezésre állása** elemet, majd bontsa ki a **rendelkezésre állási csoportok** csomópontot. Ekkor megjelenik az új rendelkezésre állási csoport ebben a tárolóban. Kattintson a jobb gombbal a rendelkezésre állási csoportra, majd válassza az **irányítópult megjelenítése** lehetőséget.

   ![Rendelkezésre állási csoport irányítópultjának megjelenítése](./media/availability-group-manually-configure-tutorial/76-showdashboard.png)

   A **AlwaysOn irányítópultjának** az alábbi képernyőképhez hasonlóan kell kinéznie:

   ![rendelkezésre állási csoport irányítópultja](./media/availability-group-manually-configure-tutorial/78-agdashboard.png)

   Láthatja a replikákat, az egyes replikák feladatátvételi módját, valamint a szinkronizálási állapotot.

2. A **Feladatátvevőfürt-kezelő** területen válassza ki a fürtöt. Válassza a **szerepkörök** lehetőséget. A használt rendelkezésre állási csoport neve a fürt egyik szerepköre. A rendelkezésre állási csoport nem rendelkezik IP-címmel az ügyfélkapcsolatokhoz, mert nem konfigurált egy figyelőt. Az Azure Load Balancer létrehozása után konfigurálnia kell a figyelőt.

   ![rendelkezésre állási csoport a Feladatátvevőfürt-kezelő](./media/availability-group-manually-configure-tutorial/80-clustermanager.png)

   > [!WARNING]
   > Ne próbálkozzon a rendelkezésre állási csoport feladatátvételével a Feladatátvevőfürt-kezelő. Az összes feladatátvételi műveletet a SSMS **AlwaysOn-irányítópultján** belül kell végrehajtani. További információ: [a Feladatátvevőfürt-kezelő és a rendelkezésre állási csoportok használatának korlátozásai](/sql/database-engine/availability-groups/windows/failover-clustering-and-always-on-availability-groups-sql-server).
    >

Ezen a ponton van egy rendelkezésre állási csoport, amely replikákat tartalmaz SQL Server két példányán. A rendelkezésre állási csoportot példányok között helyezheti át. Még nem tud csatlakozni a rendelkezésre állási csoporthoz, mert nem rendelkezik figyelővel. Az Azure Virtual Machines szolgáltatásban a figyelőnek terheléselosztó szükséges. A következő lépés a terheléselosztó létrehozása az Azure-ban.

<a name="configure-internal-load-balancer"></a>

## <a name="create-an-azure-load-balancer"></a>Azure-terheléselosztó létrehozása

[!INCLUDE [sql-ag-use-dnn-listener](../../includes/sql-ag-use-dnn-listener.md)]

Az Azure Virtual Machines szolgáltatásban egy SQL Server rendelkezésre állási csoport terheléselosztó szükséges. A terheléselosztó tárolja a rendelkezésre állási csoport figyelők és a Windows Server feladatátvevő fürt IP-címeit. Ez a szakasz összefoglalja, hogyan hozható létre a terheléselosztó a Azure Portalban.

Az Azure-ban a terheléselosztó lehet standard Load Balancer vagy egy alapszintű Load Balancer. Standard Load Balancer több funkcióval rendelkezik, mint az alapszintű Load Balancer. Rendelkezésre állási csoport esetén a standard Load Balancerra akkor van szükség, ha rendelkezésre állási zónát használ (a rendelkezésre állási csoport helyett). A terheléselosztó SKU közötti különbség részleteiért lásd: [Load BALANCER SKU-összehasonlítás](../../../load-balancer/skus.md).

1. A Azure Portal nyissa meg az erőforráscsoportot, amelyben az SQL-kiszolgálókat, majd válassza a **+ Hozzáadás** lehetőséget.
1. **Load Balancer** keresése. Válassza ki a Microsoft által közzétett terheléselosztó.

   ![Válassza ki a Microsoft által közzétett terheléselosztó](./media/availability-group-manually-configure-tutorial/82-azureloadbalancer.png)

1. Válassza a **Létrehozás** lehetőséget.
1. Konfigurálja a terheléselosztó következő paramétereit.

   | Beállítás | Mező |
   | --- | --- |
   | **Név** |Használjon egy szöveges nevet a terheléselosztó számára, például **sqlLB**. |
   | **Típus** |Belső |
   | **Virtuális hálózat** |Használja az Azure-beli virtuális hálózat nevét. |
   | **Alhálózat** |Használja annak az alhálózatnak a nevét, amelyben a virtuális gép található.  |
   | **IP-cím hozzárendelése** |Statikus |
   | **IP-cím** |Az alhálózaton elérhető címek használata. Használja ezt a címeket a rendelkezésre állási csoport figyelője számára. Vegye figyelembe, hogy ez eltér a fürt IP-címétől.  |
   | **Előfizetés** |Használja ugyanazt az előfizetést, mint a virtuális gépet. |
   | **Hely** |Ugyanazt a helyet használja, mint a virtuális gép. |

   A Azure Portal panelnek így kell kinéznie:

   ![Load Balancer létrehozása](./media/availability-group-manually-configure-tutorial/84-createloadbalancer.png)

1. A terheléselosztó létrehozásához válassza a **Létrehozás** lehetőséget.

A terheléselosztó konfigurálásához létre kell hoznia egy háttér-készletet, egy mintavételt, és be kell állítania a terheléselosztási szabályokat. Ezeket a Azure Portal.

### <a name="add-a-backend-pool-for-the-availability-group-listener"></a>Háttérbeli készlet hozzáadása a rendelkezésre állási csoport figyelője számára

1. A Azure Portal lépjen a rendelkezésre állási csoportba. Előfordulhat, hogy frissítenie kell a nézetet az újonnan létrehozott terheléselosztó megtekintéséhez.

   ![Load Balancer keresése az erőforráscsoporthoz](./media/availability-group-manually-configure-tutorial/86-findloadbalancer.png)

1. Válassza ki a terheléselosztó elemet, válassza a **háttérbeli készletek**, majd a **+ Hozzáadás** lehetőséget.

1. Adja meg a háttér-készlet nevét.

1. Társítsa a háttér-készletet a virtuális gépeket tartalmazó rendelkezésre állási csoporttal.

1. A **célként megadott hálózati IP-konfigurációk** területen jelölje be a **virtuális gép** lehetőséget, és válassza ki mindkét virtuális gépet, amely a rendelkezésre állási csoport replikáit fogja tárolni. Ne adja meg a tanúsító fájlmegosztás kiszolgálóját.

   >[!NOTE]
   >Ha a virtuális gépek nem lettek megadva, a kapcsolatok csak az elsődleges replikához lesznek sikeresek.

1. A háttér-készlet létrehozásához kattintson **az OK gombra** .

### <a name="set-the-probe"></a>A mintavétel beállítása

1. Válassza ki a terheléselosztó elemet, válassza az **állapot**-mintavételek, majd a **+ Hozzáadás** lehetőséget.

1. A figyelő állapotának mintavételét az alábbiak szerint állítsa be:

   | Beállítás | Leírás | Példa
   | --- | --- |---
   | **Név** | Szöveg | SQLAlwaysOnEndPointProbe |
   | **Protokoll** | Válassza a TCP beállítást | TCP |
   | **Port** | A fel nem használt portok | 59999 |
   | **Intervallum**  | A mintavételi kísérletek közötti időtartam másodpercben |5 |
   | **Nem kifogástalan állapot küszöbértéke** | Az egymást követő mintavételi hibák száma, amelyeknek meg kell történnie, hogy a virtuális gép nem kifogástalannak minősül  | 2 |

1. Az állapot mintavételének beállításához kattintson **az OK gombra** .

### <a name="set-the-load-balancing-rules"></a>Terheléselosztási szabályok beállítása

1. Válassza ki a terheléselosztó elemet, válassza a terheléselosztási **szabályok** lehetőséget, majd válassza a **+ Hozzáadás** lehetőséget.

1. Állítsa be a figyelő terheléselosztási szabályait az alábbiak szerint.

   | Beállítás | Leírás | Példa
   | --- | --- |---
   | **Név** | Szöveg | SQLAlwaysOnEndPointListener |
   | **Előtérbeli IP-cím** | Válasszon egy címtartományt |Használja a terheléselosztó létrehozásakor létrehozott címeket. |
   | **Protokoll** | Válassza a TCP beállítást |TCP |
   | **Port** | A rendelkezésre állási csoport figyelője portjának használata | 1433 |
   | **Háttér-port** | Ez a mező nem használható, ha a közvetlen kiszolgáló visszaadására beállított lebegőpontos IP-cím | 1433 |
   | **Mintavétel** |A mintavételhez megadott név | SQLAlwaysOnEndPointProbe |
   | **Munkamenetek megőrzése** | Legördülő lista | **Nincs** |
   | **Üresjárat időkorlátja** | A TCP-kapcsolatok megnyitásának percben tartása | 4 |
   | **Lebegőpontos IP-cím (közvetlen kiszolgáló visszaadása)** | |Engedélyezve |

   > [!WARNING]
   > A rendszer a közvetlen kiszolgáló visszaadását állítja be a létrehozás során. A név nem módosítható.
   >

1. A figyelő terheléselosztási szabályainak beállításához kattintson **az OK gombra** .

### <a name="add-the-cluster-core-ip-address-for-the-windows-server-failover-cluster-wsfc"></a>A fürt alapvető IP-címének hozzáadása a Windows Server feladatátvevő fürthöz (WSFC)

A WSFC IP-címének is szerepelnie kell a terheléselosztó számára.

1. A Azure Portal nyissa meg ugyanazt az Azure Load balancert. Válassza ki a **frontend IP-konfiguráció** elemet, és válassza a **+ Hozzáadás** lehetőséget. Használja a WSFC konfigurált IP-címet a fürt alapvető erőforrásaiban. Adja meg az IP-címet statikusként.

1. A terheléselosztó lapon válassza az **állapot**-mintavételek, majd a **+ Hozzáadás** lehetőséget.

1. Állítsa be a WSFC-fürt alapvető IP-címének állapotát a következőképpen:

   | Beállítás | Leírás | Példa
   | --- | --- |---
   | **Név** | Szöveg | WSFCEndPointProbe |
   | **Protokoll** | Válassza a TCP beállítást | TCP |
   | **Port** | A fel nem használt portok | 58888 |
   | **Intervallum**  | A mintavételi kísérletek közötti időtartam másodpercben |5 |
   | **Nem kifogástalan állapot küszöbértéke** | Az egymást követő mintavételi hibák száma, amelyeknek meg kell történnie, hogy a virtuális gép nem kifogástalannak minősül  | 2 |

1. Az állapot mintavételének beállításához kattintson **az OK gombra** .

1. Állítsa be a terheléselosztási szabályokat. Válassza a **terheléselosztási szabályok** lehetőséget, majd válassza a **+ Hozzáadás** lehetőséget.

1. Állítsa be a fürt alapvető IP-címének terheléselosztási szabályait az alábbiak szerint.

   | Beállítás | Leírás | Példa
   | --- | --- |---
   | **Név** | Szöveg | WSFCEndPoint |
   | **Előtérbeli IP-cím** | Válasszon egy címtartományt |Használja a WSFC IP-cím beállításakor létrehozott címet. Ez eltér a figyelő IP-címétől. |
   | **Protokoll** | Válassza a TCP beállítást |TCP |
   | **Port** | A fürt IP-címéhez használja a portot. Ez egy elérhető port, amely nem használatos a figyelő mintavételi portjához. | 58888 |
   | **Háttér-port** | Ez a mező nem használható, ha a közvetlen kiszolgáló visszaadására beállított lebegőpontos IP-cím | 58888 |
   | **Mintavétel** |A mintavételhez megadott név | WSFCEndPointProbe |
   | **Munkamenetek megőrzése** | Legördülő lista | **Nincs** |
   | **Üresjárat időkorlátja** | A TCP-kapcsolatok megnyitásának percben tartása | 4 |
   | **Lebegőpontos IP-cím (közvetlen kiszolgáló visszaadása)** | |Engedélyezve |

   > [!WARNING]
   > A rendszer a közvetlen kiszolgáló visszaadását állítja be a létrehozás során. A név nem módosítható.
   >

1. A terheléselosztási szabályok megadásához kattintson **az OK gombra** .

## <a name="configure-the-listener"></a><a name="configure-listener"></a> A figyelő konfigurálása

A következő lépés a rendelkezésre állási csoport figyelő konfigurálása a feladatátvevő fürtön.

> [!NOTE]
> Ez az oktatóanyag bemutatja, hogyan hozhat létre egyetlen figyelőt egyetlen ILB IP-címmel. Ha legalább egy figyelőt egy vagy több IP-címmel szeretne létrehozni, tekintse meg a [rendelkezésre állási csoport figyelője és a Load Balancer létrehozása című témakört. Azure](availability-group-listener-powershell-configure.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)-ban.
>

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-listener-port"></a>Figyelő portjának beállítása

A SQL Server Management Studioban állítsa be a figyelő portját.

1. Indítsa el SQL Server Management Studio és kapcsolódjon az elsődleges replikához.

1. Navigáljon a **magas rendelkezésre** állási rendelkezésre állási  >  **csoportok**  >  **rendelkezésre állási csoportjának figyelők** AlwaysOn.

1. Ekkor megjelenik a Feladatátvevőfürt-kezelőban létrehozott figyelő neve. Kattintson a jobb gombbal a figyelő nevére, és válassza a **Tulajdonságok** lehetőséget.

1. A **port** mezőben határozza meg a rendelkezésre állási csoport figyelő portszámát. 1433 az alapértelmezett érték. Válassza az **OK** lehetőséget.

Most már rendelkezik egy SQL Server rendelkezésre állási csoporttal a Resource Manager módban futó Azure-beli virtuális gépeken.

## <a name="test-connection-to-listener"></a>A figyelőhöz való kapcsolódás tesztelése

A kapcsolódás tesztelése:

1. Az RDP használatával csatlakozzon egy olyan SQL Serverhoz, amely ugyanabban a virtuális hálózatban található, de nem a saját replikája. A fürt más SQL Server is használható.

1. A **Sqlcmd** segédprogram használatával tesztelheti a kapcsolódást. Az alábbi szkript például egy **Sqlcmd** kapcsolatot létesít az elsődleges replikával a figyelőn keresztül a Windows-hitelesítéssel:

   ```cmd
   sqlcmd -S <listenerName> -E
   ```

   Ha a figyelő nem az alapértelmezett portot (1433) használja, akkor a portot a csatlakozási karakterláncban kell megadni. A következő `sqlcmd` parancs például csatlakozik egy figyelőhöz a 1435-as porton:

   ```cmd
   sqlcmd -S <listenerName>,1435 -E
   ```

Az SQLCMD-kapcsolat automatikusan csatlakozik a SQL Server-példányhoz, amely az elsődleges replikát tárolja.

> [!TIP]
> Győződjön meg arról, hogy a megadott port meg van nyitva a tűzfalon mindkét SQL Server-kiszolgálón. Mindkét kiszolgálónak szüksége van egy bejövő szabályra a használt TCP-porthoz. További információ: [Tűzfalszabály hozzáadása vagy szerkesztése](/previous-versions/orphan-topics/ws.11/cc753558(v=ws.11)).
>

## <a name="next-steps"></a>Következő lépések

- [Adjon hozzá egy IP-címet egy terheléselosztó számára egy második rendelkezésre állási csoport számára](availability-group-listener-powershell-configure.md#Add-IP).