---
title: Az Azure-alkalmazás ajánlati tulajdonságainak konfigurálása
description: Megtudhatja, hogyan konfigurálhatja az Azure-alkalmazási ajánlat tulajdonságait a partner Centerben.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: 6f92c138a02c9dbdc1d22ca04c733cfbee69dcd0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94488519"
---
# <a name="how-to-configure-your-azure-application-offer-properties"></a>Az Azure-alkalmazás ajánlati tulajdonságainak konfigurálása

Ez a cikk azt ismerteti, hogyan konfigurálható egy Azure-beli alkalmazás-ajánlat tulajdonságai a kereskedelmi piactéren.

A **Tulajdonságok** lapon megadhatja az ajánlatra érvényes kategóriákat, valamint a jogi szerződéseket. Ügyeljen arra, hogy az Ön ajánlatának teljes és pontos részleteit ezen a lapon lássuk el, hogy az megfelelően jelenjen meg, és a megfelelő ügyfelek számára is elérhető legyen.

## <a name="select-a-category-for-your-offer"></a>Válasszon kategóriát az ajánlathoz

A **Kategóriák** területen válassza ki a **Kategóriák** hivatkozást, majd válasszon ki legalább egyet, és legfeljebb két kategóriát az ajánlat csoportosításához a megfelelő kereskedelmi Piactéri keresési területeken. Válasszon legfeljebb két alkategóriát az egyes elsődleges és másodlagos kategóriákhoz. Ha nem alkalmazható alkategória az ajánlatra, válassza a **nem alkalmazható** lehetőséget.

## <a name="provide-terms-and-conditions"></a>Feltételek és kikötések megadása

A **jogi** területen adja meg az ajánlat használati feltételeit. Erre két lehetősége van:

- [A standard szerződés használata opcionális módosításokkal](#use-the-standard-contract)
- [Saját használati feltételek használata](#use-your-own-terms-and-conditions)

A standard szerződéssel és a választható módosításokkal kapcsolatos további információkért lásd: [standard szintű szerződés a Microsoft kereskedelmi piactéren](standard-contract.md). Letöltheti a [normál szerződés](https://go.microsoft.com/fwlink/?linkid=2041178) PDF-fájlját (Győződjön meg róla, hogy az előugró ablakok blokkolása ki van kapcsolva).

### <a name="use-the-standard-contract"></a>A standard szintű szerződés használata

Az ügyfelek beszerzési folyamatának leegyszerűsítése és a szoftvergyártók jogi összetettségének csökkentése érdekében a Microsoft szabványos szerződést kínál, amely a kereskedelmi piactéren elérhető ajánlatokhoz használható. Ha a standard szintű szerződés keretében nyújtja a szoftverét, az ügyfeleknek csak egyszer kell elolvasniuk és elfogadniuk, és nem kell egyéni használati feltételeket létrehozniuk.

1. Jelölje be a **szabványos szerződés használata a Microsoft kereskedelmi piactérhez** jelölőnégyzetet.

   ![Bemutatja a Microsoft kereskedelmi Marketplace-re vonatkozó standard szerződés használatát jelölőnégyzetet.](partner-center-portal/media/use-standard-contract.png)

1. A **megerősítő** párbeszédpanelen válassza az **elfogadás** lehetőséget. Előfordulhat, hogy fel kell görgetni, hogy láthassa.
1. A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

> [!NOTE]
> Miután közzétett egy ajánlatot a kereskedelmi piactérre vonatkozó standard szerződéssel, nem használhatja a saját egyéni használati feltételeit. Akár a standard szerződés keretében, opcionális módosításokkal vagy a saját használati feltételeinek megfelelően kínálja a megoldását.

### <a name="add-amendments-to-the-standard-contract-optional"></a>Módosítások hozzáadása a standard szerződéshez (nem kötelező)

Kétféle módosítás érhető el: _univerzális_ és _Egyéni_.

#### <a name="add-universal-amendment-terms"></a>Univerzális módosítási feltételek hozzáadása

Az **univerzális módosítási feltételek a Microsoft kereskedelmi Marketplace-re vonatkozó szabványos szerződés** mezőjébe írja be az univerzális módosítási feltételeket. Ebben a mezőben korlátlan számú karaktert adhat meg. Ezek a feltételek a AppSource, az Azure Marketplace-en és/vagy Azure Portalban jelennek meg a felderítési és vásárlási folyamat során.

#### <a name="add-one-or-more-custom-amendments"></a>Adjon hozzá egy vagy több egyéni módosítást

1. A **Microsoft kereskedelmi Marketplace-re vonatkozó standard szerződésének egyéni módosítások** területén válassza az **Egyéni módosítási időszak hozzáadása (maximum 10)** hivatkozást.
1. Az **Egyéni módosítási feltételek** mezőben adja meg a módosítási feltételeket.
1. A **bérlő azonosítója** mezőben adja meg a bérlő azonosítóját. Az ajánlat beszerzési folyamatában csak az egyéni feltételekhez megadott bérlői azonosítókkal rendelkező ügyfelek látják majd az Azure Portal.

   > [!TIP]
   > A bérlői azonosító azonosítja az ügyfelet az Azure-ban. Megkérheti az ügyfelet erre az azonosítóra, és megkeresheti a [**https://portal.azure.com**](https://portal.azure.com)  >  **Azure Active Directory**  >  **Tulajdonságok** lehetőséggel. A címtár-azonosító értéke a bérlő azonosítója (például: `50c464d3-4930-494c-963c-1e951d15360e` ). A szervezet bérlői AZONOSÍTÓját is megkeresheti a saját tartománynév URL-címével, amely a [Microsoft Azure és az Office 365-bérlői azonosítóját használja?](https://www.whatismytenantid.com/).

1. A **Leírás** mezőben opcionálisan megadhatja a bérlő azonosítójának rövid leírását. Ez a leírás segít azonosítani a módosítással megcélzott ügyfelet.
1. Másik bérlői azonosító hozzáadásához jelölje be az **ügyfél bérlői azonosítójának hozzáadása** hivatkozást, és ismételje meg a 3. és a 4. lépést. Legfeljebb 20 bérlői azonosítót adhat hozzá.
1. Egy másik módosítási kifejezés hozzáadásához ismételje meg az 1 – 5. lépést. Ajánlatunk legfeljebb tíz egyéni módosítási kifejezést biztosíthat.
1. A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

### <a name="use-your-own-terms-and-conditions"></a>Saját használati feltételek használata

A normál szerződés helyett saját használati feltételeket is megadhat. Az ügyfeleknek el kell fogadniuk ezeket a feltételeket, mielőtt kipróbálhatják az ajánlatot.

1. A **jogi** területen győződjön meg arról, hogy a **Microsoft kereskedelmi Marketplace-re vonatkozó standard szerződés használata** jelölőnégyzet be van jelölve.
1. A **feltételek és kikötések** mezőben adja meg a legfeljebb 10 000 karakter hosszúságú szöveget.
1. A következő lapra való továbblépés előtt válassza a **Piszkozat mentése** lehetőséget: ajánlatok listája.

## <a name="next-steps"></a>Következő lépések

- [Az Azure-alkalmazások ajánlati listájának konfigurálása – részletek](create-new-azure-apps-offer-listing.md)
