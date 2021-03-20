---
title: 'Azure AD Connect: szinkronizálási szolgáltatás példányai | Microsoft Docs'
description: Ez az oldal az Azure AD-példányokra vonatkozó különleges szempontokat dokumentálja.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: f340ea11-8ff5-4ae6-b09d-e939c76355a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 05/27/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c4d8b0a33763a967550453d8a205258f7583084
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90015262"
---
# <a name="azure-ad-connect-special-considerations-for-instances"></a>Azure AD Connect: a példányokra vonatkozó különleges szempontok
A Azure AD Connect leggyakrabban az Azure AD és a Microsoft 365 globális példányával együtt használják. Vannak azonban más példányok is, és ezek eltérő követelményekkel rendelkeznek az URL-címekhez és egyéb speciális megfontolásokhoz.

## <a name="microsoft-cloud-germany"></a>Microsoft Cloud Germany
A [Microsoft Cloud Németország](https://www.microsoft.de/cloud-deutschland) egy németországi adatkezelő által működtetett szuverén felhő.

| A proxykiszolgálóhoz megnyitható URL-címek |
| --- |
| \*. microsoftonline.de |
| \*.windows.net |
| + Visszavont tanúsítványok listája |

Amikor bejelentkezik az Azure AD-bérlőbe, egy fiókot kell használnia az onmicrosoft.de tartományban.

A Microsoft Cloud Németországban jelenleg nem találhatók szolgáltatások:

* A **jelszó visszaírási** a Azure ad Connect 1.1.570.0 és későbbi verziójának előzetes verziójához érhető el.
* Más prémium szintű Azure AD szolgáltatások nem érhetők el.

## <a name="microsoft-azure-government"></a>Microsoft Azure Government
A [Microsoft Azure Government felhő](https://azure.microsoft.com/features/gov/) az Egyesült Államok kormányának felhője.

Ezt a felhőt az rSync korábbi kiadásai támogatják. A Azure AD Connect build 1.1.180 a felhő következő generációja támogatott. Ez a generáció csak a US-alapú végpontokat használja, és más URL-címekkel rendelkezik, amelyeket meg kell nyitni a proxykiszolgálóhoz.

| A proxykiszolgálóhoz megnyitható URL-címek |
| --- |
| \*.microsoftonline.com |
| \*.microsoftonline.us |
| \*. windows.net (az automatikus Azure Government bérlői észleléshez szükséges) |
| \*. gov.us.microsoftonline.com |
| + Visszavont tanúsítványok listája |

> [!NOTE]
> A Azure AD Connect verzió 1.1.647.0 kezdve a AzureInstance értékének a beállításjegyzékben való beállítása már nem szükséges, feltéve, hogy a *. windows.net meg van nyitva a proxykiszolgáló (ek) ben. Azonban azon ügyfelek esetében, akik nem engedélyezik az internetkapcsolatot a Azure AD Connect-kiszolgáló (k) on, a következő manuális konfiguráció használható.

### <a name="manual-configuration"></a>Manuális konfiguráció

A következő kézi konfigurációs lépések segítségével biztosítható, hogy a Azure AD Connect Azure Government szinkronizálási végpontokat használjanak.

1. Indítsa el a Azure AD Connect telepítést.
2. Ha megjelenik az első olyan oldal, amelyben el kellene fogadnia a végfelhasználói licencszerződést, ne folytassa a telepítést, de ne hagyja a telepítő varázslót.
3. Indítsa el a regeditt, és módosítsa a beállításkulcsot az `HKLM\SOFTWARE\Microsoft\Azure AD Connect\AzureInstance` értékre `4` .
4. Lépjen vissza a Azure AD Connect telepítővarázsló, fogadja el a végfelhasználói licencszerződést, és folytassa a következővel:. A telepítés során győződjön meg arról, hogy az **Egyéni konfiguráció** telepítési útvonalát (és nem az expressz telepítést) használja, majd a szokásos módon folytassa a telepítést.

## <a name="next-steps"></a>Következő lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).
