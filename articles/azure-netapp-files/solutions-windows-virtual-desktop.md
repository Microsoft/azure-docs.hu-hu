---
title: A Windows rendszerű virtuális asztal használata a Azure NetApp Files használatával | Microsoft Docs
description: Gyakorlati útmutatást és példákat nyújt a Windows rendszerű virtuális asztalok Azure NetApp Files használatával történő üzembe helyezéséhez.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/13/2020
ms.author: b-juche
ms.openlocfilehash: 0cd1f6210fbdb74e3fd511150157dccca3e92dda
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91932464"
---
# <a name="benefits-of-using-azure-netapp-files-with-windows-virtual-desktop"></a>Az Azure NetApp Files és a Windows Virtual Desktop használatának előnyei 

Ez a cikk gyakorlati útmutatást nyújt a Windows rendszerű virtuális asztalok (WVD) Azure NetApp Files használatával történő üzembe helyezéséhez.

Azure NetApp Files egy nagy teljesítményű file Storage-szolgáltatás az Azure-ból. Akár 450 000 IOPS-t és ezredmásodperces késést is biztosíthat, amely képes a Windows rendszerű virtuális asztali környezetek rendkívül nagy méretének támogatására. A sávszélességet beállíthatja, és igény szerint megváltoztathatja a Azure NetApp Files kötetek szolgáltatási szintjét az IO szüneteltetése nélkül, és megtarthatja az adatsíkok elérését. Ez a funkció lehetővé teszi, hogy egyszerűen optimalizálja a WVD üzembe helyezési léptékét a költséghatékonyság érdekében. A kötetek teljesítményének befolyásolása nélkül is létrehozhatja a tárterület-hatékony, időponthoz kötődő pillanatképeket. Ez a funkció lehetővé teszi, hogy visszaállítsa az egyes [FSLogix-tárolókat](../virtual-desktop/store-fslogix-profile.md) a címtárból egy másolatból `~snapshot` , vagy ha azonnal visszaállítja a teljes kötetet a kötet-visszavonási képességen keresztül.  Akár 255 (rotációs) pillanatképekkel a kötetek adatvesztéssel vagy sérüléssel szembeni védelme érdekében a rendszergazdáknak számos lehetősége van visszavonni a munkát.

## <a name="sample-blueprints"></a>Minta tervezetek

Az alábbi példa bemutatja a Windows rendszerű virtuális asztalok integrálását Azure NetApp Filesokkal. A készletezett asztali forgatókönyvekben a felhasználók a készletben lévő legjobb rendelkezésre álló munkamenetre (a [szélesség-első üzemmódra](../virtual-desktop/host-pool-load-balancing.md#breadth-first-load-balancing-method)) irányítják a [több munkamenetet használó virtuális gépeket](../virtual-desktop/windows-10-multisession-faq.md#what-is-windows-10-enterprise-multi-session). Másfelől a személyes asztalok olyan helyzetekben vannak fenntartva, amikor az egyes felhasználók saját virtuális géppel rendelkeznek.

### <a name="pooled-desktop-scenario"></a>Készletezett asztali forgatókönyv

A készletezett forgatókönyv esetén a Windows rendszerű virtuális asztali csapat a következő útmutatást [ajánlja](/windows-server/remote/remote-desktop-services/virtual-machine-recs#multi-session-recommendations) a felhasználók számának a vCPU. Vegye figyelembe, hogy ebben a javaslatban nincs megadva virtuálisgép-méret.

|     Munkaterhelés típusa     |     Világos    |     Közepes    |     Magas szintű    |
|-----------------------|--------------|---------------|--------------|
|     Felhasználók száma vCPU    |     6        |     4         |     2        |


Ezt a javaslatot egy 500 felhasználói LoginVSI-teszt megerősíti, amely körülbelül 62 "tudás/közepes felhasználót" naplóz az egyes D16as_V4 virtuális gépekre. 

Ha például a 62-es felhasználók száma D16as_V4 virtuális gépen, Azure NetApp Files könnyen támogathatja a 60 000 felhasználókat a környezetben. Folyamatban van a D32as_v4 virtuális gép felső korlátjának kiértékelése. Ha a WVD-felhasználó vCPU-javaslata igaz értékkel rendelkezik a D32as_v4 esetében, több mint 120 000 felhasználó fér el az 1 000-es virtuális gépeken, mielőtt üregelő [a 1 000 IP VNet korlátját](./azure-netapp-files-network-topologies.md), ahogy az a következő ábrán látható.  

![Windows rendszerű virtuális asztali készletezett asztali forgatókönyv](../media/azure-netapp-files/solutions-pooled-desktop-scenario.png)   

### <a name="personal-desktop-scenario"></a>Személyes asztali forgatókönyv 

A személyes asztali forgatókönyvekben az alábbi ábra az általános célú építészeti javaslatot mutatja be. A felhasználók egy adott asztali hüvelyre vannak leképezve, és mindegyik Pod mindössze 1 000 virtuális gép alatt található, így a felügyeleti VNet terjedő IP-címek is helyet kapnak. A Azure NetApp Files egyszerűen kezelhet 900 + személyes asztali számítógépeket egymunkamenetes VNet, és a tényleges számú virtuális gép 1 000 mínusz a hub-VNet található felügyeleti gazdagépek száma. Ha több személyes asztalra van szükség, egyszerűen hozzáadhat több hüvelyt (gazdagép-készletet és virtuális hálózatot) az alábbi ábrán látható módon. 

![Windows rendszerű virtuális asztali személyes asztali forgatókönyv](../media/azure-netapp-files/solutions-personal-desktop-scenario.png)  

Ha ehhez hasonló POD-alapú architektúrát hoz létre, a bejelentkezéshez fontos, hogy a felhasználók mindig megtalálják a felhasználói profiljaikat. 

## <a name="next-steps"></a>Következő lépések

- [Megoldásarchitektúrák az Azure NetApp Filesszal](azure-netapp-files-solution-architectures.md)