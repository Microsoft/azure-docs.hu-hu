---
title: Virtuális gép áthelyezése egy másik régióba (Azure Site Recovery)
description: Ismerje meg, hogyan telepítheti át SQL Server virtuális gépét az egyik régióból a másikba az Azure-ban.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.subservice: migration
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/30/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 789554121af1c83d9077e6153ca9db01477bde25
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97360152"
---
# <a name="move-a-sql-server-vm-to-another-region-within-azure-with-azure-site-recovery"></a>SQL Server VM áthelyezése az Azure-ban lévő másik régióba Azure Site Recovery
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Ebből a cikkből megtudhatja, hogyan használhatja a Azure Site Recoveryt a SQL Server virtuális gép (VM) egyik régióból egy másikba való áttelepítésére az Azure-ban. 

Egy SQL Server VM másik régióba való áthelyezéséhez a következőkre van szükség:
1. [Előkészítés](#prepare-to-move): Győződjön meg róla, hogy a forrás-SQL Server VM és a célként megadott régió megfelelően fel van készítve az áthelyezésre. 
1. [Konfigurálás](#configure-azure-site-recovery-vault): a SQL Server VM áthelyezéséhez az szükséges, hogy az Azure site Recovery-tárolóban lévő replikált objektum legyen. Hozzá kell adnia a SQL Server VM a Azure Site Recovery-tárolóhoz. 
1. [Tesztelés](#test-move-process): a SQL Server VM áttelepítése a forrás régiójából a replikált célhelyre történő feladatátvételt igényel. Annak érdekében, hogy az áthelyezési folyamat sikeres legyen, először tesztelni kell, hogy a SQL Server VM sikeresen átadhatja-e a feladatátvételt a célként megadott régióba. Ez segít az esetleges problémák megoldásában, és a tényleges áthelyezés végrehajtásakor elkerülheti azokat. 
1. [Áthelyezés](#move-the-sql-server-vm): a feladatátvételi teszt sikeres átadása után biztos lehet abban, hogy a SQL Server VM áttelepítését elvégezte a virtuális gép áthelyezését a célként megadott régióba. 
1. [Tisztítás](#clean-up-source-resources): a számlázási költségek elkerülése érdekében távolítsa el a SQL Server VM a tárolóból, és az erőforráscsoport felesleges erőforrásait. 

## <a name="verify-prerequisites"></a>Előfeltételek ellenőrzése 

- Győződjön meg arról, hogy a forrás régióról a célhelyre való áttérés [támogatott](../../../site-recovery/azure-to-azure-support-matrix.md#region-support).  
- Tekintse át a [forgatókönyv architektúráját és összetevőit](../../../site-recovery/azure-to-azure-architecture.md) , valamint a [támogatási korlátozásokat és követelményeket](../../../site-recovery/azure-to-azure-support-matrix.md). 
- A fiók engedélyeinek ellenőrzése. Ha létrehozta az ingyenes Azure-fiókját, akkor Ön az előfizetés rendszergazdája. Ha nem Ön az előfizetés rendszergazdája, akkor a rendszergazdával együttműködve rendelje hozzá a szükséges engedélyeket. A virtuális gép replikálásának engedélyezéséhez és az adatmásoláshoz Azure Site Recovery használatával a következőket kell tennie: 
    - A virtuális gép létrehozásához szükséges engedélyek. A *virtuálisgép-közreműködő* beépített szerepköre rendelkezik ezekkel az engedélyekkel, amelyek a következőket tartalmazzák: 
        - Engedélyek virtuális gép létrehozásához a kiválasztott erőforráscsoporthoz. 
        - Engedélyek virtuális gép létrehozásához a kiválasztott virtuális hálózaton. 
        - A kiválasztott Storage-fiókba való íráshoz szükséges engedélyek. 
      - Azure Site Recovery műveletek kezeléséhez szükséges engedélyek. A *site Recovery közreműködő* szerepkör minden olyan engedéllyel rendelkezik, amely a Recovery Services-tárolóban lévő site Recovery műveletek kezeléséhez szükséges.  

## <a name="prepare-to-move"></a>Felkészülés az áthelyezésre
Készítse elő mind a forrás SQL Server VM, mind a cél régiót az áthelyezéshez. 

### <a name="prepare-the-source-sql-server-vm"></a>A forrás SQL Server VM előkészítése

- Győződjön meg arról, hogy a legújabb főtanúsítványok az áthelyezni kívánt SQL Server VMon találhatók. Ha a legfelső szintű tanúsítványok nem léteznek, a biztonsági korlátozások megakadályozzák, hogy a rendszer az Adatmásolást a célként megadott régióba másolja. 
- Windows rendszerű virtuális gépek esetén telepítse az összes Windows-frissítést a virtuális gépre, hogy a megbízható legfelső szintű tanúsítványok a gépen legyenek. A leválasztott környezetekben kövesse a szervezete normál Windows Update és a tanúsítvány-frissítési folyamatát. 
- Linux rendszerű virtuális gépek esetén kövesse a linuxos forgalmazója által biztosított útmutatást a legújabb megbízható főtanúsítványok és a tanúsítvány-visszavonási lista lekéréséhez a virtuális gépen. 
- Győződjön meg arról, hogy nem használ hitelesítési proxyt az áthelyezni kívánt virtuális gépek hálózati kapcsolatának szabályozására. 
- Ha az áthelyezni kívánt virtuális gép nem fér hozzá az internethez, vagy egy tűzfal proxyt használ a kimenő hozzáférés vezérléséhez, ellenőrizze a követelményeket. 
- Azonosítsa a forrás hálózatkezelési elrendezést és az összes éppen használt erőforrást. Ez magában foglalja a következőket: a terheléselosztó, a hálózati biztonsági csoportok (NSG) és a nyilvános IP-címek nem korlátozódnak. 

### <a name="prepare-the-target-region"></a>A cél régió előkészítése

- Ellenőrizze, hogy az Azure-előfizetése lehetővé teszi-e a vész-helyreállítási célra használt cél régióban lévő virtuális gépek létrehozását. A szükséges kvóta engedélyezéséhez vegye fel a kapcsolatot az ügyfélszolgálattal. 
- Győződjön meg arról, hogy az előfizetése elegendő erőforrással rendelkezik a forrás virtuális gépeknek megfelelő méretű virtuális gépek támogatásához. Ha Site Recovery használja az Adatmásolás céljára, Site Recovery a célként megadott virtuális gép méretének és a lehető legközelebbi méretének a kiválasztását. 
- Győződjön meg arról, hogy minden, a forrás hálózatkezelési elrendezésben azonosított összetevőhöz létrehoz egy célként megadott erőforrást. Ez a lépés fontos annak biztosítása érdekében, hogy a virtuális gépek rendelkezzenek a forrás régiójában lévő adott régióban lévő összes funkcióval és szolgáltatással. 
    - Azure Site Recovery automatikusan felfedi és létrehoz egy virtuális hálózatot, amikor engedélyezi a forrás virtuális gép replikálását. Emellett előre létrehozhat egy hálózatot, és hozzárendelheti a virtuális géphez a replikáció engedélyezéséhez a felhasználói folyamaton belül. A célként megadott régióban manuálisan kell létrehoznia a többi erőforrást.
- A forrás virtuális gép konfigurációja alapján a leggyakrabban használt hálózati erőforrások létrehozásához tekintse meg a következő dokumentációt: 
    - [Hálózati biztonsági csoportok](../../../virtual-network/tutorial-filter-network-traffic.md) 
    - [Terheléselosztó](../../../load-balancer/quickstart-load-balancer-standard-internal-portal.md)
    - [Nyilvános IP-cím](../../../virtual-network/virtual-network-public-ip-address.md)
    - További hálózatkezelési összetevőkért tekintse meg a [hálózatkezelési dokumentációt](../../../virtual-network/virtual-networks-overview.md).
- Ha tesztelni szeretné a konfigurációt, manuálisan hozza létre a nem éles hálózatot a célként megadott régióban. Ezt a lépést javasoljuk, mert minimális beavatkozást biztosít az üzemi hálózattal. 

## <a name="configure-azure-site-recovery-vault"></a>Azure Site Recovery-tároló konfigurálása

A következő lépések bemutatják, hogyan másolhatók az adatAzure Site Recoveryek a célként megadott régióba. Hozza létre a Recovery Services-tárolót a forrás régiótól eltérő bármely régióban. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 
1. Válassza a navigációs ablaktábla bal felső sarkában található **erőforrás létrehozása** lehetőséget. 
1. Válassza ki **& felügyeleti eszközöket** , majd válassza **a biztonsági mentés és site Recovery** lehetőséget. 
1. Az **alapvető beállítások** lap **projekt részletei** területén hozzon létre egy új erőforráscsoportot a céltartományban, vagy válasszon ki egy meglévő erőforráscsoportot a célként megadott régióban. 
1. A **példány részletei** területen adja meg a tároló nevét, majd válassza ki a kívánt **régiót** a legördülő menüből. 
1. Válassza a **felülvizsgálat + létrehozás** lehetőséget a Recovery Services-tároló létrehozásához. 
1. A navigációs ablaktábla bal felső sarkában válassza a **minden szolgáltatás** lehetőséget, majd a keresőmezőbe írja be a következőt: `recovery services` . 
1. Opcionálisan Válassza ki **Recovery Services** -tároló melletti csillagot a gyors navigációs sávon való hozzáadáshoz. 
1. Válassza a **Recovery Services** -tárolók lehetőséget, majd válassza ki a létrehozott Recovery Services-tárolót. 
1. Az **Áttekintés** panelen válassza a **replikálás** lehetőséget. 

   ![Replikáció konfigurálása](./media/move-sql-vm-different-region/configure-replication.png)

1. Válassza a **forrás** lehetőséget, majd válassza ki az **Azure** -t a forrásként. Válassza ki a megfelelő értékeket a többi legördülő mezőhöz, például a forrás virtuális gépek helyét. A **forrás erőforráscsoport** mezőben csak a **forrás helye** régióban található erőforráscsoportok lesznek láthatók. 
1. Válassza a **virtuális gépek** lehetőséget, majd válassza ki az áttelepíteni kívánt virtuális gépeket. A virtuális gép kijelölésének mentéséhez kattintson **az OK gombra** . 
1. Válassza a **Beállítások** lehetőséget, majd válassza ki a **célhelyet** a legördülő menüből. Ennek a korábban előkészített erőforráscsoporthoz kell lennie. 
1. Ha testreszabta a replikációt, válassza a **cél erőforrások létrehozása** lehetőséget, hogy létrehozza az erőforrásokat az új helyen. 
1. Az erőforrás-létrehozás befejezése után válassza a **replikáció engedélyezése** lehetőséget a SQL Server VM replikálásának megkezdéséhez a forrásból a célként megadott régióba.
1. A replikáció állapotát a helyreállítási tárolóra navigálva, a **replikált elemek** kiválasztásával és a SQL Server VM **állapotának** megtekintésével tekintheti meg. A **védett** állapot azt jelzi, hogy a replikáció befejeződött. 

   ![Replikáció állapotának ellenőrzése](./media/move-sql-vm-different-region/check-replication-status.png)

## <a name="test-move-process"></a>Mozgási folyamat tesztelése
A következő lépések bemutatják, hogyan használhatja a Azure Site Recovery az áthelyezési folyamat teszteléséhez. 

1. Navigáljon a **Recovery Services** -tárolóhoz a [Azure Portal](https://portal.azure.com) , és válassza a **replikált elemek** lehetőséget. 
1. Válassza ki az áthelyezni kívánt SQL Server VM, ellenőrizze, hogy a **replikálás állapota kifogástalan állapotú** -e, majd válassza a **feladatátvételi teszt** lehetőséget.  

   ![Feladatátvételi teszt a virtuális gépen](./media/move-sql-vm-different-region/test-failover-of-replicated-vm.png)

1. A feladatátvételi **teszt** lapon válassza ki a feladatátvételhez használni kívánt **legújabb alkalmazás-konzisztens** helyreállítási pontot, mivel ez az egyetlen olyan pillanatkép-típus, amely garantálja az adatkonzisztencia SQL Server. 
1. Válassza ki a virtuális hálózatot az **Azure Virtual Network** területen, majd kattintson **az OK gombra** a feladatátvétel teszteléséhez. 
   
   >[!IMPORTANT]
   > Javasoljuk, hogy használjon külön Azure-beli virtuálisgép-hálózatot a feladatátvételi teszthez. Ne használja a replikáció engedélyezésekor beállított éles hálózatot, és a virtuális gépeket végül át kívánja helyezni. 

1. Az előrehaladás figyeléséhez navigáljon a tárolóhoz, válassza ki **site Recovery feladatokat** a **figyelés** területen, majd válassza ki a folyamatban lévő **feladatátvételi teszt** feladatot.

   ![Feladatátvételi teszt előrehaladásának figyelése](./media/move-sql-vm-different-region/monitor-failover-test-job.png)

1. A teszt befejezése után navigáljon a **virtuális gépekhez** a portálon, és tekintse át az újonnan létrehozott virtuális gépet. Győződjön meg arról, hogy a SQL Server VM fut, a mérete megfelelő, és csatlakoztatva van a megfelelő hálózathoz. 
1. Törölje a teszt részeként létrehozott virtuális gépet, mert **a feladatátvételi beállítás** szürkén jelenik meg, amíg a feladatátvételi teszt erőforrásai nem törlődnek. Váltson vissza a tárolóhoz, válassza a **replikált elemek** lehetőséget, válassza ki a SQL Server VM, majd válassza a **feladatátvételi teszt törlése** lehetőséget. Jegyezze fel és mentse a teszttel kapcsolatos megfigyeléseket a **Megjegyzések** szakaszban, és jelölje be a tesztelés elem melletti jelölőnégyzetet **. Törölje a feladatátvételi teszt virtuális gépeket**. Az **OK** gombra kattintva törölheti az erőforrásokat a teszt után. 

   ![elemek tisztítása a feladatátvételi teszt után](./media/move-sql-vm-different-region/cleanup-test-items.png)

## <a name="move-the-sql-server-vm"></a>A SQL Server VM áthelyezése 
A következő lépések bemutatják, hogyan helyezheti át a SQL Server VM a forrás régiójából a kívánt régióba. 

1. Navigáljon a **Recovery Services** -tárolóhoz, válassza a **replikált elemek** lehetőséget, válassza ki a virtuális gépet, majd válassza a **feladatátvétel** lehetőséget. 

   ![Feladatátvétel kezdeményezése](./media/move-sql-vm-different-region/initiate-failover.png)

1. Válassza ki a **legújabb, alkalmazás-konzisztens** helyreállítási pontot a **helyreállítási pont** alatt. 
1. Jelölje be a **gép leállítása a feladatátvétel** megkezdése előtt jelölőnégyzetet. Site Recovery megkísérli leállítani a forrás virtuális gépet a feladatátvétel elindítása előtt. A feladatátvétel akkor is folytatódik, ha a Leállítás nem sikerül. 
1. A feladatátvétel elindításához kattintson **az OK gombra** .
1. Az előző szakaszban a feladatátvételi teszt figyelése során megtekintett **site Recovery feladatok** oldaláról is figyelheti a feladatátvételi folyamatot. 
1. Miután a feladatok befejeződik, győződjön meg arról, hogy a SQL Server VM a várt módon jelenik meg a célként megadott régióban. 
1. Lépjen vissza a tárolóhoz, válassza a **replikált elemek** lehetőséget, válassza ki a SQL Server VM, majd a **véglegesítés** gombra kattintva fejezze be az áthelyezési folyamatot a célként megadott régióba. Várjon, amíg a véglegesítési feladatok befejeződik. 
1. Regisztrálja a SQL Server VMt az SQL IaaS-ügynök bővítménnyel, hogy engedélyezze az SQL-alapú **virtuális gépek** kezelhetőségét a bővítményhez társított Azure Portalokban és szolgáltatásokban. További információ: [SQL Server VM regisztrálása az SQL IaaS-ügynök bővítménnyel](sql-agent-extension-manually-register-single-vm.md). 

  > [!WARNING]
  > SQL Server az adatkonzisztencia csak az alkalmazás-konzisztens Pillanatképek esetében garantált. A **legutóbbi feldolgozott** pillanatkép nem használható SQL Server feladatátvételhez, mert az összeomlás-helyreállítási pillanatkép nem tudja garantálni SQL Server adatkonzisztencia. 

## <a name="clean-up-source-resources"></a>Forrás erőforrásainak törlése
A számlázási díjak elkerüléséhez távolítsa el a SQL Server VM a tárolóból, és törölje a szükségtelenül kapcsolódó erőforrásokat. 

1. Váltson vissza az **site Recovery** -tárolóra, válassza a **replikált elemek** lehetőséget, majd válassza ki a SQL Server VM. 
1. Válassza a **replikáció letiltása** lehetőséget. Válasszon ki egy okot a védelem letiltására, majd kattintson **az OK gombra** a replikáció letiltásához. 

   >[!IMPORTANT]
   > Fontos, hogy ezt a lépést a Azure Site Recovery replikálásának elkerülése érdekében végezze el. 

1. Ha nem tervezi a forrástartomány erőforrásainak újrafelhasználását, törölje az összes kapcsolódó hálózati erőforrást és a hozzá tartozó Storage-fiókokat. 


## <a name="next-steps"></a>Következő lépések

További információért tekintse át a következő cikkeket: 

* [Windows rendszerű virtuális gépek SQL Server áttekintése](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [SQL Server Windows rendszerű virtuális gépen – gyakori kérdések](frequently-asked-questions-faq.md)
* [SQL Server a Windows rendszerű virtuális gépek díjszabási útmutatójában](pricing-guidance.md)
* [SQL Server a Windows rendszerű virtuális gépek kibocsátási megjegyzései](doc-changes-updates-release-notes.md)