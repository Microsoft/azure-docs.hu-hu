---
title: Az Adatmásolás egyszerűen, a másolás varázslóval – Azure
description: Ismerje meg, hogyan másolhatók az adatok a támogatott adatforrásokból a mosogatóba a Data Factory másolás varázslóval.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: f3686a22291e8595deabe3e207ce66744c3cdd82
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100376904"
---
# <a name="copy-or-move-data-easily-with-azure-data-factory-copy-wizard"></a>Az Adatmásolás és az adatáthelyezés Egyszerűen Azure Data Factory másolási varázslóval
> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse meg a [másolási tevékenység oktatóanyagát](../quickstart-create-data-factory-dot-net.md). 


A Azure Data Factory másolási varázsló megkönnyíti az adatbevitel folyamatát, ami általában egy teljes körű adatintegrációs forgatókönyv első lépése. A Azure Data Factory másolási varázsló futtatásakor nem kell megismernie a társított szolgáltatások, adatkészletek és folyamatok JSON-definícióit. A varázsló lépéseinek elvégzése után azonban a varázsló automatikusan létrehoz egy folyamatot, amely a kijelölt adatforrásból a kiválasztott célhelyre másolt adatok másolására szolgáló folyamat. Emellett a másolás varázsló segítségével ellenőrizheti a szerzői műveletek során betöltött adatok érvényességét, ami sok időt takaríthat meg, különösen akkor, ha az adatforrásból először végez adatgyűjtést. A másolás varázsló elindításához kattintson az adattároló kezdőlapján található **Adatmásolási** csempe elemre.

![Másolás varázsló](./media/data-factory-copy-wizard/copy-data-wizard.png)

## <a name="an-intuitive-wizard-for-copying-data"></a>Intuitív varázsló az adatok másolásához
Ez a varázsló lehetővé teszi, hogy a különböző forrásokból származó adatok könnyedén áthelyezhetők legyenek a célhelyekre percek alatt. A varázsló lépéseinek elvégzése után a rendszer automatikusan létrehoz egy másolási tevékenységet tartalmazó folyamatot a függő Data Factory entitások (társított szolgáltatások és adatkészletek) mellett. A folyamat létrehozásához nincs szükség további lépésekre.   

![Adatforrás kiválasztása](./media/data-factory-copy-wizard/select-data-source-page.png)

> [!NOTE]
> A [másolási varázsló oktatóanyaga](data-factory-copy-data-wizard-tutorial.md) című cikk részletes útmutatást nyújt egy olyan mintavételi folyamat létrehozásához, amely az adatok Azure-blobból egy Azure SQL Database táblába való másolását ismerteti. 
> 
> 

A varázsló az indítástól big datat szem előtt tartva lett kialakítva. Egyszerű és hatékony olyan Data Factory folyamatokat létrehozni, amelyek több száz mappát, fájlt vagy táblázatot helyeznek át a Adatok másolása varázsló segítségével. A varázsló a következő három funkciót támogatja: automatikus adatelőnézet, séma rögzítése és leképezése, valamint az Adatszűrés. 

## <a name="automatic-data-preview"></a>Automatikus adatelőnézet
A másolás varázsló lehetővé teszi a kijelölt adatforrásból származó adatok egy részének áttekintését annak ellenőrzésére, hogy a megfelelő adatok szerepelnek-e a másolandó adatok között. Emellett, ha a forrásadatok szövegfájlban találhatóak, a másolás varázsló elemzi a szövegfájlt a sor-és oszlop-határolójelek és a séma automatikus megismeréséhez. 

![A Fájlformátum beállításai](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>Séma rögzítése és leképezése
Előfordulhat, hogy a bemeneti adatok sémája nem egyezik meg bizonyos esetekben a kimeneti adatok sémájával. Ebben a forgatókönyvben a forrás sémából származó oszlopokat kell leképeznie a célként megadott sémából származó oszlopokra. 

A másolás varázsló automatikusan leképezi a forrás sémában lévő oszlopokat a célként megadott sémában lévő oszlopokra. A leképezések felülbírálása a legördülő lista (vagy) használatával megadható, hogy a rendszer kihagyja-e az oszlopot az adatok másolása közben.   

![Séma-hozzárendelés](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>Az adatok szűrése
A varázsló lehetővé teszi a forrásadatok szűrését, hogy csak azokat az adatforrásokat válassza ki, amelyeket át kell másolni a cél/fogadó adattárba. A szűrés csökkenti a fogadó adattárba másolandó adatok mennyiségét, így fokozza a másolási művelet átviteli sebességét. Rugalmas módszert biztosít a kapcsolódó adatbázisokban lévő adatok szűrésére egy Azure Blob mappában lévő SQL Query Language (vagy) fájlok használatával [Data Factory függvények és változók](data-factory-functions-variables.md)használatával.   

### <a name="filtering-of-data-in-a-database"></a>Adatbázisbeli Adatszűrés
A példában az SQL-lekérdezés a `Text.Format` függvényt és a `WindowStart` változót használja. 

![Kifejezések ellenőrzése](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Az Azure Blob mappában található Adatszűrés
A mappa elérési útja változóit használhatja a [rendszerváltozók](data-factory-functions-variables.md#data-factory-system-variables)alapján a futtatókörnyezetben meghatározott mappákból származó adatok másolásához. A támogatott változók a következők: **{Year}**, **{month}**, **{Day}**, **{Hour}**, **{minute**} és **{Custom}**. Példa: inputfolder/{Year}/{month}/{Day}.

Tegyük fel, hogy a bemeneti mappák a következő formátumban vannak:

```text
2016/03/01/01
2016/03/01/02
2016/03/01/03
...
```

Kattintson a **fájl vagy mappa** **Tallózás** gombjára, keresse meg az egyik mappát (például 2016->03->01->02), és kattintson a **választás** gombra. Ekkor a szövegmezőben kell megjelennie `2016/03/01/02` . Most cserélje le a **2016** -et a { **Year}**, a **03** és a **{month}**, a **01** , a **{Day}** és a **02** ({ **Hour}**) helyére, és nyomja le a TAB billentyűt. A következő négy változó formátumának kiválasztásához le kell látnia a legördülő listát:

![Rendszerváltozók használata](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

Ahogy az alábbi képernyőképen is látható, használhat **Egyéni** változót és bármely [támogatott formázó karakterláncot](/dotnet/standard/base-types/custom-date-and-time-format-strings)is. Az adott struktúrát tartalmazó mappa kiválasztásához először használja a **Tallózás** gombot. Ezután cserélje le az értéket **{Custom}** értékre, majd a TAB billentyű lenyomásával megtekintheti a szövegmezőt, ahol megadhatja a formázó karakterláncot.     

![Egyéni változó használata](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## <a name="support-for-diverse-data-and-object-types"></a>Különböző adattípusok és objektumtípusok támogatása
A másolás varázsló segítségével több száz mappát, fájlt vagy táblát helyezhet át hatékonyan.

![Válassza ki azokat a táblákat, amelyekről másolni szeretné az adatait](./media/data-factory-copy-wizard/select-tables-to-copy-data.png)

## <a name="scheduling-options"></a>Ütemezési beállítások
A másolási műveletet egyszer vagy időpontban is futtathatja (óránként, naponta stb.). Mindkét lehetőség használható a helyszíni, a Felhőbeli és a helyi asztali másolási összekötők szélességének növelésére.

Az egyszeri másolási művelettel csak egyszer engedélyezhető az adatáthelyezés a forrásból a célhelyre. Bármilyen méretű és bármilyen támogatott formátumú adatmennyiségre vonatkozik. Az ütemezett másolás lehetővé teszi az Adatmásolást az előírt ismétlődéssel. Az ütemezett másolás konfigurálásához használhat gazdag beállításokat (például újrapróbálkozás, időtúllépés és riasztások).

![Ütemezési tulajdonságok](./media/data-factory-copy-wizard/scheduling-properties.png)

## <a name="next-steps"></a>Következő lépések
A másolási tevékenységgel rendelkező folyamat létrehozásához a Data Factory másolási varázslóval című cikkből megtudhatja, hogyan hozhat létre [folyamatokat a másolás varázsló](data-factory-copy-data-wizard-tutorial.md)segítségével.