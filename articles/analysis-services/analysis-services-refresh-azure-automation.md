---
title: Azure Analysis Services modellek frissítése a Azure Automationsal | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan lehet kódokat frissíteni a Azure Analysis Services számára a Azure Automation használatával.
author: chrislound
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: chlound
ms.openlocfilehash: 7c801511b6f24cf5ef04d55bb195e3a4c62d7b6d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96491247"
---
# <a name="refresh-with-azure-automation"></a>Frissítés az Azure Automationnel

A Azure Automation és a PowerShell Runbookok használatával automatizált adatfrissítési műveleteket hajthat végre az Azure Analysis táblázatos modelleken.  

A cikkben szereplő példa a [SQLServer PowerShell-modult](/powershell/module/sqlserver/?view=sqlserver-ps&preserve-view=true)használja. A cikk későbbi részében a modell frissítését bemutató PowerShell-Runbook is elérhető.  

## <a name="authentication"></a>Hitelesítés

Az összes hívást érvényes Azure Active Directory (OAuth 2) jogkivonattal kell hitelesíteni.  A cikkben szereplő példa egy egyszerű szolgáltatásnevet (SPN) használ a Azure Analysis Services való hitelesítéshez. További információ: [egyszerű szolgáltatásnév létrehozása Azure Portal használatával](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="prerequisites"></a>Előfeltételek

> [!IMPORTANT]
> Az alábbi példa feltételezi, hogy a Azure Analysis Services tűzfal le van tiltva. Ha engedélyezve van a tűzfal, a kérelem kezdeményezője nyilvános IP-címét is tartalmaznia kell egy tűzfalszabály számára.

### <a name="install-sqlserver-modules-from-powershell-gallery"></a>Telepítse a SqlServer-modulokat a PowerShell-galériából.

1. A Azure Automation-fiókban kattintson a **modulok** elemre, majd a **Tallózás** katalógus lehetőségre.

2. A keresési sávban keresse meg a **SQLServer**.

    ![Keresési modulok](./media/analysis-services-refresh-azure-automation/1.png)

3. Válassza a SqlServer lehetőséget, majd kattintson az **Importálás** elemre.
 
    ![Modul importálása](./media/analysis-services-refresh-azure-automation/2.png)

4. Kattintson az **OK** gombra.
 
### <a name="create-a-service-principal-spn"></a>Egyszerű szolgáltatásnév (SPN) létrehozása

Az egyszerű szolgáltatásnév létrehozásával kapcsolatos további tudnivalókért lásd: [egyszerű szolgáltatásnév létrehozása Azure Portal használatával](../active-directory/develop/howto-create-service-principal-portal.md).

### <a name="configure-permissions-in-azure-analysis-services"></a>Engedélyek konfigurálása Azure Analysis Services
 
Az Ön által létrehozott egyszerű szolgáltatásnak kiszolgálói rendszergazdai engedélyekkel kell rendelkeznie a kiszolgálón. További információ: [egyszerű szolgáltatásnév hozzáadása a kiszolgálói rendszergazdai szerepkörhöz](analysis-services-addservprinc-admins.md).

## <a name="design-the-azure-automation-runbook"></a>A Azure Automation Runbook megtervezése

1. Az Automation-fiókban hozzon létre egy **hitelesítő adatokat** tartalmazó erőforrást, amelyet az egyszerű szolgáltatás biztonságos tárolására fog használni.

    ![Képernyőfelvétel: a "hitelesítő adatok hozzáadása" lapon a "hitelesítő adatok" művelet van kiválasztva.](./media/analysis-services-refresh-azure-automation/6.png)

2. Adja meg a hitelesítő adatok részleteit. A **Felhasználónév** mezőben adja meg az egyszerű szolgáltatásnév (AppID) nevet, majd a **jelszó** mezőben adja meg az egyszerű szolgáltatás titkos kulcsát.

    ![Hitelesítő adat létrehozása](./media/analysis-services-refresh-azure-automation/7.png)

3. Importálja az Automation-Runbook.

    ![Képernyőfelvétel: a "Runbookok" (runbook importálása) műveletet megjelenítő lap.](./media/analysis-services-refresh-azure-automation/8.png)

4. Tallózással keresse meg a [Refresh-Model.ps1](#sample-powershell-runbook) fájlt, adjon meg egy **nevet** és egy **leírást**, majd kattintson a **Létrehozás** gombra.

    > [!NOTE]
    > A dokumentum alján található, [minta PowerShell-Runbook](#sample-powershell-runbook) tartozó parancsfájl használatával hozzon létre egy Refresh-Model.ps1 nevű fájlt, és mentse a helyi gépre a Runbook-be való importáláshoz.

    ![Runbook importálása](./media/analysis-services-refresh-azure-automation/9.png)

5. A Runbook létrehozásakor a rendszer automatikusan szerkesztési módba lép.  Válassza a **Közzététel** lehetőséget.

    ![Runbook közzététele](./media/analysis-services-refresh-azure-automation/10.png)

    > [!NOTE]
    > A korábban létrehozott hitelesítőadat-erőforrást a runbook a **Get-AutomationPSCredential** parancs használatával kéri le.  Ezt a parancsot a rendszer átadja a **meghívó-ProcessASADatabase PowerShell-** parancsnak a Azure Analysis Services hitelesítésének végrehajtásához.

6. A **Start** gombra kattintva tesztelje a runbook.

    ![Képernyőkép: az "áttekintés" oldal, amelyen a "Start" művelet be van jelölve.](./media/analysis-services-refresh-azure-automation/11.png)

7. Töltse ki a **databasename**, a **ANALYSISSERVER** és a **REFRESHTYPE** paramétert, majd kattintson **az OK** gombra. A **WEBHOOKDATA** paraméter nem szükséges, ha a Runbook futtatása manuálisan történik.

    ![A Runbook elindítása](./media/analysis-services-refresh-azure-automation/12.png)

Ha a Runbook sikeresen végrehajtva, a következőhöz hasonló kimenetet fog kapni:

![Sikeres Futtatás](./media/analysis-services-refresh-azure-automation/13.png)

## <a name="use-a-self-contained-azure-automation-runbook"></a>Önálló Azure Automation Runbook használata

A Runbook beállítható úgy, hogy ütemezett alapon aktiválja a Azure Analysis Services modell frissítését.

Ezt a következőképpen lehet konfigurálni:

1. Az Automation Runbook kattintson az **ütemtervek** lehetőségre, majd **adjon hozzá egy ütemtervet**.
 
    ![Ütemterv létrehozása](./media/analysis-services-refresh-azure-automation/14.png)

2. Kattintson az **ütemezett**  >  **új ütemterv létrehozása** lehetőségre, majd adja meg a részleteket.

    ![Ütemterv konfigurálása](./media/analysis-services-refresh-azure-automation/15.png)

3. Kattintson a **Létrehozás** lehetőségre.

4. Adja meg az ütemterv paramétereit. Ezeket a rendszer minden alkalommal felhasználja, amikor a Runbook elindítják. Az **WEBHOOKDATA** paramétert üresen kell hagyni, ha egy ütemterven keresztül fut.

    ![Paraméterek konfigurálása](./media/analysis-services-refresh-azure-automation/16.png)

5. Kattintson az **OK** gombra.

## <a name="consume-with-data-factory"></a>Használat Data Factory

Ha a runbook Azure Data Factory használatával szeretné felhasználni, először hozzon létre egy **webhookot** a runbook. A **webhook** egy URL-címet ad meg, amely Azure Data Factory webes tevékenységgel hívható meg.

> [!IMPORTANT]
> **Webhook** létrehozásához **közzé** kell tenni a Runbook állapotát.

1. Az Automation-Runbook kattintson a **webhookok** elemre, majd kattintson a **webhook hozzáadása** elemre.

   ![Webhook hozzáadása](./media/analysis-services-refresh-azure-automation/17.png)

2. Adja meg a webhook nevét és lejáratát.  A név csak az Automation Runbook található webhookot azonosítja, az URL-cím részét képezi.

   >[!CAUTION]
   >Győződjön meg róla, hogy a varázsló bezárása előtt másolja az URL-címet, mert a Bezárás után nem tud visszakapni.
    
   ![Webhook konfigurálása](./media/analysis-services-refresh-azure-automation/18.png)

    A webhook paraméterei üresen maradhatnak.  Az Azure Data Factory webes tevékenység konfigurálásakor a paraméterek a webes hívás törzsében adhatók át.

3. Data Factory a **webes tevékenység** konfigurálása

### <a name="example"></a>Példa

   ![Példa webes tevékenységre](./media/analysis-services-refresh-azure-automation/19.png)

Az **URL-cím** a webhookból létrehozott URL-cím.

A **törzs** egy JSON-dokumentum, amely a következő tulajdonságokat tartalmazza:


|Tulajdonság  |Érték  |
|---------|---------|
|**AnalysisServicesDatabase**     |Az Azure Analysis Services adatbázis neve <br/> Példa: AdventureWorksDB         |
|**AnalysisServicesServer**     |A Azure Analysis Services-kiszolgáló neve. <br/> Például: https: \/ /westus.asazure.Windows.net/Servers/MyServer/models/AdventureWorks/         |
|**DatabaseRefreshType**     |A végrehajtandó frissítés típusa. <br/> Példa: Full         |

Példa JSON-törzsre:

```json
{
    "AnalysisServicesDatabaseName": "AdventureWorksDB",
    "AnalysisServicesServer": "asazure://westeurope.asazure.windows.net/MyAnalysisServer",
    "DatabaseRefreshType": "Full"
}
```

Ezek a paraméterek a runbook PowerShell-szkriptben vannak meghatározva.  A webes tevékenység végrehajtásakor az átadott JSON-adattartalom WEBHOOKDATA.

Ez deszerializált, és PowerShell-paraméterekként tárolódik, amelyeket a Invoke-ProcesASDatabase PowerShell-parancs használ.

![Deszerializált webhook](./media/analysis-services-refresh-azure-automation/20.png)

## <a name="use-a-hybrid-worker-with-azure-analysis-services"></a>Hibrid feldolgozók használata Azure Analysis Services

Egy statikus nyilvános IP-címmel rendelkező Azure-beli virtuális gép Azure Automation hibrid feldolgozóként is használható.  Ezt a nyilvános IP-címet ezután fel lehet venni a Azure Analysis Services tűzfalba.

> [!IMPORTANT]
> Győződjön meg arról, hogy a virtuális gép nyilvános IP-címe statikusként van konfigurálva.
>
>A hibrid feldolgozók Azure Automation konfigurálásával kapcsolatos további információkért lásd: [hibrid Runbook Worker telepítése](../automation/automation-hybrid-runbook-worker.md#hybrid-runbook-worker-installation).

A hibrid feldolgozók konfigurálása után hozzon létre egy webhookot a következő szakaszban leírtak szerint: [Data Factory használata](#consume-with-data-factory).  Az egyetlen különbség, hogy a   >  webhook konfigurálásakor kiválasztja a **hibrid** feldolgozó futtatása lehetőséget.

Példa a hibrid feldolgozót használó webhookra:

![Példa hibrid feldolgozói webhookra](./media/analysis-services-refresh-azure-automation/21.png)

## <a name="sample-powershell-runbook"></a>PowerShell-Runbook minta

A következő kódrészlet egy példa arra, hogyan végezheti el a Azure Analysis Services modell frissítését PowerShell-Runbook használatával.

```powershell
param
(
    [Parameter (Mandatory = $false)]
    [object] $WebhookData,

    [Parameter (Mandatory = $false)]
    [String] $DatabaseName,
    [Parameter (Mandatory = $false)]
    [String] $AnalysisServer,
    [Parameter (Mandatory = $false)]
    [String] $RefreshType
)

$_Credential = Get-AutomationPSCredential -Name "ServicePrincipal"

# If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData)
{ 
    # Retrieve AAS details from Webhook request body
    $atmParameters = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)
    Write-Output "CredentialName: $($atmParameters.CredentialName)"
    Write-Output "AnalysisServicesDatabaseName: $($atmParameters.AnalysisServicesDatabaseName)"
    Write-Output "AnalysisServicesServer: $($atmParameters.AnalysisServicesServer)"
    Write-Output "DatabaseRefreshType: $($atmParameters.DatabaseRefreshType)"
    
    $_databaseName = $atmParameters.AnalysisServicesDatabaseName
    $_analysisServer = $atmParameters.AnalysisServicesServer
    $_refreshType = $atmParameters.DatabaseRefreshType
 
    Invoke-ProcessASDatabase -DatabaseName $_databaseName -RefreshType $_refreshType -Server $_analysisServer -ServicePrincipal -Credential $_credential
}
else 
{
    Invoke-ProcessASDatabase -DatabaseName $DatabaseName -RefreshType $RefreshType -Server $AnalysisServer -ServicePrincipal -Credential $_Credential
}
```


## <a name="next-steps"></a>Következő lépések

[Példák](analysis-services-samples.md)  
[REST API](/rest/api/analysisservices/servers)