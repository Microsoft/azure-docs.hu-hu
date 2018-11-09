---
title: Azure-beli IaaS virtuális gépek migrálása egy másik Azure-régióba az Azure Site Recovery szolgáltatással | Microsoft Docs
description: Az Azure Site Recoveryvel Azure IaaS-virtuálisgépeket migrálhat egy Azure-régióból egy másikba.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 10/28/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 9ad994ad3dc1fc350a9a41c23574acfa2bae9629
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2018
ms.locfileid: "50212284"
---
# <a name="migrate-azure-vms-to-another-region"></a>Azure-beli virtuális gépek migrálása másik régióba

Az [Azure Site Recovery](site-recovery-overview.md) szolgáltatás helyszíni gépek és Azure-beli virtuális gépek vészhelyreállításának az üzleti folytonosság biztosítása és vészhelyreállítás (BCDR) céljából való kezelésére és vezénylésére használható, valamint az Azure-beli virtuális gépek másodlagos régióba történő migrálásának kezelésére. Az Azure-beli virtuális gépek migrálásához engedélyezi azok replikációját, és az elsődleges régióból egy tetszőleges másodlagos régióba veszi át azokat.

Ez az oktatóanyag bemutatja, hogyan migrálhat Azure-beli virtuális gépeket egy másik régióba. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Recovery Services-tároló létrehozása
> * Virtuális gép replikálásának engedélyezése
> * Feladatátvétel futtatása a virtuális gép migrálásához

Ez az oktatóanyag feltételezi, hogy már rendelkezik Azure-előfizetéssel. Ha mégsem így lenne, a kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).





## <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy rendelkezik Azure-beli virtuális gépekkel abban az Azure-régióban, amelyről migrálni szeretne.
- Ismernie kell a [forgatókönyv-architektúrát és az összetevőket](azure-to-azure-architecture.md).
- Tekintse át a [támogatási korlátokat és követelményeket](azure-to-azure-support-matrix.md) ismertető részt.



## <a name="before-you-start"></a>Előkészületek

A replikáció beállítása előtt hajtsa végre ezeket a lépéseket.


### <a name="verify-target-resources"></a>Célerőforrások ellenőrzése

1. Győződjön meg arról, hogy Azure-előfizetése lehetővé teszi-e virtuális gépek létrehozását a vészhelyreállításhoz használni kívánt célrégióban. A szükséges kvóta engedélyezéséhez vegye fel a kapcsolatot az ügyfélszolgálattal.

2. Ellenőrizze, hogy előfizetése rendelkezik-e a megfelelő erőforrásokkal a forrásként szolgáló virtuális gépeknek megfelelő méretű virtuális gépek támogatásához. A cél virtuális gép esetében a Site Recovery a méretben legközelebb állót fogja választani.


### <a name="verify-account-permissions"></a>Fiókengedélyek ellenőrzése

Ha most hozta létre az ingyenes Azure-fiókját, akkor Ön az előfizetés rendszergazdája. Ha nem Ön az előfizetés rendszergazdája, akkor a rendszergazdával együttműködve rendelje hozzá a fiókhoz a szükséges engedélyeket. Új virtuális gép replikációjának engedélyezéséhez a következőkkel kell rendelkeznie:

1. Engedélyek virtuális gép az Azure-erőforrásokban történő létrehozásához. A „Virtuális gépek közreműködője” beépített szerepkör rendelkezik ezekkel az engedélyekkel, amelyek a következők:
    - Engedély virtuális gépek a kiválasztott erőforráscsoportban történő létrehozásához
    - Engedély virtuális gépek a kiválasztott virtuális hálózaton történő létrehozásához
    - Engedély a kiválasztott tárfiókra történő íráshoz

2. Az Azure Site Recovery-műveletek kezelésére szóló engedélyre is szüksége lesz. A „Site Recovery-közreműködő” szerepkör minden olyan engedéllyel rendelkezik, amely a Site Recovery-műveletek Recovery Services-tárolóban történő kezeléséhez szükséges.


### <a name="verify-vm-outbound-access"></a>Virtuális gép kimenő hozzáférésének ellenőrzése

1. Győződjön meg arról, hogy nem használ hitelesítési proxyt a migrálni kívánt virtuális gépek hálózati kapcsolatának vezérlésére. 
2. Ebben az oktatóanyagban feltételezzük, hogy a migrálni kívánt virtuális gépek hozzáférnek az internethez, és nem használ tűzfalproxyt a kimenő hozzáférés vezérléséhez. Ha használ ilyet, ellenőrizze az [itt](azure-to-azure-tutorial-enable-replication.md#configure-outbound-network-connectivity) szereplő követelményeket.

### <a name="verify-vm-certificates"></a>Virtuális gép tanúsítványainak ellenőrzése

Ellenőrizze, hogy a legújabb főtanúsítványok mindegyike megtalálható-e a migrálni kívánt Azure-beli virtuális gépeken. Ha a legújabb főtanúsítványok nem találhatók, a virtuális gépet biztonsági okokból nem lehet regisztrálni a Site Recoveryben.

- A Windows rendszerű virtuális gépek esetében az összes új Windows-frissítést telepíteni kell a virtuális gépen, így a megbízható főtanúsítványok mindegyike megtalálható lesz a számítógépen. Leválasztott környezet esetén hajtsa végre a vállalat szabványos Windows Update- és tanúsítványfrissítési folyamatait.
- Linux rendszerű virtuális gépek esetében kövesse a Linux-terjesztőtől származó útmutatást a legújabb megbízható főtanúsítványok és a visszavont tanúsítványok listájának lekéréséhez a virtuális gépen.



## <a name="create-a-vault"></a>Tároló létrehozása

A forrásrégió kivételével bármelyik régióban létrehozhat tárolót.

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) > **Recovery Services** szolgáltatásba.
2. Kattintson az **Erőforrás létrehozása** > **Figyelés + felügyelet** > **Backup és Site Recovery** lehetőségre.
3. A **Név** mezőben adja meg a **ContosoVMVault** rövid nevet. Ha egynél több előfizetéssel rendelkezik, válassza ki ezek közül a megfelelőt.
4. Hozzon létre egy **ContosoRG** nevű erőforráscsoportot.
5. Válassza ki a kívánt Azure-régiót. A támogatott régiók megtekintéséhez olvassa el az [Azure Site Recovery – Díjszabás](https://azure.microsoft.com/pricing/details/site-recovery/) című cikknek a földrajzi elérhetőséggel foglalkozó részét.
6. Ha gyors hozzáférést szeretne a tárolóhoz az irányítópultról, kattintson a **Rögzítés az irányítópulton**, majd a **Létrehozás** gombra.

   ![Új tároló](./media/tutorial-migrate-azure-to-azure/azure-to-azure-vault.png)

Az új tároló megjelenik az **Irányítópult** **Minden erőforrás** részében, illetve a központi **Recovery Services-tárolók** oldalon.






## <a name="select-the-source"></a>Forrás kiválasztása

1. A Recovery Services-tárolókban kattintson a **ConsotoVMVault** > **+Replikálás** elemre.
2. A **Forrás** beállításnál válassza az **Azure** értéket.
3. A **Forrás helye** értékeként válassza ki azt az Azure-régiót, ahol az adott virtuális gépek éppen futnak.
4. Válassza ki a Resource Manager-alapú üzemi modellt. Válassza ki a **forrásként szolgáló erőforráscsoportot**.
5. Kattintson az **OK** gombra a beállítások mentéséhez.


## <a name="enable-replication-for-azure-vms"></a>A replikáció engedélyezése Azure-beli virtuális gépekhez

A Site Recovery lekéri az előfizetéshez és az erőforráscsoporthoz társított virtuális gépek listáját.


1. Az Azure Portalon kattintson a **Virtuális gépek** elemre.
2. Válassza ki a migrálni kívánt virtuális gépet. Ezután kattintson az **OK** gombra.
3. A **Beállítások** területen kattintson a **Vészhelyreállítás** elemre.
4. A **Vészhelyreállítás konfigurálása** > **Célrégió** részben válassza ki a célrégiót, amelybe a replikálást végezni kívánja.
5. A jelen oktatóanyag esetében fogadja el a többi alapértelmezett értéket.
6. Kattintson a **Replikáció engedélyezése** lehetőségre. Ekkor elindul a virtuális gép replikálásának engedélyezési feladata.

    ![replikáció engedélyezése](media/tutorial-migrate-azure-to-azure/settings.png)

 

## <a name="run-a-failover"></a>Feladatátvétel futtatása

1. A **Beállítások** > **Replikált elemek** területen kattintson a gépre, majd a **Feladatátvétel** ikonra.
2. A **Feladatátvétel** területen válassza a **Legújabb** elemet. Ehhez a forgatókönyvhöz nem kell figyelembe venni a titkosítási kulcs beállítását.
3. Válassza a **Gép leállítása a feladatátvétel megkezdése előtt** lehetőséget. A Site Recovery megkísérli leállítani a forrás virtuális gépeket a feladatátvétel indítása előtt. A feladatátvételi akkor is folytatódik, ha a leállítás meghiúsul. A feladatátvételi folyamatot a **Feladatok** lapon követheti nyomon.
4. Ellenőrizze, hogy az Azure-beli virtuális gép a várt módon jelenik-e meg az Azure-ban.
5. A **Replikált elemek** listában kattintson a jobb gombbal a virtuális gépre, majd kattintson a **Véglegesítés** parancsra. Ez befejezi a migrálási folyamatot.
6. A véglegesítés befejezése után kattintson a **Replikálás letiltása** elemre.  Ezzel leállítja a virtuális gép replikálását.



## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy másik régióba migrált egy Azure-beli virtuális gépet. Most konfigurálhatja a migrált virtuális gép vészhelyreállítását.

> [!div class="nextstepaction"]
> [Vészhelyreállítás beállítása migrálás után](azure-to-azure-quickstart.md)

