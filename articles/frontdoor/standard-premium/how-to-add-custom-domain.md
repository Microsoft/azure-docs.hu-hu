---
title: Egyéni tartomány hozzáadása a Azure Front Door Standard/Prémium termékváltozat konfigurációhoz
description: Ez az oktatóanyag bemutatja, hogyan lehet egyéni tartományt Azure Front Door Standard/Prémium termékváltozathoz.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: amsriva
ms.openlocfilehash: 69e216c8893f9361a18354e5d165ecc0499601aa
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107387921"
---
# <a name="create-a-custom-domain-on-azure-front-door-standardpremium-sku-preview-using-the-azure-portal"></a>Egyéni tartomány létrehozása Azure Front Door Standard/Prémium termékváltozatban (előzetes verzió) a Azure Portal

> [!Note]
> Ez a dokumentáció a Azure Front Door Standard/Prémium (előzetes verzió) verzióhoz érhető el. Információt keres a Azure Front Door? Tekintse [meg itt:](../front-door-overview.md).

Ha az Azure Front Door Standard/Prémium szintű tartományt használja az alkalmazások kézbesítéséhez, egyéni tartományra van szükség, ha azt szeretné, hogy a saját tartományneve látható legyen a végfelhasználói kérések között. A látható tartománynév hasznos lehet az ügyfelei számára, és a vállalati arculat szempontjából is.

Miután létrehozott egy Azure Front Door/Prémium szintű profilt, az alapértelmezett előtér-gazdagép altartománya a azurefd.net. Ez az altartomány akkor kerül bele az URL-címbe, Azure Front Door Standard/Premium alapértelmezés szerint tartalmat kézbesít a háttérből. Például: `https://contoso-frontend.azurefd.net/activeusers.htm`. A kényelmes használat érdekében az Azure Front Door lehetőséget ad arra, hogy egyéni tartományt társítson az alapértelmezett gazdagéphez. Ezzel a beállítással egyéni tartománnyal kézbesíti a tartalmakat az URL-címben egy standard/prémium Azure Front Door tartománynév helyett. Például: ' https://www.contoso.com/photo.png '.

> [!IMPORTANT]
> Azure Front Door Standard/Prémium (előzetes verzió) jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: Kiegészítő használati feltételek a Microsoft Azure [**előzetes verziókhoz.**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="prerequisites"></a>Előfeltételek
* Mielőtt elvégezhetné a jelen oktatóanyag lépéseit, először létre kell hoznia egy Front Doort. További információ: [Rövid útmutató: Front Door Standard/Prémium](create-front-door-portal.md).

* Ha még nem rendelkezik egyéni tartománnyal, először meg kell vásárolnia egyet egy tartományszolgáltatónál. Példákért lásd az [egyéni tartománynév vásárlásáról](../../app-service/manage-custom-dns-buy-domain.md) szóló részt.

* Ha az Azure-t használja [](../../dns/dns-overview.md)a DNS-tartományokhoz, delegálnia kell a tartományszolgáltató tartománynévrendszerét (DNS-ét) egy Azure DNS. További információ: [Tartomány delegálása a Azure DNS.](../../dns/dns-delegate-domain-azure-dns.md) Ellenkező esetben, ha tartományszolgáltatót használ a DNS-tartomány kezeléséhez, manuálisan kell ellenőriznie a tartományt a megjelenő DNS TXT-rekordok megadásával.

## <a name="add-a-new-custom-domain"></a>Új egyéni tartomány hozzáadása

> [!NOTE]
> A Nyilvános előzetes verzióban Azure DNS Standard/Prémium verzióban nem támogatott a Azure Front Door használata Apex-tartományok létrehozásához. Más DNS-szolgáltatók is támogatják a CNAME-simítást vagy DNS-et, amelyek lehetővé teszik az APEX-tartományok Azure Front Door Standard/Prémium szintű kiszolgálókhoz.

Az egyéni tartományt a portál Tartományok szakasza kezeli. Egyéni tartomány a végponthoz való társítás előtt létrehozható és érvényesíthető. Egy egyéni tartomány és annak altartományai egyszerre csak egyetlen végponthoz társíthatóak. A Front Doorshoz azonban ugyanannak az egyéni tartománynak különböző altartományai is használhatók. A különböző altartományokkal lévő egyéni tartományokat is leképezheti ugyanahhoz a Front Door végponthoz.

1. A saját profil beállításai Azure Front Door válassza a Tartományok *lehetőséget,*  majd **a Tartomány hozzáadása** gombot.

    :::image type="content" source="../media/how-to-add-custom-domain/add-domain-button.png" alt-text="Képernyőkép a tartomány kezdőlapján található Tartomány hozzáadása gombról.":::

1. Megjelenik **a Tartomány hozzáadása** lap, ahol megadhatja az egyéni tartomány adatait. Választhatja az Azure által felügyelt DNS-t, amely ajánlott, vagy választhatja a saját DNS-szolgáltató használatát. Ha az Azure által felügyelt DNS-t választja, válasszon ki egy meglévő DNS-zónát, majd válasszon ki egy egyéni altartományt, vagy hozzon létre egy újat. Ha másik DNS-szolgáltatót használ, manuálisan adja meg az egyéni tartománynevet. Az **egyéni tartomány hozzáadásához** válassza a Hozzáadás lehetőséget.

    :::image type="content" source="../media/how-to-add-custom-domain/add-domain-page.png" alt-text="Képernyőkép a Tartomány hozzáadása lapról.":::

    A rendszer létrehoz egy új egyéni tartományt a Beküldés **érvényesítési állapotával.**

    :::image type="content" source="../media/how-to-add-custom-domain/validation-state-submitting.png" alt-text="A tartományérvényesítési állapot beküldésének képernyőképe.":::

    Várjon, amíg az érvényesítési állapot **Függőben állapotra változik.** Ez a művelet néhány percet is eltelhet.

    :::image type="content" source="../media/how-to-add-custom-domain/validation-state-pending.png" alt-text="A függőben lévő tartományérvényesítési állapot képernyőképe.":::

1. Válassza a **Függőben lévő érvényesítés** állapotot. Megjelenik egy új lap, amely az egyéni tartomány érvényesítéséhez szükséges DNS TXT rekordadatokat tartalmaz. A TXT típusú rekord a következő `_dnsauth.<your_subdomain>` formátumú: . Ha egy új Azure DNS használ, válassza a  Hozzáadás gombot, és a rendszer létrehoz egy új TXT-rekordot a megjelenített rekordértékkel a Azure DNS zónában. Ha másik DNS-szolgáltatót használ, manuálisan hozzon létre egy új TXT típusú rekordot a névvel, az oldalon látható `dnsauth.<your_subdomain>` módon.

    :::image type="content" source="../media/how-to-add-custom-domain/validate-custom-domain.png" alt-text="Képernyőkép az egyéni tartomány érvényesítéséről.":::

1. Válassza ki a frissítési állapotot. Ha a tartományt a DNS TXT rekord használatával érvényesíti, az érvényesítési állapot **ellenőrzöttre változik.** A művelet ellenőrzése eltarthat néhány percig.

    :::image type="content" source="../media/how-to-add-custom-domain/domain-status-verified.png" alt-text="Képernyőkép az ellenőrzött egyéni tartományról.":::

1. Zárja be az oldalt az egyéni tartományok listájának kezdőlapjára való visszatéréshez. Az egyéni tartomány kiépítési állapotának Kiépítve állapotra kell **változnia,** az érvényesítési állapotnak pedig **Jóváhagyott állapotra kell változnia.**

    :::image type="content" source="../media/how-to-add-custom-domain/provisioned-approved-status.png" alt-text="Képernyőkép a kiépített és jóváhagyott állapotról.":::

## <a name="associate-the-custom-domain-with-your-front-door-endpoint"></a>Az egyéni tartomány társítása a Front Door végponthoz

Az egyéni tartomány ellenőrzése után hozzáadhatja azt a standard/prémium Azure Front Door végponthoz.

1. Az egyéni tartomány érvényesítése után hozzárendelheti azt egy meglévő standard/Azure Front Door végponthoz és útvonalhoz. Válassza a **Végpont társítása** hivatkozást a Végpont és útvonalak **társítása lap megnyitásához.** Válassza ki a társítani kívánt végpontot és útvonalakat. Ezután válassza a **Társítás lehetőséget.** Miután a társítási művelet befejeződött, zárja be az oldalt.

    :::image type="content" source="../media/how-to-add-custom-domain/associate-endpoint-routes.png" alt-text="Képernyőkép a végpont és az útvonalak társítása lapról.":::

    A Végpont társítási állapotának változnia kell, hogy tükrözze azt a végpontot, amelyhez az egyéni tartomány jelenleg társítva van. 

    :::image type="content" source="../media/how-to-add-custom-domain/endpoint-association-status.png" alt-text="A végpont társítási hivatkozásának képernyőképe.":::

1. Válassza a DNS-állapot hivatkozást.

    :::image type="content" source="../media/how-to-add-custom-domain/dns-state-link.png" alt-text="A DNS-állapot hivatkozásának képernyőképe.":::

1. Megjelenik **a CNAME** rekord hozzáadása vagy frissítése oldal, és megjeleníti a CNAME rekord adatait, amelyekre a forgalom elkezdődhet. Ha üzemeltetett zónákat Azure DNS használ, a CNAME-rekordok az  oldal Hozzáadás gombját választva használhatja. Ha másik DNS-szolgáltatót használ, manuálisan kell megadnia a CNAME rekord nevét és értékét az oldalon látható módon.

    :::image type="content" source="../media/how-to-add-custom-domain/add-update-cname-record.png" alt-text="A CNAME rekord hozzáadásának vagy frissítésének képernyőképe.":::

1. Miután létrejött a CNAME rekord, és az egyéni tartomány hozzá van társítva a Azure Front Door végpont befejeződik, a forgalom elindul.

    > [!NOTE]
    > Ha a HTTPS engedélyezve van, a tanúsítvány kiépítése és propagálása eltarthat néhány percig, mert a propagálás az összes peremhálózati helyen történik. 

## <a name="verify-the-custom-domain"></a>Az egyéni tartomány ellenőrzése

Miután ellenőrizte és társította az egyéni tartományt, ellenőrizze, hogy az egyéni tartomány megfelelően hivatkozik-e a végpontra.

:::image type="content" source="../media/how-to-add-custom-domain/verify-configuration.png" alt-text="Képernyőkép az ellenőrzött és a társított egyéni tartományról.":::

Végül ellenőrizze, hogy az alkalmazástartalom egy böngészőben lesz-e kiszolgálva.

## <a name="next-steps"></a>Következő lépések

Ha szeretné megtudni, hogyan engedélyezheti a HTTPS-t az egyéni tartományhoz, folytassa a következő oktatóanyagban.

> [!div class="nextstepaction"]
> [A HTTPS engedélyezése egyéni tartományhoz]()
