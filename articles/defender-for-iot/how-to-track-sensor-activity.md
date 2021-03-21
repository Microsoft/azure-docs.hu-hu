---
title: Érzékelőtevékenység nyomon követése
description: Az esemény idővonala a hálózaton észlelt tevékenység ütemtervét jeleníti meg, beleértve a riasztásokat és a riasztások felügyeleti műveleteit, a hálózati eseményeket és a felhasználói műveleteket, például a felhasználói bejelentkezést és a felhasználó törlését.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/10/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: 6dbe15da85a85303742ef28c7ca609755193f25e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100523600"
---
# <a name="track-sensor-activity"></a>Érzékelőtevékenység nyomon követése

## <a name="event-timeline"></a>Esemény idővonala

Az esemény idővonala az érzékelő által észlelt tevékenység ütemtervét jeleníti meg. Például:

  - Riasztások és riasztások kezelési műveletei

  - Hálózati események

  - Felhasználói műveletek, például felhasználói bejelentkezés és felhasználói törlés

Az esemény idővonala a hálózatban történt események időrendi nézetét jeleníti meg. Az Eseménynaptár lehetővé teszi az események láncának megértését és elemzését, amelyek megelőzik és követték a támadásokat és az incidenseket, amelyek segítenek a vizsgálatban és a kriminalisztikaben.

> [!NOTE]
> A *rendszergazdák* és a *biztonsági elemzők* elvégezhetik az ebben a részben ismertetett eljárásokat.

Az Eseménynaplók megtekintése:

- Az oldalsó menüben válassza az **esemény ütemezése** lehetőséget.

   :::image type="content" source="media/how-to-track-sensor-activity/event-timeline.png" alt-text="Tekintse meg az eseményeket az esemény idővonalán.":::

Az érzékelő által észlelt események megtekintése mellett manuálisan is hozzáadhat eseményeket az idővonalhoz. Ez a folyamat akkor hasznos, ha az esemény egy külső rendszeren történt, de hatással van a hálózatra, és fontos, hogy rögzítse az eseményt, és bemutassa azt az idősor részeként.

Események manuális hozzáadása:

- Válassza az **esemény létrehozása** lehetőséget.

Az Eseménynapló adatainak CSV-fájlba való exportálásához:

- Válassza az **Export** (Exportálás) lehetőséget.

## <a name="filter-the-event-timeline"></a>Az esemény idővonalának szűrése

Az idővonal szűrésével megjelenítheti az Önt érdeklő eszközöket és eseményeket.

Az idősor szűrése:

1. Válassza a **speciális szűrők** lehetőséget.

   :::image type="content" source="media/how-to-track-sensor-activity/advance-filters.png" alt-text="Az események szűréséhez használja a speciális szűrők ablakot.":::

2. Az események szűrőinek beállítása a következőképpen történik:

   - **Belefoglalási címek**: adott eszközök eseményeinek megjelenítése.

   - **Kizárási címek**: adott eszközök eseményeinek elrejtése.

   - **Eseménytípus belefoglalása**: adott események típusának megjelenítése.

   - **Eseménytípus kizárása**: adott események típusának elrejtése.

   - **Eszközcsoport**: válasszon ki egy eszközt, ahogy az az eszköz térképen lett meghatározva. Csak az ebből a csoportból származó események jelennek meg.

3. Válassza az **összes törlése** lehetőséget az összes kijelölt szűrő törléséhez.

4. Keresés **csak riasztások**, **riasztások**, értesítések vagy **minden esemény** alapján.

5. Válassza ki a **dátum kiválasztása** lehetőséget egy adott dátum kiválasztásához. Válasszon egy napot, egy órát és egy percet. Megjelennek a kijelölt időkeretből származó események.

6.  Válasszon **felhasználói műveleteket** a felhasználói művelet eseményeinek belefoglalásához vagy kizárásához.

7.  Kattintson a nyílra (**V**) az eseményre vonatkozó további információk megtekintéséhez:

    - A riasztás részletes leírásának megjelenítéséhez válassza ki a kapcsolódó riasztásokat (ha vannak ilyenek).

    - Válassza ki az eszközt, amely megjeleníti az eszközt a térképen.

    - Ha kapcsolódó eszközök alapján szeretne szűrni, válassza az **Események szűrése kapcsolódó eszközök alapján** lehetőséget.

    - Válassza ki a **Pcap fájlt** a Pcap-fájl (ha létezik) letöltéséhez, amely a teljes hálózat egy adott időpontban történő csomagjainak rögzítését tartalmazza. 
    
      A PCAP-fájl olyan technikai információkat tartalmaz, amelyek segítségével a hálózati mérnökök meghatározhatják az esemény pontos paramétereit. A PCAP-fájlt egy olyan hálózati protokoll-elemzővel elemezheti, mint például a Wireshark, egy nyílt forráskódú alkalmazás.

## <a name="see-also"></a>Lásd még

[Riasztások megtekintése](how-to-view-alerts.md)
