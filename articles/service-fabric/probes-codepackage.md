---
title: Azure Service Fabric-mintavételek
description: Az Azure Service Fabricban az alkalmazás-és szolgáltatás jegyzékfájljának használatával modellezhető az élő mintavétel.
ms.topic: conceptual
author: tugup
ms.author: tugup
ms.date: 3/12/2020
ms.openlocfilehash: 07a1b836ca7ea79244e303f54654dfcaa6e5fcb9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "82137586"
---
# <a name="liveness-probe"></a>Élő mintavétel
Az 7,1-es verziótól kezdődően az Azure Service Fabric támogatja az élő mintavételi mechanizmust a [tároló][containers-introduction-link] alkalmazások számára. Az élettartam-mintavétel segít jelenteni egy olyan tároló alkalmazás élettartamát, amely akkor indul újra, ha nem válaszol gyorsan.
Ez a cikk áttekintést nyújt arról, hogyan határozhat meg egy élő mintavételt jegyzékfájlok használatával.

Mielőtt folytatná ezt a cikket, ismerkedjen meg a [Service Fabric alkalmazás modelljével][application-model-link] és a [Service Fabric üzemeltetési modellel][hosting-model-link].

> [!NOTE]
> Az élettartam-mintavétel csak NAT hálózati módban lévő tárolók esetén támogatott.

## <a name="semantics"></a>Szemantika
A tárolók esetében csak egy élettartam-mintavételt adhat meg, és a következő mezők használatával szabályozhatja a viselkedését:

* `initialDelaySeconds`: Az első késleltetés másodpercben a mintavétel a tároló elindítása utáni végrehajtásának megkezdéséhez. A támogatott érték **int**. Az alapértelmezett érték 0, a minimum pedig 0.

* `timeoutSeconds`: Az az időtartam másodpercben, amely után a mintavétel sikertelen volt, ha nem fejeződött be sikeresen. A támogatott érték **int**. Az alapértelmezett érték 1, a minimum pedig 1.

* `periodSeconds`: A mintavétel gyakoriságának meghatározásához szükséges idő másodpercben. A támogatott érték **int**. Az alapértelmezett érték 10, a minimum pedig 1.

* `failureThreshold`: Ha elérjük ezt az értéket, a tároló újra fog indulni. A támogatott érték **int**. Az alapértelmezett érték 3, a minimum pedig 1.

* `successThreshold`: Hiba esetén a mintavétel sikeresnek tekintendő, ezért az érték sikeres futtatására van szükséges. A támogatott érték **int**. Az alapértelmezett érték 1, a minimum pedig 1.

Legfeljebb egy mintavételt lehet egyszerre egy tárolóba. Ha a mintavétel nem fejeződik be a **timeoutSeconds** megadott időpontban, várjon, és számoljon a **failureThreshold** felé. 

Emellett Service Fabric a következő mintavételi [állapot-jelentéseket][health-introduction-link] fogja felvenni a **DeployedServicePackage**:

* `OK`: A mintavétel sikeres a **successThreshold**-ben beállított értéknél.

* `Error`: A mintavételi **failureCount**  ==   **failureThreshold** a tároló újraindítása előtt.

* `Warning`: 
    * A mintavétel meghiúsul, és a **failureCount**  <  **failureThreshold**. Ez az állapotjelentés addig marad, amíg a **failureCount** el nem éri a **FailureThreshold** vagy a **successThreshold** értékben beállított értéket.
    * A hiba után a figyelmeztetés továbbra is a frissített egymást követő sikeres sikereket követően marad.

## <a name="specifying-a-liveness-probe"></a>Az élő mintavétel meghatározása

A mintavételt a **ServiceManifestImport** alatti ApplicationManifest.xml fájlban adhatja meg.

A mintavétel a következők bármelyikéhez tartozhat:

* HTTP
* TCP
* Exec 

### <a name="http-probe"></a>HTTP-mintavétel

HTTP-mintavétel esetén a Service Fabric HTTP-kérést küld a megadott portra és elérési útra. A 200 értéknél nagyobb vagy azzal egyenlő visszatérési kód, amely kisebb, mint 400, sikeres.

Íme egy példa a HTTP-mintavétel megadására:

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <HttpGet Path="/" Port="8081" Scheme="http">
              <HttpHeader Name="Foo" Value="Val"/>
              <HttpHeader Name="Bar" Value="val1"/>
            </HttpGet>
          </Probe>
        </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

A HTTP-mintavételhez további tulajdonságok is megadhatók:

* `path`: A HTTP-kérelemben használandó elérési út.

* `port`: A mintavételhez használandó port. Ez a tulajdonság kötelező. A tartomány értéke 1 – 65535.

* `scheme`: A csomaghoz való kapcsolódáshoz használandó séma. Ha ez a tulajdonság HTTPS-értékre van állítva, a rendszer kihagyja a tanúsítvány ellenőrzését. Az alapértelmezett beállítás a HTTP.

* `httpHeader`: A kérelemben beállított fejlécek. Több fejlécet is megadhat.

* `host`: A gazdagép IP-címe, amelyhez csatlakozni szeretne.

### <a name="tcp-probe"></a>TCP-mintavétel

TCP-mintavétel esetén a Service Fabric a megadott port használatával megpróbál megnyitni egy szoftvercsatornát a tárolón. Ha tud kapcsolatot létesíteni, a mintavétel sikeresnek minősül. Íme egy példa arra, hogyan lehet olyan mintavételt megadni, amely TCP-szoftvercsatornát használ:

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <TcpSocket Port="8081"/>
          </Probe>
        </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

### <a name="exec-probe"></a>Exec mintavétel

Ez a mintavétel egy **exec** parancsot ad ki a tárolóba, és megvárja, amíg a parancs befejeződik.

> [!NOTE]
> Az **exec** parancs vesszővel tagolt karakterláncot használ. A következő példában szereplő parancs egy Linux-tárolón fog működni.
> Ha Windows-tárolót próbál meg felvenni, használja a **cmd parancsot**.

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <Exec>
              <Command>ping,-c,2,localhost</Command>
            </Exec>
          </Probe>        
       </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

## <a name="next-steps"></a>Következő lépések
A kapcsolódó információk a következő cikkben találhatók:
* [Service Fabric és tárolók][containers-introduction-link]

<!-- Links -->
[containers-introduction-link]: service-fabric-containers-overview.md
[health-introduction-link]: service-fabric-health-introduction.md
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md

