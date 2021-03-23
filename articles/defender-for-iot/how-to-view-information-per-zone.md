---
title: További információ az egyes zónákhoz tartozó eszközökről
description: A helyszíni felügyeleti konzol segítségével átfogó információkhoz juthat egy adott zónához
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 03/21/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 685d7b1df4389c356ee7b531d179919025d298d0
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104776226"
---
# <a name="view-information-per-zone"></a>Információ megjelenítése zónában


## <a name="view-a-device-map-for-a-zone"></a>Zóna eszköz-hozzárendelésének megtekintése

Megtekintheti az érzékelő kiválasztott zónájának eszköz-hozzárendelését. Ez a nézet a kiválasztott zónához kapcsolódó összes hálózati elemet megjeleníti, beleértve az érzékelőket, a hozzájuk kapcsolódó eszközöket és egyéb információkat.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/zone-map-screenshot.png" alt-text="Képernyőkép a zóna térképéről.":::


- A **hely kezelése** ablakban válassza a zóna **megjelenítése** lehetőséget a zóna nevét tartalmazó sávon.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/default-region-to-default-business-unit-v2.png" alt-text="Alapértelmezés szerinti régió alapértelmezett üzleti egységként.":::

Ekkor megjelenik az **eszközök leképezése** ablak. Az eszközök és az eszközök térképről való megtekintéséhez a következő eszközök érhetők el. Ezekről a funkciókról a *Defender for IoT platform felhasználói útmutatója* című témakörben olvashat bővebben.

- **Térkép nagyítási nézetei**: egyszerűsített nézet, kapcsolatok nézet és részletes nézet. A megjelenített Térkép nézet a Térkép nagyítási szintjétől függ. A leképezési nézetek közötti váltáshoz módosítsa a nagyítási szinteket.

  :::image type="icon" source="media/how-to-work-with-asset-inventory-information/zoom-icon.png" border="false":::

- A **Térkép keresési és elrendezési eszközei**: különböző hálózati szegmensek, eszközök, eszközcsoport vagy rétegek megjelenítésére szolgáló eszközök.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/search-and-layout-tools.png" alt-text="Képernyőkép a keresési és elrendezési eszközök nézetről.":::

- **Az eszközökön található címkék és mutatók:** Például egy IT-hálózatban lévő alhálózatba csoportosított eszközök száma. Ebben a példában ez 8.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/labels-and-indicators.png" alt-text="A címkék és a kijelzők képernyőképe.":::

- Az **eszköz tulajdonságainak megtekintése**: például az eszköz és az alapszintű eszköz tulajdonságait figyelő érzékelő. Kattintson a jobb gombbal az eszközre az eszköz tulajdonságainak megtekintéséhez.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-properties-v2.png" alt-text="Képernyőkép az eszköz tulajdonságainak nézetéről.":::

- **Eszközhöz társított riasztás:** A kapcsolódó riasztások megtekintéséhez kattintson a jobb gombbal az eszközre.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/show-alerts.png" alt-text="Képernyőkép a riasztások megjelenítése nézetről.":::

## <a name="view-alerts-associated-with-a-zone"></a>Zónához társított riasztások megtekintése

Adott zónához tartozó riasztások megtekintése:

- Válassza a riasztás ikont a **zóna** ablakban. 

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/business-unit-view-v2.png" alt-text="Az alapértelmezett üzleti egység nézet példákkal.":::

További információt az [Áttekintés: riasztások használata](how-to-work-with-alerts-on-premises-management-console.md)című témakörben talál.

### <a name="view-the-device-inventory-of-a-zone"></a>Zóna eszköz leltározásának megtekintése

Egy adott zónához társított eszközök leltárának megtekintése:

- Válassza az **eszköz leltározásának megtekintése** lehetőséget a **zóna** ablakban.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/default-business-unit.png" alt-text="Ekkor megjelenik az eszközök leltári képernyője.":::

További információ: az [összes vállalati érzékelő észlelésének vizsgálata egy eszköz leltárában](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md).

## <a name="view-additional-zone-information"></a>További zónaadatok megtekintése

A következő további zónák információi érhetők el:

- **Zóna részletei**: a zónához tartozó eszközök, riasztások és érzékelők számának megtekintése.

- **Érzékelő részletei**: megtekintheti a zónához rendelt egyes érzékelők nevét, IP-címét és verziószámát.

- **Kapcsolat állapota**: Ha egy érzékelő le van választva, akkor kapcsolódjon az érzékelőhöz. Lásd: [érzékelők összekapcsolása a helyszíni felügyeleti konzolon](how-to-activate-and-set-up-your-on-premises-management-console.md#connect-sensors-to-the-on-premises-management-console). 

- **Frissítés** állapota: Ha a csatlakoztatott érzékelő frissítése folyamatban van, a frissítési állapotok jelennek meg. A frissítés során a helyszíni felügyeleti konzol nem fogadja az eszköz adatait az érzékelőtől.

## <a name="next-steps"></a>Következő lépések

[A globális, regionális és helyi fenyegetések megismerése](how-to-gain-insight-into-global-regional-and-local-threats.md)
