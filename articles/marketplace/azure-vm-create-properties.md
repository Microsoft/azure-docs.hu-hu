---
title: Virtuálisgép-ajánlat tulajdonságainak konfigurálása az Azure Marketplace-en
description: Ismerje meg, hogyan konfigurálhatja a virtuális gépek ajánlatának tulajdonságait az Azure Marketplace-en.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: mingshen
ms.date: 10/19/2020
ms.openlocfilehash: 281553782774c31ec8cfaf614542fd739c4d3dd9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94629529"
---
# <a name="how-to-configure-virtual-machine-offer-properties"></a>A virtuális gépek ajánlati tulajdonságainak konfigurálása

A **Tulajdonságok** lapon (válassza ki a partner Center bal oldali navigációs menüjéből) adja meg a virtuális gép (VM) ajánlatának csoportosításához használt kategóriákat az Azure Marketplace-en, az alkalmazás verzióját, valamint az ajánlatot támogató jogi szerződéseket.

## <a name="select-a-category"></a>Kategória kiválasztása

Válassza ki a kategóriákat és alkategóriákat, hogy az ajánlatot a megfelelő Azure Marketplace-beli keresési területeken helyezze el. Ügyeljen arra, hogy az ajánlat leírásában később ismertesse, hogy az ajánlat hogyan támogatja ezeket a kategóriákat.

- Válasszon ki egy elsődleges kategóriát.
- Második választható kategória (másodlagos) hozzáadásához válassza a **+ Kategóriák** hivatkozást.
- Válasszon legfeljebb két alkategóriát az elsődleges és/vagy másodlagos kategóriához. Ha nem alkalmazható alkategória az ajánlatra, válassza a **nem alkalmazható** lehetőséget. Egy második alkategória kiválasztásához használja a CTRL billentyűt és kattintson rá.

Tekintse meg a kategóriák és alkategóriák teljes listáját az [ajánlatban az ajánlott eljárásokat felsorolva](gtm-offer-listing-best-practices.md). A virtuális gép az Azure Marketplace-en mindig a **számítási** kategóriában jelenik meg.

## <a name="provide-terms-and-conditions"></a>Feltételek és kikötések megadása

A **jogi** területen adja meg az ajánlat használati feltételeit. Erre két lehetősége van:

- [A standard szerződés használata opcionális módosításokkal](#use-the-standard-contract)
- [Saját használati feltételek használata](#use-your-own-terms-and-conditions)

A standard szerződéssel és a választható módosításokkal kapcsolatos további információkért lásd: [standard szintű szerződés a Microsoft kereskedelmi piactéren](standard-contract.md). Letöltheti a [normál szerződés](https://go.microsoft.com/fwlink/?linkid=2041178) PDF-fájlját (Győződjön meg róla, hogy az előugró ablakok blokkolása ki van kapcsolva).

### <a name="use-the-standard-contract"></a>A standard szintű szerződés használata

Az ügyfelek beszerzési folyamatának leegyszerűsítése és a szoftvergyártók jogi összetettségének csökkentése érdekében a Microsoft szabványos szerződést kínál, amely a kereskedelmi piactéren elérhető ajánlatokhoz használható. Ha a standard szintű szerződés keretében nyújtja a szoftverét, az ügyfeleknek csak egyszer kell elolvasniuk és elfogadniuk, és nem kell egyéni használati feltételeket létrehozniuk.

1. Jelölje be a **szabványos szerződés használata a Microsoft kereskedelmi piactérhez** jelölőnégyzetet.

   ![Bemutatja a Microsoft kereskedelmi Marketplace-re vonatkozó standard szerződés használatát jelölőnégyzetet.](partner-center-portal/media/use-standard-contract.png)

1. A **megerősítő** párbeszédpanelen válassza az **elfogadás** lehetőséget. A képernyő méretétől függően előfordulhat, hogy fel kell görgetni, hogy megjelenjen.
1. A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

   > [!NOTE]
   > Miután közzétett egy ajánlatot a kereskedelmi piactérre vonatkozó standard szerződéssel, nem használhatja a saját egyéni használati feltételeit. Akár a standard szerződés keretében, opcionális módosításokkal vagy a saját használati feltételeinek megfelelően kínálja a megoldását.

#### <a name="add-amendments-to-the-standard-contract-optional"></a>Módosítások hozzáadása a standard szerződéshez (nem kötelező)

Kétféle módosítás érhető el: *univerzális* és *Egyéni*.

##### <a name="add-universal-amendment-terms"></a>Univerzális módosítási feltételek hozzáadása

Az **univerzális módosítási feltételek a Microsoft kereskedelmi Marketplace-re vonatkozó szabványos szerződés** mezőjébe írja be az univerzális módosítási feltételeket. Ebben a mezőben korlátlan számú karaktert adhat meg. Ezek a feltételek a AppSource, az Azure Marketplace-en és/vagy Azure Portalban jelennek meg a felderítési és vásárlási folyamat során.

##### <a name="add-one-or-more-custom-amendments"></a>Adjon hozzá egy vagy több egyéni módosítást

1. A **Microsoft kereskedelmi Marketplace-re vonatkozó standard szerződésének egyéni módosítások** területén válassza az **Egyéni módosítási időszak hozzáadása (maximum 10)** hivatkozást.
2. Adja meg az **Egyéni módosítási feltételeket** a mezőben.
3. Adja meg a **bérlő azonosítóját** a mezőben. Az ajánlat beszerzési folyamatában csak az egyéni feltételekhez megadott bérlői azonosítókkal rendelkező ügyfelek látják majd az Azure Portal.

   > [!TIP]
   > A bérlői azonosító azonosítja az ügyfelet az Azure-ban. Megkérheti az ügyfelet erre az azonosítóra, és megkeresheti a [**https://portal.azure.com**](https://portal.azure.com)  >  **Azure Active Directory**  >  **Tulajdonságok** lehetőséggel. A címtár-azonosító értéke a bérlő azonosítója (például: `50c464d3-4930-494c-963c-1e951d15360e` ). A szervezet bérlői AZONOSÍTÓját is megkeresheti a saját tartománynév URL-címével, amely a [Microsoft Azure és az Office 365-bérlői azonosítóját használja?](https://www.whatismytenantid.com/).

4. Szükség esetén a bérlői azonosító rövid **leírását** is megadhatja. Ez a leírás segít azonosítani a módosítással megcélzott ügyfelet.
5. Egy másik bérlői azonosító hozzáadásához jelölje be az **ügyfél hozzáadása a bérlői azonosítóhoz (max. 10)** hivatkozást, és ismételje meg a 3. és 4. lépést. Legfeljebb 20 bérlői azonosítót adhat hozzá.
6. Egy másik módosítási kifejezés hozzáadásához ismételje meg az 1 – 5. lépést. Ajánlatunk legfeljebb tíz egyéni módosítási kifejezést biztosíthat.
7. A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

### <a name="use-your-own-terms-and-conditions"></a>Saját használati feltételek használata

A normál szerződés helyett saját használati feltételeket is megadhat. Az ügyfeleknek el kell fogadniuk ezeket a feltételeket, mielőtt kipróbálhatják az ajánlatot.

1. A **jogi** területen szüntesse **meg a szabványos szerződés használata a Microsoft kereskedelmi piactérhez** jelölőnégyzet jelölését.
1. A **feltételek és kikötések** mezőben adja meg a legfeljebb 10 000 karakter hosszúságú szöveget.

   > [!NOTE]
   > Ha további leírásra van szüksége, adjon meg egy webcímet, amely arra a pontra mutat, ahol a feltételek és kikötések találhatók. Az ügyfelek aktív hivatkozásként jelennek meg.

1. A bal oldali navigációs menü következő lapján a Tovább gombra kattintva megadhatja a **Piszkozat mentése** **lehetőséget.**

## <a name="next-steps"></a>Következő lépések

- [Virtuálisgép-ajánlatlista konfigurálása](azure-vm-create-listing.md)
