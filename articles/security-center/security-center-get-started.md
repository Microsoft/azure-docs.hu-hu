---
title: Frissítés az Azure Defenderre – Azure Security Center
description: Ez a rövid útmutató bemutatja, hogyan frissíthet a Security Center Azure Defender-ra további biztonság érdekében.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: 5e39093e0472705111907e72b70446db53770012
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100634488"
---
# <a name="quickstart-set-up-azure-security-center"></a>Gyors útmutató: Azure Security Center beállítása

Az Azure Security Center egységes biztonsági felügyeletet és fenyegetésvédelmet biztosít a hibrid felhőalapú számítási feladatokhoz. Habár az ingyenes funkciók csak korlátozott biztonságot biztosítanak az Azure-erőforrások számára, az Azure Defender lehetővé teszi, hogy a helyszíni és egyéb felhőkre is kiterjessze ezeket a képességeket. Az Azure Defender segítségével megtalálhatja és elháríthatja a biztonsági réseket, a hozzáférés-vezérlést és az alkalmazás-vezérlőket a kártékony tevékenységek blokkolására, az elemzések és az intelligencia használatával észlelt fenyegetések észlelésére, valamint a támadás Az Azure Defender szolgáltatás díjmentesen kipróbálható. További részletekért tekintse át az [árképzést ismertető oldalt](https://azure.microsoft.com/pricing/details/security-center/).

Ez a rövid útmutató bemutatja, hogyan engedélyezheti az Azure Defender számára a biztonságot, és telepítheti a Log Analytics-ügynököt a gépeken a biztonsági rések és fenyegetések figyelése érdekében.

A következő lépéseket kell végrehajtania:

> [!div class="checklist"]
> * Security Center engedélyezése az Azure-előfizetésben
> * Azure Defender engedélyezése az Azure-előfizetésben
> * Automatikus adatgyűjtés engedélyezése

## <a name="prerequisites"></a>Előfeltételek
A Security Center használatához Microsoft Azure-előfizetéssel kell rendelkeznie. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes fiókkal](https://azure.microsoft.com/pricing/free-trial/).

Az Azure Defender előfizetésre való engedélyezéséhez hozzá kell rendelnie az előfizetés tulajdonosa, az előfizetés közreműködője vagy a biztonsági rendszergazda szerepkört.


## <a name="enable-security-center-on-your-azure-subscription"></a>Security Center engedélyezése az Azure-előfizetésben

1. Jelentkezzen be az [Azure Portalra](https://azure.microsoft.com/features/azure-portal/).

1. A portál menüjében válassza a **Security Center** lehetőséget. 

    Megnyílik a Security Center áttekintése oldal.

    :::image type="content" source="./media/security-center-get-started/overview.png" alt-text="Security Center áttekintése irányítópult" lightbox="./media/security-center-get-started/overview.png":::

A **Security Center – Áttekintés** egységes képet ad a hibrid felhőbeli számítási feladatok biztonsági rendszeréről, lehetővé téve a számítási feladatok biztonsági állapotának felderítését és felmérését, valamint a kockázatok észlelését és csökkentését. Security Center automatikusan, díjmentesen lehetővé teszi, hogy az Ön vagy egy másik előfizetés-felhasználó által korábban nem előfizetett Azure-előfizetéseket.

Megtekintheti és szűrheti az előfizetések listáját az **előfizetések** menüpont kiválasztásával. A Security Center úgy állítja be a kijelzőt, hogy tükrözze a kiválasztott előfizetések biztonsági állapotát. 

A Security Center első indítását követően néhány percen belül megjelenhet a következő:

- **Javaslatok** a csatlakoztatott erőforrások biztonságának javítására.
- A Security Center által értékelt erőforrások leltára, valamint az egyes eszközök biztonsági helyzete.

A Security Center teljes kihasználása érdekében az Azure Defender engedélyezéséhez és a Log Analytics-ügynök telepítéséhez végre kell hajtania az alábbi lépéseket.

> [!TIP]
> Ha a felügyeleti csoportban lévő összes előfizetéshez Security Center szeretne engedélyezni, tekintse [meg a Security Center engedélyezése több Azure-előfizetésben](onboard-management-group.md)című témakört.

## <a name="enable-azure-defender"></a>Az Azure Defender engedélyezése

A Security Center rövid útmutatók és oktatóanyagok esetében engedélyeznie kell az Azure Defendert. Ingyenes 30 napos próbaverzió érhető el. További részletekért tekintse át az [árképzést ismertető oldalt](https://azure.microsoft.com/pricing/details/security-center/). 

1. Az Security Center oldalsávján válassza az **első lépések** lehetőséget.

    :::image type="content" source="./media/security-center-get-started/get-started-upgrade-tab.png" alt-text="Az első lépések lap upgrade (frissítés) lapja"::: 

    A **frissítés** lap felsorolja a bevezetésre jogosult előfizetéseket és munkaterületeket.

1. Válassza ki a frissíteni kívánt munkaterületeket a **munkaterületek kiválasztása** elemre az Azure Defender listájának engedélyezéséhez.
   - Ha olyan előfizetéseket és munkaterületeket választ, amelyek nem jogosultak a próbaverzióra, a következő lépés a frissítés után megkezdődik.
   - Ha olyan munkaterületet választ, amely jogosult az ingyenes próbaverzióra, a következő lépés egy próbaverziót indít el.
1. Válassza a **frissítés** lehetőséget az Azure Defender engedélyezéséhez.

## <a name="enable-automatic-data-collection"></a>Automatikus adatgyűjtés engedélyezése
Security Center adatokat gyűjt a gépekről a biztonsági rések és fenyegetések figyelése érdekében. Az adatok gyűjtése a Log Analytics ügynök használatával történik, amely beolvassa a különböző biztonsággal kapcsolatos konfigurációkat és eseménynaplókat a gépről, és az adatokat a munkaterületre másolja az elemzéshez. Alapértelmezés szerint a Security Center létre fog hozni egy új munkaterületet.

Ha engedélyezve van az automatikus kiépítés, Security Center telepíti a Log Analytics ügynököt az összes támogatott gépre és a létrehozott újakra. Az automatikus kiépítés használata erősen ajánlott.

Az log Analytics-ügynök automatikus kiépítés engedélyezése:

1. A Security Center menüjében válassza a **díjszabás & beállítások** lehetőséget.
1. Válassza ki az adott előfizetést.
1. Az **automatikus kiépítés** lapon az Azure-beli **virtuális gépekhez tartozó log Analytics-ügynök** esetében állítsa be az állapotot **a** következőre:.
1. Kattintson a **Mentés** gombra.

    :::image type="content" source="./media/security-center-enable-data-collection/enable-automatic-provisioning.png" alt-text="Az log Analytics-ügynök automatikus kiépítés engedélyezése":::

>[!TIP]
> Ha egy munkaterületet ki kell építeni, az ügynök telepítése akár 25 percet is igénybe vehet.

A gépekre telepített ügynökkel Security Center további, a rendszerfrissítési állapottal, az operációs rendszer biztonsági beállításaival, az Endpoint Protection szolgáltatással és a biztonsági riasztások létrehozásával kapcsolatos ajánlásokat is biztosíthat.

>[!NOTE]
> Az automatikus kiépítés **kikapcsolásának** beállítása nem távolítja el a log Analytics-ügynököt az Azure-beli virtuális gépekről, ahol az ügynök már ki lett építve. Az automatikus kiépítés letiltása korlátozza az erőforrások biztonsági monitorozását.



## <a name="next-steps"></a>Következő lépések
Ebben a rövid útmutatóban engedélyezte az Azure Defender használatát, és kiépítte a Log Analytics ügynököt az egységes biztonsági felügyelet és a fenyegetések elleni védelem érdekében a hibrid felhőalapú számítási feladatokhoz. Ha többet szeretne megtudni a Security Center használatáról, folytassa a helyszínen és egyéb felhőszolgáltatásokban található Windows rendszerű számítógépek felvételével foglalkozó oktatóanyaggal.

> [!div class="nextstepaction"]
> [Gyors útmutató: nem Azure-beli gépek előkészítése](quickstart-onboard-machines.md)

Szeretné optimalizálni és menteni a Felhőbeli kiadásait?

> [!div class="nextstepaction"]
> [A költségek elemzésének megkezdése a Cost Management](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

<!--Image references-->
[2]: ./media/security-center-get-started/overview.png
[4]: ./media/security-center-get-started/get-started.png
[5]: ./media/security-center-get-started/pricing.png
[7]: ./media/security-center-get-started/security-alerts.png
[8]: ./media/security-center-get-started/recommendations.png
[9]: ./media/security-center-get-started/select-subscription.png