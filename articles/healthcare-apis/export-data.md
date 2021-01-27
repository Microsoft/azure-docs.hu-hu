---
title: Az Exportálás végrehajtása $export parancs meghívásával a FHIR készült Azure API-ban
description: Ez a cikk bemutatja, hogyan exportálhatja a FHIR-információkat a $export használatával
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 1/21/2021
ms.author: cavoeg
ms.openlocfilehash: 8ad5ee78a525b3798bbf613168ff74a9e21fe99b
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920257"
---
# <a name="how-to-export-fhir-data"></a>FHIR-adatexportálás


A tömeges exportálás funkció lehetővé teszi az adatok exportálását a FHIR-kiszolgálóról a [FHIR-specifikáció](https://hl7.org/fhir/uv/bulkdata/export/index.html)alapján. 

$Export használata előtt meg kell győződnie arról, hogy a FHIR készült Azure API használatára van konfigurálva. Az exportálási beállítások konfigurálásához és az Azure Storage-fiók létrehozásához tekintse meg [Az adatexportálás konfigurálása lapot](configure-export-data.md).

## <a name="using-export-command"></a>$export parancs használata

Miután konfigurálta az Azure API-t a FHIR-hoz az exportáláshoz, a $export paranccsal exportálhatja a szolgáltatásból az adatkészletet. Az adattárolási szolgáltatás az Exportálás konfigurálásakor megadott Storage-fiókba kerül. Ha meg szeretné tudni, hogyan hívhat meg $export parancsot a FHIR-kiszolgálón, olvassa el a dokumentációt a [HL7 FHIR $export specifikációjában](https://hl7.org/Fhir/uv/bulkdata/export/index.html). 

A FHIR készült Azure API a következő szinteken támogatja a $export:
* [System](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---system-level-export): `GET https://<<FHIR service base URL>>/$export>>`
* [Beteg](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---all-patients): `GET https://<<FHIR service base URL>>/Patient/$export>>`
* [Betegek csoportja *](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---group-of-patients) – az Azure API for FHIR exportálja az összes kapcsolódó erőforrást, de nem exportálja a csoport jellemzőit: `GET https://<<FHIR service base URL>>/Group/[ID]/$export>>`



> [!Note] 
> `Patient/$export` és `Group/[ID]/$export` Előfordulhat, hogy duplikált erőforrásokat exportál, ha az erőforrás több erőforráshoz tartozó rekeszben található, vagy több csoportban van.

Emellett az exportálási állapot ellenőrzése a hely fejléce által visszaadott URL-címen, a tényleges exportálási feladat megszakítása mellett.

## <a name="settings-and-parameters"></a>Beállítások és paraméterek

### <a name="headers"></a>Fejlécek
Két kötelező fejléc-paraméternek kell megadnia $export feladatokhoz. Az értékeket az aktuális [$export-specifikáció](https://hl7.org/Fhir/uv/bulkdata/export/index.html#headers)határozza meg.
* **Accept** -Application/fhir + JSON
* **Előnyben részesített** válasz-aszinkron

### <a name="query-parameters"></a>Lekérdezési paraméterek
A FHIR készült Azure API a következő lekérdezési paramétereket támogatja. A paraméterek mindegyike opcionális:

|Lekérdezési paraméter        | A FHIR-specifikáció határozza meg?    |  Description|
|------------------------|---|------------|
| \_outputFormat | Yes | A jelenleg három értéket támogat a FHIR spec: Application/FHIR + ndjson, Application/ndjson vagy Just ndjsonhoz való igazításhoz. Minden exportálási feladat vissza fog térni `ndjson` , és az átadott érték nem befolyásolja a kód viselkedését. |
| \_mivel | Yes | Lehetővé teszi a csak a megadott idő óta módosított erőforrások exportálását |
| \_típusa | Yes | Lehetővé teszi annak megadását, hogy milyen típusú erőforrásokat fog tartalmazni. Írja be például, hogy \_ = beteg csak a beteg erőforrásait adja vissza|
| \_typefilter | Yes | Ha finomabb szűrést szeretne kérni, \_ a Type paraméterrel együtt használhatja a typefilter-t is \_ . A _typeFilter paraméter értéke az olyan FHIR lekérdezések vesszővel tagolt listája, amelyek tovább korlátozzák az eredményeket |
| \_tároló | No |  Meghatározza azt a tárolót a konfigurált Storage-fiókon belül, ahol az adatexportálást el kell helyezni. Ha meg van adva tároló, a rendszer az adott tárolóba exportálja a nevet egy új mappába. Ha a tároló nincs megadva, a rendszer egy új tárolóba exportálja az időbélyeg és a Job ID használatával. |


## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan exportálhatja a FHIR-erőforrásokat $export parancs használatával. Következő lépésként megtudhatja, hogyan exportálhatja a de azonosított információkat:
 
>[!div class="nextstepaction"]
>[Azonosított adatértékek exportálása](de-identified-export.md)
