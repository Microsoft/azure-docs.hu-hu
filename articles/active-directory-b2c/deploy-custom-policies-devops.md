---
title: Egyéni szabályzatok üzembe helyezése az Azure-folyamatokkal
titleSuffix: Azure AD B2C
description: Ismerje meg, hogyan helyezhet üzembe Azure AD B2C egyéni házirendeket CI/CD-folyamatokban az Azure DevOps Services Azure-folyamatainak használatával.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3ca4464f40c0807e51adffca8e3e68c622314de6
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107256888"
---
# <a name="deploy-custom-policies-with-azure-pipelines"></a>Egyéni szabályzatok üzembe helyezése az Azure-folyamatokkal

Az [Azure-folyamatokban][devops-pipelines]beállított folyamatos integrációs és kézbesítési (CI/CD) folyamat használatával a szoftveres kézbesítés és a kód-vezérlés automatizálása során a Azure ad B2C egyéni szabályzatokat is megadhatja. A különböző Azure AD B2C környezetekben történő üzembe helyezéskor, például a fejlesztési, tesztelési és éles üzemi környezetben javasoljuk, hogy távolítsa el a manuális folyamatokat, és végezzen automatikus tesztelést az Azure-folyamatok használatával.

Három fő lépés szükséges ahhoz, hogy lehetővé tegye az Azure-folyamatok számára az egyéni házirendek felügyeletét Azure AD B2Con belül:

1. Webalkalmazás-regisztráció létrehozása a Azure AD B2C-bérlőben
1. Azure-tárház konfigurálása
1. Azure-folyamat konfigurálása

> [!IMPORTANT]
> Azure AD B2C egyéni szabályzatok Azure-folyamattal való kezelése jelenleg az Microsoft Graph API-végponton elérhető **előzetes** műveleteket használja `/beta` . Az API-k üzemi alkalmazásokban való használata nem támogatott. További információ: [Microsoft Graph REST API Beta-végpont referenciája](/graph/api/overview?toc=.%2fref%2ftoc.json&view=graph-rest-beta).

## <a name="prerequisites"></a>Előfeltételek

* [Azure ad B2C bérlő](tutorial-create-tenant.md)és a címtárbeli felhasználó hitelesítő adatai a [B2C IEF házirend-rendszergazdai](../active-directory/roles/permissions-reference.md#b2c-ief-policy-administrator) szerepkörrel
* A bérlőre feltöltött [Egyéni szabályzatok](tutorial-create-user-flows.md?pivots=b2c-custom-policy)
* A bérlőben regisztrált [felügyeleti alkalmazás](microsoft-graph-get-started.md) a Microsoft Graph API Permission *Policy. ReadWrite. TrustFramework*
* [Azure-folyamat](https://azure.microsoft.com/services/devops/pipelines/)és hozzáférés egy [Azure DevOps Services-projekthez][devops-create-project]

## <a name="client-credentials-grant-flow"></a>Ügyfél-hitelesítő adatok megadása folyamat

Az itt leírt forgatókönyv az Azure-folyamatok és a Azure AD B2C közötti, a OAuth 2,0 [ügyfél-hitelesítő adatok engedélyezési folyamatát](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)használó szolgáltatások közötti hívások használatát teszi lehetővé. Ez a engedélyezési folyamat lehetővé teszi a webszolgáltatások, például az Azure-folyamatok (a bizalmas ügyfél) számára, hogy a saját hitelesítő adatait használják, ahelyett, hogy a felhasználó megszemélyesítését egy másik webszolgáltatás (ebben az esetben a Microsoft Graph API) hívásakor hitelesítse. Az Azure-folyamatok nem interaktív módon szereznek be tokent, majd kérelmeket tesznek a Microsoft Graph API-nak.

## <a name="register-an-application-for-management-tasks"></a>Alkalmazás regisztrálása felügyeleti feladatokhoz

Az [Előfeltételek](#prerequisites)között említettek szerint az alkalmazás regisztrálása szükséges az Azure-folyamatok által végrehajtott PowerShell-szkriptek számára – a segítségével hozzáférhet a bérlő erőforrásaihoz.

Ha már rendelkezik az automatizálási feladatokhoz használt alkalmazás-regisztrációval, győződjön meg róla, hogy a **Microsoft Graph**  >  **Policy**  >  **Policy. ReadWrite. TrustFramework** engedélyt adta meg az alkalmazás regisztrációjának **API-engedélyein** belül.

A felügyeleti alkalmazások regisztrálásával kapcsolatos utasításokért lásd: [Azure ad B2C kezelése Microsoft Graphsal](microsoft-graph-get-started.md).

## <a name="configure-an-azure-repo"></a>Azure-tárház konfigurálása

Ha a felügyeleti alkalmazás regisztrálva van, készen áll a házirend-fájlok tárházának konfigurálására.

1. Jelentkezzen be az Azure DevOps Services-szervezetbe.
1. [Hozzon létre egy új projektet][devops-create-project] , vagy válasszon ki egy meglévő projektet.
1. A projektben navigáljon a **repók** lapra, és válassza a **fájlok** lapot. Válasszon ki egy meglévő tárházat, vagy hozzon létre egyet ehhez a gyakorlathoz.
1. Hozzon létre egy *B2CAssets* nevű mappát. Nevezze el a szükséges helyőrző fájlt *readme.MD* , és **véglegesítse** a fájlt. Ezt a fájlt később is eltávolíthatja, ha szeretné.
1. Adja hozzá Azure AD B2C házirend-fájljait a *B2CAssets* mappához. Ez magában foglalja a *TrustFrameworkBase.xml*, *TrustFrameWorkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml*, *PasswordReset.xml* és minden más létrehozott házirendet. Jegyezze fel az egyes Azure AD B2C házirend-fájlok fájlnevét egy későbbi lépésben való használatra (PowerShell-parancsfájl argumentumaként használják).
1. Hozzon létre egy *Scripts* nevű mappát az adattár gyökérkönyvtárában, nevezze el a helyőrző fájlt *DeployToB2c.ps1*. Ne véglegesítse a fájlt ezen a ponton, egy későbbi lépésben.
1. Illessze be a következő PowerShell-szkriptet *DeployToB2c.ps1ba*, majd **véglegesítse** a fájlt. A parancsfájl jogkivonatot kér az Azure AD-től, és meghívja a Microsoft Graph API-t, hogy feltöltse a szabályzatokat a *B2CAssets* mappában a Azure ad B2C bérlőre.

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

A tárház inicializálása és az egyéni házirend-fájlok feltöltése után készen áll a kiadási folyamat beállítására.

### <a name="create-pipeline"></a>Folyamat létrehozása

1. Jelentkezzen be az Azure DevOps Services-szervezetbe, és navigáljon a projekthez.
1. A projektben válassza a **folyamatok**  >    >  **új folyamat** kiadása lehetőséget.
1. **A sablon kiválasztása** területen válassza az **üres feladatot**.
1. Adja meg a **fázis nevét**, például *DeployCustomPolicies*, majd a panel bezárásához.
1. Válassza **az összetevő hozzáadása** lehetőséget, majd a **forrás típusa** területen válassza az **Azure adattár** lehetőséget.
    1. Válassza ki a PowerShell-parancsfájllal feltöltött *parancsfájlok* mappát tartalmazó forrás tárházat.
    1. Válasszon ki egy **alapértelmezett ágat**. Ha az előző szakaszban létrehozott egy új tárházat, az alapértelmezett ág a *Master*.
    1. Hagyja meg a legutóbbi **alapértelmezett verzió** beállítását *az alapértelmezett ág alapján*.
    1. Adja meg a tárház **forrás-aliasát** . Például: *policyRepo*. Ne tartalmazzon szóközt az alias nevében.
1. Válassza a **Hozzáadás** lehetőséget
1. Nevezze át a folyamatot, hogy tükrözze a szándékát. Például az *egyéni házirend-folyamat telepítése*.
1. A folyamat konfigurációjának mentéséhez válassza a **Mentés** lehetőséget.

### <a name="configure-pipeline-variables"></a>Folyamat változóinak konfigurálása

1. Válassza a **változók** fület.
1. Adja hozzá a következő változókat a **folyamat változói** alatt, és állítsa be az értékeket a megadott módon:

    | Name | Érték |
    | ---- | ----- |
    | `clientId` | A korábban regisztrált alkalmazás **alkalmazás-(ügyfél-) azonosítója** . |
    | `clientSecret` | A korábban létrehozott **ügyfél-titok** értéke. <br /> Módosítsa a változó típusát **titkosra** (válassza a zárolás ikont). |
    | `tenantId` | `your-b2c-tenant.onmicrosoft.com`, ahol *a-B2C-bérlő* a Azure ad B2C bérlő neve. |

1. A változók mentéséhez válassza a **Mentés** lehetőséget.

### <a name="add-pipeline-tasks"></a>Folyamattal kapcsolatos feladatok hozzáadása

Ezután adjon hozzá egy feladatot a házirendfájl telepítéséhez.

1. Válassza a **feladatok** fület.
1. Válassza az **ügynök feladata** lehetőséget, majd válassza a pluszjelet ( **+** ) egy feladat hozzáadásához az ügynök feladatához.
1. Keresse meg és válassza ki a **PowerShellt**. Ne válassza a "Azure PowerShell," "PowerShell a célszámítógépen" vagy egy másik PowerShell-bejegyzést.
1. Válassza ki az újonnan hozzáadott **PowerShell-parancsfájl** feladatot.
1. Adja meg a következő értékeket a PowerShell-parancsfájl feladatához:
    * **Feladat verziója**: 2. *
    * **Megjelenítendő név**: annak a szabályzatnak a neve, amelyet a feladatnak fel kell töltenie. Például *B2C_1A_TrustFrameworkBase*.
    * **Típus**: fájl elérési útja
    * **Parancsfájl elérési útja**: válassza a három pontot (**_..._* _), keresse meg a _Scripts * mappát, majd válassza ki a *DeployToB2C.ps1* fájlt.
    * **Argumentumok**

        Adja meg a következő értékeket az **argumentumokhoz**. Cserélje le az `{alias-name}` értéket az előző szakaszban megadott aliasra.

        ```PowerShell
        # Before
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/{alias-name}/B2CAssets/TrustFrameworkBase.xml
        ```

        Ha például a megadott alias *policyRepo*, az argumentumnak a következőnek kell lennie:

        ```PowerShell
        # After
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/policyRepo/B2CAssets/TrustFrameworkBase.xml
        ```

1. Válassza a **Mentés** lehetőséget az ügynök feladatainak mentéséhez.

Az imént hozzáadott feladat feltölt *egy* házirend-fájlt Azure ad B2Cba. A továbblépés előtt manuálisan aktiválja a feladatot (**kiadás létrehozása**), és győződjön meg arról, hogy a feladat sikeresen befejeződik, mielőtt további feladatokat hozna létre.

Ha a feladat sikeresen befejeződik, adja hozzá a telepítési feladatokat az egyes egyéni házirend-fájlok előző lépéseinek végrehajtásával. Módosítsa az `-PolicyId` `-PathToFile` egyes szabályzatok és argumentumok értékeit.

A értéke egy `PolicyId` XML-házirend fájljának elején található a TrustFrameworkPolicy csomóponton belül. Például a `PolicyId` következő házirend XML-fájlja *B2C_1A_TrustFrameworkBase*:

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

Az ügynökök futtatásakor és a házirend-fájlok feltöltésekor ügyeljen arra, hogy a következő sorrendben töltse fel őket:

1. *TrustFrameworkBase.xml*
1. *TrustFrameworkExtensions.xml*
1. *SignUpOrSignin.xml*
1. *ProfileEdit.xml*
1. *PasswordReset.xml*

Az Identity Experience Framework ezt a sorrendet kényszeríti, mivel a fájl szerkezete hierarchikus láncra épül.

## <a name="test-your-pipeline"></a>A folyamat tesztelése

A kiadási folyamat tesztelése:

1. Válassza a **folyamatok** , majd a **kiadások** lehetőséget.
1. Válassza ki a korábban létrehozott folyamatot, például *DeployCustomPolicies*.
1. Válassza a **kiadás létrehozása** elemet, majd válassza a **Létrehozás** lehetőséget a kiadás várólistára állításához.

Egy értesítési szalagcím jelenik meg, amely szerint a kiadás várólistára került. Az állapot megtekintéséhez válassza ki a hivatkozást az értesítési szalagcímben, vagy válassza ki a listából a **kiadások** lapon.

## <a name="next-steps"></a>Következő lépések

További információk:

* [Ügyfél-hitelesítő adatokat használó szolgáltatások közötti hívások](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)
* [Azure DevOps Services](/azure/devops/user-guide/)

<!-- LINKS - External -->
[devops]: /azure/devops/
[devops-create-project]:  /azure/devops/organizations/projects/create-project
[devops-pipelines]: /azure/devops/pipelines
