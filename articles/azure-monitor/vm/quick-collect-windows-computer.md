---
title: Adatok gyűjtése hibrid Windows-számítógépről Azure Monitor
description: Ebből a rövid útmutatóból megtudhatja, hogyan helyezheti üzembe a Log Analytics-ügynököt az Azure-on kívül futó Windows rendszerű számítógépeken, és hogyan engedélyezheti az adatgyűjtést Azure Monitor naplók használatával
services: azure-monitor
documentationcenter: azure-monitor
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 08/22/2019
ms.author: bwren
ms.custom: mvc, seo-javascript-october2019
ms.openlocfilehash: 65d60c77280b569d1bff5e4d3acbdf97ead96270
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101733788"
---
# <a name="collect-data-from-a-windows-computer-in-a-hybrid-environment-with-azure-monitor"></a>Adatok gyűjtése egy Windows rendszerű számítógépről hibrid környezetben Azure Monitor

[Azure monitor](../overview.md) adatokat gyűjthet közvetlenül a környezetében található fizikai vagy virtuális Windows rendszerű számítógépekről egy log Analytics munkaterületre, amely részletes elemzést és korrelációt biztosít. A [log Analytics ügynök](../agents/log-analytics-agent.md) telepítése lehetővé teszi, hogy a Azure monitor adatközpontból vagy más felhőalapú környezetből gyűjtsön adatokat. Ez a rövid útmutató bemutatja, hogyan konfigurálhatja a windowsos számítógépekről történő adatgyűjtést néhány egyszerű lépésben. Az Azure Windows rendszerű virtuális gépekkel kapcsolatos további információkért lásd: [adatok gyűjtése az Azure Virtual Machines szolgáltatással kapcsolatban](./quick-collect-azurevm.md).  

A támogatott konfiguráció megismeréséhez tekintse meg a [támogatott operációs rendszerek](../agents/agents-overview.md#supported-operating-systems) és [hálózati tűzfal konfigurálása](../agents/log-analytics-agent.md#network-requirements)című témakört.
 
Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure-portal"></a>Bejelentkezés az Azure portálra

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen. 

## <a name="create-a-workspace"></a>Munkaterület létrehozása

1. A Azure Portal válassza a **minden szolgáltatás** lehetőséget. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza **log Analytics munkaterületek** lehetőséget.

    ![Azure Portal](media/quick-collect-azurevm/azure-portal-log-analytics-workspaces.png)<br>  

2. Válassza a **Létrehozás** lehetőséget, majd válassza ki a kívánt beállításokat a következő elemekhez:

   * Adja meg az új **Log Analytics-munkaterület** nevét, például: *DefaultLAWorkspace*.  
   * A legördülő listából válassza ki azt az **előfizetést**, amelyikhez kapcsolódni szeretne, ha az alapértelmezett kiválasztás nem megfelelő.
   * Az **Erőforráscsoport** beállításnál válasszon ki egy meglévő erőforráscsoportot, amely egy vagy több Azure-beli virtuális gépet tartalmaz.  
   * Válassza ki a **Helyet** a virtuális gépek üzembehelyezési céljaként.  További információkért tekintse meg [a Log Analytics által támogatott régiókat](https://azure.microsoft.com/regions/services/).
   * Ha 2018. április 2. után létrehozott új előfizetésben hoz létre munkaterületet, az automatikusan a *GB-alapú* díjcsomagot használja, és a tarifacsomag kiválasztásának lehetősége nem érhető el.  Ha az április 2. előtt létrehozott meglévő előfizetéshez hoz létre munkaterületet, vagy pedig egy meglévő EA-regisztrációhoz kötött előfizetéshez, válassza ki a kívánt tarifacsomagot.  További információt az elérhető csomagokról [a Log Analytics részletes díjszabásában](https://azure.microsoft.com/pricing/details/log-analytics/) találhat.
  
        ![Log Analytics-erőforrás létrehozása panel](media/quick-collect-azurevm/create-log-analytics-workspace-azure-portal.png) 

3. Miután megadta a szükséges információkat a **log Analytics munkaterület** ablaktáblán, kattintson az **OK gombra**.  

Az **Értesítések** menüpontot kiválasztva nyomon követheti, hogyan ellenőrzi a rendszer az adatokat, és hogyan hozza létre a munkaterületet. 


## <a name="get-the-workspace-id-and-key"></a>A munkaterület AZONOSÍTÓjának és kulcsának beolvasása

Mielőtt telepítené a Windows Log Analytics-ügynökét (más néven a Microsoft monitoring Agent (MMA)), szüksége lesz a munkaterület-AZONOSÍTÓra és a kulcsra a Log Analytics-munkaterülethez. A telepítővarázsló erre az információra van szüksége az ügynök megfelelő konfigurálásához, és gondoskodik arról, hogy az Azure Monitor kommunikáljon.  

1. A Azure Portal bal felső sarkában válassza a **minden szolgáltatás** lehetőséget. A keresőmezőbe írja be a **log Analytics** kifejezést. A beíráskor a rendszer a bemenet alapján szűri a listákat. Válassza **log Analytics munkaterületek** lehetőséget.

2. Log Analytics munkaterületek listájában válassza ki a korábban létrehozott munkaterületet. (Lehet, hogy elnevezte a **DefaultLAWorkspace**.)

3. Válassza a **Speciális beállítások** lehetőséget:

    ![Log Analytics speciális beállítások](media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png)
  
4. Válassza ki a **Csatlakoztatott források**, majd a **Windowsos kiszolgálók** elemet.

5. Másolja az értékeket a **munkaterület-azonosító** és az **elsődleges kulcs** jobb oldalán. Illessze be őket a kedvenc szerkesztőjébe.

## <a name="install-the-agent-for-windows"></a>A Windowshoz készült ügynök telepítése

A következő lépésekkel telepítheti és konfigurálhatja az ügynököt Log Analytics az Azure-ban és a Azure Governmentban. A Microsoft monitoring Agent telepítőprogramjának használatával telepítheti az ügynököt a számítógépre.

1. Az előző lépések végrehajtása után a **Windows-kiszolgálók** lapon válassza ki a letölteni kívánt **Windows-ügynök** verzióját. Válassza ki a Windows operációs rendszer processzor-architektúrájának megfelelő verziót.

2. Futtassa a telepítőt, és telepítse az ügynököt a számítógépre.

3. Az **Üdvözöljük** lapon kattintson a **Tovább** gombra.

4. A **Licencfeltételek** oldalon olvassa el és fogadja el a licencet, majd kattintson az **Elfogadom** gombra.

5. A **Célmappa** lapon fogadja el az alapértelmezett telepítési mappát, vagy adjon meg egy másikat, majd kattintson a **Tovább** gombra.

6. Az **ügynök telepítési beállításai** lapon kapcsolja össze az ügynököt az Azure log Analytics, majd válassza a **tovább** lehetőséget.

7. Az **Azure log Analytics** oldalon hajtsa végre a következő lépéseket:

   1. Illessze be a korábban átmásolt **munkaterület-azonosítót** és a **munkaterület kulcsát (elsődleges kulcs)** . Ha a számítógépnek Azure Government Log Analytics munkaterületre kell jelentenie, válassza az **Azure US government** lehetőséget az **Azure Cloud** listán.  
   2. Ha a számítógépnek egy proxykiszolgálón keresztül kell kommunikálnia a Log Analytics szolgáltatással, kattintson a **Speciális** gombra, majd adja meg a proxykiszolgáló URL-címét és portszámát. Ha a proxykiszolgáló hitelesítést igényel, adja meg a proxykiszolgáló hitelesítéséhez szükséges felhasználónevet és jelszót, majd kattintson a **tovább** gombra.  

8. A konfigurációs beállítások hozzáadása után válassza a **tovább** lehetőséget:

    ![A Microsoft monitoring Agent telepítése](media/quick-collect-windows-computer/log-analytics-mma-setup-laworkspace.png)

9. A **Telepítésre kész** oldalon ellenőrizze a beállításokat, majd kattintson a **Telepítés** elemre.

10. A **Konfigurálás sikeresen befejeződött** lapon válassza a **Befejezés** lehetőséget.

Ha a telepítés és a telepítés befejeződött, a Microsoft monitoring Agent megjelenik a Vezérlőpulton. Áttekintheti a konfigurációt, és ellenőrizheti, hogy az ügynök csatlakoztatva van-e a Log Analytics munkaterülethez. Ha csatlakoztatva van, az **Azure log Analytics** lapon az ügynök megjeleníti ezt az üzenetet: **a Microsoft monitoring Agent sikeresen csatlakozott a Microsoft log Analytics szolgáltatáshoz.**<br><br> ![MMA-kapcsolatok állapota](media/quick-collect-windows-computer/log-analytics-mma-laworkspace-status.png)

## <a name="collect-event-and-performance-data"></a>Esemény- és teljesítményadatok gyűjtése

A Azure Monitor a hosszú távú elemzéshez és jelentéskészítéshez a Windows eseménynaplóból és a teljesítményszámlálókből megadott eseményeket gyűjthet. Ha egy adott feltételt észlel, akkor is végezhet műveleteket. A következő lépésekkel konfigurálhatja az események gyűjtését a Windows eseménynaplóból, illetve (kezdetnek) egyes gyakran használt teljesítményszámlálókból.  

1. A Azure Portal bal alsó sarkában válassza a **További szolgáltatások** lehetőséget. A keresőmezőbe írja be a **log Analytics** kifejezést. A beíráskor a rendszer a bemenet alapján szűri a listákat. Válassza **log Analytics munkaterületek** lehetőséget.

2. Válassza a **Speciális beállítások** lehetőséget:

    ![Log Analytics speciális beállítások](media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png)
 
3. Válassza az **Adatok**, majd a **Windows Eseménynaplók** lehetőséget.  

4. Az Eseménynapló hozzáadásához írja be a napló nevét. Adja meg a **rendszer** elemet, majd válassza a pluszjelet ( **+** ).  

5. A táblázatban válassza ki a **hibát** és a **Figyelmeztetési** megszakításokat.

6. Válassza a **Mentés** lehetőséget az oldal tetején.

7. Válassza a **Windows-teljesítményszámlálók** lehetőséget a teljesítményszámlálók Windows-számítógépen való gyűjtésének engedélyezéséhez.

8. Amikor először konfigurálja a Windows-teljesítményszámlálókat egy új Log Analytics munkaterületre, lehetősége van arra, hogy gyorsan hozzon létre több gyakori számlálót. Minden lehetőség megjelenik a listában, a mellette lévő jelölőnégyzetekkel:

    ![Windows-teljesítményszámlálók](media/quick-collect-windows-computer/windows-perfcounters-default.png).
    
    Válassza **a kijelölt teljesítményszámlálók hozzáadása** lehetőséget. A számlálók a tíz másodperces gyűjtési mintavételi intervallummal lettek hozzáadva és beállítva.

9. Válassza a **Mentés** lehetőséget az oldal tetején.

## <a name="view-collected-data"></a>Összegyűjtött adatok megtekintése

Most, hogy engedélyezte az adatgyűjtést, futtasson egy egyszerű naplót, hogy bizonyos adatok megjelenjenek a célszámítógépen.  

1. A kiválasztott munkaterületen, a bal oldali ablaktáblán válassza a **naplók** lehetőséget.

2. A naplók lekérdezése lapon írja be `Perf` a lekérdezés-szerkesztőt, és válassza a **Futtatás** lehetőséget.
 
    ![Naplóbeli keresés Log Analytics](media/quick-collect-windows-computer/log-analytics-portal-queryexample.png)

    A rendszerképben szereplő lekérdezés például 10 000 teljesítményadatokat adott vissza. Ön ennél sokkal kevesebb találatot fog kapni.

    ![Log Analytics naplóbeli keresés eredménye](media/quick-collect-windows-computer/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége rájuk, távolítsa el az ügynököt a számítógépről, és törölje a Log Analytics munkaterületet.  

Az ügynök eltávolításához hajtsa végre az alábbi lépéseket:

1. Nyissa meg a Vezérlőpultot.

2. Nyissa meg a **Programok és szolgáltatások** részt.

3. A **programok és szolgáltatások** területen válassza a **Microsoft monitoring Agent** lehetőséget, majd válassza az **Eltávolítás** lehetőséget.

A korábban létrehozott Log Analytics munkaterület törléséhez válassza ki azt, majd az erőforrás lapon válassza a **Törlés** lehetőséget:

![Log Analytics-munkaterület törlése](media/quick-collect-windows-computer/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>Következő lépések

Most, hogy operatív és teljesítményadatokat gyűjt a Windows rendszerű számítógépéről, könnyedén megkezdheti az összegyűjtött adatok feltárását, elemzését és *kezelését.*  

Az adatmegtekintés és-elemzés megismeréséhez folytassa az oktatóanyagot:

> [!div class="nextstepaction"]
> [Adatok megtekintése és elemzése a Log Analyticsben](../logs/log-analytics-tutorial.md)