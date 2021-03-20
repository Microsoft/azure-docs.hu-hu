---
title: Támogatás – Azure dedikált HSM | Microsoft Docs
description: Támogatási lehetőségek és az Azure dedikált HSM-re vonatkozó felelősségi területek különböző helyzetekben
services: dedicated-hsm
author: johndaw
manager: rkarlin
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.custom: seodec18
ms.date: 03/27/2019
ms.author: mbaldwin
ms.openlocfilehash: d83d688707baf6098d63dfde9b4181eb04fb9729
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "70881008"
---
# <a name="azure-dedicated-hsm-supportability"></a>Azure dedikált HSM-támogatás

Az Azure dedikált HSM szolgáltatás egy fizikai eszközt biztosít az ügyfelek kizárólagos használatára a teljes körű rendszergazdai ellenőrzés és felügyelet felelőssége mellett. Az elérhetővé tett eszköz egy [Gemalto SafeNet Luna 7 HSM Model A790](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/). A Microsoft nem rendelkezik rendszergazdai hozzáféréssel, ha az ügyfél nem veszi igénybe a fizikai soros portok mellékletét figyelési szerepkörként.  Hozzáférés nélkül a Microsoft nem rendelkezhet folyamatban lévő szoftver szintű karbantartási vagy Rendszerfelügyeleti feladatokkal. Ennek eredményeképpen az ügyfelek felelősek az általános működési tevékenységekért.
Az ügyfelek teljes mértékben felelősek a HSM használó alkalmazásokért, és a Gemalto támogatással vagy tanácsadással kapcsolatos segítségért kell működniük. Az üzemeltetési higiéniai ügyfelek tulajdonlása miatt nem lehetséges, hogy a Microsoft bármilyen magas rendelkezésre állású garanciát nyújtson ehhez a szolgáltatáshoz. Az ügyfél feladata annak biztosítása, hogy alkalmazásaikat megfelelően konfigurálja a magas rendelkezésre állás érdekében. A Microsoft figyeli és karbantartja az eszköz állapotát és a hálózati kapcsolatot.

## <a name="getting-support"></a>Támogatás igénylése

A dedikált HSM-hez készült ügyfélszolgálat a Microsoft és a Gemalto közötti közös munka. A Microsoft minden hardveres problémát vagy hálózati elérési utat elhárít, és a tényleges HSM-sel (például a konfigurációval, a szoftverekkel, a belső vezérlőprogram és az alkalmazás-fejlesztéssel) kapcsolatos teendőket a Gemalto fogja megoldani. Ez a támogatási modell biztosítja a leggyorsabb útvonalat a leghatékonyabb támogatáshoz. Ha bizonytalan egy adott problémával kapcsolatban, adjon meg egy támogatási kérést a Microsofttal, és gondoskodunk arról, hogy a megfelelő módon legyen átirányítva. A Microsoft minden támogatási forgatókönyvben részt vesz, és a legjobb támogatási élményt nyújtja ügyfeleinknek.

## <a name="gemalto-support"></a>Gemalto-támogatás

A dedikált HSM szolgáltatást használó ügyfelek a támogatási csomag keretében a Gemalto támogatását támogatják. Ehhez csak a Gemalto támogatási portál használatával kell regisztrálnia a regisztrációs folyamatot. Az ügyfél-azonosító és az utasítások erre a célra a Microsofttal való első részvétel részeként a dedikált HSM szolgáltatáshoz való hozzáféréshez nyújtanak hozzáférést. A Gemalto támogatásának mechanizmusa az [ügyfél-támogatási portálon](https://supportportal.gemalto.com/csm/)keresztül érhető el.
Fontos megjegyezni, hogy a Gemalto minden szoftvert és dokumentációt biztosít, amely a HSM (például az ügyfél-hozzáférési szoftver és az SDK-k) használatához szükséges a terméktámogatási portálon való letöltéssel.

### <a name="software-components"></a>Szoftver-összetevők

A HSM-eszközök konfigurációjában különféle szoftver-összetevők használatosak:

* Ügyfélszoftver
* SDK
* Eszközök

### <a name="guidance"></a>Útmutató

A Gemalto az [ügyfélszolgálati portálon](https://supportportal.gemalto.com/csm/)keresztül teszi elérhetővé az adminisztrációs és konfigurációs útmutatást. Az érvényes ügyfél-AZONOSÍTÓval való bejelentkezés után ezek a dokumentumok letölthetők. A Gemalto számos integrációs útmutatót is biztosít, amelyek segítségével az ügyfelek különböző forgatókönyvekkel és szoftveres integrációval segítik a felhasználókat. További információkért tekintse meg a [Microsoft Gemalto partner webhelyét](https://safenet.gemalto.com/partners/microsoft/).

### <a name="support"></a>Támogatás

A dedikált HSM-szolgáltatás részeként a HSM használatával kapcsolatban felmerülő bármilyen szoftver szintű probléma vagy kérdés, amely közvetlenül a Gemalto-támogatásra vonatkozik. A Gemalto az összes fent felsorolt szoftver-összetevőt és a kiépítés utáni egyéni HSM-beállításokat fogja kezelni. További információ:  [Gemalto Customer Support Portal](https://supportportal.gemalto.com/csm/).

### <a name="consulting-services"></a>Tanácsadói szolgáltatások

A HSM-et használó egyéni alkalmazások tervezéséhez, fejlesztéséhez és üzembe helyezéséhez segítségért forduljon a Gemalto-fiók képviselőjéhez.

## <a name="microsoft-support"></a>Microsoft ügyfélszolgálata

A Microsoft biztosítja, hogy a fizikai HSM-eszközök a hálózat számára elérhetők legyenek, és működési állapotban legyenek egyetlen ügyfél kizárólagos használatára. Az ügyfelek felelősek az eszköz konfigurálásával, felügyeletével és kezelésével kapcsolatban. A Microsoft feladatai közé tartoznak a következők:

* Győződjön meg arról, hogy az eszköz rendelkezik tápellátással és hűtéssel
* A HSM működési állapotának fenntartása (például megszakítási/javítási forgatókönyvek)
* Az eszköz a hálózaton keresztül érhető el.

Az alábbi problémákat jelenteni kell a Microsoftnak:

* Összetevő-hibák
* Teljes eszköz meghibásodása
* Hálózati hozzáférési problémák
* Problémák kiépítése és megszüntetése.

A Microsoft fizikai soros porton keresztül fér hozzá az eszközhöz egy olyan figyelési szerepkörrel (azaz nem rendszergazdai szerepkörrel), amely lehetővé teszi az alapvető állapot telemetria.  Ez lehetővé teszi, hogy a Microsoft proaktív értesítést nyújtson a problémáról az ügyfélnek, kivéve, ha az ügyfél úgy dönt, hogy korlátozza ezt az engedélyt. 

### <a name="provisioning-and-decommissioning"></a>Kiépítés és leszerelés

Miután az ügyfél jóváhagyott regisztrációval rendelkezik a dedikált HSM szolgáltatáshoz, létrehozhatók HSM-erőforrások (jelenleg PowerShell-lel vagy parancssori felületen keresztül, nem pedig a Azure Portal). Az erőforrás egy kiosztási folyamaton halad át, amely egy adott régióban található fizikai eszközt képez le egy ügyfél előre definiált virtuális hálózatához (VNet). Ha a VNet látható, az ügyfél hozzáférhet az eszközhöz, és a követelmények szerint további konfigurálást is biztosít. Az ügyfelek a dedikált HSM a Gemalto-ügyfélszoftver és-eszközök használatával érhetik el. Az erőforrás-létrehozási folyamatot a Microsoft támogatja. A Gemalto az egyéni konfigurációs folyamatot és azon kívül is támogatja. (lásd a fenti Gemalto-támogatást). Amikor egy ügyfél befejezte a HSM használatát, alaphelyzetbe kell állítani (vagy nullázni), hogy ne legyenek adatmegőrzési adatai. Az eszköz alaphelyzetbe állításának folyamata eltávolítja az összes egyéni konfigurációt és adatmennyiséget. A Microsoft felszabadítja az eszközt, és visszaadja a készletnek egy érintetlen állapotban. Ez azt jelenti, hogy az eszköznek a készletbe való visszaadásakor nincs bizonyíték a korábbi ügyfelek tevékenységére. 

### <a name="hardware-issues"></a>Hardverrel kapcsolatos problémák

A HSM-eszköz redundáns és cserélhető tápegységeket és ventilátor-egységeket tartalmaz.  A ventilátoros egység eltávolítása azonban továbbra is egy illetéktelen módosítási eseményt eredményez. Ha egy összetevő meghibásodása történik, a Microsoft a legmegfelelőbb eljárást fogja használni az összetevők szintjének probléma megoldásához, amely minimális megszakítást és legalacsonyabb kockázatot okoz az ügyfelek szolgáltatásának rendelkezésre állása szempontjából.
Az eszköz minden komolyabb meghibásodása azt eredményezi, hogy az eszköz a szabad készletből egy friss helyére kerül. Az ügyfél egyszerűen magában foglalja az új eszközt a meglévő HA-pár szinkronizáláshoz, és visszatér a teljes működési állapotba. A meghibásodott eszközön a rendszer eltávolítja és felaprította az adattároló eszközeit az adatközpontban. A rendszer csak a váz visszaküldését fogja visszaadni a Gemalto.


### <a name="networking-issues"></a>Hálózati problémák

Ha az ügyfelek hálózatkezelési hozzáférési problémákat tapasztalnak a HSM-eszközhöz, vegye fel a kapcsolatot a Microsoft ügyfélszolgálatával. A hálózati hozzáférés egyszerű tesztelése az SSH használata a HSM-eszközhöz való kapcsolódáshoz. Ha ez nem sikerül, forduljon a Microsoft támogatási szolgálatához.

## <a name="service-level-expectations-for-support"></a>A támogatási szolgáltatások szintjére vonatkozó elvárások

A Microsoft támogatási szolgálati szintjeivel kapcsolatban tekintse meg az [Azure támogatási csomagot](https://azure.microsoft.com/support/plans/).
A Gemalto támogatási szolgáltatási szintjeivel kapcsolatban tekintse meg a [Gemalto-támogatás alapjai című témakört](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Következő lépések

Javasoljuk, hogy az eszközök kiépítése és az alkalmazások tervezése vagy üzembe helyezése előtt jól megértse a fontos fogalmakat, például a magas rendelkezésre állást és a biztonságot.

* [Üzembe helyezési architektúra](deployment-architecture.md)
* [Magas rendelkezésre állás](high-availability.md)
* [Fizikai biztonság](physical-security.md)
* [Hálózat](networking.md)

