---
title: Identitásadatok tárolása ausztráliai és új-zélandi ügyfelek számára – Azure AD
description: Megtudhatja, Azure Active Directory hogyan tárolja az ausztrál és új-zélandi ügyfelek identitással kapcsolatos adatait.
services: active-directory
author: ajburnle
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2019
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4bb095e93a3728835e26cbe283f79569c91b7487
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479066"
---
# <a name="identity-data-storage-for-australian-and-new-zealand-customers-in-azure-active-directory"></a>Identitásadatok tárolása ausztráliai és új-zélandi ügyfelek számára Azure Active Directory

Az identitásadatokat az Azure AD egy földrajzi helyen tárolja a szervezet által a Microsoft Online-szolgáltatásra, például a Microsoft 365 azure-ba való feliratkozáskor megadott cím alapján. Az identitás-ügyféladatok tárolására vonatkozó információkért használhatja a Microsoft Adatvédelmi központ Hol találhatók az [adatai?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) című szakaszát.

> [!NOTE]
> Az Azure AD-val integrált szolgáltatások és alkalmazások hozzáférhetnek az identitás-ügyféladatokhoz. Értékelje ki az egyes használt adatokat és alkalmazásokat annak meghatározásához, hogy az adott szolgáltatás és alkalmazás hogyan feldolgozta az identitás-ügyféladatokat, és hogy megfelelnek-e a vállalat adattárolási követelményeinek. A Microsoft-szolgáltatások adattárolási helyével kapcsolatban a Microsoft biztonsági és adatkezelési központ az adatok tárolási helyével foglalkozó szakaszában találhat további információt.

Azok az ügyfelek, akik Ausztráliában vagy Új-Zélandon biztosítanak címet, az Azure AD az ausztráliai adatközpontokban őrzi meg ezen szolgáltatások identitási adatait: 
- Azure AD-címtárkezelés 
- Hitelesítés

Minden más Azure AD-szolgáltatás globális adatközpontokban tárolja az ügyféladatokat. A szolgáltatás adatközpontját a következő helyen [találja: Azure Active Directory – Hol találhatók az adatai?](https://aka.ms/AADDataMap)

## <a name="microsoft-azure-ad-multi-factor-authentication-mfa"></a>Microsoft Azure AD Többtényezős hitelesítés (MFA)

Az MFA az identitás-ügyféladatokat a globális adatközpontokban tárolja. A felhőalapú alkalmazások és szolgáltatások által gyűjtött és tárolt felhasználói adatokkal kapcsolatos Azure AD MFA és Azure MFA-kiszolgáló Az [Azure Multi-Factor Authentication](../authentication/concept-mfa-data-residency.md)felhasználói adatgyűjtésecímű cikk nyújt további tájékoztatást.

## <a name="next-steps"></a>Következő lépések
A fent ismertetett funkciókkal és funkciókkal kapcsolatos további információkért tekintse meg az alábbi cikkeket:
- [Mi az a többtényezős hitelesítés?](../authentication/concept-mfa-howitworks.md)
