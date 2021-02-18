---
title: Tároló-figyelési megoldás a Azure Monitorban | Microsoft Docs
description: A Azure Monitor tároló-figyelési megoldás segítségével egyetlen helyen tekintheti meg és kezelheti a Docker-és a Windows-tároló gazdagépeit.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/06/2020
ms.openlocfilehash: bd7301d6fa70edd8a52a7af34122f0a8672dfde8
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100614278"
---
# <a name="container-monitoring-solution-in-azure-monitor"></a>Tároló-figyelési megoldás a Azure Monitor

![Tárolók szimbóluma](./media/containers/containers-symbol.png)

Ez a cikk bemutatja, hogyan állíthatja be és használhatja a Azure Monitor tároló-figyelési megoldást, amely segítségével egyetlen helyen tekintheti meg és kezelheti a Docker-és a Windows-tároló gazdagépeit. A Docker egy olyan szoftveres virtualizációs rendszer, amely a szoftverek központi telepítésének automatizálására szolgáló tárolók létrehozására szolgál.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

A megoldás megjeleníti, hogy mely tárolók futnak, milyen tárolói képet futtatnak, és hol futnak a tárolók. Megtekintheti a tárolók által használt parancsokat bemutató részletes naplózási információkat. A tárolókat a központi naplók megtekintésével és keresésével is elháríthatja anélkül, hogy távolról kellene megtekintenie a Docker-vagy a Windows-gazdagépeket. Megtalálhatja azokat a tárolókat, amelyek zajosak lehetnek, és a gazdagépen felesleges erőforrásokat fogyasztanak. Továbbá megtekintheti a tárolók központi CPU-, memória-, tárterület-és hálózati használati és teljesítmény-információit. A Windows rendszert futtató számítógépeken a Windows Server, a Hyper-V és a Docker-tárolók segítségével központosíthatja és összehasonlíthatja a naplókat. A megoldás a következő tároló-rendszerszervezőket támogatja:

- Docker Swarm
- DC/OS
- Kubernetes
- Service Fabric
- Red Hat OpenShift

Ha az [Azure Service Fabric](../../service-fabric/service-fabric-overview.md)-ban üzembe helyezett tárolókkal rendelkezik, javasoljuk, hogy a [Service Fabric megoldást](../../service-fabric/service-fabric-diagnostics-oms-setup.md) és ezt a megoldást is engedélyezze a fürt eseményeinek figyeléséhez. A Service Fabric megoldás engedélyezése előtt tekintse át [a Service Fabric megoldás használata](../../service-fabric/service-fabric-diagnostics-event-analysis-oms.md) című témakört, amelyből megismerheti, hogy mit nyújt, és hogyan használhatja azt.

Ha érdekli az Azure Kubernetes szolgáltatásban (ak) üzemeltetett Kubernetes-környezetekben üzembe helyezett számítási feladatok teljesítményének figyelése, tekintse meg az [Azure Kubernetes szolgáltatás figyelése](../containers/container-insights-overview.md)című témakört. A tároló-figyelési megoldás nem támogatja a platform figyelését.  

Az alábbi ábrán a különböző tárolók és ügynökök közötti kapcsolatok láthatók Azure Monitorsal.

![Tárolók diagramja](./media/containers/containers-diagram.png)

## <a name="system-requirements-and-supported-platforms"></a>Rendszerkövetelmények és támogatott platformok

A Kezdés előtt tekintse át az alábbi adatokat, és ellenőrizze, hogy megfelel-e az előfeltételeknek.

### <a name="container-monitoring-solution-support-for-docker-orchestrator-and-os-platform"></a>Tároló-figyelési megoldás támogatása a Docker Orchestrator és az operációs rendszer platformján

Az alábbi táblázat ismerteti a Docker-hangolást és az operációs rendszer figyelését a tárolók, a teljesítmény és a naplók Azure Monitor-vel való felügyeletének támogatásával.   

|Docker-hangolás | ACS | Linux | Windows | Tároló<br>Leltár | Kép<br>Leltár | Csomópont<br>Leltár | Tároló<br>Teljesítmény | Tároló<br>Esemény | Esemény<br>Napló | Tároló<br>Napló |
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
| Kubernetes | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Mesosphere<br>DC/OS | &#8226; | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; |
| Docker<br>Raj | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| Szolgáltatás<br>Fabric | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Red Hat megnyitva<br>SHIFT | | &#8226; | | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; | | &#8226; |
| Windows Server<br>önálló | | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| Linux-kiszolgáló<br>önálló | | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |

### <a name="docker-versions-supported-on-linux"></a>Linux rendszeren támogatott Docker-verziók

- Docker 1,11 – 1,13
- Docker CE és EE v 17.06

### <a name="x64-linux-distributions-supported-as-container-hosts"></a>tároló gazdagépként támogatott x64 Linux-disztribúciók

- Ubuntu 14,04 LTS és 16,04 LTS
- CoreOS (stabil)
- Amazon Linux 2016.09.0
- openSUSE 13,2
- openSUSE LEAP 42,2
- CentOS 7,2 és 7,3
- SLES 12
- RHEL 7,2 és 7,3
- Red Hat OpenShift Container platform (OCP) 3,4 és 3,5
- ACS Mesosphere DC/OS 1.7.3 – 1.8.8
- ACS Kubernetes 1.4.5 – 1,6
    - A Kubernetes-események, a Kubernetes-készletek és a tároló-folyamatok csak a Linux rendszerhez készült Log Analytics-ügynök 1.4.1-45-ös vagy újabb verziójával támogatottak.
- ACS Docker Swarm

[!INCLUDE [log-analytics-agent-note.md](../../../includes/log-analytics-agent-note.md)] 

### <a name="supported-windows-operating-system"></a>Támogatott Windows operációs rendszer

- Windows Server 2016
- Windows 10 évfordulós kiadás (Professional vagy Enterprise)

### <a name="docker-versions-supported-on-windows"></a>Windows rendszeren támogatott Docker-verziók

- Docker 1,12 és 1,13
- Docker 17.03.0 és újabb verziók

## <a name="installing-and-configuring-the-solution"></a>A megoldás telepítése és konfigurálása

A megoldás telepítésekor és konfigurálásakor vegye figyelembe az alábbi információkat.

1. Adja hozzá a tároló-figyelési megoldást a Log Analytics munkaterületéhez az [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ContainersOMS?tab=Overview) -en, vagy a [felügyeleti megoldások hozzáadása a Solutions Gallery](../insights/solutions.md)használatával című témakörben ismertetett eljárással.

2. A Docker telepítése és használata Log Analytics ügynökkel. Az operációs rendszer és a Docker-Orchestrator alapján az alábbi módszerekkel konfigurálhatja az ügynököt.
   - Önálló gazdagépek esetén:
     - A támogatott Linux operációs rendszereken telepítse és futtassa a Docker-t, majd telepítse és konfigurálja a [Linux rendszerhez készült log Analytics-ügynököt](../vm/quick-collect-linux-computer.md).  
     - A CoreOS nem futtathatja a Linux rendszerhez készült Log Analytics-ügynököt. Ehelyett a Linux rendszerhez készült Log Analytics ügynök tárolós verzióját futtatja. Tekintse át a Linux Container hosts szolgáltatásait, beleértve a CoreOS vagy a Azure Government Linux Container hosts gazdagépeket, beleértve a CoreOS, ha Azure Government-felhőben tároló
     - A Windows Server 2016 és a Windows 10 rendszeren telepítse a Docker-motort és az ügyfelet, majd csatlakoztasson egy ügynököt az adatok gyűjtéséhez és a Azure Monitor küldéséhez. Ha Windows-környezettel rendelkezik, tekintse át a [Windows Container hosts telepítése és konfigurálása](#install-and-configure-windows-container-hosts) című ismertetőt.
   - A Docker több gazdagépének összehangolása esetén:
     - Ha Red Hat OpenShift-környezettel rendelkezik, tekintse át a Log Analytics ügynök konfigurálása a Red Hat OpenShift.
     - Ha a Azure Container Servicet használó Kubernetes-fürttel rendelkezik:
       - Tekintse át [a Kubernetes log Analytics Linux-ügynök konfigurálása](#configure-a-log-analytics-linux-agent-for-kubernetes)című ismertetőt.
       - Tekintse át [az log Analytics Windows-ügynök konfigurálása a Kubernetes-hez](#configure-a-log-analytics-windows-agent-for-kubernetes)című ismertetőt.
       - Tekintse át az Log Analytics Agent telepítését a Linux Kubernetes.
     - Ha Azure Container Service DC/OS-fürttel rendelkezik, további információ: [Azure Container Service DC/os-fürt figyelése Azure Monitorsal](/previous-versions/azure/container-service/dcos-swarm/container-service-monitoring-oms).
     - Ha Docker Swarm módú környezettel rendelkezik, további információt a Log Analytics ügynök konfigurálása a Docker Swarmhoz című témakörben olvashat.
     - Ha Service Fabric-fürttel rendelkezik, további információt a [tárolók figyelése Azure Monitorsal](../../service-fabric/service-fabric-diagnostics-oms-containers.md)című témakörben olvashat.

A Windows rendszerű számítógépeken a Docker-motorok telepítésével és konfigurálásával kapcsolatos további információkért tekintse át a [Docker-motor a Windowsban](/virtualization/windowscontainers/manage-docker/configure-docker-daemon) című cikkét.

> [!IMPORTANT]
> A Docker-nek futnia kell, **mielőtt** telepítené a Linux rendszerhez készült [log Analytics-ügynököt](../vm/quick-collect-linux-computer.md) a tároló gazdagépeken. Ha már telepítette az ügynököt a Docker telepítése előtt, újra kell telepítenie a Linux rendszerhez készült Log Analytics-ügynököt. A Docker-ról további információt a [Docker webhelyén](https://www.docker.com)talál.

### <a name="install-and-configure-linux-container-hosts"></a>Linux-tároló gazdagépek telepítése és konfigurálása

A Docker telepítését követően az alábbi beállításokkal konfigurálhatja az ügynököt a Docker használatára. Először a Log Analytics-munkaterület AZONOSÍTÓját és kulcsát kell használnia, amelyet a Azure Portal találhat. A munkaterületen kattintson **gyorskonfigurálás**  >  **számítógépekre** a **munkaterület-azonosító** és az **elsődleges kulcs** megtekintéséhez.  Másolja ki és illessze be mindkettőt a kedvenc szerkesztőjébe.

**Az összes Linux-tároló gazdagépen, a CoreOS kivételével:**

- További információ és a Linux rendszerhez készült Log Analytics-ügynök telepítésének lépései: [log Analytics Agent – áttekintés](../agents/log-analytics-agent.md).

**Minden Linux-tároló gazdagéphez, beleértve a CoreOS is:**

Indítsa el a figyelni kívánt tárolót. Módosítsa és használja a következő példát:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/lib/docker/containers:/var/lib/docker/containers -e WSID="your workspace id" -e KEY="your key" -h=`hostname` -p 127.0.0.1:25225:25225 --name="omsagent" --restart=always microsoft/oms
```

**Az összes Azure Government Linux-tároló gazdagép, beleértve a CoreOS:**

Indítsa el a figyelni kívánt tárolót. Módosítsa és használja a következő példát:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/log:/var/log -v /var/lib/docker/containers:/var/lib/docker/containers -e WSID="your workspace id" -e KEY="your key" -e DOMAIN="opinsights.azure.us" -p 127.0.0.1:25225:25225 -p 127.0.0.1:25224:25224/udp --name="omsagent" -h=`hostname` --restart=always microsoft/oms
```

**Váltás egy telepített linuxos ügynök használatával egy tárolóban**

Ha korábban már használta a közvetlenül telepített ügynököt, és inkább egy tárolóban futó ügynököt szeretne használni, először el kell távolítania a Linux Log Analytics-ügynökét. Lásd: [a log Analytics-ügynök](../vm/quick-collect-linux-computer.md) eltávolítása a Linuxra, hogy megtudja, hogyan távolíthatja el sikeresen az ügynököt.  

#### <a name="configure-a-log-analytics-agent-for-docker-swarm"></a>Log Analytics ügynök konfigurálása a Docker Swarmhoz

A Log Analytics-ügynököt globális szolgáltatásként is futtathatja a Docker Swarmban. Log Analytics ügynök szolgáltatás létrehozásához használja az alábbi információkat. Meg kell adnia a Log Analytics munkaterület-azonosítót és az elsődleges kulcsot.

- Futtassa a következőt a fő csomóponton.

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock --mount type=bind,source=/var/lib/docker/containers,destination=/var/lib/docker/containers -e WSID="<WORKSPACE ID>" -e KEY="<PRIMARY KEY>" -p 25225:25225 -p 25224:25224/udp  --restart-condition=on-failure microsoft/oms
    ```

##### <a name="secure-secrets-for-docker-swarm"></a>Biztonságos Titkok a Docker Swarmhoz

A Docker Swarm esetében, miután létrehozta a munkaterület-azonosító és az elsődleges kulcs titkát, a következő információk alapján hozza létre a titkos adatokat.

1. Futtassa a következőt a fő csomóponton.

    ```
    echo "WSID" | docker secret create WSID -
    echo "KEY" | docker secret create KEY -
    ```

2. Győződjön meg arról, hogy a titkos kulcsok létrehozása megfelelő volt.

    ```
    keiko@swarmm-master-13957614-0:/run# sudo docker secret ls
    ```

    ```
    ID                          NAME                CREATED             UPDATED
    j2fj153zxy91j8zbcitnjxjiv   WSID                43 minutes ago      43 minutes ago
    l9rh3n987g9c45zffuxdxetd9   KEY                 38 minutes ago      38 minutes ago
    ```

3. A következő parancs futtatásával csatlakoztassa a titkokat a tároló Log Analytics ügynökhöz.

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock --mount type=bind,source=/var/lib/docker/containers,destination=/var/lib/docker/containers --secret source=WSID,target=WSID --secret source=KEY,target=KEY  -p 25225:25225 -p 25224:25224/udp --restart-condition=on-failure microsoft/oms
    ```

#### <a name="configure-a-log-analytics-agent-for-red-hat-openshift"></a>Log Analytics ügynök konfigurálása a Red Hat OpenShift

A tároló-figyelési adatok gyűjtésének megkezdéséhez háromféleképpen lehet felvenni a Log Analytics-ügynököt a Red Hat OpenShift.

* [A Linux rendszerhez készült log Analytics-ügynök telepítése](../vm/quick-collect-linux-computer.md) közvetlenül az egyes OpenShift-csomópontokon  
* [Log Analytics virtuálisgép-bővítmény engedélyezése](../vm/quick-collect-azurevm.md) az Azure-ban élő összes OpenShift-csomóponton  
* A Log Analytics-ügynök telepítése OpenShift Daemon-set  

Ebben a szakaszban azokat a lépéseket ismertetjük, amelyek szükségesek a Log Analytics-ügynök OpenShift Daemon-setként való telepítéséhez.  

1. Jelentkezzen be a OpenShift fő csomópontjára, és másolja a [OCP-omsagent. YAML YAML-](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-omsagent.yaml) fájlt a githubról a fő csomópontjára, és módosítsa az értéket a log Analytics munkaterület-azonosítójával és az elsődleges kulccsal.
2. A következő parancsok futtatásával hozzon létre egy projektet a Azure Monitorhoz, és állítsa be a felhasználói fiókot.

    ```
    oc adm new-project omslogging --node-selector='zone=default'
    oc project omslogging  
    oc create serviceaccount omsagent  
    oc adm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oc adm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

3. A Daemon-set telepítéséhez futtassa a következőt:

    `oc create -f ocp-omsagent.yaml`

4. Annak ellenőrzéséhez, hogy megfelelően van-e konfigurálva, írja be a következőt:

    `oc describe daemonset omsagent`  

    a kimenetnek pedig a következőhöz hasonlónak kell lennie:

    ```
    [ocpadmin@khm-0 ~]$ oc describe ds oms  
    Name:           oms  
    Image(s):       microsoft/oms  
    Selector:       name=omsagent  
    Node-Selector:  zone=default  
    Labels:         agentVersion=1.4.0-12  
                    dockerProviderVersion=10.0.0-25  
                    name=omsagent  
    Desired Number of Nodes Scheduled: 3  
    Current Number of Nodes Scheduled: 3  
    Number of Nodes Misscheduled: 0  
    Pods Status:    3 Running / 0 Waiting / 0 Succeeded / 0 Failed  
    No events.  
    ```

Ha a titkokat a Log Analytics munkaterület AZONOSÍTÓjának és elsődleges kulcsának védelmére szeretné használni a Log Analytics Agent Daemon-set YAML-fájl használatakor, hajtsa végre a következő lépéseket.

1. Jelentkezzen be a OpenShift fő csomópontjára, és másolja a YAML [-fájlt a OCP-DS-omsagent. YAML](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-ds-omsagent.yaml) és a Secret generáló szkript [OCP-secretgen.sh](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-secretgen.sh) a githubból.  Ez a szkript létrehozza a Secrets-adatok védelméhez Log Analytics-munkaterület AZONOSÍTÓjának és elsődleges kulcsának titkos YAML-fájlját.  
2. A következő parancsok futtatásával hozzon létre egy projektet a Azure Monitorhoz, és állítsa be a felhasználói fiókot. A titkos létrehozó parancsfájl kéri a Log Analytics-munkaterület AZONOSÍTÓját `<WSID>` és elsődleges kulcsát `<KEY>` , és a befejezés után létrehozza a OCP. YAML fájlt.  

    ```
    oc adm new-project omslogging --node-selector='zone=default'  
    oc project omslogging  
    oc create serviceaccount omsagent  
    oc adm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oc adm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

3. A titkos fájl üzembe helyezéséhez futtassa a következőt:

    `oc create -f ocp-secret.yaml`

4. Ellenőrizze a telepítést a következő futtatásával:

    `oc describe secret omsagent-secret`  

    a kimenetnek pedig a következőhöz hasonlónak kell lennie:  

    ```
    [ocpadmin@khocp-master-0 ~]$ oc describe secret omsagent-secret  
    Name:           omsagent-secret  
    Namespace:      omslogging  
    Labels:         <none>  
    Annotations:    <none>  

    Type:   Opaque  

    Data  
    ====  
    KEY:    89 bytes  
    WSID:   37 bytes  
    ```

5. Telepítse a Log Analytics Agent démont – állítsa be a YAML-fájlt a következő futtatásával:

    `oc create -f ocp-ds-omsagent.yaml`  

6. Ellenőrizze a telepítést a következő futtatásával:

    `oc describe ds oms`

    a kimenetnek pedig a következőhöz hasonlónak kell lennie:

    ```
    [ocpadmin@khocp-master-0 ~]$ oc describe ds oms  
    Name:           oms  
    Image(s):       microsoft/oms  
    Selector:       name=omsagent  
    Node-Selector:  zone=default  
    Labels:         agentVersion=1.4.0-12  
                    dockerProviderVersion=10.0.0-25  
                    name=omsagent  
    Desired Number of Nodes Scheduled: 3  
    Current Number of Nodes Scheduled: 3  
    Number of Nodes Misscheduled: 0  
    Pods Status:    3 Running / 0 Waiting / 0 Succeeded / 0 Failed  
    No events.  
    ```

#### <a name="configure-a-log-analytics-linux-agent-for-kubernetes"></a>Log Analytics Linux-ügynök konfigurálása a Kubernetes-hez

A Kubernetes egy parancsfájl használatával létrehozhatja a YAML-fájlt a munkaterület-AZONOSÍTÓhoz és az elsődleges kulcshoz a Linux rendszerhez készült Log Analytics-ügynök telepítéséhez. A [log Analytics Docker Kubernetes GitHub](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes) lapján vannak olyan fájlok, amelyek a titkos adatokkal vagy anélkül is használhatók.

- A Linux Daemonset elemet alapértelmezett Log Analytics ügynöke nem rendelkezik titkos információval (omsagent. YAML)
- A Linux Daemonset elemet YAML fájl Log Analytics ügynöke titkos adatokat (omsagent-DS-Secrets. YAML) használ titkos létrehozási parancsfájlokkal a Secrets YAML (omsagentsecret. YAML) fájl létrehozásához.

Kiválaszthatja, hogy omsagent-DaemonSets hoz létre a titkokkal vagy anélkül.

**Alapértelmezett OMSagent Daemonset elemet YAML-fájl titkos kódok nélkül**

- Az alapértelmezett Log Analytics Agent Daemonset elemet YAML fájl esetében cserélje le a `<WSID>` és a `<KEY>` WSID és a kulcsot. Másolja a fájlt a fő csomópontba, és futtassa a következőt:

    ```
    sudo kubectl create -f omsagent.yaml
    ```

**Alapértelmezett OMSagent Daemonset elemet YAML-fájl titkos kulcsokkal**

1. Ha Log Analytics ügynök Daemonset elemet szeretné használni a titkos adatok használatával, először hozza létre a titkokat.
    1. Másolja a parancsfájlt és a titkos sablon fájlját, és győződjön meg róla, hogy ugyanazon a címtáron vannak.
        - Titkos kód generálása – secret-gen.sh
        - titkos sablon – Secret-template. YAML
    2. Futtassa a szkriptet, az alábbi példához hasonlóan. A parancsfájl a Log Analytics munkaterület-azonosítót és az elsődleges kulcsot kéri, és a megadása után a szkript létrehoz egy titkos YAML-fájlt, hogy futtatni tudja.   

        ```
        #> sudo bash ./secret-gen.sh
        ```

    3. A Secrets Pod létrehozásához futtassa a következőt:
        ```
        sudo kubectl create -f omsagentsecret.yaml
        ```

    4. Az ellenőrzéshez futtassa a következőt:

        ```
        keiko@ubuntu16-13db:~# sudo kubectl get secrets
        ```

        A kimenetnek az alábbihoz hasonlónak kell lennie:

        ```
        NAME                  TYPE                                  DATA      AGE
        default-token-gvl91   kubernetes.io/service-account-token   3         50d
        omsagent-secret       Opaque                                2         1d
        ```

        ```
        keiko@ubuntu16-13db:~# sudo kubectl describe secrets omsagent-secret
        ```

        A kimenetnek az alábbihoz hasonlónak kell lennie:

        ```
        Name:           omsagent-secret
        Namespace:      default
        Labels:         <none>
        Annotations:    <none>

        Type:   Opaque

        Data
        ====
        WSID:   36 bytes
        KEY:    88 bytes
        ```

    5. Hozza létre a omsagent démont a futtatásával ```sudo kubectl create -f omsagent-ds-secrets.yaml```

2. Ellenőrizze, hogy a Log Analytics ügynök Daemonset elemet fut-e, a következőhöz hasonlóan:

    ```
    keiko@ubuntu16-13db:~# sudo kubectl get ds omsagent
    ```

    ```
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   3         3         <none>          1h
    ```

A Kubernetes esetében egy parancsfájl segítségével létrehozza a Secrets YAML-fájlt a munkaterület-AZONOSÍTÓhoz és az elsődleges kulcshoz a Linux rendszerhez készült Log Analytics-ügynökhöz. A titkos adatok védelméhez használja a következő példában szereplő információkat a [omsagent YAML fájllal](https://github.com/Microsoft/OMS-docker/blob/master/Kubernetes/omsagent.yaml) .

```
keiko@ubuntu16-13db:~# sudo kubectl describe secrets omsagent-secret
Name:           omsagent-secret
Namespace:      default
Labels:         <none>
Annotations:    <none>

Type:   Opaque

Data
====
WSID:   36 bytes
KEY:    88 bytes
```

#### <a name="configure-a-log-analytics-windows-agent-for-kubernetes"></a>Log Analytics Windows-ügynök konfigurálása a Kubernetes-hez

A Windows Kubernetes parancsfájl használatával létrehozhatja a YAML-fájlt a munkaterület-AZONOSÍTÓhoz és az elsődleges kulcshoz a Log Analytics-ügynök telepítéséhez. A [log Analytics Docker Kubernetes GitHub](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes/windows) oldalán vannak olyan fájlok, amelyeket használhat a titkos adataival.  A főkiszolgáló és az ügynök csomópontjain külön kell telepítenie a Log Analytics-ügynököt.  

1. Ha Log Analytics ügynök Daemonset elemet szeretné használni a főcsomóponton található titkos adatok használatával, jelentkezzen be, és először hozza létre a titkos kulcsokat.
    1. Másolja a parancsfájlt és a titkos sablon fájlját, és győződjön meg róla, hogy ugyanazon a címtáron vannak.
        - Titkos kód generálása – secret-gen.sh
        - titkos sablon – Secret-template. YAML

    2. Futtassa a szkriptet, az alábbi példához hasonlóan. A parancsfájl a Log Analytics munkaterület-azonosítót és az elsődleges kulcsot kéri, és a megadása után a szkript létrehoz egy titkos YAML-fájlt, hogy futtatni tudja.

        ```
        #> sudo bash ./secret-gen.sh
        ```
    3. Hozza létre a omsagent démont a futtatásával ```kubectl create -f omsagentsecret.yaml```
    4. A következő futtatásával ellenőrizhető:

        ```
        root@ubuntu16-13db:~# kubectl get secrets
        ```

        A kimenetnek az alábbihoz hasonlónak kell lennie:

        ```
        NAME                  TYPE                                  DATA      AGE
        default-token-gvl91   kubernetes.io/service-account-token   3         50d
        omsagent-secret       Opaque                                2         1d
        root@ubuntu16-13db:~# kubectl describe secrets omsagent-secret
        Name:           omsagent-secret
        Namespace:      default
        Labels:         <none>
        Annotations:    <none>

        Type:   Opaque

        Data
        ====
        WSID:   36 bytes
        KEY:    88 bytes
        ```

    5. Hozza létre a omsagent démont a futtatásával ```kubectl create -f ws-omsagent-de-secrets.yaml```

2. Ellenőrizze, hogy a Log Analytics ügynök Daemonset elemet fut-e, a következőhöz hasonlóan:

    ```
    root@ubuntu16-13db:~# kubectl get deployment omsagent
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   1         1         <none>          1h
    ```

3. Ha a Windows rendszert futtató munkavégző csomóponton szeretné telepíteni az ügynököt, kövesse a [Windows-tároló gazdagépek telepítése és konfigurálása](#install-and-configure-windows-container-hosts)című szakasz lépéseit.

#### <a name="use-helm-to-deploy-log-analytics-agent-on-linux-kubernetes"></a>Log Analytics-ügynök üzembe helyezése a Linux Kubernetes a Helm használatával

A következő lépésekkel végezheti el a Log Analytics ügynök üzembe helyezését a Linux Kubernetes-környezetben.

1. Hozza létre a omsagent démont a futtatásával ```helm install --name omsagent --set omsagent.secret.wsid=<WSID>,omsagent.secret.key=<KEY> stable/msoms```
2. Az eredmény az alábbihoz hasonlóan fog kinézni:

    ```
    NAME:   omsagent
    LAST DEPLOYED: Tue Sep 19 20:37:46 2017
    NAMESPACE: default
    STATUS: DEPLOYED

    RESOURCES:
    ==> v1/Secret
    NAME            TYPE    DATA  AGE
    omsagent-msoms  Opaque  3     3s

    ==> v1beta1/DaemonSet
    NAME            DESIRED  CURRENT  READY  UP-TO-DATE  AVAILABLE  NODE-SELECTOR  AGE
    omsagent-msoms  3        3        3      3           3          <none>         3s
    ```

3. A omsagent állapotát a következő parancs futtatásával tekintheti ```helm status "omsagent"``` meg: és a kimenet az alábbihoz hasonlóan fog kinézni:

    ```
    keiko@k8s-master-3814F33-0:~$ helm status omsagent
    LAST DEPLOYED: Tue Sep 19 20:37:46 2017
    NAMESPACE: default
    STATUS: DEPLOYED
 
    RESOURCES:
    ==> v1/Secret
    NAME            TYPE    DATA  AGE
    omsagent-msoms  Opaque  3     17m
 
    ==> v1beta1/DaemonSet
    NAME            DESIRED  CURRENT  READY  UP-TO-DATE  AVAILABLE  NODE-SELECTOR  AGE
    omsagent-msoms  3        3        3      3           3          <none>         17m
    ```
   
    További információkért tekintse meg a [Container Solution Helm diagramot](https://aka.ms/omscontainerhelm).

### <a name="install-and-configure-windows-container-hosts"></a>Windows Container hosts telepítése és konfigurálása

A Windows-tároló gazdagépek telepítéséhez és konfigurálásához használja a szakasz információit.

#### <a name="preparation-before-installing-windows-agents"></a>Felkészülés a Windows-ügynökök telepítése előtt

Mielőtt ügynököket telepít a Windows rendszerű számítógépekre, konfigurálnia kell a Docker szolgáltatást. A konfiguráció lehetővé teszi, hogy a Windows-ügynök vagy a Azure Monitor virtuálisgép-bővítmény a Docker TCP-szoftvercsatornát használja, hogy az ügynökök távolról hozzáférhessenek a Docker-démonhoz, és rögzíteni tudják a figyeléshez szükséges adatmennyiséget.

##### <a name="to-configure-the-docker-service"></a>A Docker szolgáltatás konfigurálása  

Hajtsa végre a következő PowerShell-parancsokat a TCP-cső és a nevesített cső a Windows Serveren való engedélyezéséhez:

```
Stop-Service docker
dockerd --unregister-service
dockerd --register-service -H npipe:// -H 0.0.0.0:2375  
Start-Service docker
```

A Windows-tárolók által használt Docker Daemon-konfigurációval kapcsolatos további információkért lásd: [Docker-motor Windows rendszeren](/virtualization/windowscontainers/manage-docker/configure-docker-daemon).

#### <a name="install-windows-agents"></a>Windows-ügynökök telepítése

A Windows és a Hyper-V tárolók figyelésének engedélyezéséhez telepítse a Microsoft monitoring Agent (MMA) szolgáltatást a tároló gazdagépeket futtató Windows rendszerű számítógépekre. A helyszíni környezetben található Windows rendszerű számítógépeken lásd: Windows rendszerű [számítógépek Összekapcsolása Azure monitorhoz](../agents/agent-windows.md). Az Azure-ban futó virtuális gépek esetében a [virtuálisgép-bővítmény](../vm/quick-collect-azurevm.md)használatával kapcsolja össze őket a Azure monitor.

A Service Fabricon futó Windows-tárolók figyelésére van lehetőség. A Service Fabric azonban jelenleg csak [Az Azure-ban futó virtuális gépek](../vm/quick-collect-azurevm.md) és a helyszíni [környezetben Windows rendszert futtató számítógépek](../agents/agent-windows.md) támogatottak.

Ellenőrizheti, hogy a tároló-figyelési megoldás megfelelően van-e beállítva a Windows rendszerhez. Keresse meg a *ContainerManagement.xxx*, és ellenőrizze, hogy a felügyeleti csomag megfelelően lett-e letöltve. A fájloknak a C:\Program Files\Microsoft monitoring Agent\Agent\Health Service State\Management Packs mappában kell lenniük.

## <a name="solution-components"></a>Megoldás-összetevők

A Azure Portal navigáljon a *Solutions Galleryhoz* , és adja hozzá a **tároló-figyelési megoldást**. Ha Windows-ügynököket használ, a következő felügyeleti csomagot kell telepítenie minden olyan számítógépre, amelyre ügynök van telepítve a megoldás hozzáadásakor. A felügyeleti csomaghoz nincs szükség konfigurációra vagy karbantartásra.

- *ContainerManagement.xxx* telepítve a C:\Program Files\Microsoft monitoring Agent\Agent\Health Service State\Management Packs

## <a name="container-data-collection-details"></a>Tároló adatgyűjtési adatai

A tároló-figyelési megoldás különféle teljesítménymutatókat és adatokat gyűjt a tároló gazdagépekről és tárolóról az Ön által engedélyezett ügynökök használatával.

Az adatokat a következő típusú ügynökök gyűjtik be három percenként.

- [Linux-Log Analytics ügynök](../vm/quick-collect-linux-computer.md)
- [Windows-ügynök](../agents/agent-windows.md)
- [Log Analytics virtuálisgép-bővítmény](../vm/quick-collect-azurevm.md)

### <a name="container-records"></a>Tároló rekordjai

A következő táblázat példákat mutat be a tároló-figyelési megoldás által összegyűjtött rekordokra, valamint a naplók keresési eredményei között megjelenő adattípusokra.

| Adattípus | Adattípus a naplóbeli keresésben | Mezők |
| --- | --- | --- |
| Gazdagépek és tárolók teljesítménye | `Perf` | Számítógép, ObjectName, CounterName &#40;% processzoridő, lemez olvasás MB, lemez írása MB, memória-használat MB, hálózati fogadási bájtok, hálózati küldési bájtok, processzor kihasználtsága (mp), hálózati&#41;, kártyabirtokos számlájának megterhelését, TimeGenerated, CounterPath, SourceSystem |
| Tároló leltározása | `ContainerInventory` | TimeGenerated, számítógép, tároló neve, ContainerHostname, rendszerkép, ImageTag, ContainerState, ExitCode, EnvironmentVar, parancs, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| Tároló rendszerképének leltára | `ContainerImageInventory` | TimeGenerated, számítógép, rendszerkép, ImageTag, ImageSize, VirtualSize, fut, szüneteltetve, leállítva, sikertelen, SourceSystem, ImageID, TotalContainer |
| Tároló naplója | `ContainerLog` | TimeGenerated, számítógép, rendszerkép azonosítója, tároló neve, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| Container Service-napló | `ContainerServiceLog`  | TimeGenerated, számítógép, TimeOfCommand, rendszerkép, parancs, SourceSystem, ContainerID |
| Tároló-csomópont leltározása | `ContainerNodeInventory_CL`| TimeGenerated, számítógép, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Kubernetes leltár | `KubePodInventory_CL` | TimeGenerated, számítógép, PodLabel_deployment_s, PodLabel_deploymentconfig_s, PodLabel_docker_registry_s, Name_s, Namespace_s, PodStatus_s, PodIp_s, PodUid_g, PodCreationTimeStamp_t, SourceSystem |
| Tároló folyamata | `ContainerProcess_CL` | TimeGenerated, számítógép, Pod_s, Namespace_s, ClassName_s, InstanceID_s, Uid_s, PID_s, PPID_s, C_s, STIME_s, Tty_s, TIME_s, Cmd_s, Id_s, Name_s, SourceSystem |
| Kubernetes események | `KubeEvents_CL` | TimeGenerated, számítógép, Name_s, ObjectKind_s, Namespace_s, Reason_s, Type_s, SourceComponent_s, SourceSystem, üzenet |

A *PodLabel* -adattípusokhoz hozzáfűzött címkék saját egyéni címkék. A táblázatban látható hozzáfűzött PodLabel-feliratok példák. Igen, a, `PodLabel_deployment_s` `PodLabel_deploymentconfig_s` `PodLabel_docker_registry_s` a környezet adatkészletében és általánosságban hasonló módon fog megjelenni `PodLabel_yourlabel_s` .

## <a name="monitor-containers"></a>Tárolók figyelése
Miután engedélyezte a megoldást a Azure Portalban, a containers **(tárolók) csempén** a tároló gazdagépek és a gazdagépeken futó tárolók összegző információi láthatók.

![Tárolók csempe](./media/containers/containers-title.png)

A csempe áttekintést nyújt arról, hogy hány tároló van a környezetben, és hogy a művelet meghiúsult, fut vagy leállt.

### <a name="using-the-containers-dashboard"></a>A tárolók irányítópultjának használata

Kattintson a **tárolók** csempére. Innentől kezdve a következő nézetek láthatók:

- **Container Events** – a tároló állapotát és a meghibásodott tárolókat tartalmazó számítógépeket jeleníti meg.
- **Tároló naplófájljai** – megjeleníti a tároló naplófájljainak időbeli alakulását, valamint a legtöbb naplófájlt tartalmazó számítógépek listáját.
- **Kubernetes-események** – megjeleníti a Kubernetes események által generált diagramot, valamint azt, hogy a hüvely milyen okok miatt generálta az eseményeket. *Ezt az adatkészletet csak Linux-környezetekben használják.*
- **Kubernetes-névtér leltározása** – a névterek és a hüvelyek számát jeleníti meg, és megjeleníti a hierarchiát. *Ezt az adatkészletet csak Linux-környezetekben használják.*
- **Tároló-csomópont leltározása** – a tároló csomópontjain/gazdagépeken használt hangolási típusok számát jeleníti meg. A számítógép-csomópontokat/gazdagépeket a tárolók száma is tartalmazza. *Ezt az adatkészletet csak Linux-környezetekben használják.*
- **Tároló lemezképek leltározása** – a felhasznált tároló-lemezképek és a képtípusok számának teljes számát jeleníti meg. A lemezképek számát a Képcímke is felsorolja.
- **Tárolók állapota** – megjeleníti a tárolók és a gazdagépek futó számítógépeinek teljes számát. A számítógépeket a futó gazdagépek száma is tartalmazza.
- **Tároló folyamata** – a tárolók folyamatainak időbeli diagramját jeleníti meg. A tárolókat a tárolókban lévő parancs/folyamat futtatásával is listázhatja. *Ezt az adatkészletet csak Linux-környezetekben használják.*
- **Tároló CPU-teljesítménye** – a számítógép-csomópontok/gazdagépek átlagos CPU-kihasználtságának grafikonját jeleníti meg. A a számítógép-csomópontok/gazdagépek a CPU-kihasználtság alapján való használatát is felsorolja.
- **Tároló memória-teljesítménye** – megjeleníti a memóriahasználat időbeli diagramját. A a számítógép memóriájának kihasználtságát is felsorolja a példány neve alapján.
- **Számítógép teljesítménye** – MEGJELENÍTI a CPU-teljesítmény százalékos arányának időbeli alakulását, a memóriahasználat százalékos arányát, valamint a szabad lemezterületet (MB) az idő múlásával. A diagram bármely sorára rámutathat a további részletek megtekintéséhez.

Az irányítópult minden területe az összegyűjtött adatokon futtatott keresés vizuális ábrázolása.

![Képernyőkép, amely megjeleníti az összegyűjtött adatok megtekintésére szolgáló irányítópultot. ](./media/containers/containers-dash01.png)

![Tárolók irányítópult](./media/containers/containers-dash02.png)

A **tároló állapota** területen kattintson az alább látható felső részre.

![Tárolók állapota](./media/containers/containers-status.png)

A Log Analytics megnyílik, és megjeleníti a tárolók állapotával kapcsolatos információkat.

![Log Analytics tárolók számára](./media/containers/containers-log-search.png)

Itt szerkesztheti a keresési lekérdezést, hogy megkeresse azokat a konkrét információkat, amelyekre kíváncsi. További információ a naplók lekérdezéséről: [lekérdezések Azure monitorban való lekérdezéséhez](../logs/log-query-overview.md).

## <a name="troubleshoot-by-finding-a-failed-container"></a>Hibakeresés sikertelen tároló keresésével

Log Analytics egy tárolót **sikertelenként** jelöl meg, ha nem nulla kilépési kóddal kilépett. A **hibás tárolók** területén a környezetben található hibák és hibák áttekintése látható.

### <a name="to-find-failed-containers"></a>Sikertelen tárolók keresése

1. Kattintson a **tároló állapota** elemre.  
   ![tárolók állapota](./media/containers/containers-status.png)
2. A Log Analytics megnyílik, és megjeleníti a tárolók állapotát, a következőhöz hasonlóan.  
   ![tárolók állapota](./media/containers/containers-log-search.png)
3. Bontsa ki a meghibásodott sort, és kattintson a + gombra a feltételek a lekérdezéshez való hozzáadásához. Ezután adja meg a lekérdezés összefoglaló sorát.
   ![A megjegyzést tartalmazó sort megjelenítő képernyőkép.](./media/containers/containers-state-failed-select.png)  
1. Futtassa a lekérdezést, majd az eredmények egyik sorát kibontva tekintse meg a rendszerkép AZONOSÍTÓját.  
   ![Képernyőkép, amely bemutatja, hogyan lehet megtekinteni a rendszerkép AZONOSÍTÓját.](./media/containers/containers-state-failed.png)  
1. Írja be a következőt a napló lekérdezésbe. `ContainerImageInventory | where ImageID == <ImageID>` a rendszerkép részleteit, például a képméretet és a leállított és sikertelen lemezképek számát tekintheti meg.  
   ![sikertelen tárolók](./media/containers/containers-failed04.png)

## <a name="query-logs-for-container-data"></a>Adattárolók lekérdezési naplói

Ha egy adott hibával kapcsolatos hibaelhárítást végez, a segítségével megtekintheti, hol történnek a környezetében. A következő típusú naplók segítségével lekérdezések hozhatók létre a kívánt információk visszaküldéséhez.

- **ContainerImageInventory** – ezt a típust akkor használja, ha a rendszerkép által szervezett adatokat keres, és megtekinti a képi adatokat, például a képazonosítókat vagy a méreteket.
- **ContainerInventory** – ezt a típust akkor használja, ha a tároló helyéről, a nevükről, valamint a futó lemezképekről szeretne információt használni.
- **ContainerLog** – ezt a típust használja, ha konkrét hibanapló-információkat és-bejegyzéseket szeretne keresni.
- **ContainerNodeInventory_CL**  Akkor használja ezt a típust, ha szeretné, hogy a tárolók és csomópontok hová legyenek tárolva. A Docker-verziót, a hangolás típusát, a tárterületet és a hálózati információkat tartalmazza.
- **ContainerProcess_CL** Ezzel a típussal gyorsan megtekintheti a tárolón belül futó folyamatot.
- **ContainerServiceLog** – ezt a típust akkor használja, ha a Docker-démon (például indítási, leállítási, törlési és lekérési parancsok) naplózási adatait keresi.
- **KubeEvents_CL**  Ezzel a típussal megtekintheti a Kubernetes eseményeket.
- **KubePodInventory_CL**  Akkor használja ezt a típust, ha meg szeretné ismerni a fürt hierarchiájának információit.

### <a name="to-query-logs-for-container-data"></a>A tárolók adatnaplóinak lekérdezése

* Válasszon egy olyan rendszerképet, amelyről tudja, hogy nemrég meghiúsult, és keresse meg a naplófájlokat. Kezdje azzal, hogy megkeresi az adott képet futtató **ContainerInventory** -kereséssel rendelkező tároló nevét. Például keressen rá a következőre: `ContainerInventory | where Image == "ubuntu" and ContainerState == "Failed"`  
    ![Ubuntu-tárolók keresése](./media/containers/search-ubuntu.png)

  Bontsa ki az eredmények bármelyik sorát a tároló részleteinek megtekintéséhez.

## <a name="example-log-queries"></a>Példák a naplók lekérdezésére

Gyakran hasznos olyan lekérdezéseket létrehozni, amelyek egy példával vagy kettővel kezdődnek, majd úgy módosítják őket, hogy illeszkedjenek a környezetéhez. Kiindulási pontként a megoldás oldal jobb szélén található **lekérdezési** területtel kísérletezheti, így könnyebben hozhat létre összetettebb lekérdezéseket.

![Tárolók lekérdezései](./media/containers/containers-queries.png)

## <a name="saving-log-queries"></a>Naplózási lekérdezések mentése

A lekérdezések mentése a Azure Monitor egy standard funkciója. A mentésük után a későbbiekben hasznosnak találhatja a jövőbeli használatot.

Ha olyan lekérdezést hoz létre, amelyet hasznosnak talál, mentse azt a Keresés lap tetején található **Kedvencek** elemre kattintva. Ezután egyszerűen elérheti később a **saját irányítópult** lapon.

## <a name="next-steps"></a>Következő lépések

[Lekérdezési naplók](../logs/log-query-overview.md) a részletes tároló-adatrekordok megtekintéséhez.

