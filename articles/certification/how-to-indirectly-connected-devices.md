---
title: Certifing eszközök és közvetve csatlakoztatott eszközök
titleSuffix: Azure Certified
description: 'Lásd: indirekt módon csatlakoztatott eszköz beküldése minősítéshez.'
author: cbroad
ms.author: cbroad
ms.date: 02/23/2021
ms.topic: how-to
ms.service: certification
ms.openlocfilehash: 3a4fd2838c0ddf6d7d03d68f105fc59471b77dea
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304477"
---
# <a name="device-bundles-and-indirectly-connected-devices"></a>Eszköz-kötegek és közvetve csatlakoztatott eszközök

Az Azure-ban egy eszközön, SaaS-n vagy Pástin keresztül kommunikáló eszközök támogatásához a beküldési portál ( https://certify.azure.com/) és az eszköz katalógusa) (a csomagban lévő és az eszközökhöz kapcsolódó https://devicecatalog.azure.com) függőségek is lehetővé teszik, hogy az eszközök kombinációi hozzáférhessenek az Azure Certified eszköz programhoz.

A termék és a kínált szolgáltatások típusától függően előfordulhat, hogy a helyzet a következő lépések kombinációját igényli:


![Projekt függőségeinek létrehozása](./media/indirect-connected-device/picture-1.png )
## <a name="sensors-and-indirect-devices"></a>Érzékelők és közvetett eszközök
Számos érzékelőhöz szükség van egy eszközre az Azure-hoz való kapcsolódáshoz. Emellett előfordulhat, hogy több kompatibilis eszköz is működik az érzékelő eszközzel. **A forgatókönyvek elvégzéséhez először igazolnia kell az eszköz (eke) t, mielőtt tanúsítja az érzékelőt, amely a rajtuk keresztül továbbítja az adatokat.**

Példa a küldési kombinációk ![ beküldési példájának mátrixára](./media/indirect-connected-device/picture-2.png )

Az érzékelő hitelesítéséhez, amelyhez külön eszközre van szükség:
1.  Először [hitelesítse az eszközt](https://certify.azure.com) , és tegye közzé az Azure Certified-eszköz katalógusát
    - Ha több, kompatibilis áteresztő eszközzel rendelkezik (ahogy a fenti példában is látható), küldje el külön a minősítésre, és tegye közzé a katalógusban is.
2.  Az eszközön keresztül csatlakoztatott érzékelővel küldje el az érzékelőt a minősítéshez
    * Az "eszköz részletei&quot; szakasz &quot;függőségek&quot; lapján állítsa be a következő értékeket:
        * Függőség típusa = &quot;hardver átjáró&quot;
        * Függőségi URL = &quot;URL-hivatkozás az eszközre az eszköz katalógusában&quot;
        * Tesztelés során használatos = &quot;igen&quot;
        * Vegyen fel olyan felhasználói megjegyzéseket, amelyeket meg kell adni egy olyan felhasználónak, aki látja a termék leírását az eszköz katalógusában. (példa: &quot;Series 100-eszközök szükségesek az érzékelőkhöz az Azure-hoz való kapcsolódáshoz")

3.  Ha az eszközhöz több eszközt is szeretne hozzáadni, válassza a "+ további függőség hozzáadása" lehetőséget. Ezután kövesse ugyanezt az útmutatót, és vegye figyelembe, hogy a tesztelés során nem használták. Az ügyfél felé irányuló megjegyzésekben győződjön meg arról, hogy az ügyfelek az érzékelőhöz kapcsolódó más eszközöket is elérhetők (a tesztelés során használt eszköz alternatívájaként).

![Helyettesítő szöveg](./media/indirect-connected-device/picture-3.png "Hardver-függőség típusa")

## <a name="paas-and-saas-offerings"></a>Pásti és SaaS ajánlatok
A termék portfóliójának részeként lehet, hogy Ön tanúsítja az eszközöket, de az eszköz más szolgáltatásokat is igényel a vállalattól vagy más harmadik féltől származó vállalattól. A függőség hozzáadásához kövesse az alábbi lépéseket:
1. Az eszköz beküldési folyamatának elindítása
2. A "függőségek" lapon állítsa be a következő értékeket:
    - Függőség típusa = "szoftver szolgáltatás"
    - Szolgáltatásnév = "[a termék neve]"
    - Függőségi URL = "URL-hivatkozás a szolgáltatást leíró termék oldalára"
    - Vegyen fel minden olyan felhasználói megjegyzést, amelyet meg kell adni egy olyan felhasználónak, aki látja a termék leírását az Azure Certified-eszköz katalógusában
3. Ha más szoftverekkel, szolgáltatásokkal vagy hardveres függőségekkel szeretné felvenni az eszközt opcionálisként, válassza a "+ további függőség hozzáadása" lehetőséget, és kövesse ugyanezt az útmutatást.

![Szoftver-függőség típusa](./media/indirect-connected-device/picture-4.png )

## <a name="bundled-products"></a>Csomagolt termékek
A csomagban lévő Terméklista egyszerűen egy másik összetevővel rendelkező eszköz sikeres minősítése, amely egy termék listájában a köteg részeként lesz értékesítve. Lehetősége van olyan eszköz beküldésére, amely további összetevőket tartalmaz, például egy hőmérséklet-érzékelőt és egy kamera-érzékelőt (#1), vagy olyan érintéses érzékelőt is küldhet, amely egy áteresztő eszközt (#2) tartalmaz. Az "összetevő" funkció segítségével több összetevőt is hozzáadhat a listához.

Ha ezt szeretné tenni, formázza a termék-tőzsdei képet, hogy jelezze, hogy a termék más összetevőket tartalmaz.  Továbbá, ha a köteg további szolgáltatásokat igényel a hitelesítéshez, azonosítania kell azokat a szolgáltatások függőségein keresztül.
Példa a kötegelt termékek mátrixára

![Példa a Bundle beküldésére](./media/indirect-connected-device/picture-5.png )

A [Súgó dokumentációjában](./how-to-using-the-components-feature.md)részletesebben tájékozódhat arról, hogyan használhatja az összetevő-funkciókat az Azure Certified Device Portalon. 

Ha egy eszköz egy különálló érzékelővel rendelkező áteresztő eszköz ugyanabban a termékben, hozzon létre egy összetevőt az áteresztő eszköz megjelenítéséhez, és egy másik összetevőt, amely az érzékelőt tükrözi. Az összetevők hozzáadhatók a projekthez az eszköz részletei szakasz Product Details (termék részletei) lapján:

![Összetevők hozzáadása](./media/indirect-connected-device/picture-6.png )

Az áteresztő eszközön állítsa be az összetevő típusát ügyfélként felkészített termékként, és adja meg a többi mezőt a termékhez kapcsolódóan. Példa:

![Összetevő részletei](./media/indirect-connected-device/picture-7.png )

Az érzékelőhöz adjon hozzá egy második összetevőt, amely az összetevő típusát a perifériák és a mellékletek módszerének megfelelően állítja be Diszkrétként. Példa:

![Második összetevő részletei](./media/indirect-connected-device/picture-8.png )

Miután létrehozta az érzékelő összetevőt, szerkessze a részleteket, navigáljon az érzékelők lapra, és adja hozzá az érzékelő részleteit. Példa:

![Érzékelő részletei](./media/indirect-connected-device/picture-9.png )

Fejezze be a projektek részleteit, és küldje el az eszközét a szokásos módon történő minősítéshez.

