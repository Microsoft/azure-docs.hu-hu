---
title: Adatnaplók hozzáadása a naplókhoz egyéni fejlécek használatával – Azure API a FHIR-hez
description: Ez a cikk azt ismerteti, hogyan adhat hozzá információkat a naplókhoz egyéni HTTP-fejlécek használatával az FHIR-hez készült Azure API-ban.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: dseven
ms.author: matjazl
author: matjazl
ms.date: 10/13/2019
ms.openlocfilehash: 937be72bfec96119474e7effe9ba88a2cf253444
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103018488"
---
# <a name="add-data-to-audit-logs-by-using-custom-http-headers"></a>Az egyéni HTTP-fejlécek használatával adhat hozzá adatnaplókat a naplókhoz

Az Azure Fast Healthcare együttműködési erőforrások (FHIR) API-ban a felhasználók további információkat is igénybe vehetnek a naplófájlokban, amelyek a hívó rendszerből származnak.

Ha például az API felhasználóját egy külső rendszer hitelesíti, a rendszer továbbítja a hívást a FHIR API-nak. A FHIR API-rétegben az eredeti felhasználó adatai elvesztek, mert a hívást továbbították. Szükség lehet a felhasználói adatok naplózására és megőrzésére a naplózási vagy felügyeleti célból. A hívó rendszer a HTTP-fejlécekben megadhatja a felhasználói identitást, a hívó helyét, illetve az egyéb szükséges adatokat, amelyek a hívás továbbításakor lesznek végrehajtva.

Ezt az adatfolyamatot a következő ábrán tekintheti meg:

:::image type="content" source="media/custom-headers/custom-headers-diagram.png" alt-text="Egyéni fejlécek diagramja":::

A különböző típusú információk rögzítéséhez egyéni fejléceket használhat. Például:

* Identitás-vagy engedélyezési információk
* A hívó forrása
* Kezdeményező szervezet
* Ügyfélrendszer részletei (elektronikus egészségügyi rekord, beteg-portál)

> [!IMPORTANT]
> Ügyeljen arra, hogy az egyéni fejlécekben elküldött adatok tárolása a Microsoft belső naplózási rendszerében 30 nappal az Azure-naplók figyelése után történik. Javasoljuk, hogy az egyéni fejlécekhez való hozzáadás előtt titkosítsa az adatokat. Az ügyfél-fejléceken keresztül nem adhat át PHI-információkat.

A következő elnevezési konvenciót kell használnia a HTTP-fejlécekhez: X-MS-AZUREFHIR-AUDIT- \<name> .

Ezek a HTTP-fejlécek szerepelnek a naplóba felvett egyik tulajdonságlapon is. Például:

* X-MS-AZUREFHIR-AUDIT-USERID: 1234 
* X-MS-AZUREFHIR-AUDIT-USERLOCATION: XXXX
* X-MS-AZUREFHIR-AUDIT-XYZ: 1234

Ezt az információt a rendszer a JSON-ra szerializálja, amikor hozzáadja a tulajdonságok oszlophoz a naplóban. Például:

```json
{ "X-MS-AZUREFHIR-AUDIT-USERID" : "1234",
"X-MS-AZUREFHIR-AUDIT-USERLOCATION" : "XXXX",
"X-MS-AZUREFHIR-AUDIT-XYZ" : "1234" }
```
 
A HTTP-fejlécekhez hasonlóan a fejléc neve is megismételhető eltérő értékekkel. Például:

* X-MS-AZUREFHIR-AUDIT-USERLOCATION: kórháza
* X-MS-AZUREFHIR-AUDIT-USERLOCATION: vészhelyzet

A naplóba való felvételkor az értékek vesszővel tagolt listával vannak kombinálva. Például:

{"X-MS-AZUREFHIR-AUDIT-USERLOCATION": "Hospitala, Emergency"}
 
Legfeljebb 10 egyedi fejlécet adhat hozzá (a különböző értékekkel rendelkező fejlécek ismétlődéseit csak eggyel számítjuk). Egy fejléc értékének maximális hossza 2048 karakter.

Ha a Firefly C# ügyféloldali API-függvénytárat használja, a kód a következőhöz hasonló:

```C#
FhirClient client;
client = new FhirClient(serverUrl);
client.OnBeforeRequest += (object sender, BeforeRequestEventArgs e) =>
{
    // Add custom headers to be added to the logs
    e.RawRequest.Headers.Add("X-MS-AZUREFHIR-AUDIT-UserLocation", "HospitalA");
};
client.Get("Patient");
```
## <a name="next-steps"></a>Következő lépések
Ebből a cikkből megtudhatta, hogyan adhat hozzá adatnaplókat egyéni fejlécek használatával a FHIR készült Azure API-ban. Következő lépésként megismerheti a FHIR készült Azure API-ban konfigurálható további beállításokat is.
 
>[!div class="nextstepaction"]
>[További beállítások](azure-api-for-fhir-additional-settings.md)
