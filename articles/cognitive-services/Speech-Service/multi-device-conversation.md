---
title: Multi-Device beszélgetés (előzetes verzió) – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: A többeszközes beszélgetés révén egyszerűen hozhat létre beszéd-vagy szöveges beszélgetést több ügyfél között, és koordinálhatja a közöttük küldött üzeneteket.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: trbye
ms.openlocfilehash: 7b38bc390b28788003c2de757f45e8ecdfd5c89a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "98932427"
---
# <a name="what-is-multi-device-conversation-preview"></a>Mi a multi-Device beszélgetés (előzetes verzió)?

A **többeszközes beszélgetés** révén egyszerűen hozhat létre beszéd-vagy szöveges beszélgetést több ügyfél között, és koordinálhatja a közöttük küldött üzeneteket.

A **több eszközre kiterjedő beszélgetésekkel** a következőket teheti:

- Több ügyfelet is összekapcsolhat ugyanahhoz a beszélgetéshez, és kezelheti a közöttük lévő üzenetek küldését és fogadását.
- Egyszerűen átmásolhatja az egyes ügyfelek hangait, és elküldheti az átírást a többinek, a választható fordítással.
- Egyszerű szöveges üzenetek küldése az ügyfelek között, opcionális fordítással.

Létrehozhat olyan funkciót vagy megoldást, amely az eszközök egy tömbén működik. Minden eszköz önállóan (hang-vagy azonnali üzenetek átírásával) küldhet üzeneteket az összes többi eszköznek.

Míg a [**társalgási átirat**](conversation-transcription.md) egyetlen eszközön működik többcsatornás mikrofonos tömbvel, a **több eszközre kiterjedő beszélgetés** több eszközzel is használható, amelyek mindegyike egyetlen mikrofonnal rendelkezik.

>[!IMPORTANT]
> A többeszközes beszélgetés **nem** támogatja az ügyfelek közötti hangfájlok küldését: csak az átírást és/vagy fordítást.

## <a name="key-features"></a>A legfontosabb jellemzők

- **Valós idejű átirat** – mindenki megkapja a beszélgetés átiratát, így a szöveg valós időben is követhető, vagy később is elmenthető.
- **Valós idejű fordítás** – a szöveges fordításhoz több mint 70 [támogatott nyelv](language-support.md#text-languages) használható, a felhasználók lefordítják a beszélgetést az előnyben részesített nyelv (ek) re.
- **Olvasható átiratok** – az átírás és a fordítás könnyen követhető, írásjelekkel és mondatokkal.
- **Hang-vagy szövegbevitel** – minden felhasználó tud beszélni vagy beírni a saját eszközén, attól függően, hogy milyen nyelvi támogatási lehetőségek vannak engedélyezve a résztvevő által választott nyelven. Kérjük, tekintse meg a [nyelvi támogatást](language-support.md#speech-to-text).
- **Üzenetküldés** – a többeszközes beszélgetési szolgáltatás az egyik ügyfél által küldött üzeneteket az összes többi felhasználó számára továbbítja, a választott nyelv (ek) szerint.
- **Üzenet azonosítója** – minden üzenet, amelyet a felhasználók a beszélgetésben kapnak, az azt küldő felhasználó becenevével lesz megjelölve.

## <a name="use-cases"></a>Használati esetek

### <a name="lightweight-conversations"></a>Könnyűsúlyú beszélgetések

A beszélgetés létrehozása és csatlakoztatása egyszerű. Az egyik felhasználó "gazdagépként" fog működni, és hozzon létre egy beszélgetést, amely véletlenszerűen öt betűből álló beszélgetési kódot és egy QR-kódot generál. Minden más felhasználó csatlakozhat a beszélgetéshez a beszélgetési kód beírásával vagy a QR-kód beolvasásával. 

Mivel a felhasználók a beszélgetési programkódon keresztül csatlakoznak, és nem kell megosztaniuk a kapcsolattartási adatokat, könnyen létrehozhatók gyors, helyszíni beszélgetések.

### <a name="inclusive-meetings"></a>Befogadó értekezletek

A valós idejű átírás és a fordítás segíthet a különböző nyelveket beszélő és/vagy siket vagy nagyothalló személyek számára is elérhetővé tenni a beszélgetéseket. Az egyes személyek aktívan részt vehetnek a beszélgetésben, ha megbeszélik az előnyben részesített nyelvet, vagy azonnali üzeneteket küldenek.

### <a name="presentations"></a>Bemutatók

Emellett feliratok is megadhatók a bemutatók és előadások számára a képernyőn és a célközönség tagjainak saját eszközein is. Miután a célközönség csatlakozott a beszélgetési kódhoz, a saját eszközén láthatják a átiratot a kívánt nyelven.

## <a name="how-it-works"></a>Működés

Az összes ügyfél a Speech SDK használatával hoz létre vagy csatlakozhat egy beszélgetéshez. A beszédfelismerési SDK együttműködik a többeszközes beszélgetési szolgáltatással, amely kezeli a beszélgetés élettartamát, beleértve a résztvevők listáját, az egyes ügyfelek választott nyelvét és az elküldött üzeneteket.  

Minden ügyfél küldhet hang-vagy azonnali üzeneteket. A szolgáltatás a beszédfelismerés segítségével szöveggé alakítja át a hanganyagot, és azonnali üzeneteket küld. Ha az ügyfelek más nyelveket is választanak, akkor a szolgáltatás minden üzenetet lefordít az egyes ügyfelek megadott nyelvére.

![Többeszközes beszélgetés áttekintő diagramja](media/scenarios/multi-device-conversation.png)

## <a name="overview-of-conversation-host-and-participant"></a>A beszélgetés, a gazdagép és a résztvevő áttekintése

A **beszélgetés** egy olyan munkamenet, amelyet az egyik felhasználó elindít a többi résztvevő felhasználótól a csatlakozáshoz. Minden ügyfél az öt betűs **beszélgetési kóddal** csatlakozik a beszélgetéshez.

Mindegyik beszélgetés olyan metaadatokat hoz létre, amelyek a következőket tartalmazzák:
-    A beszélgetés elindításának és befejezésének időbélyege
-    A beszélgetésben szereplő összes résztvevő listája, beleértve az egyes felhasználók által választott beceneveket és az elsődleges nyelvet a beszédfelismeréshez vagy szövegbevitelhez.


A beszélgetésben két típusú felhasználó található: a  **gazdagép** és a **résztvevő**.

A **gazdagép** az a felhasználó, aki elindít egy beszélgetést, és az adott beszélgetés rendszergazdájaként működik.
- Minden beszélgetés csak egy gazdagépet tartalmazhat
- A beszélgetés időtartamára a gazdagépnek csatlakoznia kell a beszélgetéshez. Ha a gazdagép elhagyja a beszélgetést, a beszélgetés minden más résztvevőnél megszűnik.
- A gazdagép néhány további vezérlővel rendelkezik a beszélgetés kezeléséhez: 
    - A beszélgetés zárolása – további résztvevők kizárásának megakadályozása
    - Összes résztvevő elnémítása – megakadályozhatja, hogy a többi résztvevő üzenetet küldjön a beszélgetésnek, legyen szó a beszédről vagy az azonnali üzenetekről
    - Egyéni résztvevők elnémítása
    - Minden résztvevő feloldása
    - Egyéni résztvevők lenémítása

A **résztvevő** olyan felhasználó, aki csatlakozik egy beszélgetéshez.
- Egy résztvevő bármikor elhagyhatja és újra csatlakozhat ugyanahhoz a beszélgetéshez anélkül, hogy véget vessen a többi résztvevő beszélgetésének.
- A résztvevők nem tudják zárolni a beszélgetést, vagy elnémítani/felnémítani másokat

> [!NOTE]
> Az egyes beszélgetések akár 100 résztvevővel is rendelkezhetnek, amelyek közül 10 egyszerre akár egy adott időpontban is megbeszélhető.

## <a name="language-support"></a>Nyelvi támogatás

Beszélgetés létrehozásakor vagy azokhoz való csatlakozáskor minden felhasználónak ki kell választania egy **elsődleges nyelvet**: az a nyelv, amelyet a rendszer szeretne, és azonnali üzeneteket küld a alkalmazásban, valamint a nyelvet, amelyen más felhasználók üzenetei is megjelennek.

Kétféle nyelv létezik: **beszéd – szöveg** és **csak szöveg**:
- Ha a felhasználó az elsődleges nyelvként **beszéd-szöveg** nyelvet választ, akkor a beszélgetésben a beszéd és a szövegbevitel is használható.

- Ha a felhasználó csak **szöveges** nyelvet választ, akkor csak szöveges bevitelt tud használni, és azonnali üzeneteket küldhet a beszélgetésbe. A szöveges fordításhoz támogatott nyelvek csak szövegként használhatók, a szöveg szövegét azonban nem. Az elérhető nyelveket a [nyelvi támogatás](./language-support.md) oldalon tekintheti meg.

Az elsődleges nyelvtől függetlenül minden résztvevő további nyelveket is megadhat a beszélgetés fordításához.

Az alábbi összefoglalás azt mutatja be, hogy a felhasználó hogyan teheti meg a több eszközből álló beszélgetéseket a választott elsődleges nyelv alapján.


| A felhasználó által a beszélgetésben elvégezhető műveletek | Diktálás | Csak szöveg |
|-----------------------------------|----------------|------|
| Beszédfelismerési bemenet használata | ✔️ | ❌ |
| Azonnali üzenetek küldése | ✔️ | ✔️ |
| A beszélgetés fordítása | ✔️ | ✔️ |

> [!NOTE]
> Az elérhető beszéd-szöveg és szöveges fordítási nyelvek listáját a [támogatott nyelvek](./language-support.md)című részben tekintheti meg.



## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Beszélgetések valós idejű fordítása](quickstarts/multi-device-conversation.md)