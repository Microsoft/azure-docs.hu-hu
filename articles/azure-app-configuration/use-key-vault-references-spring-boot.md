---
title: Oktatóanyag az Azure-alkalmazások konfigurációjának használatára Key Vault a Java Spring boot-alkalmazásban | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan használhatja az Azure app Configuration Key Vault hivatkozásait egy Java Spring boot-alkalmazásból
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
ms.openlocfilehash: 04d9c7a343570349851a206fd69fdda822f790a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99981476"
---
# <a name="tutorial-use-key-vault-references-in-a-java-spring-app"></a>Oktatóanyag: Key Vault-referenciák használata Java Spring-alkalmazásokban

Ebből az oktatóanyagból megtudhatja, hogyan használhatja az Azure app Configuration szolgáltatást a Azure Key Vaultekkel együtt. Az alkalmazások konfigurálása és Key Vault a legtöbb alkalmazás-telepítésben egymás mellett használt kiegészítő szolgáltatások.

Az alkalmazás konfigurálása a szolgáltatások közös használatát segíti a Key Vaultban tárolt értékeket hivatkozó kulcsok létrehozásával. Ha az alkalmazás konfigurációja ilyen kulcsokat hoz létre, akkor az értékek helyett a Key Vault értékek URI-azonosítóit tárolja.

Az alkalmazás az alkalmazás konfigurációs ügyfelének használatával kéri le Key Vault hivatkozásokat, ugyanúgy, mint az alkalmazás konfigurációjában tárolt egyéb kulcsok esetében. Ebben az esetben az alkalmazás konfigurációjában tárolt értékek URI-k, amelyek a Key Vault értékeire hivatkoznak. Nem Key Vault értékeket vagy hitelesítő adatokat. Mivel az ügyfél Key Vault hivatkozásként ismeri fel a kulcsokat, az Key Vault használatával kéri le az értékeket.

Az alkalmazás felelős azért, hogy megfelelően hitelesítse az alkalmazások konfigurációját és a Key Vault. A két szolgáltatás nem kommunikál közvetlenül.

Ez az oktatóanyag bemutatja, hogyan implementálhatja Key Vault hivatkozásait a kódban. A szolgáltatás a gyors útmutatókban bemutatott webalkalmazásra épül. Mielőtt továbblépne, először [hozzon létre egy Java Spring-alkalmazást az alkalmazás konfigurálásával](./quickstart-java-spring-app.md) .

Az oktatóanyag lépéseihez bármilyen Kódszerkesztő használható. A [Visual Studio Code](https://code.visualstudio.com/) például egy platformfüggetlen Kódszerkesztő, amely a Windows, MacOS és Linux operációs rendszerekhez érhető el.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzon létre egy olyan alkalmazás-konfigurációs kulcsot, amely Key Vaultban tárolt értékre hivatkozik.
> * A kulcs értékének elérése egy Java Spring-alkalmazásból.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* A 8-as verzióval támogatott [Java Development Kit (JDK)](/java/azure/jdk) .
* Az [Apache Maven](https://maven.apache.org/download.cgi) 3,0-es vagy újabb verziója.

## <a name="create-a-vault"></a>Tároló létrehozása

1. Válassza az **erőforrás létrehozása** lehetőséget a Azure Portal bal felső sarkában:

    ![A képernyőképen az erőforrás létrehozása lehetőség látható a Azure Portal.](./media/quickstarts/search-services.png)
1. A keresőmezőbe írja be a **Key Vault** nevet.
1. Az eredmények listából válassza a bal oldali **kulcstartók** lehetőséget.
1. A **kulcstartók** területen válassza a **Hozzáadás** lehetőséget.
1. A **Key Vault létrehozása** jobb oldalán adja meg a következő információkat:
    * Válassza ki az **előfizetést az előfizetés** kiválasztásához.
    * Az **erőforráscsoport** területen válassza az **új létrehozása** elemet, és adjon meg egy erőforráscsoport-nevet.
    * A **Key Vault neve mezőben** egyedi nevet kell megadni. Ebben az oktatóanyagban írja be a **contoso-vault2**.
    * A **régió** legördülő listából válassza ki a kívánt helyet.
1. Hagyja meg a többi **create Key Vault** -beállítást az alapértelmezett értékekkel.
1. Válassza a **Létrehozás** lehetőséget.

Ezen a ponton az Azure-fiókja az egyetlen jogosult az új tároló elérésére.

![A képernyőfelvételen a kulcstartó látható.](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Titkos kulcs hozzáadása a Key Vaulthoz

Ha titkos kulcsot szeretne hozzáadni a tárolóhoz, néhány további lépést kell elvégeznie. Ebben az esetben adjon hozzá egy üzenetet, amely segítségével tesztelheti Key Vault lekérését. Az üzenet neve **üzenet**, és a "Hello from Key Vault" értéket tárolja.

1. A Key Vault tulajdonságok oldalain válassza a **titkok** elemet.
1. Válassza a **készítés/importálás** lehetőséget.
1. A **titkos kulcs létrehozása** panelen adja meg a következő értékeket:
    * **Feltöltési beállítások**: adja meg a **manuális** értéket.
    * **Név**: írja be az **üzenetet**.
    * **Érték**: adja meg **a Hello értéket Key Vault**.
1. Hagyja a másik **titkos** tulajdonságot az alapértelmezett értékekkel.
1. Válassza a **Létrehozás** lehetőséget.

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Key Vault-hivatkozás hozzáadása az alkalmazás konfigurációjához

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **minden erőforrás** lehetőséget, majd válassza ki a gyors útmutatóban létrehozott app Configuration Store-példányt.

1. Válassza a **Configuration Explorer** lehetőséget.

1. Válassza a **+**  >  **Key Vault-hivatkozás** létrehozása lehetőséget, majd adja meg a következő értékeket:
    * **Kulcs**: **/Application/config.keyvaultmessage** kiválasztása
    * **Címke**: hagyja üresen ezt az értéket.
    * **Előfizetés**, **erőforráscsoport** és **Key Vault**: adja meg az előző szakaszban létrehozott kulcstartó értékeinek megfelelő értékeket.
    * **Titkos** kód: válassza ki az előző szakaszban létrehozott **üzenet** titkos nevét.

## <a name="connect-to-key-vault"></a>Kapcsolódás Key Vaulthoz

1. Ebben az oktatóanyagban egy egyszerű szolgáltatásnevet használ a Key Vault való hitelesítéshez. Az egyszerű szolgáltatás létrehozásához használja az Azure CLI az [ad SP Create-for-RBAC](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) parancsot:

    ```azurecli
    az ad sp create-for-rbac -n "http://mySP" --sdk-auth
    ```

    A művelet a kulcs/érték párok sorozatát adja vissza:

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

1. A következő parancs futtatásával engedélyezheti, hogy az egyszerű szolgáltatásnév hozzáférjen a kulcstartóhoz:

    ```azurecli
    az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get
    ```

1. Futtassa az alábbi parancsot az Object-ID beszerzéséhez, majd adja hozzá az alkalmazás konfigurációjához.

    ```azurecli
    az ad sp show --id <clientId-of-your-service-principal>
    az role assignment create --role "App Configuration Data Reader" --assignee-object-id <objectId-of-your-service-principal> --resource-group <your-resource-group>
    ```

1. Hozza létre a környezeti változókat **AZURE_CLIENT_ID**, **AZURE_CLIENT_SECRET** és **AZURE_TENANT_ID**. Használja az előző lépésekben megjelenő szolgáltatásnév értékeit. A parancssorban futtassa a következő parancsokat, és indítsa újra a parancssort, hogy a módosítás érvénybe lépjen:

    ```cmd
    setx AZURE_CLIENT_ID "clientId"
    setx AZURE_CLIENT_SECRET "clientSecret"
    setx AZURE_TENANT_ID "tenantId"
    ```

    Ha a Windows PowerShellt használja, futtassa a következő parancsot:

    ```azurepowershell
    $Env:AZURE_CLIENT_ID = "clientId"
    $Env:AZURE_CLIENT_SECRET = "clientSecret"
    $Env:AZURE_TENANT_ID = "tenantId"
    ```

    Ha macOS vagy Linux rendszert használ, futtassa a következő parancsot:

    ```cmd
    export AZURE_CLIENT_ID ='clientId'
    export AZURE_CLIENT_SECRET ='clientSecret'
    export AZURE_TENANT_ID ='tenantId'
    ```


> [!NOTE]
> Ezek a Key Vault hitelesítő adatok csak az alkalmazáson belül használatosak.  Az alkalmazás közvetlenül a Key Vault használatával hitelesíti ezeket a hitelesítő adatokat az alkalmazás konfigurációs szolgáltatásának bevonása nélkül.  A Key Vault a kulcsok megosztása vagy kihelyezése nélkül biztosítja az alkalmazás és az alkalmazás konfigurációs szolgáltatásának hitelesítését.

## <a name="update-your-code-to-use-a-key-vault-reference"></a>A kód frissítése Key Vault-hivatkozás használatára

1. Hozzon létre egy **APP_CONFIGURATION_ENDPOINT** nevű környezeti változót. Állítsa be az értékét az alkalmazás konfigurációs tárolójának végpontján. A végpontot a Azure Portal **hozzáférési kulcsok** paneljén találja. A módosítás érvénybe léptetéséhez indítsa újra a parancssort. 


1. Nyissa meg a *bootstrap. properties* mappát az *erőforrások* mappában. Frissítse ezt a fájlt a **APP_CONFIGURATION_ENDPOINT** érték használatára. Távolítsa el a fájlban lévő kapcsolódási sztringre mutató hivatkozásokat. 

    ```properties
    spring.cloud.azure.appconfiguration.stores[0].endpoint= ${APP_CONFIGURATION_ENDPOINT}
    ```

1. Nyissa meg a *MessageProperties. Java* programot. Adjon hozzá egy új, *keyVaultMessage* nevű változót:

    ```java
    private String keyVaultMessage;

    public String getKeyVaultMessage() {
        return keyVaultMessage;
    }

    public void setKeyVaultMessage(String keyVaultMessage) {
        this.keyVaultMessage = keyVaultMessage;
    }
    ```

1. Nyissa meg a *HelloController. Java* programot. Frissítse a *getMessage* metódust, hogy tartalmazza a Key Vaultból beolvasott üzenetet.

    ```java
    @GetMapping
    public String getMessage() {
        return "Message: " + properties.getMessage() + "\nKey Vault message: " + properties.getKeyVaultMessage();
    }
    ```

1. Hozzon létre egy új, *AzureCredentials. Java* nevű fájlt, és adja hozzá az alábbi kódot.

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

1. Hozzon létre egy új, *AppConfiguration. Java* nevű fájlt. És adja hozzá az alábbi kódot.

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

1. Hozzon létre egy új fájlt a sources META-INF könyvtárban, a *Spring. Factorys* néven, és adja hozzá az alábbi kódot.

    ```factories
    org.springframework.cloud.bootstrap.BootstrapConfiguration=\
    com.example.demo.AppConfiguration
    ```

1. Készítse elő a Spring boot-alkalmazást a Maven használatával, és futtassa, például:

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

1. Az alkalmazás futása után a *curl* használatával tesztelheti az alkalmazást, például:

      ```shell
      curl -X GET http://localhost:8080/
      ```

    Megjelenik az alkalmazás konfigurációs tárolójában megadott üzenet. Megjelenik a Key Vaultban megadott üzenet is.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban létrehozott egy alkalmazás-konfigurációs kulcsot, amely a Key Vaultban tárolt értékre hivatkozik. Ha szeretné megtudni, hogyan használhatja a Feature Flags szolgáltatást a Java Spring-alkalmazásban, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Felügyelt identitások integrációja](./quickstart-feature-flag-spring-boot.md)
