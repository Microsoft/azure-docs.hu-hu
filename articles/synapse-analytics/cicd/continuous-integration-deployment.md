---
title: Folyamatos integráció és teljesítés a Synapse-munkaterülethez
description: Megtudhatja, hogyan helyezhet üzembe módosításokat a munkaterületen a folyamatos integráció és teljesítés használatával az egyik környezetből (fejlesztés, tesztelés, éles környezet) egy másikba.
author: liudan66
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: liud
ms.reviewer: pimorano
ms.openlocfilehash: 833478d956560c981bd6cc3ba03b48bb602f563c
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739673"
---
# <a name="continuous-integration-and-delivery-for-azure-synapse-workspace"></a>Folyamatos integráció és teljesítés Azure Synapse munkaterülethez

## <a name="overview"></a>Áttekintés

A folyamatos integráció (CONTINUOUS) a kód buildelésének és tesztelésének automatizálása minden alkalommal, amikor egy csapattag módosításokat véglegesíteni fog a verzióvezérlésben. A folyamatos üzembe helyezés (CONTINUOUS Deployment, CD) az a folyamat, amely során több tesztelési vagy átmeneti környezetből hoznak létre, tesztelnek, konfigurálnak és helyeznek üzembe éles környezetben.

Egy új Azure Synapse Analytics a folyamatos integráció és teljesítés (CI/CD) az összes entitást áthelyezi az egyik környezetből (fejlesztés, tesztelés, éles környezet) egy másikba. A munkaterület másik munkaterületre való megléptetése két részből áll. Először is használjon [Azure Resource Manager sablont (ARM-sablont)](../../azure-resource-manager/templates/overview.md) a munkaterület-erőforrások (készletek és munkaterületek) létrehozásához vagy frissítéséhez. Ezt követően összetevők (SQL-szkriptek, jegyzetfüzetek, Spark-feladatdefiníciók, folyamatok, adatkészletek, adatfolyamok stb.) áttelepítése az Azure Synapse Analytics CI/CD-eszközökkel az Azure DevOpsban. 

Ez a cikk azt ismerteti, hogyan használható egy Azure DevOps-kiadási folyamat egy virtuális Azure Synapse több környezetben való üzembe helyezésének automatizálásra.

## <a name="prerequisites"></a>Előfeltételek

Ezeknek az előfeltételeknek és konfigurációknak meg kell lennie, hogy automatizálni lehet egy Azure Synapse környezetben való üzembe helyezését.

### <a name="azure-devops"></a>Azure DevOps

- Előkészítettünk egy Azure DevOps-projektet a kiadási folyamat futtatásához.
- Adjon meg minden olyan felhasználónak, aki a szervezeti szinten ["Alapszintű"](/azure/devops/organizations/accounts/add-organization-users?view=azure-devops&tabs=preview-page)hozzáférést fog ellenőrizni, hogy látják az adott tárat.
- Tulajdonosi jogosultságok megadása a Azure Synapse számára.
- Győződjön meg arról, hogy létrehozott egy saját fejlesztésű Azure DevOps virtuálisgép-ügynököt, vagy az Azure DevOps által üzemeltetett ügynököt használja.
- Engedély egy [Azure Resource Manager-szolgáltatáskapcsolat létrehozásához az erőforráscsoporthoz.](/azure/devops/pipelines/library/service-endpoints?view=azure-devops&tabs=yaml)
- A Azure Active Directory (Azure AD) rendszergazdájának telepítenie kell az Azure DevOps Synapse Workspace Deployment Agent bővítményt az [Azure DevOps-szervezetben.](/azure/devops/marketplace/install-extension)
- Hozzon létre vagy jelöln meg egy meglévő szolgáltatásfiókot, hogy a folyamat a következőként fusson: . Szolgáltatásfiók helyett személyes hozzáférési jogkivonatot is használhat, de a folyamatok nem fognak működni a felhasználói fiók törlése után.

### <a name="azure-active-directory"></a>Azure Active Directory

- Az Azure AD-ban hozzon létre egy egyszerű szolgáltatást az üzembe helyezéshez. A Synapse Workspace Deployment (Synapse-munkaterület üzembe helyezése) feladat nem támogatja a felügyelt identitások az 1. és korábbi verziókban való használatát.
- Ehhez a művelethez Azure AD-rendszergazdai jogosultságok szükségesek.

### <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

> [!NOTE]
> Ugyanezen folyamat, ARM-sablon vagy az Azure CLI használatával is automatizálhatja és üzembe helyezheti ezeket az előfeltételeket, de a folyamatot ez a cikk nem ismerteti.

- A fejlesztéshez használt "forrás" munkaterületet egy Git-adattárakhoz kell konfigurálni a Synapse Studio. További információ: [Forrásvezérlő a Synapse Studio.](source-control.md#configuration-method-2-manage-hub)

- Egy üres munkaterület, amelybe az üzembe helyezést kell helyezni. Az üres munkaterület beállítása:

  1. Hozzon létre egy Azure Synapse Analytics munkaterületet.
  1. Adjon jogosultságot a virtuálisgép-ügynöknek és a szolgáltatásnév közreműködői jogosultságának ahhoz az erőforráscsoporthoz, amelyben az új munkaterület található.
  1. Az új munkaterületen ne konfigurálja a Git-adattár kapcsolatát.
  1. A Azure Portal keresse meg az új Azure Synapse Analytics munkaterületet, és adja meg magának és annak, aki a munkaterület tulajdonosi jogosultságával fogja futtatni Azure Synapse Analytics Azure DevOps-folyamatot. 
  1. Adja hozzá az Azure DevOps virtuálisgép-ügynököt és az egyszerű szolgáltatást a munkaterület Közreműködő szerepköréhez (ennek örököltnek kell lennie, de ellenőrizze, hogy az-e).
  1. A Azure Synapse Analytics a **Studio**  >  **Manage**  >  **IAM (A Studio IAM kezelése) munkaterületén.** Adja hozzá az Azure DevOps virtuálisgép-ügynököt és az egyszerű szolgáltatást a munkaterület-rendszergazdák csoportjához.
  1. Nyissa meg a munkaterülethez használt tárfiókot. Az IAM-ban adja hozzá a virtuálisgép-ügynököt és az egyszerű szolgáltatást a Select Storage Blob Data Contributor (Tárolóblobadatok közreműködője kiválasztása) szerepkörhöz.
  1. Hozzon létre egy kulcstartót a támogatási előfizetésben, és győződjön meg arról, hogy a meglévő munkaterület és az új munkaterület is legalább GET és LIST engedéllyel rendelkezik a tárolóhoz.
  1. Ahhoz, hogy az automatikus üzembe helyezés működjön, győződjön meg arról, hogy a csatolt szolgáltatásokban megadott kapcsolati sztringek a kulcstartóban vannak.

### <a name="additional-prerequisites"></a>További előfeltételek
 
 - A Spark-készletek és a saját üzemeltetett integrációs környezetek nem egy folyamatban vannak létrehozva. Ha saját maga által üzemeltetett integrációskörnyezetet használó csatolt szolgáltatással dolgozik, manuálisan hozza létre azt az új munkaterületen.
 - Ha jegyzetfüzeteket fejleszt, és csatlakoztatja őket egy Spark-készlethez, hozza létre újra a Spark-készletet a munkaterületen.
 - A Környezetben nem létező Spark-készlethez csatolt jegyzetfüzetek nem fognak üzembe helyezni.
 - A Spark-készlet nevének mindkét munkaterületen azonosnak kell lennie.
 - Az összes adatbázisnak, SQL-készletnek és más erőforrásnak azonos nevet ad mindkét munkaterületen.
 - Ha az üzembe helyezési kísérlet során a kiépített SQL-készletek fel vannak függesztve, az üzembe helyezés meghiúsulhat.

További információ: [CI CD Azure Synapse Analytics 4. rész – A kiadási folyamat.](https://techcommunity.microsoft.com/t5/data-architecture-blog/ci-cd-in-azure-synapse-analytics-part-4-the-release-pipeline/ba-p/2034434) 


## <a name="set-up-a-release-pipeline"></a>Kiadási folyamat beállítása

1.  Az [Azure DevOpsban](https://dev.azure.com/)nyissa meg a kiadáshoz létrehozott projektet.

1.  A lap bal oldalán válassza a **Folyamatok,** majd a **Kiadások lehetőséget.**

    ![Folyamatok, kiadások kiválasztása](media/create-release-1.png)

1.  Válassza **az Új folyamat** lehetőséget, vagy ha  már van meglévő folyamat, válassza az Új, majd az **Új kiadási folyamat lehetőséget.**

1.  Válassza az **Üres feladat sablont.**

    ![Válassza az Üres feladat lehetőséget](media/create-release-select-empty.png)

1.  A Fázis **neve mezőbe** írja be a környezet nevét.

1.  Válassza **az Összetevő hozzáadása** lehetőséget, majd válassza ki a fejlesztési tárházhoz konfigurált git-Synapse Studio. Válassza ki azt a Git-adattárat, amely a készletek és munkaterületEK ARM-sablonjának kezeléséhez használt. Ha a GitHubot használja forrásként, létre kell hoznia egy szolgáltatáskapcsolatot a GitHub-fiókhoz és a lekért adattárakhoz. További információ a [szolgáltatáskapcsolatról](/azure/devops/pipelines/library/service-endpoints) 

    ![Közzétételi ág hozzáadása](media/release-creation-github.png)

1.  Válassza ki az ARM-sablon ágát az erőforrások frissítéséhez. Az Alapértelmezett **verzió beállításnál** válassza a **Legutóbbi lehetőséget az alapértelmezett ágban.**

    ![ARM-sablon hozzáadása](media/release-creation-arm-branch.png)

1.  Válassza ki [az adattár](source-control.md#configure-publishing-settings) közzétételi ágát az Alapértelmezett **ághoz.** Alapértelmezés szerint ez a közzétételi ág `workspace_publish` a következő: . Az Alapértelmezett **verzió beállításnál** válassza a **Legutóbbi lehetőséget az alapértelmezett ágban.**

    ![Összetevő hozzáadása](media/release-creation-publish-branch.png)

## <a name="set-up-a-stage-task-for-an-arm-template-to-create-and-update-resource"></a>Fázisfeladat beállítása ARM-sablonhoz erőforrás létrehozásához és frissítéséhez 

Ha rendelkezik ARM-sablonnal egy erőforrás, például egy Azure Synapse-munkaterület, Spark- és SQL-készletek vagy kulcstartó üzembe helyezéséhez, adjon hozzá egy Azure Resource Manager Deployment (Üzembe helyezés) feladatot ezen erőforrások létrehozásához vagy frissítéséhez:

1. A fázisnézetben válassza a **Fázisfeladatok megtekintése lehetőséget.**

    ![Fázis nézet](media/release-creation-stage-view.png)

1. Hozzon létre egy új feladatot. Keressen rá az **ARM-sablon üzembe helyezésére,** majd válassza a **Hozzáadás lehetőséget.**

1. Az Üzembe helyezés feladatban válassza ki a cél munkaterülethez az előfizetést, az erőforráscsoportot és a helyet. Szükség esetén adja meg a hitelesítő adatokat.

1. A Művelet **listában** válassza az Erőforráscsoport létrehozása **vagy frissítése lehetőséget.**

1. Válassza a Sablon mező melletti három  pont **(...)** gombot. Keresse meg a Azure Resource Manager munkaterület sablonját

1. Válassza **a ...** a **Sablonparaméterek mező** mellett a paraméterfájl választáshoz.

1. Válassza **a ...** a **Sablonparaméterek felülbírálása mező** mellett, és adja meg a kívánt paraméterértékeket a cél munkaterülethez. 

1. Az Üzembe **helyezési** mód beállításhoz válassza a **Növekményes lehetőséget.**
    
    ![munkaterület és készletek üzembe helyezése](media/pools-resource-deploy.png)

1. (Nem kötelező) Adja **Azure PowerShell** a munkaterület szerepkör-hozzárendelésének megadásához és frissítéséhez. Ha kiadási folyamatot használ egy Synapse-munkaterület létrehozásához, a folyamat egyszerű szolgáltatása lesz hozzáadva alapértelmezett munkaterület-rendszergazdaként. A PowerShell futtatásával hozzáférést adhat más fiókoknak a munkaterülethez. 
    
    ![engedély megadása](media/release-creation-grant-permission.png)

 > [!WARNING]
> A Teljes üzembe helyezés módban az erőforráscsoportban található, de az új erőforráscsoportban nem megadott Resource Manager **törlődnek.** További információért tekintse meg az üzembehely [Azure Resource Manager módokat.](../../azure-resource-manager/templates/deployment-modes.md)

## <a name="set-up-a-stage-task-for-synapse-artifacts-deployment"></a>Fázisfeladat beállítása Synapse-összetevők üzembe helyezéséhez 

A [Synapse-munkaterület](https://marketplace.visualstudio.com/items?itemName=AzureSynapseWorkspace.synapsecicd-deploy) üzembe helyezési bővítményének használatával más elemeket is üzembe helyezhet a Synapse-munkaterületen, például adatkészletet, SQL-szkriptet, jegyzetfüzetet, Spark-feladatdefiníciót, adatfolyamot, folyamatot, csatolt szolgáltatást, hitelesítő adatokat és integrációs Integration Runtime.  

1. Keresse meg és szerezze be a bővítményt az **Azure DevOps Marketplace-ről**(https://marketplace.visualstudio.com/azuredevops) 

     ![Bővítmény lekérte](media/get-extension-from-market.png)

1. Válasszon ki egy szervezetet a bővítmény telepítéséhez. 

     ![A bővítmény telepítése](media/install-extension.png)

1. Győződjön meg arról, hogy az Azure DevOps-folyamat szolgáltatásnév rendelkezik előfizetési engedéllyel, és munkaterület-rendszergazdaként is hozzá van rendelve a cél munkaterülethez. 

1. Hozzon létre egy új feladatot. Keresse meg a **Synapse-munkaterület üzemelő példányát,** majd válassza a **Hozzáadás lehetőséget.**

     ![Bővítmény hozzáadása](media/add-extension-task.png)

1.  A feladatban válassza a **...** a Sablon **mező mellett** a sablonfájl választáshoz.

1. Válassza **a ...** a **Sablonparaméterek mező** mellett a paraméterfájl választáshoz.

1. Válassza ki a kapcsolatot, az erőforráscsoportot és a cél munkaterület nevét. 

1. Válassza **a ...** a Sablon **paramétereinek** felülbírálása mező mellett, és adja meg a cél munkaterület kívánt paraméterértékét, beleértve a csatolt szolgáltatásokban használt kapcsolati sztringeket és fiókkulcsokat. [További információért kattintson ide] (https://techcommunity.microsoft.com/t5/data-architecture-blog/ci-cd-in-azure-synapse-analytics-part-4-the-release-pipeline/ba-p/2034434)

    ![Synapse-munkaterület üzembe helyezése](media/create-release-artifacts-deployment.png)

> [!IMPORTANT]
> CI/CD-forgatókönyvekben az integrációskörnyezet (IR) típusának különböző környezetekben azonosnak kell lennie. Ha például a fejlesztői környezetben saját maga által üzemeltetett integrációs integrációs kiszolgálót üzemeltet, akkor ugyanezen integrációs kiszolgálónak más környezetekben, például tesztelési és éles környezetekben is saját környezetben üzemeltetettnek kell lennie. Hasonlóképpen, ha több fázisban oszt meg integrációskörnyezeteket, akkor az integrációs runtime-okat összekapcsolt, saját futtatottként kell konfigurálnia minden környezetben, például a fejlesztésben, a tesztelésben és az éles környezetben.

## <a name="create-release-for-deployment"></a>Kiadás létrehozása üzembe helyezéshez 

Az összes módosítás mentése után a Kiadás létrehozása lehetőséget **választva** manuálisan hozhat létre kiadást. A kiadások létrehozásának automatizálásával lásd: [Az Azure DevOps kiadási eseményindítói](/azure/devops/pipelines/release/triggers)

   ![Válassza a Kiadás létrehozása lehetőséget](media/release-creation-manually.png)

## <a name="use-custom-parameters-of-the-workspace-template"></a>A munkaterület-sablon egyéni paramétereinek használata 

Automatizált CI/CD-t használ, és néhány tulajdonságot módosítani szeretne az üzembe helyezés során, de a tulajdonságok alapértelmezés szerint nem paraméterezettek. Ebben az esetben felülírhatja az alapértelmezett paramétersablont.

Az alapértelmezett paramétersablon felülbírálása érdekében létre kell hoznia egy egyéni paramétersablont, egy **template-parameters-definition.jsnevű** fájlt a Git-együttműködési ág gyökérmappában. Pontosan ezt a fájlnevet kell használnia. Az együttműködési ágból való közzétételkor a Synapse-munkaterület beolvassa ezt a fájlt, és annak konfigurációját használja a paraméterek létrehozásához. Ha nem található fájl, a rendszer az alapértelmezett paramétersablont használja.

### <a name="custom-parameter-syntax"></a>Egyéni paraméterszintaxis

Az alábbiakban néhány irányelv található az egyéni paraméterfájl létrehozásához:

* Adja meg a tulajdonság elérési útját a megfelelő entitástípus alatt.
* A tulajdonságnév beállítása azt jelzi, hogy az alatta található összes tulajdonságot paraméteresíteni szeretné (csak az első szintre, nem `*` rekurzívan). Kivételeket is meg lehet adni ehhez a konfigurációhoz.
* Ha egy tulajdonság értékét sztringként adja meg, az azt jelenti, hogy paramétert szeretne a tulajdonsághoz. Használja a következő formátumot: `<action>:<name>:<stype>`.
   *  `<action>` A a következő karakterek egyike lehet:
      * `=` A azt jelenti, hogy tartsa meg az aktuális értéket a paraméter alapértelmezett értékeként.
      * `-` A azt jelenti, hogy ne tartsa meg a paraméter alapértelmezett értékét.
      * `|` A a titkos kulcsok esetében speciális eset a Azure Key Vault sztringek vagy kulcsok esetében.
   * `<name>` A a paraméter neve. Ha üres, a tulajdonság nevét veszi fel. Ha az érték egy karakterrel kezdődik, a `-` név rövidül lesz. A például `AzureStorage1_properties_typeProperties_connectionString` a rövid `AzureStorage1_connectionString` lesz.
   * `<stype>` A a paraméter típusa. Ha `<stype>` az üres, az alapértelmezett típus a `string` . Támogatott értékek: `string` , , , , és `securestring` `int` `bool` `object` `secureobject` `array` .
* Egy tömb fájlban való megadása azt jelzi, hogy a sablon egyező tulajdonsága egy tömb. A Synapse végighaladva végighaladva a tömb összes objektumát a megadott definíció használatával. A második objektum, a sztring lesz a tulajdonság neve, amely az egyes iterációk paraméterének neveként használatos.
* Egy definíció nem lehet erőforráspéldányra jellemző. Minden definíció az adott típusú összes erőforrásra vonatkozik.
* Alapértelmezés szerint az összes biztonságos sztring, például a titkos Key Vault és a biztonságos sztringek, például a kapcsolati sztringek, a kulcsok és a jogkivonatok paraméteresek.

### <a name="parameter-template-definition-samples"></a>Paramétersablonok definíciós mintái 

Az alábbi példa egy paramétersablon-definíciót mutat be:

```json
{
"Microsoft.Synapse/workspaces/notebooks": {
        "properties":{
            "bigDataPool":{
                "referenceName": "="
            }
        }
    },
    "Microsoft.Synapse/workspaces/sqlscripts": {
     "properties": {
         "content":{
             "currentConnection":{
                    "*":"-"
                 }
            } 
        }
    },
    "Microsoft.Synapse/workspaces/pipelines": {
        "properties": {
            "activities": [{
                 "typeProperties": {
                    "waitTimeInSeconds": "-::int",
                    "headers": "=::object"
                }
            }]
        }
    },
    "Microsoft.Synapse/workspaces/integrationRuntimes": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.Synapse/workspaces/triggers": {
        "properties": {
            "typeProperties": {
                "recurrence": {
                    "*": "=",
                    "interval": "=:triggerSuffix:int",
                    "frequency": "=:-freq"
                },
                "maxConcurrency": "="
            }
        }
    },
    "Microsoft.Synapse/workspaces/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                     "*": "="
                }
            }
        },
        "AzureDataLakeStore": {
            "properties": {
                "typeProperties": {
                    "dataLakeStoreUri": "="
                }
            }
        }
    },
    "Microsoft.Synapse/workspaces/datasets": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    }
}
```

Az előző sablon felépítésének magyarázata az erőforrástípusra bontva.

#### <a name="notebooks"></a>Notebooks 

* Az útvonal bármely `properties/bigDataPool/referenceName` tulajdonsága paraméteres lesz az alapértelmezett értékkel. Az egyes jegyzetfüzet-fájlokhoz csatolt Spark-készleteket paraméterezheti. 

#### <a name="sql-scripts"></a>SQL-szkriptek 

* Az elérési út tulajdonságai (poolName és databaseName) sztringekként vannak paraméterzve a sablon alapértelmezett `properties/content/currentConnection` értékei nélkül. 

#### <a name="pipelines"></a>Pipelines

* Az útvonal bármely `activities/typeProperties/waitTimeInSeconds` tulajdonsága paraméteres. A folyamat minden olyan tevékenysége, amely rendelkezik nevű kódszintű tulajdonsággal (például a tevékenység), számként lesz paraméterizálva, `waitTimeInSeconds` `Wait` alapértelmezett névvel. A sablonban azonban nem lesz alapértelmezett Resource Manager. Ez kötelező bemenet lesz a Resource Manager során.
* Hasonlóképpen, a nevű tulajdonság (például egy tevékenységben) paramétere a `headers` `Web` type `object` (Object) típussal van megadva. Alapértelmezett értékkel rendelkezik, amely megegyezik a forrás-előállító értékével.

#### <a name="integrationruntimes"></a>IntegrationRuntimes

* Az elérési út alatt található összes `typeProperties` tulajdonság paramétere a megfelelő alapértelmezett értékekkel van megadva. A típustulajdonságok alatt például két tulajdonság `IntegrationRuntimes` van: `computeProperties` és `ssisProperties` . Mindkét tulajdonságtípus a megfelelő alapértelmezett értékekkel és típusokkal (Objektum) jön létre.

#### <a name="triggers"></a>Triggerek

* A `typeProperties` alatt két tulajdonság paraméteres. Az első a , amely alapértelmezett értékkel van megadva, és `maxConcurrency` `string` típusú. Ez az alapértelmezett `<entityName>_properties_typeProperties_maxConcurrency` paraméternév.
* A `recurrence` tulajdonság paraméteres. Alatta az adott szinten található összes tulajdonságot sztringként, alapértelmezett értékekkel és paraméternevekkel kell paraméterként megadni. Ez alól kivételt képez `interval` a tulajdonság, amely típusként van paraméterizálva. `int` A paraméter neve a utótaggal van `<entityName>_properties_typeProperties_recurrence_triggerSuffix` megadva. Hasonlóképpen, `freq` a tulajdonság egy sztring, és sztringként van paraméterizálva. A tulajdonság `freq` azonban az alapértelmezett érték nélkül paraméterezett. A név rövid és utótaggal van megszűkülve. Például: `<entityName>_freq`.

#### <a name="linkedservices"></a>LinkedServices

* A csatolt szolgáltatások egyediek. Mivel a csatolt szolgáltatások és adatkészletek típusok széles skáláját biztosítják, típusspecifikus testreszabást is nyújthat. Ebben a példában a típusú összes összekapcsolt szolgáltatásra `AzureDataLakeStore` egy adott sablon lesz alkalmazva. Az összes többihez (a `*` használatával) egy másik sablon lesz alkalmazva.
* A `connectionString` tulajdonság paraméteres lesz `securestring` értékként. Nem lesz alapértelmezett értéke. Ez egy rövid paraméternévvel fog rendelkezik, amely a utótaggal `connectionString` rendelkezik.
* A tulajdonság `secretAccessKey` véletlenül egy `AzureKeyVaultSecret` (például egy Amazon S3-hez kapcsolódó szolgáltatásban). A rendszer automatikusan paraméteres titkos kulcsként Azure Key Vault, és lekéri a konfigurált kulcstartóból. Magát a kulcstartót is paraméterezheti.

#### <a name="datasets"></a>Adathalmazok

* Bár az adatkészletek típusspecifikus testreszabást is biztosítanak, a konfigurációt anélkül is meg lehet adni, hogy \* külön -szintű konfigurációra volna szükség. Az előző példában a alatt az összes `typeProperties` adatkészlet-tulajdonság paraméteres.


## <a name="best-practices-for-cicd"></a>Ajánlott eljárások a CI/CD-hez

Ha Git-integrációt használ az Azure Synapse-munkaterülettel, és olyan CI-/CD-folyamatával dolgozik, amely a módosításokat a fejlesztésből a tesztelésbe, majd az éles környezetbe áthelyezi, az alábbi ajánlott eljárásokat javasoljuk:

-   **Git-integráció.** Csak a fejlesztési és Azure Synapse Konfigurálja Git-integrációval. A teszt- és éles munkaterületek módosításai a CI/CD-n keresztül vannak telepítve, és nincs szükség Git-integrációra.
-   **Készítse elő a készleteket az összetevők migrálása előtt.** Ha a fejlesztési munkaterületen lévő készletekhez SQL-szkript vagy -jegyzetfüzet van csatolva, a különböző környezetekben lévő készletek neve azonos lesz. 
-   **Infrastruktúra kódként (IaC).** Az infrastruktúra (hálózatok, virtuális gépek, terheléselosztók és kapcsolati topológia) egy leíró modellben való kezelése ugyanazt a verziókezelést használja, mint a DevOps csapata a forráskódhoz. 
-   **Egyéb :**. Tekintse meg [az ADF-összetevők ajánlott eljárásait](../../data-factory/continuous-integration-deployment.md#best-practices-for-cicd)

## <a name="troubleshooting-artifacts-deployment"></a>Összetevők üzembe helyezésének hibaelhárítása 

### <a name="use-the-azure-synapse-analytics-workspace-deployment-task"></a>A Azure Synapse Analytics üzembe helyezési feladat használata

A Azure Synapse Analytics számos olyan összetevő van, amely nem ARM-erőforrás. Ez eltér a Azure Data Factory. Az ARM-sablon üzembe helyezési feladata nem fog megfelelően működni a Azure Synapse Analytics üzembe helyezéséhez.
 
### <a name="unexpected-token-error-in-release"></a>Váratlan jogkivonathiba a kiadásban

Ha a paraméterfájl olyan paraméterértékekkel rendelkezik, amelyek nem oldódtak fel, a kiadási folyamat nem fogja tudni elemezni a fájlt, és "váratlan token" hibát generál. Javasoljuk, hogy bírálja felül a paramétereket, vagy használja Azure Key Vault paraméterértékek lekérésére. Áthidaló megoldásként kettős escape-karaktereket is használhat.
