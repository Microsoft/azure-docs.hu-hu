---
title: Az alkalmazás forrásának titkosítása
description: Megtudhatja, hogyan titkosíthatja az alkalmazásadatokat az Azure Storage-ban, és hogyan helyezheti üzembe csomagfájlként.
ms.topic: article
ms.date: 03/06/2020
ms.openlocfilehash: a26660723fbe96a9b765401af1f0c9cfc80dbc3f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779433"
---
# <a name="encryption-at-rest-using-customer-managed-keys"></a>Titkosítás az ügyfelek által kezelt kulcsokkal

A webalkalmazás használaton belüli alkalmazásadatának titkosításához Egy Azure Storage-fiókra és egy Azure Key Vault. Ezeket a szolgáltatásokat akkor használja a rendszer, amikor egy központi telepítési csomagból futtatja az alkalmazást.

  - [Az Azure Storage titkosítást biztosít az azure-beli használatban.](../storage/common/storage-service-encryption.md) Használhat rendszer által biztosított kulcsokat vagy saját, ügyfél által kezelt kulcsokat. Itt tárolja a rendszer az alkalmazásadatokat, ha nem az Azure-beli webalkalmazásban futnak.
  - [A központi telepítési csomagból való](deploy-run-package.md) futtatás a központi telepítési App Service. Lehetővé teszi a webhely tartalmának üzembe helyezését egy Azure Storage-fiókból egy közös hozzáférésű jogosultság jogosultsága (SAS) URL-címével.
  - [Key Vault hivatkozások](app-service-key-vault-references.md) az adatok biztonsági App Service. Lehetővé teszi a titkos kulcsok futásidőben történő importálását alkalmazásbeállításokként. Ezzel titkosíthatja az Azure Storage-fiók SAS URL-címét.

## <a name="set-up-encryption-at-rest"></a>Az adattitkosítás beállítása

### <a name="create-an-azure-storage-account"></a>Azure Storage-fiók létrehozása

Először [hozzon létre egy Azure Storage-fiókot,](../storage/common/storage-account-create.md) és titkosítsa azt ügyfél által [kezelt kulcsokkal.](../storage/common/customer-managed-keys-overview.md) A tárfiók létrehozása után használja a Azure Storage Explorer [a](../vs-azure-tools-storage-manage-with-storage-explorer.md) csomagfájlok feltöltéséhez.

Következő lépésként használja a Storage Explorer [SAS létrehozásához.](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#generate-a-sas-in-storage-explorer) 

> [!NOTE]
> Mentse ezt az SAS URL-címet, mert később ezt használjuk majd a központi telepítési csomag biztonságos elérésének engedélyezéséhez futásidőben.

### <a name="configure-running-from-a-package-from-your-storage-account"></a>Futtatás konfigurálása a tárfiókból származó csomagból
  
Miután feltöltötte a fájlt a Blob Storage-be, és sas URL-címe van a fájlhoz, állítsa az alkalmazásbeállítást `WEBSITE_RUN_FROM_PACKAGE` a SAS URL-címre. Az alábbi példa ezt az Azure CLI használatával teszi meg:

```
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="<your-SAS-URL>"
```

Ha hozzáadja ezt az alkalmazásbeállítást, a webalkalmazás újraindul. Az alkalmazás újraindítása után keresse meg, és ellenőrizze, hogy az alkalmazás megfelelően elindult-e a telepítőcsomaggal. Ha az alkalmazás nem indul el megfelelően, tekintse meg a Futtatás [csomagból hibaelhárítási útmutatót.](deploy-run-package.md#troubleshooting)

### <a name="encrypt-the-application-setting-using-key-vault-references"></a>Az alkalmazásbeállítás titkosítása Key Vault használatával

Most lecserélheti az alkalmazásbeállítás értékét egy olyan Key Vault `WEBSITE_RUN_FROM_PACKAGE` SAS-kódolású URL-címre mutató hivatkozásra. Ez titkosítva tartja az SAS URL-Key Vault, ami egy további biztonsági réteget biztosít.

1. Hozzon [`az keyvault create`](/cli/azure/keyvault#az_keyvault_create) létre egy új példányt a Key Vault paranccsal.       

    ```azurecli    
    az keyvault create --name "Contoso-Vault" --resource-group <group-name> --location eastus    
    ```    

1. Kövesse [az alábbi utasításokat, hogy hozzáférést biztosítson az alkalmazásnak a](app-service-key-vault-references.md#granting-your-app-access-to-key-vault) kulcstartóhoz:

1. A következő [`az keyvault secret set`](/cli/azure/keyvault/secret#az_keyvault_secret_set) paranccsal adja hozzá a külső URL-címet titkos kódként a kulcstartóhoz:   

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ```    

1.  A következő paranccsal hozza létre az alkalmazásbeállítást a értékkel [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) a Key Vault URL-címre mutató `WEBSITE_RUN_FROM_PACKAGE` hivatkozásként:

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    A `<secret-version>` az előző parancs kimenetében `az keyvault secret set` lesz.

Ha frissíti ezt az alkalmazásbeállítást, a webalkalmazás újraindul. Az alkalmazás újraindítása után tallózással ellenőrizze, hogy megfelelően elindult-e az Key Vault használatával.

## <a name="how-to-rotate-the-access-token"></a>A hozzáférési jogkivonat elforgatása

Az ajánlott eljárás a tárfiók SAS-kulcsának rendszeres időközönkénti váltogatása. Annak érdekében, hogy a webalkalmazás ne legyen véletlenül lazán elérhető, frissítenie kell az SAS URL-címet a Key Vault.

1. Az SAS-kulcs elforgatása a tárfiókra való navigálással a Azure Portal. A **Beállítások hozzáférési**  >  **kulcsai alatt** kattintson az ikonra az SAS-kulcs váltogatni kívánt ikonra.

1. Másolja ki az új SAS URL-címet, és a következő paranccsal állítsa be a frissített SAS URL-címet a kulcstartóban:

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ``` 

1. Frissítse a Key Vault-referenciát az alkalmazásbeállításban az új titkos kulcsverzióra:

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    A `<secret-version>` az előző parancs kimenetében `az keyvault secret set` lesz.

## <a name="how-to-revoke-the-web-apps-data-access"></a>A webalkalmazás adatelérésének visszavonása

A webalkalmazás tárfiókhoz való hozzáférését kétféle módon vonhatja vissza. 

### <a name="rotate-the-sas-key-for-the-azure-storage-account"></a>Az Azure Storage-fiók SAS-kulcsának forgása

Ha a tárfiók SAS-kulcsa le lesz forgatva, a webalkalmazás többé nem fog hozzáférni a tárfiókhoz, de továbbra is a csomagfájl utolsó letöltött verziójával fog futni. Indítsa újra a webalkalmazást a legutóbb letöltött verzió ürítéhez.

### <a name="remove-the-web-apps-access-to-key-vault"></a>Távolítsa el a webalkalmazás hozzáférését a Key Vault

A webalkalmazás webhelyadatokhoz való hozzáférését úgy vonhatja vissza, hogy letiltja a webalkalmazás hozzáférését a Key Vault. Ehhez távolítsa el a webalkalmazás identitásának hozzáférési szabályzatát. Ez ugyanaz az identitás, mint amit korábban a Key Vault-hivatkozások konfigurálásakor hozott létre.

## <a name="summary"></a>Összefoglalás

Az alkalmazásfájlok most már titkosítva vannak a tárfiókban. Amikor a webalkalmazás elindul, lekéri az SAS URL-címet a kulcstartóból. Végül a webalkalmazás betölti az alkalmazásfájlokat a tárfiókból. 

Ha vissza kell vonnia a webalkalmazás tárfiókhoz való hozzáférését, visszavonhatja a kulcstartóhoz való hozzáférést, vagy elforgathatja a tárfiók kulcsait, ami érvényteleníti az SAS URL-címet.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="is-there-any-additional-charge-for-running-my-web-app-from-the-deployment-package"></a>Van további díj a webalkalmazásom üzembe helyezési csomagból való futtatásáért?

Csak az Azure Storage-fiókhoz kapcsolódó költségeket és a vonatkozó bejövő forgalom díját.

### <a name="how-does-running-from-the-deployment-package-affect-my-web-app"></a>Hogyan befolyásolja a webalkalmazásom az üzembe helyezési csomagból való futtatás?

- Az alkalmazás központi telepítési csomagból való futtatása csak `wwwroot/` olvasható. Az alkalmazás hibaüzenetet kap, amikor ebbe a könyvtárba próbál írni.
- A TAR- és GZIP-formátumok nem támogatottak.
- Ez a funkció nem kompatibilis a helyi gyorsítótárral.

## <a name="next-steps"></a>Következő lépések

- [Key Vault referencia a App Service](app-service-key-vault-references.md)
- [Inaktív adatok Azure Storage-titkosítása](../storage/common/storage-service-encryption.md)