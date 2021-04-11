---
title: Vállalati munkahelyi fiókok és partner központ
description: Hogyan ellenőrizhető, hogy a vállalata rendelkezik-e a Microsofttal beállított munkahelyi fiókkal, hozzon létre egy új munkahelyi fiókot, vagy állítson be több munkahelyi fiókot a partner centerrel való használatra.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: parthpandyaMSFT
ms.author: parthp
ms.date: 05/30/2019
ms.openlocfilehash: 69e9f9d6d7d132476796a1cdfdaaa2490457640c
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108273"
---
# <a name="company-work-accounts-and-partner-center"></a>Vállalati munkahelyi fiókok és partner központ

A partner Center vállalati munkahelyi fiókokat, más néven Azure Active Directory (AD) bérlőket használ a fiókok hozzáférésének kezeléséhez több felhasználóhoz, a vezérlési engedélyekhez, a gazdagépekhez és az alkalmazásokhoz, valamint a profilok karbantartásához. Ha a vállalata munkahelyi e-mail-fiókjának tartományát összekapcsolja a partner Center-fiókkal, a vállalat alkalmazottai a saját munkahelyi fiókjának felhasználónevével és jelszavával felügyelhetik a piactéren a piactéren elérhető ajánlatokat.

## <a name="check-whether-your-company-already-has-a-work-account"></a>Győződjön meg arról, hogy a vállalata már rendelkezik munkahelyi fiókkal

Ha vállalata előfizetett egy Microsoft Cloud Service-re, például az Azure-ra, Microsoft Intunera vagy Microsoft 365ra, akkor már rendelkezik egy munkahelyi e-mail-fiókkal (más néven a Azure Active Directory Bérlővel), amelyet a partner Center használatával lehet használni.

A következő lépésekkel ellenőrizheti:
1. Jelentkezzen be az Azure felügyeleti portálján a következő címen: https://portal.azure.com .
2. Válassza a bal oldali navigációs menü **Azure Active Directory** elemét, majd válassza az **Egyéni tartománynevek** lehetőséget.
3. Ha már rendelkezik munkahelyi fiókkal, a rendszer a tartománynevet fogja listázni.

Ha a vállalat még nem rendelkezik munkahelyi fiókkal, a rendszer létrehoz egy fiókot a partner Center regisztrációs folyamat során.

## <a name="set-up-multiple-work-accounts"></a>Több munkahelyi fiók beállítása

Mielőtt megkezdené a munkahelyi fiók használatát, gondolja át, hogy a munkahelyi fiók hány felhasználójának kell hozzáférnie a partner központhoz. Ha a munkahelyi fiókban olyan felhasználók vannak, akik nem szeretnének hozzáférni a partner központhoz, érdemes lehet több munkahelyi fiókot létrehoznia, így csak azok a felhasználók jelennek meg, akik hozzáférhetnek a partner központhoz egy adott fiókon.

## <a name="create-a-new-work-account"></a>Új munkahelyi fiók létrehozása

Ha új munkahelyi fiókot szeretne létrehozni a vállalat számára, kövesse az alábbi lépéseket. Előfordulhat, hogy segítségre van szüksége a vállalat Microsoft Azure fiókjához tartozó rendszergazdai engedélyekkel.

1. Jelentkezzen be a [Microsoft Azure Portalra](https://portal.azure.com).
2. A bal oldali navigációs menüben válassza ki a **Azure Active Directory**  ->  **felhasználókat**.
3. Válassza az **új felhasználó** lehetőséget, és hozzon létre egy új Azure Work-fiókot a név és az e-mail cím megadásával. Győződjön meg arról, hogy a **címtárbeli szerepkör** a **felhasználó** értékre van állítva, és a lenti **jelszó megjelenítése** jelölőnégyzet bejelölésével megtekintheti és megjelenítheti az automatikusan generált jelszót.
4. Válassza a **Létrehozás** lehetőséget az új felhasználó mentéséhez.

A felhasználói fiók e-mail-címének ellenőrzött tartománynévnek kell lennie a címtárban. A címtárban lévő összes ellenőrzött tartományt kilistázhatja   ->  a bal oldali navigációs menüben Azure Active Directory **Egyéni tartománynevek** lehetőség kiválasztásával.

Az egyéni tartományok Azure Active Directoryban való hozzáadásával kapcsolatos további tudnivalókért lásd: [tartomány hozzáadása vagy hozzárendelése az Azure ad-ben](/azure/active-directory/fundamentals/add-custom-domain).

## <a name="troubleshoot-work-email-sign-in"></a>Munkahelyi e-mailek bejelentkezésének hibakeresése

Ha nem sikerül bejelentkeznie a munkahelyi fiókjába (más néven az Azure AD-bérlőbe), keresse meg az alábbi ábrát, amely a legjobban megfelel a helyzetnek, és kövesse az ajánlott lépéseket.

![A munkahelyi fiók bejelentkezési hibaelhárításának ábrája](./media/manage-accounts/onboarding-aad-flow.png)

## <a name="next-steps"></a>Következő lépések

- [A kereskedelmi piactér-fiók kezelése a partner Centerben](./manage-account.md)