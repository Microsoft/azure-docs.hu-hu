---
title: 'Oktatóanyag: Felügyelt identitás használata Azure Key Vault – Windows – Azure AD'
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
ms.openlocfilehash: 92ded2d327fd7c75633bf7ef6b7dd3041725c921
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378261"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-key-vault"></a>Oktatóanyag: Hozzáférés az Azure Key Vaulthoz egy Windows VM-beli, rendszer által hozzárendelt felügyelt identitással 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Ez az oktatóanyag bemutatja, hogyan férhet hozzá egy Windows rendszerű virtuális gép (VM) rendszer által hozzárendelt felügyelt identitással a [Azure Key Vault.](../../key-vault/general/overview.md) A rendszerindítási pontként szolgáló Key Vault lehetővé teszi, hogy az ügyfélalkalmazás ezután titkos okkal hozzáférjen az Azure Active Directory által nem védett erőforrásokhoz. A felügyeltszolgáltatás-identitásokat az Azure automatikusan kezeli, és lehetővé teszi a hitelesítést az Azure AD-hitelesítést támogató szolgáltatásokban anélkül, hogy hitelesítési adatokat ad meg a kódban.

Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Hozzáférés engedélyezése a VM számára a Key Vaultban tárolt titkos kódokhoz
> * Hozzáférési jogkivonat lekérése a VM identitásával, majd a titkos kód lekérése a Key Vaultból 

## <a name="prerequisites"></a>Előfeltételek

- A felügyelt identitások ismerete. Ha még nem ismeri az Azure-erőforrások felügyelt identitására vonatkozó funkciót, tekintse meg ezt az [áttekintést](overview.md). 
- Egy Azure-fiók, [regisztráljon egy ingyenes fiókra.](https://azure.microsoft.com/free/)
- "Tulajdonos" engedélyek a megfelelő hatókörben (az előfizetésben vagy az erőforráscsoportban) a szükséges erőforrás-létrehozási és szerepkör-kezelési lépések végrehajtásához. Ha segítségre van szüksége a szerepkör-hozzárendeléssel kapcsolatos információkhoz, tekintse meg az Azure-előfizetések erőforrásaihoz való hozzáférés kezeléséhez [szükséges Azure-szerepkörök hozzárendelését.](../../role-based-access-control/role-assignments-portal.md)
- Szüksége lesz egy Windows rendszerű virtuális gépre is, amelynél engedélyezve vannak a rendszer által hozzárendelt felügyelt identitások.
  - Ha létre kell hoznia egy virtuális gépet ehhez az oktatóanyaghoz, kövesse a Virtuális gép létrehozása a rendszer által hozzárendelt identitással című [cikket](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity)

## <a name="create-a-key-vault"></a>Kulcstartó létrehozása  

Ez a szakasz bemutatja, hogyan adhat hozzáférést a virtuális gépnek egy tárolóban tárolt titkos Key Vault. Az Azure-erőforrások felügyelt identitásainak segítségével a kód hozzáférési jogkivonatokat kérhet le az olyan erőforrások felé történő hitelesítéshez, amelyek támogatják az Azure AD-hitelesítést.Az Azure AD-hitelesítést azonban nem minden Azure-szolgáltatás támogatja. Az Azure-erőforrások felügyelt identitásainak ilyen szolgáltatásokkal való használatához tárolja el a szolgáltatás hitelesítő adatait az Azure Key Vaultban, és a virtuális gép felügyelt identitásával férjen hozzá a Key Vaulthoz, hogy le tudja kérni a hitelesítő adatokat.

Először létre kell hozni egy Key Vaultot, és gondoskodni kell róla, hogy a VM rendszer által hozzárendelt felügyelt identitása hozzá tudjon férni.

1. Az Azure Portal [megnyitása](https://portal.azure.com/)
1. A bal oldali navigációs sáv tetején válassza az Erőforrás **létrehozása lehetőséget.**  
1. A Keresés **a Marketplace-en mezőbe** írja be a következőt: **Key Vault** nyomja le az **Enter billentyűt.**  
1. Válassza **Key Vault** lehetőséget az eredmények közül.
1. Kattintson a **Létrehozás** elemre.
1. Adja meg az új Key Vault **nevét**.

    ![Kulcstartó létrehozása képernyő](./media/msi-tutorial-windows-vm-access-nonaad/create-key-vault.png)

1. Adja meg az összes szükséges információt, és győződjön meg arról, hogy azt az előfizetést és erőforráscsoportot választotta ki, amelyben létrehozta az oktatóanyaghoz használt virtuális gépet.
1. Válassza az **Áttekintés+ létrehozás lehetőséget**
1. Kattintson a **Létrehozás** elemre.

### <a name="create-a-secret"></a>Titkos kulcs létrehozása

Ezután adjon hozzá egy titkos kódot a Key Vault, hogy később lekérhető a virtuális gépen futó kóddal. Ebben az oktatóanyagban a PowerShellt használjuk, de ugyanezek a fogalmak vonatkoznak a virtuális gépen végrehajtható kódokra is.

1. Lépjen az újonnan létrehozott Key Vault.
1. Válassza a **Titkos kódok** lehetőséget, és kattintson a **Hozzáadás** gombra.
1. Válassza a **Generate/Import (Generálás/Importálás) lehetőséget**
1. A Titkos **adat létrehozása a** Feltöltési lehetőségek közül képernyőn **hagyja** **bejelölve a Manuális** beállítást.
1. Adja meg a titkos kód nevét és értékét.  Az érték bármi lehet. 
1. Hagyja az aktiválási és a lejárati dátumot üresen, az **Engedélyezve** beállítást pedig az **Igen** értéken. 
1. A titkos kód létrehozásához kattintson a **Létrehozás** parancsra.

   ![Titkos kulcs létrehozása](./media/msi-tutorial-windows-vm-access-nonaad/create-secret.png)

## <a name="grant-access"></a>Hozzáférés biztosítása

A virtuális gép által használt felügyelt identitásnak hozzáférést kell biztosítani a tárolóban tárolt titkos Key Vault.

1. Lépjen az újonnan létrehozott Key Vault
1. A **bal oldali menüben** válassza a Hozzáférési szabályzat lehetőséget.
1. Válassza a **Hozzáférési szabályzat hozzáadása lehetőséget**

   ![kulcstartó – hozzáférési szabályzat létrehozása képernyő](./media/msi-tutorial-windows-vm-access-nonaad/key-vault-access-policy.png)

1. A Hozzáférési **szabályzat hozzáadása szakaszban,** a **Konfigurálás sablonból (nem kötelező)** területen válassza a **lekért** menü Titkos adatok kezelése elemét.
1. Válassza a **Rendszerbiztonsági tag kijelölése** lehetőséget, és a keresőmezőben adja meg a korábban létrehozott virtuális gép nevét.  Válassza ki a virtuális gépet az eredménylistában, majd válassza a **Kijelölés lehetőséget.**
1. Válassza a **Hozzáadás** lehetőséget
1. Kattintson a **Mentés** gombra.


## <a name="access-data"></a>Adatok elérése  

Ez a szakasz bemutatja, hogyan lehet lekérni egy hozzáférési jogkivonatot a virtuális gép identitásával, és hogyan lehet vele lekérni a titkos adatokat a Key Vault. Ha nincs telepítve a PowerShell 4.3.1-es vagy újabb verziója, [le kell töltenie és telepítenie kell a legújabb verziót](/powershell/azure/).

Először a VM rendszer által hozzárendelt felügyelt identitásával szerezzen be egy hozzáférési jogkivonatot a Key Vaulton végzett hitelesítéshez:
 
1. A portálon lépjen a **Virtuális gépek** lapra, lépjen a Windows VM-hez, és az **Áttekintés** területen kattintson a **Csatlakozás** elemre.
2. Adja meg a **Felhasználónevet** és **a Jelszót,** amelyet a Windows rendszerű virtuális gép **létrehozásakor adott hozzá.**  
3. Most, hogy létrehozott egy **távoli asztali kapcsolatot** a virtuális géppel, nyissa meg a PowerShellt a távoli munkamenetben.  
4. A PowerShellben hívjon meg egy webes kérést a bérlőn, amellyel lekéri a jogkivonatot a helyi gazdagéphez a virtuális gép adott portján.  

A PowerShell-kérés:

```powershell
$Response = Invoke-RestMethod -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -Method GET -Headers @{Metadata="true"} 
```

Az alábbi képen látható, hogy néz ki a válasz:

![kérés jogkivonattal válaszként](./media/msi-tutorial-windows-vm-access-nonaad/token.png)

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

Ha törölni szeretné az erőforrásokat, látogasson el a [Azure Portal](https://portal.azure.com)webhelyre, válassza az Erőforráscsoportok **lehetőséget,** keresse meg és válassza ki az oktatóanyag során létrehozott erőforráscsoportot (például ), majd használja az Erőforráscsoport törlése `mi-test` parancsot. 

Másik lehetőségként ezt a PowerShell vagy a [parancssori](../../azure-resource-manager/management/delete-resource-group.md) felület használatával is meg lehet tenni

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan használhat Windows VM-beli, rendszer által hozzárendelt felügyelt identitást a Azure Key Vault.  További információ az Azure Key Vault szolgáltatásról:

> [!div class="nextstepaction"]
>[Azure Key Vault](../../key-vault/general/overview.md)
