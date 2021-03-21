---
title: Azure bejárati ajtó – helyettesítő karakteres tartományok támogatása
description: Ebből a cikkből megtudhatja, hogyan támogatja az Azure bejárati ajtó a helyettesítő tartományok leképezését és kezelését az egyéni tartományok listájában.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2020
ms.author: duau
ms.openlocfilehash: 18504f1ed4200889b20c9608c9c0ad2c13c9aaa5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94425762"
---
# <a name="wildcard-domains"></a>Helyettesítő karakteres tartományok

Az APEX-tartományok és altartományok mellett az Azure bejárati profiljához tartozó helyettesítő tartományokat az előtér-gazdagépekhez vagy az egyéni tartományokhoz is hozzárendelheti. Ha az Azure-beli bejárati ajtó konfigurációjában helyettesítő karakteres tartományok vannak, a több altartománynál is leegyszerűsíti a forgalom-útválasztási viselkedést ugyanazon útválasztási szabályból származó API-, alkalmazás-vagy webhelyekhez. Nem kell módosítania a konfigurációt, hogy külön adja hozzá vagy adja meg az egyes altartományokat. Például megadhatja a, a és az útválasztási `customer1.contoso.com` `customer2.contoso.com` `customerN.contoso.com` szabályt ugyanazzal az útválasztási szabállyal, és hozzáadhatja a helyettesítő karaktert `*.contoso.com` .

A helyettesítő karakteres tartományok támogatásával javított főbb forgatókönyvek a következők:

- Nem kell minden altartományt bevezetni az Azure bevezető ajtajának profiljába, majd engedélyeznie kell a HTTPS-t az egyes altartományokhoz tartozó tanúsítványok kötéséhez.
- Ha egy alkalmazás új altartományt hoz létre, már nem kell módosítania az éles Azure-beli ajtó konfigurációját. Korábban fel kellett vennie az altartományt, hozzá kell kötnie egy tanúsítványt, csatolnia kell egy webalkalmazási tűzfal (WAF) szabályzatot, majd hozzá kell adnia a tartományt a különböző útválasztási szabályokhoz.

> [!NOTE]
> Jelenleg a helyettesítő karakteres tartományok hozzáadása a Azure DNSon keresztül csak az API, a PowerShell és az Azure CLI használatával támogatott. Nem érhető el a helyettesítő karakteres tartományok hozzáadásának és kezelésének támogatása a Azure Portalban.

## <a name="adding-wildcard-domains"></a>Helyettesítő karakteres tartományok hozzáadása

Az előtér-gazdagépekhez vagy-tartományokhoz tartozó szakaszhoz hozzáadhat helyettesítő karaktert. Az altartományokhoz hasonlóan az Azure bejárati ajtó ellenőrzi, hogy a helyettesítő tartományhoz CNAME rekord van-e hozzárendelve. Ez a DNS-megfeleltetés lehet egy közvetlen CNAME rekord leképezése, például a `*.contoso.com` leképezéshez `contoso.azurefd.net` . Vagy használhat afdverify ideiglenes leképezést is. Például a `afdverify.contoso.com` leképezett érték `afdverify.contoso.azurefd.net` ellenőrzi a CNAME rekord leképezését a helyettesítő karakternél.

> [!NOTE]
> Az Azure DNS helyettesítő rekordok használatát is támogatja.

Az előtér-gazdagépeken a helyettesítő karakteres tartomány több egyszintű altartományát is hozzáadhatja az előtér-gazdagépek korlátjának megfelelően. Ehhez a funkcióhoz a következő funkciók szükségesek:

- Eltérő útvonal definiálása egy altartományhoz, mint a többi tartomány (a helyettesítő tartományból).

- Egy adott altartományhoz eltérő WAF-házirend van. Például `*.contoso.com` lehetővé teszi a hozzáadást `foo.contoso.com` anélkül, hogy újra kellene bizonyítania a tartomány tulajdonjogát. Ez azonban nem engedélyezett, `foo.bar.contoso.com` mert nem egyetlen szintű altartománya `*.contoso.com` . `foo.bar.contoso.com`További tartomány tulajdonjogának ellenőrzése nélkül hozzá kell adni a hozzáadáshoz `*.bar.contosonews.com` .

Bizonyos korlátozásokkal adhat hozzá helyettesítő tartományokat és altartományokat:

- Ha egy helyettesítő karaktert tartalmazó tartományt ad hozzá egy Azure-beli előtérben lévő profilhoz:
  - A helyettesítő karakteres tartomány nem vehető fel más Azure-előtérben lévő profilba.
  - A helyettesítő karakteres tartomány első szintű altartománya nem adható hozzá egy másik Azure-beli bejárati profilhoz vagy egy Azure Content Delivery Network-profilhoz.
- Ha egy helyettesítő karakteres tartomány altartománya már hozzá van adva egy Azure bejárati profilhoz vagy egy Azure Content Delivery Network-profilhoz, akkor a helyettesítő tartomány nem használható az Azure bejárati profiljához.
- Ha két profil (Azure bejárati ajtó vagy Azure Content Delivery Network) egy gyökértartomány különböző altartományával rendelkezik, akkor a helyettesítő tartományokat nem lehet a profilok egyikéhez sem felvenni.

## <a name="certificate-binding"></a>Tanúsítvány kötése

A helyettesítő karakteres tartományon a HTTPS-forgalom fogadásához engedélyeznie kell a HTTPS protokollt a helyettesítő tartományon. A helyettesítő karakteres tartományhoz tartozó tanúsítvány kötéséhez helyettesítő tanúsítvány szükséges. Ennek a tanúsítványnak a tulajdonos neve is lehet a helyettesítő tartománya.

> [!NOTE]
> Jelenleg csak a saját egyéni SSL-tanúsítvány használata lehetőség áll rendelkezésre a HTTPS engedélyezéséhez helyettesítő karakteres tartományokhoz. Az Azure-beli előtérben felügyelt tanúsítványok nem használhatók helyettesítő tartományokhoz.

Megadhatja, hogy ugyanazokat a helyettesítő tanúsítványokat használja Azure Key Vault vagy az Azure-beli elülső ajtó felügyelt tanúsítványainak altartományokhoz.

Ha olyan helyettesítő karakteres tartományhoz ad hozzá altartományt, amelyhez már tartozik tanúsítvány társítva, akkor a HTTPS nem tiltható le az altartományhoz. Az altartomány a helyettesítő karakteres tartományhoz tartozó tanúsítvány kötését használja, kivéve, ha egy másik Key Vault vagy az Azure-beli elülső ajtó által felügyelt tanúsítvány felülbírálja.

## <a name="waf-policies"></a>WAF szabályzatok

A WAF szabályzatok a más tartományokhoz hasonló helyettesítő tartományokhoz is csatlakoztathatók. Egy másik WAF-házirend alkalmazható egy helyettesítő tartomány altartományára is. Az altartományok esetében meg kell adnia a használni kívánt WAF-házirendet, még akkor is, ha az azonos a helyettesítő karakteres tartománnyal. Az altartományok *nem* öröklik automatikusan a WAF szabályzatot a helyettesítő tartományból.

Ha nem szeretné, hogy egy WAF-házirend egy altartományhoz fusson, hozzon létre egy üres WAF szabályzatot, amely nem felügyelt vagy egyéni szabályrendszerek-t tartalmaz.

## <a name="routing-rules"></a>Útválasztási szabályok

Útválasztási szabályok konfigurálásakor kiválaszthatja a helyettesítő karakteres tartományt előtér-gazdagépként. Eltérő útvonal-viselkedést is használhat a helyettesítő tartományokhoz és altartományokhoz. Az Azure-beli [bejárati útvonal egyeztetésének módja](front-door-route-matching.md)című cikkben leírtak szerint a tartományra vonatkozó, a különböző útválasztási szabályokban szereplő legpontosabb egyezés van kiválasztva futásidőben.

> [!IMPORTANT]
> Az útválasztási szabályokban meg kell egyeznie az elérésiút-mintázattal, vagy az ügyfelek hibaüzeneteket fognak látni. Tegyük fel, hogy két útválasztási szabálya van, például az 1. útvonal (az `*.foo.com/*` A háttér-készlethez leképezve) és a 2-es útvonal ( `/bar.foo.com/somePath/*` a B háttér-készletre van leképezve). Ezt követően egy kérelem érkezik a következőre: `bar.foo.com/anotherPath/*` . Az Azure bejárati ajtaja kiválasztja a 2. útvonalat egy adott tartományi egyeztetés alapján, csak az útvonalakon nem talál egyező elérésiút-mintákat.

## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogyan [hozhat létre Azure-beli bejárati profilt](quickstart-create-front-door.md).
- Megtudhatja, hogyan [adhat hozzá egyéni tartományt az Azure](front-door-custom-domain.md)-beli előtérben.
- Megtudhatja, hogyan [engedélyezheti a HTTPS-t egy egyéni tartományon](front-door-custom-domain-https.md).
