---
title: Hibás sorok kezelése a Azure Data Factory leképezési adatforgalmával
description: Megtudhatja, hogyan kezelheti az SQL-csonkolt hibákat Azure Data Factory a leképezési adatfolyamatok használatával.
author: kromerm
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/22/2020
ms.author: makromer
ms.openlocfilehash: a7a03ff1a58f50f16ebefce48b9e2772a16a011a
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100386339"
---
# <a name="handle-sql-truncation-error-rows-in-data-factory-mapping-data-flows"></a>SQL-csonkolt hibák sorainak kezelése Data Factory leképezési adatforgalomban

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A leképezési adatfolyamatok használatakor Data Factory gyakori forgatókönyv, hogy az átalakított adatait Azure SQL Database-adatbázisba írja. Ebben a forgatókönyvben egy gyakori hiba feltételt kell megakadályozni, hogy az oszlop csonkítható legyen.

Az adatbázis-fogadóba az ADF-adatfolyamatok során két elsődleges módszer található a hibák kezelésére

* Az adatbázis adatainak feldolgozásakor a "Folytatás hiba esetén" beállításnál állítsa be a fogadói [hiba sorainak kezelését](./connector-azure-sql-database.md#error-row-handling) . Ez egy automatizált catch-all metódus, amely nem igényel egyéni logikát az adatfolyamban.
* Azt is megteheti, hogy az alábbi lépéseket követve olyan oszlopok naplózását adja meg, amelyek nem illeszkednek a cél sztring oszlopba, így az adatfolyam továbbra is folytatódhat.

> [!NOTE]
> Ha engedélyezi az automatikus hibajelentések kezelését, a saját hibakezelés logikájának írásakor az alábbi módszernek megfelelően a rendszer kis teljesítményű szankciót használ, és az ADF által végrehajtott további lépéseket végrehajtva kétfázisú műveletet hajt végre a hibák alátöltése érdekében.

## <a name="scenario"></a>Eset

1. Egy ```nvarchar(5)``` "Name" nevű oszlopot tartalmazó céladatbázis-táblánk van.

2. Az adatfolyamon belül a fogadótól a "Name" oszlophoz tartozó film-címeket szeretnénk leképezni.

    ![1. filmes adatfolyam](media/data-flow/error4.png)
    
3. A probléma az, hogy a film címe nem fér el egy fogadó oszlopon belül, amely csak 5 karaktert tartalmazhat. Ha végrehajtja ezt az adatfolyamatot, a következőhöz hasonló hibaüzenet jelenik meg: ```"Job failed due to reason: DF-SYS-01 at Sink 'WriteToDatabase': java.sql.BatchUpdateException: String or binary data would be truncated. java.sql.BatchUpdateException: String or binary data would be truncated."```

Ez a videó végigvezeti egy, az adatfolyamatban a hibák sorát kezelő logikát bemutató példán:
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4uOHj]

## <a name="how-to-design-around-this-condition"></a>Az állapot megtervezése

1. Ebben az esetben a "Name" oszlop maximális hossza öt karakter. Tegyük fel, hogy hozzáadunk egy feltételes felosztású átalakítást, amely lehetővé teszi számunkra, hogy az öt karakternél hosszabb sorokba írja a sorokat, miközben a többi olyan sort is lehetővé teszi, amely elfér az adott helyen az adatbázisba való íráshoz.

    ![feltételes felosztás](media/data-flow/error1.png)

2. Ez a feltételes felosztású átalakítás a "title" maximális hosszát határozza meg öt értékre. Minden olyan sor, amely kisebb vagy egyenlő, mint öt, az ```GoodRows``` adatfolyamba kerül. Minden olyan sor, amely ötnél nagyobb, az ```BadRows``` adatfolyamba kerül.

3. Most be kell jelentkeznie a sikertelen sorokat. Adjon hozzá egy fogadó átalakítást az ```BadRows``` adatfolyamhoz a naplózáshoz. Itt "automatikusan leképezjük" az összes mezőt, így a teljes tranzakciós rekord naplózása is megtörtént. Ez egy, a CSV-fájlnak a Blob Storageban lévő egyetlen fájlba való kimenete. Meghívjuk a "badrows.csv" naplófájlt.

    ![Helytelen sorok](media/data-flow/error3.png)
    
4. Az elkészült adatfolyam az alábbi ábrán látható. Most már eloszthatjuk a hibák sorait, hogy elkerülje az SQL-csonkítás hibáit, és a bejegyzéseket egy naplófájlba helyezi. Eközben a sikeres sorok továbbra is írhatnak a célként megadott adatbázisba.

    ![teljes adatforgalom](media/data-flow/error2.png)

5. Ha a hibás sorok kezelésére szolgáló beállítást választja a fogadó átalakításban, és beállítja a "kimeneti hibák sorait", az ADF automatikusan létrehoz egy CSV-fájl kimenetét a sorból, valamint az illesztőprogram által jelentett hibaüzeneteket. Ezzel az alternatív lehetőséggel nem kell manuálisan hozzáadnia ezt a logikát az adatfolyamathoz. Ezzel a lehetőséggel kis teljesítményű szankció keletkezik, így az ADF kétfázisú módszert alkalmazhat a hibák összevonására és a naplózásra.

    ![az adatforgalom befejezése a hibák soraival](media/data-flow/error-row-3.png)

## <a name="next-steps"></a>Következő lépések

* Hozza létre a többi adatáramlási logikát az adatforgalom- [átalakítások](concepts-data-flow-overview.md)leképezése használatával.