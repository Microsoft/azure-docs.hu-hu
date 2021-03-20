---
title: Az alkalmazás hibakeresése az Eclipse-ben
description: A szolgáltatások megbízhatóságának és teljesítményének javítása az Eclipse-ben való fejlesztéssel és hibakereséssel egy helyi fejlesztési fürtön.
ms.topic: conceptual
ms.date: 11/02/2017
ms.custom: devx-track-java
ms.openlocfilehash: d321e0c10b66a15e6cb309cefe711602fa12957c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91534109"
---
# <a name="debug-your-java-service-fabric-application-using-eclipse"></a>Java Service Fabric-alkalmazás hibakeresése az Eclipse használatával
> [!div class="op_single_selector"]
> * [Visual Studio/CSharp](service-fabric-debugging-your-application.md) 
> * [Eclipse/Java](service-fabric-debugging-your-application-java.md)
> 

1. Indítsa el a helyi fejlesztési fürtöt a [Service Fabric fejlesztői környezet beállítása](service-fabric-get-started-linux.md)című témakör lépéseit követve.

2. Frissítse a hibakereséshez használni kívánt szolgáltatás entryPoint.sh, hogy elindítsa a Java-folyamatot a Távoli hibakeresési paraméterekkel. Ez a fájl a következő helyen található: `ApplicationName\ServiceNamePkg\Code\entrypoint.sh` . Ebben a példában a 8001-es port van beállítva a hibakereséshez.

    ```sh
    java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -jar myapp.jar
    ```
3. Frissítse az alkalmazás-jegyzékfájlt úgy, hogy beállítja a példányszámot vagy az 1-re felkeresett szolgáltatás replikáinak darabszámát. Ezzel a beállítással elkerülheti, hogy ütközések alakuljanak ki a hibakereséshez használt portnál. Állapotmentes szolgáltatások esetén például állítsa be az `InstanceCount="1"` értéket, állapotalapú szolgáltatások esetén pedig állítsa be a cél és a minimális replikakészlet méretét 1-re a következőképpen: `TargetReplicaSetSize="1" MinReplicaSetSize="1"`.

4. Telepítse az alkalmazást.

5. Az Eclipse IDE-ben válassza a **Futtatás – > hibakeresési konfigurációk – > távoli Java-alkalmazás és a bemeneti kapcsolatok tulajdonságai** lehetőséget, és állítsa be a tulajdonságokat a következőképpen:

   ```
   Host: ipaddress
   Port: 8001
   ```
6.  Töréspontokat állíthat be a kívánt pontokon, és hibakeresést végezhet az alkalmazásban.

Ha az alkalmazás összeomlik, érdemes lehet engedélyezni a coredumps is. Hajtson végre `ulimit -c` egy rendszerhéjban, és ha 0 értéket ad vissza, akkor a coredumps nem engedélyezettek. A korlátlan coredumps engedélyezéséhez hajtsa végre a következő parancsot: `ulimit -c unlimited` . Az állapotot a parancs használatával is ellenőrizheti `ulimit -a` .  Ha szeretné frissíteni a coredump-létrehozási útvonalat, hajtsa végre a következőt: `echo '/tmp/core_%e.%p' | sudo tee /proc/sys/kernel/core_pattern` . 

### <a name="next-steps"></a>Következő lépések

* [Naplók gyűjtése Linux Azure Diagnostics használatával](./service-fabric-diagnostics-event-aggregation-lad.md).
* [A szolgáltatások helyi figyelése és diagnosztizálása](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md).
