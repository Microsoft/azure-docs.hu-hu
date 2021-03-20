---
title: REST API-hibakódok – Azure Key Vault
description: Ezeket a hibakódokat egy Azure Key Vault webszolgáltatáson végzett művelet is visszaküldheti.
keywords: ''
services: machine-learning
author: msmbaldwin
ms.custom: seodec18
ms.author: mbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: reference
ms.date: 12/16/2019
ms.openlocfilehash: 30b7e34f2a791cfd8dec1a6d8e81d706fa07939f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91631222"
---
# <a name="azure-key-vault-rest-api-error-codes"></a>Azure Key Vault REST API hibakódok
 
Egy Azure Key Vault webszolgáltatáson végzett művelet a következő hibakódokat küldheti vissza.
 
## <a name="http-401-unauthenticated-request"></a>HTTP 401: nem hitelesített kérelem

401 azt jelenti, hogy a kérést nem hitelesítik Key Vault. 

A kérést a rendszer hitelesíti, ha:

- A Key Vault ismeri a hívó identitását; és
- A hívónak meg kell próbálnia Key Vault erőforrásokhoz való hozzáférést. 

Az 401-as kérések több különböző okból is visszatérhetnek.

### <a name="no-authentication-token-attached-to-the-request"></a>Nincs hozzárendelve hitelesítési jogkivonat a kérelemhez. 

Íme egy példa PUT kérelem, amely egy titkos kulcs értékét állítja be:

``` 
PUT https://putreqexample.vault.azure.net//secrets/DatabaseRotatingPassword?api-version=7.0 HTTP/1.1
x-ms-client-request-id: 03d275a2-52a4-4bed-82c8-6fe15165affb
accept-language: en-US
Authorization: Bearer     eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSIsImtpZCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSJ9.eyJhdWQiOiJodHRwczovL3ZhdWx0LmF6dXJlLm5ldCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpYXQiOjE1NDg2OTc1MTMsIm5iZiI6MTU0ODY5NzUxMywiZXhwIjoxNTQ4NzAxNDEzLCJhaW8iOiI0MkpnWUhoODVqaVBnZHF5ZlRGZE5TdHY3bGUvQkFBPSIsImFwcGlkIjoiZmFkN2Q1YjMtNjlkNi00YjQ4LTkyNTktOGQxMjEyNGUxY2YxIiwiYXBwaWRhY3IiOiIxIiwiaWRwIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3LyIsIm9pZCI6IjM5NzVhZWVkLTdkMDgtNDUzYi1iNmY0LTQ0NWYzMjY5ODA5MSIsInN1YiI6IjM5NzVhZWVkLTdkMDgtNDUzYi1iNmY0LTQ0NWYzMjY5ODA5MSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInV0aSI6IjItZ3JoUmtlSWs2QmVZLUxuNDJtQUEiLCJ2ZXIiOiIxLjAifQ.fgubiz1MKqTJTXI8dHIV7t9Fle6FdHrkaGYKcBeVRX1WtLVuk1QVxzIFDlZKLXJ7QPNs0KWpeiWQI9IWIRK-8wO38yCqKTfDlfHOiNWGOpkKddlG729KFqakVf2w0GPyGPFCONRDAR5wjQarN9Bt8I8YbHwZQz_M1hztlnv-Lmsk1jBmech9ujD9-lTMBmSfFFbHcqquev119V7sneI-zxBZLf8C0pIDkaXf1t8y6Xr8CUJDMdlWLslCf3pBCNIOy65_TyGvy4Z4AJryTPBarNBPwOkNAtjCfZ4BDc2KqUZM5QN_VK4foP64sVzUL6mSr0Gh7lQJIL5b1qIpJxjxyQ
User-Agent: FxVersion/4.7.3324.0 OSName/Windows OSVersion/6.2.9200.0 Microsoft.Azure.KeyVault.KeyVaultClient/3.0.3.0
Content-Type: application/json; charset=utf-8
Host: putreqexample.vault.azure.net
Content-Length: 31

{
   "value": "m*gBJ7$Zuoz)"
}
```

Az "engedélyezés" fejléc a hozzáférési jogkivonat, amely az adatsík műveletek Key Vault minden hívásához szükséges. Ha a fejléc hiányzik, akkor a válasznak 401-nek kell lennie.

### <a name="the-token-lacks-the-correct-resource-associated-with-it"></a>A jogkivonat nem rendelkezik a hozzá társított megfelelő erőforrással. 

Amikor hozzáférési jogkivonatot kér az Azure OAUTH végponttól, az "erőforrás" nevű paramétert kötelező megadni. Az érték fontos a jogkivonat-szolgáltató esetében, mert a jogkivonat hatókörét a kívánt célra alkalmazza. Az Key Vaulthoz való hozzáférést biztosító **összes** jogkivonat erőforrása *https: \/ /Vault.keyvault.net* (záró perjel nélkül).

### <a name="the-token-is-expired"></a>A jogkivonat lejárt

A tokenek Base64 kódolással vannak kódolva, és az értékek dekódolva lesznek a webhelyeken, például: [http://jwt.calebb.net](http://jwt.calebb.net) . A fenti token dekódolva:

```
    {
 typ: "JWT",
 alg: "RS256",
 x5t: "nbCwW11w3XkB-xUaXwKRSLjMHGQ",
 kid: "nbCwW11w3XkB-xUaXwKRSLjMHGQ"
}.
{
 aud: "https://vault.azure.net",
 iss: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
 iat: 1548697513,
 nbf: 1548697513,
 exp: 1548701413,
 aio: "42JgYHh85jiPgdqyfTFdNStv7le/BAA=",
 appid: "fad7d5b3-69d6-4b48-9259-8d12124e1cf1",
 appidacr: "1",
 idp: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
 oid: "3975aeed-7d08-453b-b6f4-445f32698091",
 sub: "3975aeed-7d08-453b-b6f4-445f32698091",
 tid: "72f988bf-86f1-41af-91ab-2d7cd011db47",
 uti: "2-grhRkeIk6BeY-Ln42mAA",
 ver: "1.0"
}.
[signature]
```

Ebben a tokenben számos fontos rész látható:

- AUD (hallgatóság): a token erőforrása. Figyelje meg, hogy ez a következő: `https://vault.azure.net` . Ez a jogkivonat nem fog működni minden olyan erőforrás esetében, amely nem felel meg explicit módon az értéknek, például gráfnak.
- IAT (kiadás időpontja): az időpontok száma a jogkivonat kiállítása óta.
- NBF (nem korábban): az időpontok száma az időszak kezdete óta, amikor a token érvényes lesz.
- exp (lejárat): az időpontok száma a token lejárata óta.
- AppID (alkalmazás azonosítója): a kérést alkotó alkalmazás AZONOSÍTÓjának GUID azonosítója.
- TID (bérlői azonosító): a kérést készítő résztvevő bérlői AZONOSÍTÓjának GUID azonosítója

Fontos, hogy az összes érték megfelelően legyen azonosítva a jogkivonatban, hogy a kérés működjön. Ha minden helyes, akkor a kérelem nem fog 401-et eredményezni.

### <a name="troubleshooting-401"></a>Hibaelhárítás 401

a 401s a jogkivonat-létrehozási pontról kell megvizsgálni, mielőtt a rendszer a kulcstartóra kéri a kérést. Általában a token igénylésére szolgáló kód használatos. A jogkivonat fogadása után a rendszer átadja a Key Vault kérelemnek. Ha a kód helyileg fut, akkor a Hegedűs használatával rögzítheti a kérést/választ a következőre: `https://login.microsoftonline.com` . A kérés így néz ki:

``` 
POST https://login.microsoftonline.com/<key vault tenant ID>/oauth2/token HTTP/1.1
Accept: application/json
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Host: login.microsoftonline.com
Content-Length: 192

resource=https%3A%2F%2Fvault.azure.net&client_id=<registered-app-ID>&client_secret=<registered-app-secret>&client_info=1&grant_type=client_credentials
```

A következő felhasználó által megadott adatoknak helyesnek kell lenniük:

- A Key Vault-bérlő azonosítója
- Erőforrás-érték beállítva a https %3 A %2 F %2 F Vault. Azure. net (URL-kódolású)
- Ügyfél-azonosító
- Titkos ügyfélkulcs

Győződjön meg arról, hogy a kérés többi része közel azonos.

Ha csak a válasz hozzáférési jogkivonatot szeretné beszerezni, akkor dekódolhatja (a fenti ábrán látható módon), hogy biztosítsa a bérlő AZONOSÍTÓját, az ügyfél-azonosítót (alkalmazás-azonosítót) és az erőforrást.

## <a name="http-403-insufficient-permissions"></a>HTTP 403: nem megfelelő engedélyek

A HTTP 403 azt jelenti, hogy a kérés hitelesítése megtörtént (ismeri a kérelmező identitást), de az identitásnak nincs engedélye a kért erőforrás elérésére. Két ok van:

- Nincs hozzáférési házirend az identitáshoz.
- A kérelmező erőforrás IP-címe nincs jóváhagyva a Key Vault tűzfalának beállításaiban.

A HTTP 403 gyakran akkor fordul elő, ha az ügyfél alkalmazása nem használja azt az ügyfél-azonosítót, amelyet az ügyfél gondol. Ez általában azt jelenti, hogy a hozzáférési házirendek nem megfelelően vannak beállítva a tényleges hívó identitáshoz.

### <a name="troubleshooting-403"></a>Hibaelhárítás 403

Először kapcsolja be a naplózást. Ennek módjáról a [Azure Key Vault naplózása](logging.md)című témakörben talál útmutatást.

Miután bekapcsolta a naplózást, megadhatja, hogy a 403 a hozzáférési házirend vagy a tűzfal házirendje okozza-e.

#### <a name="error-due-to-firewall-policy"></a>Hiba a tűzfalszabályok miatt

"Az ügyfél címe (00.00.00.00) nincs hitelesítve, és a hívó nem megbízható szolgáltatás"

Korlátozott számú "Azure megbízható szolgáltatás" szerepel. Az Azure webhelyek **nem** megbízható Azure-szolgáltatás. További információ: [Key Vault tűzfal hozzáférése az Azure app Services](https://azidentity.azurewebsites.net/post/2019/01/03/key-vault-firewall-access-by-azure-app-services)-ban című blogbejegyzésben.

Ahhoz, hogy működjön, fel kell vennie az Azure-webhely IP-címét a Key Vaultba.

Ha a hozzáférési házirend miatt: keresse meg a kérelem objektumazonosítóát, és győződjön meg arról, hogy az objektumazonosító megegyezik azzal az objektummal, amelyhez a felhasználó megpróbálja hozzárendelni a hozzáférési házirendet. A HRE több olyan objektum is létezik, amelynek a neve megegyezik, ezért a megfelelő kiválasztása nagyon fontos. A hozzáférési szabályzat törlésével és újbóli hozzáadásával megtekintheti, hogy létezik-e több objektum ugyanazzal a névvel.

Emellett a legtöbb hozzáférési szabályzatnak nincs szüksége a "jogosult alkalmazás" használatára a portálon látható módon. A rendszer a jogosult alkalmazást az olyan "nem használt" hitelesítési forgatókönyvek esetében használja, amelyek ritkák. 


## <a name="http-429-too-many-requests"></a>HTTP 429: túl sok kérelem

A szabályozás akkor következik be, amikor a kérelmek száma meghaladja az adott időkerethez megadott maximális értéket. Ha a szabályozás bekövetkezik, a Key Vault válasza a HTTP 429 lesz. A kérések típusaihoz maximális érték van megadva. Például: egy HSM 2048 bites kulcs létrehozása 10 másodpercenként 5 kérelem, de minden más HSM-tranzakcióhoz 1000 kérelem/10 másodperc korlát tartozik. Ezért fontos tisztában lennie azzal, hogy milyen típusú hívások történnek a szabályozás okának meghatározásakor.
Általánosságban elmondható, hogy a Key Vaultra irányuló kérések száma 2000 kérelem/10 másodperc. A kivételek a [Key Vault szolgáltatási korlátaiban](service-limits.md) dokumentált fő műveletek

### <a name="troubleshooting-429"></a>Hibaelhárítás 429
A szabályozás a következő technikák használatával működik:

- Csökkentse a Key Vault felé irányuló kérések számát azáltal, hogy meghatározza, hogy vannak-e mintázatok egy kért erőforráshoz, és hogyan kell azokat gyorsítótárazni a hívó alkalmazásban. 

- Ha Key Vault szabályozást, a kérelem kódját úgy kell módosítani, hogy exponenciális leállítási használjon az újrapróbálkozáshoz. Az algoritmus magyarázata itt található: az [alkalmazás szabályozása](overview-throttling.md#how-to-throttle-your-app-in-response-to-service-limits)

- Ha a kérések száma nem csökkenthető a gyorsítótárazással és az időzített leállítási, akkor érdemes lehet több Kulcstartóba osztani a kulcsokat. Egy előfizetéshez tartozó szolgáltatási korlát ötszöröse az egyéni Key Vault korlát. Ha több mint 5 kulcstartót használ, több előfizetés használatát is figyelembe kell venni. 

A határértékek növelésére irányuló kérést tartalmazó részletes útmutató itt található: [Key Vault szabályozási útmutató](overview-throttling.md)
