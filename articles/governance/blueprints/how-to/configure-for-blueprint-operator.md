---
title: Környezet beállítása a Blueprint Operator számára
description: Ismerje meg, hogyan konfigurálhatja Azure-környezetét a Blueprint Operator Azure beépített szerepkörrel való használatra.
ms.date: 02/05/2021
ms.topic: how-to
ms.openlocfilehash: b43a33f4bac92903bd07454041d11850f217d480
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100558719"
---
# <a name="configure-your-environment-for-a-blueprint-operator"></a>A környezet konfigurálása Blueprint Operatorhoz

A terv-definíciók és a tervrajz-hozzárendelések kezelése különböző csapatokhoz rendelhető. Az építész vagy az irányítási csapat feladata az, hogy a terv definícióinak életciklus-felügyeletéhez felelősek legyenek, miközben egy operatív csapat felelős a központilag ellenőrzött tervrajz-definíciók hozzárendeléseinek kezeléséért.

A **tervrajz-kezelő** beépített szerepköre kifejezetten az ilyen típusú forgatókönyvekben való használatra készült. A szerepkör lehetővé teszi, hogy a műveleti típusok csoportja felügyelje a szervezeti terv definícióinak hozzárendelését, de ne módosítsa őket. Ehhez szükség van néhány konfigurációra az Azure-környezetben, és ez a cikk ismerteti a szükséges lépéseket.

## <a name="grant-permission-to-the-blueprint-operator"></a>Engedély megadása a Blueprint operátornak

Első lépésként adja meg a **terv operátori** szerepkört ahhoz a fiókhoz vagy biztonsági csoporthoz (ajánlott), amely a tervrajzok hozzárendelését fogja végrehajtani. Ezt a műveletet a felügyeleti csoport hierarchiájának legmagasabb szintjén kell végrehajtani, amely magában foglalja az összes felügyeleti csoportot és előfizetést, és az operatív csapatnak terv-hozzárendelési jogosultsággal kell rendelkeznie. Az engedélyek megadásakor ajánlott a legalacsonyabb jogosultsági szint elvének követése.

1. Ajánlott [Biztonsági csoport létrehozása és Tagok hozzáadása](../../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)

1. A **Blueprint operátor** [Azure-szerepkörének kiosztása](../../../role-based-access-control/role-assignments-portal.md) a fiókhoz vagy a biztonsági csoporthoz

## <a name="user-assign-managed-identity"></a>Felhasználó – felügyelt identitások kiosztása

A tervrajzok definíciója rendszer által hozzárendelt vagy felhasználó által hozzárendelt felügyelt identitásokat használhat. A **terv operátori** szerepkör használatakor azonban a terv definícióját a felhasználó által hozzárendelt felügyelt identitás használatára kell beállítani. Emellett a **terv operátori** szerepkörrel rendelkező fiókot vagy biztonsági csoportot meg kell adni a **felügyelt identitás-kezelő** szerepkörnek a felhasználó által hozzárendelt felügyelt identitáson. Ezen engedély nélkül a terv-hozzárendelések sikertelenek az engedélyek hiánya miatt.

1. [Felhasználó által hozzárendelt felügyelt identitás létrehozása](../../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) egy hozzárendelt terv használatával.

1. Adja meg a felhasználó által hozzárendelt felügyelt identitást a terv definíciójában a kívánt hatókörhöz szükséges szerepkörökkel vagy engedélyekkel.

1. [Rendelje hozzá](../../../role-based-access-control/role-assignments-portal.md) a **felügyelt identitás kezelője** Azure-szerepkörét a fiókhoz vagy a biztonsági csoporthoz. Hatókör a szerepkör-hozzárendelés az új, felhasználó által hozzárendelt felügyelt identitáshoz.

1. A **terv operátorként** [rendeljen hozzá egy tervet](../create-blueprint-portal.md#assign-a-blueprint) , amely az új, felhasználó által hozzárendelt felügyelt identitást használja.

## <a name="next-steps"></a>További lépések

- Tudnivalók a [tervek életciklusáról](../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A tervek hozzárendelése során felmerülő problémák megoldása [általános hibaelhárítással](../troubleshoot/general.md).