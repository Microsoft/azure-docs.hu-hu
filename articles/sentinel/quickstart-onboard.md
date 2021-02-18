---
title: 'Gyors útmutató: Bevezetés az Azure Sentinelbe'
description: Ebből a rövid útmutatóból megtudhatja, hogyan teheti elérhetővé az Azure Sentinelt, ha először engedélyezi, majd összekapcsolja az adatforrásokat.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: d5750b3e-bfbd-4fa0-b888-ebfab7d9c9ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: quickstart
ms.date: 10/14/2020
ms.custom: references_regions
ms.openlocfilehash: a169b93b20df14e536d8764b5fb4c889d645a1fa
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100570410"
---
# <a name="quickstart-on-board-azure-sentinel"></a>Gyors útmutató: Azure Sentinel

Ebből a rövid útmutatóból megtudhatja, hogyan hozhatja ki a fedélzeten az Azure Sentinel szolgáltatást. 

Ahhoz, hogy a fedélzeti Azure Sentinel elérhető legyen, először engedélyeznie kell az Azure Sentinelt, majd össze kell kapcsolni az adatforrásokat. Az Azure Sentinel számos összekötőt tartalmaz a Microsoft-megoldások számára, és lehetővé teszi a valós idejű integrációt, beleértve a Microsoft 365 Defender (korábban Microsoft Threat Protection) megoldásokat, Microsoft 365 forrásokat (beleértve az Office 365-et), az Azure AD-t, a Microsoft Defender for Identity (korábbi nevén Azure ATP), Microsoft Cloud App Security, Azure Defender-riasztásokat Azure Security Center és sok más. Emellett beépített összekötők találhatók a nem Microsoft-megoldások szélesebb körű biztonsági ökoszisztémájában. A Common Event Format (CEF), a syslog vagy a REST-API használatával is összekapcsolhatók az adatforrások az Azure Sentinel szolgáltatással. 

Az adatforrások összekapcsolását követően válasszon egy, az adatok alapján felszínre felkészített munkafüzetekből álló gyűjteményt. Ezek a munkafüzetek könnyen testreszabhatók az igényei szerint.

>[!IMPORTANT] 
> Az Azure Sentinel használata során felmerülő költségekkel kapcsolatos információkért lásd: az [Azure Sentinel díjszabása](https://azure.microsoft.com/pricing/details/azure-sentinel/).

## <a name="global-prerequisites"></a>Globális előfeltételek

- Aktív Azure-előfizetés, ha még nem rendelkezik ilyennel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a Kezdés előtt.

- Log Analytics munkaterület. Megtudhatja, hogyan [hozhat létre log Analytics munkaterületet](../azure-monitor/logs/quick-create-workspace.md). További információ az Log Analytics munkaterületekről: [a Azure monitor naplók telepítésének megtervezése](../azure-monitor/logs/design-logs-deployment.md).

- Az Azure Sentinel engedélyezéséhez közreműködői engedélyekkel kell rendelkeznie ahhoz az előfizetéshez, amelyben az Azure Sentinel-munkaterület található. 
- Az Azure Sentinel használatához közreműködői vagy olvasói engedélyekre van szükség ahhoz az erőforráscsoporthoz, amelyhez a munkaterület tartozik.
- Bizonyos adatforrások összekapcsolásához további engedélyekre lehet szükség.
- Az Azure Sentinel fizetős szolgáltatás. A díjszabással kapcsolatos információkért lásd: [Az Azure Sentinel ismertetése](https://go.microsoft.com/fwlink/?linkid=2104058).

### <a name="geographical-availability-and-data-residency"></a>Földrajzi Elérhetőség és adattárolás

- Az Azure Sentinel [log Analytics legtöbb GA régiójában](https://azure.microsoft.com/global-infrastructure/services/?products=monitor) futtatható munkaterületeken, kivéve a kínai és a németországi (szuverén) régiókat. Néha új Log Analytics régiók is eltarthat egy ideig az Azure Sentinel szolgáltatás bevezetéséhez. 

- Az Azure Sentinel által létrehozott adatok, például az incidensek, a könyvjelzők és az elemzési szabályok tartalmazhatnak az ügyfél Log Analytics munkaterületeiről származó vásárlói adatforrásokat. Ez az Azure Sentinel által generált információ az alábbi táblázatban szereplő földrajzi helyen található, a munkaterület földrajzi elhelyezkedése szerint:

    | Munkaterület földrajza | Az Azure Sentinel által generált adatföldrajz |
    | --- | --- |
    | Egyesült Államok<br>India<br>Brazília<br>Afrika<br>Dél-Korea<br>Egyesült Arab Emírségek | Egyesült Államok |
    | Európa<br>Franciaország<br>Svájc | Európa |
    | Ausztrália | Ausztrália |
    | Egyesült Királyság | Egyesült Királyság |
    | Kanada | Kanada |
    | Japán | Japán |
    |

## <a name="enable-azure-sentinel"></a>Az Azure Sentinel engedélyezése <a name="enable"></a>

1. Jelentkezzen be az Azure portálra. Győződjön meg arról, hogy az Azure Sentinel-t létrehozó előfizetés van kiválasztva.

1. Keresse meg és válassza ki az **Azure Sentinel** elemet.

   ![Szolgáltatások keresése](./media/quickstart-onboard/search-product.png)

1. Válassza a **Hozzáadás** lehetőséget.

1. Válassza ki a használni kívánt munkaterületet, vagy hozzon létre egy újat. Az Azure Sentinel több munkaterületen is futtatható, de az adategység egyetlen munkaterületre van elkülönítve.

   ![Válasszon munkaterületet](./media/quickstart-onboard/choose-workspace.png)

   >[!NOTE] 
   > - A Azure Security Center által létrehozott alapértelmezett munkaterületek nem jelennek meg a listában; Az Azure Sentinel nem telepíthető rajtuk.
   >

   >[!IMPORTANT]
   >
   > - A munkaterületre való üzembe helyezést követően az Azure Sentinel **jelenleg nem támogatja** a munkaterület áthelyezését más erőforráscsoportok vagy előfizetések számára. 
   >
   >   Ha már áthelyezte a munkaterületet, tiltsa le az összes aktív szabályt az **elemzés** alatt, majd engedélyezze újra az öt perc után. Ennek a legtöbb esetben a lehető leghatékonyabbnak kell lennie, de a rendszer nem támogatja, és a saját felelősségére kerül.

1. Válassza az **Azure Sentinel hozzáadása** lehetőséget.

## <a name="connect-data-sources"></a>Adatforrások csatlakoztatása

Az Azure Sentinel a szolgáltatáshoz való csatlakozással és az események és naplók Azure Sentinelbe való továbbításával végzi el a szolgáltatások és alkalmazások adatainak betöltését. Fizikai és virtuális gépek esetén telepítheti a naplókat gyűjtő Log Analytics ügynököt, és továbbíthatja őket az Azure Sentinelnek. Tűzfalak és proxyk esetén az Azure Sentinel telepíti a Log Analytics ügynököt egy Linux syslog-kiszolgálón, amelyről az ügynök gyűjti a naplófájlokat, és továbbítja azokat az Azure Sentinelnek. 
 
1. A főmenüben válassza az **adatösszekötők** lehetőséget. Ekkor megnyílik az adatösszekötők gyűjteménye.

1. A katalógus felsorolja az összes olyan adatforrást, amelyhez csatlakozhat. Válasszon ki egy adatforrást, majd az **összekötő lap megnyitása** gombot.

1. Az összekötő lap az összekötő konfigurálására vonatkozó utasításokat, valamint az esetlegesen szükséges további utasításokat tartalmazza.<br>
Ha például bejelöli a **Azure Active Directory** adatforrást, amely lehetővé teszi, hogy az Azure ad-ból származó naplókat az Azure sentinelbe továbbítsa, kiválaszthatja, hogy milyen típusú naplókat szeretne beolvasni a bejelentkezési naplókba és/vagy a naplóba. <br> További információért kövesse a telepítési utasításokat, vagy [tekintse meg a megfelelő kapcsolódási útmutatót](connect-data-sources.md) . További információ az adatösszekötők használatáról: a [Microsoft szolgáltatásainak összekapcsolása](connect-data-sources.md).

1. Az összekötő oldal **következő lépések** lapján láthatók az adatösszekötőhöz kapcsolódó beépített munkafüzetek, példák és elemzési szabályok sablonjai. Ezeket használhatja a-ként, vagy módosíthatja őket – bármely módon azonnal érdekes elemzéseket kaphat az adatairól. <br>

Az adatforrások csatlakoztatása után az adatai streamet kezdenek az Azure Sentinelbe, és készen állnak a használat megkezdésére. Megtekintheti a naplókat a [beépített munkafüzetekben](quickstart-get-visibility.md) , és megkezdheti a lekérdezések létrehozását a log Analyticsban [az adatvizsgálathoz](tutorial-investigate-cases.md).

## <a name="next-steps"></a>Következő lépések
Ebből a dokumentumból megtudhatta, hogyan lehet bevezetni és csatlakoztatni az adatforrásokat az Azure Sentinel szolgáltatáshoz. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
- Adatok továbbítása a [Common Event Format készülékekről](connect-common-event-format.md) az Azure sentinelbe.
