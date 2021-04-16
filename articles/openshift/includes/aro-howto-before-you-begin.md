---
author: sabbour
ms.author: asabbour
ms.date: 4/5/2020
ms.openlocfilehash: 1fded0ad08af4b1e5d915e32e09087c1a78bd318
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520742"
---
### <a name="create-the-cluster"></a>A fürt létrehozása

Kövesse az oktatóanyagot [egy Azure Red Hat OpenShift létrehozásához.](../tutorial-create-cluster.md) Ha a CLI helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.6.0-s vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

### <a name="connect-to-the-cluster"></a>Csatlakozás a fürthöz

A fürtök Azure Red Hat OpenShift [az oc,](https://docs.openshift.com/container-platform/4.7/cli_reference/openshift_cli/getting-started-cli.html)az OpenShift parancssori ügyfél használatával.

> [!NOTE]
> Javasoljuk, hogy telepítse az [OpenShift parancssort](../tutorial-connect-cluster.md) [a Azure Cloud Shell](https://shell.azure.com/) és használja az összes alábbi parancssori művelethez. Indítsa el a rendszerhéjat a shell.azure.com a hivatkozásra kattintva:
>
> [![Indítás beágyazása](https://docs.microsoft.com/azure/includes/media/cloud-shell-try-it/hdi-launch-cloud-shell.png "Az Azure Cloud Shell elindítása")](https://shell.azure.com/bash)

Kövesse az oktatóanyagot a CLI telepítéséhez, a fürt hitelesítő adatainak lekéréséhez és a fürthöz való csatlakozáshoz a webkonzol és az OpenShift cli használatával. [](../tutorial-connect-cluster.md)

Miután bejelentkezett, megjelenik egy üzenet, amely szerint a projektet `default` használja.

```output
Login successful.

You have access to 61 projects, the list has been suppressed. You can list all projects with 'oc projects'

Using project "default".
```
