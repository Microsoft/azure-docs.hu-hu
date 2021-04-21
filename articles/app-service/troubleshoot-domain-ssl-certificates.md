---
title: Tartomány- és TLS-/SSL-tanúsítványok hibaelhárítása
description: Megoldásokat találhat az olyan gyakori problémákra, amelyek akkor merülhetnek fel, amikor tartományt vagy TLS-/SSL-tanúsítványt konfigurál a Azure App Service.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.topic: article
ms.date: 03/01/2019
ms.author: genli
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: c2c09e1a30c9cef4d65b2d5443481c84ab779af8
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833831"
---
# <a name="troubleshoot-domain-and-tlsssl-certificate-problems-in-azure-app-service"></a>Tartomány- és TLS-/SSL-tanúsítványokkal kapcsolatos problémák elhárítása a Azure App Service

Ez a cikk azokat a gyakori problémákat sorolja fel, amelyek akkor merülhetnek fel, amikor tartományt vagy TLS-/SSL-tanúsítványt konfigurál a webalkalmazások Azure App Service. Emellett ismerteti az ilyen problémák lehetséges okait és megoldásait.

Ha a cikk bármely pontján további segítségre van szüksége, lépjen kapcsolatba az Azure-szakértőkkel az MSDN és a [Stack Overflow fórumain.](https://azure.microsoft.com/support/forums/) Alternatív megoldásként be is Azure-támogatás incidenst. A webhely Azure ügyfélszolgálata [válassza](https://azure.microsoft.com/support/options/) a **Támogatás stb. lehetőséget.**

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="certificate-problems"></a>Tanúsítványokkal kapcsolatos problémák

### <a name="you-cant-add-a-tlsssl-certificate-binding-to-an-app"></a>Nem adhat TLS-/SSL-tanúsítványkötést egy alkalmazáshoz 

#### <a name="symptom"></a>Hibajelenség

TLS-kötés hozzáadásakor a következő hibaüzenet jelenik meg:

"Az SSL-kötés hozzáadása sikertelen volt. Nem állíthat be tanúsítványt a meglévő VIP-hez, mert egy másik VIP már használja ezt a tanúsítványt."

#### <a name="cause"></a>Ok

Ez a probléma akkor fordulhat elő, ha több IP-alapú SSL-kötése van ugyanannak az IP-címnek több alkalmazásban. Az A alkalmazás például ip-alapú SSL-lel és régi tanúsítvánnyal rendelkezik. A B alkalmazás IP-alapú SSL-lel rendelkezik, amely ugyanannak az IP-címnek az új tanúsítványát használja. Amikor frissíti az alkalmazás TLS-kötését az új tanúsítvánnyal, az ezzel a hibával meghiúsul, mert a rendszer ugyanazt az IP-címet használja egy másik alkalmazáshoz. 

#### <a name="solution"></a>Megoldás 

A probléma megoldásához használja az alábbi módszerek egyikét:

- Törölje a régi tanúsítványt használó alkalmazás IP-alapú SSL-kötését. 
- Hozzon létre egy új IP-alapú SSL-kötést, amely az új tanúsítványt használja.

### <a name="you-cant-delete-a-certificate"></a>Tanúsítvány nem törölhető 

#### <a name="symptom"></a>Hibajelenség

Amikor megpróbál törölni egy tanúsítványt, a következő hibaüzenet jelenik meg:

"Nem sikerült törölni a tanúsítványt, mert jelenleg TLS-/SSL-kötésben használják. A tanúsítvány törléséhez el kell távolítani a TLS-kötést."

#### <a name="cause"></a>Ok

Ez a probléma akkor fordulhat elő, ha egy másik alkalmazás használja a tanúsítványt.

#### <a name="solution"></a>Megoldás

Távolítsa el a tanúsítvány TLS-kötését az alkalmazásokból. Ezután próbálja meg törölni a tanúsítványt. Ha továbbra sem tudja törölni a tanúsítványt, törölje az internetböngésző gyorsítótárát, és nyissa meg újra Azure Portal új böngészőablakban. Ezután próbálja meg törölni a tanúsítványt.

### <a name="you-cant-purchase-an-app-service-certificate"></a>Nem vásárolhat tanúsítványt App Service vásárláshoz 

#### <a name="symptom"></a>Hibajelenség
Nem vásárolhat új [tanúsítványt Azure App Service a](./configure-ssl-certificate.md#import-an-app-service-certificate) Azure Portal.

#### <a name="cause-and-solution"></a>Ok és megoldás
Ez a probléma a következő okok bármelyike miatt fordulhat elő:

- A App Service csomag ingyenes vagy megosztott. Ezek a tarifacsomagok nem támogatják a TLS-t. 

    **Megoldás:** Frissítse az App Service csomagját Standard verzióra.

- Az előfizetés nem rendelkezik érvényes hitelkártyával.

    **Megoldás:** Adjon hozzá egy érvényes hitelkártyát az előfizetéséhez. 

- Az előfizetési ajánlat nem támogatja az olyan App Service tanúsítvány vásárlását, mint a Microsoft Student.  

    **Megoldás:** Frissítse az előfizetését. 

- Az előfizetés elérte az előfizetésen engedélyezett vásárlások korlátját.

    **Megoldás:** App Service tanúsítványokat a rendszer 10 tanúsítványvásárlási korláttal korlátozza a fizetéses és a nagyvállalati szerződés típusú előfizetések esetében. Más előfizetés-típusok esetén a korlát 3. A korlát növeléséhez lépjen kapcsolatba a [Azure-támogatás.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
- A App Service tanúsítvány csalásként lett megjelölve. A következő hibaüzenetet kapta: "A tanúsítvány lehetséges csalás miatt meg lett jelölve. A kérés jelenleg felülvizsgálat alatt áll. Ha a tanúsítvány 24 órán belül nem használható, lépjen kapcsolatba a Azure ügyfélszolgálata."

    **Megoldás:** Ha a tanúsítvány csalásként van megjelölve, és 24 óra után nem oldódik fel, kövesse az alábbi lépéseket:

    1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
    2. A Tanúsítványok **App Service,** majd válassza ki a tanúsítványt.
    3. Válassza **a Tanúsítványkonfiguráció**  >  **2. lépés: Tartomány-ellenőrzés**  >  **ellenőrzése lehetőséget.** Ez a lépés e-mailben értesítést küld az Azure-tanúsítványszolgáltatónak a probléma megoldása érdekében.

## <a name="custom-domain-problems"></a>Egyéni tartományokkal kapcsolatos problémák

### <a name="a-custom-domain-returns-a-404-error"></a>Az egyéni tartomány 404-es hibát ad vissza 

#### <a name="symptom"></a>Hibajelenség

Amikor az egyéni tartománynévvel tallózással megkeresi a webhelyet, a következő hibaüzenet jelenik meg:

"A 404-webalkalmazás hiba nem található."

#### <a name="cause-and-solution"></a>Ok és megoldás

**1\. ok** 

A konfigurált egyéni tartományból hiányzik egy CNAME vagy egy A rekord. 

**Megoldás az 1. ok esetén**

- Ha A rekordot adott hozzá, győződjön meg arról, hogy TXT-rekord is hozzá van adva. További információ: [Az A rekord létrehozása.](./app-service-web-tutorial-custom-domain.md#create-the-a-record)
- Ha nem kell a gyökértartományt használnia az alkalmazáshoz, javasoljuk, hogy A-rekord helyett használjon CNAME rekordot.
- Ne használjon CNAME-rekordot és A-rekordot ugyanannak a tartománynak. Ez a probléma ütközést okozhat, és megakadályozhatja a tartomány megoldását. 

**2\. ok** 

Előfordulhat, hogy az internetböngésző továbbra is a tartomány régi IP-címét gyorsítótárazó. 

**Megoldás a 2. ok megoldásra**

Törölje a böngészőt. Windows-eszközök esetén a parancsot `ipconfig /flushdns` futtathatja. A [WhatsmyDNS.net](https://www.whatsmydns.net/) ellenőrizheti, hogy a tartomány az alkalmazás IP-címére mutat-e.

### <a name="you-cant-add-a-subdomain"></a>Nem adhat hozzá altartományt 

#### <a name="symptom"></a>Hibajelenség

Altartomány hozzárendeléséhez nem adhat hozzá új állomásnevet az alkalmazáshoz.

#### <a name="solution"></a>Megoldás

- Forduljon az előfizetés rendszergazdájához, és győződjön meg arról, hogy rendelkezik a gazdagépnév alkalmazáshoz való hozzáadásához szükséges engedélyekkel.
- Ha több altartományra van szüksége, javasoljuk, hogy módosítsa a tartományt az Azure Domain Name Service (DNS) szolgáltatásra. A Azure DNS 500 állomásnevet adhat hozzá az alkalmazáshoz. További információ: [Altartomány hozzáadása.](/archive/blogs/waws/mapping-a-custom-subdomain-to-an-azure-website)

### <a name="dns-cant-be-resolved"></a>A DNS nem oldható fel

#### <a name="symptom"></a>Hibajelenség

A következő hibaüzenetet kapta:

"A DNS-rekord nem található."

#### <a name="cause"></a>Ok
Ez a probléma a következő okok valamelyike miatt merül fel:

- Az időtartam (TTL) még nem járt le. Ellenőrizze a tartomány DNS-konfigurációját az TTL-érték meghatározásához, majd várja meg az időszak lejáratát.
- A DNS-konfiguráció helytelen.

#### <a name="solution"></a>Megoldás
- A probléma megoldásához várjon 48 órát.
- Ha a DNS-konfigurációban meg tudja változtatni az TTL-beállítást, módosítsa az értéket 5 percre, hogy lássa, ez megoldja-e a problémát.
- A [WhatsmyDNS.net](https://www.whatsmydns.net/) ellenőrizze, hogy a tartomány az alkalmazás IP-címére mutat-e. Ha nem, konfigurálja az A rekordot az alkalmazás megfelelő IP-címére.

### <a name="you-need-to-restore-a-deleted-domain"></a>Törölt tartományt kell visszaállítania 

#### <a name="symptom"></a>Hibajelenség
A tartomány már nem látható a Azure Portal.

#### <a name="cause"></a>Ok 
Előfordulhat, hogy az előfizetés tulajdonosa véletlenül törölte a tartományt.

#### <a name="solution"></a>Megoldás
Ha a tartományt kevesebb mint hét nappal ezelőtt törölték, a tartomány még nem indította el a törlési folyamatot. Ebben az esetben újra megvásárolhatja ugyanazt a tartományt a Azure Portal előfizetés alatt. (A keresőmezőbe írja be a pontos tartománynevet.) A tartományért nem számítunk fel újra díjat. Ha a tartomány több mint hét napja lett törölve, lépjen [kapcsolatba](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a Azure-támogatás a tartomány visszaállításához.

## <a name="domain-problems"></a>Tartományi problémák

### <a name="you-purchased-a-tlsssl-certificate-for-the-wrong-domain"></a>Nem a megfelelő tartományhoz vásárolt TLS-/SSL-tanúsítványt

#### <a name="symptom"></a>Hibajelenség

Rossz tartományhoz App Service tanúsítványt vásárolt. A tanúsítvány nem frissítheti a megfelelő tartomány használatára.

#### <a name="solution"></a>Megoldás

Törölje a tanúsítványt, majd vásároljon egy újat.

Ha a nem megfelelő tartományt használó aktuális tanúsítvány "Kiállított" állapotban van, akkor a tanúsítványért is ki lesz számlázva. App Service a tanúsítványok nem téríthetők vissza, [](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) de a Azure-támogatás, hogy van-e más lehetőség. 

### <a name="an-app-service-certificate-was-renewed-but-the-app-shows-the-old-certificate"></a>A App Service tanúsítvány meg lett újulva, de az alkalmazás megjeleníti a régi tanúsítványt 

#### <a name="symptom"></a>Hibajelenség

Az App Service tanúsítvány meg lett újulva, de az App Service tanúsítványt használó alkalmazás továbbra is a régi tanúsítványt használja. Emellett figyelmeztetést kapott arról, hogy HTTPS-protokollra van szükség.

#### <a name="cause"></a>Ok 
App Service 48 órán belül automatikusan szinkronizálja a tanúsítványt. A tanúsítványok váltogatása vagy frissítése során előfordulhat, hogy az alkalmazás továbbra is lekéri a régi tanúsítványt, és nem az újonnan frissített tanúsítványt. Ennek az az oka, hogy a tanúsítvány-erőforrás szinkronizálási feladata még nem futott. Kattintson a Szinkronizálás gombra. A szinkronizálási művelet automatikusan frissíti a tanúsítvány gazdagépnév-kötéseit a App Service az alkalmazások állásideje nélkül.

#### <a name="solution"></a>Megoldás

Kényszerítheti a tanúsítvány szinkronizálását:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza **App Service tanúsítványok lehetőséget,** majd válassza ki a tanúsítványt.
2. Válassza **a Kulcs újrakulcsának és szinkronizálásának,** majd a Szinkronizálás **lehetőséget.** A szinkronizálás befejezése némi időt vesz igénybe. 
3. Ha a szinkronizálás befejeződött, a következő értesítés jelenik meg: "Az összes erőforrás sikeresen frissítve a legújabb tanúsítvánnyal."

### <a name="domain-verification-is-not-working"></a>A tartomány ellenőrzése nem működik 

#### <a name="symptom"></a>Hibajelenség 
A App Service tanúsítvány használatához tartomány-ellenőrzés szükséges. Ha az Ellenőrzés lehetőséget **választja,** a folyamat sikertelen lesz.

#### <a name="solution"></a>Megoldás
Manuálisan ellenőrizze a tartományt TXT-rekord hozzáadásával:

1. Lépjen a tartománynevet biztosító tartománynév-szolgáltatóhoz (DNS).
1. Adjon meg egy, a saját tartományára vonatkozó TXT típusú rekordot, amely az Azure Portalon megjelenített tartományi jogkivonat értékét használja. 

Várjon néhány percet, amíg a DNS-propagálás lefut, majd válassza a Frissítés gombot az ellenőrzés aktiváláshoz.  

Másik lehetőségként a HTML-weblap metódussal manuálisan is ellenőrizheti a tartományt. Ezzel a módszerrel a hitelesítésszolgáltató megerősítheti annak a tartománynak a tartomány tulajdonjogát, amely számára a tanúsítványt kiállították.

1. Hozzon létre egy {tartomány-ellenőrzési jogkivonat}.html nevű HTML-fájlt. A fájl tartalmának a tartomány-ellenőrzési jogkivonat értékének kell lennie.
1. Töltse fel ezt a fájlt a tartományt üzemeltető webkiszolgáló gyökérkönyvtárában.
1. A **tanúsítvány állapotának** ellenőrzéshez válassza a Frissítés lehetőséget. Az ellenőrzés befejezése eltarthat néhány percig.

Ha például egy szabványos tanúsítványt vásárol az azure.com-hez az 1234abcd tartomány-ellenőrzési jogkivonattal, a felé lekért webes kérésnek az https://azure.com/1234abcd.html 1234abcd tartományt kell visszaadni. 

> [!IMPORTANT]
> A tanúsítványrendelésnek csak 15 napja van a tartomány-ellenőrzési művelet befejezésére. 15 nap után a hitelesítésszolgáltató megtagadja a tanúsítványt, és nem kell fizetnie a tanúsítványért. Ebben az esetben törölje ezt a tanúsítványt, és próbálkozzon újra.
>
> 

### <a name="you-cant-purchase-a-domain"></a>Nem vásárolhat tartományt

#### <a name="symptom"></a>Hibajelenség
Nem vásárolhat új tartományt App Service a Azure Portal.

#### <a name="cause-and-solution"></a>Ok és megoldás

Ez a probléma az alábbi okok valamelyike miatt merül fel:

- Nincs hozzáadva hitelkártya az Azure-előfizetéshez, vagy a hitelkártya érvénytelen.

    **Megoldás:** Adjon hozzá egy érvényes hitelkártyát az előfizetéséhez.

- Nem Ön az előfizetés tulajdonosa, így nem rendelkezik engedéllyel a tartományvásárláshoz.

    **Megoldás:** [Rendelje hozzá a tulajdonosi szerepkört](../role-based-access-control/role-assignments-portal.md) a fiókjához. Vagy forduljon az előfizetés rendszergazdájához, hogy engedélyt kapjon egy tartomány megvásárlására.
- Az előfizetésében elérte a tartományvásárlások maximális számát. Jelenleg legfeljebb 20 tartományt vásárolhat.

    **Megoldás:** A korlát növelésének kérése érdekében lépjen kapcsolatba a [Azure-támogatás.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
- Az Azure-előfizetésének típusa nem támogatja App Service-tartományok vásárlását.

    **Megoldás:** Frissítse Azure-előfizetését egy másik előfizetési típusra, például használat alapú fizetéses előfizetésre.

### <a name="you-cant-add-a-host-name-to-an-app"></a>Nem adhat hozzá gazdagépnevet egy alkalmazáshoz 

#### <a name="symptom"></a>Hibajelenség

Állomásnév hozzáadásakor a folyamat nem tudja érvényesíteni és ellenőrizni a tartományt.

#### <a name="cause"></a>Ok 

Ez a probléma az alábbi okok valamelyike miatt merül fel:

- Nem rendelkezik a gazdagépnév hozzáadásához szükséges engedéllyel.

    **Megoldás:** Kérje meg az előfizetés rendszergazdáját, hogy adjon engedélyt az állomásnév hozzáadására.
- A tartomány tulajdonjoga nem ellenőrizhető.

    **Megoldás:** Ellenőrizze, hogy a CNAME vagy az A rekord megfelelően van-e konfigurálva. Egyéni tartomány alkalmazáshoz rendeléséhez hozzon létre egy CNAME vagy egy A rekordot. Ha gyökértartományt szeretne használni, A és TXT rekordokat kell használnia:

    |Rekordtípus|Gazdagép|Mutasson a|
    |------|------|-----|
    |A|@|Alkalmazás IP-címe|
    |TXT|@|`<app-name>.azurewebsites.net`|
    |CNAME|www|`<app-name>.azurewebsites.net`|

## <a name="faq"></a>GYIK

**A vásárlás után konfigurálnom kell az egyéni tartományomat a webhelyemhez?**

Amikor tartományt vásárol a Azure Portal, App Service az alkalmazás automatikusan konfigurálva lesz az egyéni tartomány használatára. Nem kell további lépéseket tennie. További információ: [Azure App Service:](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name) Új Custom Domain hozzáadása a Channel9-on.

**Használhatok egy, a Azure Portal vásárolt tartományt, hogy egy Azure-beli virtuális gépre mutassak?**

Igen, a tartományt egy virtuális gépre is mutathatja. További információt az [egyéni tartománybeállítások egy Azure-szolgáltatáshoz az Azure DNS használatával történő megadását](../dns/dns-custom-domain.md) ismertető cikkben talál.

**A tartományom a GoDaddy vagy a Azure DNS?**

App Service tartományok a GoDaddyt használják a tartományregisztrációhoz, Azure DNS a tartományok gazdagépeként. 

**Engedélyezve van az automatikus megújítás, de továbbra is kaptam egy megújítási értesítést a tartományomról e-mailben. Mit tegyek?**

Ha engedélyezve van az automatikus megújítás, nincs szükség semmilyen műveletre. Az értesítő e-mail tájékoztatja, hogy a tartomány hamarosan lejár, és manuálisan újul meg, ha az automatikus megújítás nincs engedélyezve.

**A tartományom Azure DNS díjat számítunk fel?**

A tartományvásárlás kezdeti költsége csak a tartományregisztrációra vonatkozik. A regisztrációs költségek mellett használati Azure DNS díjat is kell fizetni. További információért tekintse meg [Azure DNS díjszabását.](https://azure.microsoft.com/pricing/details/dns/)

**Korábban vásároltam meg a tartományomat a Azure Portal, és át szeretnék lépni a GoDaddy üzemeltetésről a Azure DNS üzemeltetésre. Hogyan lehet ezt megtenni?**

Nem kötelező az üzemeltetésre Azure DNS át. Ha át szeretne átköltözni a Azure DNS, a Azure Portal témakör tartománykezelési Azure DNS. Ha a tartományt a App Service vásárolta meg, a GoDaddy-Azure DNS való migrálás viszonylag zökkenőmentes eljárás.

**Szeretném megvásárolni a tartományomat egy App Service tartományból, de a tartományom a GoDaddyben is Azure DNS?**

2017. július 24-től App Service portálon vásárolt tartományokat a Azure DNS. Ha más szolgáltatót szeretne használni, a webhelyükről kell beszereznie egy tartomány üzemeltetési megoldást.

**Fizetnem kell az adatvédelemért a tartományomért?**

Amikor tartományt vásárol a Azure Portal, dönthet úgy, hogy további költségek nélkül hozzáadja az adatvédelmet. Ez a tartomány megvásárlásának egyik előnye a Azure App Service.

**Ha úgy döntök, hogy már nem szeretném a tartományomat, vissza tudom szerezni a pénzt?**

Tartomány vásárlása esetén öt napig nem kell fizetnie, amely idő alatt eldöntheti, hogy nem szeretné-e a tartományt. Ha úgy dönt, hogy az adott ötnapos időszakon belül nem szeretné a tartományt, nem számítunk fel díjat. (A.uk tartományok kivételt képeznek ez alól. Ha .uk tartományt vásárol, azonnal fizetnie kell, és nem téríthető vissza.)

**Használhatom a tartományt egy másik Azure App Service az előfizetésben?**

Igen. Amikor hozzáfér az Egyéni tartományok és TLS panelhez a Azure Portal panelen, láthatja a megvásárolt tartományokat. Az alkalmazást ezen tartományok bármelyikének használatára konfigurálhatja.

**Át tudok áthelyezni egy tartományt egy előfizetésből egy másikba?**

A [Move-AzResource PowerShell-parancsmag](/powershell/module/az.Resources/Move-azResource) használatával áthelyezhet egy tartományt egy másik előfizetésbe/erőforráscsoportba.

**Hogyan kezelem az egyéni tartományomat, ha jelenleg nincs Azure App Service alkalmazásom?**

A tartományt akkor is kezelheti, ha nem App Service webalkalmazással. A tartomány használható olyan Azure-szolgáltatásokhoz, mint a virtuális gép, a tárterület stb. Ha a tartományt az App Service Web Apps-hoz szeretné használni, akkor olyan webalkalmazást kell tartalmaznia, amely nem része az Ingyenes App Service-csomagnak, hogy a tartományt a webalkalmazáshoz kösse.

**Áthelyezhetek egy egyéni tartománnyal rendelkezik webalkalmazást egy másik előfizetésbe vagy egy App Service Environment v1-ről V2-re?**

Igen, áthelyezheti a webalkalmazást az előfizetések között. Kövesse az Erőforrások áthelyezése [az Azure-ban útmutatót.](../azure-resource-manager/management/move-resource-group-and-subscription.md) A webalkalmazás áthelyezésének van néhány korlátja. További információ: Az erőforrások [App Service korlátozásai.](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)

A webalkalmazás áthelyezését követően az egyéni tartománybeállításban található tartományok gazdagépnév-kötései változatlanok maradnak. Az állomásnév-kötések konfigurálásához nincs szükség további lépésekre.
