---
title: Egyéni tartomány hozzáadása az Azure-beli előtérben standard/prémium SKU-konfigurációhoz
description: Ebből az oktatóanyagból megtudhatja, hogyan lehet egyéni tartományt bevezetni az Azure-ba a standard/prémium SKU-ra.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: amsriva
ms.openlocfilehash: 164e06024844fb5262586450b737db9c807e373a
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "101099103"
---
# <a name="create-a-custom-domain-on-azure-front-door-standardpremium-sku-preview-using-the-azure-portal"></a>Hozzon létre egy egyéni tartományt az Azure bejárati ajtó standard/Premium SKU (előzetes verzió) használatával a Azure Portal

> [!Note]
> Ez a dokumentáció az Azure bejárati ajtó standard/Premium (előzetes verzió) verziójához készült. Információt keres az Azure bejárati ajtóról? Megtekintés [itt](../front-door-overview.md).

Ha az Azure Door standard/Premium alkalmazást használja az alkalmazások kézbesítéséhez, egyéni tartományra van szükség, ha szeretné, hogy a saját tartományneve megjelenjen a végfelhasználói kérésekben. A látható tartománynév hasznos lehet az ügyfelei számára, és a vállalati arculat szempontjából is.

Miután létrehozta az Azure Door standard/Premium profilt, az alapértelmezett előtér-gazdagép a azurefd.net altartományát fogja tartalmazni. Ez az altartomány bekerül az URL-címre, ha az Azure bejárati ajtó standard/Premium tartalma alapértelmezés szerint a háttérbeli tartalmat biztosítja. Például: `https://contoso-frontend.azurefd.net/activeusers.htm`. A kényelmes használat érdekében az Azure Front Door lehetőséget ad arra, hogy egyéni tartományt társítson az alapértelmezett gazdagéphez. Ezzel a beállítással a tartalmat egy egyéni tartományba továbbítja az URL-címben az Azure bejárati ajtó standard/Premium tulajdonosi tartományneve helyett. Például: https://www.contoso.com/photo.png.

> [!IMPORTANT]
> Az Azure bejárati ajtó standard/Premium (előzetes verzió) jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: a [**Microsoft Azure előzetes verziójának kiegészítő használati feltételei**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek
* Mielőtt elvégezhetné a jelen oktatóanyag lépéseit, először létre kell hoznia egy Front Doort. További információ: gyors üzembe helyezés [a bejárati ajtó standard/prémium szintű létrehozásával](create-front-door-portal.md).

* Ha még nem rendelkezik egyéni tartománnyal, először az egyiket kell megvásárolnia egy tartományi szolgáltatóval. Példákért lásd az [egyéni tartománynév vásárlásáról](../../app-service/manage-custom-dns-buy-domain.md) szóló részt.

* Ha az Azure-t használja a [DNS-tartományok](../../dns/dns-overview.md)üzemeltetéséhez, akkor a tartományi szolgáltató tartománynevét (DNS) delegálnia kell egy Azure DNS. További információ: [tartomány delegálása Azure DNSra](../../dns/dns-delegate-domain-azure-dns.md). Ha azonban tartományi szolgáltatót használ a DNS-tartomány kezeléséhez, a DNS-alapú TXT-rekordok megadásával manuálisan kell érvényesíteni a tartományt.

## <a name="add-a-new-custom-domain"></a>Új egyéni tartomány hozzáadása

Az egyéni tartományt a portálon a tartományok felügyelik szakaszban találja. Egy egyéni tartomány létrehozható és érvényesíthető egy végponthoz való társítás előtt. Egy egyéni tartomány és az altartományok egyszerre csak egyetlen végponthoz társíthatók. Ugyanakkor különböző altartományokat is használhat ugyanazon egyéni tartományból különböző bejárati ajtók esetén. Más altartományokkal rendelkező egyéni tartományokat is leképezheti ugyanahhoz az előtérben lévő végponthoz.

1. Az Azure bejárati ajtó profiljának beállítások területén válassza a *tartományok*  , majd a **tartomány hozzáadása** gombot.

    :::image type="content" source="../media/how-to-add-custom-domain/add-domain-button.png" alt-text="Képernyőfelvétel: tartomány hozzáadása gomb a tartomány kezdőlapján.":::

1. Ekkor megjelenik a **tartomány hozzáadása** lap, ahol megadhatja az egyéni tartomány adatait. Kiválaszthatja az Azure által felügyelt DNS-t, amely javasolt, vagy dönthet úgy, hogy saját DNS-szolgáltatót használ. Ha az Azure által felügyelt DNS lehetőséget választja, válasszon ki egy meglévő DNS-zónát, majd válasszon ki egy egyéni altartományt, vagy hozzon létre egy újat. Ha más DNS-szolgáltatót használ, manuálisan adja meg az egyéni tartománynevet. Válassza a **Hozzáadás** lehetőséget az egyéni tartomány hozzáadásához.

    :::image type="content" source="../media/how-to-add-custom-domain/add-domain-page.png" alt-text="Képernyőkép a tartomány hozzáadása lapról.":::

    Az új egyéni tartomány az **elküldés** érvényesítési állapotával jön létre.

    :::image type="content" source="../media/how-to-add-custom-domain/validation-state-submitting.png" alt-text="Képernyőkép a tartomány-ellenőrzés állapotáról.":::

    Várjon, amíg az érvényesítési állapot **függőre** változik. A művelet eltarthat néhány percig.

    :::image type="content" source="../media/how-to-add-custom-domain/validation-state-pending.png" alt-text="A tartomány-ellenőrzési állapot képernyőképe függőben.":::

1. Válassza ki a **függőben lévő** érvényesítési állapotot. Az egyéni tartomány ellenőrzéséhez egy új lap jelenik meg, amely a DNS TXT-rekordhoz szükséges információkat tartalmazza. A TXT-rekord formátuma a ( `_dnsauth.<your_subdomain>` ). Ha Azure DNS-alapú zónát használ, válassza a **Hozzáadás** gombot, és a megjelenített rekord értékkel rendelkező új TXT-rekordot hozza létre a rendszer a Azure DNS zónában. Ha más DNS-szolgáltatót használ, manuálisan hozzon létre egy új TXT-rekordot a `dnsauth.<your_subdomain>` rekord értékével, ahogy az a lapon látható.

    :::image type="content" source="../media/how-to-add-custom-domain/validate-custom-domain.png" alt-text="Az egyéni tartomány érvényesítése lap képernyőképe.":::

1. Válassza ki a frissítés állapotát. Miután a tartomány ellenőrzése megtörtént a DNS TXT-rekord használatával, az érvényesítési állapot **ellenőrzött** értékre változik. A művelet érvényessége eltarthat néhány percig.

    :::image type="content" source="../media/how-to-add-custom-domain/domain-status-verified.png" alt-text="Képernyőkép az egyéni tartomány ellenőrzéséről.":::

1. A lap bezárásával térjen vissza az egyéni tartományok lista kezdőlapra. Az egyéni tartomány kiépítési állapotának a **kiépített** értékre kell váltania, és az érvényesítési állapotnak **jóvá kell hagynia**.

    :::image type="content" source="../media/how-to-add-custom-domain/provisioned-approved-status.png" alt-text="Képernyőkép a kiépített és jóváhagyott állapotról.":::

## <a name="associate-the-custom-domain-with-your-front-door-endpoint"></a>Az egyéni tartomány hozzárendelése az előtérben lévő végponthoz

Az egyéni tartomány ellenőrzése után hozzáadhatja azt az Azure bejárati ajtó standard/prémium végponthoz.

1. Ha az egyéni tartomány ellenőrzése megtörtént, azt hozzárendelheti egy meglévő Azure-beli előtérben lévő standard/prémium végponthoz és útvonalhoz. A **végpont társítása** hivatkozásra kattintva nyissa meg a **társítási végpont és útvonalak** lapot. Válassza ki a hozzárendelni kívánt végpontot és útvonalakat. Ezután válassza a **hozzárendelés** lehetőséget. A hozzárendelési művelet befejeződése után zárjuk be a lapot.

    :::image type="content" source="../media/how-to-add-custom-domain/associate-endpoint-routes.png" alt-text="Képernyőkép a végpontok és útvonalak társított oldaláról.":::

    A végpont társítási állapotának úgy kell megváltoznia, hogy tükrözze azt a végpontot, amelyhez az egyéni tartomány jelenleg társítva van. 

    :::image type="content" source="../media/how-to-add-custom-domain/endpoint-association-status.png" alt-text="A végponti társítás hivatkozásának képernyőképe.":::

1. Válassza ki a DNS-állapot hivatkozást.

    :::image type="content" source="../media/how-to-add-custom-domain/dns-state-link.png" alt-text="Képernyőkép a DNS-állapot hivatkozásáról.":::

1. Megjelenik a **CNAME rekord hozzáadása vagy frissítése** lap, és MEGJELENÍTI a CNAME rekord azon adatait, amelyeket meg kell adni, mielőtt a forgalom el tudja kezdeni a forgalmat. Ha Azure DNS üzemeltetett zónákat használ, a CNAME rekordokat a lapon a **Hozzáadás** gombra kattintva hozhatja létre. Ha más DNS-szolgáltatót használ, manuálisan kell megadnia a CNAME rekord nevét és értékét, ahogy az a lapon látható.

    :::image type="content" source="../media/how-to-add-custom-domain/add-update-cname-record.png" alt-text="Képernyőfelvétel a CNAME rekord hozzáadásáról vagy frissítéséről.":::

1. Miután létrehozta a CNAME rekordot, és az egyéni tartomány hozzá van rendelve az Azure-beli bejárati végponthoz, a forgalmi folyamat elkezd áramlani.

    > [!NOTE]
    > Ha engedélyezve van a HTTPS, a tanúsítvány kiépítés és a propagálás néhány percet is igénybe vehet, mert a rendszer az összes peremhálózati helyen elvégzi a propagálást. 

## <a name="verify-the-custom-domain"></a>Az egyéni tartomány ellenőrzése

Miután érvényesített és társította az egyéni tartományt, ellenőrizze, hogy az egyéni tartomány megfelelően van-e hivatkozva a végpontra.

:::image type="content" source="../media/how-to-add-custom-domain/verify-configuration.png" alt-text="Képernyőkép az érvényesített és a társított egyéni tartományról.":::

Végül ellenőrizze, hogy az alkalmazás tartalma megjelenik-e böngésző használatával.

## <a name="next-steps"></a>Következő lépések

Ha szeretné megtudni, hogyan engedélyezheti a HTTPS-t az egyéni tartományhoz, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [A HTTPS engedélyezése egyéni tartományhoz]()
