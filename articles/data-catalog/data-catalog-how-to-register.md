---
title: Adatforrások regisztrálása a Azure Data Catalogban
description: Ez a cikk bemutatja, hogyan regisztrálhat adatforrásokat Azure Data Catalogban, beleértve a regisztráció során kinyert metaadatokat is.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: fe132c5f80e138ef47db758ad42c04b8e854ca00
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "104674801"
---
# <a name="register-data-sources-in-azure-data-catalog"></a>Adatforrások regisztrálása a Azure Data Catalogban

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

## <a name="introduction"></a>Bevezetés
A Azure Data Catalog egy teljes körűen felügyelt felhőalapú szolgáltatás, amely a vállalati adatforrások regisztrálási és felderítési rendszereként szolgál. Más szóval a Data Catalog segít az embereknek felderíteni, értelmezni és használni az adatforrásokat, és segít a szervezeteknek a meglévő adatokból származó további értékek megszerzésében. Az adatforrások Data Catalogon keresztüli felderíthetővé tételének első lépése az adatforrás regisztrálása.

## <a name="register-data-sources"></a>Adatforrások regisztrálása
A regisztráció a metaadatok az adatforrásból való kinyerésének és az adatoknak a Data Catalog szolgáltatásba való másolásának folyamata. Az adatok helye nem változik, ahogy az sem, hogy mely rendszergazdák felügyelik az adatokat, és milyen szabályzatok érvényesek rájuk.

Adatforrás regisztrálásához tegye a következőket:
1. A Azure Data Catalog-portálon indítsa el a Data Catalog adatforrás-regisztrációs eszközt. 
2. Jelentkezzen be munkahelyi vagy iskolai fiókjával ugyanazzal a Azure Active Directory hitelesítő adatokkal, amelyeket a portálra való bejelentkezéshez használ.
3. Válassza ki a regisztrálni kívánt adatforrást.

További részletes részletekért tekintse meg az [Ismerkedés a Azure Data Catalog](data-catalog-get-started.md) oktatóanyaggal című témakört.

Az adatforrás regisztrálását követően a katalógus nyomon követi a helyét, és indexeli a metaadatokat. A felhasználók megkereshetik, böngészhetik és felfedezhetik az adatforrást, majd a helyük alapján csatlakozhatnak hozzá az alkalmazással vagy az általuk választott eszköz használatával.

## <a name="supported-data-sources"></a>Támogatott adatforrások
A jelenleg támogatott adatforrások listáját lásd: [Data Catalog DSR](data-catalog-dsr.md).

## <a name="structural-metadata"></a>Szerkezeti metaadatok
Amikor regisztrál egy adatforrást, a regisztrációs eszköz kigyűjti a kiválasztott objektumok struktúrájával kapcsolatos információkat. Ezt az információt strukturális metaadatoknak nevezzük.

Az összes objektum esetében ez a szerkezeti metaadatok tartalmazzák az objektum helyét, így az adatokat felderítő felhasználók az adott információ segítségével csatlakozhatnak az objektumhoz az általuk választott eszközökön. Más szerkezeti metaadatok az objektum neve és típusa, valamint az attribútum/oszlop neve és adattípusa is.

## <a name="descriptive-metadata"></a>Leíró metaadatok
Az adatforrásból kinyert alapvető szerkezeti metaadatok mellett az adatforrás-regisztrációs eszköz kigyűjti a leíró metaadatokat. SQL Server Analysis Services és SQL Server Reporting Services esetében ez a metaadatok a szolgáltatások által elérhető Leírás tulajdonságaiból származnak. SQL Server esetén a \_ rendszer kinyeri az MS Description Extended tulajdonsággal megadott értékeket. Oracle Database esetében az adatforrás-regisztrációs eszköz kibontja a megjegyzések oszlopot az összes \_ lap \_ Megjegyzés nézetből.

Az adatforrásból kinyert leíró metaadatok mellett a felhasználók az adatforrás-regisztrációs eszköz használatával is megadhatják a leíró metaadatokat. A felhasználók hozzáadhatnak címkéket, és meghatározhatják a regisztrált objektumok szakértőit. Az összes leíró metaadatot a rendszer átmásolja a Data Catalog szolgáltatásba a strukturális metaadatokkal együtt.

## <a name="include-previews"></a>Előzetes verziók belefoglalása
Alapértelmezés szerint csak a metaadatok vannak kinyerve az adatforrásokból, és a Data Catalog szolgáltatásba kerülnek, de az adatforrások megismerése gyakran egyszerűbb, ha megtekinti a benne található adatok mintáját.

A Data Catalog adatforrás-regisztrációs eszköz használatával megadhatja az adatokhoz tartozó pillanatkép-előnézetet az egyes táblákban és nézetekben, amelyek regisztrálva vannak. Ha úgy dönt, hogy a regisztráció során előnézeteket is tartalmaz, a regisztrációs eszköz legfeljebb 20 rekordot tartalmaz az egyes táblákból és nézetből. Ezt a pillanatképet ezután a rendszer átmásolja a katalógusba a strukturális és leíró metaadatokkal együtt.

> [!NOTE]
> A nagy számú oszloppal rendelkező széles táblák esetében kevesebb, mint 20 rekord szerepel az előzetes verzióban.
>
>

## <a name="include-data-profiles"></a>Adatprofilok belefoglalása
Ugyanúgy, mint például az előzetes verziók, hasznos környezetet biztosíthat a Data Catalog adatforrásait kereső felhasználók számára, beleértve az adatprofilokat is, így könnyebben megismerheti a felderített adatforrásokat.

A Data Catalog adatforrás-regisztrációs eszköz használatával a regisztrált táblákhoz és nézetekhez adatprofilokat is megadhat. Ha úgy dönt, hogy a regisztráció során adatprofilt is tartalmaz, a regisztrációs eszköz az egyes táblák és nézetek adatainak összesített statisztikáit tartalmazza, beleértve a következőket:

* Az objektumban lévő sorok és méretek száma.
* Az adatok és az objektum sémájának legutóbbi frissítésének dátuma.
* A null rekordok és az oszlopok eltérő értékeinek száma.
* Az oszlopok minimális, maximális, átlagos és szórási értékei.

Ezeket a statisztikákat ezután a rendszer átmásolja a katalógusba a strukturális és leíró metaadatokkal együtt.

> [!NOTE]
> A szöveg és a dátum oszlopok nem tartalmazzák az átlagos vagy szórásos statisztikai adatokat az adatprofilban.
>
>

## <a name="update-registrations"></a>Regisztrációk frissítése
Az adatforrások regisztrálása a Data Catalog során felderíthetővé teszi a metaadatokat, és a regisztráció során kibontott opcionális előnézet is használható. Ha az adatforrást frissíteni kell a katalógusban (például ha egy objektum sémája módosult, az eredetileg kizárt táblákat is bele kell foglalni, vagy frissítenie kell az előzetes verzióban található adatforrásokat), az adatforrás-regisztrációs eszköz újra futtatható.

Egy már regisztrált adatforrás újbóli regisztrálása "upsert" műveletet hajt végre: a meglévő objektumok frissülnek, és új objektumok jönnek létre. A felhasználók által a Data Catalog portálon biztosított metaadatok megmaradnak.

## <a name="summary"></a>Összefoglalás
Mivel az adatforrásból származó strukturális és leíró metaadatokat másolja a Catalog szolgáltatásba, az adatforrást a Data Catalogban regisztrálva az adatok könnyebben felderíthető és megérthetővé válik. Miután regisztrálta az adatforrást, a Data Catalog-portálon láthatja, felügyelheti és derítheti fel.

## <a name="next-steps"></a>Következő lépések
Az adatforrások regisztrálásával kapcsolatos további információkért tekintse meg az [első lépések Azure Data Catalog](data-catalog-get-started.md) oktatóanyagot.
