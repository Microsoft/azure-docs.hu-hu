---
title: 'Gyors útmutató: adatok gyűjtése hibrid Linux rendszerű számítógépről Azure Monitor'
description: Ebből a rövid útmutatóból megtudhatja, hogyan helyezheti üzembe a Log Analytics-ügynököt az Azure-on kívül futtatott Linux rendszerű számítógépeken, és hogyan engedélyezheti az adatgyűjtést Azure Monitor naplók
services: azure-monitor
documentationcenter: azure-monitor
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 12/24/2019
ms.author: bwren
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019
ms.openlocfilehash: ef6e31968f85407e8055315becdc1337b474ad70
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100628660"
---
# <a name="quickstart-collect-data-from-a-linux-computer-in-a-hybrid-environment-with-azure-monitor"></a>Gyors útmutató: adatok gyűjtése Linux rendszerű számítógépről hibrid környezetben Azure Monitor

A [Azure monitor](../overview.md) adatokat gyűjthet közvetlenül a környezetében található fizikai vagy virtuális Linux rendszerű számítógépekről egy log Analytics munkaterületre, amely részletes elemzést és korrelációt biztosít. A [log Analytics ügynök](../agents/log-analytics-agent.md) telepítése lehetővé teszi, hogy a Azure monitor adatközpontból vagy más felhőalapú környezetből gyűjtsön adatokat. Ez a rövid útmutató bemutatja, hogyan konfigurálhat és gyűjthet adatokat a Linux-kiszolgálóról néhány egyszerű lépéssel. Az Azure Linux virtuális gépekkel kapcsolatos további információkért lásd: [adatok gyűjtése az Azure Virtual Machines szolgáltatással kapcsolatban](../learn/quick-collect-azurevm.md).  

A támogatott konfiguráció megismeréséhez tekintse meg a [támogatott operációs rendszerek](../agents/agents-overview.md#supported-operating-systems) és [hálózati tűzfal konfigurálása](../agents/log-analytics-agent.md#network-requirements)című témakört.
 
Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen. 

## <a name="create-a-workspace"></a>Munkaterület létrehozása

1. A Azure Portal válassza a **minden szolgáltatás** lehetőséget. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza **log Analytics munkaterületek** lehetőséget.

    ![Log Analytics munkaterület megkeresése a Azure Portal](media/quick-collect-azurevm/azure-portal-log-analytics-workspaces.png)<br>  

2. Válassza a **Létrehozás** lehetőséget, majd válassza ki a kívánt beállításokat a következő elemekhez:

   * Adja meg az új **Log Analytics-munkaterület** nevét, például: *DefaultLAWorkspace*.  
   * A legördülő listából válassza ki azt az **előfizetést**, amelyikhez kapcsolódni szeretne, ha az alapértelmezett kiválasztás nem megfelelő.
   * Az **Erőforráscsoport** beállításnál válasszon ki egy meglévő erőforráscsoportot, amely egy vagy több Azure-beli virtuális gépet tartalmaz.  
   * Válassza ki a **Helyet** a virtuális gépek üzembehelyezési céljaként.  További információkért tekintse meg [a Log Analytics által támogatott régiókat](https://azure.microsoft.com/regions/services/).
   * Ha 2018. április 2. után létrehozott új előfizetésben hoz létre munkaterületet, az automatikusan a *GB-alapú* díjcsomagot használja, és a tarifacsomag kiválasztásának lehetősége nem érhető el.  Ha az április 2. előtt létrehozott meglévő előfizetéshez hoz létre munkaterületet, vagy pedig egy meglévő EA-regisztrációhoz kötött előfizetéshez, válassza ki a kívánt tarifacsomagot.  További információt az elérhető csomagokról [a Log Analytics részletes díjszabásában](https://azure.microsoft.com/pricing/details/log-analytics/) találhat.
  
        ![Log Analytics munkaterület létrehozása a Azure Portal](media/quick-collect-azurevm/create-log-analytics-workspace-azure-portal.png) 

3. Miután megadta a szükséges információkat a **log Analytics munkaterület** ablaktáblán, kattintson az **OK gombra**.  

Az **Értesítések** menüpontot kiválasztva nyomon követheti, hogyan ellenőrzi a rendszer az adatokat, és hogyan hozza létre a munkaterületet. 

## <a name="obtain-workspace-id-and-key"></a>A munkaterület-azonosító és -kulcs lekérése

A Linuxhoz készült Log Analytics-ügynök telepítése előtt szüksége lesz a Log Analytics-munkaterület azonosítójára és kulcsára. Ezt az információt az ügynök burkolójának parancsfájlja megköveteli, hogy megfelelően konfigurálja az ügynököt, és ellenőrizze, hogy sikeresen tud-e kommunikálni Azure Monitorokkal.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

1. A Azure Portal bal felső sarkában válassza a **minden szolgáltatás** lehetőséget. A keresőmezőbe írja be a **log Analytics** kifejezést. A beíráskor a rendszer a bemenet alapján szűri a listákat. Válassza **log Analytics munkaterületek** lehetőséget.

2. Log Analytics munkaterületek listájában válassza ki a korábban létrehozott munkaterületet. (Lehet, hogy elnevezte a **DefaultLAWorkspace**.)

3. **Ügynökök felügyeletének** kiválasztása:
 
4. Ezután válassza a **Linux-kiszolgálók** lehetőséget.

5. A **munkaterület-azonosító** és az **elsődleges kulcs** jobb oldalán lévő érték. Másolja ki és illessze be mindkettőt a kedvenc szerkesztőjébe.

## <a name="install-the-agent-for-linux"></a>A Linuxhoz készült ügynök telepítése

Az alábbi lépésekkel konfigurálhatóak az ügynök beállításai a Log Analytics használatához az Azure és az Azure Government felhőben.  

>[!NOTE]
>A Linuxhoz készült Log Analytics-ügynököt nem lehet úgy konfigurálni, hogy egynél több Log Analytics-munkaterületre is jelentsen.  

Ha a Linux rendszerű számítógépnek proxykiszolgálón keresztül kell kommunikálnia a Log Analytics szolgáltatással, a proxykonfiguráció a parancssorban a `-p [protocol://][user:password@]proxyhost[:port]` záradékkal adható meg.  A *proxyhost* tulajdonság a proxykiszolgáló teljes tartománynevét vagy IP-címét fogadja el. 

Például: `https://user01:password@proxy01.contoso.com:30443`

1. A Linux rendszerű számítógép Log Analytics munkaterülethez való kapcsolódásának konfigurálásához futtassa a következő parancsot, amely a munkaterület-azonosítót és az elsődleges kulcsot a korábban másolta. A következő parancs letölti, majd az ellenőrzőösszeg érvényesítése után telepíti az ügynököt. 
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    A következő parancs tartalmazza a `-p` proxy paramétert és a példa szintaxisát, ha a proxykiszolgáló hitelesítést igényel:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://][user:password@]proxyhost[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Ha úgy szeretné konfigurálni a Linux rendszerű számítógépet, hogy Azure Government felhőben Log Analytics munkaterülethez kapcsolódjon, futtassa a következő parancsot, amely a munkaterület-azonosítót és az elsődleges kulcsot a korábban másolta. A következő parancs letölti, majd az ellenőrzőösszeg érvényesítése után telepíti az ügynököt. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    A következő parancs tartalmazza a `-p` proxy paramétert és a példa szintaxisát, ha a proxykiszolgáló hitelesítést igényel:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://][user:password@]proxyhost[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```

3. Indítsa újra az ügynököt a következő parancs futtatásával: 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="collect-event-and-performance-data"></a>Esemény- és teljesítményadatok gyűjtése

A Azure Monitor a hosszú távú elemzésekhez és jelentéskészítéshez megadott linuxos syslog-és teljesítményszámlálók eseményeinek összegyűjtését teszi elérhetővé. Ha egy adott feltételt észlel, akkor is végezhet műveleteket. A következő lépésekkel konfigurálhatja az események gyűjtését a Linux rendszernaplójából, illetve egyes gyakran használt teljesítményszámlálókból is.  

1. A Azure Portal válassza a **minden szolgáltatás** lehetőséget. Az erőforrások listájába írja be a Log Analytics kifejezést. A beíráskor a rendszer a bemenet alapján szűri a listákat. Válassza ki **log Analytics munkaterületeket** , és a log Analytics munkaterületek listájában válassza ki a keresett munkaterületet, és válassza a **log Analytics** munkaterület **Speciális beállítások** elemét.

2. Válassza **az** adatelemet, majd válassza a **syslog** lehetőséget.  

3. A syslog hozzáadásához írja be a napló nevét. Adja meg a **syslog** -t, majd válassza ki a plusz jelet **+** .  

4. A táblában törölje a **Tájékoztatás**, az **Értesítés** és a **Hibakeresés** súlyossági szint jelölését. 

5. A konfiguráció mentéséhez kattintson a **Save (Mentés** ) gombra a lap tetején.

6. Válassza a **Linuxos teljesítményadatok** elemet a teljesítményszámláló-adatok gyűjtésének Linux rendszerű gépen történő engedélyezéséhez. 

7. Amikor először konfigurálja egy új Log Analytics-munkaterület Linux-teljesítményszámlálóit, akkor több gyakran használt számlálót is gyorsan létrehozhat. Ezek mindegyike mellett egy jelölőnégyzet található.

    ![Alapértelmezett Linux-teljesítményszámlálók kiválasztva Azure Monitor](media/quick-collect-azurevm/linux-perfcounters-azure-monitor.png)

    Válassza az **alábbi konfiguráció alkalmazása a saját gépekre** lehetőséget, majd kattintson **a kijelölt teljesítményszámlálók hozzáadása** lehetőségre. A rendszer hozzáadja a kiválasztott számlálókat, és mindegyikhez beállít egy tíz másodperces mintagyűjtési időszakot.  

8. A konfiguráció mentéséhez kattintson a **Save (Mentés** ) gombra a lap tetején.

## <a name="view-data-collected"></a>Összegyűjtött adatok megtekintése

Most, hogy engedélyezte az adatgyűjtést, futtasson le egy egyszerű naplóbeli keresést, hogy megtekinthessen néhány, a célszámtógépekről származó adatot.  

1. A kiválasztott munkaterületen, a bal oldali ablaktáblán válassza a **naplók** lehetőséget.

2. A naplók lekérdezése lapon írja be `Perf` a lekérdezés-szerkesztőt, és válassza a **Futtatás** lehetőséget.
 
    ![Naplóbeli keresés Log Analytics](media/quick-collect-linux-computer/log-analytics-portal-queryexample.png)

    A következő képen szereplő lekérdezés például 10 000 teljesítményadatokat adott vissza. Ön ennél sokkal kevesebb találatot fog kapni.

    ![Log Analytics naplóbeli keresés eredménye](media/quick-collect-linux-computer/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, eltávolíthatja az ügynököt a linuxos számítógépről, és törölheti a Log Analytics-munkaterületet.  

Az ügynök eltávolításához futtassa az alábbi parancsot a Linux rendszerű számítógépen. A *--purge* argumentum teljesen eltávolítja az ügynököt és annak konfigurációját.

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

A munkaterület törléséhez válassza ki a korábban létrehozott Log Analytics munkaterületet, és az erőforrás lapon válassza a **Törlés** lehetőséget.

![Log Analytics-erőforrás törlése](media/quick-collect-linux-computer/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>Következő lépések

Most, hogy már gyűjti a működési és teljesítményadatokat a linuxos számítógépéről, könnyen nekiláthat az *ingyenesen* gyűjtött adatok felfedezésének és elemzésének, és reagálhat a kapott eredményekre.  

Az adatok megtekintésének és elemzésének ismertetéséhez lépjen tovább az útmutatóhoz.

> [!div class="nextstepaction"]
> [Adatok megtekintése és elemzése a Log Analyticsben](../log-query/log-analytics-tutorial.md)