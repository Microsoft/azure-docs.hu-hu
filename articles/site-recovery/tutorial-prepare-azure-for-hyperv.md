---
title: Az Azure előkészítése a Hyper-V vész-helyreállításra Azure Site Recovery
description: Ismerje meg, hogyan készítheti elő az Azure-t a helyszíni Hyper-V virtuális gépek vész-helyreállításához Azure Site Recovery használatával
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: tutorial
ms.date: 11/14/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 5ca03336fc60abbb458615e922a2556dd21a79a5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101739678"
---
# <a name="prepare-azure-resources-for-hyper-v-disaster-recovery"></a>Azure-erőforrások előkészítése a Hyper-V vész-helyreállításhoz

 [Azure site Recovery](site-recovery-overview.md) segíti az üzletmenet folytonosságát és a vész-helyreállítást (BCDR) azáltal, hogy a tervezett és nem tervezett leállások során futó üzleti alkalmazásokat tart. A Site Recovery felügyeli és koordinálja a helyszíni gépek és az Azure-beli virtuális gépek vészhelyreállítását, beleértve a replikálást, a feladatátvételt és a helyreállítást.

Ez az oktatóanyag az első egy olyan sorozatban, amely leírja, hogyan állítható be a vész-helyreállítás a helyszíni Hyper-V virtuális gépekhez.

> [!NOTE]
> A forgatókönyvek legegyszerűbb telepítési útvonalát bemutató oktatóanyagokat tervezünk. Ezek az oktatóanyagok az alapértelmezett beállításokat használják, ha lehetséges, és nem jelenítik meg az összes lehetséges beállítást és elérési utat. További információkért tekintse meg az egyes kapcsolódó forgatókönyvek "útmutató" című szakaszát.

Ez az oktatóanyag bemutatja, hogyan készítheti elő az Azure-összetevőket, amikor helyszíni virtuális gépeket (Hyper-V) szeretne replikálni az Azure-ba. A következőket fogja megtanulni:

> [!div class="checklist"]
> * Ellenőrizze, hogy az Azure-fiók rendelkezik-e replikálási engedélyekkel.
> * Hozzon létre egy Azure Storage-fiókot, amely a replikált gépek lemezképeit tárolja.
> * Hozzon létre egy Recovery Services tárolót, amely a virtuális gépek és egyéb replikációs összetevők metaadatait és konfigurációs adatait tárolja.
> * Azure-hálózat beállítása. Ha az Azure-beli virtuális gépek a feladatátvételt követően jönnek létre, azok ehhez a hálózathoz csatlakoznak.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).

## <a name="sign-in"></a>Bejelentkezés

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="verify-account-permissions"></a>Fiókengedélyek ellenőrzése

Ha csak az ingyenes Azure-fiókot hozta létre, akkor Ön az előfizetés rendszergazdája. Ha nem Ön a rendszergazda, akkor a rendszergazdával együttműködve rendelje hozzá a szükséges engedélyeket. Egy új virtuális gép replikálásának engedélyezéséhez a következő engedélyekkel kell rendelkeznie:

- Virtuális gépek létrehozása a kiválasztott erőforráscsoportban.
- Virtuális gépek létrehozása a kiválasztott virtuális hálózaton.
- Írás a kiválasztott tárfiókra.

A feladatok elvégzéséhez a fiókját hozzá kell rendelni a virtuális gép közreműködői beépített szerepköréhez. A tárolóban Site Recovery műveletek kezeléséhez a fióknak hozzá kell rendelnie a Site Recovery közreműködő beépített szerepkört.

## <a name="create-a-storage-account"></a>Tárfiók létrehozása

A replikált gépek rendszerképeit egy Azure-tároló tartalmazza. Az Azure-beli virtuális gépek a tárolóból lesznek létrehozva a helyszínről az Azure-ba történő feladatátvétel során. A tárfióknak és a Recovery Services-tárolónak ugyanabban a régióban kell elhelyezkednie.

1. A [Azure Portal](https://portal.azure.com) menüben válassza az **erőforrás létrehozása**  >  **Storage**  >  **-fiók – blob, fájl, tábla, üzenetsor** lehetőséget.
2. A **Tárfiók létrehozása** területen írja be a fiók nevét.  A választott névnek egyedinek kell lennie az Azure-ban, 3 – 24 karakter hosszúnak kell lennie, és csak kisbetűket és számokat használjon. Ebben az oktatóanyagban használja a **contosovmsacct1910171607**-t.
3. Az **Üzemi modell** mezőben válassza a **Resource Manager** lehetőséget.
4. A **Fiók típusa** területen válassza a **Storage (általános célú v1)** lehetőséget. Ne válasszon blob-tárolót.
5. A **Replikáció** mezőben válassza az alapértelmezett **Írásvédett georedundáns tárolás** értéket a tárhely-redundanciához.
6. A **Teljesítmény** mezőben válassza a **Standard** lehetőséget. Ezután a **hozzáférési szint** területen válassza az alapértelmezett beállítást a **gyors** beállításnál.
7. Az **előfizetés** mezőben válassza ki azt az előfizetést, amelyben létre szeretné hozni az új Storage-fiókot.
8. Az **Erőforráscsoport** mezőben adjon meg egy új erőforráscsoportot. Az Azure-erőforráscsoport olyan logikai tároló, amelyben az Azure-erőforrások üzembe helyezése és kezelése történik. Ebben az oktatóanyagban használja a **ContosoRG**-t.
9. A **hely** mezőben válassza ki a Storage-fiók földrajzi helyét. Ehhez az oktatóanyaghoz használja a **Nyugat-Európát**.
10. Kattintson a **Létrehozás** gombra a tárfiók létrehozásához.

   ![Képernyőkép a Storage-fiók létrehozásának lehetőségeiről.](media/tutorial-prepare-azure/create-storageacct.png)

## <a name="create-a-recovery-services-vault"></a>Helyreállítási tár létrehozása

1. A Azure Portal válassza az **+ erőforrás létrehozása** lehetőséget, majd keresse meg Recovery Services az Azure Marketplace-en.
2. Válassza a **Backup és site Recovery (OMS)** lehetőséget. Ezután a **biztonsági mentés és site Recovery** lapon válassza a **Létrehozás** lehetőséget.
1. A **Recovery Services-tároló > neve** mezőben adjon meg egy rövid nevet a tároló azonosításához. Ehhez az oktatóanyaghoz használja a **ContosoVMVault** nevet.
2. Az **erőforráscsoport** területen válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy újat. Ebben az oktatóanyagban használja a **contosoRG**-t.
3. A **hely** mezőben válassza ki azt a régiót, ahol a tárolót el szeretné helyezni. Ehhez az oktatóanyaghoz használja a **Nyugat-Európát**.
4. Ha gyorsan el szeretné érni a tárolót az irányítópultról, válassza a **rögzítés az irányítópulton**  >  **Létrehozás** lehetőséget.

![A Recovery Services-tár létrehozása lap képernyőképe.](./media/tutorial-prepare-azure/new-vault-settings.png)

Az új tároló megjelenik az **irányítópult**  >  **minden erőforrás** és a fő **Recovery Services** -tárolók lapon.

## <a name="set-up-an-azure-network"></a>Azure-hálózat beállítása

Amikor a feladatátvétel után Azure-beli virtuális gépek jönnek létre a tárolóból, ehhez az Azure-hálózathoz csatlakoznak.

1. Az [Azure Portalon](https://portal.azure.com) válassza az **Erőforrás létrehozása** > **Hálózatkezelés** > **Virtuális hálózat** lehetőséget. Hagyja kiválasztva a Resource Manager üzemi modellt.
2. A **Név** mezőben adjon meg egy hálózatnevet. A névnek egyedinek kell lennie az Azure-erőforráscsoporton belül. Ebben az oktatóanyagban használja a **ContosoASRnet**-t.
3. Itt adhatja meg azt az erőforráscsoportot, amelyben létre kívánja hozni a hálózatot. Ebben az oktatóanyagban használja a meglévő erőforráscsoport- **contosoRG**.
4. A **címtartomány** mezőben adja meg a **10.0.0.0/24** értéket a hálózat tartományának megfelelően. Nincs alhálózat ehhez a hálózathoz.
5. Az **Előfizetés** mezőben válassza ki azt az előfizetést, amelyben létre kívánja hozni a hálózatot.
6. A **hely** mezőben válassza a **Nyugat-Európa** lehetőséget. A hálózatnak és a Recovery Services-tárolónak ugyanabban a régióban kell elhelyezkednie.
7. Hagyja meg az alapszintű DDoS-védelem alapértelmezett beállításait, és ne legyenek szolgáltatási végpontok a hálózaton.
8. Válassza a **Létrehozás** lehetőséget.

![Képernyőkép a virtuális hálózati beállítások létrehozásáról.](media/tutorial-prepare-azure/create-network.png)

A virtuális hálózat néhány másodperc alatt létrejön. Miután létrejött, megjelenik a Azure Portal irányítópulton.

## <a name="useful-links"></a>Hasznos hivatkozások

Ismerkedjen meg a következőkkel:
- [Azure-hálózatok](../virtual-network/virtual-networks-overview.md)
- [Felügyelt lemezek](../virtual-machines/managed-disks-overview.md)



## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Helyszíni Hyper-V-infrastruktúra előkészítése az Azure-ba való vész-helyreállításhoz](hyper-v-prepare-on-premises-tutorial.md)
