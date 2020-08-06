---
title: Hozzáférés engedélyezése az Azure-alkalmazás konfigurációjához Azure Active Directory használatával
description: A RBAC engedélyezése az Azure app Configuration-példányhoz való hozzáférés engedélyezéséhez
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/13/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 8889e7270127aa3991adb3c0575a4bce96090db2
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87830071"
---
# <a name="authorize-access-to-azure-app-configuration-using-azure-active-directory"></a>Hozzáférés engedélyezése az Azure-alkalmazás konfigurációjához Azure Active Directory használatával
A kivonatoló alapú üzenethitelesítő kód (HMAC) használata mellett az Azure app Configuration támogatja a Azure Active Directory (Azure AD) használatát az alkalmazás-konfigurációs példányok kéréseinek engedélyezéséhez.  Az Azure AD lehetővé teszi a szerepköralapú hozzáférés-vezérlés (RBAC) használatát a rendszerbiztonsági tag engedélyeinek megadásához.  Egy rendszerbiztonsági tag lehet egy felhasználó, egy [felügyelt identitás](../active-directory/managed-identities-azure-resources/overview.md) vagy egy [egyszerű alkalmazás](../active-directory/develop/app-objects-and-service-principals.md).  A szerepkörökkel és a szerepkör-hozzárendelésekkel kapcsolatos további tudnivalókért tekintse meg a [különböző szerepkörök ismertetése](../role-based-access-control/overview.md)című témakört.

## <a name="overview"></a>Áttekintés
A rendszerbiztonsági tag által az alkalmazás-konfigurációs erőforráshoz való hozzáférésre vonatkozó kéréseket engedélyezni kell. Az Azure AD-vel az erőforrásokhoz való hozzáférés kétlépéses folyamat:
1. A rendszerbiztonsági tag identitása hitelesítve van, és a rendszer OAuth 2,0 tokent ad vissza.  A jogkivonatot kérő erőforrás neve `https://login.microsoftonline.com/{tenantID}` `{tenantID}` megegyezik azzal a Azure Active Directory BÉRLŐi azonosítóval, amelyhez az egyszerű szolgáltatásnév tartozik.
2. A jogkivonat a megadott erőforráshoz való hozzáférés engedélyezésére irányuló kérelem részeként lett átadva az alkalmazás konfigurációs szolgáltatásának.

A hitelesítési lépés megköveteli, hogy egy alkalmazás-kérelem OAuth 2,0 hozzáférési jogkivonatot tartalmazzon futásidőben.  Ha egy alkalmazás egy Azure-entitáson belül fut, például egy Azure Functions alkalmazásban, egy Azure-webalkalmazásban vagy egy Azure-beli virtuális gépen, akkor a felügyelt identitás használatával férhet hozzá az erőforrásokhoz.  Ha meg szeretné tudni, hogyan hitelesítheti a felügyelt identitások által kezdeményezett kérelmeket az Azure app Configuration szolgáltatásban, tekintse meg az Azure [-erőforrásokhoz való hozzáférés hitelesítése Azure Active Directory és felügyelt identitások](howto-integrate-azure-managed-service-identity.md)használatával című témakört.

Az engedélyezési lépés megköveteli, hogy egy vagy több Azure-szerepkört hozzá lehessen rendelni a rendszerbiztonsági tag számára. Az Azure-alkalmazás konfigurálása olyan Azure-szerepköröket biztosít, amelyek magukban foglalják az alkalmazás konfigurációs erőforrásaira vonatkozó engedélyeket. A rendszerbiztonsági tag számára hozzárendelt szerepkörök határozzák meg a résztvevő számára biztosított engedélyeket. További információ az Azure-szerepkörökről: Azure [-beli beépített szerepkörök az Azure app Configuration szolgáltatáshoz](#azure-built-in-roles-for-azure-app-configuration). 

## <a name="assign-azure-roles-for-access-rights"></a>Azure-szerepkörök kiosztása hozzáférési jogosultságokhoz
Azure Active Directory (Azure AD) az [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../role-based-access-control/overview.md)segítségével engedélyezi a hozzáférési jogokat a biztonságos erőforrásokhoz.

Ha az Azure-szerepköröket egy Azure AD-rendszerbiztonsági tag számára rendeli hozzá, az Azure hozzáférést biztosít az adott rendszerbiztonsági tag erőforrásaihoz. A hozzáférés hatóköre az alkalmazás konfigurációs erőforrása. Az Azure AD rendszerbiztonsági tag lehet egy felhasználó vagy egy egyszerű alkalmazás vagy egy [felügyelt identitás az Azure-erőforrásokhoz](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="azure-built-in-roles-for-azure-app-configuration"></a>Azure-beli beépített szerepkörök az Azure app Configuration szolgáltatáshoz
Az Azure az Azure AD és a OAuth használatával engedélyezi az alkalmazások konfigurációs adataihoz való hozzáférés engedélyezését az alábbi Azure beépített szerepkörökkel:

- **Alkalmazás-konfigurációs adat tulajdonosa**: ezt a szerepkört használva olvasási/írási/törlési hozzáférést biztosíthat az alkalmazás konfigurációs adatához. Ez nem biztosít hozzáférést az alkalmazás konfigurációs erőforrásához.
- **Alkalmazás-konfigurációs Adatolvasó**: ezt a szerepkört használva olvasási hozzáférést adhat az alkalmazás konfigurációs adatához. Ez nem biztosít hozzáférést az alkalmazás konfigurációs erőforrásához.
- **Közreműködő**: használja ezt a szerepkört az alkalmazás konfigurációs erőforrásának kezeléséhez. Az alkalmazás konfigurációs adatai hozzáférési kulcsok használatával érhetők el, ez a szerepkör nem biztosít közvetlen hozzáférést az Azure AD-vel az adathoz.
- **Olvasó**: ezt a szerepkört használva olvasási hozzáférést adhat az alkalmazás konfigurációs erőforrásához. Ez nem biztosít hozzáférést az erőforrás elérési kulcsaihoz, és nem az alkalmazás konfigurációjában tárolt adathoz.

> [!NOTE]
> Jelenleg a Azure Portal és a CLI csak a HMAC-hitelesítést támogatja az alkalmazás konfigurációs információinak eléréséhez. Az Azure AD-hitelesítés nem támogatott. Ezért a Azure Portal és a CLI felhasználói számára a *közreműködő* szerepkört kell beszereznie az alkalmazás konfigurációs erőforrásának hozzáférési kulcsainak lekéréséhez. Az *alkalmazás konfigurációs Adatolvasójának* vagy az *alkalmazás konfigurációs adattulajdonosi* szerepköreinek megadása nincs hatással a Portálon és a CLI-n keresztül történő hozzáférésre.

## <a name="next-steps"></a>További lépések
További információ a [felügyelt identitások](howto-integrate-azure-managed-service-identity.md) használatáról az alkalmazás konfigurációs szolgáltatásának kezeléséhez.
