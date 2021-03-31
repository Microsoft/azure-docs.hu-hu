---
title: Azure AD Connect – átmenő hitelesítés – hitelesítési ügynökök frissítése | Microsoft Docs
description: Ez a cikk a Azure Active Directory (Azure AD) átmenő hitelesítési konfigurációjának frissítését ismerteti.
services: active-directory
keywords: Azure AD Connect átmenő hitelesítés, telepítési Active Directory, szükséges összetevők az Azure AD-hez, egyszeri bejelentkezéshez, egyszeri bejelentkezéshez
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/27/2018
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1d0507f7751305af5e626cbd7dd6e0dfd1a63a74
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95973048"
---
# <a name="azure-active-directory-pass-through-authentication-upgrade-preview-authentication-agents"></a>Azure Active Directory átmenő hitelesítés: az előzetes verziójú hitelesítési ügynökök frissítése

## <a name="overview"></a>Áttekintés

Ez a cikk az Azure AD átmenő hitelesítést az előzetes verzió használatával használó ügyfelek számára készült. Nemrég frissítettük (és felújítottuk) a hitelesítési ügynök szoftverét. _Manuálisan_ kell frissítenie a helyszíni kiszolgálókra telepített előzetes hitelesítési ügynököket. Ez a manuális frissítés csak egyszeri művelet. A hitelesítési ügynökök minden jövőbeli frissítése automatikus. A frissítés okai a következők:

- A hitelesítési ügynökök előzetes verziói nem kapnak további biztonsági vagy hibajavítási hibákat.
-   A hitelesítési ügynökök előzetes verzióit nem lehet további kiszolgálókra telepíteni a magas rendelkezésre állás érdekében.

## <a name="check-versions-of-your-authentication-agents"></a>A hitelesítési ügynökök verzióinak keresése

### <a name="step-1-check-where-your-authentication-agents-are-installed"></a>1. lépés: a hitelesítési ügynökök telepítésének engedélyezése

A következő lépésekkel ellenőrizheti, hogy a hitelesítési ügynökök hol vannak telepítve:

1. Jelentkezzen be a [Azure Active Directory felügyeleti központba](https://aad.portal.azure.com) a bérlő globális rendszergazdai hitelesítő adataival.
2. A bal oldali navigációs sávon válassza a **Azure Active Directory** lehetőséget.
3. Válassza a **Azure ad Connect** lehetőséget. 
4. Válassza **az átmenő hitelesítés** lehetőséget. Ez a panel felsorolja azokat a kiszolgálókat, amelyeken a hitelesítési ügynökök telepítve vannak.

![Azure Active Directory felügyeleti központ – átmenő hitelesítés panel](./media/how-to-connect-pta-upgrade-preview-authentication-agents/pta8.png)

### <a name="step-2-check-the-versions-of-your-authentication-agents"></a>2. lépés: a hitelesítési ügynökök verzióinak megkeresése

A hitelesítési ügynökök verzióinak az előző lépésben azonosított összes kiszolgálón való vizsgálatához kövesse az alábbi utasításokat:

1. Válassza a **Vezérlőpult – > programok – > programok és szolgáltatások lehetőséget** a helyszíni kiszolgálón.
2. Ha a "**Microsoft Azure ad-kapcsolat hitelesítési ügynöke**" bejegyzést tartalmaz, nem kell semmilyen műveletet végrehajtania ezen a kiszolgálón.
3. Ha van "**Microsoft Azure ad alkalmazásproxy-összekötő**" bejegyzés, akkor a kiszolgálón manuálisan kell frissítenie.

![A hitelesítési ügynök előzetes verziója](./media/how-to-connect-pta-upgrade-preview-authentication-agents/pta6.png)

## <a name="best-practices-to-follow-before-starting-the-upgrade"></a>A frissítés megkezdése előtt követendő ajánlott eljárások

A frissítés előtt győződjön meg arról, hogy a következő elemek vannak érvényben:

1. **Csak felhőalapú globális rendszergazdai fiók létrehozása**: ne frissítsen kizárólag felhőalapú globális rendszergazdai fiókkal olyan vészhelyzeti helyzetekben, ahol az átmenő hitelesítési ügynökök nem működnek megfelelően. További információ [a csak felhőalapú globális rendszergazdai fiók hozzáadásáról](../fundamentals/add-users-azure-active-directory.md). Ez a lépés kritikus fontosságú, és gondoskodik arról, hogy ne legyen kizárva a bérlőből.
2.  **Magas rendelkezésre állás biztosítása**: Ha korábban még nem fejeződött be, telepítsen egy második önálló hitelesítési ügynököt, hogy magas rendelkezésre állást biztosítson a bejelentkezési kérésekhez, ezekkel az [utasításokkal](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability).

## <a name="upgrading-the-authentication-agent-on-your-azure-ad-connect-server"></a>A hitelesítési ügynök frissítése a Azure AD Connect-kiszolgálón

A hitelesítési ügynök ugyanarra a kiszolgálóra való frissítése előtt frissítenie kell Azure AD Connect. Kövesse az alábbi lépéseket az elsődleges és az átmeneti Azure AD Connect-kiszolgálókon:

1. **Azure ad Connect frissítése**: kövesse ezt a [cikket](how-to-upgrade-previous-version.md) , és frissítsen a legújabb verzióra Azure ad Connect.
2. **Távolítsa el a hitelesítési ügynök előzetes verzióját**: töltse le [ezt a PowerShell-parancsfájlt](https://aka.ms/rmpreviewagent) , és futtassa rendszergazdaként a kiszolgálón.
3. **Töltse le a hitelesítési ügynök legújabb verzióját (1.5.389.0 vagy újabb verziók)**: Jelentkezzen be a [Azure Active Directory felügyeleti központba](https://aad.portal.azure.com) a bérlő globális rendszergazdai hitelesítő adataival. Válassza a **Azure Active Directory-> Azure ad Connect – > átmenő hitelesítés – > letöltési ügynök** lehetőséget. Fogadja el a [szolgáltatási feltételeket](https://aka.ms/authagenteula) , és töltse le a hitelesítési ügynök legújabb verzióját. A hitelesítési ügynököt innen is letöltheti [.](https://aka.ms/getauthagent)
4. **Telepítse a hitelesítési ügynök legújabb verzióját**: futtassa a 3. lépésben letöltött végrehajtható fájlt. Ha a rendszer kéri, adja meg a bérlő globális rendszergazdai hitelesítő adatait.
5. **Ellenőrizze, hogy telepítve van-e a legújabb verzió**: ahogy az előzőekben is látható, nyissa meg a **Vezérlőpult – > programok – > programok és szolgáltatások** elemet, és ellenőrizze, hogy van-e bejegyzés a "**Microsoft Azure ad csatlakozni a hitelesítési ügynökhöz**".

>[!NOTE]
>Ha a fenti lépések elvégzése után bejelöli a [Azure Active Directory felügyeleti központban](https://aad.portal.azure.com) az átmenő hitelesítés panelt, két hitelesítési ügynök bejegyzés jelenik meg kiszolgálónként – egy bejegyzés, amely a hitelesítési ügynököt **aktívként** és a másikat **inaktívként** jeleníti meg. Ez a _várt_ érték. Az **inaktív** bejegyzés néhány nap múlva automatikusan eldobásra kerül.

## <a name="upgrading-the-authentication-agent-on-other-servers"></a>A hitelesítési ügynök frissítése más kiszolgálókon

A következő lépésekkel frissítheti a hitelesítési ügynököket más kiszolgálókon (ahol a Azure AD Connect nincs telepítve):

1. **Távolítsa el a hitelesítési ügynök előzetes verzióját**: töltse le [ezt a PowerShell-parancsfájlt](https://aka.ms/rmpreviewagent) , és futtassa rendszergazdaként a kiszolgálón.
2. **Töltse le a hitelesítési ügynök legújabb verzióját (1.5.389.0 vagy újabb verziók)**: Jelentkezzen be a [Azure Active Directory felügyeleti központba](https://aad.portal.azure.com) a bérlő globális rendszergazdai hitelesítő adataival. Válassza a **Azure Active Directory-> Azure ad Connect – > átmenő hitelesítés – > letöltési ügynök** lehetőséget. Fogadja el a szolgáltatási feltételeket, és töltse le a legújabb verziót.
3. **Telepítse a hitelesítési ügynök legújabb verzióját**: futtassa a 2. lépésben letöltött végrehajtható fájlt. Ha a rendszer kéri, adja meg a bérlő globális rendszergazdai hitelesítő adatait.
4. **Ellenőrizze, hogy telepítve van-e a legújabb verzió**: ahogy az előzőekben is látható, nyissa meg a **Vezérlőpult – > programok – > programok és szolgáltatások** elemet, és ellenőrizze, hogy van-e egy **Microsoft Azure ad-kapcsolat hitelesítési ügynökének** nevű bejegyzés.

>[!NOTE]
>Ha a fenti lépések elvégzése után bejelöli a [Azure Active Directory felügyeleti központban](https://aad.portal.azure.com) az átmenő hitelesítés panelt, két hitelesítési ügynök bejegyzés jelenik meg kiszolgálónként – egy bejegyzés, amely a hitelesítési ügynököt **aktívként** és a másikat **inaktívként** jeleníti meg. Ez a _várt_ érték. Az **inaktív** bejegyzés néhány nap múlva automatikusan eldobásra kerül.

## <a name="next-steps"></a>Következő lépések
- [**Hibaelhárítás**](tshoot-connect-pass-through-authentication.md) – megismerheti a szolgáltatással kapcsolatos gyakori problémák megoldását.