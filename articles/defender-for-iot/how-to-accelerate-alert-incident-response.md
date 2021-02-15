---
title: Riasztási munkafolyamatok felgyorsítása
description: A riasztások és incidensek munkafolyamatainak javítása.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/02/2020
ms.service: azure
ms.topic: how-to
ms.openlocfilehash: 64e81e246ec62c8995d0e31629b4f21a2c1096b0
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2021
ms.locfileid: "100522546"
---
# <a name="accelerate-alert-workflows"></a>Riasztási munkafolyamatok felgyorsítása

Ez a cikk a riasztási munkafolyamatok felgyorsítását mutatja be a riasztási megjegyzések, a riasztási csoportok és az egyéni riasztási szabályok használatával az Azure Defender for IoT.  Ezek az eszközök segítenek a következőket:

- A hálózatban észlelt nagy mennyiségű riasztási esemény elemzése és kezelése.

- Meghatározott hálózati tevékenységek kimutatása és kezelése.

## <a name="accelerate-incident-workflows-by-using-alert-comments"></a>Incidens-munkafolyamatok felgyorsítása a riasztási megjegyzésekkel

A riasztási megjegyzésekkel segítheti a kommunikációt a magánszemélyek és a csapatok között a riasztási esemény vizsgálata során.

:::image type="content" source="media/how-to-work-with-alerts-sensor/suspicion-of malicious-activity-screen.png" alt-text="A rosszindulatú tevékenységeket bemutató képernyőkép.":::

A javításhoz használjon riasztási megjegyzéseket:

- **Munkafolyamat lépései**: riasztások enyhítésének lépései.

- **Munkafolyamat követése**: értesítés arról, hogy a lépések végrehajtása megtörtént.

- **Munkafolyamat-útmutató**: javaslatok, információk vagy figyelmeztetések megadása az eseményről.

:::image type="content" source="media/how-to-work-with-alerts-sensor/alert-comment-screen.png" alt-text="A riasztási megjegyzéseket bemutató képernyőkép.":::

Az elérhető lehetőségek listája megjelenik az egyes riasztásokban. A felhasználók kijelölhetnek egy vagy több üzenetet.

Riasztási megjegyzések hozzáadása:

1. Az oldalsó menüben válassza a **Rendszerbeállítások** elemet.

2. A **Rendszerbeállítás** ablakban válassza a **riasztási megjegyzések** elemet.

3. A **Megjegyzések hozzáadása** mezőben adja meg a megjegyzés szövegét. Legfeljebb 50 karaktert használhat. A vesszők nem megengedettek.

4. Válassza a **Hozzáadás** lehetőséget.

## <a name="accelerate-incident-workflows-by-using-alert-groups"></a>Incidens-munkafolyamatok felgyorsítása riasztási csoportok használatával

A riasztási csoportok lehetővé teszik a SOC-csapatok számára, hogy az SIEM-megoldásokban megtekintsék és szűrhetik a riasztásokat, majd a vállalati biztonsági szabályzatok és üzleti prioritások alapján Az új észlelésekkel kapcsolatos riasztások például egy felderítési csoportban vannak rendszerezve. Ez a csoport olyan riasztásokat tartalmaz, amelyek az új eszközök, új VLAN-ok, új felhasználói fiókok, új MAC-címek és egyebek észlelésével foglalkoznak.

A riasztási csoportok akkor lépnek életbe, amikor a következő partneri megoldások továbbítási szabályait hozza létre:

  - Syslog-kiszolgálók

  - QRadar

  - ArcSight

:::image type="content" source="media/how-to-work-with-alerts-sensor/create-forwarding-rule.png" alt-text="A továbbítási szabály létrehozásának képernyőképe.":::

A kapcsolódó riasztási csoport a partner kimeneti megoldásaiban jelenik meg. 

### <a name="requirements"></a>Követelmények

A riasztási csoport a támogatott partneri megoldásokban fog megjelenni a következő előtagokkal:

- **Cat** for QRadar, ArcSight, syslog CEF, syslog LEEF

- **Riasztási csoport** a syslog szöveges üzeneteihez

- Syslog-objektumok **alert_group**

Ezeket a mezőket a partneri megoldásban kell konfigurálni a riasztási csoport nevének megjelenítéséhez. Ha nincs riasztás társítva a riasztási csoporthoz, a partner megoldás mezőjében a **na** érték jelenik meg.

### <a name="default-alert-groups"></a>Alapértelmezett riasztási csoportok

A következő riasztási csoportok automatikusan definiálva vannak:
|  |  |  |
|--|--|--|
| Rendellenes kommunikációs viselkedés | Egyéni riasztások | Távelérés |
| Rendellenes HTTP-kommunikáció viselkedése | Felderítés | Parancsok újraindítása és leállítása |
| Hitelesítés | Belső vezérlőprogram módosítása | Vizsgálat |
| Jogosulatlan kommunikációs viselkedés | Nem engedélyezett parancsok | Érzékelő forgalma |
| Sávszélesség-rendellenességek | Internet-hozzáférés | Kártevők gyanúja |
| Puffer túlcsordulása | Művelet-meghibásodások | Rosszindulatú tevékenység gyanúja |
| Sikertelen parancsok | Működési problémák |  |
| Konfigurációs változások | Programozási |  |

A riasztási csoportok előre definiálva vannak. A riasztási csoportokkal kapcsolatos riasztásokkal és az egyéni riasztási csoportok létrehozásával kapcsolatos részletekért forduljon [Microsoft ügyfélszolgálatahoz](https://support.microsoft.com/supportforbusiness/productselection?sapId=82c8f35-1b8e-f274-ec11-c6efdd6dd099).

## <a name="customize-alert-rules"></a>Riasztási szabályok testreszabása

Az egyéni riasztási szabályok segítségével pontosabban azonosíthatja az Önt érdeklő tevékenységeket. 

Egyéni riasztási szabályokat a következő alapján adhat hozzá:

- Egy kategória, például egy protokoll, egy port vagy egy fájl.
- Forrás-és célcím
- A választott kategória alapján feltételt, például egy protokollhoz társított függvényt, egy fájlnevet, egy portszámot vagy egy átviteli számot.
- A dátum-és időhivatkozáson alapuló feltétel, például ha egy adott napon vagy a nap egy bizonyos részén történt észlelés.

Ha az érzékelő észleli a szabályban leírt tevékenységet, a rendszer elküldi a riasztást.
az egyes érzékelők által észlelhető információk. Definiáljon például egy olyan szabályt, amely arra utasítja az érzékelőt, hogy a forrás IP-címe, a cél IP-címe vagy a parancs (egy protokollon belül) alapján aktiválja a riasztást. Ha az érzékelő észleli a szabályban meghatározott forgalmat, egy riasztás vagy esemény jön létre.

A riasztási szabályok műveleteit is használhatja arra, hogy a Defender számára IoT a következőket:

- A riasztásból a PCAP-fájl elérésének engedélyezése a felhasználók számára.
- Riasztás súlyosságának kiosztása.
- Esemény létrehozása nem riasztás helyett. Az észlelt információk az esemény idővonalán fognak megjelenni.

:::image type="content" source="media/how-to-work-with-alerts-sensor/user-defined-rule.png" alt-text="A felhasználó által definiált szabályt megjelenítő képernyőkép.":::

A riasztási üzenet azt jelzi, hogy egy felhasználó által definiált szabály aktiválta a riasztást.

:::image type="content" source="media/how-to-work-with-alerts-sensor/customized-alerts-screen.png" alt-text="Képernyőkép, amely testreszabott riasztásokat jelenít meg.":::

Egyéni riasztási szabály létrehozása:

1. Válassza az **egyéni riasztások** lehetőséget az érzékelő oldalsó menüjében.
1. A szabály létrehozásához válassza a pluszjelet ( **+** ).
1. Adja meg a szabály nevét.
1. Válasszon ki egy kategóriát vagy protokollt a **Kategóriák** ablaktáblán.
1. Definiáljon egy adott forrás-és cél IP-címet vagy MAC-címet, vagy válasszon címet.
1. Adjon meg egy vagy több szabályt. Két feltétel hozható létre:
    - A kiválasztott kategóriához társított egyedi értékek alapján feltételt. Válassza a Hozzáadás lehetőséget, és adja meg az értékeket.
    - A tevékenység észlelésének időpontján alapuló feltételek. Az észlelések szakaszban válassza ki azt az időszakot és napot, amelyben az észlelésnek meg kell történnie a riasztás elküldéséhez. Dönthet úgy, hogy elküldi a riasztást, ha a tevékenység bármikor, munkaidő alatt vagy után is észlelhető. A munkaidő meghatározása lehetőséggel utasíthatja a Defendert, hogy IoT a munkaidőt a szervezet számára.
1. Szabály műveleteinek definiálása: 
    - Jelezze, hogy a szabály **riasztást** vagy **eseményt** indít el.
    - Súlyossági szint társítása a riasztáshoz.
    - Jelezze, hogy a riasztás tartalmaz-e PCAP fájlt.
1. Kattintson a **Mentés** gombra.

A szabály hozzá lesz adva a **testreszabott riasztási szabályok** listájához, ahol áttekintheti az alapszintű szabályok paramétereit, a szabály utolsó indításának időpontját és egyebeket. A szabály a listából is engedélyezhető és letiltható.

:::image type="content" source="media/how-to-work-with-alerts-sensor/customized-alerts-screen.png" alt-text="A felhasználó által hozzáadott testreszabott szabály képernyőképe.":::

## <a name="next-steps"></a>Következő lépések

[A riasztások által biztosított információk megtekintése](how-to-view-information-provided-in-alerts.md)

[A riasztási esemény kezelése](how-to-manage-the-alert-event.md)
