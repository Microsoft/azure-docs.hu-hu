---
title: Az Azure VMware-megoldás üzembe helyezésének vagy kiépítési hibájának támogatása
description: Az Azure VMware-megoldás privát felhőből származó információk beszerzése egy Azure VMware-megoldás üzembe helyezésére vagy kiépítési hibára vonatkozó szolgáltatási kérelem benyújtásához.
ms.topic: how-to
ms.date: 10/28/2020
ms.openlocfilehash: 27b645f4ca225fdd74bca6499b6581b3803e41a4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94542405"
---
# <a name="open-a-support-request-for-an-azure-vmware-solution-deployment-or-provisioning-failure"></a>Támogatási kérelem megnyitása Azure VMware-megoldás üzembe helyezéséhez vagy kiépítési hiba esetén

Ebből a cikkből megtudhatja, hogyan nyithat meg egy [támogatási kérést](https://rc.portal.azure.com/#create/Microsoft.Support) , és hogyan adhat meg alapvető információkat egy Azure VMware-megoldás üzembe helyezéséhez vagy kiépítési hibákhoz. 

Ha hiba lép fel a privát felhőben, meg kell nyitnia egy támogatási kérést a Azure Portalban. Ha támogatási kérést szeretne megnyitni, először szerezzen be néhány kulcsfontosságú információt a Azure Portalban:

- Korrelációs azonosító
- Azure ExpressRoute-áramkör azonosítója
- Hibaüzenetek

## <a name="get-the-correlation-id"></a>A korrelációs azonosító lekérése
 
Amikor saját felhőt vagy bármely erőforrást hoz létre az Azure-ban, a rendszer automatikusan létrehozza az erőforrás korrelációs AZONOSÍTÓját az erőforráshoz. Adja meg a saját felhő korrelációs AZONOSÍTÓját a támogatási kérelemben a gyorsabb Megnyitás és a kérés feloldása érdekében.

A Azure Portal két módon kérheti le az erőforrás korrelációs AZONOSÍTÓját:

* **Áttekintés** panel
* Üzembe helyezési naplók
 
 ### <a name="get-the-correlation-id-from-the-resource-overview"></a>A korrelációs azonosító beolvasása az erőforrás-áttekintésből

Íme egy példa a sikertelen privát Felhőbeli telepítés művelet részleteire, a korrelációs azonosító kiválasztásával:

:::image type="content" source="media/fix-deployment-provisioning-failures/failed-private-cloud-deployment.png" alt-text="Képernyőfelvétel: nem sikerült a saját Felhőbeli üzemelő példány kijelölése a kiválasztott korrelációs AZONOSÍTÓval.":::

A központi telepítés eredményeinek elérése egy privát Felhőbeli **Áttekintés** panelen:

1. A Azure Portal válassza ki saját felhőjét.

1. A bal oldali menüben válassza az **Áttekintés** lehetőséget.

A központi telepítés megkezdése után a központi telepítés eredményei megjelennek a privát felhő **Áttekintés** paneljén.

Másolja ki és mentse el a saját Felhőbeli központi telepítési korrelációs azonosítót, hogy az szerepeljen a szolgáltatási kérelemben.

### <a name="get-the-correlation-id-from-the-deployment-log"></a>A korrelációs azonosító beolvasása a telepítési naplóból

A sikertelen központi telepítés korrelációs AZONOSÍTÓjának lekéréséhez keresse meg a központi telepítési tevékenység naplóját a Azure Portal.

A telepítési napló elérése:

1. A Azure Portal válassza ki saját felhőjét, majd kattintson az értesítések ikonra.

   :::image type="content" source="media/fix-deployment-provisioning-failures/open-notifications.png" alt-text="Képernyőkép, amely a Azure Portal értesítések ikonját jeleníti meg.":::

1. Az **értesítések** ablaktáblán válassza **a további események lehetőséget a tevékenység naplójában**:

    :::image type="content" source="media/fix-deployment-provisioning-failures/more-events-in-activity-log.png" alt-text="Képernyőkép, amely az értesítések ablaktáblán kijelölt tevékenység napló hivatkozásában szereplő további eseményeket jeleníti meg.":::

1. A sikertelen központi telepítés és a hozzá tartozó korrelációs azonosító megkereséséhez keresse meg az erőforrás nevét vagy az erőforrás létrehozásához használt egyéb információt. 

    Az alábbi példa egy pc03 nevű privát felhőalapú erőforrás keresési eredményeit jeleníti meg.
 
    :::image type="content" source="media/fix-deployment-provisioning-failures/find-past-deployments.png" alt-text="Képernyőkép, amely egy példaként szolgáló saját Felhőbeli erőforrás keresési eredményeit jeleníti meg, valamint a PrivateCloud létrehozása vagy frissítése ablaktáblát.":::
 
1. A **tevékenység naplója** ablaktáblán a keresési eredmények területen válassza ki a sikertelen központi telepítés műveletének nevét.

1. A **PrivateCloud létrehozása vagy frissítése** panelen válassza a **JSON** fület, majd keresse meg `correlationId` a megjelenő naplót. Másolja az `correlationId` értéket a támogatási kérelembe való felvételhez. 
 
## <a name="copy-error-messages"></a>Hibaüzenetek másolása

Az üzembe helyezési probléma megoldásához vegye fel a Azure Portalban megjelenített hibaüzeneteket. Válasszon ki egy figyelmeztető üzenetet a hibák összegzésének megtekintéséhez:
 
:::image type="content" source="media/fix-deployment-provisioning-failures/summary-of-errors.png" alt-text="Képernyőfelvétel a hibák ablaktábla összefoglalás lapján, a másolás ikon kiválasztásával.":::

A hibaüzenet másolásához kattintson a másolás ikonra. Mentse a bemásolt üzenetet, hogy tartalmazza a támogatási kérelmét.
 
## <a name="get-the-expressroute-id-uri"></a>A ExpressRoute-azonosító (URI) beolvasása
 
Lehet, hogy egy meglévő privát felhőt szeretne méretezni a saját felhőalapú ExpressRoute áramkörével, és ez nem sikerül. Ebben az esetben szüksége lesz a támogatási kérelmében szerepeltetni kívánt ExpressRoute-AZONOSÍTÓra.

A ExpressRoute-azonosító másolása:

1. A Azure Portal válassza ki saját felhőjét.
1. A bal oldali menüben a **kezelés** területen válassza a **kapcsolat** lehetőséget. 
1. A jobb oldali ablaktáblán válassza a **ExpressRoute** lapot.
1. Válassza ki a ExpressRoute- **azonosító** másolási ikonját, és mentse a támogatási kérelemben használandó értéket.
 
:::image type="content" source="media/fix-deployment-provisioning-failures/expressroute-id.png" alt-text="Másolja a ExpressRoute-azonosítót a vágólapra."::: 
 
## <a name="pre-validation-failures"></a>Ellenőrzés előtti hibák

Ha a saját felhő előzetes ellenőrzése nem sikerült (az üzembe helyezés előtt), a rendszer nem generált korrelációs azonosítót. Ebben az esetben a következő információkat adhatja meg a támogatási kérelmében:

- Hiba-és hibaüzenetek. Ezek az üzenetek számos hiba esetén hasznosak lehetnek, például a kvótával kapcsolatos problémák esetén. Fontos, hogy a jelen cikkben leírtak szerint másolja ezeket az üzeneteket, és vegye fel őket a támogatási kérelembe.
- Az Azure VMware-megoldás saját felhő létrehozásához használt információ, beleértve a következőket:
  - Hely
  - Erőforráscsoport
  - Erőforrás neve

## <a name="create-your-support-request"></a>Támogatási kérelem létrehozása

A támogatási kérések létrehozásával kapcsolatos általános információkért lásd: [Azure-támogatási kérelem létrehozása](../azure-portal/supportability/how-to-create-azure-support-request.md). 

Támogatási kérelem létrehozása Azure VMware-megoldás üzembe helyezéséhez vagy kiépítési hibákhoz:

1. A Azure Portal válassza a **Súgó** ikont, majd válassza az **új támogatási kérelem** lehetőséget.

    :::image type="content" source="media/fix-deployment-provisioning-failures/open-sr-on-avs.png" alt-text="Képernyőkép a Azure Portal új támogatási kérés paneléről.":::

1. Adja meg vagy válassza ki a szükséges adatokat:

   1. Az **Alapszintű beállítások** lapon:

      1. A **probléma típusa** beállításnál válassza a **konfigurációs és beállítási problémák** lehetőséget.

      1. A **probléma altípusa** beállításnál válassza **a privát felhő kiépítése** lehetőséget.

   1. A **részletek** lapon:

      1. Adja meg vagy válassza ki a kért információkat.

      1. Illessze be a korrelációs azonosítót vagy a ExpressRoute AZONOSÍTÓját, ahová a rendszer ezt az információt kéri. Ha nem lát egy adott szövegmezőt ezekhez az értékekhez, illessze be őket a probléma szövegmezőbe a **részletek megadása** mezőbe.

    1. Illessze be a hibák részleteit, beleértve a másolt hibát vagy hibaüzeneteket a probléma szövegmezőben található **részletek megadása** című témakörben.

1. Tekintse át a bejegyzéseket, majd válassza a **Létrehozás** lehetőséget a támogatási kérelem létrehozásához.
