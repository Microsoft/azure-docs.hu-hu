---
title: GitRepo-kötet csatlakoztatása tárolócsoporthoz
description: Megtudhatja, hogyan csatlakoztatható gitRepo-kötet Git-adattár tárolópéldányokhoz való klónozása
ms.topic: article
ms.date: 06/15/2018
ms.openlocfilehash: 7c1249e3120dd680c52bf74fb045bedf5202b9f2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763720"
---
# <a name="mount-a-gitrepo-volume-in-azure-container-instances"></a>GitRepo-kötet csatlakoztatása a Azure Container Instances

Megtudhatja, hogyan csatlakoztatható *egy gitRepo-kötet* egy Git-adattár tárolópéldányba való klónozáshoz.

> [!NOTE]
> A *gitRepo kötet* csatlakoztatása jelenleg Linux-tárolókra korlátozódik. Miközben dolgozunk a Windows-tárolók összes szolgáltatásának elérhető elérhetőségen, a jelenlegi platformbeli különbségeket a áttekintésben [találja.](container-instances-overview.md#linux-and-windows-containers)

## <a name="gitrepo-volume"></a>gitRepo kötet

A *gitRepo kötet* csatlakoztat egy könyvtárat, és klónozza a megadott Git-adattárat a tároló indításakor. Ha *gitRepo-kötetet használ* a tárolópéldányokhoz, elkerülheti a kód hozzáadását az alkalmazásokban.

*GitRepo-kötet* csatlakoztatásakor három tulajdonságot állíthat be a kötet konfiguráláshoz:

| Tulajdonság | Kötelező | Leírás |
| -------- | -------- | ----------- |
| `repository` | Igen | A klónozni kívánt Git-adattár teljes URL-címe, beleértve vagy `http://` `https://` a teljes URL-címet.|
| `directory` | No | Az a könyvtár, amelybe az adattárat klónozni kell. Az elérési út nem tartalmazhat vagy kezdhet a következővel: " `..` .  Ha a "" értéket adja meg, a rendszer klónozza az adattárat a kötet `.` könyvtárába. Ellenkező esetben a Git-adattár a megadott név alkönyvtárába lesz klónozva a volume könyvtárban. |
| `revision` | No | A klónozni fog változat véglegesítési kivonata. Ha nincs meghatározva, a `HEAD` rendszer klónozza a változatot. |

## <a name="mount-gitrepo-volume-azure-cli"></a>GitRepo-kötet csatlakoztatása: Azure CLI

GitRepo-kötet csatlakoztatásához, amikor tárolópéldányokat helyez üzembe az [Azure CLI-ben,](/cli/azure)a és paramétereket az az container create `--gitrepo-url` `--gitrepo-mount-path` [parancsban kell][az-container-create] megírni. Igény szerint megadhatja a klónozni kívánt köteten belüli könyvtárat ( ) és a klónozni kívánt változat `--gitrepo-dir` véglegesítési kivonatát ( `--gitrepo-revision` ).

Ez a példaparancs klónozza a Microsoft [aci-helloworld][aci-helloworld] mintaalkalmazást a `/mnt/aci-helloworld` tárolópéldányba:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name hellogitrepo \
    --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --dns-name-label aci-demo \
    --ports 80 \
    --gitrepo-url https://github.com/Azure-Samples/aci-helloworld \
    --gitrepo-mount-path /mnt/aci-helloworld
```

Annak ellenőrzéséhez, hogy a gitRepo kötet csatlakoztatva van-e, indítson el egy rendszerhéjat a tárolóban [az az container exec használatával,][az-container-exec] és listából a könyvtárat:

```azurecli
az container exec --resource-group myResourceGroup --name hellogitrepo --exec-command /bin/sh
```

```output
/usr/src/app # ls -l /mnt/aci-helloworld/
total 16
-rw-r--r--    1 root     root           144 Apr 16 16:35 Dockerfile
-rw-r--r--    1 root     root          1162 Apr 16 16:35 LICENSE
-rw-r--r--    1 root     root          1237 Apr 16 16:35 README.md
drwxr-xr-x    2 root     root          4096 Apr 16 16:35 app
```

## <a name="mount-gitrepo-volume-resource-manager"></a>GitRepo-kötet csatlakoztatása: Resource Manager

GitRepo-kötet csatlakoztatása, amikor tárolópéldányokat helyez üzembe egy Azure Resource Manager-sablonnal, először töltse ki a tömböt a sablon tárolócsoport [](/azure/templates/microsoft.containerinstance/containergroups) `volumes` `properties` szakaszában. Ezután a tárolócsoport minden olyan tárolója esetén, amelyben csatlakoztatni szeretné a *gitRepo* kötetet, töltse ki a tárolódefiníció szakaszában található `volumeMounts` `properties` tömböt.

A következő példasablon Resource Manager egy tárolócsoportot hoz létre, amely egyetlen tárolóból áll. A tároló két GitHub-adattárat klónoz, amelyek a *gitRepo* kötetblokkjaiban vannak megadva. A második kötet további tulajdonságokat tartalmaz, amelyek meghatározzák a klónozni kívánt könyvtárat, valamint a klónozni kívánt adott változat véglegesítési kivonatát.

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-gitrepo.json -->
[!code-json[volume-gitrepo](~/azure-docs-json-samples/container-instances/aci-deploy-volume-gitrepo.json)]

Az előző sablonban definiált két klónozott adattár eredményül kapott könyvtárstruktúrája a következő:

```
/mnt/repo1/aci-helloworld
/mnt/repo2/my-custom-clone-directory
```

A tárolópéldányok üzembe helyezésére egy példa Azure Resource Manager sablonnal: Többtárolós csoportok üzembe helyezése a [Azure Container Instances.](container-instances-multi-container-group.md)

## <a name="private-git-repo-authentication"></a>Privát Git-adattár hitelesítése

Ha gitRepo-kötetet csatlakoztat egy privát Git-adattárhoz, adja meg a hitelesítő adatokat az adattár URL-címében. A hitelesítő adatok általában egy felhasználónév és egy személyes hozzáférési jogkivonat (PAT) formájában oszlnak meg, amely hatókörrel rendelkezik az adattárhoz.

Egy privát GitHub-adattár Azure CLI-paramétere például a következőre hasonlít `--gitrepo-url` (ahol a "gituser" a GitHub-felhasználónév, az "abcdef1234fdsa4321abcdef" pedig a felhasználó személyes hozzáférési jogkivonata):

```console
--gitrepo-url https://gituser:abcdef1234fdsa4321abcdef@github.com/GitUser/some-private-repository
```

Azure Repos Git-adattárhoz adjon meg egy felhasználónevet (a következő példában az "azurereposuser" nevet használhatja) érvényes PAT-sel kombinálva:

```console
--gitrepo-url https://azurereposuser:abcdef1234fdsa4321abcdef@dev.azure.com/your-org/_git/some-private-repository
```

A GitHubhoz és az Azure Reposhoz elérhető személyes hozzáférési jogkivonatokkal kapcsolatos további információkért tekintse meg a következőket:

GitHub: [Személyes hozzáférési jogkivonat létrehozása a parancssorhoz][pat-github]

Azure Repos: [Személyes hozzáférési jogkivonatok létrehozása][pat-repos] a hozzáférés hitelesítéséhez

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan csatlakoztatható más kötettípusok a Azure Container Instances:

* [Azure-fájlmegosztás csatlakoztatása az Azure Container Instancesben](container-instances-volume-azure-files.md)
* [EmptyDir kötet csatlakoztatása a Azure Container Instances](container-instances-volume-emptydir.md)
* [Titkos kötet csatlakoztatása a Azure Container Instances](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-helloworld]: https://github.com/Azure-Samples/aci-helloworld
[pat-github]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
[pat-repos]: /azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-exec]: /cli/azure/container#az_container_exec
