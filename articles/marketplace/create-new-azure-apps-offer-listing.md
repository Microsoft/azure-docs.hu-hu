---
title: Az Azure-alkalmazások ajánlati listájának konfigurálása – részletek
description: Ismerje meg, hogyan konfigurálhatja az Azure-alkalmazás ajánlatának listáját a partner Centerben.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: 91b6d432042aa37a1b929f61e9742f814fb9dc40
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94370326"
---
# <a name="how-to-configure-your-azure-application-offer-listing-details"></a>Az Azure-alkalmazások ajánlati listájának konfigurálása – részletek

Az Azure-alkalmazás ajánlati **listája** oldalon megadott információk a Microsoft kereskedelmi piactér online áruházában jelennek meg. Ide tartozik az ajánlat, a képernyőképek és a marketing-eszközök leírása. Ha szeretné megtekinteni, hogy ez hogyan néz ki, tekintse meg az [ajánlat részletei](plan-azure-application-offer.md#offer-listing-details)című témakört.

> [!NOTE]
> Az ajánlat tartalmának listázása (például a leírás, a dokumentumok, a képernyőképek és a használati feltételek) nem szükséges angolul, ha az ajánlat leírása a következő kifejezéssel kezdődik: "Ez az alkalmazás csak a [nem angol nyelven] érhető el." Azt is elfogadható, hogy egy _hasznos hivatkozási_ URL-címet adjon meg, amely nem az ajánlatban szereplő tartalomban használt tartalmat tartalmazza.

## <a name="marketplace-details"></a>Piactér – részletek

Az **ajánlati lista** lapon, a **piactér részletei** területen hajtsa végre az alábbi lépéseket. Ha többet szeretne megtudni ezekről a szükséges adatokról, tekintse meg az [ajánlati lista részleteit](plan-azure-application-offer.md#offer-listing-details).

1. A **Name (név** ) mező az **új ajánlat** párbeszédpanelen korábban megadott névvel van feltöltve. A nevet bármikor módosíthatja. Az itt megadott név jelenik meg az ügyfelek számára az ajánlati lista címének megfelelően.
1. A **találatok összegzése** mezőben adja meg a legfeljebb 100 karakter hosszúságú szöveget. Ezt az összegzést a piactér keresési eredményei között lehet használni.
1. A **rövid leírás** mezőbe írja be a következőt: 256 karakterből álló egyszerű szöveg. Ez az összefoglalás az ajánlat részletek lapján jelenik meg.
1. A **Leírás** mezőben adja meg az ajánlat leírását. Ez a szövegmező olyan Rich Text Editor-vezérlőkkel rendelkezik, amelyekkel a Leírás még vonzóbbá teheti a leírást. A leírást a HTML-címkék használatával is formázhatja. Ebben a mezőben legfeljebb 3 000 karaktert adhat meg, amely HTML-jelölést és szóközöket tartalmaz. További információ a HTML-formázásról: [a kereskedelmi piactér ajánlatának leírásában támogatott HTML-címkék](supported-html-tags.md).
1. Választható A **keresési kulcsszavak** mezőben adjon meg legfeljebb három olyan keresési kulcsszót, amelyet az ügyfelek a kereskedelmi piactéren találhatnak. Nem kell megadnia az ajánlat **nevét** és **leírását** , mert a szöveg automatikusan belekerül a keresésbe.
1. Az **adatvédelmi szabályzat hivatkozása** mezőbe írjon be egy hivatkozást (a https-vel kezdődően) a szervezete adatvédelmi szabályzatához. Ön felelős azért, hogy az alkalmazás megfeleljen az adatvédelmi törvényeknek és előírásoknak, valamint érvényes adatvédelmi szabályzatot biztosítson.

## <a name="add-supplemental-links-optional"></a>Kiegészítő hivatkozások hozzáadása (nem kötelező)

A következő lépések végrehajtásával kiegészítő online dokumentációra mutató hivatkozásokat adhat hozzá.

1. Az alkalmazással vagy a kapcsolódó szolgáltatásokkal kapcsolatos opcionális kiegészítő online dokumentumok hozzáadásához kattintson a **hasznos hivatkozások** területen található **hivatkozás hozzáadása** lehetőségre.
1. A megjelenő mezőkben adja meg a címet (legfeljebb 255 karakter) és az online dokumentumra mutató hivatkozást (kezdve `https://` ).
1. Egy másik hivatkozás megadásához ismételje meg az 1 – 2. lépést.

## <a name="enter-your-contact-information"></a>Adja meg a kapcsolattartási adatait

A **kapcsolattartási adatok** területen adja meg a következő névjegyek adatait:

- **Támogatási kapcsolattartó** (kötelező) – általános támogatási kérdésekre.
- **Engineering Contact** (kötelező) – technikai kérdésekben. Ezeket az információkat felhasználjuk arra, hogy kapcsolatba lépjen Önnel, ha problémái vannak az ajánlattal kapcsolatban, beleértve a minősítési problémákat is.
- **CSP programbeli Kapcsolatfelvétel** (nem kötelező) – támogatás és üzleti problémák esetén. Ezek az információk csak a CSP-partnerek számára láthatók.

Minden névjegyhez meg kell adnia egy nevet, egy telefonszámot és egy e-mail-címet (ezek nem jelennek meg nyilvánosan). Támogatási URL-cím szükséges a támogatási kapcsolattartóhoz (ez nyilvánosan jelenik meg).

1. A **támogatási kapcsolattartók** mezőben adja meg a támogatási oldal nevét, e-mail-címét, telefonszámát és URL-címét.
1. A **mérnöki kapcsolattartók** mezőben adja meg a nevet, az e-mail-címet és a telefonszámot.
1. Választható A **CSP program elérhetősége** mezőben adja meg a nevet, az e-mail-címet és a telefonszámot.
1. Ha az ajánlatot a [Cloud Solution Provider (CSP) programra](cloud-solution-providers.md)szeretné bővíteni, a **CSP-program marketing-anyagok** mezőjében adjon meg egy hivatkozást a marketing-anyagokra.

    > [!NOTE]
    > A CSP program kiterjeszti az ajánlatot a minősített ügyfelek szélesebb körére azáltal, hogy lehetővé teszi a CSP-partnerek számára, hogy csomagokat, piacokat és viszonteladást nyújtsanak. Ezeknek a viszonteladóknak hozzá kell férniük az anyagokhoz az ajánlat marketingje érdekében. További információ: piacra jutás [a Microsofttal](https://partner.microsoft.com/reach-customers/gtm).

## <a name="add-marketplace-media"></a>Piactéri adathordozó hozzáadása

### <a name="store-logos"></a>Emblémák tárolása

A **Logos** alatt töltsön fel egy **nagy méretű** emblémát PNG formátumban 216 x 216 és 350 x 350 képpont közé. A partner Center automatikusan **kis** (48 x 48) és **közepes** (90 x 90) emblémákat hoz létre, amelyeket később lecserélhet, ha szeretné.

Mindhárom embléma mérete az online áruházak különböző helyein használatos.

- A nagyméretű embléma megjelenik az ajánlat listázási lapján az Azure Marketplace-en.
- A közepes embléma akkor jelenik meg, amikor új erőforrást hoz létre Microsoft Azureban.
- A kis embléma az Azure piactér keresési eredményei között jelenik meg.

### <a name="add-screenshots-optional"></a>Képernyőképek hozzáadása (nem kötelező)

Adjon meg legfeljebb öt képernyőképet, amely bemutatja az ajánlatát. Az összes képnek 1280 x 720 képpont méretűnek és a-ben kell lennie. PNG-formátum.

1. A **képernyőképek** alatt húzza át a t. PNG-fájl a **képernyőfelvételek** mezőjébe.
1. A **Képfelirat hozzáadása** elem mellett kattintson a Szerkesztés ikonra.
1. A megjelenő párbeszédpanelen adjon meg egy feliratot, és kattintson **az OK** gombra.
1. További képernyőképek hozzáadásához ismételje meg az 1 – 3. lépést.

### <a name="add-videos-optional"></a>Videók hozzáadása (nem kötelező)

Az ajánlatot bemutató YouTube-vagy Vimeo-videókra mutató hivatkozásokat is hozzáadhat. Ezek a videók az Ön ajánlatával együtt jelennek meg az ügyfelek számára. A videó miniatűr képét kell megadnia, mérettől 1280 x 720 képpont méretűre és a-ben. PNG-formátum. Ajánlat legfeljebb négy videót adhat hozzá.

1. A **videók** területen válassza a **videó hozzáadása** hivatkozást.
1. A megjelenő mezőkben adja meg a videó nevét és hivatkozását.
1. Húzzon át egy elemet. PNG-fájl (1280 x 720 képpont) a szürke **miniatűr** dobozba.
1. Egy másik videó hozzáadásához ismételje meg az 1 – 3. lépést.

> [!TIP]
> Ha probléma merül fel a fájlok feltöltésekor, győződjön meg arról, hogy a helyi hálózat nem blokkolja a https://upload.xboxlive.com partner központ által használt szolgáltatást.

A következő lapra való továbblépés előtt válassza a **Piszkozat mentése** lehetőséget: megtekintheti a célközönséget.

## <a name="next-steps"></a>Következő lépések

- [Előzetes verzió célközönségének hozzáadása az Azure-alkalmazás ajánlatához](create-new-azure-apps-offer-preview.md)
