---
title: Közzétett Azure Certified-eszköz szerkesztése
description: Útmutató az eszköz adatainak szerkesztéséhez, miután hitelesítette és közzétette az eszközt az Azure Certified Device programon keresztül.
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: how-to
ms.date: 03/03/2021
ms.custom: template-how-to
ms.openlocfilehash: 14a29d438103f07dd35b3a3380b7cc094f215824
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304443"
---
# <a name="edit-your-published-device"></a>A közzétett eszköz szerkesztése

Előfordulhat, hogy az eszköz hitelesítése és közzététele az Azure Certified-eszköz katalógusában lehetséges, hogy frissítenie kell az eszköz adatait. Ennek oka lehet a terjesztők listájának frissítése, a vásárlási oldal URL-címeinek módosítása vagy a hardverkövetelmények frissítései (például az operációs rendszer verziója vagy egy új összetevő hozzáadása). Az Azure Certified Device Portal használatával egyszerűen frissítheti az eszköz adatait anélkül, hogy a katalógusból eltávolítsa a terméket.

## <a name="prerequisites"></a>Előfeltételek

- Be kell jelentkeznie, és rendelkeznie kell egy **jóváhagyott** projekttel az eszközéhez az [Azure Certified Device portálon](https://certify.azure.com). Ha nem rendelkezik hitelesített eszközzel, megtekintheti ezt az [oktatóanyagot](tutorial-01-creating-your-project.md) az első lépésekhez.

## <a name="editing-your-published-project"></a>A közzétett projekt szerkesztése

A projekt összegzése során figyelje meg, hogy a projekt csak olvasható módban van, mivel már át lett értékelve és el lett fogadva. A módosítások elvégzéséhez meg kell kérnie a projekt szerkesztését, és újra jóvá kell hagynia a frissítést az Azure minősítési csapatnak.

1. Kattintson a `Request Metadata Edit` lap tetején látható gombra.  

    ![Metaadat-frissítés kérése](./media/images/request-metadata-edit.png)

1. Fogadja el az értesítést az oldalon, hogy a szerkesztés után el kell küldenie a terméket a felülvizsgálathoz.
    > [!NOTE]
    > A Szerkesztés megerősítésével **nem** távolítja el az eszközt az Azure Certified-eszköz katalógusból, ha már közzé van téve. A termék korábbi verziója a katalógusban marad, amíg újra nem teszi közzé az eszközt.

1. A figyelmeztetés elfogadását követően szerkesztheti az eszköz adatait. Ügyeljen arra, hogy a `Comments for Reviewer` megváltoztatott elemek szakaszának megjegyzése ne legyen `Device Details` .

    ![Metaadatok szerkesztésének megjegyzése](./media/images/edit-notes.png)

1. A projekt összegzése lapon kattintson arra a lehetőségre, `Submit for review` hogy a módosításokat az Azure minősítési csapat újra jóváhagyja.
1. A módosítások felülvizsgálata és jóváhagyása után újra közzéteheti a katalógus módosításait a portálon keresztül (lásd az [oktatóanyagot](./tutorial-04-publishing-your-device.md)).

## <a name="next-steps"></a>Következő lépések

Sikeresen szerkesztette az eszközt az Azure Certified-eszköz katalógusában. A módosításokat megtekintheti a katalógusban, vagy egy másik eszközt is tanúsíthat!
- [Azure Certified-eszközök katalógusa](https://devicecatalog.azure.com/)
- [Ismerkedés az eszközök tanúsításával](./tutorial-01-creating-your-project.md)
