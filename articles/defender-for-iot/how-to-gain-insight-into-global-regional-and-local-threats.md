---
title: A globális, regionális és helyi fenyegetések megismerése
description: Betekintést nyerhet a globális, regionális és helyi fenyegetésekbe a helyszíni felügyeleti konzolon található webhelyszerkezet használatával.
ms.date: 12/07/2020
ms.topic: how-to
ms.openlocfilehash: db3b9fbca9acd6c4ce1cfe137a4024f66d8a6292
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104784118"
---
# <a name="gain-insight-into-global-regional-and-local-threats"></a>A globális, regionális és helyi fenyegetések megismerése

A helyszíni felügyeleti konzolon található webhelyszerkezet segítségével teljes biztonsági lefedettséget érhet el, ha a hálózatot földrajzi és logikai szegmensekre osztja, amelyek az üzleti topológiát tükrözik:

- **Földrajzi egység szintje**: egy hely a térképen bemutatott földrajzi hely szerint csoportosított eszközök számát tükrözi. Alapértelmezés szerint az Azure Defender for IoT globális térképet biztosít Önnek. A térképet úgy frissítheti, hogy az megfeleljen a szervezeti vagy üzleti struktúrájának. Például olyan térképet használhat, amely egy adott országban, városon vagy ipari Egyetemen belüli helyeket tükröz. Amikor a hely színe megváltozik a térképen, a SOC csapatot a létesítmény kritikus rendszerállapotának jelzésével látja el.

  A Térkép interaktív, és lehetővé teszi az egyes helyek megnyitását és a hely adatainak megadását.

- **Globális logikai réteg**: az üzleti egység lehetővé teszik a vállalat logikai szegmensekre való felosztását adott iparágak szerint. Ha ezt teszi, az üzleti topológiája megjelenik a térképen.

  Például egy olyan globális vállalat, amely üveg-és műanyag-gyárakat tartalmaz, és a személygépkocsi-gyárak is kezelhetők három különböző üzleti egységként. A Torontoban található fizikai helyek három különböző Glass gépsor, egy műanyag gépsor és egy tehergépkocsi-motor gépsor. Tehát ez a hely mindhárom üzleti egység képviselőiből áll.

- **Földrajzi régió szintje**: hozzon létre régiókat egy globális vállalat földrajzi régiókba való felosztásához. Előfordulhat például, hogy az ismertetett vállalat a következő régiókat használja: Észak-Amerika, Nyugat-Európa és Kelet-Európa. A Észak-Amerika mindhárom üzleti egységből származó gyárakkal rendelkezik. Nyugat-Európában a személygépkocsi-gyárak és az üveg-gyárak, valamint Kelet-Európa csak műanyag gyárakkal rendelkezik.

- **Helyi logikai szegmens szintje**: a zóna egy olyan logikai szegmens, amely egy adott helyen található, például egy funkcionális területet vagy egy termelési vonalat. A zónák használata lehetővé teszi a zóna-definícióhoz kapcsolódó biztonsági házirendek betartatását. Például egy öt termelési sort tartalmazó hely osztható öt zónába.

- **Helyi nézet szintje**: egyetlen érzékelő telepítésének helyi nézete betekintést nyújt a csatlakoztatott eszközök működési és biztonsági állapotára.

## <a name="work-with-site-map-views"></a>Webhelyszerkezet-nézetek használata

A helyszíni felügyeleti konzol átfogó képet nyújt az ipari hálózatról egy kontextusban kapcsolódó térképen. Az általános Térkép nézet a szervezet globális térképét jeleníti meg az egyes helyek földrajzi helyével együtt.

:::image type="content" source="media/how-to-work-with-maps/enterprise.png" alt-text="Képernyőkép a vállalati Térkép nézetről.":::

### <a name="color-coded-map-views"></a>Színkódolt térképi nézetek

**Zöld**: a biztonsági események száma nem éri el a rendszer számára a IoT Defender által meghatározott küszöbértéket. Nincs szükség beavatkozásra.

**Sárga**: a biztonsági események száma megegyezik azzal a küszöbértékkel, amelyet a IoT Defender adott meg a rendszeren. Vegye fontolóra az események kivizsgálását.  

**Piros**: a biztonsági események száma meghaladja azt a küszöbértéket, amelyet a IoT Defender adott meg a rendszeren. Azonnali művelet elvégzése.

### <a name="risk-level-map-views"></a>Kockázati szintű Térkép nézetei

**Kockázatértékelés**: a kockázatértékelés nézet a webhely kockázataival kapcsolatos információkat jeleníti meg. A kockázati információk segítségével rangsorolhatja a mérséklést, és létrehozhat egy útitervet a biztonsági tökéletesítések megtervezéséhez.

**Incidens válasza**: az összes nem Visszaigazolatlan riasztás központi nézetének beolvasása minden egyes helyen a vállalaton belül. Egy adott helyen észlelt riasztások részletezését és kezelését is elvégezheti.

:::image type="content" source="media/how-to-work-with-maps/incident-responses.png" alt-text="Képernyőfelvétel a vállalati Térkép nézetről az incidens válaszával.":::

**Rosszindulatú tevékenység**: Ha a rendszer kártevőt észlelt, a hely piros színnel jelenik meg. Ez azt jelzi, hogy azonnali műveletet kell végrehajtania.

:::image type="content" source="media/how-to-work-with-maps/malicious-activity.png" alt-text="Képernyőkép a vállalati Térkép nézetről rosszindulatú tevékenységgel.":::

**Működési riasztások**: az ot-rendszerek ezen térképi nézete jobb képet ad arról, hogy az ot rendszer milyen működési incidenseket tapasztalhat, például a PLC leállását, a belső vezérlőprogram feltöltését és a program feltöltését.

:::image type="content" source="media/how-to-work-with-maps/operational-alerts.png" alt-text="Képernyőkép a vállalati Térkép nézetről az operatív riasztásokkal.":::

Térkép nézet kiválasztása:

1. Válassza ki a Térkép **alapértelmezett nézetét** .
2. Válasszon ki egy nézetet.

:::image type="content" source="media/how-to-work-with-maps/map-views.png" alt-text="Képernyőkép a hely térképének alapértelmezett nézetéről.":::

## <a name="update-the-site-map-image"></a>A webhelyszerkezet rendszerképének frissítése

A IoT Defender egy alapértelmezett globális térképet biztosít. Megváltoztathatja, hogy tükrözze a szervezetét: egy ország térképét vagy egy Várostérkép, például. 

A Térkép cseréje:

1. A bal oldali ablaktáblán válassza a **Rendszerbeállítások** elemet.

2. Jelölje be a **Webhelyszerkezet módosítása** lehetőséget, majd töltse fel a grafikus fájlt az alapértelmezett leképezés helyére.

## <a name="next-step"></a>Következő lépés

[Riasztások megtekintése](how-to-view-alerts.md)
