---
title: Oktatóanyag – Azure Virtual Networks létrehozása és kezelése Linux rendszerű virtuális gépekhez
description: Ez az oktatóanyag bemutatja az Azure-alapú virtuális hálózatok Linux rendszerű virtuális gépeken való létrehozását és kezelését az Azure CLI-vel
services: virtual-machines
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
ms.collection: linux
tags: azure-resource-manager
ms.subservice: networking
ms.assetid: ''
ms.service: virtual-machines
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 6356d9b587847ff2b49574b970109ada0d785660
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102564628"
---
# <a name="tutorial-create-and-manage-azure-virtual-networks-for-linux-virtual-machines-with-the-azure-cli"></a>Oktatóanyag: Azure-alapú virtuális hálózatok létrehozása és kezelése Linux rendszerű virtuális gépeken az Azure CLI-vel

Az Azure-beli virtuális gépek Azure hálózatkezelést használnak a belső és külső hálózati kommunikációhoz. Ez az oktatóanyag végigvezeti két virtuális gép telepítésén és az Azure hálózatkezelés konfigurálásán ezen virtuális gépekhez. Az oktatóanyagban szereplő példák feltételezik, hogy a virtuális gépek üzemeltetnek egy webalkalmazást egy adatbázis-alapú háttérrendszerrel, de az oktatóanyag során nem telepítünk ilyen alkalmazást. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Virtuális hálózat és alhálózat létrehozása
> * Hozzon létre egy nyilvános IP-címet
> * Előtérbeli virtuális gép létrehozása
> * Biztonságos hálózati adatforgalom
> * Háttérbeli virtuális gép létrehozása

Ez az oktatóanyag a CLI-t használja a [Azure Cloud Shellon](../../cloud-shell/overview.md)belül, amely folyamatosan frissül a legújabb verzióra. A Cloud Shell megnyitásához válassza a **kipróbálás** lehetőséget a kód bármely blokkjának elejéről.

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.0.30-as vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="vm-networking-overview"></a>Virtuális gépek hálózatkezelése – áttekintés

Az Azure Virtual Networks lehetővé teszi a virtuális gépek, az Internet és más Azure-szolgáltatások, például a Azure SQL Database közötti biztonságos hálózati kapcsolatok létrehozását. A virtuális hálózatok alhálózatnak nevezett logikai szegmensekre oszthatók. Az alhálózatok segítségével szabályozható a hálózati forgalom, valamint biztonsági határként is használhatók. Egy virtuális géphez a telepítéskor általában egy virtuális hálózati adapter tartozik, amely egy alhálózathoz csatlakozik.

Az oktatóanyag végéig a következő virtuális hálózati erőforrások jönnek létre:

![Virtuális hálózat két alhálózattal](./media/tutorial-virtual-network/networktutorial.png)

- *myVNet* – Az a virtuális hálózat, amely segítségével a virtuális gépek kommunikálnak egymással és az internettel.
- *myFrontendSubnet* – A *myVNet* előtérbeli erőforrásai által használt alhálózat.
- *myPublicIPAddress* – Az internet felől a *myFrontendVM* elérésére használt nyilvános IP-cím.
- *myFrontentNic* – A *myFrontendVM* által a *myBackendVM* virtuális géppel való kommunikációra használt hálózati adapter.
- *myFrontendVM* – Az internet és a *myBackendVM* közötti kommunikációra használt virtuális gép.
- *myBackendNSG* – A *myFrontendVM* és a *myBackendVM* közti kommunikációt szabályozó hálózati biztonsági csoport.
- *myBackendSubnet* – *myBackendNSG* csoporthoz társított, és a háttérbeli erőforrások által használt alhálózat.
- *myBackendNic* – A *myBackendVM* által *myFrontendVM* virtuális géppel való kommunikációra használt hálózati adapter.
- *myBackendVM* – A *myFrontendVM* virtuális géppel a 22-es és 3306-os porton keresztül kommunikáló virtuális gép.

## <a name="create-a-virtual-network-and-subnet"></a>Virtuális hálózat és alhálózat létrehozása

Ebben az oktatóanyagban egy virtuális hálózatot hozunk létre két alhálózattal, egy előtérbeli alhálózatot egy webalkalmazás üzemeltetéséhez, és egy háttérbeli alhálózatot egy adatbázis-kiszolgáló üzemeltetéséhez.

A virtuális hálózat létrehozása előtt létre kell hoznia egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. A következő példában létrehozunk egy *myRGNetwork* nevű erőforráscsoportot a eastus helyen.

```azurecli-interactive 
az group create --name myRGNetwork --location eastus
```

### <a name="create-virtual-network"></a>Virtuális hálózat létrehozása

Hozzon létre egy virtuális hálózatot az [az network vnet create](/cli/azure/network/vnet) paranccsal. Ebben a példában a hálózat neve *mvVNet*, és egy *10.0.0.0/16* címelőtagot kap. Létrejön egy alhálózat is *myFrontendSubnet* néven, *10.0.1.0/24* előtaggal. Az oktatóanyag későbbi részében ehhez az alhálózathoz egy előtérbeli virtuális gép csatlakozik. 

```azurecli-interactive 
az network vnet create \
  --resource-group myRGNetwork \
  --name myVNet \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myFrontendSubnet \
  --subnet-prefix 10.0.1.0/24
```

### <a name="create-subnet"></a>Alhálózat létrehozása

Az [az network vnet subnet create](/cli/azure/network/vnet/subnet) paranccsal hozzáadunk egy új alhálózatot a virtuális hálózathoz. Ebben a példában az alhálózat neve *myBackendSubnet*, és a *10.0.2.0/24* címelőtagot kapja. A rendszer ezt az alhálózatot használja minden háttérbeli szolgáltatáshoz.

```azurecli-interactive 
az network vnet subnet create \
  --resource-group myRGNetwork \
  --vnet-name myVNet \
  --name myBackendSubnet \
  --address-prefix 10.0.2.0/24
```

Ekkorra létrehoztunk egy hálózatot, és két alhálózatra osztottuk, amelyek közül az egyik az előtérbeli szolgáltatásokhoz, a másik a háttérbeli szolgáltatásokhoz tartozik. A következő szakaszban a virtuális gépeket hozzuk létre, majd csatlakoztatjuk azokat az alhálózatokhoz.

## <a name="create-a-public-ip-address"></a>Hozzon létre egy nyilvános IP-címet

A nyilvános IP-cím lehetővé teszi, hogy az Azure-erőforrások elérhetők legyenek az interneten. A nyilvános IP-cím kiosztási módszere konfigurálható dinamikusnak vagy statikusnak. Alapértelmezés szerint a rendszer dinamikusan osztja ki a nyilvános IP-címeket. A dinamikus IP-címek az egyes virtuális gépek felszabadításakor felszabadulnak. Ennek következtében az IP-cím minden, virtuálisgép-felszabadítást is tartalmazó művelet során módosul.

A kiosztási módszer átállítható statikusra. Ez biztosítja, hogy a virtuális géphez hozzárendelve maradjon az IP-cím felszabadított állapotában is. A statikusan kiosztott IP-cím használata esetén nem adható meg az IP-cím. Ehelyett a rendszer osztja ki az IP-címet az elérhető címek készletéből.

```azurecli-interactive
az network public-ip create --resource-group myRGNetwork --name myPublicIPAddress
```

Amikor az [az vm create](/cli/azure/vm) paranccsal létrehoz egy virtuális gépet, az IP-cím-kiosztási módszer alapértelmezés szerint dinamikus. Ha az [az vm create](/cli/azure/vm) hoz létre virtuális gépet, a `--public-ip-address-allocation static` argumentum szerepeltetésével rendelhet statikus IP-cím a virtuális géphez. Ezt a műveletet nincs ismertetjük ebben az oktatóanyagban, de a következő szakaszban bemutatjuk a dinamikusan kiosztott IP-cím statikusan kiosztott címre módosításának módját. 

### <a name="change-allocation-method"></a>A kiosztási módszer módosítása

Az IP-cím-kiosztási módszer az [az network public-ip update](/cli/azure/network/public-ip) paranccsal módosítható. Ebben a példában az előtérbeli virtuális gép IP-cím-kiosztási módszerét módosítottuk statikusra.

Először szabadítsa fel a virtuális gépet.

```azurecli-interactive 
az vm deallocate --resource-group myRGNetwork --name myFrontendVM
```

Frissítse a kiosztási módszert az [az network public-ip update](/cli/azure/network/public-ip) paranccsal. Ebben az esetben az `--allocation-method` beállítása *static* (statikus) lesz.

```azurecli-interactive 
az network public-ip update --resource-group myRGNetwork --name myPublicIPAddress --allocation-method static
```

Indítsa el a virtuális gépet.

```azurecli-interactive 
az vm start --resource-group myRGNetwork --name myFrontendVM --no-wait
```

### <a name="no-public-ip-address"></a>Nincs nyilvános IP-cím

Gyakran előfordul, hogy egy virtuális gépnek nem kell elérhetőnek lennie az interneten. Ha nyilvános IP-cím nélkül szeretne létrehozni egy virtuális gépet, a `--public-ip-address ""` argumentumban egy üres idézőjelpárt szerepeltessen. Az oktatóanyag későbbi részében bemutatjuk ezt a konfigurációt.

## <a name="create-a-front-end-vm"></a>Előtérbeli virtuális gép létrehozása

Az [az vm create](/cli/azure/vm) paranccsal hozzon létre egy *myPublicIPAddress* IP-címet használó *myFrontendVM* nevű virtuális gépet.

```azurecli-interactive 
az vm create \
  --resource-group myRGNetwork \
  --name myFrontendVM \
  --vnet-name myVNet \
  --subnet myFrontendSubnet \
  --nsg myFrontendNSG \
  --public-ip-address myPublicIPAddress \
  --image UbuntuLTS \
  --generate-ssh-keys
```

## <a name="secure-network-traffic"></a>Biztonságos hálózati adatforgalom

A hálózati biztonsági csoport (NSG) egy biztonsági szabályokból álló listát tartalmaz, amelyek engedélyezik vagy megtagadják a hálózati forgalmat az Azure-alapú virtuális hálózatokhoz (VNet-ekhez) csatlakozó erőforrásoknak. Az NSG-k társíthatók alhálózatokhoz vagy egyedi hálózati adapterekhez. Amikor egy NSG-t egy hálózati adapterhez társít, az csak a társított virtuális gépre vonatkozik. Ha az NSG-t hozzárendelik egy alhálózathoz, a szabályok érvényesek lesznek az alhálózathoz csatlakozó összes erőforrásra. 

### <a name="network-security-group-rules"></a>Hálózat biztonsági csoportok szabályai

Az NSG-szabályok határozzák meg azokat a hálózatkezelési portokat, amelyeken engedélyezett vagy tiltott a forgalom. A szabályok között szerepelhetnek forrás és cél IP-címtartományok, így szabályozható az adatforgalom adott rendszerek vagy alhálózatok között. Az NSG-szabályok között megadható a prioritás is (1–4096). A szabályokat a rendszer prioritás szerinti sorrendben értékeli. A 100-as prioritású szabályt a rendszer a 200-as prioritású szabály előtt ellenőrzi.

Minden NSG tartalmaz egy alapértelmezett szabálykészletet. Az alapértelmezett szabályokat nem lehet törölni, de mivel a legalacsonyabb prioritást rendelték hozzájuk, a létrehozott szabályok felülbírálhatják azokat.

Az alapértelmezett NSG-szabályok a következők:

- **Virtuális hálózat** – A virtuális hálózatból kiinduló és oda érkező forgalom a bejövő és kimenő irányban is engedélyezve van.
- **Internet** – A kimenő forgalom engedélyezett, de a bejövő forgalom le van tiltva.
- **Terheléselosztó** – Engedélyezi az Azure terheléselosztója számára, hogy megvizsgálja a virtuális gépek és a szerepkörpéldányok állapotát. Ha nem terheléselosztásos készletet használ, ezt a szabályt felül lehet bírálni.

### <a name="create-network-security-groups"></a>Hálózati biztonsági csoportok létrehozása

A virtuális gép létrehozásával egy időben létrehozható egy hálózati biztonsági csoport is az [az vm create](/cli/azure/vm) paranccsal. Ebben a folyamatban az NSG-t a rendszer a virtuális gép hálózati adapteréhez társítja, és automatikusan létrejön egy NSG-szabály, amely a *22*-es porton keresztül bármilyen forrásból engedélyezi az adatforgalmat. Az oktatóanyag egy korábbi szakaszában az előtérbeli virtuális gép létrehozásakor automatikusan létrejött az előtérbeli NSG is. Automatikusan létrejött egy NSG-szabály is a 22-es porthoz. 

Bizonyos esetekben érdemes lehet előre létrehozni egy NSG-t, például amikor nem akarjuk, hogy létrejöjjenek alapértelmezett SSH-szabályok, vagy amikor az NSG-t egy alhálózathoz akarjuk csatolni. 

Hozzon létre egy hálózati biztonsági csoportot az [az network nsg create](/cli/azure/network/nsg) paranccsal.

```azurecli-interactive 
az network nsg create --resource-group myRGNetwork --name myBackendNSG
```

Az NSG nem egy hálózati adapterhez lesz csatolva, hanem egy alhálózathoz. Ebben a konfigurációban az alhálózathoz csatolt összes virtuális gép örökli az NSG-szabályokat.

Frissítse a meglévő *myBackendSubnet* nevű alhálózatot az új NSG-vel.

```azurecli-interactive 
az network vnet subnet update \
  --resource-group myRGNetwork \
  --vnet-name myVNet \
  --name myBackendSubnet \
  --network-security-group myBackendNSG
```

### <a name="secure-incoming-traffic"></a>Bejövő adatforgalom biztosítása

Az előtérbeli virtuális gép létrehozásakor létrejön egy NSG-szabály, amely engedélyezi a bejövő forgalmat a 22-es porton. Ez a szabály engedélyezi az SSH-kapcsolatokat a virtuális gép számára. Ebben a példában a forgalmat a *80*-as porton is engedélyezni kell. Ez a konfiguráció lehetővé teszi egy webalkalmazás elérését a virtuális gépen.

Hozzon létre egy szabályt a *80*-as porthoz az [az network nsg rule create](/cli/azure/network/nsg/rule) paranccsal.

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myFrontendNSG \
  --name http \
  --access allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 200 \
  --source-address-prefix "*" \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range 80
```

Az előtérbeli virtuális gép csak a *22*-es és a *80*-as porton érhető el. A hálózati biztonsági csoport minden egyéb adatforgalmat blokkol. Hasznos lehet az NSG-szabályok konfigurációinak megjelenítése. Az NSG-szabály konfigurációját az [az network rule list](/cli/azure/network/nsg/rule) paranccsal kérheti le. 

```azurecli-interactive 
az network nsg rule list --resource-group myRGNetwork --nsg-name myFrontendNSG --output table
```

### <a name="secure-vm-to-vm-traffic"></a>Virtuális gépek közötti kapcsolat biztosítása

A hálózati biztonsági csoport szabályai virtuális gépek közötti forgalomra is vonatkozhatnak. Ebben a példában az előtérbeli virtuális gépnek a *22*-es és a *3306*-os porton kell kommunikálnia a háttérbeli virtuális géppel. Ez a konfiguráció engedélyezi az SSH-kapcsolatokat az előtér virtuális gépről, és azt is lehetővé teszi, hogy az előtérbeli virtuális gép egy alkalmazása kommunikáljon egy háttérbeli MySQL-adatbázissal. Az összes többi forgalmat le kell tiltani az előtérbeli és a háttérbeli virtuális gépek között.

Hozzon létre egy szabályt a 22-es porthoz az [az network nsg rule create](/cli/azure/network/nsg/rule) paranccsal. Figyelje meg, hogy a `--source-address-prefix` argumentum *10.0.1.0/24* értéket határoz meg. Ez a konfiguráció biztosítja, hogy csak az előtérbeli alhálózatból érkező adatforgalom haladjon át az NSG-n.

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myBackendNSG \
  --name SSH \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 100 \
  --source-address-prefix 10.0.1.0/24 \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range "22"
```

Most adjon hozzá egy, a 3306-os porton folyó MySQL-adatforgalomra vonatkozó szabályt.

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myBackendNSG \
  --name MySQL \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 200 \
  --source-address-prefix 10.0.1.0/24 \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range "3306"
```

Végül, mivel az NSG-k egy alapértelmezett szabálya szerint minden azonos virtuális hálózaton lévő virtuális gép közötti adatforgalom engedélyezett, létrehozható egy szabály a háttérbeli NSG-khez, hogy blokkoljanak minden forgalmat. Figyelje meg, hogy a `--priority` értéke itt *300*, ami alacsonyabb az NSG- és a MySQL-szabályokénál is. Ez a konfiguráció biztosítja, hogy az SSH- és a MySQL-forgalom továbbra is áthaladhasson az NSG-n.

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myBackendNSG \
  --name denyAll \
  --access Deny \
  --protocol Tcp \
  --direction Inbound \
  --priority 300 \
  --source-address-prefix "*" \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range "*"
```

## <a name="create-back-end-vm"></a>Háttérbeli virtuális gép létrehozása

Most hozzon létre egy, a *myBackendSubnet* hálózathoz csatolt virtuális gépet. Figyelje meg, hogy az `--nsg` argumentum értéke egy üres idézőjelpár. A virtuális géppel együtt nem kell létrehozni NSG-t. A virtuális gépet a rendszer a háttérbeli alhálózathoz csatolja, amelyet az előre létrehozott háttérbeli NSG véd. Ez az NSG vonatkozik a virtuális gépre. Azt is figyelje meg, hogy az `--public-ip-address` argumentum értéke egy üres idézőjelpár. Ez a konfiguráció nyilvános IP-cím nélkül hoz létre egy virtuális gépet. 

```azurecli-interactive 
az vm create \
  --resource-group myRGNetwork \
  --name myBackendVM \
  --vnet-name myVNet \
  --subnet myBackendSubnet \
  --public-ip-address "" \
  --nsg "" \
  --image UbuntuLTS \
  --generate-ssh-keys
```

A háttérbeli virtuális gép csak a *22*-es és a *3306*-os porton keresztül érhető el az előtérbeli alhálózatból. A hálózati biztonsági csoport minden egyéb adatforgalmat blokkol. Hasznos lehet az NSG-szabályok konfigurációinak megjelenítése. Az NSG-szabály konfigurációját az [az network rule list](/cli/azure/network/nsg/rule) paranccsal kérheti le. 

```azurecli-interactive 
az network nsg rule list --resource-group myRGNetwork --nsg-name myBackendNSG --output table
```

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban virtuális gépekhez csatolva hozta létre és biztosította az Azure-hálózatokat. Megtanulta végrehajtani az alábbi műveleteket:

> [!div class="checklist"]
> * Virtuális hálózat és alhálózat létrehozása
> * Hozzon létre egy nyilvános IP-címet
> * Előtérbeli virtuális gép létrehozása
> * Biztonságos hálózati adatforgalom
> * Háttérbeli virtuális gép létrehozása

A virtuális gépek lemezeinek védelméről további információért lásd: [biztonsági mentés és vész-helyreállítás lemezek esetén](../backup-and-disaster-recovery-for-azure-iaas-disks.md).