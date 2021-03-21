---
title: Gyorsítótár ürítése az Azure bejárati ajtó standard/prémium (előzetes verzió)
description: Ebből a cikkből megtudhatja, hogyan törölheti a gyorsítótárat egy Azure-beli bejárati standard/prémium szinten.
services: frontdoor
author: duongau
manager: KumudD
ms.service: frontdoor
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: aacbf2ceab8580727b1885bf6533cd74a7c4e60a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101099061"
---
# <a name="cache-purging-in-azure-front-door-standardpremium-preview"></a>Gyorsítótár ürítése az Azure bejárati ajtó standard/prémium (előzetes verzió)

> [!Note]
> Ez a dokumentáció az Azure bejárati ajtó standard/Premium (előzetes verzió) verziójához készült. Információt keres az Azure bejárati ajtóról? Megtekintés [itt](../front-door-overview.md).

Az Azure bejárati ajtó standard/Premium gyorsítótárazza az eszközöket, amíg az eszköz élettartama (TTL) lejár. Ha egy ügyfél lejárt TTL-értékkel rendelkező objektumot kér, az Azure bejárati környezete az eszköz új, frissített példányát kéri le a kérelem kiszolgálására, majd a frissített gyorsítótárat tárolja.

Az ajánlott eljárás az, hogy a felhasználók mindig megkapják az eszközök legfrissebb példányát. Ennek az az módja, hogy az eszközöket az egyes frissítésekhez, és új URL-ként tegye közzé. Az Azure bejárati ajtó standard/Premium azonnal lekéri az új eszközöket a következő ügyfelek kéréseire. Esetenként előfordulhat, hogy a gyorsítótárazott tartalmakat törölni kívánja az összes peremhálózati csomópontról, és az összeset kényszeríti az új eszközök beolvasására. A gyorsítótárazott tartalmak törlésének oka az, hogy új frissítéseket készített az alkalmazáshoz, vagy ha olyan eszközöket szeretne frissíteni, amelyek helytelen adatokat tartalmaznak.

> [!IMPORTANT]
> Az Azure bejárati ajtó standard/Premium (előzetes verzió) jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Tekintse át az [Azure bejárati ajtó gyorsítótárazását](concept-caching.md) a gyorsítótárazás működésének megismeréséhez.

## <a name="configure-cache-purge"></a>Gyorsítótár kiürítésének konfigurálása

1. Nyissa meg az Azure bejárati profiljának áttekintés lapját a törölni kívánt eszközökkel, majd válassza a **gyorsítótár kiürítése** lehetőséget.

   :::image type="content" source="../media/how-to-cache-purge/front-door-cache-purge-1.png" alt-text="Képernyőkép a gyorsítótár kiürítéséről az Áttekintés oldalon.":::

1. Válassza ki azt a végpontot és tartományt, amelyet ki szeretne üríteni a peremhálózati csomópontokból. *(Több tartományt is kijelölhet)*

   :::image type="content" source="../media/how-to-cache-purge/front-door-cache-purge-2.png" alt-text="A gyorsítótár kiürítése lap képernyőképe.":::

1. Az összes eszköz törléséhez válassza **a kijelölt tartományokhoz tartozó összes eszköz kiürítése** lehetőséget. Ellenkező esetben a **paths (elérési utak**) mezőben adja meg a kiüríteni kívánt eszközök elérési útját.

Ezek a formátumok a kiüríteni kívánt elérési utak listája esetén támogatottak:

* **Egyetlen útvonal kiürítése**: az egyes eszközök kiürítése az eszköz teljes elérési útjának megadásával (a protokoll és a tartomány nélkül), például/Pictures/strasbourg.png.
* **Gyökértartomány kiürítése**: Ürítse ki a végpont gyökerét az elérési útban található "/*" értékkel.

A gyorsítótár kiürítése az Azure bejárati ajtó standard/preium nem megkülönbözteti a kis-és nagybetűket. Emellett a karakterláncok egyidejű lekérdezését is jelentik, ami azt jelenti, hogy az URL-cím ürítése törli az összes lekérdezési karakterlánc-változatot. 

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan [hozhat létre standard/prémium szintű bejárati ajtót](create-front-door-portal.md).
