---
title: Data Factory Azure Copy varázsló
description: Ismerje meg, hogyan másolhatók az adatok a támogatott adatforrásokból a mosdóba a Data Factory Azure Copy varázslóval.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/26/2020
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 0f95b0d62bc81a8dddc72239491a05ca78945490
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100393377"
---
# <a name="azure-data-factory-copy-wizard"></a>Azure Data Factory másolási varázsló

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. 

A Azure Data Factory másolási varázsló megkönnyíti az adatbevitel folyamatát, ami általában egy teljes körű adatintegrációs forgatókönyv első lépése. A Azure Data Factory másolási varázsló futtatásakor nem kell megismernie a társított szolgáltatások, adatkészletek és folyamatok JSON-definícióit. A varázsló automatikusan létrehoz egy folyamatot, amely a kijelölt adatforrásból a kiválasztott célhelyre másolt adatok másolását végzi. Emellett a másolás varázsló segítségével ellenőrizheti a szerzői műveletek során betöltött adatok érvényességét. Ezzel időt takaríthat meg, különösen akkor, ha az adatforrásból első alkalommal végez adatgyűjtést. A másolás varázsló elindításához kattintson az adattároló kezdőlapján található **Adatmásolási** csempe elemre.

![Másolás varázsló](./media/data-factory-copy-wizard/copy-data-wizard.png)

## <a name="designed-for-big-data"></a>big datahoz tervezve
Ez a varázsló lehetővé teszi, hogy a különböző forrásokból származó adatok könnyedén áthelyezhetők legyenek a célhelyekre percek alatt. Miután elvégezte a varázslót, a másolási tevékenységgel rendelkező folyamat automatikusan létrejön az Ön számára, valamint a függő Data Factory entitások (társított szolgáltatások és adatkészletek). A folyamat létrehozásához nincs szükség további lépésekre.   

![Adatforrás kiválasztása](./media/data-factory-copy-wizard/select-data-source-page.png)

> [!NOTE]
> Az Azure-blobokból egy Azure SQL Database táblába másolt folyamat létrehozásával kapcsolatos részletes utasításokért tekintse meg a [másolási varázsló oktatóanyagát](data-factory-copy-data-wizard-tutorial.md).

A varázsló a kezdettől kezdve big dataokkal van kialakítva, és támogatja a különböző adatok és objektumtípusok támogatását. Létrehozhat olyan Data Factory folyamatokat, amelyek több száz mappát, fájlt vagy táblát helyeznek át. A varázsló támogatja az automatikus adatelőnézett, a séma rögzítését és leképezését, valamint az adatszűrést.

## <a name="automatic-data-preview"></a>Automatikus adatelőnézet
Az adatok egy részét előzetesen megtekintheti a kiválasztott adatforrásból annak ellenőrzéséhez, hogy az adatok másolatát kívánja-e másolni. Emellett, ha a forrásadatok szövegfájlban találhatóak, a másolás varázsló elemzi a szövegfájlt, hogy a sor-és oszlop-határolójeleket és a sémát automatikusan elsajátítsa.

![A Fájlformátum beállításai](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>Séma rögzítése és leképezése
Előfordulhat, hogy a bemeneti adatok sémája nem egyezik meg bizonyos esetekben a kimeneti adatok sémájával. Ebben a forgatókönyvben a forrás sémából származó oszlopokat kell leképeznie a célként megadott sémából származó oszlopokra.

> [!TIP]
> Ha az adatok másolása SQL Server vagy Azure SQL Database az Azure szinapszis Analytics szolgáltatásba történik, ha a tábla nem létezik a célhelyen, Data Factory támogatja az automatikus tábla létrehozását a forrás sémájának használatával. További információ: [adatok áthelyezése az Azure szinapszis Analytics szolgáltatásba és onnan az Azure Data Factory használatával](./data-factory-azure-sql-data-warehouse-connector.md).

Egy legördülő lista használatával kiválaszthat egy oszlopot a forrásoldali sémából a célként megadott sémában lévő oszlopra való leképezéshez. A másolási varázsló megpróbálja megérteni az oszlopok megfeleltetésének mintáját. Ugyanezt a mintát alkalmazza a többi oszlopra is, így nem kell egyenként kiválasztania az egyes oszlopokat a séma-hozzárendelés befejezéséhez. Ha szeretné, felülbírálhatja ezeket a leképezéseket a legördülő lista segítségével, hogy egyenként képezze le az oszlopokat. A minta pontosabb lesz, ha további oszlopokat szeretne leképezni. A másolás varázsló folyamatosan frissíti a mintát, és végül eléri a megfelelő mintát az elérni kívánt oszlop-hozzárendeléshez.     

![Séma-hozzárendelés](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>Az adatok szűrése
A forrásadatok szűrésével kiválaszthatja azokat az adatforrásokat, amelyeket át kell másolni a fogadó adattárba. A szűrés csökkenti a fogadó adattárba másolandó adatok mennyiségét, így fokozza a másolási művelet átviteli sebességét. Rugalmas módszert biztosít a kapcsolódó adatbázisokban lévő adatok szűrésére az SQL lekérdezési nyelv vagy az Azure Blob mappában található fájlok használatával [Data Factory függvények és változók](data-factory-functions-variables.md)használatával.   

### <a name="filtering-of-data-in-a-database"></a>Adatbázisbeli Adatszűrés
Az alábbi képernyőfelvételen egy SQL-lekérdezés látható a `Text.Format` függvény és a `WindowStart` változó használatával.

![Kifejezések ellenőrzése](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Az Azure Blob mappában található Adatszűrés
A mappa elérési útja változóit használhatja a [rendszerváltozók](data-factory-functions-variables.md#data-factory-system-variables)alapján a futtatókörnyezetben meghatározott mappákból származó adatok másolásához. A támogatott változók a következők: **{Year}**, **{month}**, **{Day}**, **{Hour}**, **{minute**} és **{Custom}**. Például: inputfolder/{Year}/{month}/{Day}.

Tegyük fel, hogy a bemeneti mappák a következő formátumban vannak:

```text
2016/03/01/01
2016/03/01/02
2016/03/01/03
...
```

Kattintson a **fájl vagy mappa** **Tallózás** gombjára, keresse meg az egyik mappát (például 2016->03->01->02), és kattintson a **választás** gombra. Ekkor a szövegmezőben kell megjelennie `2016/03/01/02` . Most cserélje le a **2016** -et a { **Year}**, a **03** és a **{month}**, a **01** és a **{Day}**, valamint a **02** és az **{Hour}** kifejezésre, és nyomja meg a **Tab** billentyűt. A következő négy változó formátumának kiválasztásához le kell látnia a legördülő listát:

![Rendszerváltozók használata](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

Ahogy az alábbi képernyőképen is látható, használhat **Egyéni** változót és bármely [támogatott formázó karakterláncot](/dotnet/standard/base-types/custom-date-and-time-format-strings)is. Az adott struktúrát tartalmazó mappa kiválasztásához először használja a **Tallózás** gombot. Ezután cserélje le az értéket **{Custom}** értékre, majd a **Tab** billentyű lenyomásával megtekintheti a szövegmezőt, ahol megadhatja a formázó karakterláncot.     

![Egyéni változó használata](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## <a name="scheduling-options"></a>Ütemezési beállítások
A másolási műveletet egyszer vagy időpontban is futtathatja (óránként, naponta stb.). Mindkét lehetőség használható a különböző környezetekben lévő összekötők szélességére, beleértve a helyszíni, a Felhőbeli és a helyi asztali másolást is.

Az egyszeri másolási művelettel csak egyszer engedélyezhető az adatáthelyezés a forrásból a célhelyre. Bármilyen méretű és bármilyen támogatott formátumú adatmennyiségre vonatkozik. Az ütemezett másolás lehetővé teszi az Adatmásolást az előírt ismétlődéssel. Az ütemezett másolás konfigurálásához használhat gazdag beállításokat (például újrapróbálkozás, időtúllépés és riasztások).

![Ütemezési tulajdonságok](./media/data-factory-copy-wizard/scheduling-properties.png)

## <a name="troubleshooting"></a>Hibaelhárítás

Ez a szakasz a másolás varázsló gyakori hibaelhárítási módszereit vizsgálja Azure Data Factory.

> [!NOTE] 
> Ezek a hibaelhárítási tippek a másolás varázslóra vonatkoznak a Data Factory 1. verziójában. Data Factory v2 esetén tekintse meg a hibaelhárítási útmutatót a [Azure Data Factory](../data-factory-ux-troubleshoot-guide.md)hibaelhárítása című témakörben.

### <a name="error-code-unable-to-validate-in-copy-wizard"></a>Hibakód: nem lehet érvényesíteni a másolási varázslóban

- **Tünetek**: a másolás varázsló első lépésében a "nem sikerült érvényesíteni" figyelmeztető üzenet jelenik meg.
- **Okok**: Ez akkor fordulhat elő, ha a harmadik féltől származó cookie-k le vannak tiltva.
- **Megoldás**: 
    - Az Internet Explorer vagy a Microsoft Edge böngésző használata.
    - Ha Chrome böngészőt használ, kövesse az alábbi utasításokat a cookie-k hozzáadásához a *microsoftonline.com* és a *Windows.net*.
        1.  Nyissa meg a Chrome böngészőt.
        2.  Kattintson a csavarkulcsra vagy három sorra a jobb oldalon (a Google Chrome testreszabása és szabályozása).
        3.  Kattintson a **Beállítások** elemre.
        4.  Keresse meg a **cookie-kat** , vagy az **Adatvédelem** területen válassza a speciális beállítások lehetőséget.
        5.  Válassza a **tartalom beállításai** lehetőséget.    
        6.  A cookie-kat úgy kell beállítani, hogy engedélyezzék a helyi adatértékek **beállítását (ajánlott)**.
        7.  Kattintson a **kivételek kezelése** lehetőségre. Az **állomásnév minta** területen adja meg a következőt, és győződjön meg arról, hogy az **Engedélyezés** a viselkedési készlet.
            - login.microsoftonline.com
            - login.windows.net
        8.  Zárjuk be a böngészőt, és indítsa újra a programot.
    - Ha Firefox böngészőt használ, a cookie-k hozzáadásához kövesse az alábbi utasításokat.
        1. A Firefox menüben válassza az **eszközök**  >  **Beállítások** menüpontot.
        2. Az **adatvédelmi**  >  **Előzmények** területen láthatja, hogy az aktuális beállítás az **Előzmények egyéni beállításait használja**.
        3. A **harmadik féltől származó cookie-k elfogadásakor** előfordulhat, hogy a jelenlegi beállítás **soha nem** fog megjelenni, majd a jobb oldalon található **kivételek** lehetőségre kattintva adja hozzá a következő helyeket.
            - https://login.microsoftonline.com
            - https://login.windows.net
        4.  Zárjuk be a böngészőt, és indítsa újra a programot. 


### <a name="error-code-unable-to-open-login-page-and-enter-password"></a>Hibakód: nem lehet megnyitni a bejelentkezési oldalt, és meg kell adni a jelszót

- **Tünetek**: a másolási varázsló átirányítja a bejelentkezési oldalra, de a bejelentkezési oldal nem jelenik meg sikeresen.
- **Okok**: Ez a probléma akkor fordulhat elő, ha a hálózati környezetet az Office-hálózatról a otthoni hálózatra módosította. Vannak gyorsítótárak a böngészőkben. 
- **Megoldás**: 
    1.  Zárjuk be a böngészőt, és próbálkozzon újra. Ha a probléma továbbra is fennáll, folytassa a következő lépéssel.   
    2.  Ha Internet Explorer böngészőt használ, próbálja meg privát módban megnyitni (nyomja le a CTRL + SHIFT + "P" billentyűkombinációt). Ha Chrome böngészőt használ, próbálja megnyitni az inkognitóban módban (nyomja meg a CTRL + SHIFT + "N" billentyűkombinációt). Ha a probléma továbbra is fennáll, folytassa a következő lépéssel. 
    3.  Próbáljon másik böngészőt használni. 


## <a name="next-steps"></a>Következő lépések
A másolási tevékenységgel rendelkező folyamat létrehozásához a Data Factory másolási varázslóval című cikkből megtudhatja, hogyan hozhat létre [folyamatokat a másolás varázsló](data-factory-copy-data-wizard-tutorial.md)segítségével.