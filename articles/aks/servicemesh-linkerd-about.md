---
title: A Linkerd áttekintése
description: A Linkerd áttekintésének beszerzése
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 3181be62a14ec1b3450bd181172b5323ca176427
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "77593767"
---
# <a name="linkerd"></a>Linkerd

## <a name="overview"></a>Áttekintés

A [Linkerd][linkerd] egy könnyen használható és Lightweight Service Mesh.

## <a name="architecture"></a>Architektúra

A Linkerd olyan adatsíkokat biztosít, amely az ultrakönnyű [Linkerd][linkerd-proxy] speciális proxy oldalkocsis szolgáltatásokból áll. Ezek az intelligens proxyk a hálózott alkalmazások és munkaterhelések összes hálózati forgalmát vezérlik. A proxyk a [Prometheus][prometheus] -metrikák végpontján keresztül is elérhetővé teszik a metrikákat.

A vezérlő síkja a következő [összetevők][linkerd-architecture]használatával kezeli a konfigurációt és az összesített telemetria:

- **Vezérlő** – olyan API-t biztosít, amely a Linkerd parancssori felületet és az irányítópultot vezérli. A proxyk konfigurációját biztosítja.

- **Koppintás** – valós idejű órákat hozhat létre a kérelmekkel és a válaszokkal kapcsolatban.

- **Identitás** – olyan identitás-és biztonsági képességeket biztosít, amelyek lehetővé teszik a szolgáltatások közötti mTLS.

- **Web** – a Linkerd irányítópultját adja meg.


A következő architektúra-diagram azt mutatja be, hogy az adatsíkon és a vezérlési síkon lévő különböző összetevők hogyan hatnak egymásra.


![A Linkerd összetevőinek és architektúrájának áttekintése.](media/servicemesh/linkerd/about-architecture.png)


## <a name="selection-criteria"></a>Kiválasztási feltételek

Fontos megérteni és figyelembe venni a következő területeket a számítási feladatok Linkerd kiértékelése során:

- [Tervezési alapelvek](#design-principles)
- [Képességek](#capabilities)
- [Forgatókönyvek](#scenarios)


### <a name="design-principles"></a>Tervezési alapelvek

A következő tervezési alapelvek a Linkerd-projektet [ismertetik][design-principles] :

- Az **egyszerű** használat érdekében könnyen használhatónak és érthetőnek kell lennie.

- Az **erőforrásokra vonatkozó követelmények minimalizálása** – minimális teljesítmény-és erőforrás-költségeket szabhat.

- **Csak munka** – ne szakítsa meg a meglévő alkalmazásokat, és ne igényeljen összetett konfigurációt.


### <a name="capabilities"></a>Képességek

A Linkerd a következő funkciókat biztosítja:

- **Mesh** – beépített hibakeresési lehetőség

- **Traffic Management** – felosztás, időtúllépés, újrapróbálkozások, bejövő forgalom

- **Biztonság** – titkosítás (mTLS), 24 óránként automatikusan elforgatott tanúsítványok

- **Megfigyelhetőség** – arany metrikák, koppintás, nyomkövetés, szolgáltatási profilok és útvonal-metrikák, webes irányítópult topológiai gráfokkal, Prometheus, grafana


### <a name="scenarios"></a>Forgatókönyvek

A Linkerd kiválóan alkalmas a következő helyzetekben:

- Egyszerűen használható a képességgel kapcsolatos követelmények alapvető készletével

- Alacsony késés, alacsony terhelés, a megfigyelésre és az egyszerű adatforgalom kezelésére összpontosítva


## <a name="next-steps"></a>Következő lépések

Az alábbi dokumentáció ismerteti, hogyan telepítheti a Linkerd-t az Azure Kubernetes Service-ben (ak):

> [!div class="nextstepaction"]
> [A Linkerd telepítése az Azure Kubernetes szolgáltatásban (ak)][linkerd-install]

A Linkerd funkcióit és architektúráját továbbra is megismerheti:

- [Linkerd-funkciók][linkerd-features]
- [Linkerd architektúra][linkerd-architecture]

<!-- LINKS - external -->
[linkerd]: https://linkerd.io/2/overview/
[linkerd-architecture]: https://linkerd.io/2/reference/architecture/
[linkerd-features]: https://linkerd.io/2/features/
[design-principles]: https://linkerd.io/2/design-principles/
[linkerd-proxy]: https://github.com/linkerd/linkerd2-proxy

[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/

<!-- LINKS - internal -->
[linkerd-install]: ./servicemesh-linkerd-install.md