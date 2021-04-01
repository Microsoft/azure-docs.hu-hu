---
title: A szolgáltatás rácsvonalai
description: Tekintse át a szolgáltatás rácsvonalait, azok architektúráját és képességeit, valamint azt, hogy milyen szempontokat kell figyelembe vennie, amikor kiválasztja az egyiket az üzembe helyezéshez.
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: eca49a3fac1ea0398ebe1d05bde20fbca3c81232
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "77594311"
---
# <a name="about-service-meshes"></a>A szolgáltatás rácsvonalai

A Service Mesh olyan képességeket biztosít, mint például a forgalomirányítási, a rugalmasság, a házirend, a biztonság, az erős identitás és a munkaterhelések megfigyelése. Az alkalmazás elválasztja ezeket az üzemeltetési képességeket, és a szolgáltatás rácsvonala áthelyezi őket az alkalmazási rétegből, és leállítja az infrastruktúra réteget.

## <a name="scenarios"></a>Forgatókönyvek

Íme néhány olyan forgatókönyv, amely a szolgáltatásbeli háló használatakor engedélyezhető a számítási feladatokhoz:

- A **fürtben lévő összes forgalom titkosítása** – engedélyezze a kölcsönös TLS protokollt a fürt megadott szolgáltatásai között. Ezt kiterjesztheti a bejövő és kimenő forgalomra a hálózati peremhálózaton. Az alapértelmezés szerint biztonságos beállítás, amely az alkalmazás kódjához és az infrastruktúrához nem szükséges módosításokat tartalmaz.

- **Kanári és szakaszos** bevezetések – adja meg, hogy a forgalom egy részhalmaza számára milyen feltételek legyenek átirányítva a fürtben lévő új szolgáltatások készletére. A Kanári-kiadás sikeres tesztelésekor távolítsa el a feltételes útválasztást, és fokozatosan növelje az új szolgáltatás felé irányuló összes forgalom százalékos arányát. Végül az összes forgalom az új szolgáltatásra lesz irányítva.

- **Forgalomirányítási és-manipuláció** – szabályzat létrehozása egy olyan szolgáltatáshoz, amely a szolgáltatás egy adott forrásból származó verziójára korlátozza az összes forgalmat. Vagy egy olyan szabályzat, amely újrapróbálkozási stratégiát alkalmaz a megadott szolgáltatások közötti meghibásodási osztályokra. Átirányíthatja az élő forgalmat a szolgáltatások új verzióiba az áttelepítés során vagy hibakeresési problémák esetén. A rugalmasság teszteléséhez adja meg a szolgáltatások közötti hibákat a tesztkörnyezetben.

- **Megfigyelhetőség** – betekintést nyerhet abba, hogy a szolgáltatások hogyan csatlakoznak a közöttük zajló forgalomhoz. A fürtben lévő összes forgalom metrikáinak, naplóinak és nyomkövetésének beszerzése, valamint a bejövő és kimenő forgalom. Elosztott nyomkövetési képességek hozzáadása az alkalmazásokhoz.

## <a name="architecture"></a>Architektúra

A szolgáltatás hálója általában egy vezérlő síkja és az adatsíkon áll.

A **vezérlő síkja** számos olyan összetevőt tartalmaz, amelyek támogatják a szolgáltatás hálójának kezelését. Ez általában olyan felügyeleti felületet tartalmaz, amely felhasználói felület vagy API lehet. Emellett jellemzően olyan összetevőket is tartalmaznak, amelyek a szabály-és szabályzat-definíciókat kezelik, amelyek meghatározzák, hogy a szolgáltatás hálójának hogyan kell megvalósítani bizonyos képességeket. Vannak olyan összetevők is, amelyek a biztonság, például az erős identitás és a mTLS-tanúsítványok aspektusait kezelik. A szolgáltatás-rácsvonalak általában metrikákkal vagy megfigyelhető összetevővel is rendelkeznek, amely a számítási feladatokból származó mérőszámokat és telemetria gyűjti és összesíti.

Az **adatsík** jellemzően olyan proxyból áll, amely transzparens módon van befecskendezve oldalkocsiként a munkaterhelések számára. Ez a proxy úgy van konfigurálva, hogy a munkaterhelést tartalmazó Pod összes hálózati forgalmát vezérelje. Ez lehetővé teszi, hogy a proxy a mTLS-on keresztül biztonságossá tegye a forgalmat, dinamikusan irányítsa a forgalmat, alkalmazza a házirendeket a forgalomra, valamint a mérőszámok és a nyomkövetési információk gyűjtésére. 

![Jellemző a Service Mesh architektúrája](media/servicemesh/typical-architecture.png)

## <a name="capabilities"></a>Képességek

Az egyes szolgáltatásokhoz tartozó rácsvonalak természetes illeszkedéssel rendelkeznek, és a konkrét forgatókönyvek támogatására összpontosítanak, de általában úgy találja, hogy a legtöbb esetben a következő lehetőségek közül több is megvalósítható.

### <a name="traffic-management"></a>Traffic Management 

- **Protokoll** – 7. réteg (http, grpc)
- **Dinamikus útválasztás** – feltételes, súlyozás, tükrözés
- **Rugalmasság** – időtúllépések, újrapróbálkozások, áramkör-megszakítók
- **Házirend** – hozzáférés-vezérlés, díjszabási korlátok, kvóták
- **Tesztelés** – hiba-injektálás

### <a name="security"></a>Biztonság

- **Titkosítás** – mTLS, tanúsítványkezelő, külső hitelesítésszolgáltató
- **Erős identitás** – SPIFFE vagy hasonló
- **Hitelesítés – hitelesítés** , engedélyezés

### <a name="observability"></a>Megfigyelhetőség

- **Metrikák** – arany metrikák, Prometheus, grafana
- **Nyomkövetés** – a számítási feladatok közötti nyomkövetés
- **Forgalom** – fürt, bejövő/kimenő forgalom

### <a name="mesh"></a>Teljes

- **Támogatott számítási** Kubernetes, virtuális gépek
- **Több fürtből** álló átjárók, összevonás

## <a name="selection-criteria"></a>Kiválasztási feltételek

A szolgáltatás hálójának kiválasztása előtt győződjön meg róla, hogy tisztában van a követelményekkel és a Service Mesh telepítésének okaival. Próbálja ki az alábbi kérdéseket.

- **Elegendő-e a beáramlási vezérlő a saját igényeihez?** – Időnként olyan képességgel rendelkezik, mint a/b tesztelés vagy a bejövő forgalom elosztása, elegendő a szükséges forgatókönyv támogatásához. Ne vegyen fel összetettséget a környezetbe, és ne legyenek felfelé.

- **El tudják-e viselni a számítási feladatokat és a környezetet a további költségekkel kapcsolatban?** – A Service Mesh támogatásához szükséges további összetevőknek további erőforrásokra, például CPU-ra és memóriára van szükségük. Emellett az összes proxy és a hozzájuk kapcsolódó házirend ellenőrzi a forgalom késését. Ha olyan számítási feladatokkal rendelkezik, amelyek nagyon érzékenyek a késésre, vagy nem tudja biztosítani a további erőforrásokat a szolgáltatás hálójának összetevőinek lefedéséhez, majd gondolja át újra.

- **Szükségtelen a további bonyolultság?** – Ha a szolgáltatás hálójának telepítésének oka az, hogy olyan képességet szerezzen, amely nem feltétlenül kritikus fontosságú az üzleti vagy az operatív csapat számára, akkor vegye figyelembe, hogy a telepítés, a karbantartás és a konfiguráció további összetettsége is megéri-e.

- **Elfogadható a növekményes megközelítés?** – A számos képességet biztosító szolgáltatási rácsvonalak több növekményes megközelítésben is megadhatók. Csak azokat az összetevőket telepítse, amelyek szükségesek a sikeres sikeresség biztosításához. Ha jobban bízik, és további funkciókra van szükség, akkor vizsgálja meg ezeket. A kezdeti lépések elvégzésével a késztetésnek kell elállnia. 

Ha alapos megfontolás után úgy dönt, hogy szükség van egy szolgáltatási hálóra a szükséges képességek biztosításához, akkor a következő döntés a *Service Mesh?*

Vegye figyelembe a következő területeket, és ezek közül a leginkább igazodik a követelményekhez. Ez végigvezeti Önt a környezet és a számítási feladatok legmegfelelőbb méretének. A [következő lépések](#next-steps) szakaszban további információkat talál az egyes szolgáltatásbeli hálókkal kapcsolatban, valamint arról, hogy azok hogyan képezik le ezeket a területeket.

- **Technikai** forgalmi felügyelet, házirend, biztonság, Megfigyelhetőség

- **Üzleti** – kereskedelmi támogatás, Alapítvány (CNCF), OSS-licenc, irányítás

- **Működés** – telepítés/verziófrissítés, erőforrás-követelmények, teljesítménnyel kapcsolatos követelmények, integrációk (mérőszámok, telemetria, irányítópultok, eszközök, SMI), vegyes munkaterhelések (Linux és Windows Node-készletek), számítás (Kubernetes, virtuális gépek), több fürt

- **Biztonság** – hitelesítés, identitás, tanúsítványkezelő és rotáció, csatlakoztatott külső hitelesítésszolgáltató


## <a name="next-steps"></a>Következő lépések

Az alábbi dokumentáció további információkat tartalmaz az Azure Kubernetes Service (ak) szolgáltatásban kipróbálható szolgáltatási hálókkal kapcsolatban:

> [!div class="nextstepaction"]
> [További információ a Istio...][istio-about]

> [!div class="nextstepaction"]
> [További információ a Linkerd...][linkerd-about]

> [!div class="nextstepaction"]
> [További információ a Konzulról...][consul-about]

Érdemes megvizsgálni a Service Mesh-felületet (SMI) is, amely a Service Mesh-hálók szabványos felülete a Kubernetes:

- [Service Mesh-felület (SMI)][smi]


<!-- LINKS - external -->
[smi]: https://smi-spec.io/

<!-- LINKS - internal -->
[istio-about]: ./servicemesh-istio-about.md
[linkerd-about]: ./servicemesh-linkerd-about.md
[consul-about]: ./servicemesh-consul-about.md