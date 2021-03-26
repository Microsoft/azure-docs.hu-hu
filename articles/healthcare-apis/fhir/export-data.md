---
title: Az Exportálás végrehajtása $export parancs meghívásával a FHIR készült Azure API-ban
description: Ez a cikk bemutatja, hogyan exportálhatja a FHIR-információkat a $export használatával
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 3/18/2021
ms.author: cavoeg
ms.openlocfilehash: a5b3daa499546f3a30b5a4d133d77786a1916b6a
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105559196"
---
# <a name="how-to-export-fhir-data"></a>FHIR-adatexportálás


A tömeges exportálás funkció lehetővé teszi az adatok exportálását a FHIR-kiszolgálóról a [FHIR-specifikáció](https://hl7.org/fhir/uv/bulkdata/export/index.html)alapján. 

$Export használata előtt győződjön meg arról, hogy a FHIR készült Azure API konfigurálva van a használatára. Az exportálási beállítások konfigurálásához és az Azure Storage-fiók létrehozásához tekintse meg [Az adatexportálás konfigurálása lapot](configure-export-data.md).

## <a name="using-export-command"></a>$export parancs használata

Miután konfigurálta az Azure API-t a FHIR-hoz az exportáláshoz, a $export paranccsal exportálhatja a szolgáltatásból az adatkészletet. Az adattárolási szolgáltatás az Exportálás konfigurálásakor megadott Storage-fiókba kerül. Ha meg szeretné tudni, hogyan hívhat meg $export parancsot a FHIR-kiszolgálón, olvassa el a dokumentációt a [HL7 FHIR $export specifikációjában](https://hl7.org/Fhir/uv/bulkdata/export/index.html).


**Hibás állapotba ragadt feladatok**

Bizonyos helyzetekben lehetséges, hogy a feladatok rossz állapotba vannak beragadva. Ez különösen akkor fordulhat elő, ha a Storage-fiók engedélyei nem megfelelően lettek beállítva. Az Exportálás sikeres ellenőrzésének egyik módja, ha ellenőrzi a Storage-fiókját, hogy látható-e a megfelelő tároló (azaz ndjson) fájl. Ha nincsenek jelen, és nem futnak más exportálási feladatok, akkor lehetséges, hogy az aktuális feladat rossz állapotban van. Az exportálási feladatot le kell mondania egy lemondási kérelem elküldésével, és újra kell próbálkoznia a feladat ismételt sorba állításával. A helytelen állapotú exportálás alapértelmezett futási ideje 10 perc, mielőtt leállítja, majd áthelyezi az új feladatokra, vagy próbálja megismételni az exportálást. 

A FHIR készült Azure API a következő szinteken támogatja a $export:
* [System](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---system-level-export): `GET https://<<FHIR service base URL>>/$export>>`
* [Beteg](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---all-patients): `GET https://<<FHIR service base URL>>/Patient/$export>>`
* [Betegek csoportja *](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---group-of-patients) – az Azure API for FHIR exportálja az összes kapcsolódó erőforrást, de nem exportálja a csoport jellemzőit: `GET https://<<FHIR service base URL>>/Group/[ID]/$export>>`

Az adatexportálás során minden erőforrástípus külön fájlt hoz létre. Annak biztosítása érdekében, hogy az exportált fájlok ne legyenek túl nagyok. Egy új fájlt hozunk létre, miután egy exportált fájl mérete nagyobb lesz, mint 64 MB. Ennek eredményeképpen több fájl is beolvasható minden olyan erőforrástípus esetében, amely enumerálásra kerül (azaz beteg-1. ndjson, beteg-2. ndjson). 


> [!Note] 
> `Patient/$export` és `Group/[ID]/$export` Előfordulhat, hogy duplikált erőforrásokat exportál, ha az erőforrás több erőforráshoz tartozó rekeszben található, vagy több csoportban van.

Emellett az exportálási állapot ellenőrzése a hely fejléce által visszaadott URL-címen, a tényleges exportálási feladat megszakítása mellett.

### <a name="exporting-fhir-data-to-adls-gen2"></a>FHIR-ADLS Gen2 exportálása

Jelenleg a következő korlátozásokkal támogatjuk a ADLS Gen2 engedélyezett Storage-fiókok $exportét:

- A felhasználó nem tudja kihasználni a [hierarchikus névtereket](../../storage/blobs/data-lake-storage-namespace.md), de nincs lehetőség arra, hogy a tárolón belül egy adott alkönyvtárba exportálja az exportálást. Csak egy adott tárolót célozunk meg (ahol minden egyes exportáláshoz új mappát hozunk létre).
- Az Exportálás befejezése után még soha nem exportálunk semmit erre a mappára, mert az ugyanazon tárolóba való későbbi exportálás egy újonnan létrehozott mappába kerül.


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

> [!Note]
> Csak a FHIR-hez készült Azure API-hoz tartozó Storage-fiókok regisztrálhatók a $export műveletek célhelye.

## <a name="secure-export-to-azure-storage"></a>Biztonságos Exportálás az Azure Storage-ba

A FHIR készült Azure API támogatja a biztonságos exportálási műveletet. Az egyik lehetőség, hogy biztonságos exportálást futtasson, lehetővé teszi, hogy az Azure API-hoz társított IP-címek FHIR az Azure Storage-fiók eléréséhez. A konfigurációk eltérőek, attól függően, hogy a Storage-fiók azonos vagy egy másik helyen található-e az Azure API-FHIR.

### <a name="when-the-azure-storage-account-is-in-a-different-region"></a>Ha az Azure Storage-fiók egy másik régióban található

Válassza ki az Azure Storage-fiók **hálózatkezelését** a portálon. 

   :::image type="content" source="media/export-data/storage-networking.png" alt-text="Az Azure Storage hálózatkezelési beállításai." lightbox="media/export-data/storage-networking.png":::
   
Válassza a **Kijelölt hálózatok** lehetőséget. A tűzfal szakaszban válassza ki az IP-címet a **címtartomány** mezőben. IP-címtartományok hozzáadásával engedélyezheti az internetről vagy a helyszíni hálózatokból való hozzáférést. Az alábbi táblázatban megtalálja az IP-címet ahhoz az Azure-régióhoz, ahol a FHIR szolgáltatáshoz tartozó Azure API van kiépítve.

|**Azure-régió**         |**Nyilvános IP-cím** |
|:----------------------|:-------------------|
| Kelet-Ausztrália       | 20.53.44.80       |
| Közép-Kanada       | 20.48.192.84      |
| Az USA középső régiója           | 52.182.208.31     |
| USA keleti régiója              | 20.62.128.148     |
| USA 2. keleti régiója            | 20.49.102.228     |
| USA 2. keleti – EUAP       | 20.39.26.254      |
| Észak-Németország        | 51.116.51.33      |
| Középnyugat-Németország | 51.116.146.216    |
| Kelet-Japán           | 20.191.160.26     |
| Dél-Korea középső régiója        | 20.41.69.51       |
| USA északi középső régiója     | 20.49.114.188     |
| Észak-Európa         | 52.146.131.52     |
| Dél-Afrika északi régiója   | 102.133.220.197   |
| USA déli középső régiója     | 13.73.254.220     |
| Délkelet-Ázsia       | 23.98.108.42      |
| Észak-Svájc    | 51.107.60.95      |
| Az Egyesült Királyság déli régiója             | 51.104.30.170     |
| Az Egyesült Királyság nyugati régiója              | 51.137.164.94     |
| USA nyugati középső régiója      | 52.150.156.44     |
| Nyugat-Európa          | 20.61.98.66       |
| USA 2. nyugati régiója            | 40.64.135.77      |

### <a name="when-the-azure-storage-account-is-in-the-same-region"></a>Ha az Azure Storage-fiók ugyanabban a régióban található

A konfigurációs folyamat ugyanaz, mint a fentiekben, kivéve a CIDR formátumú adott IP-címtartomány használatát, a 100.64.0.0/10 helyett. Ennek az az oka, hogy az IP-címtartomány, amely magában foglalja a 100.64.0.0 – 100.127.255.255, meg kell határozni az, hogy a szolgáltatás által használt tényleges IP-cím változó, de az egyes $export kérelmek esetében a tartományon belül marad.

> [!Note] 
> Előfordulhat, hogy a 10.0.2.0/24 tartományon belül egy magánhálózati IP-cím is használható. Ebben az esetben a $export művelet sikertelen lesz. Próbálja megismételni a $export kérelmet, de nincs garancia arra, hogy a 100.64.0.0/10 tartományon belüli IP-címek a következő alkalommal lesznek felhasználva. Ez az ismert hálózatkezelési viselkedés a tervezés szerint. A másik lehetőség a Storage-fiók konfigurálása egy másik régióban.
    
## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan exportálhatja a FHIR-erőforrásokat $export parancs használatával. A következő lépésben megtudhatja, hogyan exportálhatja a de azonosított információkat:
 
>[!div class="nextstepaction"]
>[Azonosított adatértékek exportálása](de-identified-export.md)
