---
author: craigshoemaker
ms.service: static-web-apps
ms.topic: include
ms.date: 03/25/2021
ms.author: cshoe
ms.openlocfilehash: 806a30214e0307b8fa101c0de51ed2f16622d433
ms.sourcegitcommit: 44edde1ae2ff6c157432eee85829e28740c6950d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2021
ms.locfileid: "105543557"
---
| Tulajdonság | Leírás | Példa | Kötelező |
|---|---|---|---|
| `app_location` | Az alkalmazás kódjának helye. | Adja meg `/` , hogy az alkalmazás forráskódja a tárház gyökerében található-e, vagy `/app` Ha az alkalmazás kódja egy nevű könyvtárban található `app` . | Yes |
| `api_location` | A Azure Functions kódjának helye. | Adja meg, `/api` hogy az alkalmazás kódja egy nevű mappában található-e `api` . Ha nem észleli Azure Functions alkalmazást a mappában, a Build nem sikerül, a munkafolyamat feltételezi, hogy nem szeretne API-t használni. | No |
| `output_location` | A Build kimeneti könyvtárának helye a következőhöz képest: `app_location` . | Ha az alkalmazás forráskódja a (z) helyen található `/app` , és a Build szkript a mappába helyezi a fájlt, `/app/build` akkor `build` `output_location` értékeként állítsa be. | Nem |