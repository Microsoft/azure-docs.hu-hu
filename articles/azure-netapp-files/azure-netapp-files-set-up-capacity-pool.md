---
title: Kapacitáskészlet beállítása az Azure NetApp Files számára | Microsoft Docs
description: A cikk azt mutatja be, hogyan állíthat be kapacitáskészleteket, amelyekben aztán majd köteteket hozhat létre.
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
ms.date: 09/22/2020
ms.author: b-juche
ms.openlocfilehash: 2b52ad50854092cddd7b9e79cbeebd4a83017081
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91325410"
---
# <a name="set-up-a-capacity-pool"></a>Kapacitáskészlet beállítása

A beállított kapacitáskészletekben hozhat létre majd köteteket.  

## <a name="before-you-begin"></a>Előkészületek 

Már rendelkeznie kell egy létrehozott NetApp-fiókkal.   

[NetApp-fiók létrehozása](azure-netapp-files-create-netapp-account.md)

## <a name="steps"></a>Lépések 

1. Lépjen a NetApp-fiókhoz tartozó felügyelet panelre, majd a navigációs ablaktáblán kattintson a **Kapacitási készletek** elemre.  
    
    ![Navigáljon a kapacitási készlethez](../media/azure-netapp-files/azure-netapp-files-navigate-to-capacity-pool.png)

2. Új kapacitáskészlet létrehozásához kattintson a **+ Készletek hozzáadása** elemre.   
    Ekkor megjelenik az Új kapacitáskészlet ablak.

3. Adja meg az alábbi, az új kapacitáskészletre vonatkozó információkat:  
   * **Név**  
     Adja meg a kapacitáskészlet nevét.  
     A kapacitáskészlet nevének a NetApp-fiókban egyedinek kell lennie.

   * **Szolgáltatási szint**   
     Ez a mező a kapacitáskészlet célteljesítményét mutatja.  
     A kapacitási készlet szolgáltatási szintjének megadása: [**Ultra**](azure-netapp-files-service-levels.md#Ultra), [**Premium**](azure-netapp-files-service-levels.md#Premium)vagy [**standard**](azure-netapp-files-service-levels.md#Standard).

    * **Méret**     
     Adja meg a megvásárolni kívánt kapacitáskészlet-méretet.        
     A kapacitáskészletek minimális mérete 4 TiB. A létrehozott kapacitáskészlet méretének a 4 TiB egy szorzatának kell lennie.   

   * **QoS**   
     Adja meg, hogy a kapacitási készletnek a **manuális** vagy az **automatikus** QoS-típust kell használnia.  

     A QoS-típusok megismeréséhez tekintse meg a [tárolási hierarchia](azure-netapp-files-understand-storage-hierarchy.md) és a [teljesítménnyel kapcsolatos szempontokat](azure-netapp-files-performance-considerations.md) .  

     > [!IMPORTANT] 
     > A **QoS** -típus **manuálisra** állítása állandó. A manuális QoS-kapacitás készlete nem alakítható át automatikus QoS használatára. Az automatikus QoS-kapacitás készletét azonban manuális QoS használatára is alakíthatja. Lásd: [Kapacitási készlet módosítása kézi QoS használatára](manage-manual-qos-capacity-pool.md#change-to-qos).   
     > A kapacitáshoz tartozó készlet kézi QoS-típusának használatához regisztráció szükséges. Lásd: [manuális QoS-kapacitás készletének kezelése](manage-manual-qos-capacity-pool.md#register-the-feature). 

    ![Új kapacitáskészlet](../media/azure-netapp-files/azure-netapp-files-new-capacity-pool.png)

4. Kattintson a **Létrehozás** lehetőségre.

## <a name="next-steps"></a>Következő lépések 

- [Tárolási hierarchia](azure-netapp-files-understand-storage-hierarchy.md) 
- [Az Azure NetApp Files szolgáltatásszintjei](azure-netapp-files-service-levels.md)
- [Azure NetApp Files díjszabási oldala](https://azure.microsoft.com/pricing/details/storage/netapp/)
- [Manuális QoS-kapacitáskészlet kezelése](manage-manual-qos-capacity-pool.md)
- [Alhálózat delegálása az Azure NetApp Fileshoz](azure-netapp-files-delegate-subnet.md)
