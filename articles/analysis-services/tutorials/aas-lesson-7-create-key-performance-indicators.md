---
title: 'Azure Analysis Services oktatóanyag – 7. lecke: Fő teljesítménymutatók létrehozása | Microsoft Docs'
description: Ismerteti a Fő teljesítménymutatók létrehozását az Azure Analysis Services oktatóprojektjében.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 1e7fc5cd684610a5d96b5986f5c169741055c9b8
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49426818"
---
# <a name="create-key-performance-indicators"></a>Fő teljesítménymutatók létrehozása

Ebben a leckében fő teljesítménymutatókat (KPI-k) hozhat létre. A KPI-k az *Alapmérték* által meghatározott érték teljesítményének és szintén egy mérték, vagy egy abszolútérték által meghatározott *Célérték* összehasonlítására használhatók. A jelentéskészítő ügyfélalkalmazásokban a KPI-k segítségével az üzleti szakemberek gyorsan és egyszerűen ismerhetik meg az üzleti sikerek összegzését vagy állapíthatják meg a tendenciákat. További tudnivalókért lásd: [KPI-k](https://docs.microsoft.com/sql/analysis-services/tabular-models/kpis-ssas-tabular)
  
A lecke elvégzésének várható időtartama: **15 perc**.  
  
## <a name="prerequisites"></a>Előfeltételek  
Ez a témakör a táblázatos modellezéssel foglalkozó oktatóanyag részét képezi, amelyet a megfelelő sorrendben kell elvégezni. Az ebben a leckében található feladatok végrehajtása előtt be kell fejeznie az előző leckét: [6. lecke: Mértékek létrehozása](../tutorials/aas-lesson-6-create-measures.md).   
  
## <a name="create-key-performance-indicators"></a>Fő teljesítménymutatók létrehozása  
  
#### <a name="to-create-an-internetcurrentquartersalesperformance-kpi"></a>InternetCurrentQuarterSalesPerformance KPI létrehozása  
  
1.  A modelltervezőben kattintson a **FactInternetSales** táblára.  
  
2.  A mértékrácson kattintson egy üres cellára.  
  
3.  A táblázat feletti képletsávban gépelje be a következő képletet: 
 
    ```  
    InternetCurrentQuarterSalesPerformance :=DIVIDE([InternetCurrentQuarterSales]/[InternetPreviousQuarterSalesProportionToQTD],BLANK())  
    ```

    Ez a mérték a KPI Alapmértékeként szolgál.  
  
4.  A mértékrácson kattintson jobb gombbal az **InternetCurrentQuarterSalesPerformance** > **KPI létrehozása** lehetőségére.   
  
5.  A Fő teljesítménymutató (KPI) párbeszédpanel **Cél** részénél válassza az **Abszolútérték** lehetőséget, és írja be a következőt: **1.1**.  
  
7.  A bal (alsó) csúszkamezőbe írja be az **1** értéket, majd a jobb (felső) csúszkamezőbe írja be az **1,07** értéket.  
  
8.  Az **Ikonstílus kiválasztása** részben válassza a gyémánt (vörös), háromszög (sárga), kör (zöld) ikontípust.
  
    ![aas-lesson7-kpi](../tutorials/media/aas-lesson7-kpi.png)
    
    > [!TIP]  
    > Tekintse meg a bővíthető **Leírások** címkét az elérhető ikonstílusok alatt. Használja a különböző KPI-elemek leírásait, hogy könnyebben azonosíthatók legyenek az ügyfélalkalmazásokban.  
  
9. Kattintson az **OK** gombra a KPI befejezéséhez.  
  
    A mértékrácson figyelje meg az **InternetCurrentQuarterSalesPerformance** mérték melletti ikont. Ez az ikon azt mutatja, hogy ez a mérték a KPI alapértékeként szolgál.  
  
#### <a name="to-create-an-internetcurrentquartermarginperformance-kpi"></a>InternetCurrentQuarterMarginPerformance KPI létrehozása  
  
1.  A **FactInternetSales** tábla mértékrácsán kattintson egy üres cellára.  
  
2.  A táblázat feletti képletsávban gépelje be a következő képletet:  

    ```
    InternetCurrentQuarterMarginPerformance :=IF([InternetPreviousQuarterMarginProportionToQTD]<>0,([InternetCurrentQuarterMargin]-[InternetPreviousQuarterMarginProportionToQTD])/[InternetPreviousQuarterMarginProportionToQTD],BLANK())  
    ```
 
3.  Kattintson a jobb gombbal az **InternetCurrentQuarterMarginPerformance** > **KPI létrehozása** lehetőségére.  
  
4.  A Fő teljesítménymutató (KPI) párbeszédpanel **Cél** részénél válassza az **Abszolútérték** lehetőséget, és írja be a következőt: **1,25**.   
  
5.  A bal (alsó) csúszkamezőben húzza addig a csúszkát, amíg a mező **0,8** értéket nem mutat, majd a jobb (felső) csúszkamezőben is tegye ugyanezt, amíg a mező az **1,03** értéket nem mutatja.  
  
6.  Az **Ikonstílus kiválasztása** részben válassza a gyémánt (vörös), háromszög (sárga), kör (zöld) ikontípust, majd kattintson az **OK** gombra.  
  
## <a name="whats-next"></a>A következő lépések
[8. lecke: Perspektívák létrehozása](../tutorials/aas-lesson-8-create-perspectives.md).
  
  
