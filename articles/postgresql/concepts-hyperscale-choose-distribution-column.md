---
title: Terjesztési oszlopok kiválasztása – nagy kapacitású (Citus) – Azure Database for PostgreSQL
description: Megtudhatja, hogyan választhat terjesztési oszlopokat az Azure Database for PostgreSQL-nagy kapacitású (Citus) gyakori forgatókönyvekben.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 129eff8c954c0c5469d3607e6ae16ce3202630ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91929336"
---
# <a name="choose-distribution-columns-in-azure-database-for-postgresql--hyperscale-citus"></a>Terjesztési oszlopok kiválasztása Azure Database for PostgreSQL – nagy kapacitású (Citus)

Az egyes táblák elosztási oszlopának kiválasztása az egyik legfontosabb modellezési döntés. Azure Database for PostgreSQL – a nagy kapacitású (Citus) a sorok eloszlás oszlopának értéke alapján a szegmensekben lévő sorokat tárolja.

A megfelelő választás a kapcsolódó adatokkal együtt ugyanazon fizikai csomópontokon csoportosítja a lekérdezéseket, így a lekérdezések gyorsak, és az összes SQL-funkció támogatását biztosítja. Helytelen választás miatt a rendszer lassan fut, és nem támogatja az összes csomóponton futó SQL-funkciót.

Ez a cikk a két leggyakoribb nagy kapacitású (Citus) forgatókönyvekhez nyújt terjesztési oszlopokra vonatkozó tippeket.

### <a name="multi-tenant-apps"></a>Több-bérlős alkalmazások

A több-bérlős architektúra hierarchikus adatbázis-modellezési formát használ a lekérdezések a kiszolgálócsoport csomópontjai közötti elosztására. Az adathierarchia felső részét a *bérlői azonosítónak* nevezzük, és az egyes táblák oszlopaiban kell tárolni.

A nagy kapacitású (Citus) megvizsgálja a lekérdezéseket, hogy megtudja, melyik bérlői AZONOSÍTÓval jár, és megkeresi a megfelelő tábla szegmensét. A lekérdezést egyetlen, a szegmenst tartalmazó munkavégző csomópontra irányítja. Egy lekérdezés futtatása az azonos csomóponton elhelyezett összes releváns adathoz közös elhelyezésű.

A következő ábra a több-bérlős adatmodell közös elhelyezését szemlélteti. Két táblát, fiókot és kampányt tartalmaz, amelyeket mindegyik terjeszt `account_id` . Az árnyékolt mezők szegmenseket jelölnek. A zöld szegmensek tárolása egy munkavégző csomóponton történik, és a kék szegmensek egy másik munkavégző csomóponton vannak tárolva. Figyelje meg, hogy a fiókok és a kampányok közötti csatlakozási lekérdezés az összes szükséges adattal együttesen egy csomóponton található, ha mindkét tábla ugyanahhoz a fiók-azonosítóhoz van korlátozva \_ .

![Több-bérlős közös elhelyezés](media/concepts-hyperscale-choosing-distribution-column/multi-tenant-colocation.png)

Ha a kialakítást a saját sémájában szeretné alkalmazni, azonosítsa, hogy mi képezi a bérlőt az alkalmazásban. Gyakori példányok például a következők: vállalat, fiók, szervezet vagy ügyfél. Az oszlop neve a következőhöz hasonló lesz: `company_id` vagy `customer_id` . Vizsgálja meg az egyes lekérdezéseket, és kérdezzen rá, hogy működik-e, ha további WHERE záradékokkal szeretné korlátozni az összes olyan táblát, amely az azonos bérlői AZONOSÍTÓval rendelkező sorokra vonatkozik?
A több-bérlős modellben lévő lekérdezések hatóköre egy bérlőre vonatkozik. Például az értékesítés vagy a leltár lekérdezése egy adott tárolón belül van hatókörben.

#### <a name="best-practices"></a>Ajánlott eljárások

-   **Elosztott táblák particionálása közös bérlői \_ azonosító oszlop szerint.** Például egy SaaS-alkalmazásban, ahol a bérlők a vállalatok, a bérlői \_ azonosító valószínűleg a vállalat \_ azonosítója lesz.
-   **Kisméretű, több-bérlős táblák átalakítása táblázatokra.** Ha több bérlő is osztozik egy kis táblázatban, küldje el hivatkozási táblázatként.
-   **Korlátozza a bérlői azonosító alapján az összes alkalmazás lekérdezésének szűrését \_ .** Minden lekérdezésnek egyszerre egy bérlő adatait kell kérnie.

A [több-bérlős oktatóanyagban](./tutorial-design-database-hyperscale-multi-tenant.md) megtudhatja, hogyan építhet ki ilyen típusú alkalmazást.

### <a name="real-time-apps"></a>Valós idejű alkalmazások

A több-bérlős architektúra egy hierarchikus struktúrát vezet be, és az adattárolást használja a lekérdezések átirányításához a bérlők számára. Ezzel szemben a valós idejű architektúrák az adataik konkrét terjesztési tulajdonságaitól függenek, hogy magas párhuzamos feldolgozást érjenek el.

A valós idejű modellben az "Entity ID" kifejezést használjuk a terjesztési oszlopok kifejezésére. A jellemző entitások felhasználók, gazdagépek vagy eszközök.

A valós idejű lekérdezések általában dátum vagy kategória szerint csoportosított numerikus összesítéseket kérnek. A nagy kapacitású (Citus) ezeket a lekérdezéseket az egyes szegmensekre küldi el részleges eredményekre, és a végső választ a koordinátor csomóponton állítja össze. A lekérdezések akkor futnak, amikor minél több csomópont járul hozzá, és ha egyetlen csomópontnak sem kell aránytalan mennyiségű munkát végrehajtania.

#### <a name="best-practices"></a>Ajánlott eljárások

-   **Válasszon olyan oszlopot, amely a terjesztési oszlop magas fokú kardinálisa.** Az összehasonlításhoz az új, fizetős és szállított értékekkel rendelkező Order tábla állapot mezőjében a terjesztési oszlop rossz választás. Csak azokat a néhány értéket feltételezi, amelyek korlátozzák az adatok tárolására képes szegmensek számát, valamint a feldolgozni képes csomópontok számát. A nagy fokú kardinális oszlopok között érdemes kiválasztania azokat az oszlopokat, amelyek gyakran használatosak a Group By záradékokban vagy a csatlakozás kulcsaként.
-   **Válasszon egy olyan oszlopot, amely egyenletes eloszlással rendelkezik.** Ha olyan oszlopot terjeszt egy oszlopra, amely bizonyos gyakori értékekre van kiválasztva, a táblázatban lévő adatok általában bizonyos szegmensekben gyűlnek össze. A szegmenseket tároló csomópontok a többi csomópontnál több munkát végeznek.
-   **A tény-és dimenzió táblák terjesztése a közös oszlopokon.**
    A Fact táblának csak egy terjesztési kulcsa lehet. Azok a táblák, amelyek egy másik kulcshoz csatlakoznak, nem lesznek közösen elhelyezve a Fact táblával. Válassza ki az egyik dimenziót, hogy a rendszer milyen gyakran csatlakozzon egymáshoz, és hogy mekkora a csatlakozás sorainak mérete.
-   **Módosítsa a dimenzió táblázatokat a hivatkozási táblákba.** Ha egy dimenzió tábla nem helyezhető el a Fact táblával, javíthatja a lekérdezési teljesítményt úgy, hogy a dimenziós tábla másolatait az összes csomópontra terjeszti egy hivatkozási tábla formájában.

Tekintse át a [valós idejű irányítópult-oktatóanyagot](./tutorial-design-database-hyperscale-realtime.md) , amely bemutatja, hogyan építhet ki ilyen típusú alkalmazásokat.

### <a name="time-series-data"></a>Idősorozat-adatsorok

Egy idősorozatos munkafolyamatban az alkalmazások a régi információk archiválása közben lekérdezik a legutóbbi információkat.

A nagy kapacitású (Citus) modellezési idősorozat-információinak leggyakoribb hibája, hogy az időbélyeget terjesztési oszlopként használja. A kivonatok eloszlása az idő alapján látszólag véletlenszerűen, különböző szegmensekre osztja szét, és nem tartja meg a tartományokban lévő időt. Az időtartományokat tartalmazó lekérdezések, például a legfrissebbek. Ez a típusú kivonatoló eloszlás hálózati terhelést eredményez.

#### <a name="best-practices"></a>Ajánlott eljárások

-   **Ne válasszon időbélyeget a terjesztési oszlopként.** Válasszon másik terjesztési oszlopot. Egy több-bérlős alkalmazásban használja a bérlői azonosítót, vagy egy valós idejű alkalmazásban használja az entitás AZONOSÍTÓját.
-   **A PostgreSQL-táblázat particionálását használja az idő függvényében.** A tábla particionálásával egy nagy méretű, időrendbe rendezett, különböző időtartományokat tartalmazó táblába tartozó, több örökölt táblába tartozó adattábla is megszakítható. A postgres-particionálású tábla nagy kapacitású (Citus) való terjesztése az örökölt táblákhoz hoz létre szegmenseket.

## <a name="next-steps"></a>Következő lépések
- Ismerje meg [, hogy az](concepts-hyperscale-colocation.md) elosztott adattárolás hogyan segíti a lekérdezéseket a gyors futásban.
