---
title: Gyökér-vagy APEX-tartomány beléptetése meglévő Azure CDN-végpontra – Azure Portal
description: Megtudhatja, hogyan lehet egy root-vagy APEX-tartományt bevezetni egy meglévő Azure CDN végpontra a Azure Portal használatával.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: how-to
ms.date: 11/07/2020
ms.author: allensu
ms.openlocfilehash: 8ab4f698c7149d8d57f790e221ccbe35ec090fe6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94370356"
---
# <a name="onboard-a-root-or-apex-domain-to-an-existing-azure-cdn-endpoint"></a>Gyökér-vagy APEX-tartomány beléptetése meglévő Azure CDN-végpontba

A Azure CDN CNAME rekordokat használ a tartomány tulajdonjogának ellenőrzéséhez az egyéni tartományok bevezetéséhez. A CDN nem teszi elérhetővé a CDN-profilhoz társított előtérbeli IP-címet. A csúcspont tartománya nem rendelhető hozzá IP-címhez, ha a célja, hogy Azure CDN.

A DNS protokoll megakadályozza a CNAME rekordok hozzárendelését a zóna csúcsán. Ha például a tartománya a `contoso.com` ; CNAME rekordokat hozhat létre a számára, `somelabel.contoso.com` de saját maga nem hozhat létre CNAME-t `contoso.com` . Ez a korlátozás olyan alkalmazás-tulajdonosokkal kapcsolatos problémát jelent, akik Azure CDN mögötti elosztott terhelésű alkalmazásokkal rendelkeznek. Mivel a CDN-profil használata megköveteli egy CNAME rekord létrehozását, nem lehetséges a CDN-profilra mutatni a zóna csúcspontján.

Ez a probléma a Azure DNSban található alias-rekordok használatával oldható fel. A CNAME rekordoktól eltérően az alias-rekordok a zóna csúcsán jönnek létre. Az alkalmazás tulajdonosai használhatják a zóna csúcspont-rekordját egy nyilvános végpontokkal rendelkező CDN-profilra. Az alkalmazások tulajdonosai ugyanarra a CDN-profilra mutatnak, amelyet a DNS-zónán belüli bármely más tartományhoz használtak. Például, `contoso.com` és `www.contoso.com` ugyanarra a CDN-profilra mutathat. 

A csúcspont vagy a gyökértartomány a CDN-profilhoz való leképezéséhez CNAME-összeolvasztás vagy DNS-elkergetés szükséges. Olyan mechanizmus, amelyben a DNS-szolgáltató rekurzív módon oldja fel a CNAME-bejegyzést, amíg az IP-címet nem üt. Ezt a funkciót a CDN-végpontok Azure DNS támogatja. 

> [!NOTE]
> Vannak más DNS-szolgáltatók is, amelyek támogatják a CNAME-összeolvasztást vagy a DNS-t, azonban Azure CDN a Azure DNS használatát javasolja ügyfelei számára a tartományok üzemeltetéséhez.

A Azure Portal a CDN-tartomány bevezetéséhez és a HTTPS engedélyezéséhez, ha társítja azt tanúsítvány használatával a TLS-lezáráshoz. Az APEX-tartományokat root vagy Naked tartománynak is nevezzük.

## <a name="create-an-alias-record-for-zone-apex"></a>Alias-rekord létrehozása a zóna csúcsához

1. Nyissa meg **Azure DNS** konfigurációt a bekészíteni kívánt tartományhoz.

2. Válassza a **+ Rekordhalmaz** lehetőséget.

3. A **rekordtípus hozzáadása** mezőben adja meg vagy válassza ki a következő adatokat:

    | Beállítás | Érték |
    | ------- | ------|
    | Név | Adja meg a értéket **@** . |
    | Típus | Válasszon ki **egy** elemet. |
    | Aliasrekord-halmaz | Válassza az **Igen** lehetőséget. |
    | Alias típusa | Válassza ki az **Azure-erőforrás** elemet. |
    | Előfizetés kiválasztása | Válassza ki előfizetését. |
    | Azure-erőforrás | Válassza ki a CDN-végpontot. |

4. Adja meg az **élettartam** értékét.

5. A módosítások elküldéséhez kattintson **az OK gombra** .

    :::image type="content" source="./media/onboard-apex-domain/cdn-apex-alias-record.png" alt-text="Alias rekord a zóna csúcsához":::

6. A fenti lépés egy zónához tartozó csúcspont-rekordot hoz létre, amely a CDN-erőforrásra mutat. CNAME rekord-leképezési **cdnverify** használ a tartománynak a CDN-profilba való bevezetéséhez.
    1. Példa: **cdnverify.contoso.com**.
    

## <a name="onboard-the-custom-domain-on-your-cdn"></a>Az egyéni tartomány előkészítése a CDN-re

Miután regisztrálta az egyéni tartományát, hozzáadhatja azt a CDN-végpontjához. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/), és navigáljon arra a CDN-profilra, amely tartalmazza azt a végpontot, amelyet az egyéni tartományhoz kíván leképezni.
    
2. A **CDN-profil** oldalon válassza ki a CDN-végpontot, amelyet társítani kíván az egyéni tartománnyal.

    :::image type="content" source="media/onboard-apex-domain/cdn-endpoint-selection.png" alt-text="CDN-végpont kiválasztása" border="true":::
    
3. Válassza az **+ egyéni tartomány** lehetőséget. 

   :::image type="content" source="media/onboard-apex-domain/cdn-custom-domain-button.png" alt-text="Egyéni tartomány hozzáadása gomb" border="true":::

4. Az **egyéni tartomány hozzáadásakor** a **végponti állomásnév** előre ki van töltve, és a CDN-végpont URL-címéből származik: **\<endpoint-hostname>** . azureedge.net. A név nem módosítható.

5. **Egyéni állomásnév** esetén adja meg az egyéni gyökér-vagy APEX-tartományt, amelyet a CNAME rekord forrás tartományához kíván használni. 
    1. Például adja meg a **contoso.com** nevet. **Ne használja a cdnverify aldomain nevét**.

    :::image type="content" source="media/onboard-apex-domain/cdn-add-custom-domain.png" alt-text="Egyéni tartomány hozzáadása" border="true":::

6. Válassza a **Hozzáadás** lehetőséget.

   Az Azure ellenőrzi, hogy a megadott egyéni tartománynév esetében létezik-e a CNAME rekord. Ha a CNAME helyes, az egyéni tartomány érvényesítve lesz. 

   Időbe telhet, amíg az új egyéni tartománybeállítások propagálása az összes CDN-határcsomópontra megtörténik: 
    - A **Microsoft Azure CDN Standard** típusú profilok propagálása általában 10 perc alatt fejeződik be. 
    - Az **Akamai Azure CDN Standard** típusú profilok propagálása általában egy percen belül befejeződik. 
    - A **Verizon Azure CDN Standard** és a **Verizon Azure CDN Premium** típusú profilok propagálása általában 10 perc alatt fejeződik be.   

## <a name="enable-https-on-your-custom-domain"></a>HTTPS engedélyezése az egyéni tartományon

További információ a HTTPS engedélyezéséről az egyéni tartományon a Azure CDN számára: [oktatóanyag: https konfigurálása Azure CDN egyéni tartományon](cdn-custom-ssl.md)

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan [hozhat létre CDN-végpontot](cdn-create-new-endpoint.md).
