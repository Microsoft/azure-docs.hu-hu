---
title: A Windows rendszerű virtuális asztali útmutató Azure Advisora – Azure
description: A Windows rendszerű virtuális asztalok Azure Advisor ajánlásainak feloldása.
author: Heidilohr
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 666f882dec6c00b9fe5421485afded1fbd57d961
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448304"
---
# <a name="how-to-resolve-azure-advisor-recommendations"></a>Azure Advisor javaslatok megoldása

Ez a cikk azt ismerteti, Hogyan oldhatók fel a Windows rendszerű virtuális asztali Azure Advisorban megjelenő ajánlások.

## <a name="no-validation-environment-enabled"></a>"Nincs engedélyezve az érvényesítési környezet"

>[!div class="mx-imgBorder"]
>![A Azure Advisor működési kiválósági oldalának képernyőképe. A "nincs engedélyezve az érvényesítési környezet" javaslat piros színnel van kiemelve.](media/no-validation-environment.png)

Ez az ajánlás az Operational Excellence alatt jelenik meg. A javaslatnak a következőhöz hasonló figyelmeztető üzenetet is tartalmaznia kell:

"Ebben az előfizetésben nincs engedélyezve az érvényesítési környezet. A gazdagépek létrehozásakor a Tulajdonságok lapon a **nem** lehetőséget választotta a "érvényesítési környezet" elemre. Az üzletmenet folytonosságának biztosítása érdekében a Windows rendszerű virtuális asztali szolgáltatások telepítése során győződjön meg arról, hogy van legalább egy olyan alkalmazáskészlete, amelynek ellenőrzési környezete a lehetséges problémák tesztelésére szolgál.

Ezt a figyelmeztető üzenetet elküldheti, ha engedélyez egy érvényesítési környezetet az egyik gazdagép-készletben.

Ellenőrzési környezet engedélyezése:

1. Lépjen a Azure Portal kezdőlapjára, és válassza ki a módosítani kívánt gazdagép-készletet.

2. Ezután válassza ki azt az alkalmazáskészletet, amelyet éles környezetből egy ellenőrzési környezetbe szeretne váltani.

3. A gazdagép-készletben válassza a bal oldali oszlopban a **Tulajdonságok** elemet. Ezután görgessen lefelé, amíg meg nem jelenik az "érvényesítési környezet". Válassza az **Igen**, majd az **alkalmaz** lehetőséget.

>[!div class="mx-imgBorder"]
>![A Tulajdonságok menü képernyőképe Az "érvényesítési környezet" vörös színnel van kiemelve, és az "igen" buborék van kiválasztva.](media/validation-yes.png)

Ezek a módosítások nem teszik azonnal elérhetővé a figyelmeztetést, de végül el kell tűnnie. Naponta kétszer Azure Advisor frissítéseket. Addig is manuálisan elhalaszthatja vagy elvetheti a javaslatot. Javasoljuk, hogy a javaslat saját maga is legyen. Így a Azure Advisor értesítheti, ha a beállítások változásakor problémákba kerül.

## <a name="not-enough-production-non-validation-environments-enabled"></a>"Nincs elég üzemi (nem érvényesítési) környezet engedélyezve"

Ez az ajánlás az Operational Excellence alatt jelenik meg.

Ehhez a javaslathoz a figyelmeztető üzenet a következő okok egyike miatt jelenik meg:

- Túl sok gazda készlet van az érvényesítési környezetben.
- Nem rendelkezik üzemi gazdagép-készletekkel.

Azt javasoljuk, hogy a felhasználók a gazdagép-készletek kevesebb mint felét érvényesítsék egy érvényesítési környezetben.

A figyelmeztetés feloldása:

1. Lépjen a Azure Portal kezdőlapjára.

2. Válassza ki azt a gazdagép-készletet, amelyet szeretne módosítani az érvényesítéstől az éles környezetbe.

3. A gazdagép medencéjében válassza a **Tulajdonságok** fület a képernyő jobb oldalán található oszlopban. Ezután görgessen lefelé, amíg meg nem jelenik az "érvényesítési környezet". Válassza a **nem**, majd az **alkalmaz** lehetőséget.

>[!div class="mx-imgBorder"]
>![A Tulajdonságok menü képernyőképe Az "érvényesítési környezet" vörös színnel van kiemelve, és a "No" buborék van kiválasztva.](media/validation-no.png)

Ezek a módosítások nem teszik azonnal elérhetővé a figyelmeztetést, de végül el kell tűnnie. Naponta kétszer Azure Advisor frissítéseket. Addig is manuálisan elhalaszthatja vagy elvetheti a javaslatot. Javasoljuk, hogy a javaslat saját maga is legyen. Így a Azure Advisor értesítheti, ha a beállítások változásakor problémákba kerül.

## <a name="not-enough-links-are-unblocked-to-successfully-implement-your-vm"></a>"Nincs elég hivatkozás a virtuális gép sikeres megvalósításához"

Ez az ajánlás az Operational Excellence alatt jelenik meg.

A virtuális gép (VM) megfelelő működésének biztosítása érdekében fel kell oldania a megadott URL-címek blokkolását. A listát a [biztonságos URL-címek listájában](safe-url-list.md)tekintheti meg. Ha az URL-címek nincsenek feloldják, akkor a virtuális gép nem fog megfelelően működni.

A javaslat megoldásához ellenőrizze, hogy feloldja-e az összes URL- [címet a biztonságos URL-címek listáján](safe-url-list.md). Az URL-címek blokkolásának feloldásához használhatja a szolgáltatás címkéjét vagy a teljes tartománynevet.

## <a name="next-steps"></a>Következő lépések

Ha részletesebb útmutatókra van szüksége a gyakori problémák megoldásával kapcsolatban, tekintse meg a [Hibaelhárítás áttekintése, visszajelzés és a Windows rendszerű virtuális asztal támogatása](troubleshoot-set-up-overview.md)című témakört.
