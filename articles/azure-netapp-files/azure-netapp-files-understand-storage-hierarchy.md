---
title: Azure NetApp Files tárolási hierarchiája | Microsoft Docs
description: A cikk a tárhely-hierarchiát ismerteti, beleértve az Azure NetApp Files-fiókokat, -kapacitáskészleteket és -köteteket.
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
ms.topic: overview
ms.date: 09/22/2020
ms.author: b-juche
ms.openlocfilehash: 435d74e771a9d887c87c9d10e6b525ac77cf97e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91278328"
---
# <a name="storage-hierarchy-of-azure-netapp-files"></a>Az Azure NetApp Files tárhely-hierarchiája

Mielőtt létrehozna egy kötetet az Azure NetApp Filesban, meg kell vásárolnia és be kell állítania egy készletet a kiosztott kapacitáshoz.  Kapacitáskészlet beállításához rendelkeznie kell NetApp-fiókkal. Az Azure NetApp Files tárhely-hierarchiájának megismerése segítségére lehet az Azure NetApp Files-erőforrások beállítása és kezelése során.

> [!IMPORTANT] 
> A Azure NetApp Files jelenleg nem támogatja az előfizetések közötti erőforrás-áttelepítést.

## <a name="netapp-accounts"></a><a name="azure_netapp_files_account"></a>NetApp-fiókok

- A NetApp-fiókok a kapacitáskészlet-tagok felügyeleti csoportosítására szolgálnak.  
- A NetApp-fiók nem egyezik meg az általános Azure Storage-fiókkal. 
- A NetApp-fiók regionális hatókörrel rendelkezik.   
- Több NetApp-fiókkal is rendelkezhet egy régióban, de minden NetApp-fiók egyetlen régióhoz van társítva.

## <a name="capacity-pools"></a><a name="capacity_pools"></a>Kapacitáskészletek

A kapacitás-készletek működésének megismerése segít kiválasztani a tárolási igényeknek megfelelő kapacitás-készleteket. 

### <a name="general-rules-of-capacity-pools"></a>A kapacitási készletek általános szabályai

- A kapacitáskészletek mérése a kiosztott kapacitás alapján történik.   
    További információért lásd: [QoS-típusok](#qos_types) .  
- A kapacitást a megvásárolt rögzített SKU-nak (például egy 4 TiB-kapacitásnak) kell kiépíteni.
- A kapacitáskészletek csak egy szolgáltatásszinttel rendelkezhetnek.  
- Mindegyik kapacitási készlet csak egy NetApp-fiókhoz tartozhat. Egy NetApp-fiókon belül azonban több kapacitású készlet is rendelkezhet.  
- A kapacitáskészletek nem mozgathatók a NetApp-fiókok között.   
  Az alábbi, [tárhely-hierarchiát ábrázoló fogalmi diagramon](#conceptual_diagram_of_storage_hierarchy) például az 1. kapacitáskészlet nem helyezhető át az USA keleti régiójában lévő NetApp-fiókból az USA 2. nyugati régiójának NetApp-fiókjába.  
- A kapacitási készlet nem törölhető, amíg a kapacitás-készletben lévő összes kötetet nem törölték.

### <a name="quality-of-service-qos-types-for-capacity-pools"></a><a name="qos_types"></a>Szolgáltatásminőség (QoS) típusai a kapacitási készletekhez

A QoS-típus a kapacitási készlet egyik attribútuma. Azure NetApp Files két QoS-típust biztosít a kapacitás-készletek számára. 

- *Automatikus (vagy automatikus)* QoS-típus  

    A kapacitás-készlet létrehozásakor az alapértelmezett QoS-típus automatikus.

    Egy automatikus QoS-kapacitási készletben a rendszer automatikusan hozzárendeli az átviteli sebességet a készletben lévő kötetekhez, a kötetekhez rendelt mérethez viszonyítva. 

    A kötethez lefoglalt maximális átviteli sebesség a kapacitás-készlet szolgáltatási szintjétől és a kötet méretétől függ. Tekintse át a [Azure NetApp Files a szolgáltatási szinteket](azure-netapp-files-service-levels.md) , például a számítást.

- <a name="manual_qos_type"></a>*Manuális* QoS-típus  

     > [!IMPORTANT] 
     > A kapacitáshoz tartozó készlet kézi QoS-típusának használatához regisztráció szükséges.  Lásd: [manuális QoS-kapacitás készletének kezelése](manage-manual-qos-capacity-pool.md).  

    Lehetősége van arra, hogy a kézi QoS-típust használja a kapacitási készlethez.

    A manuális QoS-kapacitás készletében a kötetek kapacitását és átviteli sebességét egymástól függetlenül lehet hozzárendelni. A manuális QoS-kapacitási készlettel létrehozott kötetek teljes átviteli sebességét a készlet teljes átviteli sebessége korlátozza.  Ezt a készlet méretének és a szolgáltatási szintű átviteli sebességnek a kombinációja határozza meg. 

    Az ultra Service szinttel rendelkező 4 TiB kapacitású készlet például a kötetek számára elérhető teljes átviteli kapacitás 512 MiB/s (4 TiB x 128 MiB/s/TiB).


## <a name="volumes"></a><a name="volumes"></a>Kötetek

- A köteteket a logikai kapacitások fogyasztása méri, és méretezhető. 
- A kötet kapacitásfogyasztása beleszámít a készlet kiosztott kapacitásába.
- A kötetek átviteli sebessége a készlet rendelkezésre álló átviteli sebességével együtt számít. Lásd: [manuális QoS-típus](#manual_qos_type).
- Minden kötet egyetlen készlethez tartozik, de egy készlet több kötetet is tartalmazhat. 

## <a name="conceptual-diagram-of-storage-hierarchy"></a><a name="conceptual_diagram_of_storage_hierarchy"></a>Tárhely-hierarchia fogalmi diagramja 
Az alábbi példák az Azure-előfizetés, a NetApp-fiókok, a kapacitáskészletek és a kötetek közötti kapcsolatokat mutatják be.   

![Tárhely-hierarchia fogalmi diagramja](../media/azure-netapp-files/azure-netapp-files-storage-hierarchy.png)

## <a name="next-steps"></a>Következő lépések

- [Az Azure NetApp Files erőforráskorlátai](azure-netapp-files-resource-limits.md)
- [Regisztrálás az Azure NetApp Filesra](azure-netapp-files-register.md)
- [Az Azure NetApp Files szolgáltatásszintjei](azure-netapp-files-service-levels.md)
- [Az Azure NetApp Files teljesítményével kapcsolatos szempontok](azure-netapp-files-performance-considerations.md)
- [Kapacitáskészlet beállítása](azure-netapp-files-set-up-capacity-pool.md)
- [Manuális QoS-kapacitáskészlet kezelése](manage-manual-qos-capacity-pool.md)
