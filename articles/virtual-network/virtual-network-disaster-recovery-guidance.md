---
title: Virtuális hálózat üzletmenet-folytonossága | Microsoft Docs
description: Ismerje meg, mi a teendő az Azure-beli virtuális hálózatokat érintő Azure-szolgáltatásokkal kapcsolatos fennakadások esetén.
services: virtual-network
documentationcenter: ''
author: NarayanAnnamalai
manager: jefco
editor: ''
ms.assetid: ad260ab9-d873-43b3-8896-f9a1db9858a5
ms.service: virtual-network
ms.workload: virtual-network
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2016
ms.author: narayan
ms.reviewer: aglick
ms.openlocfilehash: 3f91d24bff0bec540ff0e7964f21c2f47c03638c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "67876174"
---
# <a name="virtual-network--business-continuity"></a>Virtual Network – üzletmenet folytonossága

## <a name="overview"></a>Áttekintés
A Virtual Network (VNet) a hálózat logikai ábrázolása a felhőben. Lehetővé teszi saját magánhálózati IP-címtartomány megadását és a hálózat alhálózatokra való szegmentálását. A virtuális hálózatok megbízhatósági kapcsolati határként szolgál a számítási erőforrások, például az Azure Virtual Machines és Cloud Services (webes/feldolgozói szerepkörök) üzemeltetéséhez. A VNet lehetővé teszi a közvetlen magánhálózati IP-kommunikációt a benne tárolt erőforrások között. Egy virtuális hálózatot VPN Gateway vagy ExpressRoute keresztül kapcsolhat egy helyszíni hálózathoz.

Egy VNet egy régió hatókörén belül jön létre. A virtuális hálózatok két különböző régióban (például az USA keleti régiójában és az USA nyugati régiójában) is *létrehozhatja* , de mivel ugyanaz a címtartomány, nem csatlakoztatható együtt. 

## <a name="business-continuity"></a>Üzleti folytonosság

Számos különböző módon lehet megszakítani az alkalmazást. Egy régiót egy természeti katasztrófa vagy egy részleges katasztrófa miatt teljesen le lehet vágni, mert több eszköz vagy szolgáltatás meghibásodik. A VNet szolgáltatásra gyakorolt hatás különbözik az egyes helyzetekben.

**K: Ha a teljes régió esetében áramkimaradás történik, mi a teendő? Ha például egy régió teljesen ki van vágva egy természeti katasztrófa miatt? Mi történik a régióban üzemeltetett virtuális hálózatokkal?**

A: a virtuális hálózat és az érintett régióban lévő erőforrások a szolgáltatás megszakadásának ideje alatt elérhetetlenné válnak.

![Egyszerű Virtual Network diagram](./media/virtual-network-disaster-recovery-guidance/vnet.png)

**K: Mit tehetek, ha újra létrehozom ugyanazt a virtuális hálózatot egy másik régióban?**

A: a virtuális hálózatok meglehetősen egyszerű erőforrások. Az Azure API-k meghívásával olyan VNet hozhat létre, amely egy másik régióban található azonos címterületet használ. Ha újra létre szeretné hozni az érintett régióban található környezetet, az API-hívások újratelepítik a Cloud Services webes és feldolgozói szerepköröket, valamint a virtuális gépeket. Ha helyszíni kapcsolattal rendelkezik, például hibrid telepítés esetén, telepítenie kell egy új VPN Gatewayt, és csatlakoznia kell a helyszíni hálózathoz.

Virtuális hálózat létrehozásához tekintse meg [a virtuális hálózat létrehozása](manage-virtual-network.md#create-a-virtual-network)című témakört.

**K: az adott régióban található VNet replikája az idő előtt újra létrejön egy másik régióban?**

Válasz: igen, létrehozhat két virtuális hálózatok ugyanazzal a magánhálózati IP-címmel és erőforrásokkal két különböző régióban, az idő előtt. Ha az internetre irányuló szolgáltatásokat a VNet üzemelteti, beállíthatja, hogy a rendszer az aktív régió felé irányítsa a forgalmat Traffic Manager. Azonban nem csatlakoztatható két virtuális hálózatok ugyanazzal a Címterület-kapcsolattal a helyszíni hálózathoz, mivel az útválasztási problémákhoz vezethet. Egy adott régióban a VNet katasztrófájának és elvesztésének időpontjában az elérhető régió többi VNet összekapcsolhatja a helyszíni hálózattal egyező címterület használatával.

Virtuális hálózat létrehozásához tekintse meg [a virtuális hálózat létrehozása](manage-virtual-network.md#create-a-virtual-network)című témakört.

