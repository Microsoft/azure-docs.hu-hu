---
title: Azure Active Directoryval való hozzáférés engedélyezése
description: Ez a cikk az Azure Signaler szolgáltatás-erőforrásokhoz való hozzáférés engedélyezéséről nyújt tájékoztatást Azure Active Directory használatával.
author: terencefan
ms.author: tefa
ms.date: 08/03/2020
ms.service: signalr
ms.topic: conceptual
ms.openlocfilehash: 37c2e41b5c81f941245b895ecd144baee3b9db6d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97797515"
---
# <a name="authorize-access-to-azure-signalr-service-resources-using-azure-active-directory"></a>Az Azure Signaler szolgáltatás erőforrásaihoz való hozzáférés engedélyezése Azure Active Directory használatával
Az Azure Signaler szolgáltatás támogatja a Azure Active Directory (Azure AD) használatát az Azure Signaler szolgáltatás erőforrásaihoz intézett kérések engedélyezéséhez. Az Azure AD-vel szerepköralapú hozzáférés-vezérlés (RBAC) használatával adhat meg engedélyeket egy rendszerbiztonsági tag számára, amely lehet egy felhasználó vagy egy egyszerű alkalmazás. További információ a szerepkörökről és a szerepkör-hozzárendelésekről: [a különböző szerepkörök megismerése](../role-based-access-control/overview.md).

## <a name="overview"></a>Áttekintés
Ha egy rendszerbiztonsági tag (egy alkalmazás) megpróbál hozzáférni egy Azure Signaler szolgáltatás-erőforráshoz, a kérést engedélyezni kell. Az Azure AD-vel az erőforrásokhoz való hozzáférés kétlépéses folyamat. 

 1. Először a rendszerbiztonsági tag identitása hitelesítve van, és a rendszer egy OAuth 2,0 tokent ad vissza. A tokent kérő erőforrás neve `https://signalr.azure.com/` .
 2. Ezután a tokent egy, az Azure Signaler szolgáltatásnak küldött kérelem részeként továbbítja a rendszer a megadott erőforráshoz való hozzáférés engedélyezéséhez.

A hitelesítési lépés megköveteli, hogy egy alkalmazás-kérelem OAuth 2,0 hozzáférési jogkivonatot tartalmazzon futásidőben. Ha a központi kiszolgáló egy Azure-entitáson, például egy Azure-beli virtuális gépen, egy virtuálisgép-méretezési csoporton vagy egy Azure Function-alkalmazáson belül fut, akkor a felügyelt identitás használatával férhet hozzá az erőforrásokhoz. A felügyelt identitás által az Azure Signaler szolgáltatásnak küldött kérések hitelesítéséhez lásd: [hozzáférés hitelesítése az Azure signaler szolgáltatás erőforrásaihoz Azure Active Directory és felügyelt identitásokkal az Azure-erőforrásokhoz](authenticate-managed-identity.md). 

Az engedélyezési lépés megköveteli, hogy egy vagy több RBAC-szerepkört hozzá lehessen rendelni a rendszerbiztonsági tag számára. Az Azure Signaler szolgáltatás olyan RBAC-szerepköröket biztosít, amelyek magukban foglalják az Azure Signaler-erőforrások engedélyeinek készleteit. A rendszerbiztonsági tag számára hozzárendelt szerepkörök határozzák meg, hogy a résztvevő milyen engedélyeket fog tartalmazni. További információ a RBAC-szerepkörökről: [Azure-beli beépített szerepkörök az Azure signaler szolgáltatáshoz](#azure-built-in-roles-for-azure-signalr-service). 

Az Azure-entitáson belül nem futó Signal hub-kiszolgáló is engedélyezheti az Azure AD-t. Ha meg szeretné tudni, hogyan kérhet hozzáférési jogkivonatot, és hogyan engedélyezheti a kérelmeket az Azure Signaler szolgáltatás erőforrásaihoz, tekintse meg a [hozzáférés az Azure Signal Service szolgáltatással az Azure ad-vel való hitelesítését az alkalmazásokból](authenticate-application.md)című témakört 

## <a name="azure-built-in-roles-for-azure-signalr-service"></a>Azure-beli beépített szerepkörök az Azure Signaler szolgáltatáshoz

- [Jelző alkalmazás-kiszolgáló]
- [Szignáló szolgáltatás olvasója]
- [Jelző szolgáltatás tulajdonosa]

## <a name="assign-rbac-roles-for-access-rights"></a>Hozzáférési jogosultságok RBAC-szerepköreinek kiosztása
Azure Active Directory (Azure AD) a [szerepköralapú hozzáférés-vezérlés (RBAC)](../role-based-access-control/overview.md)segítségével engedélyezi a hozzáférési jogokat a biztonságos erőforrásokhoz. Az Azure Signaler szolgáltatás olyan beépített Azure-szerepköröket határoz meg, amelyek az Azure Signaler szolgáltatás eléréséhez használt engedélyek közös készleteit foglalják magukban, és egyéni szerepköröket is meghatározhat az erőforráshoz való hozzáféréshez.

Ha egy Azure AD-rendszerbiztonsági tag egy RBAC-szerepkört rendel hozzá, az Azure hozzáférést biztosít ezen rendszerbiztonsági tag erőforrásaihoz. A hozzáférés az előfizetés, az erőforráscsoport vagy bármely Azure Signaler szolgáltatás-erőforrás szintjére is kiterjed. Az Azure AD rendszerbiztonsági tag lehet egy felhasználó vagy egy alkalmazás vagy egy [felügyelt identitás az Azure-erőforrásokhoz](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="built-in-roles-for-azure-signalr-service"></a>Az Azure Signaler szolgáltatás beépített szerepkörei
Az Azure az alábbi Azure-beli beépített szerepköröket biztosítja az Azure Signal Service-erőforrásokhoz való hozzáférés engedélyezéséhez az Azure AD és a OAuth használatával:

### <a name="signalr-app-server"></a>Jelző alkalmazás kiszolgálója

Ezzel a szerepkörrel biztosíthatja, hogy a rendszer ideiglenes hozzáférési kulcsot kapjon az ügyfél-jogkivonatok aláírásához.

### <a name="signalr-serverless-contributor"></a>A jelző kiszolgáló nélküli közreműködői

Ezzel a szerepkörrel biztosíthatja, hogy a hozzáférés közvetlenül az Azure Signaler szolgáltatásból kapjon ügyfél-jogkivonatot.

## <a name="next-steps"></a>Következő lépések

Tekintse meg a következő kapcsolódó cikkeket:

- [Alkalmazás hitelesítése az Azure AD-vel az Azure Signaler szolgáltatás eléréséhez](authenticate-application.md)
- [Felügyelt identitás hitelesítése az Azure AD-vel az Azure Signaler szolgáltatás eléréséhez](authenticate-managed-identity.md)