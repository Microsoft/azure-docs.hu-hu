---
title: Azure Lab Services-ütemtervek létrehozása a csapatokon belül
description: Ismerje meg, hogyan hozhat létre labor Services-ütemterveket a csapatokon belül.
ms.topic: article
ms.date: 10/07/2020
ms.openlocfilehash: 202b202f99868875a51d13e95fbcac677246cc05
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92042337"
---
# <a name="create-and-manage-lab-services-schedules-within-teams"></a>Labor Services-ütemtervek létrehozása és kezelése a csapatokon belül

Az ütemtervek lehetővé teszik a tantermi laborok konfigurálását, hogy a laborban lévő virtuális gépek automatikusan elindulnak és leálljanak egy adott időpontban. Meghatározhat egy egyszeri vagy ismétlődő ütemtervet. Az alábbi eljárások egy osztályterem laborhoz tartozó ütemtervek létrehozását és kezelését ismertetik: 

A következő módon befolyásolja az ütemtervek a labor virtuális gépeket: 

- A sablon virtuális gépe nem szerepel az ütemtervekben. 
- Csak a hozzárendelt virtuális gépek vannak elindítva. Ez azt jelenti, hogy ha egy gépet nem igényel a végfelhasználó (tanuló), a gép nem indul el az ütemezett órákon. 
- Az összes virtuális gép (akár felhasználó által igényelt, akár nem) leáll a labor-ütemterv alapján. 

> [!IMPORTANT]
> A virtuális gépek ütemezett futási ideje nem számít bele a felhasználó számára kiosztott kvótába. A kvóta az ütemezett órákon kívüli időre szól, amelyet a tanuló a virtuális gépeken tölt. 

A felhasználók a laborok [webhelyén](https://labs.azure.com)ugyanúgy hozhatnak létre, szerkeszthetnek és törölhetnek Lab-ütemterveket. Tekintse meg az [ütemtervek létrehozásával és kezelésével](how-to-create-schedules-within-teams.md)foglalkozó cikket.

## <a name="automatic-shutdown-and-disconnect-settings"></a>Automatikus leállítási és leválasztási beállítások

Több automatikus leállítási költség-vezérlési funkciót is engedélyezhet, hogy proaktív módon meggátolja a további költségeket, ha a virtuális gépeket nem aktívan használják. A következő három automatikus leállítási és leválasztási funkció kombinációja a legtöbb esetben megfogást nyújt, amikor a felhasználók véletlenül elhagyják a virtuális gépeket:
 
- A felhasználók automatikus leválasztása a virtuális gépekről, amelyeket az operációs rendszer tétlennek tekint.
- A virtuális gépek automatikus leállítása a felhasználók leválasztásakor.
- Automatikusan leállítja az elindított virtuális gépeket, de a felhasználók nem csatlakoznak.

További részletekért tekintse meg a [laborok automatikus leállítási beállításainak konfigurálása](how-to-enable-shutdown-disconnect.md)című cikket.

## <a name="next-steps"></a>Következő lépések

Lásd az alábbi cikkeket:

- [Azure Lab Services használata a csapatokon belül – áttekintés](lab-services-within-teams-overview.md)
- [Első lépések és tesztkörnyezet létrehozása a csapatokon belül](how-to-get-started-create-lab-within-teams.md)
- [Tesztkörnyezet felhasználói listájainak kezelése a csapatokon belül](how-to-manage-user-lists-within-teams.md)
- [A labor virtuálisgép-készletének kezelése a csapatokon belül](how-to-manage-vm-pool-within-teams.md)
- [Hozzáférés a csapatokon belüli virtuális gépekhez – tanuló nézet](how-to-access-vm-for-students-within-teams.md)
