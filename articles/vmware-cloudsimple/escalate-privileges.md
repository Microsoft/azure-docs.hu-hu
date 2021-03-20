---
title: Azure VMware-megoldás CloudSimple szerint – CloudSimple jogosultságok kiemelése
description: Ismerteti, hogyan lehet megtekinteni a CloudSimple engedélyeket a rendszergazdai feladatok elvégzéséhez a saját Felhőbeli vCenter
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d481717a79856583d23f61107678d2ecd1af68d6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97895727"
---
# <a name="escalate-cloudsimple-privileges-to-perform-administrative-functions-in-private-cloud-vcenter"></a>CloudSimple-jogosultságok kiemelése rendszergazdai funkciók elvégzéséhez a saját Felhőbeli vCenter

A CloudSimple-jogosultságok megközelítése úgy lett kialakítva, hogy a vCenter-felhasználók számára a normál műveletek elvégzéséhez szükséges jogosultságokat adja. Bizonyos esetekben előfordulhat, hogy a felhasználók további jogosultságokat igényelnek egy adott feladat elvégzéséhez.  Egy vCenter SSO-felhasználó jogosultságait korlátozott időtartamra is kiterjesztheti.

A jogosultságok kiterjesztésének okai az alábbiak lehetnek:

* Az Identity sources konfigurálása
* Felhasználókezelés
* Elosztott port csoport törlése
* VCenter-megoldások (például biztonsági mentési alkalmazások) telepítése
* Szolgáltatásfiókok létrehozása

> [!WARNING]
> Az emelt szintű jogosultságokkal rendelkező államban végrehajtott műveletek hátrányosan befolyásolhatják a rendszereket, és a rendszerek elérhetetlenné válását okozhatják. Csak a szükséges műveleteket hajtsa végre a eszkalációs időszak alatt.

A CloudSimple-portálon a CloudOwner helyi felhasználó jogosultságait a vCenter SSO-ra kell [kibővíteni](escalate-private-cloud-privileges.md) .  A távoli felhasználó jogosultságát csak akkor lehet megemelni, ha további Identitáskezelés van konfigurálva a vCenter-on.  A jogosultságok eszkalációja magában foglalja a kiválasztott felhasználó hozzáadását a vSphere beépített rendszergazdák csoportjához.  Csak egy felhasználó rendelkezhet megadható jogosultságokkal.  Ha egy másik felhasználó jogosultságait kell megadnia, először az aktuális felhasználók jogosultságait kell kibővíteni.

A további személyazonossági forrásokból származó felhasználókat hozzá kell adni a CloudOwner csoport tagjaként.

> [!CAUTION]
> Az új felhasználókat csak a *Cloud-Owner-Group*, a *Cloud-Global-cluster-admin-Group*, a *Cloud-Global-Storage-admin-Group*, a *Cloud-Global-Network-admin-Group* vagy a *Cloud-Global-VM-admin-Group* szolgáltatáshoz kell hozzáadni.  A *rendszergazdák* csoportba felvett felhasználók automatikusan el lesznek távolítva.  A vSphere webes felhasználói felületén csak a szolgáltatási fiókokat kell felvenni a *rendszergazdák* csoportjába, és a szolgáltatásfiókok nem használhatók.

A eszkalációs időszak alatt a CloudSimple az automatizált figyelést használja a kapcsolódó riasztási értesítésekkel, hogy azonosítsa a környezet véletlen módosításait.
