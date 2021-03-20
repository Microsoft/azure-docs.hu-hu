---
title: Modell átalakítása
description: Leírja a modell átalakításának folyamatát
author: jakrams
ms.author: jakras
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: e899b249261ea3238695a2e2be6001cb6a9bc763
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91318058"
---
# <a name="convert-models"></a>Modellek konvertálása

Az Azure Remote rendering lehetővé teszi a nagyon összetett modellek megjelenítését. A maximális teljesítmény eléréséhez az adatfeldolgozást úgy kell megadni, hogy optimális formátumban legyenek. Az adatmennyiségtől függően ez a lépés hosszabb időt is igénybe vehet. A modell betöltése során ez az idő nem lenne praktikus. Emellett a folyamat többször is megismételheti ezt a folyamatot több munkamenet esetében is. Ezen okok miatt az ARR szolgáltatás egy dedikált *konverziós szolgáltatást* biztosít, amelyet az idő előtt futtathat.
Az átalakítás után egy modell tölthető be egy Azure Storage-fiókból.

## <a name="supported-source-formats"></a>Támogatott források formátuma

Az átalakítási szolgáltatás a következő formátumokat támogatja:

- **FBX**  (2011-es verzió 2020)
- **GLTF** / **Borai** (2. x verzió)

Kisebb eltérések vannak a formátumok között az anyagi tulajdonságok átalakításával kapcsolatban, ahogy az a Chapter [anyagok leképezése a modell-formátumokhoz](../../reference/material-mapping.md)című részben szerepel.

## <a name="the-conversion-process"></a>Az átalakítási folyamat

1. [Két Azure Blob Storage-tároló előkészítése](blob-storage.md): egy a bemenethez, egy a kimenethez
1. Töltse fel a modellt a bemeneti tárolóba (opcionálisan egy alútvonalon belül)
1. Az átalakítási folyamat elindítása [a modell átalakításán keresztül REST API](conversion-rest-api.md)
1. Az átalakítási folyamat lekérdezése a szolgáltatással
1. Ha elkészült, töltsön be egy modellt
    - egy társított Storage-fiókból (lásd a "Storage-fiókok csatolása" lépéseket a [fiók létrehozása](../create-an-account.md#link-storage-accounts) a Storage-fiók összekapcsolásához című témakörben)
    - vagy *közös hozzáférési aláírás (SAS)* megadásával.

Minden modell-adat (bemenet és kimenet) a felhasználó által megadott Azure Blob Storage-tárolóban tárolódik. Az Azure-alapú távoli renderelés teljes körű irányítást biztosít az eszközök felügyeletéhez.

## <a name="pricing"></a>Díjszabás

Az átalakítás díjszabásával kapcsolatos információkért tekintse meg a [távoli renderelés díjszabását](https://azure.microsoft.com/pricing/details/remote-rendering) ismertető oldalt.


## <a name="conversion-parameters"></a>Átalakítási paraméterek

A különböző átalakítási lehetőségekért tekintse meg [ezt a fejezetet](configure-model-conversion.md).

## <a name="examples"></a>Példák

- Gyors útmutató [: modell átalakítása renderelésre](../../quickstarts/convert-model.md) – lépésenkénti bevezetés a modellek átalakítására.
- [Például](../../samples/powershell-example-scripts.md)a konverziós szolgáltatás használatát bemutató PowerShell-parancsfájlok a *parancsfájlok* mappa [ARR Samples adattárában](https://github.com/Azure/azure-remote-rendering) találhatók.

## <a name="next-steps"></a>Következő lépések

- [Modellek átalakítása az Azure Blob Storage használatával](blob-storage.md)
- [A modell átalakítási REST API](conversion-rest-api.md)
- [A modell átalakításának konfigurálása](configure-model-conversion.md)
- [Fájlok elrendezése átalakításhoz](layout-files-for-conversion.md)
- [Anyagleképzés a modellformátumokhoz](../../reference/material-mapping.md)
