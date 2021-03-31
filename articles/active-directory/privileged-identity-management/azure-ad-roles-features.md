---
title: Azure AD szerepkör-szolgáltatások a Privileged Identity Managementban | Microsoft Docs
description: Azure AD-szerepkörök kezelése hozzárendelési Privileged Identity Management (PIM)
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.topic: conceptual
ms.workload: identity
ms.date: 07/10/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4478c9c286c06d5d6c5593195a0e93abd286b8c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92371511"
---
# <a name="management-capabilities-for-azure-ad-roles-in-privileged-identity-management"></a>Az Azure AD-szerepkörök felügyeleti képességei Privileged Identity Management

Az Azure ad-szerepkörök kezelési felülete Privileged Identity Management frissült az Azure AD-szerepkörök és az Azure-erőforrás-szerepkörök kezelésének egységesítése érdekében. Korábban az Azure-beli erőforrás-szerepkörökhöz tartozó Privileged Identity Management néhány kulcsfontosságú funkciója volt, amelyek nem voltak elérhetők az Azure AD-szerepkörökhöz.

Ha a frissítés jelenleg ki van építve, a kettőt egyetlen felügyeleti kezelőfelületbe egyesítjük, és a szolgáltatásban ugyanazokat a funkciókat fogja használni az Azure AD-szerepkörökhöz, mint az Azure erőforrás-szerepkörökhöz. Ez a cikk a frissített funkciókról és a szükséges követelményekről tájékoztat.

## <a name="time-bound-assignments"></a>Időhöz kötött hozzárendelések

Korábban két lehetséges állapot volt a szerepkör-hozzárendelésekhez: *jogosult* és *állandó*. Most már beállíthatja a kezdési és befejezési időpontot is az egyes hozzárendelési típusoknál. Ez a Hozzáadás négy lehetséges állapotot biztosít, amelyekben egy hozzárendelés helyezhető el:

- Jogosult véglegesen
- Aktív állandó
- Jogosult, a hozzárendelés kezdési és befejezési dátumaival
- Aktív, a hozzárendelés kezdési és befejezési dátumát megadva

Sok esetben akkor is, ha nem szeretné, hogy a felhasználók jogosultak legyenek a megfelelő hozzárendelésre, és minden alkalommal aktiválják a szerepköröket, a hozzárendelések lejárati idejének beállításával továbbra is biztosíthatja az Azure AD-szervezet Ha például vannak olyan ideiglenes felhasználók, akik jogosultak, érdemes lehet lejáratot beállítani, hogy automatikusan eltávolítsa őket a szerepkör-hozzárendelésből a munka befejezésekor.

## <a name="new-role-settings"></a>Új szerepkör-beállítások

Az Azure AD-szerepkörök új beállításait is hozzáadjuk.

- **Korábban** csak az aktiválási beállításokat konfigurálhatja szerepkör alapján. Az aktiválási beállítások, például a többtényezős hitelesítési követelmények, valamint az incidens/kérelmek jegyre vonatkozó követelmények az adott szerepkörre jogosult összes felhasználóra érvényesek voltak.
- **Most** beállíthatja, hogy egy adott felhasználónak a többtényezős hitelesítést kell-e végrehajtania, mielőtt aktiválni tudná a szerepkört. Emellett speciális vezérléssel láthatja el az adott szerepkörökhöz kapcsolódó Privileged Identity Management e-maileket.

## <a name="extend-and-renew-assignments"></a>Hozzárendelések kiterjesztése és megújítása

Amint kideríti az időkorlátot, az első kérdés, hogy mi történik, ha egy szerepkör lejár? Ebben az új verzióban két lehetőséget biztosítunk ehhez a forgatókönyvhöz:

- **Kiterjesztés**: Ha egy szerepkör-hozzárendelés hamarosan lejár, a felhasználó a Privileged Identity Management használatával kérheti le az adott szerepkör-hozzárendelés kiterjesztését
- **Megújítás**: Ha egy szerepkör-hozzárendelés lejárt, a felhasználó Privileged Identity Management használatával megújíthatja a szerepkör-hozzárendelést

A felhasználó által kezdeményezett műveletekhez a globális rendszergazda vagy a Kiemelt szerepkörű rendszergazda jóváhagyása szükséges. A rendszergazdáknak többé nem kell megadniuk a lejáratok kezelésének üzleti tevékenységét. Csak meg kell várniuk a kiterjesztési vagy megújítási kérelmeket, és jóvá kell hagynia őket, ha a kérelem érvényes.

## <a name="api-changes"></a>API-változások

Ha az ügyfelek a frissített verziót az Azure AD-szervezetbe építették, a meglévő Graph API működése leáll. Át kell térnie az [Azure-erőforrás szerepköreinek Graph API](/graph/api/resources/privilegedidentitymanagement-resources?view=graph-rest-beta)használatára. Az adott API használatával felügyelheti az Azure AD-szerepköröket `/azureResources` `/aadroles` az aláírással, és a CÍMTÁR-azonosítóját használhatja `resourceId` .

Mindent megtettünk, hogy megismerjük a korábbi API-t használó ügyfeleket, hogy tájékoztassák őket erről a változásról az idő előtt. Ha az Azure AD-szervezet át lett helyezve az új verzióra, és továbbra is függ a régi API-tól, forduljon a csapathoz pim_preview@microsoft.com .

## <a name="powershell-change"></a>PowerShell-változás

Azon ügyfelek esetében, akik az Azure AD-szerepkörökhöz tartozó Privileged Identity Management PowerShell-modult használják, a PowerShell nem fog működni a frissítéssel. Az előző parancsmagok helyett a Privileged Identity Management-parancsmagokat kell használnia az Azure AD előzetes verzió PowerShell-modulján belül. Telepítse az Azure AD PowerShell-modult a [PowerShell-Galéria](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17). Most már [elolvashatja a PIM-műveletekhez tartozó dokumentációt és mintákat ebben a PowerShell-modulban](powershell-for-azure-ad-roles.md).

## <a name="next-steps"></a>Következő lépések

- [Egyéni Azure AD-szerepkör kiosztása](azure-ad-custom-roles-assign.md)
- [Egyéni Azure AD-szerepkör-hozzárendelés eltávolítása vagy frissítése](azure-ad-custom-roles-update-remove.md)
- [Egyéni Azure AD-szerepkör-hozzárendelés konfigurálása](azure-ad-custom-roles-configure.md)
- [Szerepkör-definíciók az Azure AD-ben](../roles/permissions-reference.md)