---
title: Az egyéni hangvezérelt szolgáltatáshoz tartozó adatelőkészítés
titleSuffix: Azure Cognitive Services
description: Hozzon létre egy egyéni hangot a márka számára a Speech szolgáltatással. A Studio-felvételek és a hozzájuk tartozó parancsfájlok megadásával a szolgáltatás egy egyedi hangmodellt hoz létre a rögzített hangra hangolva. Ezzel a hanggal megtaníthatja termékeit, eszközeit és alkalmazásait.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: 22db0006e6abb38439a2db46584559dcd9ce3f3a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "101722364"
---
# <a name="prepare-data-to-create-a-custom-voice"></a>Az adatelőkészítés egyéni hang létrehozásához

Ha készen áll egy egyéni szöveg-beszéd hang létrehozására az alkalmazáshoz, az első lépés a hangfelvételek és a hozzájuk tartozó parancsfájlok összegyűjtése a hangmodell betanításának megkezdéséhez. A beszédfelismerési szolgáltatás ezeket az adattípusokat használja egy egyedi hanghangolás létrehozásához, amely megfelel a hangfelvételek hangjának. A hang betanítása után elindíthatja a beszédfelismerést az alkalmazásaiban.

A saját szöveg-beszéd hangmodell tanítása előtt hangfelvételekre és a kapcsolódó szöveges átírásokra lesz szüksége. Ezen az oldalon áttekintjük az adattípusokat, azok használatát, valamint az egyes eszközök kezelését.

> [!NOTE]
> Ha szeretné betanítani a neurális hangvételt, meg kell adnia egy hangtehetségi profilt a hangfelvételt tartalmazó hangfelvételi fájllal, amely a beszédfelismerési adatok használatával betanítja az egyéni hangmodellt. A rögzítési parancsfájl előkészítésekor ügyeljen rá, hogy tartalmazza az alábbi mondatot. 

> "I [állapot az első és az utolsó név] tisztában vagyok azzal, hogy a hangfelvételeket [a vállalat neve] fogja használni a hang szintetikus verziójának létrehozásához és használatához."
Ezt a mondatot fogjuk használni annak ellenőrzéséhez, hogy ugyanaz a személy végzi-e a betanítási adatgyűjtést, aki a beleegyezik. További információ a [hangalapú tehetségek ellenőrzéséről](/legal/cognitive-services/speech-service/custom-neural-voice/data-privacy-security-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext) itt olvashat.

> Az egyéni neurális hang korlátozott hozzáféréssel érhető el. Győződjön meg róla, hogy tisztában van a [felelős AI-követelményekkel](/legal/cognitive-services/speech-service/custom-neural-voice/limited-access-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext) , és [alkalmazza a hozzáférést itt](https://aka.ms/customneural). 

## <a name="data-types"></a>Adattípusok

A hangalapú betanítási adatkészlet hangfelvételeket, valamint a hozzájuk tartozó átírásokat tartalmazó szövegfájlt tartalmaz. Minden hangfájlnak tartalmaznia kell egyetlen részletet (egyetlen mondattal vagy egy párbeszédpanelrendszer egyetlen bekapcsolásával), és 15 másodpercnél rövidebbnek kell lennie.

Bizonyos esetekben előfordulhat, hogy nem áll készen a megfelelő adatkészletre, és tesztelni szeretné az egyéni hangképzést a rendelkezésre álló hangfájlokkal, illetve rövid vagy hosszú, átiratokkal vagy anélkül. Olyan eszközöket biztosítunk (bétaverzió), amelyek segítenek a hang hosszúságú kimondott szöveg való szegmentálásában és átiratok előkészítésében a [Batch-átírási API](batch-transcription.md)használatával.

Ez a táblázat felsorolja az adattípusokat, valamint azt, hogy miként lehet egyéni szöveg-beszéd hangmodelleket létrehozni.

| Adattípus | Leírás | A következő esetekben használja | További feldolgozás szükséges | 
| --------- | ----------- | ----------- | --------------------------- |
| **Egyéni hosszúságú kimondott szöveg + egyező átirat** | A hangfájlok (. wav) gyűjteménye (. zip) egyéni hosszúságú kimondott szöveg. Az egyes hangfájloknak 15 másodpercnél rövidebbnek kell lenniük, és formázott átirattal (. txt) kell párosítani. | Professzionális felvételek a megfelelő átiratokkal | Képzésre kész. |
| **Hosszú hang + átirat (bétaverzió)** | Hosszú, nem szegmentált hangfájlok (20 másodpercnél hosszabb) gyűjteménye (. txt), amely az összes szóbeli szót tartalmazza. | Vannak hangfájlok és a hozzájuk tartozó átiratok, de nem a hosszúságú kimondott szöveg-ba vannak tagolva. | Szegmentálás (a Batch-átírással).<br>A hangformátum átalakítása, ahol szükséges. | 
| **Csak hang (bétaverzió)** | Átirat nélküli hangfájlok gyűjteménye (. zip). | Csak hangfájlok érhetők el, átiratok nélkül. | Szegmentálás + átirat létrehozása (batch-átírással).<br>A hangformátum átalakítása, ahol szükséges.| 

A fájlokat típus szerint kell csoportosítani egy adatkészletbe, és zip-fájlként kell feltölteni. Az egyes adatkészletek csak egyetlen adattípust tartalmazhatnak.

> [!NOTE]
> Az előfizetések által importálandó adatkészletek maximális száma 10 zip-fájl az ingyenes előfizetés (F0) felhasználóinak és a 500 a standard előfizetés (S0) felhasználói számára.

## <a name="individual-utterances--matching-transcript"></a>Egyéni hosszúságú kimondott szöveg + egyező átirat

Az egyes hosszúságú kimondott szöveg és a megfelelő átiratot kétféleképpen is előkészítheti. Írjon egy parancsfájlt, és olvassa el a hangvételt, vagy használja a nyilvánosan elérhető hangot, és írja át a szöveget. Ha ezt teszi, szerkessze a disfluencies a hangfájlokból, például az "UM" és más kitöltő hangokat, akadozik, motyogott szavakat vagy a kiejtéseket.

A jó hangmodell létrehozásához hozzon létre egy csendes helyen, magas színvonalú mikrofonnal rendelkező felvételeket. Elengedhetetlen a konzisztens kötet, a beszéd arány, a beszélő szurok és a beszéd kifejező jellege.

> [!TIP]
> Azt javasoljuk, hogy hozzon létre egy hangfelvételt az éles környezethez, és használja a Professional Recording studiót és a hangtehetséget. További információ: [hangminták rögzítése egyéni hanghoz](record-custom-voice-samples.md).

### <a name="audio-files"></a>Hangfájlok

Minden hangfájlnak tartalmaznia kell egy teljes (egyetlen mondatot vagy egy párbeszédpanelrendszer egyetlen bekapcsolását), amely kevesebb, mint 15 másodperc. Minden fájlnak ugyanabban a kimondott nyelven kell lennie. A többnyelvű egyéni szöveg-beszéd hangok nem támogatottak, kivéve a Chinese-English bi nyelvi változatát. Minden hangfájlnak egyedi numerikus fájlnevet kell tartalmaznia, a fájlnév kiterjesztésével. wav.

A hang előkészítésekor kövesse az alábbi irányelveket.

| Tulajdonság | Érték |
| -------- | ----- |
| Fájlformátum | RIFF (. wav), egy. zip fájlba csoportosítva |
| Mintavételezési arány | Legalább 16 000 Hz |
| Minták formátuma | PCM, 16 bites |
| Fájlnév | Numerikus,. wav kiterjesztéssel. Nem engedélyezettek az ismétlődő fájlnevek. |
| Hang hossza | 15 másodpercnél rövidebb |
| Archív formátum | .zip |
| Archiválás maximális mérete | 2048 MB |

> [!NOTE]
> a 16 000 Hz-nél kisebb mintavételi sebességgel rendelkező. wav-fájlokat a rendszer elutasítja. Ha a. zip fájl különböző mintavételi sebességű. wav-fájlokat tartalmaz, a rendszer csak a 16 000 Hz-es vagy annál újabb értékkel rendelkezőket importálja. A portál jelenleg a. zip archívumot akár 200 MB-ra is importálja. Azonban több Archívum is feltölthető.

### <a name="transcripts"></a>Átiratát

Az átírási fájl egy egyszerű szövegfájl. Ezeket az irányelveket az átírások előkészítéséhez használhatja.

| Tulajdonság | Érték |
| -------- | ----- |
| Fájlformátum | Egyszerű szöveg (. txt) |
| Kódolási formátum | ANSI/ASCII, UTF-8, UTF-8-BOM, UTF-16-LE vagy UTF-16-BE. A zh-CN, az ANSI/ASCII és az UTF-8 kódolások nem támogatottak. |
| Kimondott szövegek száma soronként | **Egy** – az átírási fájl minden sorának tartalmaznia kell az egyik hangfájl nevét, amelyet a megfelelő átírás követ. A fájlnevet és az átiratot tabulátorral (\t) kell elválasztani. |
| Maximális fájlméret | 2048 MB |

Az alábbi példa bemutatja, hogyan rendezi a rendszer az átiratokat egy. txt fájlban:

```
0000000001[tab] This is the waistline, and it's falling.
0000000002[tab] We have trouble scoring.
0000000003[tab] It was Janet Maslin.
```
Fontos, hogy az átiratok a megfelelő hang 100%-os pontos átiratával legyenek. Az átiratokban előforduló hibák a képzés során a minőség elvesztését mutatják be.

## <a name="long-audio--transcript-beta"></a>Hosszú hang + átirat (bétaverzió)

Bizonyos esetekben előfordulhat, hogy nem érhető el szegmentált hang. Az egyéni hangportálon keresztül biztosítunk egy szolgáltatást (bétaverziót), amely segít a hosszú hangfájlok szegmentálásában és a transzkripciók létrehozásában. Vegye figyelembe, hogy a szolgáltatás a beszédfelismerés és a szöveges előfizetés használatának feltételeit terheli.

> [!NOTE]
> A hosszú hangú szegmentálási szolgáltatás kihasználja a beszéd és a szöveg batch-átírási funkcióját, amely csak a szabványos előfizetés (S0) felhasználóit támogatja. A szegmentálás feldolgozása során a rendszer a hangfájlokat és az átiratokat is elküldi a Custom Speech szolgáltatásnak az adatfelismerési modell pontosítása érdekében, hogy az adatok pontossága is javuljon. A folyamat során a rendszer nem őrzi meg az adatmegőrzési időszakot. A szegmentálás befejezése után a rendszer csak a szegmentált hosszúságú kimondott szöveg és a leképezési átiratokat fogja tárolni a letöltéshez és a képzéshez.

### <a name="audio-files"></a>Hangfájlok

Kövesse az alábbi irányelveket a hanganyagok szegmentálására való felkészülés során.

| Tulajdonság | Érték |
| -------- | ----- |
| Fájlformátum | A RIFF (. wav) mintavételezési sebessége legalább 16 kHz – 16 bit a PCM vagy a. mp3 esetében legalább 256 KBps, a. zip fájlba csoportosítva |
| Fájlnév | Az ASCII-és a Unicode-karakterek támogatottak. Nem engedélyezettek az ismétlődő nevek. |
| Hang hossza | 20 másodpercnél hosszabb |
| Archív formátum | .zip |
| Archiválás maximális mérete | 2048 MB |

Az összes hangfájlt zip-fájlba kell csoportosítani. A. wav fájlok és az. mp3 fájlok egyetlen hangzip-fájlba helyezhetők. Feltölthet például egy "kingstory. wav" nevű hangfájlt tartalmazó zip-fájlt, 45-másodpercet, és egy "queenstory.mp3", 200-Second-Long nevű hangot. A rendszer az összes. MP3 fájlt a feldolgozás után. wav formátumúvá alakítja át.

### <a name="transcripts"></a>Átiratát

Az átiratokat fel kell készülnie az ebben a táblázatban szereplő specifikációba. Minden hangfájlnak meg kell egyeznie egy átirattal.

| Tulajdonság | Érték |
| -------- | ----- |
| Fájlformátum | Egyszerű szöveg (. txt), egy. zip-fájlba csoportosítva |
| Fájlnév | Ugyanazt a nevet használja, mint a megfelelő hangfájl |
| Kódolási formátum | UTF-8 – csak ANYAGJEGYZÉK |
| Kimondott szövegek száma soronként | Korlátlan |
| Maximális fájlméret | 2048 MB |

Az adattípusban található összes átirat fájlnak egy zip-fájlba kell csoportosítva lennie. Például feltöltött egy "kingstory. wav" nevű hangfájlt tartalmazó zip-fájlt, a 45 másodpercet, egy másikat pedig "queenstory.mp3", 200 másodperc hosszú. Fel kell töltenie egy másik, két átiratot tartalmazó zip-fájlt, amely egy "kingstory.txt" nevű, a másik pedig a "queenstory.txt". Minden egyszerű szövegfájlon belül meg kell adnia a megfelelő hang teljes átírását.

Az adatkészlet sikeres feltöltése után segítséget nyújtunk a hangfájlnak a megadott átirat alapján történő hosszúságú kimondott szöveg. Az adatkészlet letöltésével a szegmentált hosszúságú kimondott szöveg és a megfelelő átiratokat is megtekintheti. A szegmentált hosszúságú kimondott szöveg automatikusan egyedi azonosítók lesznek hozzárendelve. Fontos, hogy az Ön által megadott átiratok 100%-ban pontosak legyenek. Az átiratokban előforduló hibák csökkenthetik a hangszegmentálási pontosságot, és továbbra is bevezethetik a minőség elvesztését a betanítási fázisban, amely később következik be.

## <a name="audio-only-beta"></a>Csak hang (bétaverzió)

Ha nem rendelkezik átírásokkal a hangfelvételekhez, az adatok feltöltéséhez használja a **csak hang** lehetőséget. A rendszer segíthet a hangfájlok szegmentálásában és átírásában. Vegye figyelembe, hogy ez a szolgáltatás a beszédfelismerés és a szöveg közötti előfizetés használatát veszi figyelembe.

A hang előkészítésekor kövesse az alábbi irányelveket.

> [!NOTE]
> A hosszú hangú szegmentálási szolgáltatás kihasználja a beszéd és a szöveg batch-átírási funkcióját, amely csak a szabványos előfizetés (S0) felhasználóit támogatja.

| Tulajdonság | Érték |
| -------- | ----- |
| Fájlformátum | A RIFF (. wav) mintavételezési sebessége legalább 16 kHz – 16 bit a PCM vagy a. mp3 esetében legalább 256 KBps, a. zip fájlba csoportosítva |
| Fájlnév | Az ASCII-és a Unicode-karakterek támogatottak. Nem engedélyezett a duplikált név. |
| Hang hossza | 20 másodpercnél hosszabb |
| Archív formátum | .zip |
| Archiválás maximális mérete | 2048 MB |

Az összes hangfájlt zip-fájlba kell csoportosítani. Miután sikeresen feltöltötte az adatkészletet, segítünk a hangfájl szegmentálásában a hosszúságú kimondott szöveg a Speech batch transzkripciós szolgáltatás alapján. A szegmentált hosszúságú kimondott szöveg automatikusan egyedi azonosítók lesznek hozzárendelve. A beszédfelismerés során a rendszer a megfelelő átiratokat hozza létre. A rendszer az összes. MP3 fájlt a feldolgozás után. wav formátumúvá alakítja át. Az adatkészlet letöltésével a szegmentált hosszúságú kimondott szöveg és a megfelelő átiratokat is megtekintheti.

## <a name="next-steps"></a>Következő lépések

- [Egyéni hang létrehozása](how-to-custom-voice-create-voice.md)
- [Útmutató: hangminták rögzítése](record-custom-voice-samples.md)