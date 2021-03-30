---
title: A Azure Active Directory-portálon kockázatra megjelölt felhasználók | Microsoft Docs
description: Ismerje meg az Azure Active Directory portál kockázatosként megjelölt felhasználókról szóló biztonsági jelentését
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23bd7a26bc6215f50a2860ab4aac84f1b46301f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92308946"
---
# <a name="remediate-users-flagged-for-risk-in-the-azure-active-directory-portal"></a>Kockázatosként megjelölt felhasználók szervizelése az Azure Active Directory portálon

A Azure Active Directory (Azure AD) biztonsági jelentéseivel a környezetben feltört felhasználói fiókok valószínűsége is mérhető. A kockázatosként megjelölt felhasználó egy olyan felhasználói fiókot jelöl, amelynek elképzelhető, hogy sérült a biztonsága.

A Microsoft elkötelezte magát az Ön környezeteinek védelmére. Ezen kötelezettségvállalás részeként a Microsoft folyamatosan monitorozza a szokatlan vagy az ismert támadási mintákat követő tevékenységeket. 

Ha a rendszer olyan szokatlan tevékenységeket észlel, amelyek egy bizonyos felhasználói fiókhoz való jogosulatlan hozzáférést jelezhetnek, akkor értesítést kap, amely lehetővé teszi a művelet elvégzését. Ez nem jelenti azt, hogy a Microsoft saját rendszereit feltörték.

## <a name="access-the-users-flagged-for-risk-report"></a>A kockázatosként megjelölt felhasználókról szóló jelentés elérése

Áttekintheti a kockázatnak kitett felhasználókat a Azure Portal [kockázati jelentésében lévő felhasználók](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RiskyUsers) számára. Ha nem rendelkezik Azure AD-val, regisztráljon ingyenesen a következő címen: [https://aka.ms/AccessAAD](https://aka.ms/AccessAAD) . 

A kockázati jelentésre megjelölt felhasználók a következő műveleteket hajtják végre minden felhasználónál:

- Ideiglenes jelszó létrehozása
- Biztonságos jelszóváltoztatás megkövetelése a felhasználótól a következő bejelentkezéskor
- A felhasználó kockázatos voltának figyelmen kívül hagyása szervizelési művelet nélkül.

További információ: [a kockázat biztonsági jelentésére megjelölt felhasználók](../identity-protection/overview-identity-protection.md).

### <a name="azure-ad-subscription-for-microsoft-365-customers"></a>Azure AD-előfizetés Microsoft 365 ügyfeleknek

A Microsoft 365 hitelesítő adatait is használhatja az **Azure felügyeleti központ** eléréséhez. Miután aktiválta Azure AD-hozzáférését, a rendszer átirányítja az Azure AD portáljára. Az alapszintű előfizetési szinten a jelentésekben biztosított adatok mennyisége korlátozott. Az Azure Premium-előfizetők további adatokat és elemzéseket érhetnek el.

A kockázati jelentésekhez **megjelölt felhasználók** elérése a Microsoft 365 felügyeleti központban:

1.  A bal oldali navigációs menüből válassza a **felügyeleti központok** lehetőséget. 
2.  Válassza az **Azure ad** lehetőséget.
3.  Jelentkezzen be az **Azure Active Directory felügyeleti központba**.
4.  Ha megjelenik egy szalagcím az oldal tetején, amely azt jelzi, hogy az **új portálra** kattint, válassza ki a hivatkozást.
4.  A bal oldali navigációs menüben válassza a **Azure Active Directory** lehetőséget. 
5.  A navigációs ablaktáblán válassza ki a **Biztonság** szakaszban a **kockázatként megjelölt felhasználókat** .

## <a name="remediation-actions"></a>Szervizelési műveletek

Végezze el a következő műveleteket az érintett fiókok javításához és a környezet biztonságossá tételéhez:

1.  A többtényezős hitelesítés és az önkiszolgáló jelszó-visszaállítás [helyes adatainak ellenőrzése](https://aka.ms/MFAValid) . 
2.  [Engedélyezze a többtényezős hitelesítést](/microsoft-365/admin/security-and-compliance/set-up-multi-factor-authentication) az összes felhasználó számára. 
3.  Használja ezt a [szervizelési parancsfájlt](https://aka.ms/remediate) minden érintett fiókhoz, hogy automatikusan végrehajtsa a következő lépéseket: 

    a. Jelszó alaphelyzetbe állítása a fiók biztonságossá tételéhez és az aktív munkamenetek megöléséhez.

    b. A postaláda meghatalmazottjainak eltávolítása.

    c. A külső tartományokra irányuló levéltovábbítási szabályok letiltása.

    d. A globális levéltovábbítási tulajdonság eltávolítása a postaládára vonatkozóan.

    e. MFA engedélyezése a felhasználó fiókján.

    f. Magas jelszóösszetettség beállítása a fiókhoz.

    : A postaláda-naplózás engedélyezése.

    h. Napló létrehozása a rendszergazdának a felülvizsgálathoz.

4. Vizsgálja meg Microsoft 365 bérlőjét és egyéb informatikai infrastruktúráját, beleértve a bérlői beállítások, a felhasználói fiókok és a felhasználónkénti konfigurációs beállítások áttekintését a lehetséges módosításokhoz. Keressen perzisztenciára utaló jeleket, valamint ellenőrizze, hogy egy esetleges támadónak a behatolási kísérlet során sikerült-e hozzáférnie a VPN-hitelesítőadatokhoz vagy más vállalati erőforrásokhoz. 

5.  A vizsgálat részeként gondolja át, hogy értesítenie kell-e a kormányzati hatóságokat, beleértve a bűnüldöző szerveket is.

Ezenkívül el a következőket kell elvégeznie:

- Olvassa el és implementálja ezt az [útmutatót a szokatlan tevékenységek kezeléséhez](/archive/blogs/office365security/how-to-fix-a-compromised-hacked-microsoft-office-365-account). 
- [A naplózási folyamat lehetővé teszi](/archive/blogs/office365security/using-office-365-activity-data-to-improve-your-cybersecurity-stance-and-capability) a tevékenység elemzését a bérlőben. Ha elkészült, a naplózási tároló megkezdi a tevékenységek naplóinak feltöltését. Ezen a ponton a [biztonsági és megfelelőségi központ keresési és vizsgálati erőforrásait](https://aka.ms/sccsearch)is kihasználhatja. 
- Tekintse át az összes postafiók engedélyek delegálásával és levelek továbbításával kapcsolatos szabályait. Ezzel a [PowerShell-szkripttel](https://aka.ms/delegateforwardrules) végezheti el ezt a feladatot. 

## <a name="next-steps"></a>Következő lépések

* [Azure Active Directory Identity Protection](../identity-protection/overview-identity-protection.md)
* [Kockázatosként megjelölt felhasználók](../identity-protection/overview-identity-protection.md)