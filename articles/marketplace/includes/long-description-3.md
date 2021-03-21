---
title: fájl belefoglalása
description: file
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: mingshen-ms
ms.author: mingshen
ms.date: 05/19/2020
ms.openlocfilehash: 5197b71db4f46d9908d5539c114d636b9474a870
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95758989"
---
HTML-címkék használatával formázhatja a leírást, így jobban megtarthatja a használatát. A legtöbb címkéhez nyitó és záró összetevő szükséges, ahogy az ebben a táblázatban is látható.

|   Cél  |  Használat  |  tetszik  |  A lekéréshez   |
| --- | --- | --- | --- |
|   Félkövér  |  `<b> & </b>`  |  `<b>`Példa `</b>` : ingyenes próbaverzió  |  **Példa**: ingyenes próbaverzió   |
|   Dőlt  |  `<i> & </i>`  |  Válassza ki az `<i>` első `</i>` sort.  |  Válassza ki az *első* sort.   |
|   Új sor  |  `<br>`  |  Ez az első sor. `<br>` Ez a második sor.  |  Ez az első sor.<br>Ez a második sor.  |
|  Bekezdések  |  `<p> & </p>`  |  `<p>`Ez az első bekezdés.`</p>`<br>`<p>`Ez a második bekezdés.`</p>`   |   <p>Ez az első bekezdés.</p><p>Ez a második bekezdés.</p>   |
|   Golyók  |  `<ul> & </ul>, <li> & </li>`  |  `<ul>`<br>`<li>`A felsorolásjel-eleme`</li>`<br>`<li>`A felsorolásjel-eleme`</li>`<br>`<li>`A felsorolásjel-eleme`</li>`<br>`</ul>`  |  • A felsorolásjel-eleme<br>• A felsorolásjel-eleme<br>• A felsorolásjel-eleme   |
|   Számozás  |  `<ol> & </ol>, <li> & </li>`  |  `<ol>`<br>`<li>`A számozott elemek`</li>`<br>`<li>`A számozott elemek`</li>`<br>`<li>`A számozott elemek`</li>`<br>`</ol>`   |   1. a számozott elemek<br>2. a számozott elemek<br>3. a számozott elemek   |
|   Új sor új szám nélkül (vagy listajel)  |  `<ol> & </ol>, <li> & </li>, <br>`  |  `<ol>`<br>`<li>`Ide írja be a szöveget`</li>`<br>`<li>`szöveg hozzáadása itt `<br>` szöveg hozzáadása`</li>`<br>`</ol>`  |  1. itt szöveg hozzáadása<br>2. itt szöveg hozzáadása<br>&nbsp;&nbsp;&nbsp;&nbsp;Ide írja be a szöveget   |
|   Új bekezdés új szám nélkül (vagy listajel)  |  `<ol> & </ol>, <li> & </li>, <br>`  |  `<ol>`<br>`<li>`Ide írja be a szöveget`</li>`<br>`<li>`szöveg hozzáadása itt `<br><br>` szöveg hozzáadása`</li>`<br>`</ol>`  |  1. itt szöveg hozzáadása<br>2. itt szöveg hozzáadása<p>&nbsp;&nbsp;&nbsp;&nbsp;Ide írja be a szöveget   |
|   Fejlécek használata  |  `<h1> & </h1>, <h2> & </h2>`, akár `<h6> & </h6>`  |  `<h1>`Ez az 1. címsor`</h1>`<br>`<h2>`Ez a 2. fejléc`</h2>`<br>`<h3>`Ez a 3. címsor`</h3>`  |  **<font size="+3">Ez az 1. címsor</font>**<br>**<font size="+2">Ez a 2. fejléc</font>**<br>**<font size="+1">Ez a 3. címsor</font>**  |
| | | |
