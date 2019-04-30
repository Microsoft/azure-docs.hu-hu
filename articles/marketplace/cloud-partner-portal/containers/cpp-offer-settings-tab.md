---
title: Ajánlat beállításai az Azure-tárolók kép |} A Microsoft Docs
description: Egy Azure container az ajánlat beállításainak konfigurálása.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: pbutlerm
ms.openlocfilehash: 9a669ebad674f938d11c8e372e6f586ca3c67f03
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61472914"
---
# <a name="container-offer-settings-tab"></a>Tároló ajánlat beállítások lap

A **tárolók > Új ajánlat** oldal megnyílik az aktuális az a **ajánlat beállítások** fülre. 

![Az ajánlat identitás](./media/containers-offer-settings.png)

## <a name="offer-identity-settings"></a>Az ajánlat nastavení Identity

A **ajánlat identitás**, meg kell adnia a mezők kitöltése a következő táblázat ismerteti. Egy csillag (*) hozzáfűzi a mező nevét jelzi, hogy ez szükséges. 

|  **Mező**       |     **Leírás**                                                          |
|  ---------       |     ---------------                                                          |
| **Ajánlat azonosítója**       | Az ajánlat egyedi azonosítóját (a közzétevő profil) belül. Ez az azonosító a termék URL-címek és insights jelentésekben látható lesz. Maximális hossza legfeljebb 50 karakter hosszúságú, és a kisbetűs alfanumerikus karaktereket és kötőjeleket (-). (Az azonosító nem végződhet kötőjellel.) **Megjegyzés:** Ez a mező nem módosítható, miután ajánlat élesíti. <br> Például, ha a Contoso tesz közzé egy ajánlatot az ajánlat azonosítója **minta-tároló**, az Azure Marketplace-en URL-cím által megadott `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-container?tab=Overview`. |
| **Közzétevő azonosítója**     | A szervezet egyedi azonosítója az Azure piactéren. Az ajánlatokat társítható a közzétevő-azonosítót. Ez az érték nem lehet módosítani, az ajánlat mentése után. |
| **Name (Név)**          | Az ajánlat megjelenített neve. Ez a név jelenik meg az Azure piactéren, és a Cloud Partner portálra. Legfeljebb 50 karakterből állhat. A termék egy könnyen felismerhető névre márkanév használatát javasoljuk. A szervezet neve ne tartalmazza, kivéve, ha ezt hogyan a forgalmazás. Ha ez az ajánlat egyéb webhelyeken és kiadványok vannak marketing, győződjön meg arról, hogy a név pontosan azonos között az összes olyan. |
|  |  |

Válassza ki **mentése** az ajánlat beállításainak mentése.

## <a name="next-steps"></a>További lépések

Használja a [termékváltozatok](./cpp-skus-tab.md) lap segítségével konfigurálhatja a termékváltozatok az ajánlatban.
