---
title: Az Azure-erőforrások felügyelt identitások használata az Azure Virtual Machines szolgáltatással
description: Az Azure-erőforrások felügyelt identitásának leírása az Azure Virtual Machines szolgáltatással használható.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: conceptual
ms.custom: mvc
ms.date: 06/11/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8cc8f89720dddbee3f69cb5d933268edb17acfd2
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106055620"
---
# <a name="how-managed-identities-for-azure-resources-work-with-azure-virtual-machines"></a>Az Azure-erőforrások felügyelt identitások használata az Azure Virtual Machines szolgáltatással

Az Azure-erőforrások felügyelt identitásai Azure-szolgáltatásokat biztosítanak a Azure Active Directory automatikusan felügyelt identitással. Ezt az identitást használhatja bármely olyan szolgáltatás hitelesítéséhez, amely támogatja az Azure AD-hitelesítést, és nem rendelkezik hitelesítő adatokkal a kódban.

Ebből a cikkből megtudhatja, hogyan működnek a felügyelt identitások az Azure Virtual Machines szolgáltatással (VM).


## <a name="how-it-works"></a>Működés

Belsőleg a felügyelt identitások speciális típusú szolgáltatások, amelyek csak az Azure-erőforrásokkal használhatók. A felügyelt identitás törlése után a rendszer automatikusan eltávolítja a megfelelő egyszerű szolgáltatást.
Továbbá egy User-Assigned vagy System-Assigned identitás létrehozásakor a felügyelt identitás erőforrás-szolgáltatója (MSRP) belső tanúsítványt bocsát ki az identitásnak. 

A kód a felügyelt identitások használatával hozzáférési jogkivonatokat igényelhet az Azure AD-hitelesítést támogató szolgáltatásokhoz. Az Azure gondoskodik a szolgáltatáspéldány által használt hitelesítő adatok biztosításáról. 

Az alábbi ábrán a felügyelszolgáltatás-identitások az Azure-beli virtuális gépekkel (VM) való működése látható:

![Felügyeltszolgáltatás-identitások és Azure-beli virtuális gépek](media/how-managed-identities-work-vm/data-flow.png)

|  Tulajdonság    | Rendszer által hozzárendelt felügyelt identitás | Felhasználó által hozzárendelt felügyelt identitás |
|------|----------------------------------|--------------------------------|
| Létrehozás |  Egy Azure-erőforrás (például Azure-beli virtuális gép vagy Azure App Service) részeként lett létrehozva. | Önálló Azure-erőforrásként létrehozva. |
| Életciklus | Megosztott életciklus az Azure-erőforrással, amelyet a felügyelt identitás létrehoz. <br/> A fölérendelt erőforrás törlésekor a felügyelt identitás is törlődik. | Független életciklus. <br/> Explicit módon törölni kell. |
| Megosztás az Azure-erőforrások között | Nem osztható meg. <br/> Csak egyetlen Azure-erőforráshoz társítható. | Megosztható. <br/> Ugyanaz a felhasználó által hozzárendelt felügyelt identitás több Azure-erőforráshoz is társítható. |
| Gyakori használati helyzetek | Egyetlen Azure-erőforráson belül található munkaterhelések. <br/> Olyan munkaterhelések, amelyekhez független identitásokra van szükség. <br/> Például egyetlen virtuális gépen futó alkalmazás | Több erőforráson futó munkaterhelések, amelyek egyetlen identitást is megoszthatnak. <br/> Olyan munkaterhelések, amelyeknek a létesítési folyamat részeként meg kell adni a biztonságos erőforráshoz való előzetes engedélyezést. <br/> Olyan munkaterhelések, amelyekben az erőforrások gyakran újrahasznosíthatók, de az engedélyek konzisztensek maradnak. <br/> Például egy olyan munkaterhelés, amelyben több virtuális gépnek is hozzá kell férnie ugyanahhoz az erőforráshoz |

## <a name="system-assigned-managed-identity"></a>Rendszer által hozzárendelt felügyelt identitás

1. Az Azure Resource Managerbe érkezik egy kérés a rendszer által hozzárendelt felügyelt identitás virtuális gépen történő engedélyezésére.

2. Az Azure Resource Manager létrehoz egy szolgáltatásnevet az Azure AD-ben a virtuális gép identitása számára. A szolgáltatásnév az előfizetés által megbízhatónak tekintett Azure AD-bérlőn jön létre.

3. Azure Resource Manager úgy konfigurálja az identitást a virtuális gépen, hogy az Azure Instance Metadata Service Identity végpontot az egyszerű szolgáltatás ügyfél-azonosítójával és tanúsítvánnyal frissíti.

4. Miután a virtuális gép már rendelkezik identitással, a szolgáltatásnév-adatok használatával biztosítson hozzáférést a virtuális gépnek az Azure-erőforrásokhoz. Azure Resource Manager meghívásához az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával rendelje hozzá a megfelelő szerepkört a virtuálisgép-szolgáltatáshoz. A Key Vault meghívásához adjon hozzáférést a kódnak az adott titkos kódhoz vagy kulcshoz a Key Vaultban.

5. A virtuális gépen futó kód az Azure-példány metaadatainak szolgáltatásának végpontján igényelhet jogkivonatot, amely csak a virtuális gépről érhető el: `http://169.254.169.254/metadata/identity/oauth2/token`
    - A resource (erőforrás) paraméter határozza meg azt a szolgáltatást, amelynek a jogkivonatot meg kell küldeni. Az Azure Resource Manager hitelesítéséhez használja a `resource=https://management.azure.com/` paramétert.
    - Az API version (API-verzió) paraméter adja meg az IMDS-verziót – használja az api-version=2018-02-01, vagy egy nagyobb értéket.

6. A rendszer egy hívást intéz az Azure AD-re, és egy hozzáférési jogkivonatot igényel (az 5. lépésben leírtak szerint) a 3. lépésben konfigurált ügyfél-azonosító és tanúsítvány használatával. Az Azure AD egy JSON Web Token (JWT) formátumú hozzáférési jogkivonatot ad vissza.

7. A kód elküldi a hozzáférési jogkivonatot egy hívásban egy olyan szolgáltatásnak, amely támogatja az Azure AD-hitelesítést.

## <a name="user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás

1. Az Azure Resource Managerbe érkezik egy kérés egy felhasználó által hozzárendelt felügyelt identitás létrehozására.

2. Az Azure Resource Manager létrehoz egy szolgáltatásnevet az Azure AD-ben a felhasználó által hozzárendelt felügyelt identitás számára. A szolgáltatásnév az előfizetés által megbízhatónak tekintett Azure AD-bérlőn jön létre.

3. A Azure Resource Manager kérést kap a felhasználó által hozzárendelt felügyelt identitás konfigurálására egy virtuális gépen, és frissíti az Azure Instance Metadata Service Identity végpontot a felhasználó által hozzárendelt felügyelt identitási szolgáltatás egyszerű ügyfelének azonosítójával és tanúsítványával.

4. Miután a felhasználó által hozzárendelt felügyelt identitás létrejött, a szolgáltatásnév-adatok használatával biztosítson hozzáférést az identitásnak az Azure-erőforrásokhoz. Azure Resource Manager meghívásához az Azure RBAC használatával rendelje hozzá a megfelelő szerepkört a felhasználó által hozzárendelt identitás egyszerű szolgáltatásához. A Key Vault meghívásához adjon hozzáférést a kódnak az adott titkos kódhoz vagy kulcshoz a Key Vaultban.

   > [!Note]
   > Ezt a lépést a 3. lépés előtt is végrehajthatja.

5. A virtuális gépen futó kód az Azure Instance Metadata Service Identity végponttól kérhet jogkivonatot, amely csak a virtuális gépről érhető el: `http://169.254.169.254/metadata/identity/oauth2/token`
    - A resource (erőforrás) paraméter határozza meg azt a szolgáltatást, amelynek a jogkivonatot meg kell küldeni. Az Azure Resource Manager hitelesítéséhez használja a `resource=https://management.azure.com/` paramétert.
    - A client ID (ügyfél-azonosító) paraméter határozza meg azt az identitást, amely számára a hozzáférési jogkivonat kérvényezve lett. Ez az érték akkor szükséges egyértelműsítés céljából, ha egyazon virtuális gépen több felhasználó által hozzárendelt identitás is létezik.
    - Az API version (API verziója) paraméter az Azure Instance Metadata szolgáltatás verzióját adja meg. Használja a(z) `api-version=2018-02-01` vagy újabb verziót.

6. A rendszer egy hívást intéz az Azure AD-re, és egy hozzáférési jogkivonatot igényel (az 5. lépésben leírtak szerint) a 3. lépésben konfigurált ügyfél-azonosító és tanúsítvány használatával. Az Azure AD egy JSON Web Token (JWT) formátumú hozzáférési jogkivonatot ad vissza.
7. A kód elküldi a hozzáférési jogkivonatot egy hívásban egy olyan szolgáltatásnak, amely támogatja az Azure AD-hitelesítést.


## <a name="next-steps"></a>Következő lépések

Ismerkedjen meg az Azure-erőforrások felügyelt identitásai szolgáltatással a következő rövid útmutatók segítségével:

* [Hozzáférés a Resource Managerhez egy Windows VM-beli, rendszer által hozzárendelt felügyelt identitással](tutorial-windows-vm-access-arm.md)
* [Hozzáférés a Resource Managerhez egy Linux VM-beli, rendszer által hozzárendelt felügyelt identitással](tutorial-linux-vm-access-arm.md)
