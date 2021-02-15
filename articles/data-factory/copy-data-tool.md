---
title: Adatok másolása eszköz Azure Data Factory
description: Információt nyújt a Adatok másolása eszközről Azure Data Factory felhasználói felületen
author: dearandyxu
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/17/2020
ms.author: yexu
ms.openlocfilehash: 18d61c7f51547a524e64fc44c98e70e0f5bc2752
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100372994"
---
# <a name="copy-data-tool-in-azure-data-factory"></a>Adatok másolása eszköz az Azure Data Factoryben
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A Azure Data Factory Adatok másolása eszköz megkönnyíti és optimalizálja az adatfeldolgozás folyamatát egy adattóba, ami általában egy teljes körű adatintegrációs forgatókönyv első lépése.  Időt takaríthat meg, különösen akkor, ha a Azure Data Factory használatával tölti be az adatforrásból az adatok első alkalommal történő betöltését. Az eszköz használatának néhány előnye:

- A Azure Data Factory Adatok másolása eszköz használatakor nincs szükség a társított szolgáltatások, adatkészletek, folyamatok, tevékenységek és eseményindítók Data Factory-definícióinak megismerésére. 
- A Adatok másolása eszköz folyamata intuitív módon tölti be az adatbevitelt egy adattóba. Az eszköz automatikusan létrehozza az összes szükséges Data Factory erőforrást, hogy az adatok a kiválasztott forrás adattárból a kiválasztott célhelyre/fogadó adattárba másolva legyenek. 
- A Adatok másolása eszköz segítségével érvényesítheti a szerzői műveletek során betöltött adatmennyiséget, ami segít elkerülni a lehetséges hibákat az elején.
- Ha összetett üzleti logikát kell megvalósítani az adatgyűjtés egy adattóba való betöltéséhez, akkor továbbra is szerkesztheti a Adatok másolása eszköz által létrehozott Data Factory erőforrásokat a Data Factory felhasználói felületen a felhasználónkénti szerzői művelettel. 

Az alábbi táblázat útmutatást nyújt arra vonatkozóan, hogy mikor kell használni a Adatok másolása eszközt és a tevékenységhez tartozó szerzői műveleteket Data Factory felhasználói felületen: 

| Adatok másolása eszköz | /Tevékenység (másolási tevékenység) készítése |
| -------------- | -------------------------------------- |
| Az Azure Data Factory entitások (társított szolgáltatások, adatkészletek, folyamatok stb.) megismerése nélkül szeretne egyszerűen felépíteni egy betöltési feladatot. | Összetett és rugalmas logikát kíván megvalósítani az adatgyűjtés a Lake-be való betöltéséhez. |
| Gyorsan nagy mennyiségű adatösszetevőt szeretne betölteni egy adattóba. | A másolási tevékenységet a következő tevékenységekkel szeretné átmásolni az adattisztításhoz vagy az adatfeldolgozáshoz. |

Az Adatok másolása eszköz elindításához kattintson az adatgyár kezdőlapján található **adatok másolása** csempére.

![Első lépések lap – hivatkozás Adatok másolása eszközre](./media/doc-common-process/get-started-page.png)


## <a name="intuitive-flow-for-loading-data-into-a-data-lake"></a>Intuitív folyamat az adatbevitelhez egy adattóba
Ez az eszköz lehetővé teszi, hogy a különböző forrásokból származó adatok könnyedén áthelyezhetők legyenek a célhelyekre egy intuitív folyamattal:  

1. Konfigurálja a **forrás** beállításait.
2. Adja meg a **célhely** beállításait. 
3. Konfigurálja a másolási művelet **speciális beállításait** , például az oszlopok leképezését, a teljesítmény beállításait és a hibatűrési beállításokat. 
4. Az adatok betöltésére szolgáló feladat **ütemtervének** megadása. 
5. Tekintse át a létrehozandó Data Factory entitások **összegzését** . 
6. A folyamat **szerkesztésével módosítsa** a másolási tevékenység beállításait igény szerint. 

   Az eszközt úgy tervezték, hogy a kezdéstől big data szem előtt tartva a különböző adatok és objektumtípusok támogatását. Több száz mappa, fájl vagy tábla áthelyezésére is használható. Az eszköz támogatja az automatikus adatelőnézett, a sémák rögzítését és az automatikus leképezést, valamint az adatszűrést is.

![Adatok másolása eszköz](./media/copy-data-tool/copy-data-tool.png)

## <a name="automatic-data-preview"></a>Automatikus adatelőnézet
Az adatok egy részét előzetesen megtekintheti a kiválasztott forrás adattárból, amely lehetővé teszi a másolt adatok érvényesítését. Emellett, ha a forrásadatok szövegfájlban találhatóak, a Adatok másolása eszköz elemzi a szövegfájlt, hogy automatikusan felderítse a sor-és oszlop-határolójeleket és a sémát.

![Fájl beállításai](./media/copy-data-tool/file-format-settings.png)

Az észlelés után:

![Észlelt fájl beállításai és előnézet](./media/copy-data-tool/after-detection.png)

## <a name="schema-capture-and-automatic-mapping"></a>Séma rögzítése és automatikus leképezése
Előfordulhat, hogy az adatforrás sémája nem egyezik meg az adatforrások sémájának számos esetben. Ebben a forgatókönyvben a forrás sémából származó oszlopokat kell leképeznie a célként megadott sémából származó oszlopokra.

A Adatok másolása eszköz figyeli és megtanulja a viselkedését, ha a forrás-és a célhelyek közötti oszlopokat térképezi fel. Miután kiválasztotta a forrás adattárból egy vagy több oszlopot, és leképezi őket a célként megadott sémába, a Adatok másolása eszköz elkezdi elemezni a két oldalról kiválasztott oszlop párok mintázatát. Ezt követően ugyanezt a mintát alkalmazza a többi oszlopra is. Ezért láthatja, hogy az összes oszlop hozzá van rendelve a célhoz úgy, ahogy azt több kattintás után szeretné.  Ha nem elégedett az Adatok másolása eszköz által biztosított oszlop-hozzárendelési lehetőséggel, akkor figyelmen kívül hagyhatja, és folytathatja az oszlopok manuális leképezését. Eközben a Adatok másolása eszköz folyamatosan megtanulja és frissíti a mintát, és végül eléri a megfelelő mintát a elérni kívánt oszlop-hozzárendeléshez. 

> [!NOTE]
> Ha az adatok másolása SQL Server vagy Azure SQL Database az Azure szinapszis Analytics szolgáltatásba történik, ha a tábla nem létezik a célhelyen, Adatok másolása eszköz támogatja a tábla automatikus létrehozását a forrásoldali séma használatával. 

## <a name="filter-data"></a>Adatok szűrése
A forrásadatok szűrésével kiválaszthatja azokat az adatforrásokat, amelyeket át kell másolni a fogadó adattárba. A szűrés csökkenti a fogadó adattárba másolandó adatok mennyiségét, így fokozza a másolási művelet átviteli sebességét. Az Adatok másolása eszköz rugalmas módot biztosít a kapcsolódó adatbázisok adatszűrésére az SQL-lekérdezési nyelv vagy egy Azure Blob mappában található fájlok használatával. 

### <a name="filter-data-in-a-database"></a>Adatszűrés egy adatbázisban
Az alábbi képernyőfelvételen egy SQL-lekérdezés látható az adatszűréshez.

![Adatszűrés egy adatbázisban](./media/copy-data-tool/filter-data-in-database.png)

### <a name="filter-data-in-an-azure-blob-folder"></a>Az Azure Blob mappában lévő Adatszűrés
A mappa elérési útja változóit használhatja az adatok mappából való másolásához. A támogatott változók a következők: **{Year}**, **{month}**, **{Day}**, **{Hour}** és **{minute}**. Például: inputfolder/{Year}/{month}/{Day}. 

Tegyük fel, hogy a bemeneti mappák a következő formátumban vannak: 

```
2016/03/01/01
2016/03/01/02
2016/03/01/03
...
```

Kattintson a **fájl vagy mappa** **Tallózás** gombjára, keresse meg az egyik mappát (például 2016->03->01->02), és kattintson a **választás** gombra. A szövegmezőben a 2016/03/01/02 értéknek kell megjelennie. 

Ezután cserélje le a **2016** -et **{Year}**, **03** és { **month}**, **01** és **{Day}**, valamint **02** és **{Hour}** értékűre, majd nyomja le a **Tab** billentyűt. A következő négy változó formátumának kiválasztásához le kell látnia a legördülő listát:

![Fájl vagy mappa szűrése](./media/copy-data-tool/filter-file-or-folder.png)

A Adatok másolása eszköz olyan kifejezésekkel, függvényekkel és rendszerváltozókkal rendelkező paramétereket hoz létre, amelyek az {Year}, a {month}, a {Day}, a {Hour} és a {Minutes} ábrázolására használhatók a folyamat létrehozásakor.

## <a name="scheduling-options"></a>Ütemezési beállítások
A másolási műveletet egyszer vagy időpontban is futtathatja (óránként, naponta stb.). Ezek a lehetőségek különböző környezetekben, például a helyszíni, a Felhőbeli és a helyi asztali összekötők számára is használhatók. 

Az egyszeri másolási művelettel csak egyszer engedélyezhető az adatáthelyezés a forrásból a célhelyre. Bármilyen méretű és bármilyen támogatott formátumú adatmennyiségre vonatkozik. Az ütemezett másolás lehetővé teszi az Adatmásolást a megadott ismétlődési időpontra. Az ütemezett másolás konfigurálásához használhat gazdag beállításokat (például újrapróbálkozás, időtúllépés és riasztások).

![Ütemezési beállítások](./media/copy-data-tool/scheduling-options.png)


## <a name="next-steps"></a>Következő lépések
Próbálja ki ezeket az oktatóanyagokat, amelyek az Adatok másolása eszközt használják:

- [Gyors útmutató: adatelőállító létrehozása a Adatok másolása eszköz használatával](quickstart-create-data-factory-copy-data-tool.md)
- [Oktatóanyag: az Azure-beli Adatmásolás az Adatok másolása eszköz használatával](tutorial-copy-data-tool.md) 
- [Oktatóanyag: helyszíni adatai másolása az Azure-ba az Adatok másolása eszköz használatával](tutorial-hybrid-copy-data-tool.md)
