---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/18/2019
ms.author: alkohli
ms.openlocfilehash: 05673885336dbfb9a70843bd0ccc4e700091ad7e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "67179788"
---
|     Hibakód     |      Hibaleírás     |
|--------------------|--------------------------|
|    100             | A tároló vagy a megosztás neve csak 3 és 63 karakter közötti hosszúságú lehet.|
|    101             | A tároló vagy a megosztás neve csak betűket, számokat és kötőjeleket tartalmazhat.|
|    102             | A tároló vagy a megosztás neve csak betűket, számokat és kötőjeleket tartalmazhat.|
|    103             | A blob vagy a fájl neve nem támogatott vezérlőkaraktereket tartalmaz.|
|    104             | A blob vagy a fájl neve nem támogatott karaktereket tartalmaz.|
|    105             | A blob- vagy fájlnév túl sok szegmenst tartalmaz (a szegmenseket perjel -/ választja el egymástól).|
|    106             | A blob- vagy fájlnév túl hosszú.|
|    107             | A blob- vagy fájlnevében szereplő egyik szegmens túl hosszú. |
|    108             | A fájl mérete meghaladja a maximális méretet a feltöltéshez.    |
|    109             | A blob vagy a fájl nincs megfelelően igazítva.  |
|    110             | A Unicode kódolású fájlnév vagy blob nem érvényes.|
|    111             | A fájl vagy a blob neve vagy előtagja nem támogatott fenntartott név (például COM1).|
|    2000            | Az etag eltérése azt jelzi, hogy a felhőben és az eszközön lévő blokkblob ütközik. Az ütközés elhárításához törölje az egyik fájlt – vagy a felhőben vagy az eszközön lévő változatot.    |
|    2001            | Váratlan probléma történt a fájl feltöltését követően a fájl feldolgozása során.    Ha ezt a hibaüzenetet látja, és a hiba több mint 24 órán keresztül továbbra is fennáll, forduljon az ügyfélszolgálathoz. |
|    2002            | A fájl már meg nyitva egy másik folyamatban, és a leíró bezárásáig nem lehet feltölteni.|
|    2003            | Nem sikerült megnyitni a fájlt a feltöltéshez. Ha ezt a hibát látja, forduljon a Microsoft ügyfélszolgálatához.|
|    2004            | Nem sikerült csatlakozni a tárolóhoz az adatok tárolóba való feltöltéséhez.|
|    2005            | Nem sikerült csatlakozni a tárolóhoz, mert a fiók engedélyei helytelenek vagy elavultak. Ellenőrizze a hozzáférési engedélyeit.|
|    2006            | Nem sikerült feltölteni az adatokat a fiókba, mert a fiók vagy a megosztás le van tiltva.|
|    2007            | Nem sikerült csatlakozni a tárolóhoz, mert a fiók engedélyei helytelenek vagy elavultak. Ellenőrizze a hozzáférési engedélyeit.|
|    2008            | Nem sikerült hozzáadni az új adatokat, mert a tároló megtelt. Az Azure-specifikációkban tekintse meg, hogy típusonként mely tárolóméretek támogatottak. Az Azure File például csak az 5 TB-os maximális fájlméret támogatja.|
|    2009            | Az adatok nem tölthetők fel, mert a megosztáshoz társított tároló nem létezik.|    
|    2997            | Váratlan hiba történt. Ez egy átmeneti hiba, amely magától megoldódik.|
|    2998            | Váratlan hiba történt. Lehet, hogy a hiba magától megoldódik, de ha több mint 24 órán keresztül nem szűnik meg, forduljon a Microsoft ügyfélszolgálatához.|
|    16000           | A fájlt nem sikerült leállítani.|
|    16001           | Nem sikerült leállítani a fájlt, mert már létezik a helyi számítógépen.|
|    16002           |Nem sikerült frissíteni a fájlt, mert nincs teljesen feltöltve.|

