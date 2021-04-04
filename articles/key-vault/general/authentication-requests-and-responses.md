---
title: Hitelesítés, kérelmek és válaszok
description: Ismerje meg, hogyan használja a Azure Key Vault JSON-formátumú kérelmeket és válaszokat, valamint a Key Vault használatához szükséges hitelesítést.
services: key-vault
author: amitbapat
manager: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 58616b647affd33e96357e556ab61f85d1c62129
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96752277"
---
# <a name="authentication-requests-and-responses"></a>Hitelesítés, kérelmek és válaszok

A Azure Key Vault kétféle tárolót biztosít a Felhőbeli alkalmazások titkainak tárolásához és kezeléséhez:

|Tároló típusa|Támogatott objektumtípusok|Adatsík végpont|
|--|--|--|
| **Kulcstartók**|<ul><li>Szoftveres védelemmel ellátott kulcsok</li><li>HSM által védett kulcsok (prémium SKU-val)</li><li>Tanúsítványok</li><li>Tárfiókkulcsok</li></ul> | https://{Vault-Name}. Vault. Azure. net
|**Felügyelt HSM** |<ul><li>HSM-védett kulcsok</li></ul> | https://{HSM-Name}. managedhsm. Azure. net

Az egyes típusú objektumok eléréséhez használt URL-utótagok

|Objektumtípus|URL-cím utótagja|
|--|--|
|Szoftveres védelemmel ellátott kulcsok| /keys |
|HSM-védett kulcsok| /keys |
|Titkos kulcsok|/secrets|
|Tanúsítványok| /certificates|
|Tárfiókkulcsok|/storageaccounts
||

Azure Key Vault támogatja a JSON formátumú kérelmeket és válaszokat. A Azure Key Vaultra irányuló kérelmeket egy érvényes Azure Key Vault URL-címre irányítja a HTTPS protokollal, néhány URL-paraméterrel és JSON-kódolású kérelem és válasz Törzsével.

Ez a témakör a Azure Key Vault szolgáltatásra vonatkozó jellemzőket ismerteti. Az Azure REST-felületek (például hitelesítés/engedélyezés és hozzáférési token beszerzése) használatával kapcsolatos általános információkért tekintse meg az [azure REST API referenciát](/rest/api/azure).

## <a name="request-url"></a>URL-cím kérése  
 A kulcskezelő műveletek a HTTP POST használatával végeznek HTTP-TÖRLÉSt, beolvasást, javítást, PUT és HTTP POST és titkosítási műveleteket a meglévő kulcsfontosságú objektumokon. Azok az ügyfelek, amelyek nem támogatják az adott HTTP-műveleteket, a HTTP-BEJEGYZÉST is használhatják az X-HTTP-Request fejléc használatával a kívánt művelet megadásához. azok a kérelmek, amelyek általában nem igénylik a törzset, üres törzset is tartalmazhatnak a HTTP POST használatakor, például ha a POST törlés helyett a POST parancsot használják.  

 A Azure Key Vault objektumainak működéséhez az alábbi URL-címek használhatók:  

- HOZZon létre egy TESTKEY nevű kulcsot egy Key Vault use- `PUT /keys/TESTKEY?api-version=<api_version> HTTP/1.1`  

- IMPORTEDKEY nevű kulcs importálása Key Vault használatára – `POST /keys/IMPORTEDKEY/import?api-version=<api_version> HTTP/1.1`  

- KERESÉSI KIFEJEZÉSKÉNT nevű titkos kód beszerzése egy Key Vault-alkalmazásban – `GET /secrets/MYSECRET?api-version=<api_version> HTTP/1.1`  

- Kivonat aláírása egy TESTKEY nevű kulccsal Key Vault use- `POST /keys/TESTKEY/sign?api-version=<api_version> HTTP/1.1`  

- A Key Vaultra irányuló kérések szolgáltatója mindig a következő:
  - Tárolók esetén: `https://{keyvault-name}.vault.azure.net/`
  - Felügyelt HSM esetében: `https://{HSM-name}.managedhsm.azure.net/`

  A kulcsok mindig a/Keys útvonalon tárolódnak, a titkokat a rendszer mindig a/Secrets elérési út alatt tárolja.  

## <a name="api-version"></a>API-verzió  
 A Azure Key Vault szolgáltatás támogatja a protokollok verziószámozását a régebbi verziójú ügyfelekkel való kompatibilitás biztosításához, de az ügyfelek számára nem minden képesség lesz elérhető. Az ügyfeleknek a `api-version` lekérdezési karakterlánc paraméterrel kell megadniuk az általa támogatott protokoll verziószámát, mivel nincs alapértelmezett érték.  

 A Azure Key Vault protokoll verziói a Date számozási sémát egy {éééé} használatával követik. {MM}. {DD} formátum.  

## <a name="request-body"></a>Kérelem törzse  
 A HTTP-specifikációnak megfelelően a GET műveletekhez nem tartozhatnak kérelem törzse, és a POST és PUT műveleteknek kérelem törzstel kell rendelkezniük. A TÖRLÉSi műveletek törzse nem kötelező a HTTP-ben.  

 Hacsak a művelet leírása másként nincs jelezve, a kérelem törzsének tartalomtípusa csak Application/JSON lehet, és tartalmaznia kell egy szerializált JSON-objektumot, amely a tartalomtípusnak felel meg.  

 Hacsak a művelet leírása másként nincs jelezve, az Accept kérelem fejlécének tartalmaznia kell az Application/JSON adathordozó típusát.  

## <a name="response-body"></a>Válasz törzse  
 Hacsak a művelet leírása másként nincs megadva, a sikeres és a sikertelen műveletek válasz törzsének tartalomtípusa az Application/JSON lesz, és részletes információkat tartalmaz.  

## <a name="using-http-post"></a>HTTP POST használata  
 Előfordulhat, hogy egyes ügyfelek nem tudják használni bizonyos HTTP-műveleteket, például a javítást vagy a TÖRLÉSt. Azure Key Vault támogatja a HTTP POST alternatívát ezen ügyfelek esetében, feltéve, hogy az ügyfél az "X-HTTP-METHOD" fejlécet is tartalmazza az eredeti HTTP-művelethez. A HTTP POST támogatását a jelen dokumentumban meghatározott API-k esetében is fel kell tüntetni.  

## <a name="error-responses"></a>Hibaüzenetek  
 A hibakezelés a HTTP-állapotkódok használatát fogja használni. A tipikus eredmények a következők:  

- 2xx – sikeres: normál működéshez használatos. A válasz törzse a várt eredményt fogja tartalmazni.  

- 3xx – átirányítás: a "nincs módosítva" értékkel rendelkező 304 visszatérhet a feltételes lekérések teljesítéséhez. A jövőben más 3xx-kódokat is használhat a DNS-és elérésiút-változások jelzésére.  

- 4xx – ügyfélalkalmazás: helytelen kérések, hiányzó kulcsok, szintaktikai hibák, érvénytelen paraméterek, hitelesítési hibák stb. használata. A válasz törzse részletes hibaüzenetet fog tartalmazni.  

- 5xx – kiszolgálóhiba: belső kiszolgálóhiba esetén használatos. A válasz törzse az összegzett hibaüzeneteket fogja tartalmazni.  

  A rendszer úgy van kialakítva, hogy proxy vagy tűzfal mögött működjön. Ezért előfordulhat, hogy egy ügyfél más hibakódokat kap.  

  Ha probléma merül fel, a Azure Key Vault a válasz törzsében is visszaadja a hibaüzeneteket. A válasz törzse JSON formátumú, és a következőket veszi át:  

```  

{  
  "error":  
  {  
    "code": "BadArgument",  
    "message":  

      "’Foo’ is not a valid argument for ‘type’."  
    }  
  }  
}  

```  

## <a name="authentication"></a>Hitelesítés  
 A Azure Key Vault összes kérését hitelesíteni kell. Azure Key Vault támogatja Azure Active Directory hozzáférési jogkivonatokat, amelyek a OAuth2 [[RFC6749](https://tools.ietf.org/html/rfc6749)] használatával szerezhetők be. 
 
 Az alkalmazás regisztrálásával és a Azure Key Vault használatának hitelesítésével kapcsolatos további információkért lásd: [az ügyfélalkalmazás regisztrálása az Azure ad](/rest/api/azure/index#register-your-client-application-with-azure-ad)-ben.
 
 A hozzáférési jogkivonatokat a HTTP-engedélyezési fejléc használatával kell elküldeni a szolgáltatásnak:  

```  
PUT /keys/MYKEY?api-version=<api_version>  HTTP/1.1  
Authorization: Bearer <access_token>  

```  

 Ha nincs megadva hozzáférési jogkivonat, vagy ha a szolgáltatás nem fogadja el a jogkivonatot, a rendszer HTTP 401-es hibát ad vissza az ügyfélnek, és tartalmazza a WWW-Authenticate fejlécet, például:  

```  
401 Not Authorized  
WWW-Authenticate: Bearer authorization="…", resource="…"  

```  

 A WWW-Authenticate fejlécének paraméterei:  

-   engedélyezés: a OAuth2-engedélyezési szolgáltatás címe, amely a kérelem hozzáférési jogkivonatának beszerzésére használható.  

-   erőforrás: az `https://vault.azure.net` engedélyezési kérelemben használni kívánt erőforrás () neve.

> [!NOTE]
> Key Vault SDK-ügyfelek a titkokhoz, tanúsítványokhoz és kulcsokhoz az első hívásban, Key Vault nem biztosítanak hozzáférési jogkivonatot a bérlői adatok lekéréséhez. A rendszer a HTTP 401-et fogja használni Key Vault SDK-ügyfél használatával, ahol a Key Vault megjeleníti az alkalmazást, amely az erőforrást és a bérlőt tartalmazó WWW-Authenticate fejlécet tartalmazza, ahol a tokent el kell érnie. Ha minden megfelelően van konfigurálva, akkor az alkalmazás második hívása Key Vault tartalmaz egy érvényes jogkivonatot, és sikeres lesz. 
