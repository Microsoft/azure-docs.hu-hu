---
title: Sablonok újrafelhasználása felhők között
description: Olyan Azure Resource Manager-sablonok (ARM-sablonok) fejlesztése, amelyek konzisztensen működnek a különböző felhőalapú környezetekben. Új létrehozása vagy meglévő sablonok frissítése Azure Stackhoz.
author: marcvaneijk
ms.topic: conceptual
ms.date: 12/09/2018
ms.author: mavane
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 806556a8da97ec84fe8141b95198b4a7da95c062
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96928358"
---
# <a name="develop-arm-templates-for-cloud-consistency"></a>ARM-sablonok fejlesztése a felhő konzisztenciájához

[!INCLUDE [requires-azurerm](../../../includes/requires-azurerm.md)]

Az Azure egyik fő előnye a konzisztencia. Egy adott helyhez tartozó fejlesztési beruházások újrafelhasználhatók egy másik helyen. Egy Azure Resource Manager sablon (ARM-sablon) egységes és megismételhető a környezetekben, beleértve a globális Azure-t, az Azure szuverén felhőket és Azure Stackeket. A sablonok Felhőbeli újrafelhasználásához azonban a jelen útmutatóban ismertetett felhőalapú függőségeket kell figyelembe vennie.

A Microsoft számos helyen kínál intelligens, nagyvállalati használatra kész felhőalapú szolgáltatásokat, többek között a következőket:

* A globális Azure platform a Microsoft által felügyelt adatközpontok egyre növekvő hálózata által támogatott a világ különböző régióiban.
* Elszigetelt szuverén felhők, mint például az Azure Germany, a Azure Government és az Azure China 21Vianet. A szuverén felhők egységes platformot biztosítanak, amely a legtöbb olyan nagyszerű funkcióval rendelkezik, amelyekkel a globális Azure-ügyfelek hozzáférhetnek.
* Azure Stack egy hibrid felhőalapú platform, amely lehetővé teszi, hogy Azure-szolgáltatásokat nyújtson a szervezet adatközpontjában. A vállalatok a saját adatközpontokban állíthatnak be Azure Stackokat, vagy Azure-szolgáltatásokat használhatnak a szolgáltatóktól, Azure Stackeket futtatnak a létesítményekben (más néven üzemeltetett régiókban).

Az összes ilyen felhők magját Azure Resource Manager olyan API-t biztosít, amely lehetővé teszi a felhasználói felületek széles választékát az Azure platformmal való kommunikációhoz. Ezzel az API-val hatékony infrastruktúra-programkódot biztosítunk. Az Azure Cloud platformon elérhető bármilyen típusú erőforrás üzembe helyezhető és konfigurálható Azure Resource Manager használatával. Egyetlen sablonnal üzembe helyezheti és konfigurálhatja a teljes alkalmazást egy működési befejezési állapotra.

![Azure-környezetek](./media/templates-cloud-consistency/environments.png)

A globális Azure, a szuverén felhők, a szolgáltatott felhők és az adatközpontban lévő felhő konzisztenciája segíti a Azure Resource Manager előnyeit. A felhőalapú erőforrás-telepítés és-konfiguráció beállítása során újra felhasználhatja a fejlesztési beruházásokat a felhők között.

Azonban bár a globális, szuverén, üzemeltetett és hibrid felhők konzisztens szolgáltatásokat biztosítanak, nem minden felhők azonosak. Ennek eredményeképpen létrehozhat egy olyan sablont, amely függőségekkel rendelkezik, csak egy adott felhőben elérhető funkciókkal.

Az útmutató további része azokat a területeket ismerteti, amelyeket figyelembe kell venni a Azure Stack meglévő ARM-sablonjainak új vagy frissített állapotának tervezésekor. Az ellenőrzőlista általában a következő elemeket tartalmazza:

* Ellenőrizze, hogy a sablonban szereplő függvények, végpontok, szolgáltatások és egyéb erőforrások elérhetők-e a cél telepítési helyein.
* A beágyazott sablonok és a konfigurációs összetevők elérhető helyen tárolhatók, így biztosítva a hozzáférést a felhőben.
* Használjon dinamikus referenciákat a rögzített kódolási hivatkozások és elemek helyett.
* Győződjön meg arról, hogy a megcélzott felhőkben használt sablon-paraméterek működnek.
* Ellenőrizze, hogy az erőforrás-specifikus tulajdonságok elérhetők-e a cél felhőkben.

Az ARM-sablonok bevezetését itt tekintheti meg: [template Deployment](overview.md).

## <a name="ensure-template-functions-work"></a>A Template functions működésének biztosítása

Az ARM-sablon alapszintű szintaxisa a JSON. A sablonok a JSON-t használják, és a szintaxis kifejezésekkel és függvényekkel bővíthetők. A sablon nyelvi processzora gyakran frissül a sablon további funkcióinak támogatásához. A sablonhoz elérhető függvények részletes ismertetését lásd: [ARM-sablon függvények](template-functions.md).

A Azure Resource Manager bevezetett új sablon-függvények nem érhetők el azonnal a szuverén felhőkben vagy Azure Stack. A sablon sikeres üzembe helyezéséhez a sablonban hivatkozott összes függvénynek elérhetőnek kell lennie a cél felhőben.

A Azure Resource Manager képességek mindig a globális Azure-ba lesznek bevezetve. A következő PowerShell-szkripttel ellenőrizheti, hogy az újonnan bevezetett sablon-függvények is elérhetők-e Azure Stackban:

1. Készítse el a GitHub-tárház klónozását: [https://github.com/marcvaneijk/arm-template-functions](https://github.com/marcvaneijk/arm-template-functions) .

1. Ha már rendelkezik a tárház helyi klónozásával, kapcsolódjon a cél Azure Resource Manager a PowerShell-lel.

1. Importálja a psm1 modult, és hajtsa végre a Test-AzureRmTemplateFunctions parancsmagot:

   ```powershell
   # Import the module
   Import-module <path to local clone>\AzTemplateFunctions.psm1

   # Execute the Test-AzureRmTemplateFunctions cmdlet
   Test-AzureRmTemplateFunctions -path <path to local clone>
   ```

A parancsfájl több, kisméretű sablont telepít, amelyek mindegyike csak egyedi sablon-függvényeket tartalmaz. A parancsfájl kimenete jelentést készít a támogatott és nem elérhető sablon-függvényekről.

## <a name="working-with-linked-artifacts"></a>Társított összetevők használata

A sablon tartalmazhat csatolt összetevőkre mutató hivatkozásokat, és olyan központi telepítési erőforrást tartalmaz, amely egy másik sablonra hivatkozik. A csatolt sablonokat (más néven beágyazott sablont) a Resource Manager lekéri futásidőben. A sablonok a virtuálisgép-bővítmények összetevőire mutató hivatkozásokat is tartalmazhatnak. Ezeket az összetevőket a virtuális GÉPEN futó virtuálisgép-bővítmény kéri le a virtuálisgép-bővítmény konfigurálásához a sablon telepítése során.

A következő szakaszok ismertetik a felhő konzisztenciájával kapcsolatos szempontokat a központi telepítési sablonon kívüli összetevőket tartalmazó sablonok fejlesztésekor.

### <a name="use-nested-templates-across-regions"></a>Beágyazott sablonok használata régiók között

A sablonok kisméretű, újrafelhasználható sablonokra bonthatók, amelyek mindegyike adott célra szolgál, és az üzembe helyezési forgatókönyvek között újra felhasználható. A központi telepítés végrehajtásához meg kell adnia egy, a fő vagy fősablonként ismert sablont. Meghatározza a telepítendő erőforrásokat, például a virtuális hálózatokat, a virtuális gépeket és a webalkalmazásokat. A fő sablon tartalmazhat egy másik sablonra mutató hivatkozást is, ami azt jelenti, hogy a sablonok beágyazására is lehetőség van. Hasonlóképpen, egy beágyazott sablon más sablonokra mutató hivatkozásokat is tartalmazhat. Akár öt szintet is megadhat.

A következő kód bemutatja, hogyan hivatkozik a templateLink paraméter egy beágyazott sablonra:

```json
"resources": [
  {
     "type": "Microsoft.Resources/deployments",
     "apiVersion": "2017-05-10",
     "name": "linkedTemplate",
     "properties": {
       "mode": "incremental",
       "templateLink": {
          "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/vNet.json",
          "contentVersion":"1.0.0.0"
       }
     }
  }
]
```

Azure Resource Manager kiértékeli a fő sablont futásidőben, és lekérdezi és kiértékeli az egyes beágyazott sablonokat. Az összes beágyazott sablon beolvasása után a sablon összeolvasztásra kerül, és a rendszer további feldolgozást kezdeményez.

### <a name="make-linked-templates-accessible-across-clouds"></a>A csatolt sablonok elérhetővé tétele a felhők között

Vegye figyelembe, hogy hol és hogyan tárolhatja a használt csatolt sablonokat. Futásidőben Azure Resource Manager beolvasása – ezért közvetlen hozzáférést igényel a kapcsolódó sablonokhoz. Gyakori eljárás a GitHub használata a beágyazott sablonok tárolására. A GitHub-tárház tartalmazhat olyan fájlokat, amelyek nyilvánosan elérhetők egy URL-címen keresztül. Bár ez a technika jól működik a nyilvános felhőben és a szuverén felhőkben, egy Azure Stack környezet a vállalati hálózaton vagy egy leválasztott távoli helyen található, a kimenő internet-hozzáférés nélkül. Ezekben az esetekben a Azure Resource Manager nem fogja tudni beolvasni a beágyazott sablonokat.

A Felhőbeli üzembe helyezések jobb gyakorlata, hogy a csatolt sablonokat a cél felhő számára elérhető helyen tárolja. Ideális esetben az üzembe helyezési összetevők karbantartása és üzembe helyezése egy folyamatos integráció/folyamatos fejlesztési (CI/CD) folyamatból történik. Azt is megteheti, hogy a beágyazott sablonokat egy blob Storage-tárolóban tárolja, amelyből a Azure Resource Manager lekérheti őket.

Mivel az egyes felhőkban a blob Storage egy másik végpont teljes tartománynevét (FQDN) használ, konfigurálja a sablont a csatolt sablonok helyére két paraméterrel. A paraméterek elfogadhatják a felhasználói adatbevitelt a központi telepítés ideje alatt. A sablonokat általában több személy készíti el és osztja meg, ezért az ajánlott eljárás a paraméterek szabványos nevének használata. Az elnevezési konvenciók segítségével a sablonok a régiók, a felhők és a szerzők több felhasználhatóságát teszik lehetővé.

A következő kódban `_artifactsLocation` egyetlen helyre mutat, amely az összes üzembe helyezéssel kapcsolatos összetevőt tartalmazza. Figyelje meg, hogy az alapértelmezett érték van megadva. Ha nincs megadva bemeneti érték `_artifactsLocation` , a rendszer az alapértelmezett értéket használja a telepítéskor. A a `_artifactsLocationSasToken` () bemenetként van használatban `sasToken` . Az alapértelmezett értéknek üres sztringnek kell lennie olyan helyzetekben `_artifactsLocation` , ahol a nem védett – például egy nyilvános GitHub-tárház.

```json
"parameters": {
  "_artifactsLocation": {
    "type": "string",
    "metadata": {
      "description": "The base URI where artifacts required by this template are located."
    },
    "defaultValue": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-custom-script-windows/"
  },
  "_artifactsLocationSasToken": {
    "type": "securestring",
    "metadata": {
      "description": "The sasToken required to access _artifactsLocation."
    },
    "defaultValue": ""
  }
}
```

A sablonban a hivatkozások úgy jönnek létre, hogy az alap URI-t (a `_artifactsLocation` paraméterből) kombinálva egy relatív elérési úttal és a `_artifactsLocationSasToken` . A következő kód bemutatja, hogyan adhatja meg a beágyazott sablonra mutató hivatkozást az URI-sablon függvény használatával:

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2019-10-01",
    "name": "shared",
    "properties": {
      "mode": "Incremental",
      "templateLink": {
        "uri": "[uri(parameters('_artifactsLocation'), concat('nested/vnet.json', parameters('_artifactsLocationSasToken')))]",
        "contentVersion": "1.0.0.0"
      }
    }
  }
]
```

Ennek a módszernek a használatával a paraméter alapértelmezett értékét `_artifactsLocation` használja a rendszer. Ha a csatolt sablonokat le kell kérni egy másik helyről, a bemeneti paraméter a telepítés során használható az alapértelmezett érték felülbírálására – a sablon módosítására nincs szükség.

### <a name="use-_artifactslocation-instead-of-hardcoding-links"></a>Rögzítjük-hivatkozások helyett _artifactsLocation használata

A beágyazott sablonok használata mellett a paraméterben szereplő URL-cím a `_artifactsLocation` központi telepítési sablon összes kapcsolódó összetevője alapjaként szolgál. Néhány virtuálisgép-bővítmény tartalmaz egy hivatkozást a sablonon kívül tárolt parancsfájlra. Ezen bővítmények esetében ne parancsmagba a hivatkozásokat. Előfordulhat például, hogy az egyéni parancsfájl és a PowerShell DSC-bővítmények a GitHubon lévő külső parancsfájlra mutatnak, ahogy az a következő képen látható:

```json
"properties": {
  "publisher": "Microsoft.Compute",
  "type": "CustomScriptExtension",
  "typeHandlerVersion": "1.9",
  "autoUpgradeMinorVersion": true,
  "settings": {
    "fileUris": [
      "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
    ]
  }
}
```

A parancsfájlra mutató hivatkozások rögzítjük megakadályozhatja, hogy a sablon üzembe helyezése egy másik helyre sikeres legyen. A virtuálisgép-erőforrás konfigurálása során a virtuális gépen futó virtuálisgép-ügynök elindítja a virtuálisgép-bővítményben csatolt összes parancsfájl letöltését, majd a parancsfájlokat a virtuális gép helyi lemezén tárolja. Ez a megközelítés a "beágyazott sablonok használata régiók között" című szakaszban ismertetett beágyazott sablon hivatkozásait is tartalmazza.

A Resource Manager lekéri a beágyazott sablonokat futásidőben. Virtuálisgép-bővítmények esetén a virtuálisgép-ügynök a külső összetevők lekérését hajtja végre. Az összetevők lekérésének különböző kezdeményezője mellett a sablon definíciójában lévő megoldás is ugyanaz. Használja a _artifactsLocation paramétert az alapelérési út alapértelmezett értékével, ahol az összes összetevő tárolva van (beleértve a virtuálisgép-bővítmény parancsfájljait is) és a `_artifactsLocationSasToken` sasToken bemenetének paraméterét.

```json
"parameters": {
  "_artifactsLocation": {
    "type": "string",
    "metadata": {
      "description": "The base URI where artifacts required by this template are located."
    },
    "defaultValue": "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/"
  },
  "_artifactsLocationSasToken": {
    "type": "securestring",
    "metadata": {
      "description": "The sasToken required to access _artifactsLocation."
    },
    "defaultValue": ""
  }
}
```

Egy összetevő abszolút URI-azonosítójának létrehozásához az előnyben részesített módszer az URI-sablon függvény használata az Összefűzés sablon függvény helyett. Ha a virtuálisgép-bővítményben lévő parancsfájlokra mutató hivatkozásokat cserél a hardcoded az URI-sablon függvénnyel, a sablonban lévő funkció a felhő konzisztenciájára van konfigurálva.

```json
"properties": {
  "publisher": "Microsoft.Compute",
  "type": "CustomScriptExtension",
  "typeHandlerVersion": "1.9",
  "autoUpgradeMinorVersion": true,
  "settings": {
    "fileUris": [
      "[uri(parameters('_artifactsLocation'), concat('scripts/configure-music-app.ps1', parameters('_artifactsLocationSasToken')))]"
    ]
  }
}
```

Ezzel a módszerrel az összes üzembe helyezési összetevő, beleértve a konfigurációs parancsfájlokat is, ugyanazon a helyen tárolhatók, mint maga a sablon. Az összes hivatkozás helyének módosításához csak meg kell adnia egy másik alap URL-címet a _artifactsLocation paraméterekhez_.

## <a name="factor-in-differing-regional-capabilities"></a>Eltérő regionális képességekkel rendelkező faktor

Az Azure-ba bevezetett Újdonságok és folyamatos fejlesztés, valamint a folyamatosan bővülő szolgáltatások és frissítések terén a [régiók eltérőek lehetnek](https://azure.microsoft.com/regions/services/) a szolgáltatások és a frissítések elérhetőségében. A szigorú belső tesztelést követően a meglévő szolgáltatások új szolgáltatásai vagy frissítései általában egy érvényesítési programban részt vevő ügyfelek kis közönségéhez vezetnek be. A sikeres ügyfél-ellenőrzés után a szolgáltatások vagy frissítések elérhetők az Azure-régiók egy részhalmazán belül, majd több régióban, a szuverén felhőkbe való bevezetéssel, illetve a Azure Stack ügyfelek számára is elérhetővé tehetők.

Annak ismerete, hogy az Azure-régiók és a felhők a rendelkezésre álló szolgáltatásokban eltérőek lehetnek, elvégezheti a sablonokkal kapcsolatos proaktív döntéseket. A megfelelő kiindulópont a felhőhöz elérhető erőforrás-szolgáltatók vizsgálatával érhető el. Az erőforrás-szolgáltató az Azure-szolgáltatásokhoz elérhető erőforrások és műveletek készletét jelzi.

A sablonok erőforrásokat telepítenek és konfigurálnak. Az erőforrás típusát egy erőforrás-szolgáltató biztosítja. A számítási erőforrás-szolgáltató (Microsoft. számítás) például több erőforrástípust is biztosít, például a virtualMachines és a availabilitySets. Minden erőforrás-szolgáltató olyan API-t biztosít, Azure Resource Manager egy közös szerződés határozza meg, amely egységes, egységes authoring-élményt tesz lehetővé az összes erőforrás-szolgáltatón. Előfordulhat azonban, hogy a globális Azure-ban elérhető erőforrás-szolgáltató nem érhető el szuverén felhőben vagy Azure Stack régióban.

![Erőforrás-szolgáltatók](./media/templates-cloud-consistency/resource-providers.png)

Az adott felhőben elérhető erőforrás-szolgáltatók ellenőrzéséhez futtassa a következő szkriptet az Azure parancssori felületén ([CLI](/cli/azure/install-azure-cli)):

```azurecli-interactive
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

A következő PowerShell-parancsmagot is használhatja az elérhető erőforrás-szolgáltatók megjelenítéséhez:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
```

### <a name="verify-the-version-of-all-resource-types"></a>Az összes erőforrástípus verziójának ellenőrzése

A tulajdonságok halmaza minden erőforrástípus esetében gyakori, de mindegyik erőforráshoz saját tulajdonságok is tartozik. Az új API-verziókban a funkciók és a kapcsolódó tulajdonságok a meglévő erőforrástípusokba kerülnek. A sablonban lévő erőforrás saját API Version tulajdonsága van – `apiVersion` . Ez a verziószámozás biztosítja, hogy egy sablon meglévő erőforrás-konfigurációját a platform változásai nem érintik.

Előfordulhat, hogy a globális Azure-beli meglévő erőforrástípusok számára bevezetett új API-verziók nem érhetők el azonnal minden régióban, szuverén felhőkben vagy Azure Stackban. Ha meg szeretné tekinteni az elérhető erőforrás-szolgáltatók, erőforrástípusok és API-verziók listáját a felhőhöz, használhatja a Azure Portal erőforrás-kezelő. Erőforrás-kezelő keresése a minden szolgáltatás menüben. Bontsa ki a erőforrás-kezelő szolgáltatók csomópontját, hogy az összes elérhető erőforrás-szolgáltatót, azok erőforrás-típusait és API-verzióit visszaállítsa a felhőbe.

Az Azure CLI-ben az adott felhőben lévő összes erőforrástípus elérhető API-verziójának listázásához futtassa a következő parancsfájlt:

```azurecli-interactive
az provider list --query "[].{namespace:namespace, resourceType:resourceType[]}"
```

A következő PowerShell-parancsmagot is használhatja:

```azurepowershell-interactive
Get-AzureRmResourceProvider | select-object ProviderNamespace -ExpandProperty ResourceTypes | ft ProviderNamespace, ResourceTypeName, ApiVersions
```

### <a name="refer-to-resource-locations-with-a-parameter"></a>Tekintse meg az erőforrás-tárolóhelyek paramétert

A sablonok mindig egy adott régióban található erőforráscsoporthoz vannak telepítve. Az üzembe helyezésen kívül a sablonban lévő összes erőforráshoz tartozik egy Location tulajdonság is, amelyet a-ben telepítendő régió megadására használhat. Ahhoz, hogy a sablon fejlessze a felhő konzisztenciáját, dinamikus módszerre van szüksége az erőforrás-helyekre való hivatkozáshoz, mivel mindegyik Azure Stack tartalmazhat egyedi neveket. Az erőforrások általában ugyanabban a régióban vannak üzembe helyezve, mint az erőforráscsoport, de az olyan forgatókönyvek támogatásához, mint például a régiók közötti alkalmazás rendelkezésre állása, hasznos lehet az erőforrások több régión belüli terjesztéséhez.

Annak ellenére, hogy parancsmagba a régiók nevét egy sablon erőforrás-tulajdonságainak megadásakor, ez a módszer nem garantálja, hogy a sablon más Azure Stack környezetekben is telepíthető, mert a régió neve valószínűleg nem létezik.

A különböző régiók befogadásához adja hozzá a bemeneti paraméter helyét a sablonhoz egy alapértelmezett értékkel. Ha az üzembe helyezés során nem ad meg értéket, a rendszer az alapértelmezett értéket fogja használni.

A sablon függvény `[resourceGroup()]` egy olyan objektumot ad vissza, amely a következő kulcs/érték párokat tartalmazza:

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

Ha a bemeneti paraméter defaultValue értékében lévő objektum hely kulcsára hivatkozik, Azure Resource Manager a futtatáskor a `[resourceGroup().location]` sablon azon erőforráscsoport helyének a nevét adja meg, amelyen a sablon telepítve lesz.

```json
"parameters": {
  "location": {
    "type": "string",
    "metadata": {
      "description": "Location the resources will be deployed to."
    },
    "defaultValue": "[resourceGroup().location]"
  }
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2015-06-15",
    "name": "storageaccount1",
    "location": "[parameters('location')]",
    ...
```

Ezzel a sablon funkcióval bármilyen felhőbe üzembe helyezheti a sablont anélkül, hogy a régiók nevét még nem tudta előre megismerni. Emellett a sablon egy adott erőforrásának helye nem különbözhet az erőforráscsoport helyétől. Ebben az esetben beállíthatja, hogy az adott erőforráshoz további bemeneti paramétereket használjon, míg az ugyanabban a sablonban lévő többi erőforrás továbbra is a kezdeti hely bemeneti paraméterét használja.

### <a name="track-versions-using-api-profiles"></a>Verziók nyomon követése API-profilok használatával

Nagyon nehéz lehet nyomon követni az összes rendelkezésre álló erőforrás-szolgáltatót és a Azure Stackban található kapcsolódó API-verziókat. Például az írás időpontjában a **Microsoft. számítási/availabilitySets** legújabb API-verziója érhető el az Azure-ban `2018-04-01` , míg az Azure-hoz és Azure stack-hoz KÖZÖSen elérhető API-verzió `2016-03-30` . A **Microsoft. Storage/storageAccounts** közös API-verziója az összes Azure-és Azure stack-hely között meg van osztva `2016-01-01` , míg az Azure legújabb API-verziója `2018-02-01` .

Ebből kifolyólag az erőforrás-kezelő bevezette a sablonok API-profiljainak koncepcióját. Az API-profilok nélkül a sablonban lévő összes erőforrás egy olyan elemmel van konfigurálva, `apiVersion` amely leírja az adott erőforrás API-verzióját.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "metadata": {
          "description": "Location the resources will be deployed to."
      },
      "defaultValue": "[resourceGroup().location]"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-01-01",
      "name": "mystorageaccount",
      "location": "[parameters('location')]",
      "properties": {
        "accountType": "Standard_LRS"
      }
    },
    {
      "type": "Microsoft.Compute/availabilitySets",
      "apiVersion": "2016-03-30",
      "name": "myavailabilityset",
      "location": "[parameters('location')]",
      "properties": {
        "platformFaultDomainCount": 2,
        "platformUpdateDomainCount": 2
      }
    }
  ],
  "outputs": {}
}
```

Az API-profilok verziója aliasként működik az Azure-ban és az Azure Stack-ban közös erőforrás-típushoz tartozó egyetlen API-verzióhoz. Ahelyett, hogy egy API-verziót kellene megadnia a sablonban szereplő összes erőforráshoz, csak az API-profil verzióját kell megadnia egy új gyökérelem, és el kell `apiProfile` hagyni az `apiVersion` egyes erőforrások elemét.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "apiProfile": "2018–03-01-hybrid",
    "parameters": {
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location the resources will be deployed to."
            },
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "mystorageaccount",
            "location": "[parameters('location')]",
            "properties": {
                "accountType": "Standard_LRS"
            }
        },
        {
            "type": "Microsoft.Compute/availabilitySets",
            "name": "myavailabilityset",
            "location": "[parameters('location')]",
            "properties": {
                "platformFaultDomainCount": 2,
                "platformUpdateDomainCount": 2
            }
        }
    ],
    "outputs": {}
}
```

Az API-profil biztosítja, hogy az API-verziók elérhetők legyenek a helyek között, így nem kell manuálisan ellenőriznie az adott helyen elérhető apiVersions. Annak biztosítása érdekében, hogy az API-profil által hivatkozott API-verziók elérhetők legyenek egy Azure Stack környezetben, a Azure Stack operátoroknak a támogatási szabályzat alapján naprakészen kell tartaniuk a megoldást. Ha a rendszer több mint hat hónapja elavult, nem felel meg az előírásoknak, és a környezetet frissíteni kell.

Az API-profil nem a sablonban szükséges elem. Még ha hozzáadja is az elemet, csak olyan erőforrásokhoz lesz használatban, amelyekhez nincs `apiVersion` megadva. Ez az elem lehetővé teszi a fokozatos módosításokat, de nem igényel módosításokat a meglévő sablonokban.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "apiProfile": "2018–03-01-hybrid",
    "parameters": {
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location the resources will be deployed to."
            },
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2016-01-01",
            "name": "mystorageaccount",
            "location": "[parameters('location')]",
            "properties": {
                "accountType": "Standard_LRS"
            }
        },
        {
            "type": "Microsoft.Compute/availabilitySets",
            "name": "myavailabilityset",
            "location": "[parameters('location')]",
            "properties": {
                "platformFaultDomainCount": 2,
                "platformUpdateDomainCount": 2
            }
        }
    ],
    "outputs": {}
}
```

## <a name="check-endpoint-references"></a>Végpontok hivatkozásainak keresése

Az erőforrások a platform egyéb szolgáltatásaira is hivatkoznak. A nyilvános IP-címekhez például hozzá lehet rendelni egy nyilvános DNS-nevet. A nyilvános felhő, a szuverén felhők és a Azure Stack megoldások saját végponti névterekkel rendelkeznek. A legtöbb esetben az erőforráshoz csak egy előtag szükséges a sablonban. A futtatókörnyezet során a Azure Resource Manager hozzáfűzi a végpont értékét. Egyes végpont-értékeket explicit módon meg kell adni a sablonban.

> [!NOTE]
> A Felhőbeli konzisztencia-sablonok fejlesztéséhez ne parancsmagba a végponti névtereket.

Az alábbi két példa olyan általános végponti névtereket tartalmaz, amelyeket explicit módon meg kell adni egy erőforrás létrehozásakor:

* Storage-fiókok (blob, üzenetsor, tábla és fájl)
* Adatbázisok és Azure cache Redis-hez kapcsolódó kapcsolatainak karakterláncai

A végponti névterek a sablon kimenetében is használhatók a felhasználó számára, amikor az üzembe helyezés befejeződik. A következő gyakori példák:

* Storage-fiókok (blob, üzenetsor, tábla és fájl)
* Kapcsolatok karakterláncai (MySql, SQLServer, Rendszerkarbantartás, Custom, NotificationHub, ServiceBus, EventHub, ApiHub, DocDb, RedisCache, PostgreSQL)
* Traffic Manager
* nyilvános IP-cím Domainnamelabel értékkel
* Felhőszolgáltatások

Általában Kerülje a hardcoded-végpontok egy sablonban való használatának elkerülését. Az ajánlott eljárás az, ha a hivatkozási sablon függvény használatával dinamikusan kéri le a végpontokat. Például a végpont leggyakrabban hardcoded a Storage-fiókok végponti névtere. Minden egyes Storage-fiókhoz tartozik egy egyedi FQDN, amely a Storage-fiók végpont névterével való összefűzésével jön létre. A mystorageaccount1 nevű blob Storage-fiók különböző teljes tartományneveket eredményez a felhőtől függően:

* `mystorageaccount1.blob.core.windows.net` a globális Azure-felhőben való létrehozásakor.
* `mystorageaccount1.blob.core.chinacloudapi.cn` Az Azure China 21Vianet-felhőben való létrehozásakor.

A következő hivatkozási sablon függvény lekéri a végponti névteret a tárolási erőforrás-szolgáltatótól:

```json
"diskUri":"[concat(reference(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))).primaryEndpoints.blob, 'container/myosdisk.vhd')]"
```

A Storage-fiók végpontjának hardcoded értékének a sablon függvénnyel való lecserélésekor `reference` ugyanazt a sablont használhatja a különböző környezetekben való üzembe helyezéshez anélkül, hogy módosítani kellene a végpont-referenciát.

### <a name="refer-to-existing-resources-by-unique-id"></a>Tekintse meg a meglévő erőforrásokat egyedi azonosító alapján

Megtekintheti egy meglévő erőforrást is ugyanabból vagy egy másik erőforráscsoporthoz, valamint ugyanazon az előfizetésen belül vagy egy másik előfizetésben, ugyanazon a bérlőn belül ugyanazon a felhőben. Az erőforrás-tulajdonságok lekéréséhez az erőforrás egyedi azonosítóját kell használnia. A `resourceId` sablon függvény lekérdezi egy erőforrás egyedi azonosítóját, például SQL Servert, ahogy az alábbi kód mutatja:

```json
"outputs": {
  "resourceId":{
    "type": "string",
    "value": "[resourceId('otherResourceGroup', 'Microsoft.Sql/servers', parameters('serverName'))]"
  }
}
```

Ezután használhatja a `resourceId` függvényt a `reference` sablon függvényen belül egy adatbázis tulajdonságainak lekéréséhez. A Return objektum tartalmazza `fullyQualifiedDomainName` azt a tulajdonságot, amely a teljes végpont értékét tárolja. Ez az érték lekérése futásidőben történik, és a felhőalapú környezethez kapcsolódó végponti névteret biztosítja. Ha a rögzítjük nélkül szeretné megadni a kapcsolódási karakterláncot, a visszatérési objektum tulajdonságát közvetlenül a kapcsolódási karakterláncban tekintheti meg a következő képen látható módon:

```json
"[concat('Server=tcp:', reference(resourceId('sql', 'Microsoft.Sql/servers', parameters('test')), '2015-05-01-preview').fullyQualifiedDomainName, ',1433;Initial Catalog=', parameters('database'),';User ID=', parameters('username'), ';Password=', parameters('pass'), ';Encrypt=True;')]"
```

## <a name="consider-resource-properties"></a>Erőforrás-tulajdonságok megfontolása

Azure Stack környezetekben lévő erőforrások egyedi tulajdonságokkal rendelkeznek, amelyeket figyelembe kell vennie a sablonban.

### <a name="ensure-vm-images-are-available"></a>Győződjön meg arról, hogy a virtuálisgép-lemezképek elérhetők

Az Azure a VM-rendszerképek széles választékát kínálja. Ezek a rendszerképek a Microsoft és partnerei által történő üzembe helyezéshez készültek. A lemezképek alkotják a platformon futó virtuális gépek alapjait. A felhőalapú sablonnak azonban csak a rendelkezésre álló paraméterekre kell vonatkoznia – különösen a virtuálisgép-lemezképek közzétevője, ajánlata és SKU-jának a globális Azure, az Azure szuverén felhők vagy egy Azure Stack megoldás számára.

Egy helyen elérhető virtuálisgép-rendszerképek listájának lekéréséhez futtassa az alábbi Azure CLI-parancsot:

```azurecli-interactive
az vm image list -all
```

Ugyanezt a listát a [Get-AzureRmVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) Azure PowerShell parancsmaggal kérheti le, és a paraméterrel adhatja meg a kívánt helyet `-Location` . Például:

```azurepowershell-interactive
Get-AzureRmVMImagePublisher -Location "West Europe" | Get-AzureRmVMImageOffer | Get-AzureRmVMImageSku | Get-AzureRmVMImage
```

Ez a parancs néhány percet vesz igénybe, hogy az összes elérhető lemezképet visszaállítsa a globális Azure-felhő nyugati európai régiójában.

Ha ezeket a virtuálisgép-lemezképeket Azure Stack számára elérhetővé tette, a rendszer az összes rendelkezésre álló tárterületet felhasználja. Ahhoz, hogy még a legkisebb méretezési egység is beférjen, Azure Stack lehetővé teszi, hogy kiválassza a környezetbe felvenni kívánt képeket.

Az alábbi mintakód egy egységes megközelítést mutat be az ARM-sablonok közzétevői, ajánlati és SKU-paramétereinek kifejezésére:

```json
"storageProfile": {
    "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "2016-Datacenter",
    "version": "latest"
    }
}
```

### <a name="check-local-vm-sizes"></a>Helyi virtuálisgép-méretek keresése

Ahhoz, hogy a sablon fejlessze a felhő konzisztenciáját, meg kell győződnie arról, hogy a kívánt virtuálisgép-méret elérhető az összes célként megadott környezetben. A virtuálisgép-méretek a teljesítménnyel kapcsolatos jellemzők és képességek csoportja. Egyes virtuálisgép-méretek a virtuális gép által futtatott hardvertől függenek. Ha például GPU-re optimalizált virtuális gépet szeretne üzembe helyezni, a Hypervisort futtató hardvernek rendelkeznie kell a hardver GPU-val.

Ha a Microsoft új méretű virtuális gépet vezet be, amely bizonyos hardveres függőségekkel rendelkezik, a virtuálisgép-méret általában az Azure-felhőben lévő régiók egy kis részhalmazában érhető el. Később elérhetővé válik más régiók és felhők számára is. Annak érdekében, hogy a virtuálisgép-méret minden olyan felhőben megtalálható legyen, amelyet üzembe helyezett, az alábbi Azure CLI-paranccsal kérheti le a rendelkezésre álló méreteket:

```azurecli-interactive
az vm list-sizes --location "West Europe"
```

Azure PowerShell esetén használja a következőt:

```azurepowershell-interactive
Get-AzureRmVMSize -Location "West Europe"
```

Az elérhető szolgáltatások teljes listájáért tekintse meg a [régiók által elérhető termékek](https://azure.microsoft.com/global-infrastructure/services/?cdn=disable)című témakört.

### <a name="check-use-of-azure-managed-disks-in-azure-stack"></a>Az Azure Managed Disks használatának ellenõrzése Azure Stack

A felügyelt lemezek kezelik az Azure-bérlők tárolóját. Ahelyett, hogy explicit módon létrehozza a Storage-fiókot, és megadja a virtuális merevlemez (VHD) URI-JÁT, a felügyelt lemezekkel implicit módon hajthatja végre ezeket a műveleteket a virtuális gépek telepítésekor. A felügyelt lemezek javítják a rendelkezésre állást azáltal, hogy az ugyanazon rendelkezésre állási csoportba tartozó virtuális gépek összes lemezét különböző tárolási egységekre helyezi. Emellett a meglévő VHD-k a standard és a prémium szintű tárterületre is átalakíthatók, jelentősen kevesebb állásidővel.

Bár a felügyelt lemezek a Azure Stack ütemtervén találhatók, jelenleg nem támogatottak. Amíg ezek a felhasználók, a virtuális merevlemezek explicit módon történő megadásával kifejlesztheti a Azure Stack felhőalapú sablonjait a virtuálisgép `vhd` -erőforráshoz a sablon eleme alapján, ahogy az a következő ábrán látható:

```json
"storageProfile": {
  "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "[parameters('windowsOSVersion')]",
    "version": "latest"
  },
  "osDisk": {
    "name": "osdisk",
    "vhd": {
      "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2015-06-15').primaryEndpoints.blob, 'vhds/osdisk.vhd')]"
    },
    "caching": "ReadWrite",
    "createOption": "FromImage"
  }
}
```

Ezzel szemben, ha a felügyelt lemez konfigurációját egy sablonban szeretné megadni, távolítsa el az `vhd` elemet a lemez konfigurációjától.

```json
"storageProfile": {
  "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "[parameters('windowsOSVersion')]",
    "version": "latest"
  },
  "osDisk": {
    "caching": "ReadWrite",
    "createOption": "FromImage"
  }
}
```

Ugyanezek a módosítások is alkalmaznak [adatlemezeket](../../virtual-machines/using-managed-disks-template-deployments.md).

### <a name="verify-that-vm-extensions-are-available-in-azure-stack"></a>Győződjön meg arról, hogy a virtuálisgép-bővítmények elérhetők a Azure Stack

A felhő konzisztenciájának egy másik szempontja a [virtuálisgép-bővítmények használata a virtuális gépeken](../../virtual-machines/extensions/features-windows.md) belüli erőforrások konfigurálására. Nem minden virtuálisgép-bővítmény érhető el Azure Stackban. A sablon megadhatja a virtuálisgép-bővítményhez dedikált erőforrásokat, függőségeket és feltételeket hozhat létre a sablonon belül.

Ha például Microsoft SQL Server rendszert futtató virtuális gépet szeretne konfigurálni, akkor a virtuálisgép-bővítmény a sablon központi telepítésének részeként konfigurálhatja a SQL Server. Vegye figyelembe, hogy mi történik, ha a központi telepítési sablon egy SQL Server-t futtató virtuális gépen található adatbázis létrehozásához konfigurált alkalmazáskiszolgáló is tartalmaz. Emellett az alkalmazás-kiszolgálók virtuálisgép-bővítményének használatával is konfigurálhatja az alkalmazáskiszolgáló függőségét az SQL Server VM-bővítmény erőforrásának sikeres visszaadásához. Ez a megközelítés biztosítja, hogy a SQL Server rendszert futtató virtuális gép konfigurálva legyen, és elérhető legyen, ha az alkalmazáskiszolgáló arra utasítja, hogy hozza létre az adatbázist.

A sablon deklaratív megközelítése lehetővé teszi az erőforrások és a hozzájuk tartozó függőségek befejezési állapotának meghatározását, miközben a platform gondoskodik a függőségekhez szükséges logikáról.

#### <a name="check-that-vm-extensions-are-available"></a>Győződjön meg arról, hogy a virtuálisgép-bővítmények elérhetők

A virtuálisgép-bővítmények számos típusa létezik. A Felhőbeli konzisztencia sablonjának fejlesztésekor ügyeljen arra, hogy csak azokat a bővítményeket használja, amelyek elérhetők a sablon által célként megadott összes régióban.

Az adott régióhoz elérhető virtuálisgép-bővítmények (ebben a példában) listájának lekéréséhez `myLocation` futtassa az alábbi Azure CLI-parancsot:

```azurecli-interactive
az vm extension image list --location myLocation
```

Végrehajthatja a Azure PowerShell [Get-AzureRmVmImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) parancsmagot is, és a használatával `-Location` megadhatja a virtuális gép rendszerképének helyét. Például:

```azurepowershell-interactive
Get-AzureRmVmImagePublisher -Location myLocation | Get-AzureRmVMExtensionImageType | Get-AzureRmVMExtensionImage | Select Type, Version
```

#### <a name="ensure-that-versions-are-available"></a>Győződjön meg arról, hogy a verziók elérhetők

Mivel a virtuálisgép-bővítmények az első féltől származó Resource Manager-erőforrások, saját API-verziókkal rendelkeznek. A következő kód azt mutatja, hogy a virtuálisgép-bővítmény típusa egy beágyazott erőforrás a Microsoft. számítási erőforrás-szolgáltatóban.

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "apiVersion": "2015-06-15",
    "name": "myExtension",
    "location": "[parameters('location')]",
    ...
```

A virtuálisgép-bővítmény erőforrásának API-verziójának szerepelnie kell a sablonnal megcélzott összes helyen. A hely függőségei ugyanúgy működnek, mint az erőforrás-szolgáltató API-verziójának rendelkezésre állása, amelyet a "minden erőforrástípus verziójának ellenőrzése" című szakaszban ismertetett.

A virtuálisgép-bővítmény erőforráshoz elérhető API-verziók listájának lekéréséhez használja a [Get-AzureRmResourceProvider](/powershell/module/az.resources/get-azresourceprovider) parancsmagot a **Microsoft. számítási** erőforrás-szolgáltatóval, ahogy az a következő képen látható:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Compute" | Select-Object -ExpandProperty ResourceTypes | Select ResourceTypeName, Locations, ApiVersions | where {$_.ResourceTypeName -eq "virtualMachines/extensions"}
```

Virtuálisgép-méretezési csoportokban is használhatja a virtuálisgép-bővítményeket. Ugyanazok a tartózkodási feltételek érvényesek. Ha a sablont a felhő konzisztenciájára szeretné fejleszteni, győződjön meg arról, hogy az API-verziók elérhetők az összes olyan helyen, amelyre telepíteni kívánja a üzembe helyezését. Ha le szeretné kérni a virtuálisgép-bővítmény erőforrásának API-verzióit a méretezési csoportokhoz, használja ugyanazt a parancsmagot, mint korábban, de adja meg a virtuálisgép-méretezési csoportok erőforrástípus típusát az alábbiak szerint:

```azurepowershell-interactive
Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Compute" | Select-Object -ExpandProperty ResourceTypes | Select ResourceTypeName, Locations, ApiVersions | where {$_.ResourceTypeName -eq "virtualMachineScaleSets/extensions"}
```

Az egyes bővítmények verziószáma is megtörténik. Ez a verzió a virtuálisgép- `typeHandlerVersion` bővítmény tulajdonságában látható. Győződjön meg arról, hogy a sablon virtuálisgép- `typeHandlerVersion` bővítményei elemében megadott verzió elérhető azon a helyen, ahol a sablon üzembe helyezését tervezi. A következő kód például az 1,7-es verziót adja meg:

```json
{
    "type": "extensions",
    "apiVersion": "2016-03-30",
    "name": "MyCustomScriptExtension",
    "location": "[parameters('location')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]"
    ],
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.7",
        ...
```

Egy adott virtuálisgép-bővítmény elérhető verzióinak listájának lekéréséhez használja a [Get-AzureRmVMExtensionImage](/powershell/module/az.compute/get-azvmextensionimage) parancsmagot. A következő példa lekéri a PowerShell DSC (kívánt állapot konfiguráció) virtuálisgép-bővítményének elérhető verzióit a **myLocation**:

```azurepowershell-interactive
Get-AzureRmVMExtensionImage -Location myLocation -PublisherName Microsoft.PowerShell -Type DSC | FT
```

A közzétevők listájának lekéréséhez használja a [Get-AzureRmVmImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) parancsot. A kérelem típusához használja a [Get-AzureRmVMExtensionImageType](/powershell/module/az.compute/get-azvmextensionimagetype) elismerést.

## <a name="tips-for-testing-and-automation"></a>Tippek teszteléshez és automatizáláshoz

Az összes kapcsolódó beállítás, képesség és korlátozás nyomon követése egy sablon készítése során. A közös megközelítés a sablonok egyetlen felhővel való fejlesztése és tesztelése a többi hely megcélozása előtt. Azonban a tesztek végrehajtása a szerzői folyamat során történik, annál kevesebb hibaelhárítást és kódot kell megírnia a fejlesztői csapatnak. A hely függőségei miatt sikertelen telepítések időigényesek lehetnek a hibák megoldásához. Ezért javasoljuk az automatizált tesztelést a lehető leghamarabb a szerzői ciklusban. Végső soron kevesebb fejlesztési időre és kevesebb erőforrásra lesz szüksége, és a felhővel konzisztens összetevők még értékesebb lesznek.

Az alábbi képen egy, az integrált fejlesztési környezet (IDE) használatával egy csoport fejlesztési folyamatának tipikus példája látható. Az idősor különböző szakaszaiban különböző tesztelési típusok lesznek végrehajtva. Itt két fejlesztő dolgozik ugyanazon a megoldáson, de ez a forgatókönyv egyformán érvényes egyetlen fejlesztőre vagy egy nagy csapatra is. Minden fejlesztő általában egy központi tárház helyi példányát hozza létre, amely lehetővé teszi, hogy mindegyik a helyi másolaton működjön, anélkül, hogy befolyásolná azokat a felhasználókat, akik ugyanazon a fájlokon dolgoznak.

![A diagram két egységnyi tesztet és integrációs tesztet mutat be párhuzamosan a helyi I D E-n, amelyek egyesítik a C I/C fejlesztési folyamat egységes tesztelését, az integrációs teszteket, majd az üzembe helyezést, majd a telepítést.](./media/templates-cloud-consistency/workflow.png)

A teszteléshez és automatizáláshoz vegye figyelembe a következő tippeket:

* Végezze el a tesztelési eszközök használatát. Például a Visual Studio Code és a Visual Studio olyan IntelliSense-és egyéb funkciókat is tartalmaz, amelyek segítségével érvényesítheti a sablonokat.
* A helyi IDE-ben végzett fejlesztés során a kód minőségének javításához végezze el a statikus kódok elemzését az egységes tesztek és az integrációs tesztek segítségével.
* Ha a kezdeti fejlesztés során még jobb élményre van szó, az egység-és az integrációs tesztek csak akkor figyelmeztetnek, ha problémát észlelnek, és folytatják a teszteket. Így azonosíthatja a megoldandó problémákat, és rangsorolhatja a módosításokat, más néven a tesztelésen alapuló üzembe helyezést (TDD).
* Vegye figyelembe, hogy egyes tesztek a Azure Resource Manager csatlakoztatása nélkül is elvégezhetők. Mások, például a sablonok tesztelésének megkövetelése, hogy a Resource Manager olyan műveleteket végezzen, amelyeken nem végezhető el a kapcsolat nélküli üzemmód.
* Egy központi telepítési sablon az érvényesítési API-val való tesztelése nem egyenlő a tényleges telepítéssel. Emellett akkor is, ha egy helyi fájlból telepít egy sablont, a sablonban lévő beágyazott sablonokra mutató hivatkozásokat közvetlenül a Resource Manager kéri le, és a virtuálisgép-bővítmények által hivatkozott összetevők lekérése a telepített virtuális gépen futó virtuálisgép-ügynök által történik.

## <a name="next-steps"></a>Következő lépések

* [Azure Resource Manager a sablonra vonatkozó megfontolások](/azure-stack/user/azure-stack-develop-templates)
* [Ajánlott eljárások ARM-sablonokhoz](template-syntax.md)
