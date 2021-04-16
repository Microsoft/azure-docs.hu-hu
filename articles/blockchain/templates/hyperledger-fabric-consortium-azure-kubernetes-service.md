---
title: A Hyperledger Fabric konzorcium üzembe helyezése a Azure Kubernetes Service
description: Konzorciumhálózat üzembe helyezése és Hyperledger Fabric konfigurálása a Azure Kubernetes Service
ms.date: 03/01/2021
ms.topic: how-to
ms.reviewer: ravastra
ms.custom: contperf-fy21q3, devx-track-azurecli
ms.openlocfilehash: 03f19d1922c011c1b5304b66488e9fa8de703bf9
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478307"
---
# <a name="deploy-hyperledger-fabric-consortium-on-azure-kubernetes-service"></a>A Hyperledger Fabric konzorcium üzembe helyezése a Azure Kubernetes Service

Az Azure-beli Hyperledger Fabric (AKS) Azure Kubernetes Service sablonnal üzembe helyezhet és konfigurálhat egy Hyperledger Fabric konzorciumhálózatot.

A cikk elolvasása után:

- Jól ismeri a Hyperledger Fabric és a blokklánchálózat építőelemeiből Hyperledger Fabric összetevőket.
- Tudja meg, hogyan helyezhet üzembe és konfigurálhatja a Hyperledger Fabric konzorciumhálózatot a Azure Kubernetes Service az éles forgatókönyvekhez.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="choose-an-azure-blockchain-solution"></a>Válasszon egy Azure Blockchain megoldást

A megoldássablon használata előtt hasonlítsa össze a forgatókönyvet a rendelkezésre álló lehetőségek gyakori Azure Blockchain:

Beállítás | Szolgáltatásmodell | Gyakori eset
-------|---------------|-----------------
Megoldássablonok | IaaS | A megoldássablonok Azure Resource Manager sablonok, amelyek segítségével teljes mértékben konfigurált blokklánchálózati topológiát létesíthet. A sablonok számítási, Microsoft Azure és tárolási szolgáltatásokat helyeznek üzembe és konfigurálnak egy blokklánchálózat-típushoz. A megoldássablonok szolgáltatói szerződés nélkül biztosítanak. Támogatásért használja [a Microsoft Q&A](/answers/topics/azure-blockchain-workbench.html) lapot.
[Azure Blockchain Service](../service/overview.md) | PaaS | Azure Blockchain Service Preview leegyszerűsíti a konzorciumi blokklánchálózatok megalakítását, kezelését és irányítását. A Azure Blockchain Service paaS-t, konzorciumkezelést, illetve szerződés- és tranzakció-adatvédelmet igénylő megoldásokhoz használja.
[Azure Blockchain Workbench](../workbench/overview.md) | IaaS és PaaS | Azure Blockchain Workbench előzetes verziója olyan Azure-szolgáltatások és -képességek gyűjteménye, amelyek segítségével blokkláncalkalmazásokat hozhat létre és helyezhet üzembe az üzleti folyamatok és adatok más szervezésekkel való megosztásához. A Azure Blockchain Workbench blokkláncmegoldás prototípusának vagy egy blokkláncalkalmazás koncepciójának igazolásához használhatja. Azure Blockchain Workbench szolgáltatói szerződés nélkül biztosítjuk. Támogatásért használja [a Microsoft Q&A oldalt.](/answers/topics/azure-blockchain-workbench.html)

## <a name="deploy-the-orderer-and-peer-organization"></a>A rendelési és társszervezet üzembe helyezése

Először is szüksége lesz egy Olyan Azure-előfizetésre, amely több virtuális gép és standard szintű tárfiók üzembe helyezését is támogatja. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egy ingyenes [Azure-fiókot.](https://azure.microsoft.com/free/)

A hálózati összetevők üzembe helyezésének Hyperledger Fabric a következő [Azure Portal.](https://portal.azure.com)

1. Válassza **az Erőforrás létrehozása**  >  **Blockchain lehetőséget,** majd keressen rá a Hyperledger Fabric a Azure Kubernetes Service **(előzetes verzió) oldalon.**

2. Adja meg a projekt részleteit az **Alapvető beállítások** lapon.

    ![Az Alapvető beállítások lapot bemutató képernyőkép.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-basics.png)

3. Adja meg a következő részleteket:
    - **Előfizetés:** Válassza ki az előfizetés nevét, ahová a hálózati Hyperledger Fabric telepíteni szeretné.
    - **Erőforráscsoport:** Hozzon létre egy új erőforráscsoportot, vagy válasszon egy meglévő üres erőforráscsoportot. Az erőforráscsoport a sablon részeként üzembe helyezett összes erőforrást tartalmazza.
    - **Régió:** Válassza ki azt az Azure-régiót, amelyben üzembe szeretné helyezni Azure Kubernetes Service fürtöt a Hyperledger Fabric számára. A sablon minden olyan régióban elérhető, ahol az AKS elérhető. Válassza ki azt a régiót, ahol az előfizetése nem túllépi a virtuális gép (VM) kvótakorlátját.
    - **Erőforrás-előtag:** Adjon meg egy előtagot az üzembe helyezett erőforrások elnevezéséhez. Hat karakternél rövidebbnek kell lennie, és a karakterek kombinációjának számokat és betűket is tartalmaznia kell.
4. Válassza **a Hálóbeállítások lapot** a Hyperledger Fabric telepíteni kívánt hálózati összetevők hálózati összetevőinek meghatározásához.

    ![A Hálóbeállítások lapot bemutató képernyőkép.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-settings.png)

5. Adja meg a következő részleteket:
    - **Szervezet neve:** Adja meg a Hyperledger Fabric szervezet nevét, amely a különböző adatsíkművelethez szükséges. A szervezet nevének üzemelő példányonként egyedinek kell lennie.
    - **Hálóhálózati összetevő:** Válassza a  **Rendelési** szolgáltatást vagy a Társcsomópontokat a beállítani kívánt blokklánchálózati összetevő alapján.
    - **Csomópontok száma:** A csomópontok két típusa a következő:
        - **Rendelési szolgáltatás:** A tranzakciórendezésért felelős csomópontok a főkönyvben. Válassza ki a csomópontok számát, hogy hibatűrést biztosítson a hálózat számára. A rendelési csomópontok támogatott száma 3, 5 és 7.
        - **Társcsomópontok:** A főkönyveket és intelligens szerződéseket gazdacsomópontok. Igény szerint 1–10 csomópontot választhat.
    - **Társcsomópont világállapot-adatbázisa:** A társcsomópontok világállapot-adatbázisai. A LevelDB a társcsomópontba beágyazott alapértelmezett állapotadatbázis. A chaincode-adatokat egyszerű kulcs/érték párokként tárolja, és csak a kulcs-, kulcstartomány- és összetett kulcslekérdezéseket támogatja. A CouchDB egy opcionális alternatív állapotadatbázis, amely támogatja a gazdag lekérdezéseket, ha a chaincode adatértékeket JSON-ként modelleik. Ez a mező akkor jelenik meg,  ha a Háló **hálózati** összetevő legördülő listájában a Társcsomópontok lehetőséget választja.
    - **Háló hitelesítésszolgáltató felhasználóneve:** A Háló hitelesítésszolgáltató lehetővé teszi a hitelesítésszolgáltatót tartalmazó kiszolgálói folyamat inicializálását és elindítani. Lehetővé teszi az identitások és tanúsítványok kezelését. A sablon részeként üzembe helyezett AKS-fürtök alapértelmezés szerint egy Háló CA-podot fognak tartalmazni. Adja meg a háló hitelesítésszolgáltatói hitelesítéséhez használt felhasználónevet.
    - **Háló hitelesítésszolgáltató jelszava:** Adja meg a háló hitelesítésszolgáltatói hitelesítésének jelszavát.
    - **Jelszó megerősítése:** Erősítse meg a háló hitelesítésszolgáltató jelszavát.
    - **Tanúsítványok:** Ha saját főtanúsítványait szeretné használni a háló hitelesítésszolgáltató inicializálására, válassza a Főtanúsítvány feltöltése **a háló** hitelesítésszolgáltatóhoz lehetőséget. Ellenkező esetben a háló hitelesítésszolgáltatója alapértelmezés szerint önaírt tanúsítványokat hoz létre.
    - **Főtanúsítvány:** Töltse fel azt a főtanúsítványt (nyilvános kulcsot), amellyel a háló hitelesítésszolgáltatót inicializálni kell. A .pem formátumú tanúsítványok támogatottak. A tanúsítványoknak érvényesnek kell lennie, és UTC időzónában kell lennie.
    - **Főtanúsítvány titkos kulcsa:** Töltse fel a főtanúsítvány titkos kulcsát. Ha .pem tanúsítvánnyal rendelkezik, amely összevont nyilvános és titkos kulccsal rendelkezik, töltse fel ide is.


6. Válassza az **AKS-fürt beállítások lapját** a fürtkonfiguráció Azure Kubernetes Service meghatározásához. Az AKS-fürt különböző podokkal rendelkezik, amelyek a hálózati összetevők Hyperledger Fabric futtatására vannak konfigurálva. Az üzembe helyezett Azure-erőforrások a következőek:

    - **Hálóeszközök:** A hálózati összetevők konfigurálásáért Hyperledger Fabric eszközök.
    - **Orderer/peer podok:** A Hyperledger Fabric csomópontjai.
    - **Proxy:** NGNIX-proxypod, amelyen keresztül az ügyfélalkalmazások kommunikálhatnak az AKS-fürtgal.
    - **Háló hitelesítésszolgáltató:** A háló hitelesítésszolgáltatóját futtató pod.
    - **PostgreSQL:** A háló hitelesítésszolgáltató identitását fenntartó adatbázispéldány.
    - **Kulcstartó:** A Azure Key Vault szolgáltatás példánya, amely a Háló hitelesítésszolgáltató hitelesítő adatainak és az ügyfél által biztosított főtanúsítványok mentéséhez van üzembe téve. A tároló a sablon üzembe helyezésének újrapróbálkozása esetén használatos a sablon mechanika kezeléséhez.
    - **Felügyelt lemez:** Az Azure Managed Disks szolgáltatás példánya, amely állandó tárolót biztosít a tranzakciókönyvhöz és a társcsomópont világállapot-adatbázisához.
    - **Nyilvános** IP-cím: A fürtgal való kommunikációra üzembe helyezett AKS-fürt végpontja.

    Adja meg a következő részleteket: 

    ![Képernyőkép az A K S-fürt beállításai lapról.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-aks-cluster-settings-1.png)

    - **Kubernetes-fürt neve:** Szükség esetén módosítsa az AKS-fürt nevét. Ezt a mezőt a rendszer előre kitölti a megadott erőforrás-előtag alapján.
    - **Kubernetes-verzió:** Válassza ki a fürtön üzembe helyező Kubernetes-verziót. Az Alapvető beállítások lapon kiválasztott  régió alapján az elérhető támogatott verziók változhatnak.
    - **DNS-előtag:** Adjon meg egy DNS-névelőtagot az AKS-fürthöz. A fürt létrehozása után DNS használatával fog csatlakozni a Kubernetes API-hoz a tárolók kezelésekor.
    - **Csomópont mérete:** A Kubernetes-csomópont mérete esetén választhat az Azure-ban elérhető virtuálisgép-készletet (SKUs) felsoroló listából. Az optimális teljesítmény érdekében a Standard DS3 v2 használatát javasoljuk.
    - **Csomópontok száma:** Itt adhatja meg a fürtben üzembe helyezendő Kubernetes-csomópontok számát. Javasoljuk, hogy ezt a csomópontszámot a Hálóbeállítások lapon megadott csomópontok Hyperledger Fabric vagy annál **nagyobb számban** tartsa meg.
    - **Egyszerű szolgáltatás ügyfél-azonosítója:** Adja meg egy meglévő szolgáltatásnév ügyfél-azonosítóját, vagy hozzon létre egy újat. Az AKS-hitelesítéshez szolgáltatásnév szükséges. Tekintse meg [a szolgáltatásnév létrehozásához szükséges lépéseket.](/powershell/azure/create-azure-service-principal-azureps#create-a-service-principal)
    - **Egyszerű szolgáltatás titkos ügyféltitkja:** Adja meg a szolgáltatásnév ügyfél-azonosítójával megadott egyszerű szolgáltatás titkos ügyfél-titkos adatokat.
    - **Erősítse meg az ügyfél titkos ját:** Erősítse meg a szolgáltatásnév titkos ügyfél-titkos ját.
    - **Tárolófigyelés engedélyezése:** Engedélyezze az AKS-monitorozást, amely lehetővé teszi az AKS-naplók leküldését a megadott Log Analytics-munkaterületre.
    - **Log Analytics-munkaterület:** A Log Analytics-munkaterület a figyelés engedélyezése esetén létrehozott alapértelmezett munkaterülettel lesz feltöltve.

8. Válassza az **Áttekintés és létrehozás lapot.** Ez a lépés aktiválja a megadott értékek érvényesítését.
9. Az ellenőrzés után válassza a **Létrehozás lehetőséget.**

    Az üzembe helyezés általában 10–12 percet vesz igénybe. Az idő a megadott AKS-csomópontok méretétől és számától függően változhat.
10. A sikeres üzembe helyezést követően a jobb felső sarokban található Azure-értesítéseken keresztül kap értesítést.
11. Válassza **az Ugrás az erőforráscsoporthoz** lehetőséget a sablon üzembe helyezésének részeként létrehozott összes erőforrás ellenőrzéséhez. Minden erőforrásnév az Alapvető beállítások lapon megadott **előtaggal** kezdődik.

## <a name="build-the-consortium"></a>A konzorcium összeállítása

A blockchain konzorcium a rendelési szolgáltatás és a társcsomópontok üzembe helyezése után való felépítéséhez végezze el a következő lépéseket egymás után. Az Azure Hyperledger Fabric szkript (azhlf) segít a konzorcium beállításában, a csatorna létrehozásában és a chaincode műveletek elvégzésében.

> [!NOTE]
> Az Azure Hyperledger Fabric (azhlf) szkript frissítve lett, hogy több funkciót biztosítson. Ha a régi szkriptre szeretne hivatkozni, tekintse meg a [readme fájlt a GitHubon.](https://github.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/blob/master/consortiumScripts/README.md) Ez a szkript kompatibilis a Hyperledger Fabric 2.0.0-s Azure Kubernetes Service és újabb verzióival. Az üzemelő példány verziójának ellenőrzéséhez kövesse a Hibaelhárítás [témakörben található lépéseket.](#troubleshoot)

> [!NOTE]
> A szkript csak bemutató, fejlesztési és tesztelési forgatókönyvekhez nyújt segítséget. A szkript által létrehozott csatornának és konzorciumnak alapszintű Hyperledger Fabric a bemutató, fejlesztési és tesztelési forgatókönyvek egyszerűsítése érdekében. Az éles környezetben való beállításhoz javasoljuk, hogy frissítse a csatorna-/konzorciumi Hyperledger Fabric szabályzatokat a szervezet megfelelőségi igényeinek megfelelően a natív Hyperledger Fabric API-k használatával.


Az Azure-beli Hyperledger Fabric szkript futtatásához minden parancs végrehajtható az Azure Bash parancssori felületén (CLI). A jobb felső sarokban Azure Cloud Shell be Hyperledger Fabric Azure Kubernetes Service-sablon ![ ](./media/hyperledger-fabric-consortium-azure-kubernetes-service/arrow.png) Azure Portal. A parancssorba írja be és válassza az Enter billentyűt a Bash parancssori felületre való váltáshoz, vagy válassza a Bash lehetőséget a Cloud Shell `bash` eszköztáron. 

További [Azure Cloud Shell](../../cloud-shell/overview.md) lásd: Azure Cloud Shell.

![Képernyőkép a parancsokat a Azure Cloud Shell.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-powershell.png)


Az alábbi képen egy rendelési szervezet és egy társszervezet közötti konzorcium felépítésének lépéses folyamata látható. A következő szakaszok részletes parancsokat mutatnak be a lépések befejezéséhez.

![A konzorcium építési folyamatának ábrája.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/process-to-build-consortium-flow-chart.png)

Miután befejezte a kezdeti beállítást, az ügyfélalkalmazással a következő műveleteket érheti el: â€

- Csatornakezelés
- Konzorciumkezelés
- Üzletlánckód-kezelés

### <a name="download-client-application-files"></a>Ügyfélalkalmazás fájljainak letöltése

Az első beállítás az ügyfélalkalmazás fájljainak letöltése. Futtassa az alábbi parancsokat az összes szükséges fájl és csomag letöltéséhez:

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/azhlfToolSetup.sh | bash
cd azhlfTool
npm install
npm run setup
```

Ezek a parancsok klónozják az Azure Hyperledger Fabric-ügyfélalkalmazás kódját a nyilvános GitHub-adattárból, majd betöltik az összes függő npm-csomagot. A parancs sikeres végrehajtása után egy új node_modules az aktuális könyvtárban. Az összes szükséges csomag be lesz töltve a node_modules mappába.

### <a name="set-up-environment-variables"></a>Környezeti változók beállítása

Minden környezeti változó követi az Azure-erőforrások elnevezési konvencióját.

#### <a name="set-environment-variables-for-the-orderer-organizations-client"></a>Környezeti változók beállítása az orderer szervezet ügyfeléhez

```bash
ORDERER_ORG_SUBSCRIPTION=<ordererOrgSubscription>
ORDERER_ORG_RESOURCE_GROUP=<ordererOrgResourceGroup>
ORDERER_ORG_NAME=<ordererOrgName>
ORDERER_ADMIN_IDENTITY="admin.$ORDERER_ORG_NAME"
CHANNEL_NAME=<channelName>
```

#### <a name="set-environment-variables-for-the-peer-organizations-client"></a>Környezeti változók beállítása a társszervezet ügyfeléhez

```bash
PEER_ORG_SUBSCRIPTION=<peerOrgSubscritpion>
PEER_ORG_RESOURCE_GROUP=<peerOrgResourceGroup>
PEER_ORG_NAME=<peerOrgName>
PEER_ADMIN_IDENTITY="admin.$PEER_ORG_NAME"
CHANNEL_NAME=<channelName>
```

A konzorciumban a társszervezetek száma alapján előfordulhat, hogy meg kell ismételnie a társparancsokat, és ennek megfelelően kell beállítania a környezeti változót.

#### <a name="set-environment-variables-for-an-azure-storage-account"></a>Környezeti változók beállítása Azure-tárfiókhoz

```bash
STORAGE_SUBSCRIPTION=<subscriptionId>
STORAGE_RESOURCE_GROUP=<azureFileShareResourceGroup>
STORAGE_ACCOUNT=<azureStorageAccountName>
STORAGE_LOCATION=<azureStorageAccountLocation>
STORAGE_FILE_SHARE=<azureFileShareName>
```

Az alábbi parancsokkal hozzon létre egy Azure Storage-fiókot. Ha már rendelkezik Azure Storage-fiókkal, hagyja ki ezt a lépést.

```bash
az account set --subscription $STORAGE_SUBSCRIPTION
az group create -l $STORAGE_LOCATION -n $STORAGE_RESOURCE_GROUP
az storage account create -n $STORAGE_ACCOUNT -g  $STORAGE_RESOURCE_GROUP -l $STORAGE_LOCATION --sku Standard_LRS
```

Az alábbi parancsokkal hozzon létre egy fájlmegosztást az Azure Storage-fiókban. Ha már rendelkezik fájlmegosztásokkal, hagyja ki ezt a lépést.

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
az storage share create  --account-name $STORAGE_ACCOUNT  --account-key $STORAGE_KEY  --name $STORAGE_FILE_SHARE
```

Az alábbi parancsokkal hozzon létre egy kapcsolati sztringet egy Azure-fájlmegosztáshoz.

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
SAS_TOKEN=$(az storage account generate-sas --account-key $STORAGE_KEY --account-name $STORAGE_ACCOUNT --expiry `date -u -d "1 day" '+%Y-%m-%dT%H:%MZ'` --https-only --permissions lruwd --resource-types sco --services f | tr -d '"')
AZURE_FILE_CONNECTION_STRING=https://$STORAGE_ACCOUNT.file.core.windows.net/$STORAGE_FILE_SHARE?$SAS_TOKEN

```

### <a name="import-an-organization-connection-profile-admin-user-identity-and-msp"></a>Szervezeti kapcsolati profil, rendszergazdai felhasználói identitás és MSP importálása

A következő parancsokkal lekéri a szervezet kapcsolati profilját, rendszergazdai felhasználói identitását és felügyelt szolgáltatóját (MSP) a Azure Kubernetes Service-fürtből, és ezeket az identitásokat az ügyfélalkalmazás helyi tárolójában tárolja. Helyi tároló például az *azhlfTool/stores* könyvtár.

Az orderer szervezetnél:

```bash
./azhlf adminProfile import fromAzure -o $ORDERER_ORG_NAME -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION
./azhlf connectionProfile import fromAzure -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION -o $ORDERER_ORG_NAME   
./azhlf msp import fromAzure -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION -o $ORDERER_ORG_NAME
```

A társszervezethez:

```bash
./azhlf adminProfile import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
./azhlf connectionProfile import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
./azhlf msp import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
```

### <a name="create-a-channel"></a>Csatorna létrehozása

Az orderer szervezet ügyfeléből a következő paranccsal hozzon létre egy csatornát, amely csak a rendelési szervezetet tartalmazza.  

```bash
./azhlf channel create -c $CHANNEL_NAME -u $ORDERER_ADMIN_IDENTITY -o $ORDERER_ORG_NAME
```

### <a name="add-a-peer-organization-for-consortium-management"></a>Partnerszervezet hozzáadása konzorciumkezeléshez

>[!NOTE]
> Mielőtt elkezdi a konzorciumi műveleteket, győződjön meg arról, hogy befejezte az ügyfélalkalmazás kezdeti beállítását.  

Futtassa a következő parancsokat a megadott sorrendben egy társszervezet csatornához és konzorciumhoz való hozzáadásához: 

1.  A társszervezet ügyfeléből töltse fel a társszervezet MSP-ját az Azure Storage-ba.

      ```bash
      ./azhlf msp export toAzureStorage -f  $AZURE_FILE_CONNECTION_STRING -o $PEER_ORG_NAME
      ```
2.  Az orderer szervezet ügyfeléről töltse le a társszervezet MSP-ját az Azure Storage-ból. Ezután adja ki a parancsot, hogy hozzáadja a társszervezetet a csatornához és a konzorciumhoz.

      ```bash
      ./azhlf msp import fromAzureStorage -o $PEER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel join -c  $CHANNEL_NAME -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ./azhlf consortium join -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ```

3.  A rendelést kötő szervezet ügyfeléből töltse fel a rendelési kapcsolati profilt az Azure Storage-ba, hogy a társszervezet ezzel a kapcsolati profillal kapcsolódhat a rendelési csomópontokhoz.

      ```bash
      ./azhlf connectionProfile  export toAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ```

4.  A társszervezet ügyfeléről töltse le az orderer kapcsolati profilját az Azure Storage-ból. Ezután futtassa a parancsot társcsomópontok hozzáadásához a csatornában.

      ```bash
      ./azhlf connectionProfile  import fromAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel joinPeerNodes -o $PEER_ORG_NAME  -u $PEER_ADMIN_IDENTITY -c $CHANNEL_NAME --ordererOrg $ORDERER_ORG_NAME
      ```

Hasonlóképpen, ha további társszervezeteket szeretne hozzáadni a csatornához, frissítse a társkörnyezet változóit a szükséges társszervezetnek megfelelően, majd ismételje meg az 1–4. lépést.

### <a name="set-anchor-peers"></a>Horgonyviszonyok beállítása

A társszervezet ügyfeléről futtassa az parancsot a társszervezet horgonyhálózatának a megadott csatornán való beállítására.

>[!NOTE]
> A parancs futtatása előtt a konzorciumkezelési parancsokkal győződjön meg arról, hogy a csatornához hozzá van adva a társszervezet.

```bash
./azhlf channel setAnchorPeers -c $CHANNEL_NAME -p <anchorPeersList> -o $PEER_ORG_NAME -u $PEER_ADMIN_IDENTITY --ordererOrg $ORDERER_ORG_NAME
```

`<anchorPeersList>` A a társcsomópontok helytől elválasztott listája, amely horgony társként lesz beállítva. Például:

  - Állítsa be úgy, mintha csak a `<anchorPeersList>` `"peer1"` peer1 csomópontot szeretné horgony társként beállítani.
  - Állítsa be úgy, mintha a peer1 és a peer3 csomópontot is horgony `<anchorPeersList>` `"peer1" "peer3"` társként szeretné beállítani.

## <a name="chaincode-management-commands"></a>Üzletlánckód-kezelési parancsok

>[!NOTE]
> Mielőtt bármilyen lánckódművelettel elkezdené, győződjön meg arról, hogy végzett az ügyfélalkalmazás kezdeti beállításával.  

### <a name="set-the-chaincode-specific-environment-variables"></a>A chaincode-specifikus környezeti változók beállítása

```bash
# Peer organization name where the chaincode operation will be performed
ORGNAME=<PeerOrgName>
USER_IDENTITY="admin.$ORGNAME"  
# If you are using chaincode_example02 then set CC_NAME=â€œchaincode_example02â€
CC_NAME=<chaincodeName>  
# If you are using chaincode_example02 then set CC_VERSION=â€œ1â€ for validation
CC_VERSION=<chaincodeVersion>
# Language in which chaincode is written. Supported languages are 'node', 'golang', and 'java'  
# Default value is 'golang'  
CC_LANG=<chaincodeLanguage>  
# CC_PATH contains the path where your chaincode is placed. This is the absolute path to the chaincode project root directory.
# If you are using chaincode_example02 to validate then CC_PATH=â€œ/home/<username>/azhlfTool/samples/chaincode/src/chaincode_example02/goâ€
CC_PATH=<chaincodePath>  
# Channel on which chaincode will be instantiated/invoked/queried  
CHANNEL_NAME=<channelName>  
```

### <a name="install-chaincode"></a>A chaincode telepítése  

Futtassa a következő parancsot a lánckód társszervezeten való telepítéséhez.  

```bash
./azhlf chaincode install -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -l $CC_LANG -v $CC_VERSION  

```
A parancs a környezeti változóban beállított társszervezet összes társcsomópontra telepíti a `ORGNAME` chaincode-et. Ha a csatornában két vagy több társszervezet is van, és mindegyikre telepíteni szeretné a lánckódot, futtassa külön ezt a parancsot minden társszervezetnél.  

Kövesse az alábbi lépéseket:  

1.  Állítsa `ORGNAME` be a és a parancsot a `USER_IDENTITY` `peerOrg1` parancsnak megfelelően, majd `./azhlf chaincode install` futtassa.  
2.  Állítsa `ORGNAME` be a és a parancsot a `USER_IDENTITY` `peerOrg2` parancsnak megfelelően, majd `./azhlf chaincode install` futtassa.  

### <a name="instantiate-chaincode"></a>Lánckód példányának példánya  

A társ ügyfélalkalmazásból futtassa a következő parancsot a csatornán a lánckód példányosításához.  

```bash
./azhlf chaincode instantiate -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -v $CC_VERSION -c $CHANNEL_NAME -f <instantiateFunc> --args <instantiateFuncArgs>
```

Adja át a példányosítási függvény nevét és az argumentumok szóközöktől elválasztott listáját a `<instantiateFunc>` és `<instantiateFuncArgs>` a argumentumok számára. Ha például példányosulni chaincode_example02.go chaincode, állítsa a és a kódot `<instantiateFunc>` `init` a `<instantiateFuncArgs>` következőre: `"a" "2000" "b" "1000"` .

A jelölővel át is használhatja a gyűjtemény konfigurációs `--collections-config` JSON-fájlját. Vagy állítsa be az átmeneti argumentumokat a jelzővel a privát tranzakciókhoz használt lánckód `-t` példányosulatával.

Például:

```bash
./azhlf chaincode instantiate -c $CHANNEL_NAME -n $CC_NAME -v $CC_VERSION -o $ORGNAME -u $USER_IDENTITY --collections-config <collectionsConfigJSONFilePath>
./azhlf chaincode instantiate -c $CHANNEL_NAME -n $CC_NAME -v $CC_VERSION -o $ORGNAME -u $USER_IDENTITY --collections-config <collectionsConfigJSONFilePath> -t <transientArgs>
```

A rész a JSON-fájl elérési útja, amely a privát adatlánckód példányosítására `<collectionConfigJSONFilePath>` definiált gyűjteményeket tartalmazza. A mintagyűjtemény az *azhlfTool* könyvtárához viszonyított konfigurációs JSON-fájlját a következő elérési úton találja: `./samples/chaincode/src/private_marbles/collections_config.json` .
Érvényes `<transientArgs>` JSON-ként adja meg sztring formátumban. A speciális karaktereket escape-karakterrel kell kivédnie. Például: `'{\\\"asset\":{\\\"name\\\":\\\"asset1\\\",\\\"price\\\":99}}'`

> [!NOTE]
> Futtassa egyszer a parancsot a csatornában bármelyik társszervezetből. Miután sikeresen beküldi a tranzakciót a rendeléskezelőnek, a rendeléskezelő elosztja ezt a tranzakciót a csatorna összes társszervezete között. A lánckód ezután a csatorna összes társszervezetének társcsomópontján példányosul.  

### <a name="invoke-chaincode"></a>Lánckód meghívása  

Futtassa a következő parancsot a társszervezet ügyfeléről a chaincode függvény meghívására:  

```bash
./azhlf chaincode invoke -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <invokeFunc> -a <invokeFuncArgs>  
```

Adja át a meghívási függvény nevét és az argumentumok szóközöktől elválasztott listáját inâ€ `<invokeFunction>` âandà® âandâ® `<invokeFuncArgs>` â€ àrespectively. A chaincode_example02.go chaincode példáját folytatva, egy meghívási művelet végrehajtásához állítsa be a következőt: setà® `<invokeFunction>` â® âtoà® `invoke` â® â® âandà® `<invokeFuncArgs>` â® âto. `"a" "b" "10"`  

>[!NOTE]
> Futtassa egyszer a parancsot a csatornában bármelyik társszervezetből. Miután sikeresen beküldi a tranzakciót a rendeléskezelőnek, a rendeléskezelő elosztja ezt a tranzakciót a csatorna összes társszervezete között. A világ állapota ezután a csatorna összes társszervezetének összes társcsomópontján frissül.  


### <a name="query-chaincode"></a>Lekérdezési lánc kódja  

Futtassa a következő parancsot a chaincode lekérdezéséhez:  

```bash
./azhlf chaincode query -o $ORGNAME -p <endorsingPeers> -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <queryFunction> -a <queryFuncArgs> 
```
A társviszony-létesítők olyan társviszonyok, amelyeken telepítve van a chaincode, és a rendszer a tranzakciók végrehajtására hív meg. A beállításaként meg kell jelennie az aktuális `<endorsingPeers>` társszervezet társcsomópont-nevének. Listába sorolja egy adott lánckód és csatorna kombinációhoz a társ társokat szóközök elválasztva. Példa: `-p "peer1" "peer3"`.

Ha az *azhlfTool* használatával telepíti a chaincode-ot, adja át a társcsomópontneveket értékként a társ-argumentumnak. A lánckód az adott szervezet összes társcsomópontján telepítve van. 

Adja át a lekérdezési függvény nevét és az argumentumok szóközöktől elválasztott listáját inâ€ `<queryFunction>` âandâ® âandà€ `<queryFuncArgs>` â€ àspectively. A világállapotban az "a" érték lekérdezéséhez ismét a következőt kell figyelembe véve: à chaincode_example02.goâ® àchaincode, ha a világállapotban az "a" értékét kell lekérdezni: setâ€ â `<queryFunction>` â® âtoâ® à `query` ésâ€ â to `<queryArgs>` `"a"` .  

## <a name="troubleshoot"></a>Hibaelhárítás

### <a name="find-deployed-version"></a>Telepített verzió megkeresve

Futtassa az alábbi parancsokat a sablon üzembe helyezésének verziójának megkereséhez. Állítsa be a környezeti változókat annak az erőforráscsoportnak megfelelően, amelyben a sablon üzembe lett állítva.

```bash
SWITCH_TO_AKS_CLUSTER $AKS_CLUSTER_RESOURCE_GROUP $AKS_CLUSTER_NAME $AKS_CLUSTER_SUBSCRIPTION
kubectl describe pod fabric-tools -n tools | grep "Image:" | cut -d ":" -f 3
```

### <a name="patch-previous-version"></a>Az előző verzió javítás

Ha a 3.0.0-s verziónál régebbi sablonverziókban problémákat kell megoldania a lánckód futtatása során, kövesse az alábbi lépéseket a társcsomópontok javítással való javításához.

Töltse le a társtelepítési szkriptet.

```bash
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/scripts/patchPeerDeployment.sh -o patchPeerDeployment.sh; chmod 777 patchPeerDeployment.sh
```

Futtassa a szkriptet a következő paranccsal, és cserélje le a társ paramétereit.

```bash
source patchPeerDeployment.sh <peerOrgSubscription> <peerOrgResourceGroup> <peerOrgAKSClusterName>
```

Várjon, amíg az összes társcsomópont javításra vár. A következő paranccsal mindig ellenőrizheti a társcsomópontok állapotát a rendszerhéj különböző példányán.

```bash
kubectl get pods -n hlf
```

## <a name="support-and-feedback"></a>Támogatás és visszajelzés

Ha naprakész maradhat a blokkláncszolgáltatás-ajánlatokról és az Azure Blockchain mérnöki csapattól származó információkról, látogasson el a Azure Blockchain [blogjára.](https://azure.microsoft.com/blog/topics/blockchain/)

Ha termékkel kapcsolatos visszajelzést szeretne küldeni vagy új funkciókat szeretne igényelni, tegye közzé vagy szavazzon egy ötletre a [blokklánc azure-visszajelzési fórumán.](https://aka.ms/blockchainuservoice)

### <a name="community-support"></a>Közösségi támogatás

Lépjen kapcsolatba a Microsoft mérnökeivel és Azure Blockchain szakértőivel:

- [Microsoft Q&A lap](/answers/topics/azure-blockchain-workbench.html) 
   
  A blokkláncsablonok mérnöki támogatása az üzembe helyezési problémákra korlátozódik.
- [A Microsoft technikai közössége](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
- [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-blockchain-workbench)
