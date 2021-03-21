---
title: Azure VMware-megoldás CloudSimple – a számítási feladatok DNS-és DHCP-beállítása a privát felhőhöz
description: Ismerteti, hogyan lehet DNS-t és DHCP-t beállítani a CloudSimple saját felhőalapú környezetében futó alkalmazások és munkaterhelések számára
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: cdcb3cd7afa660909fad416ca455c041dc50321e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97896992"
---
# <a name="set-up-dns-and-dhcp-applications-and-workloads-in-your-cloudsimple-private-cloud"></a>DNS-és DHCP-alkalmazások és-munkaterhelések beállítása a saját CloudSimple-felhőben

A privát felhőalapú környezetekben futó alkalmazások és munkaterhelések névfeloldást és DHCP-szolgáltatásokat igényelnek a keresési és IP-címek hozzárendeléséhez.  Ezeknek a szolgáltatásoknak a biztosításához megfelelő DHCP-és DNS-infrastruktúra szükséges.  A virtuális gépeket konfigurálhatja úgy, hogy biztosítsa ezeket a szolgáltatásokat a saját felhőalapú környezetében.  

## <a name="prerequisites"></a>Előfeltételek

* Elosztott porttartomány konfigurált VLAN-val
* A beállítás átirányítása helyszíni vagy internetalapú DNS-kiszolgálókra
* Virtuálisgép-sablon vagy ISO virtuális gép létrehozásához

## <a name="linux-based-dns-server-setup"></a>Linux-alapú DNS-kiszolgáló beállítása

A Linux különböző csomagokat kínál a DNS-kiszolgálók beállításához.  Íme egy [példa a DigitalOcean-ről](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-ubuntu-18-04) , amely egy nyílt forráskódú kötési DNS-kiszolgáló beállítására vonatkozó utasításokat tartalmaz.

## <a name="windows-based-setup"></a>Windows-alapú telepítés

Ezek a Microsoft-témakörök leírják, hogyan állíthat be egy Windows-kiszolgálót DNS-kiszolgálóként és DHCP-kiszolgálóként.

* [Windows Server DNS-kiszolgálóként](/windows-server/networking/dns/dns-top)
* [Windows Server DHCP-kiszolgálóként](/windows-server/networking/technologies/dhcp/dhcp-top)
