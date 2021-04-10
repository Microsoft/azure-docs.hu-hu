---
title: Az Azure Arc áttekintése
description: Ismerje meg, hogy mi az Azure arc, és hogyan segíti az ügyfelek a hibrid erőforrásaik kezelését és irányítását más Azure-szolgáltatásokkal és-funkciókkal.
ms.date: 03/02/2021
ms.topic: overview
ms.openlocfilehash: 33c9d6ca87c3d8d2d8920ff429902f5876bbdc59
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101650192"
---
# <a name="azure-arc-overview"></a>Az Azure Arc áttekintése

Napjainkban a vállalatok egyre összetettebb környezetek szabályozásával és szabályozásával küzdenek. Ezek a környezetek az adatközpontokban, több felhőben és az Edge-ben is kiterjeszthetők. Minden környezet és felhő a saját különálló felügyeleti eszközeivel rendelkezik, amelyeket tanulni és működésbe kell hoznia.

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

* A GitOps használatával telepítheti a konfigurációt egy vagy több fürtön a git-Tárházak között.

*  A Kubernetes-fürtök inkompatibilitása és konfigurálása a Azure Policy használatával.

* Az Azure-beli adatszolgáltatásokat bármilyen Kubernetes-környezetben futtathatja, mintha az Azure-ban fut (különösen az Azure SQL felügyelt példánya és a Azure Database for PostgreSQL nagy kapacitású, például frissítésekkel, frissítésekkel, biztonsággal és figyeléssel). Rugalmas skálázást használhat, és az alkalmazások leállása nélkül is alkalmazhatja a frissítéseket, még az Azure-hoz való folyamatos kapcsolódás nélkül is

* A Azure Portal, az Azure CLI, a Azure PowerShell vagy az Azure REST APIt használó Azure arc-erőforrásokkal való használatra szolgáló egységes élmény.

## <a name="how-much-does-azure-arc-cost"></a>Mennyibe kerül az Azure arc?

Az alábbiakban az Azure arc által jelenleg elérhető funkciók díjszabása olvasható.

### <a name="arc-enabled-servers"></a>Arc-kompatibilis kiszolgálók

A következő Azure ív-vezérlési sík funkció díjmentesen elérhető:

* Erőforrás-szervezet Azure felügyeleti csoportok és címkék használatával.

* Keresés és indexelés az Azure Resource Graph használatával.

* Hozzáférés és biztonság az Azure RBAC és előfizetésekkel.

* Környezetek és automatizálás sablonok és bővítmények segítségével.

* Frissítéskezelés

Az arc-kompatibilis kiszolgálókon használt bármely Azure-szolgáltatás (például Azure Security Center vagy Azure Monitor) a szolgáltatás díjszabása szerint lesz felszámítva. További információkért tekintse meg az [Azure díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/).

### <a name="azure-arc-enabled-kubernetes"></a>Azure Arc-kompatibilis Kubernetes

Az arc-kompatibilis Kubernetes használt bármely Azure-szolgáltatás (például Azure Security Center vagy Azure Monitor) a szolgáltatás díjszabása szerint lesz felszámítva. Az Azure arc-kompatibilis Kubernetes található konfigurációk díjszabásával kapcsolatos további információkért lásd az [Azure díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/).

### <a name="azure-arc-enabled-data-services"></a>Azure Arc-kompatibilis adatszolgáltatások

Az aktuális előzetes verzióban az Azure arc-kompatibilis adatszolgáltatások külön díj nélkül elérhetők.

## <a name="next-steps"></a>Következő lépések

* Ha többet szeretne megtudni az ív használatára képes kiszolgálókról, tekintse meg a következő [áttekintést](./servers/overview.md) :

* Ha többet szeretne megtudni az arc-kompatibilis Kubernetes, tekintse meg a következő [áttekintést](./kubernetes/overview.md) :

* Az arc-kompatibilis adatszolgáltatásokkal kapcsolatos további tudnivalókért tekintse meg a következő [áttekintést](https://azure.microsoft.com/services/azure-arc/hybrid-data-services/) :

* Arc-kompatibilis szolgáltatások az [Jumpstart megvalósíthatósági igazolása](https://azurearcjumpstart.io/azure_arc_jumpstart/) alapján
