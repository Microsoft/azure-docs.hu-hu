---
title: 'Útmutató: a Data Catalog keresése'
description: Ez a cikk áttekintést nyújt a adatkatalógusok kereséséről.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 03/16/2021
ms.openlocfilehash: 7799266bf9cece1ed789d6fab64ec970a09fbfcb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104588438"
---
# <a name="search-the-azure-purview-data-catalog"></a>Keresés az Azure-beli hatáskörébe Data Catalog

Az adatfelderítés az első lépés az adatelemzési és adatkezelési munkaterhelések számára az adatok felhasználói számára. Az adatfelderítés időigényes lehet, mert előfordulhat, hogy nem tudja, hol találja a kívánt adatmennyiséget. Előfordulhat, hogy az adatkeresés után is kétségei vannak attól függően, hogy megbízik-e az adatkezelésben, és függőséget kell-e kihasználnia.

Az Azure-beli keresés célja, hogy felgyorsítsa az adatfelderítési folyamat felgyorsítását, hogy gyorsan megtalálják az érintett adatmennyiséget. Ez a cikk azt ismerteti, hogyan keresheti meg az Azure hatáskörébe tartozó adatkatalógust, hogy gyorsan megtalálja a keresett adatgyűjtést.

## <a name="search-the-catalog-for-assets"></a>Eszközök keresése a katalógusban

Az Azure-ban a keresősáv a hatáskörébe Studio UX tetején található.

:::image type="content" source="./media/how-to-search-catalog/purview-search-bar.png" alt-text="Az Azure-beli helyi keresősáv helyét bemutató képernyőkép" border="true":::

Ha a keresősáv gombra kattint, megtekintheti a legutóbbi keresési előzményeket és a nemrégiben elért eszközöket. Válassza az összes megtekintése lehetőséget az összes nemrég megtekintett eszköz megjelenítéséhez.

:::image type="content" source="./media/how-to-search-catalog/search-no-keywords.png" alt-text="Képernyőfelvétel a keresősáv megjelenítéséről a kulcsszavak megadása előtt" border="true":::

Adja meg azokat a kulcsszavakat, amelyek segítenek azonosítani az adategységet, például a nevét, az adattípusát, a besorolásait és a Szószedet feltételeit. Ahogy a kívánt eszközhöz kapcsolódó kulcsszavakat adja meg, az Azure-beli hatáskörébe a keresésre és a lehetséges eszköz-egyezésekre vonatkozó javaslatokat jelenít meg. A keresés befejezéséhez kattintson a "keresési eredmények megtekintése" gombra, vagy nyomja le az ENTER billentyűt.

:::image type="content" source="./media/how-to-search-catalog/search-keywords.png" alt-text="Képernyőfelvétel: a keresési sáv megjelenítése felhasználóként a kulcsszavakban" border="true":::

A találatok oldal megjeleníti azon eszközök listáját, amelyek megfelelnek a relevancia szempontjából megadott kulcsszavaknak. Számos tényező befolyásolhatja az adott eszköz relevanciás pontszámát. A lista további szűréséhez válassza ki az Ön által keresett eszközre vonatkozó konkrét adattárakat, besorolásokat, névjegyeket, címkéket és szószedeti kifejezéseket.

:::image type="content" source="./media/how-to-search-catalog/search-results.png" alt-text="A keresés eredményét bemutató képernyőkép" border="true":::

 A kívánt eszközre kattintva megtekintheti az eszköz adatai lapot, ahol megtekintheti a séma, a Lineage és az objektum tulajdonosai tulajdonságokat.

:::image type="content" source="./media/how-to-search-catalog/search-view-asset.png" alt-text="Az eszköz részletei lapot ábrázoló képernyőfelvétel" border="true":::

## <a name="search-query-syntax"></a>Keresési lekérdezés szintaxisa

Minden keresési lekérdezés kulcsszavakat és operátorokat tartalmaz. A kulcsszavak olyanok, amelyek egy adott eszköz tulajdonságainak részét képezik. A lehetséges kulcsszavak a besorolás, a Szószedet kifejezés, az eszköz leírása vagy az eszköz neve lehet. A kulcsszó csak annak a tulajdonságnak a része lehet, amelyet meg szeretne feleltetni. A kulcsszavak és az alább felsorolt operátorok használatával biztosíthatja, hogy az Azure hatáskörébe adja vissza a keresett eszközöket. 

Alább láthatók a keresési lekérdezések összeállítására szolgáló operátorok. A kezelők tetszőleges számú alkalommal kombinálhatók egyetlen lekérdezésben.

| Operátor | Definíció | Példa |
| -------- | ---------- | ------- |
| OR | Azt határozza meg, hogy egy eszköznek legalább egy kulcsszóval kell rendelkeznie. Minden sapkában kell lennie. Egy üres terület is vagy operátor.  | A lekérdezés `hive OR database` "kaptár" vagy "adatbázis" vagy mindkettőt tartalmazó eszközöket ad vissza. |
| ÉS | Azt határozza meg, hogy egy eszköznek mindkét kulcsszóval kell rendelkeznie. Minden sapkában kell lennie | A lekérdezés a `hive AND database` "kaptár" és az "adatbázis" értéket tartalmazó eszközöket ad vissza. |
| NOT | Meghatározza, hogy az eszköz nem tartalmazhatja a NOT záradéktól jobbra található kulcsszót. | A lekérdezés a `hive NOT database` "kaptár" karaktert tartalmazó eszközöket ad vissza, de az "adatbázis" nem. |
| () | Csoportosítja a kulcsszavakat és a kezelőket. Több operátor kombinálásával a zárójelek határozzák meg a műveletek sorrendjét. | A lekérdezés a `hive AND (database OR warehouse)` "kaptár" és a "Database" vagy a "Warehouse", vagy mindkettőt tartalmazó eszközöket adja vissza. |
| "" | Megadja a pontos tartalmat egy kifejezésben, amelynek a lekérdezésnek egyeznie kell. | A lekérdezés a `"hive database"` "kaptár-adatbázis" kifejezést tartalmazó eszközöket adja vissza a tulajdonságaiban. |
| * | Egy vagy több karakterből álló helyettesítő karakter. A kulcsszó első karaktere nem lehet. | A lekérdezés `hiv\` * visszaadja azokat az eszközöket, amelyek olyan tulajdonságokkal rendelkeznek, amelyek "HIV"-vel kezdődnek, például "kaptár" vagy "kaptár-Table". |
| ? | Egyetlen karakternek megfelelő helyettesítő karakter. A kulcsszó első karaktere nem lehet | A lekérdezés olyan `hiv?` eszközöket ad vissza, amelyek a "HIV" kezdetű tulajdonságokkal rendelkeznek, és négy betűből állnak, például "kaptár" vagy "," |

> [!Note]
> Minden nagybetű esetében mindig logikai operátorokat (**és**, **vagy** **nem**) kell megadni. Ellenkező esetben az eset nem számít, és nem végez felesleges szóközöket.

## <a name="next-steps"></a>Következő lépések

- [Szószedet használati feltételeinek létrehozása, importálása és exportálása](how-to-create-import-export-glossary.md)
- [Az üzleti kifejezések sablonjának kezelése Szószedet](how-to-manage-term-templates.md)
