---
title: A port- és VLAN-névfeloldás fejlesztése
description: A portok és VLAN-nevek testreszabása az érzékelőkön az eszközök feloldása érdekében.
ms.date: 12/13/2020
ms.topic: how-to
ms.openlocfilehash: de6fbe70d5a5359ad4e4c276642b9b9ed0cef00f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104784169"
---
# <a name="enhance-port-vlan-and-os-resolution"></a>A port, a VLAN és az operációs rendszer felbontásának növelése

A portok és VLAN-nevek testreszabhatók az érzékelőkön az eszközök feloldása érdekében.

## <a name="customize-port-names"></a>A portok nevének testreszabása

Az Azure Defender for IoT automatikusan hozzárendeli a neveket a legtöbb általánosan fenntartott porthoz (például DHCP vagy HTTP). A IoT által észlelt más portokhoz is testreszabhatja a portok nevét. Rendeljen hozzá például egy nevet egy nem fenntartott porthoz, mert ez a port szokatlanul magas tevékenységet mutat.

Ezek a nevek a következő esetekben jelennek meg:

  - **Az eszközbeállítások közül választhat** .

  - A portok adatait biztosító widgeteket hoz létre.

### <a name="view-custom-port-names-in-the-device-map"></a>Egyéni portok nevének megtekintése az eszköz térképen

A felhasználók által definiált nevet tartalmazó portok megjelennek az eszköz térképén, az **ismert alkalmazások** csoportban.

:::image type="content" source="media/how-to-enrich-asset-information/applications-v2.png" alt-text="Képernyőkép az eszközről, amely az ismert alkalmazások csoportot mutatja.":::

### <a name="view-custom-port-names-in-widgets"></a>Egyéni portok nevének megtekintése a widgetekben

A megadott portok nevei megjelennek a portok által a forgalomra kiterjedő widgetekben.

:::image type="content" source="media/how-to-enrich-asset-information/traffic-v2.png" alt-text="Fedezze fel a forgalmat.":::

Egyéni portok nevének meghatározása:

1. Válassza a **Rendszerbeállítások** , majd a **szabványos aliasok** lehetőséget.

2. Válassza a **port alias hozzáadása** elemet.

    :::image type="content" source="media/how-to-enrich-asset-information/edit-aliases.png" alt-text="Adja hozzá a port aliast.":::

3. Adja meg a portszámot, válassza a **TCP/UDP** lehetőséget, vagy válassza a **mindkettőt**, és adja hozzá a nevet.

4. Kattintson a **Mentés** gombra.

## <a name="configure-vlan-names"></a>VLAN-nevek konfigurálása

Az eszközök leltározási adatok az eszköz VLAN-számaival és-címkékkel bővíthetők. Az adatok dúsításán kívül megtekintheti az eszközök számát a VLAN-ban, és megtekintheti a sávszélességet a VLAN widgetek használatával.

A VLAN-ok támogatása 802.1 q-alapú (legfeljebb 4094 VLAN-AZONOSÍTÓval).

A VLAN-információk lekérésére két módszer áll rendelkezésre:

- **Automatikus észlelés**: alapértelmezés szerint az érzékelő automatikusan felderíti a VLAN-okat. A forgalmat észlelő VLAN-ok a VLAN konfigurációs képernyőjén, az adatbányászati jelentésekben és a VLAN-adatokat tartalmazó más jelentésekben jelennek meg. A nem használt VLAN-ok nem jelennek meg. Ezeket a VLAN-okat nem lehet szerkeszteni vagy törölni. 

  Mindegyik VLAN-hoz egyedi nevet kell adni. Ha nem ad meg nevet, a VLAN-szám megjelenik az összes olyan helyen, ahol a VLAN-t jelenteni kell.

- **Manuálisan hozzáadva**: a VLAN-ok manuálisan is felvehetők. Minden manuálisan hozzáadott VLAN-hoz egyedi nevet kell hozzáadnia, és szerkesztheti vagy törölheti is ezeket a VLAN-okat.

A VLAN-nevek legfeljebb 50 ASCII karakterből állhatnak.

> [!NOTE]
> A VLAN-nevek nincsenek szinkronizálva az érzékelő és a felügyeleti konzol között. A nevet a felügyeleti konzolon is meg kell határoznia.  
A Cisco kapcsolók esetében adja hozzá a következő sort a span konfigurációhoz: `monitor session 1 destination interface XX/XX encapsulation dot1q` . Ebben a parancsban az *XX/XX* a port neve és száma.

VLAN-nevek konfigurálása:

1. Az oldalsó menüben válassza a **Rendszerbeállítások** elemet.

2. A **Rendszerbeállítások** ablakban válassza a **VLAN** lehetőséget.

    :::image type="content" source="media/how-to-enrich-asset-information/edit-vlan.png" alt-text="Használja a rendszerbeállításokat a VLAN-ok szerkesztéséhez.":::

3. Adjon hozzá egy egyedi nevet az egyes VLAN-AZONOSÍTÓk mellett.

## <a name="improve-device-operating-system-classification-data-enhancement"></a>Az eszköz operációs rendszer besorolásának javítása: adatfejlesztés

Az érzékelők folyamatosan automatikusan észlelik az új eszközöket, valamint a korábban felderített eszközök változásait, például az operációs rendszer típusát.

Bizonyos körülmények között ütközések észlelhetők a felderített operációs rendszerekben. Ez például akkor fordulhat elő, ha olyan operációsrendszer-verzióval rendelkezik, amely asztali vagy kiszolgálói rendszerekre hivatkozik. Ha ez történik, értesítést fog kapni a választható operációs rendszerek besorolásával kapcsolatban.

:::image type="content" source="media/how-to-enrich-asset-information/enhance-data-screen.png" alt-text="Javítsa az adatvédelmet.":::

Vizsgálja meg az ajánlásokat az operációs rendszer besorolásának gazdagítása érdekében. Ez a besorolás megjelenik az eszközök leltárában, az adatbányászati jelentésekben és más megjelenítésekben. Az információk naprakészen hagyása javíthatja a riasztások, fenyegetések és kockázatelemzési jelentések pontosságát.

Az operációs rendszerre vonatkozó javaslatok elérése:

1. Válassza a **Rendszerbeállítások** lehetőséget.
1. Válassza **az Adatfejlesztés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

A dúsított eszköz adatainak megtekintése különböző jelentésekben:

- [Egy eszközkészlet érzékelői általi észlelések vizsgálata](how-to-investigate-sensor-detections-in-a-device-inventory.md)
- [Érzékelő trendek és statisztikai jelentések](how-to-create-trends-and-statistics-reports.md)
- [Érzékelő adatbányászati lekérdezések](how-to-create-data-mining-queries.md)
