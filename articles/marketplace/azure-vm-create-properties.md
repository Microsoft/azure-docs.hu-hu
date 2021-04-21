---
title: Virtuálisgép-ajánlat tulajdonságainak konfigurálása a Azure Marketplace
description: Ismerje meg, hogyan konfigurálhatja a virtuális gépek ajánlattulajdonságokat a Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: mingshen
ms.date: 10/19/2020
ms.openlocfilehash: 5942368ba1709127b815a35676b716955c1bee8f
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819087"
---
# <a name="configure-virtual-machine-offer-properties"></a>Virtuálisgép-ajánlat tulajdonságainak konfigurálása

A Tulajdonságok **lapon** (a bal oldali navigációs menüből kiválasztva) meghatározhatja azokat a kategóriákat, amelyek a virtuálisgép-ajánlat (VM) Azure Marketplace-ben való csoportosításához, az alkalmazás verziójához és az ajánlatot támogató jogi szerződésekhez használhatók.

## <a name="select-a-category"></a>Kategória kiválasztása

Válassza ki a kategóriákat és alkategóriákat, hogy az ajánlatot a megfelelő keresési Azure Marketplace helyezze el. Mindenképpen írja le az ajánlat leírásának későbbi részében, hogy az ajánlat hogyan támogatja ezeket a kategóriákat.

- Válasszon ki egy Elsődleges kategóriát.
- Egy második választható kategória (másodlagos) hozzáadásához válassza a **+Kategóriák hivatkozást.**
- Legfeljebb két alkategóriát válasszon az Elsődleges és/vagy a Másodlagos kategóriához. Ha az ajánlatra nem vonatkozik alkategória, válassza a **Nem alkalmazható lehetőséget.** A Ctrl+kattintással válasszon ki egy második alkategóriát.

A kategóriák és alkategóriák teljes listáját az Ajánlatlistázással kapcsolatos ajánlott [eljárások rész tartalmazza.](gtm-offer-listing-best-practices.md) A virtuálisgép-ajánlatok mindig a Számítási **kategória alatt jelennek** meg a Azure Marketplace.

## <a name="provide-terms-and-conditions"></a>Használati feltételek és feltételek megszabad

A **Jogi alatt** adja meg az ajánlat feltételeit és feltételeit. Erre két lehetősége van:

- [A standard szerződés használata választható módosításokkal](#use-the-standard-contract)
- [Saját használati feltételek használata](#use-your-own-terms-and-conditions)

A standard szerződéssel és a választható módosításokkal kapcsolatos további információkért tekintse meg standard szerződés Microsoft kereskedelmi [piactérre vonatkozó cikkeket.](standard-contract.md) Letöltheti a [](https://go.microsoft.com/fwlink/?linkid=2041178) standard szerződés PDF-fájlt (győződjön meg arról, hogy az előugró ablak blokkoló ki van kapcsolva).

### <a name="use-the-standard-contract"></a>A standard szerződés használata

Az ügyfelek beszerzési folyamatának egyszerűsítése és a szoftverszállítók jogi összetettségének csökkentése érdekében a Microsoft egy szabványos szerződést kínál, amely a kereskedelmi piactéren elérhető ajánlatokkal használható. Ha a szoftverét a szokásos szerződés keretében kínálja, az ügyfeleknek csak egyszer kell elolvasni és elfogadniuk azt, és önnek nem kell egyéni feltételeket és feltételeket létrehoznia.

1. Jelölje be a Use the standard szerződés for Microsoft's commercial marketplace (Az alkalmazás **használata a Microsoft kereskedelmi piacterén)** jelölőnégyzetet.

   ![Bemutatja a Standard szerződés használata a Microsoft kereskedelmi piacterén jelölőnégyzetet.](partner-center-portal/media/use-standard-contract.png)

1. A Megerősítés **párbeszédpanelen** válassza az **Elfogadás lehetőséget.** A képernyő méretétől függően előfordulhat, hogy fel kell görgetnie, hogy lássa.
1. A **folytatás előtt válassza a Piszkozat** mentése lehetőséget.

   > [!NOTE]
   > Miután közzétett egy ajánlatot standard szerződés kereskedelmi piactérhez, nem használhatja a saját egyéni használati feltételeit. A megoldást vagy a standard szerződés keretében, opcionális módosításokkal, vagy a saját használati feltételei szerint kínálják fel.

#### <a name="add-amendments-to-the-standard-contract-optional"></a>Módosítások hozzáadása a standard szerződéshez (nem kötelező)

Kétféle módosítás érhető *el:* univerzális és *egyéni*.

##### <a name="add-universal-amendment-terms"></a>Általános módosítási feltételek hozzáadása

A **Microsoft kereskedelmi piacterének** standard szerződésének univerzális módosítási feltételei mezőben adja meg az univerzális módosítás feltételeit. Ebben a mezőben korlátlan számú karaktert beírhat. Ezek a feltételek az AppSource-ban, a Azure Marketplace és/vagy a Azure Portal a felderítési és vásárlási folyamat során jelennek meg.

##### <a name="add-one-or-more-custom-amendments"></a>Egy vagy több egyéni módosítás hozzáadása

1. A **Microsoft kereskedelmi** piacterének Egyéni kiegészítések feltételei standard szerződés alatt válassza az Egyéni módosítási időszak hozzáadása **(max. 10)** hivatkozást.
2. Írja be **az egyéni módosítás kifejezését** a mezőbe.
3. Írja be **a bérlőazonosítót** a mezőbe. Csak az ezen egyéni feltételekhez megadott bérlői adatokkal társított ügyfelek láthatják őket az ajánlat vásárlási folyamatában a Azure Portal.

   > [!TIP]
   > A bérlőazonosító azonosítja az ügyfelet az Azure-ban. Ezt az azonosítót az ügyféltől kérheti, és a Tulajdonságok gombra [**https://portal.azure.com**](https://portal.azure.com)  >  **Azure Active Directory**  >  **rá.** A címtár-azonosító értéke a bérlő azonosítója (például `50c464d3-4930-494c-963c-1e951d15360e` ). A Céges fiókom és az [Office 365-bérlőazonosító?](https://www.whatismytenantid.com/)cím alatt található tartománynév Microsoft Azure URL-címével is ki tudja keresni az ügyfél bérlőazonosítóját.

4. Ha szükséges, adjon meg egy rövid **Leírást** a bérlőazonosítóhoz. Ez a leírás segít azonosítani a módosítás céljaként érintett ügyfelet.
5. Egy másik bérlőazonosító hozzáadásához válassza **az Add a customer's tenant ID (Max 10) (Ügyfél bérlőazonosítójának hozzáadása (max. 10) hivatkozást,** és ismételje meg a 3. és a 4. lépést. Legfeljebb 20 bérlői adatbázist adhat hozzá.
6. Egy újabb módosítási kifejezés hozzáadásához ismételje meg az 1–5. lépést. Ajánlatonként legfeljebb tíz egyéni módosítási feltételt tud biztosítani.
7. A **folytatás előtt válassza a Piszkozat** mentése lehetőséget.

### <a name="use-your-own-terms-and-conditions"></a>Saját használati feltételek használata

A standard szerződés használata helyett saját használati feltételeket is meg lehet adni. Az ügyfeleknek el kell fogadniuk ezeket a feltételeket, mielőtt kipróbálhatják az ajánlatot.

1. A **Jogi osztály** alatt törölje a Standard szerződés használata a Microsoft kereskedelmi **piacterén** jelölőnégyzet jelölését.
1. A Használati **feltételek mezőbe** legfeljebb 10 000 karakterből állhat.

   > [!NOTE]
   > Ha hosszabb leírásra van szüksége, adjon meg egy webcímet, amely arra mutat, hogy hol találhatók a használati feltételek. Ez aktív hivatkozásként jelenik meg az ügyfelek számára.

1. Válassza **a Piszkozat** mentése lehetőséget, mielőtt továbblép a bal oldali navigációs menü **Ajánlatlista lapján található következő lapra.**

## <a name="next-steps"></a>Következő lépések

- [Virtuálisgép-ajánlatlista konfigurálása](azure-vm-create-listing.md)
