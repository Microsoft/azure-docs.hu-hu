---
title: Service Health áttekintése | Microsoft Docs
description: Ismerje meg, hogyan biztosíthatja a Service Health egy testreszabható irányítópultot, amely nyomon követi az Azure-szolgáltatások állapotát azokon a régiókban, amelyeken ezeket használja.
ms.topic: conceptual
ms.date: 05/10/2019
ms.openlocfilehash: 8246b0ab93b95c13858e4ff96d0f24b255d05e55
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90967783"
---
# <a name="service-health-overview"></a>Service Health áttekintése

A Service Health testreszabható irányítópultot biztosít, amely nyomon követi az Azure-szolgáltatások állapotát azokon a régiókban, amelyeken azokat használja. Ezen az irányítópulton nyomon követheti az aktív eseményeket, például a folyamatos szolgáltatással kapcsolatos problémákat, a közelgő tervezett karbantartást vagy az érintett egészségügyi tanácsadókat. Ha az események inaktívvá válnak, a rendszer akár 90 napig helyezi el az állapotát. Végezetül használhatja a Service Health irányítópultot a szolgáltatás állapotával kapcsolatos riasztások létrehozásához és kezeléséhez, amelyek proaktívan értesítik Önt, ha a szolgáltatási problémák érintik Önt.

## <a name="service-health-events"></a>Események Service Health

A Service Health négy olyan típusú eseményt követ nyomon, amelyek hatással lehetnek az erőforrásokra:

1. **Szolgáltatásokkal kapcsolatos problémák** – az Azure-szolgáltatások által jelenleg érintett problémák. 
2. **Tervezett karbantartás** – közelgő karbantartás, amely hatással lehet a szolgáltatások rendelkezésre állására a jövőben.  
3. **Egészségügyi tanácsadók** – a figyelmet igénylő Azure-szolgáltatások változásai. Ilyenek például az Azure-szolgáltatások vagy a frissítési követelmények elavulása (például egy támogatott PHP-keretrendszerre való frissítés).
4. **Biztonsági tanácsadók** – a biztonsággal kapcsolatos értesítések vagy szabálysértések, amelyek befolyásolhatják az Azure-szolgáltatások rendelkezésre állását.

> [!NOTE]
> Service Health események megtekintéséhez a felhasználóknak meg kell [adni az olvasó szerepkört az](../role-based-access-control/role-assignments-portal.md) előfizetésben.

## <a name="get-started-with-service-health"></a>Ismerkedés a Service Health

A Service Health irányítópult elindításához válassza a portál irányítópultján a Service Health csempét. Ha korábban eltávolította a csempét, vagy egyéni irányítópultot használ, keressen rá Service Health szolgáltatásra a "További szolgáltatások" területen (az irányítópulton balra lent).

![Ismerkedés a Service Health](./media/service-health-overview/azure-service-health-overview-1.png)

## <a name="see-current-issues-which-impact-your-services"></a>Tekintse meg a szolgáltatásait érintő aktuális problémákat

A **szolgáltatással kapcsolatos problémák** nézet az Azure-szolgáltatásokban az erőforrásokat érintő esetleges problémákat mutatja. Megtudhatja, hogy mikor kezdődött el a probléma, és milyen szolgáltatásokat és régiókat érint. A legújabb frissítést is beolvashatja, hogy megtudja, mit tesz az Azure a probléma megoldásához. 

[![Szolgáltatási probléma kezelése](./media/service-health-overview/azure-service-health-overview-2.png)](./media/service-health-overview/azure-service-health-overview-2.png#lightbox)

A **lehetséges hatás** lapon megtekintheti a probléma által befolyásolható erőforrások adott listáját. Letöltheti ezeket az erőforrásokat tartalmazó CSV-listát a csapattal való megosztáshoz.

[![Szolgáltatási probléma kezelése – hatás](./media/service-health-overview/azure-service-health-overview-4.png)](./media/service-health-overview/azure-service-health-overview-4.png#lightbox)

## <a name="see-emerging-issues-which-may-impact-your-services"></a>Tekintse meg a felmerülő problémákat, amelyek hatással lehetnek a szolgáltatásaira

Előfordulhat, hogy az elterjedt szolgáltatásokkal kapcsolatos problémák az [Azure status lapra](https://status.azure.com) kerülnek, mielőtt a rendszer megcélozta az érintett ügyfelek számára a célként megadott kommunikációt. Annak biztosítása érdekében, hogy a Azure Service Health átfogó képet biztosítson az Ön számára esetlegesen felmerülő problémákról, az aktív Azure Status Page-problémák a *felmerülő problémák* Service Health. Ha egy esemény aktív az Azure status lapon, a rendszer az új problémák szalagcímét Service Health fogja megjelenni. A probléma részletes adatainak megtekintéséhez kattintson a szalagcímre.

![Új szolgáltatási probléma](./media/service-health-overview/azure-service-health-emerging-issue.png)

## <a name="get-links-and-downloadable-explanations"></a>Hivatkozások és letölthető magyarázatok beolvasása 

A probléma kezelésére szolgáló hivatkozásra kattintva kérheti le a problémát. A PDF-fájlok és a CSV-fájlok letöltése a megosztáshoz olyan személyekkel is megosztható, akik nem rendelkeznek hozzáféréssel a Azure Portalhoz.   

[![Szolgáltatási probléma kezelése – probléma kezelése](./media/service-health-overview/azure-service-health-overview-3.png)](./media/service-health-overview/azure-service-health-overview-3.png#lightbox)

## <a name="get-support-from-microsoft"></a>Támogatáskérés a Microsofttól

Forduljon az ügyfélszolgálathoz, ha az erőforrás a probléma megoldása után is rossz állapotban marad.  Használja az oldal jobb oldalán található támogatási hivatkozásokat.  

## <a name="pin-a-personalized-health-map-to-your-dashboard"></a>Személyre szabott állapotadatok rögzítése az irányítópulton

Az üzleti szempontból kritikus előfizetések, régiók és erőforrástípusok megjelenítéséhez Service Health szűrheti. Mentse a szűrőt, és rögzítse az állapotokat mutató, személyre szabott világtérképet a portál irányítópultján. 

[![Személyre szabott állapot-hozzárendelés szűrése](./media/service-health-overview/azure-service-health-overview-6a.png)](./media/service-health-overview/azure-service-health-overview-6a.png#lightbox)

![Személyre szabott állapot-hozzárendelés rögzítése](./media/service-health-overview/azure-service-health-overview-6b.png)

## <a name="configure-service-health-alerts"></a>A szolgáltatás állapotára vonatkozó riasztások konfigurálása

A Service Health integrálható Azure Monitorekkel, hogy e-mailben, szöveges üzenetben és webhook-értesítésekben figyelmeztessen az üzleti szempontból kritikus fontosságú erőforrások hatására. Hozzon létre egy műveletnapló riasztást a megfelelő szolgáltatási állapot eseményéhez. Irányítsa a riasztást a szervezet megfelelő tagjaira a műveleti csoportok használatával. További információ: [riasztások konfigurálása Service Healthhoz](./alerts-activity-log-service-notifications-portal.md)

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OaXt]

## <a name="next-steps"></a>Következő lépések

Riasztások beállítása, hogy értesítést kapjon az állapottal kapcsolatos problémákról. További információ: [ajánlott eljárások Azure Service Health riasztások beállításához](https://www.youtube.com/watch?v=k5d5ca8K6tc&list=PLLasX02E8BPBBSqygdRvlTnHfp1POwE8K&index=6&t=0s). 
