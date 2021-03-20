---
title: Gyökér-vagy APEX-tartomány beléptetése meglévő bejárati ajtóra – Azure Portal
description: Megtudhatja, hogyan lehet bevezetni egy gyökér-vagy APEX-tartományt egy meglévő bevezető ajtajához a Azure Portal használatával.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 11/13/2020
ms.author: duau
ms.openlocfilehash: 55eefe7a7490df050aa7ebc2bb41fbadcc8d8279
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94646338"
---
# <a name="onboard-a-root-or-apex-domain-on-your-front-door"></a>Gyökértartomány vagy Apex-tartomány előkészítése a Front Dooron
Az Azure bevezető ajtaja CNAME rekordokat használ a tartomány tulajdonjogának ellenőrzéséhez az egyéni tartományok bevezetéséhez. A bejárati ajtó nem teszi elérhetővé az előtér-profilhoz társított előtérbeli IP-címet. Így a csúcspont tartománya nem rendelhető hozzá IP-címhez, ha a cél az, hogy az Azure-ba bekerüljön.

A DNS protokoll megakadályozza a CNAME rekordok hozzárendelését a zóna csúcsán. Ha például a tartománya a `contoso.com` ; CNAME rekordokat hozhat létre a számára, `somelabel.contoso.com` de saját maga nem hozhat létre CNAME-t `contoso.com` . Ez a korlátozás olyan alkalmazás-tulajdonosoknak nyújt problémát, akik elosztott terhelésű alkalmazásokat vezetnek be az Azure bejárati ajtó mögött. Mivel a bejárati ajtó profiljának használata szükséges egy CNAME rekord létrehozásához, nem lehet az első ajtón lévő profilra mutatni a zóna csúcspontján.

Ez a probléma a Azure DNSban található alias-rekordok használatával oldható fel. A CNAME rekordoktól eltérően az alias-rekordok a zóna csúcsán jönnek létre. Az alkalmazás tulajdonosai használhatják a zóna csúcspont-rekordját egy nyilvános végpontokkal rendelkező bejárati profilra. Az alkalmazás tulajdonosai a DNS-zónán belüli bármely más tartományhoz tartozó bejárati ajtós profilra mutatnak. Például, `contoso.com` és `www.contoso.com` ugyanarra az előtérben lévő profilra mutathat. 

A csúcspont vagy a gyökértartomány az előtérben lévő profilhoz való leképezése alapvetően a CNAME-összeolvasztás vagy a DNS-elkergetés szükséges. Olyan mechanizmus, amelyben a DNS-szolgáltató rekurzív módon oldja fel a CNAME-bejegyzést, amíg az IP-címet nem üt. Ezt a funkciót a Azure DNS a bejárati ajtó végpontok számára támogatja. 

> [!NOTE]
> Vannak más DNS-szolgáltatók is, amelyek támogatják a CNAME-összeolvasztást vagy a DNS-t, azonban az Azure bejárati ajtó azt ajánlja, hogy az ügyfelek a tartományuk üzemeltetéséhez Azure DNS használjanak.

A Azure Portal használatával üzembe helyezhet egy APEX-tartományt az előtérben, és engedélyezheti a HTTPS-t, ha társítja azt a TLS-leállításhoz szükséges tanúsítvánnyal. Az APEX-tartományokat root vagy Naked tartománynak is nevezzük.

## <a name="create-an-alias-record-for-zone-apex"></a>Alias-rekord létrehozása a zóna csúcsához

1. Nyissa meg **Azure DNS** konfigurációt a bekészíteni kívánt tartományhoz.

1. Hozza létre vagy szerkessze a zóna csúcspontjának rekordját.

1. Adja meg a  rekord típusát *rekordként* , majd válassza az *Igen* lehetőséget az **alias-rekordhoz**. Az **alias típusát** az *Azure Resource* értékre kell beállítani.

1. Válassza ki azt az Azure-előfizetést, amelynél a bejárati ajtó profilja bekerül. Ezután válassza ki a bejárati ajtót az **Azure erőforrás** legördülő menüből.

1. A módosítások elküldéséhez kattintson **az OK gombra** .

    :::image type="content" source="./media/front-door-apex-domain/front-door-apex-alias-record.png" alt-text="Alias rekord a zóna csúcsához":::

1. A fenti lépés egy zóna csúcspont-rekordot hoz létre, amely az előtérben lévő erőforrásra mutat, valamint egy CNAME rekordot, amely a "afdverify" (példa – `afdverify.contosonews.com` ), amely a tartománynak az előtérben lévő profilba való bevezetéséhez lesz felhasználva.

## <a name="onboard-the-custom-domain-on-your-front-door"></a>Az egyéni tartomány előkészítése a bejárati ajtón

1. Az új egyéni tartomány hozzáadásához az előtér-Tervező lapon válassza a "+" ikont a frontend gazdagépek szakaszban.

1. Adja meg a gyökér vagy a csúcspont tartománynevét az egyéni Állomásnév mezőben, például: `contosonews.com` .

1. Ha a tartományból a bejárati ajtóhoz tartozó CNAME-leképezést érvényesíti, válassza a **Hozzáadás** lehetőséget az egyéni tartomány hozzáadásához.

1. A módosítások elküldéséhez válassza a **Mentés** lehetőséget.

   :::image type="content" source="./media/front-door-apex-domain/front-door-onboard-apex-domain.png" alt-text="Egyéni tartomány menü":::

## <a name="enable-https-on-your-custom-domain"></a>HTTPS engedélyezése az egyéni tartományon

1. Válassza ki a hozzáadott egyéni tartományt és az **egyéni tartomány HTTPS** szakaszát, módosítsa az állapotot **engedélyezve** értékre.

1. Válassza ki a  **tanúsítványkezelő típusát** a *saját tanúsítvány használata* lehetőségnél.

   :::image type="content" source="./media/front-door-apex-domain/front-door-onboard-apex-custom-domain.png" alt-text="Egyéni tartomány HTTPS-beállításai":::    

   > [!WARNING]
   > Az előtérben felügyelt tanúsítvány-felügyeleti típus jelenleg nem támogatott az APEX-vagy a gyökérszintű tartományokban. Az egyetlen lehetőség, hogy a HTTPS-t egy csúcson vagy gyökértartományen engedélyezze a bejárati ajtónál, a saját, Azure Key Vault üzemeltetett egyéni TLS/SSL-tanúsítványát használja.

1. Mielőtt továbblép a következő lépésre, győződjön meg arról, hogy a megfelelő engedélyekkel rendelkezik a bejárati ajtóhoz a kulcstartóhoz való hozzáféréshez.

1. Válasszon egy **Key Vault fiókot** a jelenlegi előfizetésből, majd válassza ki a megfelelő **titkos** és **titkos verziót** , hogy a megfelelő tanúsítványhoz lehessen rendelni.

1. Válassza a **frissítés** lehetőséget a kijelölés mentéséhez, majd válassza a **Mentés** lehetőséget.

1. Néhány perc elteltével válassza a **frissítés** lehetőséget, majd az egyéni tartományt újra kiválasztva tekintse meg a tanúsítvány kiépítés folyamatát. 

> [!WARNING]
> Győződjön meg arról, hogy megfelelő útválasztási szabályokat hozott létre az APEX-tartományhoz, vagy adja hozzá a tartományt a meglévő útválasztási szabályokhoz.

## <a name="next-steps"></a>Következő lépések

- Útmutató a [Front Door létrehozásához](quickstart-create-front-door.md).
- A [Front Door működésének](front-door-routing-architecture.md) ismertetése.
