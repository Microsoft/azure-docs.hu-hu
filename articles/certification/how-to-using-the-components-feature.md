---
title: Az összetevők funkció használata az Azure Certified Device Portalon
description: Útmutató az eszköz részletei szakasz összetevők funkciójának legmegfelelőbb használatához az eszköz pontos leírásához
author: nikuntjo
ms.author: nikuntjo
ms.service: certification
ms.topic: how-to
ms.date: 03/03/2021
ms.custom: template-how-to
ms.openlocfilehash: 091a666026853d677f1e9ed7e5e1736307cecbe8
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969288"
---
# <a name="add-components-on-the-portal"></a>Összetevők hozzáadása a portálon

Az oktatóanyag elvégzése során az eszköz adatainak a minősítési projekthez való [hozzáadásához](tutorial-02-adding-device-details.md) az eszköz hardveres specifikációit is le kell írnia. Ehhez a felhasználók több, különálló hardveres terméket (más néven **összetevőt**) is kijelölhetnek az eszközön. Ez lehetővé teszi, hogy jobban támogassa a további hardveres eszközöket, és lehetővé teszi, hogy az ügyfelek megtalálják a megfelelő terméket a katalógusban a szolgáltatások alapján.

## <a name="prerequisites"></a>Előfeltételek

- Be kell jelentkeznie, és rendelkeznie kell egy projekttel az eszköz számára, amelyet az [Azure Certified Device Portalon](https://certify.azure.com)hozott létre. További információkért tekintse meg az [oktatóanyagot](tutorial-01-creating-your-project.md).

## <a name="how-to-add-components"></a>Összetevők hozzáadása

Minden minősítésre benyújtott projekt tartalmazni fog egy **ügyfél-Ready termék** összetevőt (ami sok esetben maga a holisztikus termék lesz). Az ügyfél által készített termék-összetevő típusának pontosabb megismeréséhez tekintse meg a [minősítési szószedetet](./resources-glossary.md). Az összes további összetevő a saját belátása szerint is pontosan rögzíti az eszközt.

1. Válassza a `Add a component` Product Details (termék részletei) lapot.

    ![Összetevő-hivatkozás hozzáadása](./media/images/add-a-component-link.png)

1. Töltse ki a megfelelő űrlapmezőket az összetevőhöz.

    ![Összetevő részletei szakasz](./media/images/component-details-section.png)

1. Mentse az adatait az `Save Product Details` oldal alján található gomb használatával:  

    ![Termék részleteinek mentése gomb](./media/images/save-product-details-button.png)

1. Miután mentette az összetevőt, továbbra is testreszabhatja az általa támogatott hardver-képességeket. Válassza ki a `Edit` hivatkozást az összetevő neve alapján.  

    ![Összetevő szerkesztése gomb](./media/images/component-edit.png)

1. Szükség esetén adja meg a megfelelő hardver-képességi információkat.  

    ![Szerkeszthető összetevőkről szóló rész képe](./media/images/component-selection-area.png)  

    A szerkeszthető összetevő mezői (fent láthatók) a következők:

    - **Általános**: a hardver részletes adatai, például a processzorok és a biztonságos hardver
    - **Kapcsolat**: csatlakozási lehetőségek, protokollok és felületek, például rádió (k) és GPIO
    - **Gyorssegédek**: olyan hardveres gyorsítást ad meg, mint például a GPU és a VPU
    - **Érzékelők**: az elérhető érzékelők, például a GPS és a vibráció meghatározása
    - **További specifikációk**: további információk az eszközről, mint például a fizikai méretek és a tárolási/akkumulátor-információk

1. Válassza ki a `Save Product Details` Product Details (termék részletei) lap alján.

## <a name="component-use-requirements-and-recommendations"></a>Összetevő-használati követelmények és javaslatok

Lehet, hogy kérdései vannak a felvenni kívánt összetevők közül, vagy a használandó összetevő típusát. Az alábbiakban néhány példát láthat néhány olyan eszközre, amelyet Ön tanúsít, és hogyan használhatja az összetevők funkciót.

| Termék típusa                                       | Nem. Összetevők | 1. összetevő/melléklet típusa      | Components 2 +/melléklet típusa                    |
|----------------------------------------------------|------------|----------------------------------|--------------------------------------------------|
| Végtermék                                   | 1          | Ügyfélre felkészített termék, különálló | N/A                                              |
| Végtermék **leválasztható perifériával (s)** | 2 vagy több  | Ügyfélre felkészített termék, különálló | Perifériás/diszkrét vagy integrált              |
| Elkészült termék **integrált komponens (ek)** vel  | 2 vagy több  | Ügyfélre felkészített termék, különálló | Válassza a megfelelő típus/különálló vagy integrált lehetőséget |
| Solution-Ready fejlesztői csomag                             | 2 vagy több  | Ügyfélre felkészített termék, különálló | Válassza a megfelelő típus/különálló vagy integrált lehetőséget |

## <a name="example-component-usage"></a>Példa összetevő-használatra

Az alábbi példák azt mutatják be, hogyan használható a contoso nevű SZÁMÍTÓGÉPGYÁRTÓ az összetevők szolgáltatással, hogy igazolja a termékét.

1. A Falcon egy teljes körű önálló eszköz, amely nem integrálható egy nagyobb termékbe.
    1. Nem. összetevők: 1
    1. Összetevő-eszköz típusa: ügyfélre felkészített termék
    1. Melléklet típusa: diszkrét

     ![Az ügyfél által felkészített termék képe](./media/images/customer-ready-product.png)

1. A Falcon egy olyan eszköz, amely egy, az USB-ről a Falcon-kapcsolaton keresztül csatlakozó, az INC Electronics által gyártott integrált periféria-modult tartalmaz.
    1. Nem. összetevők: 2
    1. Összetevő-eszköz típusa: ügyfélre felkészített termék, perifériás
    1. Melléklet típusa: diszkrét, integrált
    
    > [!Note]
    > A perifériás összetevő integráltnak minősül, mert nem cserélhető.

     ![Perifériás példa összetevő képe](./media/images/peripheral.png)

1. A Falcon egy olyan eszköz, amely egy, a vállalati Espressif beépített processzor-Apollo52 használó integrált rendszerét tartalmazza, és egy ARM64-architektúrával rendelkezik.
    1. Nem. összetevők: 2
    1. Összetevő típusa: ügyfélre felkészített termék, rendszer a modulban
    1. Melléklet típusa: diszkrét, integrált

    > [!Note]
    > A perifériás összetevő integráltnak minősül, mert nem cserélhető. A SoM összetevő a processzor adatait is tartalmazza.

     ![A rendszer rendszerképe a modulban – példa összetevő ](./media/images/system-on-module.png)

## <a name="additional-tips"></a>További tippek

Az alábbiakban részletesebben ismertetjük az összetevők használatára vonatkozó szabályzatot. Ha bármilyen kérdése van a megfelelő összetevő-használattal kapcsolatban, vegye fel a kapcsolatot a csapatával [iotcert@microsoft.com](mailto:iotcert@microsoft.com) , és mi több, mint örömmel segítünk!

1. A projektnek **csak** egy ügyfél-előkészítési termék összetevőjét kell tartalmaznia. Ha két független eszközzel rendelkező projektet tanúsít, ezeket az eszközöket külön kell hitelesíteni.
1. Elsődlegesen a (vagy nem használt) összetevők használatával népszerűsítheti az eszköz képességeit a potenciális ügyfelek számára.
1. Az eszköz áttekintése során az Azure minősítési csapatnak csak a listához legalább egy, az ügyfélre felkészített termék-összetevőre lesz szüksége. Előfordulhat azonban, hogy a részletek nem egyértelműek, vagy hiányoznak a hiányzó adatok (például az összetevő gyártója nem érhető el az ügyfél számára kész terméktípus esetében).

## <a name="next-steps"></a>Következő lépések

Most, hogy készen áll az összetevők szolgáltatás használatára, készen áll az eszköz részleteinek elvégzésére vagy a projekt szerkesztésére a további egyértelműség érdekében.

- [Oktatóanyag: eszköz adatainak hozzáadása](tutorial-02-adding-device-details.md)
- [A közzétett eszköz szerkesztése](how-to-edit-published-device.md)

