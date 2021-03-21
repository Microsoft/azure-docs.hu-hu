---
title: A felügyelt szolgáltatási ajánlat részleteinek konfigurálása a Microsoft partner Centerben
description: Ismerje meg, hogyan konfigurálhatja a felügyelt szolgáltatás ajánlati adatait az Azure Marketplace-en a partner Center használatával.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 12/23/2020
ms.openlocfilehash: 5ab831ae6ab761804fc7a4665000a13ab61acadc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97918295"
---
# <a name="how-to-configure-your-managed-service-offer-listing-details"></a>A felügyelt szolgáltatás ajánlati listájának konfigurálása – részletek

A partner Center **ajánlati lista** lapján megadott információk az Azure piactéren jelennek meg. Ez magában foglalja az ajánlat nevét, leírását, adathordozóját és egyéb marketing-eszközeit.

> [!NOTE]
> Ha az ajánlat az angoltól eltérő nyelven érhető el, az ajánlati lista ezen a nyelven érhető el, de a leírásnak a következő angol kifejezéssel kell kezdődnie vagy végződnie: "Ez a szolgáltatás az &lt; ajánlat tartalma> nyelvén elérhető. A támogatási dokumentumokat olyan nyelven is megadhatja, amely különbözik az ajánlati lista részleteiben használt nyelvtől.

A partner Center **ajánlati lista** lapján adja meg az alább ismertetett információkat. Ha többet szeretne megtudni a felügyelt szolgáltatás ajánlatának részleteiről, tekintse át a [felügyelt szolgáltatás ajánlatának megtervezése](./plan-managed-service-offer.md)című témakört.

## <a name="marketplace-details"></a>Piactér – részletek

1. A **név** mező előre ki van töltve az új ajánlat párbeszédpanelen korábban megadott névvel, de bármikor módosítható. Ez a név jelenik meg az ajánlatnak az online áruházban való listájában.
2. A **találatok összegzése** mezőben adja meg az ajánlat célját vagy célját 100 karakternél vagy annál kevesebb karakternél.
3. A **rövid leírás** mezőben adja meg az ajánlat rövid leírását (legfeljebb 256 karakter). Ez a Azure Portalban jelenik meg az ajánlatok listájában.
4. A **Leírás** mezőben írja le a felügyelt szolgáltatás ajánlatát. Ebben a mezőben legfeljebb 2 000 karaktert adhat meg, beleértve a HTML-címkéket és a szóközöket is. További információ a HTML-formázásról: [az ajánlat leírásában támogatott HTML-címkék](./supported-html-tags.md).
5. Az **adatvédelmi szabályzat hivatkozása** mezőbe írjon be egy hivatkozást (a https-vel kezdődően) a szervezete adatvédelmi szabályzatához. Ön felelős azért, hogy az ajánlat megfelel az adatvédelmi törvényeknek és előírásoknak, valamint érvényes adatvédelmi szabályzatot biztosítson.

## <a name="useful-links"></a>Hasznos hivatkozások

Lehetősége van a megoldással kapcsolatos kiegészítő online dokumentumok biztosítására:

1. Válassza **a hivatkozás hozzáadása** lehetőséget.
2. Minden dokumentumhoz adjon meg egy nevet és egy webcímet (https-től kezdődően).

## <a name="contact-information"></a>Kapcsolattartási adatok

Adja meg a vállalatban két személy nevét, e-mail-címét és telefonszámát (Ön lehet az egyik): egy támogatási kapcsolattartó és egy mérnöki kapcsolattartó. Ezt az információt fogjuk használni az ajánlattal való kommunikációhoz. Ezek az információk nem jelennek meg az ügyfelek számára, de a Cloud Solution Provider (CSP) partnerei számára is elérhetők.

## <a name="support-urls"></a>Támogatási URL-címek

Ha az Azure-beli globális ügyfelek és/vagy Azure Government ügyfelek számára is támogatja a webhelyeket, adja meg az URL-címét a https-től kezdődően.

## <a name="marketplace-media"></a>Piactéri adathordozó

> [!NOTE]
> Ha probléma merül fel a fájlok feltöltésekor, győződjön meg arról, hogy a helyi hálózat nem blokkolja a `https://upload.xboxlive.com` partner központ által használt szolgáltatást.

### <a name="add-logos"></a>Emblémák hozzáadása

Az **emblémák** területen töltsön fel egy **nagyméretű** emblémát a alkalmazásban. PNG-formátum 216 x 216 és 350 x 350 képpont között. A partner Center automatikusan létrehoz **közepes** és **kis** emblémákat, amelyeket később lecserélhet.

* A nagyméretű embléma (216 x 216 – 350 x 350 px) megjelenik az ajánlaton az Azure Marketplace-en.
* A közepes embléma (90 x 90 px) új erőforrás létrehozásakor jelenik meg.
* A kis embléma (48 x 48 px) az Azure piactér keresési eredményeiben használható.

### <a name="add-screenshots-optional"></a>Képernyőképek hozzáadása (nem kötelező)

Akár öt képet is hozzáadhat az ajánlat bemutatásához. Az összes képnek 1280 x 720 képpont méretűnek és a-ben kell lennie. PNG-formátum.

1. A **képernyőképek** területen húzza a PNG-fájlját a **képernyőfelvétel** mezőbe.
2. Válassza a **Képfelirat hozzáadása** lehetőséget.
3. A megjelenő párbeszédpanelen adjon meg egy feliratot.
4. További képernyőképek hozzáadásához ismételje meg az 1 – 3. lépést.

### <a name="add-videos-optional"></a>Videók hozzáadása (nem kötelező)

Az ajánlatot bemutató YouTube-vagy Vimeo-videókra mutató hivatkozásokat is hozzáadhat. Ezek a videók az Ön ajánlatával együtt jelennek meg az ügyfelek számára. Meg kell adnia a videó miniatűr képét, a méret 1280 x 720 képpontot és a-t. PNG-formátum. Ajánlat legfeljebb öt videót adhat hozzá.

1. A **videók** területen válassza a **videó hozzáadása hivatkozást**.
2. A megjelenő mezőkben adja meg a videó nevét és hivatkozását.
3. Húzzon egy PNG-fájlt (1280 x 720 képpont) a szürke **miniatűrre** .
4. Egy másik videó hozzáadásához ismételje meg az 1 – 3. lépést.

A következő lapra való továbblépés előtt válassza a **Piszkozat mentése** lehetőséget: **megtekintheti a célközönséget**.

## <a name="next-steps"></a>Következő lépések

* [Előzetes verzió célközönségének hozzáadása](create-managed-service-offer-preview.md)
