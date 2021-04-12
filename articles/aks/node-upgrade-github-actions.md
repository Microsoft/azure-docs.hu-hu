---
title: AK-csomópontok frissítéseinek kezelése GitHub-műveletekkel
titleSuffix: Azure Kubernetes Service
description: Ismerje meg, hogyan frissítheti az AK-csomópontokat a GitHub-műveletekkel
services: container-service
ms.topic: article
ms.date: 11/27/2020
ms.openlocfilehash: 6876cf1e5044246492e249d8a61060cbeac46f96
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98217957"
---
# <a name="apply-security-updates-to-azure-kubernetes-service-aks-nodes-automatically-using-github-actions"></a>Biztonsági frissítések alkalmazása az Azure Kubernetes szolgáltatás (ak) csomópontjaira automatikusan GitHub-műveletek használatával

A biztonsági frissítések kulcsfontosságú részét képezik az AK-fürt biztonságának és megfelelőségének fenntartásának a mögöttes operációs rendszerhez tartozó legújabb javításokkal. Ezek a frissítések operációsrendszer-biztonsági javításokat vagy kernel-frissítéseket tartalmaznak. Egyes frissítésekhez csomópont-újraindítás szükséges a folyamat befejezéséhez.

A Futtatás során `az aks upgrade` a rendszer nulla állásidőt biztosít a frissítések alkalmazásához. A parancs kezeli a legújabb frissítéseket a fürt összes csomópontján, a csomópontokra irányítja és kiüríti a forgalmat, majd újraindítja a csomópontokat, majd engedélyezi a forgalmat a frissített csomópontokra. Ha más módszerrel frissíti a csomópontokat, az AK nem indítja automatikusan újra a csomópontokat.

> [!NOTE]
> A jelzővel való használata esetén a fő különbség az `az aks upgrade` `--node-image-only` , hogy amikor használatban van, csak a csomóponti lemezképek lesznek frissítve. Ha nincs megadva, a rendszer frissíti a csomóponti rendszerképeket és a Kubernetes-vezérlési sík verzióját is. A Részletes információkért tekintse meg a következő témakört: a [felügyelt frissítések a csomópontokon][managed-node-upgrades-article] és [a fürt frissítéseihez készült dokumentációjában][cluster-upgrades-article] .

Minden Kubernetes-csomópont egy szabványos Azure-beli virtuális gépen (VM) fut. Ezek a virtuális gépek Windows-vagy Linux-alapúak lehetnek. A Linux-alapú virtuális gépek Ubuntu-rendszerképet használnak, és az operációs rendszer úgy van konfigurálva, hogy minden este automatikusan keressen frissítéseket.

Ha a parancsot használja `az aks upgrade` , az Azure CLI új csomópontokat hoz létre a legújabb biztonsági és kernel-frissítésekkel, ezek a csomópontok kezdetben úgy vannak kihelyezve, hogy megakadályozzák, hogy az alkalmazások ne legyenek ütemezve, amíg a frissítés be nem fejeződik. A befejezést követően az Azure cordos (lehetővé teszi, hogy a csomópont ne legyen elérhető az új munkaterhelések ütemezéséhez) és a kivezetés (a meglévő munkaterheléseket a másik csomópontra helyezi át) a régebbi csomópontok és az újak kiosztásával, ami gyakorlatilag az összes ütemezett alkalmazást az új csomópontokra ruházza át.

Ez a folyamat jobb, mint a Linux-alapú kernelek manuális frissítése, mert a Linux újraindítást igényel új kernel-frissítés telepítésekor. Ha manuálisan frissíti az operációs rendszert, akkor a virtuális gépet is újra kell indítania, és manuálisan kell kiürítenie az összes alkalmazást.

Ez a cikk bemutatja, hogyan automatizálható az AK-csomópontok frissítési folyamata. A GitHub-műveletekkel és az Azure CLI-vel az automatikusan futtatott frissítési feladatok hozhatók létre `cron` .

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy rendelkezik egy meglévő AK-fürttel. Ha AK-fürtre van szüksége, tekintse meg az AK gyors üzembe helyezését [Az Azure CLI használatával][aks-quickstart-cli] vagy [a Azure Portal használatával][aks-quickstart-portal].

Szüksége lesz az Azure CLI 2.0.59 vagy újabb verziójára is, valamint a telepítésre és konfigurálásra. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

A cikk azt is feltételezi, hogy egy [GitHub][github] -fiókkal hozza létre a műveleteit a alkalmazásban.

## <a name="create-a-timed-github-action"></a>Időzített GitHub-művelet létrehozása

`cron` a egy olyan segédprogram, amely lehetővé teszi parancsok vagy feladatok futtatását automatikus ütemterv szerint. Ha az AK-csomópontok automatikus ütemezett frissítéséhez szeretne feladatot létrehozni, szüksége lesz egy adattárra a műveletek üzemeltetéséhez. A GitHub-műveletek általában ugyanabban a tárházban vannak konfigurálva, mint az alkalmazás, de bármilyen tárházat használhat. Ebben a cikkben a [profil adattárát][profile-repository]fogjuk használni. Ha még nem rendelkezik ilyennel, hozzon létre egy új tárházat a GitHub-felhasználónevével megegyező névvel.

1. Navigáljon a tárházhoz a GitHubon
1. Kattintson a lap tetején található **műveletek** fülre.
1. Ha már beállított egy munkafolyamatot ebben az adattárban, a rendszer átirányítja a befejezett futtatások listájára, ebben az esetben kattintson az **Új munkafolyamat** gombra. Ha ez az első munkafolyamata az adattárban, a GitHub néhány Project-sablont fog bemutatni, kattintson a **munkafolyamat beállítása** hivatkozásra a Leírás szövege alatt.
1. Módosítsa a munkafolyamatot `name` és a `on` címkéket az alábbihoz hasonló módon. A GitHub-műveletek ugyanazt a [POSIX cron-szintaxist][cron-syntax] használják, mint bármely Linux-alapú rendszer. Ebben az ütemtervben a munkafolyamatot 15 naponta futtatjuk a 3am címen.

    ```yml
    name: Upgrade cluster node images
    on:
      schedule:
        - cron: '0 3 */15 * *'
    ```

1. Hozzon létre egy új feladatot az alábbi használatával. A feladatot a rendszer `upgrade-node` egy Ubuntu-ügynökön futtatja, és az Azure CLI-fiókjához fog csatlakozni, hogy végrehajtsa a csomópontok frissítéséhez szükséges lépéseket.

    ```yml
    name: Upgrade cluster node images

    on:
      schedule:
        - cron: '0 3 */15 * *'

    jobs:
      upgrade-node:
        runs-on: ubuntu-latest
    ```

## <a name="set-up-the-azure-cli-in-the-workflow"></a>Az Azure CLI beállítása a munkafolyamatban

A `steps` kulcsban meg kell határoznia a munkafolyamat által a csomópontok frissítéséhez végrehajtandó összes munkát.

Töltse le és jelentkezzen be az Azure CLI-be.

1. A GitHub-műveletek képernyő jobb oldalán keresse meg a *piactér keresési sávját* , és írja be az **"Azure login"** kifejezést.
1. Ennek eredményeképpen egy Azure **login** nevű művelet jelenik meg **Az Azure**-ban:

      :::image type="content" source="media/node-upgrade-github-actions/azure-login-search.png" alt-text="A keresési eredmények két sort mutatnak, az első művelet neve &quot;Azure bejelentkezhessen and the Second&quot; Azure Container Registry bejelentkezhessen":::

1. Kattintson az **Azure-bejelentkezés** lehetőségre. A következő képernyőn kattintson a kód minta jobb felső részén található **Másolás ikonra** .

    :::image type="content" source="media/node-upgrade-github-actions/azure-login.png" alt-text="Az Azure bejelentkezési művelet eredményének panelje, amely az alábbi kódrészlettel rendelkezik, a piros négyzet a másolási ikon kiemeli a kattintást":::

1. Illessze be a következőt a `steps` kulcs alá:

      ```yml
      name: Upgrade cluster node images

      on:
        schedule:
          - cron: '0 3 */15 * *'

      jobs:
        upgrade-node:
          runs-on: ubuntu-latest

          steps:
            - name: Azure Login
              uses: Azure/login@v1.1
              with:
                creds: ${{ secrets.AZURE_CREDENTIALS }}
      ```

1. Az Azure CLI-ből futtassa az alábbi parancsot egy új Felhasználónév és jelszó létrehozásához.

    ```azurecli-interactive
    az ad sp create-for-rbac -o json
    ```

    A kimenetnek a következő JSON-hez hasonlónak kell lennie:

    ```output
    {
      "appId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "displayName": "azure-cli-xxxx-xx-xx-xx-xx-xx",
      "name": "http://azure-cli-xxxx-xx-xx-xx-xx-xx",
      "password": "xXxXxXxXx",
      "tenant": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    }
    ```

1. **Egy új böngészőablakban** navigáljon a GitHub-tárházhoz, és nyissa meg a tárház **Beállítások** lapját. Kattintson a **titkok** elemre, majd az **új adattár titkos** elemére.
1. A *név mezőben* használja a következőt: `AZURE_CREDENTIALS` .
1.  Az *Érték mezőben* adja meg az előző lépés kimenetének teljes tartalmát, és hozzon létre egy új felhasználónevet és jelszót.

    :::image type="content" source="media/node-upgrade-github-actions/azure-credential-secret.png" alt-text="Az űrlap AZURE_CREDENTIALS titkos címnek, valamint a futtatott parancs JSON-ként beillesztett kimenetét jeleníti meg.":::

1. Kattintson a **titkos kód hozzáadása** lehetőségre.

A művelet által használt CLI naplózva lesz az Azure-fiókjába, és készen áll a parancsok futtatására.

Az Azure CLI-parancsok végrehajtásához szükséges lépések létrehozásához.

1. Navigáljon a *GitHub Marketplace* **keresési lapjára** a képernyő jobb oldalán, és keressen az *Azure CLI művelettel*. Válassza *Az Azure CLI-művelet az Azure*-ban lehetőséget.

    :::image type="content" source="media/node-upgrade-github-actions/azure-cli-action.png" alt-text="Keresési eredmény az &quot;Azure CLI Action&quot; esetében az első eredmény, amelyet az Azure készített":::

1. Kattintson a *GitHub piactéren* a Másolás gombra, és illessze be a művelet tartalmát a főszerkesztőbe az *Azure bejelentkezési* lépése alatt, a következőhöz hasonlóan:

    ```yml
    name: Upgrade cluster node images

    on:
      schedule:
        - cron: '0 3 */15 * *'

    jobs:
      upgrade-node:
        runs-on: ubuntu-latest

        steps:
          - name: Azure Login
            uses: Azure/login@v1.1
            with:
              creds: ${{ secrets.AZURE_CREDENTIALS }}
          - name: Upgrade node images
            uses: Azure/cli@v1.0.0
            with:
              inlineScript: az aks upgrade -g {resourceGroupName} -n {aksClusterName} --node-image-only --yes
    ```

    > [!TIP]
    > A `-g` és a `-n` paramétereket a parancsból választhatja ki úgy, hogy hozzáadja azokat az előző lépésekhez hasonló titokhoz. Cserélje le a `{resourceGroupName}` és `{aksClusterName}` helyőrzőket a titkos társaik, például a `${{secrets.RESOURCE_GROUP_NAME}}``${{secrets.AKS_CLUSTER_NAME}}`

1. Nevezze át a fájlt a következőre: `upgrade-node-images`.
1. Kattintson a **véglegesítés indítása** gombra, adjon hozzá egy üzenetet, és mentse a munkafolyamatot.

Miután létrehozta a végrehajtást, a rendszer menti a munkafolyamatot, és készen áll a futtatásra.

> [!NOTE]
> Ha a fürt összes csomópontja helyett egyetlen csomópontos készletet szeretne frissíteni, adja hozzá a `--name` paramétert a `az aks nodepool upgrade` parancshoz a csomópont-készlet nevének megadásához. Például:
> ```azurecli-interactive
> az aks nodepool upgrade -g {resourceGroupName} --cluster-name {aksClusterName} --name {{nodePoolName}} --node-image-only
> ```

## <a name="run-the-github-action-manually"></a>A GitHub-művelet manuális futtatása

A munkafolyamatot az ütemezett futtatás mellett manuálisan is futtathatja egy új trigger hozzáadásával `on` `workflow_dispatch` . A befejezett fájlnak az alábbi YAML hasonlóan kell kinéznie:

```yml
name: Upgrade cluster node images

on:
  schedule:
    - cron: '0 3 */15 * *'
  workflow_dispatch:

jobs:
  upgrade-node:
    runs-on: ubuntu-latest

    steps:
      - name: Azure Login
        uses: Azure/login@v1.1
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}

      # Code for upgrading one or more node pools
```

## <a name="next-steps"></a>Következő lépések

- A legújabb csomópont-lemezképekkel kapcsolatos információkért tekintse meg az [AK kibocsátási megjegyzéseit](https://github.com/Azure/AKS/releases) .
- Ismerje meg, hogyan frissítheti a Kubernetes-verziót [egy AK-fürt frissítésével][cluster-upgrades-article].
- További tudnivalók a több csomópontos készletekről, valamint a csomópont-készletek [több csomópontos készletek létrehozásával és kezelésével][use-multiple-node-pools]történő frissítéséről.
- További információ a [rendszercsomópont-készletekről][system-pools]
- Ha szeretné megtudni, hogyan mentheti a költségeket a direktszínek használatával, tekintse meg [a helyszíni csomópont-készlet hozzáadása AK-hoz][spot-pools] című témakört.

<!-- LINKS - external -->
[github]: https://github.com
[profile-repository]: https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-your-github-profile/about-your-profile
[cron-syntax]: https://pubs.opengroup.org/onlinepubs/9699919799/utilities/crontab.html#tag_20_25_07

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[managed-node-upgrades-article]: node-image-upgrade.md
[cluster-upgrades-article]: upgrade-cluster.md
[system-pools]: use-system-pools.md
[spot-pools]: spot-node-pool.md
[use-multiple-node-pools]: use-multiple-node-pools.md
