---
title: Dokumentum fordítása kötegelt kérelem küldése
titleSuffix: Azure Cognitive Services
description: Dokumentum-fordítási kérelem küldése a dokumentum-fordítási szolgáltatásnak.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: 803a0b9f4496a3d785aa6f22833fee4ae6eca6e0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105613026"
---
# <a name="document-translation-submit-batch-request"></a>Dokumentum fordítása: batch-kérelem elküldése

Ezzel az API-val tömeges (batch) fordítási kérelmet küldhet a dokumentum-fordítási szolgáltatásnak. Minden kérelem több dokumentumot is tartalmazhat, és minden dokumentumhoz tartalmaznia kell egy forrás-és egy cél tárolót.

A mappák szűréséhez az előtag és az utótag szűrő (ha meg van adva) is használható. Az előtagot a rendszer a tároló neve után alkalmazza a alelérési útra.

A kérelemben szerepelhetnek szószedetek/fordítási memória, és a szolgáltatás a dokumentum fordításakor alkalmazza azokat.

Ha a Szószedet érvénytelen vagy nem érhető el a fordítás során, a dokumentum állapota hibát jelez. Ha már létezik ilyen nevű fájl a célhelyen, a rendszer felülírja. Az egyes célnyelv célcímet egyedinek kell lennie.

## <a name="request-url"></a>URL-cím kérése

Kérelem küldése `POST` a következőnek:
```HTTP
POST https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches
```

Megtudhatja, hogyan keresheti meg az [Egyéni tartománynevet](../get-started-with-document-translation.md#find-your-custom-domain-name).

> [!IMPORTANT]
>
> * **A dokumentum-fordítási szolgáltatáshoz tartozó összes API-kérelemhez egyéni tartományi végpont szükséges**.
> * Nem használhatja a végpontot a Azure Portal erőforrás- _kulcsok és a végpontok_ oldalán, sem a globális Translator végponton –, `api.cognitive.microsofttranslator.com` hogy http-kéréseket lehessen felvenni a dokumentumok fordítására.

## <a name="request-headers"></a>Kérésfejlécek

A kérelem fejlécei:

|Fejlécek|Leírás|
|--- |--- |
|Ocp-Apim-Subscription-Key|Kötelező kérelem fejléce|

## <a name="request-body-batch-submission-request"></a>Kérelem törzse: batch-küldési kérelem

|Név|Típus|Leírás|
|--- |--- |--- |
|bemenetek|BatchRequest[]|Alább felsorolt BatchRequest. A dokumentumokat tartalmazó dokumentumok vagy mappák beviteli listája. Adathordozó típusa: "Application/JSON", "text/JSON", "Application/* + JSON".|

### <a name="inputs"></a>Bevitelek

A bemeneti köteg fordítási kérelmének definíciója.

|Név|Típus|Kötelező|Leírás|
|--- |--- |--- |--- |
|source|SourceInput[]|Igaz|Inputs. Source alább látható. A bemeneti dokumentumok forrása.|
|storageType|StorageInputType[]|Igaz|az alábbi listában szereplő Inputs. storageType. A bemeneti dokumentumok forrás-karakterláncának tárolási típusa.|
|célok|TargetInput[]|Igaz|Inputs. Target alább látható. A kimenet céljának helye.|

**Inputs. Source**

A bemeneti dokumentumok forrása.

|Név|Típus|Kötelező|Leírás|
|--- |--- |--- |--- |
|filter (szűrő)|DocumentFilter[]|Hamis|Az alább felsorolt DocumentFilter [].|
|szűrő. előtag|sztring|Hamis|Kis-és nagybetűket megkülönböztető előtag-karakterlánc a dokumentumok a fordítás forrásának elérési útján történő szűréséhez. Ha például egy Azure Storage blob URI-t használ, használja az előtagot az almappák fordításra való korlátozásához.|
|Filter. utótag|sztring|Hamis|Kis-és nagybetűket megkülönböztető utótag-karakterlánc a dokumentumok a fordítás forrásának elérési útján történő szűréséhez. Ez leggyakrabban a fájlkiterjesztések esetében használatos.|
|language|sztring|Hamis|Ha nincs megadva a nyelvi kód, a rendszer automatikus észlelést hajt végre a dokumentumban.|
|sourceUrl|sztring|True (Igaz)|A mappa/tároló vagy egyetlen fájl helye a dokumentumokkal.|
|storageSource|StorageSource|Hamis|Alább felsorolt StorageSource.|
|storageSource. AzureBlob|sztring|Hamis||

**Inputs. storageType**

A bemeneti dokumentumok forrás-karakterláncának tárolási típusa.

|Név|Típus|
|--- |--- |
|file|sztring|
|mappa|sztring|

**bemenetek. cél**

A befejezett lefordított dokumentumok célhelye.

|Név|Típus|Kötelező|Leírás|
|--- |--- |--- |--- |
|category|sztring|Hamis|Kategória/egyéni rendszer fordítási kérelemhez.|
|szószedetek|Szószedet []|Hamis|Az alább felsorolt Szószedet. Szószedet listája.|
|szószedetek. Format|sztring|Hamis|Formátumban.|
|szószedetek. glossaryUrl|sztring|True (szószedetek használata esetén)|A Szószedet helye. A fájlkiterjesztés használatával kinyerjük a formázást, ha nincs megadva a Format paraméter. Ha a fordítási nyelvi pár nem szerepel a szószedetben, nem lesz alkalmazva.|
|szószedetek. storageSource|StorageSource|Hamis|A fent felsorolt StorageSource.|
|célcímet|sztring|True (Igaz)|A mappa/tároló helye a dokumentumokkal.|
|language|sztring|True (Igaz)|Két betűs cél nyelvi kódja [A nyelvi kódok listáját](../../language-support.md)itt tekintheti meg.|
|storageSource|StorageSource []|Hamis|A fent felsorolt StorageSource [].|
|version|sztring|Hamis|Verzió felirat mellett megjelenő számot.|

## <a name="example-request"></a>Példakérelem

A következő példák a Batch-kérelmekre vonatkoznak.

**A tárolóban lévő összes dokumentum fordítása**

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

**A tárolóban található összes dokumentum lefordítása szószedetek alkalmazásával**

Győződjön meg arról, hogy létrehozta a Szószedet URL-címét & SAS-tokent az adott blobhoz/dokumentumhoz (nem a tárolóhoz).

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

**Adott mappa lefordítása egy tárolóban**

Győződjön meg arról, hogy a mappanév (kis-és nagybetűk megkülönböztetése) előtagként van megadva a szűrőben – bár a SAS-jogkivonat továbbra is a tárolóhoz tartozik.

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

**Adott dokumentum lefordítása egy tárolóban**

* Győződjön meg arról, hogy a "storageType": "file" értéket adta meg
* Győződjön meg arról, hogy létrehozta a forrás URL-címet & SAS-tokent az adott blobhoz/dokumentumhoz (nem a tárolóhoz).
* Győződjön meg arról, hogy a cél URL-cím részeként adta meg a cél fájlnevét – bár a SAS-token még mindig a tárolóhoz tartozik.
* Az alábbi minta-kérelemben egyetlen dokumentum jelenik meg, amely két cél nyelvre van lefordítva

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

## <a name="response-status-codes"></a>Válasz-állapotkódok

A kérelem által visszaadott lehetséges HTTP-állapotkódok a következők:

|Állapotkód|Leírás|
|--- |--- |
|202|Elfogadott. A sikeres kérés és a Batch-kérést a szolgáltatás hozza létre. A fejléc Operation-Location a műveleti AZONOSÍTÓval rendelkező állapot-URL-címet jelöli. HeadersOperation helye: karakterlánc|
|400|Hibás kérelem. Érvénytelen kérés. Adja meg a bemeneti paramétereket.|
|401|Jogosulatlan. Ellenőrizze a hitelesítő adatokat.|
|429|A kérelmek aránya túl magas.|
|500|Belső kiszolgálóhiba.|
|503|A szolgáltatás jelenleg nem érhető el.  Próbálkozzon újra később.|
|Egyéb állapotkódok|<ul><li>Túl sok kérelem</li><li>A kiszolgáló átmenetileg nem érhető el</li></ul>|

## <a name="error-response"></a>Hiba válasza

|Név|Típus|Leírás|
|--- |--- |--- |
|code|sztring|A magas szintű hibakódokat tartalmazó enumerálások. Lehetséges értékek:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Nem engedélyezett</li></ul>|
|message|sztring|Magas szintű hibaüzenetet kap.|
|innerError|InnerErrorV2|Új belső hiba formátuma, amely megfelel a Cognitive Services API-irányelveknek. Tartalmazza a szükséges tulajdonságokat: ErrorCode, üzenet és opcionális tulajdonságok célpontja, részletek (kulcs érték párok), belső hiba (ez lehet beágyazott).|
|belső. Errorcode|sztring|Hibakód-karakterlánc beolvasása.|
|innerError. Message|sztring|Magas szintű hibaüzenetet kap.|

## <a name="examples"></a>Példák

### <a name="example-successful-response"></a>Példa sikeres válaszra

A sikeres válasz a következő információkat adja vissza.

A feladatok AZONOSÍTÓját a POST metódus válasz fejlécében Operation-Location URL-értékben találja. Az URL utolsó paramétere a művelet feladatainak azonosítója (a "/Operation/" karakterláncot követő sztring).

```HTTP
Operation-Location: https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0.preview.1/operation/0FA2822F-4C2A-4317-9C20-658C801E0E55
```

### <a name="example-error-response"></a>Példa a hiba válaszára

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

A gyors üzembe helyezéssel kapcsolatos további információkért tekintse meg a dokumentumok fordításának és az ügyféloldali kódtár használatának lépéseit.

> [!div class="nextstepaction"]
> [Ismerkedés a dokumentumok fordításával](../get-started-with-document-translation.md)
