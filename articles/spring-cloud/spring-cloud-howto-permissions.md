---
title: Engedélyek használata az Azure Spring Cloud-ban
description: Ez a cikk bemutatja, hogyan hozhat létre egyéni szerepköröket az Azure Spring Cloud-beli engedélyekhez.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/04/2020
ms.custom: devx-track-java
ms.openlocfilehash: 0c0b3bd81e0f73a7879382e28516378bd722bc17
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96498642"
---
# <a name="how-to-use-permissions-in-azure-spring-cloud"></a>Engedélyek használata az Azure Spring Cloud-ban
Ebből a cikkből megtudhatja, hogyan hozhat létre olyan egyéni szerepköröket, amelyek engedélyeket delegálnak az Azure Spring Cloud-erőforrásokhoz. Az egyéni szerepkörök az [Azure beépített szerepköreit](../role-based-access-control/built-in-roles.md) különböző Stock-engedélyekkel bővítik.

A következő egyéni szerepköröket fogjuk megvalósítani:

* **Fejlesztői szerepkör**: 
    * Üzembe helyezés
    * Tesztelés
    * Alkalmazások újraindítása
    * Alkalmazhatja és módosíthatja az alkalmazások konfigurációját a git-tárházban
    * Lekérheti a napló streamjét
* **Ops – hely megbízhatóságának tervezése**: 
    * Alkalmazások újraindítása
    * Naplók lekérése
    * Az alkalmazások és konfigurációk nem módosíthatók
* **Azure-folyamatok/Jenkins/GitHub-műveletek szerepkör**:
    * Elvégezheti a létrehozási, olvasási, frissítési és törlési műveleteket.
    * Létrehozhat és konfigurálhat mindent az Azure Spring Cloud és a szolgáltatási példányon belüli alkalmazásokban: Azure-folyamatok, Jenkins-vagy GitHub-műveletek Terraform vagy ARM-sablonok használatával

## <a name="define-developer-role"></a>Fejlesztői szerepkör definiálása

A fejlesztői szerepkör magában foglalja az alkalmazások újraindítására és a naplózási adatfolyamok megtekintésére vonatkozó engedélyeket, de nem módosíthatja az alkalmazásokat, a konfigurációt.

### <a name="navigate-subscription-and-resource-group-access-control-iam"></a>Az előfizetés és az erőforráscsoport hozzáférés-vezérlésének (IAM) navigálása

A szerepkörök definiálásának megkezdéséhez kövesse az alábbi lépéseket.

1. A Azure Portal nyissa meg azt az előfizetést és erőforráscsoportot, amelyhez az egyéni szerepkört hozzá szeretné rendelni.
2. Nyissa meg a **hozzáférés-vezérlés (iam)**.
3. Kattintson a **+ Hozzáadás** gombra.
4. Kattintson az **Egyéni szerepkör hozzáadása** elemre.
5. Kattintson a **Tovább** gombra.

   ![Egyéni szerepkör létrehozása](media/spring-cloud-permissions/create-custom-role.png)

6. Kattintson az **engedélyek hozzáadása** lehetőségre.

   ![Engedélyek hozzáadása – indítás](media/spring-cloud-permissions/add-permissions.png)

### <a name="search-for-azure-spring-cloud-permissions"></a>Azure Spring Cloud-engedélyek keresése:
7. A keresőmezőbe keresse meg a *Microsoft.app*.
Válassza *Microsoft Azure Spring Cloud* elemet.

   ![Azure Spring Cloud kiválasztása](media/spring-cloud-permissions/spring-cloud-permissions.png)

8. Válassza ki a fejlesztői szerepkör engedélyeit:

A **Microsoft. AppPlatform/Spring** listából válassza a következőket:
* Írás: Azure Spring Cloud Service-példány létrehozása vagy frissítése
* Olvasás: Azure Spring Cloud Service-példány beszerzése
* Egyéb: az Azure Spring Cloud Service-példány tesztelési kulcsainak listázása

A **Microsoft. AppPlatform/Spring/apps** elemnél válassza a következő lehetőséget:
* Olvasás: Microsoft Azure Spring Cloud-alkalmazás olvasása
* Egyéb: Microsoft Azure tavaszi Felhőbeli alkalmazás erőforrás-feltöltési URL-címének beolvasása

A **Microsoft. AppPlatform/Spring/apps/kötések** területen válassza a következőket:
* Olvasás: Microsoft Azure Spring Cloud Application-kötés olvasása

A **Microsoft. AppPlatform/Spring/apps/központi telepítések** közül válassza a következőt:
* Írás: Microsoft Azure Spring Cloud Application üzembe helyezésének írása
* Olvasás: Microsoft Azure Spring Cloud Application üzembe helyezésének olvasása
* Egyéb: Microsoft Azure Spring Cloud Application üzembe helyezésének megkezdése
* Egyéb: Microsoft Azure Spring Cloud Application üzembe helyezésének leállítása
* Egyéb: Microsoft Azure Spring Cloud Application üzembe helyezésének újraindítása
* Egyéb: Microsoft Azure Spring Cloud Application telepítési naplófájl URL-címének beolvasása

A **Microsoft. AppPlatform/Spring/apps/tartományok** területen válassza a következőket:
* Olvasás: Microsoft Azure tavaszi Felhőbeli alkalmazás egyéni tartományának olvasása

A **Microsoft. AppPlatform/Spring/Certificates** listából válassza a következőt:
* Olvasás: Microsoft Azure tavaszi Felhőbeli tanúsítvány olvasása

A **Microsoft. AppPlatform/Locations/operationResults/Spring** listából válassza a következőket:
* Olvasás: olvasási művelet eredménye

A **Microsoft. AppPlatform/Locations/operationStatus/operationId** lapon válassza a következőket:
* Olvasás: olvasási művelet állapota

    [![Developler engedélyek ](media/spring-cloud-permissions/developer-permissions-box.png) létrehozása](media/spring-cloud-permissions/developer-permissions-box.png#lightbox)

9. Kattintson a **Hozzáadás** parancsra.

10. Tekintse át az engedélyeket.

11. Kattintson az **Áttekintés és létrehozás** elemre.

## <a name="define-devops-engineer-role"></a>DevOps-mérnök szerepének meghatározása
Ez az eljárás egy, az Azure Spring Cloud apps üzembe helyezésére, tesztelésére és újraindítására vonatkozó engedélyekkel rendelkező szerepkört határoz meg.

1. Ismételje meg az eljárást az előfizetés, az erőforráscsoport és a hozzáférés-vezérlés (IAM) navigálása érdekében.
2. Válassza ki a DevOps mérnök szerepkörhöz tartozó engedélyeket:

A **Microsoft. AppPlatform/Spring** listából válassza a következőket:
* Írás: Azure Spring Cloud Service-példány létrehozása vagy frissítése
* Törlés: az Azure Spring Cloud Service-példány törlése
* Olvasás: Azure Spring Cloud Service-példány beszerzése
* Egyéb: az Azure Spring Cloud Service instance teszt végpontjának engedélyezése
* Egyéb: az Azure Spring Cloud Service instance teszt végpontjának letiltása
* Egyéb: az Azure Spring Cloud Service-példány tesztelési kulcsainak listázása
* Egyéb: az Azure Spring Cloud Service-példány tesztelési kulcsának újbóli előállítása

A **Microsoft. AppPlatform/Spring/apps** elemnél válassza a következő lehetőséget:
* Írás: Microsoft Azure Spring Cloud-alkalmazás írása
* Törlés: Microsoft Azure Spring Cloud-alkalmazás törlése
* Olvasás: Microsoft Azure Spring Cloud-alkalmazás olvasása
* Egyéb: Microsoft Azure tavaszi Felhőbeli alkalmazás erőforrás-feltöltési URL-címének beolvasása
* Egyéb: Microsoft Azure Spring Cloud Application egyéni tartományának ellenőrzése

A **Microsoft. AppPlatform/Spring/apps/kötések** területen válassza a következőket:
* Írás: Microsoft Azure Spring Cloud Application-kötés írása
* Törlés: Microsoft Azure Spring Cloud Application-kötés törlése
* Olvasás: Microsoft Azure Spring Cloud Application-kötés olvasása

A **Microsoft. AppPlatform/Spring/apps/központi telepítések** közül válassza a következőt:
* Írás: Microsoft Azure Spring Cloud Application üzembe helyezésének írása
* Törlés: az Azure Spring Cloud Application üzembe helyezésének törlése
* Olvasás: Microsoft Azure Spring Cloud Application üzembe helyezésének olvasása
* Egyéb: Microsoft Azure Spring Cloud Application üzembe helyezésének megkezdése
* Egyéb: Microsoft Azure Spring Cloud Application üzembe helyezésének leállítása
* Egyéb: Microsoft Azure Spring Cloud Application üzembe helyezésének újraindítása
* Egyéb: Microsoft Azure Spring Cloud Application telepítési naplófájl URL-címének beolvasása

A **Microsoft. AppPlatform/Spring/apps/Deployments/SKU**-ból válassza a következőt:
* Olvasás: az alkalmazás központi telepítésének listája elérhető SKU-i

A **Microsoft. AppPlatform/Locations** elemnél válassza a következőt:
* Egyéb: a név rendelkezésre állásának keresése

A Microsoft. AppPlatform/Locations/operationResults/Spring Select: READ: olvasási művelet eredménye

A **Microsoft. AppPlatform/Locations/operationStatus/operationId** lapon válassza a következőket:
* Olvasás: olvasási művelet állapota

A **Microsoft. AppPlatform/SKU**-ból válassza a következőt:
* Olvasás: elérhető SKU-lista

   [![Dev/op-engedélyek ](media/spring-cloud-permissions/dev-ops-permissions.png)](media/spring-cloud-permissions/dev-ops-permissions.png#lightbox)

3. Kattintson a **Hozzáadás** parancsra.

4. Tekintse át az engedélyeket.

5. Kattintson az **Áttekintés és létrehozás** elemre.

## <a name="define-ops---site-reliability-engineering-role"></a>Az Ops-hely megbízhatóságának mérnöki szerepkörének meghatározása
Ez az eljárás egy, az Azure Spring Cloud apps üzembe helyezésére, tesztelésére és újraindítására vonatkozó engedélyekkel rendelkező szerepkört határoz meg.

1. Ismételje meg az eljárást az előfizetés, az erőforráscsoport és a hozzáférés-vezérlés (IAM) navigálása érdekében.

2. Válassza ki az Ops-site megbízhatóság-tervezési szerepkör engedélyeit:

A **Microsoft. AppPlatform/Spring** listából válassza a következőket:
* Olvasás: Azure Spring Cloud Service-példány beszerzése
* Egyéb: az Azure Spring Cloud Service-példány tesztelési kulcsainak listázása

A **Microsoft. AppPlatform/Spring/apps** elemnél válassza a következő lehetőséget:
* Olvasás: Microsoft Azure Spring Cloud-alkalmazás olvasása

A **Microsoft. AppPlatform/alkalmazások/központi telepítések** területen válassza a következőket:
* Olvasás: Microsoft Azure Spring Cloud Application üzembe helyezésének olvasása
* Egyéb: Microsoft Azure Spring Cloud Application üzembe helyezésének megkezdése
* Egyéb: Microsoft Azure Spring Cloud Application üzembe helyezésének leállítása
* Egyéb: Microsoft Azure Spring Cloud Application üzembe helyezésének újraindítása

A **Microsoft. AppPlatform/Locations/operationResults/Spring** listából válassza a következőket:
* Olvasás: olvasási művelet eredménye

A **Microsoft. AppPlatform/Locations/operationStatus/operationId** lapon válassza a következőket:
* Olvasás: olvasási művelet állapota

   [![Ops-/sre-engedélyek ](media/spring-cloud-permissions/ops-sre-permissions.png)](media/spring-cloud-permissions/ops-sre-permissions.png#lightbox)

3. Kattintson a **Hozzáadás** parancsra.

4. Tekintse át az engedélyeket.

5. Kattintson az **Áttekintés és létrehozás** elemre.

## <a name="define-azure-pipelinesprovisioning-role"></a>Azure-folyamatok/üzembe helyezési szerepkör definiálása
Ez a Jenkins/GitHub-művelet szerepkör minden esetben létrehozható és konfigurálható az Azure Spring Cloud és az alkalmazások szolgáltatás-példánnyal. Ez a szerepkör kód kiadására vagy központi telepítésére szolgál.

1. Ismételje meg az eljárást az előfizetés, az erőforráscsoport és a hozzáférés-vezérlés (IAM) navigálása érdekében.

2. Nyissa meg az **engedélyek** beállításait.

3. Válassza ki az Azure-folyamatok/kiépítési szerepkör engedélyeit:
  
A **Microsoft. AppPlatform/Spring** listából válassza a következőket:
* Írás: Azure Spring Cloud Service-példány létrehozása vagy frissítése
* Törlés: az Azure Spring Cloud Service-példány törlése
* Olvasás: Azure Spring Cloud Service-példány beszerzése
* Egyéb: az Azure Spring Cloud Service instance teszt végpontjának engedélyezése
* Egyéb: az Azure Spring Cloud Service instance teszt végpontjának letiltása
* Egyéb: az Azure Spring Cloud Service-példány tesztelési kulcsainak listázása
* Egyéb: az Azure Spring Cloud Service-példány tesztelési kulcsának újbóli előállítása

A **Microsoft. AppPlatform/Spring/apps** elemnél válassza a következő lehetőséget:
* Írás: Microsoft Azure Spring Cloud-alkalmazás írása
* Törlés: Microsoft Azure Spring Cloud-alkalmazás törlése
* Olvasás: Microsoft Azure Spring Cloud-alkalmazás olvasása
* Egyéb: Microsoft Azure tavaszi Felhőbeli alkalmazás erőforrás-feltöltési URL-címének beolvasása
* Egyéb: Microsoft Azure Spring Cloud Application egyéni tartományának ellenőrzése

A **Microsoft. AppPlatform/Spring/apps/kötések** területen válassza a következőket:
* Írás: Microsoft Azure Spring Cloud Application-kötés írása
* Törlés: Microsoft Azure Spring Cloud Application-kötés törlése
* Olvasás: Microsoft Azure Spring Cloud Application-kötés olvasása

A **Microsoft. AppPlatform/Spring/apps/központi telepítések** közül válassza a következőt:
* Írás: Microsoft Azure Spring Cloud Application üzembe helyezésének írása
* Törlés: az Azure Spring Cloud Application üzembe helyezésének törlése
* Olvasás: Microsoft Azure Spring Cloud Application üzembe helyezésének olvasása
* Egyéb: Microsoft Azure Spring Cloud Application üzembe helyezésének megkezdése
* Egyéb: Microsoft Azure Spring Cloud Application üzembe helyezésének leállítása
* Egyéb: Microsoft Azure Spring Cloud Application üzembe helyezésének újraindítása
* Egyéb: Microsoft Azure Spring Cloud Application telepítési naplófájl URL-címének beolvasása

A **Microsoft. AppPlatform/SKU**-ból válassza a következőt:
* Olvasás: elérhető SKU-lista

A **Microsoft. AppPlatform/Locations** elemnél válassza a következőt:
* Egyéb: a név rendelkezésre állásának keresése

A **Microsoft. AppPlatform/Locations/operationResults/Spring** listából válassza a következőket:
* Olvasás: olvasási művelet eredménye

A **Microsoft. AppPlatform/Locations/operationStatus/operationId** lapon válassza a következőket:
* Olvasás: olvasási művelet állapota

A **Microsoft. AppPlatform/SKU**-ból válassza a következőt:
* Olvasás: elérhető SKU-lista

   [![Folyamatok engedélyei ](media/spring-cloud-permissions/pipelines-permissions-box.png)](media/spring-cloud-permissions/pipelines-permissions-box.png#lightbox)  

4. Kattintson a **Hozzáadás** parancsra.

5. Tekintse át az engedélyeket.

6. Kattintson az **Áttekintés és létrehozás** elemre.


## <a name="see-also"></a>Lásd még
* [Egyéni Azure-szerepkörök létrehozása vagy módosítása az Azure Portal segítségével](../role-based-access-control/custom-roles-portal.md)

Az egyéni engedélyeket definiáló három módszerről további információt a következő témakörben talál:
* [Szerepkör klónozása](../role-based-access-control/custom-roles-portal.md#clone-a-role)
* [Kezdés a nulláról](../role-based-access-control/custom-roles-portal.md#start-from-scratch)
* [Kezdés a JSON-ból](../role-based-access-control/custom-roles-portal.md#start-from-json)