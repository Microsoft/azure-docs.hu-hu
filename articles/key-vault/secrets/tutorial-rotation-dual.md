---
title: Rotációs oktatóanyag két hitelesítőadat-készletet tároló erőforrásokhoz
description: Ebből az oktatóanyagból megtudhatja, hogyan automatizálhatja a két hitelesítési hitelesítőadat-készletet felhasználó erőforrások titkos kulcsának rotációját.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: secrets
ms.topic: tutorial
ms.date: 06/22/2020
ms.author: jalichwa
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 1f656a41b0f447b90f58ec14173e418a2defb72e
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484829"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-that-have-two-sets-of-authentication-credentials"></a>Titkos kulcs rotációja automatizálható olyan erőforrásoknál, amelyek két hitelesítési hitelesítőadat-készletet is használnak

Az Azure-szolgáltatásokban való hitelesítés legjobb módja [a](../general/authentication.md)felügyelt identitás használata, de vannak olyan forgatókönyvek, ahol ez nem lehetséges. Ilyen esetekben a rendszer hozzáférési kulcsokat vagy jelszavakat használ. A hozzáférési kulcsokat és jelszavakat gyakran érdemes váltogatni.

Ez az oktatóanyag bemutatja, hogyan automatizálhatja a titkos kulcsok rendszeres rotációját olyan adatbázisok és szolgáltatások esetén, amelyek két hitelesítési hitelesítőadat-készletet használnak. Ez az oktatóanyag azt mutatja be, hogyan lehet titkos kulcsként váltogatni a Azure Key Vault tárolt Azure Storage-fiókkulcsokat. Egy értesítés által aktivált függvényt Azure Event Grid használni. 

> [!NOTE]
> A tárfiókkulcsok automatikusan kezelhetők a Key Vault ha közös hozzáférésű jogosultságkivonatokat ad meg a tárfiókhoz való delegált hozzáféréshez. Vannak olyan szolgáltatások, amelyekhez hozzáférési kulcsokkal való kapcsolati sztringekra van szükség a tárfiókhoz. Ehhez a forgatókönyvhöz ezt a megoldást javasoljuk.

Az oktatóanyag a következő rotációs megoldást ismerteti: 

![A rotációs megoldást bemutató diagram.](../media/secrets/rotation-dual/rotation-diagram.png)

Ebben a megoldásban Azure Key Vault tárfiók egyedi hozzáférési kulcsait ugyanazon titkos kulcs verzióiként tárolja, az elsődleges és a másodlagos kulcs között a későbbi verziókban váltakozóan. Ha egy hozzáférési kulcsot a titkos kulcs legújabb verziójában tárol, a rendszer újra létrehozza a másodlagos kulcsot, és hozzáadja Key Vault a titkos kulcs új legújabb verziójaként. A megoldás biztosítja az alkalmazás teljes rotációs ciklusát a legújabb újragenerált kulcsra való frissítéshez. 

1. A titkos kulcs lejárati dátuma előtt 30 nappal Key Vault a lejárati eseményt a Event Grid.
1. Event Grid ellenőrzi az esemény-előfizetéseket, és HTTP POST használatával hívja meg az eseményre előfizetett függvényalkalmazás-végpontot.
1. A függvényalkalmazás azonosítja a másodlagos kulcsot (nem a legújabbat), és a tárfiókot hívja meg az újrageneráláshoz.
1. A függvényalkalmazás hozzáadja az új újragenerált kulcsot Azure Key Vault a titkos kulcs új verziójaként.

## <a name="prerequisites"></a>Előfeltételek
* Azure-előfizetés. [Hozzon létre egyet ingyenesen.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Azure [Cloud Shell](https://shell.azure.com/). Ez az oktatóanyag a Portál-Cloud Shell PowerShell-env használatával
* Azure Key Vault.
* Két Azure Storage-fiók.

Ezt az üzembe helyezési hivatkozást akkor használhatja, ha még nincs kulcstartója és meglévő tárfiókja:

[![A Deploy to Azure (Üzembe helyezés az Azure-ban) címkével ellátott hivatkozás.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2FARM-Templates%2FInitial-Setup%2Fazuredeploy.json)

1. Az **Erőforráscsoport alatt** válassza az **Új létrehozása lehetőséget.** Adja a csoportnak a **vaultrotation nevet,** majd kattintson az **OK gombra.**
1. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.
1. Válassza a **Létrehozás** lehetőséget.

    ![Erőforráscsoport létrehozásáról készült képernyőkép.](../media/secrets/rotation-dual/dual-rotation-1.png)

Most már lesz egy kulcstartója és két tárfiókja. Ezt a beállítást az Azure CLI-ban ellenőrizheti, vagy Azure PowerShell paranccsal ellenőrizheti:
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az resource list -o table -g vaultrotation
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzResource -Name 'vaultrotation*' | Format-Table
```
---

Az eredmény a következő kimenethez hasonló lesz:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
vaultrotation-kv         vaultrotation      westus      Microsoft.KeyVault/vaults
vaultrotationstorage     vaultrotation      westus      Microsoft.Storage/storageAccounts
vaultrotationstorage2    vaultrotation      westus      Microsoft.Storage/storageAccounts
```

## <a name="create-and-deploy-the-key-rotation-function"></a>A kulcsrotációs függvény létrehozása és üzembe helyezése

A következő lépés egy függvényalkalmazás létrehozása egy rendszer által felügyelt identitással, valamint egyéb szükséges összetevőkkel. Emellett üzembe fogja helyezni a tárfiókkulcsok rotációs függvényét is.

A függvényalkalmazás rotációs függvényéhez a következő összetevőkre és konfigurációra van szükség:
- Egy Azure App Service terv
- Tárfiók a függvényalkalmazás eseményindítóinak kezeléséhez
- Hozzáférési szabályzat a titkos kulcsok eléréséhez a Key Vault
- A tárfiók kulcskezelői szolgáltatás szerepköre, amely a függvényalkalmazáshoz van hozzárendelve, hogy hozzáfér a tárfiók hozzáférési kulcshoz
- Egy kulcsrotációs függvény egy eseményindítóval és egy HTTP-eseményindítóval (igény szerinti rotáció)
- Egy Event Grid a **SecretNearExpiry eseményhez**

1. Válassza az Azure-sablon üzembe helyezésére mutató hivatkozást: 

   [![Azure-sablon üzembe helyezési hivatkozása.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2FARM-Templates%2FFunction%2Fazuredeploy.json)

1. Az **Erőforráscsoport listában** válassza a **vaultrotation lehetőséget.**
1. A **Tárfiók erőforráscsoportja** mezőben adja meg annak az erőforráscsoportnak a nevét, amelyben a tárfiók található. Tartsa meg az **alapértelmezett [resourceGroup().name]** értéket, ha a tárfiók már ugyanabban az erőforráscsoportban található, ahol a kulcsrotációs függvényt üzembe fogja helyezni.
1. A **Tárfiók neve mezőben** adja meg annak a tárfióknak a nevét, amely a váltogatni kívánt hozzáférési kulcsokat tartalmazza. Tartsa meg az **alapértelmezett [concat(resourceGroup().name, 'storage')] értéket,** ha az Előfeltételek alatt létrehozott [tárfiókot használja.](#prerequisites)
1. Az **Key Vault RG mezőbe** írja be annak az erőforráscsoportnak a nevét, amelyben a kulcstartó található. Tartsa meg az **alapértelmezett [resourceGroup().name]** értéket, ha a kulcstartó már létezik ugyanabban az erőforráscsoportban, ahol a kulcsrotációs függvényt üzembe fogja helyezni.
1. A **Key Vault név mezőbe** írja be a kulcstartó nevét. Tartsa meg az **alapértelmezett [concat(resourceGroup().name, '-kv')] értéket,** ha az Előfeltételek alatt létrehozott [kulcstartót használja.](#prerequisites)
1. A **App Service típusa mezőben** válassza a csomag üzemeltetése lehetőséget. **Prémium szintű csomagra** csak akkor van szükség, ha a kulcstartó tűzfal mögött van.
1. A **Függvényalkalmazás neve mezőben** adja meg a függvényalkalmazás nevét.
1. A Titkos **kulcs neve mezőbe** írja be annak a titkos kulcsnak a nevét, amelyben a hozzáférési kulcsokat tárolni fogja.
1. Az **Adattár URL-címe** mezőben adja meg a függvénykód GitHub-helyét. Ebben az oktatóanyagban a következőt **https://github.com/Azure-Samples/KeyVault-Rotation-StorageAccountKey-PowerShell.git** használhatja: .
1. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.
1. Válassza a **Létrehozás** lehetőséget.

   ![Képernyőkép a függvény létrehozásáról és üzembe helyezéséről.](../media/secrets/rotation-dual/dual-rotation-2.png)

Az előző lépések befejezése után lesz egy tárfiókja, egy kiszolgálófarmja, egy függvényalkalmazása és egy Application Insights. Az üzembe helyezés befejezése után a következő oldal látható:

   ![Képernyőkép az Üzembe helyezés befejeződött oldalról.](../media/secrets/rotation-dual/dual-rotation-3.png)
> [!NOTE]
> Ha hibába ütközik, válassza az Ismételt **üzembe** helyezés lehetőséget az összetevők üzembe helyezésének befejezéséhez.


A rotációs függvény üzembe helyezési sablonjai és kódja az [Azure-minták között található.](https://github.com/Azure-Samples/KeyVault-Rotation-StorageAccountKey-PowerShell)

## <a name="add-the-storage-account-access-keys-to-key-vault"></a>Adja hozzá a tárfiók hozzáférési kulcsait a Key Vault

Először állítsa be úgy a hozzáférési szabályzatot, hogy titkos kulcsok **kezelésére** vonatkozó engedélyeket adjon a felhasználónak:
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az keyvault set-policy --upn <email-address-of-user> --name vaultrotation-kv --secret-permissions set delete get list
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Set-AzKeyVaultAccessPolicy -UserPrincipalName <email-address-of-user> --name vaultrotation-kv -PermissionsToSecrets set,delete,get,list
```
---

Most már létrehozhat egy új titkos kulcsot egy tárfiók hozzáférési kulcsával értékként. Szüksége lesz a tárfiók erőforrás-azonosítójára, a titkos kód érvényességi időtartamra és a kulcsazonosítóra is a titkos kód hozzáadásához, hogy a rotációs függvény újragenerálhatja a kulcsot a tárfiókban.

Határozza meg a tárfiók erőforrás-azonosítóját. Ezt az értéket a tulajdonságban `id` találhatja meg.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az storage account show -n vaultrotationstorage
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccount -Name vaultrotationstorage -ResourceGroupName vaultrotation | Select-Object -Property *
```
---

Sorolja fel a tárfiók hozzáférési kulcsait, hogy le tudja szerezni a kulcsértékeket:
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az storage account keys list -n vaultrotationstorage
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccountKey -Name vaultrotationstorage -ResourceGroupName vaultrotation
```
---

Titkos kód hozzáadása a Key Vaulthoz a lejárati dátum holnapra, 60 napos érvényességi időtartam és tárfiók-erőforrás-azonosító. Futtassa ezt a parancsot a és a lekért `key1Value` `storageAccountResourceId` értékeivel:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddTHH:mm:ssZ")
az keyvault secret set --name storageKey --vault-name vaultrotation-kv --value <key1Value> --tags "CredentialId=key1" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
$tomorrowDate = (Get-Date).AddDays(+1).ToString('yyy-MM-ddTHH:mm:ssZ')
$secretVaule = ConvertTo-SecureString -String '<key1Value>' -AsPlainText -Force
$tags = @{
    CredentialId='key1'
    ProviderAddress='<storageAccountResourceId>'
    ValidityPeriodDays='60'
}
Set-AzKeyVaultSecret -Name storageKey -VaultName vaultrotation-kv -SecretValue $secretVaule -Tag $tags -Expires $tomorrowDate
```
---

A fenti titkos okból néhány `SecretNearExpiry` percen belül aktiválódik az esemény. Ez az esemény aktiválja a függvényt a titkos kulcs 60 napos lejárati dátummal való váltogatása érdekében. Ebben a konfigurációban a SecretNearExpiry esemény 30 naponta (a lejárat előtt 30 nappal) aktiválódik, és a rotációs függvény váltogatja a kulcs1 és a kulcs2 közötti rotációt.

A tárfiókkulcs és a titkos kulcs leolvasása és a titkos kulcs Key Vault ellenőrizheti a hozzáférési kulcsok újragenerálását, és összehasonlíthatja őket.

Ezzel a paranccsal lekérte a titkos adatokat:
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az keyvault secret show --vault-name vaultrotation-kv --name storageKey
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzKeyVaultSecret -VaultName vaultrotation-kv -Name storageKey -AsPlainText
```
---

Figyelje `CredentialId` meg, hogy a a másikra frissül, `keyName` és újra létre lesz `value` hozva:

![Képernyőkép az első tárfiók z keyvault secret show parancsának kimenetével.](../media/secrets/rotation-dual/dual-rotation-4.png)

A hozzáférési kulcsok lekérése az értékek összehasonlításhoz:
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az storage account keys list -n vaultrotationstorage 
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccountKey -Name vaultrotationstorage -ResourceGroupName vaultrotation
```
---

Figyelje meg, hogy a kulcs `value` ugyanaz, mint a Key Vaultban tárolt titkos kulcs:

![Képernyőkép az első tárfiók z tárfiókkulcslista parancsának kimenetével.](../media/secrets/rotation-dual/dual-rotation-5.png)

## <a name="add-storage-accounts-for-rotation"></a>Tárfiókok hozzáadása rotációhoz

Ugyanazt a függvényalkalmazást újra felhasználhatja több tárfiók kulcsának váltogatatása érdekében. 

Ha tárfiókkulcsokat szeretne hozzáadni egy meglévő függvényhez a rotációhoz, a következőre lesz szüksége:
- A függvényalkalmazáshoz rendelt Tárfiókkulcs-kezelői szolgáltatás szerepkör, amely hozzáfér a tárfiók hozzáférési kulcshoz.
- Egy Event Grid a **SecretNearExpiry eseményre.**

1. Válassza az Azure-sablon üzembe helyezésére mutató hivatkozást: 

   [![Azure-sablon üzembe helyezési hivatkozása.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2FARM-Templates%2FAdd-Event-Subscriptions%2Fazuredeploy.json)

1. Az **Erőforráscsoport listában** válassza a **vaultrotation lehetőséget.**
1. A **Tárfiók erőforráscsoportja** mezőben adja meg annak az erőforráscsoportnak a nevét, amelyben a tárfiók található. Tartsa meg az **alapértelmezett [resourceGroup().name]** értéket, ha a tárfiók már ugyanabban az erőforráscsoportban található, ahol a kulcsrotációs függvényt üzembe fogja helyezni.
1. A **Tárfiók neve mezőben** adja meg annak a tárfióknak a nevét, amely a váltogatni kívánt hozzáférési kulcsokat tartalmazza.
1. Az **Key Vault RG mezőbe** írja be annak az erőforráscsoportnak a nevét, amelyben a kulcstartó található. Tartsa meg az **alapértelmezett [resourceGroup().name]** értéket, ha a kulcstartó már létezik ugyanabban az erőforráscsoportban, ahol a kulcsrotációs függvényt üzembe fogja helyezni.
1. A **Key Vault név mezőbe** írja be a kulcstartó nevét.
1. A **Függvényalkalmazás neve mezőben** adja meg a függvényalkalmazás nevét.
1. A Titkos **kulcs neve mezőbe** írja be annak a titkos kulcsnak a nevét, amelyben a hozzáférési kulcsokat tárolni fogja.
1. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.
1. Válassza a **Létrehozás** lehetőséget.

   ![Képernyőkép egy további tárfiók létrehozásáról.](../media/secrets/rotation-dual/dual-rotation-7.png)

### <a name="add-another-storage-account-access-key-to-key-vault"></a>Adjon hozzá egy másik tárfiók-hozzáférési kulcsot a Key Vault

Határozza meg a tárfiók erőforrás-azonosítóját. Ezt az értéket a tulajdonságban `id` találhatja meg.
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az storage account show -n vaultrotationstorage2
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccount -Name vaultrotationstorage -ResourceGroupName vaultrotation | Select-Object -Property *
```
---

Sorolja fel a tárfiók hozzáférési kulcsait, hogy le tudja szerezni a key2 értéket:
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az storage account keys list -n vaultrotationstorage2
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccountKey -Name vaultrotationstorage2 -ResourceGroupName vaultrotation
```
---

Titkos kód hozzáadása a Key Vaulthoz a lejárati dátum holnapra, 60 napos érvényességi időtartam és tárfiók-erőforrás-azonosító. Futtassa ezt a parancsot a és a lekért `key2Value` `storageAccountResourceId` értékeivel:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
$tomorrowDate = (Get-Date).AddDays(+1).ToString('yyy-MM-ddTHH:mm:ssZ')
az keyvault secret set --name storageKey2 --vault-name vaultrotation-kv --value <key2Value> --tags "CredentialId=key2" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddTHH:mm:ssZ")
$secretVaule = ConvertTo-SecureString -String '<key1Value>' -AsPlainText -Force
$tags = @{
    CredentialId='key2';
    ProviderAddress='<storageAccountResourceId>';
    ValidityPeriodDays='60'
}
Set-AzKeyVaultSecret -Name storageKey2 -VaultName vaultrotation-kv -SecretValue $secretVaule -Tag $tags -Expires $tomorrowDate
```
---

Ezzel a paranccsal lekérte a titkos adatokat:
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az keyvault secret show --vault-name vaultrotation-kv --name storageKey2
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzKeyVaultSecret -VaultName vaultrotation-kv -Name storageKey2 -AsPlainText
```
---

Figyelje `CredentialId` meg, hogy a frissült a másikra, `keyName` és újra létre lesz `value` hozva:

![Képernyőkép a második tárfiók z keyvault secret show parancsának kimenetével.](../media/secrets/rotation-dual/dual-rotation-8.png)

Az értékek összehasonlítására lekérheti a hozzáférési kulcsokat:
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az storage account keys list -n vaultrotationstorage 
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccountKey -Name vaultrotationstorage -ResourceGroupName vaultrotation
```
---

Figyelje `value` meg, hogy a kulcs ugyanaz, mint a key vaultban tárolt titkos kulcs:

![Képernyőkép a második tárfiók z tárfiókkulcslista parancsának kimenetével.](../media/secrets/rotation-dual/dual-rotation-9.png)

## <a name="key-vault-rotation-functions-for-two-sets-of-credentials"></a>Key Vault két hitelesítőadat-készlet rotációs függvényei

Rotációs függvénysablon két hitelesítőadat-készlethez és több használatra kész függvényhez:

- [Projektsablon](https://serverlesslibrary.net/sample/bc72c6c3-bd8f-4b08-89fb-c5720c1f997f)
- [Redis Cache](https://serverlesslibrary.net/sample/0d42ac45-3db2-4383-86d7-3b92d09bc978)
- [Tárfiók](https://serverlesslibrary.net/sample/0e4e6618-a96e-4026-9e3a-74b8412213a4)
- [Cosmos DB](https://serverlesslibrary.net/sample/bcfaee79-4ced-4a5c-969b-0cc3997f47cc)

> [!NOTE]
> A fenti rotációs funkciókat nem a Microsoft, csak a közösség tagja hozta létre. A Azure Functions a Microsoft semmilyen támogatási programja vagy szolgáltatása nem támogatja, és jelen esetben, bármilyen jótállás nélkül elérhetővé válik.

## <a name="next-steps"></a>Következő lépések

- Oktatóanyag: [Egyetlen hitelesítőadat-készlet titkos kulcsok rotációja](./tutorial-rotation.md)
- Áttekintés: [A Key Vault monitorozása Azure Event Grid](../general/event-grid-overview.md)
- How to: [Create your first function in the Azure Portal](../../azure-functions/functions-get-started.md)
- How to: [Receive email when a Key Vault secret changes](../general/event-grid-logicapps.md)
- Referencia: [Azure Event Grid eseménysémát a Azure Key Vault](../../event-grid/event-schema-key-vault.md)
