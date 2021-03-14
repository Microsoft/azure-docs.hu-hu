---
title: A Application Insights kibocsátási megjegyzései | Microsoft Docs
description: Adja hozzá az üzembe helyezést, vagy készítsen jelölőket a metrikák Explorer diagramjaihoz Application Insights.
ms.topic: conceptual
ms.date: 08/14/2020
ms.openlocfilehash: 776efd56aaa523d1c2621c51cba0446a42bb7411
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2021
ms.locfileid: "103461912"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Jegyzetek a metrikus diagramokon Application Insights

A jegyzetek megmutatják, hol telepített egy új buildet vagy más jelentős eseményt. A jegyzetek segítségével egyszerűen megtekintheti, hogy a módosítások hatással voltak-e az alkalmazás teljesítményére. Ezek automatikusan létrehozhatók az Azure- [folyamatok](/azure/devops/pipelines/tasks/) Build rendszerével. Megjegyzéseket is létrehozhat, hogy minden olyan eseményt megjelölni, amelyet a PowerShellből hoz létre.

## <a name="release-annotations-with-azure-pipelines-build"></a>Kiadási jegyzetek az Azure-folyamatok létrehozásával

A kibocsátási megjegyzések az Azure DevOps felhőalapú Azure-folyamatok szolgáltatásának egyik funkciója.

### <a name="install-the-annotations-extension-one-time"></a>A jegyzetek kiterjesztésének (egyszeri) telepítése

A kiadási jegyzetek létrehozásához telepítenie kell a Visual Studio Marketplace-en elérhető számos Azure DevOps-bővítmény egyikét.

1. Jelentkezzen be az [Azure DevOps](https://azure.microsoft.com/services/devops/) -projektbe.
   
1. A Visual Studio Marketplace [kiadási jegyzetek bővítmény](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations) lapján válassza ki az Azure DevOps-szervezetet, majd válassza a **telepítés** lehetőséget a bővítmény Azure DevOps-szervezethez való hozzáadásához.
   
   ![Válasszon ki egy Azure DevOps-szervezetet, majd válassza a telepítés lehetőséget.](./media/annotations/1-install.png)
   
Csak egyszer kell telepítenie a bővítményt az Azure DevOps-szervezet számára. Mostantól a szervezet bármely projektje számára is konfigurálhatja a kibocsátási megjegyzéseket.

### <a name="configure-release-annotations"></a>Kiadási jegyzetek konfigurálása

Hozzon létre külön API-kulcsot az Azure-folyamatok egyes kiadási sablonjaihoz.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és nyissa meg az alkalmazást figyelő Application Insights-erőforrást. Ha még nem rendelkezik ilyennel, [hozzon létre egy új Application Insights erőforrást](./app-insights-overview.md).
   
1. Nyissa meg az **API-hozzáférés** lapot, és másolja a **Application Insights azonosítót**.
   
   ![Az API-hozzáférés területen másolja az alkalmazás AZONOSÍTÓját.](./media/annotations/2-app-id.png)

1. Egy külön böngészőablakban nyissa meg vagy hozza létre az Azure-folyamatok üzembe helyezéseit kezelő kiadási sablont.
   
1. Válassza a **feladat hozzáadása** lehetőséget, majd a menüből válassza a **Application Insights kibocsátási megjegyzése** feladatot.
   
   ![Válassza a feladat hozzáadása lehetőséget, és válassza ki Application Insights kiadási jegyzetet.](./media/annotations/3-add-task.png)

   > [!NOTE]
   > A kiadási jegyzet feladata jelenleg csak a Windows-alapú ügynököket támogatja; nem futtatható Linux, macOS vagy más típusú ügynökön.
   
1. Az **alkalmazás azonosítója** alatt illessze be az **API-hozzáférés** LAPRÓL másolt Application Insights azonosítót.
   
   ![Application Insights-azonosító beillesztése](./media/annotations/4-paste-app-id.png)
   
1. Vissza a Application Insights **API-hozzáférés** ablakban válassza az **API-kulcs létrehozása** lehetőséget. 
   
   ![Az API-hozzáférés lapon válassza az API-kulcs létrehozása lehetőséget.](./media/annotations/5-create-api-key.png)
   
1. Az **API-kulcs létrehozása** ablakban írja be a leírást, válassza a **Megjegyzések írása** lehetőséget, majd válassza a **kulcs létrehozása** lehetőséget. Másolja az új kulcsot.
   
   ![Az API-kulcs létrehozása ablakban írja be a leírást, válassza a megjegyzések írása lehetőséget, majd válassza a kulcs létrehozása lehetőséget.](./media/annotations/6-create-api-key.png)
   
1. A kiadási sablon ablak **változók** lapján válassza a **Hozzáadás** lehetőséget az új API-kulcs változó definíciójának létrehozásához.

1. A **név** mezőben adja meg `ApiKey` az értéket, majd az **érték** alatt ILLESSZE be a másolt API-kulcsot az **API-hozzáférés** lapról.
   
   ![Az Azure DevOps-változók lapon válassza a Hozzáadás elemet, nevezze el a ApiKey változót, majd illessze be az API-kulcsot az érték mezőbe.](./media/annotations/7-paste-api-key.png)
   
1. A sablon mentéséhez válassza a fő kiadási sablon ablak **Mentés** elemét.


   > [!NOTE]
   > Az API-kulcsok korlátozásait a [REST API Rate Limits dokumentációja](https://dev.applicationinsights.io/documentation/Authorization/Rate-limits)tartalmazza.

## <a name="view-annotations"></a>Jegyzetek megtekintése


   > [!NOTE]
   > A kibocsátási megjegyzések jelenleg nem érhetők el a Application Insights metrika ablaktábláján.

Most, amikor a kiadás sablont használja egy új kiadás üzembe helyezéséhez, a rendszer elküld egy jegyzetet Application Insights. A jegyzetek a következő helyszíneken tekinthetők meg:

A **használat** ablaktáblán lehetősége van a kiadási jegyzetek manuális létrehozására is:

![Képernyőkép – a sávdiagramok száma egy adott időszakon belül megjelenített felhasználói látogatások számával. A kiadási jegyzetek zöld pipaként jelennek meg a diagram felett, amely azt jelzi, hogy a kiadás milyen időpontban történt](./media/annotations/usage-pane.png)

Minden olyan log-alapú munkafüzet-lekérdezésben, amelyben a vizualizáció megjeleníti az időt az x tengely mentén.

![Képernyőfelvétel a munkafüzetek panelről idősorozatos naplózási lekérdezéssel megjelenített megjegyzésekkel](./media/annotations/workbooks-annotations.png)

Ha engedélyezni szeretné a jegyzeteket a munkafüzetben, ugorjon a **Speciális beállítások** elemre, és válassza a **jegyzetek megjelenítése** lehetőséget.

![Képernyőkép a speciális beállítások menüjéről, a következő szavakkal: a beállítás melletti jelölőnégyzetekkel Kiemelt jegyzeteket jelenít meg az engedélyezéshez.](./media/annotations/workbook-show-annotations.png)

A kiadás részleteinek megnyitásához válassza a Megjegyzés jelölőjét, beleértve a kérelmezőt, a verziókövetés ágat, a kiadási folyamatot és a környezetet.

## <a name="create-custom-annotations-from-powershell"></a>Egyéni jegyzetek létrehozása a PowerShellből
A GitHubról származó CreateReleaseAnnotation PowerShell-szkripttel megjegyzéseket hozhat létre bármely olyan folyamatból, amely az Azure DevOps használata nélkül használható.

1. A CreateReleaseAnnotation.ps1 helyi másolatának készítése:

    ```powershell
    
    # Copyright (c) Microsoft Corporation. All rights reserved. 
    # Licensed under the MIT License. See License.txt in the project root for license information. 
    
    # Sample usage .\CreateReleaseAnnotation.ps1 -applicationId "<appId>" -apiKey "<apiKey>" -releaseFilePath "<path to .exe with file version>" -releaseProperties @{"ReleaseDescription"="Release with annotation";"TriggerBy"="John Doe"}
    param(
        [parameter(Mandatory = $true)][string]$applicationId,
        [parameter(Mandatory = $true)][string]$apiKey,
        [parameter(Mandatory = $true)][string]$releaseFilePath,
        [parameter(Mandatory = $false)]$releaseProperties
    )
    
    $releaseName = (Get-Item $releaseFilePath).VersionInfo.FileVersion
    Write-Host "Creating release annotation $releaseName in ApplicationInsights" -ForegroundColor Cyan
    
    # background info on how fwlink works: After you submit a web request, many sites redirect through a series of intermediate pages before you finally land on the destination page.
    # So when calling Invoke-WebRequest, the result it returns comes from the final page in any redirect sequence. Hence, I set MaximumRedirection to 0, as this prevents the call to 
    # be redirected. By doing this, we get a response with status code 302, which indicates that there is a redirection link from the response body. We grab this redirection link and 
    # construct the url to make a release annotation.
    # Here's how this logic is going to works
    # 1. Client send http request, such as:  http://go.microsoft.com/fwlink/?LinkId=625115
    # 2. FWLink get the request and find out the destination URL for it, such as:  http://www.bing.com
    # 3. FWLink generate a new http response with status code “302” and with destination URL “http://www.bing.com”. Send it back to Client.
    # 4. Client, such as a powershell script, knows that status code “302” means redirection to new a location, and the target location is “http://www.bing.com”
    function GetRequestUrlFromFwLink($fwLink)
    {
        $request = Invoke-WebRequest -Uri $fwLink -MaximumRedirection 0 -UseBasicParsing -ErrorAction Ignore
        if ($request.StatusCode -eq "302") {
            return $request.Headers.Location
        }
        
        return $null
    }
    
    function CreateAnnotation($grpEnv)
    {
        $retries = 1
        $success = $false
        while (!$success -and $retries -lt 6) {
            $location = "$grpEnv/applications/$applicationId/Annotations?api-version=2015-11"
                
            Write-Host "Invoke a web request for $location to create a new release annotation. Attempting $retries"
            set-variable -Name createResultStatus -Force -Scope Local -Value $null
            set-variable -Name createResultStatusDescription -Force -Scope Local -Value $null
            set-variable -Name result -Force -Scope Local
    
            try {
                $result = Invoke-WebRequest -Uri $location -Method Put -Body $bodyJson -Headers $headers -ContentType "application/json; charset=utf-8" -UseBasicParsing
            } catch {
                if ($_.Exception){
                    if($_.Exception.Response) {
                        $createResultStatus = $_.Exception.Response.StatusCode.value__
                        $createResultStatusDescription = $_.Exception.Response.StatusDescription
                    }
                    else {
                        $createResultStatus = "Exception"
                        $createResultStatusDescription = $_.Exception.Message
                    }
                }
            }
    
            if ($result -eq $null) {
                if ($createResultStatus -eq $null) {
                    $createResultStatus = "Unknown"
                }
                if ($createResultStatusDescription -eq $null) {
                    $createResultStatusDescription = "Unknown"
                }
            }
            else {
                    $success = $true                     
            }
    
            if ($createResultStatus -eq 409 -or $createResultStatus -eq 404 -or $createResultStatus -eq 401) # no retry when conflict or unauthorized or not found
            {
                break
            }
    
            $retries = $retries + 1
            sleep 1
        }
    
        $createResultStatus
        $createResultStatusDescription
        return
    }
    
    # Need powershell version 3 or greater for script to run
    $minimumPowershellMajorVersion = 3
    if ($PSVersionTable.PSVersion.Major -lt $minimumPowershellMajorVersion) {
       Write-Host "Need powershell version $minimumPowershellMajorVersion or greater to create release annotation"
       return
    }
    
    $currentTime = (Get-Date).ToUniversalTime()
    $annotationDate = $currentTime.ToString("MMddyyyy_HHmmss")
    set-variable -Name requestBody -Force -Scope Script
    $requestBody = @{}
    $requestBody.Id = [GUID]::NewGuid()
    $requestBody.AnnotationName = $releaseName
    $requestBody.EventTime = $currentTime.GetDateTimeFormats("s")[0] # GetDateTimeFormats returns an array
    $requestBody.Category = "Deployment"
    
    if ($releaseProperties -eq $null) {
        $properties = @{}
    } else {
        $properties = $releaseProperties    
    }
    $properties.Add("ReleaseName", $releaseName)
    
    $requestBody.Properties = ConvertTo-Json($properties) -Compress
    
    $bodyJson = [System.Text.Encoding]::UTF8.GetBytes(($requestBody | ConvertTo-Json))
    $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
    $headers.Add("X-AIAPIKEY", $apiKey)
    
    set-variable -Name createAnnotationResult1 -Force -Scope Local -Value $null
    set-variable -Name createAnnotationResultDescription -Force -Scope Local -Value ""
    
    # get redirect link from fwlink
    $requestUrl = GetRequestUrlFromFwLink("http://go.microsoft.com/fwlink/?prd=11901&pver=1.0&sbp=Application%20Insights&plcid=0x409&clcid=0x409&ar=Annotations&sar=Create%20Annotation")
    if ($requestUrl -eq $null) {
        $output = "Failed to find the redirect link to create a release annotation"
        throw $output
    }
    
    $createAnnotationResult1, $createAnnotationResultDescription = CreateAnnotation($requestUrl)
    if ($createAnnotationResult1) 
    {
         $output = "Failed to create an annotation with Id: {0}. Error {1}, Description: {2}." -f $requestBody.Id, $createAnnotationResult1, $createAnnotationResultDescription
         throw $output
    }
    
    $str = "Release annotation created. Id: {0}." -f $requestBody.Id
    Write-Host $str -ForegroundColor Green
    
    ```
   
1. Az előző eljárás lépéseit követve szerezze be a Application Insights azonosítót, és hozzon létre egy API-kulcsot a Application Insights **API-hozzáférés** lapon.
   
1. Hívja meg a PowerShell-parancsfájlt a következő kóddal, és cserélje le a szögletes zárójeles helyőrzőket az értékekre. A `-releaseProperties` nem kötelező. 
   
   ```powershell
   
        .\CreateReleaseAnnotation.ps1 `
         -applicationId "<applicationId>" `
         -apiKey "<apiKey>" `
         -releaseName "<releaseName>" `
         -releaseProperties @{
             "ReleaseDescription"="<a description>";
             "TriggerBy"="<Your name>" }
   ```

Módosíthatja a parancsfájlt, például a múltbeli jegyzetek létrehozásához.

## <a name="next-steps"></a>Következő lépések

* [Munkaelemek létrehozása](./diagnostic-search.md#create-work-item)
* [Automatizálás a PowerShell használatával](./powershell.md)

