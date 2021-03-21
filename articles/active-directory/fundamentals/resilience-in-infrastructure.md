---
title: Hozzon létre rugalmasságot a IAM-infrastruktúrában Azure Active Directory
description: Az építészek és rendszergazdák számára készült útmutató nem épít rugalmasságot a IAM-infrastruktúra megszakadásához.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64fe4b8c217ec46cbb6dd046339c3ac65eebb121
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98724677"
---
# <a name="build-resilience-in-your-identity-and-access-management-infrastructure"></a>Hozzon létre rugalmasságot az identitás-és hozzáférés-kezelési infrastruktúrában

A Azure Active Directory egy globális Felhőbeli identitás-és hozzáférés-kezelési rendszer, amely kritikus fontosságú szolgáltatásokat nyújt, például a hitelesítést és a szervezet erőforrásainak engedélyezését. Ez a dokumentum útmutatást nyújt a hitelesítési vagy engedélyezési szolgáltatások megszakadásának a Azure Active Directory (Azure AD)-ra támaszkodó erőforrások esetében történő megzavarásához, valamint azok kezeléséhez és enyhítéséhez. 

A dokumentumkészlet a következőhöz lett tervezve:

* Identitás-építészek

* Identity Service-tulajdonosok

* Identity Operations-csapatok

Tekintse meg az alkalmazás- [fejlesztőknek](./resilience-app-development-overview.md) és [Azure ad B2C rendszereknek](resilience-b2c.md)szóló dokumentációt is.

## <a name="what-is-resilience"></a>Mi az a rugalmasság?

Az identitás-infrastruktúra kontextusában a rugalmasság lehetővé teszi az olyan szolgáltatások megszakadását, mint a hitelesítés és az engedélyezés, vagy más összetevők meghibásodása, minimális vagy semmilyen hatással sincs a vállalatra, a felhasználókra és a műveletekre. A megszakadási hatás súlyos lehet, a rugalmasság pedig gondos tervezést igényel.

## <a name="why-worry-about-disruption"></a>Miért kell aggódnia a fennakadás miatt?

A hitelesítési rendszer minden hívása megszakad, ha a hívás bármely összetevője meghiúsul. Ha a hitelesítés megszakad, a mögöttes összetevő hibái miatt a felhasználók nem fognak hozzáférni az alkalmazásaihoz. Ezért a hitelesítési hívások számának és a függőségek számának csökkentése fontos a rugalmasság szempontjából. Az alkalmazások fejlesztői megadhatják a tokenek kérésének gyakoriságát. Például a fejlesztőkkel együttműködve gondoskodhat arról, hogy az Azure AD által felügyelt identitásokat használják az alkalmazásaikban, ahol lehetséges. 

A jogkivonat-alapú hitelesítési rendszerekben, például az Azure AD-ben a felhasználó alkalmazásának (ügyfelének) biztonsági jogkivonatot kell bekérnie az Identity rendszerből ahhoz, hogy hozzáférhessen egy alkalmazáshoz vagy más erőforráshoz. Az érvényességi időszak alatt az ügyfél többször is használhatja ugyanazt a tokent az alkalmazás eléréséhez.

Amikor az alkalmazásnak bemutatott jogkivonat lejár, az alkalmazás elutasítja a jogkivonatot, és az ügyfélnek új jogkivonatot kell bekérnie az Azure AD-ből. Az új token beszerzése szükségessé teszi a felhasználói beavatkozást, például a hitelesítő adatok megadását vagy a hitelesítési rendszer egyéb követelményeinek teljesítését. A hosszú élettartamú tokenekkel rendelkező hitelesítési hívások gyakoriságának csökkentése csökkenti a szükségtelen interakciókat. Azonban a jogkivonat élettartamát a kevesebb házirend-értékelés által létrehozott kockázattal kell kiegyenlíteni. A jogkivonat-élettartamok kezelésével kapcsolatos további információkért tekintse meg ezt a cikket az [újrahitelesítéssel kapcsolatos kérések optimalizálásához](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md).

## <a name="ways-to-increase-resilience"></a>A rugalmasság növelésének módjai
A következő ábra a rugalmasság növelését hat konkrét módon mutatja be. Az egyes módszereket részletesen ismertetjük a cikk következő lépések részében csatolt cikkekben.
  
![A rendszergazdai rugalmasság áttekintését bemutató ábra](./media/resilience-in-infrastructure/admin-resilience-overview.png)

## <a name="next-steps"></a>Következő lépések
Rugalmassági erőforrások rendszergazdák és építészek számára
 
* [Rugalmasság létrehozása a hitelesítőadat-kezeléssel](resilience-in-credentials.md)

* [Rugalmasság kiépítése az eszközök állapotával](resilience-with-device-states.md)

* [Rugalmasság kiépítése a folyamatos hozzáférés kiértékelésének (CAE) használatával](resilience-with-continuous-access-evaluation.md)

* [Rugalmasság létrehozása külső felhasználói hitelesítéssel](resilience-b2b-authentication.md)

* [Hozzon létre rugalmasságot a hibrid hitelesítésben](resilience-in-hybrid.md)

* [Rugalmasság kialakítása az alkalmazás-hozzáférés alkalmazás-proxyval való használatához](resilience-on-premises-access.md)

Rugalmassági erőforrások fejlesztők számára

* [IAM-rugalmasság létrehozása az alkalmazásokban](resilience-app-development-overview.md)

* [Hozzon létre rugalmasságot a CIAM-rendszerekben](resilience-b2c.md)