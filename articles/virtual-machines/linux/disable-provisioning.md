---
title: A kiépítési ügynök letiltása vagy eltávolítása
description: Megtudhatja, hogyan tilthatja le vagy távolíthatja el a kiépítési ügynököt Linux rendszerű virtuális gépeken és rendszerképeken.
author: danielsollondon
ms.service: virtual-machines
ms.collection: linux
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 07/06/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: c70b02bdc554c723f53ad5f8c0d36c5eca87811e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774367"
---
# <a name="disable-or-remove-the-linux-agent-from-vms-and-images"></a>Linux-ügynök letiltása vagy eltávolítása virtuális gépekről és rendszerképekből

A Linux-ügynök eltávolítása előtt tisztában kell lennie vele, hogy mit nem tud a virtuális gép a Linux-ügynök eltávolítása után.

Az Azure-beli [](../extensions/overview.md) virtuálisgép-bővítmények kis méretű alkalmazások, amelyek üzembe helyezés utáni konfigurációs és automatizálási feladatokat biztosítanak az Azure-beli virtuális gépeken, a bővítményeket az Azure vezérlősíkja telepíti és kezeli. Az [Azure Linux-ügynök](../extensions/agent-linux.md) feladata a platformbővítmény-parancsok feldolgozása és a virtuális gépen belüli bővítmény megfelelő állapotának biztosítása.

Az Azure platform számos olyan bővítményt kínál, amelyek a virtuális gépek konfigurálásán, figyelésen, biztonságon és segédprogram-alkalmazásokon keresztül bővítmények. Az első és harmadik féltől származó bővítmények széles választéka bővítmények, például olyan kulcsfontosságú forgatókönyvek, amelyekhez bővítményeket használnak:
* Külső Azure-szolgáltatások, például a Azure Backup, a monitorozás, a lemeztitkosítás, a biztonság, a helyreplikáció és egyebek támogatása.
* SSH/ Új jelszó kér
* Virtuálisgép-konfiguráció – Egyéni szkriptek futtatása, Chef- és Puppet-ügynökök telepítése stb.
* Külső termékek, például AV-termékek, virtuális gépek sebezhetőségi eszközei, virtuális gépek és alkalmazásfigyelési eszközök.
* A bővítmények egy új virtuálisgép-telepítéssel csomagolhatóak. Például egy nagyobb üzemelő példány részei, az alkalmazások virtuális gépeken való üzembe helyezése, vagy bármely támogatott, bővítmény által működtetett rendszeren való futtatás az üzembe helyezés után.

## <a name="disabling-extension-processing"></a>Bővítményfeldolgozás letiltása

A bővítmények feldolgozását többféleképpen is letilthatja, de a  folytatás előtt el kell távolítania a virtuális gépen üzembe helyezett [](/cli/azure/vm/extension#az_vm_extension_list) összes bővítményt, például az Azure CLI használatával listából és törlésből: [](/cli/azure/vm/extension#az_vm_extension_delete)

```azurecli
az vm extension delete -g MyResourceGroup --vm-name MyVm -n extension_name
```
> [!Note]
> 
> Ha nem a fenti lépéseket, a platform 40 perc után megpróbálja elküldeni a bővítménykonfigurációt és az időtúllépést.

### <a name="disable-at-the-control-plane"></a>Letiltás a vezérlősíkon
Ha nem biztos abban, hogy a jövőben szüksége lesz-e bővítményekre, a Linux-ügynököt telepítve hagyhatja a virtuális gépen, majd letilthatja a bővítményfeldolgozási képességet a platformról. Ez a lehetőség az API vagy újabb verziókban érhető el, és nincs `Microsoft.Compute` `2018-06-01` függősége a telepített Linux-ügynök verziójától.

```azurecli
az vm update -g <resourceGroup> -n <vmName> --set osProfile.allowExtensionOperations=false
```
A fenti paranccsal egyszerűen újra meg lehet valósíthatja ezt a bővítményfeldolgozást a platformról, de állítsa "true" (igaz) értékre.

## <a name="remove-the-linux-agent-from-a-running-vm"></a>Linux-ügynök eltávolítása egy futó virtuális gépről

Győződjön meg arról, **hogy** korábban eltávolította az összes meglévő bővítményt a virtuális gépről, a fentiek szerint.

### <a name="step-1-remove-the-azure-linux-agent"></a>1. lépés: Az Azure Linux-ügynök eltávolítása

Ha csak a Linux-ügynököt távolítja el, és nem a társított konfigurációs összetevőkét, később újratelepítheti. Futtassa a következők egyikét gyökérként az Azure Linux-ügynök eltávolításához:

#### <a name="for-ubuntu-1804"></a>Ubuntu->=18.04
```bash
apt -y remove walinuxagent
```

#### <a name="for-redhat--77"></a>Redhat->= 7,7
```bash
yum -y remove WALinuxAgent
```

#### <a name="for-suse"></a>SUSE-hez
```bash
zypper --non-interactive remove python-azure-agent
```

### <a name="step-2-optional-remove-the-azure-linux-agent-artifacts"></a>2. lépés: (Nem kötelező) Az Azure Linux-ügynök összetevőinek eltávolítása
> [!IMPORTANT] 
>
> A Linux-ügynök összes társított összetevőját eltávolíthatja, de ez azt jelenti, hogy később nem tudja újratelepíteni. Ezért erősen ajánlott először letiltani a Linux-ügynököt, és eltávolítani a Linux-ügynököt csak a fentiek használatával. 

Ha tudja, hogy többé nem telepíti újra a Linux-ügynököt, a következőt futtathatja:

#### <a name="for-ubuntu-1804"></a>Ubuntu->=18.04
```bash
apt -y purge walinuxagent
rm -rf /var/lib/waagent
rm -f /var/log/waagent.log
```

#### <a name="for-redhat--77"></a>Redhat->= 7,7
```bash
yum -y remove WALinuxAgent
rm -f /etc/waagent.conf.rpmsave
rm -rf /var/lib/waagent
rm -f /var/log/waagent.log
```

#### <a name="for-suse"></a>SUSE-hez
```bash
zypper --non-interactive remove python-azure-agent
rm -f /etc/waagent.conf.rpmsave
rm -rf /var/lib/waagent
rm -f /var/log/waagent.log
```

## <a name="preparing-an-image-without-the-linux-agent"></a>Rendszerkép előkészítése Linux-ügynök nélkül
Ha olyan rendszerképe van, amely már tartalmazza a cloud-initet, és el szeretné távolítani a Linux-ügynököt, de továbbra is a cloud-init használatával szeretné kiépíteni, futtassa gyökérként a 2. lépés (és opcionálisan a 3. lépés) lépéseit az Azure Linux-ügynök eltávolításához, majd a következők eltávolítják a cloud-init konfigurációt és a gyorsítótárazott adatokat, és előkészítik a virtuális gépet egy egyéni rendszerkép létrehozására.

```bash
cloud-init clean --logs --seed 
```

## <a name="deprovision-and-create-an-image"></a>Rendszerkép megszüntetése és létrehozása
A Linux-ügynök a "waagent -deprovision+user" lépéssel eltávolíthat néhány meglévő rendszerkép-metaadatot, azonban az eltávolítás után el kell végeznie az alábbihoz hasonló műveleteket, és el kell távolítania a többi bizalmas adatot.

- Az összes meglévő SSH-gazdakulcs eltávolítása

   ```bash
   rm /etc/ssh/ssh_host_*key*
   ```
- A rendszergazdai fiók törlése

   ```bash
   touch /var/run/utmp
   userdel -f -r <admin_user_account>
   ```
- A gyökér szintű jelszó törlése

   ```bash
   passwd -d root
   ```

A fentiek befejezése után létrehozhatja az egyéni rendszerképet az Azure CLI használatával.


**Normál felügyelt rendszerkép létrehozása**
```azurecli
az vm deallocate -g <resource_group> -n <vm_name>
az vm generalize -g <resource_group> -n <vm_name>
az image create -g <resource_group> -n <image_name> --source <vm_name>
```

**Rendszerképverzió létrehozása egy Shared Image Gallery**

```azurecli
az sig image-version create \
    -g $sigResourceGroup 
    --gallery-name $sigName 
    --gallery-image-definition $imageDefName 
    --gallery-image-version 1.0.0 
    --managed-image /subscriptions/00000000-0000-0000-0000-00000000xxxx/resourceGroups/imageGroups/providers/images/MyManagedImage
```
### <a name="creating-a-vm-from-an-image-that-does-not-contain-a-linux-agent"></a>Virtuális gép létrehozása linuxos ügynököt nem tartalmazó rendszerképből
Amikor Linux-ügynök nélkül hozza létre a virtuális gépet a rendszerképből, győződjön meg arról, hogy a virtuális gép üzembe helyezésének konfigurációja azt jelzi, hogy a bővítmények nem támogatottak ezen a virtuális gépen.

> [!NOTE] 
> 
> Ha nem a fenti lépéseket, a platform 40 perc után megpróbálja elküldeni a bővítménykonfigurációt és az időtúllépést.

A virtuális gép letiltott bővítményekkel való üzembe helyezéséhez használhatja az Azure CLI-t az [--enable-agent ügynökkel.](/cli/azure/vm#az_vm_create)

```azurecli
az vm create \
    --resource-group $resourceGroup \
    --name $prodVmName \
    --image RedHat:RHEL:8.1-ci:latest \
    --admin-username azadmin \
    --ssh-key-value "$sshPubkeyPath" \
    --enable-agent false
```

Másik lehetőségként ezt az (ARM) Azure Resource Manager is használhatja a `"provisionVMAgent": false,` beállításával.

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

További információ: [Provisioning Linux](provisioning.md).
