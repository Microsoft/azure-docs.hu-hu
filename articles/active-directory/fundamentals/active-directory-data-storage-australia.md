---
title: Identitás-adattárolás az ausztrál és az új-zélandi ügyfelek számára – Azure AD
description: Megtudhatja, hol tárolja a Azure Active Directory az identitással kapcsolatos adatait az ausztrál és az új-zélandi ügyfelek számára.
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
ms.openlocfilehash: 498b75a6541da97e57e23465ae6eb23a6c854727
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94836954"
---
# <a name="identity-data-storage-for-australian-and-new-zealand-customers-in-azure-active-directory"></a>Identitás-adattárolás az ausztráliai és új-zélandi ügyfelek számára Azure Active Directory

Az identitás adatait az Azure AD tárolja földrajzi helyen a szervezete által megadott, a Microsoft Online szolgáltatásra (például Microsoft 365 és az Azure-ra való feliratkozáskor) alapuló címen. További információ a személyazonossági adatok tárolásáról: a Microsoft adatvédelmi központjának [Hol található az adatai?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) szakasza.

> [!NOTE]
> Az Azure AD-vel integrált szolgáltatások és alkalmazások hozzáférhetnek az identitás felhasználói adataihoz. Értékelje ki az egyes szolgáltatásait és alkalmazásait, hogy megtudja, hogyan dolgozza fel az Identity Customer-adatait az adott szolgáltatás és alkalmazás, és hogy megfelelnek-e a vállalat adattárolási követelményeinek. A Microsoft-szolgáltatások adattárolási helyével kapcsolatban a Microsoft biztonsági és adatkezelési központ az adatok tárolási helyével foglalkozó szakaszában találhat további információt.

Az ausztráliai vagy új-zélandi címeket szolgáltató ügyfelek esetében az Azure AD az ausztráliai adatközpontokban tárolja a szolgáltatásokhoz tartozó személyazonossági adatait: 
- Azure AD-címtár kezelése 
- Hitelesítés

Az összes többi Azure AD-szolgáltatás a globális adatközpontokban tárolja az ügyféladatokat. A szolgáltatás adatközpontjának megkereséséhez lásd: [Azure Active Directory – hol találhatók az adatai?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)

## <a name="microsoft-azure-ad-multi-factor-authentication-mfa"></a>Microsoft Azure AD Multi-Factor Authentication (MFA)

Az MFA a globális adatközpontokban tárolja a személyazonossági ügyféladatokat. Ha többet szeretne megtudni a felhőalapú Azure AD MFA és az Azure MFA-kiszolgáló által gyűjtött és tárolt felhasználói adatokról, tekintse meg az [azure multi-Factor Authentication felhasználói adatok gyűjtését](../authentication/concept-mfa-data-residency.md)ismertető témakört.

## <a name="next-steps"></a>Következő lépések
A fent ismertetett szolgáltatásokkal és funkciókkal kapcsolatos további információkért tekintse meg a következő cikkeket:
- [Mi az a többtényezős hitelesítés?](../authentication/concept-mfa-howitworks.md)