---
title: NetApp-fiók létrehozása Azure NetApp Files-hozzáféréshez | Microsoft Docs
description: A cikk az Azure NetApp Files elérésének és a NetApp-fiókok létrehozásának módját ismerteti kapacitáskészletek beállításához és kötetek létrehozásához.
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
ms.date: 03/28/2018
ms.author: b-juche
ms.openlocfilehash: e5e0c43aa8c4fbf0db89065e0fab93ee174a4ede
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96006593"
---
# <a name="create-a-netapp-account"></a>NetApp-fiók létrehozása
A NetApp-fiókok létrehozásával lehetősége lesz kapacitáskészleteket beállítani, majd azt követően köteteket létrehozni. NetApp-fiókokat az Azure NetApp Files panelen hozhat létre.

## <a name="before-you-begin"></a>Előkészületek
Meg kell kapnia a Azure NetApp Files csapatának e-mail-címét, amely megerősíti, hogy hozzáférést kapott a szolgáltatáshoz. Lásd: [várólistára vonatkozó kérelem küldése a szolgáltatás eléréséhez](azure-netapp-files-register.md#waitlist).

A NetApp erőforrás-szolgáltató használatához regisztrálnia kell az előfizetését is. Lásd: [a NetApp erőforrás-szolgáltató regisztrálása](azure-netapp-files-register.md#resource-provider).

## <a name="steps"></a>Lépések 

1. Jelentkezzen be az Azure Portalra. 
2. Az Azure NetApp Files panelt a következő módszerek egyikével érheti el:  
   * Keressen rá az **Azure NetApp Files** kifejezésre az Azure Portal keresőmezőjében.  
   * Kattintson a navigációs menü **Minden szolgáltatás** elemére, majd szűrjön az Azure NetApp Files kifejezésre.  

   Az Azure NetApp Filest „kedvencnek is jelölheti” a mellette lévő csillag ikonra kattintva. 

3. Új NetApp-fiók létrehozásához kattintson a **+ Hozzáadás** gombra.  
   Ekkor megjelenik az Új NetApp-fiók ablaka.  

4. Adja meg az alábbi információkat a NetApp-fiókra vonatkozóan: 
   * **Fióknév**  
     Adja meg az előfizetés egyedi nevét.
   * **Előfizetés**  
     Válasszon ki egyet a meglévő előfizetések közül.
   * **Erőforráscsoport**   
     Használjon egy meglévő erőforráscsoportot, vagy hozzon létre egy újat.
   * **Hely**  
     Válassza ki azt az Azure-régiót, ahol a fiókot és annak alárendelt erőforrásait létre szeretné hozni.  

     ![Új NetApp-fiók](../media/azure-netapp-files/azure-netapp-files-new-netapp-account.png)


5. Kattintson a **Létrehozás** lehetőségre.     
   A létrehozott NetApp-fiók megjelenik az Azure NetApp Files panelen. 

> [!NOTE] 
> Ha nem kapott hozzáférést a Azure NetApp Files szolgáltatáshoz, a következő hibaüzenet jelenik meg az első NetApp-fiók létrehozásakor:  
>
> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"NotFound","message":"{\r\n \"error\": {\r\n \"code\": \"InvalidResourceType\",\r\n \"message\": \"The resource type could not be found in the namespace 'Microsoft.NetApp' for api version '2017-08-15'.\"\r\n }\r\n}"}]}`

## <a name="next-steps"></a>Következő lépések  

[Kapacitáskészlet beállítása](azure-netapp-files-set-up-capacity-pool.md)

