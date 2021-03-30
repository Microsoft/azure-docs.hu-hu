---
title: Azure Lab Services labor törlése a csapatokból
description: Megtudhatja, hogyan törölhet egy Azure Lab Services labort a csapatokból.
ms.topic: article
ms.date: 10/12/2020
ms.openlocfilehash: 8d1e20f8f676eb9863187b550a3c0400871d670c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96433954"
---
# <a name="delete-labs-within-teams"></a>A csapaton belüli laborok törlése

Ez a cikk bemutatja, hogyan törölhet egy labort a **Azure Lab Services** alkalmazásból.

## <a name="prerequisites"></a>Előfeltételek

* [Hozzon létre egy labor Services-fiókot](tutorial-setup-lab-account.md#create-a-lab-account) a Azure Portal.
* Első [lépések és labor Services-tesztkörnyezet létrehozása a csapatokon belül](how-to-get-started-create-lab-within-teams.md).

## <a name="delete-labs"></a>Tesztkörnyezetek törlése

A Teams-ben létrehozott laborokat a labor [Services webhelyén](https://labs.azure.com) törölheti, ha közvetlenül törli a labort a [Azure Lab Servicesban](how-to-manage-classroom-labs.md). 

A labor törlése a csapat törlésekor is aktiválódik. Ha az a csapat, amelyben a labor létrejött, törölve lesz, a labor automatikusan törlődik 24 órával az automatikus felhasználói lista szinkronizálásának elindítása után. 

> [!IMPORTANT]
> A lap törlése vagy az alkalmazás eltávolítása nem eredményezi a labor törlését. 

Ha a lap törölve van, akkor a csoport tagsága listán szereplő felhasználók továbbra is hozzáférhetnek a virtuális gépekhez a [labor Services webhelyén](https://labs.azure.com) , kivéve, ha a labor törlését explicit módon aktiválja a tesztkörnyezet törlésével vagy a csapat törlésével. 

## <a name="next-steps"></a>Következő lépések

- [Azure Lab Services használata a csapatokon belül – áttekintés](lab-services-within-teams-overview.md)
- [Tesztkörnyezet felhasználói listájainak kezelése a csapatokon belül](how-to-manage-user-lists-within-teams.md)
- [A labor virtuálisgép-készletének kezelése a csapatokon belül](how-to-manage-vm-pool-within-teams.md)
- [Labor-ütemtervek létrehozása és kezelése a csapatokon belül](how-to-create-schedules-within-teams.md)
- [Hozzáférés a csapatokon belüli virtuális gépekhez – tanuló nézet](how-to-access-vm-for-students-within-teams.md)

