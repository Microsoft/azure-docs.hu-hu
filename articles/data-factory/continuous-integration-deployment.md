---
title: Folyamatos integráció és teljesítés az Azure Data Factoryben
description: Megtudhatja, hogyan használhatja a folyamatos integrációt és a szállítást, hogy Data Factory folyamatokat helyezzen át egy környezetből (fejlesztés, tesztelés, termelés) egy másikra.
ms.service: data-factory
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: 2176e74c46dc395672701555624aceb1b9563358
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104592115"
---
# <a name="continuous-integration-and-delivery-in-azure-data-factory"></a>Folyamatos integráció és teljesítés az Azure Data Factoryben

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>Áttekintés

A folyamatos integráció az a gyakorlat, amellyel a rendszer automatikusan és a lehető leghamarabb teszteli a kód egyes módosításait. A folyamatos teljesítés a folyamatos integráció során zajló tesztelést követi, és egy előkészítési vagy éles rendszerbe küldi le a módosításokat.

Az Azure Data Factoryben a folyamatos integráció és teljesítés (CI/CD) a Data Factory-folyamatoknak az egyik (fejlesztési, tesztelési, éles) környezetből egy másikba történő áthelyezését jelenti. A Azure Data Factory [Azure Resource Manager sablonokat](../azure-resource-manager/templates/overview.md) használ a különböző ADF-entitások (folyamatok, adatkészletek, adatforgalom stb.) konfigurációjának tárolásához. Két javasolt módszer áll rendelkezésre az adatelőállító más környezetbe való előléptetéséhez:

-    Automatikus üzembe helyezés a Data Factory Azure- [folyamatokkal](/azure/devops/pipelines/get-started/what-is-azure-pipelines) való integrálásával
-    Manuálisan tölthet fel egy Resource Manager-sablont a Data Factory UX-integrációval Azure Resource Manager használatával.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cicd-lifecycle"></a>CI/CD-életciklus

Alább látható egy példa az Azure Repos git szolgáltatással konfigurált Azure-beli adatgyár CI/CD-életciklusának áttekintésére. A git-adattárak konfigurálásával kapcsolatos további információkért lásd: [a verziókövetés a Azure Data Factory](source-control.md).

1.  Egy fejlesztési célú adatelőállítót hoz létre és konfigurál az Azure Repos git szolgáltatással. Minden fejlesztőnek jogosultnak kell lennie Data Factory erőforrások, például folyamatok és adatkészletek létrehozásához.

1.  A fejlesztő [létrehoz egy szolgáltatási ágat](source-control.md#creating-feature-branches) , amely megváltoztatja a módosításokat. A legutóbbi módosításokkal hibakeresést folytatnak a folyamaton. A folyamatok futtatásának hibakeresésével kapcsolatos további információkért lásd: [iterációs fejlesztés és hibakeresés Azure Data Factory](iterative-development-debugging.md)használatával.

1.  Miután a fejlesztők elégedettek voltak a módosításaival, létrehozhatnak egy lekéréses kérelmet a szolgáltatási ágra a fő vagy az együttműködési ágra, hogy a társaik áttekintsék a módosításokat.

1.  A lekéréses kérelem jóváhagyása és a módosítások egyesítése a fő ág esetében a módosítások a fejlesztői gyárban lesznek közzétéve.

1.  Ha a csapat készen áll a módosítások tesztelési vagy ellenőrzését (felhasználói elfogadási tesztelés) gyárba való telepítésére, a csapat az Azure-folyamatokat is kiadja, és üzembe helyezi a fejlesztői gyár kívánt verzióját a ellenőrzését. Ez az üzembe helyezés az Azure-folyamatok feladatának részeként zajlik, és Resource Manager-sablon paramétereit használja a megfelelő konfiguráció alkalmazásához.

1.  Miután ellenőrizte a módosításokat a tesztelési gyárban, telepítse az üzemi gyárat a folyamatok kiadásának következő feladatának használatával.

> [!NOTE]
> Csak a fejlesztői gyár van társítva egy git-tárházhoz. A teszt-és éles üzemekhez nem tartozhat git-tárház társítva, és csak Azure DevOps-folyamaton vagy erőforrás-kezelési sablonon keresztül frissíthető.

Az alábbi képen az életciklus különböző lépései láthatók.

![Az Azure-folyamatokkal való folyamatos integráció diagramja](media/continuous-integration-deployment/continuous-integration-image12.png)

## <a name="automate-continuous-integration-by-using-azure-pipelines-releases"></a>Folyamatos integráció automatizálása az Azure-folyamatok kiadásaival

Az alábbi útmutató egy olyan Azure-beli folyamat-kiadás beállításához nyújt útmutatást, amely automatizálja egy adatelőállító több környezetbe való telepítését.

### <a name="requirements"></a>Követelmények

-   A Visual Studio Team Foundation Serverhoz vagy az [Azure Resource Manager szolgáltatási végpontot](/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager)használó Azure reposhez kapcsolódó Azure-előfizetés.

-   Az Azure Repos git-integrációja által konfigurált adatelőállító.

-   Egy [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) , amely az egyes környezetek titkait tartalmazza.

### <a name="set-up-an-azure-pipelines-release"></a>Azure-folyamatok kiadásának beállítása

1.  Az [Azure DevOps](https://dev.azure.com/)nyissa meg az adatai-előállítóval konfigurált projektet.

1.  A lap bal oldalán válassza a **folyamatok**, majd a **kiadások** elemet.

    ![Folyamatok, kiadások kiválasztása](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  Válassza az **új folyamat** lehetőséget, vagy ha meglévő folyamatokkal rendelkezik, válassza az **új** , majd az **új kiadási folyamat** lehetőséget.

1.  Válassza ki az **üres** sablont.

    ![Üres feladatok kiválasztása](media/continuous-integration-deployment/continuous-integration-image13.png)

1.  A **szakasz neve** mezőben adja meg a környezet nevét.

1.  Válassza az összetevő **hozzáadása** lehetőséget, majd válassza ki a fejlesztői adatelőállítóval konfigurált git-tárházat. Válassza ki az adattár [közzétételi ágát](source-control.md#configure-publishing-settings) az **alapértelmezett ág** számára. Alapértelmezés szerint ez a közzétételi ág `adf_publish` . Az **alapértelmezett verziónál** válassza a **legutóbbi lehetőséget az alapértelmezett ág** lehetőségnél.

    ![Összetevő hozzáadása](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  Azure Resource Manager telepítési feladat hozzáadása:

    a.  A fázis nézetben válassza a **fázis-feladatok megtekintése** lehetőséget.

    ![Szakasz nézet](media/continuous-integration-deployment/continuous-integration-image14.png)

    b.  Hozzon létre egy új feladatot. Keresse meg az **ARM-sablon központi telepítését**, majd válassza a **Hozzáadás** lehetőséget.

    c.  A központi telepítési feladat területen válassza ki az előfizetést, az erőforráscsoportot és a célhelyet a cél adat-előállító számára. Szükség esetén adja meg a hitelesítő adatokat.

    d.  A **művelet** listában válassza az **erőforráscsoport létrehozása vagy frissítése** lehetőséget.

    e.  Kattintson a három pont gombra (**...**) a **sablon** mező mellett. Tallózással keresse meg a beállított git-tárház közzétételi ágában létrehozott Azure Resource Manager sablont. Keresse meg a fájlt az `ARMTemplateForFactory.json` <FactoryName> adf_publish ág mappájába.

    f.  Válassza a **... lehetőséget.** a **sablon paramétereinek** mező mellett válassza ki a paramétereket tartalmazó fájlt. Keresse meg a fájlt az `ARMTemplateParametersForFactory.json` <FactoryName> adf_publish ág mappájába.

    :  Válassza a **... lehetőséget.** a **felülírási sablon paraméterei** mező mellett adja meg a cél adatok előállítójának kívánt értékeit. Azure Key Vaultból származó hitelesítő adatok esetén adja meg a titkos kulcs nevét idézőjelek között. Ha például a titkos kód neve cred1, akkor adja meg a **"$ (cred1)"** értéket ehhez az értékhez.

    h. Válassza a **növekményes** lehetőséget a **központi telepítési módhoz**.

    > [!WARNING]
    > A teljes üzembe helyezési módban az erőforráscsoporthoz tartozó, de az új Resource Manager-sablonban nem megadott erőforrások **törlődni** fognak. További információkért tekintse meg [Azure Resource Manager telepítési módokat](../azure-resource-manager/templates/deployment-modes.md)

    ![Gyártási Data Factory](media/continuous-integration-deployment/continuous-integration-image9.png)

1.  A kiadási folyamat mentése.

1. A kiadás elindításához válassza a **kiadás létrehozása** lehetőséget. A kiadások létrehozásának automatizálásához tekintse meg az [Azure DevOps kiadási eseményindítók](/azure/devops/pipelines/release/triggers) című témakört.

   ![Válassza a kiadás létrehozása lehetőséget](media/continuous-integration-deployment/continuous-integration-image10.png)

> [!IMPORTANT]
> A CI/CD-helyzetekben a különböző környezetekben lévő Integration Runtime (IR) típusnak azonosnak kell lennie. Ha például saját üzemeltetésű integrációs modult használ a fejlesztési környezetben, akkor ugyanazt az IR-t más környezetekben, például tesztelési és üzemi környezetben is önálló üzemeltetéssel kell eltárolni. Hasonlóképpen, ha több fázisban osztja meg az integrációs modulokat, az integrációs modulokat az összes környezetben, például a fejlesztés, a tesztelés és az éles környezetek szerint kell konfigurálni.

### <a name="get-secrets-from-azure-key-vault"></a>Titkok beolvasása Azure Key Vault

Ha Azure Resource Manager-sablonban titkos kulcsokkal rendelkezik, javasoljuk, hogy a Azure Key Vaultt az Azure-folyamatok kiadásával használja.

A titkokat kétféleképpen kezelheti:

1.  Adja hozzá a titkokat a parameters (paraméterek) fájlhoz. További információ: [a Azure Key Vault használata a biztonságos paraméterek értékének](../azure-resource-manager/templates/key-vault-parameter.md)átadására az üzembe helyezés során.

    Hozzon létre egy másolatot a közzétételi ágra feltöltött Parameters fájlról. Adja meg azoknak a paramétereknek az értékeit, amelyeket Key Vault szeretne kapni a következő formátum használatával:

    ```json
    {
        "parameters": {
            "azureSqlReportingDbPassword": {
                "reference": {
                    "keyVault": {
                        "id": "/subscriptions/<subId>/resourceGroups/<resourcegroupId> /providers/Microsoft.KeyVault/vaults/<vault-name> "
                    },
                    "secretName": " < secret - name > "
                }
            }
        }
    }
    ```

    Ha ezt a módszert használja, a titkos kulcsot a rendszer automatikusan kihúzta a kulcstartóból.

    A paraméterek fájljának a közzétételi ágban is szerepelnie kell.

1. Az előző szakaszban ismertetett Azure Resource Manager központi telepítési feladat előtt adjon hozzá egy [Azure Key Vault feladatot](/azure/devops/pipelines/tasks/deploy/azure-key-vault) :

    1.  A **feladatok** lapon hozzon létre egy új feladatot. Keressen rá **Azure Key Vault** és vegye fel.

    1.  A Key Vault feladatban válassza ki azt az előfizetést, amelyben létrehozta a kulcstartót. Szükség esetén adja meg a hitelesítő adatokat, majd válassza ki a Key vaultot.

    ![Key Vault feladat hozzáadása](media/continuous-integration-deployment/continuous-integration-image8.png)

#### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>Engedélyek megadása az Azure-folyamatok ügynökének

Ha a megfelelő engedélyek nincsenek beállítva, a Azure Key Vault feladat meghiúsulhat a hozzáférés-megtagadási hibával. Töltse le a kiadáshoz tartozó naplókat, és keresse meg azt a. ps1 fájlt, amely a parancsot tartalmazza az Azure-folyamatok ügynökének engedélyek megadásához. A parancsot közvetlenül is futtathatja. Vagy átmásolhatja a résztvevő AZONOSÍTÓját a fájlból, és manuálisan is hozzáadhatja a hozzáférési házirendet a Azure Portal. `Get` és `List` a minimálisan szükséges engedélyek.

### <a name="updating-active-triggers"></a>Aktív eseményindítók frissítése

A központi telepítés meghiúsulhat, ha megpróbál frissíteni az aktív eseményindítókat. Az aktív eseményindítók frissítéséhez manuálisan kell leállítania azokat, majd újra kell indítania őket a telepítés után. Ezt egy Azure PowerShell feladat használatával teheti meg:

1.  A kiadás **feladatok** lapján adjon hozzá egy **Azure PowerShell** feladatot. Válassza a feladat 4-es verziója. * lehetőséget. 

1.  Válassza ki azt az előfizetést, amelyben a gyár található.

1.  Adja meg a **parancsfájl elérési útját** parancsfájl típusaként. Ehhez mentenie kell a PowerShell-szkriptet a tárházba. Az eseményindítók leállításához a következő PowerShell-parancsfájl használható:

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

A hasonló lépések végrehajtásával (a `Start-AzDataFactoryV2Trigger` függvénnyel) újraindíthatja az eseményindítókat az üzembe helyezés után.

A (z) adatfeldolgozó csapata a cikk alján található, [előzetes és üzembe helyezés utáni parancsfájlt](#script) kapott. 

## <a name="manually-promote-a-resource-manager-template-for-each-environment"></a>Resource Manager-sablon manuális előléptetése az egyes környezetekhez

1. Az **ARM-sablon** listában válassza az **ARM-sablon exportálása** lehetőséget, hogy a fejlesztési környezetben exportálja a Resource Manager-sablont az adatgyárhoz.

   ![Resource Manager-sablon exportálása](media/continuous-integration-deployment/continuous-integration-image1.png)

1. A tesztelési és éles adatgyárak területen válassza az **ARM-sablon importálása** lehetőséget. Ez a művelet elvégzi a Azure Portal, ahol importálhatja az exportált sablont. Válassza a **saját sablon létrehozása lehetőséget a szerkesztőben** a Resource Manager-sablon szerkesztőjének megnyitásához.

   ![Saját sablon létrehozása](media/continuous-integration-deployment/custom-deployment-build-your-own-template.png) 

1. Válassza a **fájl betöltése** lehetőséget, majd válassza ki a generált Resource Manager-sablont. Ez az 1. lépésben exportált. zip fájlban található fájl **arm_template.js** .

   ![Sablon szerkesztése](media/continuous-integration-deployment/custom-deployment-edit-template.png)

1. A beállítások szakaszban adja meg a konfigurációs értékeket, például a társított szolgáltatás hitelesítő adatait. Ha elkészült, válassza a **vásárlás** lehetőséget a Resource Manager-sablon telepítéséhez.

   ![Beállítások szakasz](media/continuous-integration-deployment/continuous-integration-image5.png)

## <a name="use-custom-parameters-with-the-resource-manager-template"></a>Egyéni paraméterek használata Resource Manager-sablonnal

Ha a fejlesztői gyárhoz tartozik egy társított git-tárház, felülbírálhatja a sablon közzétételével vagy exportálásával generált Resource Manager-sablon alapértelmezett paramétereit. Az alapértelmezett Resource Manager-paraméterek konfigurációját a következő helyzetekben érdemes felülbírálni:

* Automatikus CI/CD-t használ, és módosítani szeretné néhány tulajdonságot a Resource Manager üzembe helyezése során, de a tulajdonságok alapértelmezés szerint nem paraméterek.
* A gyár olyan nagy méretű, hogy az alapértelmezett Resource Manager-sablon érvénytelen, mert több, mint a maximálisan megengedett paraméterek (256).

    Az 256-os egyéni paraméterek kezeléséhez három lehetőség közül választhat:    
  
    * Használja az egyéni paraméter fájlját, és távolítsa el azokat a tulajdonságokat, amelyek nem igényelnek paraméterezés, azaz olyan tulajdonságokat, amelyek megtarthatják az alapértelmezett értéket, és így csökkenthetik a paraméterek darabszámát.
    * A adatfolyam újraértékelési logikája a paraméterek csökkentése érdekében, például a folyamat paramétereinek mindegyike azonos értékkel rendelkezik, csak a globális paramétereket használhatja.
    * Az egyik adatfeldolgozót több adatfolyamatra is kioszthatja.

Az alapértelmezett Resource Manager-paraméter konfigurációjának felülbírálásához lépjen a **felügyelet** hubhoz, és válassza az **ARM-sablon** lehetőséget a "verziókövetés" szakaszban. Az **ARM** -paraméterek konfigurálása szakaszban kattintson az ikon **szerkesztése** lehetőségre a "paraméter-konfiguráció szerkesztése" elemre, hogy megnyissa a Resource Manager-paraméter konfigurációs kódjának szerkesztőjét.

![Egyéni paraméterek kezelése](media/author-management-hub/management-hub-custom-parameters.png)

> [!NOTE]
> Az **ARM-paraméterek konfigurálása** csak "git módban" engedélyezett. Jelenleg le van tiltva "élő mód" vagy "Data Factory" módban.

Az egyéni Resource Manager-paraméterek konfigurációjának létrehozásakor létrejön egy **arm-template-parameters-definition.js** nevű fájl a git-ág gyökérkönyvtárában. Pontosan ezt a fájlnevet kell használnia.

![Egyéni paraméterek fájlja](media/continuous-integration-deployment/custom-parameters.png)

Az együttműködési ág közzétételét követően Data Factory beolvassa ezt a fájlt, és a konfigurációját használva létrehozza, hogy mely tulajdonságok kapják meg a paramétereket. Ha nem található fájl, a rendszer az alapértelmezett sablont használja.

Egy Resource Manager-sablon exportálásakor Data Factory beolvassa ezt a fájlt attól függően, hogy melyik ág éppen dolgozik, és nem az együttműködési ágat. A fájlt létrehozhatja vagy szerkesztheti egy privát ág alapján, ahol tesztelheti a módosításokat úgy, hogy kiválasztja az **ARM-sablon exportálása** lehetőséget a felhasználói felületen. Ezután egyesítheti a fájlt az együttműködési ágban.

> [!NOTE]
> Az egyéni Resource Manager-paraméterek konfigurációja nem változtatja meg az ARM-sablon 256-as korlátját. Lehetővé teszi a paraméteres tulajdonságok számának kiválasztását és csökkentését.

### <a name="custom-parameter-syntax"></a>Egyéni paraméter szintaxisa

Az alábbi útmutatást követve az egyéni paraméterek fájljának létrehozásakor **arm-template-parameters-definition.js**. A fájl az egyes entitások típusának egy szakaszát tartalmazza: trigger, folyamat, társított szolgáltatás, adatkészlet, integrációs modul és adatfolyam.

* Adja meg a tulajdonság elérési útját a megfelelő entitás típusa mezőben.
* A tulajdonságnév beállítása `*` azt jelzi, hogy az összes tulajdonságot meg szeretné parametrizálja (csak az első szintre, nem rekurzív módon). Kivételeket is megadhat ehhez a konfigurációhoz.
* Egy tulajdonság értékének karakterláncként való megadása azt jelzi, hogy meg kívánja parametrizálja a tulajdonságot. Használja a következő formátumot: `<action>:<name>:<stype>`.
   *  `<action>` a következő karakterek egyike lehet:
      * `=` azt jelenti, hogy az aktuális értéket a paraméter alapértelmezett értékeként tárolja.
      * `-` azt jelenti, hogy nem tartja meg a paraméter alapértelmezett értékét.
      * `|` a Azure Key Vault titkos kódokhoz vagy kulcsokhoz tartozó titkok esetében különleges eset.
   * `<name>` a paraméter neve. Ha üres, akkor a tulajdonság nevét veszi fel. Ha az érték egy `-` karakterrel kezdődik, a név lerövidítve lesz. Például `AzureStorage1_properties_typeProperties_connectionString` lerövidítheti a következőt: `AzureStorage1_connectionString` .
   * `<stype>` a paraméter típusa. Ha a `<stype>` értéke üres, az alapértelmezett típus: `string` . Támogatott értékek:,,,, `string` `securestring` `int` `bool` `object` `secureobject` és `array` .
* Egy tömb megadása a definíciós fájlban azt jelzi, hogy a sablonban szereplő egyező tulajdonság egy tömb. Data Factory a tömbben lévő összes objektumon megismétli a tömböt az Integration Runtime objektumában megadott definíció használatával. A második objektum, egy karakterlánc, a tulajdonság neve lesz, amely az egyes iterációk paraméterének neveként szerepel.
* Egy definíció nem lehet egy adott erőforrás-példányra jellemző. Bármely definíció az adott típusú összes erőforrásra vonatkozik.
* Alapértelmezés szerint az összes biztonságos karakterlánc, például a Key Vault titkos kódok, valamint a biztonságos karakterláncok, például a kapcsolati karakterláncok, kulcsok és tokenek paraméterei.
 
### <a name="sample-parameterization-template"></a>Minta paraméterezés-sablon

Az alábbi példa egy Resource Manager-paraméterek konfigurációjának megjelenését szemlélteti:

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "activities": [{
                "typeProperties": {
                    "waitTimeInSeconds": "-::int",
                    "headers": "=::object"
                }
            }]
        }
    },
    "Microsoft.DataFactory/factories/integrationRuntimes": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
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
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "secretAccessKey": "|"
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
    "Microsoft.DataFactory/factories/datasets": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    }
}
```
Íme egy magyarázat arról, hogy az előző sablon hogyan épül fel, az erőforrástípus szerinti bontásban.

#### <a name="pipelines"></a>Pipelines
    
* Az elérési út bármely tulajdonsága `activities/typeProperties/waitTimeInSeconds` paraméterrel van elfoglalva. A folyamatokban lévő minden olyan tevékenység, amelynek a neve `waitTimeInSeconds` (például a `Wait` tevékenység), egy alapértelmezett névvel van ellátva. A Resource Manager-sablonban azonban nem szerepel alapértelmezett érték. A Resource Manager üzembe helyezése során kötelezően megadandó adatok lesznek.
* Hasonlóképpen, egy nevű tulajdonság `headers` (például egy `Web` tevékenység) paraméterének típusa `object` (JObject). Alapértelmezett értékkel rendelkezik, amely megegyezik a forrás-előállítóval megegyező értékkel.

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

> [!NOTE]
> Az **Azure-riasztások és-mátrixok** , ha egy folyamathoz vannak konfigurálva, jelenleg nem támogatottak az ARM-környezetek paraméterei. A riasztások és mátrixok új környezetben való újraalkalmazásához kövesse [Data Factory figyelés, riasztások és mátrixok lehetőséget.](https://docs.microsoft.com/azure/data-factory/monitor-using-azure-monitor#data-factory-metrics)
> 

### <a name="default-parameterization-template"></a>Alapértelmezett paraméterezés-sablon

Alább látható az aktuális alapértelmezett paraméterezés-sablon. Ha csak néhány paramétert kell felvennie, a sablon közvetlen szerkesztése jó ötlet lehet, mivel nem fogja elveszíteni a meglévő paraméterezés-struktúrát.

```json
{
    "Microsoft.DataFactory/factories": {
        "properties": {
            "globalParameters": {
                "*": {
                    "value": "="
                }
            }
        },
        "location": "="
    },
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                },
                "computeProperties": {
                    "dataFlowProperties": {
                        "externalComputeInfo": [{
                                "accessToken": "-::secureString"
                            }
                        ]
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "host": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "poolName": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "functionAppUrl":"=",
                    "environmentUrl": "=",
                    "aadResourceId": "=",
                    "sasUri": "|:-sasUri:secureString",
                    "sasToken": "|",
                    "connectionString": "|:-connectionString:secureString",
                    "hostKeyFingerprint": "="
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/managedVirtualNetworks/managedPrivateEndpoints": {
        "properties": {
            "*": "="
        }
    }
}
```

### <a name="example-parameterizing-an-existing-azure-databricks-interactive-cluster-id"></a>Példa: parameterizing meglévő Azure Databricks interaktív fürt azonosítója

Az alábbi példa bemutatja, hogyan adhat hozzá egyetlen értéket az alapértelmezett paraméterezés-sablonhoz. Csak egy meglévő Azure Databricks interaktív Databricks szeretnénk hozzáadni egy társított szolgáltatáshoz a Parameters fájlhoz. Vegye figyelembe, hogy ez a fájl ugyanaz, mint az előző fájl, kivéve a `existingClusterId` Tulajdonságok mezőjének hozzáadásával `Microsoft.DataFactory/factories/linkedServices` .

```json
{
    "Microsoft.DataFactory/factories": {
        "properties": {
            "globalParameters": {
                "*": {
                    "value": "="
                }
            }
        },
        "location": "="
    },
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }
 
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "poolName": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "existingClusterId": "-"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```

## <a name="linked-resource-manager-templates"></a>Társított Resource Manager-sablonok

Ha beállította a CI/CD-t az adatgyárai számára, akkor előfordulhat, hogy túllépi a Azure Resource Manager-sablon korlátait, mivel a gyár nagyobb mértékben növekszik. Például az egyik korlát az erőforrások maximális száma egy Resource Manager-sablonban. Ha a gyár teljes Resource Manager-sablonjának létrehozásakor nagy gyárakat szeretne kiszolgálni, Data Factory mostantól társított Resource Manager-sablonokat hoz létre. Ezzel a szolgáltatással a teljes gyári adattartalom több fájlra van bontva, így nem korlátozza a korlátokat.

Ha a git-t konfigurálta, a csatolt sablonok a adf_publish ág teljes Resource Manager-sablonjai mellett jönnek létre és menthetők egy új, linkedTemplates nevű mappában:

![Társított Resource Manager-sablonok mappája](media/continuous-integration-deployment/linked-resource-manager-templates.png)

A társított Resource Manager-sablonok általában egy fősablonból és egy, a főkiszolgálóhoz csatolt alárendelt sablonokból állnak. A fölérendelt sablon neve ArmTemplate_master.json, és a gyermek-sablonok neve a következő mintával: ArmTemplate_0.json, ArmTemplate_1.json, és így tovább. 

Ha a teljes Resource Manager-sablon helyett csatolt sablonokat szeretne használni, frissítse a CI/CD-feladatot úgy, hogy az a ArmTemplateForFactory.js(teljes Resource Manager-sablon) helyett ArmTemplate_master.jsre mutasson. A Resource Manager emellett azt is megköveteli, hogy a csatolt sablonokat egy Storage-fiókba töltse fel, hogy az Azure az üzembe helyezés során is hozzáférhessen. További információ: [a társított Resource Manager-sablonok üzembe helyezése a vsts](/archive/blogs/najib/deploying-linked-arm-templates-with-vsts).

Ne felejtse el hozzáadni az Data Factory szkripteket a CI/CD-folyamathoz az üzembe helyezési feladat előtt és után.

Ha nincs beállítva a git, a csatolt sablonokat az **ARM-sablonok** listájában, az **Exportálás ARM-sablon** használatával érheti el.

## <a name="hotfix-production-environment"></a>Gyorsjavítások éles környezete

Ha a gyárat éles környezetben helyezi üzembe, és egy olyan hibát tapasztal, amelyet azonnal meg kell oldani, de nem tudja telepíteni az aktuális együttműködési ágat, előfordulhat, hogy telepítenie kell egy gyorsjavítást. Ez a megközelítés a Quick-Fix Engineering vagy a QFE.

1.    Az Azure DevOps nyissa meg az éles környezetben üzembe helyezett kiadást. A legutóbb telepített véglegesítő keresése.

2.    A véglegesítő üzenetből szerezze be az együttműködési ág véglegesítő AZONOSÍTÓját.

3.    Hozzon létre egy új gyorsjavítási ágat ebből a véglegesítve.

4.    Lépjen a Azure Data Factory UX-re, és váltson a gyorsjavítási ágra.

5.    A Azure Data Factory UX használatával javítsa ki a hibát. Tesztelje a módosításokat.

6.    A javítás ellenőrzése után válassza az **ARM-sablon exportálása** lehetőséget a gyorsjavítási erőforrás-kezelő sablon beszerzéséhez.

7.    Ezt a buildet manuálisan is bejelölheti az adf_publish ágra.

8.    Ha úgy állította be a kiadási folyamatot, hogy adf_publish-bejelentkezések alapján automatikusan aktiválódik, egy új kiadás automatikusan elindul. Ellenkező esetben manuálisan kell várólistát adni a kiadáshoz.

9.    Telepítse a gyorsjavítási kiadást a tesztelési és a termelési gyárakba. Ez a kiadás tartalmazza az előző éles adattartalmat, valamint az 5. lépésben elvégzett javítást.

10.   Vegye fel a módosításokat a gyorsjavításból a fejlesztési ágra, hogy a későbbi kiadások ne tartalmazzanak ugyanazt a hibát.

Tekintse meg az alábbi videót, amely részletesen ismerteti a környezetek gyors javítását ismertető videót. 

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4I7fi]

## <a name="exposure-control-and-feature-flags"></a>A kitettség szabályozása és a funkció jelzői

Ha egy csapaton dolgozik, vannak olyan példányok, amelyekben a módosításokat egyesítheti, de nem szeretné, hogy azok emelt szintű környezetekben fussanak, mint például a PROD és a QA. Ennek a forgatókönyvnek a kezeléséhez az ADF csapata [a DevOps koncepcióját javasolja a funkciók jelzői](/azure/devops/migrate/phase-features-with-feature-flags)alapján. Az ADF-ben egyesítheti a [globális paramétereket](author-global-parameters.md) és az [IF Condition tevékenységgel](control-flow-if-condition-activity.md) a logikai készletek elrejtését ezen környezeti jelzők alapján.

A szolgáltatás jelző beállításának megismeréséhez tekintse meg az alábbi videós oktatóanyagot:

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4IxdW]

## <a name="best-practices-for-cicd"></a>Ajánlott eljárások CI/CD-hez

Ha git-integrációt használ a saját adatgyárával, és rendelkezik egy CI/CD-folyamattal, amely a változásokat a fejlesztésből teszteli, majd éles környezetbe helyezi, javasoljuk az alábbi ajánlott eljárásokat:

-   **Git-integráció**. Csak a fejlesztői adatgyárat konfigurálja git-integrációval. A tesztelés és a gyártás változásai a CI/CD-n keresztül települnek, és nincs szükség git-integrációra.

-   **Üzembe helyezés előtti és utáni parancsfájl**. A CI/CD-ben a Resource Manager üzembe helyezési lépése előtt végre kell hajtania bizonyos feladatokat, például az eseményindítók leállítását és újraindítását, valamint a tisztítást végző műveletet. Javasoljuk, hogy a központi telepítési feladat előtt és után használjon PowerShell-parancsfájlokat. További információ: az [aktív eseményindítók frissítése](#updating-active-triggers). A (z) adatfeldolgozó csapata a lap alján található [parancsfájlt adott](#script) meg.

-   **Integrációs modulok és megosztás**. Az integrációs modulok nem változnak gyakran, és a CI/CD minden fázisában hasonlóak. Így Data Factory az integrációs modul azonos nevét és típusát várja a CI/CD minden szakaszában. Ha az integrációs modulokat minden fázisban meg szeretné osztani, érdemes lehet egy Ternáris-gyárat használni, amely csak a megosztott integrációs modulokat tartalmazza. Ezt a megosztott gyárat az összes környezetében használhatja társított Integration Runtime-típusként.

-   **Felügyelt magánhálózati végpont üzembe helyezése**. Ha egy saját végpont már létezik egy gyárban, és olyan ARM-sablont próbál telepíteni, amely azonos nevű, de módosított tulajdonságokkal rendelkező privát végpontot tartalmaz, akkor a telepítés sikertelen lesz. Más szóval a privát végpontok sikeresen üzembe helyezhetők, ha ugyanazokkal a tulajdonságokkal rendelkezik, mint a gyárban már meglévő. Ha bármely tulajdonság eltérő a környezetek között, felülbírálhatja azt a tulajdonság parameterizing, és a telepítés során megadhatja a megfelelő értéket.

-   **Key Vault**. Ha olyan társított szolgáltatásokat használ, amelyek kapcsolati adatait Azure Key Vault tárolja, ajánlott külön kulcstárolókat tárolni a különböző környezetekben. Külön jogosultsági szinteket is beállíthat mindegyik kulcstartóhoz. Előfordulhat például, hogy nem szeretné, hogy a csapattagok rendelkezzenek a termelési titkokhoz szükséges engedélyekkel. Ha követi ezt a megközelítést, javasoljuk, hogy az összes fázisban ugyanazokat a titkos neveket tartsa meg. Ha megtartja ugyanazokat a titkos neveket, nem kell parametrizálja minden egyes kapcsolódási karakterláncot a CI/CD-környezetek között, mert az egyetlen dolog, ami megváltoztatja a kulcstároló nevét, amely egy külön paraméter.

-  **Erőforrás-elnevezés** Az ARM-sablonok korlátozásai miatt előfordulhat, hogy a telepítés során problémák merülnek fel, ha az erőforrások szóközt tartalmaznak a névben. A Azure Data Factory csapat az erőforrások térköze helyett az "_" vagy a "-" karakterek használatát javasolja. Például "Pipeline_1" lenne egy előnyben részesített név a "folyamat 1" felett.

## <a name="unsupported-features"></a>Nem támogatott funkciók

- A tervezés szerint a Data Factory nem teszi lehetővé az erőforrások elküldését vagy szelektív közzétételét. A közzététel magában foglalja az adatelőállítóban végrehajtott összes módosítást is.

    - A adatfeldolgozó-entitások egymástól függenek. Az eseményindítók például a folyamatoktól függenek, és az adatkészletek és az egyéb folyamatok függenek egymástól. Az erőforrások egy részhalmazának szelektív közzététele váratlan viselkedést és hibákat eredményezhet.
    - Ritka esetekben, amikor szelektív közzétételre van szüksége, érdemes lehet gyorsjavítást használni. További információ: [gyorsjavítások éles környezete](#hotfix-production-environment).

- Az Azure Data Factory csapat nem javasolja, hogy az Azure RBAC-vezérlőket az egyes entitásokhoz (folyamatokhoz, adatkészletekhez stb.) rendeljen egy adat-előállítóban. Ha például egy fejlesztő hozzáfér egy folyamathoz vagy adathalmazhoz, akkor hozzá kell férnie az adat-előállítóban lévő összes folyamathoz vagy adathalmazhoz. Ha úgy érzi, hogy számos Azure-szerepkört kell megvalósítani egy adat-előállítón belül, tekintse meg a második adat-előállító üzembe helyezését ismertetőt.

-   Privát ágakból nem lehet közzétenni.

-   Jelenleg nem futtathat projekteket a bitbucket-on.

-   A riasztások és mátrixok jelenleg nem exportálhatók és nem importálhatók paraméterekként. 

## <a name="sample-pre--and-post-deployment-script"></a><a name="script"></a> Példa előtti és utáni parancsfájl

A következő minta-parancsfájl segítségével leállíthatja az eseményindítókat a telepítés előtt, és később újraindíthatja őket. A parancsfájl emellett kódot is tartalmaz az eltávolított erőforrások törléséhez. Mentse a parancsfájlt egy Azure DevOps git-tárházban, és hivatkozzon egy Azure PowerShell-feladaton keresztül a 4-es verziójú. * használatával.

Az üzembe helyezés előtti parancsfájl futtatásakor meg kell adnia a következő paraméterek variációját a **parancsfájl argumentumai** mezőben.

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $true -deleteDeployment $false`


Üzembe helyezés utáni parancsfájl futtatásakor meg kell adnia a következő paraméterek variációját a **parancsfájl argumentumai** mezőben.

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $false -deleteDeployment $true`

![Azure PowerShell-feladat](media/continuous-integration-deployment/continuous-integration-image11.png)

Itt látható az üzembe helyezés előtti és utáni telepítéshez használható szkript. A törölt erőforrásokhoz és erőforrás-hivatkozásokhoz tartozó fiókok.

  
```powershell
param
(
    [parameter(Mandatory = $false)] [String] $armTemplate,
    [parameter(Mandatory = $false)] [String] $ResourceGroupName,
    [parameter(Mandatory = $false)] [String] $DataFactoryName,
    [parameter(Mandatory = $false)] [Bool] $predeployment=$true,
    [parameter(Mandatory = $false)] [Bool] $deleteDeployment=$false
)

function getPipelineDependencies {
    param([System.Object] $activity)
    if ($activity.Pipeline) {
        return @($activity.Pipeline.ReferenceName)
    } elseif ($activity.Activities) {
        $result = @()
        $activity.Activities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        return $result
    } elseif ($activity.ifFalseActivities -or $activity.ifTrueActivities) {
        $result = @()
        $activity.ifFalseActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        $activity.ifTrueActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        return $result
    } elseif ($activity.defaultActivities) {
        $result = @()
        $activity.defaultActivities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        if ($activity.cases) {
            $activity.cases | ForEach-Object{ $_.activities } | ForEach-Object{$result += getPipelineDependencies -activity $_ }
        }
        return $result
    } else {
        return @()
    }
}

function pipelineSortUtil {
    param([Microsoft.Azure.Commands.DataFactoryV2.Models.PSPipeline]$pipeline,
    [Hashtable] $pipelineNameResourceDict,
    [Hashtable] $visited,
    [System.Collections.Stack] $sortedList)
    if ($visited[$pipeline.Name] -eq $true) {
        return;
    }
    $visited[$pipeline.Name] = $true;
    $pipeline.Activities | ForEach-Object{ getPipelineDependencies -activity $_ -pipelineNameResourceDict $pipelineNameResourceDict}  | ForEach-Object{
        pipelineSortUtil -pipeline $pipelineNameResourceDict[$_] -pipelineNameResourceDict $pipelineNameResourceDict -visited $visited -sortedList $sortedList
    }
    $sortedList.Push($pipeline)

}

function Get-SortedPipelines {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $pipelines = Get-AzDataFactoryV2Pipeline -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $ppDict = @{}
    $visited = @{}
    $stack = new-object System.Collections.Stack
    $pipelines | ForEach-Object{ $ppDict[$_.Name] = $_ }
    $pipelines | ForEach-Object{ pipelineSortUtil -pipeline $_ -pipelineNameResourceDict $ppDict -visited $visited -sortedList $stack }
    $sortedList = new-object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSPipeline]
    
    while ($stack.Count -gt 0) {
        $sortedList.Add($stack.Pop())
    }
    $sortedList
}

function triggerSortUtil {
    param([Microsoft.Azure.Commands.DataFactoryV2.Models.PSTrigger]$trigger,
    [Hashtable] $triggerNameResourceDict,
    [Hashtable] $visited,
    [System.Collections.Stack] $sortedList)
    if ($visited[$trigger.Name] -eq $true) {
        return;
    }
    $visited[$trigger.Name] = $true;
    if ($trigger.Properties.DependsOn) {
        $trigger.Properties.DependsOn | Where-Object {$_ -and $_.ReferenceTrigger} | ForEach-Object{
            triggerSortUtil -trigger $triggerNameResourceDict[$_.ReferenceTrigger.ReferenceName] -triggerNameResourceDict $triggerNameResourceDict -visited $visited -sortedList $sortedList
        }
    }
    $sortedList.Push($trigger)
}

function Get-SortedTriggers {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $triggers = Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
    $triggerDict = @{}
    $visited = @{}
    $stack = new-object System.Collections.Stack
    $triggers | ForEach-Object{ $triggerDict[$_.Name] = $_ }
    $triggers | ForEach-Object{ triggerSortUtil -trigger $_ -triggerNameResourceDict $triggerDict -visited $visited -sortedList $stack }
    $sortedList = new-object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSTrigger]
    
    while ($stack.Count -gt 0) {
        $sortedList.Add($stack.Pop())
    }
    $sortedList
}

function Get-SortedLinkedServices {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $linkedServices = Get-AzDataFactoryV2LinkedService -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
    $LinkedServiceHasDependencies = @('HDInsightLinkedService', 'HDInsightOnDemandLinkedService', 'AzureBatchLinkedService')
    $Akv = 'AzureKeyVaultLinkedService'
    $HighOrderList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]
    $RegularList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]
    $AkvList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]

    $linkedServices | ForEach-Object {
        if ($_.Properties.GetType().Name -in $LinkedServiceHasDependencies) {
            $HighOrderList.Add($_)
        }
        elseif ($_.Properties.GetType().Name -eq $Akv) {
            $AkvList.Add($_)
        }
        else {
            $RegularList.Add($_)
        }
    }

    $SortedList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]($HighOrderList.Count + $RegularList.Count + $AkvList.Count)
    $SortedList.AddRange($HighOrderList)
    $SortedList.AddRange($RegularList)
    $SortedList.AddRange($AkvList)
    $SortedList
}

$templateJson = Get-Content $armTemplate | ConvertFrom-Json
$resources = $templateJson.resources

#Triggers 
Write-Host "Getting triggers"
$triggersInTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/triggers" }
$triggerNamesInTemplate = $triggersInTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}

$triggersDeployed = Get-SortedTriggers -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

$triggersToStop = $triggersDeployed | Where-Object { $triggerNamesInTemplate -contains $_.Name } | ForEach-Object { 
    New-Object PSObject -Property @{
        Name = $_.Name
        TriggerType = $_.Properties.GetType().Name 
    }
}
$triggersToDelete = $triggersDeployed | Where-Object { $triggerNamesInTemplate -notcontains $_.Name } | ForEach-Object { 
    New-Object PSObject -Property @{
        Name = $_.Name
        TriggerType = $_.Properties.GetType().Name 
    }
}
$triggersToStart = $triggersInTemplate | Where-Object { $_.properties.runtimeState -eq "Started" -and ($_.properties.pipelines.Count -gt 0 -or $_.properties.pipeline.pipelineReference -ne $null)} | ForEach-Object { 
    New-Object PSObject -Property @{
        Name = $_.name.Substring(37, $_.name.Length-40)
        TriggerType = $_.Properties.type
    }
}

if ($predeployment -eq $true) {
    #Stop all triggers
    Write-Host "Stopping deployed triggers`n"
    $triggersToStop | ForEach-Object {
        if ($_.TriggerType -eq "BlobEventsTrigger" -or $_.TriggerType -eq "CustomEventsTrigger") {
            Write-Host "Unsubscribing" $_.Name "from events"
            $status = Remove-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
            while ($status.Status -ne "Disabled"){
                Start-Sleep -s 15
                $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
            }
        }
        Write-Host "Stopping trigger" $_.Name
        Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force
    }
}
else {
    #Deleted resources
    #pipelines
    Write-Host "Getting pipelines"
    $pipelinesADF = Get-SortedPipelines -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $pipelinesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/pipelines" }
    $pipelinesNames = $pipelinesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedpipelines = $pipelinesADF | Where-Object { $pipelinesNames -notcontains $_.Name }
    #dataflows
    $dataflowsADF = Get-AzDataFactoryV2DataFlow -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $dataflowsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/dataflows" }
    $dataflowsNames = $dataflowsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataflow = $dataflowsADF | Where-Object { $dataflowsNames -notcontains $_.Name }
    #datasets
    Write-Host "Getting datasets"
    $datasetsADF = Get-AzDataFactoryV2Dataset -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $datasetsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/datasets" }
    $datasetsNames = $datasetsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataset = $datasetsADF | Where-Object { $datasetsNames -notcontains $_.Name }
    #linkedservices
    Write-Host "Getting linked services"
    $linkedservicesADF = Get-SortedLinkedServices -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $linkedservicesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/linkedservices" }
    $linkedservicesNames = $linkedservicesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedlinkedservices = $linkedservicesADF | Where-Object { $linkedservicesNames -notcontains $_.Name }
    #Integrationruntimes
    Write-Host "Getting integration runtimes"
    $integrationruntimesADF = Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $integrationruntimesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/integrationruntimes" }
    $integrationruntimesNames = $integrationruntimesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedintegrationruntimes = $integrationruntimesADF | Where-Object { $integrationruntimesNames -notcontains $_.Name }

    #Delete resources
    Write-Host "Deleting triggers"
    $triggersToDelete | ForEach-Object { 
        Write-Host "Deleting trigger "  $_.Name
        $trig = Get-AzDataFactoryV2Trigger -name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
        if ($trig.RuntimeState -eq "Started") {
            if ($_.TriggerType -eq "BlobEventsTrigger" -or $_.TriggerType -eq "CustomEventsTrigger") {
                Write-Host "Unsubscribing trigger" $_.Name "from events"
                $status = Remove-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
                while ($status.Status -ne "Disabled"){
                    Start-Sleep -s 15
                    $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
                }
            }
            Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force 
        }
        Remove-AzDataFactoryV2Trigger -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting pipelines"
    $deletedpipelines | ForEach-Object { 
        Write-Host "Deleting pipeline " $_.Name
        Remove-AzDataFactoryV2Pipeline -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting dataflows"
    $deleteddataflow | ForEach-Object { 
        Write-Host "Deleting dataflow " $_.Name
        Remove-AzDataFactoryV2DataFlow -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting datasets"
    $deleteddataset | ForEach-Object { 
        Write-Host "Deleting dataset " $_.Name
        Remove-AzDataFactoryV2Dataset -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting linked services"
    $deletedlinkedservices | ForEach-Object { 
        Write-Host "Deleting Linked Service " $_.Name
        Remove-AzDataFactoryV2LinkedService -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting integration runtimes"
    $deletedintegrationruntimes | ForEach-Object { 
        Write-Host "Deleting integration runtime " $_.Name
        Remove-AzDataFactoryV2IntegrationRuntime -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }

    if ($deleteDeployment -eq $true) {
        Write-Host "Deleting ARM deployment ... under resource group: " $ResourceGroupName
        $deployments = Get-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName
        $deploymentsToConsider = $deployments | Where { $_.DeploymentName -like "ArmTemplate_master*" -or $_.DeploymentName -like "ArmTemplateForFactory*" } | Sort-Object -Property Timestamp -Descending
        $deploymentName = $deploymentsToConsider[0].DeploymentName

       Write-Host "Deployment to be deleted: " $deploymentName
        $deploymentOperations = Get-AzResourceGroupDeploymentOperation -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
        $deploymentsToDelete = $deploymentOperations | Where { $_.properties.targetResource.id -like "*Microsoft.Resources/deployments*" }

        $deploymentsToDelete | ForEach-Object { 
            Write-host "Deleting inner deployment: " $_.properties.targetResource.id
            Remove-AzResourceGroupDeployment -Id $_.properties.targetResource.id
        }
        Write-Host "Deleting deployment: " $deploymentName
        Remove-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName -Name $deploymentName
    }

    #Start active triggers - after cleanup efforts
    Write-Host "Starting active triggers"
    $triggersToStart | ForEach-Object { 
        if ($_.TriggerType -eq "BlobEventsTrigger" -or $_.TriggerType -eq "CustomEventsTrigger") {
            Write-Host "Subscribing" $_.Name "to events"
            $status = Add-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
            while ($status.Status -ne "Enabled"){
                Start-Sleep -s 15
                $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
            }
        }
        Write-Host "Starting trigger" $_.Name
        Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force
    }
}
```
