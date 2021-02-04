---
title: Emberi feliratú átírási irányelvek – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: A beszédfelismerés pontosságának javítása érdekében, például ha a szavakat törlik vagy helytelenül helyettesítik, a hangadatokkal együtt használhat emberi címkével ellátott átírásokat is. Az emberi címkével ellátott átiratok egy hangfájl Word-by-Word, Verbatim átiratai.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: 85f239afd1b9263440abff1f924c12cdb7eeadaa
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2021
ms.locfileid: "99560288"
---
# <a name="how-to-create-human-labeled-transcriptions"></a>Emberi címkével ellátott átiratok létrehozása

Ha szeretné javítani az elismerés pontosságát, különösen azokat a problémákat, amelyek a szavak törlésekor vagy helytelen helyettesítése miatt következnek, az emberi címkével ellátott átírásokat érdemes használni a hangadatokkal együtt. Mik az emberi feliratú átiratok? Ez egyszerűen, egy hangfájl szó szerinti Word-átirata.

Az elismerés javításához nagy minta szükséges az átírási adatgyűjtéshez, javasoljuk, hogy 10 és 20 órányi átírást adjon meg. Ezen az oldalon áttekintjük a kiváló minőségű átírások létrehozásához kialakított irányelveket. Ez az útmutató területi beállítással van elválasztva, és az angol, a mandarin kínai és a német nyelvű szakaszt is tartalmazza.

> [!NOTE]
> Nem minden alapmodell támogatja a hangfájlok testreszabását. Ha egy alapmodell nem támogatja azt, akkor a képzés a kapcsolódó szöveggel megegyező módon fogja használni a beírások szövegét.

## <a name="us-english-en-us"></a>USA angol (en-US)

Az angol hanganyaghoz tartozó emberi feliratú átírásokat egyszerű szövegként kell megadni, csak ASCII-karakterek használatával. Kerülje a Latin-1 vagy a Unicode írásjelek használatát. Ezeket a karaktereket gyakran véletlenül felveszi a rendszer a Word-feldolgozó alkalmazásból vagy a weblapokből származó adatokkal történő másoláskor. Ha ezek a karakterek jelennek meg, győződjön meg arról, hogy a megfelelő ASCII-helyettesítéssel frissíti őket.

Íme, néhány példa:

| Kerülendő karakterek | Substitution | Jegyzetek |
| ------------------- | ------------ | ----- |
| "Helló világ" | „Hello World” | A nyitó és záró idézőjelek a megfelelő ASCII-karakterekkel lettek helyettesítve. |
| John 's Day | John 's Day | Az aposztróf a megfelelő ASCII-karakterrel lett helyettesítve. |
| jó volt – nem, nagyszerű volt! | jó volt – nem, nagyszerű volt! | Az em Dash két kötőjeltel lett helyettesítve. |

### <a name="text-normalization-for-us-english"></a>Szöveg normalizálása az USA angol nyelvén

A szöveg normalizálása a szavak átalakítása a modell betanításakor használt konzisztens formátumba. Bizonyos normalizálás szabályok automatikusan érvényesek a szövegre, de javasoljuk, hogy az emberi címkével ellátott átírási adatai előkészítéséhez használja ezeket az irányelveket:

- Szavak kiírása rövidítések.
- Nem szabványos numerikus karakterláncok (például a könyvelési kifejezések) kiírása.
- A nem alfabetikus karaktereket vagy a kevert alfanumerikus karaktereket az "ejtsd" értékre kell írni.
- A szavakként kimutatott rövidítések nem szerkeszthetők (például "radar", "Laser", "RAM" vagy "NATO").
- A kiugró rövidítések, amelyeket szóközzel elválasztva külön betűnek kell kiírni.
- Ha hangot használ, a számokat olyan szavakként írja át, amelyek megfelelnek a hanganyagnak (például "101", "1 0 1" vagy "101").
- Ne ismételje meg az ismétlődő karaktereket, szavakat vagy szavakat három alkalommal, például: "igen igen igen, igen". Előfordulhat, hogy a beszédfelismerési szolgáltatás eldobta az ilyen ismétlődésekkel rendelkező sorokat.

Íme néhány példa arra, hogy milyen normalizálás után kell elvégezni az átírást:

| Eredeti szöveg               | Szöveg a normalizálás után              |
| --------------------------- | ------------------------------------- |
| Dr. Bruce-banner            | Orvos Bruce-banner                   |
| James Bond, 007             | James Bond, dupla Oh Seven           |
| Ke $ ha                       | Kesha                                 |
| Mennyi ideig tart a 2x4         | Meddig a kettő négy           |
| Az értekezlet 1 – 15 óra | Az értekezlet egy-három PM-ből kerül |
| A vérem típusa O +         | A vérem típusa O pozitív           |
| A víz H20                | A víz H 2 O                        |
| Play OU812 by van Halen     | O U 8 1 2 lejátszási mód: Van Halen           |
| UTF-8 AJ-vel              | U T F 8 az AJ-vel                      |

A következő normalizálás szabályok automatikusan érvényesek az átírásokra:

- Használjon kisbetűs karaktereket.
- Távolítsa el az összes írásjelet, kivéve az aposztrófokat a szavakon belül.
- A számok kibontása szavak/beszéd alakba, például dollárba.

Íme néhány példa az átíráshoz automatikusan végrehajtott normalizálás esetén:

| Eredeti szöveg                          | Szöveg a normalizálás után          |
| -------------------------------------- | --------------------------------- |
| "Szent tehén!" mondta Batman.               | Szent tehén mondta Batman              |
| "Mi?" mondta Batman társa, Robin. | Mi mondta a Batman pajtás Robin |
| Go Get-em!                            | Go Get em                         |
| Dupla közös                     | Dupla közös                |
| 104 Elm utca                         | 1 0 4 Elm utca            |
| Hangolás a 102,7-re                          | hangolás a 1 0 2 pont Seven    |
| A PI körülbelül 3,14                       | a PI körülbelül három pontot 1 4  |
| Informatikai költségek \$ 3,14                        | informatikai költségek 3 14           |

## <a name="mandarin-chinese-zh-cn"></a>Mandarin kínai (zh-CN)

A mandarin kínai hang emberi címkével ellátott átírásait UTF-8 kódolással kell ellátni, bájt-Order jelölővel. Kerülje a félszélességű írásjelek használatának elkerülését. Ezek a karakterek felvehetők véletlenül, amikor felkészíti az adatfeldolgozást egy szövegszerkesztő programba, vagy a weblapokról adatokkal látja el a fájlokat. Ha ezek a karakterek jelennek meg, ügyeljen arra, hogy a megfelelő teljes szélességű helyettesítéssel frissítse őket.

Íme, néhány példa:

| Kerülendő karakterek | Substitution   | Jegyzetek |
| ------------------- | -------------- | ----- |
| "你好" | "你好" | A nyitó és záró idézőjelek a megfelelő karakterekkel lettek helyettesítve. |
| 需要什么帮助? | 需要什么帮助？| A kérdőjel a megfelelő karakterrel lett helyettesítve. |

### <a name="text-normalization-for-mandarin-chinese"></a>A mandarin kínai szövegének normalizálása

A szöveg normalizálása a szavak átalakítása a modell betanításakor használt konzisztens formátumba. Bizonyos normalizálás szabályok automatikusan érvényesek a szövegre, de javasoljuk, hogy az emberi címkével ellátott átírási adatai előkészítéséhez használja ezeket az irányelveket:

- Szavak kiírása rövidítések.
- Numerikus karakterláncok kiírása szóbeli formában.

Íme néhány példa arra, hogy milyen normalizálás után kell elvégezni az átírást:

| Eredeti szöveg | Szöveg a normalizálás után |
| ------------- | ------------------------ |
| 我今年 21 | 我今年二十一 |
| 3 号楼 504 | 三号 楼 五 零 四 |

A következő normalizálás szabályok automatikusan érvényesek az átírásokra:

- Az összes írásjel eltávolítása
- Számok kibontása a szóbeli űrlapra
- Teljes szélességű betűk átalakítása félszélességű betűkre
- Nagybetűk használata az összes angol kifejezéshez

Íme néhány példa az átíráshoz automatikusan végrehajtott normalizálás esetén:

| Eredeti szöveg | Szöveg a normalizálás után |
| ------------- | ------------------------ |
| 3,1415 | 三 点 一 四 一 五 |
| ¥3,5 | 三 元 五 角 |
| w f y z | W F Y Z |
| 1992 年 8 月 8 日 | 一 九 九 二 年 八 月 八 日 |
| 你吃饭了吗? | 你 吃饭 了 吗 |
| 下午 5:00 的航班 | 下午 五点 的 航班 |
| 我今年 21 岁 | 我 今年 二十 一 岁 |

## <a name="german-de-de-and-other-languages"></a>Német (de-DE) és egyéb nyelvek

A német hangra (és más, nem angol vagy mandarin kínai nyelvekre) származó, emberi címkével ellátott átírásokat UTF-8 kódolással kell ellátni egy byte-Order jelölővel. Minden hangfájlhoz meg kell adni egy emberi címkével ellátott átiratot.

### <a name="text-normalization-for-german"></a>Szöveg normalizálása német nyelven

A szöveg normalizálása a szavak átalakítása a modell betanításakor használt konzisztens formátumba. Bizonyos normalizálás szabályok automatikusan érvényesek a szövegre, de javasoljuk, hogy az emberi címkével ellátott átírási adatai előkészítéséhez használja ezeket az irányelveket:

- A tizedesjegyek írása "," és nem ".".
- Írási elválasztók ":" és nem "" karakterrel (például: 12:00 UHR).
- Rövidítések, például "CA" nincs lecserélve. Javasoljuk, hogy használja a teljes szóbeli űrlapot.
- A négy fő matematikai operátor (+,-, \* és/) el lesz távolítva. Javasoljuk, hogy a "Plus", a "mínusz", a "mal" és a "geteilt" írásos formában cserélje le őket.
- Az összehasonlító operátorok el lesznek távolítva (=, < és >). Azt javasoljuk, hogy cserélje le őket "Gleich", "Kleiner ALS" és "grösser ALS" értékre.
- Írjon a törtek, például a 3/4 írásos formában (például: "Drei Viertel", 3/4 helyett).
- Cserélje le a "EUR" szimbólumot az "euro" írott formában.

Íme néhány példa arra, hogy milyen normalizálás után kell elvégezni az átírást:

| Eredeti szöveg    | Felhasználói normalizálás utáni szöveg | A rendszernormalizálás utáni szöveg       |
| ---------------- | ----------------------------- | ------------------------------------- |
| Es ist 12,23 UHR | Es ist 12:23 UHR              | es ist zwölf UHR Drei und Zwanzig UHR |
| {12,45}          | {12,45}                       | zwölf komma Vier Fünf                 |
| 2 + 3-4        | 2 plusz 3 mínusz 4              | Zwei Plus Drei, mínusz Vier             |

A következő normalizálás szabályok automatikusan érvényesek az átírásokra:

- Használjon kisbetűs karaktereket az összes szöveghez.
- Távolítsa el az összes írásjelet, többek között a különböző típusú idézőjeleket ("test", "test", "test" és "test").
- Sorok elvetése bármilyen speciális karakterből ebből a készletből: ¢ ¤ ¥ ¦ § © ª ¬® ° ± ² μ × ÿ Ø ¬ ¬.
- Kibonthatja a szóbeli formát, beleértve a dollár vagy az euro összegét.
- Csak a, o és Ön esetében fogadja el a beírni kívánt értéket. Másokat a "th" kifejezés váltja fel, vagy a rendszer elveti.

Íme néhány példa az átíráshoz automatikusan végrehajtott normalizálás esetén:

| Eredeti szöveg    | Szöveg a normalizálás után |
| ---------------- | ------------------------ |
| Frankfurter Ring | Frankfurter Ring         |
| ¡ Eine Frag!     | Eine-Frag               |
| Wir, a Hegedűs       | Wir-Hegedűs                |

### <a name="text-normalization-for-japanese"></a>Szöveg normalizálása japán nyelven

Japán (ja-JP) esetében az egyes mondatok maximális hossza 90 karakter. A hosszú mondatokkal rendelkező vonalakat a rendszer elveti. Hosszabb szöveg hozzáadásához szúrjon be egy pontot a között.

## <a name="next-steps"></a>Következő lépések

- [Az adatfeldolgozás előkészítése és tesztelése](./how-to-custom-speech-test-and-train.md)
- [Az adatai ellenőrzése](how-to-custom-speech-inspect-data.md)
- [Az adatai kiértékelése](how-to-custom-speech-evaluate-data.md)
- [A modell betanítása](how-to-custom-speech-train-model.md)
- [A modell üzembe helyezése](./how-to-custom-speech-train-model.md)