---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 04/20/2020
ms.author: cephalin
ms.openlocfilehash: c3fa57dd162fbbfbf0d46f73bffc78f279ef2968
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "83649129"
---
* **Felhasználói szintű hitelesítő adatok**: a teljes Azure-fiókhoz tartozó hitelesítő adatok egy halmaza. Felhasználható arra, hogy a App Service bármely olyan alkalmazáshoz, amely az Azure-fiókhoz való hozzáféréshez szükséges. Ez az alapértelmezett beállítás, amely a portál grafikus felhasználói felületén található (például az alkalmazás [erőforrás-oldalának](../articles/azure-resource-manager/management/manage-resources-portal.md#manage-resources) **áttekintése** és **tulajdonságai** ). Ha a felhasználó Role-Based Access Control (RBAC) vagy a rendszergazdai engedélyeken keresztül kap hozzáférést az alkalmazáshoz, a felhasználó a hozzáférés visszavonása előtt saját felhasználói szintű hitelesítő adatait használhatja. Ne ossza meg ezeket a hitelesítő adatokat más Azure-felhasználókkal.

* **Alkalmazás szintű hitelesítő adatok**: az egyes alkalmazásokhoz tartozó hitelesítő adatok egy halmaza. Ezt csak az alkalmazásra lehet használni. Az egyes alkalmazások hitelesítő adatai automatikusan létrejönnek az alkalmazás létrehozásakor. Nem konfigurálhatók manuálisan, de bármikor visszaállíthatók. Ahhoz, hogy a felhasználók hozzáférést kapjanak az alkalmazás szintű hitelesítő adatokhoz a (RBAC) használatával, a felhasználónak közreműködőnek vagy magasabbnak kell lennie az alkalmazásban (beleértve a webhely közreműködői beépített szerepkörét). Az olvasók számára nem engedélyezett a közzététel, és nem férhetnek hozzá ezekhez a hitelesítő adatokhoz.