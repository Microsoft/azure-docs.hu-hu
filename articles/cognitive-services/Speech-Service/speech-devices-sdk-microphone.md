---
title: Beszédfelismerési eszközök SDK mikrofon tömb javaslatai
titleSuffix: Azure Cognitive Services
description: Beszédfelismerési eszközök SDK mikrofon tömb ajánlásai. Ezek a tömb geometriái ajánlottak a Microsoft audio Stackhez való használatra.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: erhopf
ms.openlocfilehash: 698a1d52af6c2472d6c025851ead1a0b6a6dff82
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "95015341"
---
# <a name="speech-devices-sdk-microphone-array-recommendations"></a>Beszédfelismerési eszközök SDK mikrofon tömb javaslatai

Ebből a cikkből megtudhatja, hogyan tervezhet meg egy mikrofon-tömböt a Speech Devices SDK-hoz.

A Speech Devices SDK a legjobban egy olyan mikrofonos tömbvel működik, amely az alábbi irányelvek szerint lett kialakítva, beleértve a mikrofon geometriáját és az összetevők kijelölését. Az integrációval és az elektromos szempontokkal kapcsolatos útmutatást is kaphat.

## <a name="microphone-geometry"></a>Mikrofon geometriája

A következő tömb-geometriák használata ajánlott a Microsoft audio Stacktel való használatra. A hangforrások helye és a környezeti zaj elutasítása nagyobb számú, meghatározott alkalmazásokkal, felhasználói forgatókönyvekkel és az eszköz űrlap-adattípussal kapcsolatos függőségekkel bővült.

| A mikrofon & geometriája | Körkörös tömb | Körkörös tömb | Lineáris tömb | Lineáris tömb |
| --- | -------------- | --- | ------------ | --- |
|     | <img src="media/speech-devices-sdk/7-mic-c.png" alt="7 mic circular array" width="150"/> | <img src="media/speech-devices-sdk/4-mic-c.png" alt="4 mic circular array" width="150"/> | <img src="media/speech-devices-sdk/4-mic-l.png" alt="4 mic linear array" width="150"/> | <img src="media/speech-devices-sdk/2-mic-l.png" alt="2 mic linear array" width="150"/> |
| \# Mikrofon | 7 | 4 | 4 | 2 |
| Geometria | 6 külső, 1 központ, sugár = 42,5 mm, egyenletesen elhelyezkedő | 3 külső, 1 központ, sugár = 42,5 mm, egyenletesen elhelyezkedő | Hossz = 120 mm, térköz = 40 mm | Térköz = 40 mm |

A mikrofon-csatornákat a fenti tömbben ábrázolt számozásnak megfelelően kell megrendelni, a 0-tól való növekedéssel. A Microsoft audio Stackhez a hanglejátszás további hivatkozása szükséges az ECHO lemondásának elvégzéséhez.

## <a name="component-selection"></a>Összetevő kiválasztása

A mikrofon-összetevőket úgy kell kiválasztani, hogy pontosan reprodukálni lehessen a zaj és a torzítás nélküli jeleket.

A mikrofonok kiválasztásakor a javasolt tulajdonságok a következők:

| Paraméter | Ajánlott |
| --------- | ----------- |
| SNR | \>= 65 dB (1 kHz jel 94 dBSPL, A-súlyozott zaj) |
| Amplitúdó egyeztetése | ± 1 dB @ 1 kHz |
| Fázis egyeztetése | ± 2 ° @ 1 kHz |
| Akusztikai túlterhelés pont (AOP) | \>= 120 dBSPL (THD = 10%) |
| Átviteli sebesség | Minimum 24 bites |
| Mintavételezési frekvencia | Minimum 16 kHz\* |
| Gyakorisági válasz | ± 3 dB, 200-8000 Hz-es úszó maszk\* |
| Megbízhatóság | Tárolási hőmérséklet-tartomány – 40 °C – 70 °C<br />Működési hőmérséklet-tartomány – 20 °C – 55 °C |

\*_A magas színvonalú kommunikációs (VoIP) alkalmazások esetében nagyobb mintavételi vagy "szélesebb" frekvenciatartományokra lehet szükség._

A megfelelő összetevők kiválasztását megfelelő elektroakusztikus-integrációval kell párosítani a felhasznált összetevők teljesítményének elkerülése érdekében. Az egyedi használati esetek további követelményeket is szükségessé tehetnek (például működési hőmérsékleti tartományok).

## <a name="microphone-array-integration"></a>Mikrofonos tömb integrációja

A mikrofon tömb teljesítménye az eszközbe való integráláskor eltér az összetevő-specifikációtól. Fontos, hogy a mikrofonok az integráció után is megfelelőek legyenek. Ezért az eszköz teljesítménye a rögzített nyereség vagy EQ után mérve a következő javaslatoknak kell megfelelnie:

| Paraméter          | Ajánlott                                        |
| ------------------ | -------------------------------------------------- |
| SNR                | \> 63 dB (1 kHz-es jel 94 dBSPL, A-súlyozott zaj) |
| Kimeneti érzékenység | -26 dBFS/PA @ 1 kHz (ajánlott)                  |
| Amplitúdó egyeztetése | ± 2 dB, 200-8000 Hz                                |
| THD\*             | ≤ 1%, 200-8000 Hz, 94 dBSPL, 5. sorrend             |
| Gyakorisági válasz | ± 6 dB, 200-8000 Hz-es úszó maszk\*\*              |

\*\*_A THD méréséhez alacsony torzítású hangszóró szükséges (például Neumann KH120)_

\*\*_A magas színvonalú kommunikációs (VoIP) alkalmazások esetében szükség lehet a "szélesebb" frekvenciatartományokra._

## <a name="speaker-integration-recommendations"></a>Beszélő integrációs javaslatok

Mivel az ECHO-lemondás szükséges a beszélőket tartalmazó beszédfelismerési eszközökhöz, további javaslatok is elérhetők a hangszórók kiválasztásához és az integrációhoz.

| Paraméter | Ajánlott |
| --------- | ----------- |
| A linearitás szempontjai | Nem lineáris feldolgozás a hangsugárzó-hivatkozás után, ellenkező esetben a hardveres visszacsatolási hivatkozási adatfolyam szükséges |
| Beszélő visszacsatolása | WASAPI, privát API-k, egyéni ALSA beépülő modul (Linux) vagy belső vezérlőprogram-csatornán keresztül biztosított |
| THD | 3rd oktáv-sávok minimális 5., 70 dBA lejátszás @ 0,8 m ≤ 6,3%, 315-500 Hz ≤ 5%, 630-5000 Hz |
| Visszhangos csatlakozás a mikrofonokhoz | \> -10 dB TCLw-T használó ITU-T G. 122 melléklet B. 4 módszer, normalizálva a MIC szintjére<br />TCLw = TCLwmeasured \+ (mért szint – a cél kimenetének érzékenysége)<br />TCLw = TCLwmeasured \+ (mért szint – (-26)) |

## <a name="integration-design-architecture"></a>Integrációs tervezési architektúra

A következő, architektúrára vonatkozó irányelvek szükségesek a mikrofonok eszközbe való integrálásakor:

| Paraméter | Ajánlás |
| --------- | -------------- |
| A MIC-portok hasonlósága | Az összes mikrofonos port azonos hosszúságú a tömbben |
| MIC-portok méretei | A port mérete Ø 0,8-1,0 mm. Port hossz/port átmérő \< 2 |
| MIC-lezárás         | A zárolásokat egységesen implementálva, egymás után kell végrehajtani. Ajánlott \> 70%-os tömörítési arány a hab tömítésekhez |
| A MIC megbízhatósága     | A hálót a por és a bejövő forgalom megelőzésére kell használni (a PCB-k és az alsó beérkező mikrofonok között, valamint a tömítés/felső fedél) |
| MIC elkülönítése       | Gumi tömítések és vibrációs leválasztás struktúrán keresztül, különösen az integrált hangszórók miatti vibrációs útvonalak elkülönítéséhez |
| Mintavételi óra      | Az eszköz hangjának szabad vibrálása és eldobása alacsony driftgel |
| Rögzítési képesség   | Az eszköznek képesnek kell lennie az egyes csatornán lévő nyers adatfolyamok egyidejű rögzítésére |
| USB                 | Az összes USB hangbeviteli eszköznek az [USB-hangeszközök Rev3 specifikációjának](https://www.usb.org/document-library/usb-audio-devices-rev-30-and-adopters-agreement) megfelelően kell beállítania a leírót. |
| Mikrofon geometriája | Az illesztőprogramoknak helyesen kell bevezetniük a [mikrofon tömb geometriájának leíróit](/windows-hardware/drivers/audio/ksproperty-audio-mic-array-geometry) |
| Felfedező     | Az eszközök nem rendelkezhetnek a nem felderíthető vagy nem ellenőrizhető hardver-, belső vezérlőprogram-vagy harmadik féltől származó szoftveres, nem lineáris hangfeldolgozási algoritmusokkal az eszközre. |
| Rögzítési formátum      | A rögzítési formátumoknak a minimális mintavételezési sebességet kell használniuk 16 kHz és ajánlott 24 bites mélység |

## <a name="electrical-architecture-considerations"></a>Az elektromos architektúra szempontjai

Adott esetben a tömbök csatlakoztathatók USB-állomáshoz (például a Microsoft audio stacket futtató SoC-hez), valamint a Speech Services vagy más alkalmazások felületéhez.

A hardveres összetevők, például a PDM-TDM átalakításnak biztosítaniuk kell, hogy a mikrofonok dinamikus tartománya és SNR megmaradjon az ismételt mintavevők között.

A nagy sebességű, 2,0-es USB-hangosztályt minden audio MCU támogatnia kell, hogy a szükséges sávszélességet akár hét csatornán, nagyobb mintavételi sebességgel és kis mélységgel lehessen biztosítani.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [További információ a Speech Devices SDK-ról](speech-devices-sdk.md)