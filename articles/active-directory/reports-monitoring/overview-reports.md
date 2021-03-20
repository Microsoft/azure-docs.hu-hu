---
title: Mi az Azure Active Directory-jelentés? | Microsoft Docs
description: Az Azure Active Directory jelentések általános áttekintését nyújtja.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 6141a333-38db-478a-927e-526f1e7614f4
ms.service: active-directory
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 09/30/2020
ms.author: markvi
ms.reviewer: sarbar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4f9a51c10a4f390e5627bccf35ab5dc74689e9c6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91566822"
---
# <a name="what-are-azure-active-directory-reports"></a>Mi az Azure Active Directory-jelentés?

A Azure Active Directory-(Azure AD-) jelentések átfogó áttekintést nyújtanak a környezettel kapcsolatos tevékenységekről. A megadott adatokkal a következőket teheti:

- Meghatározhatja, hogy a felhasználók hogyan használják az alkalmazásokat és szolgáltatásokat
- Észlelheti a környezet állapotát befolyásoló lehetséges kockázatokat
- Elháríthatja azokat a hibákat, amelyek megakadályozzák a felhasználók munkavégzését  

A jelentéskészítési architektúra két alappillérre támaszkodik:

- [Biztonsági jelentések](#security-reports)
- [Tevékenységjelentések](#activity-reports)

![Jelentéskészítés](./media/overview-reports/01.png)


## <a name="security-reports"></a>Biztonsági jelentések

A biztonsági jelentések segítenek a szervezet identitásának védelmében. A biztonsági jelentések két típusa létezik:

- **Kockázatosként megjelölt felhasználók** – A [kockázatosként megjelölt felhasználók biztonsági jelentésben](../identity-protection/overview-identity-protection.md) azon felhasználói fiókok áttekintését találja, amelyek biztonsága esetleg sérült.

- **Kockázatos bejelentkezések** – A [kockázatos bejelentkezés biztonsági jelentés](../identity-protection/overview-identity-protection.md) olyan bejelentkezési kísérleteket jelöl, amelyeket elképzelhető, hogy olyan személy hajtott végre, aki nem a felhasználói fiókok jogos tulajdonosa. 

### <a name="what-azure-ad-license-do-you-need-to-access-a-security-report"></a>Milyen Azure AD-licencre van szükség a biztonsági jelentések eléréséhez?  

Az Azure AD minden kiadása biztosítja a kockázatos és kockázatos bejelentkezési jelentésekhez megjelölt felhasználókat. A jelentések részletességi szintje azonban különbözik a kiadások között: 

- A **ingyenes Azure Active Directory és az alapszintű kiadásokban** a kockázatok és a kockázatos bejelentkezések számára megjelölt felhasználók listája jelenik meg. 

- A **prémium szintű Azure Active Directory 1** kiadás kiterjeszti ezt a modellt azáltal, hogy megvizsgálja az egyes jelentésekhez észlelt mögöttes kockázati észlelések némelyikét is. 

- A **prémium szintű Azure Active Directory 2** kiadás a legrészletesebb információkat tartalmazza a mögöttes kockázati észlelésekről, és lehetővé teszi olyan biztonsági szabályzatok konfigurálását, amelyek automatikusan reagálnak a konfigurált kockázati szintekre.


## <a name="activity-reports"></a>Tevékenységjelentések

A tevékenységgel kapcsolatos jelentések segítenek megérteni a szervezet felhasználói viselkedését. Az Azure AD-ben kétféle tevékenységi jelentés létezik:

- **Naplók** – A [naplók tevékenységjelentés](concept-audit-logs.md) hozzáférést nyújt a bérlőn elvégzett összes feladat előzményeihez.

- **Bejelentkezések** – A [bejelentkezések tevékenységjelentéssel](concept-sign-ins.md) meghatározhatja, hogy ki hajtotta végre a naplók jelentés által jelentett feladatokat.



> [!VIDEO https://www.youtube.com/embed/ACVpH6C_NL8]




### <a name="audit-logs-report"></a>Naplók jelentés 

A [naplók jelentés](concept-audit-logs.md) a rendszertevékenységek rekordjait tartalmazza megfelelőségi célokból. Ezek az adatok lehetővé teszik például a következő gyakori helyzetek kezelését:

- A bérlőmben valaki hozzáfért egy rendszergazdai csoporthoz. Ki biztosított neki hozzáférést? 

- Tudni szeretném az egy adott alkalmazásba bejelentkező felhasználók listáját, mert nemrég helyeztem üzembe az alkalmazást, és látni szeretném, hogy jól működik-e

- Tudni szeretném, hogy hány jelszó-visszaállítás történik a bérlőmön


#### <a name="what-azure-ad-license-do-you-need-to-access-the-audit-logs-report"></a>Milyen Azure AD-licencre van szükség a naplójelentéshez való hozzáféréshez?  

A naplójelentés olyan szolgáltatásokhoz érhető el, amelyekhez licenccel rendelkezik. Ha rendelkezik licenccel egy adott szolgáltatáshoz, annak naplóinformációihoz is hozzáfér. A [Azure Active Directory díjszabási oldalán](https://azure.microsoft.com/pricing/details/active-directory/)megtekintheti a deatiled funkcióinak összehasonlítását a [különböző típusú licencek](../fundamentals/active-directory-whatis.md#what-are-the-azure-ad-licenses) esetében. További részletek: [Azure Active Directory szolgáltatások és képességek](../fundamentals/active-directory-whatis.md#which-features-work-in-azure-ad).

### <a name="sign-ins-report"></a>Bejelentkezések jelentés

A [bejelentkezések jelentés](concept-sign-ins.md) lehetővé teszi a következő kérdésekre adott válaszok megkeresését:

- Milyen egy adott felhasználó bejelentkezési mintázata?
- Hány felhasználó jelentkezett be egy adott héten?
- Milyen állapotúak ezek a bejelentkezések?

#### <a name="what-azure-ad-license-do-you-need-to-access-the-sign-ins-activity-report"></a>Milyen Azure AD-licencre van szükség a bejelentkezési tevékenységjelentéshez való hozzáféréshez?  

A bejelentkezési tevékenységjelentés eléréséhez a bérlőnek prémium szintű Azure AD-licenccel kell rendelkeznie.

## <a name="programmatic-access"></a>Szoftveres hozzáférés

A felhasználói felületen kívül az Azure AD [programozott hozzáférést](concept-reporting-api.md) is biztosít a jelentések adatokhoz a REST-alapú API-k segítségével. Különböző programnyelvekkel és eszközökkel hívhatja ezeket az API-kat. 

## <a name="next-steps"></a>Következő lépések

- [Kockázatos bejelentkezési jelentések](../identity-protection/overview-identity-protection.md)
- [Naplók jelentés](concept-audit-logs.md)
- [Bejelentkezési naplók jelentés](concept-sign-ins.md)