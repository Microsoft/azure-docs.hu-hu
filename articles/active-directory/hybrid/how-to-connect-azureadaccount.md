---
title: Az Azure AD Connector-fiók jelszavának módosítása | Microsoft Docs
description: Ez a témakör az Azure AD Connector-fiók visszaállítására szolgáló dokumentumot ismerteti.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6077043a-27f1-4304-a44b-81dc46620f24
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: e4f31c560fe3dd91689b361ed520e466fd52da1c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "85360010"
---
# <a name="change-the-azure-ad-connector-account-password"></a>Az Azure AD Connector-fiók jelszavának módosítása
Az Azure AD Connector-fióknak ingyenes szolgáltatásnak kellene lennie. Ha alaphelyzetbe kell állítania a hitelesítő adatait, akkor ez a témakör Önnek szól. Ha például egy globális rendszergazda véletlenül alaphelyzetbe állítja a fiók jelszavát a PowerShell használatával.

## <a name="reset-the-credentials"></a>Hitelesítő adatok alaphelyzetbe állítása
Ha az Azure AD Connector-fiók hitelesítési problémák miatt nem tud kapcsolatba lépni az Azure AD-vel, akkor a jelszó alaphelyzetbe állítható.

1. Jelentkezzen be a Azure AD Connect Sync-kiszolgálóra, és indítsa el a PowerShellt.
2. Futtassa az `Add-ADSyncAADServiceAccount` parancsot.
   ![PowerShell-parancsmag addadsyncaadserviceaccount](./media/how-to-connect-azureadaccount/addadsyncaadserviceaccount.png)
3. Adja meg az Azure AD globális rendszergazdai hitelesítő adatait.

Ez a parancsmag alaphelyzetbe állítja a szolgáltatásfiók jelszavát, és frissíti azt az Azure AD-ben és a Szinkronizáló motorban is.

## <a name="known-issues-these-steps-can-solve"></a>A lépések által ismert problémák elhárítása
Ez a szakasz azoknak a hibáknak a listáját tartalmazza, amelyeket az Azure AD Connector-fiókban a hitelesítő adatok alaphelyzetbe állításával rögzítettek.

---
6900-es esemény: a kiszolgáló váratlan hibát észlelt a jelszó-módosítási értesítés feldolgozása során: AADSTS70002: hiba történt a hitelesítő adatok érvényesítése során. AADSTS50054: a rendszer a régi jelszót használja a hitelesítéshez.

---
Event 659 hiba történt a jelszóházirend-szinkronizálás konfigurációjának beolvasása közben. Microsoft. IdentityModel. clients. ActiveDirectory. AdalServiceException: AADSTS70002: hiba történt a hitelesítő adatok érvényesítése során. AADSTS50054: a rendszer a régi jelszót használja a hitelesítéshez.

## <a name="next-steps"></a>Következő lépések
**Áttekintő témakörök**

* [Azure AD Connect szinkronizálás: a szinkronizálás megismerése és testreszabása](how-to-connect-sync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)
