---
title: A Azure Active Directory erőforrásaihoz való külső hozzáférés biztonságossá tétele
description: Útmutató az építészek és a rendszergazdák számára a belső erőforrásokhoz való külső hozzáférés biztonságossá tételéhez
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39300c887245a99dce2913661f6492c85a1e6722
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/23/2020
ms.locfileid: "97743962"
---
# <a name="securing-external-access-to-resources"></a>Az erőforrásokhoz való külső hozzáférés biztonságossá tétele

A külső partnerekkel való biztonságos együttműködés biztosítja, hogy a megfelelő külső partnerek megfelelő hozzáférést biztosítanak a belső erőforrásokhoz a megfelelő időtartamra. A holisztikus irányítási megközelítés segítségével csökkentheti a biztonsági kockázatokat, teljesítheti a megfelelőségi célokat, és meggyőződhet arról, hogy ki férhet hozzá.

A nem szabályozott együttműködés a hozzáférés tulajdonjogának egyértelművé tételét, valamint a bizalmas erőforrások elérhetővé tételének lehetőségét eredményezi. A biztonságos és szabályozott együttműködésre való áttéréssel gondoskodhat arról, hogy a külső felhasználók számára egyértelmű legyen a tulajdonjog és az elszámoltathatóság. Ide tartoznak az alábbiak:

* A külső szervezetek és a bennük lévő felhasználók kezelése, amelyeknek hozzáférésük van az erőforrásokhoz.

* A hozzáférés megfelelő, áttekintett és időkorlátjának biztosítása.

* Az üzleti tulajdonosok képessé tétele az együttműködésre az IT-részlegen belül létrehozott Guard Rails-ben.

Ha meg kell felelnie a megfelelőségi keretrendszereknek, a szabályozott együttműködés lehetővé teszi, hogy igazolja a hozzáférés megfelelőségét.

A Microsoft átfogó eszközöket kínál a külső hozzáférés biztonságos eléréséhez.  Azure Active Directory (Azure AD) B2B-együttműködés bármely külső együttműködési terv középpontjában áll. Az Azure AD B2B integrálható az Azure AD más eszközeivel és a Microsoft 365-szolgáltatások eszközeivel a külső hozzáférés biztonságossá tételéhez és kezeléséhez.

A dokumentumkészlet úgy lett kialakítva, hogy lehetővé tegye, hogy az ad hoc vagy lazán irányított külső együttműködésből egy biztonságosabb állapotba lépjen. 

## <a name="next-steps"></a>További lépések

Tekintse meg a következő cikkeket az erőforrásokhoz való külső hozzáférés biztonságossá tételéhez. Javasoljuk, hogy a felsorolt sorrendben végezze el a műveleteket.


1. [A kívánt biztonsági helyzet meghatározása a külső hozzáféréshez](1-secure-access-posture.md)

2. [Aktuális állapot felderítése](2-secure-access-current-state.md)

3. [Irányítási terv létrehozása](3-secure-access-plan.md)

4. [Biztonsági csoportok használata](4-secure-access-groups.md)

5. [Áttérés az Azure AD B2B-re](5-secure-access-b2b.md)

6. [Biztonságos hozzáférés a jogosultságok kezelésével](6-secure-access-entitlement-managment.md)

7. [Biztonságos hozzáférés feltételes hozzáférési szabályzatokkal](7-secure-access-conditional-access.md)

8. [Biztonságos hozzáférés érzékenységi címkékkel](8-secure-access-sensitivity-labels.md)

9. [Biztonságos hozzáférés a Microsoft Teams, a OneDrive és a SharePoint rendszerhez](9-secure-access-teams-sharepoint.md)
