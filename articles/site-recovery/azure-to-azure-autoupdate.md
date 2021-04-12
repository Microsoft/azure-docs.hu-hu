---
title: A mobilitási szolgáltatás automatikus frissítése Azure Site Recovery
description: A mobilitási szolgáltatás automatikus frissítésének áttekintése az Azure-beli virtuális gépek Azure Site Recovery használatával történő replikálása során.
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/02/2020
ms.author: sideeksh
ms.openlocfilehash: 42d3c74229ab7eeec0ac716073a9e631775fd002
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96187340"
---
# <a name="automatic-update-of-the-mobility-service-in-azure-to-azure-replication"></a>A mobilitási szolgáltatás automatikus frissítése az Azure-ról az Azure-ba történő replikációra

Azure Site Recovery a havi kiadási lépésszám segítségével elháríthatja a problémákat, és javíthatja a meglévő szolgáltatásokat, illetve újakat vehet fel. Ha továbbra is meg szeretné őrizni a szolgáltatást, minden hónapban meg kell terveznie a javítások telepítését. Az egyes frissítésekhez kapcsolódó terhelés elkerülése érdekében engedélyezheti Site Recovery számára az összetevők frissítését.

Az [Azure – Azure vész-helyreállítási architektúrában](azure-to-azure-architecture.md)említettek szerint a mobilitási szolgáltatás minden olyan Azure-beli virtuális gépre (VM) telepítve van, amelyen engedélyezve van az egyik Azure-régióból a másikba való replikáció. Ha automatikus frissítéseket használ, minden új kiadás frissíti a mobilitási szolgáltatás bővítményét.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="how-automatic-updates-work"></a>Az automatikus frissítések működése

Ha a Site Recovery használatával kezeli a frissítéseket, az az Azure-szolgáltatások által használt globális runbook helyez üzembe egy Automation-fiókon keresztül, amely ugyanabban az előfizetésben jön létre, mint a tároló. Mindegyik tároló egy Automation-fiókot használ. A tárolóban lévő minden egyes virtuális gép esetében a runbook ellenőrzi az aktív automatikus frissítéseket. Ha a mobilitási szolgáltatás bővítményének újabb verziója érhető el, a rendszer telepíti a frissítést.

Az alapértelmezett runbook-ütemterv napi rendszerességgel 12:00 ÓRAKOR történik a replikált virtuális gép Földrajzának időzónájában. A runbook-ütemtervet az Automation-fiók használatával is módosíthatja.

> [!NOTE]
> A 35-es [kumulatív frissítéstől](site-recovery-whats-new.md#updates-march-2019)kezdve kiválaszthatja a frissítésekhez használandó meglévő Automation-fiókot. A 35-es kumulatív frissítés előtt a Site Recovery alapértelmezés szerint létrehozta az Automation-fiókot. Ezt a beállítást csak akkor válassza ki, ha engedélyezi egy virtuális gép replikálását. Nem érhető el olyan virtuális géphez, amelyen már engedélyezve van a replikáció. A kiválasztott beállítás az azonos tárolóban védett összes Azure-beli virtuális gépre vonatkozik.

Az automatikus frissítések bekapcsolása nem igényli az Azure-beli virtuális gépek újraindítását, vagy a folyamatban lévő replikációt is befolyásolja.

Az Automation-fiókban a feladatok számlázása a hónap során felhasznált feladatok futtatókörnyezeti percének számától függ. A feladatok végrehajtása naponta körülbelül egy percet vesz igénybe, és ingyenes egységként van lefoglalva. Alapértelmezés szerint a 500 perc egy Automation-fiók ingyenes egysége, ahogy az az alábbi táblázatban is látható:

| A csomagban foglalt ingyenes egységek (havonta) | Ár |
|---|---|
| Feladatok futtatókörnyezete 500 perc | ₹ 0.14/perc

## <a name="enable-automatic-updates"></a>Automatikus frissítések engedélyezése

A bővítmény frissítéseinek Site Recovery többféleképpen is kezelhetők:

- [Kezelés a replikálás engedélyezése lépés részeként](#manage-as-part-of-the-enable-replication-step)
- [A bővítmény frissítési beállításainak váltása a tárolón belül](#toggle-the-extension-update-settings-inside-the-vault)
- [Frissítések manuális kezelése](#manage-updates-manually)

### <a name="manage-as-part-of-the-enable-replication-step"></a>Kezelés a replikálás engedélyezése lépés részeként

Ha engedélyezi egy virtuális gép replikálását a virtuálisgép- [nézetből](azure-to-azure-quickstart.md) vagy [a Recovery Services](azure-to-azure-how-to-enable-replication.md)-tárolóból, engedélyezheti site Recovery számára a site Recovery bővítmény frissítéseinek kezelését, vagy manuálisan is kezelheti azt.

:::image type="content" source="./media/azure-to-azure-autoupdate/enable-rep.png" alt-text="Bővítmény beállításai":::

### <a name="toggle-the-extension-update-settings-inside-the-vault"></a>A bővítmény frissítési beállításainak váltása a tárolón belül

1. A Recovery Services-tárolóban lépjen a   >  **site Recovery infrastruktúra** kezelése elemre.
1. **Az Azure Virtual Machines**  >  **Extension Update Settings**(az Azure-ban) lehetőségnél  >  **engedélyezze a site Recovery kezelését**, válassza **a be** lehetőséget.

   Ha manuálisan szeretné kezelni a bővítményt, válassza a **ki** lehetőséget.

1. Kattintson a **Mentés** gombra.

:::image type="content" source="./media/azure-to-azure-autoupdate/vault-toggle.png" alt-text="Bővítmény frissítési beállításai":::

> [!IMPORTANT]
> Ha a **kezelés engedélyezése site Recovery lehetőséget** választja, a rendszer a tárolóban lévő összes virtuális gépre alkalmazza a beállítást.

> [!NOTE]
> Bármelyik lehetőség értesíti a frissítések kezeléséhez használt Automation-fiókról. Ha első alkalommal használja ezt a szolgáltatást egy tárolóban, a rendszer alapértelmezés szerint új Automation-fiókot hoz létre. Másik lehetőségként testreszabhatja a beállítást, és kiválaszthat egy meglévő Automation-fiókot. A definiálást követően minden további művelet, amely engedélyezi a replikációt ugyanazon a tárolón, a kiválasztott Automation-fiókot fogja használni. Jelenleg a legördülő menü csak azokat az Automation-fiókokat sorolja fel, amelyek ugyanabban az erőforráscsoporthoz találhatók, mint a tároló.

> [!IMPORTANT]
> Az alábbi szkriptet Automation-fiók környezetében kell futtatni.
Egyéni Automation-fiók esetén használja a következő parancsfájlt:

```azurepowershell
param(
    [Parameter(Mandatory=$true)]
    [String] $VaultResourceId,
    [Parameter(Mandatory=$true)]
    [ValidateSet("Enabled",'Disabled')]
    [Alias("Enabled or Disabled")]
    [String] $AutoUpdateAction,
    [Parameter(Mandatory=$false)]
    [String] $AutomationAccountArmId
)
$SiteRecoveryRunbookName = "Modify-AutoUpdateForVaultForPatner"
$TaskId = [guid]::NewGuid().ToString()
$SubscriptionId = "00000000-0000-0000-0000-000000000000"
$AsrApiVersion = "2018-01-10"
$RunAsConnectionName = "AzureRunAsConnection"
$ArmEndPoint = "https://management.azure.com"
$AadAuthority = "https://login.windows.net/"
$AadAudience = "https://management.core.windows.net/"
$AzureEnvironment = "AzureCloud"
$Timeout = "160"
function Throw-TerminatingErrorMessage
{
        Param
    (
        [Parameter(Mandatory=$true)]
        [String]
        $Message
        )
    throw ("Message: {0}, TaskId: {1}.") -f $Message, $TaskId
}
function Write-Tracing
{
        Param
    (
        [Parameter(Mandatory=$true)]
        [ValidateSet("Informational", "Warning", "ErrorLevel", "Succeeded", IgnoreCase = $true)]
                [String]
        $Level,
        [Parameter(Mandatory=$true)]
        [String]
        $Message,
            [Switch]
        $DisplayMessageToUser
        )
    Write-Output $Message
}
function Write-InformationTracing
{
        Param
    (
        [Parameter(Mandatory=$true)]
        [String]
        $Message
        )
    Write-Tracing -Message $Message -Level Informational -DisplayMessageToUser
}
function ValidateInput()
{
    try
    {
        if(!$VaultResourceId.StartsWith("/subscriptions", [System.StringComparison]::OrdinalIgnoreCase))
        {
            $ErrorMessage = "The vault resource id should start with /subscriptions."
            throw $ErrorMessage
        }
        $Tokens = $VaultResourceId.SubString(1).Split("/")
        if(!($Tokens.Count % 2 -eq 0))
        {
            $ErrorMessage = ("Odd Number of tokens: {0}." -f $Tokens.Count)
            throw $ErrorMessage
        }
        if(!($Tokens.Count/2 -eq 4))
        {
            $ErrorMessage = ("Invalid number of resource in vault ARM id expected:4, actual:{0}." -f ($Tokens.Count/2))
            throw $ErrorMessage
        }
        if($AutoUpdateAction -ieq "Enabled" -and [string]::IsNullOrEmpty($AutomationAccountArmId))
        {
            $ErrorMessage = ("The automation account ARM id should not be null or empty when AutoUpdateAction is enabled.")
            throw $ErrorMessage
        }
    }
    catch
    {
        $ErrorMessage = ("ValidateInput failed with [Exception: {0}]." -f $_.Exception)
        Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
        Throw-TerminatingErrorMessage -Message $ErrorMessage
    }
}
function Initialize-SubscriptionId()
{
    try
    {
        $Tokens = $VaultResourceId.SubString(1).Split("/")
        $Count = 0
                $ArmResources = @{}
        while($Count -lt $Tokens.Count)
        {
            $ArmResources[$Tokens[$Count]] = $Tokens[$Count+1]
            $Count = $Count + 2
        }
                return $ArmResources["subscriptions"]
    }
    catch
    {
        Write-Tracing -Level ErrorLevel -Message ("Initialize-SubscriptionId: failed with [Exception: {0}]." -f $_.Exception) -DisplayMessageToUser
        throw
    }
}
function Invoke-InternalRestMethod($Uri, $Headers, [ref]$Result)
{
    $RetryCount = 0
    $MaxRetry = 3
    do
    {
        try
        {
            $ResultObject = Invoke-RestMethod -Uri $Uri -Headers $Headers
            ($Result.Value) += ($ResultObject)
            break
        }
        catch
        {
            Write-InformationTracing ("Retry Count: {0}, Exception: {1}." -f $RetryCount, $_.Exception)
            $RetryCount++
            if(!($RetryCount -le $MaxRetry))
            {
                throw
            }
            Start-Sleep -Milliseconds 2000
        }
    }while($true)
}
function Invoke-InternalWebRequest($Uri, $Headers, $Method, $Body, $ContentType, [ref]$Result)
{
    $RetryCount = 0
    $MaxRetry = 3
    do
    {
        try
        {
            $ResultObject = Invoke-WebRequest -Uri $UpdateUrl -Headers $Header -Method 'PATCH' `
                -Body $InputJson  -ContentType "application/json" -UseBasicParsing
            ($Result.Value) += ($ResultObject)
            break
        }
        catch
        {
            Write-InformationTracing ("Retry Count: {0}, Exception: {1}." -f $RetryCount, $_.Exception)
            $RetryCount++
            if(!($RetryCount -le $MaxRetry))
            {
                throw
            }
            Start-Sleep -Milliseconds 2000
        }
    }while($true)
}
function Get-Header([ref]$Header, $AadAudience, $AadAuthority, $RunAsConnectionName){
    try
    {
        $RunAsConnection = Get-AutomationConnection -Name $RunAsConnectionName
        $TenantId = $RunAsConnection.TenantId
        $ApplicationId = $RunAsConnection.ApplicationId
        $CertificateThumbprint = $RunAsConnection.CertificateThumbprint
        $Path = "cert:\CurrentUser\My\{0}" -f $CertificateThumbprint
        $Secret = Get-ChildItem -Path $Path
        $ClientCredential = New-Object Microsoft.IdentityModel.Clients.ActiveDirectory.ClientAssertionCertificate(
                $ApplicationId,
                $Secret)
        # Trim the forward slash from the AadAuthority if it exist.
        $AadAuthority = $AadAuthority.TrimEnd("/")
        $AuthContext = New-Object Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext(
            "{0}/{1}" -f $AadAuthority, $TenantId )
        $AuthenticationResult = $authContext.AcquireToken($AadAudience, $Clientcredential)
        $Header.Value['Content-Type'] = 'application\json'
        $Header.Value['Authorization'] = $AuthenticationResult.CreateAuthorizationHeader()
        $Header.Value["x-ms-client-request-id"] = $TaskId + "/" + (New-Guid).ToString() + "-" + (Get-Date).ToString("u")
    }
    catch
    {
        $ErrorMessage = ("Get-BearerToken: failed with [Exception: {0}]." -f $_.Exception)
        Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
        Throw-TerminatingErrorMessage -Message $ErrorMessage
    }
}
function Get-ProtectionContainerToBeModified([ref] $ContainerMappingList)
{
    try
    {
        Write-InformationTracing ("Get protection container mappings : {0}." -f $VaultResourceId)
        $ContainerMappingListUrl = $ArmEndPoint + $VaultResourceId + "/replicationProtectionContainerMappings" + "?api-version=" + $AsrApiVersion
        Write-InformationTracing ("Getting the bearer token and the header.")
        Get-Header ([ref]$Header) $AadAudience $AadAuthority $RunAsConnectionName
        $Result = @()
        Invoke-InternalRestMethod -Uri $ContainerMappingListUrl -Headers $header -Result ([ref]$Result)
        $ContainerMappings = $Result[0]
        Write-InformationTracing ("Total retrieved container mappings: {0}." -f $ContainerMappings.Value.Count)
        foreach($Mapping in $ContainerMappings.Value)
        {
            if(($Mapping.properties.providerSpecificDetails -eq $null) -or ($Mapping.properties.providerSpecificDetails.instanceType -ine "A2A"))
            {
                Write-InformationTracing ("Mapping properties: {0}." -f ($Mapping.properties))
                Write-InformationTracing ("Ignoring container mapping: {0} as the provider does not match." -f ($Mapping.Id))
                continue;
            }
            if($Mapping.Properties.State -ine "Paired")
            {
                Write-InformationTracing ("Ignoring container mapping: {0} as the state is not paired." -f ($Mapping.Id))
                continue;
            }
            Write-InformationTracing ("Provider specific details {0}." -f ($Mapping.properties.providerSpecificDetails))
            $MappingAutoUpdateStatus = $Mapping.properties.providerSpecificDetails.agentAutoUpdateStatus
            $MappingAutomationAccountArmId = $Mapping.properties.providerSpecificDetails.automationAccountArmId
            $MappingHealthErrorCount = $Mapping.properties.HealthErrorDetails.Count
            if($AutoUpdateAction -ieq "Enabled" -and
                ($MappingAutoUpdateStatus -ieq "Enabled") -and
                ($MappingAutomationAccountArmId -ieq $AutomationAccountArmId) -and
                ($MappingHealthErrorCount -eq 0))
            {
                Write-InformationTracing ("Provider specific details {0}." -f ($Mapping.properties))
                Write-InformationTracing ("Ignoring container mapping: {0} as the auto update is already enabled and is healthy." -f ($Mapping.Id))
                continue;
            }
            ($ContainerMappingList.Value).Add($Mapping.id)
        }
    }
    catch
    {
        $ErrorMessage = ("Get-ProtectionContainerToBeModified: failed with [Exception: {0}]." -f $_.Exception)
        Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
        Throw-TerminatingErrorMessage -Message $ErrorMessage
    }
}
$OperationStartTime = Get-Date
$ContainerMappingList = New-Object System.Collections.Generic.List[System.String]
$JobsInProgressList = @()
$JobsCompletedSuccessList = @()
$JobsCompletedFailedList = @()
$JobsFailedToStart = 0
$JobsTimedOut = 0
$Header = @{}
$AzureRMProfile = Get-Module -ListAvailable -Name AzureRM.Profile | Select Name, Version, Path
$AzureRmProfileModulePath = Split-Path -Parent $AzureRMProfile.Path
Add-Type -Path (Join-Path $AzureRmProfileModulePath "Microsoft.IdentityModel.Clients.ActiveDirectory.dll")
$Inputs = ("Tracing inputs VaultResourceId: {0}, Timeout: {1}, AutoUpdateAction: {2}, AutomationAccountArmId: {3}." -f $VaultResourceId, $Timeout, $AutoUpdateAction, $AutomationAccountArmId)
Write-Tracing -Message $Inputs -Level Informational -DisplayMessageToUser
$CloudConfig = ("Tracing cloud configuration ArmEndPoint: {0}, AadAuthority: {1}, AadAudience: {2}." -f $ArmEndPoint, $AadAuthority, $AadAudience)
Write-Tracing -Message $CloudConfig -Level Informational -DisplayMessageToUser
$AutomationConfig = ("Tracing automation configuration RunAsConnectionName: {0}." -f $RunAsConnectionName)
Write-Tracing -Message $AutomationConfig -Level Informational -DisplayMessageToUser
ValidateInput
$SubscriptionId = Initialize-SubscriptionId
Get-ProtectionContainerToBeModified ([ref]$ContainerMappingList)
$Input = @{
  "properties"= @{
    "providerSpecificInput"= @{
        "instanceType" = "A2A"
        "agentAutoUpdateStatus" = $AutoUpdateAction
        "automationAccountArmId" = $AutomationAccountArmId
    }
  }
}
$InputJson = $Input |  ConvertTo-Json
if ($ContainerMappingList.Count -eq 0)
{
    Write-Tracing -Level Succeeded -Message ("Exiting as there are no container mappings to be modified.") -DisplayMessageToUser
    exit
}
Write-InformationTracing ("Container mappings to be updated has been retrieved with count: {0}." -f $ContainerMappingList.Count)
try
{
    Write-InformationTracing ("Start the modify container mapping jobs.")
    ForEach($Mapping in $ContainerMappingList)
    {
    try {
            $UpdateUrl = $ArmEndPoint + $Mapping + "?api-version=" + $AsrApiVersion
            Get-Header ([ref]$Header) $AadAudience $AadAuthority $RunAsConnectionName
            $Result = @()
            Invoke-InternalWebRequest -Uri $UpdateUrl -Headers $Header -Method 'PATCH' `
                -Body $InputJson  -ContentType "application/json" -Result ([ref]$Result)
            $Result = $Result[0]
            $JobAsyncUrl = $Result.Headers['Azure-AsyncOperation']
            Write-InformationTracing ("The modify container mapping job invoked with async url: {0}." -f $JobAsyncUrl)
            $JobsInProgressList += $JobAsyncUrl;
            # Rate controlling the set calls to maximum 60 calls per minute.
            # ASR throttling for set calls is 200 in 1 minute.
            Start-Sleep -Milliseconds 1000
        }
        catch{
            Write-InformationTracing ("The modify container mappings job creation failed for: {0}." -f $Ru)
            Write-InformationTracing $_
            $JobsFailedToStart++
        }
    }
    Write-InformationTracing ("Total modify container mappings has been initiated: {0}." -f $JobsInProgressList.Count)
}
catch
{
    $ErrorMessage = ("Modify container mapping jobs failed with [Exception: {0}]." -f $_.Exception)
    Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
    Throw-TerminatingErrorMessage -Message $ErrorMessage
}
try
{
    while($JobsInProgressList.Count -ne 0)
    {
        Sleep -Seconds 30
        $JobsInProgressListInternal = @()
        ForEach($JobAsyncUrl in $JobsInProgressList)
        {
            try
            {
                Get-Header ([ref]$Header) $AadAudience $AadAuthority $RunAsConnectionName
                $Result = Invoke-RestMethod -Uri $JobAsyncUrl -Headers $header
                $JobState = $Result.Status
                if($JobState -ieq "InProgress")
                {
                    $JobsInProgressListInternal += $JobAsyncUrl
                }
                elseif($JobState -ieq "Succeeded" -or `
                    $JobState -ieq "PartiallySucceeded" -or `
                    $JobState -ieq "CompletedWithInformation")
                {
                    Write-InformationTracing ("Jobs succeeded with state: {0}." -f $JobState)
                    $JobsCompletedSuccessList += $JobAsyncUrl
                }
                else
                {
                    Write-InformationTracing ("Jobs failed with state: {0}." -f $JobState)
                    $JobsCompletedFailedList += $JobAsyncUrl
                }
            }
            catch
            {
                Write-InformationTracing ("The get job failed with: {0}. Ignoring the exception and retrying the next job." -f $_.Exception)
                # The job on which the tracking failed, will be considered in progress and tried again later.
                $JobsInProgressListInternal += $JobAsyncUrl
            }
            # Rate controlling the get calls to maximum 120 calls each minute.
            # ASR throttling for get calls is 10000 in 60 minutes.
            Start-Sleep -Milliseconds 500
        }
        Write-InformationTracing ("Jobs remaining {0}." -f $JobsInProgressListInternal.Count)
        $CurrentTime = Get-Date
        if($CurrentTime -gt $OperationStartTime.AddMinutes($Timeout))
        {
            Write-InformationTracing ("Tracing modify cloud pairing jobs has timed out.")
            $JobsTimedOut = $JobsInProgressListInternal.Count
            $JobsInProgressListInternal = @()
        }
        $JobsInProgressList = $JobsInProgressListInternal
    }
}
catch
{
    $ErrorMessage = ("Tracking modify cloud pairing jobs failed with [Exception: {0}]." -f $_.Exception)
    Write-Tracing -Level ErrorLevel -Message $ErrorMessage  -DisplayMessageToUser
    Throw-TerminatingErrorMessage -Message $ErrorMessage
}
Write-InformationTracing ("Tracking modify cloud pairing jobs completed.")
Write-InformationTracing ("Modify cloud pairing jobs success: {0}." -f $JobsCompletedSuccessList.Count)
Write-InformationTracing ("Modify cloud pairing jobs failed: {0}." -f $JobsCompletedFailedList.Count)
Write-InformationTracing ("Modify cloud pairing jobs failed to start: {0}." -f $JobsFailedToStart)
Write-InformationTracing ("Modify cloud pairing jobs timedout: {0}." -f $JobsTimedOut)
if($JobsTimedOut -gt  0)
{
    $ErrorMessage = "One or more modify cloud pairing jobs has timedout."
    Write-Tracing -Level ErrorLevel -Message ($ErrorMessage)
    Throw-TerminatingErrorMessage -Message $ErrorMessage
}
elseif($JobsCompletedSuccessList.Count -ne $ContainerMappingList.Count)
{
    $ErrorMessage = "One or more modify cloud pairing jobs failed."
    Write-Tracing -Level ErrorLevel -Message ($ErrorMessage)
    Throw-TerminatingErrorMessage -Message $ErrorMessage
}
Write-Tracing -Level Succeeded -Message ("Modify cloud pairing completed.") -DisplayMessageToUser
```

### <a name="manage-updates-manually"></a>Frissítések manuális kezelése

1. Ha a virtuális gépekre telepített mobilitási szolgáltatásnak új frissítései vannak, akkor a következő értesítés jelenik meg: **új site Recovery replikációs ügynök frissítése elérhető. Kattintson ide a telepítéshez.**

   :::image type="content" source="./media/vmware-azure-install-mobility-service/replicated-item-notif.png" alt-text="Replikált elemek ablak":::

1. Válassza ki az értesítést a virtuális gép kiválasztási oldalának megnyitásához.
1. Válassza ki a frissíteni kívánt virtuális gépeket, majd kattintson **az OK gombra**. Minden kiválasztott virtuális gép esetében elindul a mobilitási szolgáltatás frissítése.

   :::image type="content" source="./media/vmware-azure-install-mobility-service/update-okpng.png" alt-text="Replikált elemek virtuálisgép-listája":::

## <a name="common-issues-and-troubleshooting"></a>Gyakori problémák és hibaelhárítás

Ha probléma merül fel az automatikus frissítésekkel kapcsolatban, hibaüzenet jelenik meg a **konfigurációs problémák** alatt a tároló irányítópultján.

Ha nem tudja engedélyezni az automatikus frissítéseket, tekintse meg az alábbi gyakori hibákat és ajánlott műveleteket:

- **Hiba**: nincs engedélye Azure-beli futtató fiók (egyszerű szolgáltatásnév) létrehozására, és adja meg a közreműködői szerepkört az egyszerű szolgáltatásnak.

  **Javasolt művelet**: Ellenőrizze, hogy a bejelentkezett fiók hozzá van-e rendelve közreműködőként, és próbálkozzon újra. Az engedélyek kiosztásával kapcsolatos további információkért tekintse meg a szükséges engedélyek című szakaszt, [útmutató: a portál használatával létrehozhat egy Azure ad-alkalmazást és egy egyszerű szolgáltatásnevet, amely hozzáférhet az erőforrásokhoz](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

  Az automatikus frissítések engedélyezése után a legtöbb probléma kijavításához válassza a **javítás** lehetőséget. Ha a javítás gomb nem érhető el, tekintse meg a bővítmény frissítési beállításai ablaktáblájában megjelenő hibaüzenetet.

  :::image type="content" source="./media/azure-to-azure-autoupdate/repair.png" alt-text="Site Recovery szolgáltatás javítása gomb a bővítmény frissítési beállításainál":::

- **Hiba**: a futtató fiók nem jogosult a Recovery Services-erőforrás elérésére.

  **Javasolt művelet**: törölje, majd [hozza létre újra a futtató fiókot](../automation/manage-runas-account.md). Vagy ellenőrizze, hogy az Automation futtató fiók Azure Active Directory alkalmazása hozzáférhet-e a Recovery Services-erőforráshoz.

- **Hiba**: a futtató fiók nem található. Ezek egyike törölve lett vagy nem lett létrehozva – Azure Active Directory alkalmazás, szolgáltatásnév, szerepkör, Automation-tanúsítvány eszköze, Automation-kapcsolati eszköz –, vagy az ujjlenyomat nem azonos a tanúsítvány és a kapcsolat között.

  **Javasolt művelet**: törölje, majd [hozza létre újra a futtató fiókot](../automation/manage-runas-account.md).

- **Hiba**: az Automation-fiók által használt Azure-beli futtató tanúsítvány hamarosan lejár.

  A futtató fiókhoz létrehozott önaláírt tanúsítvány a létrehozás dátumától számítva egy évig lejár. A tanúsítványt bármikor meg lehet újítani a lejárata előtt. Ha regisztrált az e-mailes értesítésekre, akkor is megkapja az e-maileket, amikor egy műveletre van szükség az Ön oldaláról. Ez a hiba két hónappal a lejárati dátum előtt jelenik meg, és kritikus hibára vált, ha a tanúsítvány lejárt. Ha a tanúsítvány lejárt, az automatikus frissítés addig nem fog működni, amíg meg nem újítja.

  **Javasolt művelet**: a probléma megoldásához válassza a **javítás** , majd a **tanúsítvány megújítása** lehetőséget.

  :::image type="content" source="./media/azure-to-azure-autoupdate/automation-account-renew-runas-certificate.PNG" alt-text="tanúsítvány megújítása":::

  > [!NOTE]
  > A tanúsítvány megújítása után frissítse a lapot az aktuális állapot megjelenítéséhez.
