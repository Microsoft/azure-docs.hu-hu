---
title: A virtuális gépek ajánlati adatainak konfigurálása az Azure Marketplace-en
description: Ismerje meg, hogyan konfigurálhatja a virtuális gépek ajánlati adatait az Azure Marketplace-en.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: mingshen
ms.date: 10/19/2020
ms.openlocfilehash: 7030ffeb49c0a3919894f6dcf81e7252777267c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94629597"
---
# <a name="how-to-configure-virtual-machine-offer-listing-details"></a>A virtuális gépek ajánlati listájának konfigurálása – részletek

Az ajánlati **lista** lapon (válassza ki a partner Center bal oldali navigációs menüjéből) adja meg az ajánlat részleteit, például az ajánlat nevét, a leírását, a hivatkozásokat és a névjegyeket.

> [!NOTE]
> Az ajánlat tartalmait, például a leírást, a dokumentumokat, a képernyőképeket és a használati feltételeket nem kell angolul megadnia, feltéve, hogy az ajánlat leírása a következő kifejezéssel kezdődik: "Ez az alkalmazás csak a (z) verzióban érhető el \<non-English language> ." Megadhat egy webcímet is, amely egy olyan webhelyre mutató hivatkozást tartalmaz, amely nem az ajánlatban szereplő tartalomban használt tartalommal rendelkezik.

## <a name="marketplace-details"></a>Piactér – részletek

### <a name="name"></a>Name

Az itt megadott név jelenik meg az ügyfelek számára az ajánlati lista címeként. Ez a mező automatikusan ki van töltve az ajánlat- **alias** mezőben megadott névvel az ajánlat létrehozásakor. A név:

- A védjegyek és a szerzői jogi szimbólumok is lehetnek.
- 50 karakternél rövidebbnek kell lennie.
- Nem szerepelhetnek hangulatjelek.

### <a name="search-results-summary"></a>Keresési eredmények összegzése

Adja meg az ajánlat rövid leírását az Azure Marketplace keresési eredményeiben való megjelenítéshez. Akár 100 karaktert is tartalmazhat.

### <a name="short-description"></a>Rövid leírás

Adja meg az ajánlat az Azure piactér keresési eredményeiben való megjelenítésének további leírását. Akár 256 karaktert is tartalmazhat.

### <a name="description"></a>Leírás

[!INCLUDE [Long description-1](includes/long-description-1.md)]

[!INCLUDE [Long description-2](includes/long-description-2.md)]

HTML-címkék használatával formázhatja a leírást, így jobban megtarthatja a használatát. Az engedélyezett címkék listáját lásd: [támogatott HTML-címkék](supported-html-tags.md).

### <a name="privacy-policy-link"></a>Adatvédelmi szabályzat hivatkozása

Adja meg a szervezete adatvédelmi szabályzatának webcímet (URL-címét). Győződjön meg arról, hogy az ajánlat megfelel az adatvédelmi törvényeknek és előírásoknak. Érvényes adatvédelmi szabályzatot is közzé kell tennie a webhelyén.

## <a name="useful-links"></a>Hasznos hivatkozások

Adja meg az ajánlatával kapcsolatos kiegészítő online dokumentumokat. Hivatkozás hozzáadásához válassza a **hivatkozás hozzáadása** lehetőséget, és végezze el a következő mezőket:

- **Név**: az ügyfelek a részleteket tartalmazó oldalon fogják látni a nevet.
- **Hivatkozás**: adjon meg egy webcímet, amely lehetővé teszi az ügyfelek számára az online dokumentum megtekintését.

## <a name="customer-support-links"></a>Ügyfélszolgálati hivatkozások

Adja meg a támogatási webhelyet, ahol az ügyfelek elérheti a támogatási csapatot.

- Azure globális támogatási webhely
- Azure Government támogatási webhely

## <a name="partner-support-contact"></a>Partneri támogatási kapcsolattartó

Adja meg a Microsoft-partnerek **nevét**, **e-mail-címét** és **telefonszámát** , amelyet akkor kell használni, amikor az ügyfelek egy támogatási jegyet nyitnak meg. Ez az információ nem szerepel az Azure Marketplace-en.

## <a name="engineering-contact"></a>Mérnöki kapcsolattartó

Adja meg a Microsoft számára a **nevet**, **e-mailt** és **telefont** , ha az ajánlattal kapcsolatos problémák merülnek fel, beleértve a minősítéssel kapcsolatos problémákat is. Ez az információ nem szerepel az Azure Marketplace-en.

## <a name="cloud-solution-provider-program-contact"></a>Cloud Solution Provider programbeli kapcsolat

Adja meg a CSP-programot kezelő személy **nevét**, **e-mail-címét** és **telefonszámát** (ha van ilyen). Ez az információ nem szerepel az Azure Marketplace-en. A **CSP-program marketing anyagainál** adja meg azt a webcímet, ahol a programban résztvevő partnerek megtekinthetik a marketing-anyagokat.

## <a name="marketplace-media"></a>Piactéri adathordozó

Adja meg az ajánlathoz használni kívánt emblémákat és képeket. Minden képnek PNG formátumúnak kell lennie. A homályos képek miatt a rendszer elutasítja a kérelmet.

[!INCLUDE [logo tips](includes/graphics-suggestions.md)]

>[!NOTE]
>Ha probléma merül fel a fájlok feltöltésekor, győződjön meg arról, hogy a helyi hálózat nem blokkolja a https://upload.xboxlive.com partner központ által használt szolgáltatást.

### <a name="logos"></a>Logók

Adjon meg egy PNG-fájlt a **nagy** méretű emblémához. A partner Center ezt fogja használni egy **kis** és **közepes** embléma létrehozásához. Ezeket később is lecserélheti a különböző rendszerképekre.

- **Nagyméretű** (216 x 216 és 350 x 350 px, kötelező)
- **Közepes** (90 x 90 px, nem kötelező)
- **Kicsi** (48 x 48 px, opcionális)

Ezeket az emblémákat a lista különböző helyein használják:

[!INCLUDE [logos-azure-marketplace-only](includes/logos-azure-marketplace-only.md)]

[!INCLUDE [Logo tips](includes/graphics-suggestions.md)]

### <a name="screenshots"></a>Képernyőképek

Akár öt képernyőképet is felvehet, amelyek bemutatják, hogyan működik az ajánlata. Minden képernyőképnek 1280 x 720 képpont méretűnek és PNG formátumúnak kell lennie. Minden képernyőképnek tartalmaznia kell egy feliratot.

### <a name="videos"></a>Videók

Akár öt videót is hozzáadhat, amelyek bemutatják az ajánlatát. A videókat külső videó szolgáltatásban kell tárolni. Adja meg a videó nevét, webcímeit és miniatűr PNG-képét 1280 x 720 képpontban.

További információforrások a piactéren: ajánlott [eljárások a Piactéri ajánlatokhoz](gtm-offer-listing-best-practices.md).

A bal oldali navigációs menü következő lapjának folytatásához kattintson a **Piszkozat mentése** lehetőségre, és **tekintse meg a célközönséget**.

## <a name="next-steps"></a>Következő lépések

- [Előzetes verzió célközönségének létrehozása](azure-vm-create-preview.md)
