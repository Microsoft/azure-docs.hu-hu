---
title: Azure NetApp Files manuális QoS-kapacitásának készlete | Microsoft Docs
description: Bevezetőt biztosít a QoS-kapacitás készletéhez, és további információkra mutató hivatkozásokat tartalmaz.
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
ms.date: 02/04/2021
ms.author: b-juche
ms.openlocfilehash: 13acee8b21adf946192544afcea17b4a8d9b9ec9
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99581125"
---
# <a name="manual-qos-capacity-pool"></a>Manuális QoS-kapacitáskészlet

Ez a cikk bevezetést nyújt a kézi szolgáltatásminőség (QoS) kapacitás-készlet funkcióival kapcsolatban.

## <a name="how-manual-qos-differs-from-auto-qos"></a>Hogyan különbözik az automatikus QoS a manuális QoS-től

A [QoS-típus](azure-netapp-files-understand-storage-hierarchy.md#qos_types) a kapacitási készlet egyik attribútuma. Azure NetApp Files két QoS-típust biztosít a kapacitás-készletek számára – automatikus (alapértelmezett) és manuális.  

A *manuális* QoS-kapacitás készletében a kötetek kapacitását és átviteli sebességét egymástól függetlenül lehet hozzárendelni. A minimális és maximális átviteli sebesség tekintetében lásd: [Azure NetApp Files erőforrás-korlátai](azure-netapp-files-resource-limits.md#resource-limits). A manuális QoS-kapacitási készlettel létrehozott kötetek teljes átviteli sebességét a készlet teljes átviteli sebessége korlátozza. Ezt a készlet méretének és a szolgáltatási szintű átviteli sebességnek a kombinációja határozza meg. 

Egy *automatikus* QoS-kapacitási készletben a rendszer automatikusan hozzárendeli az átviteli sebességet a készletben lévő kötetekhez, a kötetekhez rendelt mérethez viszonyítva.  

A QoS-típusokkal kapcsolatos megfontolásokért tekintse meg a [Azure NetApp Files tárolási hierarchiáját](azure-netapp-files-understand-storage-hierarchy.md) , valamint a [Azure NetApp Files teljesítményével kapcsolatos szempontokat](azure-netapp-files-performance-considerations.md) .

## <a name="example-of-using-manual-qos"></a>Példa kézi QoS használatára

Ha manuális QoS-kapacitási készletet használ, például egy SAP HANA rendszer, egy Oracle-adatbázis vagy más, több kötetet igénylő számítási feladat, akkor a kapacitási készlet használható az alkalmazás köteteinek létrehozásához.  Az egyes kötetek egyedi méretet és átviteli sebességet biztosítanak az alkalmazásra vonatkozó követelmények teljesítése érdekében.  Az előnyökkel kapcsolatos további információkért lásd: [átviteli sebességre vonatkozó példa kötetek esetén a manuális QoS-kapacitási készletben](azure-netapp-files-service-levels.md#throughput-limit-examples-of-volumes-in-a-manual-qos-capacity-pool) .  

## <a name="how-to-specify-the-manual-qos-type"></a>A manuális QoS-típus megadása

A [kapacitás-készlet létrehozásakor](azure-netapp-files-set-up-capacity-pool.md)megadhatja, hogy a kapacitási készlet a kézi QoS-típust használja.  [Meglévő kapacitási készletet is módosíthat](manage-manual-qos-capacity-pool.md#change-to-qos) a kézi QoS-típus használatára. 

A kapacitás típusának manuális QoS-re való beállítása állandó változás. A manuális QoS-típus kapacitása eszköz nem alakítható át automatikus QoS-kapacitási készletre. 

A manuális QoS-típus használatához [regisztrálnia kell a szolgáltatást](manage-manual-qos-capacity-pool.md#register-the-feature).  

## <a name="next-steps"></a>Következő lépések

* [Manuális QoS-kapacitáskészlet kezelése](manage-manual-qos-capacity-pool.md)
* [Kapacitáskészlet beállítása](azure-netapp-files-set-up-capacity-pool.md)
* [Tárolási hierarchia](azure-netapp-files-understand-storage-hierarchy.md) 
* [Az Azure NetApp Files szolgáltatásszintjei](azure-netapp-files-service-levels.md)
* [Az Azure NetApp Files teljesítményével kapcsolatos szempontok](azure-netapp-files-performance-considerations.md)
* [Az Azure NetApp Files költségmodellje](azure-netapp-files-cost-model.md)
* [Az Azure NetApp Files erőforráskorlátai](azure-netapp-files-resource-limits.md)
* [NFS-kötet létrehozása](azure-netapp-files-create-volumes.md)
* [SMB-kötet létrehozása](azure-netapp-files-create-volumes-smb.md)
* [Kettős protokollú kötet létrehozása](create-volumes-dual-protocol.md)
* [Az Azure NetApp Files metrikái](azure-netapp-files-metrics.md)
* [Kapacitáskészletekkel kapcsolatos hibák elhárítása](troubleshoot-capacity-pools.md)
