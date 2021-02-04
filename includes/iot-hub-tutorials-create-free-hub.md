---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 04/19/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 9151a84aa03e9b87b02278672622d458fbc5281e
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2021
ms.locfileid: "99538711"
---
IoT Hub létrehozása az Azure Portallal:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Az Azure kezdőlapján válassza az **erőforrás létrehozása** lehetőséget, majd írja be  a IoT hub **kifejezést a piactéren**.

1. Válassza a **IoT hub** elemet a keresési eredmények közül, majd válassza a **Létrehozás** lehetőséget.

1. Az **alapvető beállítások** lapon végezze el a következő mezőket:

   - **Előfizetés**: válassza ki a hubhoz használni kívánt előfizetést.

   - **Erőforráscsoport**: válasszon ki egy erőforráscsoportot, vagy hozzon létre újat. Új létrehozásához válassza az **új létrehozása** elemet, és adja meg a használni kívánt nevet. Meglévő erőforráscsoport használatához válassza ki az erőforráscsoportot. További információ: [Azure Resource Manager erőforráscsoportok kezelése](../articles/azure-resource-manager/management/manage-resource-groups-portal.md). Ez az oktatóanyag a **tutorials-iot-hub-rg** nevet használja.

   - **Régió**: válassza ki azt a régiót, amelyben el szeretné helyezni a hubot. Válassza ki az Önhöz legközelebb eső helyet. Egyes szolgáltatások, például az [IoT hub-adatfolyamok](../articles/iot-hub/iot-hub-device-streams-overview.md), csak bizonyos régiókban érhetők el. Ezen korlátozott funkciók esetében ki kell választania a támogatott régiók egyikét. Ez az oktatóanyag az **USA nyugati** régióját használja.

   - **IoT hub neve**: adja meg a hub nevét. A névnek globálisan egyedinek kell lennie. Ez az oktatóanyag **oktatóanyagokat használ – IOT-hub**. Saját hub létrehozásakor egyedi nevet kell választania.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   :::image type="content" source="media/iot-hub-tutorials-create-free-hub/hub-definition-basics.png" alt-text="Hub létrehozása a Azure Portalban":::

1. Válassza a **Tovább: hálózatkezelés** lehetőséget a központ létrehozásának folytatásához.

   Válassza ki azokat a végpontokat, amelyek kapcsolódhatnak a IoT Hubhoz. Kiválaszthatja a nyilvános végpont **(minden hálózat)** alapértelmezett beállítását, vagy választhatja a **nyilvános végpontot (a kijelölt IP-címtartományok)** vagy a **magánhálózati végpontot**. Fogadja el az oktatóanyag alapértelmezett beállítását.

   :::image type="content" source="media/iot-hub-tutorials-create-free-hub/hub-definition-networking.png" alt-text="Válassza ki azokat a végpontokat, amelyek csatlakozhatnak.":::

1. Válassza a **Tovább: kezelés** lehetőséget a központ létrehozásához.

   :::image type="content" source="media/iot-hub-tutorials-create-free-hub/hub-definition-management.png" alt-text="Egy új hub méretének és méretezésének beállítása a Azure Portal használatával.":::

   Itt elfogadhatja az alapértelmezett beállításokat. Ha szükséges, a következő mezők bármelyikét módosíthatja:

   - **Díjszabás és méretezési szintek**: a kiválasztott csomag. Válassza az ingyenes szintet. Az ingyenes szintet tesztelésre és kiértékelésre tervezték. Lehetővé teszi, hogy a 500-es eszközök naponta és legfeljebb 8 000 üzenetet csatlakoztassanak az elosztóhoz. Az egyes Azure-előfizetések az ingyenes szinten hozhatnak létre egy IoT hubot.

   - **IoT hub egységek**: a napi egységenként engedélyezett üzenetek száma a központ díjszabási szintjétől függ. Ha például azt szeretné, hogy a központ támogassa a 700 000-es bejövő üzeneteket, két S1 szintű egységet választ.
   Az egyes Azure-előfizetések az ingyenes szinten hozhatnak létre egy IoT hubot. További információ a többi lehetőségről: [a megfelelő IoT hub](../articles/iot-hub/iot-hub-scaling.md)-szinten való választás.

   - **Defender for IoT**: ezt bekapcsolva további veszélyforrások elleni védelmet adhat hozzá a IoT és az eszközeihez. Ez a lehetőség az ingyenes szinten lévő hubok esetében nem érhető el. További információ erről a szolgáltatásról: [Azure Security Center a IoT](/azure/asc-for-iot/).

   - **Speciális beállítások**  >  **Eszközről a felhőbe irányuló partíciók**: Ez a tulajdonság az üzenetek egyidejű olvasóinak számát kapcsolja az eszközről a felhőbe irányuló üzenetekhez. A legtöbb hubhoz csak négy partíció szükséges. Az ingyenes szint hub két partícióra van korlátozva.

1. Kattintson a **Next (tovább** ) gombra, hogy a következő képernyőre folytassa a címkéket.

   A címkék név/érték párok. Ugyanazt a címkét több erőforráshoz és erőforráscsoporthoz is hozzárendelheti az erőforrások kategorizálása és a számlázás konszolidálása céljából. További információ: [címkék használata az Azure-erőforrások rendszerezéséhez](../articles/azure-resource-manager/management/tag-resources.md).

   :::image type="content" source="media/iot-hub-tutorials-create-free-hub/hub-definition-tags.png" alt-text="Rendeljen hozzá címkéket a hubhoz a Azure Portal használatával.":::

1. Válassza a **Next (tovább): felülvizsgálat + létrehozás** elemet a választási lehetőségek áttekintéséhez. Ehhez hasonló képernyő jelenik meg, de a hub létrehozásakor kiválasztott értékekkel.

   :::image type="content" source="media/iot-hub-tutorials-create-free-hub/hub-definition-create.png" alt-text="Tekintse át az új központ létrehozásával kapcsolatos információkat.":::

1. Jegyezze fel az IoT Hub választott nevét. Ezt az értéket az oktatóanyag későbbi részében még használni fogja.
