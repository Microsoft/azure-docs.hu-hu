---
title: Symantec ICDx-adatkapcsolatok összekötése az Azure Sentinel szolgáltatással | Microsoft Docs
description: Megtudhatja, hogyan használhatja a Symantec ICDx-összekötőt az összes Symantec biztonsági megoldás naplójának az Azure Sentinel használatával történő egyszerű összekapcsolásához.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 7b32baa23a31342095e542d60745a503b81e89a9
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/10/2021
ms.locfileid: "100092706"
---
# <a name="connect-your-symantec-icdx-appliance"></a>A Symantec ICDx-berendezés összekötése 



A Symantec ICDx Connector segítségével könnyedén csatlakoztathatja az összes Symantec biztonsági megoldás naplóját az Azure Sentinelhez, megtekintheti az irányítópultokat, egyéni riasztásokat hozhat létre, és javíthatja a vizsgálatot. Ez nagyobb betekintést nyújt a szervezet hálózatára, és javítja a biztonsági műveletek képességeit. A Symantec ICDx és az Azure Sentinel közötti integráció a REST API használatát teszi lehetővé.


> [!NOTE]
> Az Azure Sentinel-t futtató munkaterület földrajzi helye tárolja az adatmennyiséget.

## <a name="configure-and-connect-symantec-icdx"></a>A Symantec ICDx konfigurálása és összekapcsolása 

A Symantec ICDx közvetlenül az Azure Sentinelbe integrálhatja és exportálhatja a naplókat.

1. Microsoft Azure Sentinel (Log Analytics) továbbítók hozzáadásához nyissa meg a ICDx felügyeleti konzolt.
2. A ICDx navigációs sávon kattintson a **konfiguráció** elemre. 
3. A **konfiguráció** képernyő felső részén kattintson a **továbbítók** elemre.
4. A **továbbítók** területen Microsoft Azure Sentinel (log Analytics) mellett kattintson a **Hozzáadás** gombra. 
4. A **Microsoft Azure Sentinel (log Analytics)** ablakban kattintson a **speciális megjelenítése** elemre. 
5. A kibontott és a Microsoft Azure Sentinel (Log Analytics) ablak felső részén tegye a következőket:
    -   **Név**: írja be a továbbító nevét, amely nem hosszabb 30 karakternél. Válasszon egy egyedi, értelmes nevet. Ez a név jelenik meg a továbbítók listájában a **konfiguráció** képernyőjén, illetve az **irányítópult képernyőjén az irányítópultokon** . Például: Microsoft Azure Log Analytics Kelet. A mező kitöltése kötelező.
    -   **Leírás**: adja meg a továbbító leírását. Ez a leírás a továbbítók listáján is megjelenik a **konfigurációs** képernyőn. Olyan részleteket is tartalmazhat, mint például a továbbított eseménytípus, valamint az adatok vizsgálatát igénylő csoport.
    -   **Indítási típus**: válassza ki a továbbító konfigurációjának indítási módszerét. A lehetőségek kéziek és automatikusak.<br>Az alapértelmezett érték az automatikus. 
6. Az **események** területen tegye a következőket: 
    - **Forrás**: válasszon ki egy vagy több olyan archívumot, amelyből az eseményeket továbbítani kívánja. Kiválaszthatja az aktív gyűjtő archívumokat (beleértve a közös archívumot is), az árva gyűjtő archívumokat (azaz a törölt gyűjtők archívumait), a ICDx-fogadó archívumokat vagy a rendszer archívumát. <br>Az alapértelmezett érték a közös Archívum.
      > [!NOTE]
      > A ICDx-fogadó archívumok külön névvel vannak felsorolva. 
 
    - **Szűrő**: adjon hozzá egy szűrőt, amely meghatározza a továbbítandó események részhalmazát. Tegye a következők egyikét:
        - Egy szűrési feltétel kiválasztásához kattintson a típus, az attribútum, az operátor és az érték elemre. 
        - A szűrő mezőben tekintse át a szűrési feltételt. Közvetlenül a mezőben szerkesztheti, vagy szükség szerint törölheti is.
        - Kattintson a vagy a vagy a gombra a szűrési feltételhez való hozzáadáshoz.
        - Egy mentett lekérdezés alkalmazásához kattintson a mentett lekérdezések lehetőségre is.
    - **Belefoglalt attribútumok**: írja be a továbbított adatmennyiségbe felvenni kívánt attribútumok vesszővel tagolt listáját. A befoglalt attribútumok elsőbbséget élveznek a kizárt attribútumokkal szemben.
    - **Kizárt attribútumok**: írja be a továbbított adatokból kizárandó attribútumok vesszővel tagolt listáját.
    - **Köteg mérete**: válassza ki a kötegbe küldendő események számát. A lehetőségek a következők: 10, 50, 100, 500 és 1000.<br>Az alapértelmezett érték 100. 
    - **Díjszabási korlát**: válassza ki az események továbbításának sebességét, az események másodpercenkénti számaként kifejezve. A lehetőségek korlátlanok, 500, 1000, 5000, 10000. <br> Az alapértelmezett érték a 5000. 
7. Az **Azure-cél** területen tegye a következőket: 
    - **Munkaterület azonosítója**: illessze be a munkaterület-azonosítót az alábbi listából. A mező kitöltése kötelező.
    - **Elsődleges kulcs**: illessze be az elsődleges kulcsot az alábbi listából. A mező kitöltése kötelező.
    - **Egyéni napló neve**: írja be az egyéni napló nevét a Microsoft Azure Portal log Analytics munkaterületen, amelyre az eseményeket továbbítani fogja. Az alapértelmezett érték a SymantecICDx. A mező kitöltése kötelező.
8. A továbbító konfigurációjának befejezéséhez kattintson a *Mentés* gombra. 
9. A továbbító indításához a **Beállítások** területen kattintson a **továbbiak** , majd a **Start** gombra.
10. Ha a Symantec ICDx eseményeihez a Log Analytics vonatkozó sémát szeretné használni, keresse meg a **SymantecICDx_CL**.


## <a name="validate-connectivity"></a>Kapcsolat ellenőrzése

Akár 20 percet is igénybe vehet, amíg a naplók meg nem kezdődnek a Log Analytics. 



## <a name="next-steps"></a>Következő lépések
Ebből a dokumentumból megtanulta, hogyan csatlakoztatható a Symantec ICDx az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.


