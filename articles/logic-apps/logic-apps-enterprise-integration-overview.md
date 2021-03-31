---
title: B2B vállalati integráció
description: Ismerkedjen meg a vállalati integráció automatizált VÁLLALATKÖZI munkafolyamatainak létrehozásával Azure Logic Apps és Enterprise Integration Pack használatával
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, logicappspm
ms.topic: overview
ms.date: 08/01/2019
ms.openlocfilehash: adf15904d4490647074d6e2b46ac7e9e871dd388
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "86536402"
---
# <a name="b2b-enterprise-integration-solutions-with-azure-logic-apps-and-enterprise-integration-pack"></a>B2B vállalati integrációs megoldások Azure Logic Apps és Enterprise Integration Pack

A vállalatok közötti (B2B) megoldások és a szervezetek közötti zökkenőmentes kommunikáció érdekében a Enterprise Integration Pack (EIP) és a [Azure Logic apps](../logic-apps/logic-apps-overview.md)használatával automatizált Skálázható vállalati integrációs munkafolyamatokat hozhat létre. Bár a szervezetek különböző protokollokat és formátumokat használnak, elektronikus úton is cserélhetnek üzeneteket. A EIP különböző formátumokat alakít át egy olyan formátumba, amelyet a szervezet rendszerei feldolgozhatnak és támogatnak az iparági szabványoknak megfelelő protokollokat, beleértve az [AS2](../logic-apps/logic-apps-enterprise-integration-as2.md), a [X12](logic-apps-enterprise-integration-x12.md)és a [EDIFACT](../logic-apps/logic-apps-enterprise-integration-edifact.md). Emellett a titkosítás és a digitális aláírások használatával is javíthatja az üzenetek biztonságát. A EIP támogatja ezeket a [vállalati integrációs összekötőket](../connectors/apis-list.md#integration-account-connectors) és az iparági szabványokat:

* Elektronikus adatcsere (EDI)
* Enterprise Application Integration (EAI)

Ha már ismeri a Microsoft BizTalk Server vagy az Azure BizTalk Servicest, a EIP hasonló fogalmakat követ, így a funkciók könnyen használhatók. Azonban az egyik legnagyobb különbség az, hogy a EIP építészeti szempontból az "integrációs fiókok" alapján egyszerűsíti a VÁLLALATKÖZI kommunikációban használt összetevők tárolását és kezelését. Ezek a fiókok olyan felhőalapú tárolók, amelyek az összes összetevőt tárolják, például a partnereket, a szerződéseket, a sémákat, a térképeket és a tanúsítványokat. 

## <a name="why-use-the-enterprise-integration-pack"></a>Miért használja a Enterprise Integration Pack?

* A EIP egyetlen helyen tárolhatja az összes összetevőt – az integrációs fiókját.

* Azure Logic Apps és összekötők használatával VÁLLALATKÖZI munkafolyamatokat hozhat létre és integrálhat külső gyártótól származó szoftveres (SaaS) alkalmazásokkal, helyszíni alkalmazásokkal és egyéni alkalmazásokkal.

* Az Azure functions használatával egyéni kódot hozhat létre a logikai alkalmazásokhoz.

## <a name="how-do-i-get-started"></a>Hogyan kezdhetek hozzá?

A B2B Logic app-munkafolyamatok EIP való létrehozásának megkezdéséhez a következő elemek szükségesek:

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* [Integrációs fiók](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) a használni kívánt összetevőkkel

* Térképek és sémák létrehozásához használhatja a Visual Studio 2015 2,0 és a Visual Studio 2015 [Microsoft Azure Logic Apps vállalati integráció eszközeit](https://aka.ms/vsmapsandschemas) .

Miután létrehozott egy integrációs fiókot, és hozzáadja az összetevőkhöz, elindíthatja a VÁLLALATKÖZI munkafolyamatokat ezekkel az összetevőkkel egy logikai alkalmazás létrehozásával a Azure Portalban. Ha most ismerkedik a Logic apps [szolgáltatással, hozzon létre egy alapszintű logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md). Ezeknek az összetevőknek a működéséhez először össze kell kapcsolnia az integrációs fiókot a logikai alkalmazással. Ezután a logikai alkalmazás elérheti az integrációs fiókját. A Visual Studióval vagy a [PowerShell](/powershell/module/az.logicapp)-lel is létrehozhat, kezelhet és telepíthet logikai alkalmazásokat.

A B2B Logic Apps-alkalmazások létrehozásának első lépései:

![B2B logikai alkalmazások létrehozásának előfeltételei](./media/logic-apps-enterprise-integration-overview/overview.png)  

## <a name="try-now"></a>Kipróbálom most

[AS2-üzeneteket küldő és fogadó teljes mértékben működőképes minta logikai alkalmazás üzembe helyezése](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-as2-send-receive)

## <a name="next-steps"></a>Következő lépések

* [Kereskedelmi partnerek létrehozása](logic-apps-enterprise-integration-partners.md)
* [Szerződések létrehozása](../logic-apps/logic-apps-enterprise-integration-agreements.md)
* [Sémák felvétele](logic-apps-enterprise-integration-schemas.md)
* [Térképek hozzáadása](../logic-apps/logic-apps-enterprise-integration-maps.md)
* [Migráció a BizTalk Servicesből](../logic-apps/logic-apps-move-from-mabs.md)
