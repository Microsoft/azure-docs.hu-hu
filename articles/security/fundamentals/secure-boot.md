---
title: Belső vezérlőprogram biztonságos rendszerindítása – Azure Security
description: Az Azure belső vezérlőprogram biztonságos rendszerindításának technikai áttekintése.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: feb28b1d448d0146046ed789d1389a3a42f344de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94557665"
---
# <a name="secure-boot"></a>Biztonságos rendszerindítás

A biztonságos rendszerindítás a [Unified Extensible Firmware Interface](https://en.wikipedia.org/wiki/Unified_Extensible_Firmware_Interface) (UEFI) egyik funkciója, amelyhez az összes alacsony szintű belső vezérlőprogram és szoftver-összetevő ellenőrzése szükséges a betöltés előtt. A rendszerindítás során az UEFI biztonságos rendszerindítás ellenőrzi az egyes rendszerindító szoftverek aláírását, beleértve az UEFI belső vezérlőprogram-illesztőprogramjait (más néven Option ROMs), Extensible Firmware Interface (EFI) alkalmazásokat, valamint az operációs rendszer illesztőprogramjait és bináris fájljait. Ha az aláírások érvényesek vagy megbízhatók az eredeti berendezésgyártó (OEM) számára, a gép elindul és a belső vezérlőprogram vezérli az operációs rendszert.

## <a name="components-and-process"></a>Összetevők és folyamat

A biztonságos rendszerindítás ezekre a kritikus összetevőkre támaszkodik:

- Platform kulcsa (PK) – megbízhatósági kapcsolatot létesít a platform tulajdonosa (Microsoft) és a belső vezérlőprogram között. A PKpub, a privát fél pedig a PKpriv.
- Key beléptetési kulcs adatbázisa (KEK) – megbízhatósági kapcsolatot létesít az operációs rendszer és a platform belső vezérlőprogramja között. A KEKpub, a privát fél pedig a KEKpriv.
- Aláírási adatbázis (db) – a platform belső vezérlőprogram használatával való interakcióra jóváhagyott belső vezérlőprogram és a szoftveres kódokhoz tartozó megbízható aláírók (nyilvános kulcsok és tanúsítványok) kivonatait tárolja.
- Visszavont aláírások adatbázisa (dbx) – a kód modulok olyan visszavont kivonatait tartalmazza, amelyeket kártékonynak, sebezhetőnek, sérültnek vagy nem megbízhatónak azonosítottak. Ha egy kivonat az aláírási adatbázisban és a visszavont aláírások dB-ben található, a visszavont aláírások adatbázisa precedenst kap.

Az alábbi ábra és folyamat ismerteti az összetevők frissítésének módját:

![A biztonságos rendszerindítási összetevőket bemutató diagram.](./media/secure-boot/secure-boot.png)

Az OEM a gép nem felejtő RAM-ában (NV-RAM) tárolja a biztonságos rendszerindítási kivonatokat a gyártás idején.

1. Az aláírási adatbázis (db) az UEFI-alkalmazások, az operációs rendszerbeli betöltők (például a Microsoft operációsrendszer-betöltő vagy a rendszerindító-kezelő) és a megbízható UEFI-illesztőprogramok aláírásával vagy képkivonatával van feltöltve.
2. A visszavont aláírások adatbázisa (dbx) a már nem megbízható modulok kivonatával van feltöltve.
3. A Key beléptetési kulcs (KEK) adatbázisa olyan aláíró kulcsokkal van feltöltve, amelyek segítségével frissíthető az aláírás-adatbázis és a visszavont aláírások adatbázisa. Az adatbázisok szerkeszthetők a megfelelő kulccsal aláírt, vagy egy fizikailag is jogosult felhasználó által a belső vezérlőprogram menüivel írt frissítések segítségével.
4. Az adatbázis, a DBX és a KEK-adatbázisok hozzáadása, valamint a belső vezérlőprogram-ellenőrzés és a tesztelés befejezése után az OEM lezárja a belső vezérlőprogramot a platform-kulcs (PK) szerkesztésével és létrehozásával. A PK használatával a frissítések a KEK-be vagy a biztonságos rendszerindítás kikapcsolására használhatók.

A rendszerindítási folyamat egyes szakaszaiban a belső vezérlőprogram, a bootloader, az operációs rendszer, a kernel-illesztőprogramok és az egyéb rendszerindító láncú összetevők kivonatait számítjuk ki és hasonlítjuk össze az elfogadható értékekkel. A nem megbízhatóként felderített belső vezérlőprogram és szoftverek betöltése nem engedélyezett. Így az alacsony szintű kártevő-befecskendezés vagy a rendszerindítás előtti kártevők elleni támadás blokkolható.

## <a name="secure-boot-on-the-azure-fleet"></a>Biztonságos rendszerindítás az Azure-flottán
Napjainkban minden olyan gép, amely az Azure számítási flottába van bevezetve, és az ügyfelek munkaterheléseit üzemelteti, a biztonságos rendszerindítás engedélyezve van a gyári szintekről. A rendszer a hardveres buildout és az integrációs folyamat minden egyes fázisában megcélozza a megcélzó eszközöket és folyamatokat, így biztosítva, hogy a biztonságos rendszerindítási engedélyezés nem áll helyre véletlenül vagy rosszindulatú szándékkal.

Annak ellenőrzése, hogy az adatbázis és a DBX kivonatok helyesek-e, gondoskodik a következőkről:

- A rendszerbetöltő megtalálható az egyik db-bejegyzésben
- A rendszerbetöltő aláírása érvényes
- A gazdagép megbízható szoftverrel indul

 A KEKpub és a PKpub aláírásának ellenőrzésével meggyőződhet arról, hogy csak a megbízható felek rendelkeznek a megbízhatónak ítélt szoftverek definícióinak módosításához szükséges engedéllyel. Végül, a biztonságos rendszerindítás aktív állapotának biztosításával ellenőrizheti, hogy a definíciók érvényben vannak-e.

## <a name="next-steps"></a>Következő lépések
Ha többet szeretne megtudni a platform integritásának és biztonságának elvégzéséről, olvassa el a következőt:

- [A belső vezérlőprogram biztonsága](firmware.md)
- [Mért rendszerindítás és gazdagép-igazolás](measured-boot-host-attestation.md)
- [Project Cerberus](project-cerberus.md)
- [Titkosítás inaktív állapotban](encryption-atrest.md)
- [A hipervizor biztonsága](hypervisor.md)