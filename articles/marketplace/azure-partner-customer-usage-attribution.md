---
title: Kereskedelmi Piactéri partner és ügyfél-használati jóváírás
description: Tekintse át az Azure Marketplace-megoldások felhasználói használatának nyomon követését ismertető cikket.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: cpercy737
ms.author: camper
ms.date: 11/4/2020
ms.custom: devx-track-terraform
ms.openlocfilehash: 99e1e77a37afbdc1ed54767700574316ed03fae3
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99525245"
---
# <a name="commercial-marketplace-partner-and-customer-usage-attribution"></a>Kereskedelmi Piactéri partner és ügyfél-használati jóváírás

Az ügyfél-használati jóváírás egy olyan módszer, amellyel az ügyfél-előfizetésekben futó Azure-erőforrásokat társíthatja a megoldás futtatásához. Ezen társítások belső Microsoft-rendszerekben való kialakítása nagyobb láthatóságot biztosít a szoftvert futtató Azure-lábnyomon. Ha elfogadja ezt a nyomon követési képességet, a Microsoft értékesítési csapatával összehangolja a Microsoft-partneri programokhoz kapcsolódó krediteket.

A társítást az Azure Marketplace-en, a gyors üzembe helyezési tárban, a privát GitHub-adattárakon és a 1:1-es ügyfél-munkavégzésen keresztül alakíthatja ki tartós IP-címmel (például egy alkalmazás fejlesztésével).

A Customer használati jóváírás három üzembe helyezési lehetőséget támogat:

- Azure Resource Manager-sablonok: a partnerek Resource Manager-sablonok használatával telepíthetik az Azure-szolgáltatásokat a partner szoftverének futtatásához. A partnerek létrehozhatnak egy Resource Manager-sablont az Azure-megoldás infrastruktúrájának és konfigurációjának definiálásához. A Resource Manager-sablonok lehetővé teszik a megoldás üzembe helyezését a teljes életciklusa során. Biztos lehet abban, hogy az erőforrások konzisztens állapotban vannak telepítve.
- Azure Resource Manager API-k: a partnerek közvetlenül meghívhatják a Resource Manager API-kat egy Resource Manager-sablon üzembe helyezéséhez, vagy az API-hívások létrehozásához az Azure-szolgáltatások közvetlen kiépítéséhez.
- Terraform: a partnerek a Terraform használatával üzembe helyezhetnek egy Resource Manager-sablont, vagy közvetlenül üzembe helyezhetik az Azure-szolgáltatásokat.

>[!IMPORTANT]
>- Az ügyfél-használati jóváírás nem alkalmas a rendszerintegrátorok, a felügyelt szolgáltatók vagy az Azure-on futó szoftverek üzembe helyezésére és kezelésére szolgáló eszközök működésének nyomon követésére.
>
>- Az ügyfél-használati jóváírás új központi telepítéseket tartalmaz, és nem támogatja a már telepített meglévő erőforrások címkézését.
>
>- Az Azure piactéren közzétett Azure- [alkalmazásokhoz](./create-new-azure-apps-offer.md) az ügyfél-használati jóváírás szükséges.
>
>- Nem minden Azure-szolgáltatás kompatibilis az ügyfél-használati jóváírással. Az Azure Kubernetes Services (ak) és a VM Scale Sets olyan ismert problémákkal rendelkeznek, amelyek jelenleg a használatot okozzák.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-guids"></a>GUID-azonosítók létrehozása

A GUID azonosító egy egyedi hivatkozási azonosító, amelynek 32 hexadecimális számjegye van. A nyomkövetési GUID azonosítók létrehozásához GUID-generátort kell használnia, például a PowerShellen keresztül.

```powershell
[guid]::NewGuid()
```

Javasoljuk, hogy hozzon létre egyedi GUID azonosítót minden ajánlathoz és terjesztési csatornához az egyes termékekhez. Dönthet úgy, hogy egyetlen GUID azonosítót használ a termék több terjesztési csatornához, ha nem szeretné, hogy a jelentés szét legyen osztva.

Ha egy terméket egy sablon használatával telepít, és az Azure piactéren és a GitHubon is elérhető, két különböző GUID azonosítót hozhat létre és regisztrálhat:

- Az A termék az Azure piactéren
- A termék a GitHubon

A jelentéskészítés Microsoft Partner Network azonosító és GUID használatával történik.

A használatot részletesebben is nyomon követheti a további GUID azonosítók regisztrálásával és a csomagok közötti GUID azonosítók módosításával, ahol a csomagok egy ajánlat különböző változatai.

## <a name="register-guids"></a>GUID-azonosítók regisztrálása

A GUID azonosítókat regisztrálni kell a partner Centerben, hogy lehetővé váljon az ügyfél-használati jóváírás.

Miután hozzáadta a GUID azonosítót a sablonhoz vagy a felhasználói ügynökhöz, és regisztrálja a GUID azonosítót a partner Centerben, a rendszer nyomon követi a jövőbeli központi telepítéseket.

> [!NOTE]
> Ha a partner centeren keresztül teszi közzé [Azure-alkalmazásait](./create-new-azure-apps-offer.md) az Azure Marketplace-en, a sablonban használt új GUID azonosító automatikusan regisztrálva lesz a partner Center-profilban a sablon feltöltésekor.  

1. Jelentkezzen be a [partner központba](https://partner.microsoft.com/dashboard).

1. Regisztráljon [kereskedelmi Piactéri közzétevőként](https://aka.ms/JoinMarketplace).

   * A partnereknek [profilra van](./partner-center-portal/create-account.md)szükségük a partner Centerben. Javasoljuk, hogy az ajánlatot az Azure Marketplace-en vagy a AppSource sorolja fel.
   * A partnerek több GUID-azonosítót is regisztrálhatnak.
   * A partnerek a nem Piactéri megoldások sablonjaihoz és ajánlatokhoz regisztrálhatnak GUID azonosítókat.

1. Válassza a **Beállítások** (fogaskerék ikon) lehetőséget a jobb felső sarokban > **Fiókbeállítások** menüpontot.

1. A **szervezeti profilok**  >  **azonosítói** lapon válassza a **nyomkövetési GUID hozzáadása** elemet.

1. A **GUID** mezőben adja meg a nyomkövetési GUID azonosítóját. Az előtag nélkül adja meg a GUID azonosítót `pid-` . A **Leírás** mezőben adja meg az ajánlat nevét vagy leírását.

1. Egynél több GUID-azonosító regisztrálásához válassza ismét a **nyomkövetési GUID hozzáadása** elemet. Az oldalon további mezők jelennek meg.

1. Kattintson a **Mentés** gombra.

## <a name="use-resource-manager-templates"></a>Resource Manager-sablonok használata
Számos partneri megoldás Azure Resource Manager-sablonok használatával van üzembe helyezve. Ha van olyan Resource Manager-sablonja, amely elérhető az Azure Marketplace-en, a GitHubon vagy egy rövid útmutatóként, a sablon módosításának folyamata, amely lehetővé teszi az ügyfél-használati jóváírást, azonnal továbbítsa.

> [!NOTE]
> A megoldási sablonok létrehozásával és közzétételével kapcsolatos további információkért lásd:
> * [Hozza létre és telepítse az első Resource Manager-sablont](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).
>* [Azure-alkalmazás ajánlata](./create-new-azure-apps-offer.md).
>* Videó: [megoldási sablonok és felügyelt alkalmazások fejlesztése az Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603)-en.


Globálisan egyedi azonosító (GUID) hozzáadásához egyetlen módosítást kell végeznie a fő sablonfájl számára:

1. [Hozzon létre egy GUID-azonosítót](#create-guids) a javasolt metódus használatával, és [regisztrálja a GUID azonosítót](#register-guids).

1. Nyissa meg a Resource Manager-sablont.

1. Vegyen fel egy [Microsoft. Resources/Deployments](/azure/templates/microsoft.resources/deployments) nevű új erőforrást a fő sablonfájlba. Az erőforrásnak csak a fájl **mainTemplate.js** vagy **azuredeploy.js** kell lennie, és nem minden beágyazott vagy csatolt sablonban.

1. Adja meg a GUID értéket az `pid-` előtag neve után az erőforrás neveként. Ha például a GUID a következő: eb7927c8-dd66-43e1-b0cf-c346a422063, az erőforrás neve _PID-eb7927c8-dd66-43e1-b0cf-c346a422063_ lesz.

1. Keresse meg az esetleges hibákat a sablonban.

1. Tegye közzé újra a sablont a megfelelő adattárakban.

1. [Ellenőrizze a GUID sikerességét a sablon központi telepítésében](#verify-the-guid-deployment).

### <a name="sample-resource-manager-template-code"></a>Példa Resource Manager-sablon kódjára

A sablon követési erőforrásainak engedélyezéséhez hozzá kell adnia a következő további erőforrást az erőforrások szakaszban. Ügyeljen arra, hogy az alábbi mintakód a saját bemeneteit használja a fő sablonfájl hozzáadásakor.
Az erőforrást fel kell venni a **mainTemplate.js** vagy **azuredeploy.js** csak fájlba, és nem minden beágyazott vagy csatolt sablonban.

```json
// Make sure to modify this sample code with your own inputs where applicable

{ // add this resource to the resources section in the mainTemplate.json (do not add the entire file)
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

## <a name="use-the-resource-manager-apis"></a>A Resource Manager API-k használata

Bizonyos esetekben előfordulhat, hogy közvetlenül a Resource Manager REST API-kon keresztül szeretne hívásokat kezdeményezni az Azure-szolgáltatások üzembe helyezéséhez. Az Azure a hívások engedélyezéséhez [több SDK](../index.yml?pivot=sdkstools) -t is támogat. Az SDK-k egyikét használhatja, vagy a REST API-kat közvetlenül is meghívhatja az erőforrások üzembe helyezéséhez.

Ha Resource Manager-sablont használ, a megoldást a korábban ismertetett utasítások követésével címkézheti. Ha nem használ Resource Manager-sablont, és közvetlen API-hívásokat végez, továbbra is címkézheti az üzembe helyezést az Azure-erőforrások használatának hozzárendeléséhez.

### <a name="tag-a-deployment-with-the-resource-manager-apis"></a>Központi telepítés címkézése a Resource Manager API-kkal

Ha engedélyezni szeretné az ügyfelek használati feltételeit, az API-hívások tervezésekor vegyen fel egy GUID azonosítót a kérelem felhasználói ügynök fejlécében. Adja hozzá a GUID azonosítót az egyes ajánlatokhoz vagy SKU-hoz. Formázza a karakterláncot az `pid-` előtaggal, és vegye fel a partner által generált GUID azonosítót. Íme egy példa a felhasználói ügynökbe való beszúráshoz használt GUID formátumra:

![Példa GUID formátumra](media/marketplace-publishers-guide/tracking-sample-guid-for-lu-2.PNG)

> [!NOTE]
> A karakterlánc formátuma fontos. Ha az `pid-` előtag nem szerepel a szolgáltatásban, az adatlekérdezés nem lehetséges. Különböző SDK-k nyomon követése eltérő. Ennek a módszernek a megvalósításához tekintse át az előnyben részesített Azure SDK támogatási és nyomkövetési megközelítését.

#### <a name="example-the-python-sdk"></a>Példa: a Python SDK

A Python esetében használja a **config** attribútumot. Az attribútumot csak UserAgent lehet hozzáadni. Bemutatunk egy példát:

![Attribútum hozzáadása felhasználói ügynökhöz](media/marketplace-publishers-guide/python-for-lu.PNG)

> [!NOTE]
> Adja hozzá az attribútumot az egyes ügyfelekhez. Nincs globális statikus konfiguráció. Előfordulhat, hogy az ügyfél-előállítót címkével látja el, hogy minden ügyfél nyomon követi a nyomkövetést. További információkért tekintse meg ezt a [Client Factory-mintát a githubon](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79).

#### <a name="example-the-net-sdk"></a>Példa: a .NET SDK

A .NET esetében ügyeljen arra, hogy a felhasználói ügynököt állítsa be. A [Microsoft. Azure. Management. Fluent](/dotnet/api/microsoft.azure.management.fluent) függvénytár használatával beállíthatja a felhasználói ügynököt a következő kóddal (például C# nyelven):

```csharp

var azure = Microsoft.Azure.Management.Fluent.Azure
    .Configure()
    // Add your pid in the user agent header
    .WithUserAgent("pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX", String.Empty) 
    .Authenticate(/* Credentials created via Microsoft.Azure.Management.ResourceManager.Fluent.SdkContext.AzureCredentialsFactory */)
    .WithSubscription("<subscription ID>");
```

#### <a name="tag-a-deployment-by-using-the-azure-powershell"></a>Központi telepítés címkézése a Azure PowerShell használatával

Ha Azure PowerShell segítségével telepít erőforrásokat, a következő módszer használatával fűzze hozzá a GUID azonosítóját:

```powershell
[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")
```

#### <a name="tag-a-deployment-by-using-the-azure-cli"></a>Központi telepítés címkézése az Azure CLI használatával

Ha az Azure CLI-t használja a GUID-azonosító hozzáfűzéséhez, állítsa be a **AZURE_HTTP_USER_AGENT** környezeti változót. Ezt a változót egy parancsfájl hatókörén belül állíthatja be. A változót globálisan is beállíthatja a rendszerhéj hatóköréhez:

```powershell
export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'
```

További információ: [Go nyelvhez készült Azure SDK](/azure/developer/go/).

## <a name="use-terraform"></a>Terraform használata

A Terraform támogatása az Azure-szolgáltató 1.21.0-kiadásán keresztül érhető el: [https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019](https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019) .  Ez a támogatás minden olyan partnerre vonatkozik, amely a Terraform-n keresztül telepíti a megoldását, valamint az Azure-szolgáltató által üzembe helyezett és mért összes erőforrást (1.21.0 vagy újabb verzió).

A Terraform-hez készült Azure-szolgáltató hozzáadta a [*partner_id*](https://www.terraform.io/docs/providers/azurerm/#partner_id) nevű új opcionális mezőt, amely a megoldáshoz használt nyomkövetési GUID azonosítóját adja meg. A mező értéke a *ARM_PARTNER_ID* környezeti változóból is származhat.

```
provider "azurerm" {
          subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          ……
          # new stuff for ISV attribution
          partner_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"}
```
Azok a partnerek, akik az ügyfél-használati jóváírás által követett Terraform szeretnék telepíteni az üzembe helyezést, a következőket kell tenniük:

* GUID létrehozása (az egyes ajánlatokhoz vagy SKU-hoz hozzá kell adni a GUID azonosítót)
* Frissítse az Azure-szolgáltatóját a *partner_id* értékének a GUID azonosítóra való beállításához (a GUID-ot ne a "PID-" beállítással állítsa be, csak állítsa a tényleges GUID azonosítóra)

## <a name="verify-the-guid-deployment"></a>A GUID központi telepítésének ellenőrzése

A sablon módosítása és a tesztek futtatása után futtassa a következő PowerShell-parancsfájlt a telepített és címkézett erőforrások lekéréséhez.

A parancsfájl segítségével ellenőrizheti, hogy a GUID sikeresen hozzá van-e adva a Resource Manager-sablonhoz. A parancsfájl nem vonatkozik a Resource Manager API-ra vagy a Terraform üzemelő példányokra.

Jelentkezzen be az Azure-ba. Válassza ki azt az előfizetést, amelynek a központi telepítését ellenőrizni szeretné a parancsfájl futtatása előtt. Futtassa a parancsfájlt az üzemelő példány előfizetési környezetében.

A központi telepítés **GUID-azonosítója** és **resourceGroup** neve kötelező paraméterek.

[Az eredeti szkript](https://gist.github.com/bmoore-msft/ae6b8226311014d6e7177c5127c7eba1#file-verify-deploymentguid-ps1) a githubon szerezhető be.

```powershell
Param(
    [GUID][Parameter(Mandatory=$true)]$guid,
    [string][Parameter(Mandatory=$true)]$resourceGroupName
)

# Get the correlationId of the pid deployment

$correlationId = (Get-AzResourceGroupDeployment -ResourceGroupName
$resourceGroupName -Name "pid-$guid").correlationId

# Find all deployments with that correlationId

$deployments = Get-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName | Where-Object{$_.correlationId -eq $correlationId}

# Find all deploymentOperations in a deployment by name
# PowerShell doesn't surface outputResources on the deployment
# or correlationId on the deploymentOperation

foreach ($deployment in $deployments){

# Get deploymentOperations by deploymentName
# then the resourceId for any create operation

($deployment | Get-AzResourceGroupDeploymentOperation | Where-Object{$_.properties.provisioningOperation -eq "Create" -and $_.properties.targetResource.resourceType -ne "Microsoft.Resources/deployments"}).properties.targetResource.id

}
```
## <a name="report"></a>Jelentés
Az Azure-használat nyomon követése az ügyfél-használati jóváíráson keresztül nem érhető el az ISV-partnerek számára. A jelentéskészítő a kereskedelmi piactér programhoz való hozzáadása a partner Centerben, amely az ügyfél-használati jóváírásra vonatkozik, a 2021-as második felet célozza meg.

## <a name="notify-your-customers"></a>Ügyfelek értesítése

A partnereknek tájékoztatniuk kell az ügyfelet az ügyfél-használati jóváírást használó üzembe helyezésekről. A Microsoft azokat az Azure-használati adatokat jelenti, amelyek a partnerhez való központi telepítésekhez vannak társítva. Az alábbi példák olyan tartalmakat tartalmaznak, amelyekkel értesítheti ügyfeleit ezekről az üzemelő példányokról. A példákban cserélje le a \<PARTNER> nevet a vállalat nevére. A partnereknek meg kell győződniük arról, hogy az értesítés igazodik az adatvédelemhez és a gyűjtési szabályzatokhoz, beleértve azokat a lehetőségeket, amelyekkel kizárható a nyomon követésből.

### <a name="notification-for-resource-manager-template-deployments"></a>Értesítés Resource Manager-sablonok üzembe helyezéséhez

A sablon központi telepítésekor a Microsoft azonosítani tudja a \<PARTNER> telepített Azure-erőforrásokkal rendelkező szoftverek telepítését. A Microsoft képes a szoftver támogatásához használt Azure-erőforrások összekapcsolására. A Microsoft ezeket az adatokat gyűjti, hogy a lehető legjobb élményt nyújtsa termékeivel, és üzleti tevékenységét. Az adatokat a Microsoft adatvédelmi szabályzatai gyűjtik és szabályozzák, amelyek a következő címen találhatók: [https://www.microsoft.com/trustcenter](https://www.microsoft.com/trustcenter) .

### <a name="notification-for-sdk-or-api-deployments"></a>Értesítés SDK-vagy API-telepítésekhez

A szoftverek központi telepítésekor a \<PARTNER> Microsoft képes azonosítani a \<PARTNER> szoftver telepítését a központilag telepített Azure-erőforrásokkal. A Microsoft képes a szoftver támogatásához használt Azure-erőforrások összekapcsolására. A Microsoft ezeket az adatokat gyűjti, hogy a lehető legjobb élményt nyújtsa termékeivel, és üzleti tevékenységét. Az adatokat a Microsoft adatvédelmi szabályzatai gyűjtik és szabályozzák, amelyek a következő címen találhatók: [https://www.microsoft.com/trustcenter](https://www.microsoft.com/trustcenter) .

## <a name="get-support"></a>Támogatás kérése

Ismerje meg a kereskedelmi piactér támogatási lehetőségeit a [partner Center kereskedelmi piactér programjának támogatásával](support.md).

### <a name="how-to-submit-a-technical-consultation-request"></a>Technikai tanácsadási kérelem beküldése

1. Látogasson el a [partner Technical Services](https://aka.ms/TechnicalJourney)webhelyre.
1. Válassza a felhőalapú infrastruktúra és felügyelet lehetőséget, és egy új oldal nyílik meg a technikai utazás megtekintéséhez.
1. A központi telepítési szolgáltatások területen kattintson a kérelem elküldése gombra.
1. Jelentkezzen be a MSA (MPN-fiók) vagy a HRE (partner irányítópult-fiókkal). a bejelentkezési hitelesítő adatai alapján megnyílik egy online kérelem űrlap:
    * Töltse ki/tekintse át a kapcsolattartási adatokat.
    * A konzultáció részleteit előre ki lehet tölteni, vagy a legördülő listából választhat.
    * Adja meg a címet és a probléma leírását (a lehető legrészletesebben adja meg a problémát).

1. Kattintson a Submit (Küldés) gombra

Részletes útmutatást találhat a képernyőképek [használatáról a Technical Sales and Deployment Services használatával](https://aka.ms/TechConsultInstructions).

### <a name="whats-next"></a>A következő lépések

A Microsoft partner technikai tanácsadója felveszi Önnel a kapcsolatot, hogy meghívja az igényeinek megfelelő hatókört.

## <a name="faq"></a>GYIK

**Milyen előnyökkel jár a GUID hozzáadása a sablonhoz?**

A Microsoft olyan partnereket biztosít, akik a megoldásuk és a befolyásolt használatuk alapján megtekintik az ügyfelek központi telepítését. A Microsoft és a partner egyaránt használhatja ezeket az információkat az értékesítési csapatok közötti szorosabb részvételhez. A Microsoft és a partner is használhatja az adatmennyiséget, hogy az egyéni partnereknek az Azure-növekedésre gyakorolt hatásával egységes képet kapjon.

**A GUID hozzáadása után módosítható?**

Igen, az ügyfél vagy a megvalósítási partner testreszabhatja a sablont, és módosíthatja vagy eltávolíthatja a GUID azonosítót. Javasoljuk, hogy a partnerek proaktív módon írják le az erőforrás és a GUID azonosító szerepét az ügyfelek és a partnerek számára, hogy megakadályozzák a GUID eltávolítását vagy szerkesztését. A GUID módosítása csak az új, nem létező, központi telepítéseket és erőforrásokat érinti.

**Követhetem nyomon a nem Microsoft-tárházból telepített sablonokat, például a GitHubot?**

Igen, ha a sablon üzembe helyezése során a GUID szerepel, a rendszer a használatot nyomon követi. A partnereknek továbbra is regisztrálniuk kell a GUID azonosítókat.

**Az ügyfél jelentéseket is fogad?**

Az ügyfelek nyomon követhetik az egyes erőforrások vagy az ügyfél által meghatározott erőforráscsoportok használatát a Azure Portalon belül. Az ügyfelek nem látják a GUID által kibontott használatot.

**A következő módszer hasonló a rekord digitális partneréhez (DPOR)?**

Ez az új módszer, amellyel az üzembe helyezést és a használatot a partneri megoldáshoz csatlakoztathatja, mechanizmust biztosít a partneri megoldások Azure-beli használatra való összekapcsolásához. A DPOR egy tanácsadó (rendszerintegrátor) vagy egy felügyeleti (felügyelt szolgáltató) partner az ügyfél Azure-előfizetésével való hozzárendelésére szolgál.

**Használhatok privát, egyéni VHD-t az Azure piactéren elérhető megoldási sablonhoz?**

Nem. A virtuális gép rendszerképének az Azure Marketplace-ről kell származnia: [közzétételi útmutató a virtuálisgép-ajánlatokhoz az Azure Marketplace](marketplace-virtual-machines.md)-en.

Létrehozhat egy virtuálisgép-ajánlatot a piactéren az egyéni VHD használatával, és megjelölheti magánjellegűként, így senki sem láthatja. Ezután hivatkozzon erre a virtuális gépre a megoldás sablonjában.

**Nem sikerült frissíteni a fő sablon *contentVersion* tulajdonságát?**

Ez valószínűleg egy hiba, ha a sablon üzembe helyezése egy másik sablonból származó TemplateLink történik, amely valamilyen oknál fogva a régebbi contentVersion vár. A megkerülő megoldás a metadata tulajdonság használata:

```
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "metadata": {
        "contentVersion": "1.0.1.0"
    },
    "parameters": {
```
