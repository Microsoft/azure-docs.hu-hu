---
title: Feladat-futtatókörnyezet környezeti változói
description: A feladat futásidejű környezeti változóinak útmutatója és referenciája Azure Batch elemzésekhez.
ms.topic: conceptual
ms.date: 12/30/2020
ms.openlocfilehash: dbdc13e28a3a0c772480d2602f147e0d3354ff48
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104669984"
---
# <a name="azure-batch-runtime-environment-variables"></a>Azure Batch futtatókörnyezet környezeti változói

A [Azure batch szolgáltatás](https://azure.microsoft.com/services/batch/) az alábbi környezeti változókat állítja be a számítási csomópontokon. Ezeket a környezeti változókat a feladatok parancssorában, illetve a parancssorok által futtatott programokban és parancsfájlokban lehet hivatkozni.

A környezeti változók batch használatával történő használatáról további információt a [környezeti beállítások a feladatokhoz](jobs-and-tasks.md#environment-settings-for-tasks)című témakörben talál.

## <a name="environment-variable-visibility"></a>Környezeti változó láthatósága

Ezek a környezeti változók csak a **feladat felhasználójának** kontextusában láthatók, ez a felhasználói fiók azon a csomóponton, amelyen a feladat végre lett hajtva. Ezeket a változókat nem fogja látni, ha RDP protokoll (RDP) vagy Secure Shell (SSH) és a (a) környezeti változók használatával [távolról csatlakozik](./error-handling.md#connect-to-compute-nodes) egy számítási csomóponthoz. Ez azért van, mert a távoli kapcsolat kialakításához használt felhasználói fiók nem egyezik a tevékenységhez használt fiókkal.

Egy környezeti változó aktuális értékének lekéréséhez indítson el `cmd.exe` egy Windows számítási csomóponton vagy `/bin/sh` egy Linux-csomóponton:

`cmd /c set <ENV_VARIABLE_NAME>`

`/bin/sh -c "printenv <ENV_VARIABLE_NAME>"`

## <a name="command-line-expansion-of-environment-variables"></a>Környezeti változók parancssori bővítése

A számítási csomópontokon végzett feladatok által végrehajtott parancssorok nem egy rendszerhéj alatt futnak. Ez azt jelenti, hogy ezek a parancssorok nem használhatnak natív módon olyan rendszerhéj-szolgáltatásokat, mint például a környezeti változók bővítése (beleértve a `PATH` következőt is). Az ilyen funkciók használatához a rendszerhéjat a parancssorban kell meghívni. Indítsa el például `cmd.exe` a Windows számítási csomópontokon vagy `/bin/sh` Linux-csomópontokon:

`cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

`/bin/sh -c "MyTaskApplication $MY_ENV_VAR"`

## <a name="environment-variables"></a>Környezeti változók

| Változó neve                     | Leírás                                                              | Rendelkezésre állás | Példa |
|-----------------------------------|--------------------------------------------------------------------------|--------------|---------|
| AZ_BATCH_ACCOUNT_NAME           | Annak a Batch-fióknak a neve, amelyhez a feladat tartozik.                  | Minden feladat.   | mybatchaccount |
| AZ_BATCH_ACCOUNT_URL            | A Batch-fiók URL-címe. | Minden feladat. | `https://myaccount.westus.batch.azure.com` |
| AZ_BATCH_APP_PACKAGE            | Az alkalmazáscsomag környezeti változóinak előtagja. Ha például a "FOO" "1" alkalmazás a készletre van telepítve, a környezeti változó AZ_BATCH_APP_PACKAGE_FOO_1 (Linux rendszeren) vagy AZ_BATCH_APP_PACKAGE_FOO # 1 (Windows rendszeren). AZ_BATCH_APP_PACKAGE_FOO_1 mutat a csomag letöltésének helyére (egy mappába). Az alkalmazáscsomag alapértelmezett verziójának használatakor használja a AZ_BATCH_APP_PACKAGE környezeti változót a verziószámok nélkül. Ha a Linux rendszerben az alkalmazáscsomag neve "Agent-Linux-x64", a verziószám pedig "1.1.46.0, a környezet neve valójában: AZ_BATCH_APP_PACKAGE_agent_linux_x64_1_1_46_0, aláhúzások és kisbetű használatával. További információ: [a telepített alkalmazások végrehajtása](batch-application-packages.md#execute-the-installed-applications) további részletekért. | A társított alkalmazáscsomag feladatainak egyike. Az összes feladathoz is elérhető, ha a csomópontnak van alkalmazáscsomag. | AZ_BATCH_APP_PACKAGE_FOO_1 (Linux) vagy AZ_BATCH_APP_PACKAGE_FOO # 1 (Windows) |
| AZ_BATCH_AUTHENTICATION_TOKEN   | Egy hitelesítési jogkivonat, amely hozzáférést biztosít a Batch szolgáltatási műveleteinek korlátozott készletéhez. Ez a környezeti változó csak akkor jelenik meg, ha a [authenticationTokenSettings](/rest/api/batchservice/task/add#authenticationtokensettings) be van állítva a [feladat hozzáadásakor](/rest/api/batchservice/task/add#request-body). A jogkivonat értékét a Batch API-k hitelesítő adatként használják a Batch-ügyfél létrehozásához, például a [BatchClient. Open () .NET API](/dotnet/api/microsoft.azure.batch.batchclient.open#Microsoft_Azure_Batch_BatchClient_Open_Microsoft_Azure_Batch_Auth_BatchTokenCredentials_)-ban. | Minden feladat. | OAuth2 hozzáférési token |
| AZ_BATCH_CERTIFICATES_DIR       | Egy könyvtár a [feladat munkakönyvtárában](files-and-directories.md) , amelyben a rendszer a Linux számítási csomópontjain tárolja a tanúsítványokat. Ez a környezeti változó nem vonatkozik a Windows számítási csomópontjaira.                                                  | Minden feladat.   |  /mnt/batch/tasks/workitems/batchjob001/job-1/task001/certs |
| AZ_BATCH_HOST_LIST              | A [többpéldányos feladat](batch-mpi.md) számára a formátumban lefoglalt csomópontok listája `nodeIP,nodeIP` . | Több példányos elsődleges és alfeladatok. | `10.0.0.4,10.0.0.5` |
| AZ_BATCH_IS_CURRENT_NODE_MASTER | Megadja, hogy az aktuális csomópont a [többpéldányos feladat](batch-mpi.md)fő csomópontja-e. A lehetséges értékek a következők: `true` és `false` .| Több példányos elsődleges és alfeladatok. | `true` |
| AZ_BATCH_JOB_ID                 | Annak a fióknak az azonosítója, amelyhez a feladat tartozik. | Minden feladat, kivéve a kezdési feladatot. | batchjob001 |
| AZ_BATCH_JOB_PREP_DIR           | A csomóponton a feladat-előkészítési [feladat könyvtárának](files-and-directories.md) teljes elérési útja. | Minden feladat, a kezdési feladat és a feladat-előkészítési feladat kivételével. Csak akkor érhető el, ha a feladat feladat-előkészítési feladattal van konfigurálva. | C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation |
| AZ_BATCH_JOB_PREP_WORKING_DIR   | A csomóponton a feladat-előkészítési [feladat munkakönyvtárának](files-and-directories.md) teljes elérési útja. | Minden feladat, a kezdési feladat és a feladat-előkészítési feladat kivételével. Csak akkor érhető el, ha a feladat feladat-előkészítési feladattal van konfigurálva. | C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation\wd |
| AZ_BATCH_MASTER_NODE            | Annak a számítási csomópontnak az IP-címe és portja, amelyen egy [többpéldányos feladat](batch-mpi.md) elsődleges feladata fut. Ne használja az itt megadott portot MPI-vagy NCCL-kommunikációhoz – a Azure Batch szolgáltatás számára van fenntartva. Használja inkább a MASTER_PORT változót, vagy állítsa be úgy, hogy a parancssori argumentumon keresztül átadott értékkel (a 6105-es port jó alapértelmezett választás) vagy a pénzmosás-készletek értékének használatával adja meg a következőt:. | Több példányos elsődleges és alfeladatok. | `10.0.0.4:6000` |
| AZ_BATCH_NODE_ID                | Annak a csomópontnak az azonosítója, amelyhez a feladat hozzá van rendelve. | Minden feladat. | TVM-1219235766_3-20160919t172711z |
| AZ_BATCH_NODE_IS_DEDICATED      | Ha `true` az aktuális csomópont egy dedikált csomópont. Ha `false` Ez egy [alacsony prioritású csomópont](batch-low-pri-vms.md). | Minden feladat. | `true` |
| AZ_BATCH_NODE_LIST              | A [többpéldányos feladat](batch-mpi.md) számára a formátumban lefoglalt csomópontok listája `nodeIP;nodeIP` . | Több példányos elsődleges és alfeladatok. | `10.0.0.4;10.0.0.5` |
| AZ_BATCH_NODE_MOUNTS_DIR        | A csomópont szintű [fájlrendszer csatlakoztatási](virtual-file-mount.md) helyének teljes elérési útja, ahol az összes csatlakoztatási könyvtár található. A Windows-fájlmegosztás meghajtóbetűjelet használ, így a Windows esetében a csatlakoztatási meghajtó az eszközök és meghajtók részét képezi.  |  Minden feladat, beleértve az indítási feladatot, hozzáférhet a felhasználóhoz, mivel a felhasználó ismeri a csatlakoztatott könyvtár csatlakoztatási engedélyeit. | Az Ubuntuban például a hely a következő: `/mnt/batch/tasks/fsmounts` |
| AZ_BATCH_NODE_ROOT_DIR          | A csomóponton lévő összes [Batch-könyvtár](files-and-directories.md) gyökerének teljes elérési útja. | Minden feladat. | C:\user\tasks |
| AZ_BATCH_NODE_SHARED_DIR        | A csomóponton a [megosztott könyvtár](files-and-directories.md) teljes elérési útja. A csomóponton végrehajtandó összes feladat olvasási/írási hozzáféréssel rendelkezik ehhez a címtárhoz. A más csomópontokon végrehajtandó feladatok nem rendelkeznek távoli hozzáféréssel ehhez a címtárhoz (nem "megosztott" hálózati könyvtár). | Minden feladat. | C:\user\tasks\shared |
| AZ_BATCH_NODE_STARTUP_DIR       | A csomóponton a [Start feladat könyvtárának](files-and-directories.md) teljes elérési útja. | Minden feladat. | C:\user\tasks\startup |
| AZ_BATCH_POOL_ID                | Annak a készletnek azonosítója, amelyen a tevékenység fut. | Minden feladat. | batchpool001 |
| AZ_BATCH_TASK_DIR               | A csomóponton a [feladat könyvtárának](files-and-directories.md) teljes elérési útja. Ez a könyvtár tartalmazza a `stdout.txt` és a `stderr.txt` feladatot, valamint a AZ_BATCH_TASK_WORKING_DIR. | Minden feladat. | C:\user\tasks\workitems\batchjob001\job-1\task001 |
| AZ_BATCH_TASK_ID                | Az aktuális tevékenység azonosítója. | Minden feladat, kivéve a kezdési feladatot. | task001 |
| AZ_BATCH_TASK_SHARED_DIR | Egy [többpéldányos feladat](batch-mpi.md)elsődleges feladatához és minden feladatához azonos könyvtár elérési útja. Az elérési út minden olyan csomóponton megtalálható, amelyen a többpéldányos feladat fut, és az olvasási/írási hozzáférés az adott csomóponton futó feladatok parancsai számára (a [koordinációs parancs](batch-mpi.md#coordination-command) és az [alkalmazási parancs](batch-mpi.md#application-command)egyaránt elérhető). Az alfeladatok vagy a más csomópontokon végrehajtandó elsődleges feladatok nem rendelkeznek távoli hozzáféréssel ehhez a címtárhoz (nem "megosztott" hálózati könyvtár). | Több példányos elsődleges és alfeladatok. | C:\user\tasks\workitems\multiinstancesamplejob\job-1\multiinstancesampletask |
| AZ_BATCH_TASK_WORKING_DIR       | A csomóponton a [feladat munkakönyvtárának](files-and-directories.md) teljes elérési útja. A jelenleg futó feladat írási/olvasási hozzáféréssel rendelkezik ehhez a címtárhoz. | Minden feladat. | C:\user\tasks\workitems\batchjob001\job-1\task001\wd |
| AZ_BATCH_TASK_WORKING_DIR       | A csomóponton a [feladat munkakönyvtárának](files-and-directories.md) teljes elérési útja. A jelenleg futó feladat írási/olvasási hozzáféréssel rendelkezik ehhez a címtárhoz. | Minden feladat. | C:\user\tasks\workitems\batchjob001\job-1\task001\wd |
| AZ_BATCH_TASK_RESERVED_EPHEMERAL_DISK_SPACE_BYTES | A lemezterület aktuális küszöbértéke, amelyre a virtuális gép megjelölve lesz `DiskFull` . | Minden feladat. | 1000000 |
| CCP_NODES                       | A [többpéldányos feladathoz](batch-mpi.md)lefoglalt csomópontok és magok száma. A csomópontok és a magok formátuma az alábbiak szerint jelenik meg: `numNodes<space>node1IP<space>node1Cores<space>`<br/>`node2IP<space>node2Cores<space> ...`, ahol a csomópontok számát egy vagy több csomópont IP-címe, valamint a magok száma követi. |  Több példányos elsődleges és alfeladatok. |`2 10.0.0.4 1 10.0.0.5 1` |

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan [használhat környezeti változókat a Batch](jobs-and-tasks.md#environment-settings-for-tasks)szolgáltatással.
- További információ a [Batch-fájlokról és-címtárakról](files-and-directories.md)
- A [többpéldányos feladatok](batch-mpi.md)megismerése.
