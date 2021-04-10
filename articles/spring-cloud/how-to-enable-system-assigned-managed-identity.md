---
title: Az Azure Spring Cloud-alkalmazás rendszer által hozzárendelt felügyelt identitásának engedélyezése
description: A rendszerhez rendelt felügyelt identitás engedélyezése az alkalmazáshoz.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 05/13/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 2fbdd46b872c4c70999355d457045f4aac8aa34c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104877971"
---
# <a name="how-to-enable-system-assigned-managed-identity-for-azure-spring-cloud-application"></a>A rendszerhez rendelt felügyelt identitás engedélyezése az Azure Spring Cloud Application szolgáltatáshoz

**Ez a cikk a következőkre vonatkozik:** ✔️ Java ✔️ C #

Az Azure-erőforrások felügyelt identitásai automatikusan felügyelt identitást biztosítanak Azure Active Directory egy Azure-erőforráshoz, például az Azure Spring Cloud-alkalmazáshoz. Ezt az identitást használhatja bármely olyan szolgáltatás hitelesítéséhez, amely támogatja az Azure AD-hitelesítést, és nem rendelkezik hitelesítő adatokkal a kódban.

Ez a cikk bemutatja, hogyan engedélyezheti és tilthatja le a rendszerhez rendelt felügyelt identitásokat egy Azure Spring Cloud-alkalmazáshoz a Azure Portal és a CLI használatával (az 0.2.4 verzióban elérhető).

## <a name="prerequisites"></a>Előfeltételek
Ha nem ismeri az Azure-erőforrások felügyelt identitásait, tekintse meg az [Áttekintés szakaszt](../active-directory/managed-identities-azure-resources/overview.md).
Szüksége lesz egy üzembe helyezett Azure Spring Cloud-példányra. Kövesse a gyors [üzembe helyezést az Azure CLI használatával](spring-cloud-quickstart.md).

## <a name="add-a-system-assigned-identity"></a>Rendszerhez rendelt identitás hozzáadása
A rendszer által hozzárendelt identitással rendelkező alkalmazások létrehozásához további tulajdonságot kell beállítani az alkalmazáshoz.

### <a name="using-azure-portal"></a>Az Azure Portal használata
A [Azure Portal](https://portal.azure.com/)felügyelt identitásának beállításához először hozzon létre egy alkalmazást, majd engedélyezze a szolgáltatást.

1. A szokásos módon hozzon létre egy alkalmazást a portálon. Navigáljon a portálon.
2. Görgessen le a **Beállítások** csoportba a bal oldali navigációs ablaktáblán.
3. Válassza az **identitás** lehetőséget.
4. A **rendszerhez rendelt** lapon váltson az **állapot** bekapcsolva *értékre*. Kattintson a **Mentés** gombra.

 ![Felügyelt identitás a portálon](./media/spring-cloud-managed-identity/identity-1.png)

### <a name="using-azure-cli"></a>Az Azure parancssori felület használata
Az alkalmazás létrehozásakor vagy egy meglévő alkalmazásban engedélyezheti a rendszer által hozzárendelt felügyelt identitást.

**Rendszer által hozzárendelt felügyelt identitás engedélyezése alkalmazás létrehozásakor**

Az alábbi példa egy *APP_NAME* nevű alkalmazást hoz létre egy rendszerhez rendelt felügyelt identitással, a paraméter által kért módon `--assign-identity` .

```azurecli
az spring-cloud app create -n app_name -s service_name -g resource_group_name --assign-identity
```

**A rendszerhez rendelt felügyelt identitás engedélyezése meglévő alkalmazásokban** `az spring-cloud app identity assign` A parancs használatával engedélyezheti a rendszerhez rendelt identitást egy meglévő alkalmazásban.

```azurecli
az spring-cloud app identity assign -n app_name -s service_name -g resource_group_name
```

## <a name="obtain-tokens-for-azure-resources"></a>Jogkivonatok beszerzése az Azure-erőforrásokhoz
Egy alkalmazás a felügyelt identitásával lekérheti a jogkivonatokat a Azure Active Directory által védett egyéb erőforrások, például a Azure Key Vault eléréséhez. Ezek a jogkivonatok az erőforráshoz hozzáférő alkalmazást jelölik, nem az alkalmazás adott felhasználóját.

Előfordulhat, hogy [a cél erőforrást úgy kell konfigurálnia, hogy engedélyezze az alkalmazáshoz való hozzáférést](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md). Ha például jogkivonatot kér a Key Vaulthoz való hozzáféréshez, győződjön meg arról, hogy az alkalmazás identitását tartalmazó hozzáférési szabályzatot adott hozzá. Ellenkező esetben a rendszer elutasítja a Key Vault meghívásait, még akkor is, ha azok tartalmazzák a jogkivonatot. Ha többet szeretne megtudni arról, hogy mely erőforrások támogatják Azure Active Directory jogkivonatokat, tekintse meg az Azure [ad-hitelesítést támogató Azure-szolgáltatásokat](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

Az Azure Spring Cloud ugyanazt a végpontot osztja meg az Azure virtuális géppel való jogkivonat-beszerzéshez. A jogkivonatok beszerzéséhez a Java SDK vagy a Spring boot Starter használatát javasoljuk.  Lásd: a virtuálisgép- [token használata](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) különböző kód-és parancsfájl-példákhoz, valamint útmutatás a fontos témakörökhöz, például a jogkivonat lejáratának és a http-hibák kezeléséhez.

Ajánlott: használja a Java SDK-t vagy a Spring boot Starter-t a jogkivonatok lekéréséhez.  Tekintse meg a mintákat a [következő lépésekben](#next-steps).

## <a name="disable-system-assigned-identity-from-an-app"></a>Rendszer által hozzárendelt identitás letiltása egy alkalmazásból
A rendszer által hozzárendelt identitások eltávolítása az Azure AD-ből is törlődik. Az alkalmazás-erőforrás törlése automatikusan eltávolítja a rendszer által hozzárendelt identitásokat az Azure AD-ből.

### <a name="using-azure-portal"></a>Az Azure Portal használata
A rendszer által hozzárendelt felügyelt identitás eltávolítása egy olyan alkalmazásból, amely már nem igényel rá:

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com/) az Azure Spring Cloud-példányt tartalmazó Azure-előfizetéshez társított fiókkal.
1. Navigáljon a kívánt virtuális géphez, és válassza az **identitás** elemet.
1. A **rendszerhez rendelt** / **állapot** területen válassza a **ki** lehetőséget, majd kattintson a **Mentés** gombra:

 ![Felügyelt identitás](./media/spring-cloud-managed-identity/remove-identity.png)

### <a name="using-azure-cli"></a>Az Azure parancssori felület használata
A következő paranccsal távolíthatja el a rendszer által hozzárendelt felügyelt identitást egy olyan alkalmazásból, amely már nem igényli azt:
```azurecli
az spring-cloud app identity remove -n app_name -s service_name -g resource_group_name
```

## <a name="next-steps"></a>Következő lépések

* [Felügyelt identitásokkal rendelkező Azure Key Vault elérése a Spring boot Starter-ben](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/spring/azure-spring-boot-starter-keyvault-secrets/README.md#use-msi--managed-identities)
* [További információ az Azure-erőforrások felügyelt identitásáról](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [Felügyelt identitások használata a Java SDK-val](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples)
