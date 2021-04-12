---
title: 'Rövid útmutató: az adat tömeges betöltése dedikált SQL-készlettel'
description: A szinapszis Studio használatával tömegesen tölthet be az adataikat egy dedikált SQL-készletbe az Azure szinapszis Analytics szolgáltatásban.
services: synapse-analytics
author: gaursa
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: quickstart
ms.date: 12/11/2020
ms.author: gaursa
ms.reviewer: jrasnick
ms.openlocfilehash: be15a37a9a2965da36f7e8f884a0a3112106b9ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104586726"
---
# <a name="quickstart-bulk-loading-with-synapse-studio"></a>Gyors útmutató: tömeges betöltés a szinapszis Studióval

Az adatgyűjtés egyszerűen betöltődik a szinapszis Studióban található tömeges betöltés varázslóval. A szinapszis Studio az Azure szinapszis Analytics egyik funkciója. A tömeges betöltés varázsló végigvezeti egy T-SQL-parancsfájl létrehozásán a [másolási utasítással](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) , amely az adatok tömeges betöltését teszi ki egy dedikált SQL-készletbe. 

## <a name="entry-points-to-the-bulk-load-wizard"></a>Belépési pontok a tömeges betöltés varázslóba

Az adatok tömeges betöltéséhez kattintson a jobb gombbal az alábbi területre a szinapszis Studio: egy olyan fájl vagy mappa, amely a munkaterülethez csatolt Azure Storage-fiókból származik.

![Képernyőfelvétel: a jobb gombbal kattintva egy fájlra vagy mappára a Storage-fiókból.](./sql/media/bulk-load/bulk-load-entry-point-0.png)

## <a name="prerequisites"></a>Előfeltételek

- A varázsló létrehoz egy MÁSOLÁSi utasítást, amely Azure Active Directory (Azure AD) átmenő hitelesítést használ a hitelesítéshez. Az [Azure ad-felhasználónak hozzáféréssel](./sql-data-warehouse/quickstart-bulk-load-copy-tsql-examples.md#d-azure-active-directory-authentication) kell rendelkeznie a munkaterülethez legalább a Storage blob Adatközreműködői Azure-szerepkörrel a Azure Data Lake Storage Gen2 fiókhoz. 

- A [másolási utasítás használatához](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#permissions) és a tábla létrehozásához szükséges engedélyekkel kell rendelkeznie, ha új táblát hoz létre a betöltéshez.

- A Data Lake Storage Gen2 fiókhoz társított társított szolgáltatásnak hozzáféréssel kell rendelkeznie a betöltendő *fájlhoz vagy mappához* . Ha például a társított szolgáltatás hitelesítési mechanizmusa felügyelt identitás, a munkaterület felügyelt identitásának legalább Storage blob Adatolvasó engedéllyel kell rendelkeznie a Storage-fiókban.

- Ha egy virtuális hálózat engedélyezve van a munkaterületen, győződjön meg arról, hogy az Data Lake Storage Gen2 fiók társított szolgáltatásaihoz tartozó, a forrásadatok és a hibaüzenetek elérésére szolgáló integrált futtatókörnyezetben engedélyezve van az interaktív létrehozás. Interaktív szerzői műveletek szükségesek az autoschema-észleléshez, a forrásfájl tartalmának megtekintéséhez és a varázslón belüli Data Lake Storage Gen2 Storage-fiókok tallózásához.

## <a name="steps"></a>Lépések

1. A **forrás tárolási hely** panelen válassza ki a Storage-fiókot, valamint azt a fájlt vagy mappát, amelyből be van töltve. A varázsló automatikusan megpróbálja felderíteni a parketta-fájlokat és a tagolt szövegfájlokat (CSV), beleértve a forrás mezőinek a fájlból a megfelelő cél SQL-adattípusokra való leképezését. 

   ![A forrás helyének kiválasztását bemutató képernyőkép.](./sql/media/bulk-load/bulk-load-source-location.png)

2. Válassza ki a fájlformátum beállításait, beleértve a hibák beállításait, ha a tömeges betöltési folyamat során visszautasított sorok vannak. Az **előnézet adatok** lehetőség kiválasztásával megtekintheti, hogy a másolási utasítás hogyan elemezi a fájlt, hogy segítsen a fájlformátum beállításainak konfigurálásában. Válassza ki az **adatok előnézetét** minden alkalommal, amikor módosítja a fájlformátum beállítását, hogy megtudja, hogyan elemezze a fájlt a frissített beállítással.

   ![Az adatmegjelenítést bemutató képernyőkép.](./sql/media/bulk-load/bulk-load-file-format-settings-preview-data.png) 

   > [!NOTE]  
   >
   > - A tömeges betöltés varázsló nem támogatja az adatmegjelenítést több karakterből álló mező lezárókkal. Többkarakteres mező lezárójának megadásakor a varázsló egyetlen oszlopon belül tekinti meg az adatmegjelenítést. 
   > - Amikor kiválasztja az **oszlopok nevének** kiválasztását, a tömeges betöltés varázsló az **első** sor mezőben megadott első sorból elemzi az oszlopok nevét. A tömeges betöltés varázsló a `FIRSTROW` másolási utasításban szereplő értéket automatikusan eggyel növeli a fejlécsor figyelmen kívül hagyásával. 
   > - A több karakterből álló sorok lezáróinak megadása a COPY utasításban támogatott. A tömeges betöltés varázsló azonban nem támogatja, és hibát jelez.

3. Válassza ki a betölteni kívánt dedikált SQL-készletet, beleértve azt is, hogy a terhelés egy meglévő táblára vagy egy új táblára vonatkozik-e.
   ![A célhely kijelölését bemutató képernyőkép.](./sql/media/bulk-load/bulk-load-target-location.png)
4. Válassza az **oszlop-hozzárendelés konfigurálása** lehetőséget, hogy ellenőrizze, hogy rendelkezik-e a megfelelő oszlop-hozzárendeléssel. Megjegyzés: a rendszer automatikusan észleli az oszlopok neveit, ha engedélyezi az **oszlopnevek Kikövetkeztető oszlopának nevét**. Az új táblák esetében az oszlop-hozzárendelés konfigurálása kritikus fontosságú a cél oszlop adattípusának frissítéséhez.

   ![Az oszlop-hozzárendelés konfigurálását bemutató képernyőkép.](./sql/media/bulk-load/bulk-load-target-location-column-mapping.png)
5. Válassza a **parancsfájl megnyitása** lehetőséget. A rendszer egy T-SQL-szkriptet hoz létre a MÁSOLÁSi utasítással, amely betöltődik a adattóban.
   ![Képernyőkép, amely az SQL-szkript megnyitását mutatja be.](./sql/media/bulk-load/bulk-load-target-final-script.png)

## <a name="next-steps"></a>Következő lépések

- A MÁSOLÁSi lehetőségekkel kapcsolatos további információkért olvassa el a [copy utasításról](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#syntax) szóló cikket.
- A kinyerési, átalakítási és betöltési (ETL) folyamat használatával kapcsolatos információkért tekintse meg az [adatok betöltésének áttekintése](./sql-data-warehouse/design-elt-data-loading.md#what-is-elt) című cikket.
