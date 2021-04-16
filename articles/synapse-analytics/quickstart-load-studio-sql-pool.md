---
title: 'Rövid útmutató: Adatok tömeges betöltése dedikált SQL-készlet használatával'
description: Adatok Synapse Studio adatok egy dedikált SQL-készletbe való tömeges betöltéséhez a Azure Synapse Analytics.
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: quickstart
ms.date: 12/11/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 838138fb6ca6f64b4296b54a81bc2764c3f1399c
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567910"
---
# <a name="quickstart-bulk-loading-with-synapse-studio"></a>Rövid útmutató: Tömeges betöltés Synapse Studio

Az adatok betöltése a Tömeges betöltés varázslóval egyszerűen Synapse Studio. Synapse Studio az egyik Azure Synapse Analytics. A Tömeges betöltés varázsló végigvezeti egy T-SQL-szkript [COPY](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) utasítással történő létrehozásának folyamatán, hogy adatokat töltsön be egy dedikált SQL-készletbe. 

## <a name="entry-points-to-the-bulk-load-wizard"></a>Belépési pontok a Tömeges betöltés varázslóhoz

Az adatok tömeges betöltéséhez kattintson a jobb gombbal az alábbi területre a Synapse Studio: egy fájlra vagy mappára a munkaterülethez csatolt Azure Storage-fiókból.

![Képernyőkép egy tárfiók egy fájljára vagy mappájára való jobb gombbal való kattintásról.](./sql/media/bulk-load/bulk-load-entry-point-0.png)

## <a name="prerequisites"></a>Előfeltételek

- A varázsló létrehoz egy COPY utasítást, amely Azure Active Directory (Azure AD) átmenő protokollt használ a hitelesítéshez. Az [Azure AD-felhasználónak hozzá](./sql-data-warehouse/quickstart-bulk-load-copy-tsql-examples.md#d-azure-active-directory-authentication) kell férnie a munkaterülethez, amely legalább a Storage-blobadatok közreműködője Azure-szerepkört használja a Azure Data Lake Storage Gen2 fiókhoz. 

- Ha új táblát hoz létre, amelybe betölt, rendelkeznie kell a [COPY](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#permissions) utasítás és a Tábla létrehozása engedélyekkel.

- A fiókhoz társított Data Lake Storage Gen2 *hozzáféréssel kell* rendelkezik a betöltni kívánt fájlhoz vagy mappához. Ha például a csatolt szolgáltatás hitelesítési mechanizmusa egy felügyelt identitás, a munkaterület felügyelt identitásának legalább Storage Blob-adatolvasó engedéllyel kell rendelkeznie a tárfiókon.

- Ha a munkaterületen engedélyezve van egy virtuális hálózat, győződjön meg arról, hogy a Data Lake Storage Gen2-fiók társított szolgáltatásaihoz társított integrált futásidőben engedélyezve van a forrásadatok és a hibafájl helye. Az automatikus vegyészlelés észleléséhez, a forrásfájl tartalmának előnézetéhez és a tárfiókok varázslón belüli böngészéséhez interaktív szerzői Data Lake Storage Gen2 szükséges.

## <a name="steps"></a>Lépések

1. A Forrás **tárolási hely panelen** válassza ki a tárfiókot és a fájlt vagy mappát, amelyből betölti. A varázsló automatikusan megpróbálja észlelni a Parquet-fájlokat és a tagolt szöveges (CSV-) fájlokat, beleértve a forrásmezők leképezését a fájlból a megfelelő cél SQL-adattípusra. 

   ![A forráshely kiválasztását bemutató képernyőkép.](./sql/media/bulk-load/bulk-load-source-location.png)

2. Válassza ki a fájlformátum beállításait, beleértve a hibabeállításokat arra az időszakra, amikor a rendszer elutasítja a sorokat a tömeges betöltési folyamat során. Az Adatok **előnézete lehetőséget** választva megtekintheti, hogyan elemezi a COPY utasítás a fájlt a fájlformátum beállításainak konfigurálása érdekében. Válassza **az Adatok előnézete** lehetőséget minden alkalommal, amikor módosít egy fájlformátum-beállítást, hogy lássa, hogyan fogja a COPY utasítás a frissített beállítással elemezni a fájlt.

   ![Az adatok előnézetét bemutató képernyőkép.](./sql/media/bulk-load/bulk-load-file-format-settings-preview-data.png) 

   > [!NOTE]  
   >
   > - A Tömeges betöltés varázsló nem támogatja az adatok több karakterből álló mezőválasztókkal való előnézetét. Több karakterből álló mező-lezáró megadásakor a varázsló egyetlen oszlopban fogja előnézetben megtekintheti az adatokat. 
   > - Ha a **Defer column names**(Oszlopnevek következtetése) lehetőséget választja, a Bulk Load (Tömeges betöltés) varázsló az Első sor mezőben megadott első sor **oszlopneveit fogja** elemezni. A Tömeges betöltés varázsló automatikusan növeli a COPY utasítás értékét 1-ével, hogy figyelmen kívül hagyja `FIRSTROW` ezt a fejlécsort. 
   > - A COPY utasítás támogatja a több karakterből álló sorválasztók megadását. A Tömeges betöltés varázsló azonban nem támogatja, és hibaüzenetet ad vissza.

3. Válassza ki a betölteni kívánt dedikált SQL-készletet, beleértve azt is, hogy a terhelés egy meglévő táblához vagy egy új táblához lesz-e beterve.
   ![A célhely kiválasztását bemutató képernyőkép.](./sql/media/bulk-load/bulk-load-target-location.png)
4. Válassza **az Oszlopleképezés konfigurálása** lehetőséget, hogy ellenőrizze, hogy a megfelelő oszlopleképezéssel van-e meg. Figyelje meg, hogy az oszlopok neve automatikusan észlelve lesz, ha engedélyezte **a Defer column names (Oszlopnevek következtetése) beállítást.** Új táblák esetén az oszlopleképezés konfigurálása kritikus fontosságú a céloszlop adattípusának frissítéséhez.

   ![Az oszlopleképezés konfigurálását bemutató képernyőkép.](./sql/media/bulk-load/bulk-load-target-location-column-mapping.png)
5. Válassza **a Szkript megnyitása lehetőséget.** Létrejön egy T-SQL-szkript a COPY utasítással, amely betöltődik a data lake-ről.
   ![Képernyőkép az SQL-szkript megnyitásáról.](./sql/media/bulk-load/bulk-load-target-final-script.png)

## <a name="next-steps"></a>Következő lépések

- A [COPY (MÁSOLÁS) képességekkel](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#syntax) kapcsolatos további információkért tekintse meg a COPY utasítással kapcsolatos cikket.
- A [kinyerési,](./sql-data-warehouse/design-elt-data-loading.md#what-is-elt) átalakítási és betöltési (ETL) folyamat használatával kapcsolatos információkért tekintse meg az adatok betöltését áttekintő cikket.
