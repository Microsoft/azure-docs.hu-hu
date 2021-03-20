---
title: Feladatok végpontok közötti futtatása sablonok használatával
description: Csak a CLI-parancsokkal hozhat létre készleteket, tölthet fel bemeneti adatokat, feladatokat és kapcsolódó tevékenységeket hozhat létre, és letöltheti az eredményül kapott kimeneti adatokat.
ms.topic: how-to
ms.date: 10/08/2020
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 845a32c2feda5a5a3b8d44d237c62db94cae1779
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91848721"
---
# <a name="use-azure-batch-cli-templates-and-file-transfer"></a>A CLI-sablonok és a fájlátvitel Azure Batch használata

Ha batch-bővítményt használ az Azure CLI-hez, a Batch-feladatokat kód írása nélkül is futtathatja.

Hozzon létre és használjon JSON-sablonfájlokat az Azure CLI-vel batch-készletek,-feladatok és-feladatok létrehozásához. A CLI bővítmény parancsaival egyszerűen töltheti fel a feladatok bemeneti fájljait a Batch-fiókhoz társított Storage-fiókba, és letöltheti a feladatok kimeneti fájljait.

> [!NOTE]
> A JSON-fájlok nem támogatják a [Azure Resource Manager-sablonokkal](../azure-resource-manager/templates/template-syntax.md)megegyező funkciókat. A rendszer a nyers REST-kérelem törzséhez hasonlóan formázta őket. A CLI-bővítmény nem módosítja a meglévő parancsokat, de hasonló sablon-lehetőséggel rendelkezik, amely részleges Azure Resource Manager sablon-funkciókat tesz elérhetővé. Lásd: [Azure batch CLI-bővítmények Windows, Mac és Linux rendszerekhez](https://github.com/Azure/azure-batch-cli-extensions).

## <a name="overview"></a>Áttekintés

Az Azure CLI-bővítmény lehetővé teszi, hogy a Batch a nem fejlesztő felhasználók számára teljes körűen használható legyen. Csak a CLI-parancsokkal hozhat létre készleteket, tölthet fel bemeneti adatokat, feladatokat és kapcsolódó tevékenységeket hozhat létre, és letöltheti az eredményül kapott kimeneti adatokat. Nincs szükség további kódokra. Futtassa közvetlenül a CLI-parancsokat, vagy integrálja őket parancsfájlokba.

A Batch-sablonok a meglévő batch-támogatásra épülnek az [Azure CLI](batch-cli-get-started.md#json-files-for-resource-creation) -ben a JSON-fájlokhoz a készletek, feladatok, feladatok és egyéb elemek létrehozásakor. A Batch-sablonok a következő képességeket vehetik fel:

-   A paraméterek meghatározhatók. A sablon használatakor a rendszer csak a paraméter értékeit adja meg az elem létrehozásához, és a sablon törzsében a többi elem tulajdonság értékét is megadja. A Batch és a Batch által futtatandó alkalmazások létrehozhatnak sablonokat, megadhatják a készlet, a feladat és a feladat tulajdonság értékeit. A Batch és/vagy az alkalmazások csak a definiált paraméterek értékeit kell megadniuk.

-   A feladat-előállítók létrehoznak egy vagy több feladatot egy adott feladathoz, elkerülve a feladatok elküldésének számos feltételének szükségességét, és jelentősen leegyszerűsíti a feladat beadását.


A feladatok jellemzően bemeneti adatfájlokat használnak, és kimeneti adatfájlokat hoznak létre. Alapértelmezés szerint a Storage-fiók minden batch-fiókkal társítva van. Fájlok átvitele ebbe a Storage-fiókba és onnan a parancssori felület használatával, kódolás nélkül és tárolási hitelesítő adatok nélkül.

Az [FFmpeg](https://ffmpeg.org/) például egy népszerű alkalmazás, amely a hang-és videofájlokat dolgozza fel. Az alábbi lépéseket követve a Azure Batch CLI-vel meghívhatja az FFmpeg-t, hogy átkódolja a forrásfájlokat a különböző felbontásokra.

-   Hozzon létre egy készlet sablont. A sablont létrehozó felhasználó tudja, hogyan hívhatja meg az FFmpeg-alkalmazást és annak követelményeit; megadják a megfelelő operációs rendszert, a virtuálisgép-méretet, az FFmpeg telepítését (egy alkalmazáscsomag vagy egy csomagkezelő használatával, például) és más Pool-tulajdonságértékek értékét. A rendszer létrehozza a paramétereket, így a sablon használatakor csak a készlet AZONOSÍTÓját és a virtuális gépek számát kell megadnia.

-   Hozzon létre egy sablont. A sablont létrehozó felhasználó tudja, hogyan kell meghívni az FFmpeg-t a forrás videó átkódolására egy másik felbontásra, és megadja a feladat parancssorát; azt is tudják, hogy van egy mappa, amely a forrásként szolgáló videofájlokat tartalmazza, és egy bemeneti fájlhoz szükséges feladatot kell megadnia.

-   Az átkódolni kívánt videofájlokat tartalmazó végfelhasználó először a készlet sablonnal hoz létre egy készletet, amely csak a készlet AZONOSÍTÓját és a szükséges virtuális gépek számát adja meg. Ezután fel tudják tölteni a forrásfájlokat a transcode-be. Ezután egy feladatot a feladatütemezés használatával lehet elküldeni, csak a készlet AZONOSÍTÓját és a feltöltött forrásfájlok helyét adja meg. A Batch-feladat létrehozása folyamatban van, és a rendszer egy bemeneti fájlon keresztül létrehoz egy feladatot. Végül a konvertált kimeneti fájlok tölthetők le.

## <a name="installation"></a>Telepítés

A Azure Batch CLI-bővítmény telepítéséhez először [telepítse az Azure cli 2,0](/cli/azure/install-azure-cli)-es verzióját, vagy futtassa az Azure CLI-t [Azure Cloud Shellban](../cloud-shell/overview.md).

Telepítse a Batch bővítmény legújabb verzióját az alábbi Azure CLI-parancs használatával:

```azurecli
az extension add --name azure-batch-cli-extensions
```

A Batch CLI bővítménnyel és további telepítési lehetőségekkel kapcsolatos további információkért lásd a [GitHub](https://github.com/Azure/azure-batch-cli-extensions)-tárházat.


A CLI-bővítmény funkcióinak használatához szüksége van egy Azure Batch fiókra, valamint a fájlokat a Storage-ba és a tárolóba átmásoló parancsokhoz egy társított Storage-fiók.

Az Azure CLI-vel való bejelentkezéshez lásd: batch- [erőforrások kezelése az Azure CLI-vel](batch-cli-get-started.md).

## <a name="templates"></a>Sablonok

Azure Batch sablonok hasonlók a Azure Resource Manager-sablonokhoz, a funkcionalitásban és a szintaxisban. Ezek az elemek tulajdonságainak nevét és értékeit tartalmazó JSON-fájlok, de a következő fő fogalmakat is felveszik:

-   **Paraméterek**

    -   Tulajdonságok értékének engedélyezése a törzs szakaszban, csak a sablon használatakor szükséges paraméterek értékeit kell megadni. Egy készlet teljes definíciója például elhelyezhető a törzsben, és csak egy paraméter van definiálva; a készlet `poolId` létrehozásához csak egy készlet-azonosító karakterláncot kell megadni.

    -   A sablon törzsét valaki a Batch ismeretével és a Batch által futtatandó alkalmazások létrehozásával tudja megtervezni. a sablon használatakor csak a szerző által definiált paraméterek értékeit kell megadni. A nem részletes batch és/vagy Application Knowledge nélküli felhasználók ezért használhatják a sablonokat.

-   **Változók**

    -   Egyszerű vagy összetett paraméterérték megadásának engedélyezése egy helyen és a sablon törzsének egy vagy több helyén való használata. A változók leegyszerűsítik és csökkenthetik a sablon méretét, és azt is megtarthatják, hogy az egyik hely a tulajdonságok módosítására szolgál.

-   **Magasabb szintű szerkezetek**

    -   A sablonban olyan magasabb szintű szerkezetek érhetők el, amelyek még nem érhetők el a Batch API-kon. Például egy feladat-előállító olyan Feladatsorban definiálható, amely több feladatot hoz létre a feladathoz, egy gyakori feladatdefiníció használatával. Ezek a szerkezetek nem igényelnek kódot a több JSON-fájl, például egy fájl/feladat dinamikus létrehozásához, valamint parancsfájlok létrehozásához az alkalmazások csomagkezelő használatával történő telepítéséhez.

    -   Ezek a szerkezetek felvehetők a Batch szolgáltatásba, és elérhetők a Batch API-k, a UI stb. esetében.

### <a name="pool-templates"></a>Készlet-sablonok

A készlet sablonjai a paraméterek és változók szabványos sablonra vonatkozó képességeit támogatják. A következő magasabb szintű konstrukciókat is támogatják:

-   **Csomagok hivatkozásai**

    -   Opcionálisan lehetővé teszi a szoftverek másolását a készlet csomópontjaira a csomagkezelő használatával. A csomagkezelő és a csomag azonosítója meg van adva. Egy vagy több csomag bejelentésével elkerülhető egy olyan parancsfájl létrehozása, amely beolvassa a szükséges csomagokat, telepíti a parancsfájlt, és futtatja a szkriptet minden egyes készlet-csomóponton.

A következő példa egy olyan sablont mutat be, amely létrehozza a Linux rendszerű virtuális gépek készletét, amelyeken az FFmpeg telepítve van. A használatához csak a készlet azonosító sztringjét és a készletben lévő virtuális gépek számát adja meg:

```json
{
    "parameters": {
        "nodeCount": {
            "type": "int",
            "metadata": {
                "description": "The number of pool nodes"
            }
        },
        "poolId": {
            "type": "string",
            "metadata": {
                "description": "The pool ID "
            }
        }
    },
    "pool": {
        "type": "Microsoft.Batch/batchAccounts/pools",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "[parameters('poolId')]",
            "virtualMachineConfiguration": {
                "imageReference": {
                    "publisher": "Canonical",
                    "offer": "UbuntuServer",
                    "sku": "16.04-LTS",
                    "version": "latest"
                },
                "nodeAgentSKUId": "batch.node.ubuntu 16.04"
            },
            "vmSize": "STANDARD_D3_V2",
            "targetDedicatedNodes": "[parameters('nodeCount')]",
            "enableAutoScale": false,
            "taskSlotsPerNode": 1,
            "packageReferences": [
                {
                    "type": "aptPackage",
                    "id": "ffmpeg"
                }
            ]
        }
    }
}
```

Ha a sablonfájl nevepool-ffmpeg.js, akkor a következő módon hívja _meg_ a sablont:

```azurecli
az batch pool create --template pool-ffmpeg.json
```

A CLI megkéri, hogy adjon meg értékeket a `poolId` és `nodeCount` paraméterek számára. A paramétereket egy JSON-fájlban is megadhatja. Például:

```json
{
  "poolId": {
    "value": "mypool"
  },
  "nodeCount": {
    "value": 2
  }
}
```

Ha a paraméterek JSON-fájljának nevepool-parameters.js, akkor a következő módon hívja *meg* a sablont:

```azurecli
az batch pool create --template pool-ffmpeg.json --parameters pool-parameters.json
```

### <a name="job-templates"></a>Feladatok sablonjai

A feladatütemezés támogatja a paraméterek és változók szabványos sablonra vonatkozó képességeit. A következő magasabb szintű konstrukciókat is támogatják:

-   **Feladat-előállító**

    -   Több feladatot hoz létre egy feladathoz egy adott feladatdefiníció alapján. A Task Factory háromféle típusa támogatott – a parametrikus sweep, a Task per file és a Task Collection.

Az alábbi példa egy olyan sablont mutat be, amely létrehoz egy feladatot, amely az 1. szervizcsomaggal ellátott videofájlok átkódolására szolgál az FFmpeg használatával a két alacsonyabb felbontás egyikével. Egy feladatot hoz létre egy forrásoldali videofájl alapján. A feladatok beviteléhez és a kimenethez kapcsolódó fájlcsoportok részletes ismertetését lásd: fájlcsoportok [és](#file-groups-and-file-transfer) fájlátvitel.

```json
{
    "parameters": {
        "poolId": {
            "type": "string",
            "metadata": {
                "description": "The name of Azure Batch pool which runs the job"
            }
        },
        "jobId": {
            "type": "string",
            "metadata": {
                "description": "The name of Azure Batch job"
            }
        },
        "resolution": {
            "type": "string",
            "defaultValue": "428x240",
            "allowedValues": [
                "428x240",
                "854x480"
            ],
            "metadata": {
                "description": "Target video resolution"
            }
        }
    },
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "[parameters('jobId')]",
            "constraints": {
                "maxWallClockTime": "PT5H",
                "maxTaskRetryCount": 1
            },
            "poolInfo": {
                "poolId": "[parameters('poolId')]"
            },
            "taskFactory": {
                "type": "taskPerFile",
                "source": {
                    "fileGroup": "ffmpeg-input"
                },
                "repeatTask": {
                    "commandLine": "ffmpeg -i {fileName} -y -s [parameters('resolution')] -strict -2 {fileNameWithoutExtension}_[parameters('resolution')].mp4",
                    "resourceFiles": [
                        {
                            "blobSource": "{url}",
                            "filePath": "{fileName}"
                        }
                    ],
                    "outputFiles": [
                        {
                            "filePattern": "{fileNameWithoutExtension}_[parameters('resolution')].mp4",
                            "destination": {
                                "autoStorage": {
                                    "path": "{fileNameWithoutExtension}_[parameters('resolution')].mp4",
                                    "fileGroup": "ffmpeg-output"
                                }
                            },
                            "uploadOptions": {
                                "uploadCondition": "TaskSuccess"
                            }
                        }
                    ]
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
```

Ha a sablonfájl nevejob-ffmpeg.js, akkor a következő módon hívja _meg_ a sablont:

```azurecli
az batch job create --template job-ffmpeg.json
```

Ahogy korábban is, a CLI megkéri, hogy adjon meg értékeket a paraméterek számára. A paramétereket egy JSON-fájlban is megadhatja.

### <a name="use-templates-in-batch-explorer"></a>Sablonok használata Batch Explorer

Batch-készlet vagy-feladatok létrehozásához feltölthet egy batch CLI-sablont a [Batch Explorer](https://github.com/Azure/BatchExplorer) asztali alkalmazásba (korábbi nevén BatchLabs). Az előre definiált készletből és a feladatokból is választhat a Batch Explorer gyűjteményben.

Sablon feltöltése:

1. A Batch Explorer **területen válassza a** katalógus  >  **helyi sablonok** lehetőséget.

2. Válasszon ki vagy húzzon át egy helyi készletet vagy sablont.

3. Válassza a **sablon használata** lehetőséget, és kövesse a képernyőn megjelenő utasításokat.

## <a name="file-groups-and-file-transfer"></a>Fájlcsoportok és fájlátvitel

A legtöbb feladat és feladat bemeneti fájlokat igényel, és kimeneti fájlokat hoz létre. A bemeneti fájlok és a kimeneti fájlok általában a-ügyfélről a csomópontra vagy a csomópontról az ügyfélre kerülnek át. A Azure Batch CLI-bővítmény elvonta a fájlátvitelt, és az egyes batch-fiókokhoz hozzárendelhető Storage-fiókot használja.

Egy fájl egy, az Azure Storage-fiókban létrehozott tárolónak felel meg. Előfordulhat, hogy a fájl almappákkal rendelkezik.

A Batch CLI bővítmény olyan parancsokat biztosít, amelyekkel fájlokat tölthet fel az ügyfélről egy adott csoportba, és letöltheti a fájlokat a megadott fájlkiszolgálón egy ügyfélre.

```azurecli
az batch file upload --local-path c:\source_videos\*.mp4
    --file-group ffmpeg-input

az batch file download --file-group ffmpeg-output --local-path
    c:\output_lowres_videos
```

A készlet-és a feladatütemezés lehetővé teszi a Fájlcsoportok számára tárolt fájlok megadását a Pool-csomópontokra való másoláshoz, illetve a készlet csomópontjain lévő fájlokhoz való visszatéréshez. A korábban megadott feladat sablonjában például a rendszer az *FFmpeg-input* fájlrendszert adja meg a feladat-előállító számára, mivel a forrásként átmásolt fájlok helye az átkódoláshoz a csomópontra. A file Group *FFmpeg-output* az a hely, ahol a rendszer átmásolt kimeneti fájlokat másol az egyes feladatokat futtató csomópontról.

## <a name="summary"></a>Összefoglalás

A sablon-és fájlátviteli támogatás jelenleg csak az Azure CLI-hez lett hozzáadva. A cél a Batch olyan felhasználók számára való kibontása, akik a Batch API-k, például a kutatók és az informatikai felhasználók használatával nem szükséges programkódot fejleszteni. A kódolás nélkül az Azure-t, batch-t és a Batch által futtatandó alkalmazásokat használó felhasználók sablonokat hozhatnak létre a készlethez és a feladatok létrehozásához. A sablon paraméterei segítségével a felhasználók a Batch részletes ismerete nélkül, az alkalmazások pedig használhatják a sablonokat.

Próbálja ki az Azure CLI batch-bővítményét, és küldjön visszajelzést vagy javaslatokat a jelen cikkhez vagy a [Batch közösségi](https://github.com/Azure/Batch)tárházhoz tartozó megjegyzésekben.

## <a name="next-steps"></a>Következő lépések

- A részletes telepítési és használati dokumentáció, példák és forráskódok az [Azure GitHub](https://github.com/Azure/azure-batch-cli-extensions)-tárházban érhetők el.

- További információ a Batch-erőforrások létrehozásához és kezeléséhez [Batch Explorer](https://github.com/Azure/BatchExplorer) használatáról.
