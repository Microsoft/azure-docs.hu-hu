---
title: OPC-közzétevő futtatása – Azure | Microsoft Docs
description: Ez a cikk az OPC-közzétevő futtatását és hibakeresését ismerteti. Emellett a teljesítménnyel és a memóriával kapcsolatos szempontokat is tárgyalja.
author: dominicbetts
ms.author: dobett
ms.date: 06/10/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: f7d6581a1892ebd74a1adba5c09c0af9d3cf9d43
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105646118"
---
# <a name="run-opc-publisher"></a>Az OPC Publisher futtatása

> [!IMPORTANT]
> A cikk frissítését követően tekintse meg az [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) a legfrissebb tartalomhoz című cikket.

Ez a cikk az ad debug OPC-közzétevő futtatását ismerteti. Emellett a teljesítménnyel és a memóriával kapcsolatos szempontokat is tárgyalja.

## <a name="command-line-options"></a>Parancssori kapcsolók

Az alkalmazás használata a parancssori kapcsoló használatával jelenik meg a `--help` következő módon:

```sh/cmd
Current directory is: /appdata
Log file is: <hostname>-publisher.log
Log level is: info

OPC Publisher V2.3.0
Informational version: V2.3.0+Branch.develop_hans_methodlog.Sha.0985e54f01a0b0d7f143b1248936022ea5d749f9

Usage: opcpublisher.exe <applicationname> [<IoT Hubconnectionstring>] [<options>]

OPC Edge Publisher to subscribe to configured OPC UA servers and send telemetry to Azure IoT Hub.
To exit the application, just press CTRL-C while it is running.

applicationname: the OPC UA application name to use, required
                  The application name is also used to register the publisher under this name in the
                  IoT Hub device registry.

IoT Hubconnectionstring: the IoT Hub owner connectionstring, optional

There are a couple of environment variables which can be used to control the application:
_HUB_CS: sets the IoT Hub owner connectionstring
_GW_LOGP: sets the filename of the log file to use
_TPC_SP: sets the path to store certificates of trusted stations
_GW_PNFP: sets the filename of the publishing configuration file

Command line arguments overrule environment variable settings.

Options:
      --pf, --publishfile=VALUE
                              the filename to configure the nodes to publish.
                                Default: '/appdata/publishednodes.json'
      --tc, --telemetryconfigfile=VALUE
                              the filename to configure the ingested telemetry
                                Default: ''
  -s, --site=VALUE           the site OPC Publisher is working in. if specified
                                this domain is appended (delimited by a ':' to
                                the 'ApplicationURI' property when telemetry is
                                sent to IoT Hub.
                                The value must follow the syntactical rules of a
                                DNS hostname.
                                Default: not set
      --ic, --iotcentral     publisher will send OPC UA data in IoTCentral
                                compatible format (DisplayName of a node is used
                                as key, this key is the Field name in IoTCentral)
                                . you need to ensure that all DisplayName's are
                                unique. (Auto enables fetch display name)
                                Default: False
      --sw, --sessionconnectwait=VALUE
                              specify the wait time in seconds publisher is
                                trying to connect to disconnected endpoints and
                                starts monitoring unmonitored items
                                Min: 10
                                Default: 10
      --mq, --monitoreditemqueuecapacity=VALUE
                              specify how many notifications of monitored items
                                can be stored in the internal queue, if the data
                                can not be sent quick enough to IoT Hub
                                Min: 1024
                                Default: 8192
      --di, --diagnosticsinterval=VALUE
                              shows publisher diagnostic info at the specified
                                interval in seconds (need log level info).
                                -1 disables remote diagnostic log and diagnostic
                                output
                                0 disables diagnostic output
                                Default: 0
      --ns, --noshutdown=VALUE
                              same as runforever.
                                Default: False
      --rf, --runforever     publisher can not be stopped by pressing a key on
                                the console, but will run forever.
                                Default: False
      --lf, --logfile=VALUE  the filename of the logfile to use.
                                Default: './<hostname>-publisher.log'
      --lt, --logflushtimespan=VALUE
                              the timespan in seconds when the logfile should be
                                flushed.
                                Default: 00:00:30 sec
      --ll, --loglevel=VALUE the loglevel to use (allowed: fatal, error, warn,
                                info, debug, verbose).
                                Default: info
        --ih, --IoT Hubprotocol=VALUE
                              the protocol to use for communication with IoT Hub (
                                allowed values: Amqp, Http1, Amqp_WebSocket_Only,
                                  Amqp_Tcp_Only, Mqtt, Mqtt_WebSocket_Only, Mqtt_
                                Tcp_Only) or IoT EdgeHub (allowed values: Mqtt_
                                Tcp_Only, Amqp_Tcp_Only).
                                Default for IoT Hub: Mqtt_WebSocket_Only
                                Default for IoT EdgeHub: Amqp_Tcp_Only
      --ms, --IoT Hubmessagesize=VALUE
                              the max size of a message which can be send to
                                IoT Hub. when telemetry of this size is available
                                it will be sent.
                                0 will enforce immediate send when telemetry is
                                available
                                Min: 0
                                Max: 262144
                                Default: 262144
      --si, --IoT Hubsendinterval=VALUE
                              the interval in seconds when telemetry should be
                                send to IoT Hub. If 0, then only the
                                IoT Hubmessagesize parameter controls when
                                telemetry is sent.
                                Default: '10'
      --dc, --deviceconnectionstring=VALUE
                              if publisher is not able to register itself with
                                IoT Hub, you can create a device with name <
                                applicationname> manually and pass in the
                                connectionstring of this device.
                                Default: none
  -c, --connectionstring=VALUE
                              the IoT Hub owner connectionstring.
                                Default: none
      --hb, --heartbeatinterval=VALUE
                              the publisher is using this as default value in
                                seconds for the heartbeat interval setting of
                                nodes without
                                a heartbeat interval setting.
                                Default: 0
      --sf, --skipfirstevent=VALUE
                              the publisher is using this as default value for
                                the skip first event setting of nodes without
                                a skip first event setting.
                                Default: False
      --pn, --portnum=VALUE  the server port of the publisher OPC server
                                endpoint.
                                Default: 62222
      --pa, --path=VALUE     the enpoint URL path part of the publisher OPC
                                server endpoint.
                                Default: '/UA/Publisher'
      --lr, --ldsreginterval=VALUE
                              the LDS(-ME) registration interval in ms. If 0,
                                then the registration is disabled.
                                Default: 0
      --ol, --opcmaxstringlen=VALUE
                              the max length of a string opc can transmit/
                                receive.
                                Default: 131072
      --ot, --operationtimeout=VALUE
                              the operation timeout of the publisher OPC UA
                                client in ms.
                                Default: 120000
      --oi, --opcsamplinginterval=VALUE
                              the publisher is using this as default value in
                                milliseconds to request the servers to sample
                                the nodes with this interval
                                this value might be revised by the OPC UA
                                servers to a supported sampling interval.
                                please check the OPC UA specification for
                                details how this is handled by the OPC UA stack.
                                a negative value will set the sampling interval
                                to the publishing interval of the subscription
                                this node is on.
                                0 will configure the OPC UA server to sample in
                                the highest possible resolution and should be
                                taken with care.
                                Default: 1000
      --op, --opcpublishinginterval=VALUE
                              the publisher is using this as default value in
                                milliseconds for the publishing interval setting
                                of the subscriptions established to the OPC UA
                                servers.
                                please check the OPC UA specification for
                                details how this is handled by the OPC UA stack.
                                a value less than or equal zero will let the
                                server revise the publishing interval.
                                Default: 0
      --ct, --createsessiontimeout=VALUE
                              specify the timeout in seconds used when creating
                                a session to an endpoint. On unsuccessful
                                connection attemps a backoff up to 5 times the
                                specified timeout value is used.
                                Min: 1
                                Default: 10
      --ki, --keepaliveinterval=VALUE
                              specify the interval in seconds the publisher is
                                sending keep alive messages to the OPC servers
                                on the endpoints it is connected to.
                                Min: 2
                                Default: 2
      --kt, --keepalivethreshold=VALUE
                              specify the number of keep alive packets a server
                                can miss, before the session is disconneced
                                Min: 1
                                Default: 5
      --aa, --autoaccept     the publisher trusts all servers it is
                                establishing a connection to.
                                Default: False
      --tm, --trustmyself=VALUE
                              same as trustowncert.
                                Default: False
      --to, --trustowncert   the publisher certificate is put into the trusted
                                certificate store automatically.
                                Default: False
      --fd, --fetchdisplayname=VALUE
                              same as fetchname.
                                Default: False
      --fn, --fetchname      enable to read the display name of a published
                                node from the server. this will increase the
                                runtime.
                                Default: False
      --ss, --suppressedopcstatuscodes=VALUE
                              specifies the OPC UA status codes for which no
                                events should be generated.
                                Default: BadNoCommunication,
                                BadWaitingForInitialData
      --at, --appcertstoretype=VALUE
                              the own application cert store type.
                                (allowed values: Directory, X509Store)
                                Default: 'Directory'
      --ap, --appcertstorepath=VALUE
                              the path where the own application cert should be
                                stored
                                Default (depends on store type):
                                X509Store: 'CurrentUser\UA_MachineDefault'
                                Directory: 'pki/own'
      --tp, --trustedcertstorepath=VALUE
                              the path of the trusted cert store
                                Default: 'pki/trusted'
      --rp, --rejectedcertstorepath=VALUE
                              the path of the rejected cert store
                                Default 'pki/rejected'
      --ip, --issuercertstorepath=VALUE
                              the path of the trusted issuer cert store
                                Default 'pki/issuer'
      --csr                  show data to create a certificate signing request
                                Default 'False'
      --ab, --applicationcertbase64=VALUE
                              update/set this applications certificate with the
                                certificate passed in as bas64 string
      --af, --applicationcertfile=VALUE
                              update/set this applications certificate with the
                                certificate file specified
      --pb, --privatekeybase64=VALUE
                              initial provisioning of the application
                                certificate (with a PEM or PFX fomat) requires a
                                private key passed in as base64 string
      --pk, --privatekeyfile=VALUE
                              initial provisioning of the application
                                certificate (with a PEM or PFX fomat) requires a
                                private key passed in as file
      --cp, --certpassword=VALUE
                              the optional password for the PEM or PFX or the
                                installed application certificate
      --tb, --addtrustedcertbase64=VALUE
                              adds the certificate to the applications trusted
                                cert store passed in as base64 string (multiple
                                strings supported)
      --tf, --addtrustedcertfile=VALUE
                              adds the certificate file(s) to the applications
                                trusted cert store passed in as base64 string (
                                multiple filenames supported)
      --ib, --addissuercertbase64=VALUE
                              adds the specified issuer certificate to the
                                applications trusted issuer cert store passed in
                                as base64 string (multiple strings supported)
      --if, --addissuercertfile=VALUE
                              adds the specified issuer certificate file(s) to
                                the applications trusted issuer cert store (
                                multiple filenames supported)
      --rb, --updatecrlbase64=VALUE
                              update the CRL passed in as base64 string to the
                                corresponding cert store (trusted or trusted
                                issuer)
      --uc, --updatecrlfile=VALUE
                              update the CRL passed in as file to the
                                corresponding cert store (trusted or trusted
                                issuer)
      --rc, --removecert=VALUE
                              remove cert(s) with the given thumbprint(s) (
                                multiple thumbprints supported)
      --dt, --devicecertstoretype=VALUE
                              the IoT Hub device cert store type.
                                (allowed values: Directory, X509Store)
                                Default: X509Store
      --dp, --devicecertstorepath=VALUE
                              the path of the iot device cert store
                                Default Default (depends on store type):
                                X509Store: 'My'
                                Directory: 'CertificateStores/IoT Hub'
  -i, --install              register OPC Publisher with IoT Hub and then exits.
                                Default:  False
  -h, --help                 show this message and exit
      --st, --opcstacktracemask=VALUE
                              ignored, only supported for backward comaptibility.
      --sd, --shopfloordomain=VALUE
                              same as site option, only there for backward
                                compatibility
                                The value must follow the syntactical rules of a
                                DNS hostname.
                                Default: not set
      --vc, --verboseconsole=VALUE
                              ignored, only supported for backward comaptibility.
      --as, --autotrustservercerts=VALUE
                              same as autoaccept, only supported for backward
                                cmpatibility.
                                Default: False
      --tt, --trustedcertstoretype=VALUE
                              ignored, only supported for backward compatibility.
                                the trusted cert store will always reside in a
                                directory.
      --rt, --rejectedcertstoretype=VALUE
                              ignored, only supported for backward compatibility.
                                the rejected cert store will always reside in a
                                directory.
      --it, --issuercertstoretype=VALUE
                              ignored, only supported for backward compatibility.
                                the trusted issuer cert store will always
                                reside in a directory.
```

Általában csak az alkalmazás első futtatásakor adja meg az IoT Hub tulajdonosi kapcsolatok sztringjét. A rendszer titkosítja a kapcsolatok karakterláncát, és a platform tanúsítványtárolójában tárolja. A későbbi futtatások során az alkalmazás a tanúsítványtárolóból olvassa be a kapcsolatok karakterláncát. Ha minden futtatáskor megadja a kapcsolatok karakterláncát, akkor a rendszer eltávolítja és újból létrehozza a IoT Hub eszköz beállításjegyzékében az alkalmazáshoz létrehozott eszközt.

## <a name="run-natively-on-windows"></a>Natív Futtatás Windows rendszeren

Nyissa meg a **opcpublisher. SLN** projektet a Visual Studióval, hozza létre a megoldást, és tegye közzé. Az alkalmazást a következőképpen indíthatja el az Ön által közzétett **célként megadott könyvtárban** :

```cmd
dotnet opcpublisher.dll <applicationname> [<IoT Hubconnectionstring>] [options]
```

## <a name="use-a-self-built-container"></a>Saját építésű tároló használata

Készítse el saját tárolóját, és kezdje a következőképpen:

```sh/cmd
docker run <your-container-name> <applicationname> [<IoT Hubconnectionstring>] [options]
```

## <a name="use-a-container-from-microsoft-container-registry"></a>Tároló használata a Microsoft Container Registry

Létezik egy előre elkészített tároló a Microsoft Container Registryban. A következőképpen indíthatja el:

```sh/cmd
docker run mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

Ellenőrizze a [Docker hub](https://hub.docker.com/_/microsoft-iotedge-opc-publisher) -t a támogatott operációs rendszerek és processzor-architektúrák megtekintéséhez. Ha az operációs rendszer és a CPU architektúrája támogatott, a Docker automatikusan kiválasztja a megfelelő tárolót.

## <a name="run-as-an-azure-iot-edge-module"></a>Futtatás Azure IoT Edge modulként

Az OPC-közzétevő készen áll [Azure IoT Edge](../iot-edge/index.yml) modulként való használatra. Ha az OPC-közzétevőt IoT Edge modulként használja, az egyetlen támogatott átviteli protokoll **Amqp_Tcp_Only** és **Mqtt_Tcp_Only**.

Ha az OPC-közzétevőt modulként szeretné hozzáadni a IoT Edge üzembe helyezéséhez, lépjen a Azure Portal IoT Hub beállításaihoz, és végezze el a következő lépéseket:

1. Nyissa meg **IoT Edge** és hozza létre vagy válassza ki IoT Edge eszközét.
1. Válassza a **modulok beállítása** lehetőséget.
1. Válassza a **Hozzáadás** lehetőséget a **telepítési modulok** területen, majd **IoT Edge modult**.
1. A **név** mezőbe írja be a **közzétevőt**.
1. A **RENDSZERKÉP URI** mezőjébe írja be a `mcr.microsoft.com/iotedge/opc-publisher:<tag>`
1. A rendelkezésre álló címkék a [Docker hub](https://hub.docker.com/_/microsoft-iotedge-opc-publisher) -ban találhatók
1. Illessze be a következő JSON-t a **tároló létrehozási beállítások** mezőjébe:

    ```json
    {
        "Hostname": "publisher",
        "Cmd": [
            "--aa"
        ]
    }
    ```

    Ez a konfiguráció úgy konfigurálja a IoT Edget, hogy elindítson egy **Publisher** nevű tárolót az OPC közzétevő rendszerképének használatával. A tároló rendszerének állomásneve a **kiadó** értékre van állítva. Az OPC-közzétevőt a következő parancssori argumentummal kell meghívni: `--aa` . Ha ezt a lehetőséget választja, az OPC-közzétevő megbízik az OPC UA-kiszolgálók tanúsítványán, amelyhez csatlakozik. Bármely OPC-közzétevő parancssori kapcsolót használhat. Az egyetlen korlátozás az IoT Edge által támogatott **tároló-létrehozási beállítások** mérete.

1. Hagyja változatlanul a többi beállítást, és válassza a **Mentés** lehetőséget.
1. Ha az OPC-közzétevő kimenetét helyileg szeretné feldolgozni egy másik IoT Edge modullal, térjen vissza a **modulok beállítása** lapra. Ezután nyissa **meg az útvonalak megadása** lapot, és adjon hozzá egy új útvonalat, amely a következő JSON-hoz hasonlít:

    ```json
    {
      "routes": {
        "processingModuleToIoT Hub": "FROM /messages/modules/processingModule/outputs/* INTO $upstream",
        "opcPublisherToProcessingModule": "FROM /messages/modules/publisher INTO BrokeredEndpoint(\"/modules/processingModule/inputs/input1\")"
      }
    }
    ```

1. A **modulok beállítása** lapon kattintson a **tovább** gombra, amíg el nem éri a konfiguráció utolsó lapját.
1. Válassza a **Submit (Küldés** ) lehetőséget a konfiguráció IoT Edge való elküldéséhez.
1. Amikor elindította IoT Edget a peremhálózati eszközén, és a Docker-tároló **közzétevője** fut, az OPC-közzétevő naplózási kimenetét az vagy a Logfile (naplófájl) használatával vagy a naplófájl ellenőrzésével tekintheti meg `docker logs -f publisher` . Az előző példában a naplófájl felül van `d:\iiotegde\publisher-publisher.log` . Használhatja a [IOT-Edge-OPC-Publisher-Diagnostics eszközt](https://github.com/Azure-Samples/iot-edge-opc-publisher-diagnostics)is.

### <a name="make-the-configuration-files-accessible-on-the-host"></a>A konfigurációs fájlok elérhetővé tétele a gazdagépen

Ahhoz, hogy a IoT Edge modul konfigurációs fájljai elérhetők legyenek a gazdagép fájlrendszerében, használja a következő **tároló-létrehozási beállításokat**. A következő példa egy Linux-tárolókat használ a Windows rendszerhez:

```json
{
    "Hostname": "publisher",
    "Cmd": [
        "--pf=./pn.json",
        "--aa"
    ],
    "HostConfig": {
        "Binds": [
            "d:/iiotedge:/appdata"
        ]
    }
}
```

Ezekkel a beállításokkal az OPC-közzétevő beolvassa azokat a csomópontokat, amelyeket közzé kell tenni a fájlból, `./pn.json` és a tároló munkakönyvtára indítási értékre van állítva `/appdata` . Ezekkel a beállításokkal az OPC-közzétevő beolvassa a fájlt `/appdata/pn.json` a tárolóból a konfiguráció eléréséhez. A kapcsoló nélkül az `--pf` OPC-közzétevő megpróbálja beolvasni az alapértelmezett konfigurációs fájlt `./publishednodes.json` .

A rendszer az alapértelmezett nevet használja a naplófájlban, `publisher-publisher.log` `/appdata` és a `CertificateStores` könyvtárat is létrehozza ebben a címtárban.

Ahhoz, hogy az összes fájl elérhető legyen a gazdagép fájlrendszerében, a tároló konfigurációjának kötési csatlakoztatási kötetre van szüksége. A `d://iiotedge:/appdata` kötés leképezi a könyvtárat `/appdata` , amely a tároló indításának aktuális munkakönyvtára a gazdagép könyvtárába `d://iiotedge` . Ezen beállítás nélkül a tároló következő indításakor nem maradnak meg a fájlok adatfájlja.

Ha Windows-tárolókat futtat, akkor a paraméter szintaxisa `Binds` eltérő. A tároló indításakor a munkakönyvtár `c:\appdata` . Ha a konfigurációs fájlt a gazdagépen található könyvtárba szeretné helyezni `d:\iiotedge` , adja meg a következő leképezést a `HostConfig` szakaszban:

```json
"HostConfig": {
    "Binds": [
        "d:/iiotedge:c:/appdata"
    ]
}
```

Linux-tárolók Linux rendszeren való futtatásakor a paraméter szintaxisa `Binds` újra eltér. A tároló indításakor a munkakönyvtár `/appdata` . Ha a konfigurációs fájlt a gazdagépen található könyvtárba szeretné helyezni `/iiotedge` , adja meg a következő leképezést a `HostConfig` szakaszban:

```json
"HostConfig": {
    "Binds": [
        "/iiotedge:/appdata"
    ]
}
```

## <a name="considerations-when-using-a-container"></a>A tárolók használatakor megfontolandó szempontok

A következő részekben a tárolók használatakor figyelembe kell venni néhány dolgot:

### <a name="access-to-the-opc-publisher-opc-ua-server"></a>Hozzáférés az OPC Publisher OPC UA-kiszolgálóhoz

Alapértelmezés szerint az OPC Publisher OPC UA-kiszolgáló a 62222-es portot figyeli. Ha ezt a bejövő portot egy tárolóban szeretné elérhetővé tenni, használja a következő parancsot:

```sh/cmd
docker run -p 62222:62222 mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="enable-intercontainer-name-resolution"></a>A tároló névfeloldásának engedélyezése

Ha engedélyezni szeretné a névfeloldást a tárolón belül más tárolók számára, hozzon létre egy felhasználót a Docker Bridge Network definiálásával, és a kapcsoló használatával kapcsolja össze a tárolót a hálózattal `--network` . Rendeljen hozzá egy nevet a tárolóhoz a `--name` következő paranccsal:

```sh/cmd
docker network create -d bridge iot_edge
docker run --network iot_edge --name publisher mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

A tároló már elérhető az ugyanazon a hálózaton lévő `publisher` más tárolók nevével.

### <a name="access-other-systems-from-within-the-container"></a>Más rendszerek elérése a tárolón belülről

Az előző szakaszban ismertetett paraméterek használatával más tárolók is elérhetők. Ha az operációs rendszer, amelyen a Docker üzemel, a DNS engedélyezve van, majd minden olyan rendszerhez hozzáfér, amely ismeri a DNS működését.

A NetBIOS-névfeloldást használó hálózatokban engedélyezze a hozzáférést más rendszerekhez úgy, hogy a tárolót a `--add-host` kapcsolóval indítja el. Ezzel a beállítással hatékonyan adhat hozzá egy bejegyzést a tároló gazdagép fájljához:

```cmd/sh
docker run --add-host mydevbox:192.168.178.23  mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="assign-a-hostname"></a>Állomásnév kiosztása

Az OPC-közzétevő a tanúsítvány-és végpont-generáláshoz használt gép állomásnevét használja. A Docker véletlenszerűen kiválasztott állomásnevet választ, ha az egyiket nem a beállítás állította be `-h` . Az alábbi példa bemutatja, hogyan állíthatja be a tároló belső állomásnevét a következőre `publisher` :

```sh/cmd
docker run -h publisher mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="use-bind-mounts-shared-filesystem"></a>Kötési csatlakoztatások használata (megosztott fájlrendszer)

A tároló fájlrendszer használata helyett kiválaszthatja a gazdagép fájlrendszerét a konfigurációs adatok és a naplófájlok tárolásához. A beállítás konfigurálásához használja a `-v` `docker run` kötés csatlakoztatási módja lehetőséget.

## <a name="opc-ua-x509-certificates"></a>OPC UA X. 509 tanúsítványok

Az OPC UA X. 509 tanúsítványokat használ az OPC UA-ügyfél és-kiszolgáló hitelesítésére a kapcsolat létesítése és a közöttük lévő kommunikáció titkosítása érdekében. Az OPC-közzétevő az OPC UA-verem által karbantartott tanúsítványtárolókat használja az összes tanúsítvány kezeléséhez. Indításkor az OPC-közzétevő ellenőrzi, hogy van-e tanúsítvány önmagához. Ha nincs tanúsítvány a tanúsítványtárolóban, és az egyik nem lett átadva a parancssorban, az OPC-közzétevő létrehoz egy önaláírt tanúsítványt. További információ: a **InitApplicationSecurityAsync** metódusa `OpcApplicationConfigurationSecurity.cs` .

Az önaláírt tanúsítványok nem biztosítanak biztonságot, mivel azokat nem megbízható HITELESÍTÉSSZOLGÁLTATÓ írta alá.

Az OPC-közzétevő parancssori kapcsolókat biztosít a következőhöz:

- Az OPC-közzétevő által használt aktuális alkalmazás-tanúsítványhoz tartozó CSR-információ beolvasása.
- Az OPC-közzétevő kiépítése HITELESÍTÉSSZOLGÁLTATÓI aláírású tanúsítvánnyal.
- Hozzon létre OPC-közzétevőt egy új kulcspárt és a hozzá tartozó HITELESÍTÉSSZOLGÁLTATÓI aláírt tanúsítvánnyal.
- Tanúsítványok hozzáadása megbízható társ vagy megbízható kiállító tanúsítvány-tárolóhoz.
- Visszavont tanúsítványok listájának hozzáadása.
- Távolítson el egy tanúsítványt a megbízható társ vagy a megbízható kiállítók tanúsítványtárolóból.

Ezekkel a beállításokkal fájlok vagy Base64 kódolású karakterláncok használatával adhat át paramétereket.

Az összes tanúsítványtároló alapértelmezett tárolási típusa a fájlrendszer, amelyet a parancssori kapcsolók használatával módosíthat. Mivel a tároló nem biztosít állandó tárterületet a fájlrendszerében, másik áruházbeli típust kell választania. A Docker beállítás használatával megtarthatja `-v` a tanúsítványtárolókat a gazdagép fájlrendszerében vagy egy Docker-köteten. Ha Docker-kötetet használ, Base64 kódolású karakterláncokkal adhat át tanúsítványokat.

A futásidejű környezet befolyásolja a tanúsítványok megőrzésének módját. Ne hozzon létre új tanúsítványtárolókat minden alkalommal, amikor futtatja az alkalmazást:

- A Windows rendszeren natív módon futtatott alkalmazás-tanúsítványtároló nem használható, `Directory` mert a titkos kulcshoz való hozzáférés sikertelen. Ebben az esetben használja a kapcsolót `--at X509Store` .
- Linux Docker-tárolóként futtatva a tanúsítványtárolókat leképezheti a gazdagép fájlrendszerére a Docker Run kapcsolóval `-v <hostdirectory>:/appdata` . Ezzel a beállítással a tanúsítvány állandó lesz az alkalmazás futása során.
- Linux Docker-tárolóként fut, és X509-tárolót szeretne használni az alkalmazás tanúsítványához, használja a Docker Run kapcsolót `-v x509certstores:/root/.dotnet/corefx/cryptography/x509stores` és az alkalmazás lehetőséget. `--at X509Store`

## <a name="performance-and-memory-considerations"></a>Teljesítménnyel és memóriával kapcsolatos megfontolások

Ez a szakasz a memória és a teljesítmény kezelésének lehetőségeit ismerteti:

### <a name="command-line-parameters-to-control-performance-and-memory"></a>A teljesítmény és a memória vezérlésére szolgáló parancssori paraméterek

Az OPC-közzétevő futtatásakor ismernie kell a teljesítményre vonatkozó követelményeket, valamint a gazdagépen elérhető memória-erőforrásokat.

A memória és a teljesítmény egymástól függ, és a konfigurációtól függ, hogy hány csomópontot szeretne közzétenni. Győződjön meg arról, hogy a következő paraméterek megfelelnek a követelményeknek:

- IoT Hub küldési időköz: `--si`
- IoT Hub üzenet mérete (alapértelmezett `1` ): `--ms`
- Figyelt elemek várólistájának kapacitása: `--mq`

A `--mq` paraméter a belső várólista kapacitásának felső határát szabályozza, amely az összes OPC-csomópont értékének változási értesítéseit pufferbe helyezi. Ha az OPC-közzétevő nem tud üzeneteket küldeni IoT Hub elég gyors, akkor ez a várólista pufferbe helyezi az értesítéseket. A paraméterrel állítható be a pufferelt értesítések száma. Ha úgy látja, hogy a várólistában lévő elemek száma egyre növekszik a tesztek futtatásakor, az üzenetek elvesztésének elkerüléséhez tegye a következőket:

- Csökkentse a IoT Hub küldési időközt
- IoT Hub üzenet méretének növeléséhez

A `--si` paraméter arra kényszeríti az OPC-közzétevőt, hogy a megadott időközönként üzeneteket küldjön a IoT hubnak. Az OPC-közzétevő azonnal üzenetet küld, amint eléri a paraméter által megadott méretű üzenetet `--ms` , vagy ha eléri a paraméter által megadott időközt `--si` . Az üzenet méretének letiltásához használja a parancsot `--ms 0` . Ebben az esetben az OPC-közzétevő a lehető legnagyobb méretű IoT Hub 256 kB-os üzenetet használja a Batch-adatmennyiséghez.

A `--ms` paraméter lehetővé teszi a Batch-üzenetek küldését IoT hub. Az Ön által használt protokoll határozza meg, hogy a rendszer a hasznos adatok elküldésének tényleges időpontjához képest magas-e az üzenet küldésének IoT Hub. Ha a forgatókönyv lehetővé teszi az IoT Hub által betöltött adatmennyiség késleltetését, az OPC-közzétevőt úgy konfigurálja, hogy az 256 kB-os legnagyobb méretű üzenetet használja.

Az OPC-közzétevő éles környezetben való használata előtt tesztelje a teljesítményt és a memóriát a termelési körülmények között. A `--di` paraméterrel megadhatja azt az időközt (másodpercben), ameddig az OPC-közzétevő diagnosztikai adatokat ír.

### <a name="test-measurements"></a>Tesztelési mérések

A következő példában a diagnosztika különböző értékekkel `--si` és `--ms` paraméterekkel teszi közzé a 500 csomópontokat, amelyek 1 másodperces OPC közzétételi intervallummal rendelkeznek.  A teszt az OPC-közzétevő hibakeresési felépítését használta a Windows 10 rendszerre natívan 120 másodpercig. A IoT Hub protokoll az alapértelmezett MQTT protokoll volt.

#### <a name="default-configuration---si-10---ms-262144"></a>Alapértelmezett konfiguráció (--si 10--MS 262144)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:33:05 (started @ 26.10.2017 15:31:09)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 0
monitored item notifications enqueued: 54363
monitored item notifications enqueue failure: 0
monitored item notifications dequeued: 54363
---------------------------------
messages sent to IoT Hub: 109
last successful msg sent @: 26.10.2017 15:33:04
bytes sent to IoT Hub: 12709429
avg msg size: 116600
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 90
--si setting: 10
--ms setting: 262144
--ih setting: Mqtt
==========================================================================
```

Az alapértelmezett konfiguráció 10 másodpercenként küldi az adatokat IoT Hub, vagy ha a IoT Hub számára elérhető 256 kB. Ez a konfiguráció körülbelül 10 másodperces késleltetést tesz fel, de a nagy méretű üzenetek mérete miatt a rendszer az adatvesztés legalacsonyabb valószínűséggel rendelkezik. A diagnosztika kimenete azt mutatja, hogy nincsenek elveszett OPC csomópont-frissítések: `monitored item notifications enqueue failure: 0` .

#### <a name="constant-send-interval---si-1---ms-0"></a>Állandó küldési időköz (--si 1--MS 0)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:35:59 (started @ 26.10.2017 15:34:03)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 0
monitored item notifications enqueued: 54243
monitored item notifications enqueue failure: 0
monitored item notifications dequeued: 54243
---------------------------------
messages sent to IoT Hub: 109
last successful msg sent @: 26.10.2017 15:35:59
bytes sent to IoT Hub: 12683836
avg msg size: 116365
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 90
--si setting: 1
--ms setting: 0
--ih setting: Mqtt
==========================================================================
```

Ha az üzenet mérete 0-ra van állítva, akkor az OPC-közzétevő belsőleg batchs-adatkötegeket használ a legnagyobb támogatott IoT Hub üzenet méretével, amely 256 kB. A diagnosztikai kimenet az üzenetek átlagos méretének 115 019 bájtot jeleníti meg. Ebben a konfigurációban az OPC-közzétevő nem veszíti el az OPC-csomópontok összes frissítését, és az alapértelmezett értékhez képest alacsonyabb késéssel rendelkezik.

### <a name="send-each-opc-node-value-update---si-0---ms-0"></a>Minden OPC csomópont-érték frissítésének elküldése (--si 0--MS 0)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:39:33 (started @ 26.10.2017 15:37:37)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 8184
monitored item notifications enqueued: 54232
monitored item notifications enqueue failure: 44624
monitored item notifications dequeued: 1424
---------------------------------
messages sent to IoT Hub: 1423
last successful msg sent @: 26.10.2017 15:39:33
bytes sent to IoT Hub: 333046
avg msg size: 234
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 96
--si setting: 0
--ms setting: 0
--ih setting: Mqtt
==========================================================================
```

Ez a konfiguráció minden egyes OPC csomópont-értékre megváltoztat egy üzenetet, hogy IoT Hub. A diagnosztika azt mutatja, hogy az üzenet átlagos mérete 234 bájt, ami kicsi. Ennek a konfigurációnak az az előnye, hogy az OPC-közzétevő nem hoz létre késést. Az elveszett OPC Node Value Updates ( `monitored item notifications enqueue failure: 44624` ) értéke magas, ami lehetővé teszi, hogy ez a konfiguráció a nagy mennyiségű telemetria közzéteendő forgatókönyvek esetében ne legyen megfelelő.

### <a name="maximum-batching---si-0---ms-262144"></a>Maximális feldolgozás (--si 0--MS 262144)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:42:55 (started @ 26.10.2017 15:41:00)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 0
monitored item notifications enqueued: 54137
monitored item notifications enqueue failure: 0
monitored item notifications dequeued: 54137
---------------------------------
messages sent to IoT Hub: 48
last successful msg sent @: 26.10.2017 15:42:55
bytes sent to IoT Hub: 12565544
avg msg size: 261782
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 90
--si setting: 0
--ms setting: 262144
--ih setting: Mqtt
==========================================================================
```

Ebben a konfigurációban a lehető legtöbb OPC-csomópont-érték frissül. A maximális IoT Hub üzenet mérete 256 kB, amely itt van konfigurálva. Nincs kért küldési időköz, ami azt jelenti, hogy a betöltéshez IoT Hub adatmennyiség határozza meg a késést. Ez a konfiguráció a legkisebb valószínűséggel elveszíti az OPC-csomópontok értékeit, és alkalmas a nagy számú csomópont közzétételére. Ha ezt a konfigurációt használja, győződjön meg arról, hogy a forgatókönyv nem rendelkezik olyan feltételekkel, amelyek nagy késleltetést mutatnak, ha az 256 kB méretű üzenet nem érhető el.

## <a name="debug-the-application"></a>Az alkalmazás hibakeresése

Az alkalmazás hibakereséséhez nyissa meg a **opcpublisher. SLN** Solution fájlt a Visual Studióval, és használja a Visual Studio hibakereső eszközeit.

Ha el kell érnie az OPC UA-kiszolgálót az OPC-közzétevőben, ellenőrizze, hogy a tűzfal engedélyezi-e a hozzáférést a kiszolgáló által figyelt porthoz. Az alapértelmezett port a: 62222.

## <a name="control-the-application-remotely"></a>Az alkalmazás távoli vezérlése

A csomópontok közzétételre való konfigurálása IoT Hub közvetlen módszerek használatával végezhető el.

Az OPC-közzétevő néhány további IoT Hub közvetlen metódus-hívást hajt végre a következő olvasáshoz:

- Általános információk.
- Az OPC-munkamenetekre,-előfizetésekre és a figyelt elemekre vonatkozó diagnosztikai információk.
- IoT Hub üzenetekre és eseményekre vonatkozó diagnosztikai információk.
- Az indítási napló.
- A napló utolsó 100 sora.
- Állítsa le az alkalmazást.

A következő GitHub-Tárházak olyan eszközöket tartalmaznak, amelyekkel [konfigurálhatja a csomópontokat](https://github.com/Azure-Samples/iot-edge-opc-publisher-nodeconfiguration) , és [elolvashatja a diagnosztikai adatokat](https://github.com/Azure-Samples/iot-edge-opc-publisher-diagnostics). Mindkét eszköz tárolóként is elérhető a Docker hub-ban.

## <a name="use-a-sample-opc-ua-server"></a>Minta OPC UA-kiszolgáló használata

Ha nem rendelkezik valós OPC UA-kiszolgálóval, a kezdéshez használhatja a [minta OPC ua PLC](https://github.com/Azure-Samples/iot-edge-opc-plc) -t. Ez a minta PLC a Docker hub-on is elérhető.

Számos címkét valósít meg, amelyek véletlenszerű adatmennyiségeket és címkéket eredményeznek. Kiterjesztheti a mintát, ha további címkéző értékeket kell szimulálnia.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte az OPC-közzétevő futtatását, a javasolt következő lépések az [OPC Twin](overview-opc-twin.md) és az [OPC](overview-opc-vault.md)-tároló megismerése.