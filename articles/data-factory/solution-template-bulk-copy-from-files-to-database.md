---
title: Tömeges másolás fájlokból adatbázisba
description: Megtudhatja, hogyan használhat megoldási sablonnal az adatok tömeges másolását Azure Data Lake Storage Gen2ról az Azure szinapszis Analytics/Azure SQL Databaseba.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 12/09/2020
ms.openlocfilehash: 5f68c7b33a26bc8c01f3f413943351ff8d61b380
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100376121"
---
# <a name="bulk-copy-from-files-to-database"></a>Tömeges másolás fájlokból adatbázisba

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ez a cikk egy megoldási sablont ismertet, amellyel az adatok tömegesen másolhatók az Azure szinapszis Analytics/Azure SQL Databaseba Azure Data Lake Storage Gen2.

## <a name="about-this-solution-template"></a>Tudnivalók a megoldási sablonról

Ez a sablon Azure Data Lake Storage Gen2 forrásból kérdez le fájlokat. Ezután megismétli a forrás minden fájlját, és átmásolja a fájlt a célhely adattárba. 

Ez a sablon jelenleg csak az adatok **DelimitedText** formátumban való másolását támogatja. Más adatformátumokban lévő fájlok is beolvashatók a forrás adattárból, de nem másolhatók a célhely adattárára.  

A sablon három tevékenységet tartalmaz:
- A **metaadatok beolvasása** tevékenység lekéri a fájlokat a Azure Data Lake Storage Gen2ból, és átadja őket a következő *foreach* tevékenységnek.
- A **foreach** tevékenység lekéri a *metaadatok beolvasása* tevékenység fájljait, és megismétli az egyes fájlokat a *másolási* tevékenységnek.
- A **másolási** tevékenység a *foreach* tevékenységben található, hogy az egyes fájlokat a forrás adattárból a cél adattárba másolja.

A sablon a következő két paramétert definiálja:
- A *SourceContainer* az a legfelső szintű tároló elérési útja, ahová a rendszer átmásolja az adatait a Azure Data Lake Storage Gen2. 
- A *SourceDirectory* a gyökér tárolóban lévő könyvtár elérési útja, ahol az adatok a Azure Data Lake Storage Gen2ból másolódnak.

## <a name="how-to-use-this-solution-template"></a>A megoldás sablonjának használata

1. Nyissa meg a **fájlok tömeges másolását az adatbázis** sablonba. Hozzon létre egy **új** kapcsolódást a forrás Gen2-tárolóhoz. Ügyeljen arra, hogy a "GetMetadataDataset" és a "SourceDataset" a forrásfájl-tároló ugyanazon kapcsolódására hivatkozik.

    ![Új kapcsolódás létrehozása a forrás-adattárhoz](media/solution-template-bulk-copy-from-files-to-database/source-connection.png)

2. Hozzon létre egy **új** kapcsolódást ahhoz a fogadó adattárhoz, amelybe az adatok másolása folyamatban van.

    ![Új kapcsolódás létrehozása a fogadó adattárához](media/solution-template-bulk-copy-from-files-to-database/destination-connection.png)
    
3. Válassza **a sablon használata** lehetőséget.

    ![Sablon használata](media/solution-template-bulk-copy-from-files-to-database/use-template.png)
    
4. A folyamat a következő példában látható módon jön létre:

    ![A folyamat áttekintése](media/solution-template-bulk-copy-from-files-to-database/new-pipeline.png)

    > [!NOTE]
    > Ha úgy döntött, hogy az **Azure szinapszis Analytics** a fenti **2. lépésben** megadott adat célhelyként van kiválasztva, meg kell adnia egy kapcsolódást az Azure Blob Storage-hoz az átmeneti tároláshoz, az Azure szinapszis Analytics alapszintű verziójának megfelelően. A következő képernyőfelvételen látható, hogy a sablon automatikusan létrehozza a blob Storage *tárolási útvonalát* . Ellenőrizze, hogy létrejött-e a tároló a folyamat futtatása után.
        
    ![Alapszintű beállítás](media/solution-template-bulk-copy-from-files-to-database/staging-account.png)

5. Válassza a **hibakeresés** lehetőséget, adja meg a **paramétereket**, majd kattintson a **Befejezés gombra**.

    ![Kattintson * * hibakeresés * *](media/solution-template-bulk-copy-from-files-to-database/debug-run.png)

6. A folyamat futásának sikeres befejeződése után az alábbi példához hasonló eredményeket láthat:

    ![Az eredmény áttekintése](media/solution-template-bulk-copy-from-files-to-database/run-succeeded.png)

       
## <a name="next-steps"></a>Következő lépések

- [Az Azure Data Factory bemutatása](introduction.md)