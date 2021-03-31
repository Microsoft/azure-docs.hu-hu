---
title: A konzul áttekintése
description: A konzul áttekintésének beszerzése
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 77cb6a693c5a73d0498b0acf9bc4ad8cc9f8f32f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "83774010"
---
# <a name="consul"></a>Konzul

## <a name="overview"></a>Áttekintés

A [konzul][consul] egy több adatközpontot támogató Service hálózatkezelési megoldás, amely a szolgáltatásokhoz való kapcsolódást és biztonságos szolgáltatásokat biztosít a futásidejű platformokon. A [kapcsolat][consul-features] a Service Mesh képességeket biztosító összetevő.

## <a name="architecture"></a>Architektúra

A konzul olyan adatsíkokat biztosít, amely alapértelmezés szerint a [megbízott][envoy-proxy]-alapú [oldalkocsiból][consul-sidecar] áll. A konzul egy csatlakoztatható proxy architektúrával rendelkezik. Ezek az intelligens proxyk a hálózott alkalmazások és munkaterhelések összes hálózati forgalmát vezérlik.

A vezérlő síkja a következő [összetevőkön][consul-architecture]keresztül kezeli a konfigurációt és a szabályzatot:

- **Kiszolgáló** – a konzuli fürt állapotát fenntartó kiszolgáló módban futó konzuli ügynök.

- **Client** – egy egyszerű ügyfél módban futó konzuli ügynök. Minden számítási csomópontnak rendelkeznie kell futtató ügynökkel. Ez az ügyfél a munkaterhelések és a konzuli konfiguráció közötti konfigurációt és házirendet közvetíti. 

A következő architektúra-diagram azt mutatja be, hogy az adatsíkon és a vezérlési síkon lévő különböző összetevők hogyan hatnak egymásra.

![A konzul összetevőinek és architektúrájának áttekintése.](media/servicemesh/consul/about-architecture.png)


## <a name="selection-criteria"></a>Kiválasztási feltételek

Fontos megérteni és figyelembe venni a következő területeket a konzul kiértékelése során a számítási feladatokhoz:

- [Konzuli alapelvek](#consul-principles)
- [Képességek](#capabilities)
- [Forgatókönyvek](#scenarios)


### <a name="consul-principles"></a>Konzuli alapelvek

A következő alapelvek a konzuli projektet [ismertetik][consul-principles] :

- **API-vezérelt** – az összes konfiguráció és házirend kodifikálása.

- Bárhonnan csatlakoztatható munkaterhelések **futtatása és összekapcsolhatók** a futásidejű platformok (Kubernetes, virtuális gépek, kiszolgáló nélküli) között.

- **Kiterjesztheti és integrálhatja a** biztonságos csatlakoztatási feladatokat az infrastruktúrán keresztül.


### <a name="capabilities"></a>Képességek

A konzul a következő funkciókat biztosítja:

- **Mesh** – átjáró (több adatközpont), virtuális gépek (fürtön kívüli csomópontok), szolgáltatás-szinkronizálás, beépített hibakeresési lehetőség

- **Proxys** – megbízott, beépített proxy, csatlakoztatható, L4 proxy elérhető Windows-alapú számítási feladatokhoz

- **Traffic Management** – útválasztás, felosztás, feloldás

- **Szabályzat** – szándékok, ACL-ek

- **Biztonság** – engedélyezés, hitelesítés, TITKOSÍTÁS, SPIFFE-alapú identitások, külső hitelesítésszolgáltató (tároló), tanúsítványkezelő és rotációs

- **Megfigyelhetőség** – mérőszámok, kezelőfelületi irányítópult, Prometheus, grafana


### <a name="scenarios"></a>Forgatókönyvek

A konzul kiválóan alkalmas a következő helyzetekben való használatra:

- A meglévő konzuli csatlakoztatott munkaterhelések kiterjesztése

- Megfelelőségi követelmények a Tanúsítványkezelők között

- Több fürtön keresztüli szolgáltatás hálója

- A szolgáltatás hálójának részét képező virtuális gépeken alapuló számítási feladatok



## <a name="next-steps"></a>Következő lépések

Az alábbi dokumentáció ismerteti, hogyan telepítheti a konzult az Azure Kubernetes Service-ben (ak):

> [!div class="nextstepaction"]
> [A konzul telepítése az Azure Kubernetes szolgáltatásban (ak)][consul-install]

A konzul funkcióit és architektúráját továbbra is megismerheti:

- [Konzuli Első lépések oktatóanyagok][consul-getting-started]
- [Konzuli funkciók][consul-features]
- [Konzuli architektúra][consul-architecture]
- [Konzul – a kapcsolat működése][consul-how-connect-works]

<!-- LINKS - external -->
[consul]: https://www.consul.io/mesh.html
[consul-features]: https://www.consul.io/docs/connect/index.html
[consul-architecture]: https://www.consul.io/docs/internals/architecture.html
[consul-sidecar]: https://www.consul.io/docs/connect/proxies.html
[consul-how-connect-works]: https://www.consul.io/docs/connect/connect-internals.html
[consul-principles]: https://www.consul.io/
[consul-getting-started]:https://learn.hashicorp.com/consul?track=gs-consul-service-mesh#gs-consul-service-mesh

[envoy-proxy]: https://www.envoyproxy.io/
[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/

<!-- LINKS - internal -->
[consul-install]: ./servicemesh-consul-install.md
