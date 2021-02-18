---
title: A csomagok rögzítésével proaktív hálózati figyelést végezhet a riasztások használatával – Azure Functions
titleSuffix: Azure Network Watcher
description: Ez a cikk azt ismerteti, hogyan hozható létre riasztás által aktivált csomagok rögzítése az Azure Network Watcher
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: 75e6e7c4-b3ba-4173-8815-b00d7d824e11
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 8e2af73be0fc887b132f523133159472ce1d1f98
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100570976"
---
# <a name="use-packet-capture-for-proactive-network-monitoring-with-alerts-and-azure-functions"></a>Az előjelzéses hálózati figyeléshez használja a csomagok rögzítését riasztásokkal és Azure Functionsokkal

Network Watcher a csomagok rögzítése rögzítési munkameneteket hoz létre a virtuális gépeken belüli és kívüli forgalom nyomon követésére. A rögzítési fájl tartalmazhat olyan szűrőt, amely csak a figyelni kívánt forgalom nyomon követésére van meghatározva. Ezeket az adattárakat tároló blobokban vagy helyileg, a vendég gépen tárolja a rendszer.

Ez a funkció távolról is elindítható más automatizálási forgatókönyvek, például a Azure Functions használatával. A csomagok rögzítése lehetőséget biztosít az előjelzéses rögzítések futtatására a meghatározott hálózati rendellenességek alapján. Más felhasználások közé tartozik a hálózati statisztika összegyűjtése, a hálózati behatolásokkal kapcsolatos információk beszerzése, az ügyfél-kiszolgáló kommunikáció hibakeresése és egyebek.

Az Azure-ban üzembe helyezett erőforrások 24/7. Ön és a munkatársai nem tudják aktívan figyelni az összes erőforrás állapotát, 24/7. Például mi történik, ha egy probléma 2 ÓRAKOR jelentkezik?

Az Azure-ökoszisztémán belüli Network Watcher, riasztás és függvények használatával proaktív módon reagálhat az adatokra és az eszközökre a hálózatban felmerülő problémák megoldásához.

![A diagram egy olyan virtuális gép Network Watcher bővítményét jeleníti meg, amely egy T C P szegmensbe áramló, > 100-as hibát eredményezett, amely Azure Functionsre áramlik, amely Network Watcher, amely Network Watcher kiterjesztésbe kerül.][scenario]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

* A [Azure PowerShell](/powershell/azure/install-Az-ps)legújabb verziója.
* Network Watcher meglévő példánya. Ha még nem rendelkezik ilyennel, [hozzon létre Network Watcher egy példányát](network-watcher-create.md).
* Egy meglévő virtuális gép ugyanabban a régióban, mint Network Watcher a [Windows-bővítmény](../virtual-machines/extensions/network-watcher-windows.md) vagy a [linuxos virtuálisgép-bővítmény](../virtual-machines/extensions/network-watcher-linux.md).

## <a name="scenario"></a>Eset

Ebben a példában a virtuális gép a szokásosnál több TCP-szegmenst küld, és riasztást szeretne kapni. A TCP-szegmensek példaként használhatók, de bármilyen riasztási feltételt használhat.

Ha a rendszer riasztást küld, a csomagok szintjének megismerése érdekében meg kell kapnia a kommunikációt. Ezután elvégezheti a virtuális gép normál kommunikációra történő visszaküldésének lépéseit.

Ez a forgatókönyv feltételezi, hogy rendelkezik egy meglévő Network Watcher-példánnyal és egy érvényes virtuális géppel rendelkező erőforráscsoporthoz.

A következő lista áttekintést nyújt a munkafolyamatról:

1. A rendszer riasztást indít a virtuális gépen.
1. A riasztás egy webhookon keresztül hívja meg az Azure-függvényt.
1. Az Azure-függvény feldolgozza a riasztást, és elindítja Network Watcher csomag rögzítési munkamenetét.
1. A csomag rögzítése a virtuális gépen fut, és összegyűjti a forgalmat.
1. A csomag rögzítési fájlját a rendszer a Storage-fiókba feltölti felülvizsgálat és diagnosztika céljából.

A folyamat automatizálásához hozzon létre egy riasztást a virtuális gépen, és kapcsolja össze az incidens bekövetkeztekor. Létrehozunk egy függvényt is, amelyet a rendszer a Network Watcherba hív.

Ez a forgatókönyv a következő műveleteket végzi el:

* Létrehoz egy Azure-függvényt, amely elindítja a csomagok rögzítését.
* Riasztási szabályt hoz létre egy virtuális gépen, és konfigurálja a riasztási szabályt az Azure-függvény meghívásához.

## <a name="create-an-azure-function"></a>Azure-függvény létrehozása

Első lépésként létre kell hoznia egy Azure-függvényt a riasztás feldolgozásához és a csomagok rögzítésének létrehozásához.

1. A [Azure Portal](https://portal.azure.com)válassza az **erőforrás létrehozása**  >  **számítási**  >  **függvényalkalmazás** lehetőséget.

    ![Függvényalkalmazás létrehozása][1-1]

2. A **függvényalkalmazás** panelen adja meg a következő értékeket, majd kattintson az **OK** gombra az alkalmazás létrehozásához:

    |**Beállítás** | **Érték** | **Részletek** |
    |---|---|---|
    |**Alkalmazás neve**|PacketCaptureExample|A Function alkalmazás neve.|
    |**Előfizetés**|[Az Ön előfizetése] Az előfizetés, amelyhez létre kívánja hozni a Function alkalmazást.||
    |**Erőforráscsoport**|PacketCaptureRG|A Function alkalmazást tartalmazó erőforráscsoport.|
    |**Szolgáltatási csomag**|Használatalapú csomag| A Function app által használt terv típusa. A lehetőségek a következők: felhasználás vagy Azure App Service terv. |
    |**Hely**|Az USA középső régiója| Az a régió, amelyben létre kívánja hozni a Function alkalmazást.|
    |**Tárfiók**|automatikusan létrehozott| Az általános célú tároláshoz Azure Functions szükséges Storage-fiók.|

3. A **PacketCaptureExample Function apps** panelen válassza a **functions**  >  **Egyéni függvény** lehetőséget  > **+** .

4. Válassza a **HttpTrigger-PowerShell** lehetőséget, majd adja meg a többi adatot. Végül a függvény létrehozásához válassza a **Létrehozás** lehetőséget.

    |**Beállítás** | **Érték** | **Részletek** |
    |---|---|---|
    |**Forgatókönyv**|Kísérleti|Forgatókönyv típusa|
    |**A függvény neve**|AlertPacketCapturePowerShell|A függvény neve|
    |**Engedélyszint**|Függvény|A függvény engedélyezési szintje|

![Függvények – példa][functions1]

> [!NOTE]
> A PowerShell-sablon kísérleti jellegű, és nem rendelkezik teljes körű támogatással.

Ehhez a példához testreszabásokra van szükség, és az alábbi lépésekben ismertetjük őket.

### <a name="add-modules"></a>Modulok hozzáadása

Network Watcher PowerShell-parancsmagok használatához töltse fel a legújabb PowerShell-modult a Function alkalmazásba.

1. Futtassa a következő PowerShell-parancsot a helyi gépen, amelyen telepítve vannak a legújabb Azure PowerShell modulok:

    ```powershell
    (Get-Module Az.Network).Path
    ```

    Ez a példa a Azure PowerShell modulok helyi elérési útját adja meg. Ezek a mappák egy későbbi lépésben használatosak. Az ebben a forgatókönyvben használt modulok a következők:

   * Az.Network

   * Az.Accounts

   * Az.Resources

     ![PowerShell-mappák][functions5]

1. Válassza a **Function app Settings**  >  **app Service Editor** lehetőséget.

    ![A függvényalkalmazás beállításai][functions2]

1. Kattintson a jobb gombbal a **AlertPacketCapturePowershell** mappára, majd hozzon létre egy **azuremodules** nevű mappát. 

4. Hozzon létre egy almappát minden szükséges modulhoz.

    ![Mappa és almappák][functions3]

    * Az.Network

    * Az.Accounts

    * Az.Resources

1. Kattintson a jobb gombbal az az **. Network** almappába, majd válassza a **fájlok feltöltése** lehetőséget. 

6. Nyissa meg az Azure-modulokat. A helyi az **. Network** mappában válassza ki a mappában található összes fájlt. Ez után válassza az **OK** gombot. 

7. Ismételje meg ezeket a lépéseket az **az. accounts** és **az. Resources** esetében.

    ![Fájlok feltöltése][functions6]

1. Miután végzett, minden mappának rendelkeznie kell a PowerShell-modul fájljaival a helyi gépről.

    ![PowerShell-fájlok][functions7]

### <a name="authentication"></a>Hitelesítés

A PowerShell-parancsmagok használatához hitelesítenie kell magát. A hitelesítést a Function alkalmazásban konfigurálhatja. A hitelesítés konfigurálásához konfigurálnia kell a környezeti változókat, és fel kell töltenie egy titkosított kulcsot a Function alkalmazásba.

> [!NOTE]
> Ez a forgatókönyv csak egy példát mutat be a Azure Functions használatával történő hitelesítés megvalósítására. Ezt többféleképpen is megteheti.

#### <a name="encrypted-credentials"></a>Titkosított hitelesítő adatok

A következő PowerShell-parancsfájl létrehoz egy **PassEncryptKey. Key** nevű kulcsfájl-fájlt. Emellett a megadott jelszó titkosított verzióját is tartalmazza. Ez a jelszó megegyezik a hitelesítéshez használt Azure Active Directory alkalmazáshoz megadott jelszóval.

```powershell
#Variables
$keypath = "C:\temp\PassEncryptKey.key"
$AESKey = New-Object Byte[] 32
$Password = "<insert a password here>"

#Keys
[Security.Cryptography.RNGCryptoServiceProvider]::Create().GetBytes($AESKey) 
Set-Content $keypath $AESKey

#Get encrypted password
$secPw = ConvertTo-SecureString -AsPlainText $Password -Force
$AESKey = Get-content $KeyPath
$Encryptedpassword = $secPw | ConvertFrom-SecureString -Key $AESKey
$Encryptedpassword
```

A Function alkalmazás App Service Editor hozzon létre egy **kulcsok** nevű mappát a **AlertPacketCapturePowerShell** területen. Ezután töltse fel az előző PowerShell-mintában létrehozott **PassEncryptKey. Key** fájlt.

![Functions-kulcs][functions8]

### <a name="retrieve-values-for-environment-variables"></a>Környezeti változók értékeinek beolvasása

A végső követelmény a hitelesítési értékek eléréséhez szükséges környezeti változók beállítása. Az alábbi listában a létrehozott környezeti változók láthatók:

* AzureClientID

* AzureTenant

* AzureCredPassword


#### <a name="azureclientid"></a>AzureClientID

Az ügyfél-azonosító a Azure Active Directory alkalmazás alkalmazás-azonosítója.

1. Ha még nem rendelkezik a használni kívánt alkalmazással, futtassa az alábbi példát egy alkalmazás létrehozásához.

    ```powershell
    $app = New-AzADApplication -DisplayName "ExampleAutomationAccount_MF" -HomePage "https://exampleapp.com" -IdentifierUris "https://exampleapp1.com/ExampleFunctionsAccount" -Password "<same password as defined earlier>"
    New-AzADServicePrincipal -ApplicationId $app.ApplicationId
    Start-Sleep 15
    New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $app.ApplicationId
    ```

   > [!NOTE]
   > Az alkalmazás létrehozásakor használt jelszónak ugyanazt a jelszót kell megadnia, amelyet korábban a kulcsfájl mentésekor hozott létre.

1. A Azure Portal válassza az **előfizetések** lehetőséget. Válassza ki a használni kívánt előfizetést, majd válassza a **hozzáférés-vezérlés (iam)** lehetőséget.

    ![A függvények IAM][functions9]

1. Válassza ki a használni kívánt fiókot, majd válassza a **Tulajdonságok** lehetőséget. Másolja ki az alkalmazás AZONOSÍTÓját.

    ![Functions-alkalmazás azonosítója][functions10]

#### <a name="azuretenant"></a>AzureTenant

Szerezze be a bérlő AZONOSÍTÓját a következő PowerShell-minta futtatásával:

```powershell
(Get-AzSubscription -SubscriptionName "<subscriptionName>").TenantId
```

#### <a name="azurecredpassword"></a>AzureCredPassword

A AzureCredPassword környezeti változó értéke az az érték, amelyet a következő PowerShell-minta futtatásával érhet el. Ez a példa az előző **titkosított hitelesítő adatok** szakaszban látható. A szükséges érték a változó kimenete `$Encryptedpassword` .  Ez az egyszerű szolgáltatás jelszava, amelyet a PowerShell-parancsfájl használatával titkosított.

```powershell
#Variables
$keypath = "C:\temp\PassEncryptKey.key"
$AESKey = New-Object Byte[] 32
$Password = "<insert a password here>"

#Keys
[Security.Cryptography.RNGCryptoServiceProvider]::Create().GetBytes($AESKey) 
Set-Content $keypath $AESKey

#Get encrypted password
$secPw = ConvertTo-SecureString -AsPlainText $Password -Force
$AESKey = Get-content $KeyPath
$Encryptedpassword = $secPw | ConvertFrom-SecureString -Key $AESKey
$Encryptedpassword
```

### <a name="store-the-environment-variables"></a>A környezeti változók tárolása

1. Nyissa meg a Function alkalmazást. Ezután válassza a **Function app Settings**(Alkalmazásbeállítások  >  **konfigurálása**) lehetőséget.

    ![Alkalmazásbeállítások konfigurálása][functions11]

1. Adja hozzá a környezeti változókat és azok értékeit az alkalmazás beállításaihoz, majd válassza a **Mentés** lehetőséget.

    ![Alkalmazásbeállítások][functions12]

### <a name="add-powershell-to-the-function"></a>PowerShell hozzáadása a függvényhez

Itt az ideje, hogy a hívások Network Watcher Az Azure-függvényből legyenek. A követelményektől függően ennek a funkciónak a megvalósítása eltérő lehet. A kód általános folyamata azonban a következő:

1. Bemeneti paraméterek feldolgozása
2. Meglévő csomagok rögzítésének lekérdezése a korlátok ellenőrzéséhez és a névfeloldási ütközések feloldásához.
3. Hozzon létre egy csomagot a megfelelő paraméterekkel.
4. A lekérdezési csomagok rögzítése rendszeresen, amíg be nem fejeződik.
5. A felhasználó értesítése arról, hogy a csomag rögzítési munkamenete befejeződött.

A következő példában a függvényben használható PowerShell-kód látható. A **subscriptionId**, a **ResourceGroupName** és a **storageAccountName** helyett értékeket kell cserélni.

```powershell
            #Import Azure PowerShell modules required to make calls to Network Watcher
            Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\Az.Accounts\Az.Accounts.psd1" -Global
            Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\Az.Network\Az.Network.psd1" -Global
            Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\Az.Resources\Az.Resources.psd1" -Global

            #Process alert request body
            $requestBody = Get-Content $req -Raw | ConvertFrom-Json

            #Storage account ID to save captures in
            $storageaccountid = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}"

            #Packet capture vars
            $packetcapturename = "PSAzureFunction"
            $packetCaptureLimit = 10
            $packetCaptureDuration = 10

            #Credentials
            $tenant = $env:AzureTenant
            $pw = $env:AzureCredPassword
            $clientid = $env:AzureClientId
            $keypath = "D:\home\site\wwwroot\AlertPacketCapturePowerShell\keys\PassEncryptKey.key"

            #Authentication
            $secpassword = $pw | ConvertTo-SecureString -Key (Get-Content $keypath)
            $credential = New-Object System.Management.Automation.PSCredential ($clientid, $secpassword)
            Connect-AzAccount -ServicePrincipal -Tenant $tenant -Credential $credential #-WarningAction SilentlyContinue | out-null


            #Get the VM that fired the alert
            if($requestBody.context.resourceType -eq "Microsoft.Compute/virtualMachines")
            {
                Write-Output ("Subscription ID: {0}" -f $requestBody.context.subscriptionId)
                Write-Output ("Resource Group:  {0}" -f $requestBody.context.resourceGroupName)
                Write-Output ("Resource Name:  {0}" -f $requestBody.context.resourceName)
                Write-Output ("Resource Type:  {0}" -f $requestBody.context.resourceType)

                #Get the Network Watcher in the VM's region
                $networkWatcher = Get-AzResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq $requestBody.context.resourceRegion}

                #Get existing packetCaptures
                $packetCaptures = Get-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher

                #Remove existing packet capture created by the function (if it exists)
                $packetCaptures | %{if($_.Name -eq $packetCaptureName)
                { 
                    Remove-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName $packetCaptureName
                }}

                #Initiate packet capture on the VM that fired the alert
                if ((Get-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher).Count -lt $packetCaptureLimit){
                    echo "Initiating Packet Capture"
                    New-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -TargetVirtualMachineId $requestBody.context.resourceId -PacketCaptureName $packetCaptureName -StorageAccountId $storageaccountid -TimeLimitInSeconds $packetCaptureDuration
                    Out-File -Encoding Ascii -FilePath $res -inputObject "Packet Capture created on ${requestBody.context.resourceID}"
                }
            } 
 ``` 
#### <a name="retrieve-the-function-url"></a>A függvény URL-címének beolvasása 
1. A függvény létrehozása után konfigurálja a riasztást úgy, hogy meghívja a függvényhez társított URL-címet. Az érték beszerzéséhez másolja a függvény URL-címét a Function alkalmazásból.

    ![A függvény URL-címének megkeresése][functions13]

2. Másolja a Function alkalmazáshoz tartozó függvény URL-címét.

    ![A függvény URL-címének másolása][2]

Ha egyéni tulajdonságokat kell megadnia a webhook POST-kérelem hasznos adataiban, tekintse meg a [webhook konfigurálása Azure metrikai riasztásra](../azure-monitor/alerts/alerts-webhooks.md)című témakört.

## <a name="configure-an-alert-on-a-vm"></a>Riasztás konfigurálása virtuális gépen

A riasztások úgy konfigurálhatók, hogy a felhasználók értesítése, ha egy adott metrika átlép egy hozzá rendelt küszöbértéket. Ebben a példában a riasztás az elküldett TCP-szegmenseken található, de a riasztás számos más metrika esetén aktiválható. Ebben a példában egy riasztás úgy van konfigurálva, hogy meghívjon egy webhookot a függvény meghívására.

### <a name="create-the-alert-rule"></a>A riasztási szabály létrehozása

Nyissa meg a meglévő virtuális gépet, majd adjon hozzá egy riasztási szabályt. A riasztások konfigurálásával kapcsolatos részletesebb dokumentációt a [riasztások létrehozása Azure monitor az Azure-szolgáltatások – Azure Portal](../azure-monitor/alerts/alerts-classic-portal.md)című témakörben talál. Adja meg a következő értékeket a **riasztási szabály** panelen, majd kattintson az **OK gombra**.

  |**Beállítás** | **Érték** | **Részletek** |
  |---|---|---|
  |**Név**|TCP_Segments_Sent_Exceeded|A riasztási szabály neve.|
  |**Leírás**|A TCP-szegmensek elküldése túllépte a küszöbértéket|A riasztási szabály leírása.|
  |**Metrika**|Eljuttatott TCP-szegmensek| A riasztás elindításához használandó metrika. |
  |**Feltétel**|Nagyobb, mint| A metrika kiértékeléséhez használandó feltétel.|
  |**Küszöbérték**|100| A riasztást kiváltó metrika értéke. Ezt az értéket a környezet érvényes értékére kell beállítani.|
  |**Időszak**|Az utóbbi öt percben| Meghatározza azt az időszakot, amelyben a küszöbértéket meg kell keresni a metrikában.|
  |**Webhook**|[webhook URL-címe a Function alkalmazásból]| Az előző lépésekben létrehozott Function alkalmazás webhook URL-címe.|

> [!NOTE]
> A TCP-szegmensek metrikája alapértelmezés szerint nincs engedélyezve. További információ a további mérőszámok engedélyezéséről: a [monitorozás és a diagnosztika engedélyezése](../azure-monitor/overview.md).

## <a name="review-the-results"></a>Az eredmények áttekintése

A riasztási eseményindítók feltételeinek meghatározása után létrejön egy csomag rögzítése. Lépjen Network Watcher, majd válassza a **csomagok rögzítése** lehetőséget. Ezen az oldalon kiválaszthatja a csomag rögzítési fájljának hivatkozását a csomag rögzítésének letöltéséhez.

![Csomagok rögzítésének megtekintése][functions14]

Ha a rögzítési fájl helyileg van tárolva, a virtuális gépre való bejelentkezéssel kérheti le.

A fájlok Azure Storage-fiókokból való letöltésével kapcsolatos utasításokért lásd: [Az Azure Blob Storage használatának első lépései a .NET használatával](../storage/blobs/storage-quickstart-blobs-dotnet.md). Egy másik eszköz, amelyet használhat, [Storage Explorer](https://storageexplorer.com/).

A rögzítés letöltését követően megtekintheti azt bármely olyan eszközzel, amely képes a **. Cap** fájl olvasására. A következő két eszközre mutató hivatkozásokat talál:

- [Microsoft Message Analyzer](/message-analyzer/microsoft-message-analyzer-operating-guide)
- [WireShark](https://www.wireshark.org/)

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan tekintheti meg a csomagokat, ha a Wireshark használatával meglátogatja a [csomagok rögzítése elemzését](network-watcher-deep-packet-inspection.md).


[1]: ./media/network-watcher-alert-triggered-packet-capture/figure1.png
[1-1]: ./media/network-watcher-alert-triggered-packet-capture/figure1-1.png
[2]: ./media/network-watcher-alert-triggered-packet-capture/figure2.png
[3]: ./media/network-watcher-alert-triggered-packet-capture/figure3.png
[functions1]:./media/network-watcher-alert-triggered-packet-capture/functions1.png
[functions2]:./media/network-watcher-alert-triggered-packet-capture/functions2.png
[functions3]:./media/network-watcher-alert-triggered-packet-capture/functions3.png
[functions4]:./media/network-watcher-alert-triggered-packet-capture/functions4.png
[functions5]:./media/network-watcher-alert-triggered-packet-capture/functions5.png
[functions6]:./media/network-watcher-alert-triggered-packet-capture/functions6.png
[functions7]:./media/network-watcher-alert-triggered-packet-capture/functions7.png
[functions8]:./media/network-watcher-alert-triggered-packet-capture/functions8.png
[functions9]:./media/network-watcher-alert-triggered-packet-capture/functions9.png
[functions10]:./media/network-watcher-alert-triggered-packet-capture/functions10.png
[functions11]:./media/network-watcher-alert-triggered-packet-capture/functions11.png
[functions12]:./media/network-watcher-alert-triggered-packet-capture/functions12.png
[functions13]:./media/network-watcher-alert-triggered-packet-capture/functions13.png
[functions14]:./media/network-watcher-alert-triggered-packet-capture/functions14.png
[scenario]:./media/network-watcher-alert-triggered-packet-capture/scenario.png