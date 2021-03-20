---
title: Azure Spring Cloud geo – vész-helyreállítás | Microsoft Docs
description: Ismerje meg, hogyan védhető a Spring Cloud-alkalmazás a regionális kimaradásokból
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: e18193b40bb7c59a21a279f451673dc7d11140e4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92092902"
---
# <a name="azure-spring-cloud-disaster-recovery"></a>Azure Spring Cloud vész-helyreállítás

**Ez a cikk a következőkre vonatkozik:** ✔️ Java ✔️ C #

Ez a cikk azokat a stratégiákat ismerteti, amelyekkel biztosíthatja, hogy az Azure Spring Cloud-alkalmazásai a leállás során is védve legyenek.  Előfordulhat, hogy bármely régió vagy adatközpont a regionális katasztrófák által okozott állásidőt tapasztalhatja, de a gondos tervezés csökkentheti az ügyfelekre gyakorolt hatást.

## <a name="plan-your-application-deployment"></a>Az alkalmazás központi telepítésének megtervezése

Az Azure Spring Cloud-alkalmazások egy adott régióban futnak.  Az Azure világszerte számos földrajzi területen működik. Az Azure földrajz a világ egy meghatározott területe, amely legalább egy Azure-régiót tartalmaz. Az Azure-régió egy földrajzon belüli terület, amely egy vagy több adatközpontot tartalmaz.  Minden egyes Azure-régió párosítva van egy másik régióval, amely ugyanabban a földrajzi helyen található, és regionális párokat alkot. Az Azure szerializálja a platform frissítéseit (tervezett karbantartás) a regionális párok között, így biztosítva, hogy egyszerre csak egy régió legyen frissítve az egyes párokban. Ha egy leállás több régiót érint, akkor az egyes párokban legalább egy régiót előnyben részesíti a helyreállítás.

A magas rendelkezésre állás és a katasztrófák elleni védelem biztosításához szükséges, hogy a Spring Cloud-alkalmazásokat több régióra telepítse.  Az Azure a [párosított régiók](../best-practices-availability-paired-regions.md) listáját jeleníti meg, hogy a tavaszi Felhőbeli üzemelő példányokat a regionális párokra tervezze.  Javasoljuk, hogy a mikro-szolgáltatás architektúrájának tervezésekor három fő tényezőt vegye figyelembe: a régiók elérhetőségét, az Azure párosított régiókat és a szolgáltatás rendelkezésre állását.

*  Régió elérhetősége: válasszon ki egy földrajzi területet a felhasználók számára a hálózati késés és az átviteli idő minimalizálásához.
*  Azure párosított régiók: ha szükséges, a kiválasztott földrajzi területen válassza a párosított régiók lehetőséget az összehangolt platform-frissítések és a rangsorolt helyreállítási erőfeszítések biztosításához.
*  Szolgáltatás rendelkezésre állása: döntse el, hogy a párosított régióknak meleg/gyakori, meleg/meleg vagy meleg/hidegen kell futniuk.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Az Azure Traffic Manager használata a forgalom irányításához

Az [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) a DNS-alapú forgalom terheléselosztását biztosítja, és több régióban is terjesztheti a hálózati forgalmat.  Az Azure Traffic Manager használatával irányíthatja az ügyfeleket a legközelebbi Azure Spring Cloud Service-példányra.  A legjobb teljesítmény és redundancia érdekében az Azure Traffic Manageron keresztül az összes alkalmazás forgalmát az Azure Spring Cloud Service-be való elküldés előtt irányítsa.

Ha több régióban is rendelkezik Azure Spring Cloud-alkalmazásokkal, az Azure Traffic Manager segítségével szabályozhatja az alkalmazások forgalmának áramlását az egyes régiókban.  Definiáljon egy Azure Traffic Manager végpontot az egyes szolgáltatásokhoz a szolgáltatás IP-címének használatával. Az ügyfeleknek csatlakozniuk kell egy Azure Traffic Manager DNS-névhez, amely az Azure Spring Cloud szolgáltatásra mutat.  Az Azure Traffic Manager terheléselosztási forgalmat a meghatározott végpontok között.  Ha egy katasztrófa egy adatközpontot üt ki, akkor az Azure Traffic Manager az adott régiótól a párja felé irányítja a forgalmat, így biztosítva a szolgáltatás folytonosságát.

## <a name="create-azure-traffic-manager-for-azure-spring-cloud"></a>Azure-Traffic Manager létrehozása az Azure Spring Cloud-hoz

1. Azure Spring Cloud létrehozása két különböző régióban.
Két különböző régióban (az USA keleti régiójában és Nyugat-Európában) üzembe helyezett Azure Spring Cloud két szolgáltatási példányra lesz szüksége. Nyisson meg egy meglévő Azure Spring Cloud-alkalmazást a Azure Portal használatával két szolgáltatási példány létrehozásához. A forgalom elsődleges és feladatátvételi végpontként szolgál. 

**Két szolgáltatási példány adatai:**

| Szolgáltatásnév | Hely | Alkalmazás |
|--|--|--|
| Service-Sample-a | USA keleti régiója | átjáró/Auth-szolgáltatás/fiók-szolgáltatás |
| szolgáltatás – minta – b | Nyugat-Európa | átjáró/Auth-szolgáltatás/fiók-szolgáltatás |

2. Egyéni tartomány beállítása a Service-hez egyéni tartomány- [dokumentum](spring-cloud-tutorial-custom-domain.md) beállítása a két meglévő szolgáltatási példány egyéni tartományának beállításához. A sikeres beállítás után mindkét szolgáltatási példány az egyéni tartományhoz lesz kötve: bcdr-test.contoso.com

3. Hozzon létre egy Traffic Managert és két végpontot: [hozzon létre egy Traffic Manager profilt a Azure Portal használatával](../traffic-manager/quickstart-create-traffic-manager-profile.md).

Itt látható a Traffic Manager-profil:
* Traffic Manager DNS-név: `http://asc-bcdr.trafficmanager.net`
* Végponti profilok: 

| Profil | Típus | Cél | Prioritás | Egyéni fejléc beállításai |
|--|--|--|--|--|
| Egy profil végpontja | Külső végpont | service-sample-a.asc-test.net | 1 | gazdagép: bcdr-test.contoso.com |
| B végpont profilja | Külső végpont | service-sample-b.asc-test.net | 2 | gazdagép: bcdr-test.contoso.com |

4. Hozzon létre egy CNAME rekordot a DNS-zónában: bcdr-test.contoso.com CNAME asc-bcdr.trafficmanager.net. 

5. Most a környezet teljesen be van állítva. Az ügyfeleknek el kell tudniuk érni az alkalmazást a következőn keresztül: bcdr-test.contoso.com

## <a name="next-steps"></a>Következő lépések

* [Rövid útmutató: az első Azure Spring Cloud-alkalmazás üzembe helyezése](spring-cloud-quickstart.md)
