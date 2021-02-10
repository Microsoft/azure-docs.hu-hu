---
title: Long audio API – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogy a hosszú hangalapú API hogyan lett kialakítva a hosszú formátumú szöveg és a beszéd közötti aszinkron szintézishez.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/11/2020
ms.author: trbye
ms.openlocfilehash: e28bd5b5caca259201758f0c633b2120a411f422
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2021
ms.locfileid: "100007448"
---
# <a name="long-audio-api"></a>Hosszú hang API

A hosszú hangalapú API a hosszú formátumú szöveg és a beszéd aszinkron szintéziséhez készült (például hangkönyvek, Hírek és dokumentumok). Ez az API nem adja vissza a szintetizált hangot valós időben, ezért a várt érték az, hogy a válasz (ok) ra fog szavazni, és a kimenet (eke) t a szolgáltatás által elérhetővé tettnek megfelelően használja fel. A Speech SDK által használt szöveg-és beszédfelismerési API-val ellentétben a hosszú hangalapú API 10 percnél tovább képes létrehozni a szintetizált hangot, így ideális választás a kiadók és a hangtartalom-platformok számára, hogy hosszú hanganyagokat hozzon létre, például hangkönyveket egy kötegben.

A hosszú hang API további előnyei:

* A szolgáltatás által visszaadott szintetizált beszéd a legjobb neurális hangokat használja.
* Nincs szükség hang-végpont üzembe helyezésére, mert a hangokat nem valós idejű batch-módban szintetizálja.

> [!NOTE]
> A Long audio API mostantól támogatja a [nyilvános neurális hangokat](./language-support.md#neural-voices) és az [Egyéni neurális hangokat](./how-to-custom-voice.md#custom-neural-voices)is.

## <a name="workflow"></a>Munkafolyamat

A hosszú hangalapú API használatakor a rendszer a szintetizált szövegfájlt vagy fájlokat küldi el, lekérdezi az állapotot, majd ha az állapot sikeres, letöltheti az audió kimenetet.

Ez az ábra a munkafolyamat magas szintű áttekintését tartalmazza.

![Hosszú hang-API munkafolyamat-diagram](media/long-audio-api/long-audio-api-workflow.png)

## <a name="prepare-content-for-synthesis"></a>Tartalom előkészítése a szintézishez

A szövegfájl előkészítésekor győződjön meg róla, hogy:

* Egyszerű szöveges (. txt) vagy SSML szöveg (. txt)
* Kódolása [UTF-8, byte Order Mark (BOM)](https://www.w3.org/International/questions/qa-utf8-bom.en#bom) értékkel
* Egyetlen fájl, nem zip
* Több mint 400 karaktert [tartalmaz az egyszerű szöveges vagy a 400](./text-to-speech.md#pricing-note) -es SSML-szövegekhez, és kisebb, mint 10 000 bekezdés
  * Egyszerű szöveg esetén az egyes bekezdéseket az ENTER/Return – [egyszerű szöveges beviteli példa](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/en-US.txt) **megadásával** választjuk el.
  * A SSML szövegek esetében az egyes SSML-darabok bekezdésnek tekintendők. A SSML-darabokat különböző bekezdések szerint kell elválasztani – a [SSML szövegének](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/SSMLTextInputSample.txt) megjelenítése – példa

## <a name="python-example"></a>Python-példa

Ez a szakasz olyan Python-példákat tartalmaz, amelyek a hosszú hang API alapszintű használatát mutatják be. Hozzon létre egy új Python-projektet a kedvenc IDE-környezetében vagy szerkesztőjében. Ezután másolja a kódrészletet egy nevű fájlba `long_audio_synthesis_client.py` .

```python
import json
import ntpath
import requests
```

Ezek a kódtárak a HTTP-kérések összeállítására szolgálnak, és meghívják a szöveg-beszéd hosszú hangszintézis REST API.

### <a name="get-a-list-of-supported-voices"></a>A támogatott hangok listájának beolvasása

A támogatott hangok listájának lekéréséhez küldje el a GET kérelmet a következőnek: `https://<endpoint>/api/texttospeech/v3.0/longaudiosynthesis/voices` .


Ez a kód lehetővé teszi, hogy teljes listát kapjon a hangokat egy adott régióhoz vagy végponthoz, amelyet használhat.
```python
def get_voices():
    region = '<region>'
    key = '<your_key>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis/voices'.format(region)
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    response = requests.get(url, headers=header)
    print(response.text)

get_voices()
```

Cserélje le a következő értékeket:

* Cserélje le a `<your_key>` billentyűt a beszédfelismerési szolgáltatás előfizetési kulcsára. Ezek az információk a [Azure Portal](https://aka.ms/azureportal)erőforrásának **Áttekintés** lapján érhetők el.
* Cserélje le `<region>` a helyére azt a régiót, ahol a beszédfelismerési erőforrás létrejött (például: `eastus` vagy `westus` ). Ezek az információk a [Azure Portal](https://aka.ms/azureportal)erőforrásának **Áttekintés** lapján érhetők el.

Ekkor a következőhöz hasonló kimenet jelenik meg:

```console
{
  "values": [
    {
      "locale": "en-US",
      "voiceName": "en-US-AriaNeural",
      "description": "",
      "gender": "Female",
      "createdDateTime": "2020-05-21T05:57:39.123Z",
      "properties": {
        "publicAvailable": true
      }
    },
    {
      "id": "8fafd8cd-5f95-4a27-a0ce-59260f873141"
      "locale": "en-US",
      "voiceName": "my custom neural voice",
      "description": "",
      "gender": "Male",
      "createdDateTime": "2020-05-21T05:25:40.243Z",
      "properties": {
        "publicAvailable": false
      }
    }
  ]
}
```

Ha a **Properties. publicAvailable** értéke **igaz**, a hang egy nyilvános neurális hang. Ellenkező esetben ez egy egyéni neurális hang.

### <a name="convert-text-to-speech"></a>Szöveg konvertálása beszédre

Készítse elő a bemeneti szövegfájlt egyszerű szöveges vagy SSML szövegben, majd adja hozzá az alábbi kódot a következőhöz `long_audio_synthesis_client.py` :

> [!NOTE]
> `concatenateResult` egy választható paraméter. Ha ez a paraméter nincs megadva, a rendszer a hangkimeneteket egy bekezdés alapján hozza létre. A hanganyagot 1 kimenetre is összefűzheti a paraméter beállításával. 
> `outputFormat` szintén nem kötelező. Alapértelmezés szerint a hang kimenete a riff-16khz-16bit-mono-PCM értékre van beállítva. További információ a támogatott hangkimeneti formátumokról: [hangkimeneti formátumok](#audio-output-formats).

```python
def submit_synthesis():
    region = '<region>'
    key = '<your_key>'
    input_file_path = '<input_file_path>'
    locale = '<locale>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis'.format(region)
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    voice_identities = [
        {
            'voicename': '<voice_name>'
        }
    ]

    payload = {
        'displayname': 'long audio synthesis sample',
        'description': 'sample description',
        'locale': locale,
        'voices': json.dumps(voice_identities),
        'outputformat': 'riff-16khz-16bit-mono-pcm',
        'concatenateresult': True,
    }

    filename = ntpath.basename(input_file_path)
    files = {
        'script': (filename, open(input_file_path, 'rb'), 'text/plain')
    }

    response = requests.post(url, payload, headers=header, files=files)
    print('response.status_code: %d' % response.status_code)
    print(response.headers['Location'])

submit_synthesis()
```

Cserélje le a következő értékeket:

* Cserélje le a `<your_key>` billentyűt a beszédfelismerési szolgáltatás előfizetési kulcsára. Ezek az információk a [Azure Portal](https://aka.ms/azureportal)erőforrásának **Áttekintés** lapján érhetők el.
* Cserélje le `<region>` a helyére azt a régiót, ahol a beszédfelismerési erőforrás létrejött (például: `eastus` vagy `westus` ). Ezek az információk a [Azure Portal](https://aka.ms/azureportal)erőforrásának **Áttekintés** lapján érhetők el.
* Cserélje le a `<input_file_path>` szöveget a szövegről beszédre előkészített szövegfájl elérési útjára.
* Cserélje le `<locale>` a helyére a kívánt kimeneti területi beállítást. További információ: [nyelvi támogatás](language-support.md#neural-voices).

Használja az előző hívása által visszaadott hangok egyikét a `/voices` végpontra.

* Ha nyilvános neurális hangvételt használ, cserélje le `<voice_name>` a kívánt kimeneti hangra.
* Egyéni neurális hang használatához cserélje le a `voice_identities` változót a következőre, és cserélje le `<voice_id>` az `id` Egyéni neurális hangra.
```Python
voice_identities = [
    {
        'id': '<voice_id>'
    }
]
```

Ekkor a következőhöz hasonló kimenet jelenik meg:

```console
response.status_code: 202
https://<endpoint>/api/texttospeech/v3.0/longaudiosynthesis/<guid>
```

> [!NOTE]
> Ha egynél több bemeneti fájllal rendelkezik, több kérést is el kell küldenie. Néhány korlátozást kell figyelembe venni.
> * Az ügyfél legfeljebb **5** kérést küldhet a kiszolgálónak másodpercenként az egyes Azure-előfizetési fiókokhoz. Ha meghaladja a korlátozást, akkor az ügyfél 429 hibakódot kap (túl sok kérés). Csökkentse a kérések másodpercenkénti számát.
> * A kiszolgáló az egyes Azure-előfizetésekhez tartozó fiókokhoz legfeljebb **120** kérelmet futtathat, és a várólistára állíthatja. Ha meghaladja a korlátozást, a kiszolgáló 429 hibakódot ad vissza (túl sok kérés). Várjon, és ne küldje el az új kérést, amíg néhány kérelem be nem fejeződik.

A kimenetben lévő URL-cím használható a kérelem állapotának beolvasásához.

### <a name="get-information-of-a-submitted-request"></a>Beküldött kérelem adatainak beolvasása

Egy elküldött összefoglaló kérelem állapotának lekéréséhez egyszerűen küldjön egy GET-kérést az előző lépésben visszaadott URL-címre.
```Python

def get_synthesis():
    url = '<url>'
    key = '<your_key>'
    header = {
        'Ocp-Apim-Subscription-Key': key
    }
    response = requests.get(url, headers=header)
    print(response.text)

get_synthesis()
```
A kimenet a következőképpen fog kinézni:
```console
response.status_code: 200
{
  "models": [
    {
      "voiceName": "en-US-AriaNeural"
    }
  ],
  "properties": {
    "outputFormat": "riff-16khz-16bit-mono-pcm",
    "concatenateResult": false,
    "totalDuration": "PT5M57.252S",
    "billableCharacterCount": 3048
  },
  "id": "eb3d7a81-ee3e-4e9a-b725-713383e71677",
  "lastActionDateTime": "2021-01-14T11:12:27.240Z",
  "status": "Succeeded",
  "createdDateTime": "2021-01-14T11:11:02.557Z",
  "locale": "en-US",
  "displayName": "long audio synthesis sample",
  "description": "sample description"
}
```

A tulajdonságból megtekintheti a `status` kérelem állapotát. A kérés az `NotStarted` állapotból kezdődik, majd a és a értékre `Running` vált, végül pedig a következő lesz: `Succeeded` `Failed` . A hurok használatával lekérdezheti az API-t, amíg az állapot be nem válik `Succeeded` .

### <a name="download-audio-result"></a>Hang eredményének letöltése

Ha egy összefoglaló kérelem sikeres, letöltheti a hangeredményt a GET API meghívásával `/files` .

```python
def get_files():
    id = '<request_id>'
    region = '<region>'
    key = '<your_key>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis/{}/files'.format(region, id)
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    response = requests.get(url, headers=header)
    print('response.status_code: %d' % response.status_code)
    print(response.text)

get_files()
```
A helyére írja be annak `<request_id>` a kérésnek az azonosítóját, amelyről le szeretné tölteni az eredményt. Az előző lépés válaszában található.

A kimenet a következőképpen fog kinézni:
```console
response.status_code: 200
{
  "values": [
    {
      "name": "2779f2aa-4e21-4d13-8afb-6b3104d6661a.txt",
      "kind": "LongAudioSynthesisScript",
      "properties": {
        "size": 4200
      },
      "createdDateTime": "2021-01-14T11:11:02.410Z",
      "links": {
        "contentUrl": "https://customvoice-usw.blob.core.windows.net/artifacts/input.txt?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    },
    {
      "name": "voicesynthesis_waves.zip",
      "kind": "LongAudioSynthesisResult",
      "properties": {
        "size": 9290000
      },
      "createdDateTime": "2021-01-14T11:12:27.226Z",
      "links": {
        "contentUrl": "https://customvoice-usw.blob.core.windows.net/artifacts/voicesynthesis_waves.zip?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    }
  ]
}
```
A kimenet 2 fájlból álló adatokat tartalmaz. Az a-val `"kind": "LongAudioSynthesisScript"` a bemeneti szkript elküldve. A másik a a `"kind": "LongAudioSynthesisResult"` kérelem eredménye.
Az eredmény zip-fájl, amely tartalmazza a generált hangkimeneti fájlokat, valamint a bemeneti szöveg másolatát.

Mindkét fájl letölthető a tulajdonságában lévő URL-címről `links.contentUrl` .

### <a name="get-all-synthesis-requests"></a>Az összes összefoglaló kérelem beolvasása

Az összes elküldött kérelem listáját a következő kóddal érheti el:

```python
def get_synthesis():
    region = '<region>'
    key = '<your_key>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis/'.format(region)    
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    response = requests.get(url, headers=header)
    print('response.status_code: %d' % response.status_code)
    print(response.text)

get_synthesis()
```

A kimenet a következőhöz hasonló lesz:
```console
response.status_code: 200
{
  "values": [
    {
      "models": [
        {
          "id": "8fafd8cd-5f95-4a27-a0ce-59260f873141",
          "voiceName": "my custom neural voice"
        }
      ],
      "properties": {
        "outputFormat": "riff-16khz-16bit-mono-pcm",
        "concatenateResult": false,
        "totalDuration": "PT1S",
        "billableCharacterCount": 5
      },
      "id": "f9f0bb74-dfa5-423d-95e7-58a5e1479315",
      "lastActionDateTime": "2021-01-05T07:25:42.433Z",
      "status": "Succeeded",
      "createdDateTime": "2021-01-05T07:25:13.600Z",
      "locale": "en-US",
      "displayName": "Long Audio Synthesis",
      "description": "Long audio synthesis sample"
    },
    {
      "models": [
        {
          "voiceName": "en-US-AriaNeural"
        }
      ],
      "properties": {
        "outputFormat": "riff-16khz-16bit-mono-pcm",
        "concatenateResult": false,
        "totalDuration": "PT5M57.252S",
        "billableCharacterCount": 3048
      },
      "id": "eb3d7a81-ee3e-4e9a-b725-713383e71677",
      "lastActionDateTime": "2021-01-14T11:12:27.240Z",
      "status": "Succeeded",
      "createdDateTime": "2021-01-14T11:11:02.557Z",
      "locale": "en-US",
      "displayName": "long audio synthesis sample",
      "description": "sample description"
    }
  ]
}
```

`values` a tulajdonság a szintézisi kérelmek listáját tartalmazza. A lista oldalszámmal van ellátva, és a lap maximális mérete 100. Ha több mint 100 kérelem érkezik, a rendszer egy `"@nextLink"` tulajdonságot biztosít a többoldalas lista következő oldalának beolvasásához.

```console
  "@nextLink": "https://<endpoint>/api/texttospeech/v3.0/longaudiosynthesis/?top=100&skip=100"
```

Az oldal mérete és a szám kihagyása az `skip` `top` URL-cím paraméterének megadásával is elvégezhető.

### <a name="remove-previous-requests"></a>Korábbi kérelmek eltávolítása

A szolgáltatás minden egyes Azure-előfizetési fiók esetében **20 000** -kérelmeket fog tartani. Ha a kérelem összege meghaladja ezt a korlátozást, távolítsa el az előző kéréseket, mielőtt újakat hozna. Ha nem távolítja el a meglévő kérelmeket, a rendszer hibaüzenetet küld.

A következő kód bemutatja, hogyan távolíthat el egy adott szintézis-kérelmet.
```python
def delete_synthesis():
    id = '<request_id>'
    region = '<region>'
    key = '<your_key>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis/{}/'.format(region, id)
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    response = requests.delete(url, headers=header)
    print('response.status_code: %d' % response.status_code)
```

Ha a kérés sikeresen el lett távolítva, a válasz állapotkód HTTP 204 lesz (nincs tartalom).

```console
response.status_code: 204
```

> [!NOTE]
> A vagy az állapotú kérelmeket `NotStarted` `Running` nem lehet eltávolítani vagy törölni.

A kész a `long_audio_synthesis_client.py` [githubon](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Python/voiceclient.py)érhető el.

## <a name="http-status-codes"></a>HTTP-állapotkódok

A következő táblázat a REST API HTTP-válaszait és üzeneteit részletezi.

| API | HTTP-állapotkód | Leírás | Megoldás |
|-----|------------------|-------------|----------|
| Létrehozás | 400 | A hangszintézis nincs engedélyezve ebben a régióban. | A Speech előfizetés kulcsának módosítása egy támogatott régióval. |
|        | 400 | Csak az ehhez a régióhoz tartozó **normál** beszédfelismerési előfizetés érvényes. | Módosítsa a Speech előfizetés kulcsát a "standard" árképzési szintre. |
|        | 400 | Túllépi az Azure-fiókra vonatkozó 20 000-es kérelem korlátját. Új adatbázisok elküldése előtt távolítson el néhány kérelmet. | A kiszolgáló minden Azure-fiók esetében 20 000-kérelmeket fog tartani. Néhány kérelem törlése az újak elküldése előtt. |
|        | 400 | Ez a modell nem használható a következő hangszintézisben: {modelID}. | Győződjön meg arról, hogy a ({modelID}) állapota helyes. |
|        | 400 | A kérelem régiója nem egyezik a következő modell régiójával: {modelID}. | Győződjön meg arról, hogy a {modelID} régiója egyezik a kérelem régiójával. |
|        | 400 | A hangszintézis csak az UTF-8 kódolású szövegfájlt támogatja az byte-Order jelölővel. | Győződjön meg arról, hogy a bemeneti fájlok UTF-8 kódolással vannak ellátva a byte-Order jelölővel. |
|        | 400 | A hangszintézisi kérelemben csak érvényes SSML-bemenetek engedélyezettek. | Győződjön meg arról, hogy a bemeneti SSML-kifejezések helyesek. |
|        | 400 | A (z) {voiceName} nevű hang nem található a bemeneti fájlban. | A bemeneti SSML hangjának neve nincs igazítva a modell azonosítójával. |
|        | 400 | A bemeneti fájlban lévő bekezdések számának 10 000-nál kisebbnek kell lennie. | Győződjön meg arról, hogy a fájlban lévő bekezdések száma kisebb, mint 10 000. |
|        | 400 | A bemeneti fájlnak 400 karakternél nagyobbnak kell lennie. | Győződjön meg arról, hogy a bemeneti fájl mérete meghaladja a 400 karaktert. |
|        | 404 | A hangszintézis definíciójában deklarált modell nem található: {modelID}. | Győződjön meg arról, hogy a ({modelID}) helyes. |
|        | 429 | Túllépi az aktív hangszintézis korlátot. Várjon, amíg néhány kérelem befejeződik. | A kiszolgáló az egyes Azure-fiókokhoz legfeljebb 120 kérelem futtatására és várólistára helyezésére jogosult. Várjon, és ne küldje el az új kéréseket, amíg néhány kérelem be nem fejeződik. |
| Mind       | 429 | Túl sok kérés van. | Az ügyfél legfeljebb 5 kérést küldhet a kiszolgálónak másodpercenként minden egyes Azure-fiókhoz. Csökkentse a kérések másodpercenkénti számát. |
| Törlés    | 400 | A hangszintézisi feladat még használatban van. | Csak a **befejezett** vagy **sikertelen** kéréseket lehet törölni. |
| GetByID   | 404 | A megadott entitás nem található. | Győződjön meg arról, hogy a szintézis azonosítója helyes. |

## <a name="regions-and-endpoints"></a>Régiók és végpontok

A hosszú hang API több régióban is elérhető egyedi végpontokkal.

| Region | Végpont |
|--------|----------|
| USA keleti régiója | `https://eastus.customvoice.api.speech.microsoft.com` |
| Közép-India | `https://centralindia.customvoice.api.speech.microsoft.com` |
| Délkelet-Ázsia | `https://southeastasia.customvoice.api.speech.microsoft.com` |
| Az Egyesült Királyság déli régiója | `https://uksouth.customvoice.api.speech.microsoft.com` |
| Nyugat-Európa | `https://westeurope.customvoice.api.speech.microsoft.com` |

## <a name="audio-output-formats"></a>Hangkimeneti formátumok

A rugalmas hangkimeneti formátumokat támogatjuk. A "concatenateResult" paraméter beállításával egy adott kimenetet adhat meg, vagy egy adott kimenetet is összefűzött a hangkimenetekhez. A hosszú hang API a következő hangkimeneti formátumokat támogatja:

> [!NOTE]
> Az alapértelmezett hang formátuma a riff-16khz-16bit-mono-PCM.

* riff-8khz-16bit-mono-PCM
* riff-16khz-16bit-mono-PCM
* riff-24khz-16bit-mono-PCM
* riff-48kHz-16bit-mono-PCM
* hang-16khz-32kbitrate-mono-MP3
* hang-16khz-64kbitrate-mono-MP3
* hang-16khz-128kbitrate-mono-MP3
* hang-24khz-48kbitrate-mono-MP3
* hang-24khz-96kbitrate-mono-MP3
* hang-24khz-160kbitrate-mono-MP3

## <a name="sample-code"></a>Mintakód
A hosszú hang API-mintakód elérhető a GitHubon.

* [Mintakód: Python](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/Python)
* [Mintakód: C #](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [Mintakód: Java](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)
