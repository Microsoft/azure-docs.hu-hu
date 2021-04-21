---
title: Oktatóanyag a Azure App Configuration Key Vault java-alkalmazásokban való Spring Boot való | Microsoft Docs
description: Ez az oktatóanyag bemutatja, hogyan használhatja Azure App Configuration Java Key Vault java-Spring Boot referenciáit
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 08/11/2020
ms.author: alkemper
ms.custom: mvc, devx-track-java, devx-track-azurecli
ms.openlocfilehash: 7c5534ab836968bc4e72a54db1ddb9667d366558
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768848"
---
# <a name="tutorial-use-key-vault-references-in-a-java-spring-app"></a>Oktatóanyag: Key Vault referenciák használata Java Spring-alkalmazásokban

Ez az oktatóanyag bemutatja, hogyan használhatja a Azure App Configuration szolgáltatást a Azure Key Vault. App Configuration és Key Vault a legtöbb alkalmazástelepítésben egymás mellett használt kiegészítő szolgáltatások.

App Configuration segítségével együtt használhatja a szolgáltatásokat a szolgáltatásokban tárolt értékekre hivatkozó kulcsok létrehozásával Key Vault. Amikor App Configuration ilyen kulcsokat hoz létre, a saját értékek helyett Key Vault URI-ját tárolja.

Az alkalmazás a App Configuration ügyfélszolgáltató használatával lekéri a Key Vault-hivatkozásokat, csakúgy, mint a App Configuration. Ebben az esetben a tárolóban tárolt App Configuration olyan URI-k, amelyek a Key Vault. Nem értékeket Key Vault hitelesítő adatokat. Mivel az ügyfélszolgáltató hivatkozásként ismeri fel Key Vault kulcsokat, a Key Vault lekéri az értékeket.

Az alkalmazás felelős a megfelelő hitelesítésért a App Configuration és Key Vault. A két szolgáltatás nem kommunikál közvetlenül.

Ez az oktatóanyag bemutatja, hogyan implementálja Key Vault referenciáit a kódban. A rövid útmutatókban bemutatott webalkalmazásra épül. A folytatás előtt először a [Create a Java Spring app with App Configuration (Java Spring-alkalmazás](./quickstart-java-spring-app.md) létrehozása a App Configuration lépésekkel.

Az oktatóanyagban található lépéseket bármilyen kódszerkesztővel el is használhatja. A [Visual Studio Code](https://code.visualstudio.com/) például egy platformfüggetlen kódszerkesztő, amely Windows, macOS és Linux operációs rendszerekhez érhető el.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzon létre egy App Configuration kulcsot, amely a tárolóban tárolt Key Vault.
> * Hozzáférés a kulcs értékhez egy Java Spring-alkalmazásból.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés [– hozzon létre egyet ingyenesen](https://azure.microsoft.com/free/)
* Egy támogatott [Java fejlesztői készlet (JDK)](/java/azure/jdk) 8-as verzióval.
* [Az Apache Maven](https://maven.apache.org/download.cgi) 3.0-s vagy újabb verziója.

## <a name="create-a-vault"></a>Tároló létrehozása

1. Válassza **az Erőforrás létrehozása** lehetőséget a bal felső sarokban a Azure Portal:

    ![Képernyőkép az Erőforrás létrehozása lehetőségről a Azure Portal.](./media/quickstarts/search-services.png)
1. A keresőmezőbe írja be a **Key Vault** nevet.
1. Az eredmények listájában válassza a **bal oldalon található Kulcstartók** lehetőséget.
1. A **Key Vaultsban válassza** a Hozzáadás **lehetőséget.**
1. A Kulcstartó létrehozása jobb **oldalon** adja meg a következő információkat:
    * Válassza **az Előfizetés** lehetőséget az előfizetés kiválasztásához.
    * Az **Erőforráscsoport mezőben** válassza az **Új létrehozása lehetőséget,** és adjon meg egy erőforráscsoport-nevet.
    * A **Kulcstartó neve alatt** meg kell adni egy egyedi nevet. Ebben az oktatóanyagban adja meg a **Contoso-vault2 adhatja meg** a következőt: .
    * A Régió **legördülő** listában válasszon egy helyet.
1. Hagyja meg a **többi Kulcstartó létrehozása beállítást** az alapértelmezett értékekkel.
1. Válassza a **Létrehozás** lehetőséget.

Ezen a ponton az Ön Azure-fiókja az egyetlen, aki jogosult hozzáférni ehhez az új tárolóhoz.

![Képernyőkép a kulcstartóról.](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Titkos kulcs hozzáadása a Key Vaulthoz

Ha titkos gombra van szüksége a tárolóhoz való hozzáadáshoz, néhány további lépést is végre kell tartania. Ebben az esetben adjon hozzá egy üzenetet, amely a lekérés tesztelésére Key Vault használható. Az üzenet neve **Message**,, és a "Hello from Key Vault" értéket tárolja benne.

1. A tulajdonságok Key Vault oldalán válassza a Titkos **kulcsok lehetőséget.**
1. Válassza **a Generate/Import (Generálás/Importálás) lehetőséget.**
1. A Titkos **adat létrehozása panelen** adja meg a következő értékeket:
    * **Feltöltési beállítások:** Adja meg a **Manuális lehetőséget.**
    * **Név:** Írja be a **következő üzenetet:**.
    * **Érték:** Írja be a **Hello Key Vault.**
1. A többi **Titkos adat létrehozása tulajdonságot** hagyja meg az alapértelmezett értékekkel.
1. Válassza a **Létrehozás** lehetőséget.

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Hivatkozás Key Vault a App Configuration

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza **a Minden erőforrás** lehetőséget, majd App Configuration a rövid útmutatóban létrehozott tárolópéldányt.

1. Válassza **a Configuration Explorer lehetőséget.**

1. Válassza **a +**  >  **Kulcstartó-referencia létrehozása lehetőséget,** majd adja meg a következő értékeket:
    * **Kulcs:** Válassza **az /application/config.keyvaultmessage lehetőséget**
    * **Címke:** Ezt az értéket hagyja üresen.
    * **Előfizetés,** **Erőforráscsoport** és **Kulcstartó:** Adja meg az előző szakaszban létrehozott kulcstartóban található értékeknek megfelelő értékeket.
    * **Titkos:** Válassza ki az előző szakaszban létrehozott **Message** (Üzenet) nevű titkos secret (Titkos üzenet) lehetőséget.

## <a name="connect-to-key-vault"></a>Csatlakozás Key Vault

1. Ebben az oktatóanyagban szolgáltatásnévvel fog hitelesítést használni a Key Vault. A szolgáltatásnév létrehozásához használja az Azure CLI [az ad sp create-for-rbac parancsot:](/cli/azure/ad/sp#az_ad_sp_create_for_rbac)

    ```azurecli
    az ad sp create-for-rbac -n "http://mySP" --sdk-auth
    ```

    Ez a művelet kulcs/érték párok sorozatát adja vissza:

    ```json
    {
    "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
    "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
    "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
    "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
    }
    ```

1. Futtassa a következő parancsot, hogy a szolgáltatásnév hozzáfér a kulcstartóhoz:

    ```azurecli
    az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get
    ```

1. Futtassa a következő parancsot az objektumazonosító lekért futtatásához, majd adja hozzá App Configuration.

    ```azurecli
    az ad sp show --id <clientId-of-your-service-principal>
    az role assignment create --role "App Configuration Data Reader" --assignee-object-id <objectId-of-your-service-principal> --resource-group <your-resource-group>
    ```

1. Hozza létre a környezeti változókat **AZURE_CLIENT_ID**, **AZURE_CLIENT_SECRET** és **AZURE_TENANT_ID.** Használja a szolgáltatásnév előző lépésekben megjelenő értékeit. A parancssorban futtassa a következő parancsokat, és indítsa újra a parancssort, hogy a módosítás érvénybe léptetőd érvénybe lép:

    ```cmd
    setx AZURE_CLIENT_ID "clientId"
    setx AZURE_CLIENT_SECRET "clientSecret"
    setx AZURE_TENANT_ID "tenantId"
    ```

    Ha a parancsot Windows PowerShell, futtassa a következő parancsot:

    ```azurepowershell
    $Env:AZURE_CLIENT_ID = "clientId"
    $Env:AZURE_CLIENT_SECRET = "clientSecret"
    $Env:AZURE_TENANT_ID = "tenantId"
    ```

    MacOS vagy Linux rendszeren futtassa a következő parancsot:

    ```cmd
    export AZURE_CLIENT_ID ='clientId'
    export AZURE_CLIENT_SECRET ='clientSecret'
    export AZURE_TENANT_ID ='tenantId'
    ```


> [!NOTE]
> Ezek Key Vault hitelesítő adatokat csak az alkalmazáson belül használja.  Az alkalmazás ezekkel a hitelesítő Key Vault hitelesítéssel közvetlenül, a App Configuration nélkül.  A Key Vault hitelesítést biztosít az alkalmazáshoz és a App Configuration szolgáltatáshoz is, kulcsok megosztása vagy felfedése nélkül.

## <a name="update-your-code-to-use-a-key-vault-reference"></a>Kód frissítése új referencia Key Vault használatára

1. Hozzon létre egy nevű környezeti **APP_CONFIGURATION_ENDPOINT.** Állítsa be az értékét a saját App Configuration végpontjára. A végpontot a hozzáférési kulcsok **panelen** találja a Azure Portal. Indítsa újra a parancssort, hogy a módosítás érvénybe lép. 


1. Nyissa *meg a bootstrap.properties* fájlt a *resources mappában.* Frissítse ezt a fájlt a APP_CONFIGURATION_ENDPOINT **használatára.** Távolítsa el a fájlban található kapcsolati sztringre mutató hivatkozásokat. 

    ```properties
    spring.cloud.azure.appconfiguration.stores[0].endpoint= ${APP_CONFIGURATION_ENDPOINT}
    ```

1. Nyissa *meg a MessageProperties.java-t.* Adjon hozzá egy *új, keyVaultMessage nevű változót:*

    ```java
    private String keyVaultMessage;

    public String getKeyVaultMessage() {
        return keyVaultMessage;
    }

    public void setKeyVaultMessage(String keyVaultMessage) {
        this.keyVaultMessage = keyVaultMessage;
    }
    ```

1. Nyissa meg *a HelloController.java fájlját.* Frissítse a *getMessage* metódust, hogy tartalmazza a lekérdezésből lekért Key Vault.

    ```java
    @GetMapping
    public String getMessage() {
        return "Message: " + properties.getMessage() + "\nKey Vault message: " + properties.getKeyVaultMessage();
    }
    ```

1. Hozzon létre egy *új fájlt AzureCredentials.java* néven, és adja hozzá az alábbi kódot.

    ```java
    package com.example.demo;

    import com.azure.core.credential.TokenCredential;
    import com.azure.identity.EnvironmentCredentialBuilder;
    import com.microsoft.azure.spring.cloud.config.AppConfigurationCredentialProvider;
    import com.microsoft.azure.spring.cloud.config.KeyVaultCredentialProvider;

    public class AzureCredentials implements AppConfigurationCredentialProvider, KeyVaultCredentialProvider{

        @Override
        public TokenCredential getKeyVaultCredential(String uri) {
            return getCredential();
        }

        @Override
        public TokenCredential getAppConfigCredential(String uri) {
            return getCredential();
        }

        private TokenCredential getCredential() {
            return new EnvironmentCredentialBuilder().build();
        }

    }
    ```

1. Hozzon létre egy új fájlt *AppConfiguration.java néven.* Adja hozzá az alábbi kódot.

    ```java
    package com.example.demo;

    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.Configuration;

    @Configuration
    public class AppConfiguration {

        @Bean
        public AzureCredentials azureCredentials() {
            return new AzureCredentials();
        }
    }
    ```

1. Hozzon létre egy *spring.factories* nevű új fájlt az erőforrások META-INF könyvtárában, és adja hozzá az alábbi kódot.

    ```factories
    org.springframework.cloud.bootstrap.BootstrapConfiguration=\
    com.example.demo.AppConfiguration
    ```

1. Készítse el Spring Boot alkalmazást a Maven segítségével, és futtassa, például:

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

1. Az alkalmazás futása után a *curl* használatával tesztelje az alkalmazást, például:

      ```shell
      curl -X GET http://localhost:8080/
      ```

    Megjelenik a tárolóban megadott App Configuration üzenet. A megadott üzenet is megjelenik a Key Vault.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban létrehozott egy App Configuration kulcsot, amely egy, a Key Vault. Ha meg szeretne ismerkedni a funkciójelölők használatával a Java Spring-alkalmazásban, folytassa a következő oktatóanyagokkal.

> [!div class="nextstepaction"]
> [Felügyelt identitás integrációja](./quickstart-feature-flag-spring-boot.md)
