---
title: Ügyfél-hibakódok az Azure IoT Hub eszköz frissítéséhez | Microsoft Docs
description: Ez a dokumentum a különböző eszköz-frissítési összetevőkhöz tartozó ügyfél-hibakódok táblázatát tartalmazza.
author: lichris
ms.author: lichris
ms.date: 2/18/2021
ms.topic: reference
ms.service: iot-hub-device-update
ms.openlocfilehash: 5251d0cb09e40305d1efd89c31d3af0fa36ad385
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101663652"
---
# <a name="device-update-for-iot-hub-error-codes"></a>IoT Hub hibakódok eszközének frissítése

Ez a dokumentum a különböző eszköz-frissítési összetevőkhöz tartozó hibakódok táblázatát tartalmazza. Ez olyan felhasználók számára készült, akik a hibák diagnosztizálásához és elhárításához szeretnék elemezni a saját hibakódokat.

Két elsődleges ügyféloldali összetevőből állhatnak a hibakódok: az eszköz frissítési ügynöke és a kézbesítési optimalizálási ügynök.

## <a name="device-update-agent"></a>Eszköz frissítési ügynöke

### <a name="resultcode-and-extendedresultcode"></a>ResultCode és ExtendedResultCode

Az eszköz frissítése IoT Hub Core PnP-kapcsolati jelentésekhez `ResultCode` és a `ExtendedResultCode` hibák diagnosztizálásához használható. [További](device-update-plug-and-play.md) információ az eszköz Update Core PnP felületéről.

#### <a name="resultcode"></a>ResultCode

`ResultCode` általános állapotkód, és a http-állapotkód konvenciója.
[További](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) információ a HTTP-állapotkódok használatáról.

#### <a name="extendedresultcode"></a>ExtendedResultCode

`ExtendedResultCode` egy kódolt hibákkal rendelkező egész szám.

A `ExtendedResultCode` PnP-felületen valószínűleg az aláírt egész számot fogja látni. A dekódolásához `ExtendedResultCode` alakítsa át az aláírt egész számot előjel nélküli hexadecimális értékre. A rendszer csak az első 4 bájtot `ExtendedResultCode` használja, és olyan formában van, `F` `FFFFFFF` ahol az első Nibbles a **létesítmény kódja** , a többi Bite pedig a **hibakód**.

**Létesítmények kódjai**

| Létesítmény kódja     | Leírás  |
|-------------------|--------------|
| T                 | Hiba történt a DO SDK-ból|
| E                 | Hibakód egy errno |


Például:

A `ExtendedResultCode` értéke `-536870781`

A előjel nélküli hexadecimális ábrázolása `-536870781` `FFFFFFFF E0000083` .

| Kihagyás    | Létesítmény kódja  | Hibakód   |
|-----------|----------------|--------------|
| FFFFFFFF  | E              | 0000083      |

`0x83` a hexadecimális értéke `131` decimális, amely a errno értéke `ENOLCK` .

## <a name="delivery-optimization-agent"></a>Kézbesítési optimalizálási ügynök
A következő táblázat felsorolja az eszköz frissítési ügyfelének kézbesítési optimalizálási (DO) összetevőjére vonatkozó hibakódokat. A DO összetevő feladata a frissítési tartalom letöltése a IoT-eszközre.

A DO hibakódot az API-hívásokra adott válaszokban fellépő kivételek vizsgálatával lehet megszerezni.

| Hibakód  | Karakterlánc-hiba                       | Típus                 | Leírás |
|-------------|------------------------------------|----------------------|-------------|
| 0x80D01001L | DO_E_NO_SERVICE                    | n.a.                  | A kézbesítési optimalizálás nem tudta biztosítani a szolgáltatást |
| 0x80D02002L | DO_E_DOWNLOAD_NO_PROGRESS          | Feladatok letöltése         | A fájl letöltése nem történt meg a megadott időszakon belül. |
| 0x80D02003L | DO_E_JOB_NOT_FOUND                 | Feladatok letöltése         | A feladatot nem található |
| 0x80D02005L | DO_E_NO_DOWNLOADS                  | Feladatok letöltése         | Jelenleg nem találhatók letöltések |
| 0x80D0200CL | DO_E_JOB_TOO_OLD                   | Feladatok letöltése         | A feladat nem fejeződött be, vagy meg lett szakítva a maximális korhatár elérése előtt |
| 0x80D02011L | DO_E_UNKNOWN_PROPERTY_ID           | Feladatok letöltése         | A SetProperty () vagy a GetProperty () metódus ismeretlen tulajdonság-AZONOSÍTÓval lett meghívva |
| 0x80D02012L | DO_E_READ_ONLY_PROPERTY            | Feladatok letöltése         | A SetProperty () nem hívható meg írásvédett tulajdonságon |
| 0x80D02013L | DO_E_INVALID_STATE                 | Feladatok letöltése         | A kért művelet nem engedélyezett az aktuális feladattípusban. Lehet, hogy a feladatot megszakították vagy befejezték az átvitelt. Jelenleg csak olvasható állapotban van. |
| 0x80D02018L | DO_E_FILE_DOWNLOADSINK_UNSPECIFIED | Feladatok letöltése         | Nem lehet elindítani a letöltést, mert nincs megadva a letöltés fogadója (helyi fájl vagy stream felület). |
| 0x80D02200L | DO_E_DOWNLOAD_NO_URI               | IDODownload felület| A letöltés URI megadása nélkül lett elindítva |
| 0x80D03805L | DO_E_BLOCKED_BY_NO_NETWORK         | Átmeneti feltételek | A letöltés szünetel a hálózati kapcsolat elvesztése miatt |
| 0x80D05001L | DO_E_HTTP_BLOCKSIZE_MISMATCH       | HTTP                 | A HTTP-kiszolgáló olyan választ adott vissza, amelynek adatmérete nem egyezik meg a kért értékkel |
| 0x80D05002L | DO_E_HTTP_CERT_VALIDATION          | HTTP                 | A HTTP-kiszolgáló tanúsítványának ellenőrzése nem sikerült |
| 0x80D05010L | DO_E_INVALID_RANGE                 | HTTP                 | A megadott bájt-tartomány érvénytelen |
| 0x80D05011L | DO_E_INSUFFICIENT_RANGE_SUPPORT    | HTTP                 | A kiszolgáló nem támogatja a szükséges HTTP protokollt. A kézbesítési optimalizálás (DO) megköveteli, hogy a kiszolgáló támogassa a tartomány protokoll fejlécét. |
| 0x80D05012L | DO_E_OVERLAPPING_RANGES            | HTTP                 | A byte-tartományok listája néhány átfedésben lévő tartományt tartalmaz, amelyek nem támogatottak. |
## <a name="device-update-content-service"></a>Eszköz frissítési tartalmi szolgáltatása
A következő táblázat felsorolja az eszköz frissítési szolgáltatásának Content Service összetevőjére vonatkozó hibakódokat. A Content Service összetevő felelős a frissítési tartalom importálásának kezeléséért.

| Hibakód                    | Karakterlánc-hiba                                                               | Következő lépések                         |
|-------------------------------|----------------------------------------------------------------------------|------------------------------------|
| "UpdateAlreadyExists"         | Már létezik azonos identitású frissítés.                              | Győződjön meg arról, hogy olyan frissítést importál, amely még nem lett importálva az eszköz ezen példányára a IoT Hub. |
| "DuplicateContentImport"      | A tartalom egyszerre többször lett importálva.                  | Ugyanaz, mint a UpdateAlreadyExists esetében. |
| "CannotProcessImportManifest" | Hiba történt az importálási jegyzékfájl feldolgozásakor.                                          | A megfelelő importálási jegyzékfájl formázásához tekintse meg az [fogalmak importálása](./import-concepts.md) és a [frissítési dokumentáció importálása](./import-update.md) című témakört. |
| "CannotDownload"              | Az importálási jegyzékfájl nem tölthető le.                                           | Győződjön meg arról, hogy az importálási jegyzékfájl URL-címe továbbra is érvényes. |
| "CannotParse"                 | Az importálási jegyzékfájl nem elemezhető.                                              | Győződjön meg róla, hogy az importálási jegyzékfájl a [frissítés](./import-update.md) dokumentációjában meghatározott sémához tartozik pontossággal. |
| "UnsupportedVersion"          | A jegyzékfájl-séma verziójának importálása nem támogatott.                           | Győződjön meg arról, hogy az importálási jegyzékfájl a [frissítési](./import-update.md) dokumentációban meghatározott legújabb sémát használja. |
| "UpdateLimitExceeded"         | Hiba történt a frissítés importálásakor, mert túllépte a korlátot.                              | Elérte a különböző szolgáltatók, nevek vagy verziók megengedett számát a IoT Hub eszköz frissítésének példányában. Töröljön néhány frissítést a példányból, és próbálkozzon újra. |
| "UpdateProvider"              | Új frissítési szolgáltató nem importálható.                                       | Elérte a IoT Hubhoz tartozó eszköz frissítési példányában engedélyezett különböző __szolgáltatók__ számának korlátozását. Töröljön néhány frissítést a példányból, és próbálkozzon újra. |
| "UpdateName"                  | A megadott szolgáltató új frissítési neve nem importálható.                | Elérte a IoT Hubhoz tartozó eszköz frissítési példányában az egyik szolgáltató által engedélyezett különböző __nevek__ számát. Töröljön néhány frissítést a példányból, és próbálkozzon újra. |
| "UpdateVersion"               | A megadott szolgáltató és név esetében nem lehet új frissítési verziót importálni.    | Elérte az egy szolgáltató által engedélyezett különböző __verziók__ számát, valamint a IoT hub eszköz frissítésének példányában megadott nevet. Töröljön néhány frissítést az adott névvel a példányból, és próbálkozzon újra. |
| "UpdateProviderCompatibility" | Nem lehet importálni további frissítési szolgáltatót a megadott kompatibilitással. | Az eszköz gyártója és az eszköz modellje kompatibilitási tulajdonságainak meghatározásakor vegye figyelembe, hogy az IoT Hub az adott gyártó/modell esetében egyetlen szolgáltatót és név kombinációt támogat. Ez azt jelenti, hogy ha ugyanazokat a gyártói/modell-kompatibilitási tulajdonságokat próbálja használni egynél több szolgáltató/név kombinációval, ezeket a hibákat fogja látni. Ennek megoldásához ellenőrizze, hogy egy adott eszköz (a gyártó/modell által definiált) összes frissítése ugyanazt a szolgáltatót és nevet használja-e. Habár nem szükséges, érdemes lehet megfontolni, hogy a szolgáltató ugyanaz, mint a gyártó és a név megegyezik a modellel, csak az egyszerűség kedvéért. |
| "UpdateNameCompatibility"     | Nem lehet importálni további frissítési nevet a megadott kompatibilitással.     | Ugyanaz, mint a UpdateProviderCompatibility. ContentLimitNamespaceCompatibility esetében. |
| "UpdateVersionCompatibility"  | Nem importálható további frissítési verzió a megadott kompatibilitással.  | Ugyanaz, mint a UpdateProviderCompatibility. ContentLimitNamespaceCompatibility esetében. |
| "CannotProcessUpdateFile"     | Hiba történt a forrásfájl feldolgozásakor.                                              |                                    |
| "ContentFileCannotDownload"   | A forrásfájl nem tölthető le.                                               | Győződjön meg arról, hogy a frissítési fájl (ok) URL-címe továbbra is érvényes. |

**[Következő lépés: az eszköz frissítésével kapcsolatos problémák elhárítása](.\troubleshoot-device-update.md)**