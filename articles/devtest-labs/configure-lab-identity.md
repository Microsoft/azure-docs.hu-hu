---
title: Tesztkörnyezet identitásának konfigurálása Azure DevTest Labs
description: Megtudhatja, hogyan konfigurálhat labor-identitásokat az Azure DevTest-ben.
ms.topic: article
ms.date: 08/20/2020
ms.openlocfilehash: a652eb5751f9b723911a1c1baaaaf9860febc5b6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88719686"
---
# <a name="configure-a-lab-identity"></a>Tesztkörnyezet identitásának konfigurálása

A felhőalapú alkalmazások készítése során általános kihívást jelenti a hitelesítő adatok a kódban való kezelése, amelyekkel az alkalmazás magát a felhőalapú szolgáltatásokban hitelesíti. A hitelesítő adatok biztonságának megőrzése fontos feladat. Ideális esetben a hitelesítő adatok soha nem jelennek meg a fejlesztői munkaállomásokon, és a verziókövetési rendszerbe sem kerülnek be. Azure Key Vault lehetővé teszi a hitelesítő adatok, titkok és egyéb kulcsok biztonságos tárolását, de a kódnak hitelesítenie kell a Key Vault, hogy beolvassa őket. 

A Azure Active Directory (Azure AD) Azure-erőforrások funkciójának felügyelt identitásai megoldják ezt a problémát. A szolgáltatás automatikusan felügyelt identitást biztosít az Azure-szolgáltatások számára az Azure AD-ben. Ezzel az identitással bármely, az Azure AD-hitelesítést támogató szolgáltatásban, többek között a Key Vaultban is elvégezheti a hitelesítést anélkül, hogy a hitelesítő adatok a kódban szerepelnének. További információ az [Azure-beli felügyelt identitásokról](../active-directory/managed-identities-azure-resources/overview.md). 

A felügyelt identitásoknak két típusa létezik: 

## <a name="system-assigned-managed-identity"></a>Rendszer által hozzárendelt felügyelt identitás  

Egy **rendszerhez rendelt felügyelt identitás**   közvetlenül van engedélyezve egy Azure-beli szolgáltatási példányon. Az identitás engedélyezésekor az Azure létrehoz egy identitást a példány számára a példány előfizetése által megbízhatónak tekintett Azure AD-bérlőn. Az identitás létrehozása után a rendszer hozzárendeli a hitelesítő adatokat a példányon. A rendszerhez rendelt identitás életciklusa közvetlenül ahhoz az Azure Service-példányhoz van kötve, amelyhez engedélyezve van. A példány törlésekor az Azure automatikusan törli a hitelesítő adatokat és az identitást az Azure AD-ben. 

### <a name="scenarios-for-using-labs-system-assigned-identity"></a>A Lab rendszerhez rendelt identitás használatának forgatókönyvei  

Minden DevTest Labs olyan rendszerhez rendelt identitással jön létre, amely a labor élettartama szempontjából érvényes marad. A rendszerhez rendelt identitás a következő célokra szolgál:  

- A több virtuális gépre és/vagy platformra, mint szolgáltatási környezetre való felpörgetéshez használt [Azure Resource Manager](devtest-lab-create-environment-from-arm.md) -alapú telepítések a labor rendszerhez rendelt identitás használatával lesznek végrehajtva.  
- Az ügyfél által felügyelt kulccsal rendelkező Lab-lemezek lemezes titkosítását a labor rendszerhez rendelt identitása támogatja. Ha explicit módon hozzáférést biztosít a labor identitásához a lemez titkosítási készletének eléréséhez, a labor az Ön nevében titkosíthatja az összes virtuális gép lemezét. További információ arról [, hogyan engedélyezhető a lemezek titkosítása](encrypt-disks-customer-managed-keys.md) a labor-lemezeken egy ügyfél által felügyelt kulcs használatával.  

### <a name="configure-identity"></a>Identitás konfigurálása

Ez a szakasz bemutatja, hogyan konfigurálhatja a labor identitás-házirendjét.

> [!NOTE]
> A 8/10/2020-es előtt létrehozott laborok esetében a rendszer a hozzárendelt identitást kikapcsolva értékre állítja. A labor tulajdonosaként bekapcsolhatja azt az esetre, ha az előző szakaszban felsorolt célokra szeretné használni a laborokat.  
>
> Az 8/10/2020-et követően létrehozott új laborok esetében a labor rendszerhez rendelt identitása alapértelmezés szerint be van állítva, és a labor tulajdonosa nem fogja tudni kikapcsolni a labor életciklusát.  

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Keressen rá a **DevTest Labs** kifejezésre.
1. A Labs listából válassza ki a kívánt labort.
1. Válassza **a konfiguráció és szabályzatok**  ->  **identitás (előzetes verzió)** lehetőséget. 

> [!div class="mx-imgBorder"]
> ![Identitás konfigurálása](./media/configure-lab-identity/configure-identity.png)

## <a name="user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás  

A felhasználó által hozzárendelt felügyelt identitás önálló Azure-erőforrásként jön létre. Egy létrehozási folyamaton keresztül az Azure létrehoz egy identitást a használt előfizetés által megbízhatónak tekintett Azure AD-bérlőn. Az identitás a létrehozását követően hozzárendelhető egy vagy több Azure-beli szolgáltatáspéldányhoz. A felhasználó által hozzárendelt identitás életciklusa külön felügyelhető azon Azure-szolgáltatási példányok életciklusa alapján, amelyekhez hozzá van rendelve. 

A DevTest Labs a virtuális gépekhez és Azure Resource Manager-alapú környezetekhez egyaránt támogatja a felhasználóhoz rendelt identitásokat.  További információkért tekintse át a következők témaköröket:

- [Felhasználó által hozzárendelt identitás hozzáadása labor Azure Resource Manager környezetek üzembe helyezéséhez](use-managed-identities-environments.md)
- [Felhasználó által hozzárendelt identitások hozzáadása a tesztkörnyezet virtuális gépei üzembe helyezéséhez](enable-managed-identities-lab-vms.md)

## <a name="next-steps"></a>Következő lépések

A [Cost Management konfigurálásának](devtest-lab-configure-cost-management.md) áttekintése