---
title: A rugalmasság kiépítése a folyamatos hozzáférés kiértékelésének használatával Azure Active Directory
description: Útmutató az építészek és a rendszergazdák számára a CAE használatához
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
ms.openlocfilehash: 1a12cc3299fbb5916afba48d2608024f28999634
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98724627"
---
# <a name="build-resilience-by-using-continuous-access-evaluation"></a>Rugalmasság kiépítése a folyamatos hozzáférés kiértékelésének használatával

A [folyamatos hozzáférés kiértékelése](../conditional-access/concept-continuous-access-evaluation.md) (CAE) lehetővé teszi az Azure ad-alkalmazások számára az olyan kritikus eseményekre való előfizetést, amelyeket később kiértékel és érvényesíteni lehet. Ide tartozik a következő események kiértékelése:

* A törölt vagy letiltott felhasználói fiók

* A felhasználó jelszava módosítva

* Az MFA engedélyezve van a felhasználó számára.

* A rendszergazda explicit módon visszavonja a jogkivonatot.

* A rendszer emelt szintű felhasználói kockázatot észlelt.

Ennek eredményeképpen az alkalmazások az Azure AD által jelzett események alapján visszautasítják a nem lejárt jogkivonatokat az alábbi ábrán látható módon.

![a CAE conceptualiagram](./media/resilience-with-cae/admin-resilience-continuous-access-evaluation.png)

## <a name="how-does-cae-help"></a>Hogyan segít a CAE?

Ez a mechanizmus lehetővé teszi, hogy az Azure AD hosszú élettartamú tokeneket bocsásson ki, miközben lehetővé teszi, hogy az alkalmazások visszavonják a hozzáférést, és csak szükség esetén kényszerítsék újra a hitelesítést. A minta nettó eredménye kevesebb hívás a tokenek beszerzésére, ami azt jelenti, hogy a végpontok közötti folyamat rugalmasabb. 

A CAE használatához a szolgáltatásnak és az ügyfélnek is a CAE-kompatibilisnek kell lennie. Microsoft 365 olyan szolgáltatásokat, mint az Exchange Online, a Teams és a SharePoint Online support CAE. Az Office 365-szolgáltatásokat (például az Outlook Web App-t) és az Office 365 natív ügyfelek adott verzióit használó ügyféloldali felhasználói felület a CAE-kompatibilis. A Microsoft Cloud Services további szolgáltatásai CAE-kompatibilisek lesznek.

A Microsoft együttműködik az iparággal olyan [szabványok kidolgozásában](https://openid.net/wg/sse/) , amelyek lehetővé teszik a harmadik féltől származó alkalmazások számára a funkció használatát. A CAE-kompatibilis alkalmazásokat is fejlesztheti. További információért lásd: rugalmasság létrehozása az alkalmazásban.

## <a name="how-do-i-implement-cae"></a>Hogyan a CAE implementálása?

* [Engedélyezze a CAE](../conditional-access/concept-continuous-access-evaluation.md) -t az Azure ad biztonsági konfigurációjában.

* Győződjön meg arról, hogy a szervezete Microsoft Office natív alkalmazások [kompatibilis verzióit](../conditional-access/concept-continuous-access-evaluation.md) használja.

* [Optimalizálja az újrahitelesítésre vonatkozó utasításokat](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md).

 
## <a name="next-steps"></a>Következő lépések
Rugalmassági erőforrások rendszergazdák és építészek számára
 
* [Rugalmasság létrehozása a hitelesítőadat-kezeléssel](resilience-in-credentials.md)

* [Rugalmasság kiépítése az eszközök állapotával](resilience-with-device-states.md)

* [Rugalmasság létrehozása külső felhasználói hitelesítéssel](resilience-b2b-authentication.md)

* [Hozzon létre rugalmasságot a hibrid hitelesítésben](resilience-in-hybrid.md)

* [Rugalmasság kialakítása az alkalmazás-hozzáférés alkalmazás-proxyval való használatához](resilience-on-premises-access.md)

Rugalmassági erőforrások fejlesztők számára

* [IAM-rugalmasság létrehozása az alkalmazásokban](resilience-app-development-overview.md)

* [Hozzon létre rugalmasságot a CIAM-rendszerekben](resilience-b2c.md)