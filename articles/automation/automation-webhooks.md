---
title: Runbook Azure Automation webhookból
description: Ez a cikk bemutatja, hogyan indíthat el egy runbookot egy webhookkal a Azure Automation http-hívásból.
services: automation
ms.subservice: process-automation
ms.date: 03/18/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 338fb56c4af5c24b7b746ffd6508c2fe7d52b131
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830195"
---
# <a name="start-a-runbook-from-a-webhook"></a>Runbook indítása webhookból

A webhookok lehetővé teszik, hogy egy külső szolgáltatás egy adott runbookot indítson Azure Automation egy HTTP-kérésen keresztül. A külső szolgáltatások közé tartozik az Azure DevOps Services, a GitHub, Azure Monitor naplók és egyéni alkalmazások. Az ilyen szolgáltatások webhookkal indítják el a runbookokat anélkül, hogy a teljes Azure Automation API-t. A runbookok indításának egyéb módszereivel összehasonlíthatja a webhookokat a [Runbook](./start-runbooks.md)indítása a Azure Automation.

> [!NOTE]
> A Python-runbookok webhookokkal való elindítani nem támogatottak.

![WebhookokÁttekintés](media/automation-webhooks/webhook-overview-image.png)

A TLS 1.2 webhookokkal kapcsolatos ügyfélkövetelményeit lásd: [TLS 1.2](automation-managing-data.md#tls-12-enforcement-for-azure-automation)kényszerítési szolgáltatás Azure Automation.

## <a name="webhook-properties"></a>Webhook tulajdonságai

Az alábbi táblázat azokat a tulajdonságokat ismerteti, amelyek a webhookhoz konfigurálhatóak.

| Tulajdonság | Leírás |
|:--- |:--- |
| Név |A webhook neve. Bármilyen nevet meg lehet adni, mivel az nem lesz elérhető az ügyfél számára. Csak arra használható, hogy azonosítsa a runbookot a Azure Automation. Ajánlott eljárásként adjon nevet a webhooknak az azt használó ügyfélhez kapcsolódóan. |
| URL-cím |A webhook URL-címe. Ez az egyedi cím, amit az ügyfél http POST-hívással hív meg a webhookhoz csatolt runbook elindítani. A webhook létrehozásakor automatikusan létrejön. Egyéni URL-címet nem lehet megadni. <br> <br> Az URL-cím tartalmaz egy biztonsági jogkivonatot, amely lehetővé teszi, hogy egy külső rendszer további hitelesítés nélkül hívja meg a runbookot. Ezért az URL-címet jelszóként kell kezelni. Biztonsági okokból csak a webhook létrehozásakor Azure Portal URL-címet. Jegyezze fel az URL-címet egy biztonságos helyen későbbi használatra. |
| Lejárat dátuma | A webhook lejárati dátuma, amely után már nem használható. A webhook létrehozása után módosíthatja a lejárati dátumot, ha a webhook még nem járt le. |
| Engedélyezve | Beállítás, amely azt jelzi, hogy a webhook alapértelmezés szerint engedélyezve van-e a létrehozásakor. Ha ezt a tulajdonságot Letiltva beállításra adja meg, egyetlen ügyfél sem használhatja a webhookot. Ezt a tulajdonságot a webhook létrehozásakor vagy a létrehozása után bármikor beállíthatja. |

## <a name="parameters-used-when-the-webhook-starts-a-runbook"></a>A webhook runbook indításakor használt paraméterek

A webhook meghatározhatja a runbook indításakor használt runbook-paraméterek értékeit. A webhooknak tartalmaznia kell értékeket minden kötelező runbook-paraméterhez, és tartalmazhat értékeket a választható paraméterekhez. A webhookhoz konfigurált paraméterértékek a webhook létrehozása után is módosíthatók. Egy runbookhoz csatolt több webhook különböző runbook-paraméterértékeket használhat. Amikor egy ügyfél webhookkal elindít egy runbookot, az nem bírálhatja felül a webhookban meghatározott paraméterértékeket.

Az ügyféltől való adatfogadáshoz a runbook egyetlen, nevű paramétert `WebhookData` támogat. Ez a paraméter definiál egy objektumot, amely egy POST-kérésbe az ügyfél által tartalmazott adatokat tartalmazza.

![WebhookData tulajdonságai](media/automation-webhooks/webhook-data-properties.png)

A `WebhookData` paraméter a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Leírás |
|:--- |:--- |
| `WebhookName` | A webhook neve. |
| `RequestHeader` | A bejövő POST-kérés fejlécét tartalmazó kivonattábla. |
| `RequestBody` | A bejövő POST-kérés törzse. Ez a törzs megőriz minden adatformázást, például sztringet, JSON-t, XML-t vagy űrlapkódoltat. A runbookot úgy kell megírni, hogy az a várt adatformátummal működjön. |

A paraméter támogatásához nem szükséges konfigurálni a webhookot, és a runbooknak nem kell `WebhookData` elfogadnia. Ha a runbook nem határozza meg a paramétert, a rendszer figyelmen kívül hagyja az ügyféltől küldött kérelem részleteit.

> [!NOTE]
> Webhook hívása esetén az ügyfélnek minden paraméterértéket tárolnia kell arra az esetre, ha a hívás meghiúsul. Ha hálózati kimaradás vagy kapcsolati probléma van, az alkalmazás nem tudja lekérni a sikertelen webhookhívásokat.

Ha a webhook létrehozásakor megad egy értéket, az felül lesz bírálva, amikor a webhook elindítja a runbookot az ügyfél `WebhookData` POST-kéréséből származó adatokkal. Ez akkor is előfordul, ha az alkalmazás nem tartalmaz adatokat a kérelem törzsében. 

Ha egy webhooktól különböző mechanizmussal definiáló runbookot indít el, megadhatja a runbook által felismert `WebhookData` `WebhookData` értéket. Ennek az értéknek olyan [](#webhook-properties) objektumnak kell lennie, amely ugyanazokkal a tulajdonságokkal rendelkezik, mint a paraméter, hogy a runbook ugyanúgy működjön vele, mint a webhookok által átadott `WebhookData` `WebhookData` tényleges objektumokkal.

Ha például a Azure Portal-ból kezdi el a következő runbookot, és szeretne átadni néhány minta webhookadatot a teszteléshez, az adatokat JSON-ban kell átadnia a felhasználói felületen.

![WebhookData paraméter a felhasználói felületről](media/automation-webhooks/WebhookData-parameter-from-UI.png)

A következő runbook-példához definiáljuk a következő `WebhookData` tulajdonságait:

* **WebhookName:** MyWebhook
* **RequestBody**: `*[{'ResourceGroup': 'myResourceGroup','Name': 'vm01'},{'ResourceGroup': 'myResourceGroup','Name': 'vm02'}]*`

Most át kell adni a következő JSON-objektumot a felhasználói felületen a `WebhookData` paraméterhez. Ez a példa kocsivissza karakterekkel és újsor karakterekkel megegyezik a webhookból átadott formátummal.

```json
{"WebhookName":"mywebhook","RequestBody":"[\r\n {\r\n \"ResourceGroup\": \"vm01\",\r\n \"Name\": \"vm01\"\r\n },\r\n {\r\n \"ResourceGroup\": \"vm02\",\r\n \"Name\": \"vm02\"\r\n }\r\n]"}
```

![Indítsa el a WebhookData paramétert a felhasználói felületről](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> Azure Automation naplózza az összes bemeneti paraméter értékét a runbook feladatával. Így az ügyfél által a webhookkérésben megadott bemenetek naplózva vannak, és bárki számára elérhetők, aki hozzáféréssel rendelkezik az automatizálási feladathoz. Ezért körültekintően kell eljárnia a bizalmas adatok webhookhívásokbe való be- és beiktázatásában.

## <a name="webhook-security"></a>Webhook-biztonság

A webhookok biztonsága az URL-cím védelmére támaszkodik, amely egy biztonsági jogkivonatot tartalmaz, amely lehetővé teszi a webhook meghívását. Azure Automation nem végez hitelesítést a kérésen, amíg az a megfelelő URL-címre van érvénybe hozva. Emiatt az ügyfelek nem használhatnak webhookokat olyan runbookok esetében, amelyek rendkívül bizalmas műveleteket végeznek anélkül, hogy alternatív módon érvényesíteik a kérést.

Vegye figyelembe a következő stratégiákat:

* A runbookba logikát is be lehet foglalni annak megállapításához, hogy webhookok hívják-e meg. A runbooknak ellenőriznie `WebhookName` kell a paraméter `WebhookData` tulajdonságát. A runbook további ellenőrzést végezhet, ha adott információkat keres a és tulajdonságok `RequestHeader` `RequestBody` között.

* Kérje meg a runbookot, hogy hajtson végre valamilyen ellenőrzést egy külső feltételen, amikor webhookkérést kap. Vehet például egy olyan runbookot, amelyet a GitHub hív meg, amikor új véglegesítés történik egy GitHub-adattárban. Előfordulhat, hogy a runbook a GitHubhoz csatlakozva ellenőrzi, hogy történt-e új véglegesítés a folytatás előtt.

* Azure Automation támogatja az Azure-beli virtuális hálózati szolgáltatáscímkéket, különösen a [GuestAndHybridManagement címkét.](../virtual-network/service-tags-overview.md) A szolgáltatáscímkék használatával hálózati hozzáférés-vezérlést [](../firewall/service-tags.md) határozhat meg a hálózati biztonsági csoportokhoz vagy Azure Firewall webhookokat aktiválhat a virtuális hálózaton belülről. [](../virtual-network/network-security-groups-overview.md#security-rules) A szolgáltatáscímkék adott IP-címek helyett használhatók a biztonsági szabályok létrehozásakor. Ha egy szabály megfelelő forrás- vagy célmezőjeként megadja a **GuestAndHybridManagement**  szolgáltatáscímkenevet, engedélyezheti vagy megtagadhatja az Automation szolgáltatás forgalmát. Ez a szolgáltatáscímke nem támogatja részletesebb vezérlést az IP-címtartományok egy adott régióra való korlátozásával.

## <a name="create-a-webhook"></a>Webhook létrehozása

Az alábbi eljárással hozhat létre a runbookhoz csatolt új webhookot a Azure Portal.

1. A forgatókönyv Runbookok Azure Portal kattintson arra a runbookra, amelybe a webhook elindul a runbook részleteinek megtekintéséhez. Győződjön meg arról, hogy a runbook **Állapota** mezője **Közzétettre van állítva.**
2. Kattintson a lap tetején található **Webhook** elemre a Webhook hozzáadása lap megnyitásához.
3. Kattintson **az Új webhook létrehozása elemre** a Webhook létrehozása lap megnyitásához.
4. Töltse ki a  **webhook** Név és Lejárat dátuma mezőit, és adja meg, hogy engedélyezni kell-e. További információ ezekről a tulajdonságokról: [Webhook](#webhook-properties) tulajdonságai.
5. Kattintson a másolás ikonra, majd nyomja le a Ctrl+C billentyűkombinációt a webhook URL-címének másoláshoz. Ezután rögzítse egy biztonságos helyen. 

    > [!IMPORTANT]
    > A webhook létrehozása után nem tudja ismét lekérni az URL-címet. Győződjön meg arról, hogy a fentiek szerint másolja és rögzíti.

   ![Webhook URL-címe](media/automation-webhooks/copy-webhook-url.png)

1. Kattintson **a Paraméterek** elemre a runbook paramétereinek értékeinek megadása érdekében. Ha a runbook kötelező paraméterekkel rendelkezik, nem hozhatja létre a webhookot, hacsak nem ad meg értékeket.

2. A webhook létrehozásához kattintson a **Létrehozás** elemre.

## <a name="use-a-webhook"></a>Webhook használata

A webhookok létrehozása után az ügyfélnek ki kell adnunk egy HTTP-kérést a `POST` webhook URL-címével. A szintaxis a következő:

```http
http://<Webhook Server>/token?=<Token Value>
```

Az ügyfél az alábbi visszatérési kódok egyikét kapja meg a `POST` kéréstől.

| Code | Szöveg | Description |
|:--- |:--- |:--- |
| 202 |Elfogadva |A kérelem el lett fogadva, és a runbook sikeresen várólistára lett stb. |
| 400 |Hibás kérés |A kérést a következő okok miatt nem fogadták el: <ul> <li>A webhook lejárt.</li> <li>A webhook le van tiltva.</li> <li>Az URL-címben található jogkivonat érvénytelen.</li>  </ul> |
| 404 |Nem található |A kérelmet a következő okok miatt nem fogadták el: <ul> <li>A webhook nem található.</li> <li>A runbook nem található.</li> <li>A fiók nem található.</li>  </ul> |
| 500 |Belső kiszolgálóhiba |Az URL-cím érvényes volt, de hiba történt. Kérjük, adja meg újra a kérést. |

Ha a kérés sikeres, a webhook-válasz JSON formátumban tartalmazza a feladat azonosítóját az alább látható módon. Egyetlen feladatazonosítót tartalmaz, de a JSON formátum lehetővé teszi a jövőbeli lehetséges fejlesztéseket.

```json
{"JobIds":["<JobId>"]}
```

Az ügyfél nem tudja meghatározni, hogy mikor fejeződik be a runbook-feladat, vagy hogy mikor fejeződött be a webhook. Ezt az információt a feladatazonosító egy másik mechanizmussal, például a Windows PowerShell vagy a Azure Automation [](/powershell/module/servicemanagement/azure.service/get-azureautomationjob) [mechanizmussal derítheti fel.](/rest/api/automation/job)

### <a name="use-a-webhook-from-an-arm-template"></a>ARM-sablonból származó webhook használata

Az Automation-webhookok is meghívhatóak Azure Resource Manager [(ARM) sablonjaival.](/azure/azure-resource-manager/templates/overview) Az ARM-sablon egy kérést ad ki, és a többi ügyfélhez hasonló visszatérési kódot `POST` kap. Lásd: [Webhook használata.](#use-a-webhook)

   > [!NOTE]
   > Biztonsági okokból a rendszer csak a sablon első üzembe helyezésekor ad vissza URI-t.

Ez a mintasablon létrehoz egy tesztkörnyezetet, és visszaadja a létrehozott webhook URI-ját.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "automationAccountName": {
            "type": "String",
            "metadata": {
                "description": "Automation account name"
            }
        },
        "webhookName": {
            "type": "String",
            "metadata": {
                "description": "Webhook Name"
            }
        },
        "runbookName": {
            "type": "String",
            "metadata": {
                "description": "Runbook Name for which webhook will be created"
            }
        },
        "WebhookExpiryTime": {
            "type": "String",
            "metadata": {
                "description": "Webhook Expiry time"
            }
        },
        "_artifactsLocation": {
            "defaultValue": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-automation/",
            "type": "String",
            "metadata": {
                "description": "URI to artifacts location"
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Automation/automationAccounts",
            "apiVersion": "2020-01-13-preview",
            "name": "[parameters('automationAccountName')]",
            "location": "[resourceGroup().location]",
            "properties": {
                "sku": {
                    "name": "Free"
                }
            },
            "resources": [
                {
                    "type": "runbooks",
                    "apiVersion": "2018-06-30",
                    "name": "[parameters('runbookName')]",
                    "location": "[resourceGroup().location]",
                    "dependsOn": [
                        "[parameters('automationAccountName')]"
                    ],
                    "properties": {
                        "runbookType": "Python2",
                        "logProgress": "false",
                        "logVerbose": "false",
                        "description": "Sample Runbook",
                        "publishContentLink": {
                            "uri": "[uri(parameters('_artifactsLocation'), 'scripts/AzureAutomationTutorialPython2.py')]",
                            "version": "1.0.0.0"
                        }
                    }
                },
                {
                    "type": "webhooks",
                    "apiVersion": "2018-06-30",
                    "name": "[parameters('webhookName')]",
                    "dependsOn": [
                        "[parameters('automationAccountName')]",
                        "[parameters('runbookName')]"
                    ],
                    "properties": {
                        "isEnabled": true,
                        "expiryTime": "[parameters('WebhookExpiryTime')]",
                        "runbook": {
                            "name": "[parameters('runbookName')]"
                        }
                    }
                }
            ]
        }
    ],
    "outputs": {
        "webhookUri": {
            "type": "String",
            "value": "[reference(parameters('webhookName')).uri]"
        }
    }
}
```

## <a name="renew-a-webhook"></a>Webhook megújítása

A létrehozott webhookok érvényességi időtartama 10 év, amely után automatikusan lejár. Ha egy webhook lejárt, nem aktiválhatja újra. Csak eltávolíthatja, majd újra létrehozhatja. 

Kiterjeszthet egy olyan webhookot, amely még nem érte el a lejárati idejét. Webhookok kiterjesztése:

1. Keresse meg a webhookot tartalmazó runbookot. 
2. A Resources (Erőforrások) alatt válassza a **Webhooks (Webhookok)** **lehetőséget.** 
3. Kattintson a kiterjeszteni kívánt webhookra. 
4. A Webhook lapon válasszon ki egy új lejárati dátumot és időpontot, majd kattintson a **Mentés gombra.**

## <a name="sample-runbook"></a>Minta runbook

A következő minta runbook elfogadja a webhookadatokat, és elindítja a kérés törzsében megadott virtuális gépeket. A runbook teszteléshez kattintson az Automation-fiók **Runbookok** területén a **Runbook létrehozása elemre.** Ha nem tudja, hogyan hozhat létre runbookot, tekintse meg [a runbook létrehozását.](automation-quickstart-create-runbook.md)

> [!NOTE]
> Nem grafikus PowerShell-runbookok esetén, amelyek a `Add-AzAccount` `Add-AzureRMAccount` [Connect-AzAccount aliasai.](/powershell/module/az.accounts/connect-azaccount) Használhatja ezeket a parancsmagokat, [](automation-update-azure-modules.md) vagy frissítheti az Automation-fiókjában található modulokat a legújabb verziókra. Előfordulhat, hogy akkor is frissítenie kell a modulokat, ha most hozott létre egy új Automation-fiókot.

```powershell
param
(
    [Parameter (Mandatory = $false)]
    [object] $WebhookData
)

# If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData) {

    # Check header for message to validate request
    if ($WebhookData.RequestHeader.message -eq 'StartedbyContoso')
    {
        Write-Output "Header has required information"}
    else
    {
        Write-Output "Header missing required information";
        exit;
    }

    # Retrieve VMs from Webhook request body
    $vms = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Authenticate to Azure by using the service principal and certificate. Then, set the subscription.

    Write-Output "Authenticating to Azure with service principal and certificate"
    $ConnectionAssetName = "AzureRunAsConnection"
    Write-Output "Get connection asset: $ConnectionAssetName"

    $Conn = Get-AutomationConnection -Name $ConnectionAssetName
            if ($Conn -eq $null)
            {
                throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
            }
            Write-Output "Authenticating to Azure with service principal."
            Add-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Output

        # Start each virtual machine
        foreach ($vm in $vms)
        {
            $vmName = $vm.Name
            Write-Output "Starting $vmName"
            Start-AzVM -Name $vm.Name -ResourceGroup $vm.ResourceGroup
        }
}
else {
    # Error
    write-Error "This runbook is meant to be started from an Azure alert webhook only."
}
```

## <a name="test-the-sample"></a>A minta tesztelése

Az alábbi példa Windows PowerShell webhookkal indít el egy runbookot. Bármely nyelv, amely képes HTTP-kérések igénylésére, használhat webhookot. Windows PowerShell itt példaként használjuk.

A runbook a kérelem törzsében JSON formátumban formázott virtuális gépek listáját várja. A runbook ellenőrzi, hogy a fejlécek tartalmaznak-e egy meghatározott üzenetet, amely ellenőrzi, hogy a webhook hívója érvényes-e.

```azurepowershell-interactive
$uri = "<webHook Uri>"

$vms  = @(
            @{ Name="vm01";ResourceGroup="vm01"},
            @{ Name="vm02";ResourceGroup="vm02"}
        )
$body = ConvertTo-Json -InputObject $vms
$header = @{ message="StartedbyContoso"}
$response = Invoke-WebRequest -Method Post -Uri $uri -Body $body -Headers $header
$jobid = (ConvertFrom-Json ($response.Content)).jobids[0]
```

Az alábbi példa a runbook számára a tulajdonságában elérhető kérés `RequestBody` törzsét mutatja `WebhookData` be. Ez az érték JSON formátumban van formázva, hogy kompatibilis legyen a kérés törzsében szereplő formátummal.

```json
[
    {
        "Name":  "vm01",
        "ResourceGroup":  "myResourceGroup"
    },
    {
        "Name":  "vm02",
        "ResourceGroup":  "myResourceGroup"
    }
]
```

Az alábbi képen a rendszer által küldött kérés Windows PowerShell az eredményül kapott válasz látható. A feladatazonosító a válaszból lesz kinyerve, és sztringgé lesz konvertálva.

![Webhookok gomb](media/automation-webhooks/webhook-request-response.png)

## <a name="next-steps"></a>Következő lépések

* Runbook riasztásból való aktiválását lásd: Riasztás használata [runbook Azure Automation aktiválása.](automation-create-alert-triggered-runbook.md)
