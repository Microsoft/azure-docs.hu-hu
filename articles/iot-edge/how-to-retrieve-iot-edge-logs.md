---
title: IoT Edge naplók beolvasása – Azure IoT Edge
description: IoT Edge modul naplójának lekérése és feltöltése az Azure Blob Storageba.
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 11/12/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
ms.custom: devx-track-azurecli
services: iot-edge
ms.openlocfilehash: 69f7ec5114ad650f33eae740a54a3821b76ef2ac
ms.sourcegitcommit: 445ecb22233b75a829d0fcf1c9501ada2a4bdfa3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99475539"
---
# <a name="retrieve-logs-from-iot-edge-deployments"></a>Naplók beolvasása IoT Edge központi telepítésből

A IoT Edge üzemelő példányokból származó naplók beolvasása anélkül, hogy fizikai vagy SSH-hozzáférést kellene használnia az eszközhöz az IoT Edge Agent modulban foglalt közvetlen módszerek használatával. A közvetlen metódusok implementálva vannak az eszközön, majd a felhőből is meghívhatók. A IoT Edge ügynök olyan közvetlen metódusokat tartalmaz, amelyek segítségével távolról figyelheti és kezelheti IoT Edge eszközeit. A cikkben tárgyalt közvetlen metódusok általánosan elérhetők a 1.0.10 kiadásával.

További információ a közvetlen módszerekről, a használatáról és a saját modulok megvalósításáról: [közvetlen módszerek megismerése és meghívása a IoT hubból](../iot-hub/iot-hub-devguide-direct-methods.md).

A közvetlen metódusok neve a kis-és nagybetűk megkülönböztetésére szolgál.

## <a name="recommended-logging-format"></a>Ajánlott naplózási formátum

Habár nem szükséges, a szolgáltatással való legjobb kompatibilitás érdekében az ajánlott naplózási formátum a következő:

```
<{Log Level}> {Timestamp} {Message Text}
```

`{Log Level}` a [syslog súlyossági szintjének formátumát](https://wikipedia.org/wiki/Syslog#Severity_level) kell követnie, és a következő formátumban kell `{Timestamp}` formázni: `yyyy-MM-dd hh:mm:ss.fff zzz` .

A [IoT Edge naplózó osztálya](https://github.com/Azure/iotedge/blob/master/edge-util/src/Microsoft.Azure.Devices.Edge.Util/Logger.cs) kanonikus implementációként szolgál.

## <a name="retrieve-module-logs"></a>Modul naplófájljainak beolvasása

A **GetModuleLogs** Direct metódussal kérheti le egy IoT Edge modul naplóit.

Ez a metódus egy JSON-adattartalmat fogad el a következő sémával:

```json
    {
       "schemaVersion": "1.0",
       "items": [
          {
             "id": "regex string",
             "filter": {
                "tail": "int",
                "since": "string",
                "until": "string",
                "loglevel": "int",
                "regex": "regex string"
             }
          }
       ],
       "encoding": "gzip/none",
       "contentType": "json/text" 
    }
```

| Név | Típus | Leírás |
|-|-|-|
| sémaverzióval | sztring | Beállítás értéke `1.0` |
| elemek | JSON-tömb | Egy tömb `id` és egy `filter` rekordok. |
| ID (Azonosító) | sztring | Egy reguláris kifejezés, amely megadja a modul nevét. Egy peremhálózati eszközön több modulnak is megfelel. A [.net reguláris kifejezések](/dotnet/standard/base-types/regular-expressions) formátuma várható. |
| filter (szűrő) | JSON szakasz | A `id` rekordban szereplő reguláris kifejezésnek megfelelő modulokra alkalmazandó naplózási szűrők. |
| farok | egész szám | A napló sorainak száma a múltban, a legutóbbi kezdéstől kezdve. Választható. |
| mivel | sztring | A naplók csak ennyi idő után térnek vissza, mint az időtartam (1 d, 90 m, 2 nap 3 óra 2 perc), rfc3339 timestamp vagy UNIX timestamp.  Ha mindkettő `tail` és `since` a meg van adva, a rendszer a naplókat először az érték használatával kérdezi le `since` . Ezt követően az `tail` érték az eredményre lesz alkalmazva, és a rendszer a végeredményt adja vissza. Választható. |
| amíg | sztring | Csak a megadott időpont előtt, a rfc3339 timestamp, a UNIX timestamp vagy az időtartam (1 d, 90 m, 2 nap 3 óra 2 perc) után ad vissza naplókat. Választható. |
| naplózási szint | egész szám | A naplózási sorok szűrése a megadott naplózási szintnél kisebb vagy azzal egyenlő. A naplófájloknak a javasolt naplózási formátumot kell követniük, és a [syslog súlyossági szintje standard szintűt](https://en.wikipedia.org/wiki/Syslog#Severity_level) kell használniuk. Választható. |
| regex | sztring | A megadott reguláris kifejezéssel egyező tartalmat tartalmazó naplófájlok szűrése a [.net reguláris kifejezési](/dotnet/standard/base-types/regular-expressions) formátum használatával. Választható. |
| kódolási | sztring | Vagy `gzip`, vagy `none`. Az alapértelmezett szint a `none`. |
| contentType | sztring | Vagy `json`, vagy `text`. Az alapértelmezett szint a `text`. |

> [!NOTE]
> Ha a naplók tartalma meghaladja a közvetlen metódusok (amely jelenleg 128 KB) értékét, a válasz hibát jelez.

A naplók sikeres lekérése **"status" (állapot** ) értéket ad vissza: 200, amelyet a modulból beolvasott naplókat tartalmazó adattartalom követ, amelyet a kérésben megadott beállítások szűrnek.

Például:

```azurecli
az iot hub invoke-module-method --method-name 'GetModuleLogs' -n <hub name> -d <device id> -m '$edgeAgent' --method-payload \
'
    {
       "schemaVersion": "1.0",
       "items": [
          {
             "id": "edgeAgent",
             "filter": {
                "tail": 10
             }
          }
       ],
       "encoding": "none",
       "contentType": "text"
    }
'
```

A Azure Portal hívja meg a metódust a metódus nevével `GetModuleLogs` és a következő JSON-adattartalommal:

```json
    {
       "schemaVersion": "1.0",
       "items": [
          {
             "id": "edgeAgent",
             "filter": {
                "tail": 10
             }
          }
       ],
       "encoding": "none",
       "contentType": "text"
    }
```

![Közvetlen "GetModuleLogs" metódus hívása Azure Portal](./media/how-to-retrieve-iot-edge-logs/invoke-get-module-logs.png)

A CLI kimenetét a Linux-segédprogramok, például a [gzip](https://en.wikipedia.org/wiki/Gzip)használatával is elvégezheti a tömörített válaszok feldolgozásához. Például:

```azurecli
az iot hub invoke-module-method \
  --method-name 'GetModuleLogs' \
  -n <hub name> \
  -d <device id> \
  -m '$edgeAgent' \
  --method-payload '{"contentType": "text","schemaVersion": "1.0","encoding": "gzip","items": [{"id": "edgeHub","filter": {"since": "2d","tail": 1000}}],}' \
  -o tsv --query 'payload[0].payloadBytes' \
  | base64 --decode \
  | gzip -d
```

## <a name="upload-module-logs"></a>Modul-naplók feltöltése

A **UploadModuleLogs** Direct metódus használatával küldje el a kért naplókat egy adott Azure Blob Storage-tárolóba.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"

> [!NOTE]
> Ha a naplókat egy eszköz mögötti eszközről szeretné feltölteni, a felső rétegbeli eszközön konfigurálnia kell az [API-proxyt és a blob Storage-modult](how-to-configure-api-proxy-module.md) . Ezek a modulok a naplókat az alsóbb rétegbeli eszközről az átjáró-eszközön keresztül a Felhőbeli tárolóba irányítják.

::: moniker-end

Ez a metódus a **GetModuleLogs** hasonló JSON-adattartalmat fogad el, a "sas URL" kulcs hozzáadásával:

```json
    {
       "schemaVersion": "1.0",
       "sasUrl": "Full path to SAS URL",
       "items": [
          {
             "id": "regex string",
             "filter": {
                "tail": "int",
                "since": "string",
                "until": "string",
                "loglevel": "int",
                "regex": "regex string"
             }
          }
       ],
       "encoding": "gzip/none",
       "contentType": "json/text" 
    }
```

| Név | Típus | Leírás |
|-|-|-|
| Sas URL | karakterlánc (URI) | Az [Azure Blob Storage tárolóhoz írási hozzáféréssel rendelkező közös hozzáférés-aláírási URL-cím](/archive/blogs/jpsanders/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer). |

A naplók feltöltésére vonatkozó sikeres kérelem **"állapotot" ad vissza: 200** , amelyet a következő sémával rendelkező tartalom követ:

```json
    {
        "status": "string",
        "message": "string",
        "correlationId": "GUID"
    }
```

| Név | Típus | Leírás |
|-|-|-|
| status | sztring | Az egyik,,,, `NotStarted` `Running` `Completed` `Failed` vagy `Unknown` . |
| message | sztring | Hibaüzenet, ha hiba, üres karakterlánc. |
| correlationId | sztring   | A feltöltési kérelem állapotára lekérdezni kívánt azonosító. |

Például:

A következő hívás az összes modul utolsó 100 sorát tölti fel tömörített JSON formátumban:

```azurecli
az iot hub invoke-module-method --method-name UploadModuleLogs -n <hub name> -d <device id> -m \$edgeAgent --method-payload \
'
    {
        "schemaVersion": "1.0",
        "sasUrl": "<sasUrl>",
        "items": [
            {
                "id": ".*",
                "filter": {
                    "tail": 100
                }
            }
        ],
        "encoding": "gzip",
        "contentType": "json"
    }
'
```

A következő meghívás az utolsó 100-es naplófájlt tölti fel a edgeAgent és a edgeHub-ből a tempSensor-modul utolsó 1000-naplójából a nem tömörített szöveg formátumban:

```azurecli
az iot hub invoke-module-method --method-name UploadModuleLogs -n <hub name> -d <device id> -m \$edgeAgent --method-payload \
'
    {
        "schemaVersion": "1.0",
        "sasUrl": "<sasUrl>",
        "items": [
            {
                "id": "edge",
                "filter": {
                    "tail": 100
                }
            },
            {
                "id": "tempSensor",
                "filter": {
                    "tail": 1000
                }
            }
        ],
        "encoding": "none",
        "contentType": "text"
    }
'
```

A Azure Portalban hívja meg a metódust a metódus nevével `UploadModuleLogs` és a következő JSON-adattartalommal, miután feltöltte a sas URL az adataival:

```json
    {
       "schemaVersion": "1.0",
       "sasUrl": "<sasUrl>",
       "items": [
          {
             "id": "edgeAgent",
             "filter": {
                "tail": 10
             }
          }
       ],
       "encoding": "none",
       "contentType": "text"
    }
```

![Közvetlen "UploadModuleLogs" metódus hívása Azure Portal](./media/how-to-retrieve-iot-edge-logs/invoke-upload-module-logs.png)

## <a name="upload-support-bundle-diagnostics"></a>Támogatási csomag diagnosztika feltöltése

A **UploadSupportBundle** Direct metódussal IoT Edge modul naplófájljainak zip-fájlját csomagolhatja és töltheti fel egy elérhető Azure Blob Storage-tárolóba. Ez a közvetlen metódus futtatja a [`iotedge support-bundle`](./troubleshoot.md#gather-debug-information-with-support-bundle-command) parancsot a IoT Edge eszközön a naplók beszerzéséhez.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"

> [!NOTE]
> Ha a naplókat egy eszköz mögötti eszközről szeretné feltölteni, a felső rétegbeli eszközön konfigurálnia kell az [API-proxyt és a blob Storage-modult](how-to-configure-api-proxy-module.md) . Ezek a modulok a naplókat az alsóbb rétegbeli eszközről az átjáró-eszközön keresztül a Felhőbeli tárolóba irányítják.

::: moniker-end

Ez a metódus egy JSON-adattartalmat fogad el a következő sémával:

```json
    {
        "schemaVersion": "1.0",
        "sasUrl": "Full path to SAS url",
        "since": "2d",
        "until": "1d",
        "edgeRuntimeOnly": false
    }
```

| Név | Típus | Leírás |
|-|-|-|
| sémaverzióval | sztring | Beállítás értéke `1.0` |
| Sas URL | karakterlánc (URI) | [Az Azure Blob Storage tárolóhoz írási hozzáféréssel rendelkező közös hozzáférés-aláírási URL-cím](/archive/blogs/jpsanders/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer) |
| mivel | sztring | A naplók csak ennyi idő után térnek vissza, mint az időtartam (1 d, 90 m, 2 nap 3 óra 2 perc), rfc3339 timestamp vagy UNIX timestamp. Választható. |
| amíg | sztring | Csak a megadott időpont előtt, a rfc3339 timestamp, a UNIX timestamp vagy az időtartam (1 d, 90 m, 2 nap 3 óra 2 perc) után ad vissza naplókat. Választható. |
| edgeRuntimeOnly | boolean | Ha az értéke igaz, csak a Edge-ügynök, az Edge hub és a Edge biztonsági démon naplóit kell visszaadnia. Alapértelmezett érték: false (hamis).  Választható. |

> [!IMPORTANT]
> IoT Edge támogatási csomag személyazonosításra alkalmas adatokat is tartalmazhat.

A naplók feltöltésére vonatkozó sikeres kérelem **"állapotot" ad vissza: 200** , amelyet a **UploadModuleLogs** -választal megegyező sémával rendelkező tartalom követ:

```json
    {
        "status": "string",
        "message": "string",
        "correlationId": "GUID"
    }
```

| Név | Típus | Leírás |
|-|-|-|
| status | sztring | Az egyik,,,, `NotStarted` `Running` `Completed` `Failed` vagy `Unknown` . |
| message | sztring | Hibaüzenet, ha hiba, üres karakterlánc. |
| correlationId | sztring   | A feltöltési kérelem állapotára lekérdezni kívánt azonosító. |

Például:

```azurecli
az iot hub invoke-module-method --method-name 'UploadSupportBundle' -n <hub name> -d <device id> -m '$edgeAgent' --method-payload \
'
    {
        "schemaVersion": "1.0",
        "sasUrl": "Full path to SAS url",
        "since": "2d",
        "until": "1d",
        "edgeRuntimeOnly": false
    }
'
```

A Azure Portalban hívja meg a metódust a metódus nevével `UploadSupportBundle` és a következő JSON-adattartalommal, miután feltöltte a sas URL az adataival:

```json
    {
        "schemaVersion": "1.0",
        "sasUrl": "Full path to SAS url",
        "since": "2d",
        "until": "1d",
        "edgeRuntimeOnly": false
    }
```

![Közvetlen "UploadSupportBundle" metódus hívása Azure Portal](./media/how-to-retrieve-iot-edge-logs/invoke-upload-support-bundle.png)

## <a name="get-upload-request-status"></a>Feltöltési kérelem állapotának beolvasása

A **GetTaskStatus** Direct metódus használatával kérdezheti le a feltöltési naplókra vonatkozó kérések állapotát. A **GetTaskStatus** -kérelem hasznos `correlationId` adatai a feltöltési naplók kérelem használatával kapják meg a feladat állapotát. A a `correlationId` **UploadModuleLogs** Direct metódus hívására adott válaszként.

Ez a metódus egy JSON-adattartalmat fogad el a következő sémával:

```json
    {
      "schemaVersion": "1.0",
      "correlationId": "<GUID>"
    }
```

A naplók feltöltésére vonatkozó sikeres kérelem **"állapotot" ad vissza: 200** , amelyet a **UploadModuleLogs** -választal megegyező sémával rendelkező tartalom követ:

```json
    {
        "status": "string",
        "message": "string",
        "correlationId": "GUID"
    }
```

| Név | Típus | Leírás |
|-|-|-|
| status | sztring | Az egyik,,,, `NotStarted` `Running` `Completed` `Failed` vagy `Unknown` . |
| message | sztring | Hibaüzenet, ha hiba, üres karakterlánc. |
| correlationId | sztring   | A feltöltési kérelem állapotára lekérdezni kívánt azonosító. |

Például:

```azurecli
az iot hub invoke-module-method --method-name 'GetTaskStatus' -n <hub name> -d <device id> -m '$edgeAgent' --method-payload \
'
    {
      "schemaVersion": "1.0",
      "correlationId": "<GUID>"
    }
'
```

A Azure Portalban hívja meg a metódust a metódus nevével `GetTaskStatus` és a következő JSON-adattartalommal, miután kitöltötte a GUID azonosítóját az adataival:

```json
    {
      "schemaVersion": "1.0",
      "correlationId": "<GUID>"
    }
```

![Közvetlen "GetTaskStatus" metódus hívása Azure Portal](./media/how-to-retrieve-iot-edge-logs/invoke-get-task-status.png)

## <a name="next-steps"></a>Következő lépések

[A IoT Edge-ügynök és az IoT Edge hub-modulok ikrek tulajdonságai](module-edgeagent-edgehub.md)
