---
title: Az alkalmazás forrásának titkosítása
description: Titkosítsa az alkalmazásadatokat az Azure Storage-ban, és telepítse csomagfájlként.
ms.topic: article
ms.date: 03/06/2020
ms.openlocfilehash: 71668bf27628bb2af2dfc7112d28ba10ecfdf9f3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768812"
---
# <a name="encrypt-your-application-data-at-rest-using-customer-managed-keys"></a>Az alkalmazásadatok titkosítása az ügyfelek által kezelt kulcsokkal

A függvényalkalmazás használaton belüli alkalmazásadatának titkosításához Azure Storage-fiókra és Azure Key Vault. Ezek a szolgáltatások akkor használatosak, ha az alkalmazást egy központi telepítési csomagból futtatja.

  - [Az Azure Storage titkosítást biztosít az adatok tárolásához.](../storage/common/storage-service-encryption.md) Használhat rendszer által biztosított kulcsokat vagy saját, ügyfél által kezelt kulcsokat. Az alkalmazásadatok itt vannak tárolva, ha nem az Azure-beli függvényalkalmazásban futnak.
  - [A központi telepítési csomagból való](run-functions-from-deployment-package.md) futtatás a központi telepítési App Service. Lehetővé teszi a webhely tartalmának üzembe helyezését egy Azure Storage-fiókból egy közös hozzáférésű jogosultság signature (SAS) URL-cím használatával.
  - [Key Vault hivatkozások](../app-service/app-service-key-vault-references.md) az adatok biztonsági App Service. Lehetővé teszi a titkos kulcsok futásidőben történő importálását alkalmazásbeállításokként. Ezzel titkosíthatja az Azure Storage-fiók SAS URL-címét.

## <a name="set-up-encryption-at-rest"></a>Titkosítás beállítása az adattitkosításhoz

### <a name="create-an-azure-storage-account"></a>Azure Storage-fiók létrehozása

Először [hozzon létre egy Azure Storage-fiókot,](../storage/common/storage-account-create.md) [és titkosítsa azt ügyfél által kezelt kulcsokkal.](../storage/common/customer-managed-keys-overview.md) A tárfiók létrehozása után használja a Azure Storage Explorer [a](../vs-azure-tools-storage-manage-with-storage-explorer.md) csomagfájlok feltöltéséhez.

A következő lépés az Storage Explorer [SAS létrehozása.](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#generate-a-sas-in-storage-explorer) 

> [!NOTE]
> Mentse ezt az SAS URL-címet, mert később ezt használjuk majd a telepítési csomag biztonságos elérésének engedélyezéséhez futásidőben.

### <a name="configure-running-from-a-package-from-your-storage-account"></a>Futtatás konfigurálása egy csomagból a tárfiókból
  
Miután feltöltötte a fájlt a Blob Storage-be, és már van SAS URL-címe a fájlhoz, állítsa az alkalmazásbeállítást a `WEBSITE_RUN_FROM_PACKAGE` SAS URL-címre. Az alábbi példa ezt az Azure CLI használatával teszi meg:

```
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="<your-SAS-URL>"
```

Ha hozzáadja ezt az alkalmazásbeállítást, a függvényalkalmazás újraindul. Az alkalmazás újraindítása után keresse meg, és ellenőrizze, hogy az alkalmazás megfelelően elindult-e a telepítőcsomaggal. Ha az alkalmazás nem indul el megfelelően, tekintse meg a Futtatás [csomagból hibaelhárítási útmutatót.](run-functions-from-deployment-package.md#troubleshooting)

### <a name="encrypt-the-application-setting-using-key-vault-references"></a>Az alkalmazásbeállítás titkosítása a Key Vault használatával

Most lecserélheti az alkalmazásbeállítás értékét egy Key Vault `WEBSITE_RUN_FROM_PACKAGE` SAS-kódolású URL-címre mutató hivatkozásra. Ez titkosítva tartja az SAS URL-Key Vault, ami egy további biztonsági réteget biztosít.

1. Hozzon [`az keyvault create`](/cli/azure/keyvault#az_keyvault_create) létre egy új példányt a Key Vault paranccsal.       

    ```azurecli    
    az keyvault create --name "Contoso-Vault" --resource-group <group-name> --location eastus    
    ```    

1. Kövesse [az alábbi utasításokat, hogy hozzáférést biztosítson az alkalmazásnak](../app-service/app-service-key-vault-references.md#granting-your-app-access-to-key-vault) a kulcstartóhoz:

1. A következő [`az keyvault secret set`](/cli/azure/keyvault/secret#az_keyvault_secret_set) paranccsal adja hozzá a külső URL-címet titkos kódként a kulcstartóhoz:   

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ```    

1.  A következő paranccsal hozza létre az alkalmazásbeállítást a [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) `WEBSITE_RUN_FROM_PACKAGE` értékkel a Key Vault URL-címre mutató hivatkozásként:

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    A `<secret-version>` az előző parancs kimenetében `az keyvault secret set` lesz.

Ha frissíti ezt az alkalmazásbeállítást, a függvényalkalmazás újraindul. Az alkalmazás újraindítása után tallózással ellenőrizze, hogy megfelelően elindult-e az Key Vault használatával.

## <a name="how-to-rotate-the-access-token"></a>A hozzáférési jogkivonat elforgatása

A tárfiók SAS-kulcsának rendszeres időközönkénti váltogatása ajánlott. Annak érdekében, hogy a függvényalkalmazás véletlenül ne lazán elérést biztosítson, frissítenie kell az SAS URL-címet a Key Vault.

1. Az SAS-kulcs elforgatása a tárfiókra való navigálással a Azure Portal. A **Beállítások hozzáférési** kulcsok  >  **alatt** kattintson a ikonra az SAS-kulcs váltogatni kívánt módosításához.

1. Másolja ki az új SAS URL-címet, és a következő paranccsal állítsa be a frissített SAS URL-címet a kulcstartóban:

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ``` 

1. Frissítse a Key Vault-referenciát az alkalmazásbeállításban az új titkos kulcsverzióra:

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    A `<secret-version>` az előző parancs kimenetében `az keyvault secret set` lesz.

## <a name="how-to-revoke-the-function-apps-data-access"></a>A függvényalkalmazás adatelérésének visszavonása

A függvényalkalmazás tárfiókhoz való hozzáférésének visszavonására két módszer létezik. 

### <a name="rotate-the-sas-key-for-the-azure-storage-account"></a>Az Azure Storage-fiók SAS-kulcsának elforgatása

Ha a tárfiók SAS-kulcsa le lesz forgatva, a függvényalkalmazás többé nem fog hozzáférni a tárfiókhoz, de továbbra is a csomagfájl utolsó letöltött verziójával fog futni. Indítsa újra a függvényalkalmazást a legutóbb letöltött verzió ürítéhez.

### <a name="remove-the-function-apps-access-to-key-vault"></a>A függvényalkalmazás hozzáférésének eltávolítása Key Vault

A függvényalkalmazás webhelyadatokhoz való hozzáférésének visszavonásához tiltsa le a függvényalkalmazás hozzáférését a Key Vault. Ehhez távolítsa el a függvényalkalmazás identitásának hozzáférési szabályzatát. Ez ugyanaz az identitás, mint amit korábban a Key Vault-hivatkozások konfigurálásakor hozott létre.

## <a name="summary"></a>Összefoglalás

Az alkalmazásfájlok mostantól titkosítva vannak a tárfiókban. Amikor a függvényalkalmazás elindul, lekéri az SAS URL-címet a kulcstartóból. Végül a függvényalkalmazás betölti az alkalmazásfájlokat a tárfiókból. 

Ha vissza kell vonnia a függvényalkalmazás tárfiókhoz való hozzáférését, visszavonhatja a kulcstartóhoz való hozzáférést, vagy elforgathatja a tárfiók kulcsait, ami érvényteleníti az SAS URL-címet.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="is-there-any-additional-charge-for-running-my-function-app-from-the-deployment-package"></a>Van további díj a függvényalkalmazásomnak az üzembe helyezési csomagból való futtatásáért?

Csak az Azure Storage-fiókhoz kapcsolódó költségeket és a vonatkozó bejövő forgalom díját.

### <a name="how-does-running-from-the-deployment-package-affect-my-function-app"></a>Hogyan befolyásolja a függvényalkalmazásom az üzembe helyezési csomagból való futtatás?

- Ha az alkalmazást a központi telepítési csomagból futtatja, `wwwroot/` az csak olvasható lesz. Az alkalmazás hibaüzenetet kap, amikor ebbe a könyvtárba próbál írni.
- A TAR- és GZIP-formátumok nem támogatottak.
- Ez a funkció nem kompatibilis a helyi gyorsítótárral.

## <a name="next-steps"></a>Következő lépések

- [Key Vault hivatkozások a App Service](../app-service/app-service-key-vault-references.md)
- [Inaktív adatok Azure Storage-titkosítása](../storage/common/storage-service-encryption.md)