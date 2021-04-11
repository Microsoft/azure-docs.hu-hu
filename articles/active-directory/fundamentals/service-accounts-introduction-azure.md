---
title: A Azure Active Directory-szolgáltatásfiókok biztonságossá tételének bemutatása
description: A Azure Active Directoryban elérhető szolgáltatásfiókok típusainak magyarázata.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 3/1/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: d657f1df14b083631227cb7c19f64b65be8801d0
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "107010465"
---
# <a name="introduction-to-securing-azure-service-accounts"></a>Bevezetés az Azure-szolgáltatásfiókok biztonságossá tételéhez

A Azure Active Directory natív típusú szolgáltatásfiókok a következők: felügyelt identitások, egyszerű szolgáltatások és felhasználói fiókok. A szolgáltatásfiókok olyan speciális típusú fiókok, amelyek nem emberi entitások, például alkalmazások, API-k vagy más szolgáltatások ábrázolására szolgálnak. Ezek az entitások a szolgáltatásfiók által biztosított biztonsági környezeten belül működnek. 

## <a name="types-of-azure-active-directory-service-accounts"></a>Azure Active Directory-szolgáltatásfiókok típusai

Az Azure-ban üzemeltetett szolgáltatások esetében ajánlott felügyelt identitást használni, ha ez lehetséges, és ha nem, akkor egyszerű szolgáltatásnevet. A felügyelt identitások nem használhatók az Azure-on kívül üzemeltetett szolgáltatásokhoz. Ebben az esetben javasoljuk, hogy egy egyszerű szolgáltatásnevet. Ha felügyelt identitást vagy szolgáltatásnevet is használhat, tegye meg. Azt javasoljuk, hogy ne használjon Azure Active Directory felhasználói fiókot szolgáltatásfiókként. Tekintse meg az alábbi táblázatot az összegzéshez.
 

| Szolgáltatás üzemeltetése| Felügyelt identitás| Szolgáltatásnév| Azure-felhasználói fiók |
| - | - | - | - |
|A szolgáltatás az Azure-ban üzemel.| Igen. <br>Ajánlott, ha a szolgáltatás <br>támogatja a felügyelt identitást.| Igen.| Nem ajánlott. |
| A szolgáltatás nem az Azure-ban fut.| Nem| Igen. Ajánlott.| Nem ajánlott. |
| A szolgáltatás több-bérlős| Nem| Igen. Ajánlott.| Nem. |


## <a name="managed-identities"></a>Felügyelt identitások

A felügyelt identitások biztonságos Azure Active Directory (Azure AD) identitások, amelyek az Azure-erőforrások identitásának biztosításához lettek létrehozva. A [felügyelt identitásoknak két típusa](../managed-identities-azure-resources/overview.md#managed-identity-types)van: 
 
* A rendszer által hozzárendelt felügyelt identitások közvetlenül a szolgáltatás egy példányához rendelhetők hozzá. 

* A felhasználó által hozzárendelt felügyelt identitások önálló erőforrásként hozhatók létre. 

További információ: a [felügyelt identitások biztonságossá tétele](service-accounts-managed-identities.md). A felügyelt identitásokkal kapcsolatos általános információkért lásd: [Mi az Azure-erőforrások felügyelt identitása?](../managed-identities-azure-resources/overview.md)

## <a name="service-principals"></a>Szolgáltatásnevek

Ha nem használhat felügyelt identitást az alkalmazás ábrázolására, használjon egyszerű szolgáltatásnevet. Az egyszerű szolgáltatásokkal egyetlen Bérlővel és több-bérlős alkalmazásokkal is használhatók. 

Egy egyszerű szolgáltatásnév egy alkalmazás-objektum helyi ábrázolása egyetlen Azure AD-bérlőben. Az alkalmazás-példány identitása működik, meghatározza, hogy ki férhet hozzá az alkalmazáshoz, és hogy milyen erőforrásokhoz férhet hozzá. Egy egyszerű szolgáltatásnév a (helyi) helyen jön létre minden olyan bérlőn, ahol az alkalmazás használatban van, és a globálisan egyedi alkalmazásobjektum használatára hivatkozik. A bérlő biztosítja az egyszerű szolgáltatás bejelentkezését és az erőforrásokhoz való hozzáférést.

Az egyszerű szolgáltatásoknak két mechanizmusa van a hitelesítéshez – Ügyféltanúsítványok és ügyfél-titkos kulcsok használatával. A tanúsítványok biztonságosabbak: ha lehetséges, használjon ügyféltanúsítványt. Az ügyfél-titkoktól eltérően az Ügyféltanúsítványok nem ágyazhatók be véletlenül programkódba.

Az egyszerű szolgáltatások biztonságossá tételével kapcsolatban lásd: az [egyszerű szolgáltatások biztonságossá tétele](service-accounts-principal.md).

 
## <a name="next-steps"></a>Következő lépések


Az Azure-szolgáltatásfiókok biztonságossá tételével kapcsolatos további információkért lásd:

[Felügyelt identitások biztonságossá tétele](service-accounts-managed-identities.md)

[Az egyszerű szolgáltatások biztonságossá tétele](service-accounts-principal.md)

[Az Azure-szolgáltatásfiókok szabályozása](service-accounts-governing-azure.md)
