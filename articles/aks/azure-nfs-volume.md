---
title: NFS-Ubuntu Linux kiszolgáló kötetének létrehozása
titleSuffix: Azure Kubernetes Service
description: Megtudhatja, hogyan hozhat létre manuálisan NFS Ubuntu Linux Server-kötetet a hüvelyekkel való használatra az Azure Kubernetes szolgáltatásban (ak)
services: container-service
author: ozboms
ms.topic: article
ms.date: 4/25/2019
ms.author: obboms
ms.openlocfilehash: 4e817d572a98ffb8135adf58d13f50ccacbc8746
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86251994"
---
# <a name="manually-create-and-use-an-nfs-network-file-system-linux-server-volume-with-azure-kubernetes-service-aks"></a>NFS-(hálózati fájlrendszer) Linux Server-kötet manuális létrehozása és használata az Azure Kubernetes szolgáltatással (ak)
A tárolók közötti adatmegosztás gyakran a Container-alapú szolgáltatások és alkalmazások szükséges összetevője. Általában különböző hüvelyek vannak, amelyekhez ugyanahhoz az információhoz kell hozzáférni egy külső állandó köteten.    
Habár az Azure Files egy lehetőség, az NFS-kiszolgáló létrehozása Azure-beli virtuális gépen a tartósan megosztott tároló egy másik formája. 

Ez a cikk bemutatja, hogyan hozhat létre NFS-kiszolgálót Ubuntu rendszerű virtuális gépen. Továbbá adja meg az AK-tárolók hozzáférését ehhez a megosztott fájlrendszerhez.

## <a name="before-you-begin"></a>Előkészületek
Ez a cikk feltételezi, hogy rendelkezik egy meglévő AK-fürttel. Ha AK-fürtre van szüksége, tekintse meg az AK gyors üzembe helyezését [Az Azure CLI használatával][aks-quickstart-cli] vagy [a Azure Portal használatával][aks-quickstart-portal].

Az AK-fürtnek az NFS-kiszolgálóval azonos vagy összetartozó virtuális hálózatokban kell lennie. A fürtöt egy meglévő VNET kell létrehozni, amely a virtuális géppel megegyező VNET lehet.

A meglévő VNET való konfigurálás lépései a következő dokumentációban olvashatók: [AK-fürt létrehozása a meglévő VNET][aks-virtual-network] és a [virtuális hálózatok összekapcsolása a VNET][peer-virtual-networks] -társítással

Azt is feltételezi, hogy létrehozott egy Ubuntu Linux virtuális gépet (például 18,04 LTS). A beállítások és a méret az Azure-on keresztül üzembe helyezhető. A Linux gyors üzembe helyezését lásd: Linux rendszerű [virtuális gépek kezelése][linux-create].

Ha először telepíti az AK-fürtöt, az Azure automatikusan feltölti a virtuális hálózat mezőt az Ubuntu-gép üzembe helyezése során, így azok ugyanabban a VNET belül laknak. Ha azonban inkább egyenrangú hálózatokat szeretne használni, tekintse meg a fenti dokumentációt.

## <a name="deploying-the-nfs-server-onto-a-virtual-machine"></a>Az NFS-kiszolgáló üzembe helyezése virtuális gépen
Az alábbi szkripttel állíthatja be az NFS-kiszolgálót az Ubuntu rendszerű virtuális gépen:
```bash
#!/bin/bash

# This script should be executed on Linux Ubuntu Virtual Machine

EXPORT_DIRECTORY=${1:-/export/data}
DATA_DIRECTORY=${2:-/data}
AKS_SUBNET=${3:-*}

echo "Updating packages"
apt-get -y update

echo "Installing NFS kernel server"

apt-get -y install nfs-kernel-server

echo "Making data directory ${DATA_DIRECTORY}"
mkdir -p ${DATA_DIRECTORY}

echo "Making new directory to be exported and linked to data directory: ${EXPORT_DIRECTORY}"
mkdir -p ${EXPORT_DIRECTORY}

echo "Mount binding ${DATA_DIRECTORY} to ${EXPORT_DIRECTORY}"
mount --bind ${DATA_DIRECTORY} ${EXPORT_DIRECTORY}

echo "Giving 777 permissions to ${EXPORT_DIRECTORY} directory"
chmod 777 ${EXPORT_DIRECTORY}

parentdir="$(dirname "$EXPORT_DIRECTORY")"
echo "Giving 777 permissions to parent: ${parentdir} directory"
chmod 777 $parentdir

echo "Appending bound directories into fstab"
echo "${DATA_DIRECTORY}    ${EXPORT_DIRECTORY}   none    bind  0  0" >> /etc/fstab

echo "Appending localhost and Kubernetes subnet address ${AKS_SUBNET} to exports configuration file"
echo "/export        ${AKS_SUBNET}(rw,async,insecure,fsid=0,crossmnt,no_subtree_check)" >> /etc/exports
echo "/export        localhost(rw,async,insecure,fsid=0,crossmnt,no_subtree_check)" >> /etc/exports

nohup service nfs-kernel-server restart
```
A kiszolgáló újraindul (a parancsfájl miatt), és csatlakoztathatja az NFS-kiszolgálót az AK-hoz.

>[!IMPORTANT]  
>Győződjön meg arról, hogy a **AKS_SUBNET** a megfelelővel cseréli ki a fürtöt, vagy "*" megnyitja az NFS-kiszolgálót minden portra és kapcsolatra.

Miután létrehozta a virtuális gépet, másolja a fenti szkriptet egy fájlba. Ezután áthelyezheti azt a helyi gépről vagy a szkriptből a virtuális gépre a következő használatával: 
```console
scp /path/to/script_file username@vm-ip-address:/home/{username}
```
Ha a parancsfájl a virtuális gépen található, SSH-t telepíthet a virtuális gépre, és végrehajthatja azt a parancs használatával:
```console
sudo ./nfs-server-setup.sh
```
Ha a végrehajtás egy engedély megtagadási hibája miatt meghiúsul, állítsa be a végrehajtási engedélyt a következő parancs használatával:
```console
chmod +x ~/nfs-server-setup.sh
```

## <a name="connecting-aks-cluster-to-nfs-server"></a>AK-fürt csatlakoztatása az NFS-kiszolgálóhoz
Az NFS-kiszolgálót összekapcsolhatjuk a fürttel úgy, hogy kiépítünk egy állandó kötetet és állandó mennyiségi jogcímet, amely megadja a kötet elérésének módját.

Szükség van a két szolgáltatás összekapcsolására ugyanazon vagy társ virtuális hálózatokban. A fürt ugyanazon VNET való beállítására vonatkozó utasítások itt találhatók: [AK-fürt létrehozása a meglévő VNET][aks-virtual-network]

Ha ugyanabban a virtuális hálózatban (vagy társ) található, állandó kötetet és állandó mennyiségi jogcímet kell kiépíteni az AK-fürtben. A tárolók ezután csatlakoztatni tudják az NFS-meghajtót a helyi címtárhoz.

Az alábbi példa az állandó kötet Kubernetes-definícióját mutatja be (ez a definíció feltételezi, hogy a fürt és a virtuális gép ugyanabban a VNET található):

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: <NFS_NAME>
  labels:
    type: nfs
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteMany
  nfs:
    server: <NFS_INTERNAL_IP>
    path: <NFS_EXPORT_FILE_PATH>
```
Cserélje le az **NFS_INTERNAL_IP**, **NFS_NAME** és **NFS_EXPORT_FILE_PATH** NFS-kiszolgáló adataira.

Szüksége lesz egy állandó kötet-jogcím fájlra is. Íme egy példa a következőkre:

>[!IMPORTANT]  
>a **"storageClassName"** üres karakterláncot kell hagyni, vagy a jogcím nem fog működni.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: <NFS_NAME>
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 1Gi
  selector: 
    matchLabels:
      type: nfs
```

## <a name="troubleshooting"></a>Hibaelhárítás
Ha nem tud csatlakozni a kiszolgálóhoz egy fürtről, akkor előfordulhat, hogy az exportált könyvtár vagy annak szülője nem rendelkezik megfelelő engedélyekkel a kiszolgáló eléréséhez.

Győződjön meg arról, hogy az exportálási címtár és a hozzá tartozó szülő címtár 777 engedélyekkel rendelkezik.

Az engedélyeket az alábbi parancs futtatásával tekintheti meg, a címtárakban pedig *"drwxrwxrwx"* engedélyekkel kell rendelkezniük:
```console
ls -l
```

## <a name="more-information"></a>További információ
A teljes körű bemutatóhoz, illetve az NFS-kiszolgáló beállításával kapcsolatban a részletes oktatóanyagban talál további útmutatót:
  - [NFS-oktatóanyag][nfs-tutorial]

## <a name="next-steps"></a>Következő lépések

A kapcsolódó ajánlott eljárásokért lásd: [ajánlott eljárások a tároláshoz és a biztonsági mentéshez az AK-ban][operator-best-practices-storage].

<!-- LINKS - external -->
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[linux-create]: ../virtual-machines/linux/tutorial-manage-vm.md
[nfs-tutorial]: https://help.ubuntu.com/community/SettingUpNFSHowTo#Pre-Installation_Setup
[aks-virtual-network]: ./configure-kubenet.md#create-an-aks-cluster-in-the-virtual-network
[peer-virtual-networks]: ../virtual-network/tutorial-connect-virtual-networks-portal.md

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[operator-best-practices-storage]: operator-best-practices-storage.md
