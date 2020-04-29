---
title: Erőforrás létrehozása a Modern olvasóhoz
titleSuffix: Azure Cognitive Services
description: Ez a cikk bemutatja, hogyan hozhat létre egy új, teljes értékű olvasó-erőforrást egy egyéni altartománnyal, majd hogyan konfigurálhatja az Azure AD-t az Azure-bérlőben.
services: cognitive-services
author: rwaller
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: rwaller
ms.openlocfilehash: 41efe4592c65ae3cdd85ce1b212554e50691905a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "78330719"
---
# <a name="create-an-immersive-reader-resource-and-configure-azure-active-directory-authentication"></a>Részletes olvasó erőforrás létrehozása és Azure Active Directory hitelesítés konfigurálása

Ebben a cikkben egy olyan parancsfájlt biztosítunk, amely egy teljes olvasó erőforrást hoz létre, és konfigurálja Azure Active Directory (Azure AD) hitelesítést. Minden alkalommal, amikor létrejön egy magára ejtő olvasó erőforrás, legyen az a parancsfájl vagy a portálon, az Azure AD-engedélyekkel is konfigurálni kell. Ez a szkript segítséget nyújt Önnek.

A szkript úgy van kialakítva, hogy az összes szükséges teljes olvasót és Azure AD-erőforrást egyetlen lépésben hozza létre és konfigurálja. Ugyanakkor az Azure AD-hitelesítést is konfigurálhatja egy meglévő, magával ragadó olvasó erőforráshoz, ha például már létrehozta az egyiket a Azure Portal.

Egyes ügyfelek esetében szükség lehet több, a fejlesztéshez és a gyártáshoz, vagy akár több, a szolgáltatás üzembe helyezéséhez használt régióra is. Ilyen esetekben előfordulhat, hogy a parancsfájlt többször is felhasználhatja, hogy különböző, az olvasóhoz tartozó erőforrásokat hozzon létre, és az Azure AD-engedélyekkel konfigurálható legyen.

A szkript rugalmasnak lett tervezve. Először az előfizetésben található, a meglévő és az Azure AD-erőforrásokat fogja keresni, és csak akkor kell létrehoznia őket, ha még nem léteznek. Ha első alkalommal hozza létre az olvasói erőforrást, a szkript mindent megtesz, amire szüksége van. Ha azt szeretné, hogy az Azure AD-t egy meglévő, a portálon létrehozott, magától az olvasói erőforráshoz konfigurálja, azt is végrehajtja. Emellett több magával ragadó olvasó-erőforrás létrehozására és konfigurálására is használható.

## <a name="set-up-powershell-environment"></a>PowerShell-környezet beállítása

1. Először nyissa meg a [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview). Győződjön meg arról, hogy a Cloud Shell a PowerShell értékre van állítva a bal felső sarokban, `pwsh`vagy írja be a következőt:.

1. Másolja és illessze be a következő kódrészletet a rendszerhéjba.

    ```azurepowershell-interactive
    function Create-ImmersiveReaderResource(
        [Parameter(Mandatory=$true, Position=0)] [String] $SubscriptionName,
        [Parameter(Mandatory=$true)] [String] $ResourceName,
        [Parameter(Mandatory=$true)] [String] $ResourceSubdomain,
        [Parameter(Mandatory=$true)] [String] $ResourceSKU,
        [Parameter(Mandatory=$true)] [String] $ResourceLocation,
        [Parameter(Mandatory=$true)] [String] $ResourceGroupName,
        [Parameter(Mandatory=$true)] [String] $ResourceGroupLocation,
        [Parameter(Mandatory=$true)] [String] $AADAppDisplayName="ImmersiveReaderAAD",
        [Parameter(Mandatory=$true)] [String] $AADAppIdentifierUri,
        [Parameter(Mandatory=$true)] [String] $AADAppClientSecret
    )
    {
        $unused = ''
        if (-not [System.Uri]::TryCreate($AADAppIdentifierUri, [System.UriKind]::Absolute, [ref] $unused)) {
            throw "Error: AADAppIdentifierUri must be a valid URI"
        }

        Write-Host "Setting the active subscription to '$SubscriptionName'"
        $subscriptionExists = Get-AzSubscription -SubscriptionName $SubscriptionName
        if (-not $subscriptionExists) {
            throw "Error: Subscription does not exist"
        }
        az account set --subscription $SubscriptionName

        $resourceGroupExists = az group exists --name $ResourceGroupName
        if ($resourceGroupExists -eq "false") {
            Write-Host "Resource group does not exist. Creating resource group"
            $groupResult = az group create --name $ResourceGroupName --location $ResourceGroupLocation
            if (-not $groupResult) {
                throw "Error: Failed to create resource group"
            }
            Write-Host "Resource group created successfully"
        }

        # Create an Immersive Reader resource if it doesn't already exist
        $resourceId = az cognitiveservices account show --resource-group $ResourceGroupName --name $ResourceName --query "id" -o tsv
        if (-not $resourceId) {
            Write-Host "Creating the new Immersive Reader resource '$ResourceName' (SKU '$ResourceSKU') in '$ResourceLocation' with subdomain '$ResourceSubdomain'"
            $resourceId = az cognitiveservices account create `
                            --name $ResourceName `
                            --resource-group $ResourceGroupName `
                            --kind ImmersiveReader `
                            --sku $ResourceSKU `
                            --location $ResourceLocation `
                            --custom-domain $ResourceSubdomain `
                            --query "id" `
                            -o tsv

            if (-not $resourceId) {
                throw "Error: Failed to create Immersive Reader resource"
            }
            Write-Host "Immersive Reader resource created successfully"
        }

        # Create an Azure Active Directory app if it doesn't already exist
        $clientId = az ad app show --id $AADAppIdentifierUri --query "appId" -o tsv
        if (-not $clientId) {
            Write-Host "Creating new Azure Active Directory app"
            $clientId = az ad app create --password $AADAppClientSecret --display-name $AADAppDisplayName --identifier-uris $AADAppIdentifierUri --query "appId" -o tsv

            if (-not $clientId) {
                throw "Error: Failed to create Azure Active Directory app"
            }
            Write-Host "Azure Active Directory app created successfully"
        }

        # Create a service principal if it doesn't already exist
        $principalId = az ad sp show --id $AADAppIdentifierUri --query "objectId" -o tsv
        if (-not $principalId) {
            Write-Host "Creating new service principal"
            az ad sp create --id $clientId | Out-Null
            $principalId = az ad sp show --id $AADAppIdentifierUri --query "objectId" -o tsv

            if (-not $principalId) {
                throw "Error: Failed to create new service principal"
            }
            Write-Host "New service principal created successfully"
        }

        # Sleep for 5 seconds to allow the new service principal to propagate
        Write-Host "Sleeping for 5 seconds"
        Start-Sleep -Seconds 5

        Write-Host "Granting service principal access to the newly created Immersive Reader resource"
        $accessResult = az role assignment create --assignee $principalId --scope $resourceId --role "Cognitive Services User"
        if (-not $accessResult) {
            throw "Error: Failed to grant service principal access"
        }
        Write-Host "Service principal access granted successfully"

        # Grab the tenant ID, which is needed when obtaining an Azure AD token
        $tenantId = az account show --query "tenantId" -o tsv

        # Collect the information needed to obtain an Azure AD token into one object
        $result = @{}
        $result.TenantId = $tenantId
        $result.ClientId = $clientId
        $result.ClientSecret = $AADAppClientSecret
        $result.Subdomain = $ResourceSubdomain

        Write-Host "Success! " -ForegroundColor Green -NoNewline
        Write-Host "Save the following JSON object to a text file for future reference:"
        Write-Output (ConvertTo-Json $result)
    }
    ```

1. Futtassa a függvényt `Create-ImmersiveReaderResource`, és szükség szerint adja meg a paramétereket.

    ```azurepowershell-interactive
    Create-ImmersiveReaderResource
      -SubscriptionName '<SUBSCRIPTION_NAME>' `
      -ResourceName '<RESOURCE_NAME>' `
      -ResourceSubdomain '<RESOURCE_SUBDOMAIN>' `
      -ResourceSKU '<RESOURCE_SKU>' `
      -ResourceLocation '<RESOURCE_LOCATION>' `
      -ResourceGroupName '<RESOURCE_GROUP_NAME>' `
      -ResourceGroupLocation '<RESOURCE_GROUP_LOCATION>' `
      -AADAppDisplayName '<AAD_APP_DISPLAY_NAME>' `
      -AADAppIdentifierUri '<AAD_APP_IDENTIFIER_URI>' `
      -AADAppClientSecret '<AAD_APP_CLIENT_SECRET>'
    ```

    | Paraméter | Megjegyzések |
    | --- | --- |
    | SubscriptionName |Annak az Azure-előfizetésnek a neve, amelyet a magától elolvasó erőforráshoz kíván használni. Erőforrás létrehozásához előfizetés szükséges. |
    | ResourceName nevű erőforrásáról |  Alfanumerikusnak kell lennie, és tartalmazhat "-" karaktert, feltéve, hogy a "-" nem az első vagy az utolsó karakter. A hossz nem lehet hosszabb 63 karakternél.|
    | ResourceSubdomain |Egy egyéni altartományra van szükség a magától elolvasó erőforráshoz. Az SDK ezt az altartományt használja, amikor meghívja az olvasót, hogy elindítsa a lebilincselő olvasó szolgáltatást. Az altartománynak globálisan egyedinek kell lennie. Az altartománynak alfanumerikusnak kell lennie, és tartalmazhat "-" karaktert, feltéve, hogy a "-" nem az első vagy az utolsó karakter. A hossz nem lehet hosszabb 63 karakternél. Ez a paraméter nem kötelező, ha az erőforrás már létezik. |
    | ResourceSKU |Beállítások: `S0`. Az egyes rendelkezésre álló SKU-ra vonatkozó további információkért látogasson el [Cognitive Services díjszabási oldalára](https://azure.microsoft.com/pricing/details/cognitive-services/immersive-reader/) . Ez a paraméter nem kötelező, ha az erőforrás már létezik. |
    | ResourceLocation |Beállítások: `eastus`, `eastus2` `southcentralus` `westus`,,, `westus2`, `australiaeast`, `southeastasia`, `centralindia`, `japaneast`, `northeurope`, `uksouth`, `westeurope`. Ez a paraméter nem kötelező, ha az erőforrás már létezik. |
    | ResourceGroupName |Erőforrások jönnek létre az előfizetésekben található erőforráscsoportok között. Adja meg egy meglévő erőforráscsoport nevét. Ha az erőforráscsoport még nem létezik, a rendszer létrehoz egy új nevet. |
    | ResourceGroupLocation |Ha az erőforráscsoport nem létezik, meg kell adnia egy helyet, amelyben létre kívánja hozni a csoportot. A helyszínek listájának megkereséséhez futtassa `az account list-locations`a parancsot. Használja a visszaadott eredmény *Name* (szóközök nélkül) tulajdonságát. Ez a paraméter nem kötelező, ha az erőforráscsoport már létezik. |
    | AADAppDisplayName |A Azure Active Directory alkalmazás megjelenített neve. Ha egy meglévő Azure AD-alkalmazás nem található, a rendszer létrehoz egy új nevet. Ez a paraméter nem kötelező, ha az Azure AD-alkalmazás már létezik. |
    | AADAppIdentifierUri |Az Azure AD-alkalmazás URI-ja. Ha egy meglévő Azure AD-alkalmazás nem található, a rendszer létrehoz egy újat ezzel az URI-val. Például: `https://immersivereaderaad-mycompany`. |
    | AADAppClientSecret |Az Ön által létrehozott jelszó, amelyet később a rendszer a token beszerzése során fog használni, hogy elindítsa az olvasót. A jelszónak legalább 16 karakterből kell állnia, és legalább 1 speciális karaktert kell tartalmaznia, és legalább 1 numerikus karaktert kell tartalmaznia. |

1. Másolja a JSON-kimenetet egy szövegfájlba későbbi használatra. A kimenetnek az alábbihoz hasonlóan kell kinéznie.

    ```json
    {
      "TenantId": "...",
      "ClientId": "...",
      "ClientSecret": "...",
      "Subdomain": "..."
    }
    ```

## <a name="next-steps"></a>További lépések

* Tekintse meg a [Node. js](./quickstart-nodejs.md) rövid útmutatóját, amelyből megtudhatja, hogy mit tehet a magával az olvasó SDK-val a Node. js használatával
* Tekintse meg a [Python-oktatóanyagot](./tutorial-python.md) , amelyből megtudhatja, hogy mit tehet a részletes olvasó SDK-val a Python használatával
* Tekintse meg a [Swift-oktatóanyagot](./tutorial-ios-picture-immersive-reader.md) , amelyből megtudhatja, mit tehet a gyors
* Ismerkedjen meg a [magára az olvasói SDK](https://github.com/microsoft/immersive-reader-sdk) -val és az [olvasói SDK-referenciával](./reference.md)




