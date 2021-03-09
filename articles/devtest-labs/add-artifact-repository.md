---
title: Összetevő hozzáadása a laborhoz Azure DevTest Labsban | Microsoft Docs
description: Megtudhatja, hogyan adhatja meg a laborhoz tartozó saját Azure DevTest Labs, hogy az eszközök ne legyenek elérhetők a nyilvános tárolóban.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: da4e345b18a46226853d71bbf66af0487f1a761f
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102502195"
---
# <a name="add-an-artifact-repository-to-your-lab-in-devtest-labs"></a>A DevTest Labs szolgáltatásban lévő tároló hozzáadása a laborhoz
A DevTest Labs lehetővé teszi, hogy a virtuális gép létrehozásakor vagy a virtuális gép létrehozása után adja meg a virtuális géphez hozzáadandó összetevőt. Ez az összetevő lehet egy eszköz vagy egy alkalmazás, amelyet telepíteni kíván a virtuális gépre. Az összetevők egy GitHub vagy Azure DevOps git-tárházból betöltött JSON-fájlban vannak meghatározva.

A DevTest Labs által karbantartott nyilvános összetevő- [tárház](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts)számos gyakori eszközt biztosít a Windows és a Linux rendszerhez is. A rendszer automatikusan hozzáadja az adattárra mutató hivatkozást a laborhoz. Létrehozhat egy saját összetevő-tárházat olyan konkrét eszközökkel, amelyek nem érhetők el a nyilvános összetevő-tárházban. Az egyéni összetevők létrehozásával kapcsolatos további információkért lásd: [egyéni összetevők létrehozása](devtest-lab-artifact-author.md).

Ez a cikk azt ismerteti, hogyan adhat hozzá egyéni összetevő-tárházat Azure Portal, Azure Resource Management-sablonok és Azure PowerShell használatával. A PowerShell-vagy CLI-parancsfájlok írásával automatizálhatja az összetevők tárházának laborba való hozzáadását.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek
Ahhoz, hogy egy tárházat vegyen fel a laborba, először szerezze be a legfontosabb információkat a tárházból. A következő szakaszok azt ismertetik, hogyan kérhető le a **githubon** vagy az **Azure DevOps** tárolt adattárakhoz szükséges információk.

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>A GitHub-adattár klónozási URL-címének és a személyes hozzáférési jogkivonat beszerzése

1. Nyissa meg a GitHub-tárház kezdőlapját, amely tartalmazza az összetevő vagy a Resource Manager-sablon definícióit.
2. Válassza a **Clone or download** (Klónozás vagy letöltés) lehetőséget.
3. Az URL-cím vágólapra másolásához válassza a **https-klón URL-címe** gombot. Mentse az URL-címet későbbi használatra.
4. A GitHub jobb felső sarkában válassza ki a profil rendszerképét, majd válassza a **Beállítások** lehetőséget.
5. A bal oldali **személyes beállítások** menüben válassza a **fejlesztői beállítások** elemet.
6. A bal oldali menüben válassza a **személyes hozzáférési tokenek** lehetőséget.
7. Válassza az **új jogkivonat előállítása** lehetőséget.
8. Az **új személyes hozzáférési jogkivonat** lapon a **jogkivonat leírása** területen adjon meg egy leírást. Fogadja el az alapértelmezett elemeket a **hatókörök kiválasztása** területen, majd válassza a **jogkivonat előállítása** lehetőséget.
9. Mentse a generált tokent. Később a tokent használja.
10. A GitHub bezárásához.   

### <a name="get-the-azure-repos-clone-url-and-personal-access-token"></a>Az Azure Repos Clone URL-cím és a személyes hozzáférési jogkivonat beszerzése
1. Nyissa meg a csapat gyűjteményének kezdőlapját (például: `https://contoso-web-team.visualstudio.com` ), majd válassza ki a projektet.
2. A projekt kezdőlapján válassza a **kód** lehetőséget.
3. A klónozott URL-cím megtekintéséhez a projekt **kódja** lapon válassza a **klónozás** lehetőséget.
4. Mentse az URL-címet. Később az URL-címet használja.
5. Személyes hozzáférési jogkivonat létrehozásához a felhasználói fiók legördülő menüjében válassza a **saját profil** lehetőséget.
6. A profil adatai lapon válassza a **Biztonság** elemet.
7. A **biztonsági > személyes hozzáférési tokenek** lapon válassza az **+ új jogkivonat** lehetőséget.
8. Az **új személyes hozzáférési jogkivonat létrehozása** oldalon:
   1. Adja meg a jogkivonat **nevét** .
   2. A **szervezet** listában válassza az **összes elérhető szervezet** lehetőséget.
   3. A **lejárat (UTC)** listában válassza a **90 nap** lehetőséget, vagy egy egyéni meghatározott lejárati időszakot.
   4. Válassza ki a hatókörök **teljes hozzáférési** beállítását.
   5. Válassza a **Létrehozás** lehetőséget.
9. Az új jogkivonat megjelenik a **személyes hozzáférési tokenek** listájában. Válassza a **token másolása** lehetőséget, majd mentse a jogkivonat értékét későbbi használatra.
10. Folytassa a labor összekapcsolásával a tárház szakaszával.

## <a name="use-azure-portal"></a>Az Azure Portal használata
Ez a szakasz azokat a lépéseket ismerteti, amelyekkel egy összetevő-tárházat adhat hozzá egy laborhoz a Azure Portal.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza a **További szolgáltatások** lehetőséget, majd válassza a **DevTest Labs** lehetőséget a szolgáltatások listájából.
3. A Labs listából válassza ki a labort.
4. Válassza a **konfiguráció és szabályzatok** lehetőséget a bal oldali menüben.
5. A bal oldali menüben válassza a **külső erőforrások** szakaszban található **adattárak** elemet.
6. Válassza a **+ Hozzáadás** lehetőséget az eszköztáron.

    ![Az adattár hozzáadása gomb](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. A **Tárházak** lapon a következő információkat kell megadni:
   1. **Név**. Adja meg az adattár nevét.
   2. **Git Clone URL-cím**. Adja meg a korábban a GitHub vagy az Azure DevOps Services szolgáltatásból másolt git HTTPS Clone URL-címet.
   3. **Ág**. A definíciók beszerzéséhez adja meg a ágat.
   4. **Személyes hozzáférési jogkivonat**. Adja meg azt a személyes hozzáférési jogkivonatot, amelyet korábban a GitHub vagy az Azure DevOps Services szolgáltatásból kapott.
   5. **Mappa elérési útjai** Adja meg legalább egy olyan mappa elérési útját, amely az összetevő vagy a Resource Manager-sablon definícióit tartalmazó klónozási URL-címhez képest van. Alkönyvtár megadásakor ügyeljen arra, hogy a mappa elérési útja tartalmazza a továbbítás perjelét.

        ![Adattárakhoz tartozó területek](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. Kattintson a **Mentés** gombra.

## <a name="use-azure-resource-manager-template"></a>Azure Resource Manager sablon használata
Az Azure erőforrás-kezelési (Azure Resource Manager) Sablonok JSON-fájlok, amelyek az Azure-ban létrehozni kívánt erőforrásokat írják le. További információ ezekről a sablonokról: [Azure Resource Manager-sablonok készítése](../azure-resource-manager/templates/template-syntax.md).

Ez a szakasz azt ismerteti, hogyan adhat hozzá egy összetevőt a laborhoz egy Azure Resource Manager sablon használatával.  A sablon létrehozza a labort, ha még nem létezik.

### <a name="template"></a>Sablon
A cikkben használt minta sablon a következő információkat gyűjti össze paraméterek használatával:. A paraméterek többsége intelligens alapértékekkel rendelkezik, de néhány értéket meg kell adni. Meg kell adnia a labor nevét, az összetevő-tárház URI azonosítóját, valamint a tárház biztonsági jogkivonatát.

- Labor neve
- Az DevTest Labs felhasználói felületének (UI) megjelenítendő neve az összetevő-tárházhoz. Az alapértelmezett érték: `Team Repository` .
- A tárház URI-ja (példa: `https://github.com/<myteam>/<nameofrepo>.git` vagy `"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"` .
- A tárházban található ágakat tartalmazó ág. Az alapértelmezett érték: `master` .
- Az összetevőket tartalmazó mappa neve. Az alapértelmezett érték: `/Artifacts` .
- A tárház típusa. Az engedélyezett értékek a következők: `VsoGit` vagy `GitHub` .
- A tárház hozzáférési jogkivonata.

    ```json
    {

        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "labName": {
                "type": "string"
            },
            "artifactRepositoryDisplayName": {
                "type": "string",
                "defaultValue": "Team Repository"
            },
            "artifactRepoUri": {
                "type": "string"
            },
            "artifactRepoBranch": {
                "type": "string",
                "defaultValue": "master"
            },
            "artifactRepoFolder": {
                "type": "string",
                "defaultValue": "/Artifacts"
            },
            "artifactRepoType": {
                "type": "string",
                "allowedValues": ["VsoGit", "GitHub"]
            },
            "artifactRepoSecurityToken": {
                "type": "securestring"
            }
        },
        "variables": {
            "artifactRepositoryName": "[concat('Repo-', uniqueString(subscription().subscriptionId))]"
        },
        "resources": [{
                "apiVersion": "2016-05-15",
                "type": "Microsoft.DevTestLab/labs",
                "name": "[parameters('labName')]",
                "location": "[resourceGroup().location]",
                "resources": [
                    {
                        "apiVersion": "2016-05-15",
                        "name": "[variables('artifactRepositoryName')]",
                        "type": "artifactSources",
                        "dependsOn": [
                            "[resourceId('Microsoft.DevTestLab/labs', parameters('labName'))]"
                        ],
                        "properties": {
                            "uri": "[parameters('artifactRepoUri')]",
                            "folderPath": "[parameters('artifactRepoFolder')]",
                            "branchRef": "[parameters('artifactRepoBranch')]",
                            "displayName": "[parameters('artifactRepositoryDisplayName')]",
                            "securityToken": "[parameters('artifactRepoSecurityToken')]",
                            "sourceType": "[parameters('artifactRepoType')]",
                            "status": "Enabled"
                        }
                    }
                ]
            }
        ]
    }
    ```


### <a name="deploy-the-template"></a>A sablon üzembe helyezése
A sablon üzembe helyezése többféleképpen is elvégezhető az Azure-ban, és ha az erőforrás már nem létezik, vagy nem frissült, akkor ha létezik. Részletekért tekintse meg a következő cikkeket:

- [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](../azure-resource-manager/templates/deploy-powershell.md)
- [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure parancssori felületével](../azure-resource-manager/templates/deploy-cli.md)
- [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure Portallal](../azure-resource-manager/templates/deploy-portal.md)
- [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure Manager REST API-val](../azure-resource-manager/templates/deploy-rest.md)

Nézzük meg, hogyan helyezheti üzembe a sablont a PowerShellben. A sablon üzembe helyezéséhez használt parancsmagok környezetfüggők, így az aktuális bérlő és a jelenlegi előfizetés használatos. A [set-AzContext](/powershell/module/az.accounts/set-azcontext) használata a sablon telepítése előtt, ha szükséges, a környezet módosításához.

Először hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)használatával. Ha a használni kívánt erőforráscsoport már létezik, ugorja át ezt a lépést.

```powershell
New-AzResourceGroup -Name MyLabResourceGroup1 -Location westus
```

Ezután hozzon létre egy központi telepítést az erőforráscsoporthoz a [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment)használatával. Ez a parancsmag az Azure-beli erőforrás-módosításokat alkalmazza. Az adott erőforráscsoporthoz több erőforrás-telepítés is elvégezhető. Ha többször is telepíti ugyanazt az erőforráscsoportot, győződjön meg arról, hogy az egyes központi telepítések neve egyedi.

```powershell
New-AzResourceGroupDeployment `
    -Name MyLabResourceGroup-Deployment1 `
    -ResourceGroupName MyLabResourceGroup1 `
    -TemplateFile azuredeploy.json `
    -TemplateParameterFile azuredeploy.parameters.json
```

A New-AzResourceGroupDeployment sikeres futtatása után a parancs a fontos információkat, például a kiépítési állapotot (sikeres) és a sablon kimeneteit is megjeleníti.

## <a name="use-azure-powershell"></a>Azure PowerShell használatával
Ez a szakasz egy PowerShell-parancsfájlt tartalmaz, amely egy összetevő-adattár tesztkörnyezetben való hozzáadására használható. Ha még nem rendelkezik Azure PowerShellval, tekintse meg a telepítési [és konfigurálási Azure PowerShell](/powershell/azure/) részletes útmutatását.

### <a name="full-script"></a>Teljes szkript
Itt látható a teljes szkript, beleértve néhány részletes üzenetet és megjegyzést:

**New-DevTestLabArtifactRepository.ps1**:

```powershell

<#

.SYNOPSIS
This script creates a new custom repository and adds it to an existing DevTest Lab.

.PARAMETER LabName
The name of the lab.

.PARAMETER LabResourceGroupName
The name of the resource group that contains the lab.

.PARAMETER ArtifactRepositoryName
Name for the new artifact repository.
Script creates a random name for the repository if it is not specified.

.PARAMETER ArtifactRepositoryDisplayName
Display name for the artifact repository.
This is the name that shows up in the Azure portal (https://portal.azure.com) when viewing all the artifact repositories for a lab.

.PARAMETER RepositoryUri
Uri to the repository.

.PARAMETER RepositoryBranch
Branch in which artifact files can be found. Defaults to 'master'.

.PARAMETER FolderPath
Folder under which artifacts can be found. Defaults to '/Artifacts'

.PARAMETER PersonalAccessToken
Security token for access to GitHub or VSOGit repository.
See https://azure.microsoft.com/documentation/articles/devtest-lab-add-artifact-repo/ for instructions to get personal access token

.PARAMETER SourceType
Whether artifact is VSOGit or GitHub repository.

.EXAMPLE
Set-AzContext -SubscriptionId 11111111-1111-1111-1111-111111111111
.\New-DevTestLabArtifactRepository.ps1 -LabName "mydevtestlab" -LabResourceGroupName "mydtlrg" -ArtifactRepositoryName "MyTeam Repository" -RepositoryUri "https://github.com/<myteam>/<nameofrepo>.git" -PersonalAccessToken "1111...." -SourceType "GitHub"

.NOTES
Script uses the current Az context. To set the context, use the Set-AzContext cmdlet

#>


[CmdletBinding()]
Param(

    [Parameter(Mandatory=$true)]
    $LabName,

    [Parameter(Mandatory=$true)]
    $LabResourceGroupName,
    $ArtifactRepositoryName,
    $ArtifactRepositoryDisplayName  = 'Team Artifact Repository',

    [Parameter(Mandatory=$true)]
    $RepositoryUri,
    $RepositoryBranch = 'master',
    $FolderPath = '/Artifacts',

    [Parameter(Mandatory=$true)]
    $PersonalAccessToken ,

    [Parameter(Mandatory=$true)]
    [ValidateSet('VsoGit', 'GitHub')]
    $SourceType
)


#Set artifact repository internal name,
# if not set by user.

if ($ArtifactRepositoryName -eq $null){
    $ArtifactRepositoryName = "PrivateRepo" + (Get-Random -Maximum 999)
}

# Sign in to Azure
Connect-AzAccount


#Get Lab Resource
$LabResource = Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceName $LabName -ResourceGroupName $LabResourceGroupName

Write-Verbose "Lab Name: $($LabResource.Name)"
Write-Verbose "Lab Resource Group Name: $($LabResource.ResourceGroupName)"
Write-Verbose "Lab Resource Location: $($LabResource.Location)"

Write-Verbose "Artifact Repository Internal Name: $ArtifactRepositoryName"

#Prepare properties object for call to New-AzResource
$propertiesObject = @{
    uri = $RepositoryUri;
    folderPath = $FolderPath;
    branchRef = $RepositoryBranch;
    displayName = $ArtifactRepositoryDisplayName;
    securityToken = $PersonalAccessToken;
    sourceType = $SourceType;
    status = 'Enabled'
}

Write-Verbose @"Properties to be passed to New-AzResource:$($propertiesObject | Out-String)"

#Resource will be added to current subscription.
$resourcetype = 'Microsoft.DevTestLab/labs/artifactSources'
$resourceName = $LabName + '/' + $ArtifactRepositoryName
Write-Verbose "Az ResourceType: $resourcetype"
Write-Verbose "Az ResourceName: $resourceName"

Write-Verbose "Creating artifact repository '$ArtifactRepositoryDisplayName'..."
$result = New-AzResource -Location $LabResource.Location -ResourceGroupName $LabResource.ResourceGroupName -properties $propertiesObject -ResourceType $resourcetype -ResourceName $resourceName -ApiVersion 2016-05-15 -Force


#Alternate implementation:
# Use resourceId rather than resourcetype and resourcename parameters.
# Using resourceId allows you to specify the $SubscriptionId rather than using the
# subscription id of Get-AzContext.
#$resourceId = "/subscriptions/$SubscriptionId/resourceGroups/$($LabResource.ResourceGroupName)/providers/Microsoft.DevTestLab/labs/$LabName/artifactSources/$ArtifactRepositoryName"
#$result = New-AzResource -properties $propertiesObject -ResourceId $resourceId -ApiVersion 2016-05-15 -Force


# Check the result
if ($result.Properties.ProvisioningState -eq "Succeeded") {
    Write-Verbose ("Successfully added artifact repository source '$ArtifactRepositoryDisplayName'")
}
else {
    Write-Error ("Error adding artifact repository source '$ArtifactRepositoryDisplayName'")
}

#Return the newly created resource so it may be used in subsequent scripts
return $result
```

### <a name="run-the-powershell-script"></a>A PowerShell-parancsprogram futtatása
Az alábbi példa bemutatja, hogyan futtathatja a szkriptet:

```powershell
Set-AzContext -SubscriptionId <Your Azure subscription ID>

.\New-DevTestLabArtifactRepository.ps1 -LabName "mydevtestlab" -LabResourceGroupName "mydtlrg" -ArtifactRepositoryName "MyTeam Repository" -RepositoryUri "https://github.com/<myteam>/<nameofrepo>.git" -PersonalAccessToken "1111...." -SourceType "GitHub"
```


### <a name="parameters"></a>Paraméterek
A cikkben szereplő PowerShell-parancsfájl a következő paramétereket veszi figyelembe:

| Paraméter | Leírás |
| --------- | ----------- |
| LabName | A labor neve. |
| ArtifactRepositoryName | Az új összetevő-tárház neve. Ha nincs megadva, a szkript létrehoz egy véletlenszerű nevet a tárházhoz. |
| ArtifactRepositoryDisplayName | Az összetevő-tárház megjelenítendő neve. Ez a név jelenik meg a Azure Portalban (a https://portal.azure.com) labor összes összetevő-tárházának megtekintésekor. |
| RepositoryUri | A tárház URI-ja. Példák: `https://github.com/<myteam>/<nameofrepo>.git` vagy `"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"` .|
| RepositoryBranch | Az összetevők, amelyekben megtalálható az összetevők fájljai. Az alapértelmezett érték a "Master". |
| FolderPath | A mappában található összetevők. Alapértelmezett érték: "/artifacts" |
| PersonalAccessToken | Biztonsági jogkivonat a GitHub vagy a VSOGit adattár eléréséhez. A személyes hozzáférési token beszerzésére vonatkozó utasításokért tekintse meg az előfeltételek című szakaszt. |
| SourceType | Azt jelzi, hogy az összetevő VSOGit vagy GitHub-tárház. |

A tárháznak szüksége van egy belső névre az azonosításhoz, amely eltér a Azure Portalban látható megjelenítendő névvel. A belső név nem jelenik meg a Azure Portal használatával, de az Azure REST API-k vagy Azure PowerShell használatakor jelenik meg. A szkript megadja a nevet, ha az egyiket nem a parancsfájl felhasználója adta meg.

```powershell
#Set artifact repository name, if not set by user
if ($ArtifactRepositoryName -eq $null){
    $ArtifactRepositoryName = "PrivateRepo" + (Get-Random -Maximum 999)
}
```

### <a name="powershell-commands-used-in-the-script"></a>A parancsfájlban használt PowerShell-parancsok

| PowerShell-parancs | Jegyzetek |
| ------------------ | ----- |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Ezzel a paranccsal lekérheti a labor részleteit, például annak helyét. |
| [New-AzResource](/powershell/module/az.resources/new-azresource) | Az összetevő-Tárházak hozzáadására nincs specifikus parancs. Az általános [New-AzResource](/powershell/module/az.resources/new-azresource) parancsmag végzi a feladatot. Ehhez a parancsmaghoz vagy a **ResourceId** , vagy a **resourcename** és a **resourcetype** pár szükséges a létrehozandó erőforrás típusának megismeréséhez. Ez a minta parancsfájl az erőforrás neve és az erőforrástípus pár értéket használja. <br/><br/>Figyelje meg, hogy az összetevő-tárház forrását ugyanazon a helyen hozza létre, és a laborban megegyező erőforráscsoporthoz tartozik.|

A parancsfájl új erőforrást hoz létre a jelenlegi előfizetéshez. A [Get-AzContext](/powershell/module/az.accounts/get-azcontext) használatával tekintheti meg ezeket az információkat. A [set-AzContext](/powershell/module/az.accounts/set-azcontext) használatával állítsa be az aktuális bérlőt és előfizetést.

Az erőforrás neve és az erőforrástípus adatainak felderítésére a legjobb módszer, ha a [Test Drive Azure REST API](https://azure.github.io/projects/apis/) -k webhelyét használja. Tekintse meg a [DevTest Labs – 2016-05-15](https://aka.ms/dtlrestapis) szolgáltatót, ahol megtekintheti a DevTest Labs-szolgáltató rendelkezésre álló REST API-jait. A parancsfájl a következő erőforrás-AZONOSÍTÓval rendelkező felhasználókat adja meg.

```powershell
"/subscriptions/$SubscriptionId/resourceGroups/$($LabResource.ResourceGroupName)/providers/Microsoft.DevTestLab/labs/$LabName/artifactSources/$ArtifactRepositoryName"
```

Az erőforrástípus minden, a "szolgáltatók" elem után szerepel az URI-ban, a kapcsos zárójelben felsorolt elemek kivételével. Az erőforrás neve a kapcsos zárójelben látható. Ha az erőforrás neveként egynél több elem várható, akkor az egyes elemeket perjelként válassza el.

```powershell
$resourcetype = 'Microsoft.DevTestLab/labs/artifactSources'
$resourceName = $LabName + '/' + $ArtifactRepositoryName
```


## <a name="next-steps"></a>Következő lépések
- [A laborhoz tartozó kötelező összetevők megadása Azure DevTest Labs](devtest-lab-mandatory-artifacts.md)
- [Egyéni összetevők létrehozása a DevTest Labs virtuális géphez](devtest-lab-artifact-author.md)
- [Az összetevők hibáinak diagnosztizálása a laborban](devtest-lab-troubleshoot-artifact-failure.md)
