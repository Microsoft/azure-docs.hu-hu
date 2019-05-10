---
title: A TRAFFIC Manager Végponttípusok |} A Microsoft Docs
description: Ez a cikk ismerteti a különböző típusú végpontok használható az Azure Traffic Managerrel
services: traffic-manager
documentationcenter: ''
author: kumudd
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/29/2017
ms.author: kumud
ms.openlocfilehash: dc76f56b6c05f22a380ff33715fe22e8c72e4891
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/09/2019
ms.locfileid: "65508433"
---
# <a name="traffic-manager-endpoints"></a>Traffic Manager-végpontok
A Microsoft Azure Traffic Manager lehetővé teszi, hogy vezérelheti a hálózati forgalom hogyan ossza el a különböző adatközpontokban futó alkalmazások központi telepítéseit. A Traffic Manager konfigurálása minden egyes alkalmazás üzembe helyezési-végpontként. Amikor a Traffic Manager DNS-kérelmet kap, azt úgy dönt, a DNS-válaszban visszaadandó elérhető végpontot. A TRAFFIC manager végpont aktuális állapota és a forgalom-útválasztási módszer a kiválasztott adatbázisok. További információkért lásd: [Traffic Manager működése](traffic-manager-how-it-works.md).

A végpont a Traffic Manager által támogatott három típusa van:
* **Azure-beli** használhatók az Azure-ban üzemeltetett szolgáltatások.
* **Külső végpontok** IPv4/IPv6-címek, teljes tartománynevek, vagy az vagy egy másik üzemeltetésszolgáltatónál kívül, amely lehet a helyszíni Azure-ban üzemeltetett szolgáltatások szolgálnak.
* **A beágyazott végpontokat** egyesítheti a Traffic Manager-profilok létrehozása a rugalmas forgalom-útválasztási rendszerek nagyobb, összetettebb központi telepítések igényeinek támogatásához használt.

Hogyan van összevonva, különböző típusú végpontok egy Traffic Manager-profil korlátozva van. Az egyes profilok végponttípusok bármilyen kombinációját is tartalmazhat.

A következő szakaszok ismertetik az egyes többletköltségeket típusú végpont.

## <a name="azure-endpoints"></a>Azure-végpontok

Azure-beli Azure-alapú szolgáltatásokat a Traffic Manager szolgálnak. A következő Azure-erőforrás-típusokat támogatja:

* PaaS felhőszolgáltatások.
* Web Apps
* Webalkalmazás tárolóhelyeinek
* PublicIPAddress erőforrásokat (virtuális gépek is csatlakoztathatók közvetlenül vagy egy Azure Load Balancer-n keresztül). A nyilvános IP-címre rendelkeznie kell egy DNS-nevet, egy Traffic Manager-profil használható rendelt.

PublicIPAddress erőforrásokat az Azure Resource Manager-erőforrások. Nem léteznek, a klasszikus üzemi modellben. Így azok csak a támogatott Traffic Manager az Azure Resource Manager élményt. A többi végponttípusok erőforrás-kezelő, mind a klasszikus üzemi modell használatával támogatottak.

Azure-beli használatakor a Traffic Manager észleli, ha a "Klasszikus" IaaS virtuális gép, a felhőalapú szolgáltatás vagy a webalkalmazás leállt, és elindult. Ez az állapot a végpont állapotának tükrözi. Lásd: [Traffic Manager végpont figyelése](traffic-manager-monitoring.md#endpoint-and-profile-status) részleteiről. A mögöttes szolgáltatás leáll, amikor a Traffic Manager nem végez végpont állapot-ellenőrzések vagy közvetlen forgalom a végpontra. Számlázási Traffic Manager-események nem fordulhat elő, a leállított példány. A szolgáltatás újraindításakor számlázási folytatja, és a végpont abban az esetben jogosult forgalom fogadására. Az észlelés nem vonatkozik a PublicIpAddress végpontokat.

## <a name="external-endpoints"></a>Külső végpontok

Külső végpontokat vagy IPv4-/ IPv6-címek, teljes tartománynevek, vagy az Azure-on kívüli szolgáltatások szolgálnak. IPv4/IPv6-cím végpontok használata lehetővé teszi a traffic manager anélkül, hogy egy DNS-nevet a számukra a végpontok állapotának ellenőrzéséhez. Ennek eredményeképpen a Traffic Manager válaszolhat A/AAAA rekord lekérdezéseket, hogy a végpont a válasz visszaadásakor. Azure-on kívüli szolgáltatások lehetnek egy üzemeltetett szolgáltatás a helyszíni vagy másik szolgáltatóhoz. Külső végpontok használt egyenként, vagy ugyanazon a Traffic Manager-profil végpontok a megadott IPv4 vagy IPv6-címek, amelyek csak a külső végpontokat is lehet, kivéve az Azure-végpontok kombinálva is. Külső végpontokkal rendelkező Azure-beli kombinálásával lehetővé teszi a különböző forgatókönyvekben:

* Nagyobb redundancia-meglévő helyszíni alkalmazásokhoz vagy egy aktív – aktív vagy aktív – passzív feladatátvétel modell biztosítása az Azure. 
* Forgalom irányítása végpont, amelyek nem rendelkeznek a hozzájuk társított DNS-név. Emellett az összesített DNS-keresési késés csökkentése eltávolítása kell futtatni az első IP-címet, egy DNS-nevet adja vissza a második DNS-lekérdezést. 
* A felhasználók számára a világ különböző pontjain található alkalmazás késés csökkentése érdekében, további földrajzi helyek, az Azure-ban egy meglévő helyszíni alkalmazást kiterjesztése. További információkért lásd: [Traffic Manager "Teljesítmény" forgalom-útválasztást](traffic-manager-routing-methods.md#performance).
* Adjon meg ennél nagyobb kapacitásra egy meglévő helyszíni alkalmazás, folyamatosan vagy egy "adatlöketek felhőbe irányuló" megoldás az Azure-ugrásszerű kielégítése érdekében.

Bizonyos esetekben hasznos lehet hivatkozni az Azure-szolgáltatások külső végpontok használata (példák: a [– gyakori kérdések](traffic-manager-faqs.md#traffic-manager-endpoints)). Állapot-ellenőrzések ebben az esetben az Azure-beli sebességét, nem külső végpontokat kiszámlázzuk. Azonban eltérően az Azure-végpont, ha leállítja vagy törli a mögöttes szolgáltatás állapotának ellenőrzése a számlázás továbbra is fennáll, tiltsa le, illetve a Traffic Manager-végpont törlése.

## <a name="nested-endpoints"></a>Beágyazott végpontokat

Beágyazott végpontokat össze több Traffic Manager-profilokat hozhat létre rugalmas forgalom-útválasztási sémákat, és nagyobb méretű, összetett központi telepítések az igényeinek támogatása. A beágyazott végpontokat a "gyermek" profil profilhoz lesz hozzáadva végpontként a "parent". A gyermek és szülő profilokat más végpontok bármilyen típusú, beleértve a más beágyazott profilok is tartalmazhat. További információkért lásd: [beágyazott Traffic Manager-profilok](traffic-manager-nested-profiles.md).

## <a name="web-apps-as-endpoints"></a>Webes alkalmazások végpontként

További megfontolásokat a alkalmazni: Web Apps Traffic Manager-végpontként konfigurálásakor:

1. Web Apps csak a "Standard" Termékváltozat vagy meghaladja a jogosultak a Traffic Managerrel történő használathoz. Sikertelen kísérlet egy alacsonyabb termékváltozat webes alkalmazás hozzáadásához. A Traffic Manager többé nem forgalmat küld a webalkalmazást egy meglévő Web Apps-Termékváltozat alacsonyabb szolgáltatásszintre eredmények. További információ a támogatott csomagok: a [App Service-csomagok](https://azure.microsoft.com/pricing/details/app-service/plans/)
2. Ha a végpont HTTP-kérést kap, akkor használja a "host" a kérelemben szereplő tartományfejléc meghatározásához melyik webalkalmazás kell kiszolgálni a kérelmet. Az állomásfejlécnek kezdeményezhető a kérést, például a "contosoapp.azurewebsites.net" DNS-nevét tartalmazza. Webalkalmazással együtt egy másik DNS-név használatához regisztrálni kell a DNS-nevet az alkalmazás az egyéni tartomány nevét. Ha egy Azure-végpont hozzáadása egy webalkalmazás-végpontot, a Traffic Manager-profil DNS neve automatikusan regisztrálja az alkalmazás. A végpont törlése a rendszer automatikusan törli a regisztrációt.
3. Minden egyes Traffic Manager-profil legfeljebb egy webalkalmazás-végpontot rendelkezhet minden egyes Azure-régióból. Külső végpontként megkerüléséhez ezt a korlátozást, konfigurálhatja egy webalkalmazást. További információkért lásd: a [– gyakori kérdések](traffic-manager-faqs.md#traffic-manager-endpoints).

## <a name="enabling-and-disabling-endpoints"></a>Engedélyezése és letiltása a végpontok

A Traffic Manager végpont letiltása forgalom ideiglenesen eltávolítása karbantartási módban lévő vagy újratelepítés alatt álló végpont hasznos lehet. A végpont újra működik, ha újra engedélyezni lehet.

Végpontok engedélyezve van, és le van tiltva a Traffic Manager portal, PowerShell, a parancssori felület vagy a REST API-n keresztül is.

> [!NOTE]
> Az Azure a végpont letiltása nem rendszerbeli üzembe helyezési állapotát az Azure-ban. Az Azure-szolgáltatások (például egy virtuális gép vagy a Web App továbbra is fut, és akkor is, ha le van tiltva a Traffic Manager forgalom fogadására. Forgalom közvetlenül a szolgáltatáspéldány helyett a Traffic Manager profil DNS-név használatával lehet megoldani. További információkért lásd: [Traffic Manager működése](traffic-manager-how-it-works.md).

Az aktuális való jogosultságát, minden végpont forgalom fogadására a következő tényezőktől függ:

* A profil állapota (engedélyezve/letiltva)
* A végpont állapotának (engedélyezve/letiltva)
* Az eredmények az egészségügyi ellenőrzi, hogy a végpont

További információkért lásd: [Traffic Manager végpont figyelése](traffic-manager-monitoring.md#endpoint-and-profile-status).

> [!NOTE]
> A Traffic Manager a DNS szintjén működik, mert nem tudja befolyásolni a meglévő kapcsolatok bármely végpont. A végpont nem érhető el, ha a Traffic Manager arra utasítja a elérhető végpontot egy másik új kapcsolatokat. Azonban a gazdagép a letiltott vagy nem megfelelő állapotú végpont mögött továbbra is a meglévő kapcsolatok keresztül a forgalom fogadásához, mindaddig, amíg a munkamenetek megszűnik. Alkalmazások a munkamenet időtartama a meglévő kapcsolatok kiürítési forgalom engedélyezésére kell korlátozni.

Ha a profilban szereplő összes végpont le vannak tiltva, vagy le van tiltva, a profil, a Traffic Manager egy új DNS-lekérdezés "NXDOMAIN" választ küld.


## <a name="next-steps"></a>További lépések

* Ismerje meg, [Traffic Manager működése](traffic-manager-how-it-works.md).
* Tudnivalók a Traffic Managerről [végpont ellenőrzési és automatikus feladatátvételi](traffic-manager-monitoring.md).
* Tudnivalók a Traffic Managerről [forgalom-útválasztási módszerek](traffic-manager-routing-methods.md).
