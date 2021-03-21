---
title: Egyéni Conda-csatorna létrehozása a csomagok kezeléséhez
description: Ismerje meg, hogyan hozhat létre egyéni Conda-csatornát a csomagok kezeléséhez
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 528ba4a1be3650a81772d78a438f03611b9bd761
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102107684"
---
# <a name="create-a-custom-conda-channel-for-package-management"></a>Egyéni Conda-csatorna létrehozása a csomagok kezeléséhez 
A Python-csomagok telepítésekor a Conda csomagkezelő csatornákat használ a csomagok kereséséhez. Előfordulhat, hogy különféle okokból létre kell hoznia egy egyéni Conda-csatornát. Előfordulhat például, hogy a következőket találja:

- a munkaterület kiszűrése védett, és a kimenő kapcsolatok le vannak tiltva.  
- olyan csomagokat tartalmaz, amelyeket nem kíván feltölteni a nyilvános adattárakba.
- a munkaterületen lévő felhasználók számára szeretné beállítani a másodlagos tárházat.

Ebben a cikkben egy lépésenkénti útmutatót adunk, amely segít az egyéni Conda-csatorna létrehozásában Azure Data Lake Storage-fiókjában.

## <a name="set-up-your-local-machine"></a>A helyi gép beállítása

1. Telepítse a Conda a helyi gépre. Az [Azure szinapszis Spark futtatókörnyezetét](./apache-spark-version-support.md) az ugyanazon a futtatókörnyezeten használt Conda-verzió azonosításához tekintheti meg.
   
2. Egyéni csatorna létrehozásához telepítse a Conda-buildet.
```
conda install conda-build
```
3. Rendezze az összes csomagot a használni kívánt platformra. Ebben a példában az anaconda-archívumot telepítjük a helyi gépre.

```
sudo wget https://repo.continuum.io/archive/Anaconda3-4.4.0-Linux-x86_64.sh 
sudo chmod +x Anaconda3-4.4.0-Linux-x86_64.sh  
sudo bash Anaconda3-4.4.0-Linux-x86_64.sh -b -p /usr/lib/anaconda3 
export PATH="/usr/lib/anaconda3/bin:$PATH" 
sudo chmod 777 -R /usr/lib/anaconda3a.  
```
## <a name="mount-the-storage-account-onto-your-machine"></a>A Storage-fiók csatlakoztatása a géphez
Ezután csatlakoztatni fogjuk a Azure Data Lake Storage Gen2 fiókot a helyi gépre. Ezt a folyamatot WASB-fiókkal is lehet elvégezni; a ADLSg2-fiókhoz azonban egy példa is elérhető. 
 
A Storage-fiók helyi gépen való csatlakoztatásával kapcsolatos további információkért tekintse meg [ezt a lapot](https://github.com/Azure/azure-storage-fuse#blobfuse ). 

1. A blobfuse a Microsoft-termékekhez készült Linux-tárházból is telepítheti.

```
wget https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb 
sudo dpkg -i packages-microsoft-prod.deb 
sudo apt-get update 
sudo apt-get install blobfuse fuse 
export AZURE_STORAGE_ACCOUNT=<<myaccountname>
export AZURE_STORAGE_ACCESS_KEY=<<myaccountkey>>
export AZURE_STORAGE_BLOB_ENDPOINT=*.dfs.core.windows.net 
```

2. Hozza létre a csatlakoztatási pont ( ```mkdir /path/to/mount``` ), és csatlakoztassa a BLOB-tárolót a blobfuse. Ebben a példában a **privatechannel** értéket használjuk a **mycontainer** változóhoz.
   
```
blobfuse /path/to/mount --container-name=mycontainer --tmp-path=/mnt/blobfusetmp --use-adls=true --log-level=LOG_DEBUG 
sudo mkdir -p /mnt/blobfusetmp
sudo chown <myuser> /mnt/blobfusetmp
```
## <a name="create-the-channel"></a>A csatorna létrehozása
A következő lépésben létrehozunk egy egyéni Conda-csatornát. 

1. A helyi gépen hozzon létre egy könyvtárat az egyéni csatorna összes csomagjának rendszerezéséhez.
   
```
mkdir /home/trusted-service-user/privatechannel 
cd ~/privatechannel/ 
mkdir channel1/linux64 
```

2. Rendezze az összes ```tar.bz2``` csomagot https://repo.anaconda.com/pkgs/main/linux-64/ az alkönyvtárba. Ügyeljen arra, hogy az összes függő tar. bz2-csomagot is tartalmazza. 

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

További információt [a Conda felhasználói útmutatójában találhat az](https://docs.conda.io/projects/conda/latest/user-guide/tasks/create-custom-channels.html) egyéni csatornák létrehozásához. 

## <a name="storage-account-permissions"></a>Storage-fiók engedélyei
Most a Storage-fiókra vonatkozó engedélyeket is ellenőrizni kell. Az engedélyek beállításához Navigáljon arra az elérési útra, ahová az egyéni csatornát létre szeretné hozni. Ezután hozzon létre egy SAS-jogkivonatot, ```privatechannel``` amely olvasási, listázási és végrehajtási engedélyekkel rendelkezik. 

Ekkor a csatorna neve lesz az ebből a folyamatból generált blob SAS URL-cím.  

## <a name="create-a-sample-conda-environment-configuration-file"></a>Minta Conda-környezet konfigurációs fájljának létrehozása
Utolsó lépésként ellenőrizze a telepítési folyamatot egy minta Conda- ```environment.yml``` fájl létrehozásával. Ha a DEP-kompatibilis munkaterületen van, meg kell adnia a ``nodefaults`` csatornát a környezeti fájlban.

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
A minta Conda-fájl létrehozása után létrehozhat egy virtuális Conda-környezetet. 

```
conda env create –file sample.yml  
source activate env 
conda list 
```
Most, hogy ellenőrizte az egyéni csatornát, a [Python-készlet felügyeleti](./apache-spark-manage-python-packages.md) folyamatával frissítheti a tárakat a Apache Spark készletén.

## <a name="next-steps"></a>Következő lépések
- Az alapértelmezett könyvtárak megtekintése: [Apache Spark verzió támogatása](apache-spark-version-support.md)
- Python-csomagok kezelése: [Python-csomag kezelése](./apache-spark-manage-python-packages.md)

