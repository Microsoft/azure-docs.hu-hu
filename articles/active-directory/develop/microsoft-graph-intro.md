---
title: Microsoft Graph API | Microsoft Docs
description: A Microsoft Graph API egy REST-alapú webes API, amely lehetővé teszi Microsoft Cloud szolgáltatás erőforrásainak elérését.
author: davidmu1
services: active-directory
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/13/2020
ms.author: davidmu
ms.custom: aaddev
ms.openlocfilehash: 6c1b4390282f6d54178365714b1e2e665b4cf061
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2020
ms.locfileid: "79136500"
---
# <a name="microsoft-graph-api"></a>Microsoft Graph API

A Microsoft Graph API egy REST-alapú webes API, amely lehetővé teszi Microsoft Cloud szolgáltatás erőforrásainak elérését. Miután regisztrálta az alkalmazást, és beolvassa egy felhasználó vagy szolgáltatás hitelesítési jogkivonatait, megteheti a kéréseket a Microsoft Graph API-nak. További információ: [Microsoft Graph áttekintése](https://docs.microsoft.com/graph/overview).

A Microsoft Graph REST API-kat és ügyféloldali kódtárakat tesz elérhetővé a következő Microsoft 365-szolgáltatásokban lévő adatok eléréséhez:
- Office 365-szolgáltatások: ás, Excel, Microsoft Books, Microsoft Teams, OneDrive, OneNote, Outlook/Exchange, Planner és SharePoint
- Nagyvállalati mobilitási és biztonsági szolgáltatások: Advanced Threat Analytics, komplex veszélyforrások elleni védelem, Azure Active Directory, Identity Manager és Intune
- Windows 10 Services: tevékenységek, eszközök, értesítések
- Dynamics 365 Business Central

## <a name="versions"></a>Verziók

Microsoft Graph jelenleg két verziót támogat: v 1.0 és Beta. A v 1.0 verziója általánosan elérhető API-kat tartalmaz. Használja a 1.0-s verziót az összes éles alkalmazáshoz. A Beta a jelenleg előzetes verzióban elérhető API-kat tartalmazza. Mivel előfordulhat, hogy a bétaverzió API-jai nem módosítják a változásokat, javasoljuk, hogy a bétaverziót csak a fejlesztés alatt álló alkalmazások tesztelésére használja. Ne használjon béta API-kat az éles alkalmazásokban. További tudnivalókért tekintse [meg a verziószámozást, a támogatást és a változtatási szabályzatokat a Microsoft Graph](https://docs.microsoft.com/graph/versioning-and-support).

A Beta API-k használatának megkezdéséhez lásd: [Microsoft Graph béta-végpont referenciája](https://docs.microsoft.com/graph/api/overview?view=graph-rest-beta)

A 1.0-s API-k használatának megkezdéséhez lásd a [Microsoft Graph REST API 1.0-s verziójának dokumentációját](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0) .

## <a name="get-started"></a>Bevezetés

Ha olyan erőforrást szeretne olvasni vagy írni, mint például egy felhasználó vagy egy e-mail-üzenet, a következőhöz hasonló kérelmet kell létrehoznia:

`{HTTP method} https://graph.microsoft.com/{version}/{resource}?{query-parameters}`

A kiépített kérelem elemeivel kapcsolatos további információkért lásd: [a Microsoft Graph API használata](https://docs.microsoft.com/graph/use-the-api)

A gyors üzembe helyezési minták bemutatják, hogyan érheti el a Microsoft Graph API hatékonyságát. Azok a minták, amelyek egyetlen hitelesítéssel érhetők el két szolgáltatáshoz: Microsoft-fiók és az Outlook. Minden rövid útmutató hozzáfér Microsoft-fiók felhasználói profilokból származó adatokhoz, és megjeleníti az eseményeket a naptárból.
A rövid útmutatók négy lépést foglalnak magukban:
- Válassza ki a platformot
- Alkalmazás-azonosító (ügyfél-azonosító) beszerzése
- A minta összeállítása
- Jelentkezzen be, és tekintse meg az eseményeket a naptárban

Ha elvégezte a gyors üzembe helyezést, már van egy alkalmazás, amely készen áll a futtatásra. További információ: Microsoft Graph rövid útmutató – [Gyakori kérdések](https://docs.microsoft.com/graph/quick-start-faq). A minták használatának megkezdéséhez lásd: [Microsoft Graph](https://developer.microsoft.com/graph/quick-start)rövid útmutató.

## <a name="tools"></a>Eszközök

A Microsoft Graph Explorer egy webalapú eszköz, amellyel Microsoft Graph API-k használatával hozhat létre és tesztelheti a kérelmeket. Microsoft Graph Explorer a következő címen érhető el: `https://developer.microsoft.com/graph/graph-explorer`.

A Poster olyan eszköz, amellyel a Microsoft Graph API-k használatával hozhat létre és tesztelheti a kérelmeket. A Poster a következő címen tölthető le: `https://www.getpostman.com/`. Ha a Poster-ben szeretné használni a Microsoft Grapht, használja a Microsoft Graph gyűjteményt a Poster szolgáltatásban. További információ: [Poster használata a Microsoft Graph API-val](/graph/use-postman?context=graph%2Fapi%2Fbeta&view=graph-rest-beta).
