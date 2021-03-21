---
title: Távoli helyszíni erőforrások kezelése PowerShell-függvények használatával
description: Megtudhatja, hogyan konfigurálhat Hibrid kapcsolatok a Azure Relayban egy PowerShell-függvény alkalmazás helyszíni erőforrásokhoz való összekapcsolásához, amelyet aztán a helyszíni erőforrás távoli kezelésére használhat.
author: eamono
ms.topic: conceptual
ms.date: 04/26/2020
ms.author: eamono
ms.openlocfilehash: 5e01ffd8e17fda9113c7ec0fdb2c7f436b39c810
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97936906"
---
# <a name="managing-hybrid-environments-with-powershell-in-azure-functions-and-app-service-hybrid-connections"></a>Hibrid környezetek kezelése a PowerShell-lel Azure Functions és App Service Hibrid kapcsolatok

A Azure App Service Hibrid kapcsolatok funkció lehetővé teszi a más hálózatokban lévő erőforrásokhoz való hozzáférést. Erről a képességről a [hibrid kapcsolatok](../app-service/app-service-hybrid-connections.md) dokumentációjában olvashat bővebben. Ez a cikk azt ismerteti, hogyan használhatja ezt a képességet a helyszíni kiszolgálókat célzó PowerShell-függvények futtatásához. Ezzel a kiszolgálóval a helyszíni környezet összes erőforrását egy Azure PowerShell függvényből lehet kezelni.


## <a name="configure-an-on-premises-server-for-powershell-remoting"></a>Helyszíni kiszolgáló konfigurálása a PowerShell táveléréshez

A következő parancsfájl lehetővé teszi a PowerShell távelérését, és létrehoz egy új tűzfalszabály és egy WinRM HTTPS-figyelőt. Tesztelési célból önaláírt tanúsítványt használ a rendszer. Éles környezetben javasoljuk, hogy aláírt tanúsítványt használjon.

```powershell
# For configuration of WinRM, see
# https://docs.microsoft.com/windows/win32/winrm/installation-and-configuration-for-windows-remote-management.

# Enable PowerShell remoting.
Enable-PSRemoting -Force

# Create firewall rule for WinRM. The default HTTPS port is 5986.
New-NetFirewallRule -Name "WinRM HTTPS" `
                    -DisplayName "WinRM HTTPS" `
                    -Enabled True `
                    -Profile "Any" `
                    -Action "Allow" `
                    -Direction "Inbound" `
                    -LocalPort 5986 `
                    -Protocol "TCP"

# Create new self-signed-certificate to be used by WinRM.
$Thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME  -CertStoreLocation Cert:\LocalMachine\My).Thumbprint

# Create WinRM HTTPS listener.
$Cmd = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:COMPUTERNAME ""; CertificateThumbprint=""$Thumbprint""}"
cmd.exe /C $Cmd
```

## <a name="create-a-powershell-function-app-in-the-portal"></a>PowerShell-függvény alkalmazás létrehozása a portálon

A App Service Hibrid kapcsolatok funkció csak az alapszintű, a standard és az elkülönített díjszabási csomagokban érhető el. Ha a Function alkalmazást a PowerShell-lel hozza létre, hozzon létre vagy válasszon ki egyet az alábbi csomagok közül.

1. Az Azure Portal menüjében vagy a **Kezdőlapon** válassza az **Erőforrás létrehozása** elemet.

1. Az **új** lapon válassza a **számítási**  >  **függvényalkalmazás** elemet.

1. Az **alapvető** beállítások lapon használja az alábbi táblázatban megadott Function App-beállításokat.

    | Beállítás      | Ajánlott érték  | Leírás |
    | ------------ | ---------------- | ----------- |
    | **Előfizetés** | Az Ön előfizetése | Az előfizetés, amelyben létrehozta az új függvényalkalmazást. |
    | **[Erőforráscsoport](../azure-resource-manager/management/overview.md)** |  *myResourceGroup* | Az új erőforráscsoport neve, amelyben létrehozza a függvényalkalmazást. |
    | **Függvényalkalmazás neve** | Globálisan egyedi név | Az új függvényalkalmazást azonosító név. Az érvényes karakterek az `a-z` (kis- és nagybetűk megkülönböztetése nélkül) `0-9`és az `-`.  |
    |**Közzététel**| Kód | Kódfájlok közzétételét teszi lehetővé egy Docker-tárolóban. |
    | **Futtatókörnyezet verme** | Elsődleges nyelv | Válassza a PowerShell mag lehetőséget. |
    |**Verzió**| Verziószám | Válassza ki a telepített futtatókörnyezet verzióját.  |
    |**Régió**| Előnyben részesített régió | Válasszon egy Önhöz közeli [régiót](https://azure.microsoft.com/regions/) vagy a funkciókhoz tartozó egyéb szolgáltatások közelében. |

    :::image type="content" source="./media/functions-hybrid-powershell/function-app-create-basics.png" alt-text="Function-alkalmazás létrehozása – alapismeretek." border="true":::

1. Válassza a **Tovább: Üzemeltetés** lehetőségre. Az **üzemeltetés** lapon adja meg a következő beállításokat.

    | Beállítás      | Ajánlott érték  | Leírás |
    | ------------ | ---------------- | ----------- |
    | **[Storage-fiók](../storage/common/storage-account-create.md)** |  Globálisan egyedi név |  Hozzon létre egy tárfiókot a függvényalkalmazás számára. A Storage-fiókok nevének 3 – 24 karakter hosszúnak kell lennie, és csak számokat és kisbetűket tartalmazhat. Meglévő fiókot is használhat, amelynek meg kell felelnie a [Storage-fiókra vonatkozó követelményeknek](../azure-functions/storage-considerations.md#storage-account-requirements). |
    |**Operációs rendszer**| Előnyben részesített operációs rendszer | Az operációs rendszer előre ki van választva a futásidejű verem kiválasztása alapján, de szükség esetén módosíthatja a beállítást. |
    | **[Csomag típusa](../azure-functions/functions-scale.md)** | **App Service-csomag** | Válassza az **app Service-csomag** elemet. Ha App Service-csomagot használ, foglalkoznia kell a [függvényalkalmazás méretezésével](../azure-functions/functions-scale.md).  |

    :::image type="content" source="./media/functions-hybrid-powershell/function-app-create-hosting.png" alt-text="Hozzon létre egy függvény alkalmazás-üzemeltetést." border="true":::

1. Válassza a **Tovább: figyelés** lehetőséget. A **figyelés** lapon adja meg a következő beállításokat.

    | Beállítás      | Ajánlott érték  | Leírás |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../azure-functions/functions-monitoring.md)** | Alapértelmezett | Létrehoz egy Application Insights erőforrást ugyanahhoz az *alkalmazáshoz* a legközelebbi támogatott régióban. A beállítás kibontásával vagy az **új létrehozása** lehetőség kiválasztásával módosíthatja a Application Insights nevét, vagy választhat egy másik régiót egy olyan [Azure-földrajzban](https://azure.microsoft.com/global-infrastructure/geographies/) , ahol az adatait tárolni szeretné. |

    :::image type="content" source="./media/functions-hybrid-powershell/function-app-create-monitoring.png" alt-text="Function-alkalmazás létrehozása – figyelés." border="true":::

1. Válassza a **felülvizsgálat + létrehozás** lehetőséget az alkalmazás-konfiguráció kiválasztásának áttekintéséhez.

1. A **felülvizsgálat + létrehozás** oldalon tekintse át a beállításokat, majd válassza a **Létrehozás** lehetőséget a Function alkalmazás kiépítéséhez és üzembe helyezéséhez.

1. Válassza az **értesítések** ikont a portál jobb felső sarkában, és figyelje meg az **üzembe helyezés sikeres** üzenetét.

1. Az új függvényalkalmazás megtekintéséhez válassza az **Erőforrás megnyitása** lehetőséget. Kiválaszthatja **a rögzítés az irányítópulton** lehetőséget is. A rögzítéssel egyszerűbbé válik a Function app-erőforráshoz való visszatérés az irányítópultról.

## <a name="create-a-hybrid-connection-for-the-function-app"></a>Hibrid kapcsolatok létrehozása a Function alkalmazáshoz

A hibrid kapcsolatok konfigurálása a Function alkalmazás hálózatkezelési szakasza alapján történik:

1. Az imént létrehozott Function alkalmazás **Beállítások** területén válassza a **hálózatkezelés** lehetőséget. 
1. Válassza **a hibrid kapcsolatok végpontok konfigurálása** lehetőséget.
   
    :::image type="content" source="./media/functions-hybrid-powershell/configure-hybrid-connection-endpoint.png" alt-text="Konfigurálja a hibrid kapcsolatok végpontját." border="true":::

1. Válassza a **hibrid kapcsolatok hozzáadása** lehetőséget.
   
    :::image type="content" source="./media/functions-hybrid-powershell/hybrid-connection-overview.png" alt-text="Hibrid kapcsolatok hozzáadása." border="true":::

1. Adja meg a hibrid kapcsolatok adatait, amint az a következő képernyőképen látható. Lehetősége van arra, hogy a **végpont gazdagépének** beállításai megegyezzenek a helyszíni kiszolgáló állomásneve, hogy a kiszolgáló később is megjegyezze a távoli parancsok futtatásakor. A port megegyezik a kiszolgálón korábban definiált alapértelmezett Windows távfelügyeleti szolgáltatás portjával.
  
      :::image type="content" source="./media/functions-hybrid-powershell/add-hybrid-connection.png" alt-text="Hibrid kapcsolatok hozzáadása." border="true":::

    | Beállítás      | Ajánlott érték  |
    | ------------ | ---------------- |
    | **Hibrid kapcsolatok neve** | ContosoHybridOnPremisesServer |
    | **Végponti állomás** | finance1 |
    | **Végpont portja** | 5986 |
    | **Servicebus-névtér** | Új létrehozása |
    | **Hely** | Válasszon ki egy elérhető helyet |
    | **Név** | contosopowershellhybrid | 

1. A hibrid kapcsolatok létrehozásához kattintson **az OK gombra** .

## <a name="download-and-install-the-hybrid-connection"></a>A hibrid kapcsolatok letöltése és telepítése

1. Válassza a **Csatlakozáskezelő letöltése** lehetőséget, hogy az *. msi* fájlt helyileg mentse a számítógépre.

    :::image type="content" source="./media/functions-hybrid-powershell/download-hybrid-connection-installer.png" alt-text="Töltse le a telepítőt." border="true":::

1. Másolja az *. msi* fájlt a helyi számítógépről a helyszíni kiszolgálóra.
1. Futtassa a hibridkapcsolat-kezelő telepítőjét a szolgáltatás a helyszíni kiszolgálón való telepítéséhez.

    :::image type="content" source="./media/functions-hybrid-powershell/hybrid-installation.png" alt-text="Telepítse a hibrid kapcsolatokat." border="true":::

1. A portálon nyissa meg a hibrid kapcsolatokat, majd másolja az átjáró kapcsolódási karakterláncát a vágólapra.

    :::image type="content" source="./media/functions-hybrid-powershell/copy-hybrid-connection.png" alt-text="Másolja a hibrid kapcsolatok sztringjét." border="true":::

1. Nyissa meg a hibridkapcsolat-kezelő felhasználói felületet a helyszíni kiszolgálón.

    :::image type="content" source="./media/functions-hybrid-powershell/hybrid-connection-ui.png" alt-text="Nyissa meg a hibrid kapcsolatok felhasználói felületét." border="true":::

1. Válassza a **bevitel manuálisan** lehetőséget, és illessze be a kapcsolatok karakterláncát a vágólapról.

    :::image type="content" source="./media/functions-hybrid-powershell/enter-manual-connection.png" alt-text="Illessze be a hibrid kapcsolatokat." border="true":::

1. Ha nem csatlakoztatottként jelenik meg, indítsa újra a hibridkapcsolat-kezelő a PowerShellből.
    ```powershell
    Restart-Service HybridConnectionManager
    ```

## <a name="create-an-app-setting-for-the-password-of-an-administrator-account"></a>Alkalmazás-beállítás létrehozása rendszergazdai fiók jelszavához

1. A Function alkalmazás **beállításai** területen válassza a **Konfigurálás** lehetőséget. 
1. Válassza az **+ új alkalmazás beállítása** lehetőséget.

    :::image type="content" source="./media/functions-hybrid-powershell/select-configuration.png" alt-text="Adja meg a rendszergazdai fiók jelszavát." border="true":::

1. Nevezze el a beállítás **ContosoUserPassword**, és adja meg a jelszót. Válassza az **OK** lehetőséget.
1. Válassza a **Mentés** lehetőséget a jelszó tárolásához a Function alkalmazásban.

    :::image type="content" source="./media/functions-hybrid-powershell/save-administrator-password.png" alt-text="Mentse a rendszergazdai fiók jelszavát." border="true":::

## <a name="create-a-function-http-trigger"></a>Függvény HTTP-triggerének létrehozása

1. A Function alkalmazásban válassza a **függvények**, majd a **+ Hozzáadás** lehetőséget.

    :::image type="content" source="./media/functions-hybrid-powershell/create-http-trigger-function.png" alt-text="Hozzon létre új HTTP-triggert." border="true":::

1. Válassza ki a **http-trigger** sablonját.

    :::image type="content" source="./media/functions-hybrid-powershell/select-http-trigger-template.png" alt-text="Válassza ki a HTTP-trigger sablonját." border="true":::

1. Nevezze el az új függvényt, és válassza a **létrehozási függvény** lehetőséget.

    :::image type="content" source="./media/functions-hybrid-powershell/create-new-http-function.png" alt-text="Nevezze el és hozza létre az új HTTP trigger függvényt." border="true":::

## <a name="test-the-function"></a>A függvény tesztelése

1. Az új függvényben válassza a **Code + test** elemet. Cserélje le a PowerShell-kódot a sablonból a következő kódra:

    ```powershell
    # Input bindings are passed in via param block.
    param($Request, $TriggerMetadata)
    
    # Write to the Azure Functions log stream.
    Write-Output "PowerShell HTTP trigger function processed a request."
    
    # Note that ContosoUserPassword is a function app setting, so I can access it as $env:ContosoUserPassword.
    $UserName = "ContosoUser"
    $securedPassword = ConvertTo-SecureString  $Env:ContosoUserPassword -AsPlainText -Force
    $Credential = [System.management.automation.pscredential]::new($UserName, $SecuredPassword)
    
    # This is the name of the hybrid connection Endpoint.
    $HybridEndpoint = "finance1"
    
    $Script = {
        Param(
            [Parameter(Mandatory=$True)]
            [String] $Service
        )
        Get-Service $Service
    }
    
    Write-Output "Scenario 1: Running command via Invoke-Command"
    Invoke-Command -ComputerName $HybridEndpoint `
                   -Credential $Credential `
                   -Port 5986 `
                   -UseSSL `
                   -ScriptBlock $Script `
                   -ArgumentList "*" `
                   -SessionOption (New-PSSessionOption -SkipCACheck)
    ```

1. Kattintson a **Mentés** gombra.

    :::image type="content" source="./media/functions-hybrid-powershell/save-http-function.png" alt-text="Módosítsa a PowerShell-kódot, és mentse a HTTP trigger függvényt." border="true":::

 1. Válassza a **teszt** lehetőséget, majd válassza a **Futtatás** lehetőséget a függvény teszteléséhez. Tekintse át a naplókat, és ellenőrizze, hogy a teszt sikeres volt-e.

     :::image type="content" source="./media/functions-hybrid-powershell/test-function-hybrid.png" alt-text="HTTP-trigger funkció tesztelése." border="true":::

## <a name="managing-other-systems-on-premises"></a>Más rendszerek helyszíni kezelése

A csatlakoztatott helyszíni kiszolgáló használatával kapcsolódhat a helyi környezetben lévő más kiszolgálókhoz és felügyeleti rendszerekhez. Így a PowerShell-függvények segítségével kezelheti az adatközpont-műveleteket az Azure-ban. A következő parancsfájl egy PowerShell-konfigurációs munkamenetet regisztrál, amely a megadott hitelesítő adatok alatt fut. A hitelesítő adatoknak a távoli kiszolgálók rendszergazdájának kell lenniük. Ezt a konfigurációt használhatja a helyi kiszolgálón vagy adatközpontban található többi végpont eléréséhez.

```powershell
# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Note that ContosoUserPassword is a function app setting, so I can access it as $env:ContosoUserPassword.
$UserName = "ContosoUser"
$SecuredPassword = ConvertTo-SecureString  $Env:ContosoUserPassword -AsPlainText -Force
$Credential = [System.management.automation.pscredential]::new($UserName, $SecuredPassword)

# This is the name of the hybrid connection Endpoint.
$HybridEndpoint = "finance1"

# The remote server that will be connected to run remote PowerShell commands on
$RemoteServer = "finance2".

Write-Output "Use hybrid connection server as a jump box to connect to a remote machine"

# We are registering an endpoint that runs under credentials ($Credential) that has access to the remote server.
$SessionName = "HybridSession"
$ScriptCommand = {
    param (
        [Parameter(Mandatory=$True)]
        $SessionName)

    if (-not (Get-PSSessionConfiguration -Name $SessionName -ErrorAction SilentlyContinue))
    {
        Register-PSSessionConfiguration -Name $SessionName -RunAsCredential $Using:Credential
    }
}

Write-Output "Registering session on hybrid connection jumpbox"
Invoke-Command -ComputerName $HybridEndpoint `
               -Credential $Credential `
               -Port 5986 `
               -UseSSL `
               -ScriptBlock $ScriptCommand `
               -ArgumentList $SessionName `
               -SessionOption (New-PSSessionOption -SkipCACheck)

# Script to run on the jump box to run against the second machine.
$RemoteScriptCommand = {
    param (
        [Parameter(Mandatory=$True)]
        $ComputerName)
        # Write out the hostname of the hybrid connection server.
        hostname
        # Write out the hostname of the remote server.
        Invoke-Command -ComputerName $ComputerName -Credential $Using:Credential -ScriptBlock {hostname} `
                        -UseSSL -Port 5986 -SessionOption (New-PSSessionOption -SkipCACheck)
}

Write-Output "Running command against remote machine via jumpbox by connecting to the PowerShell configuration session"
Invoke-Command -ComputerName $HybridEndpoint `
               -Credential $Credential `
               -Port 5986 `
               -UseSSL `
               -ScriptBlock $RemoteScriptCommand `
               -ArgumentList $RemoteServer `
               -SessionOption (New-PSSessionOption -SkipCACheck) `
               -ConfigurationName $SessionName
```

Cserélje le a parancsfájlban az alábbi változókat a környezet megfelelő értékeire:
* $HybridEndpoint
* $RemoteServer

Az előző két forgatókönyvben a Azure Functions és Hibrid kapcsolatok PowerShell használatával csatlakozhat a helyszíni környezetekhez, és kezelheti azokat. Javasoljuk, hogy ismerkedjen meg a [functions](./functions-reference-powershell.md) [hibrid kapcsolatok](../app-service/app-service-hybrid-connections.md) és PowerShell szolgáltatásával.

Az Azure [Virtual Network](./functions-create-vnet.md) szolgáltatással a helyszíni környezethez is csatlakozhat Azure Functionson keresztül.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"] 
> [További információ a PowerShell-függvények használatáról](functions-reference-powershell.md)
