---
title: Virtuális gépek magánhálózati IP-címeinek konfigurálása (klasszikus) – parancssori felület
titlesuffix: Azure Virtual Network
description: Megtudhatja, hogyan konfigurálhatja a magánhálózati IP-címek a virtuális gépek (klasszikus) használatával az Azure klasszikus parancssori felület (CLI).
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: genli
ms.openlocfilehash: 9deaf4b1d80ad4e55e7c971998e8b1f5ea562257
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196587"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-the-azure-classic-cli"></a>A klasszikus Azure CLI használatával virtuális gépek (klasszikus) magánhálózati IP-címek konfigurálása

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. Emellett [kezelése a Resource Manager-alapú üzemi modellben statikus magánhálózati IP-cím](virtual-networks-static-private-ip-arm-cli.md).

A minta az Azure klasszikus parancssori felület parancsai olvashat a már létrehozott egy egyszerű környezetben várható. Ha azt szeretné, akkor jelennek meg ebben a dokumentumban a parancsok futtatásához először hozhat létre a leírt tesztkörnyezet [hozzon létre egy vnetet](virtual-networks-create-vnet-classic-cli.md).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Statikus magánhálózati IP-cím megadása a virtuális gép létrehozásakor
Nevű új virtuális gép létrehozása *DNS01* új felhőalapú szolgáltatás nevű *TestService* a fenti forgatókönyv alapján, kövesse az alábbi lépéseket:

1. Ha még sosem használta az Azure CLI-t, akkor tekintse meg [Install and Configure the Azure CLI](/cli/azure/install-cli-version-1.0) (Az Azure CLI telepítése és konfigurálása) részt, és kövesse az utasításokat addig a pontig, ahol ki kell választania az Azure-fiókot és -előfizetést.
2. Futtassa a **azure-szolgáltatás létrehozása** parancs használatával a felhőalapú szolgáltatás létrehozása.
   
        azure service create TestService --location uscentral
   
    Várt kimenet:
   
        info:    Executing command service create
        info:    Creating cloud service
        data:    Cloud service name TestService
        info:    service create command OK
3. Futtassa a **azure virtuális gép létrehozása** parancsot a virtuális gép létrehozásához. Figyelje meg, hogy egy statikus magánhálózati IP-cím értékét. A kimenet után látható lista ismerteti a használt paramétereket.
   
        azure vm create -l centralus -n DNS01 -w TestVNet -S "192.168.1.101" TestService bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2 adminuser AdminP@ssw0rd
   
    Várt kimenet:
   
        info:    Executing command vm create
        warn:    --vm-size has not been specified. Defaulting to "Small".
        info:    Looking up image bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2
        info:    Looking up virtual network
        info:    Looking up cloud service
        warn:    --location option will be ignored
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Retrieving storage accounts
        info:    Creating VM
        info:    OK
        info:    vm create command OK
   
   * **-l (vagy --location)** . Az Azure régió, ahol a virtuális gép létrejön. A mi esetünkben *centralus*.
   * **-n (vagy--vm-név)** . Neve a virtuális gép létrehozása.
   * **-w (vagy--virtuális hálózat neve)** . A VNet neve, ahol a virtuális gép létrejön. 
   * **-S (vagy--statikus ip-)** . Statikus magánhálózati IP-címet a virtuális gép számára.
   * **TestService**. A felhőszolgáltatás, ahol létrejön a virtuális gép neve.
   * **bd507d3a70934695bc2128e3e5a255ba__RightImage – Windows – 2012 R2-x64-v14.2**. A virtuális gép létrehozásához használt lemezkép.
   * **adminuser**. Helyi rendszergazda a Windows virtuális gép számára.
   * <strong>AdminP@ssw0rd</strong>. A Windows virtuális gép helyi rendszergazdai jelszót.

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Hogyan kérheti le a statikus magánhálózati IP-címadatok egy virtuális géphez
Az a fenti szkript létrehozni a virtuális gép statikus privát IP-címadatok megtekintéséhez futtassa a következő Azure CLI-parancsot, és tekintse meg az értékét *hálózati StaticIP*:

    azure vm static-ip show DNS01

Várt kimenet:

    info:    Executing command vm static-ip show
    info:    Getting virtual machines
    data:    Network StaticIP "192.168.1.101"
    info:    vm static-ip show command OK

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Virtuális gép statikus privát IP-cím eltávolítása
Statikus magánhálózati IP-cím eltávolítása adja hozzá a fenti szkript a virtuális gép futtatása az Azure CLI-parancsot:

    azure vm static-ip remove DNS01

Várt kimenet:

    info:    Executing command vm static-ip remove
    info:    Getting virtual machines
    info:    Reading network configuration
    info:    Updating network configuration
    info:    vm static-ip remove command OK

## <a name="how-to-add-a-static-private-ip-to-an-existing-vm"></a>Meglévő virtuális géphez statikus magánhálózati IP-cím hozzáadása
Ha a fenti szkript használatával létrehozott virtuális géphez statikus magánhálózati IP-címet szeretne hozzáadni, futtassa a következő parancsot:

    azure vm static-ip set DNS01 192.168.1.101

Várt kimenet:

    info:    Executing command vm static-ip set
    info:    Getting virtual machines
    info:    Looking up virtual network
    info:    Reading network configuration
    info:    Updating network configuration
    info:    vm static-ip set command OK

## <a name="set-ip-addresses-within-the-operating-system"></a>Állítsa be az operációs rendszer belüli IP-címek

Javasoljuk, hogy nem statikusan rendel a privát IP-cím az Azure virtuális gépen belül a virtuális gépek, az operációs rendszer rendelt, kivéve, ha szükséges. Ha manuálisan állítsa be a magánhálózati IP-címet az operációs rendszerből, győződjön meg arról, hogy-e az Azure virtuális Géphez rendelt magánhálózati IP-címet a címmel, vagy a virtuális gép is megszakad a kapcsolat. A virtuális gép operációs rendszerén belül ne rendeljen hozzá manuálisan egy Azure-beli virtuális géphez hozzárendelt nyilvános IP-címet.

## <a name="next-steps"></a>Következő lépések
* Ismerje meg [lefoglalt nyilvános IP-cím](virtual-networks-reserved-public-ip.md) címeket.
* Ismerje meg [példányszintű nyilvános IP (ILPIP)](virtual-networks-instance-level-public-ip.md) címeket.
* Tekintse át a [fenntartott IP-REST API-k](https://msdn.microsoft.com/library/azure/dn722420.aspx).