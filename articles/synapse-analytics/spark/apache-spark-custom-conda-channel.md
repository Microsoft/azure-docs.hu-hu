---
title: Egyéni Conda-csatorna létrehozása csomagkezeléshez
description: Megtudhatja, hogyan hozhat létre egyéni Conda-csatornát csomagkezeléshez
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 26b6adefd2d334c9fe570bfa7e63bb06b55b9d20
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588768"
---
# <a name="create-a-custom-conda-channel-for-package-management"></a>Egyéni Conda-csatorna létrehozása csomagkezeléshez 
Python-csomagok telepítésekor a Conda csomagkezelő csatornákon keres csomagokat. Előfordulhat, hogy több okból is létre kell hoznia egy egyéni Conda-csatornát. Például a következőt találhatja:

- A munkaterület adatkiszivárgással védett, és a kimenő kapcsolatok le vannak tiltva.  
- olyan csomagjai vannak, amelyek nyilvános adattárakba való feltöltését nem szeretné.
- A munkaterületen lévő felhasználók számára alternatív adattárat szeretne beállítani.

Ebben a cikkben egy részletes útmutatót biztosítunk, amely segítséget nyújt az egyéni Conda-csatorna létrehozásához a Azure Data Lake Storage fiókjában.

## <a name="set-up-your-local-machine"></a>A helyi gép beállítása

1. Telepítse a Condát a helyi gépre. Az ugyanazon a Azure Synapse [használt](./apache-spark-version-support.md) Conda-verzió azonosításához tekintse meg a Spark-Azure Synapse.
   
2. Egyéni csatorna létrehozásához telepítse a conda-buildet.
```
conda install conda-build
```
3. Rendszerezze az összes csomagot a kiszolgálni kívánt platformhoz. Ebben a példában az Anaconda archívumot fogjuk telepíteni a helyi gépre.

```
sudo wget https://repo.continuum.io/archive/Anaconda3-4.4.0-Linux-x86_64.sh 
sudo chmod +x Anaconda3-4.4.0-Linux-x86_64.sh  
sudo bash Anaconda3-4.4.0-Linux-x86_64.sh -b -p /usr/lib/anaconda3 
export PATH="/usr/lib/anaconda3/bin:$PATH" 
sudo chmod 777 -R /usr/lib/anaconda3a.  
```
## <a name="mount-the-storage-account-onto-your-machine"></a>A tárfiók csatlakoztatása a géphez
Ezután csatlakoztatjuk a Azure Data Lake Storage Gen2 fiókot a helyi géphez. Ez a folyamat WASB-fiókkal is használhatja; azonban végigveszünk egy példát az ADLSg2-fiókra 
 
A tárfiók helyi gépen való csatlakoztatásának további információiért látogasson el erre [az oldalra.](https://github.com/Azure/azure-storage-fuse#blobfuse ) 

1. A blobfuse a Microsoft-termékek linuxos szoftvertárházában telepíthető.

```
wget https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb 
sudo dpkg -i packages-microsoft-prod.deb 
sudo apt-get update 
sudo apt-get install blobfuse fuse 
export AZURE_STORAGE_ACCOUNT=<<myaccountname>
export AZURE_STORAGE_ACCESS_KEY=<<myaccountkey>>
export AZURE_STORAGE_BLOB_ENDPOINT=*.dfs.core.windows.net 
```

2. Hozza létre a csatlakozási pontot ( ```mkdir /path/to/mount``` ), és csatlakoztassa a Blob-tárolót a blobfuse használatával. Ebben a példában a **privatechannel** értéket használjuk a **mycontainer változóhoz.**
   
```
blobfuse /path/to/mount --container-name=mycontainer --tmp-path=/mnt/blobfusetmp --use-adls=true --log-level=LOG_DEBUG 
sudo mkdir -p /mnt/blobfusetmp
sudo chown <myuser> /mnt/blobfusetmp
```
## <a name="create-the-channel"></a>A csatorna létrehozása
A következő lépésekben egy egyéni Conda-csatornát hozunk létre. 

1. A helyi gépen hozzon létre egy könyvtárat, amely az egyéni csatorna összes csomagját rendszerezheti.
   
```
mkdir /home/trusted-service-user/privatechannel 
cd ~/privatechannel/ 
mkdir channel1/linux64 
```

2. Rendezze az ```tar.bz2``` összes csomagot a https://repo.anaconda.com/pkgs/main/linux-64/ alkönyvtárba. Mindenképpen foglalja bele az összes függő tar.bz2 csomagot is. 

```
cd channel1 
mkdir noarch 
echo '{}' > noarch/repodata.json 
bzip2 -k noarch/repodata.json 

// Create channel 

conda index channel1/noarch 
conda index channel1/linux-64 
conda index channel1 
```

További információért látogasson el a [Conda](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/create-custom-channels.html) egyéni csatornák létrehozására vonatkozó felhasználói útmutatóját is. 

## <a name="storage-account-permissions"></a>Tárfiók engedélyei
Most érvényesítenünk kell a tárfiók engedélyeinek érvényességét. Az engedélyek beállításhoz lépjen arra az elérési útra, ahol az egyéni csatorna létre lesz hozva. Ezután hozzon létre egy SAS-jogkivonatot, ```privatechannel``` amely olvasási, listás és végrehajtási engedélyekkel rendelkezik. 

A csatorna neve mostantól a folyamat során létrehozott blob SAS URL-címe lesz.  

## <a name="create-a-sample-conda-environment-configuration-file"></a>Minta Conda-környezet konfigurációs fájljának létrehozása
Végül ellenőrizze a telepítési folyamatot egy Minta Conda-fájl ```environment.yml``` létrehozásával. Ha DEP-kompatibilis munkaterülettel dolgozik, meg kell adnia a csatornát a ``nodefaults`` környezeti fájlban.

Példa a Conda konfigurációs fájlra:
```
name: sample 
channels: 
  - https://<<storage account name>>.blob.core.windows.net/privatechannel/channel?<<SAS Token>
  - nodefaults 
dependencies: 
  - openssl 
  - ncurses 
```
Miután létrehozta a Conda-mintafájlt, létrehozhat egy virtuális Conda-környezetet. 

```
conda env create –file sample.yml  
source activate env 
conda list 
```
Most, hogy ellenőrizte az egyéni csatornát, a [Python](./apache-spark-manage-python-packages.md) készletkezelési folyamatának használatával frissítheti a kódtárakat a Apache Spark készletben.

## <a name="next-steps"></a>Következő lépések
- Az alapértelmezett kódtárak megtekintése: [Apache Spark támogatása](apache-spark-version-support.md)
- Python-csomagok kezelése: [Python-csomagkezelés](./apache-spark-manage-python-packages.md)

