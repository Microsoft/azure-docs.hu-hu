---
title: Oktatóanyag `:` felügyelt identitás használata a Azure Key Vault-Linux-Azure ad eléréséhez
description: Az oktatóanyag azt ismerteti, hogyan lehet hozzáférni az Azure Resource Managerhez egy Linux VM-beli, rendszer által hozzárendelt felügyelt identitással.
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
ms.date: 12/16/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 61673210dafbdbdbc56477ed58fb0cbe9335efcf
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012690"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-key-vault"></a>Oktatóanyag: Az Azure Key Vault elérése Linux VM-beli, rendszer által hozzárendelt felügyelt identitással 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Ez az oktatóanyag bemutatja, hogyan használható a linuxos virtuális gép (VM) egy rendszer által hozzárendelt felügyelt identitással a [Azure Key Vault](../../key-vault/general/overview.md)eléréséhez. A rendszerindítási szolgáltatásként szolgáló Key Vault lehetővé teszi, hogy az ügyfélalkalmazás egy titkos kulcsot használjon a Azure Active Directory (AD) által nem védett erőforrások eléréséhez. A felügyelt szolgáltatás identitásait a rendszer automatikusan kezeli az Azure-ban, és lehetővé teszi az Azure AD-hitelesítést támogató szolgáltatások hitelesítését, anélkül, hogy a kód a hitelesítési adatokat is tartalmazza.

Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Hozzáférés engedélyezése a VM számára a Key Vaultban tárolt titkos kódokhoz 
> * Hozzáférési jogkivonat lekérése a VM identitásával, majd a titkos kód lekérése a Key Vaultból 
 
## <a name="prerequisites"></a>Előfeltételek

- A felügyelt identitások alapszintű ismerete. Ha még nem ismeri az Azure-erőforrások felügyelt identitására vonatkozó funkciót, tekintse meg ezt az [áttekintést](overview.md). 
- Egy Azure-fiókkal, [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- A "tulajdonos" engedély a megfelelő hatókörben (előfizetése vagy erőforráscsoport) a szükséges erőforrás-létrehozási és szerepkör-felügyeleti lépések végrehajtásához. Ha segítségre van szüksége a szerepkör-hozzárendeléssel kapcsolatban, tekintse meg az Azure [-szerepkörök hozzárendelése az Azure-előfizetés erőforrásaihoz való hozzáférés kezeléséhez](../../role-based-access-control/role-assignments-portal.md)című témakört
- Szüksége van egy linuxos virtuális gépre is, amelyhez engedélyezve van a rendszerhez rendelt felügyelt identitások.
  - Ha létre kell hoznia egy virtuális gépet ehhez az oktatóanyaghoz, kövesse a [linuxos virtuális gép létrehozása a Azure Portal](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine) használatával című cikket.


## <a name="create-a-key-vault"></a>Kulcstartó létrehozása  

Ez a szakasz bemutatja, hogyan biztosítható a virtuális gép hozzáférése egy Key Vault tárolt titkos kulcshoz. Az Azure-erőforrások felügyelt identitásainak segítségével a kód hozzáférési jogkivonatokat kérhet le az olyan erőforrások felé történő hitelesítéshez, amelyek támogatják az Azure AD-hitelesítést.Az Azure AD-hitelesítést azonban nem minden Azure-szolgáltatás támogatja. Az Azure-erőforrások felügyelt identitásainak ilyen szolgáltatásokkal való használatához tárolja el a szolgáltatás hitelesítő adatait az Azure Key Vaultban, és a virtuális gép felügyelt identitásával férjen hozzá a Key Vaulthoz, hogy le tudja kérni a hitelesítő adatokat.

Először létre kell hozni egy Key Vaultot, és gondoskodni kell róla, hogy a VM rendszer által hozzárendelt felügyelt identitása hozzá tudjon férni.

1. Az Azure [Portal](https://portal.azure.com/) megnyitása
1. A bal oldali navigációs sáv tetején válassza az **erőforrás létrehozása** lehetőséget.  
1. A **Keresés a piactéren** mezőbe írja be a **Key Vault** kifejezést, és nyomja le az **ENTER billentyűt**.  
1. Válassza ki **Key Vault** az eredmények közül.
1. Kattintson a **Létrehozás** elemre.
1. Adja meg az új Key Vault **nevét**.

    ![Key Vault-képernyő létrehozása](./media/tutorial-linux-vm-access-nonaad/create-key-vault.png)

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

   ![Titkos kulcs létrehozása](./media/tutorial-linux-vm-access-nonaad/create-secret.png)

## <a name="grant-access"></a>Hozzáférés biztosítása

A virtuális gép által használt felügyelt identitásnak hozzáférést kell adni ahhoz, hogy elolvassa a Key Vaultban tárolni kívánt titkot.

1. Navigáljon az újonnan létrehozott Key Vault
1. A bal oldali menüben válassza a **hozzáférési szabályzat** lehetőséget.
1. Válassza a **hozzáférési házirend hozzáadása** lehetőséget.

   ![Key Vault – hozzáférési szabályzat létrehozása képernyő](./media/tutorial-linux-vm-access-nonaad/key-vault-access-policy.png)

1. A **hozzáférési házirend hozzáadása** szakasz **Konfigurálás a sablon alapján (nem kötelező)** területen válassza a **titkos kezelés** lehetőséget a legördülő menüből.
1. Válassza a **Rendszerbiztonsági tag kijelölése** lehetőséget, és a keresőmezőben adja meg a korábban létrehozott virtuális gép nevét.  Válassza ki a virtuális gépet az eredmények listájában, és válassza a **kiválasztás** lehetőséget.
1. Válassza a **Hozzáadás** lehetőséget
1. Kattintson a **Mentés** gombra.

## <a name="access-data"></a>Adatok elérése

A lépések elvégzéséhez szüksége lesz egy SSH-ügyfélre.  Windows használata esetén használhatja a [Linux Windows alrendszerében](/windows/wsl/about) elérhető SSH-ügyfelet. Amennyiben segítségre van szüksége az SSH-ügyfél kulcsának konfigurálásához, [Az SSH-kulcsok és a Windows együttes használata az Azure-ban](../../virtual-machines/linux/ssh-from-windows.md) vagy [Nyilvános és titkos SSH-kulcspár létrehozása és használata az Azure-ban Linux rendszerű virtuális gépekhez](../../virtual-machines/linux/mac-create-ssh-keys.md) című cikkekben talál további információt.

>[!IMPORTANT]
> Minden Azure SDK támogatja az Azure. Identity könyvtárat, amely megkönnyíti az Azure AD-tokenek megszerzését a TARGET Services eléréséhez. További információ az [Azure SDK](https://azure.microsoft.com/downloads/) -k használatáról és az Azure. Identity Library kihasználása.
> - [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme?view=azure-dotnet)
> - [JAVA](https://docs.microsoft.com/java/api/overview/azure/identity-readme?view=azure-java-stable)
> - [JavaScript](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme?view=azure-node-latest)
> - [Python](https://docs.microsoft.com/python/api/overview/azure/identity-readme?view=azure-python)


1. A portálon lépjen a Linux virtuális gépre, és az **Áttekintés** területen kattintson a **Csatlakozás** gombra. 
2. **Kapcsolódjon** a virtuális géphez az Ön által választott SSH-ügyféllel. 
3. A terminálablakban a CURL használatával indítson egy olyan kérést az Azure-erőforrások helyi felügyelt identitásainak végpontjára, amely lekér egy hozzáférési jogkivonatot az Azure Key Vaultból.  
 
    A hozzáférési jogkivonatra vonatkozó CURL-kérelmet alább láthatja.  
    
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true  
    ```
    A válasz tartalmazza a Resource Manager eléréséhez szükséges hozzáférési jogkivonatot. 
    
    Válasz:  
    
    ```bash
    {"access_token":"eyJ0eXAi...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://vault.azure.net",
    "token_type":"Bearer"} 
    ```
    
    A hozzáférési jogkivonat használatával hitelesíthet az Azure Key Vaultban.  A következő CURL-kérés bemutatja, hogyan lehet a Key Vaultból titkos kulcsot beolvasni a CURL és a Key Vault REST API használatával.  Szüksége lesz a Key Vault URL-címére, amely a Key Vault **Áttekintés** lapjának **Alapvető erőforrások** szakaszában található.  Szüksége lesz az előző hívásban lekért hozzáférési jogkivonatra is. 
        
    ```bash
    curl 'https://<YOUR-KEY-VAULT-URL>/secrets/<secret-name>?api-version=2016-10-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```
    
    A válasz a következőképp fog kinézni: 
    
    ```bash
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
Miután lekérte a titkos kódot a Key Vaultból, a használatával hitelesítést végezhet olyan szolgáltatásokban, amelyekhez név és jelszó szükséges.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha szeretné törölni az erőforrásokat, látogasson el a [Azure Portalra](https://portal.azure.com), válassza az **erőforráscsoportok** lehetőséget, keresse meg és válassza ki az oktatóanyag folyamatában létrehozott erőforráscsoportot (például `mi-test` ), majd használja az **erőforráscsoport törlése** parancsot.

Ezt [a PowerShell vagy a parancssori](../../azure-resource-manager/management/delete-resource-group.md) felület használatával is elvégezheti.

## <a name="next-steps"></a>Következő lépések

Az oktatóanyag bemutatta, hogyan érhető el Azure Key Vault a Linux VM-beli, rendszer által hozzárendelt felügyelt identitással.  További információ az Azure Key Vault szolgáltatásról:

> [!div class="nextstepaction"]
>[Azure Key Vault](../../key-vault/general/overview.md)