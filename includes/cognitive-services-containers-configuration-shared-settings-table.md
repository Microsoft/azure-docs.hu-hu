---
author: IEvangelist
ms.author: dapine
ms.date: 10/02/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: a6f399e19cadf3d6ce9edfaecb3d904e62c498aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "96006873"
---
A tároló a következő konfigurációs beállításokkal rendelkezik:

|Kötelező|Beállítás|Rendeltetés|
|--|--|--|
|Yes|[ApiKey](#apikey-configuration-setting)|A számlázási információk nyomon követése.|
|No|[ApplicationInsights](#applicationinsights-setting)|Lehetővé teszi az [Azure Application Insights](/azure/application-insights) telemetria támogatásának hozzáadását a tárolóhoz.|
|Yes|[Számlázás](#billing-configuration-setting)|Meghatározza a szolgáltatási erőforrás végpontjának URI-JÁT az Azure-ban.|
|Yes|[EULA](#eula-setting)| Azt jelzi, hogy elfogadta a tároló licencét.|
|No|[Fluentd](#fluentd-settings)|Naplózza a naplót, és opcionálisan metrikus adatokat is egy Fluent-kiszolgálóra.|
|No|HTTP-proxy|Konfigurál egy HTTP-proxyt a kimenő kérések elkészítése céljából.|
|No|[Logging](#logging-settings)|ASP.NET Core naplózási támogatást biztosít a tárolóhoz. |
|No|[Tartók](#mount-settings)|Adatokat olvas és ír a gazdagépről a tárolóba, a tárolóból pedig vissza a gazdagépre.|