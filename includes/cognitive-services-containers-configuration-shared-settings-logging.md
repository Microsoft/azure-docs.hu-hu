---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 7b0059dbaafe42200ebfa8dc65ea585a49c5b83f
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712510"
---
A `Logging` beállítások kezelése a tároló az ASP.NET Core-naplózás támogatása. A tárolót használja az ASP.NET Core alkalmazás használhatja az ugyanazon konfigurációs beállításokat és értékeket. 

A következő naplózási szolgáltatók támogatottak a tárolót:

|Szolgáltató|Cél|
|--|--|
|[Console](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#console-provider)|Az ASP.NET Core `Console` naplózási szolgáltató. Az ASP.NET Core-konfigurációs beállításokat és a naplózás szolgáltatóhoz tartozó alapértelmezett értékeket támogatottak.|
|[Hibakeresés](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#debug-provider)|Az ASP.NET Core `Debug` naplózási szolgáltató. Az ASP.NET Core-konfigurációs beállításokat és a naplózás szolgáltatóhoz tartozó alapértelmezett értékeket támogatottak.|
|[Lemez](#disk-logging)|A JSON-naplózás szolgáltató. A naplózás szolgáltató naplóadatokat ír a kimeneti csatlakoztatási.|

A tároló parancs naplózási információkat tárolja, a kimeneti csatlakoztatási JSON formátumban:

```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<billing-endpoint> \
ApiKey=<api-key> \
Logging:Disk:Format=json
```

A tároló a parancs megjeleníti a hibakeresési adatok előtaggal `dbug`, míg a tároló fut:

```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
<registry-location>/<image-name> \
Eula=accept \
Billing=<billing-endpoint> \
ApiKey=<api-key> \
Logging:Console:LogLevel:Default=Debug
```

### <a name="disk-logging"></a>Lemez naplózása

A `Disk` naplózási szolgáltató támogatja-e a következő beállításokat:  

| Name (Név) | Adattípus | Leírás |
|------|-----------|-------------|
| `Format` | Karakterlánc | A kimeneti formátum a rendszernapló fájljaiban.<br/> **Megjegyzés:** Ezt az értéket kell beállítani `json` a naplózás szolgáltatónak. Ezt az értéket egy kimeneti csatlakoztatási közben hárítható el egy tároló megadása nélkül, ha hiba történik. |
| `MaxFileSize` | Egész szám | A maximális méretét megabájtban (MB), a naplófájlok. Ha az aktuális naplófájl méretét megfelel-e vagy meghaladja ezt az értéket, egy új naplófájl indítja el a naplózás szolgáltató. Ha meg van adva a -1, a naplófájl méretét csak korlátozza a maximális méretet, ha bármely, a kimeneti csatlakoztatási. Az alapértelmezett érték az 1. |

ASP.NET Core-naplózás támogatást konfigurálásával kapcsolatos további információkért lásd: [fájl konfigurációs beállítások](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1).

