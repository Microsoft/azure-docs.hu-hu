---
title: Ajánlott eljárások a sablonokhoz
description: A Azure Resource Manager sablonok (ARM-sablonok) létrehozásához ajánlott megközelítéseket ismerteti. Javaslatokat nyújt a gyakori problémák elkerülésére a sablonok használatakor.
ms.topic: conceptual
ms.date: 12/01/2020
ms.openlocfilehash: 583a113df9cdb1951daf1002dd69531f050cfb54
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99257997"
---
# <a name="arm-template-best-practices"></a>ARM-sablon – ajánlott eljárások

Ez a cikk bemutatja, hogyan használhatók az ajánlott eljárások az Azure Resource Manager-sablon (ARM-sablon) összeállításakor. Ezek a javaslatok segítenek elkerülni a gyakori problémákat, amikor egy ARM-sablont használ egy megoldás üzembe helyezéséhez.

## <a name="template-limits"></a>Sablonok korlátai

Korlátozza a sablon méretét 4 MB-ra, az egyes paramétereket pedig 64 KB-ra. A 4 MB-os korlát a sablon végső állapotára vonatkozik, miután az ismétlődő erőforrás-definíciókkal bővült, valamint a változók és paraméterek értékeit.

A következőket is korlátozhatja:

* 256 paraméterek
* 256 változók
* 800-erőforrások (beleértve a példányszámot)
* 64 kimeneti értékek
* 24 576 karakter egy sablon kifejezésében

Az egyes sablonokra vonatkozó korlátokat egy beágyazott sablon használatával lehet meghaladni. További információ: [csatolt és beágyazott sablonok használata Azure-erőforrások telepítésekor](linked-templates.md). A paraméterek, változók és kimenetek számának csökkentése érdekében több értéket is egyesítheti egy objektumban. További információ: [objektumok paraméterként](/azure/architecture/guide/azure-resource-manager/advanced-templates/objects-as-parameters).

## <a name="resource-group"></a>Erőforráscsoport

Amikor erőforrásokat telepít egy erőforráscsoporthoz, az erőforráscsoport az erőforrásokkal kapcsolatos metaadatokat tárolja. A metaadatokat az erőforráscsoport helye tárolja.

Ha az erőforráscsoport régiója átmenetileg nem érhető el, az erőforráscsoport erőforrásai nem frissíthetők, mert a metaadatok nem érhetők el. A más régiókban lévő erőforrások továbbra is a várt módon fognak működni, de nem frissítheti őket. A kockázat minimalizálásához keresse meg az erőforráscsoportot és az erőforrásokat ugyanabban a régióban.

## <a name="parameters"></a>Paraméterek

Az ebben a szakaszban található információk hasznosak lehetnek, ha [paraméterekkel](template-parameters.md)dolgozik.

### <a name="general-recommendations-for-parameters"></a>Paraméterekre vonatkozó általános javaslatok

* Csökkentse a paraméterek használatát. Ehelyett használjon változókat vagy literál értékeket azokhoz a tulajdonságokhoz, amelyeket nem kell megadni az üzembe helyezés során.

* Használjon teve-esetet a paraméterek neveihez.

* A paraméterek olyan beállításokhoz használhatók, amelyek a környezettől függően, például az SKU, a méret vagy a kapacitás szerint változnak.

* Használja az egyszerű azonosításhoz megadni kívánt erőforrásnevek paramétereit.

* Adja meg a metaadatok összes paraméterének leírását.

    ```json
    "parameters": {
      "storageAccountType": {
        "type": "string",
        "metadata": {
          "description": "The type of the new storage account created to store the VM disks."
        }
      }
    }
    ```

* A nem bizalmas paraméterek alapértelmezett értékének meghatározása. Az alapértelmezett érték megadásával megkönnyíti a sablon üzembe helyezését, a sablon felhasználói pedig egy megfelelő értéket láthatnak. Egy paraméter alapértelmezett értékének érvényesnek kell lennie az alapértelmezett telepítési konfigurációban lévő összes felhasználó számára.

    ```json
    "parameters": {
      "storageAccountType": {
        "type": "string",
        "defaultValue": "Standard_GRS",
        "metadata": {
          "description": "The type of the new storage account created to store the VM disks."
        }
      }
    }
    ```

* Egy opcionális paraméter megadásához ne használjon üres karakterláncot alapértelmezett értékként. Ehelyett használjon literál értéket vagy nyelvi kifejezést egy érték létrehozásához.

   ```json
   "storageAccountName": {
     "type": "string",
     "defaultValue": "[concat('storage', uniqueString(resourceGroup().id))]",
     "metadata": {
       "description": "Name of the storage account"
     }
   }
   ```

* Használjon `allowedValues` takarékosan. Csak akkor használja, ha meg kell győződnie arról, hogy néhány érték nem szerepel az engedélyezett beállítások között. Ha `allowedValues` túl széles körben használja, letilthatja az érvényes központi telepítéseket, ha nem tartja naprakészen a listát.

* Ha a sablon egyik paramétere megegyezik a PowerShell üzembe helyezési parancsában szereplő paraméterekkel, az erőforrás-kezelő feloldja ezt az elnevezési ütközést úgy, hogy hozzáadja a Postfix **FromTemplate** a sablon paraméterhez. Ha például egy **ResourceGroupName** nevű paramétert tartalmaz a sablonban, az ütközik a [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) parancsmag **ResourceGroupName** paraméterével. Az üzembe helyezés során a rendszer kéri, hogy adjon meg egy értéket a **ResourceGroupNameFromTemplate** számára.

### <a name="security-recommendations-for-parameters"></a>Paraméterekkel kapcsolatos biztonsági javaslatok

* Mindig használja a felhasználónevek és jelszavak (vagy titkos kódok) paramétereit.

* `securestring`Minden jelszóhoz és titokhoz használható. Ha bizalmas adatokat továbbít egy JSON-objektumban, használja a `secureObject` típust. A biztonságos karakterláncot vagy biztonságos objektumtípust tartalmazó sablon-paraméterek nem olvashatók be az erőforrás-telepítés után.

    ```json
    "parameters": {
      "secretValue": {
        "type": "securestring",
        "metadata": {
          "description": "The value of the secret to store in the vault."
        }
      }
    }
    ```

* Ne adja meg az alapértelmezett értékeket a felhasználónevek, jelszavak vagy bármely olyan érték számára, amelyhez `secureString` típus szükséges.

* Ne adja meg az alkalmazás támadási felületét növelő tulajdonságok alapértelmezett értékeit.

### <a name="location-recommendations-for-parameters"></a>Paraméterekre vonatkozó javaslatok

* Egy paraméter használatával adja meg az erőforrások helyét, és állítsa be az alapértelmezett értéket `resourceGroup().location` . A Location paraméter megadása lehetővé teszi a sablon felhasználói számára egy olyan hely megadását, ahol az erőforrások üzembe helyezésére jogosultak.

   ```json
   "parameters": {
     "location": {
       "type": "string",
       "defaultValue": "[resourceGroup().location]",
       "metadata": {
         "description": "The location in which the resources should be deployed."
       }
     }
   }
   ```

* Ne határozza meg `allowedValues` a Location paramétert. Előfordulhat, hogy a megadott webhelyek nem érhetők el az összes felhőben.

* Használja a Location paraméter értékét azon erőforrások esetében, amelyeknek valószínűleg ugyanazon a helyen kell lenniük. Ez a megközelítés lekicsinyíti a felhasználók számára a helyadatok megadására vonatkozó kérések számát.

* Az összes helyen nem elérhető erőforrások esetében használjon külön paramétert, vagy határozzon meg egy literális helyet.

## <a name="variables"></a>Változók

A következő információk hasznosak lehetnek a [változók](template-variables.md)használatakor:

* A Camel Case változó nevek használata.

* Használjon változókat olyan értékekhez, amelyeket többször kell használni a sablonban. Ha egy értéket csak egyszer használ, a rögzített érték megkönnyíti a sablon olvasását.

* Használjon változókat a sablon függvények összetett elrendezése alapján létrehozott értékekhez. A sablon könnyebben olvasható, ha a komplex kifejezés csak a változókban jelenik meg.

* A [hivatkozási](template-functions-resource.md#reference) függvény nem használható a `variables` sablon szakaszában. A `reference` függvény az erőforrás futásidejű állapotáról származtatja az értékét. A változók azonban a sablon kezdeti elemzése során is megoldhatók. Olyan értékeket kell megadnia, amelyeknek a `reference` függvényt közvetlenül a `resources` sablon vagy szakaszában kell megadniuk `outputs` .

* Adja meg azokat az erőforrásnevek változóit, amelyeknek egyedinek kell lenniük.

* Használjon egy [másolási hurkot a változók között](copy-variables.md) , hogy ismétlődő JSON-objektumokat hozzon létre.

* Távolítsa el a nem használt változókat.

## <a name="api-version"></a>API-verzió

Állítsa a `apiVersion` tulajdonságot egy rögzített API-verzióra az erőforrás típusához. Új sablon létrehozásakor javasoljuk, hogy az erőforrástípus legújabb API-verzióját használja. Az elérhető értékek meghatározásához tekintse meg a [sablon-referenciát](/azure/templates/).

Ha a sablon a várt módon működik, javasoljuk, hogy használja ugyanazt az API-verziót. Ha ugyanazt az API-verziót használja, nem kell aggódnia a későbbi verziókban esetlegesen bevezetett változtatások miatt.

Ne használjon paramétert az API-verzióhoz. Az erőforrás tulajdonságai és értékei az API-verziótól eltérőek lehetnek. Az IntelliSense egy szerkesztőprogramban nem tudja meghatározni a megfelelő sémát, ha az API-verzió értéke paraméter. Ha olyan API-verziót ad át, amely nem egyezik a sablon tulajdonságaival, a telepítés sikertelen lesz.

Ne használjon változókat az API-verzióhoz. Különösen ne használja a [providers függvényt](template-functions-resource.md#providers) az API-verziók dinamikus beszerzésére az üzembe helyezés során. Előfordulhat, hogy a dinamikusan lekért API-verzió nem egyezik meg a sablon tulajdonságaival.

## <a name="resource-dependencies"></a>Erőforrás-függőségek

A beállított [függőségek](define-resource-dependency.md) meghatározásakor kövesse az alábbi irányelveket:

* Használja a `reference` függvényt, és adja meg az erőforrás nevét a tulajdonság megosztásához szükséges erőforrások közötti implicit függőség beállításához. `dependsOn`Ha már definiált implicit függőséget, ne adjon hozzá explicit elemet. Ez a megközelítés csökkenti a szükségtelen függőségek kockázatát. Egy implicit függőség beállítására példát a [hivatkozás és lista függvények](define-resource-dependency.md#reference-and-list-functions)című témakörben talál.

* Adja meg a gyermek erőforrást a szülő erőforrástól függőként.

* A False értékre beállított [állapotú](conditional-resource-deployment.md) erőforrásokat a rendszer automatikusan eltávolítja a függőségi sorrendből. Állítsa be a függőségeket úgy, hogy az erőforrás mindig telepítve legyen.

* A függőségek kaszkádolt beállítása explicit módon történő beállítás nélkül. A virtuális gép például egy virtuális hálózati adaptertől függ, és a virtuális hálózati adapter a virtuális hálózattól és a nyilvános IP-címektől függ. Ezért a virtuális gépet mindhárom erőforrás után telepíti a rendszer, de a virtuális gépet a mindhárom erőforrástól függőként nem állítja be explicit módon. Ez a megközelítés tisztázza a függőségi sorrendet, és megkönnyíti a sablon későbbi módosítását.

* Ha az üzembe helyezés előtt meg lehet határozni egy értéket, próbálja meg az erőforrást függőség nélkül telepíteni. Ha például egy konfigurációs értéknek egy másik erőforrás nevére van szüksége, lehet, hogy nincs szüksége függőségre. Ez az útmutató nem mindig működik, mert egyes erőforrások ellenőrzik a másik erőforrás létezését. Ha hibaüzenetet kap, vegyen fel egy függőséget.

## <a name="resources"></a>Források

A következő információk hasznosak lehetnek az [erőforrásokkal](template-syntax.md#resources)való munka során:

* Annak érdekében, hogy más közreműködők is megértsék az erőforrás célját, az `comments` egyes erőforrásokhoz a sablonban kell megadni.

    ```json
    "resources": [
      {
        "name": "[variables('storageAccountName')]",
        "type": "Microsoft.Storage/storageAccounts",
        "apiVersion": "2019-06-01",
        "location": "[resourceGroup().location]",
        "comments": "This storage account is used to store the VM disks.",
          ...
      }
    ]
    ```

* Ha *nyilvános végpontot* használ a sablonban (például egy Azure Blob Storage nyilvános végpontot), *ne* a névteret. A `reference` függvény használatával dinamikusan kérheti le a névteret. Ezt a módszert használhatja a sablon különböző nyilvános névtérbeli környezetekben történő üzembe helyezéséhez anélkül, hogy manuálisan módosítaná a végpontot a sablonban. Állítsa az API-verziót ugyanarra a verzióra, amelyet a sablonban használt Storage-fiókhoz használ.

    ```json
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": "true",
        "storageUri": "[reference(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').primaryEndpoints.blob]"
      }
    }
    ```

   Ha a Storage-fiók ugyanabban a sablonban van telepítve, amelyet Ön hoz létre, és a Storage-fiók neve nincs megosztva a sablonban lévő másik erőforrással, nem kell megadnia a szolgáltatói névteret vagy az `apiVersion` erőforrásra való hivatkozáskor. Az alábbi példa az egyszerűsített szintaxist mutatja be.

    ```json
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": "true",
        "storageUri": "[reference(variables('storageAccountName')).primaryEndpoints.blob]"
      }
    }
    ```

   Hivatkozhat egy másik erőforráscsoporthoz tartozó meglévő Storage-fiókra is.

    ```json
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": "true",
        "storageUri": "[reference(resourceId(parameters('existingResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('existingStorageAccountName')), '2019-06-01').primaryEndpoints.blob]"
      }
    }
    ```

* Csak akkor rendeljen nyilvános IP-címeket a virtuális géphez, ha egy alkalmazáshoz szükség van. A virtuális géphez (VM) való kapcsolódáshoz, illetve felügyeleti vagy felügyeleti célból a bejövő NAT-szabályokat, a virtuális hálózati átjárókat vagy a Jumpbox használhatja.

     A virtuális gépekhez való csatlakozásról további információt a következő témakörben talál:

   * [Virtuális gépek futtatása N szintű architektúrához az Azure-ban](/azure/architecture/reference-architectures/n-tier/n-tier-sql-server)
   * [WinRM-hozzáférés beállítása virtuális gépekhez Azure Resource Manager](../../virtual-machines/windows/winrm.md)
   * [Külső hozzáférés engedélyezése a virtuális géphez a Azure Portal használatával](../../virtual-machines/windows/nsg-quickstart-portal.md)
   * [Külső hozzáférés engedélyezése a virtuális géphez a PowerShell használatával](../../virtual-machines/windows/nsg-quickstart-powershell.md)
   * [Külső hozzáférés engedélyezése linuxos virtuális géphez az Azure CLI használatával](../../virtual-machines/linux/nsg-quickstart.md)

* A `domainNameLabel` nyilvános IP-címek tulajdonságának egyedinek kell lennie. Az `domainNameLabel` értéknek 3 – 63 karakter hosszúnak kell lennie, és követnie kell az ebben a reguláris kifejezésben megadott szabályokat: `^[a-z][a-z0-9-]{1,61}[a-z0-9]$` . Mivel a `uniqueString` függvény 13 karakter hosszúságú karakterláncot hoz létre, a `dnsPrefixString` paraméter 50 karakterre van korlátozva.

    ```json
    "parameters": {
      "dnsPrefixString": {
        "type": "string",
        "maxLength": 50,
        "metadata": {
          "description": "The DNS label for the public IP address. It must be lowercase. It should match the following regular expression, or it will raise an error: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$"
        }
      }
    },
    "variables": {
      "dnsPrefix": "[concat(parameters('dnsPrefixString'),uniquestring(resourceGroup().id))]"
    }
    ```

* Amikor jelszót ad hozzá egy egyéni parancsfájl-bővítményhez, használja a tulajdonságot `commandToExecute` a `protectedSettings` tulajdonságban.

    ```json
    "properties": {
      "publisher": "Microsoft.Azure.Extensions",
      "type": "CustomScript",
      "typeHandlerVersion": "2.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "fileUris": [
          "[concat(variables('template').assets, '/lamp-app/install_lamp.sh')]"
        ]
      },
      "protectedSettings": {
        "commandToExecute": "[concat('sh install_lamp.sh ', parameters('mySqlPassword'))]"
      }
    }
    ```

   > [!NOTE]
   > Annak biztosítása érdekében, hogy a titkos kódok titkosítva legyenek a virtuális gépek és bővítmények paraméterként való átadásakor, használja a `protectedSettings` megfelelő bővítmények tulajdonságát.

* Adja meg az alapértelmezett értékekkel rendelkező tulajdonságok explicit értékeit, amelyek idővel változhatnak. Ha például egy AK-fürtöt telepít, megadhatja vagy kihagyhatja a `kubernetesVersion` tulajdonságot. Ha nem adja meg, akkor [a fürt alapértelmezett értéke az N-1 alverzió és a legújabb javítás](../../aks/supported-kubernetes-versions.md#azure-portal-and-cli-versions). Amikor ARM-sablonnal telepíti a fürtöt, ez az alapértelmezett viselkedés valószínűleg nem a várt érték. A sablon újbóli üzembe helyezése azt eredményezheti, hogy a fürt frissítése váratlanul megszakadt egy új Kubernetes-verzióra. Ehelyett érdemes megadnia egy explicit verziószámot, majd manuálisan módosítania, amikor készen áll a fürt frissítésére.

## <a name="use-test-toolkit"></a>A test Toolkit használata

Az ARM-sablon tesztelési eszközkészlete egy olyan parancsfájl, amely ellenőrzi, hogy a sablon ajánlott eljárásokat használ-e. Ha a sablon nem felel meg az ajánlott eljárásoknak, a figyelmeztetések listáját adja vissza a javasolt módosításokkal. A test Toolkit segítségével megtudhatja, hogyan implementálhatja az ajánlott eljárásokat a sablonban.

A sablon befejezése után futtassa a tesztelési eszközkészletet, és ellenőrizze, hogy van-e lehetőség a megvalósításának javítására. További információkért lásd: [ARM-sablon tesztelési eszközkészlet használata](test-toolkit.md).

## <a name="next-steps"></a>Következő lépések

* A sablonfájl struktúrájával kapcsolatos információkért lásd [az ARM-sablonok szerkezetének és szintaxisának megismerését](template-syntax.md)ismertető témakört.
* Az összes Azure-beli felhőalapú környezetben működő sablonok létrehozásával kapcsolatos javaslatokért lásd: [ARM-sablonok fejlesztése a Felhőbeli konzisztencia](templates-cloud-consistency.md)érdekében.
