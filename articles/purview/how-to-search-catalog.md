---
title: 'How to: search the Data Catalog'
description: Ez a cikk áttekintést nyújt az adatkatalógusban való keresésről.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 03/16/2021
ms.openlocfilehash: 178604335968c3664bde51c144759c1c040c359d
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107564913"
---
# <a name="search-the-azure-purview-data-catalog"></a>Keresés az Azure Purview Data Catalog

Az adatfelderítés az adatelemzési vagy adatirányítási számítási feladatok első lépése az adat felhasználók számára. Az adatfelderítés időigényes lehet, mert előfordulhat, hogy nem tudja, hol találja a kívánt adatokat. Még az adatok megkeresése után is lehetnek kétségei, hogy megbízhat-e az adatokban, és függhet-e az adatoktól.

Az Azure Purview-keresés célja, hogy felgyorsítsa az adatfelderítési folyamatot, és gyorsan megtalálja a fontos adatokat. Ez a cikk bemutatja, hogyan kereshet az Azure Purview-adatkatalógusban a keresett adatok gyors megkereséséhez.

## <a name="search-the-catalog-for-assets"></a>Eszközök keresése a katalógusban

Az Azure Purview-ban a keresősáv a Purview Studio felhasználói felületének tetején található.

:::image type="content" source="./media/how-to-search-catalog/purview-search-bar.png" alt-text="Képernyőkép az Azure Purview keresősáv helyéről" border="true":::

Ha a keresősávra kattint, láthatja a legutóbbi keresési előzményeket és a nemrégiben elért eszközöket. Az összes Az összes megtekintése eszköz megtekintéséhez válassza a "Az összes megtekintése" lehetőséget.

:::image type="content" source="./media/how-to-search-catalog/search-no-keywords.png" alt-text="Képernyőkép a keresősávról a kulcsszavak bevitele előtt" border="true":::

Olyan kulcsszavakat adjon meg, amelyek segítenek azonosítani az eszközt, például a nevét, az adattípust, a besorolásokat és a szószedet kifejezéseket. Amikor a kívánt eszközhöz kapcsolódó kulcsszavakat ad meg, az Azure Purview javaslatokat jelenít meg a kereshető adatokra és a lehetséges eszközmeg egyezésekre. A keresés befejezéséhez kattintson a "Keresési eredmények megtekintése" elemre, vagy nyomja le az Enter billentyűt.

:::image type="content" source="./media/how-to-search-catalog/search-keywords.png" alt-text="Képernyőkép a keresősávról, amikor a felhasználó kulcsszavakat ad meg" border="true":::

A keresési eredmények oldalán a relevancia alapján megadott kulcsszavaknak megfelelő eszközök listája látható. Az eszközök relevanciapontszámát különböző tényezők befolyásolhatják. A listát tovább szűrheti, ha kiválasztja a keresett adattárra vonatkozó adott adattárakat, besorolásokat, névjegyeket, címkéket és szószedet kifejezéseket.

:::image type="content" source="./media/how-to-search-catalog/search-results.png" alt-text="Képernyőkép egy keresés eredményeiről" border="true":::

 Kattintson a kívánt eszközre az eszköz részleteit tartalmazó oldal megtekintéséhez, ahol megtekintheti a sémát, a leszárazást és az eszköztulajdonosokat.

:::image type="content" source="./media/how-to-search-catalog/search-view-asset.png" alt-text="Képernyőkép az eszközadatok lapról" border="true":::

## <a name="search-query-syntax"></a>Keresési lekérdezési szintaxis

Minden keresési lekérdezés kulcsszavakból és operátorból áll. A kulcsszó egy olyan dolog, amely az eszköz tulajdonságainak részét képezi. Lehetséges kulcsszavak: besorolás, szószedet, eszközleírás vagy eszköznév. A kulcsszó csak annak a tulajdonságnak a része lehet, amelynek meg kell egyeznie. Kulcsszavak és az alább felsorolt operátorok használatával biztosíthatja, hogy az Azure Purview visszaadja a keresett eszközöket. 

Az alábbiakban azok az operátorok olvashatók, amelyek keresési lekérdezések írására használhatók. Az operátorok egy lekérdezésben annyiszor kombinálhatók, amennyire csak szükségük van.

| Operátor | Definíció | Példa |
| -------- | ---------- | ------- |
| OR | Megadja, hogy az adateszköznek a két kulcsszó közül legalább az egyiknek kell lennie. Minden 0000-nek kell lennie. A térköz egyben OR operátor is.  | A lekérdezés `hive OR database` olyan adateszközöket ad vissza, amelyek "hive" vagy "database" vagy mindkettőt tartalmaznak. |
| ÉS | Megadja, hogy az adateszköznek mindkét kulcsszóval kell lennie. Minden cap-ban meg kell lennie | A lekérdezés olyan eszközöket ad vissza, amelyek a `hive AND database` "hive" és az "adatbázis" adatokat is tartalmazzák. |
| NOT | Megadja, hogy egy adateszköz nem tartalmazhatja a NOT záradéktól jobbra található kulcsszót | A lekérdezés olyan eszközöket ad vissza, amelyek tartalmazzák a `hive NOT database` "hive" értéket, de a "database" értéket nem. |
| () | Kulcsszavak és operátorok egy halmazát csoportosan tartalmazza. Több operátor kombinálása esetén a zárójelek határozzák meg a műveletek sorrendjét. | A lekérdezés `hive AND (database OR warehouse)` olyan eszközöket ad vissza, amelyek "hive" és "database" vagy "warehouse" vagy mindkettőt tartalmaznak. |
| "" | Pontos tartalmat ad meg egy kifejezésben, amely alapján a lekérdezésnek egyeznie kell. | A lekérdezés olyan eszközöket ad vissza, amelyek tulajdonságaikban a `"hive database"` "hive database" kifejezés található |
| * | Helyettesítő karakter, amely egy vagy több karakterre illeszkedik. Nem lehet egy kulcsszó első karaktere. | A lekérdezés olyan adateszközöket ad vissza, amelyek olyan tulajdonságokkal rendelkeznek, amelyek "dat" tulajdonságokkal rendelkeznek, például `dat*` "data" vagy "database". |
| ? | Helyettesítő karakter, amely egyetlen karakterre illeszkedik. Nem lehet egy kulcsszó első karaktere | A lekérdezés olyan adateszközöket ad vissza, amelyek tulajdonságai "dat" betűvel kezdődnek, és négy betűből állnak, például `dat?` "date" vagy "data". |

> [!Note]
> Mindig adja meg a logikai operátorokat (**AND**, **OR**, **NOT**) minden nagybetűben. Ellenkező esetben a kis- és a kis- és a szóközök nem számítanak.

## <a name="next-steps"></a>Következő lépések

- [Szószedet kifejezések létrehozása, importálása és exportálása](how-to-create-import-export-glossary.md)
- [Üzleti szószedet kifejezéssablonjainak kezelése](how-to-manage-term-templates.md)
