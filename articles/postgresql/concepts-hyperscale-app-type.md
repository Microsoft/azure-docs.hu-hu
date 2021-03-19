---
title: Alkalmazás típusának meghatározása – nagy kapacitású (Citus) – Azure Database for PostgreSQL
description: Az alkalmazás azonosítása a hatékony elosztott adatmodellezéshez
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 07/17/2020
ms.openlocfilehash: 92333857177d33307d6997bfcbdf79787d3ab127
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90895950"
---
# <a name="determining-application-type"></a>Alkalmazás típusának meghatározása

A hatékony lekérdezések nagy kapacitású-(Citus-) kiszolgálón való futtatása megköveteli, hogy a táblák megfelelően legyenek elosztva a kiszolgálók között. Az ajánlott terjesztés az alkalmazás típusától és a lekérdezési mintáktól függ.

A nagy kapacitású-on (Citus) jól működő alkalmazások széles körben működnek. Az adatmodellezés első lépéseként meg kell határozni, hogy melyikük jobban hasonlít az alkalmazáshoz.

## <a name="at-a-glance"></a>Dióhéjban

| Több-bérlős alkalmazások                                 | Valós idejű alkalmazások                                |
|-----------------------------------------------------------|-------------------------------------------------------|
| Időnként több tucat vagy több száz tábla található a sémában          | Kis számú tábla                                |
| Egy bérlőhöz (vállalathoz/áruházhoz) kapcsolódó lekérdezések egyszerre | Viszonylag egyszerű elemzési lekérdezések összesítésekkel |
| OLTP számítási feladatok a webes ügyfelek kiszolgálásához                    | Nagy mennyiségű betöltött adat, melyek többsége nem módosítható           |
| A bérlőnkénti analitikai lekérdezéseket kiszolgáló OLAP számítási feladatok   | Gyakran az események nagy méretű táblája áll a középpontjukban            |

## <a name="examples-and-characteristics"></a>Példák és jellemzők

**Több-bérlős alkalmazás**

> Ezek általában olyan SaaS-alkalmazások, amelyek más vállalatokat, fiókokat vagy szervezeteket szolgálnak ki. A legtöbb SaaS-alkalmazás eredendően összehasonlítható. Természetes dimenzióval rendelkeznek, amely a csomópontok közötti adatelosztásra terjed ki: csak a bérlői \_ azonosító alapján.
>
> A nagy kapacitású (Citus) lehetővé teszi, hogy az adatbázist akár több millió bérlőre is kibővítse, anélkül, hogy újra kellene terveznie az alkalmazást. Megtarthatja a szükséges kapcsolatot, például az illesztéseket, a külső kulcsra vonatkozó korlátozásokat, a tranzakciókat, a SAVAt és a konzisztenciát.
>
> -   **Példák**: olyan webhelyek, amelyek más vállalkozások, például egy digitális marketing megoldás vagy egy értékesítési automatizálási eszköz számára biztosítanak tárolókat.
> -   **Jellemzők**: egyetlen bérlőhöz kapcsolódó lekérdezések, amelyek nem csatlakoznak az információkhoz a bérlők között. Ez magában foglalja a OLTP számítási feladatait a bérlői elemzési lekérdezéseket kiszolgáló webes ügyfelek és OLAP számítási feladatok kiszolgálásához. Az adatbázis-sémában több tucat vagy több száz tábla is látható a több-bérlős adatmodellhez.
>
> A több-bérlős alkalmazások nagy kapacitású (Citus) való méretezéséhez az alkalmazás kódjának minimálisan szükséges módosításaira is szükség van. Támogatottak a népszerű keretrendszerek, például a Ruby on Rails és a Django.

**Valós idejű elemzések**

> Az alkalmazások nagy párhuzamosságot igényelnek, és több száz magot koordinálnak a gyors eredmények számszerű, statisztikai vagy számlálási lekérdezésekhez.  Az SQL-lekérdezések több csomóponton történő skálázásával és tetszés a nagy kapacitású (Citus) lehetővé teszi, hogy egy másodperc alatt valós idejű lekérdezéseket végezzen több milliárd rekord között.
>
> A valós idejű elemzési adatmodellekben lévő táblákat jellemzően olyan oszlopok osztják el, mint például a felhasználói \_ azonosító, az állomás \_ azonosítója vagy az eszköz \_ azonosítója.
>
> -   **Példák**: az ügyfelek felé irányuló elemzési irányítópultok, amelyek almásodperces válaszidőt igényelnek.
> -   **Jellemzők**: néhány táblázat, gyakran az eszköz-, hely-vagy felhasználói események nagy táblázatának középpontba helyezése, amely nagy mennyiségű, többnyire nem módosítható adatot igényel. Viszonylag egyszerű (de nagy számítási igényű) elemzési lekérdezések, amelyek számos összesítést és csoportos BYs-t érintenek.

Ha a helyzet a fenti esethez hasonló, akkor a következő lépés az, hogy eldöntse, hogyan ossza fel az adatait a kiszolgálói csoportba. A teljesítmény biztosításához az adatbázis-rendszergazda \' által választott terjesztési oszlopoknak meg kell egyezniük a tipikus lekérdezések hozzáférési mintáinak.

## <a name="next-steps"></a>Következő lépések

* [Válasszon egy terjesztési oszlopot](concepts-hyperscale-choose-distribution-column.md) az alkalmazás tábláihoz az Adatterjesztés hatékony elosztásához
