---
title: Microsoft Graph API
description: A Microsoft Graph API egy RESTful webes API, amely lehetővé teszi a Microsoft Cloud szolgáltatási erőforrásokhoz való hozzáférést.
author: davidmu1
services: active-directory
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 02/13/2020
ms.author: davidmu
ms.custom: aaddev
ms.openlocfilehash: 2e689e620a5aeb7c5028f1a1b30dd6def8e447ab
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529983"
---
# <a name="microsoft-graph-api"></a>Microsoft Graph API

A Microsoft Graph API egy RESTful webes API, amely lehetővé teszi a Microsoft Cloud szolgáltatási erőforrásokhoz való hozzáférést. Miután regisztrálta az alkalmazást, és lekért hitelesítési jogkivonatokat egy felhasználóhoz vagy szolgáltatáshoz, kéréseket kérhet a Microsoft Graph API-hoz. További információ: [Overview of Microsoft Graph](/graph/overview).

Microsoft Graph REST API-kat és ügyfélkódtárakat biztosít a következő szolgáltatásokban Microsoft 365 adatok eléréséhez:
- Microsoft 365 szolgáltatások: Delve, Excel, Microsoft Bookings, Microsoft Teams, OneDrive, OneNote, Outlook/Exchange, Planner és SharePoint
- Nagyvállalati mobilitási és biztonsági szolgáltatások: Advanced Threat Analytics, Advanced Threat Protection, Azure Active Directory, Identity Manager és Intune
- Windows 10 szolgáltatások: tevékenységek, eszközök, értesítések
- Dynamics 365 Business Central

## <a name="versions"></a>Verziók

Microsoft Graph jelenleg két verziót támogat: az 1.0-s verziót és a bétaverziót. Az 1.0-s verzió általánosan elérhető API-kat tartalmaz. Minden éles alkalmazáshoz használja az 1.0-s verziót. A bétaverzió jelenleg előzetes verzióban elérhető API-kat tartalmaz. Mivel a bétaverziós API-kban használjuk a legfrissebb változásokat, javasoljuk, hogy csak a bétaverziót használja a fejlesztés alatt áll alkalmazások teszteléséhez; ne használjon bétaverziójú API-kat az éles alkalmazásokban. További információ: [Verziószámozási, támogatási és a](/graph/versioning-and-support)legfrissebb változási szabályzatok a Microsoft Graph.

A bétaverziós API-k használatának első Microsoft Graph tekintse meg [a bétaverzió végpontjának referenciáját](/graph/api/overview?view=graph-rest-beta&preserve-view=true)

Az 1.0-s és az 1.0-s Microsoft Graph REST API [útmutatója](/graph/api/overview?view=graph-rest-1.0&preserve-view=true)

## <a name="get-started"></a>Bevezetés

Egy erőforrás, például felhasználó vagy e-mail-üzenet olvasása vagy írása esetén a következő módon kell kérést összeépítenie:

`{HTTP method} https://graph.microsoft.com/{version}/{resource}?{query-parameters}`

További információ a felépített kérés elemeiről: [A Microsoft Graph API használata](/graph/use-the-api)

Gyorsútmutató-minták érhetők el, amelyek megmutatják, hogyan férhet hozzá a Microsoft Graph API-hoz. A rendelkezésre álló minták két szolgáltatást érhetők el egyetlen hitelesítéssel: az Microsoft-fiók outlookot. Minden rövid útmutató hozzáfér a Microsoft-fiók felhasználói profilokból származó információkhoz, és megjeleníti a naptárból származó eseményeket.
A rövid útmutatók négy lépésből áll:
- Platform kiválasztása
- Szerezze be az alkalmazásazonosítót (ügyfél-azonosító)
- A minta összeállítása
- Bejelentkezés és események megtekintése a naptárban

A rövid útmutató befejezésekor egy futtatásra kész alkalmazása lesz. További információért tekintse meg a gyakori [Microsoft Graph című rövid útmutatót.](/graph/quick-start-faq) A minták első lépésekhez tekintse meg a Microsoft Graph [útmutatót.](https://developer.microsoft.com/graph/quick-start)

## <a name="tools"></a>Eszközök

Microsoft Graph Explorer egy webalapú eszköz, amely kérések építésére és tesztelésére használható Microsoft Graph API-k használatával. Az Explorer Microsoft Graph a következő: `https://developer.microsoft.com/graph/graph-explorer` .

A Postman egy olyan eszköz, amely kérések építésére és tesztelésére is használható a Microsoft Graph API-k használatával. A Postmant a következő címről töltheti le: `https://www.getpostman.com/` . A Postmanben Microsoft Graph a Postmanben a Microsoft Graph a Postmanben. További információ: [A Postman használata a Microsoft Graph API-val.](/graph/use-postman)
