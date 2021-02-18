---
title: Biztonsági fejlécek konfigurálása az Azure-beli előtérben standard/prémium (előzetes verzió) szabálykészlet
description: Ez a cikk útmutatást nyújt arról, hogyan használható a szabálykészlet a biztonsági fejlécek konfigurálására.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: c08ba57f43969bb2f0ee9c66b6cb4e92879ed258
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "101099109"
---
# <a name="configure-security-headers-with-azure-front-door-standardpremium-preview-rule-set"></a>Biztonsági fejlécek konfigurálása az Azure-beli előtérben standard/prémium (előzetes verzió) szabálykészlet

> [!Note]
> Ez a dokumentáció az Azure bejárati ajtó standard/Premium (előzetes verzió) verziójához készült. Információt keres az Azure bejárati ajtóról? Megtekintés [itt](../front-door-overview.md).

Ez a cikk bemutatja, hogyan implementálhatja a biztonsági fejléceket a böngészőalapú sebezhetőségek, például a HTTP Strict-Transport-Security (HSTS), az X-XSS-Protection, a Content-Security-Policy vagy az X-frame-Options használatának megakadályozására. A biztonság-alapú attribútumok cookie-kkal is meghatározhatók.

Az alábbi példa bemutatja, hogyan adhat hozzá egy Content-Security-Policy fejlécet az útvonal elérési útját megegyező bejövő kérelmekhez. Itt csak a megbízható webhelyről származó szkripteket engedélyezzük **https://apiphany.portal.azure-api.net** az alkalmazáson való futtatáshoz.

> [!IMPORTANT]
> Az Azure bejárati ajtó standard/Premium (előzetes verzió) jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

* A biztonsági fejlécek konfigurálása előtt először létre kell hoznia egy bejárati ajtót. További információkért lásd a [Rövid útmutató: Front Door létrehozása](create-front-door-portal.md) szakaszt.
* Tekintse át a [szabálykészlet beállítását](how-to-configure-rule-set.md) , ha korábban még nem használta a szabálykészlet funkciót.

## <a name="add-a-content-security-policy-header-in-azure-portal"></a>Tartalom-biztonsági házirend fejlécének hozzáadása a Azure Portal

1. Nyissa meg az Azure bejárati ajtó standard/Premium profilját, és válassza a **Settings** ( **szabálykészlet** ) lehetőséget a beállítások területen.

1. Új szabálykészlet hozzáadásához válassza a **Hozzáadás** lehetőséget. Adjon meg egy **nevet** a szabálynak, majd adja meg a szabály **nevét** . Válassza **a művelet hozzáadása** , majd a **Válasz fejléc** lehetőséget.

1. Állítsa be úgy a kezelőt, hogy **fűzze** hozzá ezt a fejlécet az útvonal összes bejövő kérelmének válaszként.

1. Adja hozzá a fejléc neve: **Content-Security-Policy** , és határozza meg, hogy a fejlécnek milyen értékeket kell elfogadnia. Ebben a forgatókönyvben a *"script-src" önmagát https://apiphany.portal.azure-api.net* választjuk.

1. Miután hozzáadta az összes szabályt, amelyet a konfigurációhoz szeretne rendelni, ne felejtse el hozzárendelni a szabályt egy útvonalhoz. Ez a lépés *szükséges* ahhoz, hogy a szabály meg lehessen adni a műveletet. 

> [!NOTE]
> Ebben a forgatókönyvben nem vettünk fel [egyezési feltételeket](concept-rule-set-match-conditions.md) a szabályhoz. A rendszer a társított útvonalon megadott elérési útra megegyező összes bejövő kérelmet alkalmazza. Ha azt szeretné, hogy csak a kérelmek egy részhalmazára vonatkozzon, ügyeljen arra, hogy a szabályhoz az adott **egyeztetési feltételeket** adja hozzá.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

### <a name="deleting-a-rule"></a>Szabály törlése

Az előző lépésekben beállította a Content-Security-Policy fejlécet a szabálykészlet beállítással. Ha már nincs szüksége egy szabályra, kiválaszthatja a szabálykészlet nevét, majd a szabály törlése lehetőséget is. 

### <a name="deleting-a-rule-set"></a>Szabálykészlet törlése

Ha törölni szeretne egy szabályt, győződjön meg róla, hogy az összes útvonalról a törlés előtt hozzárendeli azt. A szabálykészlet törlésével kapcsolatos részletes útmutatásért tekintse meg a [szabálykészlet konfigurálását](how-to-configure-rule-set.md)ismertető témakört.

## <a name="next-steps"></a>Következő lépések

Ha meg szeretné tudni, hogyan konfigurálhat egy webalkalmazási tűzfalat az előtérben, tekintse meg a [webalkalmazási tűzfalat és a bejárati ajtót](../../web-application-firewall/afds/afds-overview.md)
