---
title: Kapcsolódás Azure Resource Managerhoz a Azure Stack Edge Pro GPU-eszközön
description: Ismerteti, hogyan csatlakozhat a Azure Stack Edge Pro GPU-val futó Azure Resource Managerhoz Azure PowerShell használatával.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/01/2021
ms.author: alkohli
ms.openlocfilehash: 27c0c211b9844a34b0cb43dcd05037ba4a548f3e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102638196"
---
# <a name="connect-to-azure-resource-manager-on-your-azure-stack-edge-pro-device"></a>Azure Resource Manager csatlakoztatása a Azure Stack Edge Pro-eszközön

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Az Azure Resource Manager egy olyan felügyeleti réteget biztosít, amely lehetővé teszi az Azure-előfizetésben lévő erőforrások létrehozását, frissítését és törlését. Az Azure Stack Edge Pro-eszköz ugyanazokat a Azure Resource Manager API-kat támogatja a helyi előfizetésben lévő virtuális gépek létrehozásához, frissítéséhez és törléséhez. Ez a támogatás lehetővé teszi az eszköznek a felhővel konzisztens módon történő kezelését. 

Ez az oktatóanyag azt ismerteti, hogyan csatlakozhat a helyi API-khoz Azure Stack Edge Pro-eszközön a Azure PowerShell használatával Azure Resource Manager segítségével.

## <a name="about-azure-resource-manager"></a>Tudnivalók az Azure Resource Manager használatáról

A Azure Resource Manager konzisztens felügyeleti réteget biztosít a Azure Stack Edge Pro-eszköz API meghívásához, valamint olyan műveletek elvégzéséhez, mint például a virtuális gépek létrehozása, frissítése és törlése. A Azure Resource Manager architektúrája részletesen szerepel a következő ábrán.

![Diagram a Azure Resource Manager](media/azure-stack-edge-gpu-connect-resource-manager/edge-device-flow.svg)


## <a name="endpoints-on-azure-stack-edge-pro-device"></a>Azure Stack Edge Pro-eszközön futó végpontok

A következő táblázat összefoglalja az eszközön elérhető különböző végpontokat, a támogatott protokollokat és a végpontok eléréséhez szükséges portokat. A cikk során a végpontokra mutató hivatkozásokat talál.

| # | Végpont | Támogatott protokollok | Használt port | Alkalmazási cél |
| --- | --- | --- | --- | --- |
| 1. | Azure Resource Manager | https | 443 | Azure Resource Managerhoz való kapcsolódás automatizáláshoz |
| 2. | Biztonsági jogkivonat szolgáltatás | https | 443 | Hitelesítés a hozzáférési és frissítési jogkivonatok használatával |
| 3. | Blob | https | 443 | A blob Storage-hoz való kapcsolódás a REST használatával |


## <a name="connecting-to-azure-resource-manager-workflow"></a>Csatlakozás Azure Resource Manager munkafolyamathoz

Az eszköz helyi API-khoz való csatlakozásának folyamata a következő lépések végrehajtásával Azure Resource Manager szükséges:

| . Lépés # | Ezt a lépést kell elvégeznie... | .. ezen a helyen. |
| --- | --- | --- |
| 1. | [Az Azure Stack Edge Pro-eszköz konfigurálása](#step-1-configure-azure-stack-edge-pro-device) | Helyi webes felhasználói felület |
| 2. | [Tanúsítványok létrehozása és telepítése](#step-2-create-and-install-certificates) | Windows-ügyfél/helyi webes felhasználói felület |
| 3. | [Az előfeltételek áttekintése és konfigurálása](#step-3-install-powershell-on-the-client) | Windows-ügyfél |
| 4. | [Azure PowerShell beállítása az ügyfélen](#step-4-set-up-azure-powershell-on-the-client) | Windows-ügyfél |
| 5. | [Gazdagép fájljának módosítása végpont-névfeloldáshoz](#step-5-modify-host-file-for-endpoint-name-resolution) | Windows-ügyfél vagy DNS-kiszolgáló |
| 6. | [Győződjön meg arról, hogy a végpont neve fel van oldva](#step-6-verify-endpoint-name-resolution-on-the-client) | Windows-ügyfél |
| 7. | [Azure PowerShell parancsmagok használata a Azure Resource Managerhoz való kapcsolódás ellenőrzéséhez](#step-7-set-azure-resource-manager-environment) | Windows-ügyfél |

A következő részek részletesen ismertetik a Azure Resource Managerhoz való csatlakozáshoz szükséges fenti lépéseket.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy az Azure Resource Manager által az eszközhöz való csatlakozáshoz használt ügyfél TLS 1,2-et használ. További információért látogasson el a [TLS 1,2 konfigurálása Windows-ügyfélhez Azure stack Edge Pro-eszköz eléréséhez](azure-stack-edge-gpu-configure-tls-settings.md).

## <a name="step-1-configure-azure-stack-edge-pro-device"></a>1. lépés: Azure Stack Edge Pro-eszköz konfigurálása 

Hajtsa végre az alábbi lépéseket a Azure Stack Edge Pro-eszköz helyi webes FELÜLETén.

1. Töltse ki a Azure Stack Edge Pro-eszköz hálózati beállításait. 

    ![Helyi webes felhasználói felület "hálózati beállítások" lapja](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-2.png)


    Jegyezze fel az eszköz IP-címét. Ezt az IP-címet később fogja használni.

2. Konfigurálja az eszköz nevét és a DNS-tartományt az **eszköz** oldaláról. Jegyezze fel az eszköz nevét és a DNS-tartományt, ahogy ezeket később használni fogja.

    ![Helyi webes felhasználói felület "eszköz" lapja](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/device-2.png)

    > [!IMPORTANT]
    > A rendszer az eszköz nevét, DNS-tartományát fogja használni a kitett végpontok létrehozásához.
    > A helyi webes felhasználói felület **eszköz** lapján a Azure Resource Manager és a blob végpontokat használja.


## <a name="step-2-create-and-install-certificates"></a>2. lépés: tanúsítványok létrehozása és telepítése

A tanúsítványok biztosítják, hogy a kommunikáció megbízható legyen. A Azure Stack Edge Pro-eszközön automatikusan létrejönnek az önaláírt berendezések, a blobok és a Azure Resource Manager tanúsítványok. Igény szerint saját aláírt blobot is bevihet, és Azure Resource Manager tanúsítványokat is.

Ha saját aláírt tanúsítványt használ, a tanúsítvány megfelelő aláíró láncára is szüksége lesz. Az aláíró lánchoz, Azure Resource Managerhoz és az eszközön található blob-tanúsítványokhoz szüksége lesz a megfelelő tanúsítványokra az ügyfélszámítógépen is, hogy hitelesítse és kommunikáljon az eszközzel.

A Azure Resource Managerhoz való kapcsolódáshoz létre kell hoznia vagy be kell szereznie az aláírási láncot és az Endpoint tanúsítványokat, importálnia kell ezeket a tanúsítványokat a Windows-ügyfélre, majd végül fel kell töltenie ezeket a tanúsítványokat

### <a name="create-certificates-optional"></a>Tanúsítványok létrehozása (nem kötelező)

Csak tesztelési és fejlesztési célokra használhatja a Windows PowerShellt tanúsítványok létrehozásához a helyi rendszeren. Az ügyfél tanúsítványainak létrehozásakor kövesse az alábbi irányelveket:

1. Először létre kell hoznia egy főtanúsítványt az aláírási lánchoz. További információ: az [aláíró tanúsítványok létrehozásának](azure-stack-edge-gpu-manage-certificates.md#create-signing-chain-certificate)lépései.

2. Ezután létrehozhatja a blobhoz tartozó végpont-tanúsítványokat, és Azure Resource Manager is. Ezeket a végpontokat a helyi webes felhasználói felület **eszköz** lapján érheti el. Lásd a [végponti tanúsítványok létrehozásának](azure-stack-edge-gpu-manage-certificates.md#create-signed-endpoint-certificates)lépéseit.

3. Az összes tanúsítvány esetében győződjön meg arról, hogy a tulajdonos neve és a tulajdonos alternatív neve megfelel a következő irányelveknek:

    |Típus |Tulajdonos neve (SN)  |Tulajdonos alternatív neve (SAN)  |Tulajdonos neve – példa |
    |---------|---------|---------|---------|
    |Azure Resource Manager|`management.<Device name>.<Dns Domain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`|`management.mydevice1.microsoftdatabox.com` |
    |Blob Storage|`*.blob.<Device name>.<Dns Domain>`|`*.blob.< Device name>.<Dns Domain>`|`*.blob.mydevice1.microsoftdatabox.com` |
    |Több-SAN önálló tanúsítvány mindkét végponthoz|`<Device name>.<dnsdomain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`<br>`*.blob.<Device name>.<Dns Domain>`|`mydevice1.microsoftdatabox.com` |

A tanúsítványokkal kapcsolatos további információkért lépjen a [tanúsítványok kezelése](azure-stack-edge-gpu-manage-certificates.md)című témakörre.

### <a name="upload-certificates-on-the-device"></a>Tanúsítványok feltöltése az eszközön

Az előző lépésben létrehozott tanúsítványok az ügyfél személyes tárolójába kerülnek. Ezeket a tanúsítványokat a megfelelő formátumú fájlokra kell exportálni az ügyfélen, amelyeket aztán fel lehet tölteni az eszközre.

1. A főtanúsítványt a *. cer* kiterjesztésű der formátumú fájlként kell exportálni. A részletes lépésekért lásd: [tanúsítványok exportálása. cer formátumú fájlként](azure-stack-edge-gpu-manage-certificates.md#export-certificates-as-der-format).

2. A végponti tanúsítványokat *. pfx* -fájlként kell exportálni titkos kulcsokkal. A részletes lépésekért lásd: [tanúsítványok exportálása. pfx-fájlként, titkos kulcsokkal](azure-stack-edge-gpu-manage-certificates.md#export-certificates-as-pfx-format-with-private-key).

3. Ezután a rendszer feltölti a gyökér-és végpont-tanúsítványokat az eszközre a helyi webes felhasználói felület **tanúsítványok** lapján található **+ tanúsítvány hozzáadása** lehetőség használatával. A tanúsítványok feltöltéséhez kövesse a [tanúsítványok feltöltése](azure-stack-edge-gpu-manage-certificates.md#upload-certificates)című témakör lépéseit.


### <a name="import-certificates-on-the-client-running-azure-powershell"></a>Tanúsítványok importálása a Azure PowerShell rendszert futtató ügyfélen

A Windows-ügyfélnek, amelyen meg kell hívnia a Azure Resource Manager API-kat, létre kell hoznia a megbízhatóságot az eszközzel. Ennek érdekében az előző lépésben létrehozott tanúsítványokat importálni kell a Windows-ügyfélen a megfelelő tanúsítványtárolóba.

1. A (z *). cer* KITERJESZTÉSű der-formátumban exportált főtanúsítványt most importálni kell az ügyfélrendszer megbízható legfelső szintű hitelesítésszolgáltatók hatóságaiba. A részletes lépésekért lásd: [tanúsítványok importálása a megbízható legfelső szintű hitelesítésszolgáltatók tárolójába.](azure-stack-edge-gpu-manage-certificates.md#import-certificates-as-der-format)

2. A *. pfx* fájlba exportált végpont-tanúsítványokat *. cer* fájlként kell exportálni. Ezt a *. cer* -et a rendszer a **személyes** tanúsítványtárolóba importálja. A részletes lépésekért lásd: [tanúsítványok importálása személyes tárolóba](azure-stack-edge-gpu-manage-certificates.md#import-certificates-as-der-format).

## <a name="step-3-install-powershell-on-the-client"></a>3. lépés: a PowerShell telepítése az ügyfélen 

A Windows-ügyfélnek meg kell felelnie a következő előfeltételeknek:

1. Futtassa a PowerShell 5,0-es verzióját. A PowerShell 5,0-es verziójának kell lennie. A PowerShell Core nem támogatott. A PowerShell verziójának a rendszeren való vizsgálatához futtassa a következő parancsmagot:

    ```powershell
    $PSVersionTable.PSVersion
    ```

    Hasonlítsa **össze a** főverziót, és győződjön meg róla, hogy 5,0 vagy újabb.

    Ha elavult verzióval rendelkezik, tekintse meg a [meglévő Windows PowerShell frissítését](/powershell/scripting/install/installing-windows-powershell?view=powershell-6&preserve-view=true#upgrading-existing-windows-powershell) ismertető témakört.

    Ha nem \' rendelkezik PowerShell 5,0-lel, kövesse a [Windows PowerShell telepítését](/powershell/scripting/install/installing-windows-powershell?view=powershell-6&preserve-view=true).

    Az alábbiakban egy példa látható a kimenetre.

    ```powershell
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved. 
    Try the new cross-platform PowerShell https://aka.ms/pscore6
    PS C:\windows\system32> $PSVersionTable.PSVersion
    Major  Minor  Build  Revision
    -----  -----  -----  --------
    5      1      18362  145
    ```
    
2. A PowerShell-galériahoz férhet hozzá.

    Futtassa a PowerShellt rendszergazdaként. Ellenőrizze, hogy a `PSGallery` regisztrálva van-e adattárként.

    ```powershell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop
    Get-PSRepository -Name "PSGallery"
    ```
    
    Az alábbiakban egy példa látható a kimenetre.
    
    ```powershell
    PS C:\windows\system32> Import-Module -Name PowerShellGet -ErrorAction Stop
    PS C:\windows\system32> Import-Module -Name PackageManagement -ErrorAction Stop
    PS C:\windows\system32> Get-PSRepository -Name "PSGallery"
    Name                      InstallationPolicy   SourceLocation
    ----                      ------------------   --------------
    PSGallery                 Trusted              https://www.powershellgallery.com/api/v2
    ```
    
Ha a tárház nem megbízható, vagy további információra van szüksége, tekintse meg [a PowerShell-Galéria kisegítő lehetőségek ellenőrzése](/azure-stack/operator/azure-stack-powershell-install?view=azs-1908&preserve-view=true&preserve-view=true#2-validate-the-powershell-gallery-accessibility)című témakört.

## <a name="step-4-set-up-azure-powershell-on-the-client"></a>4. lépés: Azure PowerShell beállítása az ügyfélen 

<!--1. Verify the API profile of the client and identify which version of the Azure PowerShell modules and libraries to include on your client. In this example, the client system will be running Azure Stack 1904 or later. For more information, see [Azure Resource Manager API profiles](/azure-stack/user/azure-stack-version-profiles?view=azs-1908#azure-resource-manager-api-profiles).-->

1. Telepítenie kell Azure PowerShell modulokat az ügyfélen, amely együttműködik az eszközzel.

    a. Futtassa a PowerShellt rendszergazdaként. Hozzá kell férnie a PowerShell-galériához. 


    b. A szükséges Azure PowerShell-modulok a PowerShell-galériaból való telepítéséhez futtassa a következő parancsot:

    ```powershell
    # Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet.
    
    Install-Module -Name AzureRM.BootStrapper
    
   # Install and import the API Version Profile into the current PowerShell session.
    
    Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
    
    # Confirm the installation of PowerShell
    Get-Module -Name "Azure*" -ListAvailable
    ```
    
    Győződjön meg arról, hogy rendelkezik a telepítés végén futó Azure-RM modul 2.5.0-es verziójával. 
    Ha már rendelkezik az Azure-ban már meglévő verziójával, amely nem felel meg a szükséges verziónak, távolítsa el az alábbi parancsot a használatával:

    `Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose`

    Ekkor újra telepítenie kell a szükséges verziót.
   

Alább látható egy minta kimenet, amely azt jelzi, hogy a AzureRM Version 2.5.0-modulok telepítése sikeresen megtörtént.

```powershell
PS C:\windows\system32> Install-Module -Name AzureRM.BootStrapper
PS C:\windows\system32> Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
Loading Profile 2019-03-01-hybrid
PS C:\windows\system32> Get-Module -Name "Azure*" -ListAvailable
 
    Directory: C:\Program Files\WindowsPowerShell\Modules
 
ModuleType Version    Name                          ExportedCommands
---------- -------    ----                          ----------------
Script     4.5.0      Azure.Storage                       {Get-AzureStorageTable, New-AzureStorageTableSASToken, New...
Script     2.5.0      AzureRM
Script     0.5.0      AzureRM.BootStrapper                {Update-AzureRmProfile, Uninstall-AzureRmProfile, Install-...
Script     4.6.1      AzureRM.Compute                     {Remove-AzureRmAvailabilitySet, Get-AzureRmAvailabilitySet...
Script     3.5.1      AzureRM.Dns                         {Get-AzureRmDnsRecordSet, New-AzureRmDnsRecordConfig, Remo...
Script     5.1.5      AzureRM.Insights                    {Get-AzureRmMetricDefinition, Get-AzureRmMetric, Remove-Az...
Script     4.2.0      AzureRM.KeyVault                    {Add-AzureKeyVaultCertificate, Set-AzureKeyVaultCertificat...
Script     5.0.1      AzureRM.Network                     {Add-AzureRmApplicationGatewayAuthenticationCertificate, G...
Script     5.8.3      AzureRM.profile                     {Disable-AzureRmDataCollection, Disable-AzureRmContextAuto...
Script     6.4.3      AzureRM.Resources                   {Get-AzureRmProviderOperation, Remove-AzureRmRoleAssignmen...
Script     5.0.4      AzureRM.Storage                     {Get-AzureRmStorageAccount, Get-AzureRmStorageAccountKey, ...
Script     4.0.2      AzureRM.Tags                        {Remove-AzureRmTag, Get-AzureRmTag, New-AzureRmTag}
Script     4.0.3      AzureRM.UsageAggregates             Get-UsageAggregates
Script     5.0.1      AzureRM.Websites                    {Get-AzureRmAppServicePlan, Set-AzureRmAppServicePlan, New...

 
    Directory: C:\Program Files (x86)\Microsoft Azure Information Protection\Powershell
 
ModuleType Version    Name                            ExportedCommands
---------- -------    ----                           ----------------
Binary     1.48.204.0 AzureInformationProtection          {Clear-RMSAuthentication, Get-RMSFileStatus, Get-RMSServer...
```


## <a name="step-5-modify-host-file-for-endpoint-name-resolution"></a>5. lépés: az állomás fájljának módosítása végpont-névfeloldáshoz 

Ezután hozzáadja az Azure konzisztens VIP-t, amelyet az eszköz helyi webes FELÜLETén definiált:

- A gazdagép fájlja az ügyfélen, vagy
- A DNS-kiszolgáló konfigurációja

> [!IMPORTANT]
> Javasoljuk, hogy módosítsa a DNS-kiszolgáló konfigurációját a végpont nevének feloldásához.

Az eszközhöz való kapcsolódáshoz használt Windows-ügyfélen hajtsa végre a következő lépéseket:

1. Indítsa el a **jegyzettömböt** rendszergazdaként, majd nyissa meg a C:\Windows\System32\Drivers\etc. címen található **hosts** fájlt.

    ![Windows Intéző – fájl](media/azure-stack-edge-gpu-connect-resource-manager/hosts-file.png)

2. Adja hozzá a következő bejegyzéseket a **gazdagépek** fájljához a megfelelő értékekkel helyettesítve az eszközön: 

    ```
    <Device IP> login.<appliance name>.<DNS domain>
    <Device IP> management.<appliance name>.<DNS domain>
    <Device IP> <storage name>.blob.<appliance name>.<DNS domain>
    ```

    > [!IMPORTANT]
    > A Hosts fájlban lévő bejegyzésnek pontosan meg kell egyeznie, hogy egy későbbi lépésben csatlakozhasson Azure Resource Managerhoz. Győződjön meg arról, hogy a DNS-tartomány bejegyzése itt mind a kisbetűs.

    Az eszköz IP-címét egy korábbi lépésben mentette a helyi webes KEZELŐFELÜLETről.

    A bejelentkezés. \<appliance name\> .\<DNS domain\> a bejegyzés a biztonsági jogkivonat szolgáltatás (STS) végpontja. Az STS felelős a biztonsági jogkivonatok létrehozásához, ellenőrzéséhez, megújításához és törléséhez. A biztonsági jogkivonat szolgáltatás az eszköz és az ügyfél közötti folyamatos kommunikációhoz használt hozzáférési jogkivonat és frissítési jogkivonat létrehozásához használatos.

3. A hivatkozáshoz használja az alábbi képet. Mentse a **hosts** fájlt.

    ![tároló fájl a Jegyzettömbben](media/azure-stack-edge-gpu-connect-resource-manager/hosts-file-notepad.png)

## <a name="step-6-verify-endpoint-name-resolution-on-the-client"></a>6. lépés: a végpont névfeloldásának ellenőrzése az ügyfélen

Ellenőrizze, hogy a végpont neve fel van-e oldva azon az ügyfélen, amelyet az Azure konzisztens VIP-hez való kapcsolódáshoz használ.

1. A ping.exe parancssori segédprogrammal megtekintheti, hogy a végpont neve fel van-e oldva. Az IP-cím megadása esetén a ping parancs visszaadja az újra nyomkövetést eredményező számítógép TCP/IP-állomásnevét \' .

    Adja hozzá a `-a` kapcsolót a parancssorhoz az alábbi példában látható módon. Ha az állomásnév visszaváltható, akkor a válaszban a potenciálisan értékes információkat is visszaadja.

    ![Pingelés parancssorban](media/azure-stack-edge-gpu-connect-resource-manager/ping-command-prompt.png)



## <a name="step-7-set-azure-resource-manager-environment"></a>7. lépés: Azure Resource Manager környezet beállítása

Állítsa be a Azure Resource Manager környezetet, és ellenőrizze, hogy az eszköz Azure Resource Manager-e az ügyfél-kommunikációra. Az ellenőrzéshez hajtsa végre a következő lépéseket:


1. Az `Add-AzureRmEnvironment` parancsmag alkalmazásával gondoskodhat arról, hogy az Azure Resource Manager használatával történő kommunikáció megfelelően működjön, és az API-hívások az Azure Resource Manager részére dedikált 443-as porton keresztül haladjanak.

    Az `Add-AzureRmEnvironment` parancsmag végpontokat és metaadatokat hoz létre, amelyek lehetővé teszik az Azure Resource Manager-parancsmagok számára az Azure Resource Manager új példányához való csatlakozást. 


    > [!IMPORTANT]
    > A következő parancsmagban megadott Azure Resource Manager végpont URL-címe megkülönbözteti a kis-és nagybetűket. Győződjön meg arról, hogy a végpont URL-címe az összes kisbetűs, és megfelel a Hosts fájlban megadott értéknek. Ha az eset nem egyezik, hibaüzenet jelenik meg.

    ```powershell
    Add-AzureRmEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>/"
    ```

    Alább látható egy minta kimenet:
    
    ```powershell
    PS C:\windows\system32> Add-AzureRmEnvironment -Name AzDBE -ARMEndpoint https://management.dbe-n6hugc2ra.microsoftdatabox.com/
    
    Name  Resource Manager Url                    ActiveDirectory Authority
    ----  --------------------                   -------------------------
    AzDBE https://management.dbe-n6hugc2ra.microsoftdatabox.com https://login.dbe-n6hugc2ra.microsoftdatabox.com/adfs/
    ```

2. Környezetként az Azure Stack Edge Pro lehetőséget, az Azure Resource Manager-hívásokhoz használandó portként pedig a 443-as portot adja meg. A környezet két módon adható meg:

    - Állítsa be a környezetet. Írja be a következő parancsot:

    ```powershell
    Set-AzureRMEnvironment -Name <Environment Name>
    ```
    
    További információkért keresse fel a [set-AzureRMEnvironment](/powershell/module/azurerm.profile/set-azurermenvironment?view=azurermps-6.13.0&preserve-view=true).

    - Adja meg a környezetet minden olyan parancsmagnál, amelyet végrehajt. Ez biztosítja, hogy minden API-hívás a megfelelő környezetben legyen. Alapértelmezés szerint a hívások az Azure nyilvános verzióján keresztül történnek, de azt szeretné, hogy az Azure Stack Edge Pro-eszközhöz beállított környezeten át lehessen lépni.

    - A [AzureRM-környezetek váltásával](#switch-environments)kapcsolatos további információkért lásd:.

2. A helyi eszköz API-jait hívja meg az Azure Resource Managerhez tartozó kapcsolatok hitelesítéséhez. 

    1. Ezek a hitelesítő adatok egy helyi számítógépfiók, és kizárólag API-hozzáféréshez használatosak.

    2. A-t vagy a-t a `login-AzureRMAccount` parancson keresztül is csatlakozhat `Connect-AzureRMAccount` . 

        1. A bejelentkezéshez írja be a következő parancsot. Az ebben a példányban található bérlői azonosító Hard Coded-c0257de7-538f-415c-993A-1b87a031879d. Használja a következő felhasználónevet és jelszót.

            - **Felhasználónév**  -  *EdgeArmUser*

            - **Jelszó**  -  [Állítsa be Azure Resource Manager jelszavát](azure-stack-edge-gpu-set-azure-resource-manager-password.md) , és ezt a jelszót használja a bejelentkezéshez. 

            ```powershell
            PS C:\windows\system32> $pass = ConvertTo-SecureString "<Your password>" -AsPlainText -Force;
            PS C:\windows\system32> $cred = New-Object System.Management.Automation.PSCredential("EdgeArmUser", $pass)
            PS C:\windows\system32> Connect-AzureRmAccount -EnvironmentName AzDBE -TenantId c0257de7-538f-415c-993a-1b87a031879d -credential $cred
            
            Account       SubscriptionName   TenantId            Environment
            -------       ----------------   --------            -----------
            EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzDBE
            
            PS C:\windows\system32>
            ```
                   
        
            A bejelentkezés másik módja a `login-AzureRmAccount` parancsmag használata. 
            
            `login-AzureRMAccount -EnvironmentName <Environment Name>` – TenantId c0257de7-538f-415c-993A-1b87a031879d 

            Az alábbi példa a parancs kimenetét jeleníti meg. 
         
            ```powershell
            PS C:\Users\Administrator> login-AzureRMAccount -EnvironmentName AzDBE -TenantId c0257de7-538f-415c-993a-1b87a031879d
            
            Account         SubscriptionName  TenantId              Environment
            -------         ----------------  --------              -------
            EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzDBE
            PS C:\Users\Administrator>
            ```



> [!IMPORTANT]
> A Azure Resource Managerhoz való kapcsolódás 1,5 óránként lejár, vagy ha a Azure Stack Edge Pro-eszköz újraindul. Ha ez történik, a futtatott parancsmagok hibaüzeneteket küldenek ahhoz, hogy többé ne kapcsolódjon az Azure-hoz. Újra be kell jelentkeznie.

## <a name="switch-environments"></a>Környezetek váltása

A `Disconnect-AzureRmAccount` parancs futtatásával váltson át egy másikra `AzureRmEnvironment` . 

Ha `Set-AzureRmEnvironment` a és a `Login-AzureRmAccount` használata nélkül használja `Disconnect-AzureRmAccount` a környezetet, a környezet nem lett ténylegesen átkapcsolva.  

Az alábbi példák bemutatják, hogyan válthat két környezet `AzDBE1` és a között `AzDBE2` .

Először sorolja fel az ügyfél összes meglévő környezetét.


```azurepowershell
PS C:\WINDOWS\system32> Get-AzureRmEnvironment
Name    Resource Manager Url     ActiveDirectory Authority
----    --------------------      -------------------------
AzureChinaCloud   https://management.chinacloudapi.cn/                 https://login.chinacloudapi.cn/
AzureCloud        https://management.azure.com/                        https://login.microsoftonline.com/
AzureGermanCloud  https://management.microsoftazure.de/                https://login.microsoftonline.de/
AzDBE1            https://management.HVTG1T2-Test.microsoftdatabox.com https://login.hvtg1t2-test.microsoftdatabox.com/adfs/
AzureUSGovernment https://management.usgovcloudapi.net/                https://login.microsoftonline.us/
AzDBE2            https://management.CVV4PX2-Test.microsoftdatabox.com https://login.cvv4px2-test.microsoftdatabox.com/adfs/
```

Ezután adja meg, hogy mely környezettel csatlakozik a Azure Resource Manageron keresztül.

```azurepowershell
PS C:\WINDOWS\system32> Get-AzureRmContext |fl *

Name               : Default Provider Subscription (A4257FDE-B946-4E01-ADE7-674760B8D1A3) - EdgeArmUser@localhost
Account            : EdgeArmUser@localhost
Environment        : AzDBE2
Subscription       : a4257fde-b946-4e01-ade7-674760b8d1a3
Tenant             : c0257de7-538f-415c-993a-1b87a031879d
TokenCache         : Microsoft.Azure.Commands.Common.Authentication.ProtectedFileTokenCache
VersionProfile     :
ExtendedProperties : {}
```

A másik környezetbe való váltás előtt le kell választania az aktuális környezetből.


```azurepowershell
PS C:\WINDOWS\system32> Disconnect-AzureRmAccount

Id                    : EdgeArmUser@localhost
Type                  : User
Tenants               : {c0257de7-538f-415c-993a-1b87a031879d}
AccessToken           :
Credential            :
TenantMap             : {}
CertificateThumbprint :
ExtendedProperties    : {[Subscriptions, A4257FDE-B946-4E01-ADE7-674760B8D1A3], [Tenants, c0257de7-538f-415c-993a-1b87a031879d]}
```

Jelentkezzen be a másik környezetbe. Az alábbiakban látható a mintakimenet.

```azurepowershell
PS C:\WINDOWS\system32> Login-AzureRmAccount -Environment "AzDBE1" -TenantId $ArmTenantId

Account     SubscriptionName   TenantId        Environment
-------     ----------------   --------        -----------
EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzDBE1
```

Futtassa ezt a parancsmagot annak megerősítéséhez, hogy melyik környezethez kapcsolódik.

```azurepowershell
PS C:\WINDOWS\system32> Get-AzureRmContext |fl *

Name               : Default Provider Subscription (A4257FDE-B946-4E01-ADE7-674760B8D1A3) - EdgeArmUser@localhost
Account            : EdgeArmUser@localhost
Environment        : AzDBE1
Subscription       : a4257fde-b946-4e01-ade7-674760b8d1a3
Tenant             : c0257de7-538f-415c-993a-1b87a031879d
TokenCache         : Microsoft.Azure.Commands.Common.Authentication.ProtectedFileTokenCache
VersionProfile     :
ExtendedProperties : {}
```
Ezzel átváltotta a kívánt környezetet.

## <a name="next-steps"></a>Következő lépések

[Virtuális gépek üzembe helyezése Azure stack Edge Pro-eszközön](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md).
