---
title: Gitrepo típusú-kötet csatlakoztatása a tároló csoportjához
description: Megtudhatja, hogyan csatlakoztathat egy Gitrepo típusú-kötetet a git-tárház klónozásához a Container instances szolgáltatásba
ms.topic: article
ms.date: 06/15/2018
ms.openlocfilehash: 5f361b0bbd4a02f754e47c8321a664af05036601
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86260709"
---
# <a name="mount-a-gitrepo-volume-in-azure-container-instances"></a>Gitrepo típusú-kötet csatlakoztatása Azure Container Instances

Megtudhatja, hogyan csatlakoztathat egy *gitrepo típusú* -kötetet a git-tárház klónozásához a Container instances szolgáltatásban.

> [!NOTE]
> A *gitrepo típusú* -kötet csatlakoztatása jelenleg csak Linux-tárolók számára engedélyezett. Miközben dolgozunk a Windows-tárolók összes funkciójának bekapcsolásán, az [áttekintésben](container-instances-overview.md#linux-and-windows-containers)megtalálhatja az aktuális platformmal kapcsolatos különbségeket.

## <a name="gitrepo-volume"></a>Gitrepo típusú-kötet

A *gitrepo típusú* kötet csatlakoztat egy könyvtárat, és a tároló indításakor a megadott git-tárházat klónozottba helyezi. Ha *gitrepo típusú* -kötetet használ a tároló példányaiban, elkerülheti a kód hozzáadását az alkalmazásaiban.

*Gitrepo típusú* -kötet csatlakoztatásakor a kötet konfigurálásához három tulajdonságot állíthat be:

| Tulajdonság | Kötelező | Leírás |
| -------- | -------- | ----------- |
| `repository` | Igen | A klónozott git-tárház teljes URL-címe, beleértve a vagy a-t is `http://` `https://` .|
| `directory` | No | A könyvtárat, amelybe a tárházat klónozottnak kell lennie. Az elérési út nem tartalmazhat vagy kezdődhet a következővel: " `..` ".  Ha a "" lehetőséget választja `.` , a tárház klónozása a kötet könyvtárába történik. Ellenkező esetben a git-tárház klónozása a megadott név alkönyvtárába történik a kötet könyvtárában. |
| `revision` | No | A klónozott változat véglegesített kivonata. Ha nincs megadva, a `HEAD` változat klónozásra kerül. |

## <a name="mount-gitrepo-volume-azure-cli"></a>Gitrepo típusú-kötet csatlakoztatása: Azure CLI

Ha Gitrepo típusú-kötetet szeretne csatlakoztatni, amikor tároló-példányokat helyez üzembe az [Azure CLI](/cli/azure)-vel, adja meg a `--gitrepo-url` és a `--gitrepo-mount-path` paramétereket az az [Container Create][az-container-create] paranccsal. Opcionálisan megadhatja a köteten belüli könyvtárat a klónozás a következőbe: ( `--gitrepo-dir` ), valamint a verzió elküldési kivonatának klónozását ( `--gitrepo-revision` ).

Ez a példában szereplő parancs klónozott a Microsoft [ACI-HelloWorld][aci-helloworld] minta alkalmazást a `/mnt/aci-helloworld` tároló-példányba:

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

A Gitrepo típusú kötet csatlakoztatásának ellenőrzéséhez indítson el egy rendszerhéjt a tárolóban az [az Container exec][az-container-exec] paranccsal, és sorolja fel a könyvtárat:

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

## <a name="mount-gitrepo-volume-resource-manager"></a>Gitrepo típusú-kötet csatlakoztatása: Resource Manager

Ha Gitrepo típusú-kötetet szeretne csatlakoztatni, amikor [Azure Resource Manager sablonnal](/azure/templates/microsoft.containerinstance/containergroups)telepít tároló példányokat, először töltse ki a `volumes` tömböt a `properties` sablon tároló csoport szakaszában. Ezt követően a tároló csoport minden olyan tárolójában, amelyben a *gitrepo típusú* -kötetet csatlakoztatni szeretné, töltse ki a `volumeMounts` tömböt a `properties` tároló definíciójának szakaszában.

A következő Resource Manager-sablon például létrehoz egy tároló csoportot, amely egyetlen tárolóból áll. A tároló klónozása két GitHub-tárházat határoz meg a *gitrepo típusú* . A második kötet további tulajdonságokat is tartalmaz, amelyek megadják a klónozáshoz szükséges könyvtárat, valamint a klónozás egy adott változatának véglegesítő kivonatát.

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-gitrepo.json -->
[!code-json[volume-gitrepo](~/azure-docs-json-samples/container-instances/aci-deploy-volume-gitrepo.json)]

Az előző sablonban definiált két klónozott repó eredményeként létrejövő címtár-struktúra a következő:

```
/mnt/repo1/aci-helloworld
/mnt/repo2/my-custom-clone-directory
```

Ha egy Azure Resource Manager sablonnal szeretné megtekinteni a tároló példány központi telepítésének példáját, tekintse meg a [többtárolós csoportok telepítése Azure Container Instancesban](container-instances-multi-container-group.md)című témakört.

## <a name="private-git-repo-authentication"></a>Privát git-tárház hitelesítése

Ha egy privát git-tárházhoz szeretne Gitrepo típusú-kötetet csatlakoztatni, akkor a tárház URL-címében a hitelesítő adatokat kell megadni. A hitelesítő adatok általában egy Felhasználónév és egy személyes hozzáférési jogkivonat (PAT) formájában vannak, amely hatókörön belüli hozzáférést biztosít a tárházhoz.

`--gitrepo-url`Egy privát GitHub-tárházhoz tartozó Azure CLI-paraméter például a következőhöz hasonlóan jelenik meg (ahol a "gituser" a GitHub-Felhasználónév, a "abcdef1234fdsa4321abcdef" pedig a felhasználó személyes hozzáférési jogkivonata):

```console
--gitrepo-url https://gituser:abcdef1234fdsa4321abcdef@github.com/GitUser/some-private-repository
```

Egy Azure Repos git-tárház esetében adja meg a felhasználónevet (a következő példában a "azurereposuser" kifejezést használhatja) egy érvényes PAT-sel együtt:

```console
--gitrepo-url https://azurereposuser:abcdef1234fdsa4321abcdef@dev.azure.com/your-org/_git/some-private-repository
```

A GitHub és az Azure Repos személyes hozzáférési jogkivonatával kapcsolatos további információkért tekintse meg a következőket:

GitHub: [személyes hozzáférési jogkivonat létrehozása a parancssorhoz][pat-github]

Azure Repos: [személyes hozzáférési tokenek létrehozása a hozzáférés hitelesítéséhez][pat-repos]

## <a name="next-steps"></a>Következő lépések

További mennyiségi típusok csatlakoztatása a Azure Container Instancesban:

* [Azure-fájlmegosztás csatlakoztatása az Azure Container Instancesben](container-instances-volume-azure-files.md)
* [EmptyDir-kötet csatlakoztatása Azure Container Instances](container-instances-volume-emptydir.md)
* [Titkos kötet csatlakoztatása Azure Container Instances](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-helloworld]: https://github.com/Azure-Samples/aci-helloworld
[pat-github]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
[pat-repos]: /azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
