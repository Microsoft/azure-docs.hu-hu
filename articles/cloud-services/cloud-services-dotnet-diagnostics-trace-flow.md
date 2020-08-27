---
title: A folyamat nyomon követése Cloud Services alkalmazásban Azure Diagnostics
titleSuffix: Azure Cloud Services
description: Nyomkövetési üzenetek hozzáadása egy Azure-alkalmazáshoz a hibakereséshez, a teljesítmény méréséhez, a figyeléshez, a forgalom elemzéséhez és egyebekhez.
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.devlang: dotnet
ms.custom: devx-track-dotnet
ms.topic: article
ms.date: 02/20/2016
ms.author: tagore
ms.openlocfilehash: 8270766413729454181c461d469d49e418a1aa67
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88932304"
---
# <a name="trace-the-flow-of-a-cloud-services-application-with-azure-diagnostics"></a>Cloud Services alkalmazás folyamatának nyomon követése Azure Diagnostics
A nyomkövetési módszer segítségével figyelheti az alkalmazás végrehajtását a futás közben. A [System. Diagnostics. Trace](/dotnet/api/system.diagnostics.trace), a [System. Diagnostics. debug](/dotnet/api/system.diagnostics.debug)és a [System. Diagnostics. TraceSource](/dotnet/api/system.diagnostics.tracesource) osztályok segítségével rögzítheti a hibákkal és az alkalmazások végrehajtásával kapcsolatos információkat a naplókban, szövegfájlokban vagy más eszközökben későbbi elemzés céljából. További információ a nyomkövetésről: [alkalmazások nyomon követése és](/dotnet/framework/debug-trace-profile/tracing-and-instrumenting-applications)kialakítása.

## <a name="use-trace-statements-and-trace-switches"></a>Nyomkövetési utasítások és nyomkövetési kapcsolók használata
Hajtsa végre a nyomkövetést a Cloud Services alkalmazásban úgy, hogy hozzáadja a [DiagnosticMonitorTraceListener](/previous-versions/azure/reference/ee758610(v=azure.100)) az alkalmazás konfigurációjához, és hívásokat kezdeményez a System. Diagnostics. Trace vagy System. Diagnostics. debug fájlhoz az alkalmazás kódjában. Használja a *app.config* konfigurációs fájlját a feldolgozói szerepkörökhöz és a webes szerepkörökhöz tartozó *web.confighoz * . Amikor egy Visual Studio-sablonnal hoz létre egy új üzemeltetett szolgáltatást, a rendszer automatikusan hozzáadja Azure Diagnostics a projekthez, és a DiagnosticMonitorTraceListener hozzáadja a megfelelő konfigurációs fájlhoz a hozzáadott szerepkörökhöz.

A nyomkövetési utasítások elhelyezésével kapcsolatos további információkért lásd [: útmutató: nyomkövetési utasítások hozzáadása az alkalmazás kódjához](/dotnet/framework/debug-trace-profile/how-to-add-trace-statements-to-application-code).

A [nyomkövetési kapcsolók](/dotnet/framework/debug-trace-profile/trace-switches) a kódban való elhelyezésével szabályozhatja, hogy a nyomkövetés bekövetkezik-e, és hogy mennyire kiterjedt. Ez lehetővé teszi az alkalmazás állapotának figyelését éles környezetben. Ez különösen fontos egy olyan üzleti alkalmazásban, amely több számítógépen futó több összetevőt használ. További információkért lásd [: útmutató: nyomkövetési kapcsolók konfigurálása](/dotnet/framework/debug-trace-profile/how-to-create-initialize-and-configure-trace-switches).

## <a name="configure-the-trace-listener-in-an-azure-application"></a>Nyomkövetési figyelő konfigurálása Azure-alkalmazásokban
Nyomkövetési, hibakeresési és TraceSource az elküldött üzenetek összegyűjtéséhez és rögzítéséhez "figyelőket" kell beállítania. A figyelők a nyomkövetési üzeneteket gyűjtik, tárolják és irányítják. A nyomkövetés kimenetét egy megfelelő célra irányítják, például egy naplóba, egy ablakba vagy szövegfájlba. Azure Diagnostics a [DiagnosticMonitorTraceListener](/previous-versions/azure/reference/ee758610(v=azure.100)) osztályt használja.

Az alábbi eljárás végrehajtása előtt inicializálnia kell az Azure diagnosztikai figyelőt. Ehhez tekintse meg a [diagnosztika engedélyezése a Microsoft Azure-ben](cloud-services-dotnet-diagnostics.md)című témakört.

Vegye figyelembe, hogy ha a Visual Studio által biztosított sablonokat használja, a figyelő konfigurációját a rendszer automatikusan hozzáadja Önnek.

### <a name="add-a-trace-listener"></a>Nyomkövetési figyelő hozzáadása
1. Nyissa meg a szerepkörhöz tartozó web.config vagy app.config fájlt.
2. Adja hozzá a következő kódot a fájlhoz. Módosítsa a Version attribútumot úgy, hogy az a szerelvény verziószámát használja, amelyre hivatkozik. A szerelvény verziója nem feltétlenül változik az egyes Azure SDK-kiadásokkal, hacsak nincs frissítés.
   
    ```
    <system.diagnostics>
        <trace>
            <listeners>
                <add type="Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitorTraceListener,
                  Microsoft.WindowsAzure.Diagnostics,
                  Version=2.8.0.0,
                  Culture=neutral,
                  PublicKeyToken=31bf3856ad364e35"
                  name="AzureDiagnostics">
                    <filter type="" />
                </add>
            </listeners>
        </trace>
    </system.diagnostics>
    ```
   > [!IMPORTANT]
   > Győződjön meg arról, hogy rendelkezik a Microsoft. WindowsAzure. Diagnostics szerelvényre mutató projekttel. Frissítse a fenti XML verziószámát, hogy megegyezzen a hivatkozott Microsoft. WindowsAzure. Diagnostics szerelvény verziójával.
   > 
   > 
3. Mentse a konfigurációs fájlt.

További információ a figyelőkről: [nyomkövetési figyelők](/dotnet/framework/debug-trace-profile/trace-listeners).

Miután elvégezte a figyelő hozzáadásának lépéseit, nyomkövetési utasításokat adhat hozzá a kódhoz.

### <a name="to-add-trace-statement-to-your-code"></a>Nyomkövetési utasítás hozzáadása a kódhoz
1. Nyisson meg egy forrásfájlt az alkalmazáshoz. Például a \<RoleName> . cs fájl a feldolgozói szerepkörhöz vagy a webes szerepkörhöz.
2. Adja hozzá a következő using direktívát, ha még nincs hozzáadva:
    ```
        using System.Diagnostics;
    ```
3. Adja meg azokat a nyomkövetési utasításokat, amelyekben adatokat szeretne rögzíteni az alkalmazás állapotáról. A nyomkövetési utasítás kimenetének formázásához többféle módszert is használhat. További információkért lásd [: útmutató: nyomkövetési utasítások hozzáadása az alkalmazás kódjához](/dotnet/framework/debug-trace-profile/how-to-add-trace-statements-to-application-code).
4. Mentse a forrásfájlt.




