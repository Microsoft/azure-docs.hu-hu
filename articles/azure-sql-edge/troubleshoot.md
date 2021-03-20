---
title: Azure SQL Edge-telepítések hibaelhárítása
description: További információ az Azure SQL Edge telepítésekor előforduló lehetséges hibákról
keywords: SQL Edge, hibaelhárítás, telepítési hibák
services: sql-edge
ms.service: sql-edge
ms.topic: troubleshooting
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 517fed0dd9eb1736344546bde9f79e52ee17182f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91333103"
---
# <a name="troubleshooting-azure-sql-edge-deployments"></a>Azure SQL Edge-telepítések hibaelhárítása 

Ez a cikk az Azure SQL Edge-tárolók üzembe helyezése és használata során észlelt lehetséges hibákról nyújt információkat, valamint hibaelhárítási technikákat biztosít a problémák megoldásához. 

Az Azure SQL Edge két üzembe helyezési modellt támogat: 
- Csatlakoztatott üzembe helyezés Azure IoT Edgeon keresztül: az Azure SQL Edge elérhető az Azure piactéren, és modulként üzembe helyezhető [Azure IoT Edgehoz](../iot-edge/about-iot-edge.md). További információ: az [Azure SQL Edge üzembe helyezése](deploy-portal.md).<br>

- Leválasztott központi telepítés: az Azure SQL Edge-tároló lemezképei a Docker hub-ból tölthetők le, és önálló Docker-tárolóként vagy kubernetes-fürtön is üzembe helyezhetők. További információ: az [Azure SQL Edge üzembe helyezése a Docker-vel](disconnected-deployment.md) és az [Azure SQL Edge-tároló üzembe helyezése a Kubernetes-ben](deploy-kubernetes.md).

## <a name="troubleshooting-iot-edge-device-and-deployments"></a>IoT Edge eszköz és központi telepítések hibaelhárítása

Ha hibaüzenet jelenik meg az SQL Edge Azure IoT Edgeon keresztüli telepítésekor, győződjön meg arról, hogy a `iotedge` szolgáltatás megfelelően van konfigurálva és fut. A következő dokumentumok hasznosak lehetnek a Azure IoT Edge kapcsolatos problémák elhárításához:
- [A Azure IoT Edge kapcsolatos gyakori problémák és megoldások](../iot-edge/troubleshoot-common-errors.md).
- [A IoT Edge eszköz hibáinak megoldása](../iot-edge/troubleshoot.md)

## <a name="docker-command-errors"></a>Docker-parancs hibái

Ha bármilyen parancshoz hibaüzenetet kap, ellenőrizze, `docker` hogy fut-e a Docker szolgáltatás, és emelt szintű engedélyekkel próbálja meg futtatni.

Linux rendszeren például a következő hibaüzenet jelenhet meg a parancsok futtatásakor `docker` :

```output
Cannot connect to the Docker daemon. Is the docker daemon running on this host?
```

Ha ezt a hibaüzenetet Linux rendszeren kapja meg, próbálja meg futtatni a következővel megegyező parancsokat `sudo` . Ha ez nem sikerül, ellenőrizze, hogy fut-e a Docker szolgáltatás, és szükség esetén indítsa el.

```bash
sudo systemctl status docker
sudo systemctl start docker
```

Windows rendszeren ellenőrizze, hogy rendszergazdaként elindítja-e a PowerShellt vagy a parancssort.

## <a name="azure-sql-edge-container-startup-errors"></a>Azure SQL Edge-tároló indítási hibái

Ha az SQL Edge-tárolót nem sikerül futtatni, próbálja ki a következő teszteket:

- Ha Azure IoT Edge használ, győződjön meg arról, hogy a modul rendszerképei sikeresen letöltődnek, és a környezeti változók és a tároló-létrehozási beállítások helyesen vannak megadva a modul jegyzékfájljában.

- Ha Docker-vagy kubernetes-alapú telepítést használ, győződjön meg arról, hogy a `docker run` parancs megfelelően van formázva. További információkért lásd: az [Azure SQL Edge üzembe helyezése a Docker-vel](disconnected-deployment.md) és az [Azure SQL Edge-tároló üzembe helyezése a Kubernetes-ben](deploy-kubernetes.md).

- Ha olyan hibaüzenetet kap, mint például `failed to create endpoint CONTAINER_NAME on network bridge. Error starting proxy: listen tcp 0.0.0.0:1433 bind: address already in use.` a, a 1433-es tároló-portot egy már használatban lévő portra kísérli meg leképezni. Ez akkor fordulhat elő, ha az SQL Edge szolgáltatást helyileg futtatja a gazdagépen. Akkor is előfordulhat, ha két SQL Edge-tárolót indít el, és mindkettőt ugyanarra a gazdagép-portra próbálja hozzárendelni. Ha ez történik, a `-p` paraméter használatával rendelje hozzá a 1433-es tároló-portot egy másik gazdagép-porthoz. Például: 

    ```bash
    sudo docker run --cap-add SYS_PTRACE -e 'ACCEPT_EULA=1' -e 'MSSQL_SA_PASSWORD=yourStrong(!)Password' -p 1433:1433 --name azuresqledge -d mcr.microsoft.com/azure-sql-edge-developer.
    ```

- Ha hibaüzenet jelenik meg `Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get http://%2Fvar%2Frun%2Fdocker.sock/v1.30tdout=1&tail=all: dial unix /var/run/docker.sock: connect: permission denied` , például amikor egy tárolót próbál elindítani, adja hozzá a felhasználót az Ubuntu Docker-csoportjához. Ezután jelentkezzen be újra, és jelentkezzen be újra, mivel ez a változás hatással lesz az új munkamenetekre. 

   ```bash
    usermod -aG docker $USER
   ```

- Ellenőrizze, hogy van-e hibaüzenet a tárolóból.

   ```bash
   docker logs e69e056c702d
   ```

- Ha tároló-felügyeleti szoftvert használ, győződjön meg arról, hogy az támogatja a root-ként futó tároló-folyamatokat. A tárolóban lévő sqlservr folyamat root-ként fut.

- Alapértelmezés szerint az Azure SQL Edge-tárolók nem root nevű felhasználóként futnak `mssql` . Ha csatlakoztatási pontokat vagy adatköteteket használ az adatmegőrzéshez, ellenőrizze, hogy a `mssql` felhasználó rendelkezik-e megfelelő engedélyekkel a köteten. További információ: [Futtatás nem gyökérszintű felhasználóként](configure.md#run-azure-sql-edge-as-non-root-user) és [adatok](configure.md#persist-your-data)megőrzése.

- Ha az SQL Edge Docker-tároló azonnal kilép az indítás után, ellenőrizze a Docker-naplókat. Ha a Windows PowerShellt használja a `docker run` paranccsal, idézőjelek helyett használjon idézőjeleket. A PowerShell Core használatával egyetlen idézőjelet használjon.

- Tekintse át az [SQL Edge-hibák naplóit](#errorlogs).

## <a name="sql-edge-connection-failures"></a>SQL Edge-kapcsolatok hibái

Ha nem tud csatlakozni a tárolóban futó SQL Edge-példányhoz, próbálja meg a következő teszteket:

- Ellenőrizze, hogy az SQL Edge-tároló fut-e a kimenet **állapot** oszlopának megtekintésével `docker ps -a` . Ha nem, használja `docker start <Container ID>` a parancsot az alkalmazás elindításához.

- Ha nem alapértelmezett gazdagép-portra (1433) van leképezve, győződjön meg arról, hogy a portot adja meg a kapcsolódási karakterláncban. A port leképezését a kimenet **portok** oszlopában tekintheti meg `docker ps -a` . Az Azure SQL Edge-hez való csatlakozással kapcsolatos további információkért lásd: [csatlakozás és lekérdezés az Azure SQL Edge](connect.md) használatával

- Ha korábban már telepítette az SQL Edge-t a leképezett adatmennyiséggel vagy az adatmennyiség-tárolóval, és most már a meglévő leképezett adatmennyiséget vagy az adatmennyiség-tárolót használja, az SQL Edge figyelmen kívül hagyja a `MSSQL_SA_PASSWORD` környezeti változó értékét. Ehelyett a korábban konfigurált SA felhasználói jelszót használja a rendszer. Ez azért történik, mert az SQL Edge újrahasznosítja a meglévő főadatbázis-fájlokat a leképezett köteten vagy az adatkötet-tárolóban. Ha ezt a problémát futtatja, a következő lehetőségeket használhatja:

    - Csatlakozás a korábban használt jelszóval, ha még elérhető.
    - Konfigurálja az SQL Edge-t egy másik csatlakoztatott kötet vagy adatkötet-tároló használatára.
    - Távolítsa el a meglévő főadatbázis-fájlokat (Master. mdf és mastlog. mdf) a leképezett kötetről vagy az adatkötet-tárolóból.

- Tekintse át az [SQL Edge-hibák naplóit](#errorlogs).

## <a name="sql-edge-setup-and-error-logs"></a><a id="errorlogs"></a> SQL Edge telepítési és hibanapló

Alapértelmezés szerint az SQL Edge-hibák naplói a tárolóban található **/var/opt/MSSQL/log** könyvtárban találhatók, és a következő módokon érhetők el:

- Ha a tároló létrehozásakor a **/var/opt/MSSQL** csatlakoztatta a gazdagéphez, akkor ehelyett a gazdagépen lévő leképezési útvonalon tekintse meg a **napló** alkönyvtárát.
- Interaktív parancssor használatával a tárolóhoz való kapcsolódáshoz. Ha a tároló nem fut, először indítsa el a tárolót. Ezután használjon egy interaktív parancssort a naplók vizsgálatához. A tároló-azonosítót a parancs futtatásával kérheti le `docker ps` .

    ```bash
    docker start <ContainerID>
    docker exec -it <ContainerID> "/bin/bash"
    ```

    Futtassa a következő parancsokat a tárolón belüli bash-munkamenetből:

    ```bash
    cd /var/opt/mssql/log
    cat errorlog
    ```
- Ha az SQL Edge-tároló működik és fut, és az ügyféleszközök használatával tud csatlakozni a példányhoz, akkor a tárolt eljárással `sp_readerrorlog` elolvashatja az SQL Edge-hibanapló tartalmát.

## <a name="execute-commands-in-a-container"></a>Parancsok végrehajtása egy tárolóban

Ha rendelkezik futó tárolóval, parancsokat futtathat a tárolóban egy gazdagép terminálján belül.

A tároló AZONOSÍTÓjának lekéréséhez futtassa a következőt:

```bash
docker ps -a
```

Bash-terminál elindítása a tároló futtatásakor:

```bash
docker exec -it <Container ID> /bin/bash
```

Most futtathatja a parancsokat úgy, mintha a tárolón belül futtatja őket a terminálon. Ha elkészült, írja be a következőt: `exit` . Ez kilép az interaktív parancs-munkamenetből, de a tároló továbbra is fut.

### <a name="enabling-verbose-logging"></a>Részletes naplózás engedélyezése

Ha az adatfolyam-kezelő motor alapértelmezett naplózási szintje nem biztosít elegendő információt, az adatfolyam-kezelő motor hibakeresési naplózása az SQL Edge-ben is engedélyezhető. A hibakeresési naplózás engedélyezéséhez adja hozzá a `RuntimeLogLevel=debug` környezeti változót az SQL Edge-telepítéshez. A hibakeresési naplózás engedélyezése után próbálja meg reprodukálni a problémát, és keresse meg a megfelelő üzeneteket vagy kivételeket a naplókban. 

> [!NOTE]
> A részletes naplózási beállítást csak hibaelhárításhoz kell használni, és nem a szokásos üzemi számítási feladatokhoz. 


## <a name="next-steps"></a>Következő lépések

- [Machine Learning és mesterséges intelligencia az SQL Edge ONNX](onnx-overview.md)
- [Adatfolyamok az Azure SQL Edge-ben](stream-data.md)
- [Adatok megőrzése és tisztítása](data-retention-overview.md)
- [Az időbeli hézagok kitöltése és az imputált hiányzó értékek](imputing-missing-values.md)







