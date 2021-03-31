---
title: Azure Migrate-projektek hibaelhárítása
description: Segít a Azure Migrate projektek létrehozásával és kezelésével kapcsolatos problémák elhárításában.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: troubleshooting
ms.date: 01/01/2020
ms.openlocfilehash: f68a57d3780f388488d48835f322ff04ab7c7187
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96753382"
---
# <a name="troubleshoot-azure-migrate-projects"></a>Azure Migrate-projektek hibaelhárítása

Ez a cikk a [Azure Migrate](migrate-services-overview.md) projektek létrehozásakor és kezelésekor felmerülő problémák megoldásában nyújt segítséget.

## <a name="how-to-add-new-project"></a>Új projekt hozzáadása

Egy előfizetéshez több Azure Migrate projekt is tartozhat. [Megtudhatja, hogyan](./create-manage-projects.md) hozhat létre egy projektet első alkalommal, illetve hogyan [adhat hozzá további](create-manage-projects.md#create-additional-projects) projekteket.

## <a name="what-azure-permissions-are-needed"></a>Milyen Azure-engedélyekre van szükség?

Azure Migrate projekt létrehozásához közreműködői vagy tulajdonosi engedélyekkel kell rendelkeznie az előfizetésben.

## <a name="cant-find-a-project"></a>Nem található projekt

Egy meglévő Azure Migrate projekt megkeresése attól függ, hogy a Azure Migrate aktuális vagy régi verzióját használja-e. [Kövesse a következőt](create-manage-projects.md#find-a-project):.


## <a name="cant-find-a-geography"></a>Nem található földrajz

Azure Migrate projektet a [nyilvános](migrate-support-matrix.md#supported-geographies-public-cloud) és a [kormányzati felhők](migrate-support-matrix.md#supported-geographies-azure-government)számára támogatott földrajzi területeken hozhat létre.

## <a name="what-are-vm-limits"></a>Mik a virtuális gépek korlátai?

Egyetlen projektben akár 35 000 VMware virtuális gépet, akár 35 000 Hyper-V virtuális gépet is megvizsgálhat. A projektek tartalmazhatják a VMware virtuális gépeket és a Hyper-V virtuális gépeket is, az értékelési korlátokig.

## <a name="can-i-upgrade-old-project"></a>Frissíthetem a régi projektet?

Azure Migrate korábbi verziójának projektjei nem frissíthetők. [Létre kell hoznia egy új projektet](./create-manage-projects.md), és hozzá kell adnia az eszközöket.

## <a name="cant-create-a-project"></a>Nem hozható létre projekt

Ha egy projektet próbál létrehozni és üzembe helyezési hibát észlel:

- Próbálja meg újra létrehozni a projektet abban az esetben, ha átmeneti hiba történt. A **központi telepítések** területen kattintson az **újbóli üzembe helyezés** elemre az újbóli próbálkozáshoz.
- Győződjön meg arról, hogy közreműködői vagy tulajdonosi engedélyekkel rendelkezik az előfizetésben.
- Ha újonnan felvett földrajzba végez üzembe helyezést, várjon egy rövid ideig, és próbálkozzon újra.
- Ha a következő hibaüzenet jelenik meg: "a kérelmeknek felhasználói azonosítókat kell tartalmazniuk", ez azt jelezheti, hogy nincs hozzáférése a szervezet Azure Active Directory (Azure AD) bérlőhöz. Ebben az esetben:
    - Amikor első alkalommal ad hozzá egy Azure AD-bérlőhöz, e-mailben meghívót kap a bérlőhöz való csatlakozáshoz.
    - Fogadja el a bérlőhöz hozzáadandó meghívót.
    - Ha nem látja az e-mailt, lépjen kapcsolatba a felhasználóval a bérlőhöz, és kérje meg őket, hogy küldje el újra [a meghívót](../active-directory/external-identities/add-users-administrator.md#resend-invitations-to-guest-users) .
    - A meghívót tartalmazó e-mail fogadása után nyissa meg a meghívót, és válassza ki a hivatkozást a meghívás elfogadásához. Ezután jelentkezzen ki a Azure Portalból, és jelentkezzen be újra. (a böngésző frissítése nem fog működni.) Ezután megkezdheti az áttelepítési projekt létrehozását.

## <a name="how-do-i-delete-a-project"></a>Projekt törlése Hogyan

A projektek törléséhez [kövesse az alábbi utasításokat](create-manage-projects.md#delete-a-project) . Vegye figyelembe, hogy amikor töröl egy projektet, a projektben és a felderített gépek metaadatait is törli a projektből.

## <a name="added-tools-dont-show"></a>A hozzáadott eszközök nem jelennek meg

Győződjön meg arról, hogy a megfelelő projekt van kiválasztva. A Azure Migrate hub > **kiszolgálók** vagy **adatbázisok** ablakban kattintson a **módosítás** gombra a **projekt migrálása (módosítás)** elemre a képernyő jobb felső sarkában. Válassza ki a megfelelő előfizetést és a projekt nevét > **az OK gombra**. A lapot frissíteni kell a kiválasztott projekt hozzáadott eszközeivel.

## <a name="next-steps"></a>Következő lépések

[Felmérési](how-to-assess.md) vagy [áttelepítési](how-to-migrate.md) eszközök hozzáadása Azure Migrate projektekhez.