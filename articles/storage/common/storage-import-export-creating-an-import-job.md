---
title: Az Azure importálási és exportálási szolgáltatáshoz importálási feladat létrehozása |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre a Microsoft Azure Import/Export szolgáltatás importálását.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: fa76f4fb5d4da5fd00bb9fa4ed862c6977a47e90
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "61483077"
---
# <a name="creating-an-import-job-for-the-azure-importexport-service"></a>Az Azure Import/Export szolgáltatás importálási feladat létrehozása

A Microsoft Azure Import/Export szolgáltatás REST API használatával importálási feladat létrehozása az alábbi lépésekből áll:

- Felkészülés az Azure Import/Export eszközzel rendelkező meghajtókat.

- A hely, amelyhez a meghajtó szállításra való beszerzéséről.

- Az importálási feladat létrehozása.

- A szállítási a Microsoftnak a meghajtók támogatott szolgáltató szolgáltatáson keresztül.

- Az importálási feladat frissítése a a szállítás részleteiről.

  Lásd: [adatok átvitele a Blob Storage a Microsoft Azure Import/Export szolgáltatás használata](storage-import-export-service.md) áttekintését, az Import/Export szolgáltatás és a egy oktatóanyag, amely azt ismerteti, hogyan használható a [az Azure portal](https://portal.azure.com/) hozhat létre és importálási kezelése és a feladatok exportálása.

## <a name="preparing-drives-with-the-azure-importexport-tool"></a>Az Azure Import/Export eszközzel együtt meghajtók előkészítése

A meghajtók előkészítése importálási feladatokhoz lépések megegyeznek-e a feladatot a portálon keresztül vagy a REST API használatával hoz létre.

Az alábbi, a meghajtó előkészítése rövid áttekintése. Tekintse meg a [Azure Import-ExportTool referencia](storage-import-export-tool-how-to-v1.md) részletes utasításokért. Az Azure Import/Export eszközt letöltheti [Itt](https://go.microsoft.com/fwlink/?LinkID=301900).

A meghajtó előkészítése foglalja magában:

- Az importálandó adatok azonosítása.

- A cél a Windows Azure Storage blobok azonosítása.

- Az Azure Import/Export eszköz használata az adatok másolása egy vagy több merevlemez-meghajtókat.

  Az Azure Import/Export eszköz is generál a jegyzékfájlt a meghajtókhoz, az előkészítés során. A jegyzékfájlt tartalmaz:

- Egy enumerálás feltöltési és a blobokhoz az érintett fájlok a leképezések az összes fájlt.

- Az egyes fájlok szegmenseinek ellenőrzőösszegek.

- A metaadat- és tulajdonságfájljainak minden egyes blob társítandó vonatkozó adatokat.

- Ha egy blob feltöltése folyamatban van, a neve megegyezik egy meglévő blobot a tárolóban elvégzendő listája. A lehetséges értékek: a) a blob felülírja a fájlt a, b) tartani a meglévő blobra, majd feltölti a skip, c) hozzáfűzése egy utótagot a nevét, hogy más fájlok nem ütköznek.

## <a name="obtaining-your-shipping-location"></a>A szállítási címhez tartozó hely beszerzése

Importálási feladat létrehozása előtt be kell szereznie egy szállítási hely nevét és címét meghívásával a [lista helyek](https://docs.microsoft.com/rest/api/storageimportexport/locations/list) műveletet. `List Locations` helyek és a levelezési címét listáját adja vissza. Válasszon egy helyet a visszaadott listában, és arra a címre rögzített meghajtók kiszállítása. Is használhatja a `Get Location` művelet egy konkrét hely a szállítási cím közvetlenül beszerzése.

 A szállítási címhez tartozó hely beszerzése az alábbi lépésekkel:

-   Azonosítsa a helyet a tárfiók nevére. Ez az érték alatt található a **hely** a storage-fiókban található **irányítópult** az Azure portal vagy a service management API művelet használatával lekérdezett [Storage-fiók beszerzése Tulajdonságok](/rest/api/storagerp/storageaccounts).

-   A hely, amelyhez ez a tárfiók feldolgozni meghívásával lekérése a `Get Location` műveletet.

-   Ha a `AlternateLocations` helye tulajdonsága tartalmaz magát a helyet, akkor már használja ezt a helyet. Ellenkező esetben hívja a `Get Location` a másodlagos helyek újra a műveletet. Az eredeti helyre ideiglenesen megtakarítása karbantartás céljából.

## <a name="creating-the-import-job"></a>Az importálási feladat létrehozása
Az importálási feladat létrehozásához hívja a [Put feladat](/rest/api/storageimportexport/jobs) műveletet. Adja meg a következő információkat kell:

-   A feladat nevét.

-   A tárfiók neve.

-   A szállítási hely neve, az előző lépésben beszerzett.

-   Feladat típusa (importálás).

-   A válaszcím, ahol a meghajtók kell küldeni az importálási feladat befejezése után.

-   A feladat meghajtók listája. Minden meghajtó meghajtó előkészítési lépés során kapott a következőket kell tartalmaznia:

    -   A meghajtó azonosítója

    -   A BitLocker-kulcs

    -   Az Alkalmazásjegyzék-fájl relatív elérési út a merevlemezen

    -   A Base16 kódolású Alkalmazásjegyzék-fájl MD5-kivonat

## <a name="shipping-your-drives"></a>A szállítási meghajtó
Meg kell meghajtók kiszállítása a a címet az előző lépésben beszerzett, és az Import/Export szolgáltatás meg kell adnia a csomag a nyomkövetési azonosító szám.

> [!NOTE]
>  Meg kell egy támogatott szolgáltató szolgáltatással, amely a csomag biztosít egy nyomkövetési azonosító szám meghajtók kiszállítása.

## <a name="updating-the-import-job-with-your-shipping-information"></a>A szállítási adatait az importálási feladat frissítése
Miután a nyomkövetési azonosító szám, hívja a [feladat tulajdonságainak frissítése](https://docs.microsoft.com/rest/api/storageimportexport/Jobs/Update) szállítási szállító nevét, a feladat a nyomkövetési azonosító szám és a Szállítmányozó számlaszáma a visszaszállítási frissítésére szolgáló művelet. Opcionálisan megadhatja a meghajtókat és a szállítási címhez tartozó dátumot is a számot.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]

## <a name="next-steps"></a>További lépések

* [Az Import/Export szolgáltatás REST API használatával](storage-import-export-using-the-rest-api.md)
