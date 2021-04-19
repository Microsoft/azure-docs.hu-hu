---
title: Összekötővel és formázással kapcsolatos problémák elhárítása adatfolyamok leképezése során
description: Megtudhatja, hogyan háríthatja el az összekötővel és formátummal kapcsolatos adatfolyam-problémákat a Azure Data Factory.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 04/12/2021
ms.openlocfilehash: 7596bf3182f59758eeee66e248404e98c18da2e7
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107715471"
---
# <a name="troubleshoot-connector-and-format-issues-in-mapping-data-flows-in-azure-data-factory"></a>Összekötővel és formázással kapcsolatos problémák elhárítása adatfolyamok leképezése során a Azure Data Factory


Ez a cikk az összekötőkhöz és a leképezési adatfolyamok formátumával kapcsolatos hibaelhárítási módszereket vizsgálja meg az Azure Data Factory (ADF) szolgáltatásban.


## <a name="cosmos-db--json"></a>Cosmos DB & JSON

### <a name="support-customized-schemas-in-the-source"></a>Testreszabott sémák támogatása a forrásban

#### <a name="symptoms"></a>Hibajelenségek
Ha az ADF-adatfolyammal szeretne adatokat áthelyezni vagy áthelyezni Cosmos DB/JSON-fájlból más adattárakba, a forrásadatok egyes oszlopai kimaradhatnak. 

#### <a name="cause"></a>Ok 
A sémamentes összekötők esetében (az egyes sorok oszlopszáma, oszlopneve és oszlop adattípusa a többivel összehasonlítva eltérő lehet), az ADF alapértelmezés szerint mintasorokat használ (például az első 100 vagy 1000 sor adatait) a séma kikövetkeztetéséhez, és a kikövetkeztetett eredmény lesz az adatok beolvasható sémájaként. Így ha az adattárai olyan további oszlopokkal is vannak, amelyek nem jelennek meg a mintasorokban, akkor ezeknek a további oszlopoknak az adatait a rendszer nem olvassa be, nem továbbítja vagy továbbítja a fogadó adattárakba.

#### <a name="recommendation"></a>Ajánlás
Az alapértelmezett viselkedés felülírása és további mezők beírása esetén az ADF lehetőségeket biztosít a forrásséma testreszabására. További/hiányzó oszlopokat is megadhat, amelyek hiányozhatnak a séma-következtetési eredményben az adatfolyam forrás-leképezésében az adatok olvasása érdekében, és az alábbi lehetőségek egyikével állíthatja be a testreszabott sémát. Általában inkább **az 1.** lehetőséget részesíti előnyben.

- **1.** lehetőség: Az eredeti forrásadatokkal összehasonlítva, amelyek lehetnek egy nagy méretű fájl, tábla vagy tároló, amely több millió sort tartalmaz összetett sémákkal, létrehozhat egy ideiglenes táblát/tárolót néhány sorból, amely tartalmazza az összes olvasni kívánt oszlopot, majd továbbléphet a következő műveletre: 

    1. Az adatfolyam-forrás **Hibakeresési beállítások használatával** a teljes séma lekért importálási leképezés mintafájlokkal/táblákkal használható.  Kövesse az alábbi képen látható lépéseket:<br/>

        ![A javaslat 1–1. lehetőségét bemutató képernyőkép](./media/data-flow-troubleshoot-connector-format/customize-schema-option-1-1.png)<br/>
         1. Válassza **a Beállítások hibakeresése** lehetőséget az adatfolyam vásznán.
         1. Az előugró panelen válassza a **Mintatábla** lehetőséget a **cosmosSource lapon.** 
         1. Adja meg a tábla nevét a **Tábla blokkban.**
         1. A **beállítások mentéséhez** válassza a Mentés lehetőséget.
         1. Válassza a **Leképezés importálása lehetőséget.**<br/>  
    
    1. Módosítsa a **Hibakeresési beállításokat** úgy, hogy a forrásadatkészletet használja a fennmaradó adatátmozgatáshoz/-átalakításhoz. Továbbléphet a következő képen látható lépésekkel:<br/>

        ![A javaslat 1–2. lehetőségét bemutató képernyőkép](./media/data-flow-troubleshoot-connector-format/customize-schema-option-1-2.png) <br/>   
         1. Válassza **a Beállítások hibakeresése** lehetőséget az adatfolyam vásznán.
         1. Az előugró panelen válassza a **forrásadatkészlet** lehetőséget a **cosmosSource lapon.**
         1. A **beállítások mentéséhez** válassza a Mentés lehetőséget.<br/>
    
    Ezt követően az ADF adatfolyam-futásidejű példánya a testre szabott sémát fogja használni az eredeti adattár adatainak beolvassához. <br/>

- **2. lehetőség:** Ha ismeri a forrásadatok sémáját és DSL-nyelvét, manuálisan frissítheti az adatfolyam forrásszk szkriptet további/kihagyott oszlopok hozzáadásához az adatok olvasása érdekében. Az alábbi képen egy példa látható: 

    ![A javaslat 2. lehetőségét bemutató képernyőkép](./media/data-flow-troubleshoot-connector-format/customize-schema-option-2.png)

## <a name="next-steps"></a>Következő lépések
A hibaelhárítással kapcsolatos további segítségért tekintse meg az alábbi forrásokat:

*  [Adatfolyamok leképezésének hibaelhárítása a Azure Data Factory](data-flow-troubleshoot-guide.md)
*  [Data Factory blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory szolgáltatáskérések](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-videók](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Stack Overflow fórum a Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-információk a Data Factory](https://twitter.com/hashtag/DataFactory)