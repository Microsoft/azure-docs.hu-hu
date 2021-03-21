---
title: A Hyperledger Fabric Consortium üzembe helyezése az Azure Kubernetes Service-ben
description: Hyperledger Fabric Consortium-hálózat üzembe helyezése és konfigurálása az Azure Kubernetes Service-ben
ms.date: 03/01/2021
ms.topic: how-to
ms.reviewer: ravastra
ms.custom: contperf-fy21q3
ms.openlocfilehash: 42d16adbc5e6396c8d5d38176ac7681c712f4555
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102101103"
---
# <a name="deploy-hyperledger-fabric-consortium-on-azure-kubernetes-service"></a>A Hyperledger Fabric Consortium üzembe helyezése az Azure Kubernetes Service-ben

A Hyperledger Fabric az Azure Kubernetes Service (ak) sablon használatával üzembe helyezheti és konfigurálhatja a Hyperledger Fabric Consortium Network szolgáltatást az Azure-ban.

A cikk elolvasása után:

- A Hyperledger-hálót és a Hyperledger Fabric blockchain-hálózat építőelemeit alkotó összetevőket is jól ismeri.
- Ismerje meg, hogyan helyezhet üzembe és konfigurálhat egy Hyperledger Fabric Consortium-hálózatot az Azure Kubernetes Service-ben az éles környezetekben.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="choose-an-azure-blockchain-solution"></a>Azure Blockchain-megoldás kiválasztása

A megoldási sablon használata előtt hasonlítsa össze a forgatókönyvet az elérhető Azure Blockchain-beállítások gyakori használati eseteivel:

Beállítás | Szolgáltatási modell | Gyakori használati eset
-------|---------------|-----------------
Megoldássablonok | IaaS | A megoldási sablonok olyan Azure Resource Manager-sablonok, amelyekkel teljes körűen konfigurált blockchain hálózati topológiát lehet kiépíteni. A Sablonok Microsoft Azure számítási, hálózatkezelési és tárolási szolgáltatásokat telepítenek és konfigurálnak a blockchain. A megoldási sablonokat szolgáltatási szintű szerződés nélkül biztosítjuk. Támogatáshoz használja a [Microsoft Q&egy oldalát](/answers/topics/azure-blockchain-workbench.html) .
[Azure Blockchain Service](../service/overview.md) | PaaS | Az Azure Blockchain szolgáltatás előzetes verziója leegyszerűsíti a konzorciumi Blockchain hálózatok képződését, kezelését és irányítását. Az Azure Blockchain Service olyan megoldások esetében használható, amelyek a Pásti, a konzorciumok felügyeletére, vagy a szerződés és a tranzakciók adatvédelmet igénylik
[Azure Blockchain Workbench](../workbench/overview.md) | IaaS és Péter | Az Azure Blockchain Workbench előzetes verziója olyan Azure-szolgáltatások és-funkciók gyűjteménye, amelyek segítségével Blockchain-alkalmazásokat hozhat létre és helyezhet üzembe más szervezetekkel való üzleti folyamatok és adatmennyiségek megosztásához. Az Azure Blockchain Workbench használatával Blockchain-megoldást vagy egy Blockchain-alkalmazásra vonatkozó megvalósíthatósági koncepciót használhat. Az Azure Blockchain Workbench szolgáltatói szerződés nélkül biztosított. Támogatáshoz használja a [Microsoft Q&egy oldalát](/answers/topics/azure-blockchain-workbench.html) .

## <a name="deploy-the-orderer-and-peer-organization"></a>A sorrend és a társ-szervezet üzembe helyezése

A kezdéshez olyan Azure-előfizetésre van szükség, amely támogatja több virtuális gép és standard Storage-fiók telepítését. Ha nem rendelkezik Azure-előfizetéssel, [létrehozhat egy ingyenes Azure-fiókot](https://azure.microsoft.com/free/)is.

A Hyperledger háló hálózati összetevőinek üzembe helyezésének megkezdéséhez nyissa meg a [Azure Portal](https://portal.azure.com).

1. Válassza az **erőforrás létrehozása**  >  **Blockchain** elemet, majd keresse **meg a Hyperledger-hálót az Azure Kubernetes Service-ben (előzetes verzió)**.

2. Adja meg a projekt részleteit az **alapok** lapon.

    ![Az alapvető beállítások lapot megjelenítő képernyőkép.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-basics.png)

3. Adja meg a következő részleteket:
    - **Előfizetés**: válassza ki azt az előfizetést, ahová a Hyperledger háló hálózati összetevőit telepíteni szeretné.
    - **Erőforráscsoport**: hozzon létre egy új erőforráscsoportot, vagy válasszon ki egy meglévő üres erőforráscsoportot. Az erőforráscsoport a sablon részeként telepített összes erőforrást fogja tárolni.
    - **Régió**: válassza ki azt az Azure-régiót, ahol az Azure Kubernetes Service-fürtöt telepíteni szeretné a Hyperledger-háló összetevőihez. A sablon minden olyan régióban elérhető, ahol az AK elérhető. Válassza ki azt a régiót, ahol az előfizetése nem éri el a virtuális gép (VM) kvótájának korlátját.
    - **Erőforrás-előtag**: adjon meg egy előtagot a központilag telepített erőforrások elnevezéséhez. 6 karakternél rövidebbnek kell lennie, és a karakterek kombinációjának tartalmaznia kell a számokat és a betűket is.
4. A **háló beállításai** lapon adhatja meg a Hyperledger háló hálózati összetevőit, amelyeket telepíteni kíván.

    ![Képernyőkép, amely a háló beállításai lapot jeleníti meg.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-settings.png)

5. Adja meg a következő részleteket:
    - **Szervezet neve**: írja be a Hyperledger-háló szervezet nevét, amely a különböző adatsíkok-műveletekhez szükséges. Az üzembe helyezéshez a szervezet nevének egyedinek kell lennie.
    - **Háló hálózati összetevő**: válassza a szolgáltatás vagy **társ csomópontok** **rendezése** lehetőséget a beállítani kívánt blockchain hálózati összetevő alapján.
    - **Csomópontok száma**: a következő két típusú csomópont létezik:
        - **Rendezési szolgáltatás**: a főkönyvben a tranzakciók rendezéséhez felelős csomópontok. Válassza ki a hálózati hibatűrést biztosító csomópontok számát. A támogatott megrendelési csomópontok száma 3, 5 és 7.
        - **Társ-csomópontok**: a főkönyveket és az intelligens szerződéseket futtató csomópontok. A követelmények alapján 1 – 10 csomópontot is választhat.
    - **Társ-csomópont globális állapotának adatbázisa**: a társ-csomópontok globális állapotú adatbázisai. A LevelDB az alapértelmezett állapotú adatbázis, amely a társ csomópontban van beágyazva. Egyszerű kulcs/érték párokként tárolja a chaincode, és csak a kulcs, a kulcs tartomány és az összetett kulcsok lekérdezéseit támogatja. A CouchDB egy opcionális, alternatív állapotú adatbázis, amely támogatja a gazdag lekérdezéseket, ha a chaincode az adatértékek JSON-ként vannak modellezve. Ez a mező akkor jelenik meg, ha a **háló hálózati összetevő** legördülő listájában a **társ csomópontok** lehetőséget választja.
    - **FABRIC CA-Felhasználónév**: a háló hitelesítésszolgáltatója lehetővé teszi egy olyan kiszolgálói folyamat inicializálását és elindítását, amely a hitelesítésszolgáltatót üzemelteti. Lehetővé teszi az identitások és a tanúsítványok kezelését. A sablon részeként üzembe helyezett minden AK-fürthöz alapértelmezés szerint a Fabric CA Pod lesz. Adja meg a háló HITELESÍTÉSSZOLGÁLTATÓI hitelesítéshez használt felhasználónevet.
    - **FABRIC CA-jelszó**: adja meg a háló hitelesítésszolgáltatói hitelesítésének jelszavát.
    - **Jelszó megerősítése**: erősítse meg a háló hitelesítésszolgáltatói jelszavát.
    - **Tanúsítványok**: Ha saját főtanúsítványokat kíván használni a háló hitelesítésszolgáltató inicializálásához, akkor válassza a **Főtanúsítvány feltöltése** lehetőségre. Ellenkező esetben a Fabric CA automatikusan önaláírt tanúsítványokat hoz létre.
    - **Főtanúsítvány**: töltse fel a főtanúsítványt (nyilvános kulcs), amellyel a háló hitelesítésszolgáltatót inicializálni kell. A. PEM formátumú tanúsítványok támogatottak. A tanúsítványoknak érvényesnek és UTC-időzónának kell lenniük.
    - **Főtanúsítvány titkos kulcsa**: töltse fel a főtanúsítvány titkos kulcsát. Ha a. PEM tanúsítvánnyal rendelkezik, amely nyilvános és titkos kulccsal rendelkezik, töltse fel itt is.


6. Válassza az **AK-fürt beállításai** lapot az Azure Kubernetes szolgáltatás-fürt konfigurációjának definiálásához. Az AK-fürthöz különböző hüvelyek vannak konfigurálva a Hyperledger háló hálózati összetevőinek futtatásához. Az üzembe helyezett Azure-erőforrások a következők:

    - **Háló eszközei**: a Hyperledger-háló összetevőinek konfigurálásához felelős eszközök.
    - **Megrendelő/társ hüvelyek**: a Hyperledger háló hálózat csomópontjai.
    - **Proxy**: egy ngnix proxy Pod, amelyen keresztül az ügyfélalkalmazások kommunikálni tudnak az AK-fürttel.
    - **FABRIC CA**: a Fabric CA-t futtató Pod.
    - **PostgreSQL**: adatbázis-példány, amely karbantartja a háló hitelesítésszolgáltatói identitásait.
    - **Key Vault**: a Azure Key Vault szolgáltatás példánya, amelyet a rendszer a háló hitelesítésszolgáltatói hitelesítő adatainak és az ügyfél által biztosított főtanúsítványoknak a mentéséhez telepített. A tár a sablon központi telepítésének újrapróbálkozása esetén használatos a sablon mechanikaának kezeléséhez.
    - **Felügyelt lemez**: az Azure Managed Disks szolgáltatás olyan példánya, amely állandó tárolót biztosít a főkönyvhez és a társ-csomópont globális állapotú adatbázisához.
    - **Nyilvános IP-cím**: a fürttel folytatott kommunikációhoz üzembe helyezett AK-fürt végpontja.

    Adja meg a következő részleteket: 

    ![Képernyőkép, amely megjeleníti a K S fürt beállításait tartalmazó lapot.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-aks-cluster-settings-1.png)

    - **Kubernetes-fürt neve**: szükség esetén módosítsa az AK-fürt nevét. Ez a mező előre fel van töltve a megadott erőforrás-előtag alapján.
    - **Kubernetes verziója**: válassza ki a fürtön telepítendő Kubernetes verzióját. Az **alapok** lapon kiválasztott régió alapján előfordulhat, hogy az elérhető támogatott verziók változhatnak.
    - **DNS-előtag**: adjon meg egy tartománynévrendszer-(DNS-) nevet az AK-fürthöz. A DNS használatával csatlakozhat a Kubernetes API-hoz a tárolók kezelésekor a fürt létrehozása után.
    - **Csomópont mérete**: a Kubernetes csomópont méretéhez az Azure-ban elérhető virtuálisgép-készletezési egységek (SKU-EK) listájából választhat. Az optimális teljesítmény érdekében javasoljuk a standard DS3 v2 használatát.
    - **Csomópontok száma**: adja meg a fürtben telepítendő Kubernetes-csomópontok számát. Javasoljuk, hogy a csomópontok számát a **háló beállításai** lapon megadott Hyperledger-háló-csomópontok számával vagy annál nagyobb értékkel őrizze meg.
    - **Egyszerű szolgáltatásnév ügyfél-azonosítója**: adja meg egy meglévő szolgáltatásnév ügyfél-azonosítóját, vagy hozzon létre egy újat. Az AK-hitelesítéshez egyszerű szolgáltatásnév szükséges. Tekintse [meg az egyszerű szolgáltatásnév létrehozásának lépéseit](/powershell/azure/create-azure-service-principal-azureps#create-a-service-principal).
    - **Egyszerű szolgáltatásnév ügyfél titka**: adja meg az egyszerű szolgáltatásnév ügyfél-azonosítójában megadott szolgáltatásnév titkos kulcsát.
    - **Ügyfél titkos kulcsának megerősítése**: erősítse meg az egyszerű szolgáltatásnév ügyfél-titkos kulcsát.
    - **Tárolók figyelésének engedélyezése**: válassza az AK-figyelés engedélyezése lehetőséget, amely lehetővé teszi az AK-naplók számára a megadott log Analytics munkaterületre való leküldést.
    - **Log Analytics munkaterület**: a log Analytics munkaterület a figyelés engedélyezésekor létrehozott alapértelmezett munkaterülettel lesz feltöltve.

8. Válassza a **felülvizsgálat és létrehozás** lapot. Ez a lépés elindítja a megadott értékek érvényesítését.
9. Az érvényesítési fázisok után válassza a **Létrehozás** lehetőséget.

    Az üzembe helyezés általában 10 – 12 percet vesz igénybe. Az idő a megadott AK-csomópontok méretétől és számától függően változhat.
10. A sikeres üzembe helyezést követően értesítést kap az Azure-értesítéseken a jobb felső sarokban.
11. Válassza az **Ugrás az erőforráscsoporthoz** lehetőséget a sablon központi telepítésének részeként létrehozott összes erőforrás megtekintéséhez. Az összes erőforrás neve az **alapok** lapon megadott előtaggal fog kezdődni.

## <a name="build-the-consortium"></a>A konzorcium összeállítása

Ha a blockchain-konzorciumot a rendezési szolgáltatás és a társ-csomópontok üzembe helyezése után szeretné felépíteni, hajtsa végre a következő lépéseket a sorozatban. Az Azure Hyperledger Fabric-szkript (azhlf) segítséget nyújt a konzorcium beállításában, a csatorna létrehozásában és a chaincode műveletek végrehajtásában.

> [!NOTE]
> A rendszer frissítette az Azure Hyperledger Fabric (azhlf) parancsfájlt, amely további funkciókat biztosít. Ha a régi szkriptre szeretne hivatkozni, tekintse meg a következőt: [Tudnivalók a githubon](https://github.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/blob/master/consortiumScripts/README.md). Ez a szkript kompatibilis a Hyperledger Fabrictel az Azure Kubernetes Service template 2.0.0 és újabb verzióiban. A központi telepítés verziójának vizsgálatához kövesse a [Hibaelhárítás](#troubleshoot)című szakasz lépéseit.

> [!NOTE]
> A parancsfájl csak a bemutató, a fejlesztés és a tesztelési forgatókönyvek megkönnyítésére szolgál. A szkript által létrehozott csatorna és konzorcium rendelkezik alapszintű Hyperledger, amely egyszerűbbé teszi a bemutató-, fejlesztési és tesztelési forgatókönyveket. Az éles környezet beállítása esetén javasolt a Channel/Consortium Hyperledger-szabályzatok frissítése a szervezet megfelelőségi igényeinek megfelelően a natív Hyperledger-háló API-k használatával.


Az Azure Hyperledger Fabric-szkript futtatásához szükséges összes parancs az Azure bash parancssori felületén (CLI) keresztül hajtható végre. A ![ Azure Portal jobb felső sarkában található Hyperledger-hálón keresztül bejelentkezhet Azure Cloud Shellra az Azure Kubernetes Service template ](./media/hyperledger-fabric-consortium-azure-kubernetes-service/arrow.png) lehetőséggel. A parancssorba írja be a parancsot, `bash` majd válassza az ENTER billentyűt a bash CLI-re való váltáshoz, vagy válassza a **bash** elemet a Cloud Shell eszköztárból.

További információ: [Azure Cloud Shell](../../cloud-shell/overview.md) .

![Képernyőkép, amely a Azure Cloud Shell parancsait jeleníti meg.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-powershell.png)


Az alábbi képen egy olyan lépésenkénti folyamat látható, amellyel a rendszer létrehoz egy konzorciumot egy megrendelő szervezet és egy társ-szervezet között. A következő fejezetekben részletes parancsok jelennek meg a lépések végrehajtásához.

![A konzorcium létrehozási folyamatának ábrája.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/process-to-build-consortium-flow-chart.png)

A kezdeti beállítás befejezését követően az ügyfélalkalmazás használatával a következő műveleteket végezheti el:  

- Csatorna kezelése
- Konzorciumok kezelése
- Chaincode-kezelés

### <a name="download-client-application-files"></a>Ügyfél-alkalmazásfájlok letöltése

Az első beállítás az ügyfélalkalmazás fájljainak letöltése. Futtassa az alábbi parancsokat az összes szükséges fájl és csomag letöltéséhez:

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/azhlfToolSetup.sh | bash
cd azhlfTool
npm install
npm run setup
```

Ezek a parancsok a nyilvános GitHub-tárházból fogják betölteni az Azure Hyperledger Fabric-ügyfélalkalmazás kódját, majd betöltik az összes függő NPM-csomagot. A parancs sikeres végrehajtása után node_modules mappát láthat az aktuális könyvtárban. Az összes szükséges csomag betöltődik a node_modules mappába.

### <a name="set-up-environment-variables"></a>Környezeti változók beállítása

Az összes környezeti változó az Azure erőforrás-elnevezési konvenciót követi.

#### <a name="set-environment-variables-for-the-orderer-organizations-client"></a>Környezeti változók beállítása a megrendelő szervezet ügyfeléhez

```bash
ORDERER_ORG_SUBSCRIPTION=<ordererOrgSubscription>
ORDERER_ORG_RESOURCE_GROUP=<ordererOrgResourceGroup>
ORDERER_ORG_NAME=<ordererOrgName>
ORDERER_ADMIN_IDENTITY="admin.$ORDERER_ORG_NAME"
CHANNEL_NAME=<channelName>
```

#### <a name="set-environment-variables-for-the-peer-organizations-client"></a>Környezeti változók beállítása a társ szervezet ügyfeléhez

```bash
PEER_ORG_SUBSCRIPTION=<peerOrgSubscritpion>
PEER_ORG_RESOURCE_GROUP=<peerOrgResourceGroup>
PEER_ORG_NAME=<peerOrgName>
PEER_ADMIN_IDENTITY="admin.$PEER_ORG_NAME"
CHANNEL_NAME=<channelName>
```

A konzorciumban található társ-szervezetek száma alapján előfordulhat, hogy meg kell ismételnie a társ-parancsokat, és ennek megfelelően kell beállítania a környezeti változót.

#### <a name="set-environment-variables-for-an-azure-storage-account"></a>Környezeti változók beállítása Azure Storage-fiókhoz

```bash
STORAGE_SUBSCRIPTION=<subscriptionId>
STORAGE_RESOURCE_GROUP=<azureFileShareResourceGroup>
STORAGE_ACCOUNT=<azureStorageAccountName>
STORAGE_LOCATION=<azureStorageAccountLocation>
STORAGE_FILE_SHARE=<azureFileShareName>
```

Azure Storage-fiók létrehozásához használja a következő parancsokat. Ha már rendelkezik Azure Storage-fiókkal, hagyja ki ezt a lépést.

```bash
az account set --subscription $STORAGE_SUBSCRIPTION
az group create -l $STORAGE_LOCATION -n $STORAGE_RESOURCE_GROUP
az storage account create -n $STORAGE_ACCOUNT -g  $STORAGE_RESOURCE_GROUP -l $STORAGE_LOCATION --sku Standard_LRS
```

Az alábbi parancsokkal hozhat létre fájlmegosztást az Azure Storage-fiókban. Ha már van fájlmegosztási fájlja, hagyja ki ezt a lépést.

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
az storage share create  --account-name $STORAGE_ACCOUNT  --account-key $STORAGE_KEY  --name $STORAGE_FILE_SHARE
```

Használja az alábbi parancsokat egy Azure-fájlmegosztás kapcsolódási karakterláncának létrehozásához.

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
SAS_TOKEN=$(az storage account generate-sas --account-key $STORAGE_KEY --account-name $STORAGE_ACCOUNT --expiry `date -u -d "1 day" '+%Y-%m-%dT%H:%MZ'` --https-only --permissions lruwd --resource-types sco --services f | tr -d '"')
AZURE_FILE_CONNECTION_STRING=https://$STORAGE_ACCOUNT.file.core.windows.net/$STORAGE_FILE_SHARE?$SAS_TOKEN

```

### <a name="import-an-organization-connection-profile-admin-user-identity-and-msp"></a>Szervezeti kapcsolatok profiljának, rendszergazdai felhasználói identitásának és az MSP-nek a importálása

A következő parancsokkal beolvashatja a szervezet kapcsolódási profilját, a rendszergazdai felhasználói identitást és a felügyelt szolgáltatót (MSP) az Azure Kubernetes Service-fürtből, és tárolhatja ezeket az identitásokat az ügyfélalkalmazás helyi tárolójában. Helyi tároló például a *azhlfTool/Stores* könyvtár.

A rendezési szervezet számára:

```bash
./azhlf adminProfile import fromAzure -o $ORDERER_ORG_NAME -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION
./azhlf connectionProfile import fromAzure -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION -o $ORDERER_ORG_NAME   
./azhlf msp import fromAzure -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION -o $ORDERER_ORG_NAME
```

A társ-szervezet számára:

```bash
./azhlf adminProfile import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
./azhlf connectionProfile import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
./azhlf msp import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
```

### <a name="create-a-channel"></a>Csatorna létrehozása

A rendezési szervezet ügyfeléről a következő parancs használatával hozzon létre egy csatornát, amely csak a rendezési szervezetet tartalmazza.  

```bash
./azhlf channel create -c $CHANNEL_NAME -u $ORDERER_ADMIN_IDENTITY -o $ORDERER_ORG_NAME
```

### <a name="add-a-peer-organization-for-consortium-management"></a>Társ-szervezet hozzáadása a konzorciumok felügyeletéhez

>[!NOTE]
> A konzorciumi művelet megkezdése előtt győződjön meg arról, hogy elkészült az ügyfélalkalmazás kezdeti beállításával.  

A megadott sorrendben futtassa a következő parancsokat egy társ-szervezet hozzáadásához egy csatornában és konzorciumban: 

1.  A társ-szervezet ügyfelétől töltse fel a társ-szervezet MSP-adatait az Azure Storage-ba.

      ```bash
      ./azhlf msp export toAzureStorage -f  $AZURE_FILE_CONNECTION_STRING -o $PEER_ORG_NAME
      ```
2.  Töltse le az Azure Storage-ból a társ-szervezet MSP-fájlját az orderer-szervezet ügyfelétől. Ezután adja ki a parancsot a társ-szervezet hozzáadásához a csatornában és a konzorciumban.

      ```bash
      ./azhlf msp import fromAzureStorage -o $PEER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel join -c  $CHANNEL_NAME -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ./azhlf consortium join -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ```

3.  Töltse fel a sorrendet a rendezési szervezet ügyfeléről az Azure Storage-ban, hogy a társ szervezet a kapcsolati profil használatával csatlakozhasson a sorrendi csomópontokhoz.

      ```bash
      ./azhlf connectionProfile  export toAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ```

4.  A társ-szervezet ügyfelétől töltse le a megrendelő profilját az Azure Storage-ból. Ezután futtassa a parancsot a csatorna egyenrangú csomópontjainak hozzáadásához.

      ```bash
      ./azhlf connectionProfile  import fromAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel joinPeerNodes -o $PEER_ORG_NAME  -u $PEER_ADMIN_IDENTITY -c $CHANNEL_NAME --ordererOrg $ORDERER_ORG_NAME
      ```

Hasonlóképpen, ha további társközi szervezeteket szeretne hozzáadni a csatornához, frissítse a társ-környezeti változókat a szükséges társ-szervezet szerint, és ismételje meg az 1 – 4. lépést.

### <a name="set-anchor-peers"></a>Horgony társak beállítása

A társ-szervezet ügyfelétől futtassa a parancsot, hogy a megadott csatornán állítsa be a társ szervezethez tartozó horgony társait.

>[!NOTE]
> A parancs futtatása előtt győződjön meg arról, hogy a társ-szervezet hozzá van adva a csatornához a konzorcium-felügyeleti parancsok használatával.

```bash
./azhlf channel setAnchorPeers -c $CHANNEL_NAME -p <anchorPeersList> -o $PEER_ORG_NAME -u $PEER_ADMIN_IDENTITY --ordererOrg $ORDERER_ORG_NAME
```

`<anchorPeersList>` egy szóközzel elválasztott lista a társ-csomópontok számára. Például:

  - Állítsa be `<anchorPeersList>` úgy, `"peer1"` hogy csak a peer1 csomópontot adja meg a horgony társként.
  - Állítsa be `<anchorPeersList>` úgy, `"peer1" "peer3"` hogy a peer1 és a peer3 csomópontokat is horgonyként adja meg.

## <a name="chaincode-management-commands"></a>Chaincode-kezelési parancsok

>[!NOTE]
> A chaincode művelet megkezdése előtt győződjön meg arról, hogy végrehajtotta az ügyfélalkalmazás kezdeti beállítását.  

### <a name="set-the-chaincode-specific-environment-variables"></a>Chaincode-specifikus környezeti változók beállítása

```bash
# Peer organization name where the chaincode operation will be performed
ORGNAME=<PeerOrgName>
USER_IDENTITY="admin.$ORGNAME"  
# If you are using chaincode_example02 then set CC_NAME=“chaincode_example02”
CC_NAME=<chaincodeName>  
# If you are using chaincode_example02 then set CC_VERSION=“1” for validation
CC_VERSION=<chaincodeVersion>
# Language in which chaincode is written. Supported languages are 'node', 'golang', and 'java'  
# Default value is 'golang'  
CC_LANG=<chaincodeLanguage>  
# CC_PATH contains the path where your chaincode is placed. This is the absolute path to the chaincode project root directory.
# If you are using chaincode_example02 to validate then CC_PATH=“/home/<username>/azhlfTool/samples/chaincode/src/chaincode_example02/go”
CC_PATH=<chaincodePath>  
# Channel on which chaincode will be instantiated/invoked/queried  
CHANNEL_NAME=<channelName>  
```

### <a name="install-chaincode"></a>A chaincode telepítése  

Futtassa a következő parancsot a chaincode telepítéséhez a társ-szervezetben.  

```bash
./azhlf chaincode install -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -l $CC_LANG -v $CC_VERSION  

```
A parancs a környezeti változóban beállított társ-szervezeti csomópont összes chaincode telepíti a-t `ORGNAME` . Ha két vagy több társközi szervezet található a csatornán, és az összes chaincode telepíteni kívánja, futtassa ezt a parancsot minden egyes társ-szervezetnél.  

Kövesse az alábbi lépéseket:  

1.  Állítsa be `ORGNAME` , majd `USER_IDENTITY` `peerOrg1` futtassa a `./azhlf chaincode install` parancsot.  
2.  Állítsa be `ORGNAME` , majd `USER_IDENTITY` `peerOrg2` futtassa a `./azhlf chaincode install` parancsot.  

### <a name="instantiate-chaincode"></a>Chaincode példányának példányai  

A társ-ügyfélalkalmazás használatával futtassa a következő parancsot a chaincode a csatornán való létrehozásához.  

```bash
./azhlf chaincode instantiate -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -v $CC_VERSION -c $CHANNEL_NAME -f <instantiateFunc> --args <instantiateFuncArgs>
```

Adja át a (z) és a (z) és a (z) argumentumok példányának nevét és szóközzel tagolt listáját `<instantiateFunc>` `<instantiateFuncArgs>` . Például chaincode_example02. go chaincode létrehozásához állítsa a következőre: `<instantiateFunc>` `init` és `<instantiateFuncArgs>` to `"a" "2000" "b" "1000"` .

A gyűjtemény konfigurációs JSON-fájlját a jelző használatával is átadhatja `--collections-config` . Vagy állítsa be az átmeneti argumentumokat a jelző használatával a `-t` privát tranzakciókhoz használt chaincode-példányok létrehozásakor.

Például:

```bash
./azhlf chaincode instantiate -c $CHANNEL_NAME -n $CC_NAME -v $CC_VERSION -o $ORGNAME -u $USER_IDENTITY --collections-config <collectionsConfigJSONFilePath>
./azhlf chaincode instantiate -c $CHANNEL_NAME -n $CC_NAME -v $CC_VERSION -o $ORGNAME -u $USER_IDENTITY --collections-config <collectionsConfigJSONFilePath> -t <transientArgs>
```

Ez a `<collectionConfigJSONFilePath>` rész a privát adatchaincode-példányhoz definiált gyűjteményeket tartalmazó JSON-fájl elérési útja. A *azhlfTool* könyvtárhoz viszonyított minta-gyűjtemény konfigurációs JSON-fájlját a következő elérési úton találja: `./samples/chaincode/src/private_marbles/collections_config.json` .
`<transientArgs>`Érvényes JSON-ként adja át karakterlánc formátumúként. Escape bármely speciális karakter. Például: `'{\\\"asset\":{\\\"name\\\":\\\"asset1\\\",\\\"price\\\":99}}'`

> [!NOTE]
> Futtassa a parancsot egyszer a Channel egyik társ-munkahelyéről. Miután sikeresen elküldte a tranzakciót a megrendelő számára, a megrendelő elosztja ezt a tranzakciót a csatorna összes társ-szervezete számára. A Chaincode ezt követően a csatornán lévő összes társ-csomóponton létrehozza a rendszer.  

### <a name="invoke-chaincode"></a>Chaincode meghívása  

A társ-szervezet ügyfelétől futtassa a következő parancsot a chaincode függvény meghívásához:  

```bash
./azhlf chaincode invoke -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <invokeFunc> -a <invokeFuncArgs>  
```

Adja meg a (z) és a (z) és a (z  `<invokeFunction>`    `<invokeFuncArgs>`   ) függvény meghívása a függvények neve és a szóközök Folytassa a chaincode_example02. go chaincode-példával a Meghívási művelet elvégzéséhez a  `<invokeFunction>`    `invoke`   és  `<invokeFuncArgs>`   a `"a" "b" "10"` értékre.  

>[!NOTE]
> Futtassa a parancsot egyszer a Channel egyik társ-munkahelyéről. Miután sikeresen elküldte a tranzakciót a megrendelő számára, a megrendelő elosztja ezt a tranzakciót a csatorna összes társ-szervezete számára. A rendszer ezután frissíti a globális állapotot a csatorna összes társ-csomópontján.  


### <a name="query-chaincode"></a>Chaincode lekérdezése  

Futtassa a következő parancsot a chaincode lekérdezéséhez:  

```bash
./azhlf chaincode query -o $ORGNAME -p <endorsingPeers> -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <queryFunction> -a <queryFuncArgs> 
```
A társak jóváhagyása olyan társak, ahol a chaincode telepítve van, és a rendszer a tranzakciók végrehajtásához hívja. Be kell állítania `<endorsingPeers>` a társ-csomópontok nevét az aktuális társ-szervezetből. Egy adott chaincode és csatorna-kombinációhoz tartozó, szóközökkel elválasztott társítások listázása. Példa: `-p "peer1" "peer3"`.

Ha a *azhlfTool* -t használja a chaincode telepítéséhez, adja át a társ-csomópontok nevét a jóváhagyó társ argumentum értékeként. A Chaincode az adott szervezet minden társ-csomópontjára telepítve van. 

Adja át a lekérdezési függvény nevét és az argumentumok szóközzel tagolt listáját  `<queryFunction>`    `<queryFuncArgs>`   . Ismételje meg a chaincode_example02. go chaincode hivatkozásként, hogy lekérdezze az "a" értéket a globális állapotban, a  `<queryFunction>`   és a értékre  `query`  `<queryArgs>` `"a"` .  

## <a name="troubleshoot"></a>Hibaelhárítás

### <a name="find-deployed-version"></a>Telepített verzió keresése

Futtassa a következő parancsokat a sablon központi telepítésének megkereséséhez. Állítsa be a környezeti változókat azon erőforráscsoport szerint, amelyben a sablon telepítve lett.

```bash
SWITCH_TO_AKS_CLUSTER $AKS_CLUSTER_RESOURCE_GROUP $AKS_CLUSTER_NAME $AKS_CLUSTER_SUBSCRIPTION
kubectl describe pod fabric-tools -n tools | grep "Image:" | cut -d ":" -f 3
```

### <a name="patch-previous-version"></a>Korábbi verzió javítása

Ha olyan problémák merülnek fel, amelyek a chaincode futtatásával kapcsolatos problémákat tapasztalnak a v 3.0.0 alatt lévő sablonok valamelyik központi telepítésében, kövesse az alábbi lépéseket a társ-csomópontok javításával történő javításához.

Töltse le a társ-telepítési parancsfájlt.

```bash
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/scripts/patchPeerDeployment.sh -o patchPeerDeployment.sh; chmod 777 patchPeerDeployment.sh
```

Futtassa a szkriptet a következő paranccsal, és cserélje le a társának paramétereit.

```bash
source patchPeerDeployment.sh <peerOrgSubscription> <peerOrgResourceGroup> <peerOrgAKSClusterName>
```

Várja meg, amíg az összes társ-csomópontot javítani szeretné. A következő parancs használatával mindig ellenőrizhető a társ-csomópontok állapota a rendszerhéj különböző példányaiban.

```bash
kubectl get pods -n hlf
```

## <a name="support-and-feedback"></a>Támogatás és visszajelzés

Az Azure Blockchain Engineering csapata blockchain-ajánlatait és információit az [Azure blockchain blogjában](https://azure.microsoft.com/blog/topics/blockchain/)tekintheti meg.

Ha visszajelzést szeretne küldeni, vagy új szolgáltatásokat szeretne kérni, tegye közzé vagy szavazzon a [blockchain készült Azure feedback fórumán](https://aka.ms/blockchainuservoice)keresztül.

### <a name="community-support"></a>Közösségi támogatás

Folytassa a Microsoft-mérnökökkel és az Azure Blockchain közösségi szakértőivel:

- [A Microsoft Q&egy oldalt](/answers/topics/azure-blockchain-workbench.html) 
   
  A blockchain-sablonok mérnöki támogatása az üzembe helyezési problémákra korlátozódik.
- [Microsoft technikai Közösség](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
- [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-blockchain-workbench)
