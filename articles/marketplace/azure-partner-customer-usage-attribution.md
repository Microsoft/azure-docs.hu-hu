---
title: Azure Customer használati jóváírás
description: Tekintse át az ügyfelek használatának nyomon követését az Azure-alkalmazásokhoz a kereskedelmi piactéren és a partnerek által fejlesztett egyéb telepíthető IP-címen.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: cpercy737
ms.author: camper
ms.date: 03/22/2021
ms.custom: devx-track-terraform
ms.openlocfilehash: 53edd3ec9a8d30d0c25f994db4a8b6f0199c2169
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105558414"
---
# <a name="azure-customer-usage-attribution"></a>Azure Customer használati jóváírás

Az ügyfél-használati jóváírás az Azure-erőforrásokból származó, az Ön partnereként való üzembe helyezése során létrehozott ügyfél-előfizetésekben található használati adatokat társítja. Ezen társítások belső Microsoft-rendszerekben való kialakítása nagyobb láthatóságot biztosít a szoftvert futtató Azure-lábnyomon. A [kereskedelmi piactéren elérhető Azure-alkalmazások](#commercial-marketplace-azure-apps)esetében ez a nyomon követési funkció segít a Microsoft értékesítési csapatokkal való összehangolásban és a Microsoft-partneri programokhoz való kreditek megszerzésében.

A Customer használati jóváírás három üzembe helyezési lehetőséget támogat:

1. Azure Resource Manager-sablonok (az Azure-alkalmazások közös kitűzései, amelyek a kereskedelmi piactéren "megoldási sablonok" vagy "felügyelt alkalmazások" néven is ismertek): a partnerek Resource Manager-sablonokat hozhatnak létre az Azure-megoldások infrastruktúrájának és konfigurációjának meghatározásához. A Resource Manager-sablonok lehetővé teszik, hogy az ügyfelek konzisztens és ismételhető állapotban telepíthessék a megoldás erőforrásait.
1. Azure Resource Manager API-k: a partnerek hívhatják a Resource Manager API-kat egy Resource Manager-sablon üzembe helyezéséhez vagy az Azure-szolgáltatások közvetlen kiépítéséhez.
1. Terraform: a partnerek a Terraform használatával üzembe helyezhetnek egy Resource Manager-sablont, vagy közvetlenül üzembe helyezhetik az Azure-szolgáltatásokat.

A [jelen cikk későbbi részében](#other-use-cases)ismertetett, a kereskedelmi piactéren kívüli ügyfél-használati adatok másodlagos használati esetei.

>[!IMPORTANT]
>- Az ügyfél-használati jóváírás nem alkalmas az Azure-erőforrások üzembe helyezésére és kezelésére szolgáló rendszerintegrátorok, felügyelt szolgáltatók vagy eszközök működésének nyomon követésére.
>- Az ügyfél-használati jóváírás új központi telepítéseket tartalmaz, és nem támogatja a már telepített erőforrások nyomon követését.
>- Nem minden Azure-szolgáltatás kompatibilis az ügyfél-használati jóváírással. Az Azure Kubernetes Services (ak), a VM Scale Sets és a Azure Batch olyan ismert problémákkal rendelkezik, amelyek a használat alatt állnak.

## <a name="commercial-marketplace-azure-apps"></a>Kereskedelmi piactér – Azure-alkalmazások

A kereskedelmi piactéren közzétett Azure-alkalmazások Azure-használatának nyomon követése nagyrészt automatikusan történik. Ha egy Resource Manager-sablont tölt fel a [Marketplace Azure-alkalmazás csomagjának technikai konfigurációjának](./create-new-azure-apps-offer-solution.md#define-the-technical-configuration)részeként, a Partner Center Azure Resource Manager által olvasható követési azonosítót ad hozzá.

Ha Azure Resource Manager API-kat használ, hozzá kell adnia a követési AZONOSÍTÓját az [alábbi utasítások](#use-resource-manager-apis) alapján, hogy átadja azt a Azure Resource Managernak, mivel a kód üzembe helyezi az erőforrásokat. Ez az azonosító a csomag technikai konfiguráció oldalán, a partner Centerben látható. 

> [!NOTE]
> A meglévő Azure-alkalmazások esetében egy egyszeri áttelepítés 2021 márciusában kezdődött, hogy frissítse a követési azonosítókat az egyes csomagokra vonatkozó technikai konfigurációban. Ezen ajánlatok korábbi üzembe helyezésének használata a Microsoft rendszereiben is nyomon követhető.
>
>Az ajánlatok frissítése során már nem kell felvennie a **Microsoft. Resources/Deployments** típusú erőforrás típusát a fő sablonfájlba.

## <a name="other-use-cases"></a>Egyéb használati esetek 

Az ügyfél-használati jóváírás használatával nyomon követheti a kereskedelmi piactéren nem elérhető megoldások Azure-használatát. Ezek a megoldások általában a gyors üzembe helyezési tárházban, a privát GitHub-adattárakban, illetve a tartós IP-címet (például telepíthető és méretezhető alkalmazás) létrehozó 1:1-ügyfelektől származnak.

Több manuális lépés szükséges:

1. Hozzon létre egy vagy több, a nyomkövetési azonosítóként használandó GUID azonosítót.
1. Regisztrálja ezeket a GUID azonosítókat a partner Centerben.
1. Adja hozzá a regisztrált GUID azonosítókat az Azure-alkalmazáshoz és/vagy a felhasználói ügynök sztringekhez.

### <a name="create-guids"></a>GUID-azonosítók létrehozása

A partner Center által a kereskedelmi piactéren az Azure-alkalmazások nevében létrehozott követési azonosítókkal ellentétben a CUA egyéb alkalmazásaihoz létre kell hoznia egy GUID azonosítót, amelyet nyomon követési azonosítóként kíván használni. A GUID azonosító egy egyedi hivatkozási azonosító, amelynek 32 hexadecimális számjegye van. A nyomkövetési GUID azonosítók létrehozásához GUID-generátort kell használnia, például a PowerShellen keresztül:

```powershell
[guid]::NewGuid()
```

Minden termékhez és terjesztési csatornához egyedi GUID azonosítót kell létrehoznia. Ha nem szeretné, hogy a jelentés szét legyen osztva, egyetlen GUID-azonosítót használhat a termék több terjesztési csatornájának. A jelentéskészítés Microsoft Partner Network ID és GUID azonosítóval történik.

### <a name="register-guids"></a>GUID-azonosítók regisztrálása

A GUID azonosítókat regisztrálni kell a partner Centerben, hogy partnereként hozzá lehessen rendelni őket:

1. Jelentkezzen be a [partner központba](https://partner.microsoft.com/dashboard).

1. Regisztráljon [kereskedelmi Piactéri közzétevőként](https://aka.ms/JoinMarketplace).

1. Válassza a **Beállítások** (fogaskerék ikon) lehetőséget a jobb felső sarokban, majd a **Fiókbeállítások** menüpontot.

1. Válassza a **szervezeti profilok**  >  **azonosítóinak**  >  **hozzáadása követési GUID** elemet.

1. A **GUID** mezőben adja meg a nyomkövetési GUID azonosítóját. Az előtag nélkül adja meg a GUID azonosítót `pid-` . A **Leírás** mezőben adja meg a megoldás nevét vagy leírását.

1. Egynél több GUID-azonosító regisztrálásához válassza ismét a **nyomkövetési GUID hozzáadása** elemet. Az oldalon további mezők jelennek meg.

1. Kattintson a **Mentés** gombra.

### <a name="add-a-guid-to-a-resource-manager-template"></a>GUID hozzáadása Resource Manager-sablonhoz

A regisztrált GUID-azonosítók Resource Manager-sablonokhoz való hozzáadásához végezze el a fő sablonfájl egyetlen módosítását:

1. Nyissa meg a Resource Manager-sablont.

1. Vegyen fel egy [Microsoft. Resources/Deployments](/azure/templates/microsoft.resources/deployments) nevű új erőforrást a fő sablonfájlba. Az erőforrásnak csak a fájl **mainTemplate.js** vagy **azuredeploy.js** kell lennie, nem pedig egyetlen beágyazott vagy csatolt sablonban sem.

1. Adja meg a GUID értéket az `pid-` előtag neve után az erőforrás neveként. Ha például a GUID a következő: eb7927c8-dd66-43e1-b0cf-c346a422063, az erőforrás neve **PID-eb7927c8-dd66-43e1-b0cf-c346a422063** lesz. Példa:
 
```json
{ // add this resource to the resources section in the mainTemplate.json
    "apiVersion": "2020-06-01",
    "name": "pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX", // use your generated GUID here
    "type": "Microsoft.Resources/deployments",
    "properties": {
        "mode": "Incremental",
        "template": {
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "resources": []
        }
    }
} // remove all comments from the file when complete
```
4. Keresse meg a hibákat a sablonban.

1. Tegye közzé újra a sablont a megfelelő adattárakban.

1. [Ellenőrizze a GUID sikerességét a sablon központi telepítésében](#verify-deployments-tracked-with-a-guid).

> [!TIP]
> A Resource Manager-sablonok létrehozásával és közzétételével kapcsolatos további információkért lásd: [az első Resource Manager-sablon létrehozása és üzembe helyezése](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).

### <a name="verify-deployments-tracked-with-a-guid"></a>A GUID azonosítóval követett központi telepítések ellenőrzése

A sablon módosítása és a tesztek futtatása után futtassa a következő PowerShell-parancsfájlt a telepített és címkézett erőforrások lekéréséhez.

A parancsfájl segítségével ellenőrizheti, hogy a GUID sikeresen hozzá van-e adva a Resource Manager-sablonhoz. A parancsfájl nem vonatkozik a Resource Manager API-ra vagy a Terraform üzemelő példányokra.

Jelentkezzen be az Azure-ba. Válassza ki azt az előfizetést, amelyet a parancsfájl futtatása előtt szeretne ellenőrizni. Futtassa a parancsfájlt az üzemelő példány előfizetési környezetében.

A **GUID** (az alábbi "deploymentName") és a **resourceGroupName** neve kötelező paraméterek.

[Az eredeti szkript](https://gist.github.com/bmoore-msft/ae6b8226311014d6e7177c5127c7eba1) a githubon szerezhető be.

```powershell
Param(
    [string][Parameter(Mandatory=$true)]$deploymentName, # the full name of the deployment, e.g. pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
    [string][Parameter(Mandatory=$true)]$resourceGroupName
)

# Get the correlationId of the named deployment
$correlationId = (Get-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -Name "$deploymentName").correlationId

# Find all deployments with that correlationId
$deployments = Get-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName | Where-Object{$_.correlationId -eq $correlationId}

# Find all deploymentOperations in all deployments with that correlationId as PowerShell doesn't surface outputResources on the deployment or correlationId on the deploymentOperation

foreach ($deployment in $deployments){
    # Get deploymentOperations by deploymentName
    # then the resourceIds for each resource
    ($deployment | Get-AzResourceGroupDeploymentOperation | Where-Object{$_.targetResource -notlike "*Microsoft.Resources/deployments*"}).TargetResource
}
```

### <a name="notify-your-customers"></a>Ügyfelek értesítése

A partnereknek tájékoztatniuk kell az ügyfelet az ügyfél-használati jóváírást használó üzembe helyezésekről. A Microsoft az ezekhez az üzembe helyezésekhez társított Azure-használatot jelenti a partnerhez. Az alábbi példák olyan tartalmakat tartalmaznak, amelyekkel értesítheti ügyfeleit ezekről az üzemelő példányokról. A példákban cserélje le a \<PARTNER> nevet a vállalat nevére. A partnereknek biztosítaniuk kell, hogy az értesítés igazodjon az adatvédelemhez és a gyűjtési szabályzatokhoz, beleértve azokat a lehetőségeket is, amelyekkel kizárható a nyomon követésből.

#### <a name="notification-for-resource-manager-template-deployments"></a>Értesítés Resource Manager-sablonok üzembe helyezéséhez

A sablon központi telepítésekor a Microsoft azonosítani tudja a \<PARTNER> telepített Azure-erőforrásokkal rendelkező szoftverek telepítését. A Microsoft ezeket az erőforrásokat a szoftver támogatásához használhatja. A Microsoft ezeket az adatokat gyűjti, hogy a lehető legjobb élményt nyújtsa termékeivel, és üzleti tevékenységét. Az adatokat a Microsoft adatvédelmi szabályzatai gyűjtik össze és szabályozzák, amelyek a következő címen találhatók: [https://www.microsoft.com/trustcenter](https://www.microsoft.com/trustcenter) .

#### <a name="notification-for-sdk-or-api-deployments"></a>Értesítés SDK-vagy API-telepítésekhez

A szoftverek központi telepítésekor a \<PARTNER> Microsoft azonosítani tudja a \<PARTNER> telepített Azure-erőforrásokkal rendelkező szoftverek telepítését. A Microsoft ezeket az erőforrásokat a szoftver támogatásához használhatja. A Microsoft ezeket az adatokat gyűjti, hogy a lehető legjobb élményt nyújtsa termékeivel, és üzleti tevékenységét. Az adatokat a Microsoft adatvédelmi szabályzatai gyűjtik össze és szabályozzák, amelyek a következő címen találhatók: [https://www.microsoft.com/trustcenter](https://www.microsoft.com/trustcenter) .

## <a name="use-resource-manager-apis"></a>Resource Manager API-k használata

Bizonyos esetekben az Azure-szolgáltatások üzembe helyezéséhez közvetlenül a Resource Manager REST API-kkal is indíthat hívásokat. Az Azure a hívások engedélyezéséhez [több SDK](../index.yml?pivot=sdkstools) -t is támogat. Az SDK-k egyikét használhatja, vagy meghívhatja a REST API-kat közvetlenül az erőforrások üzembe helyezéséhez.

Ha engedélyezni szeretné az ügyfelek használati feltételeit, az API-hívások tervezésekor vegye fel a követési azonosítót a kérelem felhasználói ügynök fejlécében. Formázza a karakterláncot az `pid-` előtaggal. Angol nyelvű Példák:

```xml
//Commercial Marketplace Azure app
pid-contoso-myoffer-partnercenter //copy the tracking ID exactly as it appears in Partner Center

//Other use cases
pid-b6addd8f-5ff4-4fc0-a2b5-0ec7861106c4 //enter your GUID after "pid-"
```
> [!IMPORTANT]
> Ha Resource Manager API-kat használ a kereskedelmi piactéren egy Azure-alkalmazással, használja a partner Centerben megadott követési azonosítót. Ne használjon GUID azonosítót.

A különböző SDK-k eltérően működnek együtt a Resource Manager API-kkal, és a kódban valamilyen eltérésre lesz szükség. Az alábbi példák a nem kereskedelmi piactér megközelítését mutatják be a GUID használatával, és számos népszerű Azure SDK-t fednek le.

#### <a name="example-python-sdk"></a>Példa: Python SDK

A Python esetében használja a **config** attribútumot. Az attribútumot csak UserAgent lehet hozzáadni. Példa:

```python
client = azure.mgmt.servicebus.ServiceBusManagementClient(**parameters)
client.config.add_user_agent("pid-b6addd8f-5ff4-4fc0-a2b5-0ec7861106c4")
```
> [!IMPORTANT]
> Adja hozzá az attribútumot az egyes ügyfelekhez. Nincs globális statikus konfiguráció. Az ügyfél-előállítót címkézheti az összes ügyfél nyomon követése érdekében. További információkért tekintse meg ezt a [Client Factory-mintát a githubon](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79).

#### <a name="example-net-sdk"></a>Példa: .NET SDK

A .NET esetében ügyeljen arra, hogy a felhasználói ügynököt állítsa be. A [Microsoft. Azure. Management. Fluent](/dotnet/api/microsoft.azure.management.fluent) függvénytár használatával állítsa be a felhasználói ügynököt a következő kóddal (például C#):

```csharp
var azure = Microsoft.Azure.Management.Fluent.Azure
    .Configure()
    // Add your pid in the user agent header
    .WithUserAgent("pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX", String.Empty) 
    .Authenticate(/* Credentials created via Microsoft.Azure.Management.ResourceManager.Fluent.SdkContext.AzureCredentialsFactory */)
    .WithSubscription("<subscription ID>");
```

#### <a name="example-azure-powershell"></a>Példa: Azure PowerShell

Ha Azure PowerShell használatával helyezi üzembe az erőforrásokat, a következő metódussal fűzze hozzá a GUID-azonosítót:

```powershell
[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")
```

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

#### <a name="example-azure-cli"></a>Példa: Azure CLI

Ha az Azure CLI-t használja a GUID-azonosító hozzáfűzéséhez, állítsa a **AZURE_HTTP_USER_AGENT** környezeti változót egy parancsfájl hatókörén belül. A változót globálisan is beállíthatja a rendszerhéj hatóköréhez:

```powershell
export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'
```

További információ: [Go nyelvhez készült Azure SDK](/azure/developer/go/).

## <a name="use-terraform"></a>Terraform használata

A Terraform támogatása az Azure-szolgáltató 1.21.0 kiadásán keresztül érhető el: [https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019](https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019) . Ez minden olyan partnerre vonatkozik, amely a Terraform-n keresztül telepíti a megoldását, valamint az Azure-szolgáltató által üzembe helyezett és mért összes erőforrást (1.21.0 vagy újabb verzió).

A Terraform készült Azure-szolgáltató hozzáadta a megoldáshoz használt nyomkövetési GUID megadásához [*partner_id*](https://www.terraform.io/docs/providers/azurerm/#partner_id) nevű új, választható mezőt. A mező értéke a *ARM_PARTNER_ID* környezeti változóból is származhat.

```
provider "azurerm" {
          subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          ……
          # new stuff for ISV attribution
          partner_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"}
```
*Partner_id* értékének beállítása egy regisztrált GUID azonosítóra. Ne előtagként adja meg a GUID azonosítót "PID-" értékkel, csak állítsa be a tényleges GUID azonosítóra.

> [!IMPORTANT]
> Ha a Terraform-t a kereskedelmi piactéren egy Azure-alkalmazással használja, használja a partner Centerben megadott teljes nyomkövetési azonosítót. Ne használjon GUID azonosítót.

## <a name="get-support"></a>Támogatás kérése

Ismerje meg a kereskedelmi piactér támogatási lehetőségeit a [partner Center kereskedelmi piactér programjának támogatásával](support.md).

### <a name="how-to-submit-a-technical-consultation-request"></a>Technikai tanácsadási kérelem beküldése

1. Látogasson el a [partner Technical Services](https://aka.ms/TechnicalJourney)webhelyre.
1. A technikai utazás megtekintéséhez válassza a **felhőalapú infrastruktúra és felügyelet** lehetőséget.
1. Válassza a **központi telepítési szolgáltatások**  >  **kérelem küldése** lehetőséget.
1. Jelentkezzen be a MSA (MPN-fiók) vagy a HRE (partner irányítópult-fiókkal).
1. Töltse ki/tekintse át a megnyíló űrlapon a kapcsolattartási adatokat. A konzultáció részleteit előre ki lehet tölteni, vagy lehet, hogy legördülő lehetőségek állnak rendelkezésre.
1. Adja meg a probléma címét és részletes leírását.
1. Válassza a **Küldés** lehetőséget.

Részletes útmutatást találhat a képernyőképek [használatáról a Technical Sales and Deployment Services használatával](https://aka.ms/TechConsultInstructions).

A Microsoft partner technikai tanácsadója felveszi Önnel a kapcsolatot, hogy meghívja az igényeinek megfelelő hatókört.

## <a name="report"></a>Jelentés
Az Azure-használat nyomon követése az ügyfél-használati jóváíráson keresztül nem érhető el az ISV-partnerek számára. A jelentéskészítő a kereskedelmi piactér programhoz való hozzáadása a partner Centerben, amely az ügyfél-használati jóváírásra vonatkozik, a 2021-as második felet célozza meg.

## <a name="faq"></a>GYIK

#### <a name="after-a-tracking-id-is-added-can-it-be-changed"></a>A nyomkövetési azonosító hozzáadása után módosítható?

A kereskedelmi piactéren az Azure-alkalmazások nyomkövetési azonosítóit a partner Center automatikusan kezeli. Az ügyfél azonban letöltheti a sablont, és módosíthatja vagy eltávolíthatja a követési azonosítót. A partnereknek proaktív módon kell leírniuk a követési azonosító szerepét az ügyfeleknek az Eltávolítás vagy a Szerkesztés megelőzése érdekében. A nyomkövetési azonosító módosítása csak az új központi telepítéseket és erőforrásokat érinti, nem a meglévőket.

#### <a name="can-i-track-templates-deployed-from-a-non-microsoft-repository-like-github"></a>Követhetem nyomon a nem Microsoft-tárházból telepített sablonokat, például a GitHubot?

Igen, ha a követési azonosító szerepel a sablon telepítésekor, a használat nyomon követhető. A közzétevő és a nem Microsoft adattárból üzembe helyezett sablon közötti társítás fenntartásához először le kell töltenie a közzétett sablon egy példányát (amely tartalmazza a követési azonosítót) az ajánlat kereskedelmi Piactéri listájában a Azure Portal. Tegye közzé ezt a verziót a GitHubon vagy egy másik nem Microsoft-tárházban.

Ha a sablon nem szerepel a kereskedelmi piactéren, és tartalmaz egy regisztrált GUID azonosítót, győződjön meg arról, hogy a GUID szerepel a GitHubon vagy más nem Microsoft-tárházban közzétett verzióban.

#### <a name="does-the-customer-receive-reporting-as-well"></a>Az ügyfél jelentéseket is fogad?

Nem. Az ügyfelek nyomon követhetik az összes erőforrás vagy erőforráscsoport használatát a Azure Portalon belül. Az ügyfelek nem látják a használatot a CUA követési azonosítója alapján.

#### <a name="is-customer-usage-attribution-similar-to-the-digital-partner-of-record-dpor-or-partner-admin-link-pal"></a>Az ügyfél-használati jóváírás a Record (DPOR) vagy a partner rendszergazdai hivatkozás (PAL) digitális partneréhez hasonló?

Az ügyfél-használati jóváírás olyan mechanizmus, amellyel az Azure-használatot társíthatja egy partner megismételhető, telepíthető IP-címmel, amely a társítás időpontjában üzemel. A DPOR és a PAL arra szolgál, hogy egy tanácsadót (rendszerintegrátort) vagy egy felügyeleti (felügyelt szolgáltatói) partnert társítson az ügyfél releváns Azure-lábnyomával, amikor a partner az ügyféllel is foglalkozik.