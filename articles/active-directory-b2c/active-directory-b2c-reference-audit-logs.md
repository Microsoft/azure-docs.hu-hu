---
title: Naplók elérése és áttekintése
titleSuffix: Azure AD B2C
description: Hogyan érheti el Azure AD B2C naplózási naplókat programozott módon és a Azure Portalban.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 10/16/2019
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: feefe7cf6d559360defd7c7f830a9e3f2e583cd6
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2019
ms.locfileid: "74948232"
---
# <a name="accessing-azure-ad-b2c-audit-logs"></a>Azure AD B2C naplók elérése

A Azure Active Directory B2C (Azure AD B2C) a B2C-erőforrásokkal, a kiállított jogkivonatokkal és a rendszergazdai hozzáféréssel kapcsolatos tevékenységeket tartalmazó naplókat bocsát ki. Ez a cikk röviden áttekinti a naplókban elérhető információkat, és leírja, hogyan férhet hozzá ezekhez az adatokhoz a Azure AD B2C bérlőhöz.

A naplózási naplózási események csak **hét napig**őrződnek meg. Tervezze meg a naplók letöltését és tárolását az alább látható módszerek valamelyikével, ha hosszabb megőrzési időtartamra van szüksége.

> [!NOTE]
> Az egyes Azure AD B2C alkalmazásokhoz tartozó felhasználói bejelentkezések nem jelennek meg a Azure Portal **Azure Active Directory** vagy **Azure ad B2C** lapjain a **felhasználók** szakaszban. A bejelentkezési események felhasználói tevékenységet mutatnak, de nem kapcsolhatók vissza arra a B2C-alkalmazásra, amelyhez a felhasználó bejelentkezett. Az ehhez szükséges naplókat a jelen cikkben ismertetett módon kell használni.

## <a name="overview-of-activities-available-in-the-b2c-category-of-audit-logs"></a>A B2C-kategóriákban elérhető tevékenységek áttekintése

A naplókban a **B2C** kategória a következő típusú tevékenységeket tartalmazza:

|Tevékenység típusa |Leírás  |
|---------|---------|
|Engedélyezés |A B2C-erőforrásokhoz való hozzáférés engedélyezésével kapcsolatos tevékenységek (például egy rendszergazda, amely a B2C-szabályzatok listáját használja).         |
|Könyvtár |Címtár-attribútumokkal kapcsolatos tevékenységek, amikor egy rendszergazda bejelentkezik a Azure Portal használatával. |
|Jelentkezés | A B2C-alkalmazásokhoz tartozó létrehozási, olvasási, frissítési és törlési (szifilisz-) műveletek. |
|Jelmagyarázat |A B2C-kulcstárolóban tárolt kulcsokra vonatkozó szifilisz-műveletek. |
|Erőforrás |A B2C-erőforrásokra vonatkozó szifilisz-műveletek. Például házirendek és identitás-szolgáltatók.
|Hitelesítés |A felhasználói hitelesítő adatok és a jogkivonatok kiadásának ellenőrzése.|

A felhasználói objektumhoz tartozó szifilisz-tevékenységek esetében tekintse meg az **alapvető könyvtár** kategóriát.

## <a name="example-activity"></a>Példa tevékenységre

Ez a példa a Azure Portal képet jeleníti meg, amikor egy felhasználó külső identitás-szolgáltatóval jelentkezik be, ebben az esetben a Facebook:

![Példa a naplózási tevékenység részleteinek oldalára Azure Portal](./media/active-directory-b2c-reference-audit-logs/audit-logs-example.png)

A tevékenység részletei panel a következő releváns információkat tartalmazza:

|Section|Mező|Leírás|
|-------|-----|-----------|
| Tevékenység | Név | Melyik tevékenység történt. Tegyük fel például, *hogy kiállít egy id_token az alkalmazáshoz*, amely a tényleges felhasználói bejelentkezést vonja le. |
| Kezdeményező (színész) | ObjectId | Annak a B2C-alkalmazásnak az **azonosítója** , amelyhez a felhasználó bejelentkezik. Ez az azonosító nem látható a Azure Portalban, de a Microsoft Graph API-n keresztül érhető el. |
| Kezdeményező (színész) | SPN | Annak a B2C-alkalmazásnak az **azonosítója** , amelyhez a felhasználó bejelentkezik. |
| Cél(ok) | ObjectId | A bejelentkezett felhasználó **objektumazonosító** . |
| További részletek | TenantId | A Azure AD B2C bérlő **bérlői azonosítója** . |
| További részletek | PolicyId | Annak a felhasználói folyamatnak (házirendnek) a **házirend-azonosítója** , amellyel a rendszer aláírja a felhasználót a alkalmazásban. |
| További részletek | ApplicationId | Annak a B2C-alkalmazásnak az **azonosítója** , amelyhez a felhasználó bejelentkezik. |

## <a name="view-audit-logs-in-the-azure-portal"></a>Naplófájlok megtekintése a Azure Portal

A Azure Portal hozzáférést biztosít a Azure AD B2C bérlő naplójának eseményeihez.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com)
1. Váltson arra a könyvtárra, amely a Azure AD B2C bérlőt tartalmazza, majd keresse meg **Azure ad B2C**.
1. A bal oldali menü **tevékenységek** területén válassza a **naplók**lehetőséget.

Megjelenik az elmúlt hét napban naplózott tevékenységi események listája.

![Példa szűrőre két tevékenységgel kapcsolatos eseménnyel kapcsolatban Azure Portal](media/active-directory-b2c-reference-audit-logs/audit-logs-example-filter.png)

Több szűrési lehetőség is elérhető, beleértve a következőket:

* **Tevékenység erőforrástípus** – a [tevékenységek elérhetővé](#overview-of-activities-available-in-the-b2c-category-of-audit-logs) tételének áttekintése című táblázatban látható tevékenységtípusok alapján szűrhet.
* **Dátum** – a megjelenített tevékenységek dátum-tartományának szűrése.

Ha kijelöl egy sort a listában, a program megjeleníti az esemény tevékenységének részleteit.

Ha le szeretné tölteni a tevékenységi események listáját egy vesszővel tagolt (CSV) fájlban, válassza a **Letöltés**lehetőséget.

## <a name="get-audit-logs-with-the-azure-ad-reporting-api"></a>Naplók beszerzése az Azure AD Reporting API-val

A naplók ugyanazon a folyamaton lesznek közzétéve, mint a Azure Active Directory egyéb tevékenységei, így azok a [Azure Active Directory jelentési API](https://docs.microsoft.com/graph/api/directoryaudit-list)-n keresztül érhetők el. További információ: Ismerkedés [a Azure Active Directory Reporting API-val](../active-directory/reports-monitoring/concept-reporting-api.md).

### <a name="enable-reporting-api-access"></a>Jelentéskészítési API-hozzáférés engedélyezése

Az Azure AD Reporting API-hoz való parancsfájl-vagy alkalmazás-hozzáférés engedélyezéséhez Azure Active Directory alkalmazásra van szükség a Azure AD B2C-bérlőben a következő API-engedélyekkel:

* Microsoft Graph > alkalmazás engedélyei > AuditLog. Read. All

Ezeket az engedélyeket engedélyezheti a B2C-bérlőn belüli meglévő Azure Active Directory alkalmazások regisztrálásához, vagy létrehozhat egy újat, kifejezetten a log Automation használatával.

Kövesse az alábbi lépéseket egy alkalmazás regisztrálásához, adja meg a szükséges Microsoft Graph API-engedélyeket, majd hozzon létre egy ügyfél-titkot.

### <a name="register-application-in-azure-active-directory"></a>Alkalmazás regisztrálása a Azure Active Directoryban

[!INCLUDE [active-directory-b2c-appreg-mgmt](../../includes/active-directory-b2c-appreg-mgmt.md)]

### <a name="assign-api-access-permissions"></a>API-hozzáférési engedélyek kiosztása

#### <a name="applicationstabapplications"></a>[Alkalmazások](#tab/applications/)

1. A **regisztrált alkalmazás** áttekintése lapon válassza a **Beállítások**lehetőséget.
1. Az **API-hozzáférés**területen válassza a **szükséges engedélyek**lehetőséget.
1. Válassza a **Hozzáadás**lehetőséget, majd **válasszon ki egy API**-t.
1. Válassza a **Microsoft Graph**lehetőséget, majd **válassza a elemet**.
1. Az **alkalmazás engedélyei**területen válassza az **összes naplózási napló adatának olvasása**elemet.
1. Kattintson a **kiválasztás** gombra, majd válassza a **kész**lehetőséget.
1. Válassza az **engedélyek megadása**lehetőséget, majd válassza az **Igen**lehetőséget.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Alkalmazásregisztrációk (előzetes verzió)](#tab/app-reg-preview/)

1. A **kezelés**területen válassza az **API-engedélyek**lehetőséget.
1. A **konfigurált engedélyek**területen válassza **az engedély hozzáadása**elemet.
1. Válassza a **Microsoft API** -k fület.
1. Válassza a **Microsoft Graph**lehetőséget.
1. Válassza ki az **alkalmazás engedélyeit**.
1. Bontsa ki a **AuditLog** elemet, majd válassza ki a **AuditLog. Read. All** jelölőnégyzetet.
1. Válassza az **engedélyek hozzáadása**lehetőséget. Az utasítás szerint várjon néhány percet, mielőtt továbblép a következő lépésre.
1. Válassza a **rendszergazdai jóváhagyás megadása (a bérlő neve)** lehetőséget.
1. Válassza ki a jelenleg bejelentkezett fiókot, ha az hozzá van rendelve a *globális rendszergazdai* szerepkörhöz, vagy jelentkezzen be egy olyan fiókkal, amely a *globális rendszergazdai* szerepkörhöz lett rendelve a Azure ad B2C-bérlőben.
1. Válassza ki az **Elfogadás** lehetőséget.
1. Válassza a **frissítés**lehetőséget, majd ellenőrizze, hogy a "engedélyezve..." az *AuditLog. Read. All* engedély **állapota** alatt jelenik meg. Eltarthat néhány percig, amíg az engedélyek propagálása megtörténik.

* * *

### <a name="create-client-secret"></a>Ügyfél titkos kulcsának létrehozása

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

Most már rendelkezik egy alkalmazással a szükséges API-hozzáféréssel, egy alkalmazás-AZONOSÍTÓval és egy, az Automation-parancsfájlokban használható kulccsal. A cikk későbbi részében a PowerShell-szkript című szakaszban talál példát arra, hogyan kérhető le a tevékenység eseményei egy parancsfájl használatával.

### <a name="access-the-api"></a>Hozzáférés az API-hoz

Azure AD B2C naplózási események API-n keresztüli letöltéséhez szűrje a naplókat a `B2C` kategóriába. A kategória szerinti szűréshez használja a `filter` lekérdezési karakterlánc paramétert az Azure AD Reporting API-végpont meghívásakor.

```HTTP
https://graph.microsoft.com/v1.0/auditLogs/directoryAudits?$filter=loggedByService eq 'B2C' and activityDateTime gt 2019-09-10T02:28:17Z
```

### <a name="powershell-script"></a>PowerShell-szkript

Az alábbi PowerShell-szkript bemutatja, hogyan lehet lekérdezni az Azure AD Reporting API-t. Az API lekérdezése után kinyomtatja a naplózott eseményeket a standard kimenetre, majd a JSON-kimenetet fájlba írja.

Ezt a szkriptet a [Azure Cloud Shell](../cloud-shell/overview.md)is kipróbálhatja. Ne felejtse el frissíteni az alkalmazás-azonosítóval, az ügyfél titkos kódjával és a Azure AD B2C bérlő nevével.

```powershell
# This script requires the registration of a Web Application in Azure Active Directory:
# https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-reporting-api

# Constants
$ClientID       = "your-client-application-id-here"       # Insert your application's client ID, a GUID (registered by Global Admin)
$ClientSecret   = "your-client-application-secret-here"   # Insert your application's client secret
$tenantdomain   = "your-b2c-tenant.onmicrosoft.com"       # Insert your Azure AD B2C tenant; for example, contoso.onmicrosoft.com
$loginURL       = "https://login.microsoftonline.com"
$resource       = "https://graph.microsoft.com"           # Microsoft Graph API resource URI
$7daysago       = "{0:s}" -f (get-date).AddDays(-7) + "Z" # Use 'AddMinutes(-5)' to decrement minutes, for example
Write-Output "Searching for events starting $7daysago"

# Create HTTP header, get an OAuth2 access token based on client id, secret and tenant domain
$body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

# Parse audit report items, save output to file(s): auditX.json, where X = 0 thru n for number of nextLink pages
if ($oauth.access_token -ne $null) {
    $i=0
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
    $url = "https://graph.microsoft.com/v1.0/auditLogs/directoryAudits?`$filter=loggedByService eq 'B2C' and activityDateTime gt  " + $7daysago

    # loop through each query page (1 through n)
    Do {
        # display each event on the console window
        Write-Output "Fetching data using Uri: $url"
        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output ($event | ConvertTo-Json)
        }

        # save the query page to an output file
        Write-Output "Save the output to a file audit$i.json"
        $myReport.Content | Out-File -FilePath audit$i.json -Force
        $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'
        $i = $i+1
    } while($url -ne $null)
} else {
    Write-Host "ERROR: No Access Token"
}
```

Itt látható a cikkben korábban bemutatott példa tevékenység esemény JSON-ábrázolása:

```JSON
{
    "id": "B2C_DQO3J_4984536",
    "category": "Authentication",
    "correlationId": "00000000-0000-0000-0000-000000000000",
    "result": "success",
    "resultReason": "N/A",
    "activityDisplayName": "Issue an id_token to the application",
    "activityDateTime": "2019-09-14T18:13:17.0618117Z",
    "loggedByService": "B2C",
    "operationType": "",
    "initiatedBy": {
        "user": null,
        "app": {
            "appId": "00000000-0000-0000-0000-000000000000",
            "displayName": null,
            "servicePrincipalId": null,
            "servicePrincipalName": "00000000-0000-0000-0000-000000000000"
        }
    },
    "targetResources": [
        {
            "id": "00000000-0000-0000-0000-000000000000",
            "displayName": null,
            "type": "User",
            "userPrincipalName": null,
            "groupType": null,
            "modifiedProperties": []
        }
    ],
    "additionalDetails": [
        {
            "key": "TenantId",
            "value": "test.onmicrosoft.com"
        },
        {
            "key": "PolicyId",
            "value": "B2C_1A_signup_signin"
        },
        {
            "key": "ApplicationId",
            "value": "00000000-0000-0000-0000-000000000000"
        },
        {
            "key": "Client",
            "value": "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.132 Safari/537.36"
        },
        {
            "key": "IdentityProviderName",
            "value": "facebook"
        },
        {
            "key": "IdentityProviderApplicationId",
            "value": "0000000000000000"
        },
        {
            "key": "ClientIpAddress",
            "value": "127.0.0.1"
        }
    ]
}
```

## <a name="next-steps"></a>Következő lépések

Automatizálhat más felügyeleti feladatokat, például [kezelheti a felhasználókat a .net](active-directory-b2c-devquickstarts-graph-dotnet.md)használatával.
