---
title: Azure Lab Services a Microsoft Teams szolgáltatáson belül
description: Áttekintést nyújt Azure Lab Services használatáról a Microsoft Teams szolgáltatásban.
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: f39613427ed174dfca80d4a48be9473ab7025e79
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96433926"
---
# <a name="azure-lab-services-within-microsoft-teams"></a>Azure Lab Services a Microsoft Teams szolgáltatáson belül

A Azure Lab Services a Microsoft Teams szolgáltatásban **Azure Lab Services** Teams alkalmazással is kihasználható. A labor-fiók tulajdonosi/közreműködői/létrehozói hozzáféréssel rendelkező csapatának tulajdonosai létrehozhatnak laborokat, és virtuális gépeket hozhatnak létre a csapat minden tagja számára.

Ez a cikk a Azure Lab Services csapatokon belüli használatának előnyeit mutatja be, és más cikkekre mutató hivatkozásokat is tartalmaz, amelyekkel megtudhatja, hogyan hozhat létre és kezelhet laborokat a csapatokon belül. 

> [!NOTE]
>**Azure Lab Services** A Teams alkalmazás csak csapathoz vehető fel, nem adható hozzá egyéni csevegésekhez vagy csoportos csevegésekhez.

## <a name="benefits"></a>Előnyök

Azure Lab Services a Microsoft Teams integrációja segít a pedagógusok számára a tantermi környezet kialakításában és a csapaton belüli virtuális labor-környezetekben (osztály): 

* Az oktatók úgy állíthatják be a laborokat, hogy a tanulók a csapaton belül is hozzáférjenek a virtuális gépekhez, anélkül, hogy elhagyják [Azure Lab Services](https://labs.azure.com)a csapatokat, és nem kell megkeresniük
* Egyszeri bejelentkezés (SSO) a csapatokból a Azure Lab Servicesba.
* A csapat és a labor tulajdonosai számára nem szükséges, hogy az osztályokat két különböző rendszeren is meg lehessen őrizni – a labor felhasználói listán automatikusan ki van töltve a csoport tagsága, és a szinkronizálást minden 24 órában automatikusan elvégzi a rendszer. 
* A sablon virtuális gép kezdeti közzétételét követően a labor kapacitása (azaz a laborban lévő virtuális gépek száma) automatikusan ki lesz igazítva a csoport tagságának felhasználói hozzáadásával/törlésével. 
* A csapat és a labor tulajdonosai csak a csapattal és diákokkal kapcsolatos laborokat fogják megtekinteni, és csak azokat a virtuális gépeket tekintik meg, amelyek az adott csapat számára lettek kiépítve. 
* A rendszer automatikusan regisztrálja a felhasználókat a laborban, és a rendszer automatikusan hozzárendeli a virtuális gépeket az első bejelentkezéshez a tesztkörnyezet közzétételekor. Az oktatóknak nem kell elküldeniük a meghívókat és a tanulókat nem kell külön regisztrálni a laborba.  

## <a name="next-steps"></a>Következő lépések

Lásd az alábbi cikkeket:

- [Első lépések és tesztkörnyezet létrehozása a csapatokon belül](how-to-get-started-create-lab-within-teams.md)
- [Tesztkörnyezet felhasználói listájainak kezelése a csapatokon belül](how-to-manage-user-lists-within-teams.md)
- [A labor virtuálisgép-készletének kezelése a csapatokon belül](how-to-manage-vm-pool-within-teams.md)
- [Labor-ütemtervek létrehozása és kezelése a csapatokon belül](how-to-create-schedules-within-teams.md)
- [Hozzáférés a csapatokon belüli virtuális gépekhez – tanuló nézet](how-to-access-vm-for-students-within-teams.md)
