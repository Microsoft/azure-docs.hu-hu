---
title: Azure Site Recovery erőforrások előkészítése az Azure VMware-Megoldásbeli virtuális gépek vész-helyreállításához
description: Ismerje meg, hogyan készítheti elő az Azure-erőforrásokat az Azure VMware-megoldások Azure Site Recovery használatával történő vész-helyreállításához.
services: site-recovery
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/29/2020
ms.author: harshacs
ms.custom: MVC
ms.openlocfilehash: 2bd305e3760a8c3d743037e7d90b71f5e9579eda
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93395478"
---
# <a name="prepare-azure-site-recovery-resources-for-disaster-recovery-of-azure-vmware-solution-vms"></a>Azure Site Recovery erőforrások előkészítése az Azure VMware-Megoldásbeli virtuális gépek vész-helyreállításához

Ez a cikk az Azure-erőforrások és-összetevők előkészítését ismerteti, hogy az Azure VMware-megoldás virtuális gépei vész-helyreállítását [Azure site Recovery](site-recovery-overview.md) szolgáltatás használatával állítsa be. Az [Azure VMware megoldás](../azure-vmware/introduction.md) saját felhőket biztosít az Azure-ban. Ezek a privát felhők olyan vSphere-fürtöket tartalmaznak, amelyek dedikált operációs rendszer nélküli Azure-infrastruktúrából épülnek fel.

Ez a cikk egy sorozat első oktatóanyaga, amely bemutatja, hogyan állíthatja be a vész-helyreállítást az Azure VMware-megoldás virtuális gépei számára. 


Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Győződjön meg arról, hogy az Azure-fiók replikációs engedélyekkel rendelkezik.
> * Recovery Services-tároló létrehozása. A tároló virtuális gépek és más replikációs összetevők metaadatait és konfigurációs adatait tárolja.
> * Azure-beli virtuális hálózat (VNet) beállítása. Ha az Azure-beli virtuális gépek a feladatátvételt követően jönnek létre, azok ehhez a hálózathoz csatlakoznak.

> [!NOTE]
> Az oktatóanyagok a forgatókönyvek legegyszerűbb telepítési útvonalát mutatják be. Ahol lehet, az alapértelmezett beállításokat használják, és nem mutatják be az összes lehetséges beállítást és útvonalat. Részletes utasításokért tekintse át a Site Recovery tartalomjegyzékének útmutató című cikkét.

> [!NOTE]
> A Azure Site Recovery Azure VMware-megoldáshoz való használatának néhány fogalma átfedésben van a helyszíni VMware virtuális gépek vész-helyreállításával, ezért a dokumentációt ennek megfelelően kereszthivatkozásként kell megadni.

## <a name="before-you-start"></a>Előkészületek

- Azure VMware-megoldás saját Felhőbeli [üzembe helyezése](../azure-vmware/tutorial-create-private-cloud.md) az Azure-ban
- A [VMware](vmware-azure-architecture.md) vész-helyreállítási architektúrájának áttekintése
- Gyakori kérdések a [VMware](vmware-azure-common-questions.md) -ről

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/). Ezután jelentkezzen be a [Azure Portalba](https://portal.azure.com).


## <a name="verify-account-permissions"></a>Fiókengedélyek ellenőrzése

Ha most hozta létre az ingyenes Azure-fiókját, akkor Ön az előfizetés rendszergazdája, és rendelkezik a szükséges engedélyekkel. Ha nem Ön az előfizetés rendszergazdája, akkor a rendszergazdával együttműködve rendelje hozzá a fiókhoz a szükséges engedélyeket. Egy új virtuális gép replikálásának engedélyezéséhez a következő engedélyekkel kell rendelkeznie:

- Virtuális gépek létrehozása a kiválasztott erőforráscsoportban.
- Virtuális gépek létrehozása a kiválasztott virtuális hálózaton.
- Írás egy Azure Storage-fiókba.
- Írás egy Azure-beli felügyelt lemezre.

A feladatok végrehajtásához az Ön fiókjának rendelkeznie kell a Virtuális gépek közreműködője beépített szerepkörrel. Emellett a tárolóban Site Recovery műveletek kezeléséhez a fióknak hozzá kell rendelnie a Site Recovery közreműködő beépített szerepkört.


## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

1. A Azure Portal menüben válassza az **erőforrás létrehozása** lehetőséget, és keresse meg a piactéren a **helyreállítást**.
2. Válassza a **biztonsági mentés és site Recovery** lehetőséget a keresési eredmények közül, majd a biztonsági mentés és a site Recovery lapon kattintson a **Létrehozás** gombra. 
3. A Recovery Services-tároló **létrehozása** lapon válassza ki az **előfizetést**. **Contoso-előfizetést** használunk.
4. Az **erőforráscsoport** területen válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy újat. Ebben az oktatóanyagban a **contosoRG**-ot használjuk.
5. A tár **neve** mezőben adjon meg egy rövid nevet a tároló azonosításához. Ehhez az oktatóanyag-sorozathoz a **ContosoVMVault** nevet használjuk.
6. A **régió** területen válassza ki azt a régiót, amelyben a tárolót el szeretné helyezni. a **Nyugat-Európa** beállítást használjuk.
7. Válassza az **Áttekintés + létrehozás** lehetőséget.

   ![A Recovery Services-tár létrehozása lap képernyőképe.](./media/tutorial-prepare-azure/new-vault-settings.png)

   Az új tároló ekkor megjelenik az **irányítópult**  >  **minden erőforrás** és a fő **Recovery Services** -tárolók lapon.

## <a name="set-up-an-azure-network"></a>Azure-hálózat beállítása

 Az Azure VMware-megoldás virtuális gépei replikálódnak az Azure Managed Disks szolgáltatásba. Feladatátvétel esetén az Azure-beli virtuális gépek ezekből a felügyelt lemezekről jönnek létre, és az ebben az eljárásban megadott Azure-hálózathoz csatlakoznak.

1. Az [Azure Portalon](https://portal.azure.com) válassza az **Erőforrás létrehozása** > **Hálózatkezelés** > **Virtuális hálózat** lehetőséget.
2. Válassza ki a **Resource Managert** a telepítési modellként.
3. A **Név** mezőben adjon meg egy hálózatnevet. A névnek egyedinek kell lennie az Azure-erőforráscsoporton belül. Ebben az oktatóanyagban a **ContosoASRnet** nevet használjuk.
4. A **címterület** mezőben adja meg a virtuális hálózat CÍMTARTOMÁNYT a CdR jelöléssel. A **10.1.0.0/24-** et használjuk.
5. Az **Előfizetés** mezőben válassza ki azt az előfizetést, amelyben létre kívánja hozni a hálózatot.
6. Itt adhatja meg azt az **erőforráscsoportot** , amelyben a hálózat létrejön. A **contosoRG** meglévő erőforráscsoportot használjuk.
7. A **hely** mezőben válassza ki ugyanazt a régiót, mint amelyben a Recovery Services-tárolót létrehozták. Az oktatóanyagban ez **Nyugat-Európa**. A hálózatnak ugyanabban a régióban kell lennie, mint a tárolónak.
8. A **címtartomány** mezőben adja meg a hálózat tartományát. A **10.1.0.0/24**-et használja, és nem használ alhálózatot.
9. Elhagyjuk az alapszintű DDoS-védelem alapértelmezett lehetőségeit, a szolgáltatási végpontok nélkül vagy a tűzfalat a hálózaton.
9. Válassza a **Létrehozás** lehetőséget.

   ![Képernyőkép a virtuális hálózati beállítások létrehozásáról.](media/tutorial-prepare-azure/create-network.png)

A virtuális hálózat néhány másodperc alatt létrejön. Miután létrejött, megjelenik a Azure Portal irányítópulton.




## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Az infrastruktúra előkészítése](avs-tutorial-prepare-avs.md)
- Az Azure Networks [ismertetése](../virtual-network/virtual-networks-overview.md).
- [További információ a](../virtual-machines/managed-disks-overview.md) felügyelt lemezekről.
