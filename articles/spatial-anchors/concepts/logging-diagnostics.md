---
title: Naplózás és diagnosztika
description: Részletes magyarázat a naplózás és a diagnosztika létrehozásához és lekéréséhez az Azure térbeli Horgonyokban.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: da8ffd7ff0b8473ce558943bb420b36f26c3fc32
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "95494641"
---
# <a name="logging-and-diagnostics-in-azure-spatial-anchors"></a>Naplózás és diagnosztika az Azure térbeli Horgonyokban

Az Azure térbeli horgonyok egy szabványos naplózási mechanizmust biztosítanak, amely hasznos az alkalmazások fejlesztéséhez. A térbeli horgonyok diagnosztikai naplózási módja akkor hasznos, ha további információra van szüksége a hibakereséshez. A diagnosztikai naplózás a környezet lemezképeit tárolja.

## <a name="standard-logging"></a>Szabványos naplózás
A térbeli horgonyok API-ban előfizethet a naplózási mechanizmusra, hogy hasznos naplókat kapjon az alkalmazások fejlesztéséhez és hibakereséséhez. A standard naplózási API-k nem tárolnak képeket a környezetről az eszköz lemezén. Az SDK ezeket a naplókat esemény-visszahívásként biztosítja. A naplók integrálása az alkalmazás naplózási mechanizmusba.

### <a name="configuration-of-log-messages"></a>A naplóüzenetek konfigurálása
A felhasználó számára két visszahívási számot kell megkeresni. Az alábbi példa bemutatja, hogyan konfigurálhatja a munkamenetet.

```csharp
    cloudSpatialAnchorSession = new CloudSpatialAnchorSession();
    . . .
    // set up the log level for the runtime session
    cloudSpatialAnchorSession.LogLevel = SessionLogLevel.Information;

    // configure the callback for the debug log
    cloudSpatialAnchorSession.OnLogDebug += CloudSpatialAnchorSession_OnLogDebug;

    // configure the callback for the error log
    cloudSpatialAnchorSession.Error += CloudSpatialAnchorSession_Error;
```

### <a name="events-and-properties"></a>Események és tulajdonságok

Ezek az események visszahívása a naplók és hibák feldolgozására szolgál a munkamenetből:

- [Naplózási szint](/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.loglevel): a futtatókörnyezetből fogadott események részletességi szintjét adja meg.
- [OnLogDebug](/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.onlogdebug): szabványos hibakeresési naplózási eseményeket biztosít.
- [Hiba](/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.error): olyan naplózási eseményeket biztosít, amelyekben a futtatókörnyezet hibát jelez.

## <a name="diagnostics-logging"></a>Diagnosztika naplózása

A naplózási műveletek szabványos működési módján kívül a térbeli horgonyok is diagnosztikai móddal rendelkeznek. A diagnosztikai mód rögzíti a környezet lemezképeit, és naplózza őket a lemezre. Ezt a módot használhatja bizonyos típusú problémák hibakereséséhez, például a hiba kiszámítható módon történő megkereséséhez. A diagnosztikai naplózás engedélyezése csak egy adott probléma újbóli létrehozásához. Ezután tiltsa le. Ha a szokásos módon futtatja az alkalmazásokat, ne engedélyezze a diagnosztikát.

A Microsofttal való támogatási interakció során a Microsoft képviselője megkérheti, hogy szeretne-e diagnosztikai csomagot küldeni a további vizsgálathoz. Ebben az esetben dönthet úgy, hogy engedélyezi a diagnosztika engedélyezését, és reprodukálja a problémát, hogy elküldje a diagnosztikai csomagot.

Ha Microsoft-képviselőtől kapott előzetes visszaigazolás nélkül küld el diagnosztikai naplót a Microsoftnak, a Küldés nem válaszol.

A következő részben bemutatjuk, hogyan engedélyezheti a diagnosztikai üzemmódot, és hogyan küldhet diagnosztikai naplókat a Microsoftnak.

### <a name="enable-diagnostics-logging"></a>Diagnosztikai naplózás engedélyezése

Ha engedélyezi a munkamenetet a diagnosztikai naplózáshoz, a munkamenetben lévő összes művelet a helyi fájlrendszerben lévő diagnosztikai naplózással rendelkezik. A naplózás során a rendszer menti a környezet lemezképeit a lemezre.

```csharp
private void ConfigureSession()
{
    cloudSpatialAnchorSession = new CloudSpatialAnchorSession();
    . . .

    // set up the log level for the runtime session
    cloudSpatialAnchorSession.LogLevel = SessionLogLevel.Information;

    // configure the callbacks for logging and errors
    cloudSpatialAnchorSession.OnLogDebug += CloudSpatialAnchorSession_OnLogDebug;
    cloudSpatialAnchorSession.Error += CloudSpatialAnchorSession_Error;

    // opt in to diagnostics logging of environment images
    // if this is enabled, the diagnostics bundle includes images of the environment captured by the session
    cloudSpatialAnchorSession.Diagnostics.ImagesEnabled = true;

    // set the level of detail to be collected in the diagnostics log by the session
    cloudSpatialAnchorSession.Diagnostics.LogLevel = SessionLogLevel.All;

    // set the max bundle size to capture the bug information
    cloudSpatialAnchorSession.Diagnostics.MaxDiskSizeInMB = 200;
    . . .
}
```

### <a name="submit-the-diagnostics-bundle"></a>A diagnosztikai csomag elküldése

A következő kódrészlet bemutatja, hogyan küldhet be diagnosztikai csomagokat a Microsoftnak. Ez a csomag a diagnosztika engedélyezése után a munkamenet által rögzített környezet lemezképeit is tartalmazza.

```csharp
// method to handle the diagnostics bundle submission
private async Task CreateAndSubmitBundle()
{
    // create the diagnostics bundle manifest to collect the session information
    string path = await cloudSpatialAnchorSession
                              .Diagnostics
                              .CreateManifestAsync("Description of the issue");

    // submit the manifest and data to send feedback to Microsoft
    await cloudSpatialAnchorSession.Diagnostics.SubmitManifestAsync(path);
}
```

### <a name="parts-of-a-diagnostics-bundle"></a>Diagnosztikai csomag részei
A diagnosztikai csomag a következő információkat tartalmazza:

- **Kulcsképek lemezképei**: a munkamenet során rögzített környezet képei a diagnosztika engedélyezése közben.
- **Naplók**: a futtatókörnyezet által rögzített események naplózása.
- **Munkamenet metaadatainak**: a munkamenetet azonosító metaadatok.