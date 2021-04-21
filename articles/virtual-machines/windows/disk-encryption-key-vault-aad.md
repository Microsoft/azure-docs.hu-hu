---
title: Kulcstartó létrehozása és konfigurálása Azure Disk Encryption Azure AD-val (korábbi kiadás)
description: Ebből a cikkből megtudhatja, hogyan hozhat létre és konfigurálható kulcstartó Azure Disk Encryption Azure AD-val.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.topic: how-to
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: b3ba3e18f175fad543dbb2350df76354b758575f
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813811"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption-with-azure-ad-previous-release"></a>Kulcstartó létrehozása és konfigurálása Azure Disk Encryption Azure AD-val (korábbi kiadás)

**A virtuális gépek új Azure Disk Encryption szükségtelenné teszi, hogy Azure AD-alkalmazásparamétert biztosítsunk a virtuális gépek lemeztitkosításának engedélyezéséhez. Az új kiadásban már nem kell Azure AD-beli hitelesítő adatokat megadnia a titkosítás engedélyezése lépés során. Minden új virtuális gépnek titkosítva kell lennie az Azure AD alkalmazásparaméterei nélkül az új kiadással. A virtuálisgép-lemeztitkosítás új kiadással való engedélyezésére vonatkozó utasításokért lásd: [Azure Disk Encryption.](disk-encryption-overview.md) Az Azure AD-alkalmazásparaméterekkel már titkosított virtuális gépek továbbra is támogatottak, és továbbra is az AAD-szintaxissal kell fenntartani őket.**

Azure Disk Encryption titkosítási Azure Key Vault a lemeztitkosítási kulcsok és titkos kulcsok vezérlésére és kezelésére.  További információ a kulcstartókról: [A kulcstartók Azure Key Vault](../../key-vault/general/overview.md) a [Kulcstartó biztonságossá biztosítása.](../../key-vault/general/security-features.md) 

Az Azure AD-val (Azure Disk Encryption) való használatra használt kulcstartó létrehozása és konfigurálása három lépésből áll:

1. Kulcstartó létrehozása. 
2. Azure AD-alkalmazás és -szolgáltatásnév beállítása.
3. A kulcstartó hozzáférési szabályzatának beállítása az Azure AD-alkalmazás számára.
4. A kulcstartó speciális hozzáférési szabályzatainak beállítása.
 
Szükség esetén létrehozhat vagy importálhat egy kulcstitkosítási kulcsot (KEK).

Az eszközök [telepítésének és az](disk-encryption-key-vault.md) Azure-hoz való csatlakozásnak a lépéseit a Kulcstartó létrehozása és konfigurálása Azure Disk Encryption cikkben [talál.](disk-encryption-key-vault.md#install-tools-and-connect-to-azure)

> [!Note]
> A cikkben található lépések az előfeltételként szükséges [parancssori Azure Disk Encryption és](https://github.com/ejarvi/ade-cli-getting-started) az előfeltételként Azure Disk Encryption [PowerShell-szkriptben vannak automatizálva.](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)


## <a name="create-a-key-vault"></a>Kulcstartó létrehozása 
Azure Disk Encryption integrálva van a [Azure Key Vault,](https://azure.microsoft.com/documentation/services/key-vault/) így vezérelhetőek és kezelhetők a lemeztitkosítási kulcsok és titkos kulcsok a Key Vault-előfizetésben. Létrehozhat egy kulcstartót, vagy használhat egy meglévőt a Azure Disk Encryption. További információ a kulcstartókról: [A](../../key-vault/general/overview.md) kulcstartók Azure Key Vault [a Key Vault biztonságossá tere.](../../key-vault/general/security-features.md) Kulcstartó létrehozásához Resource Manager sablont, Azure PowerShell az Azure CLI-t. 


>[!WARNING]
>Annak érdekében, hogy a titkosítási titkos kulcsok ne lépik át a régiók határait, a Azure Disk Encryption a Key Vault és a virtuális gépeket ugyanabban a régióban kell elhelyezni. Hozzon létre és Key Vault, amely ugyanabban a régióban található, mint a titkosítani szükséges virtuális gép. 


### <a name="create-a-key-vault-with-powershell"></a>Kulcstartó létrehozása a PowerShell használatával

A [New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault) parancsmaggal Azure PowerShell kulcstartót hozhat létre. További parancsmagok a Key Vault: [Az.KeyVault.](/powershell/module/az.keyvault/) 

1. Szükség esetén hozzon létre egy új erőforráscsoportot a [New-AzResourceGroup segítségével.](/powershell/module/az.Resources/New-azResourceGroup)  Az adatközpontok helyének listához használja a [Get-AzLocation et.](/powershell/module/az.resources/get-azlocation) 
     
     ```azurepowershell-interactive
     # Get-AzLocation 
     New-AzResourceGroup –Name 'MyKeyVaultResourceGroup' –Location 'East US'
     ```

1. Új kulcstartó létrehozása a [New-AzKeyVault használatával](/powershell/module/az.keyvault/New-azKeyVault)
    
      ```azurepowershell-interactive
     New-AzKeyVault -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -Location 'East US'
     ```

4. Jegyezze fel a tároló **nevét,** az **erőforráscsoport** nevét, az erőforrás-azonosítót, a tároló  **URI-ját** és az objektumazonosítót, amelyek később a lemezek titkosításakor vannak visszaadva.  


### <a name="create-a-key-vault-with-azure-cli"></a>Kulcstartó létrehozása az Azure CLI használatával
Kulcstartóját az az [keyvault](/cli/azure/keyvault#commands) parancsokkal kezelheti az Azure CLI-val. Kulcstartó létrehozásához használja az [az keyvault create kulcsot.](/cli/azure/keyvault#az_keyvault_create)

1. Szükség esetén hozzon létre egy új erőforráscsoportot [az az group create gombra.](/cli/azure/group#az_group_create) A helyek listához használja [az az account list-locations et](/cli/azure/account#az_account_list) 
     
     ```azurecli-interactive
     # To list locations: az account list-locations --output table
     az group create -n "MyKeyVaultResourceGroup" -l "East US"
     ```

3. Hozzon létre egy új kulcstartót [az az keyvault create használatával.](/cli/azure/keyvault#az_keyvault_create)
    
     ```azurecli-interactive
     az keyvault create --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --location "East US"
     ```

4. Jegyezze fel a tároló **nevét** (nevét), **az erőforráscsoport** **nevét,** az erőforrás-azonosítót (ID), a tároló  **URI-ját** és a később használatra visszaadott objektumazonosítót. 

### <a name="create-a-key-vault-with-a-resource-manager-template"></a>Kulcstartó létrehozása Resource Manager sablonnal

Kulcstartót a következő sablonnal [hozhat Resource Manager:](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

1. Az Azure gyorsindítási sablonban kattintson az Üzembe **helyezés az Azure-ban elemre.**
2. Válassza ki az előfizetést, az erőforráscsoportot, az erőforráscsoport helyét, Key Vault nevét, az objektumazonosítót, a jogi feltételeket és a szerződést, majd kattintson a **Vásárlás gombra.** 


## <a name="set-up-an-azure-ad-app-and-service-principal"></a>Azure AD-alkalmazás és -szolgáltatásnév beállítása 
Ha engedélyeznie kell a titkosítást egy futó Azure-beli virtuális gépen, a Azure Disk Encryption létrehozza és beírja a titkosítási kulcsokat a kulcstartóba. A kulcstartóban található titkosítási kulcsok kezeléséhez Azure AD-hitelesítés szükséges. Hozzon létre egy Azure AD-alkalmazást erre a célra. Hitelesítési célokra használhatja az ügyfél titkos kulcsán alapuló hitelesítést vagy az ügyfél-tanúsítványalapú [Azure AD-hitelesítést.](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md)


### <a name="set-up-an-azure-ad-app-and-service-principal-with-azure-powershell"></a>Azure AD-alkalmazás és -szolgáltatásnév beállítása Azure PowerShell 
A következő parancsok végrehajtásához szerezze be és használja az [Azure AD PowerShell-modult.](/powershell/azure/active-directory/install-adv2) 

1. Azure AD-alkalmazás létrehozásához használja a [New-AzADApplication](/powershell/module/az.resources/new-azadapplication) PowerShell-parancsmagot. A MyApplicationHomePage és a MyApplicationUri bármilyen érték lehet.

     ```azurepowershell
     $aadClientSecret = "My AAD client secret"
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force
     $azureAdApplication = New-AzADApplication -DisplayName "My Application Display Name" -HomePage "https://MyApplicationHomePage" -IdentifierUris "https://MyApplicationUri" -Password $aadClientSecretSec
     $servicePrincipal = New-AzADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId
     ```

3. A $azureAdApplication.ApplicationId az Azure AD-ügyfélazonosító, a $aadClientSecret pedig az ügyfél titkos Azure Disk Encryption. Az Azure AD-ügyfél titkos titokban való megfelelő védelme. A `$azureAdApplication.ApplicationId` futtatása megmutatja az ApplicationID-t.


### <a name="set-up-an-azure-ad-app-and-service-principal-with-azure-cli"></a>Azure AD-alkalmazás és -szolgáltatásnév beállítása az Azure CLI-val

A szolgáltatásnéveket az Azure CLI-val kezelheti [az az ad sp parancsokkal.](/cli/azure/ad/sp) További információ: [Azure-szolgáltatásnév létrehozása.](/cli/azure/create-an-azure-service-principal-azure-cli)

1. Hozzon létre egy új szolgáltatásnév.
     
     ```azurecli-interactive
     az ad sp create-for-rbac --name "ServicePrincipalName" --password "My-AAD-client-secret" --skip-assignment 
     ```
3.  A visszaadott appId a más parancsokban használt Azure AD-ügyfélazonosító. Az az keyvault set-policy esetében is ezt az SPN-t fogja használni. A jelszó az ügyfél titkos jelszava, amely később a jelszó engedélyezéséhez Azure Disk Encryption. Az Azure AD titkos ügyféltitkának megfelelő védelme.
 
### <a name="set-up-an-azure-ad-app-and-service-principal-though-the-azure-portal"></a>Azure AD-alkalmazás és -szolgáltatásnév beállítása a Azure Portal
Az Azure AD-alkalmazás létrehozásához kövesse [a](../../active-directory/develop/howto-create-service-principal-portal.md) Portál használata Azure Active Directory-alkalmazás és -szolgáltatásnév létrehozásához szükséges lépéseket, amelyek hozzáférhetnek az erőforrásokhoz. Az alábbi lépésenként közvetlenül a cikk szakaszba lép, és csak ezt kell végrehajtania. 

1. [A szükséges engedélyek ellenőrzése](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)
2. [Új Azure Active Directory létrehozása](../../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal) 
     - Az alkalmazás létrehozásakor bármilyen nevet és bejelentkezési URL-címet használhat.
3. [Szerezze be az alkalmazásazonosítót és a hitelesítési kulcsot.](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) 
     - A hitelesítési kulcs a titkos ügyfélkód, és a Set-AzVMDiskEncryptionExtension AadClientSecret szolgáltatása. 
        - Az alkalmazás a hitelesítési kulcsot használja hitelesítő adatként az Azure AD-be való bejelentkezéshez. A Azure Portal ezt a titkos kulcsot kulcsoknak nevezzük, de nincs kapcsolatuk a kulcstartókhoz. Ennek a titkos nak a megfelelő biztonságának biztosítása. 
     - Az alkalmazásazonosítót később az alkalmazás AadClientId azonosítójaként fogja használni a Set-AzVMDiskEncryptionExtension és a Set-AzKeyVaultAccessPolicy ServicePrincipalName paramétereként. 

## <a name="set-the-key-vault-access-policy-for-the-azure-ad-app"></a>A kulcstartó hozzáférési szabályzatának beállítása az Azure AD-alkalmazás számára
Ahhoz, hogy titkosítási titkos kódokat írjon egy adott Key Vault-Azure Disk Encryption, szüksége van annak a Azure Active Directory-alkalmazásnak az ügyfél-azonosítójára és titkos ügyféltitkjára, amely engedéllyel rendelkezik a titkos kulcsok írásához a Key Vault. 

> [!NOTE]
> Azure Disk Encryption a következő hozzáférési szabályzatokat kell konfigurálnia az Azure AD-ügyfélalkalmazáshoz: _WrapKey_ és _Set_ engedélyek.

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-azure-powershell"></a>Kulcstartó-hozzáférési szabályzat beállítása az Azure AD-alkalmazáshoz Azure PowerShell
Az Azure AD-alkalmazásnak jogosultságokkal kell hozzáférnie a tárolóban található kulcsokhoz vagy titkos kulcsokhoz. A [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) parancsmaggal engedélyeket adhat az alkalmazásnak a _–ServicePrincipalName_ paraméterértékként használt ügyfél-azonosító (amelyet az alkalmazás regisztrálva lett) használatával. További tudnivalókért tekintse meg a következő [blogbejegyzést: Azure Key Vault - Step by Step (Részletes).](/archive/blogs/kv/azure-key-vault-step-by-step) 

1. Állítsa be az AD-alkalmazás kulcstartó-hozzáférési szabályzatát a PowerShell használatával.

     ```azurepowershell
     $keyVaultName = 'MySecureVault'
     $aadClientID = 'MyAadAppClientID'
     $KVRGname = 'MyKeyVaultResourceGroup'
     Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname
     ```

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-azure-cli"></a>Az Azure AD-alkalmazás kulcstartó-hozzáférési szabályzatának beállítása az Azure CLI-val
A hozzáférési szabályzat beállítását az [az keyvault set-policy](/cli/azure/keyvault#az_keyvault_set_policy) használatával állíthatja be. További információ: [Manage Key Vault using CLI 2.0 (Az alkalmazások kezelése a CLI 2.0 használatával).](../../key-vault/general/manage-with-cli2.md#authorizing-an-application-to-use-a-key-or-secret)

Adjon hozzáférést az Azure CLI-n keresztül létrehozott szolgáltatásnévnek, hogy titkos kulcsokat lekért és kulcsokat burkol a következő paranccsal:

```azurecli-interactive
az keyvault set-policy --name "MySecureVault" --spn "<spn created with CLI/the Azure AD ClientID>" --key-permissions wrapKey --secret-permissions set
```

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-the-portal"></a>Az Azure AD-alkalmazás kulcstartó-hozzáférési szabályzatának beállítása a portálon

1. Nyissa meg az erőforráscsoportot a kulcstartóval.
2. Válassza ki a kulcstartót, válassza a Hozzáférési **szabályzatok lehetőséget,** majd kattintson az **Új hozzáadása elemre.**
3. A **Rendszerbiztonsági tag kiválasztása alatt** keresse meg és válassza ki a létrehozott Azure AD-alkalmazást. 
4. A **Kulcsengedélyek alatt** jelölje be a Wrap Key (Kulcs **burkelése)** jelölőnégyzetet **a Cryptographic Operations (Titkosítási műveletek) alatt.**
5. Titkos **engedélyekkel kapcsolatosakért** jelölje be a **Set (Beállítás) jelölőnégyzetet** a **Secret Management Operations (Titkos hozzáférés-kezelési műveletek) alatt.**
6. Kattintson **az OK** gombra a hozzáférési szabályzat mentéshez. 

![Azure Key Vault titkosítási műveletek – Kulcs becsomagolása](../media/disk-encryption/keyvault-portal-fig3.png)

![Azure Key Vault titkos tok engedélyei – Beállítás](../media/disk-encryption/keyvault-portal-fig3b.png)

## <a name="set-key-vault-advanced-access-policies"></a>A kulcstartó speciális hozzáférési szabályzatainak beállítása
Az Azure platformnak hozzá kell férni a kulcstartóban található titkosítási kulcsokhoz vagy titkos kulcsokhoz, hogy elérhetővé tegye őket a virtuális gép számára a kötetek indításához és visszafejtéséhez. Engedélyezze a lemeztitkosítást a kulcstartón, különben az üzembe helyezések sikertelenek lesznek.  

### <a name="set-key-vault-advanced-access-policies-with-azure-powershell"></a>Kulcstartó speciális hozzáférési szabályzatok beállítása Azure PowerShell
 A Key Vault [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) PowerShell-parancsmagját használva engedélyezze a lemeztitkosítást a kulcstartóhoz.

  - **A Key Vault engedélyezése lemeztitkosításhoz:** Az Azure Disk Encryptionhez enabledForDiskEncryption szükséges.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDiskEncryption
     ```

  - **Engedélyezze Key Vault telepítéshez, ha szükséges:** Engedélyezi a Microsoft.Compute erőforrás-szolgáltatónak, hogy titkos kulcsokat lekérje ebből a kulcstartóból, amikor az erőforrás létrehozásakor erre a kulcstartóra hivatkozik, például egy virtuális gép létrehozásakor.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDeployment
     ```

  - **Engedélyezze Key Vault sablontelepítéshez, ha szükséges:** Engedélyezi Azure Resource Manager, hogy titkos kulcsokat lekért erről a kulcstartóról, amikor erre a kulcstartóra hivatkozik egy sablon üzembe helyezése során.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForTemplateDeployment
     ```

### <a name="set-key-vault-advanced-access-policies-using-the-azure-cli"></a>Kulcstartó speciális hozzáférési szabályzatának beállítása az Azure CLI használatával
Az [az keyvault update parancs használatával](/cli/azure/keyvault#az_keyvault_update) engedélyezze a lemeztitkosítást a kulcstartóhoz. 

 - **A Key Vault engedélyezése lemeztitkosításhoz:** A lemeztitkosítás engedélyezése kötelező. 

     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **Engedélyezze Key Vault központi telepítéshez, ha szükséges:** Engedélyezze Virtual Machines számára a titkos kulcsként tárolt tanúsítványok lekérését a tárolóból.
     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **Engedélyezze Key Vault sablontelepítéshez, ha szükséges:** Engedélyezze Resource Manager számára a titkos kulcsok lekérését a tárolóból.
     ```azurecli-interactive  
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-template-deployment "true"
     ```


### <a name="set-key-vault-advanced-access-policies-through-the-azure-portal"></a>Kulcstartó speciális hozzáférési szabályzatának beállítása a Azure Portal

1. Válassza ki a kulcstartót, válassza a **Hozzáférési** szabályzatok lehetőséget, majd kattintson ide **a speciális hozzáférési szabályzatok kiválasztásához.**
2. Jelölje be az Enable access to Azure Disk Encryption for volume encryption (Hozzáférés engedélyezése Azure Disk Encryption **kötettitkosításhoz) feliratú jelölőnégyzetet.**
3. Jelölje **be az Azure-Virtual Machines** hozzáférésének engedélyezése az üzembe helyezéshez és/vagy a Hozzáférés engedélyezése Azure Resource Manager a sablonok üzembe helyezéséhez lehetőséget, ha szükséges.  
4. Kattintson a **Mentés** gombra.

![Azure Key Vault – speciális hozzáférési szabályzatok](../media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-optional"></a>Kulcstitkosítási kulcs beállítása (nem kötelező)
Ha kulcstitkosítási kulcsot (KEK) szeretne használni a titkosítási kulcsok további biztonsági rétegeként, adjon hozzá egy KEK-et a kulcstartóhoz. Az [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) parancsmag használatával hozzon létre egy kulcstitkosítási kulcsot a kulcstartóban. A KEK-et a helyszíni kulcskezelő HSM-ről is importálhatja. További információt a [dokumentációban Key Vault talál.](../../key-vault/keys/hsm-protected-keys.md) Ha kulcstitkosítási kulcs van megadva, a Azure Disk Encryption ezzel a kulccsal burk szóval burköli a titkosítási titkos kódokat, mielőtt Key Vault. 

* Kulcsok generálásakor RSA-kulcstípust használjon. Azure Disk Encryption az elliptikus görbe kulcsok használata még nem támogatott.

* A Key Vault titkos kulcsának és kek URL-címének verziószámozva kell lennie. Az Azure kikényszeríteni a verziószámozás ezen korlátozását. Az érvényes titkos kódért és a KEK URL-címekért tekintse meg az alábbi példákat:

  * Példa egy érvényes titkos URL-címre:   *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Példa érvényes KEK URL-címre:   *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Azure Disk Encryption nem támogatja a portszámok kulcstartókulcsok és KEK URL-címek részeként való megadását. A nem támogatott és támogatott kulcstartó-URL-címekre az alábbi példákban talál példákat:

  * A Key Vault elfogadhatatlan URL-címe  *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Elfogadható kulcstartó URL-címe:   *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="set-up-a-key-encryption-key-with-azure-powershell"></a>Kulcstitkosítási kulcs beállítása Azure PowerShell 
A PowerShell-szkript használata előtt ismernie kell a Azure Disk Encryption előfeltételeit, hogy megismerheti a szkript lépéseit. Előfordulhat, hogy a mint szkriptnek módosításokra van szüksége a környezetben. Ez a szkript létrehozza Azure Disk Encryption összes előfeltételt, és titkosít egy meglévő IaaS virtuális gépet, kulcstitkosítási kulccsal burkolva a lemeztitkosítási kulcsot. 

 ```powershell
 # Step 1: Create a new resource group and key vault in the same location.
     # Fill in 'MyLocation', 'MyKeyVaultResourceGroup', and 'MySecureVault' with your values.
     # Use Get-AzLocation to get available locations and use the DisplayName.
     # To use an existing resource group, comment out the line for New-AzResourceGroup
     
     $Loc = 'MyLocation';
     $KVRGname = 'MyKeyVaultResourceGroup';
     $KeyVaultName = 'MySecureVault'; 
     New-AzResourceGroup –Name  $KVRGname –Location $Loc;
     New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc;
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname;
     $KeyVaultResourceId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname).ResourceId;
     $diskEncryptionKeyVaultUrl = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname).VaultUri;
     
 # Step 2: Create the AD application and service principal.
     # Fill in 'MyAADClientSecret', "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
     # MyApplicationHomePage and the MyApplicationUri can be any values you wish.
     
     $aadClientSecret =  'MyAADClientSecret';
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force;
     $azureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -Password $aadClientSecretSec
     $servicePrincipal = New-AzADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId;
     $aadClientID = $azureAdApplication.ApplicationId;
     
 #Step 3: Enable the vault for disk encryption and set the access policy for the Azure AD application.
     
     Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption;
     Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName  $KVRGname;
     
 #Step 4: Create a new key in the key vault with the Add-AzKeyVaultKey cmdlet.
     # Fill in 'MyKeyEncryptionKey' with your value.
     
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName -Destination 'Software';
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     
 #Step 5: Encrypt the disks of an existing IaaS VM
     # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values. 
     
     $VMName = 'MySecureVM';
      $VMRGName = 'MyVirtualMachineResourceGroup';
     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
```

## <a name="certificate-based-authentication-optional"></a>Tanúsítványalapú hitelesítés (nem kötelező)
Ha tanúsítványhitelesítést szeretne használni, feltölthet egyet a kulcstartóba, és üzembe helyezheti az ügyfélen. A PowerShell-szkript használata előtt ismernie kell a Azure Disk Encryption előfeltételeit, hogy megismerheti a szkript lépéseit. Előfordulhat, hogy a mint szkriptnek módosításokra van szüksége a környezetben.

     
 ```powershell

 # Fill in "MyKeyVaultResourceGroup", "MySecureVault", and 'MyLocation' ('My location' only if needed)

   $KVRGname = 'MyKeyVaultResourceGroup'
   $KeyVaultName= 'MySecureVault'

   # Create a key vault and set enabledForDiskEncryption property on it. 
   # Comment out the next three lines if you already have an existing key vault enabled for encryption. No need to set 'My location' in this case.

   $Loc = 'MyLocation'
   New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption

   #Setting some variables with the key vault information 
   $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname
   $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
   $KeyVaultResourceId = $KeyVault.ResourceId

   # Create the Azure AD application and associate the certificate with it. 
   # Fill in "C:\certificates\mycert.pfx", "Password", "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
   # MyApplicationHomePage and the MyApplicationUri can be any values you wish

   $CertPath = "C:\certificates\mycert.pfx"
   $CertPassword = "Password"
   $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
   $CertValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

   $AzureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -CertValue $CertValue 
   $ServicePrincipal = New-AzADServicePrincipal -ApplicationId $AzureAdApplication.ApplicationId

   $AADClientID = $AzureAdApplication.ApplicationId
   $aadClientCertThumbprint= $cert.Thumbprint

   Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname
   
   # Upload the pfx file to the key vault. 
   # Fill in "MyAADCert".  

   $KeyVaultSecretName = "MyAADCert"
   $FileContentBytes = get-content $CertPath -Encoding Byte
   $FileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
           $JSONObject = @"
           { 
               "data" : "$filecontentencoded", 
               "dataType" : "pfx", 
               "password" : "$CertPassword" 
           } 
"@

   $JSONObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
   $JSONEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

   #Set the secret and set the key vault policy for -EnabledForDeployment

   $Secret = ConvertTo-SecureString -String $JSONEncoded -AsPlainText -Force
   Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName -SecretValue $Secret
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDeployment

   # Deploy the certificate to the VM
   # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values.

   $VMName = 'MySecureVM'
   $VMRGName = 'MyVirtualMachineResourceGroup'
   $CertUrl = (Get-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName).Id
   $SourceVaultId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGName).ResourceId
   $VM = Get-AzVM -ResourceGroupName $VMRGName -Name $VMName 
   $VM = Add-AzVMSecret -VM $VM -SourceVaultId $SourceVaultId -CertificateStore "My" -CertificateUrl $CertUrl
   Update-AzVM -VM $VM -ResourceGroupName $VMRGName 

   #Enable encryption on the VM using Azure AD client ID and the client certificate thumbprint

   Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId
 ```

## <a name="certificate-based-authentication-and-a-kek-optional"></a>Tanúsítványalapú hitelesítés és KEK (nem kötelező)

Ha tanúsítványhitelesítést szeretne használni, és a titkosítási kulcsot egy KEK-hez szeretné burkni, használhatja példaként az alábbi szkriptet. A PowerShell-szkript használata előtt ismernie kell az összes korábbi Azure Disk Encryption előfeltételt, hogy megismerheti a szkript lépéseit. Előfordulhat, hogy a mint szkriptnek módosításokra van szüksége a környezetben.

     
 ```powershell
# Fill in 'MyKeyVaultResourceGroup', 'MySecureVault', and 'MyLocation' (if needed)

   $KVRGname = 'MyKeyVaultResourceGroup'
   $KeyVaultName= 'MySecureVault'

   # Create a key vault and set enabledForDiskEncryption property on it. 
   # Comment out the next three lines if you already have an existing key vault enabled for encryption.

   $Loc = 'MyLocation'
   New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption

   # Create the Azure AD application and associate the certificate with it.  
   # Fill in "C:\certificates\mycert.pfx", "Password", "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
   # MyApplicationHomePage and the MyApplicationUri can be any values you wish

   $CertPath = "C:\certificates\mycert.pfx"
   $CertPassword = "Password"
   $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
   $CertValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

   $AzureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -CertValue $CertValue 
   $ServicePrincipal = New-AzADServicePrincipal -ApplicationId $AzureAdApplication.ApplicationId

   $AADClientID = $AzureAdApplication.ApplicationId
   $aadClientCertThumbprint= $cert.Thumbprint

   ## Give access for setting secrets and wraping keys
   Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname

   # Upload the pfx file to the key vault. 
   # Fill in "MyAADCert". 

   $KeyVaultSecretName = "MyAADCert"
   $FileContentBytes = get-content $CertPath -Encoding Byte
   $FileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
           $JSONObject = @"
           { 
               "data" : "$filecontentencoded", 
               "dataType" : "pfx", 
               "password" : "$CertPassword" 
           } 
"@

   $JSONObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
   $JSONEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

   #Set the secret and set the key vault policy for deployment

   $Secret = ConvertTo-SecureString -String $JSONEncoded -AsPlainText -Force
   Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName -SecretValue $Secret
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDeployment

   #Setting some variables with the key vault information and generating a KEK 
   # FIll in 'KEKName'
   
   $KEKName ='KEKName'
   $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname
   $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
   $KeyVaultResourceId = $KeyVault.ResourceId
   $KEK = Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $KEKName -Destination "Software"
   $KeyEncryptionKeyUrl = $KEK.Key.kid



   # Deploy the certificate to the VM
   # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values.

   $VMName = 'MySecureVM';
   $VMRGName = 'MyVirtualMachineResourceGroup';
   $CertUrl = (Get-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName).Id
   $SourceVaultId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGName).ResourceId
   $VM = Get-AzVM -ResourceGroupName $VMRGName -Name $VMName 
   $VM = Add-AzVMSecret -VM $VM -SourceVaultId $SourceVaultId -CertificateStore "My" -CertificateUrl $CertUrl
   Update-AzVM -VM $VM -ResourceGroupName $VMRGName

   #Enable encryption on the VM using Azure AD client ID and the client certificate thumbprint

   Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId
```

 
## <a name="next-steps"></a>Következő lépések

[Az Azure Disk Encryption Azure AD-vel való használatának engedélyezése Windows rendszerű virtuális gépeken (korábbi kiadás)](disk-encryption-windows-aad.md)
