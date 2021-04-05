---
title: Java-alkalmazás üzembe helyezése Service Fabric-fürtön az Azure-ban
description: Ez az oktatóanyag azt mutatja be, hogyan kell üzembe helyezni egy Java Service Fabric-alkalmazást egy Azure Service Fabric-fürtön.
ms.topic: tutorial
ms.date: 02/26/2018
ms.custom: mvc, devx-track-java, devx-track-azurecli
ms.openlocfilehash: 48cb7df49fb918ebd30883edc573cc63b6e35e4a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98789397"
---
# <a name="tutorial-deploy-a-java-application-to-a-service-fabric-cluster-in-azure"></a>Oktatóanyag: Java-alkalmazás üzembe helyezése egy Service Fabric-fürtön az Azure-ban

Ez az oktatóanyag egy sorozat harmadik része, és azt mutatja be, hogyan kell üzembe helyezni egy Service Fabric-alkalmazást egy Azure-fürtön.

A sorozat harmadik részében az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Biztonságos Linux-fürt létrehozása az Azure-ban
> * Alkalmazás üzembe helyezése a fürtön

Ebben az oktatóanyag-sorozatban az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * [Service Fabric Reliable Services Java-alkalmazás létrehozása](service-fabric-tutorial-create-java-app.md)
> * [Az alkalmazás üzembe helyezése és hibakeresése egy helyi fürtön](service-fabric-tutorial-debug-log-local-cluster.md)
> * Alkalmazás üzembe helyezése egy Azure-fürtön
> * [Figyelés és diagnosztika beállítása az alkalmazáshoz](service-fabric-tutorial-java-elk.md)
> * [CI/CD beállítása](service-fabric-tutorial-java-jenkins.md)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Az Azure CLI összetevő telepítése](/cli/azure/install-azure-cli)
* Telepítse a Service Fabric SDK [Mac](service-fabric-get-started-mac.md) vagy [Linux](service-fabric-get-started-linux.md) rendszerhez való változatát.
* [A Python 3 telepítése](https://wiki.python.org/moin/BeginnersGuide/Download)

## <a name="create-a-service-fabric-cluster-in-azure"></a>Service Fabric-fürt létrehozása az Azure-ban

A következő lépésekkel hozhatja létre azokat az erőforrásokat, amelyekre szükség van az alkalmazás üzembe helyezéséhez a Service Fabric-fürtön. Azokat az erőforrásokat is be kell állítani, amelyek ahhoz szükségesek, hogy az ELK (Elasticsearch, Logstash, Kibana) segítségével monitorozhassa a megoldás állapotát. Ez a gyakorlatban azt jelenti, hogy az [Event Hubs](https://azure.microsoft.com/services/event-hubs/) fogja gyűjteni a Service Fabric naplóit, és a rendszer úgy van konfigurálva, hogy a Service Fabric-fürt naplóit elküldje a Logstash-példánynak.

1. Nyisson meg egy terminált, és töltse le a segítő szkripteket és sablonokat tartalmazó következő csomagot, amely segítségére lesz az erőforrások létrehozásában az Azure-ban.

    ```bash
    git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
    ```

2. Jelentkezzen be az Azure-fiókjába

    ```azurecli
    az login
    ```

3. Állítsa be az Azure-előfizetést, amelyet használni szeretne az erőforrások létrehozásához.

    ```azurecli
    az account set --subscription [SUBSCRIPTION-ID]
    ```

4. Futtassa a következő parancsot a *service-fabric-java-quickstart/AzureCluster* mappából, hogy létrehozzon egy fürttanúsítványt a Key Vaultban. Ezzel a tanúsítvánnyal fog gondoskodni a Service Fabric-fürt védelméről. Adja meg a régiót (meg kell egyeznie a Service Fabric-fürt régiójával), a kulcstároló erőforráscsoport nevét, a kulcstároló nevét, a tanúsítvány jelszavát és a fürt DNS-nevét.

    ```bash
    ./new-service-fabric-cluster-certificate.sh [REGION] [KEY-VAULT-RESOURCE-GROUP] [KEY-VAULT-NAME] [CERTIFICATE-PASSWORD] [CLUSTER-DNS-NAME-FOR-CERTIFICATE]

    Example: ./new-service-fabric-cluster-certificate.sh 'westus' 'testkeyvaultrg' 'testkeyvault' '<password>' 'testservicefabric.westus.cloudapp.azure.com'
    ```

    Az előző parancs a következő információkat adja vissza, amelyekre később még szüksége lesz.

    ```output
    Source Vault Resource Id: /subscriptions/<subscription_id>/resourceGroups/testkeyvaultrg/providers/Microsoft.KeyVault/vaults/<name>
    Certificate URL: https://<name>.vault.azure.net/secrets/<cluster-dns-name-for-certificate>/<guid>
    Certificate Thumbprint: <THUMBPRINT>
    ```

5. Hozzon létre egy erőforráscsoportot a naplóit tároló tárfiók számára.

    ```azurecli
    az group create --location [REGION] --name [RESOURCE-GROUP-NAME]

    Example: az group create --location westus --name teststorageaccountrg
    ```

6. Hozzon létre egy tárfiókot, amely tárolni fogja a beszerzett naplókat.

    ```azurecli
    az storage account create -g [RESOURCE-GROUP-NAME] -l [REGION] --name [STORAGE-ACCOUNT-NAME] --kind Storage

    Example: az storage account create -g teststorageaccountrg -l westus --name teststorageaccount --kind Storage
    ```

7. Lépjen be az [Azure Portalra](https://portal.azure.com), és nyissa meg a Storage-fiókja **Közös hozzáférésű jogosultságkód** lapját. Hozza létre az SAS-azonosítót az alábbi módon.

    ![SAS létrehozása a Storage-hoz](./media/service-fabric-tutorial-java-deploy-azure/storagesas.png)

8. Készítsen másolatot a fiók SAS URL-címéről, és őrizze meg a Service Fabric-fürt létrehozásáig. Az URL-cím a következőhöz fog hasonlítani:

    ```output
    ?sv=2017-04-17&ss=bfqt&srt=sco&sp=rwdlacup&se=2018-01-31T03:24:04Z&st=2018-01-30T19:24:04Z&spr=https,http&sig=IrkO1bVQCHcaKaTiJ5gilLSC5Wxtghu%2FJAeeY5HR%2BPU%3D
    ```

9. Hozzon létre egy erőforráscsoportot, amely tartalmazza az Event Hubs-erőforrásokat. Az Event Hubs fogja küldeni a Service Fabric üzeneteit az ELK-erőforrásokat futtató kiszolgálónak.

    ```azurecli
    az group create --location [REGION] --name [RESOURCE-GROUP-NAME]

    Example: az group create --location westus --name testeventhubsrg
    ```

10. Hozzon létre egy Event Hubs-erőforrást a következő paranccsal. Az útmutatásokat követve adja meg a namespaceName, az eventHubName, a consumerGroupName, a sendAuthorizationRule és a receiveAuthorizationRule részleteit.

    ```azurecli
    az deployment group create -g [RESOURCE-GROUP-NAME] --template-file eventhubsdeploy.json

    Example:
    az deployment group create -g testeventhubsrg --template-file eventhubsdeploy.json
    Please provide string value for 'namespaceName' (? for help): testeventhubnamespace
    Please provide string value for 'eventHubName' (? for help): testeventhub
    Please provide string value for 'consumerGroupName' (? for help): testeventhubconsumergroup
    Please provide string value for 'sendAuthorizationRuleName' (? for help): sender
    Please provide string value for 'receiveAuthorizationRuleName' (? for help): receiver
    ```

    Másolja az **output** mező tartalmát az előző parancs JSON-kimenetébe. A küldő adataira a Service Fabric-fürt létrehozásakor van szükség. A fogadó nevét és kulcsát érdemes elmenteni a következő oktatóanyaghoz, amelyben a Logstash szolgáltatást fogja konfigurálni az Event Hubs üzeneteinek fogadására. A következő blob egy lehetséges példa a JSON-kimenetre:

    ```json
    "outputs": {
        "receiver Key": {
            "type": "String",
            "value": "[KEY]"
        },
        "receiver Name": {
            "type": "String",
            "value": "receiver"
        },
        "sender Key": {
            "type": "String",
            "value": "[KEY]"
        },
        "sender Name": {
            "type": "String",
            "value": "sender"
        }
    }
    ```

11. Futtassa az *eventhubssastoken.py* szkriptet, hogy létrehozza az SAS URL-címet a létrehozott Event Hubs-erőforráshoz. A Service Fabric-fürt ezt az SAS URL-címet fogja használni arra, hogy naplókat küldjön az Event Hubsnak. Így az URL-cím a **feladói** szabályzat alapján jön létre. A szkript visszaadja az SAS URL-címet annak az Event Hubs-erőforrásnak, amelyre a következő lépésben lesz szükség:

    ```python
    python3 eventhubssastoken.py 'testeventhubs' 'testeventhubs' 'sender' '[PRIMARY-KEY]'
    ```

    Másolja az **sr** mező értékét a visszaadott JSON-kimenetbe. Az **sr** mező értéke az Event Hubs számára készült SAS-azonosító. A következő URL-cím egy lehetséges példa az **sr** mező tartalmára:

    ```output
    https%3A%2F%testeventhub.servicebus.windows.net%testeventhub&sig=7AlFYnbvEm%2Bat8ALi54JqHU4i6imoFxkjKHS0zI8z8I%3D&se=1517354876&skn=sender
    ```

    A EventHubs SAS URL-címe a következő struktúrát követi: `https://<namespacename>.servicebus.windows.net/<eventhubsname>?sr=<sastoken>` . Például: `https://testeventhubnamespace.servicebus.windows.net/testeventhub?sr=https%3A%2F%testeventhub.servicebus.windows.net%testeventhub&sig=7AlFYnbvEm%2Bat8ALi54JqHU4i6imoFxkjKHS0zI8z8I%3D&se=1517354876&skn=sender`

12. Nyissa meg az *sfdeploy.parameters.json* fájlt, és cserélje le a következő tartalmakat az előző lépésekből származó értékekre. [SAS-URL-STORAGE-ACCOUNT] – a 8. lépésben feljegyezve. [SAS-URL-EVENT-HUBS] – a 11. lépésben feljegyezve.

    ```json
    "applicationDiagnosticsStorageAccountName": {
        "value": "teststorageaccount"
    },
    "applicationDiagnosticsStorageAccountSasToken": {
        "value": "[SAS-URL-STORAGE-ACCOUNT]"
    },
    "loggingEventHubSAS": {
        "value": "[SAS-URL-EVENT-HUBS]"
    }
    ```

13. Megnyitja az **sfdeploy.parameters.json**-t. Módosítsa az alábbi paramétereket, majd mentse a fájlt.
    - **clusterName**. Csak kisbetűket és számokat használjon.
    - **adminUserName** (ürestől eltérő értékre)
    - **adminPassword** (ürestől eltérő értékre)

14. Futtassa a következő parancsot a Service Fabric-fürt létrehozásához.

    ```azurecli
    az sf cluster create --location 'westus' --resource-group 'testlinux' --template-file sfdeploy.json --parameter-file sfdeploy.parameters.json --secret-identifier <certificate_url_from_step4>
    ```

## <a name="deploy-your-application-to-the-cluster"></a>Alkalmazás üzembe helyezése a fürtön

1. Az alkalmazás üzembe helyezése előtt ki kell egészítenie a *Voting/VotingApplication/ApplicationManifest.xml* fájlt a következő kódrészlettel. Az **X509FindValue** mező a **Service Fabric-fürt létrehozása az Azure-ban** szakasz 4. lépésében kapott ujjlenyomat. Ez a kódrészlet az **ApplicationManifest** mezőbe (a gyökérmezőbe) van beágyazva.

    ```xml
    <Certificates>
          <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="[CERTIFICATE-THUMBPRINT]" />
    </Certificates>
    ```

2. Ahhoz, hogy üzembe helyezhesse az alkalmazást a fürtön, az SFCTL segítségével kapcsolatot kell létesítenie a fürttel. Az SFCTL csak a PEM-fájl, valamint a nyilvános és a privát kulcs birtokában tud kapcsolódni a fürthöz. Ezért a következő parancsot kell futtatnia, hogy egy nyilvános és privát kulccsal is rendelkező PEM-fájlt állítson elő. 

    ```bash
    openssl pkcs12 -in <clustername>.<region>.cloudapp.azure.com.pfx -out sfctlconnection.pem -nodes -passin pass:<password>
    ```

3. Futtassa a következő parancsot a fürthöz való csatlakozáshoz.

    ```bash
    sfctl cluster select --endpoint https://<clustername>.<region>.cloudapp.azure.com:19080 --pem sfctlconnection.pem --no-verify
    ```

4. Az alkalmazás üzembe helyezéséhez keresse meg a *Voting/Scripts* mappát, és futtassa az **install.sh** szkriptet.

    ```bash
    ./install.sh
    ```

5. A Service Fabric Explorer eléréséhez nyisson meg egy böngészőt, és írja be a következőt: `https://testlinuxcluster.westus.cloudapp.azure.com:19080`. A tanúsítványtárolóból válassza ki a tanúsítványt, amellyel csatlakozni szeretne ehhez a végponthoz. Linux-rendszerek esetében a *new-service-fabric-cluster-certificate.sh* szkripttel létrehozott tanúsítványokat a Chrome-ba kell importálni, hogy a Service Fabric Explorert meg lehessen jeleníteni. Mac gépek esetében a PFX-fájlt telepíteni kell a kulcskarikában. A rendszer jelzi, ha az alkalmazás települt a fürtre.

    ![SFX Java Azure](./media/service-fabric-tutorial-java-deploy-azure/sfxjavaonazure.png)

6. Az alkalmazás megnyitásához írja be a következőt: `https://testlinuxcluster.westus.cloudapp.azure.com:8080`

    ![Szavazóalkalmazás – Java Azure](./media/service-fabric-tutorial-java-deploy-azure/votingappjavaazure.png)

7. Ha el kívánja távolítani az alkalmazást a fürtről, futtassa az *uninstall.sh* szkriptet a **Scripts** mappában.

    ```bash
    ./uninstall.sh
    ```

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Biztonságos Linux-fürt létrehozása az Azure-ban
> * Az ELK-val végzett monitorozáshoz szükséges erőforrások létrehozása

Folytassa a következő oktatóanyaggal:
> [!div class="nextstepaction"]
> [A monitorozás és a diagnosztika beállítása](service-fabric-tutorial-java-elk.md)
