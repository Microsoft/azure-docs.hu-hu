---
title: Fordítás kezdése
titleSuffix: Azure Cognitive Services
description: Indítson el egy dokumentumfordítási kérelmet a Document Translation szolgáltatással.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: 1d167962e22953a4a9ca69ece347f8427b9218c9
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107836247"
---
# <a name="start-translation"></a>Fordítás kezdése

Ezzel az API-val tömeges (kötegelt) fordítási kérelmet indít el a Document Translation szolgáltatással. Minden kérés több dokumentumot tartalmazhat, és minden dokumentumhoz tartalmaznia kell egy forrás- és céltárolót.

Az előtag- és utótagszűrő (ha meg van adva) a mappák szűréséhez használható. Az előtag a tároló neve utáni aloldalra lesz alkalmazva.

Szószedetek/fordítási memória is szerepeltethető a kérésben, és a szolgáltatás alkalmazza őket a dokumentum fordításakor.

Ha a szószedet érvénytelen vagy nem érhető el a fordítás során, a dokumentum állapota hibát jelez. Ha már létezik ilyen nevű fájl a célhelyen, a rendszer felülírja azt. Az egyes célnyelvek targetUrl-ének egyedinek kell lennie.

## <a name="request-url"></a>URL-cím kérése

Kérés küldése a `POST` következőre:
```HTTP
POST https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches
```

Megtudhatja, hogyan találhatja meg [az egyéni tartománynevet.](../get-started-with-document-translation.md#find-your-custom-domain-name)

> [!IMPORTANT]
>
> * **A Document Translation szolgáltatásnak minden API-kéréséhez egyéni tartományvégpontra van szükség.**
> * Nem használhatja az erőforráskulcsok és Azure Portal  oldalán található végpontot, sem a globális fordítóvégpontot – a Dokumentumfordítás szolgáltatásnak `api.cognitive.microsofttranslator.com` való HTTP-kérések igénylésére.

## <a name="request-headers"></a>Kérésfejlécek

A kérelemfejlécek a következőek:

|Fejlécek|Description|
|--- |--- |
|Ocp-Apim-Subscription-Key|Szükséges kérelemfejléc|

## <a name="request-body-batch-submission-request"></a>Kérelem törzse: Kötegelt beküldési kérelem

|Név|Típus|Description|
|--- |--- |--- |
|Bemenetek|BatchRequest[]|A BatchRequest az alábbiakban látható. A dokumentumokat tartalmazó dokumentumok vagy mappák bemeneti listája. Médiatípusok: "application/json", "text/json", "application/*+json".|

### <a name="inputs"></a>Bevitelek

A bemeneti kötegelt fordítási kérelem definíciója.

|Név|Típus|Kötelező|Leírás|
|--- |--- |--- |--- |
|source|SourceInput[]|Igaz|az alábbiakban felsorolt inputs.source fájlban. A bemeneti dokumentumok forrása.|
|storageType (tárolótípus)|StorageInputType[]|Igaz|az alábbiakban felsorolt inputs.storageType. A bemeneti dokumentumok forrássringének tárolótípusa.|
|Célok|TargetInput[]|Igaz|az alábbiakban felsorolt inputs.target. A kimenet célhelyének helye.|

**inputs.source**

A bemeneti dokumentumok forrása.

|Név|Típus|Kötelező|Leírás|
|--- |--- |--- |--- |
|filter (szűrő)|DocumentFilter[]|Hamis|DocumentFilter[] az alábbiakban látható.|
|filter.prefix|sztring|Hamis|Kis- és nagybetűket megkülönböztető előtagsző sztring a fordítás forrásútvonalában található dokumentumok szűréséhez. Azure Storage-blob URI használata esetén például az előtaggal korlátozhatja az almappákat a fordításhoz.|
|filter.suffix|sztring|Hamis|Kis- és nagybetűket megkülönböztető utótagsring a dokumentumok forrásútvonalon való szűréséhez a fordításhoz. Ez leggyakrabban fájlkiterjesztéshez használható.|
|language|sztring|Hamis|Nyelvi kód Ha nincs megadva, a rendszer automatikusan észleli a dokumentumot.|
|sourceUrl|sztring|True (Igaz)|A mappa/tároló vagy egyetlen fájl helye a dokumentumokkal.|
|storageSource|StorageSource|Hamis|Az alábbiakban felsorolt StorageSource.|
|storageSource.AzureBlob|sztring|Hamis||

**inputs.storageType**

A bemeneti dokumentumok forrássringének tárolótípusa.

|Név|Típus|
|--- |--- |
|file|sztring|
|Mappa|sztring|

**inputs.target**

A kész lefordított dokumentumok célhelye.

|Név|Típus|Kötelező|Leírás|
|--- |--- |--- |--- |
|category|sztring|Hamis|Kategória/egyéni rendszer a fordítási kéréshez.|
|Szószedetek|Szószedet[]|Hamis|Az alábbiakban felsorolt szószedet. Szószedetlista.|
|glossaries.format|sztring|Hamis|Formátum.|
|glossaries.glossaryUrl|sztring|Igaz (ha szószedeteket használ)|A szószedet helye. Ha a formátumparaméter nincs megadva, a fájlkiterjesztéssel fogjuk kinyerni a formázást. Ha a fordítási nyelvpár nincs jelen a szószedetben, akkor nem lesz alkalmazva.|
|glossaries.storageSource|StorageSource|Hamis|A fent felsorolt StorageSource.|
|targetUrl|sztring|True (Igaz)|A mappa/tároló helye a dokumentumokkal.|
|language|sztring|True (Igaz)|Kétbetűs célnyelvi kód. Lásd [a nyelvkódok listáját.](../../language-support.md)|
|storageSource|StorageSource []|Hamis|StorageSource [] a fent felsoroltak szerint.|
|version|sztring|Hamis|Verzió felirat mellett megjelenő számot.|

## <a name="example-request"></a>Példakérelem

Az alábbiakban példákat talál a kötegelt kérelmekre.

**Egy tárolóban található összes dokumentum fordítása**

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": https://my.blob.core.windows.net/source-en?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=SDRPMjE4nfrH3csmKLILkT%2Fv3e0Q6SWpssuuQl1NmfM%3D
            },
            "targets": [
                {
                    "targetUrl": https://my.blob.core.windows.net/target-fr?sv=2019-12-12&st=2021-03-05T17%3A49%3A02Z&se=2021-03-13T17%3A49%3A00Z&sr=c&sp=wdl&sig=Sq%2BYdNbhgbq4hLT0o1UUOsTnQJFU590sWYo4BOhhQhs%3D,
                    "language": "fr"
                }
            ]
        }
    ]
}
```

**Egy szószedeteket alkalmazó tárolóban található összes dokumentum lefordítása**

Győződjön meg arról, hogy létrehozta a szószedet URL& SAS-jogkivonatot az adott blobhoz/dokumentumhoz (nem a tárolóhoz)

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": https://my.blob.core.windows.net/source-en?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=SDRPMjE4nfrH3csmKLILkT%2Fv3e0Q6SWpssuuQl1NmfM%3D
            },
            "targets": [
                {
                    "targetUrl": https://my.blob.core.windows.net/target-fr?sv=2019-12-12&st=2021-03-05T17%3A49%3A02Z&se=2021-03-13T17%3A49%3A00Z&sr=c&sp=wdl&sig=Sq%2BYdNbhgbq4hLT0o1UUOsTnQJFU590sWYo4BOhhQhs%3D,
                    "language": "fr"
     "glossaries": [
                        {
                            "glossaryUrl": https://my.blob.core.windows.net/glossaries/en-fr.xlf?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=BsciG3NWoOoRjOYesTaUmxlXzyjsX4AgVkt2AsxJ9to%3D,
                            "format": "xliff",
                            "version": "1.2"
                        }
                    ]

                }
            ]
        }
    ]
}
```

**Tároló adott mappájának fordítása**

Győződjön meg arról, hogy a szűrőben előtagként a mappanevet (a kis- és nagybetűket megkülönböztetve) adja meg, bár az SAS-jogkivonat továbbra is a tárolóhoz van megadva.

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": https://my.blob.core.windows.net/source-en?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=SDRPMjE4nfrH3csmKLILkT%2Fv3e0Q6SWpssuuQl1NmfM%3D,
                "filter": {
                    "prefix": "MyFolder/"
                }
            },
            "targets": [
                {
                    "targetUrl": https://my.blob.core.windows.net/target-fr?sv=2019-12-12&st=2021-03-05T17%3A49%3A02Z&se=2021-03-13T17%3A49%3A00Z&sr=c&sp=wdl&sig=Sq%2BYdNbhgbq4hLT0o1UUOsTnQJFU590sWYo4BOhhQhs%3D,
                    "language": "fr"
                }
            ]
        }
    ]
}
```

**Adott dokumentum fordítása egy tárolóban**

* Győződjön meg arról, hogy a "storageType": "File" értéket adott meg
* Győződjön meg arról, hogy létrehozta a forrás URL& SAS-jogkivonatot az adott blobhoz/dokumentumhoz (nem a tárolóhoz)
* Győződjön meg arról, hogy a cél URL-cím részeként meg lett adva a célfájlnév, bár az SAS-jogkivonat továbbra is a tárolóhoz van megadva.
* Az alábbi mintakérés egyetlen dokumentumot mutat be, amely két célnyelvre lesz lefordítva

```json
{
    "inputs": [
        {
            "storageType": "File",
            "source": {
                "sourceUrl": https://my.blob.core.windows.net/source-en/source-english.docx?sv=2019-12-12&st=2021-01-26T18%3A30%3A20Z&se=2021-02-05T18%3A30%3A00Z&sr=c&sp=rl&sig=d7PZKyQsIeE6xb%2B1M4Yb56I%2FEEKoNIF65D%2Fs0IFsYcE%3D
            },
            "targets": [
                {
                    "targetUrl": https://my.blob.core.windows.net/target/try/Target-Spanish.docx?sv=2019-12-12&st=2021-01-26T18%3A31%3A11Z&se=2021-02-05T18%3A31%3A00Z&sr=c&sp=wl&sig=AgddSzXLXwHKpGHr7wALt2DGQJHCzNFF%2F3L94JHAWZM%3D,
                    "language": "es"
                },
                {
                    "targetUrl": https://my.blob.core.windows.net/target/try/Target-German.docx?sv=2019-12-12&st=2021-01-26T18%3A31%3A11Z&se=2021-02-05T18%3A31%3A00Z&sr=c&sp=wl&sig=AgddSzXLXwHKpGHr7wALt2DGQJHCzNFF%2F3L94JHAWZM%3D,
                    "language": "de"
                }
            ]
        }
    ]
}
```

## <a name="response-status-codes"></a>Válasz állapotkódok

A kérések által visszaadott lehetséges HTTP-állapotkódok a következők.

|Állapotkód|Description|
|--- |--- |
|202|Elfogadott. A sikeres kérést és a kötegelt kérést a szolgáltatás hozta létre. A fejléc Operation-Location url-címet fog jelezni az ID.HeadersOperation-Location: sztring művelettel|
|400|Hibás kérés. Érvénytelen kérés. Ellenőrizze a bemeneti paramétereket.|
|401|Jogosulatlan. Ellenőrizze hitelesítő adatait.|
|429|A kérelmek sebessége túl magas.|
|500|Belső kiszolgálóhiba.|
|503|A szolgáltatás jelenleg nem érhető el.  Próbálkozzon újra később.|
|Egyéb állapotkódok|<ul><li>Túl sok kérelem</li><li>A kiszolgáló ideiglenesen nem érhető el</li></ul>|

## <a name="error-response"></a>Hibaválasz

|Név|Típus|Description|
|--- |--- |--- |
|code|sztring|Magas szintű hibakódokat tartalmazó felsorolások. Lehetséges értékek:<br/><ul><li>InternalServerError</li><li>InvalidArgument (Érvénytelen nyelv)</li><li>InvalidRequest (Érvénytelen kérdés)</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Nem engedélyezett</li></ul>|
|message|sztring|Magas szintű hibaüzenetet kap.|
|innerError|InnerErrorV2|Új belső hibaformátum, amely megfelel Cognitive Services API-irányelveknek. Tartalmazza a szükséges ErrorCode tulajdonságokat, az üzenetet és az opcionális céltulajdonságokat, a részleteket (kulcs-érték párt), a belső hibát (ez beágyazható).|
|Belső. Hibakód|sztring|Lekérte a kód hibasringet.|
|innerError.message|sztring|Magas szintű hibaüzenetet kap.|

## <a name="examples"></a>Példák

### <a name="example-successful-response"></a>Példa a sikeres válaszra

A sikeres válasz az alábbi adatokat tartalmazza.

A feladatazonosítót a POST-metódus válaszfejlécében, az URLOperation-Location találja. Az URL-cím utolsó paramétere a művelet feladatazonosítója (a "/operation/" sztring).

```HTTP
Operation-Location: https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0.preview.1/operation/0FA2822F-4C2A-4317-9C20-658C801E0E55
```

### <a name="example-error-response"></a>Példa hibaválaszra

```JSON
{
  "error": {
    "code": "ServiceUnavailable",
    "message": "Service is temporary unavailable",
    "innerError": {
      "code": "ServiceTemporaryUnavailable",
      "message": "Service is currently unavailable.  Please try again later"
    }
  }
}
```

## <a name="next-steps"></a>Következő lépések

A dokumentumfordítás és az ügyféloldali kódtár használatával kapcsolatos további információkért kövesse a rövid útmutatót.

> [!div class="nextstepaction"]
> [A dokumentumfordítás első lépések](../get-started-with-document-translation.md)
