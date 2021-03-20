---
title: Az üzleti Szószedet funkcióinak megismerése az Azure hatáskörébe (előzetes verzió)
description: Ez a cikk az Azure hatáskörébe tartozó üzleti szószedetet ismerteti.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/13/2020
ms.openlocfilehash: 8b391438d8d6605e7ef493a6552af634db840ad5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96553256"
---
# <a name="understand-business-glossary-features-in-azure-purview"></a>Az üzleti Szószedet funkcióinak megismerése az Azure hatáskörébe

Ez a cikk az Azure hatáskörébe tartozó üzleti Szószedet funkció áttekintését tartalmazza. 

## <a name="business-glossary"></a>Üzleti szószedet

A Szószedet az üzleti felhasználók számára biztosít szókincset.  Olyan üzleti feltételekből áll, amelyek egymáshoz kapcsolódnak, és lehetővé teszik, hogy kategorizálva legyenek, hogy más kontextusokban is érthetőek legyenek. Ezeket a kifejezéseket ezután olyan eszközökhöz lehet hozzárendelni, mint például egy adatbázis, egy tábla, egy oszlop stb. Ez segít az adattárakhoz kapcsolódó műszaki szakzsargonk összevonásában, és lehetővé teszi az üzleti felhasználó számára, hogy felderítse és működjön együtt a szókincsben lévő, a számukra ismerős információkkal.


Az üzleti Szószedet a feltételek gyűjteménye. Minden kifejezés a szervezet egy objektumát jelöli, és nagyon valószínű, hogy több kifejezés is ugyanazt az objektumot jelképezi. Az ügyfelet ügyfélként, vásárlóként vagy vásárlóként is lehet hivatkozni. Ezek a több kifejezés kapcsolattal rendelkezik egymással. A jelen feltételek közötti kapcsolat a következők egyike lehet:

- szinonimák – eltérő kifejezések ugyanazzal a definícióval
- kapcsolódó – eltérő név hasonló definícióval

Ugyanez a kifejezés több üzleti objektumot is magában foglalhat. Fontos, hogy minden egyes kifejezés jól definiált és világosan érthető legyen a szervezeten belül.

## <a name="custom-attributes"></a>Egyéni attribútumok

Az Azure-beli hatáskörébe nyolc beépített attribútum használható bármely üzleti Szószedet-kifejezéshez:
- Name
- Definíció
- Adatkezelők
- Adatszakértők
- Betűszó
- Szinonimák
- Kapcsolódó feltételek
- Erőforrások

Ezek az attribútumok nem szerkeszthetők és nem törölhetők. Ezek az attribútumok azonban nem elegendőek ahhoz, hogy teljes mértékben definiálják a kifejezéseket egy szervezetben. A probléma megoldásához a hatáskörébe tartozik egy olyan szolgáltatás, amely egyéni attribútumokat határozhat meg a szószedethez.

## <a name="term-templates"></a>Lejárati sablonok

A sablonok kifejezése lehetővé teszi a szószedetben szereplő egyéni attribútumok logikai csoportosítását a katalógusban. A funkció lehetővé teszi, hogy csoportosítsa az összes releváns egyéni attribútumot egy sablonban, majd alkalmazza a sablont a Szószedet kifejezésének létrehozásakor. Például az összes pénzügyi vonatkozású egyéni attribútum, például a Cost Center, a profit center, a könyvelési kód egy term sablon pénzügyi sablonban csoportosítható, a pénzügyi sablon pedig a pénzügyi Szószedet feltételeit is felhasználhatja.

Az összes szabványos attribútum egy rendszer alapértelmezett sablonban van csoportosítva. Az Ön által létrehozott bármely sablon a sablon létrehozási folyamatának részeként létrehozott további egyéni attribútumokkal együtt fogja tartalmazni ezeket az attribútumokat.

## <a name="glossary-vs-classification-vs-sensitivity-labels"></a>Szószedet vs besorolás vs érzékenységi Címkék

Habár a Szószedet feltételei, a besorolások és a címkék egy adategységhez fűzött megjegyzések, a katalógus kontextusában mindegyiknek más jelentése van. 

### <a name="glossary"></a>Szószedet

A fentiekben leírtaknak megfelelően az üzleti Szószedet kifejezés határozza meg a szervezet üzleti szókincsét, és segít a vállalat különböző osztályai közötti szakadék áthidalása terén.

### <a name="classifications"></a>Besorolások

A besorolások olyan megjegyzések, amelyek entitásokhoz rendelhetők. A besorolások rugalmassága lehetővé teszi, hogy több forgatókönyvhöz is használhassa őket, például:

- az adategységekben tárolt adatok természetének megértése
- hozzáférés-vezérlési szabályzatok definiálása

A hatáskörébe jelenleg több mint 100 rendszerosztályozó tartozik, és a katalógusban definiálhatja saját besorolásait. A beolvasási folyamat részeként automatikusan felismeri ezeket a besorolásokat, és alkalmazza azokat az adategységekre és sémára. Ugyanakkor bármikor felülbírálhatja őket. Az emberi felülbírálások soha nem cserélhetők le automatizált vizsgálatokkal.

### <a name="sensitivity-labels"></a>Bizalmassági címkék

Az érzékenységi címkék olyan típusú megjegyzések, amelyek lehetővé teszik a szervezet adatainak besorolását és megóvását anélkül, hogy a hatékonyságot és az együttműködést kellene akadályozni. Az érzékenységi címkék segítségével azonosíthatja a szervezeti adatain belüli besorolási típusok kategóriáit, és csoportosíthatja az egyes kategóriákra alkalmazni kívánt házirendeket. A hatáskörébe ugyanazokat a kényes adattípusokat használja, mint a Microsoft 365, ami lehetővé teszi a meglévő biztonsági szabályzatok és a teljes tartalom és adatkészletek védelmét. Ugyanazok a címkék oszthatók meg Microsoft Office termékek és adategységek között a hatáskörébe.

## <a name="next-steps"></a>Következő lépések

- [Távú sablonok kezelése](how-to-manage-term-templates.md)
- [Az adatkatalógus tallózása az Azure hatáskörébe](how-to-browse-catalog.md)
