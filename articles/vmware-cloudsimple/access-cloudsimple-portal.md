---
title: Azure VMware-megoldás elérése a CloudSimple-portálon
description: Leírja, hogyan érhető el a VMware Solution by CloudSimple portálja az Azure Portalról
author: Ajayan1008
ms.author: v-hborys
ms.date: 06/04/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0aa7a9a1f19a9d4fb2c555b08753e0b57c657974
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97895155"
---
# <a name="access-the-vmware-solution-by-cloudsimple-portal-from-the-azure-portal"></a>A VMware-megoldás elérése a CloudSimple-portálon a Azure Portal

Az egyszeri bejelentkezés támogatott a CloudSimple-portálhoz való hozzáféréshez. Miután bejelentkezett a Azure Portalba, a CloudSimple-portálra való bejelentkezés nélkül is hozzáférhet. Amikor először fér hozzá a CloudSimple-portálhoz, a rendszer felszólítja, hogy engedélyezze a [CloudSimple szolgáltatás-engedélyezési](#consent-to-cloudsimple-service-authorization-application) alkalmazást.  Az engedélyezés egyszeri művelet.

## <a name="before-you-begin"></a>Előkészületek

A beépített **tulajdonosi** és **közreműködői** szerepkörökkel rendelkező felhasználók hozzáférhetnek a CloudSimple-portálhoz.  A szerepköröket olyan erőforráscsoporthoz kell konfigurálni, ahol a CloudSimple szolgáltatás telepítve van.  A szerepkörök is konfigurálhatók a CloudSimple szolgáltatás objektumán.  A szerepkör ellenőrzésével kapcsolatos további információkért tekintse meg a [szerepkör-hozzárendelések megtekintése](../role-based-access-control/check-access.md) című cikket. Csak a beépített **tulajdonosi** és **közreműködői** szerepkörökkel rendelkező felhasználók férhetnek hozzá a CloudSimple-portálhoz.  A szerepköröket az előfizetésen kell konfigurálni.  A szerepkör ellenőrzésével kapcsolatos további információkért tekintse meg a [szerepkör-hozzárendelések megtekintése](../role-based-access-control/check-access.md) című cikket.

Ha egyéni szerepköröket használ, a szerepkörnek a következő műveletek bármelyikével kell rendelkeznie ```Actions``` .  További információ az egyéni szerepkörökről: [Egyéni Azure-szerepkörök](../role-based-access-control/custom-roles.md).  Ha bármelyik művelet a részét képezi ```NotActions``` , a felhasználó nem férhet hozzá a CloudSimple-portálhoz.

```
Microsoft.VMwareCloudSimple/*
Microsoft.VMwareCloudSimple/*/write
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*/write
```

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="access-the-cloudsimple-portal"></a>Hozzáférés a CloudSimple portáljához

1. Válassza az **Összes szolgáltatás** elemet.

2. Keressen rá a **CloudSimple Services** kifejezésre.

3. Válassza ki azt a CloudSimple-szolgáltatást, amelyen létre szeretné hozni a saját Felhőjét.

4. Az **Áttekintés** lapon kattintson **az Ugrás a CloudSimple portálra** elemre.  Ha első alkalommal éri el a CloudSimple-Azure Portal portált, a rendszer felszólítja, hogy engedélyezze a [CloudSimple szolgáltatás-engedélyezési](#consent-to-cloudsimple-service-authorization-application) alkalmazást. 

    ![A CloudSimple-portál elindítása](media/launch-cloudsimple-portal.png)

> [!NOTE]
> Ha kijelöl egy privát Felhőbeli műveletet (például egy privát felhő létrehozását vagy kibővítését) közvetlenül a Azure Portal, megnyílik a CloudSimple portál a jelzett oldalon.

A CloudSimple-portálon válassza a **Kezdőlap** lehetőséget a saját felhőkre vonatkozó összegző információk megjelenítéséhez. A privát felhők erőforrásai és kapacitása látható, valamint a riasztások és a beavatkozást igénylő feladatok. A gyakori feladatokhoz kattintson az oldal tetején található elnevezett ikonokra.

![Kezdőlap](media/cloudsimple-portal-home.png)

## <a name="consent-to-cloudsimple-service-authorization-application"></a>Hozzájárulás a CloudSimple Service Authorization alkalmazáshoz

Ha első alkalommal indítja el a CloudSimple-portált a Azure Portal, a CloudSimple szolgáltatás-engedélyezési alkalmazáshoz kell beleegyeznie.  Válassza az **elfogadás** lehetőséget a kért engedélyek megadásához és a CloudSimple-portál eléréséhez.

![Hozzájárulás a CloudSimple szolgáltatás engedélyezéséhez – rendszergazdák](media/cloudsimple-azure-consent.png)

Ha globális rendszergazdai jogosultsággal rendelkezik, akkor Ön hozzájárulhat a szervezetéhez.  Válassza a **Jóváhagyás a szervezet nevében** lehetőséget.

![Hozzájárulás a CloudSimple szolgáltatás engedélyezéséhez – globális rendszergazda](media/cloudsimple-azure-consent-global-admin.png)

Ha az engedélyei nem engedélyezik a hozzáférést a CloudSimple-portálhoz, forduljon a bérlő globális rendszergazdájához a szükséges engedélyek megadásához.  A globális rendszergazda hozzájárulhat a szervezet nevében.

![Hozzájárulás a CloudSimple szolgáltatás engedélyezéséhez – rendszergazdákat igényel](media/cloudsimple-azure-consent-requires-administrator.png)

## <a name="next-steps"></a>Következő lépések

* Megtudhatja, hogyan [hozhat létre saját felhőt](./create-private-cloud.md)
* Ismerje meg, hogyan [konfigurálhatja a saját felhőalapú környezetét](quickstart-create-private-cloud.md)
