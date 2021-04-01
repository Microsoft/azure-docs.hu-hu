---
title: A tartomány és a TLS/SSL-tanúsítványok hibáinak megoldása
description: A tartomány-vagy TLS/SSL-tanúsítvány Azure App Serviceban való konfigurálásakor felmerülő gyakori problémák megoldásait találja.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.topic: article
ms.date: 03/01/2019
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: 691cbd79e82432c8e919dcbb51642a76000296dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97607609"
---
# <a name="troubleshoot-domain-and-tlsssl-certificate-problems-in-azure-app-service"></a>A tartományok és a TLS/SSL-tanúsítványok problémáinak elhárítása Azure App Service

Ez a cikk azokat a gyakori problémákat sorolja fel, amelyek akkor fordulhatnak elő, amikor a Azure App Service webalkalmazásaihoz konfigurál egy tartomány-vagy TLS/SSL-tanúsítványt. Emellett leírja a problémák lehetséges okait és megoldásait is.

Ha a cikk bármely pontján további segítségre van szüksége, vegye fel a kapcsolatot az [MSDN-és stack overflow fórumokban](https://azure.microsoft.com/support/forums/)található Azure-szakértőkkel. Másik lehetőségként egy Azure-támogatási incidenst is megadhat. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a **támogatás kérése** lehetőséget.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="certificate-problems"></a>Tanúsítványokkal kapcsolatos problémák

### <a name="you-cant-add-a-tlsssl-certificate-binding-to-an-app"></a>Nem adható hozzá TLS/SSL-tanúsítvány kötése egy alkalmazáshoz 

#### <a name="symptom"></a>Hibajelenség

Ha TLS-kötést ad hozzá, a következő hibaüzenet jelenik meg:

"Nem sikerült hozzáadni az SSL-kötést. Nem állítható be a tanúsítvány a meglévő VIP-hez, mert egy másik VIP már használja ezt a tanúsítványt. "

#### <a name="cause"></a>Ok

Ez a probléma akkor fordulhat elő, ha több IP-alapú SSL-kötést használ ugyanahhoz az IP-címhez több alkalmazás között. Az A alkalmazás például egy olyan IP-alapú SSL-t tartalmaz, amely egy régi tanúsítvánnyal rendelkezik. A B alkalmazásnak van egy IP-alapú SSL-je, amely egy új tanúsítvánnyal rendelkezik ugyanahhoz az IP-címhez. Ha az alkalmazás TLS-kötését az új tanúsítvánnyal frissíti, akkor ez a hiba meghiúsul, mert ugyanazt az IP-címet használja egy másik alkalmazáshoz. 

#### <a name="solution"></a>Megoldás 

A probléma megoldásához használja az alábbi módszerek egyikét:

- Törölje az IP-alapú SSL-kötést azon az alkalmazáson, amely a régi tanúsítványt használja. 
- Hozzon létre egy új IP-alapú SSL-kötést, amely az új tanúsítványt használja.

### <a name="you-cant-delete-a-certificate"></a>A tanúsítvány nem törölhető 

#### <a name="symptom"></a>Hibajelenség

Amikor megpróbálja törölni a tanúsítványt, a következő hibaüzenet jelenik meg:

"Nem sikerült törölni a tanúsítványt, mert jelenleg egy TLS-/SSL-kötésben van használatban. A tanúsítvány törléséhez el kell távolítani a TLS-kötést. "

#### <a name="cause"></a>Ok

Ez a probléma akkor fordulhat elő, ha egy másik alkalmazás használja a tanúsítványt.

#### <a name="solution"></a>Megoldás

Távolítsa el az adott tanúsítványhoz tartozó TLS-kötést az alkalmazásokból. Ezután próbálja meg törölni a tanúsítványt. Ha továbbra sem tudja törölni a tanúsítványt, törölje az internetböngésző gyorsítótárát, majd nyissa meg újra a Azure Portal egy új böngészőablakban. Ezután próbálja meg törölni a tanúsítványt.

### <a name="you-cant-purchase-an-app-service-certificate"></a>Nem vásárolhat App Service tanúsítványt 

#### <a name="symptom"></a>Hibajelenség
A Azure Portal nem vásárolhat [Azure app Service tanúsítványt](./configure-ssl-certificate.md#import-an-app-service-certificate) .

#### <a name="cause-and-solution"></a>Ok és megoldás
Ez a probléma a következő okok bármelyike miatt fordulhat elő:

- Az App Service-csomag ingyenes vagy megosztott. Ezek az árképzési szintek nem támogatják a TLS-t. 

    **Megoldás**: frissítse a app Service tervet az alkalmazásról a standard szintre.

- Az előfizetéshez nem tartozik érvényes hitelkártya.

    **Megoldás**: adjon hozzá egy érvényes bankkártyát az előfizetéséhez. 

- Az előfizetési ajánlat nem támogatja App Service-tanúsítvány, például a Microsoft Student megvásárlását.  

    **Megoldás**: frissítse az előfizetését. 

- Az előfizetés elérte az előfizetésben engedélyezett vásárlások korlátját.

    **Megoldás**: app Service a tanúsítványokhoz legfeljebb 10 tanúsítvány vásárolható meg az utólagos elszámolású és az EA-előfizetések típusaihoz. Más előfizetési típusok esetén a korlát 3. A korlát növeléséhez forduljon az [Azure ügyfélszolgálatához](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Az App Service-tanúsítvány csalásként lett megjelölve. A következő hibaüzenetet kapta: "a tanúsítvány a lehetséges csalások miatt meg lett jelölve. A kérelem jelenleg felülvizsgálat alatt áll. Ha a tanúsítvány 24 órán belül nem lesz használható, forduljon az Azure ügyfélszolgálatához. "

    **Megoldás**: Ha a tanúsítvány csalásként van megjelölve, és 24 óra elteltével nem oldódik meg, kövesse az alábbi lépéseket:

    1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
    2. Lépjen **app Service a tanúsítványok** elemre, és válassza ki a tanúsítványt.
    3. A **tanúsítvány konfigurációjának** kiválasztása  >  **2. lépés:** a  >  **tartomány ellenőrzésének** ellenőrzése. Ez a lépés e-mailben értesítést küld az Azure-tanúsítvány szolgáltatójának a probléma megoldásához.

## <a name="custom-domain-problems"></a>Egyéni tartományi problémák

### <a name="a-custom-domain-returns-a-404-error"></a>Egy egyéni tartomány 404-es hibát ad vissza 

#### <a name="symptom"></a>Hibajelenség

Ha az Egyéni tartománynév használatával keres a webhelyre, a következő hibaüzenet jelenik meg:

"Hiba 404 – a webalkalmazás nem található."

#### <a name="cause-and-solution"></a>Ok és megoldás

**1\. ok** 

A konfigurált egyéni tartományból hiányzik egy CNAME vagy egy rekord. 

**Megoldás az 1. ok esetén**

- Ha hozzáadott egy rekordot, győződjön meg róla, hogy a TXT-rekord is hozzá van adva. További információt [az a rekord létrehozása](./app-service-web-tutorial-custom-domain.md#create-the-a-record)című témakörben talál.
- Ha nem kell használnia az alkalmazás legfelső szintű tartományát, azt javasoljuk, hogy egy rekord helyett CNAME-rekordot használjon.
- Ne használjon egyszerre egy CNAME rekordot és egy rekordot ugyanahhoz a tartományhoz. Ez a probléma ütközést okozhat, és megakadályozhatja a tartomány feloldását. 

**2\. ok** 

Előfordulhat, hogy az internetböngésző továbbra is gyorsítótárazza a tartomány régi IP-címét. 

**Megoldás a 2. okból**

Törölje a böngészőt. Windows-eszközök esetén futtathatja a parancsot `ipconfig /flushdns` . A [WhatsmyDNS.net](https://www.whatsmydns.net/) használatával ellenőrizze, hogy a tartomány az alkalmazás IP-címére mutat-e.

### <a name="you-cant-add-a-subdomain"></a>Nem adhat hozzá altartományt 

#### <a name="symptom"></a>Hibajelenség

Egy altartomány hozzárendeléséhez nem adhat hozzá új állomásnevet az alkalmazáshoz.

#### <a name="solution"></a>Megoldás

- Az előfizetés rendszergazdájával ellenőrizze, hogy van-e engedélye az állomásnév hozzáadására az alkalmazáshoz.
- Ha több altartományra van szüksége, javasoljuk, hogy változtassa meg az Azure tartománynév-szolgáltatás (DNS) üzemeltetését. Azure DNS használatával 500-állomásneveket adhat hozzá az alkalmazáshoz. További információ: [altartomány hozzáadása](/archive/blogs/waws/mapping-a-custom-subdomain-to-an-azure-website).

### <a name="dns-cant-be-resolved"></a>A DNS nem oldható fel

#### <a name="symptom"></a>Hibajelenség

A következő hibaüzenetet kapta:

"A DNS-rekord nem található."

#### <a name="cause"></a>Ok
Ez a probléma a következő okok egyike miatt fordul elő:

- Az élettartam (TTL) időszaka nem járt le. Ellenőrizze a tartomány DNS-konfigurációját, hogy meghatározza a TTL-értéket, majd várjon, amíg lejár az időszak.
- A DNS-konfiguráció helytelen.

#### <a name="solution"></a>Megoldás
- Várjon 48 órát a probléma megoldásához.
- Ha a DNS-konfigurációban módosíthatja a TTL-beállítást, módosítsa az értéket 5 percre, és ellenőrizze, hogy ez megoldja-e a problémát.
- A [WhatsmyDNS.net](https://www.whatsmydns.net/) használatával ellenőrizze, hogy a tartomány az alkalmazás IP-címére mutat-e. Ha nem, konfigurálja az a rekordot az alkalmazás megfelelő IP-címére.

### <a name="you-need-to-restore-a-deleted-domain"></a>A törölt tartományt vissza kell állítania 

#### <a name="symptom"></a>Hibajelenség
A tartomány már nem látható a Azure Portalban.

#### <a name="cause"></a>Ok 
Előfordulhat, hogy az előfizetés tulajdonosa véletlenül törölte a tartományt.

#### <a name="solution"></a>Megoldás
Ha a tartományt a hét napja kevesebb, mint hét napja törölte, a tartomány még nem indította el a törlési folyamatot. Ebben az esetben ugyanezt a tartományt is megvásárolhatja a Azure Portal ugyanazon előfizetés alatt. (Ügyeljen arra, hogy a keresőmezőbe írja be a pontos tartománynevet.) Erre a tartományra vonatkozóan nem számítunk fel újra díjat. Ha a tartományt több mint hét napja törölte, forduljon az [Azure támogatási szolgálatához](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a tartomány visszaállításával kapcsolatos segítségért.

## <a name="domain-problems"></a>Tartományi problémák

### <a name="you-purchased-a-tlsssl-certificate-for-the-wrong-domain"></a>TLS/SSL-tanúsítványt vásárolt a helytelen tartományhoz

#### <a name="symptom"></a>Hibajelenség

Nem megfelelő tartományhoz vásárolt App Service-tanúsítványt. A tanúsítvány nem frissíthető a megfelelő tartomány használatára.

#### <a name="solution"></a>Megoldás

Törölje a tanúsítványt, majd vásároljon egy új tanúsítványt.

Ha a hibás tartományt használó aktuális tanúsítvány a "kiállított" állapotban van, akkor a tanúsítványért is fizetnie kell. App Service tanúsítványok nem téríthetők vissza, de az [Azure támogatási szolgálatával](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) is megtudhatja, hogy vannak-e más lehetőségek. 

### <a name="an-app-service-certificate-was-renewed-but-the-app-shows-the-old-certificate"></a>Egy App Service tanúsítvány megújítása megtörtént, de az alkalmazás a régi tanúsítványt jeleníti meg. 

#### <a name="symptom"></a>Hibajelenség

Az App Service-tanúsítvány megújítása megtörtént, de a App Service tanúsítványt használó alkalmazás továbbra is a régi tanúsítványt használja. Emellett figyelmeztetést kapott, hogy a HTTPS protokoll megadása kötelező.

#### <a name="cause"></a>Ok 
A App Service 48 órán belül automatikusan szinkronizálja a tanúsítványt. Amikor elforgat vagy frissít egy tanúsítványt, néha az alkalmazás továbbra is beolvassa a régi tanúsítványt, nem pedig az újonnan frissített tanúsítványt. Ennek az az oka, hogy a tanúsítvány-erőforrás szinkronizálásának feladata még nem fut. Kattintson a szinkronizálás elemre. A szinkronizálási művelet automatikusan frissíti a tanúsítványhoz tartozó állomásnév-kötéseket App Service anélkül, hogy leállást okozna az alkalmazásai számára.

#### <a name="solution"></a>Megoldás

A tanúsítvány szinkronizálását kényszerítheti:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza ki **app Service tanúsítványokat**, majd válassza ki a tanúsítványt.
2. Válassza a **regenerálás és szinkronizálás** lehetőséget, majd válassza a **szinkronizálás** lehetőséget. A szinkronizálás eltarthat egy ideig. 
3. A szinkronizálás befejezésekor a következő értesítés jelenik meg: "az összes erőforrás frissítése sikeresen megtörtént a legújabb tanúsítvánnyal."

### <a name="domain-verification-is-not-working"></a>A tartomány ellenőrzése nem működik 

#### <a name="symptom"></a>Hibajelenség 
A App Service tanúsítványhoz a tanúsítvány használatának megkezdése előtt tartományi ellenőrzés szükséges. Ha az **ellenőrzés** lehetőséget választja, a folyamat sikertelen lesz.

#### <a name="solution"></a>Megoldás
A tartomány manuális ellenőrzése TXT-rekord hozzáadásával:

1. Lépjen a tartománynevet biztosító tartománynév-szolgáltatóhoz (DNS).
1. Adjon meg egy, a saját tartományára vonatkozó TXT típusú rekordot, amely az Azure Portalon megjelenített tartományi jogkivonat értékét használja. 

Várjon néhány percet a DNS-propagálás futtatására, majd kattintson a **frissítés** gombra az ellenőrzés elindításához. 

Alternatív megoldásként a HTML-weblap metódus használatával manuálisan is ellenőrizheti a tartományt. Ezzel a módszerrel a hitelesítésszolgáltató megerősítheti annak a tartománynak a tulajdonjogát, amelyre a tanúsítványt kiállították.

1. Hozzon létre egy {domain ellenőrző jogkivonat}. html nevű HTML-fájlt. A fájl tartalmának a tartomány-ellenőrzési jogkivonat értékének kell lennie.
1. Töltse fel ezt a fájlt a tartományt üzemeltető webkiszolgáló gyökerébe.
1. A tanúsítvány állapotának megtekintéséhez válassza a **frissítés** lehetőséget. Az ellenőrzés befejezéséhez néhány percet is igénybe vehet.

Ha például a azure.com standard tanúsítványát vásárolja meg a tartomány-ellenőrzési jogkivonat 1234abcd, egy webes kérelemnek https://azure.com/1234abcd.html kell visszaadnia a 1234abcd. 

> [!IMPORTANT]
> A tanúsítvány megrendelése csak 15 napot tartalmaz a tartomány-ellenőrzési művelet befejezéséhez. 15 nap elteltével a hitelesítésszolgáltató megtagadja a tanúsítványt, és a tanúsítványért nem számítunk fel díjat. Ebben a helyzetben törölje a tanúsítványt, és próbálkozzon újra.
>
> 

### <a name="you-cant-purchase-a-domain"></a>Nem vásárolhat tartományt

#### <a name="symptom"></a>Hibajelenség
A Azure Portal nem vásárolhat App Service tartományt.

#### <a name="cause-and-solution"></a>Ok és megoldás

Ez a probléma a következő okok egyike miatt fordul elő:

- Nincs hozzáadva hitelkártya az Azure-előfizetéshez, vagy a hitelkártya érvénytelen.

    **Megoldás**: adjon hozzá egy érvényes bankkártyát az előfizetéséhez.

- Nem Ön az előfizetés tulajdonosa, így nem rendelkezik engedéllyel a tartományvásárláshoz.

    **Megoldás**: [rendelje hozzá a tulajdonosi szerepkört](../role-based-access-control/role-assignments-portal.md) a fiókhoz. Vagy forduljon az előfizetés rendszergazdájához, és kérjen engedélyt a tartomány megvásárlására.
- Az előfizetésében elérte a tartományvásárlások maximális számát. Jelenleg legfeljebb 20 tartományt vásárolhat.

    **Megoldás**: a korlát növelésének igényléséhez forduljon az [Azure ügyfélszolgálatához](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Az Azure-előfizetésének típusa nem támogatja App Service-tartományok vásárlását.

    **Megoldás**: frissítse az Azure-előfizetést egy másik előfizetés-típusra, például egy utólagos elszámolású előfizetésre.

### <a name="you-cant-add-a-host-name-to-an-app"></a>Nem adhat hozzá állomásnevet az alkalmazáshoz 

#### <a name="symptom"></a>Hibajelenség

Állomásnév hozzáadásakor a folyamat nem ellenőrzi és ellenőrzi a tartományt.

#### <a name="cause"></a>Ok 

Ez a probléma a következő okok egyike miatt fordul elő:

- Nincs engedélye az állomásnév hozzáadására.

    **Megoldás**: kérje meg az előfizetés rendszergazdáját, hogy adjon engedélyt az állomásnév hozzáadására.
- Nem lehetett ellenőrizni a tartomány tulajdonjogát.

    **Megoldás**: Ellenőrizze, hogy a CNAME vagy egy rekord megfelelően van-e konfigurálva. Ha egyéni tartományt szeretne hozzárendelni egy alkalmazáshoz, hozzon létre egy CNAME rekordot vagy egy rekordot. Ha legfelső szintű tartományt kíván használni, A és A TXT típusú rekordokat kell használnia:

    |Rekordtípus|Gazdagép|Mutasson a|
    |------|------|-----|
    |A|@|Alkalmazás IP-címe|
    |TXT|@|`<app-name>.azurewebsites.net`|
    |CNAME|www|`<app-name>.azurewebsites.net`|

## <a name="faq"></a>GYIK

**Meg kell-e adni a saját webhelyem egyéni tartományát a vásárlás után?**

Amikor megvásárol egy tartományt a Azure Portal, a App Service alkalmazás automatikusan az adott egyéni tartomány használatára van konfigurálva. Nem kell további lépéseket végrehajtania. További információkért tekintse meg [Azure app Service saját súgóját: adjon hozzá egy egyéni tartománynevet](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name) a Channel9.

**Használhatok a Azure Portalban megvásárolt tartományt egy Azure-beli virtuális gépre való Rámutatás helyett?**

Igen, a tartományt egy virtuális gépre irányíthatja. További információt az [egyéni tartománybeállítások egy Azure-szolgáltatáshoz az Azure DNS használatával történő megadását](../dns/dns-custom-domain.md) ismertető cikkben talál.

**A saját tartománya a GoDaddy vagy a Azure DNS?**

App Service tartományok a GoDaddy használatával tartományi regisztrációt használnak, és Azure DNS a tartományok üzemeltetéséhez. 

**Engedélyezve van az automatikus megújítás, de a saját tartományra vonatkozó megújítási értesítés is érkezett e-mailben. Mit tegyek?**

Ha engedélyezve van az automatikus megújítás, semmilyen műveletet nem kell elvégeznie. Az értesítő e-mailt arról tájékoztatjuk, hogy a tartomány le van zárva, és manuálisan megújítható, ha nincs engedélyezve az automatikus megújítás.

**Kell-e fizetnem a saját tartományt üzemeltető Azure DNSért?**

A tartományi vásárlás kezdeti díja csak a tartományi regisztrációra vonatkozik. A regisztrációs költségek mellett a használat alapján felmerül a Azure DNS díja. További információ: [Azure DNS díjszabása](https://azure.microsoft.com/pricing/details/dns/) .

**Korábban megvásároltam a tartományt a Azure Portal, és szeretném áttérni a GoDaddy-gazdagépről Azure DNS-üzemeltetésre. Hogyan tehetem meg ezt?**

A Azure DNS-üzemeltetésre való Migrálás nem kötelező. Ha Azure DNSre szeretne áttérni, a Azure Portal a tartományi felügyeleti felület a Azure DNSre való áttéréshez szükséges lépésekről nyújt információt. Ha a tartományt App Service keresztül vásárolta meg, akkor a GoDaddy-gazdagépről a Azure DNSra történő áttelepítés viszonylag zökkenőmentes eljárás.

**Szeretném megvásárolni a tartományt a App Service tartományból, de a Azure DNS helyett a saját tartományt is üzemeltetem a GoDaddyben?**

2017. július 24-én kezdődően a portálon megvásárolt App Service tartományok a Azure DNSon futnak. Ha inkább egy másik szolgáltatót szeretne használni, a webhelyére kell lépnie egy tartományi üzemeltetési megoldás beszerzéséhez.

**Kell-e fizetnem a saját tartomány adatvédelem terén?**

Ha a Azure Portal keresztül vásárol egy tartományt, lehetősége van arra, hogy az adatvédelmet külön díj nélkül adja hozzá. Ez az egyik előnye, hogy a tartomány megvásárlása Azure App Service használatával történik.

**Ha úgy döntöttem, hogy már nem szeretném, hogy a tartományom, Visszakaphatom a pénzemet?**

Egy tartomány megvásárlásakor nem számítunk fel öt napos időszakra, amely során eldöntheti, hogy nem kívánja-e a tartományt használni. Ha úgy dönt, hogy nem szeretné, hogy a tartomány az adott ötéves időszakon belül legyen, nem számítunk fel díjat. (. az Egyesült királyságbeli tartományok kivételt képeznek. Ha egy. Egyesült királyságbeli tartományt vásárol, azonnal felszámítja, és nem lehet visszatéríteni.)

**Használhatom a tartományt egy másik Azure App Service alkalmazásban is az előfizetésben?**

Igen. Ha a Azure Portal az egyéni tartományokhoz és a TLS-panelhez fér hozzá, a megvásárolt tartományok láthatók. Az alkalmazást úgy is konfigurálhatja, hogy az adott tartományokat használja.

**Átvihetek egy tartományt egy előfizetésből egy másik előfizetésbe?**

A [Move-AzResource](/powershell/module/az.Resources/Move-azResource) PowerShell-parancsmag használatával áthelyezheti a tartományt egy másik előfizetésre vagy erőforrás-csoportra.

**Hogyan kezelhetem az egyéni tartományt, ha jelenleg nem Azure App Service alkalmazásom van?**

A tartományt akkor is kezelheti, ha nincs App Service webalkalmazása. A tartomány használható Azure-szolgáltatásokhoz, például virtuális gépekhez, tároláshoz stb. Ha a tartományt a App Service Web Appshoz kívánja használni, akkor meg kell adnia egy webalkalmazást, amely nem szerepel a Ingyenes App Service tervben ahhoz, hogy a tartomány a webalkalmazáshoz legyen kötve.

**Áthelyezhetek egy webalkalmazást egyéni tartománnyal egy másik előfizetésbe vagy App Service Environment v1-ről v2-re?**

Igen, áthelyezheti a webalkalmazást az előfizetések között. Kövesse az [erőforrások Azure-ban való áthelyezésének](../azure-resource-manager/management/move-resource-group-and-subscription.md)útmutatását. A webalkalmazás áthelyezése néhány korlátozással jár. További információ: [app Service erőforrások áthelyezésének korlátozásai](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md).

A webalkalmazás áthelyezése után az egyéni tartományokban lévő tartományok állomásnév-kötései változatlanok maradnak. Az állomásnév-kötések konfigurálásához nincs szükség további lépésekre.