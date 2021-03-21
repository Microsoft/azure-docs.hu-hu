---
title: Oktatóanyag – kapcsolódás Azure Red Hat OpenShift 4 rendszerű fürthöz
description: Útmutató Microsoft Azure Red Hat OpenShift-fürthöz való kapcsolódáshoz
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: azure-redhat-openshift
ms.date: 04/24/2020
ms.openlocfilehash: 5295f0fbd28140bc0b278f3a44915239df200a70
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102215663"
---
# <a name="tutorial-connect-to-an-azure-red-hat-openshift-4-cluster"></a>Oktatóanyag: Kapcsolódás Azure Red Hat OpenShift 4 rendszerű fürthöz

Ebben az oktatóanyagban, amely három részből áll, a OpenShift 4-es verzióját futtató Azure Red Hat OpenShift-fürthöz csatlakozik a OpenShift webkonzolon keresztül a kubeadmin-felhasználóként. Az alábbiak végrehajtásának módját ismerheti meg:
> [!div class="checklist"]
> * `kubeadmin`Hitelesítő adatok beszerzése a fürthöz
> * A OpenShift parancssori felületének telepítése
> * Kapcsolódás Azure Red Hat OpenShift-fürthöz az OpenShift CLI használatával

## <a name="before-you-begin"></a>Előkészületek

Az előző oktatóanyagokban létrehoztak egy Azure Red Hat OpenShift-fürtöt. Ha még nem tette meg ezeket a lépéseket, és követni szeretné a lépéseket, kezdje az [1. oktatóanyag használatával – hozzon létre egy Azure Red Hat OpenShift 4 fürtöt.](tutorial-create-cluster.md)

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.6.0 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="connect-to-the-cluster"></a>Csatlakozás a fürthöz

A felhasználó segítségével bejelentkezhet a fürtbe `kubeadmin` .  Futtassa a következő parancsot a felhasználó jelszavának megkereséséhez `kubeadmin` .

```azurecli-interactive
az aro list-credentials \
  --name $CLUSTER \
  --resource-group $RESOURCEGROUP
```

A következő példa kimenete azt mutatja be, hogy a jelszó milyen nyelven fog megjelenni `kubeadminPassword` .

```json
{
  "kubeadminPassword": "<generated password>",
  "kubeadminUsername": "kubeadmin"
}
```

A fürt konzoljának URL-címét a következő parancs futtatásával érheti el, amely így fog kinézni: `https://console-openshift-console.apps.<random>.<region>.aroapp.io/` .

```azurecli-interactive
 az aro show \
    --name $CLUSTER \
    --resource-group $RESOURCEGROUP \
    --query "consoleProfile.url" -o tsv
```

Indítsa el a konzol URL-címét egy böngészőben, és jelentkezzen be a `kubeadmin` hitelesítő adatok használatával.

![Azure Red Hat OpenShift bejelentkezési képernyő](media/aro4-login.png)

## <a name="install-the-openshift-cli"></a>A OpenShift parancssori felületének telepítése

Ha bejelentkezett a OpenShift webkonzolba, kattintson a következőre **?** a jobb felső sarokban, majd a **parancssori eszközök menüpontban**. Töltse le a számítógépének megfelelő kiadást.

![Képernyőkép, amely kiemeli a listában szereplő parancssori eszközök lehetőséget, amikor kiválasztja a? ikonnal.](media/aro4-download-cli.png)

A parancssori felület legújabb kiadását is letöltheti a gépre <https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/> .

Ha a Azure Cloud Shell parancsokat futtatja, töltse le a legújabb OpenShift 4 CLI Linux rendszerhez.

```azurecli-interactive
cd ~
wget https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/openshift-client-linux.tar.gz

mkdir openshift
tar -zxvf openshift-client-linux.tar.gz -C openshift
echo 'export PATH=$PATH:~/openshift' >> ~/.bashrc && source ~/.bashrc
```

## <a name="connect-using-the-openshift-cli"></a>Kapcsolat a OpenShift CLI használatával

Kérje le az API-kiszolgáló címeit.

```azurecli-interactive
apiServer=$(az aro show -g $RESOURCEGROUP -n $CLUSTER --query apiserverProfile.url -o tsv)
```

Jelentkezzen be a OpenShift-fürt API-kiszolgálójára a következő parancs használatával. Cserélje le **\<kubeadmin password>** az t az imént beolvasott jelszóra.

```azurecli-interactive
oc login $apiServer -u kubeadmin -p <kubeadmin password>
```

## <a name="next-steps"></a>Következő lépések

Az oktatóanyag jelen részében megismerkedhetett a következőkkel:
> [!div class="checklist"]
> * `kubeadmin`Hitelesítő adatok beszerzése a fürthöz
> * A OpenShift parancssori felületének telepítése
> * Kapcsolódás Azure Red Hat OpenShift-fürthöz az OpenShift CLI használatával

Folytassa a következő oktatóanyaggal:
> [!div class="nextstepaction"]
> [Azure Red Hat OpenShift-fürt törlése](tutorial-delete-cluster.md)