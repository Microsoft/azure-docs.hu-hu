---
title: A OpenShift Container platform 3,11 üzembe helyezése az Azure-ban – problémamegoldás
description: A OpenShift Container platform 3,11 üzembe helyezésének hibája az Azure-ban.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.custom: devx-track-ansible
ms.openlocfilehash: b6dfb4cb697e7d66c7db323df9786ff7ad45e1d7
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101672331"
---
# <a name="troubleshoot-openshift-container-platform-311-deployment-in-azure"></a>A OpenShift Container platform 3,11 üzembe helyezése az Azure-ban – problémamegoldás

Ha a OpenShift-fürt nem üzemel sikeresen, a Azure Portal hibaüzenetet küld. Nehéz lehet beolvasni a kimenetet, ami megnehezíti a probléma azonosítását. A 3, 4 vagy 5 kilépési kód gyors vizsgálata a kimenetben. A következő három kilépési kóddal kapcsolatos információkat tartalmaz:

- 3. kilépési kód: a Red Hat-előfizetés felhasználónevének/jelszavának vagy a szervezet AZONOSÍTÓjának/aktiválási kulcsa helytelen
- 4. kilépési kód: a Red Hat-készlet azonosítója helytelen, vagy nincsenek elérhető jogosultságok
- 5. kilépési kód: nem sikerült kiépíteni a Docker vékony készletének kötetét

Az összes többi kilépési kód esetében az SSH-n keresztül csatlakozhat a gazdagépekhez a naplófájlok megtekintéséhez.

**Az OpenShift-tárolóplatform 3.11-es verziója**

SSH-t a Ansible forgatókönyv-gazdagéphez. A sablonhoz vagy a Piactéri ajánlathoz használja a megerősített gazdagépet. A megerősített környezetből SSH-t használhat a fürt összes többi csomópontjára (Master, infra, CNS, számítási). A naplófájlok megtekintéséhez legfelső szintűnek kell lennie. A root alapértelmezés szerint le van tiltva az SSH-hozzáféréshez, ezért ne használja a root SSH-t más csomópontokhoz.

**OKD**

SSH-t a Ansible forgatókönyv-gazdagéphez. A OKD-sablonhoz (3,9-es és korábbi verziók) használja a Master-0 gazdagépet. A OKD-sablonhoz (3,10-es és újabb verzió) használja a megerősített gazdagépet. A Ansible forgatókönyv-gazdagépről SSH-t használhat a fürt összes többi csomópontjára (Master, infra, CNS, számítási). A naplófájlok megtekintéséhez root (sudo su-) értékűnek kell lennie. A root alapértelmezés szerint le van tiltva az SSH-hozzáféréshez, ezért ne használja a root SSH-t más csomópontokhoz.

## <a name="log-files"></a>Naplófájlok

A gazdagép-előkészítési parancsfájlok naplófájljai (stderr és StdOut) az `/var/lib/waagent/custom-script/download/0` összes gazdagépen találhatók. Ha hiba történt a gazdagép előkészítése során, tekintse meg ezeket a naplófájlokat a hiba megállapításához.

Ha az előkészítési parancsfájlok sikeresen futottak, akkor meg kell vizsgálni a naplófájlokat a `/var/lib/waagent/custom-script/download/1` Ansible ötletekbõl-gazdagép könyvtárában. Ha a hiba a OpenShift tényleges telepítése közben történt, akkor az stdout-fájl megjeleníti a hibát. Ezekkel az információkkal további segítségért forduljon az ügyfélszolgálathoz.

Példa kimenetre

```json
TASK [openshift_storage_glusterfs : Load heketi topology] **********************
fatal: [mycluster-master-0]: FAILED! => {"changed": true, "cmd": ["oc", "--config=/tmp/openshift-glusterfs-ansible-IbhnUM/admin.kubeconfig", "rsh", "--namespace=glusterfs", "deploy-heketi-storage-1-d9xl5", "heketi-cli", "-s", "http://localhost:8080", "--user", "admin", "--secret", "VuoJURT0/96E42Vv8+XHfsFpSS8R20rH1OiMs3OqARQ=", "topology", "load", "--json=/tmp/openshift-glusterfs-ansible-IbhnUM/topology.json", "2>&1"], "delta": "0:00:21.477831", "end": "2018-05-20 02:49:11.912899", "failed": true, "failed_when_result": true, "rc": 0, "start": "2018-05-20 02:48:50.435068", "stderr": "", "stderr_lines": [], "stdout": "Creating cluster ... ID: 794b285745b1c5d7089e1c5729ec7cd2\n\tAllowing file volumes on cluster.\n\tAllowing block volumes on cluster.\n\tCreating node mycluster-cns-0 ... ID: 45f1a3bfc20a4196e59ebb567e0e02b4\n\t\tAdding device /dev/sdd ... OK\n\t\tAdding device /dev/sde ... OK\n\t\tAdding device /dev/sdf ... OK\n\tCreating node mycluster-cns-1 ... ID: 596f80d7bbd78a1ea548930f23135131\n\t\tAdding device /dev/sdc ... Unable to add device: Unable to execute command on glusterfs-storage-4zc42:   Device /dev/sdc excluded by a filter.\n\t\tAdding device /dev/sde ... OK\n\t\tAdding device /dev/sdd ... OK\n\tCreating node mycluster-cns-2 ... ID: 42c0170aa2799559747622acceba2e3f\n\t\tAdding device /dev/sde ... OK\n\t\tAdding device /dev/sdf ... OK\n\t\tAdding device /dev/sdd ... OK", "stdout_lines": ["Creating cluster ... ID: 794b285745b1c5d7089e1c5729ec7cd2", "\tAllowing file volumes on cluster.", "\tAllowing block volumes on cluster.", "\tCreating node mycluster-cns-0 ... ID: 45f1a3bfc20a4196e59ebb567e0e02b4", "\t\tAdding device /dev/sdd ... OK", "\t\tAdding device /dev/sde ... OK", "\t\tAdding device /dev/sdf ... OK", "\tCreating node mycluster-cns-1 ... ID: 596f80d7bbd78a1ea548930f23135131", "\t\tAdding device /dev/sdc ... Unable to add device: Unable to execute command on glusterfs-storage-4zc42:   Device /dev/sdc excluded by a filter.", "\t\tAdding device /dev/sde ... OK", "\t\tAdding device /dev/sdd ... OK", "\tCreating node mycluster-cns-2 ... ID: 42c0170aa2799559747622acceba2e3f", "\t\tAdding device /dev/sde ... OK", "\t\tAdding device /dev/sdf ... OK", "\t\tAdding device /dev/sdd ... OK"]}

PLAY RECAP *********************************************************************
mycluster-cns-0       : ok=146  changed=57   unreachable=0    failed=0   
mycluster-cns-1       : ok=146  changed=57   unreachable=0    failed=0   
mycluster-cns-2       : ok=146  changed=57   unreachable=0    failed=0   
mycluster-infra-0     : ok=143  changed=55   unreachable=0    failed=0   
mycluster-infra-1     : ok=143  changed=55   unreachable=0    failed=0   
mycluster-infra-2     : ok=143  changed=55   unreachable=0    failed=0   
mycluster-master-0    : ok=502  changed=198  unreachable=0    failed=1   
mycluster-master-1    : ok=348  changed=140  unreachable=0    failed=0   
mycluster-master-2    : ok=348  changed=140  unreachable=0    failed=0   
mycluster-node-0      : ok=143  changed=55   unreachable=0    failed=0   
mycluster-node-1      : ok=143  changed=55   unreachable=0    failed=0   
localhost                  : ok=13   changed=0    unreachable=0    failed=0   

INSTALLER STATUS ***************************************************************
Initialization             : Complete (0:00:39)
Health Check               : Complete (0:00:24)
etcd Install               : Complete (0:01:24)
Master Install             : Complete (0:14:59)
Master Additional Install  : Complete (0:01:10)
Node Install               : Complete (0:10:58)
GlusterFS Install          : In Progress (0:03:33)
    This phase can be restarted by running: playbooks/openshift-glusterfs/config.yml

Failure summary:

  1. Hosts:    mycluster-master-0
     Play:     Configure GlusterFS
     Task:     Load heketi topology
     Message:  Failed without returning a message.
```

A telepítés során leggyakrabban előforduló hibák a következők:

1. A titkos kulcs jelszava
2. Nem megfelelően hozták létre a Key Vault titkos kulcsát.
3. Az egyszerű szolgáltatásnév hitelesítő adatai helytelenül lettek megadva
4. Az egyszerű szolgáltatásnév nem rendelkezik közreműködői hozzáféréssel az erőforráscsoporthoz

### <a name="private-key-has-a-passphrase"></a>A titkos kulcs jelszava

Hibaüzenet jelenik meg, amely miatt a rendszer megtagadta az engedélyt az SSH-hoz. SSH-t a Ansible forgatókönyv-gazdagépre, hogy keressen egy jelszót a titkos kulcson.

### <a name="key-vault-secret-with-private-key-wasnt-created-correctly"></a>Nem megfelelően hozták létre a Key Vault titkos kulcsát.

A titkos kulcsot a rendszer átmásolja a Ansible ötletekbõl-gazdagépre – ~/.ssh/id_rsa. Győződjön meg arról, hogy a fájl helyes. Tesztelés: nyisson meg egy SSH-munkamenetet a Ansible forgatókönyv-gazdagép egyik fürtjén.

### <a name="service-principal-credentials-were-entered-incorrectly"></a>Az egyszerű szolgáltatásnév hitelesítő adatai helytelenül lettek megadva

A sablon vagy Marketplace-ajánlat bemenetének megadásakor a helytelen információ lett megadva. Győződjön meg arról, hogy az egyszerű szolgáltatásnév megfelelő appId (clientId) és jelszavát (clientSecret) használja. Ellenőrizze az alábbi Azure CLI-parancs kiadásával.

```azurecli
az login --service-principal -u <client id> -p <client secret> -t <tenant id>
```

### <a name="service-principal-doesnt-have-contributor-access-to-the-resource-group"></a>Az egyszerű szolgáltatásnév nem rendelkezik közreműködői hozzáféréssel az erőforráscsoporthoz

Ha az Azure Cloud Provider engedélyezve van, akkor a használt szolgáltatásnak közreműködői hozzáféréssel kell rendelkeznie az erőforráscsoporthoz. Ellenőrizze az alábbi Azure CLI-parancs kiadásával.

```azurecli
az group update -g <openshift resource group> --set tags.sptest=test
```

## <a name="additional-tools"></a>További eszközök

Bizonyos hibák esetén az alábbi parancsokkal további információkat kaphat:

1. systemctl állapota \<service>
2. journalctl – XE
