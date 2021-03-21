---
title: Folyamatos integráció és kézbesítés a szinapszis-munkaterülethez
description: Megtudhatja, hogyan használhatja a folyamatos integrációt és a kézbesítést a munkaterületen végrehajtott módosítások egy másik környezetből (fejlesztés, tesztelés, éles környezetben) való üzembe helyezéséhez.
services: synapse-analytics
author: liud
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: liud
ms.reviewer: pimorano
ms.openlocfilehash: de3738573bb9bb6f045a45d290c74ba9e6902a5e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103561957"
---
# <a name="continuous-integration-and-delivery-for-azure-synapse-workspace"></a>Folyamatos integráció és kézbesítés az Azure szinapszis-munkaterülethez

## <a name="overview"></a>Áttekintés

A folyamatos integráció (CI) a kód kiépítésének és tesztelésének automatizálása minden alkalommal, amikor egy csapattag véglegesíti a verziókövetés változásait. A folyamatos üzembe helyezés (CD) az a folyamat, amellyel több tesztelési vagy átmeneti környezetből lehet éles környezetbe felépíteni, tesztelni, konfigurálni és üzembe helyezni.

Az Azure szinapszis munkaterülete esetében a folyamatos integráció és a szállítás (CI/CD) minden entitást az egyik környezetből (fejlesztés, tesztelés, termelés) helyez át egy másikra. Ha a munkaterületet egy másik munkaterületre szeretné előléptetni, két részből áll: a munkaterület-erőforrások (készletek és munkaterületek) létrehozásához vagy frissítéséhez [Azure Resource Manager-sablonok](../../azure-resource-manager/templates/overview.md) használatával. az összetevők (SQL-parancsfájlok, jegyzetfüzetek, Spark-feladattípusok, folyamatok, adatforgalom stb.) áttelepíthetők a szinapszis CI/CD-eszközökkel az Azure DevOps-ben. 

Ebből a cikkből megtudhatja, hogyan automatizálható a szinapszis-munkaterület telepítése több környezetbe az Azure kiadási folyamat használatával.

## <a name="prerequisites"></a>Előfeltételek

-   A fejlesztéshez használt munkaterület a Studio git-tárházával lett konfigurálva, lásd: a [szinapszis Studio verziókövetés](source-control.md).
-   Egy Azure DevOps-projekt készült a kiadási folyamat futtatásához.

## <a name="set-up-a-release-pipelines"></a>Kiadási folyamatok beállítása

1.  Az [Azure DevOps](https://dev.azure.com/)nyissa meg a kiadáshoz létrehozott projektet.

1.  A lap bal oldalán válassza a **folyamatok**, majd a **kiadások** elemet.

    ![Folyamatok, kiadások kiválasztása](media/create-release-1.png)

1.  Válassza az **új folyamat** lehetőséget, vagy ha meglévő folyamatokkal rendelkezik, válassza az **új** , majd az **új kiadási folyamat** lehetőséget.

1.  Válassza ki az **üres** sablont.

    ![Üres feladatok kiválasztása](media/create-release-select-empty.png)

1.  A **szakasz neve** mezőben adja meg a környezet nevét.

1.  Válassza az összetevő **hozzáadása** lehetőséget, majd válassza ki a fejlesztési szinapszis Studióval konfigurált git-tárházat. Válassza ki a készletek és munkaterületek ARM-sablonjának kezeléséhez használt git-tárházat. Ha a GitHubot használja forrásként, létre kell hoznia egy szolgáltatási kapcsolódást a GitHub-fiókjához, és le kell kérnie a tárolókat. További információ a [szolgáltatás kapcsolatairól](/azure/devops/pipelines/library/service-endpoints) 

    ![Közzétételi ág hozzáadása](media/release-creation-github.png)

1.  Válassza ki az ARM-sablon ágat az erőforrások frissítéséhez. Az **alapértelmezett verziónál** válassza a **legutóbbi lehetőséget az alapértelmezett ág** lehetőségnél.

    ![ARM-sablon hozzáadása](media/release-creation-arm-branch.png)

1.  Válassza ki az adattár [közzétételi ágát](source-control.md#configure-publishing-settings) az **alapértelmezett ág** számára. Alapértelmezés szerint ez a közzétételi ág `workspace_publish` . Az **alapértelmezett verziónál** válassza a **legutóbbi lehetőséget az alapértelmezett ág** lehetőségnél.

    ![Összetevő hozzáadása](media/release-creation-publish-branch.png)

## <a name="set-up-a-stage-task-for-arm-resource-create-and-update"></a>Fázis-feladat beállítása az ARM-erőforrások létrehozásához és frissítéséhez 

Azure Resource Manager telepítési feladat hozzáadása erőforrások létrehozásához vagy frissítéséhez, beleértve a munkaterületet és a készleteket:

1. A fázis nézetben válassza a **fázis-feladatok megtekintése** lehetőséget.

    ![Szakasz nézet](media/release-creation-stage-view.png)

1. Hozzon létre egy új feladatot. Keresse meg az **ARM-sablon központi telepítését**, majd válassza a **Hozzáadás** lehetőséget.

1. A központi telepítési feladat területen válassza ki az előfizetést, az erőforráscsoportot és a célhelyet a cél munkaterülethez. Szükség esetén adja meg a hitelesítő adatokat.

1. A **művelet** listában válassza az **erőforráscsoport létrehozása vagy frissítése** lehetőséget.

1. Kattintson a három pont gombra (**...**) a **sablon** mező mellett. Tallózással keresse meg a cél munkaterület Azure Resource Manager sablonját

1. Válassza a **... lehetőséget.** a **sablon paramétereinek** mező mellett válassza ki a paramétereket tartalmazó fájlt.

1. Válassza a **... lehetőséget.** a **felülírási sablon paraméterei** mező mellett adja meg a kívánt paramétereket a cél munkaterülethez. 

1. Válassza a **növekményes** lehetőséget a **központi telepítési módhoz**.
    
    ![munkaterület és készletek üzembe helyezése](media/pools-resource-deploy.png)

1. Választható **Azure PowerShell** hozzáadása a munkaterület szerepkör-hozzárendelésének engedélyezéséhez és frissítéséhez. Ha a kiadási folyamattal hoz létre egy szinapszis-munkaterületet, a rendszer a folyamat egyszerű szolgáltatását adja hozzá alapértelmezett munkaterület-rendszergazdaként. A PowerShell futtatásával más fiókokhoz is hozzáférést biztosíthat a munkaterülethez. 
    
    ![engedély megadása](media/release-creation-grant-permission.png)

 > [!WARNING]
> A teljes üzembe helyezési módban az erőforráscsoporthoz tartozó, de az új Resource Manager-sablonban nem megadott erőforrások **törlődni** fognak. További információkért tekintse meg [Azure Resource Manager telepítési módokat](../../azure-resource-manager/templates/deployment-modes.md)

## <a name="set-up-a-stage-task-for-artifacts-deployment"></a>Fázis-feladat beállítása az összetevők üzembe helyezéséhez 

A [szinapszis munkaterület üzembe](https://marketplace.visualstudio.com/items?itemName=AzureSynapseWorkspace.synapsecicd-deploy) helyezési bővítményével más elemeket helyezhet üzembe a szinapszis munkaterületen, például ADATKÉSZLET, SQL-parancsfájl, jegyzetfüzet, Spark-feladatdefiníció, adatfolyam, folyamat, társított szolgáltatás, hitelesítő adatok és IR (Integration Runtime).  

1. A bővítmény keresése és beszerzése az **Azure DevOps piactéren**(https://marketplace.visualstudio.com/azuredevops) 

     ![Bővítmény beolvasása](media/get-extension-from-market.png)

1. Válasszon ki egy szervezetet a bővítmény telepítéséhez. 

     ![A bővítmény telepítése](media/install-extension.png)

1. Győződjön meg arról, hogy az Azure DevOps-folyamat egyszerű szolgáltatása az előfizetés engedéllyel rendelkezik, és a cél munkaterület munkaterület-rendszergazdája is hozzá van rendelve. 

1. Hozzon létre egy új feladatot. Keressen rá a **szinapszis munkaterület üzembe helyezésére**, majd válassza a **Hozzáadás** lehetőséget.

     ![Bővítmény hozzáadása](media/add-extension-task.png)

1.  A feladatban válassza a **... lehetőséget.** **a sablon mező mellett** válassza ki a sablonfájlt.

1. Válassza a **... lehetőséget.** a **sablon paramétereinek** mező mellett válassza ki a paramétereket tartalmazó fájlt.

1. Válassza ki a kapcsolatokat, az erőforráscsoportot és a cél munkaterület nevét. 

1. Válassza a **... lehetőséget.** a **felülírási sablon paraméterei** mező mellett adja meg a kívánt paramétereket a cél munkaterülethez. 

    ![Szinapszis-munkaterület üzembe helyezése](media/create-release-artifacts-deployment.png)

> [!IMPORTANT]
> A CI/CD-helyzetekben a különböző környezetekben lévő Integration Runtime (IR) típusnak azonosnak kell lennie. Ha például saját üzemeltetésű integrációs modult használ a fejlesztési környezetben, akkor ugyanazt az IR-t más környezetekben, például tesztelési és üzemi környezetben is önálló üzemeltetéssel kell eltárolni. Hasonlóképpen, ha több fázisban osztja meg az integrációs modulokat, az integrációs modulokat az összes környezetben, például a fejlesztés, a tesztelés és az éles környezetek szerint kell konfigurálni.

## <a name="create-release-for-deployment"></a>Kiadás létrehozása az üzembe helyezéshez 

Az összes módosítás mentése után kiválaszthatja, hogy a kiadás **létrehozása** lehetőséggel manuálisan hozzon létre egy kiadást. A kiadások létrehozásának automatizálásához tekintse meg az [Azure DevOps kiadási eseményindítók](/azure/devops/pipelines/release/triggers) című témakört.

   ![Válassza a kiadás létrehozása lehetőséget](media/release-creation-manually.png)

## <a name="use-custom-parameters-of-the-workspace-template"></a>A munkaterület-sablon egyéni paramétereinek használata 

Automatikus CI/CD-t használ, és az üzembe helyezés során módosítani kíván néhány tulajdonságot, de a tulajdonságok alapértelmezés szerint nem paraméteresen vannak kiválasztva. Ebben az esetben felülbírálhatja az alapértelmezett paraméter sablonját.

Az alapértelmezett paraméter-sablon felülbírálásához létre kell hoznia egy egyéni paraméter-sablont, egy **template-parameters-definition.js** nevű fájlt a git együttműködési ág gyökérkönyvtárában. Pontosan ezt a fájlnevet kell használnia. Az együttműködési ág közzétételét követően a szinapszis munkaterület beolvassa ezt a fájlt, és a konfigurációját használja a paraméterek létrehozásához. Ha nem található fájl, a rendszer az alapértelmezett paraméter-sablont használja.

### <a name="custom-parameter-syntax"></a>Egyéni paraméter szintaxisa

Az alábbi útmutató az egyéni paraméterek fájljának létrehozásához nyújt útmutatást:

* Adja meg a tulajdonság elérési útját a megfelelő entitás típusa mezőben.
* A tulajdonságnév beállítása `*` azt jelzi, hogy az összes tulajdonságot meg szeretné parametrizálja (csak az első szintre, nem rekurzív módon). Kivételeket is megadhat ehhez a konfigurációhoz.
* Egy tulajdonság értékének karakterláncként való megadása azt jelzi, hogy meg kívánja parametrizálja a tulajdonságot. Használja a következő formátumot: `<action>:<name>:<stype>`.
   *  `<action>` a következő karakterek egyike lehet:
      * `=` azt jelenti, hogy az aktuális értéket a paraméter alapértelmezett értékeként tárolja.
      * `-` azt jelenti, hogy nem tartja meg a paraméter alapértelmezett értékét.
      * `|` a Azure Key Vault titkos kódokhoz vagy kulcsokhoz tartozó titkok esetében különleges eset.
   * `<name>` a paraméter neve. Ha üres, akkor a tulajdonság nevét veszi fel. Ha az érték egy `-` karakterrel kezdődik, a név lerövidítve lesz. Például `AzureStorage1_properties_typeProperties_connectionString` lerövidítheti a következőt: `AzureStorage1_connectionString` .
   * `<stype>` a paraméter típusa. Ha a `<stype>` értéke üres, az alapértelmezett típus: `string` . Támogatott értékek:,,,, `string` `securestring` `int` `bool` `object` `secureobject` és `array` .
* Egy tömb megadása a fájlban azt jelzi, hogy a sablonban szereplő egyező tulajdonság egy tömb. A szinapszis megismétli a tömbben lévő összes objektumot a megadott definíció használatával. A második objektum, egy karakterlánc, a tulajdonság neve lesz, amely az egyes iterációk paraméterének neveként szerepel.
* Egy definíció nem lehet egy adott erőforrás-példányra jellemző. Bármely definíció az adott típusú összes erőforrásra vonatkozik.
* Alapértelmezés szerint az összes biztonságos karakterlánc, például a Key Vault titkos kódok, valamint a biztonságos karakterláncok, például a kapcsolati karakterláncok, kulcsok és tokenek paraméterei.

### <a name="parameter-template-definition-samples"></a>A paraméterérték definíciós mintái 

Íme egy példa arra, hogyan néz ki a paraméter-sablon definíciója:

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
Íme egy magyarázat arról, hogy az előző sablon hogyan épül fel, az erőforrástípus szerinti bontásban.

#### <a name="notebooks"></a>Notebooks 

* Az elérési út bármely tulajdonsága az `properties/bigDataPool/referenceName` alapértelmezett értékkel van paraméterben. Az egyes jegyzetfüzet-fájlokhoz csatolt Spark-készletet is parametrizálja. 

#### <a name="sql-scripts"></a>SQL-parancsfájlok 

* Az elérési úthoz tartozó tulajdonságok (poolName és databaseName) `properties/content/currentConnection` karakterláncként vannak paraméterként a sablon alapértelmezett értékei nélkül. 

#### <a name="pipelines"></a>Pipelines

* Az elérési út bármely tulajdonsága `activities/typeProperties/waitTimeInSeconds` paraméterrel van elfoglalva. A folyamatokban lévő minden olyan tevékenység, amelynek a neve `waitTimeInSeconds` (például a `Wait` tevékenység), egy alapértelmezett névvel van ellátva. A Resource Manager-sablonban azonban nem szerepel alapértelmezett érték. A Resource Manager üzembe helyezése során kötelezően megadandó adatok lesznek.
* Hasonlóképpen, egy nevű tulajdonság `headers` (például egy `Web` tevékenység) paraméterének típusa `object` (Object) van. Alapértelmezett értékkel rendelkezik, amely megegyezik a forrás-előállítóval megegyező értékkel.

#### <a name="integrationruntimes"></a>IntegrationRuntimes

* Az elérési út alatti összes tulajdonság a `typeProperties` megfelelő alapértelmezett értékekkel van ellátva. Például két tulajdonság van a `IntegrationRuntimes` típus tulajdonságainál: `computeProperties` és `ssisProperties` . Mindkét tulajdonság típusa a megfelelő alapértelmezett értékekkel és típusokkal (objektummal) jön létre.

#### <a name="triggers"></a>Triggerek

* A `typeProperties` rendszerben a két tulajdonság paraméteres. Az első a `maxConcurrency` , amely az alapértelmezett értékkel van megadva, és típusa `string` . Az alapértelmezett paraméter neve `<entityName>_properties_typeProperties_maxConcurrency` .
* A `recurrence` tulajdonság paraméterrel is rendelkezik. Ebben az esetben az adott szinten lévő összes tulajdonságot karakterláncként kell megadni, alapértelmezett értékekkel és paraméterek nevével. Kivételt képez a `interval` tulajdonság, amely típusként van paraméterként `int` . A paraméter neve utótaggal van ellátva `<entityName>_properties_typeProperties_recurrence_triggerSuffix` . Hasonlóképpen, a `freq` tulajdonság egy karakterlánc, és karakterláncként van paraméterként. A tulajdonság azonban `freq` alapértelmezett érték nélkül van paraméterben. A név rövidítve és utótaggal van elnevezve. Például: `<entityName>_freq`.

#### <a name="linkedservices"></a>LinkedServices

* A társított szolgáltatások egyediek. Mivel a társított szolgáltatások és adatkészletek sokféle típusúak, a típus-specifikus testreszabást is megadhatja. Ebben a példában az összes típusú társított szolgáltatás esetében `AzureDataLakeStore` egy adott sablon lesz alkalmazva. Minden más (a szolgáltatáson keresztül `*` ) egy másik sablon lesz alkalmazva.
* A `connectionString` tulajdonság értéke paraméterként fog megjelenni `securestring` . Nem rendelkezik alapértelmezett értékkel. Egy rövidített paraméter neve lesz, amely a (z) utótaggal van ellátva `connectionString` .
* A tulajdonság `secretAccessKey` egy `AzureKeyVaultSecret` (például egy Amazon S3-beli társított szolgáltatás) esetében történik. Automatikusan Azure Key Vault titokként van konfigurálva, és a konfigurált kulcstartóból beolvasva. Saját maga is parametrizálja a kulcstartót.

#### <a name="datasets"></a>Adathalmazok

* Bár a típus-specifikus Testreszabás elérhető az adatkészletekhez, a konfigurációt explicit módon nem lehet konfigurálni \* . Az előző példában az összes adatkészlet-tulajdonság `typeProperties` paraméterrel van elfoglalva.


## <a name="best-practices-for-cicd"></a>Ajánlott eljárások CI/CD-hez

Ha git-integrációt használ a szinapszis-munkaterülettel, és rendelkezik egy CI/CD-folyamattal, amely a változásokat a fejlesztésből teszteli, majd éles környezetbe helyezi, javasoljuk az alábbi ajánlott eljárásokat:

-   **Git-integráció**. Csak a fejlesztési szinapszis munkaterületet konfigurálja a git-integrációval. A teszt-és éles munkaterületek változásai a CI/CD-n keresztül telepíthetők, és nincs szükség git-integrációra.
-   **Készletek előkészítése az összetevők migrálása előtt**. Ha a fejlesztői munkaterületen a készletekhez csatolt SQL-parancsfájl vagy jegyzetfüzet van, akkor a rendszer a különböző környezetekben lévő készletek azonos nevét is elvárta. 
-   **Infrastruktúra-kód (IaC)**. Az infrastruktúra (hálózatok, virtuális gépek, terheléselosztó és a kapcsolatok topológiája) kezelése egy leíró modellben ugyanazt a verziószámozást használja, mint a DevOps-csapat a forráskódot használja. 
-   **Mások**. Lásd: [ajánlott eljárások az ADF](../../data-factory/continuous-integration-deployment.md#best-practices-for-cicd) -összetevőkhöz

## <a name="troubleshooting-artifacts-deployment"></a>Összetevők telepítésének hibaelhárítása 

### <a name="use-the-synapse-workspace-deployment-task"></a>A szinapszis munkaterület telepítési feladatának használata

A Szinapszisban számos olyan összetevő van, amely nem ARM-erőforrás. Ez eltér a Azure Data Factorytól. Az ARM-sablon telepítési feladata nem fog megfelelően működni a szinapszis-összetevők üzembe helyezéséhez
 
### <a name="unexpected-token-error-in-release"></a>Váratlan jogkivonat-hiba történt a kiadásban

Ha a paraméter értéke nem kerül megmenekülésre, a kiadási folyamat nem fogja elemezni a fájlt, és a "váratlan token" hibaüzenetet eredményezi. Javasoljuk, hogy felülbírálja a paramétereket, vagy használja az Azure kulcstartót a paraméterek értékeinek lekéréséhez. A kettős Escape-karakterek is megkerülő megoldásként is használhatók.
