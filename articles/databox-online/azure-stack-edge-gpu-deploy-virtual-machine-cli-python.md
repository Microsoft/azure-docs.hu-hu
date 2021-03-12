---
title: Virtuális gépek üzembe helyezése a Azure Stack Edge Pro-eszköz GPU-n keresztül az Azure CLI és a Python használatával
description: Ismerteti, hogyan lehet virtuális gépeket (VM-ket) létrehozni és felügyelni egy Azure Stack Edge Pro GPU-eszközön az Azure CLI és a Python használatával.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/04/2021
ms.author: alkohli
ms.openlocfilehash: 43853f2eba05794540a72fe64e748fcafd9073d2
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "102638156"
---
# <a name="deploy-vms-on-your-azure-stack-edge-pro-gpu-device-using-azure-cli-and-python"></a>Virtuális gépek üzembe helyezése Azure Stack Edge Pro GPU-eszközön az Azure CLI és a Python használatával

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

[!INCLUDE [azure-stack-edge-gateway-deploy-virtual-machine-overview](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-overview.md)]

Ez az oktatóanyag leírja, hogyan hozhat létre és kezelhet virtuális gépeket az Azure Stack Edge Pro-eszközön az Azure parancssori felület (CLI) és a Python használatával.

## <a name="vm-deployment-workflow"></a>Virtuális gép üzembe helyezésének munkafolyamata

Az üzembe helyezési munkafolyamat a következő ábrán látható.

![Virtuális gép üzembe helyezésének munkafolyamata](media/azure-stack-edge-gpu-deploy-virtual-machine-powershell/vm-workflow-r.svg)

Az üzembe helyezési munkafolyamat magas szintű összefoglalása a következő:

1. Kapcsolódás Azure Resource Managerhoz
2. Erőforráscsoport létrehozása
3. Tárfiók létrehozása
4. BLOB URI hozzáadása a Hosts fájlhoz
5. Tanúsítványok telepítése
6. VHD feltöltése
7. Felügyelt lemezek létrehozása a VHD-ből
8. Virtuálisgép-rendszerkép létrehozása a rendszerkép által felügyelt lemezről
9. Virtuális gép létrehozása korábban létrehozott erőforrásokkal
10. Virtuális hálózat létrehozása
11. VNIC létrehozása a VNet alhálózati azonosító használatával

A munkafolyamat-diagram részletes ismertetését lásd: [virtuális gépek üzembe helyezése Azure stack Edge Pro-eszközön Azure PowerShell használatával](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md). További információ a Azure Resource Managerhoz való kapcsolódásról: [kapcsolódás Azure Resource Manager a Azure PowerShell használatával](azure-stack-edge-gpu-connect-resource-manager.md).

## <a name="prerequisites"></a>Előfeltételek

Mielőtt megkezdi a virtuális gép létrehozását és felügyeletét az Azure CLI és a Python használatával a Azure Stack Edge Pro-eszközön, meg kell győződnie arról, hogy végrehajtotta az alábbi lépéseket:

1. Az Azure Stack Edge Pro-eszköz hálózati beállításait az [1. lépés: Azure stack Edge Pro-eszköz konfigurálása](azure-stack-edge-gpu-connect-resource-manager.md#step-1-configure-azure-stack-edge-pro-device)című cikkben leírtak szerint végezte el.

2. Hálózati adapter engedélyezése a számítási feladatokhoz. Ez a hálózati adapter IP-címe virtuális kapcsoló létrehozásához használatos a virtuális gép telepítéséhez. Az alábbi lépések végigvezetik a folyamaton:

    1. Ugrás a **számítási** feladatokra. Válassza ki azt a hálózati adaptert, amelyet a virtuális kapcsoló létrehozásához használni fog.

        > [!IMPORTANT] 
        > Csak egy portot lehet beállítani a számítási feladatokhoz.

    2. A hálózati adapteren engedélyezze a számítást. Azure Stack Edge Pro létrehoz és felügyel egy, az adott hálózati adapternek megfelelő virtuális kapcsolót.

    <!--If you decide to use another network interface for compute, make sure that you:

    - Delete all the VMs that you have deployed using Azure Resource Manager.

    - Delete all virtual network interfaces and the virtual network associated with this network interface.

    - You can now enable another network interface for compute.-->

3. Létrehozta és telepítette az összes tanúsítványt a Azure Stack Edge Pro-eszközön és az ügyfél megbízható tárolójában. Kövesse a [2. lépés: tanúsítványok létrehozása és telepítése](azure-stack-edge-gpu-connect-resource-manager.md#step-2-create-and-install-certificates)című témakörben ismertetett eljárást.

4. Létrehozott egy Base-64 kódolású *. cer* tanúsítványt (PEM formátumban) az Azure stack Edge Pro-eszközhöz. Ez a tanúsítvány már fel van töltve aláírási láncként az eszközön, és telepítve van az ügyfél megbízható legfelső szintű tárolójába. Ennek a tanúsítványnak a *PEM* -formátumban is szükség van a Python működéséhez ezen az ügyfélen.

    Alakítsa át ezt a tanúsítványt `pem` a formátumra a `certutil` parancs használatával. Ezt a parancsot a tanúsítványt tartalmazó könyvtárba kell futtatnia.

    ```powershell
    certutil.exe <SourceCertificateName.cer> <DestinationCertificateName.pem>
    ```
    Az alábbi példa a parancs használatát mutatja be:

    ```powershell
    PS C:\Certificates> certutil.exe -encode aze-root.cer aze-root.pem
    Input Length = 2150
    Output Length = 3014
    CertUtil: -encode command completed successfully.
    PS C:\Certificates>
    ```    
    Ezt később is hozzáadja `pem` a Python áruházhoz.

5. Az eszköz IP-címét a **hálózat** lapján, az eszköz helyi webes felületén kell hozzárendelni. Adja hozzá ezt az IP-címet a következőhöz:

    - A gazdagép fájlja az ügyfélen, vagy
    - A DNS-kiszolgáló konfigurációja
    
    > [!IMPORTANT]
    > Javasoljuk, hogy módosítsa a DNS-kiszolgáló konfigurációját a végpont nevének feloldásához.

    1. Indítsa el a **jegyzettömböt** rendszergazdaként (rendszergazdai jogosultság szükséges a fájl mentéséhez), majd nyissa meg a következő helyen található **hosts** fájlt: `C:\Windows\System32\Drivers\etc` .
    
        ![Windows Intéző – fájl](media/azure-stack-edge-gpu-connect-resource-manager/hosts-file.png)
    
    2. Adja hozzá a következő bejegyzéseket a **gazdagépek** fájljához a megfelelő értékekkel helyettesítve az eszközön:
    
        ```
        <Device IP> login.<appliance name>.<DNS domain>
        <Device IP> management.<appliance name>.<DNS domain>
        <Device IP> <storage name>.blob.<appliance name>.<DNS domain>
        ```
    3. Használja az alábbi képet a hivatkozáshoz. Mentse a **hosts** fájlt.

        ![tároló fájl a Jegyzettömbben](media/azure-stack-edge-gpu-deploy-virtual-machine-cli-python/hosts-screenshot-boxed.png)

6. [Töltse le az](https://aka.ms/ase-vm-python) ebben az eljárásban használt Python-szkriptet.

## <a name="step-1-set-up-azure-clipython-on-the-client"></a>1. lépés: az Azure CLI/Python beállítása az ügyfélen

### <a name="verify-profile-and-install-azure-cli"></a>Profil ellenőrzése és az Azure CLI telepítése

<!--1. Verify the API profile of the client and identify which version of the modules and libraries to include on your client. In this example, the client system will be running Azure Stack 1904 or later. For more information, see [Azure Resource Manager API profiles](/azure-stack/user/azure-stack-version-profiles?view=azs-1908&preserve-view=true#azure-resource-manager-api-profiles).-->

1. Telepítse az Azure CLI-t az ügyfélre. Ebben a példában az Azure CLI-2.0.80 telepítve lett. Az Azure CLI verziójának ellenőrzéséhez futtassa a `az --version` parancsot.

    A fenti parancs kimenete a következő:

    ```output
    PS C:\windows\system32> az --version
    azure-cli                         2.0.80
    
    command-modules-nspkg              2.0.3
    core                              2.0.80
    nspkg                              3.0.4
    telemetry                          1.0.4
    Extensions:
    azure-cli-iot-ext                  0.7.1
    
    Python location 'C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\python.exe'
    Extensions directory 'C:\.azure\cliextensions'
    
    Python (Windows) 3.6.6 (v3.6.6:4cf1f54eb7, Jun 27 2018, 02:47:15) [MSC v.1900 32 bit (Intel)]
    
    Legal docs and information: aka.ms/AzureCliLegal
    
    Your CLI is up-to-date.
    
    Please let us know how we are doing: https://aka.ms/clihats
    PS C:\windows\system32>
    ```

    Ha nem rendelkezik Azure CLI-vel, töltse le és [telepítse az Azure CLI](/cli/azure/install-azure-cli-windows)-t Windows rendszeren. Az Azure CLI-t a Windows parancssorból vagy a Windows PowerShell használatával futtathatja.

2. Jegyezze fel a parancssori felület Python-helyét. Az Azure CLI-hez készült megbízható Főtanúsítvány-tároló helyének meghatározásához szükség van a Python-helyre.

3. A cikkben használt minta parancsfájl futtatásához a következő Python-függvénytár-verziókra lesz szüksége:

    ```powershell
    azure-common==1.1.23
    azure-mgmt-resource==2.1.0
    azure-mgmt-network==2.7.0
    azure-mgmt-compute==5.0.0
    azure-mgmt-storage==1.5.0
    azure-storage-blob==1.2.0rc1
    haikunator
    msrestazure==0.6.2
    ```
    A verziók telepítéséhez futtassa a következő parancsot:

    ```powershell
    .\python.exe -m pip install haikunator
    ```

    A következő minta kimenet a Haikunator telepítését mutatja be:

    ```output
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> .\python.exe -m pip install haikunator

    Collecting haikunator
      Downloading https://files.pythonhosted.org/packages/43/fa/130968f1a1bb1461c287b9ff35c630460801783243acda2cbf3a4c5964a5/haikunator-2.1.0-py2.py3-none-any.whl
    
    Installing collected packages: haikunator
    Successfully installed haikunator-2.1.0
    You are using pip version 10.0.1, however version 20.0.1 is available.
    You should consider upgrading using the 'python -m pip install --upgrade pip' command.
    
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> 
    ```

    A következő minta kimenet a pip telepítését mutatja be `msrestazure` : 
    
    ```output
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> .\python.exe -m pip install msrestazure==0.6.2
    Requirement already satisfied: msrestazure==0.6.2 in c:\program files (x86)\microsoft sdks\azure\cli2\lib\site-packages (0.6.2)
    Requirement already satisfied: msrest<2.0.0,>=0.6.0 in c:\program files (x86)\microsoft sdks\azure\cli2\lib\site-packages (from msrestazure==0.6.2) (0.6.10)
    === CUT ===========================  CUT ==================================
    Requirement already satisfied: cffi!=1.11.3,>=1.8 in c:\program files (x86)\microsoft sdks\azure\cli2\lib\site-packages (from cryptography>=1.1.0->adal<2.0.0,>=0.6.0->msrestazure==0.6.2) (1.13.2)
    Requirement already satisfied: pycparser in c:\program files (x86)\microsoft sdks\azure\cli2\lib\site-packages (from cffi!=1.11.3,>=1.8->cryptography>=1.1.0->adal<2.0.0,>=0.6.0->msrestazure==0.6.2) (2.18)
    You are using pip version 10.0.1, however version 20.0.1 is available.
    You should consider upgrading using the 'python -m pip install --upgrade pip' command.
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ```

### <a name="trust-the-azure-stack-edge-pro-ca-root-certificate"></a>Az Azure Stack Edge Pro HITELESÍTÉSSZOLGÁLTATÓI főtanúsítványának megbízhatósága

1. Keresse meg a tanúsítvány helyét a gépen. A hely változhat attól függően, hogy hol telepítette `az cli` . Futtassa a Windows PowerShellt rendszergazdaként. Váltson arra a elérési útra, ahol a `az cli` telepített Python: `C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\python.exe` .

    A tanúsítvány helyének lekéréséhez írja be a következő parancsot:

    ```powershell
    .\python -c "import certifi; print(certifi.where())"
    ```
    
    A parancsmag a tanúsítvány helyét adja vissza, az alább látható módon:  
        
    ```output
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> .\python -c "import certifi; print(certifi.where())"
    C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\certifi\cacert.pem
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ```
      
    Jegyezze fel ezt a helyet, mert később használni fogja – `C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\certifi\cacert.pem`

2. A meglévő Python-tanúsítványhoz való hozzáfűzéssel bízza meg a Azure Stack Edge Pro HITELESÍTÉSSZOLGÁLTATÓI főtanúsítványát. Adja meg az elérési utat, ahová a PEM-tanúsítványt korábban mentette.

    ```powershell
    $pemFile = "<Path to the pem format certificate>"
    ```
    Egy példa elérési útja "C:\VM-scripts\rootteam3device.pem" lenne.
    
    Ezután írja be a következő parancsokat a Windows PowerShellbe:

    ```powershell
    $root = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
        $root.Import($pemFile)
        
    Write-Host "Extracting required information from the cert file"
    $md5Hash    = (Get-FileHash -Path $pemFile -Algorithm MD5).Hash.ToLower()
    $sha1Hash   = (Get-FileHash -Path $pemFile -Algorithm SHA1).Hash.ToLower()
    $sha256Hash = (Get-FileHash -Path $pemFile -Algorithm SHA256).Hash.ToLower()

    $issuerEntry  = [string]::Format("# Issuer: {0}", $root.Issuer)
    $subjectEntry = [string]::Format("# Subject: {0}", $root.Subject)
    $labelEntry   = [string]::Format("# Label: {0}", $root.Subject.Split('=')[-1])
    $serialEntry  = [string]::Format("# Serial: {0}", $root.GetSerialNumberString().ToLower())
    $md5Entry = [string]::Format("# MD5 Fingerprint: {0}", $md5Hash)
    $sha1Entry= [string]::Format("# SHA1 Fingerprint: {0}", $sha1Hash)
    $sha256Entry  = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
    $certText = (Get-Content -Path $pemFile -Raw).ToString().Replace("`r`n","`n")
    
    $rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
    $serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText
    
    Write-Host "Adding the certificate content to Python Cert store"
    Add-Content "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem" $rootCertEntry
    
    Write-Host "Python Cert store was updated to allow the Azure Stack Edge Pro CA root certificate"
    ```
    
### <a name="connect-to-azure-stack-edge-pro"></a>Kapcsolódás Azure Stack Edge Pro-hoz

1. A parancs futtatásával regisztrálja Azure Stack Edge Pro-környezetét `az cloud register` .

    Bizonyos helyzetekben a közvetlen kimenő internetkapcsolatot egy proxyn vagy tűzfalon keresztül irányítjuk, amely kikényszeríti az SSL-elfogást. Ezekben az esetekben az az Cloud Register parancs hibát jelez, például \" nem tudja lekérni a végpontokat a felhőből. \" A hiba megkerüléséhez állítsa be a következő környezeti változókat a Windows PowerShellben:

    ```powershell
    $ENV:AZURE_CLI_DISABLE_CONNECTION_VERIFICATION = 1 
    $ENV:ADAL_PYTHON_SSL_NO_VERIFY = 1
    ```

2. Adja meg a környezeti változókat a parancsfájlhoz Azure Resource Manager végponthoz, az erőforrások létrehozásának helyét, valamint a forrás VHD helyének elérési útját. Az erőforrások helye rögzített az összes Azure Stack Edge Pro-eszközön, és a következőre van beállítva: `dbelocal` . Meg kell adnia a cím előtagjait és a magánhálózati IP-címet is. Az összes alábbi környezeti változó értéke a (z) kivételével az értékek alapján megadott értékek `AZURE_RESOURCE_LOCATION` , amelyeknek hardcoded kell lennie `"dbelocal"` .

    ```powershell
    $ENV:ARM_ENDPOINT = "https://management.team3device.teatraining1.com"
    $ENV:AZURE_RESOURCE_LOCATION = "dbelocal"
    $ENV:VHD_FILE_PATH = "C:\Downloads\Ubuntu1604\Ubuntu13.vhd"
    $ENV:ADDRESS_PREFIXES = "5.5.0.0/16"
    $ENV:PRIVATE_IP_ADDRESS = "5.5.174.126"
    ```

3. Regisztrálja a környezetét. Futtassa az alábbi paramétereket az az Cloud Register:

    | Érték | Leírás | Példa |
    | --- | --- | --- |
    | Környezet neve | Annak a környezetnek a neve, amelyhez csatlakozni próbál | Adjon meg egy nevet, például: `aze-environ` |
    | Resource Manager-végpont | Ez az URL-cím `https://Management.<appliancename><dnsdomain>` . <br> Az URL-cím beszerzéséhez nyissa meg a **eszközök** lapot az eszköz helyi webes felületén. |Például: `https://management.team3device.teatraining1.com`.  |
    
    ```powershell
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.<appliance name>.<DNS domain>"
    ```
    Az alábbiakban a fenti parancs minta használatát láthatja:
    
    ```powershell
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> az cloud register -n az-new-env --endpoint-resource-manager "https://management.team3device.teatraining1.com"
    ```
    
    
4. Állítsa be az aktív környezetet a következő parancsok használatával:

    ```powershell
    az cloud set -n <EnvironmentName>
    ```
    Az alábbiakban a fenti parancs minta használatát láthatja:

    ```powershell
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> az cloud set -n az-new-env
    Switched active cloud to 'az-new-env'.
    Use 'az login' to log in to this cloud.
    Use 'az account set' to set the active subscription.
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ```

4. Jelentkezzen be az Azure Stack Edge Pro-környezetbe a `az login` paranccsal. Bejelentkezhet a Azure Stack Edge Pro-környezetbe, akár felhasználóként, akár [szolgáltatásnévként](../active-directory/develop/app-objects-and-service-principals.md).

   A következő lépésekkel jelentkezhet be *felhasználóként*:

   Megadhatja a felhasználónevet és a jelszót közvetlenül a `az login` parancson belül, vagy egy böngészőben végezheti el a hitelesítést. Az utóbbit akkor kell végrehajtania, ha a fiókjában engedélyezve van a többtényezős hitelesítés.

   A következő példa a példák használatát mutatja be `az login` :
    
    ```powershell
    PS C:\Certificates> az login -u EdgeARMuser
    ```
   A login parancs használata után a rendszer jelszót kér. Adja meg a Azure Resource Manager jelszavát.

   Az alábbi példa a sikeres bejelentkezést mutatja be a jelszó megadása után:  
   
   ```output
   PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> az login -u EdgeARMuser
   Password:
   [
        {
            "cloudName": "az-new-env",
            "id": "A4257FDE-B946-4E01-ADE7-674760B8D1A3",
            "isDefault": true,
            "name": "Default Provider Subscription",
            "state": "Enabled",
            "tenantId": "c0257de7-538f-415c-993a-1b87a031879d",
            "user": {
                "name": "EdgeArmUser@localhost",
                "type": "user"
            }
        }
   ]
   PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
   ```
   Jegyezze fel a és az `id` `tenantId` értékeket, mivel ezek az értékek a Azure Resource Manager előfizetés-azonosítójának és Azure Resource Manager BÉRLŐi azonosítónak felelnek meg, és a későbbi lépésben lesznek használva.
       
   A következő környezeti változókat kell beállítani az *egyszerű szolgáltatásnév* működéséhez:

   ```
   $ENV:ARM_TENANT_ID = "c0257de7-538f-415c-993a-1b87a031879d"
   $ENV:ARM_CLIENT_ID = "cbd868c5-7207-431f-8d16-1cb144b50971"
   $ENV:ARM_CLIENT_SECRET - "<Your Azure Resource Manager password>"
   $ENV:ARM_SUBSCRIPTION_ID = "A4257FDE-B946-4E01-ADE7-674760B8D1A3"
   ```

   A Azure Resource Manager Ügyfél-azonosítója nem rögzített. Az Azure Resource Manager-bérlő azonosítója és Azure Resource Manager előfizetés-azonosítója egyaránt megtalálható a `az login` korábban futtatott parancs kimenetében. A Azure Resource Manager-ügyfél titkos kulcsa a beállított Azure Resource Manager jelszó.

   További információ: [Azure Resource Manager Password](azure-stack-edge-j-series-set-azure-resource-manager-password.md).

5. Módosítsa a profilt az 2019-03-01-Hybrid verzióra. A profil verziójának módosításához futtassa a következő parancsot:

    ```powershell
    az cloud update --profile 2019-03-01-hybrid
    ```

    A következő példa a példák használatát mutatja be `az cloud update` :

    ```powershell
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> az cloud update --profile 2019-03-01-hybrid
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ```

## <a name="step-2-create-a-vm"></a>2. lépés: virtuális gép létrehozása

A rendszer egy Python-szkriptet hoz létre a virtuális gép létrehozásához. Attól függően, hogy felhasználóként jelentkezett be, vagy egyszerű szolgáltatásnévként van beállítva, a parancsfájl ennek megfelelően viszi a bemenetet, és létrehoz egy virtuális gépet.

1. Futtassa a Python-szkriptet ugyanabból a címtárból, ahol a Python telepítve van.

    `.\python.exe example_dbe_arguments_name_https.py cli`

2. A szkript futásakor a VHD feltöltése 20-30 percet vesz igénybe. A feltöltési művelet előrehaladásának megtekintéséhez Azure Storage Explorer vagy AzCopy használható.

    Íme egy példa a szkript sikeres futtatásának kimenetére. A parancsfájl létrehoz egy erőforráscsoport összes erőforrását, és ezekkel az erőforrásokkal létrehoz egy virtuális gépet, és végül törli az erőforráscsoportot, beleértve az összes létrehozott erőforrást.

    
    ```powershell
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> .\python.exe example_dbe_arguments_name_https.py cli
    
    Create Resource Group
    Create a storage account
    Uploading to Azure Stack Storage as blob:
            ubuntu13.vhd
    
    Listing blobs...
            ubuntu13.vhd
    
    VM image resource id:
                /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/azure-sample-group-virtual-machines118/providers/Microsoft.Compute/images/UbuntuImage
    
    Create Vnet
    Create Subnet
    Create NIC
    Creating Linux Virtual Machine
    Tag Virtual Machine
    Create (empty) managed Data Disk
    Get Virtual Machine by Name
    Attach Data Disk
    Detach Data Disk
    Deallocating the VM (to prepare for a disk resize)
    Update OS disk size
    Start VM
    Restart VM
    Stop VM
    
    List VMs in subscription
            VM: VmName118
    
    List VMs in resource group
            VM: VmName118
    
    Delete VM
    All example operations completed successfully!
    
    Delete Resource Group
    Deleted: azure-sample-group-virtual-machines118
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ``` 


## <a name="next-steps"></a>Következő lépések

[Általános az CLI parancsok Linux rendszerű virtuális gépekhez](../virtual-machines/linux/cli-manage.md)