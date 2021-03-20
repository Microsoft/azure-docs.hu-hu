---
title: Alias-rekordok áttekintése – Azure DNS
description: Ez a cikk a Microsoft Azure DNS-ben található alias-rekordok támogatását ismerteti.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 08/09/2019
ms.author: rohink
ms.openlocfilehash: 8b2576669357aae7e5fe423515933c2ce4a23a7d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94954477"
---
# <a name="azure-dns-alias-records-overview"></a>Az Azure DNS-aliasrekordok áttekintése

Azure DNS az alias-rekordok egy DNS-rekordhalmaz minősítései. A DNS-zónán belül más Azure-erőforrásokra is hivatkozhatnak. Létrehozhat például egy olyan alias-rekordot, amely egy Azure nyilvános IP-címre hivatkozik egy rekord helyett. Az alias-rekord dinamikusan egy Azure nyilvános IP-cím szolgáltatási példányára mutat. Ennek eredményeképpen az alias-rekordok zökkenőmentesen frissülnek a DNS-feloldás során.

Egy Azure DNS zónában a következő bejegyzéstípusok esetében támogatott az alias-rekord: 

- A
- AAAA
- CNAME

> [!NOTE]
> Ha egy [Azure Traffic Manager-profilra](../traffic-manager/quickstart-create-traffic-manager-profile.md) mutató alias-rekordot szeretne használni az a vagy AAAA bejegyzéstípushoz, meg kell győződnie arról, hogy a Traffic Manager-profil csak [külső végpontokkal](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints)rendelkezik. Meg kell adnia az IPv4-vagy IPv6-címeket a Traffic Manager külső végpontjai számára. A végpontokban nem használhatók teljes tartománynevek (FQDN-EK). Ideális esetben statikus IP-címeket használjon.

## <a name="capabilities"></a>Képességek

- **Mutasson egy nyilvános IP-erőforrásra egy DNS-A/AAAA-rekorddal.** Létrehozhat egy/AAAA-rekordot, és egy alias-rekordot állíthat be úgy, hogy egy nyilvános IP-erőforrásra mutasson (standard vagy alapszintű). A DNS-rekordtípus automatikusan megváltozik, ha a nyilvános IP-cím megváltozik vagy törölve lett. A helytelen IP-címekre mutató DNS-rekordokat nem lehet elkerülni.

   A jelenlegi határérték 20 alias-rekordhalmazt állít be erőforrásként.

- **Mutasson egy Traffic Manager-profilra egy DNS A/AAAA/CNAME-rekorddal.** Létrehozhat egy egy/AAAA vagy CNAME rekordhalmazt, és alias-rekordokat használva rámutathat Traffic Manager profilra. Ez különösen akkor hasznos, ha egy zóna csúcsán kell átirányítani a forgalmat, mivel a hagyományos CNAME rekordok nem támogatottak a zóna csúcspontján. Tegyük fel például, hogy a Traffic Manager profilja myprofile.trafficmanager.net, és az üzleti DNS-zónája contoso.com. Létrehozhat egy A/AAAA típusú alias-rekordhalmazt a contoso.com (a zóna csúcspontja) és a myprofile.trafficmanager.net pontra.
- **Mutasson egy Azure Content Delivery Network (CDN) végpontra**. Ez akkor hasznos, ha statikus webhelyeket hoz létre az Azure Storage és a Azure CDN használatával.
- **Mutasson egy másik DNS-rekordhalmazra ugyanazon a zónán belül.** Az aliasrekordok hivatkozhatnak más azonos típusú rekordhalmazokra. Egy DNS CNAME-rekord például lehet egy másik CNAME-rekord aliasa. Ez a megoldás akkor hasznos, ha azt szeretné, hogy bizonyos rekordhalmazok aliasok és néhány nem alias legyen.

## <a name="scenarios"></a>Forgatókönyvek

Néhány gyakori forgatókönyv az alias-rekordokhoz.

### <a name="prevent-dangling-dns-records"></a>DNS-rekordok lelógó használatának megakadályozása

A hagyományos DNS-rekordokkal kapcsolatos gyakori probléma a rekordok letétele. Például olyan DNS-rekordok, amelyek nem frissültek az IP-címek változásainak megfelelően. A probléma különösen a/AAAA vagy A CNAME rekordtípusok esetében fordul elő.

Ha a célként megadott IP-cím vagy CNAME-rekord már nem létezik, akkor a hozzá tartozó DNS-rekordot manuálisan kell frissíteni, ha a DNS-zóna egy hagyományos rekordot tartalmaz. Egyes szervezeteknél előfordulhat, hogy a manuális frissítés a feldolgozási problémák, illetve a szerepkörök és a társított jogosultsági szintek elkülönítése miatt nem zajlik időben. Előfordulhat például, hogy a szerepkör egy alkalmazáshoz tartozó CNAME vagy IP-cím törlésére jogosult. De nem rendelkezik elegendő jogosultsággal a célokra mutató DNS-rekord frissítéséhez. A DNS-rekord frissítésének késleltetése miatt előfordulhat, hogy a felhasználók áramkimaradást okozhatnak.

Az alias-rekordok megakadályozzák, hogy a hivatkozások szorosan összekapcsolják egy DNS-rekord életciklusát egy Azure-erőforrással. Vegyünk például egy olyan DNS-rekordot, amely alias-rekordként van minősítve, hogy egy nyilvános IP-címre vagy egy Traffic Manager profilra mutasson. Ha törli a mögöttes erőforrásokat, a DNS-alias rekord üres halmaz lesz. A továbbiakban nem hivatkozik a törölt erőforrásra.

### <a name="update-dns-record-set-automatically-when-application-ip-addresses-change"></a>DNS-rekord automatikus frissítése az alkalmazás IP-címeinek módosításakor

Ez a forgatókönyv hasonló az előzőhöz. Lehet, hogy egy alkalmazás át lett helyezve, vagy a mögöttes virtuális gép újraindul. Az alias rekord ezután automatikusan frissül, amikor az IP-cím módosul a mögöttes nyilvános IP-erőforráshoz. Ezzel elkerülhető, hogy a felhasználók egy másik, a régi nyilvános IP-címet hozzárendelő alkalmazáshoz legyenek irányítva.

### <a name="host-load-balanced-applications-at-the-zone-apex"></a>Elosztott terhelésű alkalmazások a zóna csúcsán

A DNS protokoll megakadályozza a CNAME rekordok hozzárendelését a zóna csúcsán. Például ha a tartománya contoso.com; a somelabel.contoso.com CNAME rekordokat hozhat létre; a CNAME nem hozható létre a contoso.com számára.
Ez a korlátozás olyan alkalmazás-tulajdonosoknak nyújt problémát, akik az [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)mögött elosztott terhelésű alkalmazásokkal rendelkeznek. Mivel a Traffic Manager profil használata megköveteli egy CNAME rekord létrehozását, nem lehet a zóna csúcsán lévő Traffic Manager profilra mutatni.

Ez a probléma alias-rekordok használatával oldható meg. A CNAME rekordoktól eltérően az alias-rekordok a zóna csúcsán jönnek létre, és az alkalmazás tulajdonosai használhatják a zóna csúcs-rekordját egy olyan Traffic Manager-profilra, amely külső végpontokkal rendelkezik. Az alkalmazások tulajdonosai ugyanarra a Traffic Manager-profilra mutatnak, amelyet a DNS-zónán belüli bármely más tartományhoz használtak.

Például a contoso.com és a www \. contoso.com ugyanarra a Traffic Manager profilra mutathat. Ha többet szeretne megtudni az alias-rekordok Azure Traffic Manager-profilokkal való használatáról, tekintse meg a következő lépések című szakaszt.

### <a name="point-zone-apex-to-azure-cdn-endpoints"></a>Pont Azure CDN végpontokhoz

A Traffic Manager-profilhoz hasonlóan az alias-rekordok is használhatók a DNS-zóna csúcspontjának Azure CDN végpontokra való rámutatása érdekében. Ez akkor hasznos, ha statikus webhelyeket hoz létre az Azure Storage és a Azure CDN használatával. Ezt követően a webhelyhez a "www" előtag nélkül is hozzáférhet a DNS-névhez.

Ha például a statikus webhely neve `www.contoso.com` , a felhasználók hozzáférhetnek a webhelyhez anélkül, `contoso.com` hogy a www-t a DNS-névnek kell megadnia.

Az előzőekben leírtak szerint a CNAME rekordok nem támogatottak a zóna csúcspontján. Ezért nem használhat CNAME-rekordot a CDN-végpontra irányuló contoso.com pontra. Ehelyett használhat alias-rekordot úgy, hogy a zóna csúcsát közvetlenül egy CDN-végpontra irányítsa.

> [!NOTE]
> Az Akamai-től Azure CDN CDN-végpontokra mutató, a zónákhoz tartozó csúcsok jelenleg nem támogatottak.

## <a name="next-steps"></a>Következő lépések

Az alias-rekordokkal kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- [Oktatóanyag: alias-rekord konfigurálása egy Azure nyilvános IP-címre való hivatkozáshoz](tutorial-alias-pip.md)
- [Oktatóanyag: aliasrekord konfigurálása a hogy, támogassa a legfelső szintű tartományneveket a Traffic Managerrel](tutorial-alias-tm.md)
- [DNS – gyakori kérdések](./dns-faq.md#alias-records)