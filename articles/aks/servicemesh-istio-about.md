---
title: A Istio áttekintése
description: A Istio áttekintésének beszerzése
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 8518e30a54c2486abf84cd9ac026cc4dccb3fa84
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "77593900"
---
# <a name="istio"></a>Istio

## <a name="overview"></a>Áttekintés

A [Istio][istio] egy teljes funkcionalitású, testreszabható és bővíthető szolgáltatás háló.

## <a name="architecture"></a>Architektúra

A Istio egy, a [megbízottat][envoy-proxy]-alapú oldalkocsiból álló adatsíkokat biztosít. Ezek az intelligens proxyk a hálózott alkalmazások és munkaterhelések összes hálózati forgalmát vezérlik.

A vezérlő síkja a következő [összetevőkön][what-is-istio]keresztül kezeli a konfigurációt, a szabályzatot és a telemetria:

- **Keverő** – a hozzáférés-vezérlési és használati szabályzatok betartatása. A telemetria gyűjti a [Prometheus][prometheus]-ba leküldett proxykat.

- **Pilot** – szolgáltatás-felderítési és forgalomirányítási házirend/konfiguráció a proxyk számára.

- A **Citadella** olyan identitás-és biztonsági képességeket biztosít, amelyek lehetővé teszik a szolgáltatások közötti mTLS.

- **Hasáb** – absztrakt és konfigurációt biztosít az összetevők számára.

A következő architektúra-diagram azt mutatja be, hogy az adatsíkon és a vezérlési síkon lévő különböző összetevők hogyan hatnak egymásra.


![A Istio összetevőinek és architektúrájának áttekintése.](media/servicemesh/istio/about-architecture.png)


## <a name="selection-criteria"></a>Kiválasztási feltételek

Fontos megérteni és figyelembe venni a következő területeket a számítási feladatok Istio kiértékelése során:

- [Tervezési célok](#design-goals)
- [Képességek](#capabilities)
- [Forgatókönyvek](#scenarios)


### <a name="design-goals"></a>Tervezési célok

A következő tervezési célok a Istio projektet [ismertetik][design-goals] :

- Az **átláthatóság maximalizálása** – lehetővé teszi, hogy a rendszerből valós érték beszerzéséhez minimálisan szükséges mennyiségű munkát fogadjanak el.

- **Bővíthetőség** – képesnek kell lennie növelni és alkalmazkodni a változó igényekhez.

- **Hordozhatóság** – könnyen futtatható különböző típusú környezetekben – a felhőben, a helyszínen.

- **Házirend-egységesség** – a házirend-definíció konzisztenciája számos különböző erőforrásban.


### <a name="capabilities"></a>Képességek

A Istio a következő funkciókat biztosítja:

- **Mesh** – átjárók (több fürt), virtuális gépek (mesh-bővítés)

- **Traffic Management** – útválasztás, felosztás, időtúllépés, áramkör-megszakítók, újrapróbálkozások, bejövő és kimenő forgalom

- **Házirend** – hozzáférés-vezérlés, díjszabási korlát, kvóta, egyéni házirend-adapterek

- **Biztonság** – hitelesítés (JWT), engedélyezés, titkosítás (mTLS), külső hitelesítésszolgáltató (HashiCorp-tároló)

- **Megfigyelhetőség** – arany mérőszámok, tükrözés, nyomkövetés, egyéni adapterek, Prometheus, grafana

### <a name="scenarios"></a>Forgatókönyvek

A Istio kiválóan alkalmas a következő helyzetekben:

- Bővíthetőség és a képességek gazdag készletének megkövetelése

- A háló bővítése virtuális gépeken alapuló számítási feladatokkal

- Több fürtből álló szolgáltatás hálója

## <a name="next-steps"></a>Következő lépések

Az alábbi dokumentáció ismerteti, hogyan telepítheti a Istio-t az Azure Kubernetes Service-ben (ak):

> [!div class="nextstepaction"]
> [A Istio telepítése az Azure Kubernetes szolgáltatásban (ak)][istio-install]

A Istio-fogalmakat és a további üzembe helyezési modelleket továbbra is megismerheti:

- [Istio fogalmak][what-is-istio]
- [Istio üzembe helyezési modellek][deployment-models]

<!-- LINKS - external -->
[istio]: https://istio.io
[what-is-istio]: https://istio.io/docs/concepts/what-is-istio/
[design-goals]: https://istio.io/docs/concepts/what-is-istio/#design-goals
[deployment-models]: https://istio.io/docs/concepts/deployment-models/

[envoy-proxy]: https://www.envoyproxy.io/
[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/

<!-- LINKS - internal -->
[istio-install]: ./servicemesh-istio-install.md
