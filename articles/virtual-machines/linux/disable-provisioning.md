---
title: A kiépítési ügynök letiltása vagy eltávolítása
description: Megtudhatja, hogyan tilthatja le vagy távolíthatja el a kiépítési ügynököt Linux rendszerű virtuális gépeken és lemezképeken.
author: danielsollondon
ms.service: virtual-machines
ms.collection: linux
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 07/06/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: 7c797957c292b9859ca41951b15f58c3d0be40b2
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102561066"
---
# <a name="disable-or-remove-the-linux-agent-from-vms-and-images"></a>A Linux-ügynök letiltása vagy eltávolítása a virtuális gépekről és a lemezképekről

A Linux-ügynök eltávolítása előtt tisztában kell lennie azzal, hogy a Linux-ügynök eltávolítása után milyen virtuális gépek nem lesznek képesek a művelet elvégzésére.

Az Azure virtuálisgép- [bővítmények](../extensions/overview.md) olyan kisméretű alkalmazások, amelyek üzembe helyezés utáni konfigurációs és automatizálási feladatokat biztosítanak az Azure-beli virtuális gépeken, a bővítményeket az Azure vezérlő síkja telepíti és kezeli. Az [Azure Linux-ügynök](../extensions/agent-linux.md) feladata, hogy feldolgozza a platform-bővítmény parancsait, és gondoskodjon arról, hogy a bővítmény helyes állapota a virtuális gépen belül legyen.

Az Azure platform számos olyan bővítményt üzemeltet, amely a virtuálisgép-konfigurációtól, a figyelési, a biztonsági és a segédprogram-alkalmazásokból származik. Az első és a harmadik féltől származó bővítmények széles választékát, példákat tartalmaz a bővítmények által használt főbb forgatókönyvekre:
* Az Azure-szolgáltatások (például a Azure Backup, a figyelés, a lemezek titkosítása, a biztonság, a hely replikálása és mások) támogatása.
* SSH/jelszó alaphelyzetbe állítása
* VM-konfiguráció – egyéni parancsfájlok futtatása, Chef, Puppet Agent stb. telepítése.
* A harmadik féltől származó termékek, például az AV-termékek, a virtuális gép sebezhetőségi eszközei, a virtuális gépek és az alkalmazások figyelése.
* A bővítmények a virtuális gépek új üzembe helyezésével is elhelyezhetők. Lehetnek például egy nagyobb üzembe helyezések, a virtuális gépek üzembe helyezése, illetve a támogatott bővítmények által a telepítés után futtatott alkalmazások.

## <a name="disabling-extension-processing"></a>Bővítmény feldolgozásának letiltása

Több módon is letilthatja a bővítmények feldolgozását, az igényeinek megfelelően, de a folytatás előtt el **kell** távolítania a virtuális gépre telepített összes bővítményt, például az Azure [CLI használatával:](/cli/azure/vm/extension#az-vm-extension-list) [](/cli/azure/vm/extension#az-vm-extension-delete)

```azurecli
az vm extension delete -g MyResourceGroup --vm-name MyVm -n extension_name
```
> [!Note]
> 
> Ha ezt nem teszi meg, a platform megpróbálja elküldeni a bővítmény konfigurációját és időtúllépését a 40min után.

### <a name="disable-at-the-control-plane"></a>Letiltás a vezérlési síkon
Ha nem biztos abban, hogy a jövőben szükség van-e a bővítményekre, a Linux-ügynököt a virtuális gépre is telepítheti, majd letilthatja a bővítmény feldolgozási képességeit a platformról. Ez a beállítás az API- `Microsoft.Compute` verzióban `2018-06-01` vagy magasabbban érhető el, és nem függ a Linux-ügynök telepített verziójától.

```azurecli
az vm update -g <resourceGroup> -n <vmName> --set osProfile.allowExtensionOperations=false
```
A bővítmény feldolgozását könnyedén újraengedélyezheti a platformról, a fenti paranccsal azonban "true" (igaz) értékre állíthatja.

## <a name="remove-the-linux-agent-from-a-running-vm"></a>Linux-ügynök eltávolítása egy futó virtuális gépről

Győződjön meg arról, hogy az összes meglévő bővítményt **eltávolította** a virtuális gépről a fentiek szerint.

### <a name="step-1-remove-the-azure-linux-agent"></a>1. lépés: az Azure Linux-ügynök eltávolítása

Ha csak a Linux-ügynököt távolítja el, és nem a társított konfigurációs összetevőket, akkor később újratelepítheti. Az Azure Linux-ügynök eltávolításához futtassa a következők egyikét:

#### <a name="for-ubuntu-1804"></a>Ubuntu >= 18,04
```bash
apt -y remove walinuxagent
```

#### <a name="for-redhat--77"></a>RedHat >= 7,7
```bash
yum -y remove WALinuxAgent
```

#### <a name="for-suse"></a>SUSE esetén
```bash
zypper --non-interactive remove python-azure-agent
```

### <a name="step-2-optional-remove-the-azure-linux-agent-artifacts"></a>2. lépés: (nem kötelező) az Azure Linux-ügynök összetevőinek eltávolítása
> [!IMPORTANT] 
>
> Eltávolíthatja a Linux-ügynök összes kapcsolódó összetevőjét, de ez azt jelenti, hogy később nem lehet újratelepíteni. Ezért javasoljuk, hogy először tiltsa le a Linux-ügynököt, és távolítsa el a Linux-ügynököt a fentiekben leírtak szerint. 

Ha tudja, hogy többé nem fogja újra újratelepíteni a Linux-ügynököt, futtassa a következőt:

#### <a name="for-ubuntu-1804"></a>Ubuntu >= 18,04
```bash
apt -y purge walinuxagent
rm -rf /var/lib/waagent
rm -f /var/log/waagent.log
```

#### <a name="for-redhat--77"></a>RedHat >= 7,7
```bash
yum -y remove WALinuxAgent
rm -f /etc/waagent.conf.rpmsave
rm -rf /var/lib/waagent
rm -f /var/log/waagent.log
```

#### <a name="for-suse"></a>SUSE esetén
```bash
zypper --non-interactive remove python-azure-agent
rm -f /etc/waagent.conf.rpmsave
rm -rf /var/lib/waagent
rm -f /var/log/waagent.log
```

## <a name="preparing-an-image-without-the-linux-agent"></a>Rendszerkép előkészítése Linux-ügynök nélkül
Ha olyan rendszerképpel rendelkezik, amely már tartalmazza a Cloud-init-t, és el szeretné távolítani a Linux-ügynököt, de a Cloud-init használatával továbbra is üzembe kell helyeznie, futtassa a 2. lépésben (és opcionálisan 3. lépés) az Azure Linux-ügynök eltávolításához.

```bash
cloud-init clean --logs --seed 
```

## <a name="deprovision-and-create-an-image"></a>Rendszerkép kiépítése és létrehozása
A Linux-ügynök képes megtisztítani a meglévő képmetaadatok némelyikét, az "waagent-megszüntetés + felhasználó" lépéssel azonban a eltávolítása után el kell végeznie az alábbi műveleteket, és el kell távolítania a többi bizalmas adatát is.

- Az összes meglévő SSH-gazdagép kulcsának eltávolítása

   ```bash
   rm /etc/ssh/ssh_host_*key*
   ```
- A rendszergazdai fiók törlése

   ```bash
   touch /var/run/utmp
   userdel -f -r <admin_user_account>
   ```
- A gyökér jelszavának törlése

   ```bash
   passwd -d root
   ```

A fentiek elvégzése után létrehozhatja az egyéni rendszerképet az Azure CLI használatával.


**Normál felügyelt rendszerkép létrehozása**
```azurecli
az vm deallocate -g <resource_group> -n <vm_name>
az vm generalize -g <resource_group> -n <vm_name>
az image create -g <resource_group> -n <image_name> --source <vm_name>
```

**Rendszerkép-verzió létrehozása megosztott rendszerkép-gyűjteményben**

```azurecli
az sig image-version create \
    -g $sigResourceGroup 
    --gallery-name $sigName 
    --gallery-image-definition $imageDefName 
    --gallery-image-version 1.0.0 
    --managed-image /subscriptions/00000000-0000-0000-0000-00000000xxxx/resourceGroups/imageGroups/providers/images/MyManagedImage
```
### <a name="creating-a-vm-from-an-image-that-does-not-contain-a-linux-agent"></a>Virtuális gép létrehozása Linux-ügynököt nem tartalmazó rendszerképből
Ha a virtuális gépet Linux-ügynök nélkül hozza létre a rendszerképből, gondoskodnia kell arról, hogy a virtuális gép üzembe helyezési konfigurációja a bővítmények ne legyenek támogatottak ezen a virtuális gépen.

> [!NOTE] 
> 
> Ha ezt nem teszi meg, a platform megpróbálja elküldeni a bővítmény konfigurációját és időtúllépését a 40min után.

Ha a virtuális gépet a bővítmények letiltásával szeretné telepíteni, használhatja az Azure CLI-t az [--enable-Agent](/cli/azure/vm#az-vm-create)paranccsal.

```azurecli
az vm create \
    --resource-group $resourceGroup \
    --name $prodVmName \
    --image RedHat:RHEL:8.1-ci:latest \
    --admin-username azadmin \
    --ssh-key-value "$sshPubkeyPath" \
    --enable-agent false
```

Azt is megteheti, hogy az Azure Resource Manager (ARM) sablonokat használja a beállítással `"provisionVMAgent": false,` .

```json
"osProfile": {
    "computerName": "[parameters('virtualMachineName')]",
    "adminUsername": "[parameters('adminUsername')]",
    "linuxConfiguration": {
        "disablePasswordAuthentication": "true",
        "provisionVMAgent": false,
        "ssh": {
            "publicKeys": [
                {
                    "path": "[concat('/home/', parameters('adminUsername'), '/.ssh/authorized_keys')]",
                    "keyData": "[parameters('adminPublicKey')]"
```

## <a name="next-steps"></a>Következő lépések

További információ: [Linux kiépítés](provisioning.md).
