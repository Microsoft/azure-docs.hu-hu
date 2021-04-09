---
title: Hozzáférési kérelmek jóváhagyása vagy megtagadása – Azure AD-jogosultságok kezelése
description: Megtudhatja, hogyan engedélyezheti vagy tagadhatja meg a hozzáférési csomagra irányuló kérelmeket Azure Active Directory jogosultságok kezelésében a saját hozzáférési portál használatával.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: fddb3b171e5a26273cb2e0045f11e3a4dbb48c5f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97655101"
---
# <a name="approve-or-deny-access-requests-in-azure-ad-entitlement-management"></a>Hozzáférési kérelmek jóváhagyása vagy elutasítása az Azure AD-jogosultságok kezelésében

Az Azure AD-jogosultságok kezelésével házirendeket állíthat be a hozzáférési csomagok jóváhagyásának megköveteléséhez, és egy vagy több jóváhagyót is választhat. Ez a cikk azt ismerteti, hogyan hagyhatják jóvá vagy tagadhatják meg a kijelölt jóváhagyók a hozzáférési csomagok kérelmeit.

## <a name="open-request"></a>Kérelem megnyitása

A hozzáférési kérelmek jóváhagyásának vagy megtagadásának első lépéseként meg kell keresni és meg kell nyitni a hozzáférési kérelmet függőben lévő jóváhagyást. A hozzáférési kérelem két módon nyitható meg.

**Előfeltételként szükséges szerepkör:** Jóváhagyó

1. Keressen olyan Microsoft Azure e-mailt, amely arra kéri, hogy hagyja jóvá vagy tagadja meg a kérést. Példa erre az e-mail címre:

    ![Hozzáférési kérelem jóváhagyása a csomag e-mail-címéhez](./media/entitlement-management-shared/approver-request-email.png)

1. A hozzáférési kérelem megnyitásához kattintson a **jóváhagyás vagy a Megtagadás kérelem** hivatkozásra.

1. Jelentkezzen be a saját hozzáférési portálra.

Ha nem rendelkezik az e-mail-címmel, a következő lépésekkel megtalálhatja a jóváhagyásra váró hozzáférési kérelmeket.

1. Jelentkezzen be a saját hozzáférési portálján a következő címen: [https://myaccess.microsoft.com](https://myaccess.microsoft.com) .  (Az USA kormányzati szervei számára a saját hozzáférési portál hivatkozásában található tartomány lesz `myaccess.microsoft.us` .)

1. A bal oldali menüben kattintson a **jóváhagyások** elemre, hogy megtekintse a hozzáférési kérelmek függőben lévő jóváhagyásának listáját.

1. A **függőben** lévő lapon keresse meg a kérelmet.

## <a name="view-requestors-answers-to-questions-preview"></a>A kérelmező kérdéseire adott válaszok megtekintése (előzetes verzió)

1. Nyissa meg a saját hozzáférés **jóváhagyások** lapját.

1. Lépjen a jóváhagyni kívánt kérelemre, és kattintson a **részletek** lehetőségre. Ha készen áll a döntésre, a **jóváhagyás** vagy a **Megtagadás** lehetőségre is kattinthat.

1. Kattintson a **kérelem részletei** elemre.

    ![Hozzáférési portál – hozzáférési kérelem – kattintson a kérelem részletei lehetőségre](./media/entitlement-management-request-approve/requestor-information-request-details.png)

1. A kérelmező által megadott információk a panel alján lesznek.

    ![A kérés részleteit megjelenítő képernyőkép](./media/entitlement-management-request-approve/requestor-information-requestor-answers.png)

1. A kérelmező által megadott információk alapján jóváhagyhatja vagy megtagadhatja a kérelmet. Tekintse meg az útmutató jóváhagyása vagy elutasítása című témakör lépéseit.

## <a name="approve-or-deny-request"></a>Kérelem jóváhagyása vagy elutasítása

Miután megnyitotta a hozzáférési kérelmek jóváhagyását, megtekintheti azokat a részleteket, amelyek segítségével jóváhagyhatja vagy megtagadhatja a döntést.

**Előfeltételként szükséges szerepkör:** Jóváhagyó

1. Kattintson a **nézet** hivatkozásra a hozzáférési kérés panel megnyitásához.

1. A hozzáférési kérelem részleteinek megtekintéséhez kattintson a **részletek** gombra.

    A részletek tartalmazzák a felhasználó nevét, szervezetét, a hozzáférési kezdési és befejezési dátumát, ha van ilyen, az üzleti indoklás, a kérelem elküldésekor, valamint a kérelem érvényességének lejárta után.

1. Kattintson a **jóváhagyás** vagy a **Megtagadás** gombra.

1. Szükség esetén adjon meg egy okot.

    ![Képernyőfelvétel: az a lap, amelyben elfogadják vagy megtagadják a kérést.](./media/entitlement-management-request-approve/my-access-approve-request.png)

1. A döntés elküldéséhez kattintson a **Submit (Küldés** ) gombra.

    Ha egy házirend több jóváhagyóval van konfigurálva, csak egy jóváhagyónak kell döntenie a függőben lévő jóváhagyásról. Miután egy jóváhagyó elküldte döntését a hozzáférési kérésnek, a kérés befejeződött, és már nem érhető el a többi jóváhagyó számára a kérelem jóváhagyásához vagy elutasításához. A többi jóváhagyó láthatja a kérelemre vonatkozó döntést és a döntéshozót a saját hozzáférési portálján. Jelenleg csak egy egyfázisú jóváhagyás támogatott.

    Ha a konfigurált jóváhagyók egyike sem tudja jóváhagyni vagy megtagadni a hozzáférési kérelmet, a kérelem érvényessége a konfigurált kérelem időtartama után lejár. A felhasználó értesítést kap arról, hogy a hozzáférési kérelem lejárt, és újra el kell küldenie a hozzáférési kérést.

## <a name="next-steps"></a>Következő lépések

- [Hozzáférési csomaghoz való hozzáférés kérése](entitlement-management-request-access.md)
- [Kérelmek feldolgozása és e-mail-értesítések](entitlement-management-process.md)
