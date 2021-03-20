---
title: Azure Media Player kisegítő lehetőségek
description: További információ a Azure Media Player kisegítő lehetőségeiről.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 2231c2969bbfce1668002ad4f5f719e0b8e13de5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "81726556"
---
# <a name="accessibility"></a>Akadálymentesség #

A Azure Media Player képernyőolvasó funkciókkal, például a Windows Narrátorával és az Apple OSX/iOS-kommentárokkal működik. A felhasználói felület gombjaihoz alternatív címkék érhetők el, a képernyőolvasó pedig képes olvasni ezeket az alternatív címkéket, amikor a felhasználó navigál. Az operációs rendszer szintjén további konfigurációk is megadhatók.

## <a name="captions-and-subtitles"></a>Feliratok és feliratok ##

Jelenleg a Azure Media Player jelenleg csak az igény szerinti események feliratait támogatja, WebVTT formátumú és élő eseményekkel, a CEA 708 használatával. A TTML formátum jelenleg nem támogatott. A feliratok és opcionális feliratok segítségével a lejátszó szélesebb közönség igényeit tudja kiszolgálni, például hallássérültekét vagy olyan személyekét, akik más nyelven szeretnék a feliratokat olvasni. A feliratok segítségével maga a videó is jobban követhető, segítik a megértést, valamint hanghatásokra érzékeny környezetben (például munkahelyen) is lehetővé teszik a videó megtekintését.  

## <a name="high-contrast-mode"></a>Kontrasztos mód ##

Azure Media Player alapértelmezett felhasználói felülete megfelel a legtöbb eszköz-és böngésző kontrasztos nézetének. A konfigurációk az operációs rendszer szintjén adhatók meg.

## <a name="mobility-options"></a>Mobilitási lehetőségek ##

### <a name="tabbing-focus"></a>Lapfülek fókusza ###

Az általános HTML-szabványok által biztosított tabbing fókusz a Azure Media Playerban érhető el. A TAB fókuszának engedélyezéséhez hozzá kell adnia `tabindex=0` (vagy egy másik értéket, ha tudomásul veszi, hogyan befolyásolja a TAB rendezését a HTML-ben) a HTML-hez `<video>` hasonló módon: `<video ... tabindex=0>...</video>` . Egyes platformokon a vezérlőelemek fókusza csak akkor lehet jelen, ha a vezérlők láthatók, és ha a platform támogatja ezeket a képességeket.

Ha engedélyezve van a lapfülek fókusza, a végfelhasználók az egértől függően hatékonyan kezelhetik és vezérelhetik a videó lejátszót. A helyi menü vagy az átirányítható elem a TAB billentyű lenyomásával és az ENTER vagy a SZÓKÖZ billentyűvel választható ki. Ha megnyomja az ENTER vagy a szóköz billentyűt a helyi menüben, a rendszer kibontja azt, hogy a végfelhasználó folytassa a bejárást a menüelem kiválasztásához. Ha elérte a kívánt elemet, az Enter vagy a Szóköz billentyűvel választhatja ki.

### <a name="hotkeys"></a>Billentyűparancsok ###

Azure Media Player támogatja a billentyűzeten keresztüli gyors vezérlést. Egy webböngészőben a mögöttes video elem vezérlésének egyetlen módja, ha a lejátszóra koncentrál. Ha már van Fókuszban a lejátszó, a gyorsbillentyű vezérelheti a lejátszó funkcióit.  Az alábbi táblázat a különböző billentyűparancsokat és a hozzájuk rendelt műveleteket tartalmazza:

| Billentyűparancs              | Működés                                                                |
|----------------------|-------------------------------------------------------------------------|
| F/f                  | Megnyitja vagy bezárja a lejátszó teljes képernyős nézetét.                                  |
| M/m                  | Elnémítja a hangerőt, vagy feloldja a némítást.                                          |
| Felfelé és lefelé nyíl    | Növeli és csökkenti a lejátszó hangerejét.                                    |
| Balra és jobbra nyíl | Vissza és előre ugrik a videóban.                                  |
| 0,1,2,3,4,5,6,7,8,9  | A videó előrehaladása a \- lenyomott billentyűtől függően 0% 90%-ra változik |
| Kattintson a művelet elemre.         | Lejátssza vagy szünetelteti a videót.                                                   |

## <a name="next-steps"></a>Következő lépések

<!---Some context for the following links goes here--->
- [Azure Media Player rövid útmutató](azure-media-player-quickstart.md)