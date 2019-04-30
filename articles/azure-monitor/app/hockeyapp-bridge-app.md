---
title: Az Azure Application Insights a HockeyApp-adatok feltárása |} A Microsoft Docs
description: Használatát és teljesítményét az Azure-alkalmazáshoz az Application insights segítségével elemezheti.
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: 97783cc6-67d6-465f-9926-cb9821f4176e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/30/2017
ms.author: mbullwin
ms.openlocfilehash: 79adfbfde25903bfe92c94507071c9d0fe303ef1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60898714"
---
# <a name="exploring-hockeyapp-data-in-application-insights"></a>Az Application Insights a HockeyApp-adatok feltárása

> [!NOTE]
> Új alkalmazások hockeyapp szolgáltatással már nem érhető el. HockeyApp meglévő telepítések továbbra is működni fog. A Visual Studio App Center mostantól a Microsoft ajánlott szolgáltatása új mobile Apps-alkalmazások figyelésére. [Ismerje meg, hogyan állítható be az alkalmazásokat az App Center és az Application Insights](../../azure-monitor/learn/mobile-center-quickstart.md).

[A HockeyApp](https://azure.microsoft.com/services/hockeyapp/) egy szolgáltatás élő asztali és mobil alkalmazások figyelésére. A HockeyApp-küldhet egyéni és nyomkövetési telemetriai használat monitorozása és diagnosztizálása (mellett összeomlási adatok beolvasása) segítik. Ez az adatfolyam telemetria kérdezhetők a nagy teljesítményű [Analytics](../../azure-monitor/app/analytics.md) funkcióját [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). Emellett akkor is [exportálja az egyéni és nyomkövetési telemetriai](export-telemetry.md). Ahhoz, hogy ezeket a funkciókat, beállíthatja, hogy az Application Insights a HockeyApp egyéni adatokat közvetítenek hidat.

## <a name="the-hockeyapp-bridge-app"></a>A HockeyApp Bridge alkalmazás
A HockeyApp Bridge alkalmazás funkciója a core, az egyéni HockeyApp és az Application Insights – híváslánc-telemetria hozzáférhet az elemzési és a folyamatos exportálás funkciók révén. A HockeyApp Bridge alkalmazás létrehozása után a HockeyApp által gyűjtött nyomkövetési és egyéni események ezeket a funkciókat elérhető lesz. Nézzük meg, hogyan állítható be egy híd alkalmazásokat.

A HockeyApp, nyissa meg a fiók beállításait, [API-jogkivonatai](https://rink.hockeyapp.net/manage/auth_tokens). Hozzon létre egy új jogkivonatot, vagy újra felhasználhatja egy már meglévőt. A minimális jogosultságok szükséges "csak olvashatók". Készítsen róla egy másolatot az API-token.

![A HockeyApp API token beszerzése](./media/hockeyapp-bridge-app/01.png)

A Microsoft Azure portal megnyitásához és [hozzon létre egy Application Insights-erőforrást](../../azure-monitor/app/create-new-resource.md ). Az alkalmazástípus állítsa "HockeyApp bridge alkalmazás":

![Új Application Insights-erőforrás](./media/hockeyapp-bridge-app/02.png)

Nem kell beállítani egy nevét – ez lesz automatikusan közötti értékre lehet beállítani a HockeyApp nevét.

A HockeyApp bridge mező jelenik meg. 

![Adja meg a híd mezők](./media/hockeyapp-bridge-app/03.png)

Adja meg a korábban feljegyzett HockeyApp-jogkivonatot. Ez a művelet feltölti a "HockeyApp Application" legördülő menüből a HockeyApp-alkalmazásokkal. Válassza ki a használni kívánt, és végezze el a többi mezőjét. 

Nyissa meg az új erőforrás. 

Vegye figyelembe, hogy az adatok eltarthat áramló elindításához.

![Várakozás az adatok Application Insights-erőforrás](./media/hockeyapp-bridge-app/04.png)

Ennyi az egész! Ettől kezdve a HockeyApp-kialakítva alkalmazásban gyűjtött és egyéni nyomkövetési adatok jelenleg is az Application Insights Analytics és a folyamatos exportálás funkcióit érhető el.

Röviden vizsgáljuk meg ezeket a funkciókat elérhető.

## <a name="analytics"></a>Elemzés
Analytics az ad hoc lekérdezését az adatokról, diagnosztizálása és a telemetria elemzését, és gyorsan felderítése kiváltó okokért és minták lehetővé teszi a hatékony eszközzel.

![Elemzés](./media/hockeyapp-bridge-app/05.png)

* [További információ Analytics szolgáltatásról](../../azure-monitor/log-query/get-started-portal.md)

## <a name="continuous-export"></a>Folyamatos exportálás
A folyamatos exportálás lehetővé teszi az adatok exportálása az Azure Blob Storage-tárolókat. Ez nagyon hasznos, ha az adatok hosszabb a megőrzési időtartam, az Application Insights által jelenleg kínált van szüksége. Is megőrizni az adatokat blob storage-ban, SQL-adatbázis, vagy az előnyben részesített adattárház-megoldás feldolgozni azt.

[További tudnivalók a folyamatos exportálás](export-telemetry.md)

## <a name="next-steps"></a>További lépések
* [Az adatok elemzési vonatkozik](../../azure-monitor/log-query/get-started-portal.md)

