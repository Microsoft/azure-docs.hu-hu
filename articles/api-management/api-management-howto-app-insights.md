---
title: Az Azure API Management integrálása az Azure-Application Insights
titleSuffix: Azure API Management
description: Megtudhatja, hogyan naplózhatja és tekintheti meg az Azure-API Management az Azure Application Insightsban.
services: api-management
author: mikebudzynski
ms.service: api-management
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/25/2021
ms.author: apimpm
ms.openlocfilehash: 97f4eb34b88b3454d65b65d236833e1256c98671
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103564270"
---
# <a name="how-to-integrate-azure-api-management-with-azure-application-insights"></a>Az Azure API Management integrációja az Azure Application Insightsszal

Az Azure API Management lehetővé teszi az Azure Application Insights egyszerű integrálását – a webes fejlesztők számára bővíthető szolgáltatás több platformon futó alkalmazások létrehozásához és kezeléséhez. Ez az útmutató végigvezeti az integráció minden lépésén, és leírja azokat a stratégiákat, amelyek csökkentik a teljesítményre gyakorolt hatást a API Management Service-példányon.

## <a name="prerequisites"></a>Előfeltételek

Az útmutató követéséhez Azure API Management-példánnyal kell rendelkeznie. Ha még nem rendelkezik ilyennel, először hajtsa végre az [oktatóanyagot](get-started-create-service-instance.md) .

## <a name="create-an-application-insights-instance"></a>Application Insights példány létrehozása

A Application Insights használata előtt először létre kell hoznia a szolgáltatás egy példányát. A példányok Azure Portal használatával történő létrehozásának lépéseiért lásd: [munkaterület-alapú Application Insights-erőforrások](../azure-monitor/app/create-workspace-resource.md).
## <a name="create-a-connection-between-application-insights-and-api-management"></a>Kapcsolat létrehozása Application Insights és API Management között

1. Navigáljon az **Azure API Management Service-példányhoz** a **Azure Portal**.
1. A bal oldali menüben válassza a **Application Insights** lehetőséget.
1. Kattintson a **+ Hozzáadás** gombra.  
    :::image type="content" source="media/api-management-howto-app-insights/apim-app-insights-logger-1.png" alt-text="Az új kapcsolatok hozzáadásának helyét bemutató képernyőkép":::
1. Válassza ki a korábban létrehozott **Application Insights** példányt, és adjon meg egy rövid leírást.
1. Kattintson a **Létrehozás** lehetőségre.
1. Most létrehozott egy Application Insights naplózó eszközt a kialakítási kulccsal. Ekkor meg kell jelennie a listában.  
    :::image type="content" source="media/api-management-howto-app-insights/apim-app-insights-logger-2.png" alt-text="Képernyőkép, amely bemutatja, hol tekinthetők meg az újonnan létrehozott Application Insights Tuskózó a kialakítási kulccsal":::

> [!NOTE]
> A jelenet mögött egy [naplózó](/rest/api/apimanagement/2019-12-01/logger/createorupdate) entitás jön létre a API Management-példányban, amely a Application Insights példány kialakítási kulcsát tartalmazza.

## <a name="enable-application-insights-logging-for-your-api"></a>Application Insights naplózás engedélyezése az API-hoz

1. Navigáljon az **Azure API Management Service-példányhoz** a **Azure Portal**.
1. A bal oldali menüből válassza ki az **API-k** elemet.
1. Kattintson az API-ra, ebben az esetben a **bemutató konferencia API**-ra. Ha be van állítva, válasszon egy verziót.
1. Nyissa meg a **Beállítások** lapot a felső sávon.
1. Görgessen le a **diagnosztikai naplók** szakaszhoz.  
    :::image type="content" source="media/api-management-howto-app-insights/apim-app-insights-api-1.png" alt-text="App-bepillantások naplózó":::
1. Jelölje be az **Engedélyezés** jelölőnégyzetet.
1. Válassza ki a csatolt naplózó elemet a **cél** legördülő menüben.
1. Adja meg a **100** bemenetet **mintavételezésként (%)** , és jelölje be a **mindig naplózott hibák** jelölőnégyzetet.
1. Kattintson a **Mentés** gombra.

> [!WARNING]
> Ha felülbírálja az alapértelmezett értéket ( **0** ), a naplózási értékhez tartozó **adattartalom-bájtok száma** jelentősen csökkentheti az API-k teljesítményét.

> [!NOTE]
> A jelenet mögött egy "applicationinsights" nevű [diagnosztikai](/rest/api/apimanagement/2019-12-01/diagnostic/createorupdate) entitás jön létre az API szintjén.

| Beállítás neve                        | Érték típusa                        | Description                                                                                                                                                                                                                                                                                                                                      |
|-------------------------------------|-----------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Engedélyezés                              | boolean                           | Megadja, hogy engedélyezve van-e az API naplózása.                                                                                                                                                                                                                                                                                                |
| Cél                         | Azure Application Insights Logger | A használandó Azure Application Insights-naplózó megadása                                                                                                                                                                                                                                                                                           |
| Mintavételezés (%)                        | tizedes tört                           | 0 és 100 közötti értékek (százalék). <br/> Meghatározza a Application Insights naplózni kívánt kérelmek százalékos arányát. 0%-os mintavételezés: a rendszer a naplózott nulla kérelmeket jelenti, míg a 100% mintavételezés az összes naplózott kérelmet jelenti. <br/> Ezzel a beállítással csökkentheti a teljesítményre gyakorolt hatást a Application Insights kérelmek naplózásakor. Lásd [a teljesítménnyel kapcsolatos szempontokat és a napló mintavételezését](#performance-implications-and-log-sampling). |
| Hibák naplózása mindig                   | boolean                           | Ha ez a beállítás be van jelölve, a rendszer minden hibát naplóz a Application Insightsba, a **mintavételezési** beállítástól függetlenül.   
| Ügyfél IP-címének naplózása | |  Ha ez a beállítás be van jelölve, az API-kérelmekhez tartozó ügyfél IP-címe a Application Insights lesz naplózva.                                         |
| Részletesség         |                                   | Megadja a részletességi szintet. A rendszer csak a magasabb súlyossági szintű egyéni nyomkövetéseket naplózza. Alapértelmezett: információ.      | 
| Korrelációs protokoll |  |  Válassza ki a több összetevő által eljuttatott telemetria korrelációhoz használt protokollt. Alapértelmezett: örökölt <br/>További információ: [telemetria korreláció a Application Insightsban](../azure-monitor/app/correlation.md).  |
| Alapszintű beállítások: naplózandó fejlécek              | list                              | Azokat a fejléceket adja meg, amelyeket a rendszer a kérelmek és válaszok Application Insights naplózni fog.  Alapértelmezett: nincsenek naplózva fejlécek.                                                                                                                                                                                                             |
| Alapszintű beállítások: a naplózni kívánt hasznos adatmennyiség bájtban | egész szám                           | Azt határozza meg, hogy a törzsnek hány első bájtja legyen naplózva a kérelmek és válaszok Application Insights.  Alapértelmezett: 0.                                                                                                                                                                                                    |
| Speciális beállítások: előtér-kérelem  |                                   | Meghatározza, hogy a rendszer hogyan naplózza Application Insights a *frontend-kérelmeket* . A *frontend-kérelem* az Azure API Management szolgáltatásba beérkező kérelem.                                                                                                                                                                        |
| Speciális beállítások: frontend-válasz |                                   | Megadja, hogy a rendszer naplózza-e a *frontend-válaszokat* Application Insightsba. A *frontend válasza* az Azure API Management szolgáltatásból kimenő válasz.                                                                                                                                                                   |
| Speciális beállítások: háttérbeli kérelem   |                                   | Megadja, hogy a rendszer naplózza-e a *háttérbeli kérelmeket* Application Insightsba. A *háttér-kérelem* az Azure API Management szolgáltatásból kimenő kérelem.                                                                                                                                                                        |
| Speciális beállítások: háttérbeli válasz  |                                   | Megadja, hogy a rendszer naplózza-e a *háttérbeli válaszokat* a Application Insightsba. A *háttérbeli válasz* az Azure API Management szolgáltatásba érkező válasz.                                                                                                                                                                       |

> [!NOTE]
> Különböző szinteken is megadhatja a naplózók – egyetlen API-naplózó vagy az összes API-hoz tartozó naplózó.
>  
> Ha mindkettőt megadja:
> + Ha különböző naplózók, akkor mindkettőt használni fogjuk (multiplex naplók),
> + Ha ugyanazok a naplózók, de eltérő beállításokkal rendelkeznek, akkor az önálló API-k (több szemcsés szint) esetében az egyik az összes API-t felülbírálja.

## <a name="what-data-is-added-to-application-insights"></a>A Application Insightshoz hozzáadott adatértékek

Application Insights fogadása:

+ Telemetria-elem *kérése* minden bejövő kérelemhez (előtér-*kérelem*, előtér- *Válasz*)
+ *Függőségi* telemetria elem, a háttér-szolgáltatásba továbbított minden kérelem esetében (*háttér-kérelem*, *háttérbeli válasz*),
+ *Kivétel* telemetria elem, minden sikertelen kérelem esetén:
    + lezárt ügyfélkapcsolat miatt nem sikerült
    + az API *-házirendek on-Error* szakaszának elindítása
    + a válasz HTTP-állapotkódot megfelelő 4xx vagy 5xx.
+ *Nyomkövetési* telemetria elem, ha [nyomkövetési](api-management-advanced-policies.md#Trace) házirendet konfigurál. A `severity` házirendben megadott beállításnak `trace` egyenlőnek vagy annál nagyobbnak kell lennie, mint a `verbosity` Application Insights naplózásban megadott beállításnak.

> [!NOTE]
> A Application Insights-példányok maximális méretével és számával, valamint a metrikák és események számával kapcsolatos információkért tekintse meg a [Application Insights korlátozásait](../azure-monitor/service-limits.md#application-insights) .

## <a name="performance-implications-and-log-sampling"></a>Teljesítményre gyakorolt hatás és a napló mintavételezése

> [!WARNING]
> Az összes esemény naplózása súlyos teljesítménybeli következményekkel járhat, a bejövő kérések számától függően.

A belső terhelési tesztek alapján a funkció engedélyezése 40%-50%-os csökkenést eredményezett az átviteli sebességben, amikor a kérelmek száma túllépte az 1 000 kérelmek másodpercenkénti számát. Application Insights az alkalmazások teljesítményének felmérésére szolgáló statisztikai elemzések használatára lett tervezve. Nem tekinthető naplózási rendszernek, és nem alkalmas arra, hogy a nagy mennyiségű API-ra vonatkozó egyedi kérelmeket naplózza.

A bejelentkezett kérelmek számát a **mintavételezési** beállítás módosításával módosíthatja (lásd az előző lépéseket). A 100% érték azt jelenti, hogy az összes kérelem naplózva van, míg a 0% nem tükrözi a naplózást. A **mintavétel** segít csökkenteni a telemetria mennyiségét, ami gyakorlatilag megakadályozza a teljesítmény jelentős romlását, miközben továbbra is a naplózás előnyeit hordozza.

A fejlécek és a kérelmek és válaszok naplózásának kihagyása pozitív hatással lesz a teljesítménnyel kapcsolatos problémák enyhítésére is.

## <a name="video"></a>Videó

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2pkXv]
>
>

## <a name="next-steps"></a>Következő lépések

+ További információ az [Azure Application Insightsról](/azure/application-insights/).
+ Vegye fontolóra [Az Azure Event Hubs való bejelentkezést](api-management-howto-log-event-hubs.md).
