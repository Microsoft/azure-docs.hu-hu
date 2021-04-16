---
title: Oktatóanyag – Fürt Azure Red Hat OpenShift törlése
description: Ebből az oktatóanyagból megtudhatja, hogyan törölhet Azure Red Hat OpenShift fürtöt az Azure CLI használatával
author: sakthi-vetrivel
ms.custom: fasttrack-edit, devx-track-azurecli
ms.author: suvetriv
ms.topic: tutorial
ms.service: azure-redhat-openshift
ms.date: 04/24/2020
ms.openlocfilehash: 65bb27f1f85b7a26e35074da84cfc27b2a5761a1
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484761"
---
# <a name="tutorial-delete-an-azure-red-hat-openshift-4-cluster"></a>Oktatóanyag: 4 Azure Red Hat OpenShift törlése

Ebben az oktatóanyagban, amely háromrészes sorozat harmadik része, Azure Red Hat OpenShift OpenShift 4-et futtató fürtöt törölünk. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Azure Red Hat OpenShift-fürt törlése


## <a name="before-you-begin"></a>Előkészületek

Az előző oktatóanyagokban létrehozott egy Azure Red Hat OpenShift fürtöt, és csatlakozott hozzá az OpenShift webkonzollal. Ha ezeket a lépéseket még nem tette meg, és szeretné követni a lépéseket, kezdje az 1. oktatóanyag [– Azure Red Hat Openshift 4-fürt létrehozása lépésekkel.](tutorial-create-cluster.md)

Ha a CLI helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.6.0-s vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Ha helyileg futtatja az Azure CLI-t, futtassa a következőt `az login` az Azure-ba való bejelentkezéshez: .

```bash
az login
```

Ha több előfizetéshez is hozzáféréssel rendelkezik, futtassa a következőt: . Cserélje le a et `az account set -s {subscription ID}` `{subscription ID}` a használni kívánt előfizetésre.

## <a name="delete-the-cluster"></a>A fürt törlése

Az előző oktatóanyagokban az alábbi változók voltak beállítva.

```bash
CLUSTER=yourclustername
RESOURCEGROUP=yourresourcegroup
```

Az alábbi értékekkel törölje a fürtöt:

```bash
az aro delete --resource-group $RESOURCEGROUP --name $CLUSTER
```

Ezután meg kell erősítenie, hogy törölni szeretné-e a fürtöt. A megerősítése után több percig is eltarthat `y` a fürt törlése. Amikor a parancs befejeződik, a teljes erőforráscsoport és az összes benne lévő erőforrás törölve lesz.

## <a name="next-steps"></a>Következő lépések

Az oktatóanyag jelen részében megismerkedhetett a következőkkel:
> [!div class="checklist"]
> * Azure Red Hat OpenShift 4-fürt törlése

További információ az OpenShift a [hivatalos Red Hat OpenShift-dokumentációval való használatával kapcsolatban](https://docs.openshift.com/container-platform/4.6/welcome/index.html)
