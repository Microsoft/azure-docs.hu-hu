---
title: Oktatóanyag `:` felügyelt identitás használata Azure Key Vault-Windows-Azure ad eléréséhez
description: Az oktatóanyag azt ismerteti, hogyan férhet hozzá az Azure Key Vaulthoz egy Windows VM-beli, rendszer által hozzárendelt felügyelt identitással.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f29c9daba80ffc57b96cf5bd82690dea9ac6429
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101093750"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-key-vault"></a>Oktatóanyag: Hozzáférés az Azure Key Vaulthoz egy Windows VM-beli, rendszer által hozzárendelt felügyelt identitással 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Ebből az oktatóanyagból megtudhatja, hogy a Windows rendszerű virtuális gépek hogyan használhatják a rendszerhez rendelt felügyelt identitást [Azure Key Vault](../../key-vault/general/overview.md)eléréséhez. A rendszerindítási szolgáltatásként szolgáló Key Vault lehetővé teszi, hogy az ügyfélalkalmazás egy titkos kulcsot használjon a Azure Active Directory (AD) által nem védett erőforrások eléréséhez. A felügyelt szolgáltatás identitásait a rendszer automatikusan kezeli az Azure-ban, és lehetővé teszi az Azure AD-hitelesítést támogató szolgáltatások hitelesítését, anélkül, hogy a kód a hitelesítési adatokat is tartalmazza.

Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Hozzáférés engedélyezése a VM számára a Key Vaultban tárolt titkos kódokhoz
> * Hozzáférési jogkivonat lekérése a VM identitásával, majd a titkos kód lekérése a Key Vaultból 

## <a name="prerequisites"></a>Előfeltételek

- A felügyelt identitások ismerete. Ha még nem ismeri az Azure-erőforrások felügyelt identitására vonatkozó funkciót, tekintse meg ezt az [áttekintést](overview.md). 
- Egy Azure-fiókkal, [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- A "tulajdonos" engedély a megfelelő hatókörben (előfizetése vagy erőforráscsoport) a szükséges erőforrás-létrehozási és szerepkör-felügyeleti lépések végrehajtásához. Ha segítségre van szüksége a szerepkör-hozzárendeléssel kapcsolatban, tekintse meg az Azure [-szerepkörök hozzárendelése az Azure-előfizetés erőforrásaihoz való hozzáférés kezeléséhez](../../role-based-access-control/role-assignments-portal.md)című témakört
- Szükség van egy Windows rendszerű virtuális gépre is, amelyhez engedélyezve van a rendszerhez rendelt felügyelt identitások.
  - Ha létre kell hoznia egy virtuális gépet ehhez az oktatóanyaghoz, kövesse a [virtuális gép létrehozása rendszer által hozzárendelt identitással](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity) című cikket.

## <a name="create-a-key-vault"></a>Kulcstartó létrehozása  

Ez a szakasz bemutatja, hogyan biztosítható a virtuális gép hozzáférése egy Key Vault tárolt titkos kulcshoz. Az Azure-erőforrások felügyelt identitásainak segítségével a kód hozzáférési jogkivonatokat kérhet le az olyan erőforrások felé történő hitelesítéshez, amelyek támogatják az Azure AD-hitelesítést.Az Azure AD-hitelesítést azonban nem minden Azure-szolgáltatás támogatja. Az Azure-erőforrások felügyelt identitásainak ilyen szolgáltatásokkal való használatához tárolja el a szolgáltatás hitelesítő adatait az Azure Key Vaultban, és a virtuális gép felügyelt identitásával férjen hozzá a Key Vaulthoz, hogy le tudja kérni a hitelesítő adatokat.

Először létre kell hozni egy Key Vaultot, és gondoskodni kell róla, hogy a VM rendszer által hozzárendelt felügyelt identitása hozzá tudjon férni.

1. Az Azure [Portal](https://portal.azure.com/) megnyitása
1. A bal oldali navigációs sáv tetején válassza az **erőforrás létrehozása** lehetőséget.  
1. A **Keresés a piactéren** mezőbe írja be a **Key Vault** kifejezést, és nyomja le az **ENTER billentyűt**.  
1. Válassza ki **Key Vault** az eredmények közül.
1. Kattintson a **Létrehozás** elemre.
1. Adja meg az új Key Vault **nevét**.

    ![Key Vault-képernyő létrehozása](./media/msi-tutorial-windows-vm-access-nonaad/create-key-vault.png)

1. Töltse ki az összes szükséges információt, és ügyeljen arra, hogy kiválassza azt az előfizetést és erőforráscsoportot, amelyben az oktatóanyaghoz használt virtuális gépet létrehozta.
1. Válassza a **felülvizsgálat + létrehozás** lehetőséget
1. Kattintson a **Létrehozás** elemre.

### <a name="create-a-secret"></a>Titkos kulcs létrehozása

Ezután adjon hozzá egy titkos kulcsot a Key Vaulthoz, így később lekérheti a virtuális gépen futó kód használatával. Ebben az oktatóanyagban a PowerShellt használjuk, de ugyanezek a fogalmak érvényesek az ezen a virtuális gépen futtatott kódokra is.

1. Navigáljon az újonnan létrehozott Key Vault.
1. Válassza a **Titkos kódok** lehetőséget, és kattintson a **Hozzáadás** gombra.
1. **Előállítási/importálási** lehetőség kiválasztása
1. A **titkos kód létrehozása** a **feltöltési beállításokból lapon** válassza a **manuális** lehetőséget.
1. Adja meg a titkos kód nevét és értékét.  Az érték bármi lehet. 
1. Hagyja az aktiválási és a lejárati dátumot üresen, az **Engedélyezve** beállítást pedig az **Igen** értéken. 
1. A titkos kód létrehozásához kattintson a **Létrehozás** parancsra.

   ![Titkos kulcs létrehozása](./media/msi-tutorial-windows-vm-access-nonaad/create-secret.png)

## <a name="grant-access"></a>Hozzáférés biztosítása

A virtuális gép által használt felügyelt identitásnak hozzáférést kell adni ahhoz, hogy elolvassa a Key Vaultban tárolni kívánt titkot.

1. Navigáljon az újonnan létrehozott Key Vault
1. A bal oldali menüben válassza a **hozzáférési szabályzat** lehetőséget.
1. Válassza a **hozzáférési házirend hozzáadása** lehetőséget.

   ![Key Vault – hozzáférési szabályzat létrehozása képernyő](./media/msi-tutorial-windows-vm-access-nonaad/key-vault-access-policy.png)

1. A **hozzáférési házirend hozzáadása** szakasz **Konfigurálás a sablon alapján (nem kötelező)** területen válassza a **titkos kezelés** lehetőséget a legördülő menüből.
1. Válassza a **Rendszerbiztonsági tag kijelölése** lehetőséget, és a keresőmezőben adja meg a korábban létrehozott virtuális gép nevét.  Válassza ki a virtuális gépet az eredmények listájában, és válassza a **kiválasztás** lehetőséget.
1. Válassza a **Hozzáadás** lehetőséget
1. Kattintson a **Mentés** gombra.


## <a name="access-data"></a>Adatok elérése  

Ez a szakasz bemutatja, hogyan kérhet hozzáférési tokent a virtuális gép identitásával, és hogyan kérheti le a titkos kulcsot a Key Vaultból. Ha nincs telepítve a PowerShell 4.3.1-es vagy újabb verziója, [le kell töltenie és telepítenie kell a legújabb verziót](/powershell/azure/).

Először a VM rendszer által hozzárendelt felügyelt identitásával szerezzen be egy hozzáférési jogkivonatot a Key Vaulton végzett hitelesítéshez:
 
1. A portálon lépjen a **Virtuális gépek** lapra, lépjen a Windows VM-hez, és az **Áttekintés** területen kattintson a **Csatlakozás** elemre.
2. Adja meg a **felhasználónevét** és **jelszavát** , amelyet a **Windows rendszerű virtuális gép** létrehozásakor adott hozzá.  
3. Most, hogy létrehozott egy **távoli asztali kapcsolatot** a virtuális géppel, nyissa meg a PowerShellt a távoli munkamenetben.  
4. A PowerShellben hívjon meg egy webes kérést a bérlőn, amellyel lekéri a jogkivonatot a helyi gazdagéphez a virtuális gép adott portján.  

A PowerShell-kérés:

```powershell
$Response = Invoke-RestMethod -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -Method GET -Headers @{Metadata="true"} 
```

Láthatja, hogy a válasz a következőhöz hasonlít:

![kérelem jogkivonat-választal](./media/msi-tutorial-windows-vm-access-nonaad/token.png)

Ezután nyerje ki a hozzáférési jogkivonatot a válaszból.  

```powershell
   $KeyVaultToken = $Response.access_token
```

Végül a Powershell Invoke-WebRequest parancsával kérje le a Key Vaultban korábban létrehozott titkos kódot úgy, hogy a hozzáférési jogkivonatot az engedélyezési fejlécbe foglalja.  Szüksége lesz a Key Vault URL-címére, amely a Key Vault **Áttekintés** lapjának **Alapvető erőforrások** szakaszában található.  

```powershell
Invoke-RestMethod -Uri https://<your-key-vault-URL>/secrets/<secret-name>?api-version=2016-10-01 -Method GET -Headers @{Authorization="Bearer $KeyVaultToken"}
```

A válasz a következőképp fog kinézni: 

```powershell
  value       id                                                                                    attributes
  -----       --                                                                                    ----------
  'My Secret' https://mi-lab-vault.vault.azure.net/secrets/mi-test/50644e90b13249b584c44b9f712f2e51 @{enabled=True; created=16…
```

Miután lekérte a titkos kódot a Key Vaultból, a használatával hitelesítést végezhet olyan szolgáltatásokban, amelyekhez név és jelszó szükséges.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha szeretné törölni az erőforrásokat, látogasson el a [Azure Portalra](https://portal.azure.com), válassza az **erőforráscsoportok** lehetőséget, keresse meg és válassza ki az oktatóanyag folyamatában létrehozott erőforráscsoportot (például `mi-test` ), majd használja az **erőforráscsoport törlése** parancsot.

Ezt [a PowerShell vagy a parancssori](../../azure-resource-manager/management/delete-resource-group.md) felület használatával is elvégezheti.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan használhatja a Windows rendszerű virtuális gépekhez rendelt felügyelt identitást Azure Key Vault eléréséhez.  További információ az Azure Key Vault szolgáltatásról:

> [!div class="nextstepaction"]
>[Azure Key Vault](../../key-vault/general/overview.md)