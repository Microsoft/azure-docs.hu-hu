---
title: Jutalom pontszáma – személyre szabás
description: A jutalmazási pontszám azt jelzi, hogy a felhasználó személyre szabása milyen mértékben RewardActionID. A jutalom pontszám értékét az üzleti logikája határozza meg, a felhasználói viselkedés megfigyelése alapján. A személyre szabott gépi tanulási modelljeit a jutalmak kiértékelésével teheti meg.
ms.service: cognitive-services
ms.subservice: personalizer
ms.date: 02/20/2020
ms.topic: conceptual
ms.openlocfilehash: f3249ba2089c3d9650aa46f665353ad392d0e773
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94365567"
---
# <a name="reward-scores-indicate-success-of-personalization"></a>A jutalom pontszámai a személyre szabás sikerességét jelzik

A jutalmazási pontszám azt jelzi, hogy a felhasználó személyre szabása milyen mértékben [RewardActionID](/rest/api/cognitiveservices/personalizer/rank/rank#response). A jutalom pontszám értékét az üzleti logikája határozza meg, a felhasználói viselkedés megfigyelése alapján.

A személyre szabott gépi tanulási modelljeit a jutalmak kiértékelésével teheti meg.

Megtudhatja, [Hogyan](how-to-settings.md#configure-rewards-for-the-feedback-loop) konfigurálhatja az alapértelmezett jutalom pontszámát a Azure Portal személyre szabott erőforrásához.

## <a name="use-reward-api-to-send-reward-score-to-personalizer"></a>Jutalmazási pontszám küldése a személyre szabáshoz a jutalmazási API használatával

A jutalmakat a [jutalmazási API](/rest/api/cognitiveservices/personalizer/events/reward)-nak kell elküldeni a személyre. A jutalom általában 0 és 1 közötti szám. Bizonyos helyzetekben lehetséges negatív jutalom a-1 értékkel, és csak akkor használható, ha a megerősítő tanulást (RL) tapasztalta. A személyre szabottan a modell a lehető legmagasabb összegű jutalmak eléréséhez biztosít időt.

A rendszer a felhasználó viselkedésének megtörténte után elküldje a jutalmakat, ami lehet nappal később. A személyre szabott maximális idő megvárja, amíg az esemény nem minősül jutalomnak, vagy az alapértelmezett jutalom a Azure Portal a [jutalom várakozási idejére](#reward-wait-time) van konfigurálva.

Ha az eseményre vonatkozó jutalom pontszáma nem érkezett meg a **jutalmazási várakozási időn** belül, akkor az **alapértelmezett jutalom** lesz alkalmazva. Az **[alapértelmezett jutalom](how-to-settings.md#configure-reward-settings-for-the-feedback-loop-based-on-use-case)** általában nullára van konfigurálva.


## <a name="behaviors-and-data-to-consider-for-rewards"></a>A jutalmak alapján megfontolandó viselkedések és adatkezelési szempontok

Ezeket a jeleket és viselkedéseket érdemes figyelembe venni a jutalom pontszámának kontextusában:

* A javaslatok közvetlen felhasználói bevitele, ha a beállítások bekerülnek ("Ez az X?").
* Munkamenet hossza
* A munkamenetek közötti idő.
* A felhasználó interakcióinak elemzése.
* Közvetlen kérdések és kis-és nagyméretű felmérések, ahol a robot kéri a felhasználót, hogy visszajelzést kapjon a hasznosságról és a pontosságról.
* Válasz a riasztásokra, vagy késleltetés a riasztásokra adott válaszként.

## <a name="composing-reward-scores"></a>Jutalmazási pontszámok összeállítása

A jutalom pontszámát az üzleti logikában kell kiszámítani. A pontszám a következőképpen jeleníthető meg:

* Egyszeri elküldéses szám
* Azonnal eljuttatott pontszám (például 0,8), és egy további, később eljuttatott pontszám (általában 0,2).

## <a name="default-rewards"></a>Alapértelmezett jutalmak

Ha a [jutalmazási várakozási időn](#reward-wait-time)belül nem érkezik jutalom, az időtartam a rangsorolási hívás óta, a személyre szabott, implicit módon alkalmazza az **alapértelmezett jutalmat** erre a rangsorolási eseményre.

## <a name="building-up-rewards-with-multiple-factors"></a>Előnyök kiépítése több tényezővel

A hatékony személyre szabás érdekében több tényező alapján is felépítheti a jutalmazási pontszámot.

Ezeket a szabályokat például alkalmazhatja a videó tartalmának személyre szabásához:

|Felhasználói viselkedés|Részleges pontszám értéke|
|--|--|
|A felhasználó a legfelső elemre kattintott.|+ 0,5 jutalom|
|A felhasználó megnyitotta az adott elemmel kapcsolatos tényleges tartalmat.|+ 0,3 jutalom|
|A felhasználó 5 percet figyelt a tartalomból, vagy 30%-ot, attól függően, hogy melyik a hosszú.|+ 0,2 jutalom|
|||

Ezután elküldheti a teljes jutalmat az API-nak.

## <a name="calling-the-reward-api-multiple-times"></a>A jutalmazási API többszöri meghívása

A jutalmazási API-t ugyanazzal az AZONOSÍTÓJÚ eseménnyel is meghívhatja, különböző jutalmazási pontszámokat küldhet. Ha személyre szabja ezeket a jutalmakat, az adott eseményhez tartozó végső jutalmat a személyre szabott konfigurációban megadott módon összesítve határozza meg.

Összesítési értékek:

*  **Először is**: fogadja el az esemény első jutalmazási pontszámát, és elveti a többiet.
* **Sum**: a Napszállta összegyűjtött összes jutalmat begyűjti, és együtt adja hozzá őket.

A **jutalmazási várakozási idő** után kapott összes nyereményt elvetettük, és nincs hatással a modellek betanítására.

A jutalmazási pontszámok hozzáadásával a végső jutalom a várt pontszám-tartományon kívül esik. Ez a szolgáltatás nem fog működni.

## <a name="best-practices-for-calculating-reward-score"></a>Ajánlott eljárások a jutalmazási pontszám kiszámításához

* **Tekintse át a sikeres személyre szabáshoz szükséges valós mutatókat**: a kattintások szempontjából könnyen átgondolható, de a jó jutalom az, hogy mit szeretne elérni a felhasználók számára, ahelyett, *hogy el* szeretné *érni* a felhasználókat.  Előfordulhat például, hogy a kattintásokra való jutalom a clickbait-tartalom kiválasztásához vezethet.

* **Jutalom pontszáma a személyre szabás kihasználása érdekében**: egy film javaslatának személyre szabása remélhetőleg azt eredményezi, hogy a felhasználó megnézi a filmet, és magas minősítést ad neki. Mivel a film minősítése valószínűleg sok dologtól függ (az eljáró minőségtől, a felhasználó hangulatának minőségétől), ez nem jó jutalom a *személyre szabáshoz* . A film első néhány percét megtekintő felhasználó a személyre szabás hatékonyságának jobb jele lehet, ha pedig az 5 perc után 1 jutalmat küld, a jobb jel lesz.

* A **jutalmak csak a RewardActionID vonatkoznak**: a személyre szabott jutalom a RewardActionID-ben megadott művelet hatékonyságát értelmezi. Ha úgy dönt, hogy más műveleteket is megjelenít, és a felhasználó rákattint rájuk, a jutalomnak nulla értékűnek kell lennie.

* A nem **szándékolt következmények megfontolása**: hozzon létre jutalmazási funkciókat, amelyek az [etikai és a felelősségteljes használat](ethics-responsible-use.md)érdekében vezetnek a felelős eredményekhez.

* **Növekményes jutalmak használata**: a kisebb felhasználói viselkedésmódokkal kapcsolatos részleges jutalmak hozzáadásával a személyre szabott előnyökkel lehet elérni a jobb jutalmakat. Ez a növekményes jutalom lehetővé teszi az algoritmus számára, hogy megismerje, hogy minél közelebb kerüljön a felhasználóhoz a végső kívánt viselkedésben.
    * Ha a filmek listáját jeleníti meg, ha a felhasználó az elsőre mutat a további információk megtekintéséhez, akkor megtudhatja, hogy az egyes felhasználók bevonása történt. A viselkedés a 0,1 értékű jutalmazási pontszámmal számítható fel.
    * Ha a felhasználó megnyitotta a lapot, és kilépett, a jutalom pontszáma 0,2 lehet.

## <a name="reward-wait-time"></a>Jutalom várakozási ideje

A személyre szabás a rangsorolási hívások információit a modell betanítása érdekében a jutalmazási hívásokban elküldett jutalmakkal fogja összekapcsolni. Ezek különböző időpontokban is előfordulhatnak. A személyre szabás csak korlátozott ideig vár, a rangsor meghívásakor, még akkor is, ha a rangsor hívása inaktív eseményként történt, és később aktiválva lett.

Ha a **jutalmazási várakozási idő** lejár, és nincs jutalomra vonatkozó információ, a program az adott eseményre vonatkozó alapértelmezett jutalmat alkalmazza. A maximális várakozási időtartam 6 nap.

## <a name="best-practices-for-reward-wait-time"></a>Ajánlott eljárások a jutalmazási várakozási időre

A jobb eredmények érdekében kövesse ezeket az ajánlásokat.

* A megtérülési várakozási időt a lehető legrövidebb időn belül elhagyhatja a felhasználói visszajelzések elvégzéséhez.

* Ne válasszon olyan időtartamot, amely rövidebb, mint a visszajelzés kéréséhez szükséges idő. Ha például egy adott jutalom egy felhasználónál 1 perces videó megtekintése után következik be, a kísérlet hosszának legalább kétszeresnak kell lennie.

## <a name="next-steps"></a>Következő lépések

* [Megerősítő tanulás](concepts-reinforcement-learning.md)
* [A Rank API kipróbálása](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank/console)
* [A jutalmazási API kipróbálása](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward)