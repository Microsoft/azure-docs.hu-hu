---
title: Manuális QoS-kapacitási készlet kezelése a Azure NetApp Fileshoz | Microsoft Docs
description: Ismerteti, hogyan lehet felügyelni a kézi QoS-típust használó kapacitási készletet, beleértve a manuális QoS-kapacitás készletének beállítását és a kapacitási készlet módosítását a kézi QoS használatára.
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
ms.topic: how-to
ms.date: 09/28/2020
ms.author: b-juche
ms.openlocfilehash: d2abb66684738e97ade2a2e67309a4e7b53c4734
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/29/2020
ms.locfileid: "91442079"
---
# <a name="manage-a-manual-qos-capacity-pool"></a>Manuális QoS-kapacitáskészlet kezelése

Ez a cikk azt ismerteti, hogyan lehet felügyelni a kézi QoS-típust használó kapacitási készletet.  

A QoS-típusokkal kapcsolatos megfontolások megismeréséhez tekintse meg a [Azure NetApp Files tárolási hierarchiáját](azure-netapp-files-understand-storage-hierarchy.md) , valamint a [Azure NetApp Files teljesítményével kapcsolatos szempontokat](azure-netapp-files-performance-considerations.md) .  

## <a name="register-the-feature"></a>A szolgáltatás regisztrálása
A manuális QoS-típus funkció jelenleg előzetes verzióban érhető el. Ha első alkalommal használja ezt a szolgáltatást, először regisztrálnia kell a szolgáltatást.
  
1.  Regisztrálja a szolgáltatást:

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFFlexPool
    ```

2. A szolgáltatás regisztrálási állapotának ellenõrzése: 

    > [!NOTE]
    > A **RegistrationState** a `Registering` módosítás előtt legfeljebb 60 percig lehet `Registered` . A folytatás előtt várjon, amíg az állapot **regisztrálva** lesz.

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFFlexPool
    ```
Használhatja az [Azure CLI-parancsokat](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest&preserve-view=true) is, `az feature register` és `az feature show` regisztrálhatja a funkciót, és megjelenítheti a regisztrációs állapotot. 

## <a name="set-up-a-new-manual-qos-capacity-pool"></a>Új manuális QoS-kapacitás készletének beállítása 

Új kapacitási készlet létrehozása a kézi QoS-típus használatával:

1. Kövesse a [Kapacitási készlet beállítása](azure-netapp-files-set-up-capacity-pool.md)című témakör lépéseit.  

2. Az új kapacitási készlet ablakban válassza ki a **manuális QoS** -típust.  

## <a name="change-a-capacity-pool-to-use-manual-qos"></a><a name="change-to-qos"></a>Kapacitási készlet módosítása kézi QoS használatára

Módosíthatja az automatikus QoS-típust jelenleg használó kapacitás-készletet a kézi QoS-típus használatához.  

> [!IMPORTANT]
> A kapacitás típusának manuális QoS-re való beállítása állandó változás. A manuális QoS-típus kapacitása eszköz nem alakítható át automatikus QoS-kapacitási készletre. 

1. A NetApp-fiók kezelés paneljén kattintson a **Kapacitási készletek** elemre a meglévő kapacitási készletek megjelenítéséhez.   
 
2.  Kattintson arra a kapacitás-készletre, amelyet kézi QoS használatával szeretne módosítani.

3.  Kattintson a **QoS-típus módosítása**elemre. Ezután állítsa **kézire**az **Új QoS-típust** . Kattintson az **OK** gombra. 

![QoS-típus módosítása](../media/azure-netapp-files/change-qos-type.png)


## <a name="monitor-the-throughput-of-a-manual-qos-capacity-pool"></a>Manuális QoS-kapacitási készlet teljesítményének figyelése  

A metrikák segítségével figyelheti a kötetek olvasási és írási sebességét.  Lásd: [Azure NetApp Files metrikái](azure-netapp-files-metrics.md).  

## <a name="modify-the-allotted-throughput-of-a-manual-qos-volume"></a>Manuális QoS-kötet kiosztott átviteli sebességének módosítása 

Ha egy kötetet kézi QoS-kapacitású készletben tárol, igény szerint módosíthatja a megszabott kötet átviteli sebességét.

1. A **kötetek** lapon válassza ki azt a kötetet, amelynek az átviteli sebességét módosítani szeretné.   

2. Kattintson az **átviteli sebesség módosítása**elemre. Itt adhatja meg a kívánt **átviteli sebességet (MIB/S)** . Kattintson az **OK** gombra. 

    ![QoS átviteli sebességének módosítása](../media/azure-netapp-files/change-qos-throughput.png)

## <a name="next-steps"></a>További lépések  

* [Kapacitáskészlet beállítása](azure-netapp-files-set-up-capacity-pool.md)
* [Az Azure NetApp Files metrikái](azure-netapp-files-metrics.md)
* [Az Azure NetApp Files teljesítményével kapcsolatos szempontok](azure-netapp-files-performance-considerations.md)
* [A kapacitási készlettel kapcsolatos problémák elhárítása](troubleshoot-capacity-pools.md)
* [Az Azure NetApp Files tárhely-hierarchiája](azure-netapp-files-understand-storage-hierarchy.md)
* [Az Azure NetApp Files szolgáltatásszintjei](azure-netapp-files-service-levels.md)
* [Az Azure NetApp Files költségmodellje](azure-netapp-files-cost-model.md)
* [Az Azure NetApp Files erőforráskorlátai](azure-netapp-files-resource-limits.md)
* [NFS-kötet létrehozása](azure-netapp-files-create-volumes.md)
* [SMB-kötet létrehozása](azure-netapp-files-create-volumes-smb.md)
* [Kettős protokollú kötet létrehozása](create-volumes-dual-protocol.md)


