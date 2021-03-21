---
title: Mi az a szótár? -Egyéni fordító
titleSuffix: Azure Cognitive Services
description: A szótár egy igazított dokumentum, amely megadja a mondatok vagy mondatok (és azok fordításai) listáját, amelyeket mindig a Microsoft Translatornek kell lefordítani. A szótárakat más néven szószedeteknek vagy terminusoknak is nevezik.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 160aa372cec2d018d5a3558f99e079624d965098
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98895560"
---
# <a name="what-is-a-dictionary"></a>Mi az a szótár?

A szótár olyan igazított pár dokumentum, amely a mondatok vagy mondatok listáját és a hozzájuk tartozó fordításokat határozza meg. Használjon szótárt a képzésben, ha azt szeretné, hogy a Microsoft Translator mindig lefordítsa a forrás kifejezés vagy mondat bármely példányát a szótárban megadott fordítás használatával. A szótárakat más néven szószedeteknek vagy kifejezéseknek nevezzük. A szótárt a lista összes feltételének "másolás és csere" kifejezésével lehet meggondolni. Emellett a Microsoft Custom Translator Service saját általános célú szótárakat is létrehoz és használ a fordítás minőségének javítása érdekében. Az ügyfél által megadott szótár azonban precedenst kap, és először a szavak vagy mondatok keresésére keres rá.

A szótárak csak olyan nyelvi párokban működő projektekhez működnek, amelyek teljes mértékben támogatottak a Microsoft általános neurális hálózati modellje mögött. [Tekintse meg a nyelvek teljes listáját](../language-support.md#customization).

## <a name="phrase-dictionary"></a>Szótár kifejezése
A kifejezés szótár megkülönbözteti a kis-és nagybetűket. Pontos keresés és csere művelet. Ha egy kifejezés szótárt is tartalmaz a modell tanításához, a felsorolt szavak vagy kifejezések a megadott módon vannak lefordítva. A mondat többi része a szokásos módon lesz lefordítva. A kifejezéseket tartalmazó szótár segítségével megadhatja azokat a kifejezéseket, amelyek nem fordíthatók le a szótárban található forrás-és célfájl azonos fordított kifejezésének megadásával.

## <a name="sentence-dictionary"></a>Mondat szótár
A mondat szótár kis-és nagybetűk megkülönböztetése. A mondat szótár lehetővé teszi, hogy pontos cél fordítást határozzon meg a forrás mondathoz. A mondatok szótárának egyeztetéséhez a teljes elküldött mondatnak meg kell egyeznie a forrás szótár bejegyzésével. Ha a forrás szótár bejegyzése írásjelekkel végződik, a rendszer figyelmen kívül hagyja az egyeztetés során. Ha a mondatnak csak egy része egyezik, a bejegyzés nem egyezik.  Ha a rendszer egyezést észlel, a rendszer visszaadja a mondathoz tartozó szótár cél bejegyzését.

## <a name="dictionary-only-trainings"></a>Csak szótári képzések
A modelleket csak a szótárak adatai alapján lehet betanítani. Ehhez válassza ki a használni kívánt szótári dokumentumot (vagy több szótárt tartalmazó dokumentumot), majd koppintson a modell létrehozása lehetőségre. Mivel ez egy csak szótárban bekövetkező képzés, nem szükségesek minimális számú tanítási mondat. A modell általában sokkal gyorsabb képzést tesz lehetővé, mint a szokásos képzések.  Az eredményül kapott modellek a Microsoft alapmodelleket használják a fordításhoz a hozzáadott szótárak hozzáadásával.  Nem fog teszt jelentést kapni.

>[!Note]
>Az egyéni fordító nem mondattal igazítja a szótárakat, ezért fontos, hogy a szótár dokumentumaiban azonos számú forrás-és cél-kifejezés/mondat legyen, és hogy pontosan legyenek igazítva.

## <a name="recommendations"></a>Javaslatok

- A szótárak nem helyettesíthetik a modell betanítását a betanítási adatai használatával. Javasoljuk, hogy elkerülje őket, és hagyja, hogy a rendszer tanuljanak a betanítási adatokból. Ha azonban a mondatokat vagy az összetett főcímeket a-ként kell megjeleníteni, használjon szótárt.
- A kifejezés szótárát takarékosan kell használni. Ezért ügyeljen arra, hogy ha egy mondaton belüli kifejezést cserél le, a mondaton belüli környezet elveszett vagy korlátozott a mondat hátralévő részének lefordítása érdekében. Ennek eredményeképpen a mondatban szereplő kifejezés vagy szó a megadott szótár szerint lesz lefordítva, a mondat általános fordítási minősége gyakran csökken.
- A kifejezés szótára jól működik az olyan összetett nevek esetében, mint például a Terméknév ("Microsoft SQL Server"), a megfelelő nevek ("Hamburg városa") vagy a termék funkciói ("pivot Table"). Nem működik együtt a műveletekhez és a melléknevekhez, mert ezek általában erősen ragozott a forrásban vagy a célként megadott nyelven. Az ajánlott eljárás az, hogy elkerülje a szótárbeli bejegyzéseket bármilyen, de összetett kifejezésre.
- Kifejezés szótárának használatakor fontos a nagybetűk és a központozás. A szótár bejegyzései csak a beviteli mondatban szereplő szavakat és kifejezéseket egyeznek meg, amelyek pontosan ugyanazt a kihasználat és írásjelet használják, mint a forrás-szótár fájljában. Emellett a fordítások tükrözik a célként megadott szótárban található nagybetűket és a központozást is. Ha például egy angol nyelvről spanyol rendszerre tanít ki egy kifejezést, amely a forrásfájl "US" kifejezését, "EE"-t használ. UU. " a célfájl. Ha olyan mondat fordítását kéri, amely tartalmazza a "US" szót (nem tőkésített), akkor ez nem felel meg a szótárnak. Ha azonban egy olyan mondat fordítását kéri, amely tartalmazza a "US" szót (tőkésített), akkor az megfelel a szótárnak, és a fordítás "EE"-t tartalmaz. UU. " Vegye figyelembe, hogy a fordításban a nagybetűk és a központozás eltérő lehet, mint a szótárbeli célfájl esetében, és a forrásban lévő tőkésítés és írásjelek eltérőek lehetnek. A cél nyelvének szabályait követi.
- A mondatok szótárának használatakor a rendszer figyelmen kívül hagyja a mondat végét. Ha például a forrás-szótár "Ez a mondat a központozás vége!" kifejezést tartalmazza, akkor az "Ez a mondat írásjelekkel végződik" kifejezéseket tartalmazó fordítási kérések egyeznek.
- Ha egy szó többször is megjelenik egy szótárban, a rendszer mindig a megadott utolsó bejegyzést fogja használni. Ezért a szótár nem tartalmazhat ugyanazon szó több fordítását.

## <a name="next-steps"></a>Következő lépések

- Olvassa el [a dokumentumok formátumait ismertető útmutatót](document-formats-naming-convention.md).