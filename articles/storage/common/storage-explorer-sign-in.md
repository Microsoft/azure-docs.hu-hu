---
title: Jelentkezzen be a Azure Storage Explorer | Microsoft Docs
description: Dokumentáció a Azure Storage Explorer
services: storage
author: MRayermannMSFT
ms.service: storage
ms.topic: article
ms.date: 04/01/2021
ms.author: chuye
ms.openlocfilehash: ef02842d189746a1801d97f91b92f249947c832d
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568699"
---
# <a name="sign-in-to-storage-explorer"></a>Jelentkezzen be a Storage Explorer

Az Azure Storage-erőforrások elérésének ajánlott módja a bejelentkezés a Storage Explorer. A bejelentkezéshez kihasználhatja az Azure AD-alapú engedélyeket, például az RBAC-t és a Gen2 POSIX ACL-eket. 

## <a name="how-to-sign-in"></a>A bejelentkezés menete

Ha be szeretne jelentkezni a Storage Explorer, nyissa meg a **Csatlakozás párbeszédpanelt.** A Csatlakozás **párbeszédpanelt a** bal oldali függőleges eszköztáron, vagy a Fiók panel **Fiók hozzáadása...** gombjára **kattintva nyithatja meg.**

Miután megnyitotta a párbeszédpanelt, válassza az Előfizetés lehetőséget az erőforrás típusaként, amelyhez csatlakozni szeretne, majd kattintson a **Tovább gombra.** 

Most ki kell választania, hogy melyik Azure-környezetbe szeretne bejelentkezni. Választhat bármelyik ismert környezetből, például az Azure-ból vagy az Azure China-ból, vagy hozzáadhatja saját környezetét. Miután kiválasztotta a környezetet, kattintson a **Tovább gombra.**

Ezen a ponton elindul az operációs rendszer **alapértelmezett webböngészője,** és megnyílik egy bejelentkezési oldal. A legjobb eredmény érdekében hagyja megnyitva ezt a böngészőablakot, amíg nem használ Storage Explorer vagy legalábbis addig, amíg az összes várt MFA-t el nem hajtotta. Miután végzett a bejelentkezést, visszatérhet a Storage Explorer.

## <a name="managing-accounts"></a>Fiókok kezelése

A Fiók panelen kezelheti és távolíthatja el azokat az Azure-fiókokat, amelyekbe **bejelentkezett.** A Fiók panel **megnyitásához** kattintson a Fiókok **kezelése** gombra a bal oldali függőleges eszköztáron.

A Fiók **panelen** láthatja az összes olyan fiókot, amelybe bejelentkezett. Az egyes fiókok alatt a következő lesz:
- A bérlők, amelyekhez a fiók tartozik
- Minden bérlőhöz azok az előfizetések, amelyekhez hozzáféréssel rendelkezik

Alapértelmezés szerint a Storage Explorer csak a saját bérlőjébe jelentkezik be. Ha egy másik bérlő előfizetését és erőforrásait szeretné megtekinteni, aktiválnia kell a bérlőt. A bérlő aktiválásához jelölje be a mellette melletti jelölőnégyzetet. Ha végzett a bérlővel való munka, a deaktiváláshoz törölje a jelölőnégyzet jelölését. Saját bérlője nem inaktiválható.

A bérlő aktiválása után szükség lehet a hitelesítő adatok újra megadására, mielőtt Storage Explorer betöltheti az előfizetéseket vagy hozzáférhet a bérlő erőforrásaihoz. A hitelesítő adatok újra megadását általában egy feltételes hozzáférési (CA) szabályzat, például a többtényezős hitelesítés (MFA) miatt kell megadni. És annak ellenére, hogy már végrehajtott MFA-t egy másik bérlőn, lehet, hogy újra el kell végeznie. A hitelesítő adatok újra megadásához egyszerűen kattintson a **Hitelesítő adatok újra megadása... lehetőségre.** A Hiba **részletei...** elemre kattintva azt is láthatja, hogy pontosan miért nem sikerült betölteni az előfizetéseket.

Miután betöltötte az előfizetéseket, a jelölőnégyzeteik be- és jelölésének be- és jelölését bejelölve választhatja ki, hogy mely előfizetéseket szeretné szűrni.

Ha a teljes Azure-fiókot el szeretné  távolítani, kattintson a fiók melletti Eltávolítás gombra.

## <a name="changing-where-sign-in-happens"></a>A bejelentkezés helyének módosítása

Alapértelmezés szerint a bejelentkezés az operációs rendszer alapértelmezett webböngészőjében **történik.** Az alapértelmezett webböngészővel történő bejelentkezés leegyszerűsíti a CA-szabályzatokkal, például az MFA-val védett erőforrásokhoz való hozzáférést. Ha valamilyen okból nem tud  bejelentkezni az operációs rendszer alapértelmezett webböngészőjébe, módosíthatja, hogy a Storage Explorer hogyan végzi a bejelentkezést.

A **Beállítások**  >    >  **alkalmazásba való bejelentkezés alatt** keresse meg a Bejelentkezés a **következővel beállítást.** Három beállítás érhető el:
- **Alapértelmezett webböngésző:** a bejelentkezés az operációs rendszer alapértelmezett **webböngészőjében történik.** Ez a lehetőség ajánlott.
- **Integrált bejelentkezés:** a bejelentkezés egy új Storage Explorer meg. Ez a lehetőség akkor lehet hasznos, ha egyszerre több Microsoft-fiókkal (MSA-val) próbál bejelentkezni. Ha ezt a lehetőséget választja, problémákat okozhat néhány CA-szabályzattal kapcsolatban.
- **Eszközkódfolyam:** Storage Explorer egy böngészőablakba beírni a kódot. Ez a lehetőség nem ajánlott. Az eszközkódfolyam nem kompatibilis számos CA-szabályzattal.

## <a name="troubleshooting-sign-in-issues"></a>Bejelentkezési problémák elhárítása

Ha nem tudja bejelentkezni, vagy problémákat okoz egy Azure-fiókkal a bejelentkezés után, tekintse meg a Storage Explorer hibaelhárítási útmutatójának bejelentkezési [szakaszát.](./storage-explorer-troubleshooting.md#sign-in-issues)
