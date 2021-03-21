---
title: A Media Services Operations REST API áttekintése | Microsoft Docs
description: A Media Services műveletek REST API feladatok, eszközök, élő csatornák és egyéb erőforrások létrehozásához használhatók egy Media Services-fiókban. Ez a cikk Azure Media Services v2 REST API áttekintést nyújt.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: a5f1c5e7-ec52-4e26-9a44-d9ea699f68d9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.reviewer: johndeu
ms.openlocfilehash: 9f147e333e4d1b95a14dd3121d7ab304b6166248
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103010048"
---
# <a name="media-services-operations-rest-api-overview"></a>Media Services Operations REST API áttekintése

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Tekintse meg a legújabb, [Media Services v3](../latest/index.yml)verziót. Lásd még: [az áttelepítési útmutató v2-től v3-ig](../latest/migrate-v-2-v-3-migration-introduction.md)

A **Media Services Operations Rest** API-val feladatok, eszközök, élő csatornák és más erőforrások létrehozására van szükség egy Media Services-fiókban. További információ: [Media Services operations REST API Reference](/rest/api/media/operations/azure-media-services-rest-api-reference).

A Media Services olyan REST API biztosít, amely a JSON vagy az Atom + pub XML formátumot is elfogadja. Media Services REST API olyan speciális HTTP-fejléceket igényel, amelyeket az egyes ügyfeleknek el kell küldeniük a Media Serviceshoz való csatlakozáskor, valamint a választható fejlécek készletében. A következő szakaszok azokat a fejléceket és HTTP-műveleteket ismertetik, amelyeket a kérelmek létrehozásakor és a Media Servicestól kapott válaszok fogadásakor használhat.

A Media Services REST API hitelesítése Azure Active Directory hitelesítésen keresztül történik, amely az [Azure ad-hitelesítés használata a Azure Media Services API Rest-tel való eléréséhez](media-services-rest-connect-with-aad.md) .

## <a name="considerations"></a>Megfontolandó szempontok

A REST használatakor a következő szempontokat kell figyelembe venni.

* Az entitások lekérdezése esetén a rendszer egy legfeljebb 1000 entitást ad vissza, mert a nyilvános REST v2 a lekérdezés eredményét 1000 eredményre korlátozza. A **kihagyás** és a végrehajtás (.net)/ **Top** **(REST** ) használata szükséges a [jelen .net-Példa](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) és [a REST API példa](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities)szerint. 
* Ha JSON-t használ, és megadja, hogy az **__metadata** kulcsszót használja a kérelemben (például egy csatolt objektumra való hivatkozáshoz), be kell állítania az **Accept** fejlécet a [JSON részletes formátumba](https://www.odata.org/documentation/odata-version-3-0/json-verbose-format/) (lásd a következő példát). A OData nem érti a kérelemben szereplő **__metadata** tulajdonságot, kivéve, ha azt részletesre állítja.  

    ```console
    POST https://media.windows.net/API/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

    {
        "Name" : "NewTestJob", 
        "InputMediaAssets" : 
            [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aba5356eb-30ff-4dc6-9e5a-41e4223540e7')"}}]
    . . . 
   ```

## <a name="standard-http-request-headers-supported-by-media-services"></a>A Media Services által támogatott szabványos HTTP-kérelmek fejlécei
A Media Servicesban megjelenő minden egyes híváshoz meg kell adni a szükséges fejléceket, amelyeket fel kell vennie a kérelembe, valamint a választható fejléceket is, amelyeket érdemes felvennie. Az alábbi táblázat felsorolja a szükséges fejléceket:

| Fejléc | Típus | Érték |
| --- | --- | --- |
| Engedélyezés |Tulajdonos |A tulajdonos az egyetlen elfogadott engedélyezési mechanizmus. Az értéknek tartalmaznia kell a Azure Active Directory által biztosított hozzáférési jogkivonatot is. |
| x-MS-Version |Tizedesjegy |2,17 (vagy a legújabb verzió)|
| DataServiceVersion |Tizedesjegy |3.0 |
| MaxDataServiceVersion |Tizedesjegy |3.0 |

> [!NOTE]
> Mivel Media Services a OData használatával teszi elérhetővé a REST API-kat, a DataServiceVersion és a MaxDataServiceVersion fejléceket minden kérelembe bele kell foglalni; Ha azonban nem, akkor a jelenleg Media Services feltételezi, hogy a használatban lévő DataServiceVersion értéke 3,0.
> 
> 

A következő a választható fejlécek halmaza:

| Fejléc | Típus | Érték |
| --- | --- | --- |
| Date |RFC 1123 dátum |A kérés időbélyege |
| Elfogadás |Tartalomtípus |A kért tartalomtípus a válaszhoz, például a következő:<p> -Application/JSON; OData = részletes<p> -Application/Atom + XML<p> A válaszok eltérő tartalomtípussal rendelkezhetnek, például egy blob beolvasásával, ahol a sikeres válasz a blob streamet tartalmazza hasznos adattartalomként. |
| Accept-Encoding |Gzip, deflate |A GZIP és a deflate kódolás, ha alkalmazható. Megjegyzés: a nagyméretű erőforrások esetében Media Services figyelmen kívül hagyhatja ezt a fejlécet, és nem tömörített adatmennyiséget adhat vissza. |
| Accept-Language |"en", "es" stb. |Megadja a válasz előnyben részesített nyelvét. |
| Accept-Charset |Karakterkészlet típusa, például "UTF-8" |Az alapértelmezett érték az UTF-8. |
| X-HTTP-Method |HTTP-metódus |Lehetővé teszi az olyan ügyfelek vagy tűzfalak használatát, amelyek nem támogatják az olyan HTTP-metódusokat, mint a PUT vagy a DELETE, ha ezeket a metódusokat szeretné használni, egy GET hívással. |
| Content-Type |Tartalomtípus |A kérelem törzsének tartalomtípusa a PUT vagy POST kérelmekben. |
| ügyfél-kérelem azonosítója |Sztring |Egy hívó által definiált érték, amely az adott kérelmet azonosítja. Ha meg van adva, a rendszer ezt az értéket fogja tartalmazni a válaszüzenetben a kérelem leképezéséhez. <p><p>**Fontos**<p>Az értékeket a 2096b (2k) értékre kell korlátozni. |

## <a name="standard-http-response-headers-supported-by-media-services"></a>A Media Services által támogatott szabványos HTTP-válasz fejlécek
A következőkben egy olyan fejléc található, amely a kért erőforrástól és a végrehajtandó művelettől függően visszatérhet Önnek.

| Fejléc | Típus | Érték |
| --- | --- | --- |
| kérelem azonosítója |Sztring |Az aktuális művelet egyedi azonosítója, a szolgáltatás létrehozva. |
| ügyfél-kérelem azonosítója |Sztring |A hívó által az eredeti kérelemben megadott azonosító, ha van ilyen. |
| Date |RFC 1123 dátum |A kérelem feldolgozásának dátuma és időpontja. |
| Content-Type |Változó |A válasz törzsének tartalomtípusa |
| Content-Encoding |Változó |A gzip vagy a deflate, ha szükséges. |

## <a name="standard-http-verbs-supported-by-media-services"></a>A Media Services által támogatott szabványos HTTP-műveletek
A következő lista a http-kérelmekhez használható HTTP-műveletek teljes listáját tartalmazza:

| Művelet | Leírás |
| --- | --- |
| GET |Egy objektum aktuális értékét adja vissza. |
| POST |Létrehoz egy objektumot a megadott információk alapján, vagy elküld egy parancsot. |
| PUT |Lecserél egy objektumot, vagy létrehoz egy elnevezett objektumot (ha van ilyen). |
| DELETE |Töröl egy objektumot. |
| KÖRLEVÉL |Egy meglévő objektumot frissít, amelynek elnevezett tulajdonsága megváltozik. |
| HEAD |A GET válaszhoz tartozó objektum metaadatait adja vissza. |

## <a name="discover-and-browse-the-media-services-entity-model"></a>A Media Services entitás modell felderítése és tallózása
Media Services entitások felderíthetővé tételéhez a $metadata művelet is használható. Lehetővé teszi az összes érvényes entitás típusának, az entitás tulajdonságainak, társításának, funkcióinak, műveleteinek és így beolvasását. Ha hozzáadja a $metadata műveletet a Media Services REST API végpont végéhez, elérheti ezt a felderítési szolgáltatást.

 /API/$metadata.

Ha egy böngészőben szeretné megtekinteni a metaadatokat, a "? API-Version = 2. x" utótagot az URI végéhez kell hozzáfűzni, vagy a kérelemben ne adja meg az x-MS-Version fejlécet.

## <a name="authenticate-with-media-services-rest-using-azure-active-directory"></a>Hitelesítés Media Services REST használatával Azure Active Directory

A REST APIon végzett hitelesítés Azure Active Directoryon (HRE) keresztül történik.
A Media Services fiókjának az Azure Portalról történő megszerzésével kapcsolatos további információkért tekintse meg [a Azure Media Services API Azure ad-hitelesítéssel történő elérését](media-services-use-aad-auth-to-access-ams-api.md)ismertető témakört.

Az Azure AD-hitelesítéssel REST APIhoz kapcsolódó kódok írásához tekintse meg a következő cikket: az [Azure ad-hitelesítés használata a Azure Media Services API Rest-tel való eléréséhez](media-services-rest-connect-with-aad.md).

## <a name="next-steps"></a>Következő lépések
Az Azure AD-hitelesítés Media Services REST API használatával történő használatáról további információt [Az Azure ad-hitelesítés használata a Azure Media Services API Rest-tel való eléréséhez](media-services-rest-connect-with-aad.md)című témakörben talál.

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
