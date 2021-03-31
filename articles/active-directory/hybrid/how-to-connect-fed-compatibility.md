---
title: Az Azure AD összevonás kompatibilitási listája
description: Ezen a lapon az egyszeri bejelentkezés megvalósítására szolgáló nem Microsoft-identitású szolgáltatók találhatók.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 22c8693e-8915-446d-b383-27e9587988ec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/23/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 895e7347feea3190ba9bdc273200d2985dfaa0d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89661818"
---
# <a name="azure-ad-federation-compatibility-list"></a>Az Azure AD összevonás kompatibilitási listája
A Azure Active Directory lehetővé teszi az egyszeri bejelentkezést és az alkalmazásokhoz való hozzáférés biztonságát a Microsoft 365 és más Microsoft online szolgáltatások számára a hibrid és a kizárólag felhőalapú megvalósításokhoz anélkül, hogy harmadik féltől származó megoldást kellene igényelni. Microsoft 365, mint a Microsoft online szolgáltatásainak többsége, a címtárszolgáltatások, a hitelesítés és az engedélyezés Azure Active Directory is integrálva van. Azure Active Directory emellett egyszeri bejelentkezést is biztosít több ezer SaaS-alkalmazáshoz és helyszíni webalkalmazásokhoz. Tekintse meg a támogatott SaaS-alkalmazások Azure Active Directory [alkalmazás-gyűjteményét](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps) . 

## <a name="idp-validation"></a>IDENTITÁSSZOLGÁLTATÓ érvényesítése
Ha a szervezet egy külső gyártótól származó összevonási megoldást használ, az egyszeri bejelentkezést a helyszíni Active Directory felhasználók számára is konfigurálhatja a Microsoft Online Services szolgáltatással (például Microsoft 365), feltéve, hogy a harmadik féltől származó összevonási megoldás kompatibilis a Azure Active Directorysal.  A kompatibilitással kapcsolatos kérdésekért forduljon az identitás-szolgáltatóhoz.  Ha szeretné megtekinteni a Microsoft által az Azure AD-vel való kompatibilitásra korábban tesztelt identitás-szolgáltatók listáját, tekintse meg az [Azure ad Identity Provider kompatibilitási dokumentációját](https://www.microsoft.com/download/details.aspx?id=56843). 

>[!NOTE]
>A Microsoft már nem biztosít érvényesítési teszteket a független identitás-szolgáltatók számára a Azure Active Directory való kompatibilitás érdekében. Ha tesztelni szeretné a terméket az együttműködéshez, tekintse meg ezeket az [irányelveket](https://www.microsoft.com/download/details.aspx?id=56843). 

## <a name="next-steps"></a>Következő lépések

- [A helyszíni címtárak integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)
- [Azure AD Connect és összevonás](how-to-connect-fed-whatis.md)
