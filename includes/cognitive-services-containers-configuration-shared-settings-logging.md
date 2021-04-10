---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: a2a5935079a339e85713e9cbcd0f32c211cabbb5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105729751"
---
A `Logging` Beállítások kezelik ASP.net Core naplózási támogatást a tárolóhoz. Ugyanazokat a konfigurációs beállításokat és értékeket használhatja a tárolóhoz, amelyeket egy ASP.NET Core alkalmazáshoz használ. 

A tároló a következő naplózási szolgáltatókat támogatja:

|Szolgáltató|Cél|
|--|--|
|[Console](/aspnet/core/fundamentals/logging/#console-provider)|A ASP.NET Core `Console` naplózási szolgáltató. A naplózási szolgáltató összes ASP.NET Core konfigurációs beállítása és alapértelmezett értéke támogatott.|
|[Hibakeresés](/aspnet/core/fundamentals/logging/#debug-provider)|A ASP.NET Core `Debug` naplózási szolgáltató. A naplózási szolgáltató összes ASP.NET Core konfigurációs beállítása és alapértelmezett értéke támogatott.|
|[Lemez](#disk-logging)|A JSON-naplózási szolgáltató. Ez a naplózási szolgáltató a naplófájlba írja az adatokat a kimeneti csatlakoztatásba.|

Ez a tároló parancs JSON formátumban tárolja a naplózási adatokat a kimeneti csatlakoztatásra:

```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<endpoint> \
ApiKey=<api-key> \
Logging:Disk:Format=json
```

Ez a tároló-parancs a hibakeresési információit, a-előtagot jeleníti meg, `dbug` miközben a tároló fut:

```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
<registry-location>/<image-name> \
Eula=accept \
Billing=<endpoint> \
ApiKey=<api-key> \
Logging:Console:LogLevel:Default=Debug
```

### <a name="disk-logging"></a>Lemez naplózása

A `Disk` naplózási szolgáltató a következő konfigurációs beállításokat támogatja:

| Name | Adattípus | Leírás |
|------|-----------|-------------|
| `Format` | Sztring | A naplófájlok kimeneti formátuma.<br/> **Megjegyzés:** Ezt az értéket úgy kell beállítani, hogy `json` engedélyezze a naplózási szolgáltatót. Ha ez az érték meg van adva, anélkül, hogy a kimeneti csatlakoztatást is megadja egy tároló példányának létrehozásakor, hiba történik. |
| `MaxFileSize` | Egész szám | Egy naplófájl maximális mérete megabájtban (MB). Ha az aktuális naplófájl mérete megfelel vagy meghaladja ezt az értéket, a naplózási szolgáltató elindít egy új naplófájlt. Ha a-1 érték van megadva, a naplófájl méretét csak a kimeneti csatlakoztatáshoz tartozó Maximális fájlméret korlátozza. Az alapértelmezett érték az 1. |

További információ a ASP.NET Core naplózási támogatásának konfigurálásáról: [beállítások fájl konfigurálása](/aspnet/core/fundamentals/logging/).