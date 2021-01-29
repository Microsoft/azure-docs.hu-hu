---
title: Traffic Manager végpontok típusai | Microsoft Docs
description: Ez a cikk az Azure Traffic Manager használható különböző típusú végpontokat ismerteti.
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/21/2021
ms.author: duau
ms.openlocfilehash: 7686f2f97da0113704216dcab741c063a80d3136
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99051227"
---
# <a name="traffic-manager-endpoints"></a>Traffic Manager-végpontok

Microsoft Azure Traffic Manager lehetővé teszi a hálózati forgalom elosztását a különböző adatközpontokban futó alkalmazások központi telepítésének szabályozásával. Az alkalmazások üzembe helyezést mindig „végpontként” kell konfigurálnia a Traffic Managerben. Amikor a Traffic Manager DNS-kérést kap, kiválaszt egy elérhető végpontot, melyet visszaad a DNS-válaszban. A Traffic Manager a jelenlegi végponti állapot és a forgalom-útválasztási módszer közül választhat. További információ: [How Traffic Manager Works](traffic-manager-how-it-works.md).

A Traffic Manager három típusú végpontot támogat:

* Az **Azure-végpontok** az Azure-ban üzemeltetett szolgáltatásokhoz használatosak.
* A **külső végpontok** IPv4/IPv6-címekhez, teljes tartománynevek vagy az Azure-on kívül üzemeltetett szolgáltatásokhoz használatosak. A tézisek szolgáltatásai lehetnek a helyszínen vagy más szolgáltatóval is.
* **Beágyazott végpontokkal** Traffic Manager-profilok kombinálhatók rugalmasabb útválasztási sémák létrehozásához a nagyobb, összetettebb üzemelő példányok igényeinek megfelelően.

Nincs korlátozás arra vonatkozóan, hogy a különböző típusú végpontok hogyan kombinálhatók egyetlen Traffic Manager-profilban. Minden profil a végpontok bármilyen kombinációját tartalmazhatja.

Az alábbi szakaszok részletesebben ismertetik az egyes végpontok típusait.

## <a name="azure-endpoints"></a>Azure-végpontok

Az Azure-végpontok a Traffic Manager Azure-alapú szolgáltatásaihoz használatosak. A következő Azure-erőforrástípusok támogatottak:

* Pásti Cloud Services.
* Web Apps
* Webalkalmazás-tárolóhelyek
* PublicIPAddress-erőforrások (amelyek közvetlenül vagy egy Azure Load Balancer használatával csatlakoztathatók a virtuális gépekhez. A publicIpAddress hozzá kell rendelni egy Traffic Manager-profilban használandó DNS-nevet.

A PublicIPAddress erőforrások Azure Resource Manager erőforrásai. Nem léteznek a klasszikus üzemi modellben, és csak Traffic Manager Azure Resource Manager-élményekben támogatottak. A többi végpontot a Resource Manager és a klasszikus üzemi modell is támogatja.

Az Azure-végpontok használatakor Traffic Manager észleli a webalkalmazások leállítását és elindítását. Ez az állapot a végpont állapota szerint jelenik meg. További részletek: [Traffic Manager Endpoint monitoring](traffic-manager-monitoring.md#endpoint-and-profile-status) . A mögöttes szolgáltatás leállításakor Traffic Manager nem végez végpont-állapot-ellenőrzéseket, vagy a végpontra irányítja a forgalmat. A leállított példány esetében nem történik Traffic Manager számlázási esemény. A szolgáltatás újraindításakor a számlázás folytatódik, és a végpont jogosult a forgalom fogadására. Ez az észlelés nem vonatkozik a PublicIpAddress-végpontokra.

## <a name="external-endpoints"></a>Külső végpontok

A külső végpontok IPv4/IPv6-címekhez, teljes tartománynevek vagy az Azure-on kívüli szolgáltatásokhoz használatosak. Az IPv4/IPv6-cím végpontok használata lehetővé teszi a Traffic Manager számára a végpontok állapotának ellenőrzését anélkül, hogy DNS-nevet kellene adni számukra. Ennek eredményeképpen Traffic Manager tud válaszolni a lekérdezésekre egy/AAAA-rekorddal, amikor a végpontot válaszként adja vissza. Az Azure-on kívüli szolgáltatások tartalmazhatják a helyszíni vagy más szolgáltató által üzemeltetett szolgáltatásokat. A külső végpontok önállóan vagy az Azure-végpontokkal együtt is használhatók ugyanabban a Traffic Manager-profilban. Kivételt képeznek az IPv4-vagy IPv6-címekként megadott végpontok, amelyek csak külső végpontok lehetnek. Az Azure-végpontok külső végpontokkal való kombinálása különböző forgatókönyveket tesz lehetővé:

* Az Azure-t használó aktív-aktív vagy aktív-passzív feladatátvételi modellben nagyobb redundancia biztosítható egy meglévő helyszíni alkalmazáshoz. 
* Irányítsa a forgalmat olyan végpontokra, amelyek nem rendelkeznek hozzájuk társított DNS-névvel. A DNS-címkeresés általános késését is csökkenti, ha nem szeretné, hogy egy második DNS-lekérdezést futtasson a visszaadott DNS-név IP-címének lekéréséhez.
* Csökkentheti az alkalmazások késését a világ felhasználói számára, kiterjesztheti a meglévő helyszíni alkalmazásokat az Azure más földrajzi helyeire. További információ: [Traffic Manager "Performance" forgalom útválasztása](traffic-manager-routing-methods.md#performance).
* Nagyobb kapacitást biztosít egy meglévő helyszíni alkalmazás számára, akár folyamatosan, akár "burst-to-Cloud" megoldásként, amely az Azure-ban igénybe veszi az igényeknek megfelelő tüske használatát.

Bizonyos esetekben hasznos külső végpontokat használni az Azure-szolgáltatások hivatkozásához. Példákért tekintse meg a [gyakori kérdéseket](traffic-manager-faqs.md#traffic-manager-endpoints) . Az állapot-ellenőrzéseket az Azure-végpontok díjszabása alapján számítjuk fel, nem a külső végpontok arányát. Az Azure-végpontokkal ellentétben, ha leállítja vagy törli az alapul szolgáló szolgáltatást, az állapot-ellenőrzési számlázás folytatódik. A számlázás a Traffic Manager letiltását vagy törlését követően leáll.

## <a name="nested-endpoints"></a>Beágyazott végpontok

A beágyazott végpontok több Traffic Manager-profilt egyesítenek, hogy rugalmas forgalom-útválasztási sémákat hozzanak létre, amelyek támogatják a nagyobb és összetett központi telepítések igényeit. Beágyazott végpontok esetén a "Child" profil hozzáadása végpontként a "Parent" profilhoz. A gyermek-és a szülő profilok bármilyen típusú végpontot tartalmazhatnak, beleértve a többi beágyazott profilt is. 

További információ: [beágyazott Traffic Manager profilok](traffic-manager-nested-profiles.md).

## <a name="web-apps-as-endpoints"></a>Web Apps végpontként

Néhány megfontolandó szempont Web Apps Traffic Manager végpontként való konfigurálásakor:

1. Csak a "standard" SKU vagy újabb Web Apps jogosult a Traffic Manager használatára. Egy alacsonyabb SKU-hoz tartozó webalkalmazás hozzáadásának kísérlete sikertelen. Egy meglévő webalkalmazás SKU-jának visszaminősítése Traffic Manager már nem küld forgalmat a webalkalmazásnak. További információ a támogatott csomagokról: [app Service csomagok](https://azure.microsoft.com/pricing/details/app-service/plans/)
2. Ha egy végpont HTTP-kérést kap, a kérelemben szereplő "host" fejléc használatával határozza meg, hogy melyik webalkalmazásnak kell kiszolgálnia a kérelmet. A állomásfejléc tartalmazza a kérelem elindításához használt DNS-nevet (például "contosoapp.azurewebsites.net"). Ha más DNS-nevet szeretne használni a webalkalmazással, a DNS-nevet az alkalmazás egyéni tartománynevéként kell regisztrálni. Webalkalmazás-végpont Azure-végpontként való hozzáadásakor a rendszer automatikusan regisztrálja az Traffic Manager profil DNS-nevét az alkalmazáshoz. A rendszer automatikusan eltávolítja ezt a regisztrációt a végpont törlésekor.
3. Minden Traffic Manager profilhoz tartozhat egyetlen webalkalmazás-végpont az egyes Azure-régiókban. Ahhoz, hogy megkerülje a korlátozást, a webalkalmazást külső végpontként is konfigurálhatja. További információt a [Gyakori kérdések](traffic-manager-faqs.md#traffic-manager-endpoints)című témakörben talál.

## <a name="enabling-and-disabling-endpoints"></a>Végpontok engedélyezése és letiltása

A végpontok Traffic Manager való letiltása hasznos lehet a karbantartási módban lévő vagy újratelepített végpontról érkező forgalom ideiglenes eltávolításához. Ha a végpont ismét fut, akkor újra engedélyezhető.

A végpontokat engedélyezheti vagy letilthatja a Traffic Manager portál, a PowerShell, a CLI vagy a REST API használatával.

> [!NOTE]
> Az Azure-végpontok letiltása semmi köze az Azure-beli üzembe helyezési állapotához. Egy Azure-szolgáltatás (például egy virtuális gép vagy egy webalkalmazás továbbra is fut, és akkor is képes fogadni a forgalmat, ha a Traffic Manager le van tiltva.) A forgalom közvetlenül a szolgáltatási példányhoz fordulhat, nem pedig a Traffic Manager profil DNS-nevével. További információ: [how Traffic Manager Works](traffic-manager-how-it-works.md).

Az egyes végpontok a forgalom fogadására való jelenlegi jogosultsága az alábbi tényezőktől függ:

* A profil állapota (engedélyezve/letiltva)
* A végpont állapota (engedélyezve/letiltva)
* Az adott végpont állapot-ellenőrzésének eredményei

Részletekért lásd: [Traffic Manager végpont figyelése](traffic-manager-monitoring.md#endpoint-and-profile-status).

> [!NOTE]
> Mivel Traffic Manager a DNS-szinten működik, nem tudja befolyásolni a meglévő kapcsolatokat egyetlen végpontra sem. Ha egy végpont nem érhető el, Traffic Manager egy másik elérhető végpontra irányítja az új kapcsolatokat. A letiltott vagy nem kifogástalan végpont mögötti gazdagép azonban továbbra is fogadhat forgalmat a meglévő kapcsolatokon keresztül, amíg a munkamenetek le nem állnak. Az alkalmazásoknak korlátoznia kell a munkamenet időtartamát, hogy a forgalom leálljon a meglévő kapcsolatokból.

Ha egy profil összes végpontja le van tiltva, vagy ha maga a profil le van tiltva, akkor Traffic Manager "NXDOMAIN" választ küld egy új DNS-lekérdezésre.

## <a name="faqs"></a>Gyakori kérdések

* [Használhatok Traffic Manager több előfizetésből származó végpontokkal?](./traffic-manager-faqs.md#can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions)

* [Használhatom Traffic Manager a Cloud Service átmeneti tárolóhelyeit?](./traffic-manager-faqs.md#can-i-use-traffic-manager-with-cloud-service-staging-slots)

* [Támogatja a Traffic Manager az IPv6-végpontokat?](./traffic-manager-faqs.md#does-traffic-manager-support-ipv6-endpoints)

* [Használhatok több webalkalmazással rendelkező Traffic Manager ugyanabban a régióban?](./traffic-manager-faqs.md#can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region)

* [Hogyan az Traffic Manager-profil Azure-végpontját egy másik erőforráscsoporthoz helyezi át?](./traffic-manager-faqs.md#how-do-i-move-my-traffic-manager-profiles-azure-endpoints-to-a-different-resource-group-or-subscription)

## <a name="next-steps"></a>Következő lépések

* Ismerje meg, [Hogyan működik a Traffic Manager](traffic-manager-how-it-works.md).
* Ismerkedjen meg Traffic Manager [végpontok figyelésével és az automatikus feladatátvételsel](traffic-manager-monitoring.md).
* További információ a Traffic Manager [forgalom-útválasztási módszerekről](traffic-manager-routing-methods.md).