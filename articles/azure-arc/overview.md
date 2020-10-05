---
title: Az Azure Arc áttekintése
description: Ismerje meg, hogy mi az Azure arc, és hogyan segíti az ügyfelek a hibrid erőforrásaik kezelését és irányítását más Azure-szolgáltatásokkal és-funkciókkal.
ms.date: 09/23/2020
ms.topic: overview
ms.openlocfilehash: 8e48378e5032768b3f56a5d99c1189e282ff37f8
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91714154"
---
# <a name="azure-arc-overview"></a>Az Azure Arc áttekintése

Napjainkban a vállalatok az egyre összetettebb környezet szabályozásával és szabályozásával küzdenek. Ezek a környezetek az adatközpontokban, több felhőben és az Edge-ben is kiterjeszthetők. Minden környezet és felhő saját, különálló felügyeleti eszközöket tartalmaz, amelyeket meg kell tanulnia és működésbe kell hoznia.

Ezzel párhuzamosan a New DevOps és a ITOps működési modelljei nehezen valósíthatók meg, mivel a meglévő eszközök nem támogatják az új Felhőbeli natív minták támogatását.

Az Azure arc egyszerűbbé teszi a szabályozást és a felügyeletet egy egységes, többfelhős és helyszíni felügyeleti platform megvalósításával. Az Azure arc lehetővé teszi, hogy a teljes környezetet egyetlen üvegtábla segítségével kezelje a meglévő erőforrások Azure Resource Manager való kivetítésével. Mostantól kezelheti a virtuális gépeket, a Kubernetes-fürtöket és az adatbázisokat úgy, mintha az Azure-ban futnak. Függetlenül attól, hogy hol laknak, használhat ismerős Azure-szolgáltatásokat és felügyeleti képességeket. Az Azure arc lehetővé teszi, hogy továbbra is a hagyományos ITOps használja, és bevezesse az új Felhőbeli natív mintázatok támogatásához szükséges DevOps-eljárásokat.

:::image type="content" source="./media/overview/azure-arc-control-plane.png" alt-text="Azure-beli ív felügyeletének vezérlési sík diagramja" border="false":::

Napjainkban az Azure arc lehetővé teszi az Azure-on kívül üzemeltetett következő erőforrástípusok kezelését:

* Kiszolgálók – Windows vagy Linux rendszerű fizikai és virtuális gépek.
* Kubernetes-fürtök – több Kubernetes-eloszlás támogatása.
* Azure adatszolgáltatások – Azure SQL Database és PostgreSQL nagy kapacitású Services.

## <a name="what-does-azure-arc-deliver"></a>Mit jelent az Azure arc?

Az Azure arc legfontosabb funkciói a következők:

* Konzisztens leltár, felügyelet, irányítás és biztonság megvalósítása a kiszolgálók számára a környezetében.

* Konfigurálja az Azure-beli virtuálisgép- [bővítményeket](./servers/manage-vm-extensions.md) az Azure felügyeleti szolgáltatások használatára a kiszolgálók figyeléséhez, biztonságossá tételéhez és frissítéséhez.

* Kubernetes-fürtök kezelése és szabályozása skálán.

* Az alkalmazások és a konfigurációk GitOps való üzembe helyezéséhez használjon programkód-alapú konfigurációt, közvetlenül a forrás-vezérlőelemről, például a GitHubról.

* A Kubernetes-fürtökön a Azure Policy használatával nulla érintéses megfelelőség és konfiguráció.

* Az Azure-beli adatszolgáltatásokat bármilyen Kubernetes-környezetben, például az Azure SQL felügyelt példányain és Azure Database for PostgreSQL nagy kapacitású is futtathatja, ha az Azure-ban futtatja a frissítéseket, a frissítéseket, a biztonságot és a figyelést. Rugalmasan méretezhető, a frissítések alkalmazása az alkalmazások leállása nélkül, még akkor is, ha nem kapcsolódik az Azure-hoz.

* A Azure Portal, az Azure CLI, a Azure PowerShell vagy az Azure REST APIt használó Azure arc-erőforrásokkal való használatra szolgáló egységes élmény.

## <a name="how-much-does-azure-arc-cost"></a>Mennyibe kerül az Azure arc?

Az alábbiakban az Azure arc által jelenleg elérhető funkciók díjszabása olvasható.

### <a name="arc-enabled-servers"></a>Arc-kompatibilis kiszolgálók

Az Azure arc-vezérlési sík funkció díjmentesen vehető igénybe.Ide tartoznak az alábbiak:

* Erőforrás-szervezet Azure felügyeleti csoportok és címkék használatával.

* Keresés és indexelés az Azure Resource Graph használatával.

* Hozzáférés és biztonság az Azure RBAC és előfizetésekkel.

* Környezetek és automatizálás sablonok és bővítmények segítségével.

* Frissítés kezelése

Az arc-kompatibilis kiszolgálókon használt bármely Azure-szolgáltatás (például Azure Security Center vagy Azure Monitor) a szolgáltatás díjszabása szerint lesz felszámítva. További információ: Azure- [díjszabási oldal](https://azure.microsoft.com/pricing/).

### <a name="azure-arc-enabled-kubernetes"></a>Azure Arc-kompatibilis Kubernetes

Az aktuális előzetes verzióban az Azure arc-kompatibilis Kubernetes díjmentesen vehető igénybe.

### <a name="azure-arc-enabled-data-services"></a>Azure Arc-kompatibilis adatszolgáltatások

Az aktuális előzetes verzióban az Azure arc-kompatibilis adatszolgáltatások díjmentesen vehetők igénybe.

## <a name="next-steps"></a>További lépések

* Ha többet szeretne megtudni az ív használatára képes kiszolgálókról, tekintse meg a következő [áttekintést](./servers/overview.md) :

* Ha többet szeretne megtudni az arc-kompatibilis Kubernetes, tekintse meg a következő [áttekintést](./kubernetes/overview.md) :

* Az arc-kompatibilis adatszolgáltatásokkal kapcsolatos további tudnivalókért tekintse meg a következő [áttekintést](https://azure.microsoft.com/services/azure-arc/hybrid-data-services/) :
