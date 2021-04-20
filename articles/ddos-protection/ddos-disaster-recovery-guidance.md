---
title: Azure DDoS Protection Standard üzletmenet-folytonossági | Microsoft Docs
description: Megtudhatja, mit kell tenni, ha egy Azure-szolgáltatáskimaradás hatással van a Azure DDoS Protection Standardra.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/16/2021
ms.author: yitoh
ms.topic: article
ms.openlocfilehash: 5085f1584a737e75adccf4ec23bf709bede28a4b
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730454"
---
# <a name="azure-ddos-protection-standard--business-continuity"></a>Azure DDoS Protection Standard – üzletmenet-folytonosság

A standard Azure DDoS Protection üzletmenet-folytonosság és vészhelyreállítás lehetővé teszi, hogy a vállalat fennakadások esetén is tovább üzemeljon. Ez a cikk a rendelkezésre állást (régión belüli) és a vészhelyreállítást ismerteti.

## <a name="overview"></a>Áttekintés
Az Azure DDoS Protection Standard megvédi a nyilvános IP-címeket a virtuális hálózatokon. A védelem egyszerűen engedélyezhető bármilyen új vagy meglévő virtuális hálózaton, és nem igényel alkalmazás- vagy erőforrás-módosításokat.

A Virtual Network (VNet) a hálózat logikai reprezentációja a felhőben. A virtuális hálózatok megbízhatósági határként szolgálnak az erőforrások , például a Azure Application Gateway, a Azure Firewall és az Azure Virtual Machines. Egy régió hatókörében jön létre. Két *különböző* régióban (például az USA keleti régiójában és az USA nyugati régiójában) hozhat létre azonos címtérekkel virtuális hálózatokat, de mivel azonos címtérük van, nem csatlakoztathatja őket. 

## <a name="business-continuity"></a>Az üzletmenet folytonossága

Az alkalmazás több különböző módon is megzavarható. Egy régió egy természeti katasztrófa vagy részleges katasztrófa miatt teljesen el is kimaradást okozhat több eszköz vagy szolgáltatás meghibásodása miatt. A védett virtuális hálózatokra gyakorolt hatás ezekben a helyzetekben eltérő.

**K: Ha egy teljes régió kimaradása történik, mit tegyek? Például ha egy régió teljesen le van vágva egy természeti katasztrófa miatt? Mi történik a régióban üzemeltetett virtuális hálózatokkal?**

A: A virtuális hálózat és az érintett régióban lévő erőforrások nem érhetők el a szolgáltatáskimaradás ideje alatt.

![Egyszerű Virtual Network diagram.](../virtual-network/media/virtual-network-disaster-recovery-guidance/vnet.png)

**K: Mit tegyek, ha ugyanazt a virtuális hálózatot újra létrehozhatom egy másik régióban?**

A: A virtuális hálózatok viszonylag egyszerű erőforrások. Az Azure API-k meghívásával létrehozhat egy azonos címtéren található virtuális hálózatokat egy másik régióban. Az érintett régióban található környezet újbóli létrehozása érdekében API-hívásokkal újból üzembe kell állítania az erőforrásokat a virtuális hálózatokon. Ha helyszíni kapcsolattal rendelkezik, például hibrid környezetben, új virtuális hálózatot kell VPN Gateway, és csatlakoznia kell a helyszíni hálózathoz.

Virtuális hálózat létrehozásához [lásd: Virtuális hálózat létrehozása.](../virtual-network/manage-virtual-network.md#create-a-virtual-network)

**K: Előre létre lehet-e hozva egy adott régió virtuális hálózatának replikája egy másik régióban?**

V: Igen, előre létrehozhat két virtuális hálózatokat ugyanazokkal a magánhálózati IP-címtérekkel és erőforrásokkal két különböző régióban. Ha a virtuális hálózatban internetkapcsolattal kapcsolatos szolgáltatásokat biztosít, akkor beállíthatja a Traffic Manager, hogy az aktív régióra irányítsa a forgalmat. Azonban nem csatlakoztathat két azonos címtérű virtuális hálózatot a helyszíni hálózathoz, mivel ez útválasztási problémákat okozna. Az egyik régióban található virtuális hálózat katasztrófa és adatvesztés esetén csatlakoztathatja a rendelkezésre álló régióban található másik virtuális hálózatot a megfelelő címtér és a helyszíni hálózat között.

Virtuális hálózat létrehozásához [lásd: Virtuális hálózat létrehozása.](../virtual-network/manage-virtual-network.md#create-a-virtual-network)

## <a name="next-steps"></a>Következő lépések

- Ismerje meg, [hogyan hozhat létre DDoS Protection-tervet.](manage-ddos-protection.md)