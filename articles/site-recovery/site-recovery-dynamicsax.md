---
title: A Dynamics AX vész-helyreállítása Azure Site Recovery
description: Ismerje meg, hogyan állíthatja be a Dynamics AX vész-helyreállítását Azure Site Recovery
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 11/27/2018
ms.openlocfilehash: dfa3c108d00aeba9c7d42e96e7a40736a087a508
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "86133824"
---
# <a name="set-up-disaster-recovery-for-a-multitier-dynamics-ax-application"></a>Vész-helyreállítás beállítása többplatformos Dynamics AX-alkalmazáshoz   




 A Dynamics AX az egyik legnépszerűbb ERP-megoldás, amelyet a vállalatok a különböző helyszíneken felhasználva szabványosítják a folyamatokat, kezelhetik az erőforrásokat és egyszerűsítik a megfelelőséget. Mivel az alkalmazás kritikus fontosságú a szervezet számára, katasztrófa esetén az alkalmazásnak minimális időben kell futnia.

Napjainkban a Dynamics AX nem biztosít beépített vész-helyreállítási lehetőségeket. A Dynamics AX számos kiszolgálói összetevőből áll, mint például a Windows Application Object Server, a Azure Active Directory, a Azure SQL Database, a SharePoint Server és a Reporting Services. Ezeknek az összetevőknek a vész-helyreállítási felszámolásához nem csak a költséges, hanem a hibák is fennállnak.

Ez a cikk azt ismerteti, hogyan hozhat létre vész-helyreállítási megoldást a Dynamics AX-alkalmazáshoz [Azure site Recovery](site-recovery-overview.md)használatával. A tervezett/nem tervezett feladatátvételeket is magában foglalja egy egykattintásos helyreállítási terv, támogatott konfigurációk és előfeltételek használatával.



## <a name="prerequisites"></a>Előfeltételek

A Dynamics AX-alkalmazások vész-helyreállításának a Site Recovery használatával történő megvalósításához a következő előfeltételek szükségesek:

• A helyszíni Dynamics AX üzembe helyezésének beállítása.

• Hozzon létre egy Site Recovery tárolót egy Azure-előfizetésben.

• Ha az Azure a helyreállítási hely, futtassa az Azure Virtual Machine Readiness Assessment eszközt a virtuális gépeken. Kompatibilisnek kell lenniük az Azure Virtual Machines és Site Recovery szolgáltatásokkal.

## <a name="site-recovery-support"></a>Site Recovery támogatása

Ennek a cikknek a létrehozása céljából a VMware virtuális gépeket a Dynamics AX 2012 R3 verzióval használtuk a Windows Server 2012 R2 Enterprise rendszeren. Mivel Site Recovery replikáció az alkalmazás agnosztikusja, a következő forgatókönyvek esetében az itt megadott ajánlásokat várjuk.

### <a name="source-and-target"></a>Forrás és cél

**Forgatókönyv** | **Egy másodlagos helyre** | **Az Azure-ba**
--- | --- | ---
**Hyper-V** | Igen | Yes
**VMware** | Igen | Yes
**Fizikai kiszolgáló** | Igen | Yes

## <a name="enable-disaster-recovery-of-the-dynamics-ax-application-by-using-site-recovery"></a>A Dynamics AX-alkalmazás vész-helyreállításának engedélyezése Site Recovery használatával
### <a name="protect-your-dynamics-ax-application"></a>A Dynamics AX-alkalmazás megóvása
Az alkalmazás teljes replikálásának és helyreállításának engedélyezéséhez védeni kell a Dynamics AX minden összetevőjét.

### <a name="1-set-up-active-directory-and-dns-replication"></a>1. a Active Directory és a DNS-replikáció beállítása

Active Directory szükséges a Dynamics AX-alkalmazás működéséhez a vész-helyreállítási helyen. Az ügyfél helyszíni környezetének összetettsége alapján javasolt a következő két lehetőség választása.

**1\. lehetőség**

Az ügyfél kis számú alkalmazást és egyetlen tartományvezérlőt tartalmaz a teljes helyszíni helyhez, és a teljes webhely feladatátvételét tervezi. Azt javasoljuk, hogy Site Recovery replikáció használatával replikálja a tartományvezérlői gépet egy másodlagos helyre (a helyek közötti és a helyek közötti Azure-forgatókönyvek esetében egyaránt alkalmazható).

**2\. lehetőség**

Az ügyfél nagy számú alkalmazással rendelkezik, és egy Active Directory erdőt futtat, és egy időben több alkalmazás feladatátvételét tervezi. Javasoljuk, hogy állítson be egy további tartományvezérlőt a vész-helyreállítási webhelyre (másodlagos helyre vagy az Azure-ba).

 További információ: [tartományvezérlő elérhetővé tétele vész-helyreállítási helyen](site-recovery-active-directory.md). A dokumentum további részében feltételezzük, hogy a vész-helyreállítási helyen a tartományvezérlő elérhető.

### <a name="2-set-up-sql-server-replication"></a>2. SQL Server replikáció beállítása
Az SQL-csomag védelmének ajánlott beállításával kapcsolatos technikai útmutatásért lásd: [alkalmazások replikálása SQL Server és Azure site Recovery](site-recovery-sql.md).

### <a name="3-enable-protection-for-the-dynamics-ax-client-and-application-object-server-vms"></a>3. a Dynamics AX-ügyfél és az Application Object Server-alapú virtuális gépek védelmének engedélyezése
Végezze el a megfelelő Site Recovery konfigurációt annak alapján, hogy a virtuális gépek telepítve vannak-e a [Hyper-V-n](./hyper-v-azure-tutorial.md) vagy [VMware](./vmware-azure-tutorial.md)-en.

> [!TIP]
> Javasoljuk, hogy állítsa be az összeomlás-konzisztens gyakoriságot 15 percre.
>

A következő pillanatkép a Dynamics-Components virtuális gépek védelmi állapotát mutatja be egy VMware-helyek közötti védelmi forgatókönyvben.

![Védett elemek](./media/site-recovery-dynamics-ax/protecteditems.png)

### <a name="4-configure-networking"></a>4. hálózatkezelés konfigurálása
**VIRTUÁLIS gépek számítási és hálózati beállításainak konfigurálása**

A Dynamics AX-ügyfél és az Application Object Server-alapú virtuális gépek esetében konfigurálja a hálózati beállításokat Site Recovery, hogy a virtuálisgép-hálózatok a feladatátvételt követően a megfelelő vész-helyreállítási hálózathoz legyenek csatolva. Győződjön meg arról, hogy az ilyen rétegek vész-helyreállítási hálózata az SQL-szinten irányítható.

A replikált elemek közül kiválaszthatja a virtuális gépet a hálózati beállítások konfigurálásához, ahogy az a következő pillanatképben látható:

* Application Object Server-kiszolgálók esetén válassza ki a megfelelő rendelkezésre állási készletet.

* Ha statikus IP-címet használ, akkor határozza meg azt az IP-címet, amelyet a virtuális gépnek a **cél IP** -szövegmezőben kell elvégeznie.

    ![Hálózati beállítások](./media/site-recovery-dynamics-ax/vmpropertiesaos1.png)


### <a name="5-create-a-recovery-plan"></a>5. helyreállítási terv létrehozása

A feladatátvételi folyamat automatizálásához létrehozhat egy helyreállítási tervet a Site Recovery. Vegyen fel egy alkalmazási szintet és egy webes szintet a helyreállítási tervbe. Rendeljen különböző csoportokba, hogy az előtér az alkalmazási réteg előtt leálljon.

1. Válassza ki az előfizetéshez tartozó Site Recovery-tárolót, és válassza a **helyreállítási tervek** csempét.

2. Válassza a **+ helyreállítási terv** lehetőséget, és adjon meg egy nevet.

3. Válassza ki a **forrást** és a **célt**. A cél lehet Azure vagy másodlagos hely. Ha az Azure-t választja, meg kell adnia a telepítési modellt.

    ![Helyreállítási terv létrehozása](./media/site-recovery-dynamics-ax/recoveryplancreation1.png)

4. Válassza ki az Application Object-kiszolgálót és a helyreállítási tervhez tartozó ügyfél virtuális gépeket, és válassza ki a ✓.

    ![Elemek kijelölése](./media/site-recovery-dynamics-ax/selectvms.png)

    Helyreállítási terv példája:

    ![A helyreállítási terv részletei](./media/site-recovery-dynamics-ax/recoveryplan.png)

A Dynamics AX-alkalmazás helyreállítási tervét a következő lépések végrehajtásával szabhatja testre. Az előző pillanatkép a teljes helyreállítási tervet mutatja az összes lépés hozzáadása után.


* **SQL Server feladatátvételi lépések**: az SQL Serverre vonatkozó helyreállítási lépésekről további információt a következő témakörben talál: [Replication applications with SQL Server and Azure site Recovery](site-recovery-sql.md).

* **1. feladatátvételi csoport**: az Application Object Server-alapú virtuális gépek feladatátvétele.
Győződjön meg arról, hogy a kiválasztott helyreállítási pont a lehető legrövidebb legyen az adatbázis-PIT-hez, de még nem.

* **Parancsfájl**: a terheléselosztó hozzáadása (csak E-A).
Adjon hozzá egy parancsfájlt (Azure Automation), miután az Application Object Server virtuálisgép-csoportja felveszi a terheléselosztó hozzáadását. Ezt a feladatot parancsfájl használatával végezheti el. További információkért lásd: [Load Balancer hozzáadása többplatformos alkalmazás vész-helyreállításához](https://azure.microsoft.com/blog/cloud-migration-and-disaster-recovery-of-load-balanced-multi-tier-applications-using-azure-site-recovery/).

* **2. feladatátvételi csoport**: a Dynamics AX-ügyfél virtuális gépei feladatátvétele. A helyreállítási terv részeként adja át a virtuális gépek webes szintjeinek feladatátvételét.


### <a name="perform-a-test-failover"></a>Feladatátvételi teszt végrehajtása

A feladatátvételi teszt során Active Directory vonatkozó további információkért tekintse meg a "Active Directory vész-helyreállítási megoldás" című útmutatót.

A feladatátvételi teszt során az SQL Serverre vonatkozó további információkért lásd: [alkalmazások replikálása SQL Server és Azure site Recovery](site-recovery-sql.md).

1. Lépjen a Azure Portalra, és válassza ki a Site Recovery-tárolót.

2. Válassza ki a Dynamics AX-hoz létrehozott helyreállítási tervet.

3. Kattintson a **Feladatátvétel tesztelése** elemre.

4. Válassza ki a virtuális hálózatot a feladatátvételi teszt folyamatának elindításához.

5. A másodlagos környezet létrehozása után elvégezheti az érvényességét.

6. Az érvényesítések befejezése után válassza az **érvényesítések kész** lehetőséget, és a teszt feladatátvételi környezet megtisztítva.

A feladatátvételi teszt végrehajtásával kapcsolatos további információkért lásd: a [feladatátvételi teszt tesztelése az Azure-ban site Recovery](site-recovery-test-failover-to-azure.md).

### <a name="perform-a-failover"></a>Feladatátvétel végrehajtása

1. Lépjen a Azure Portalra, és válassza ki a Site Recovery-tárolót.

2. Válassza ki a Dynamics AX-hoz létrehozott helyreállítási tervet.

3. Válassza a **feladatátvétel** lehetőséget, majd válassza a **feladatátvétel** lehetőséget.

4. Válassza ki a célként kijelölt hálózatot, és válassza a **✓** lehetőséget a feladatátvételi folyamat elindításához.

A feladatátvétel végrehajtásával kapcsolatos további információkért lásd: [feladatátvétel a site Recoveryban](site-recovery-failover.md).

### <a name="perform-a-failback"></a>Feladat-visszavétel végrehajtása

A feladat-visszavétel során SQL Server vonatkozó szempontokat lásd: [alkalmazások replikálása SQL Server és Azure site Recovery](site-recovery-sql.md).

1. Lépjen a Azure Portalra, és válassza ki a Site Recovery-tárolót.

2. Válassza ki a Dynamics AX-hoz létrehozott helyreállítási tervet.

3. Válassza a **feladatátvétel** lehetőséget, majd válassza a **feladatátvétel** lehetőséget.

4. Válassza a **változás iránya** lehetőséget.

5. Válassza ki a megfelelő beállításokat: az adatszinkronizálást és a virtuális gépek létrehozását.

6. A feladat-visszavételi folyamat elindításához válassza a **✓** lehetőséget.


A feladat-visszavétel végrehajtásával kapcsolatos további információkért lásd: [VMWare virtuális gépek visszavétele az Azure-ból a](./vmware-azure-failback.md)helyszíni rendszerbe.

## <a name="summary"></a>Összefoglalás
Site Recovery használatával létrehozhat egy teljes automatikus vész-helyreállítási tervet a Dynamics AX-alkalmazáshoz. Megszakítás esetén másodpercek alatt kezdeményezheti a feladatátvételt, és percek alatt üzembe helyezheti és futtathatja az alkalmazást.

## <a name="next-steps"></a>Következő lépések
Ha többet szeretne megtudni a vállalati munkaterhelések Site Recovery használatával történő védelméről, tekintse meg a [milyen számítási feladatokat?](site-recovery-workload.md)című témakört.
