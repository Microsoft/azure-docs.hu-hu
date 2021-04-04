---
title: Azure Cosmos DB kulcsok tárolása és elérése Key Vault használatával
description: A Azure Key Vault használatával tárolhatja és érheti el Azure Cosmos DB kapcsolati karakterláncot, kulcsokat és végpontokat.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: how-to
ms.date: 05/23/2019
ms.reviewer: sngun
ms.openlocfilehash: a9bea0664f99a21ac734de666c802e9875ff00b5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359322"
---
# <a name="secure-azure-cosmos-keys-using-azure-key-vault"></a>Azure Cosmos-kulcsok védelme az Azure Key Vaulttal 
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

>[!IMPORTANT]
> A Azure Cosmos DB kulcsok eléréséhez ajánlott megoldás egy [rendszer által hozzárendelt felügyelt identitás](managed-identity-based-authentication.md)használata. Ha a szolgáltatás nem tudja kihasználni a felügyelt identitások előnyeit, használja a [tanúsítvány-alapú megoldást](certificate-based-authentication.md). Ha a felügyelt Identity megoldás és a tanúsítvány-alapú megoldás sem felel meg az igényeinek, használja az alábbi Key Vault-megoldást.

Az alkalmazások Azure Cosmos DB használatakor a végpont és a kulcs használatával férhet hozzá az adatbázishoz, a gyűjteményekhez és a dokumentumokhoz az alkalmazás konfigurációs fájlján belül.  A kulcsokat és URL-címeket azonban nem lehet közvetlenül az alkalmazás kódjába helyezni, mert az összes felhasználó számára elérhetők tiszta szöveges formátumban. Azt szeretné, hogy a végpont és a kulcsok biztonságos mechanizmuson keresztül legyenek elérhetők. Az Azure Key Vault segít a titkos alkalmazáskulcsok biztonságos kezelésében és tárolásában.

A következő lépések szükségesek a Key Vault Azure Cosmos DB elérési kulcsainak tárolásához és olvasásához:

* Kulcstartó létrehozása  
* Azure Cosmos DB hozzáférési kulcsok hozzáadása a Key Vault  
* Azure-Webalkalmazás létrehozása  
* Regisztrálja az alkalmazást & engedélyt ad a Key Vault olvasásához  


## <a name="create-a-key-vault"></a>Kulcstartó létrehozása

1. Jelentkezzen be [Azure Portalba](https://portal.azure.com/).  
2. Válassza **az erőforrás létrehozása > biztonsági > Key Vault** lehetőséget.  
3. A **Kulcstartó létrehozása** szakaszban adja meg a következő információkat:  
   * **Név:** Adjon egyedi nevet a Key Vaultnak.  
   * **Előfizetés:** Válassza ki az előfizetést, amelyet használni fog.  
   * Az **Erőforráscsoport** területen válassza az **Új létrehozása** lehetőséget, és írja be az erőforráscsoport nevét.  
   * A Hely legördülő menüből válassza ki a helyet.  
   * Hagyja meg az egyéb beállításokat az alapértelmezett beállításokkal.  
4. A fenti adatok megadása után válassza a **Létrehozás** elemet.  

## <a name="add-azure-cosmos-db-access-keys-to-the-key-vault"></a>Adja hozzá Azure Cosmos DB hozzáférési kulcsokat a Key Vaulthoz.
1. Navigáljon az előző lépésben létrehozott Key Vault, majd nyissa meg a **titkok** lapot.  
2. Válassza a **+ előállítás/importálás** lehetőséget, 

   * Válassza  a manuális **lehetőséget a feltöltési beállításokhoz**.
   * Adja meg a titkos kulcs **nevét**
   * Adja meg a Cosmos DB-fiókjának a Value ( **érték** ) mezőbe való kapcsolási karakterláncát. Ezt követően válassza a **Profil létrehozása** lehetőséget.

   :::image type="content" source="./media/access-secrets-from-keyvault/create-a-secret.png" alt-text="Titkos kulcs létrehozása":::

4. A titkos kód létrehozása után nyissa meg, és másolja a * * titkos azonosítót, amely a következő formátumban van. Ezt az azonosítót a következő szakaszban fogja használni. 

   `https://<Key_Vault_Name>.vault.azure.net/secrets/<Secret _Name>/<ID>`

## <a name="create-an-azure-web-application"></a>Azure-Webalkalmazás létrehozása

1. Hozzon létre egy Azure-webalkalmazást, vagy töltse le az alkalmazást a [GitHub-adattárból](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/Demo/keyvaultdemo). Ez egy egyszerű MVC-alkalmazás.  

2. Bontsa ki a letöltött alkalmazást, és nyissa meg a **HomeController. cs** fájlt. Frissítse a titkos azonosítót a következő sorba:

   `var secret = await keyVaultClient.GetSecretAsync("<Your Key Vault’s secret identifier>")`

3. **Mentse** a fájlt, és hozza **létre** a megoldást.  
4. Ezután telepítse az alkalmazást az Azure-ba. Kattintson a jobb gombbal a projekt elemre, és válassza a **Közzététel** lehetőséget. Hozzon létre egy új App Service-profilt (nevezze el az alkalmazás WebAppKeyVault1), és válassza a **Közzététel** lehetőséget.   

5. Az alkalmazás telepítése után. A Azure Portal navigáljon az üzembe helyezett webalkalmazáshoz, és kapcsolja be az alkalmazás **felügyelt szolgáltatás identitását** .  

   :::image type="content" source="./media/access-secrets-from-keyvault/turn-on-managed-service-identity.png" alt-text="Felügyelt szolgáltatás identitása":::

Ha most futtatja az alkalmazást, a következő hibaüzenet jelenik meg, mivel nem kapott engedélyt az alkalmazásra Key Vault.

:::image type="content" source="./media/access-secrets-from-keyvault/app-deployed-without-access.png" alt-text="Az alkalmazás hozzáférés nélkül lett üzembe helyezve":::

## <a name="register-the-application--grant-permissions-to-read-the-key-vault"></a>Regisztrálja az alkalmazást & engedélyt ad a Key Vault olvasásához

Ebben a szakaszban regisztrálnia kell az alkalmazást Azure Active Directory, és engedélyt kell adnia az alkalmazásnak a Key Vault olvasásához. 

1. Navigáljon a Azure Portalhoz, és nyissa meg az előző szakaszban létrehozott **Key Vault** .  

2. Nyissa meg a **hozzáférési házirendeket**, válassza az **+ új** keresés a telepített webalkalmazásban lehetőséget, válassza az engedélyek lehetőséget, majd kattintson **az OK gombra**.  

   :::image type="content" source="./media/access-secrets-from-keyvault/add-access-policy.png" alt-text="Hozzáférési szabályzat hozzáadása":::

Most, ha futtatja az alkalmazást, elolvashatja a titkos kulcsot Key Vaultról.

:::image type="content" source="./media/access-secrets-from-keyvault/app-deployed-with-access.png" alt-text="Titkos kulccsal üzembe helyezett alkalmazás":::
 
Hasonlóképpen hozzáadhat egy felhasználót is a kulcstartó eléréséhez. A **hozzáférési szabályzatok** kiválasztásával, majd az alkalmazás Visual studióból való futtatásához szükséges összes engedély megadásával saját magának kell felvennie a Key Vault. Ha az alkalmazás az asztalról fut, az identitást veszi igénybe.

## <a name="next-steps"></a>Következő lépések

* Ha Azure Cosmos DB tűzfalat szeretne konfigurálni, tekintse meg a [tűzfalat támogató](how-to-configure-firewall.md) cikket.
* A virtuális hálózati szolgáltatás végpontjának konfigurálásához tekintse meg a [biztonságos hozzáférés a VNet szolgáltatás végpontjának használatával](how-to-configure-vnet-service-endpoint.md) című cikket.
