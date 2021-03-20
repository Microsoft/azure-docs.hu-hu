---
title: Több-bérlős háttér vége
titleSuffix: Azure Application Gateway
description: Ez az oldal áttekintést nyújt az Application Gateway több-bérlős háttérrendszerekhez elérhető támogatásáról.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 06/09/2020
ms.author: victorh
ms.openlocfilehash: fe6ea6f348d796962141bd39ff858d891a29a2f6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93397688"
---
# <a name="application-gateway-support-for-multi-tenant-back-ends-such-as-app-service"></a>Application Gateway támogatja a több-bérlős háttérrendszer, például az App Service használatát

A webkiszolgálók több-bérlős architektúrájának kialakításában több webhely fut ugyanazon a webkiszolgáló-példányon. Az állomásnevek a különböző üzemeltetett alkalmazások megkülönböztetésére szolgálnak. Alapértelmezés szerint az Application Gateway nem módosítja az ügyféltől származó bejövő HTTP-állomásfejlécet, és így küldi tovább a háttérrendszernek. Ez jól működik a háttér-készletek tagjai, például a hálózati adapterek, a virtuálisgép-méretezési csoportok, a nyilvános IP-címek, a belső IP-címek és a teljes tartománynevek esetében, mivel ezek nem függnek egy adott állomásfejléc-vagy SNI-bővítménnyel a megfelelő végpontra Számos olyan szolgáltatás, mint például az Azure app Service Web Apps és az Azure API Management, amelyek több-bérlős jellegűek, és egy adott állomásfejléc-vagy SNI-bővítményt használnak a megfelelő végpontra való feloldáshoz. Általában az alkalmazás DNS-neve, amely viszont az Application gatewayhez társított DNS-név, eltér a háttér-szolgáltatás tartománynevétől. Ezért az Application Gateway által fogadott eredeti kérelemben szereplő állomásfejléc nem egyezik meg a háttér-szolgáltatás állomásneve nevével. Emiatt, ha az Application Gateway-ből a háttérbe irányuló kérelemben szereplő állomásfejléc nem változik a háttérrendszer állomásneve, a több-bérlős háttérrendszer nem tudja feloldani a kérést a megfelelő végpontra. 

Az Application Gatewayben elérhető egy olyan funkció, amely lehetővé teszi a felhasználóknak a HTTP-kiszolgálófejléc felülbírálását a kérelmekben a háttérrendszer gazdaneve alapján. Ez biztosítja a több-bérlős háttérrendszerek, mint az Azure App Service Web Apps és az API Management támogatását. A funkció a v1 és v2 standard, valamint a WAF termékváltozat esetében is elérhető. 

![gazdagép felülbírálása](./media/application-gateway-web-app-overview/host-override.png)

> [!NOTE]
> Ez nem alkalmazható az Azure app Service Environment (bemutató) szolgáltatásra, mivel a szolgáltató egy dedikált erőforrás az Azure app Service-től, amely több-bérlős erőforrás.

## <a name="override-host-header-in-the-request"></a>A kérelemben szereplő állomásfejléc felülbírálása

A gazdagép felülbírálásának megadásának lehetősége a http- [beállításokban](./configuration-overview.md#http-settings) van meghatározva, és a szabályok létrehozása során bármely háttér-készletre alkalmazható. A több-bérlős háttérrel rendelkező állomásfejléc-és SNI-bővítmények felülbírálása a következő két módon támogatott:

- Az állomásnév beállítása a HTTP-beállításokban explicit módon megadott rögzített értékre. Ez a funkció biztosítja, hogy a gazdagép fejléce felülbírálva legyen az értékre az adott HTTP-beállításokat alkalmazó háttér-készletre irányuló összes forgalom esetében. A végpontok közötti TLS használatakor ez a felülbírált állomásnév a SNI-bővítményben van használatban. Ez a funkció olyan forgatókönyveket tesz lehetővé, amelyekben a háttérbeli készlet farmja olyan állomásfejléc-t vár, amely eltér a bejövő ügyfél-állomásfejléc-fejléctől.

- A gazdagép nevének származtatása a háttérbeli készlet tagjainak IP-címéről vagy teljes tartománynevéről. A HTTP-beállítások azt is lehetővé teszik, hogy a rendszer dinamikusan kiválassza az állomásnevet a háttérbeli készlet tagjainak teljes tartománynevéről, ha úgy van konfigurálva, hogy az adott háttérbeli készletből származó állomásnév származtatása beállítást használja. A végpontok közötti TLS használatakor ez az állomásnév a teljes tartománynévből származik, és a SNI-bővítményben van használatban. Ez a funkció lehetővé teszi olyan forgatókönyvek használatát, amelyekben egy háttér-készlet két vagy több több-bérlős, például Azure Web Apps-szolgáltatással rendelkezhet, és a kérelem állomásneve az egyes tagok számára a teljes tartománynévből származtatott állomásnevet tartalmazza. Ennek a forgatókönyvnek a megvalósításához a [pick hostname](./configuration-http-settings.md#pick-host-name-from-back-end-address) nevű http-beállításokat használjuk a háttér-címről, amely dinamikusan felülbírálja az eredeti kérelemben szereplő állomásfejléc-fejlécet a háttér-készletben említettek szerint.  Ha például a háttérbeli készlet teljes tartományneve a "contoso11.azurewebsites.net" és a "contoso22.azurewebsites.net" értéket tartalmazza, akkor az eredeti kérelem állomásneve, amely a contoso.com lesz felülbírálva a contoso11.azurewebsites.net vagy a contoso22.azurewebsites.net, ha a kérést a megfelelő háttér-kiszolgálónak küldik. 

  ![webalkalmazás-forgatókönyv](./media/application-gateway-web-app-overview/scenario.png)

Ezzel a képességgel az ügyfelek megadhatják a HTTP-beállítások és az egyéni mintavételek beállítási lehetőségeit a megfelelő konfigurációhoz. Ez a beállítás egy figyelőhöz és egy háttér-készlethez van kötve egy szabály használatával.

## <a name="special-considerations"></a>Különleges szempontok

### <a name="tls-termination-and-end-to-end-tls-with-multi-tenant-services"></a>TLS-lezárás és végpontok közötti TLS több-bérlős szolgáltatásokkal

A TLS-lezárás és a végpontok közötti TLS-titkosítás a több-bérlős szolgáltatások esetében is támogatott. Az Application Gateway TLS-lezárása esetén a TLS-tanúsítvány továbbra is szükséges az Application Gateway-figyelőhöz való hozzáadáshoz. A végpontok közötti TLS esetében azonban a megbízható Azure-szolgáltatások, például az Azure app Service Web Apps nem igénylik a háttérrendszer használatát az Application gatewayben. Ezért nincs szükség hitelesítési tanúsítványok hozzáadására. 

![végpontok közötti TLS](./media/application-gateway-web-app-overview/end-to-end-ssl.png)

Figyelje meg, hogy a fenti képen nincs szükség hitelesítési tanúsítványok hozzáadására, ha az App Service-t háttérként választja.

### <a name="health-probe"></a>Állapotadat-mintavétel

A gazdagép fejlécének felülbírálása a **http-beállításokban** csak a kérést és annak útválasztását érinti. nem befolyásolja az állapot mintavételi viselkedését. A végpontok közötti funkciók működéséhez a mintavételi és a HTTP-beállításokat is úgy kell módosítani, hogy azok a megfelelő konfigurációt tükrözzék. Amellett, hogy lehetővé teszi a gazdagép fejlécének megadását a mintavételi konfigurációban, az egyéni mintavételek is támogatják a gazdagép fejlécének a jelenleg konfigurált HTTP-beállításokból való származtatása lehetőségét. Ezt a konfigurációt a mintavételi konfigurációban a `PickHostNameFromBackendHttpSettings` paraméterrel is megadhatjuk.

### <a name="redirection-to-app-services-url-scenario"></a>Átirányítás App Service URL-címére

Előfordulhatnak olyan forgatókönyvek, amelyekben az App Service-válaszban szereplő állomásnév a Application Gatewayhoz társított tartomány helyett a *. azurewebsites.net állomásnévre irányíthatja a végfelhasználói böngészőt. Ez a probléma a következő esetekben fordulhat elő:

- Az átirányítás konfigurálva van a App Serviceon. Az átirányítás olyan egyszerű lehet, mint egy záró perjel hozzáadása a kéréshez.
- Rendelkezik Azure AD-hitelesítéssel, amely az átirányítás okát okozza.

Az ilyen esetek megoldásához lásd: [az App Service URL-címére irányuló átirányítás hibaelhárítása](./troubleshoot-app-service-redirection-app-service-url.md).

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan állíthat be egy olyan Application Gateway-t egy több-bérlős alkalmazással, mint például az Azure app Service web app a háttérbeli készlet tagjaként, ha meglátogatja [app Service webalkalmazások konfigurálását Application Gateway](./configure-web-app-portal.md)
