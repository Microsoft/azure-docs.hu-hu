---
title: B2B vállalati integráció
description: Ismerje meg, hogyan építsen ki automatizált B2B-munkafolyamatokat a vállalati integrációhoz Azure Logic Apps és Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, logicappspm
ms.topic: overview
ms.date: 03/24/2021
ms.openlocfilehash: 09d253aae4c054db5efdc252f62986044e1d366b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771856"
---
# <a name="b2b-enterprise-integration-solutions-with-azure-logic-apps-and-enterprise-integration-pack"></a>B2B vállalati integrációs megoldások Azure Logic Apps és Enterprise Integration Pack

A vállalat közötti (B2B) megoldásokhoz és a szervezetek közötti zökkenőmentes kommunikációhoz automatizált, skálázható vállalati integrációs munkafolyamatokat építhet ki az Enterprise Integration Pack (EIP) és a [Azure Logic Apps.](../logic-apps/logic-apps-overview.md) Bár a szervezetek különböző protokollokat és formátumokat használnak, elektronikusan is küldhetnek üzeneteket. Az EIP különböző formátumokat alakít át olyan formátumba, amely a szervezet rendszerei számára feldolgozható és támogatja az iparági szabványnak megfelelő protokollokat, például az [AS2,](../logic-apps/logic-apps-enterprise-integration-as2.md) [az X12](logic-apps-enterprise-integration-x12.md)és az [EDIFACT protokollt.](../logic-apps/logic-apps-enterprise-integration-edifact.md) Az üzenetek biztonságát titkosítással és digitális aláírással is javíthatja. Az EIP támogatja ezeket a vállalati [integrációs összekötőket és](../connectors/managed.md#enterprise-connectors) az alábbi iparági szabványokat:

* Elektronikus adatcsere (EDI)
* Enterprise Application Integration (EAI)

Ha már ismeri a Microsoft BizTalk Server vagy az Azure BizTalk Services szolgáltatást, az EIP hasonló fogalmakat követ, így a funkciók könnyen használhatók. Az egyik fő különbség azonban az, hogy az EIP architekturális alapja az "integrációs fiókok", hogy egyszerűbbé tegye a B2B-kommunikációban használt összetevők tárolását és kezelését. Ezek a fiókok olyan felhőalapú tárolók, amelyek az összes összetevőt tárolják, például a partnereket, a szerződéseket, a sémákat, a térképeket és a tanúsítványokat. 

## <a name="why-use-the-enterprise-integration-pack"></a>Miért érdemes a Enterprise Integration Pack?

* Az EIP-nek az összes összetevőt egy helyen tárolhatja – az integrációs fiókjában.

* B2B-munkafolyamatokat hozhat létre, és külső szoftverszolgáltatással (SaaS) származó alkalmazásokkal, helyszíni alkalmazásokkal és egyéni alkalmazásokkal integrálhatja őket Azure Logic Apps és összekötők használatával.

* Az Azure Functions használatával egyéni kódot hozhat létre a logikai alkalmazásokhoz.

## <a name="how-do-i-get-started"></a>Hogyan kezdhetek hozzá?

Mielőtt elkezdi a B2B logikai alkalmazások munkafolyamatait az EIP-ben, a következő elemekre lesz szüksége:

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Integrációs [fiók](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) a használni kívánt összetevőkből

* Térképek és sémák létrehozásához használhatja a [Microsoft Azure Logic Apps Vállalati integráció Tools for Visual Studio 2015 2.0 és 2015](https://aka.ms/vsmapsandschemas) Visual Studio eszközöket.

Miután létrehozott egy integrációs fiókot és hozzáadta az összetevőket, ezekkel az összetevőkvel elkezdhet B2B-munkafolyamatokat létrehozni, ha létrehoz egy logikai alkalmazást a Azure Portal. Ha még nem használja a logikai alkalmazásokat, próbáljon [létrehozni egy alapszintű logikai alkalmazást.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Ezeknek az összetevőknek a munkához először csatolja az integrációs fiókot a logikai alkalmazáshoz. Ezután a logikai alkalmazás hozzáférhet az integrációs fiókhoz. Logikai alkalmazásokat is létrehozhat, kezelhet és telepíthet a Visual Studio [vagy a PowerShell használatával.](/powershell/module/az.logicapp)

A B2B logikai alkalmazások építésének első lépései a következő magas szintű lépések:

![A B2B logikai alkalmazások létrehozásának előfeltételei](./media/logic-apps-enterprise-integration-overview/overview.png)  

## <a name="try-now"></a>Kipróbálom most

[Egy teljesen működőképes logikai mintaalkalmazás üzembe helyezése, amely AS2-üzeneteket küld és fogad](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-as2-send-receive)

## <a name="next-steps"></a>Következő lépések

* [Kereskedelmi partnerek létrehozása](logic-apps-enterprise-integration-partners.md)
* [Szerződések létrehozása](../logic-apps/logic-apps-enterprise-integration-agreements.md)
* [Sémák felvétele](logic-apps-enterprise-integration-schemas.md)
* [Térképek hozzáadása](../logic-apps/logic-apps-enterprise-integration-maps.md)
* [Migráció a BizTalk Servicesből](../logic-apps/logic-apps-move-from-mabs.md)
