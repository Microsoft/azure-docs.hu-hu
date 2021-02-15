---
title: Riasztások megtekintése
description: A riasztásokat a különböző kategóriák szerint tekintheti meg, és a keresési funkciók segítségével megtalálhatja a fontos riasztásokat.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/02/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 6dc2a9683a48f20816adc8ce0ee0c1e8dc57b287
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2021
ms.locfileid: "100523668"
---
# <a name="view-alerts"></a>Riasztások megtekintése

Ez a cikk bemutatja, hogyan tekintheti meg az érzékelő által aktivált riasztásokat, és hogyan kezelheti őket a riasztási eszközökkel.

A riasztásokat különböző kategóriák alapján tekintheti meg, például az archivált vagy rögzített riasztásokat. Kereshet olyan riasztásokat is, mint például az IP-vagy MAC-címeken alapuló riasztások.  

A riasztásokat az érzékelő irányítópultján is megtekintheti.

Riasztások megtekintése:

- Válassza a **riasztások** lehetőséget az oldal menüjében. A riasztások ablak megjeleníti az érzékelő által észlelt riasztásokat.

  :::image type="content" source="media/how-to-work-with-alerts-sensor/alerts-screen.png" alt-text="A riasztások képernyő nézete.":::

## <a name="view-alerts-by-category"></a>Riasztások megtekintése kategóriánként

A riasztások a **riasztások** főnézetből különböző kategóriák szerint tekinthetők meg. Válasszon ki egy riasztást a részletek áttekintéséhez és az esemény kezeléséhez.

| Rendezés típus szerint | Description |
|--|--|
| **Fontos riasztások** | A riasztások fontosság szerint rendezve jelennek meg. |
| **Rögzített riasztások** | Riasztások, amelyeket a felhasználó a további vizsgálathoz rögzített. A rögzített riasztások archiválása nem történik meg, és a rögzített mappában 14 napig vannak tárolva. |
| **Legutóbbi riasztások** | Riasztások idő szerint rendezve. |
| **Elismert riasztások** | Azok a riasztások, amelyek el lettek fogadva és nem kezeltek, illetve amelyek el lettek némítva, és nem lettek felvéve. |
| **Archivált riasztások** | A rendszer által automatikusan archivált riasztások. Csak a rendszergazda felhasználó férhet hozzájuk. |

## <a name="search-for-alerts-of-interest"></a>Érdekes riasztások keresése

A riasztások fő nézete különböző keresési funkciókat kínál, amelyek segítenek a fontos riasztások megtalálásában.

:::image type="content" source="media/how-to-work-with-alerts-sensor/main-alerts-view.png" alt-text="Riasztások a képernyőképen.":::

### <a name="text-search"></a>Szöveges keresés

A riasztások szöveg, számok vagy karakterek alapján való kereséséhez használja az ingyenes keresési lehetőséget.

Keresés:

- Írja be a szükséges szöveget az ingyenes Keresés mezőbe, majd nyomja le az ENTER billentyűt a billentyűzeten.

A keresés törlése:

- Törölje a szöveget az ingyenes Keresés mezőbe, majd nyomja le az ENTER billentyűt a billentyűzeten.

### <a name="device-group-or-device-ip-address-search"></a>Eszközcsoport vagy eszköz IP-címének keresése

Olyan riasztások keresése, amelyek adott IP-címekre vagy-csoportokra hivatkoznak. Az erőforráscsoportok az eszköz-hozzárendelésben jönnek létre.

Speciális szűrők használata:

1. A **riasztások** fő nézetében válassza a **speciális szűrők** lehetőséget.

2. Válasszon ki egy erőforráscsoportot vagy egy eszközt.

3. Válassza a **Confirm (megerősítés**) lehetőséget.

4. A keresés törléséhez válassza az **összes törlése** lehetőséget.

### <a name="security-versus-operational-alert-search"></a>Biztonság és működési riasztás keresése

Váltás az operatív és a biztonsági riasztások megtekintése között:

- A **biztonsági** riasztások potenciális kártevő-forgalmat, hálózati rendellenességeket, szabályzat megsértését és protokollok megsértését jelentik.

- Az **operatív** riasztások hálózati rendellenességeket, szabályzatok megsértését és protokollok megsértését jelentik.

Ha egyik lehetőség sincs kiválasztva, az összes riasztás megjelenik.

:::image type="content" source="media/how-to-work-with-alerts-sensor/alerts-security.png" alt-text="Biztonság a riasztások képernyőjén.":::

## <a name="alert-page-options"></a>Riasztási oldal beállításai

A riasztási üzenetek a következő műveleteket biztosítják:

- Válassza ki :::image type="icon" source="media/how-to-work-with-alerts-sensor/acknowledge-an-alert-icon.png" border="false"::: a riasztás visszaigazolásához.

- Válassza ki :::image type="icon" source="media/how-to-work-with-alerts-sensor/unacknowledge-an-alert-icon.png" border="false"::: a riasztás visszaigazolásának visszavételét.

- Válassza ki :::image type="icon" source="media/how-to-work-with-alerts-sensor/pin-an-alert-icon.png" border="false"::: a riasztás rögzítését.

- Válassza ki :::image type="icon" source="media/how-to-work-with-alerts-sensor/unpin-an-alert-icon.png" border="false"::: a riasztás feloldásához.

- Válassza ki :::image type="icon" source="media/how-to-work-with-alerts-sensor/acknowledge-all-alerts-icon.png" border="false"::: az összes riasztás visszaigazolásához.

- Válassza ki :::image type="icon" source="media/how-to-work-with-alerts-sensor/learn-and-acknowledge-all-alerts.png" border="false"::: az összes riasztás megismeréséhez és elfogadásához.

- Válassza ki :::image type="icon" source="media/how-to-work-with-alerts-sensor/export-to-csv.png" border="false"::: a riasztási adatok. csv fájlba való exportálását. A **kiterjesztett riasztások exportálási** beállításával a riasztási információkat külön sorokban exportálhatja a több eszközt magában foglaló riasztásokhoz.

## <a name="alert-pop-up-window-options"></a>Riasztás előugró ablakának beállításai

- Válassza a :::image type="icon" source="media/how-to-work-with-alerts-sensor/export-to-pdf.png" border="false"::: ikont a riasztási jelentés PDF-fájlként való letöltéséhez.

- Válassza ki az :::image type="icon" source="media/how-to-work-with-alerts-sensor/download-pcap.png" border="false"::: ikont a Pcap fájl letöltéséhez. A fájl megtekinthető a Wireshark, az ingyenes hálózati protokoll-elemzővel.

- Jelölje be :::image type="icon" source="media/how-to-work-with-alerts-sensor/download-filtered-pcap.png" border="false"::: egy olyan szűrt Pcap-fájl letöltéséhez, amely csak a megfelelő riasztási csomagokat tartalmazza, így csökkentve a kimeneti fájlok méretét, és lehetővé teszi a célzottabb elemzést. A fájl a Wireshark használatával tekinthető meg.

- Válassza ki az :::image type="icon" source="media/how-to-work-with-alerts-sensor/show-alert-in-timeline.png" border="false"::: ikont, hogy megjelenjen a riasztás az esemény idővonalában.

- Válassza ki az :::image type="icon" source="media/how-to-work-with-alerts-sensor/pin-an-alert-icon.png" border="false"::: ikont a riasztás rögzítéséhez.

- Válassza ki az :::image type="icon" source="media/how-to-work-with-alerts-sensor/unpin-an-alert-icon.png" border="false"::: ikont a riasztás feloldásához.

- Válassza ki :::image type="icon" source="media/how-to-work-with-alerts-sensor/learn-icon.png" border="false"::: , hogy jóváhagyja a forgalmat (csak biztonsági elemzők és rendszergazdák számára).

- Válasszon ki egy eszközt, hogy megjelenjen az eszköz térképén.

## <a name="next-steps"></a>Következő lépések

[A riasztási esemény kezelése](how-to-manage-the-alert-event.md)

[Riasztási munkafolyamatok felgyorsítása](how-to-accelerate-alert-incident-response.md)
