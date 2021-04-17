---
title: Egyéni szabályzatok üzembe helyezése az Azure Pipelines használatával
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan helyezhet Azure AD B2C egyéni szabályzatokat egy CI-/CD-folyamatban az Azure Pipelines használatával az Azure DevOps Servicesben.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 913f21b90043209cae1ec9963619389bcb452781
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529423"
---
# <a name="deploy-custom-policies-with-azure-pipelines"></a>Egyéni szabályzatok üzembe helyezése az Azure Pipelines használatával

Az [Azure Pipelinesban][devops-pipelines]beállított folyamatos integrációs és kézbesítési (CI/CD) folyamat használatával egyéni szabályzatokat Azure AD B2C szoftverszállítási és kódvezérlési automatizálásba. A különböző Azure AD B2C, például fejlesztési, tesztelési és éles környezetekben történő üzembe helyezés során javasoljuk, hogy távolítsa el a manuális folyamatokat, és végezzen automatizált tesztelést az Azure Pipelines használatával.

Három elsődleges lépés szükséges ahhoz, hogy az Azure Pipelines kezelni tudja az egyéni szabályzatokat a Azure AD B2C:

1. Webalkalmazás-regisztráció létrehozása a Azure AD B2C bérlőben
1. Azure-beli repo konfigurálása
1. Azure-folyamat konfigurálása

> [!IMPORTANT]
> Az Azure AD B2C szabályzatok Azure Pipelinessal  való kezelése jelenleg előzetes verziójú műveleteket használ, amelyek az API-végponton Microsoft Graph `/beta` érhetők el. Ezeknek az API-knak az éles alkalmazásokban való használata nem támogatott. További információért tekintse meg a [bétaverziós Microsoft Graph REST API referenciáját.](/graph/api/overview?toc=.%2fref%2ftoc.json&view=graph-rest-beta&preserve-view=true)

## <a name="prerequisites"></a>Előfeltételek

* [Azure AD B2C bérlőhöz,](tutorial-create-tenant.md)valamint a címtárban A [B2C IEF](../active-directory/roles/permissions-reference.md#b2c-ief-policy-administrator) házirend-rendszergazdai szerepkörű felhasználó hitelesítő adatai
* [A bérlőbe](tutorial-create-user-flows.md?pivots=b2c-custom-policy) feltöltött egyéni szabályzatok
* [A bérlőben](microsoft-graph-get-started.md) a *Policy.ReadWrite.TrustFramework* API Microsoft Graph engedéllyel regisztrált felügyeleti alkalmazás
* [Azure Pipeline](https://azure.microsoft.com/services/devops/pipelines/), és hozzáférés egy [Azure DevOps Services-projekthez][devops-create-project]

## <a name="client-credentials-grant-flow"></a>Ügyfél hitelesítő adatainak megadására vonatkozó folyamat

Az itt leírt forgatókönyv az Azure Pipelines és a Azure AD B2C közötti szolgáltatások közötti hívásokat használja az OAuth 2.0 ügyfél-hitelesítő adatok [megadásának folyamatával.](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md) Ez az engedélyezési folyamat lehetővé teszi, hogy egy webszolgáltatás, például az Azure Pipelines (a bizalmas ügyfél) saját hitelesítő adatait használja ahelyett, hogy megszemélyesít egy felhasználót a hitelesítéshez egy másik webszolgáltatás (ebben az esetben a Microsoft Graph API) hívása során. Az Azure Pipelines nem interaktív módon szerez be egy jogkivonatot, majd kéréseket Microsoft Graph API-nak.

## <a name="register-an-application-for-management-tasks"></a>Alkalmazás regisztrálása felügyeleti feladatokhoz

Az [Előfeltételekben](#prerequisites)említettek szerint olyan alkalmazásregisztrációra van szüksége, amelyet az Azure Pipelines által végrehajtott PowerShell-szkriptek a bérlő erőforrásainak eléréséhez használhatnak.

Ha már rendelkezik automatizálási feladatokhoz használt alkalmazásregisztrációval, győződjön meg arról, hogy az alkalmazásregisztráció **API-engedélyeinek** megfelelő Microsoft Graph  >    >  **Policy.ReadWrite.TrustFramework** engedéllyel rendelkezik. 

A felügyeleti alkalmazások regisztrálásával kapcsolatos utasításokért lásd: [Manage Azure AD B2C with Microsoft Graph](microsoft-graph-get-started.md).

## <a name="configure-an-azure-repo"></a>Azure-beli repo konfigurálása

Ha regisztrált egy felügyeleti alkalmazást, készen áll arra, hogy konfigurálja a házirendfájlok adattárát.

1. Jelentkezzen be az Azure DevOps Services-szervezetbe.
1. [Hozzon létre egy új projektet,][devops-create-project] vagy válasszon ki egy meglévő projektet.
1. A projektben lépjen az **Adattárak lapra, és** válassza a **Fájlok** lapot. Válasszon ki egy meglévő adattárat, vagy hozzon létre egyet ehhez a gyakorlathoz.
1. Hozzon létre egy *B2CAssets nevű mappát.* Nevezze el a szükséges helyőrző *fájlt README.md* **véglegesítéséhez.** Ezt a fájlt később eltávolíthatja, ha szeretné.
1. Adja hozzá Azure AD B2C szabályzatfájlokat a *B2CAssets mappához.* Ebbe beletartozik a *TrustFrameworkBase.xml*, *TrustFrameWorkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml*, *PasswordReset.xml*, és minden más létrehozott szabályzat. Jegyezz fel az egyes Azure AD B2C fájlnevét, hogy egy későbbi lépésben használva használjuk őket (PowerShell-szkript argumentumaiként használhatók).
1. Hozzon létre egy *Scripts* nevű mappát az adattár gyökérkönyvtárában, a helyőrző fájlnak pedig a következő *nevetDeployToB2c.ps1.* A fájlt ne véglegesítéssel, csak egy későbbi lépésben fogja megtenni.
1. Illessze be a következő PowerShell-szkriptet *aDeployToB2c.ps1,* majd **véglegesítheti a** fájlt. A szkript egy jogkivonatot szerez be az Azure AD-ból, és a Microsoft Graph API-t hívja meg, hogy feltöltse a szabályzatokat a *B2CAssets* mappában a Azure AD B2C bérlőjébe.

    ```PowerShell
    [Cmdletbinding()]
    Param(
        [Parameter(Mandatory = $true)][string]$ClientID,
        [Parameter(Mandatory = $true)][string]$ClientSecret,
        [Parameter(Mandatory = $true)][string]$TenantId,
        [Parameter(Mandatory = $true)][string]$PolicyId,
        [Parameter(Mandatory = $true)][string]$PathToFile
    )

    try {
        $body = @{grant_type = "client_credentials"; scope = "https://graph.microsoft.com/.default"; client_id = $ClientID; client_secret = $ClientSecret }

        $response = Invoke-RestMethod -Uri https://login.microsoftonline.com/$TenantId/oauth2/v2.0/token -Method Post -Body $body
        $token = $response.access_token

        $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
        $headers.Add("Content-Type", 'application/xml')
        $headers.Add("Authorization", 'Bearer ' + $token)

        $graphuri = 'https://graph.microsoft.com/beta/trustframework/policies/' + $PolicyId + '/$value'
        $policycontent = Get-Content $PathToFile
        $response = Invoke-RestMethod -Uri $graphuri -Method Put -Body $policycontent -Headers $headers

        Write-Host "Policy" $PolicyId "uploaded successfully."
    }
    catch {
        Write-Host "StatusCode:" $_.Exception.Response.StatusCode.value__

        $_

        $streamReader = [System.IO.StreamReader]::new($_.Exception.Response.GetResponseStream())
        $streamReader.BaseStream.Position = 0
        $streamReader.DiscardBufferedData()
        $errResp = $streamReader.ReadToEnd()
        $streamReader.Close()

        $ErrResp

        exit 1
    }

    exit 0
    ```

## <a name="configure-your-azure-pipeline"></a>Az Azure-folyamat konfigurálása

Most, hogy inicializálta és feltölti az adattárat az egyéni szabályzatfájlokkal, készen áll a kiadási folyamat beállításra.

### <a name="create-pipeline"></a>Folyamat létrehozása

1. Jelentkezzen be az Azure DevOps Services-szervezetbe, és keresse meg a projektet.
1. A projektben válassza a **Pipelines Releases** New pipeline (Folyamatok  >  **kiadása új**  >  **folyamat) lehetőséget.**
1. A **Select a template (Sablon kiválasztása) alatt** válassza az Empty job **(Üres feladat) lehetőséget.**
1. Adjon meg **egy fázisnevet**(például *DeployCustomPolicies)*, majd zárja be a panelt.
1. Válassza az Add an artifact (Összetevő **hozzáadása)** lehetőséget, majd a **Source type (Forrás típusa)** alatt válassza az **Azure Repository (Azure-adattár) lehetőséget.**
    1. Válassza ki azt a forrástárházat, amely a PowerShell-szkriptbe beírt *Scripts* mappát tartalmazza.
    1. Válasszon egy **Alapértelmezett ágat.** Ha az előző szakaszban létrehozott egy új adattárat, az alapértelmezett ág a *master.*
    1. Hagyja meg a Legújabb **alapértelmezett** verzió *beállítást az alapértelmezett ágban.*
    1. Adjon meg **egy Forrás aliast** az adattárhoz. Például: *policyRepo*. Ne foglalhat szóközöket az aliasnévbe.
1. Válassza a **Hozzáadás** lehetőséget
1. Nevezze át a folyamatot, hogy tükrözze a szándékát. Például: *Egyéni házirend-folyamat üzembe helyezése.*
1. A **folyamat konfigurációjának** mentéshez válassza a Mentés lehetőséget.

### <a name="configure-pipeline-variables"></a>Folyamatváltozók konfigurálása

1. Válassza **a Változók** lapot.
1. Adja hozzá a következő változókat a **Pipeline változók alatt,** és állítsa be az értékeiket a megadottak szerint:

    | Name | Érték |
    | ---- | ----- |
    | `clientId` | A korábban regisztrált alkalmazás alkalmazás- **(ügyfél-)** azonosítója. |
    | `clientSecret` | A korábban létrehozott **titkos** ügyfélkitok értéke. <br /> Módosítsa a változó típusát **titkosra** (válassza a zárolás ikont). |
    | `tenantId` | `your-b2c-tenant.onmicrosoft.com`, ahol *a your-b2c-tenant* a bérlő Azure AD B2C neve. |

1. A **változók** mentéshez válassza a Mentés lehetőséget.

### <a name="add-pipeline-tasks"></a>Folyamatfeladatok hozzáadása

Ezután adjon hozzá egy feladatot egy házirendfájl telepítéséhez.

1. Válassza a **Feladatok** lapot.
1. Válassza **az Ügynökfeladat** lehetőséget, majd a pluszjelet ( ) választva adjon hozzá egy tevékenységet **+** az ügynökfeladathoz.
1. Keresse meg és válassza a **PowerShell lehetőséget.** Ne válassza a "Azure PowerShell" vagy a "PowerShell a célgépeken" vagy más PowerShell-bejegyzést.
1. Válassza ki az újonnan hozzáadott **PowerShell-szkriptfeladatot.**
1. Adja meg a következő értékeket a PowerShell-szkript feladathoz:
    * **Feladat verziója:** 2.*
    * **Megjelenített név:** Annak a szabályzatnak a neve, amelybe a feladatnak fel kell töltenie. Például: *B2C_1A_TrustFrameworkBase.*
    * **Típus:** Fájl elérési útja
    * **Szkript elérési útja:** Válassza a három pont (**_..._* _), keresse meg a _Scripts* mappát, majd válassza ki *DeployToB2C.ps1* fájlt.
    * **Érvek:**

        Az Argumentumok mezőben adja **meg a következő értékeket.** Cserélje `{alias-name}` le a helyére az előző szakaszban megadott aliast.

        ```PowerShell
        # Before
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/{alias-name}/B2CAssets/TrustFrameworkBase.xml
        ```

        Ha például a megadott alias a *policyRepo,* az argumentumsornak a következőnek kell lennie:

        ```PowerShell
        # After
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/policyRepo/B2CAssets/TrustFrameworkBase.xml
        ```

1. Az **ügynök feladat** mentéshez válassza a Mentés lehetőséget.

Az előbb hozzáadott feladat feltölt *egy* szabályzatfájlt a Azure AD B2C. A folytatás előtt aktiválja manuálisan a feladatot **(Kiadás** létrehozása) annak érdekében, hogy a feladat sikeresen befejeződjon, mielőtt további tevékenységeket hozna létre.

Ha a feladat sikeresen befejeződik, adja hozzá a telepítési feladatokat az egyes egyéni házirendfájlok előző lépéseit végrehajtva. Módosítsa az `-PolicyId` egyes házirendek és `-PathToFile` argumentumértékeit.

A értéke egy XML-házirendfájl elején található `PolicyId` a TrustFrameworkPolicy csomópontban. A következő szabályzat `PolicyId` XML-fájlja például a B2C_1A_TrustFrameworkBase:

```xml
<TrustFrameworkPolicy
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xmlns:xsd="http://www.w3.org/2001/XMLSchema"
xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
PolicySchemaVersion="0.3.0.0"
TenantId="contoso.onmicrosoft.com"
PolicyId= "B2C_1A_TrustFrameworkBase"
PublicPolicyUri="http://contoso.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
```

Az ügynökök futtatásakor és a szabályzatfájlok feltöltésekor győződjön meg arról, hogy az ügynökök a következő sorrendben vannak feltöltve:

1. *TrustFrameworkBase.xml*
1. *TrustFrameworkExtensions.xml*
1. *SignUpOrSignin.xml*
1. *ProfileEdit.xml*
1. *PasswordReset.xml*

A Identity Experience Framework kényszeríti ezt a sorrendet, mivel a fájlstruktúra egy hierarchikus láncra épül.

## <a name="test-your-pipeline"></a>A folyamat tesztelése

A kiadási folyamat tesztelése:

1. Válassza **a Folyamatok,** majd a **Kiadások lehetőséget.**
1. Válassza ki a korábban létrehozott folyamatot, például *DeployCustomPolicies.*
1. Válassza **a Kiadás létrehozása,** majd a **Létrehozás** lehetőséget a kiadás várólistára való várakozásához.

Megjelenik egy értesítési szalagcím, amely szerint egy kiadás várólistára került. Az állapot megtekintéséhez kattintson az értesítési szalagcímen található hivatkozásra, vagy válassza ki azt a **Releases (Kiadások)** lapon található listában.

## <a name="next-steps"></a>Következő lépések

További információk:

* [Szolgáltatások között ügyfél-hitelesítő adatokat használó hívások](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)
* [Azure DevOps Services](/azure/devops/user-guide/)

<!-- LINKS - External -->
[devops]: /azure/devops/
[devops-create-project]:  /azure/devops/organizations/projects/create-project
[devops-pipelines]: /azure/devops/pipelines
