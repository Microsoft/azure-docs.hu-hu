---
title: Git-integráció Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Megtudhatja, Azure Machine Learning hogyan integrálható egy helyi Git-adattárakba az adattár, az ág és az aktuális véglegesítési információk betanítás során való nyomon követéséhez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.date: 04/08/2021
ms.openlocfilehash: 60dca43f95b190791c8fb593042ed612340a3af5
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874548"
---
# <a name="git-integration-for-azure-machine-learning"></a>Git-integráció Azure Machine Learning

[A Git](https://git-scm.com/) egy népszerű verzióvezérlő rendszer, amely lehetővé teszi a projektek megosztását és közös működését. 

Azure Machine Learning teljes mértékben támogatja a Git-adattárakat a munka nyomon követéséhez – az adattárakat közvetlenül a megosztott munkaterület fájlrendszerére klónozhatja, a Gitet a helyi munkaállomáson használhatja, vagy a Gitet ci-/CD-folyamatból is használhatja.

Amikor feladatot ad Azure Machine Learning, ha a forrásfájlokat egy helyi Git-adattárban tárolják, akkor a betanítási folyamat részeként nyomon követi az adattár információit.

Mivel Azure Machine Learning helyi Git-adattár információit követi nyomon, nincs konkrét központi tárházhoz kötve. Az adattár klónozható a GitHubról, a GitLab-ről, a Bitbucketről, az Azure DevOpsról vagy bármely más git-kompatibilis szolgáltatásból.

> [!TIP]
> A Visual Studio Code használatával egy grafikus felhasználói felületen keresztül kommunikálhat a Gittel. Ha egy távoli számítási Azure Machine Learning-példányhoz szeretne csatlakozni a Visual Studio Code használatával, tekintse meg a Csatlakozás Azure Machine Learning számítási példányhoz az [Visual Studio Code (előzetes verzió) szolgáltatásban)](how-to-set-up-vs-code-remote.md)
>
> További információ a code Visual Studio funkcióiról: [Using Version Control in VS Code (Verzióvezérlés](https://code.visualstudio.com/docs/editor/versioncontrol) használata a VS Code-ban) és Working with GitHub in VS Code (A [GitHub használata a VS Code-ban).](https://code.visualstudio.com/docs/editor/github)

## <a name="clone-git-repositories-into-your-workspace-file-system"></a>Git-adattárak klónozása a munkaterület fájlrendszerébe
Azure Machine Learning megosztott fájlrendszert biztosít a munkaterület összes felhasználója számára.
Ha Git-adattárat szeretné klónozni ebbe a fájlmegosztásba, javasoljuk, hogy hozzon létre egy számítási példányt, & [nyisson meg egy terminált.](how-to-access-terminal.md)
A terminál megnyitása után hozzáférhet egy teljes Git-ügyfélhez, és a Git CLI használatával klónozhatja és használhatja a Gitet.

Javasoljuk, hogy klónozza az adattárat a felhasználók könyvtárába, hogy mások ne ütköznek közvetlenül a munkaágban.

Bármilyen git-adattárat klónozhat, amelybe hitelesíthet (GitHub, Azure Repos, BitBucket stb.)

A klónozásról a Git CLI használatával kapcsolatos [útmutatóban található további információ.](https://guides.github.com/introduction/git-handbook/)

## <a name="authenticate-your-git-account-with-ssh"></a>Git-fiók hitelesítése SSH-val
### <a name="generate-a-new-ssh-key"></a>Új SSH-kulcs létrehozása
1) [Nyissa meg a terminálablakot](./how-to-access-terminal.md) a Azure Machine Learning Notebook lapon.

2) Illessze be az alábbi szöveget, és az e-mail-címét helyettesítve.

```bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

Ez létrehoz egy új SSH-kulcsot, címkeként használva a megadott e-mailt.

```
> Generating public/private rsa key pair.
```

3) Amikor a rendszer az "Enter a file in which to save the key" (Írja be a fájlt, amelybe a kulcsot menteni fogja) kérdésre, nyomja le az Enter billentyűt. Ez fogadja el az alapértelmezett fájlhelyet.

4) Ellenőrizze, hogy az alapértelmezett hely a /home/azureuser/.ssh, majd nyomja le az Enter billentyűt. Ellenkező esetben adja meg a /home/azureuser/.ssh helyet.

> [!TIP]
> Győződjön meg arról, hogy az SSH-kulcs a /home/azureuser/.ssh fájlban van mentve. Ez a fájl a számítási példányon van mentve, csak a számítási példány tulajdonosa számára érhető el

```
> Enter a file in which to save the key (/home/azureuser/.ssh/id_rsa): [Press enter]
```

5) Amikor a rendszer kéri, adjon meg egy biztonságos jelszót. Javasoljuk, hogy adjon hozzá egy jelszót az SSH-kulcshoz a további biztonság érdekében

```
> Enter passphrase (empty for no passphrase): [Type a passphrase]
> Enter same passphrase again: [Type passphrase again]
```

### <a name="add-the-public-key-to-git-account"></a>A nyilvános kulcs hozzáadása a Git-fiókhoz
1) A terminálablakban másolja ki a nyilvános kulcsfájl tartalmát. Ha átnevezte a kulcsot, cserélje id_rsa.pub helyére a nyilvános kulcs fájlnevét.

```bash
cat ~/.ssh/id_rsa.pub
```
> [!TIP]
> **Másolás és beillesztés a terminálban**
> * Windows: `Ctrl-Insert` a másoláshoz és a vagy a `Ctrl-Shift-v` `Shift-Insert` beillesztéshez.
> * Mac OS: `Cmd-c` másoláshoz és `Cmd-v` beillesztéshez.
> * Előfordulhat, hogy a FireFox/IE nem támogatja megfelelően a vágólapra vonatkozó engedélyeket.

2) Jelölje ki és másolja a vágólapra a kulcskimenetet.

+ [GitHub](https://docs.github.com/github/authenticating-to-github/adding-a-new-ssh-key-to-your-github-account)

+ [GitLab](https://docs.gitlab.com/ee/ssh/#adding-an-ssh-key-to-your-gitlab-account)

+ [Azure DevOps](/azure/devops/repos/git/use-ssh-keys-to-authenticate#step-2--add-the-public-key-to-azure-devops-servicestfs)  Kezdje a **2. lépéssel.**

+ [BitBucket](https://support.atlassian.com/bitbucket-cloud/docs/set-up-an-ssh-key/#SetupanSSHkey-ssh2). Kezdje a **4. lépéssel.**

### <a name="clone-the-git-repository-with-ssh"></a>A Git-adattár klónozása SSH-val

1) Másolja ki az SSH Git-klón URL-címét a Git-adattárból.

2) Illessze be az URL-címet az alábbi `git clone` parancsba az SSH Git-adattár URL-címének használatára. Ez a következőre hasonlít:

```bash
git clone git@example.com:GitUser/azureml-example.git
Cloning into 'azureml-example'...
```

A következő hasonló választ fog látni:

```bash
The authenticity of host 'example.com (192.30.255.112)' can't be established.
RSA key fingerprint is SHA256:nThbg6kXUpJWGl7E1IGOCspRomTxdCARLviKw6E5SY8.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'github.com,192.30.255.112' (RSA) to the list of known hosts.
```

Előfordulhat, hogy az SSH megjeleníti a kiszolgáló SSH-ujjlenyomatát, és megkéri, hogy ellenőrizze. Ellenőrizze, hogy a megjelenített ujjlenyomat megegyezik-e az egyik ujjlenyomattal az SSH nyilvános kulcsok oldalán.

Az SSH ezt az ujjlenyomatot jeleníti meg, amikor egy ismeretlen gazdagéphez csatlakozik, hogy megvédje a ["man-in-the-middle" támadásoktól.](/previous-versions/windows/it-pro/windows-2000-server/cc959354(v=technet.10)) Ha elfogadja a gazdagép ujjlenyomatát, az SSH csak akkor kéri újra, ha az ujjlenyomat megváltozik.

3) Amikor a kapcsolódás folytatásáról ad kérdést, írja be a következőt: `yes` . A Git klónozza az adattárat, és beállít egy távoli forrást az SSH-val való csatlakozáshoz a jövőbeli Git-parancsokhoz.

## <a name="track-code-that-comes-from-git-repositories"></a>Git-adattárakból származó kód nyomon követése

Amikor elküldi a betanítás futtatását a Python SDK-ból vagy a Machine Learning CLI-ről, a modell betanításához szükséges fájlokat a rendszer feltölti a munkaterületre. Ha a parancs elérhető a fejlesztési környezetben, a feltöltési folyamat annak ellenőrzéséhez használja, hogy a fájlok `git` git-adattárban vannak-e tárolva. Ha igen, akkor a git-adattárból származó információk is fel vannak töltve a betanítás során. Ezek az információk a betanítás futtatásának alábbi tulajdonságaiban vannak tárolva:

| Tulajdonság | Az érték lekért git-parancsa | Description |
| ----- | ----- | ----- |
| `azureml.git.repository_uri` | `git ls-remote --get-url` | Az URI, amelyből az adattár klónozva lett. |
| `mlflow.source.git.repoURL` | `git ls-remote --get-url` | Az URI, amelyből az adattár klónozva lett. |
| `azureml.git.branch` | `git symbolic-ref --short HEAD` | A futtatás elküldött aktív ága. |
| `mlflow.source.git.branch` | `git symbolic-ref --short HEAD` | A futtatás elküldött aktív ága. |
| `azureml.git.commit` | `git rev-parse HEAD` | A futtatáshoz elküldött kód véglegesítési kivonata. |
| `mlflow.source.git.commit` | `git rev-parse HEAD` | A futtatáshoz elküldött kód véglegesítési kivonata. |
| `azureml.git.dirty` | `git status --porcelain .` | `True`, ha az ág/véglegesítés nem megfelelő; Ellenkező `false` esetben: . |

A rendszer ezeket az információkat olyan futtatásokkal küldi el, amelyek becslátort, gépi tanulási folyamatot vagy szkriptfuttatást használnak.

Ha a betanítás fájljai nem a fejlesztési környezet git-adattárában találhatók, vagy a parancs nem érhető el, akkor a rendszer nem követi nyomon a Gittel kapcsolatos `git` információkat.

> [!TIP]
> Annak ellenőrzéséhez, hogy a git parancs elérhető-e a fejlesztési környezetben, nyisson meg egy felületi munkamenetet, egy parancssort, egy PowerShellt vagy más parancssori felületet, és írja be a következő parancsot:
>
> ```
> git --version
> ```
>
> Ha telepítve van, és az elérési úton a következő hasonló választ kapja: `git version 2.4.1` . A git fejlesztői környezetben való telepítésével kapcsolatos további információkért tekintse meg a [Git webhelyét.](https://git-scm.com/)

## <a name="view-the-logged-information"></a>A naplózott adatok megtekintése

A git-adatok a betanítás futtatásának tulajdonságaiban vannak tárolva. Ezt az információt megtekintheti a Azure Portal, a Python SDK és a CLI használatával. 

### <a name="azure-portal"></a>Azure Portal

1. A [studioportálon](https://ml.azure.com)válassza ki a munkaterületét.
1. Válassza __a Kísérletek__ lehetőséget, majd válasszon egyet a kísérletek közül.
1. Válasszon ki egy futtatást a __RUN NUMBER (FUTTATÁS SZÁMA) oszlopból.__
1. Válassza __a Kimenetek + naplók__ lehetőséget, majd bontsa ki a __naplókat__ és __az azureml-bejegyzéseket.__ Válassza ki az azure-ral kezdődik __### \_ hivatkozást.__

A naplózott adatok a következő JSON-hoz hasonló szöveget tartalmaznak:

```json
"properties": {
    "_azureml.ComputeTargetType": "batchai",
    "ContentSnapshotId": "5ca66406-cbac-4d7d-bc95-f5a51dd3e57e",
    "azureml.git.repository_uri": "git@github.com:azure/machinelearningnotebooks",
    "mlflow.source.git.repoURL": "git@github.com:azure/machinelearningnotebooks",
    "azureml.git.branch": "master",
    "mlflow.source.git.branch": "master",
    "azureml.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "mlflow.source.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "azureml.git.dirty": "True",
    "AzureML.DerivedImageName": "azureml/azureml_9d3568242c6bfef9631879915768deaf",
    "ProcessInfoFile": "azureml-logs/process_info.json",
    "ProcessStatusFile": "azureml-logs/process_status.json"
}
```

### <a name="python-sdk"></a>Python SDK

A betanítási futtatás elküldése után [a](/python/api/azureml-core/azureml.core.run%28class%29) rendszer futtatási objektumot ad vissza. Az `properties` objektum attribútuma tartalmazza a naplózott git-adatokat. A következő kód például a véglegesítési kivonatot olvassa be:

```python
run.properties['azureml.git.commit']
```

### <a name="cli"></a>parancssori felület

A `az ml run` CLI-paranccsal lekérheti a tulajdonságokat egy futtatásból. A következő parancs például a nevű kísérlet utolsó futtatásának tulajdonságait adja `train-on-amlcompute` vissza:

```azurecli-interactive
az ml run list -e train-on-amlcompute --last 1 -w myworkspace -g myresourcegroup --query '[].properties'
```

További információt az [az ml run referenciadokumentációban](/cli/azure/ml/run) talál.

## <a name="next-steps"></a>Következő lépések

* [Számítási célok használata a modell betanításhoz](how-to-set-up-training-targets.md)