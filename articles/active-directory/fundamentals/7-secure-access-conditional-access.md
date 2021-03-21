---
title: Külső hozzáférés kezelése Azure Active Directory feltételes hozzáféréssel
description: Azure Active Directory feltételes hozzáférési szabályzatok használata az erőforrásokhoz való külső hozzáférés biztonságossá tételéhez.
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
ms.openlocfilehash: 64209a4d9ca200c69783a4ae317b38beef8ded89
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98222309"
---
# <a name="manage-external-access-with-conditional-access-policies"></a>Külső hozzáférés kezelése feltételes hozzáférési szabályzatokkal 

A [feltételes hozzáférés](../conditional-access/overview.md) az az eszköz, amelyet az Azure ad használ a jelek összekapcsolására, a házirendek betartatására, valamint annak meghatározására, hogy a felhasználó számára engedélyezett-e az erőforrásokhoz való hozzáférés. A feltételes hozzáférési szabályzatok létrehozásáról és használatáról (feltételes hozzáférési szabályzatok) a [feltételes hozzáférés központi telepítésének megtervezése](../conditional-access/plan-conditional-access.md)című témakörben olvashat részletesen. 

![Feltételes hozzáférési jelek és döntések diagramja](media/secure-external-access//7-conditional-access-signals.png)



Ez a cikk a feltételes hozzáférési szabályzatok külső felhasználókra való alkalmazását ismerteti, és feltételezi, hogy nem rendelkezik hozzáféréssel a [jogosultságok kezeléséhez](../governance/entitlement-management-overview.md) szükséges funkciókhoz. A feltételes hozzáférési szabályzatok a jogosultságok kezelése mellett is használhatók.

A dokumentumkészlet korábbi részében [létrehozott egy biztonsági tervet](3-secure-access-plan.md) , amely a következőket ismerteti:

* Az alkalmazások és az erőforrások ugyanazokkal a biztonsági követelményekkel rendelkeznek, és a hozzáféréshez csoportosíthatók.

* A külső felhasználókra vonatkozó bejelentkezési követelmények.

Ezt a csomagot fogja használni a külső hozzáféréshez szükséges feltételes hozzáférési szabályzatok létrehozásához. 

> [!IMPORTANT]
> Hozzon létre néhány külső felhasználói teszt fiókot, hogy tesztelni tudja a létrehozott házirendeket, mielőtt alkalmazná azokat az összes külső felhasználóra.

## <a name="conditional-access-policies-for-external-access"></a>Feltételes hozzáférési szabályzatok külső hozzáféréshez

Az alábbiakban a külső hozzáférés feltételes hozzáférési házirendekkel való szabályozásával kapcsolatos ajánlott eljárásokat ismertetjük.

* Ha nem használhat csatlakoztatott szervezeteket a jogosultságok kezelése szolgáltatásban, hozzon létre egy Azure AD biztonsági csoportot vagy Microsoft 365 csoportot minden olyan partnerszervezet számára, amellyel együttműködik. Rendelje hozzá az adott partner összes felhasználóját a csoporthoz. Ezeket a csoportokat a feltételes hozzáférési házirendekben használhatja.

* A lehető legkevesebb feltételes hozzáférési házirendet hozzon létre. Az azonos hozzáférési igényekkel rendelkező alkalmazások esetében vegye fel őket ugyanahhoz a szabályzathoz.  
‎ 
   > [!NOTE]
   > A feltételes hozzáférési szabályzatok legfeljebb 250 alkalmazást igényelhetnek. Ha több mint 250 alkalmazásnak ugyanaz a hozzáférési igénye, duplikált szabályzatokat hozzon létre. Az A szabályzat a 1-250-es alkalmazásokra vonatkozik, a B házirend a 251-500-es és egyéb alkalmazásokra is érvényes lesz.

* Egyértelmű elnevezési konvencióval rendelkező külső hozzáférésre vonatkozó szabályzatok. Az egyik elnevezési konvenció *ExternalAccess_actiontaken_AppGroup*. Például ExternalAccess_Block_FinanceApps.

## <a name="block-all-external-users-from-resources"></a>Az összes külső felhasználó letiltása az erőforrásokból

Letilthatja a külső felhasználók számára, hogy a feltételes hozzáférési szabályzatokkal hozzáférjenek az erőforrások meghatározott csoportjaihoz. Miután meghatározta, hogy milyen erőforrásokhoz kívánja letiltani a hozzáférést, hozzon létre egy szabályzatot.

Olyan házirend létrehozása, amely letiltja a külső felhasználók hozzáférését egy adott alkalmazáshoz:

1. Nyissa meg a **Azure Portal**, válassza a **Azure Active Directory** lehetőséget, válassza a **Biztonság**, majd a **feltételes hozzáférés** lehetőséget.

2. Válassza az **új szabályzat** lehetőséget, és írjon be egy **nevet**, például ExternalAccess_Block_FinanceApps

3. Válassza **a felhasználók és a csoportok** lehetőséget. A beágyazás lapon válassza a **felhasználók és csoportok kiválasztása**, majd a **minden vendég és a külső felhasználók** lehetőséget. 

4. Válassza a **kizárás** lehetőséget, és adja meg a rendszergazdai csoport (ok) t és a vészhelyzeti hozzáférési (break-Glass) fiókokat.

5. Válassza a **Felhőbeli alkalmazások vagy műveletek** lehetőséget, válassza az **alkalmazások kiválasztása** lehetőséget, válassza ki az összes olyan alkalmazást, amelynél le szeretné tiltani a külső hozzáférést, majd válassza a **kiválasztás** lehetőséget.

6. Válassza a **feltételek** lehetőséget, válassza a **helyek** lehetőséget, a konfigurálás területen válassza az **Igen**, majd **a tetszőleges helyet**.

7. A hozzáférés-vezérlés területen válassza a **támogatás** lehetőséget, módosítsa a váltás **blokkra**, majd válassza a **kiválasztás** lehetőséget.

8. Győződjön meg arról, hogy a házirend engedélyezése beállítás **csak jelentésre** van beállítva, majd válassza a **Létrehozás** lehetőséget.

## <a name="block-external-access-to-all-except-specific-external-users"></a>Külső hozzáférés letiltása az egyes külső felhasználók kivételével

Előfordulhat, hogy egyes esetekben le szeretné tiltani a külső felhasználókat, kivéve egy adott csoportot. Előfordulhat például, hogy le szeretné tiltani az összes külső felhasználót, kivéve azokat, akik a pénzügyi alkalmazásokból dolgoznak a pénzügyi csapatnál. Ehhez tegye a következőket:

1. Hozzon létre egy biztonsági csoportot, amely a pénzügyi csoportot elérő külső felhasználókat szeretné tárolni.

2. Kövesse az 1-3-es lépéseket a külső hozzáférés letiltása a fenti erőforrásokból című témakörben.

3. A 4. lépésben adja hozzá azt a biztonsági csoportot, amelyet ki szeretne zárni a pénzügyi alkalmazásoktól.

4. Hajtsa végre a további lépéseket.

## <a name="implement-conditional-access"></a>Feltételes hozzáférés megvalósítása

Számos gyakori feltételes hozzáférési szabályzat dokumentálva van. Tekintse meg a következőt, amely a külső felhasználók számára is alkalmazkodik.

* [Multi-Factor Authentication megkövetelése minden felhasználó számára](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)

* [Felhasználói kockázatalapú feltételes hozzáférés](../conditional-access/howto-conditional-access-policy-risk-user.md)

* [A nem megbízható hálózatokból való hozzáféréshez Multi-Factor Authentication szükséges](../conditional-access/untrusted-networks.md) 

* [Használati feltételek megkövetelése](../conditional-access/terms-of-use.md)

## <a name="next-steps"></a>Következő lépések

Tekintse meg a következő cikkeket az erőforrásokhoz való külső hozzáférés biztonságossá tételéhez. Javasoljuk, hogy a felsorolt sorrendben végezze el a műveleteket.

1. [A kívánt biztonsági helyzet meghatározása a külső hozzáféréshez](1-secure-access-posture.md)

2. [Aktuális állapot felderítése](2-secure-access-current-state.md)

3. [Irányítási terv létrehozása](3-secure-access-plan.md)

4. [Biztonsági csoportok használata](4-secure-access-groups.md)

5. [Áttérés az Azure AD B2B-re](5-secure-access-b2b.md)

6. [Biztonságos hozzáférés a jogosultságok kezelésével](6-secure-access-entitlement-managment.md)

7. [Biztonságos hozzáférés feltételes hozzáférési házirendekkel](7-secure-access-conditional-access.md) (itt)

8. [Biztonságos hozzáférés érzékenységi címkékkel](8-secure-access-sensitivity-labels.md)

9. [Biztonságos hozzáférés a Microsoft Teams, a OneDrive és a SharePoint rendszerhez](9-secure-access-teams-sharepoint.md)
