---
title: Az Azure-foglalások bérlők közötti átvitelének megoldása
description: Ebből a cikkből megtudhatja, hogy a foglalás tulajdonosai hogyan továbbítanak egy foglalási rendelést egy Azure Active Directory bérlőtől egy másikba.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 02/24/2021
ms.openlocfilehash: 79473d57cc7504e7e6ef4ef68ba0cee74203f62b
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102055835"
---
# <a name="troubleshoot-reservation-transfers-between-tenants"></a>A bérlők közötti foglalás-átvitel hibáinak megoldása

Ebből a cikkből megtudhatja, hogy a foglalás tulajdonosai hogyan továbbítanak egy foglalási rendelést egy Azure Active Directory bérlőtől egy másikba. A foglalási sorrend címtárának módosításakor a lefoglalási rendeléshez és a függő foglalásokhoz is eltávolítja az Azure-RBAC elérését. Csak a módosítás után lesz hozzáférése. A címtár módosítása nem módosítja a foglalási rendelés számlázási tulajdonjogát. A címtár a szülő foglalási rendelés és a függő foglalások esetében módosul.

A bérlők közötti átvitelhez nem szükséges a foglalási Exchange és a lemondás.

A foglalás egy másik bérlőbe való átvitele után további tulajdonosokat is fel szeretne venni a foglalásba. További információ: [kik kezelhetik a foglalásokat alapértelmezés](view-reservations.md#who-can-manage-a-reservation-by-default)szerint.

Foglalási rendelés átadásakor a rendszer a rendelés alatt lévő összes foglalást továbbítja.

## <a name="transfer-a-reservation"></a>Foglalás átvitele

A következő lépések végrehajtásával átviheti a foglalási sorrendet, és egy másik bérlőtől függő fenntartásokat hajthat végre.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Ha Ön nem számlázási rendszergazda, de Ön a foglalás tulajdonosa, navigáljon a **foglalások** részhez, és ugorjon a 6. lépésre.
1. Lépjen a **Cost Management + Számlázás** lapra.
    - Ha Ön egy nagyvállalati rendszergazda, a bal oldali menüben válassza ki a **Számlázási hatókörök** elemet, majd a számlázási hatókörök listájában válasszon egyet.
    - Ha Ön a Microsoft ügyfél-szerződés számlázási profiljának tulajdonosa, a bal oldali menüben válassza a **Számlázási profilok** lehetőséget. A számlázási profilok listájában válasszon egyet.
1. A bal oldali menüben válassza a **foglalási tranzakciók** elemet. Megjelenik a foglalási tranzakciók listája.
1. A lap tetején található szalagcím beolvassa a *Számlázási rendszergazdák a foglalásokat kezelhetik. Kattintson ide a foglalások kezeléséhez.* Válassza ki a szalagcímet.
1. Megjelenik az EA-regisztrációhoz vagy a számlázási profilhoz tartozó foglalások teljes listája.
1. Válassza ki az átvinni kívánt foglalást.
1. A foglalás részletei között válassza ki a foglalási rendelés AZONOSÍTÓját.
1. A foglalási sorrendben válassza a **címtár módosítása** lehetőséget.
1. A Change Directory (címtár módosítása) ablaktáblán válassza ki azt az Azure AD-címtárat, amelyre át szeretné vinni a foglalást, majd válassza a **Confirm (megerősítés**) lehetőséget.

## <a name="next-steps"></a>Következő lépések

- További információ a fenntartott példányokról: [Mi az az Azure Reservations?](save-compute-costs-reservations.md).