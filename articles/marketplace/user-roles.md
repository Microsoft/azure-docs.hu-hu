---
title: Felhasználói szerepkörök és engedélyek kiosztása az Azure Marketplace-en
description: Megtudhatja, hogyan rendelhet hozzá szerepköröket és engedélyeket a felhasználók számára a partner Center kereskedelmi piactér programban.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: varsha-sarah
ms.author: vavargh
ms.custom: contperf-fy21q2
ms.date: 04/07/2021
ms.openlocfilehash: 5a395205662e6c9ab0bde0e227f045ca46cfed45
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107228265"
---
# <a name="assign-user-roles-and-permissions"></a>Felhasználói szerepkörök és engedélyek hozzárendelése

**Megfelelő szerepkörök**

- Tulajdonos
- Manager

A piactérhez vagy a fejlesztői fiókhoz kapcsolódó képességek eléréséhez legalább egyet hozzá kell rendelni a következő szerepkörökhöz. Az egyes szerepkörök meghatározott engedélyekkel rendelkeznek a fiókon belüli bizonyos funkciók végrehajtásához.

| Szerepkör | Leírás |
| ------------ | ------------- |
| Tulajdonos | Közzétevői fiók létrehozásakor a rendszer tulajdonosi szerepkört rendel hozzá a partnerhez.<br><br>Engedély részletei:<br><br>&#10004;&#160;&#160;a fiók teljes tulajdonjogát.<br>&#10004;&#160;&#160;felhasználói típus hozzáadása<br>&#10004;&#160;&#160;a kifizetési és adózási információkat is elvégezheti<br>&#10004;&#160;&#160;visszajelzési jelentések és összegzések megtekintése<br>&#10004;&#160;&#160;a közzétevő fiók regisztrálása bármely más fejlesztői programba |
| Manager | &#10004;&#160;&#160;a közzétevő fiók felhasználóinak, szerepköreinek és engedélyeinek kezelése<br>Bérlők &#10004;&#160;&#160;kezelése<br>Hozzáférési adó és kifizetési beállítások &#x2718;&#160;&#160; |
| Fejlesztő | &#10004;&#160;&#160;feltöltési csomag<br>Alkalmazások és bővítmények &#10004;&#160;&#160;elküldése<br>A telemetria részletes használati jelentéseinek megtekintése &#10004;&#160;&#160;<br>&#x2718;&#160;&#160;hozzáférés pénzügyi információkhoz vagy Fiókbeállítások |
| Üzleti közreműködő | &#10004;&#160;&#160;hozzáférési pénzügyi információk<br>Díjszabási adatok &#10004;&#160;&#160;beállítása<br>Új alkalmazások és bővítmények &#x2718;&#160;&#160;létrehozása vagy elküldése |
| Pénzügyi közreműködő | &#10004;&#160;&#160;a kifizetési jelentések megtekintése<br>&#10004;&#160;&#160;a kifizetések és az adózási profilok kezelése<br>Az alkalmazások és a beállítások módosítása &#x2718;&#160;&#160; |
| Marketingszakértő | &#10004;&#160;&#160;válasz a vásárlói felülvizsgálatokra<br>Nem pénzügyi jelentések &#10004;&#160;&#160;megtekintése<br>Az alkalmazások és a beállítások módosítása &#x2718;&#160;&#160; |
|||

> [!NOTE]
> A kereskedelmi piactér programban a globális rendszergazda, az üzleti közreműködő, a pénzügyi közreműködő és a marketinges szerepkörök nem használatosak. A szerepkörök felhasználóknak való kiosztása nincs hatással. Csak a kezelő és a fejlesztői szerepkörök adnak engedélyeket a felhasználóknak.

További információ a szerepkörök és engedélyek kezeléséről a partner Center más területein, például a Azure Active Directory (AD), a felhőalapú megoldás-szolgáltató (CSP), a Vezérlőpult-gyártó (CPV), a vendég felhasználók vagy a Microsoft Partner Network (MPN): [felhasználói szerepkörök és engedélyek társítása a partner Centerben](/partner-center/permissions-overview).

> [!NOTE]
> Az ezeken a vonalakon végzett felhasználói felügyelet, szerepkör-hozzárendelési tevékenységek a fiók kontextusában lesznek. Ha másik fiókot szeretne kezelni, tekintse meg az eladó közötti váltás szakaszát.
