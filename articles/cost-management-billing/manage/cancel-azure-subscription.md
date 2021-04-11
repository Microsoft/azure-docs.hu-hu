---
title: Az Azure-előfizetés lemondása
description: Ismerteti, hogyan mondható le az Azure-előfizetés, például az ingyenes próba előfizetése
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: banders
ms.openlocfilehash: b20134cbfb85c2dcaef1227e6390c8c6047ad5be
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106109390"
---
# <a name="cancel-your-azure-subscription"></a>Az Azure-előfizetés lemondása

Ha nincs már szüksége az Azure-előfizetésre, lemondhatja azt az Azure Portalon.

Bár nem kötelező, a Microsoft *javasolja*, hogy végezze el az alábbi műveleteket az előfizetés lemondása előtt:

* Készítsen az adatokról biztonsági másolatot. Ha például az Azure Storage-ban vagy SQL-ben tárol adatokat, töltsön le róluk egy másolatot. Ha virtuális géppel rendelkezik, helyben mentsen róla egy rendszerképet.
* Állítsa le a szolgáltatásokat. Lépjen a [felügyeleti portál erőforrások oldalára](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources), és **állítsa le** az összes aktuálisan futó virtuális gépet, alkalmazást, és egyéb szolgáltatást.
* Fontolja meg adatai migrálását. Lásd [az erőforrások új erőforráscsoportba vagy előfizetésbe való áthelyezését](../../azure-resource-manager/management/move-resource-group-and-subscription.md) ismertető témakört.
* Törölje az összes erőforrást és erőforráscsoportot.
* Ha bármilyen egyéni szerepkörrel rendelkezik, amely erre az előfizetésre hivatkozik az `AssignableScopes` alatt, akkor az előfizetés eltávolításához frissítenie kell az egyéni szerepköröket. Ha az előfizetés lemondása után próbál frissíteni egy egyéni szerepkört, lehetséges, hogy hibaüzenetet kap. További információkért tekintse meg az [Egyéni szerepkörökkel felmerülő problémák hibaelhárítása](../../role-based-access-control/troubleshooting.md#problems-with-custom-roles) és az [Egyéni Azure-szerepkörök](../../role-based-access-control/custom-roles.md) cikkeket.

Ha egy fizetett Azure-támogatási csomagot mond le, az előfizetési időszak hátralévő ideje továbbra is ki lesz számlázva. További információkért tekintse meg az [Azure támogatási csomagokat](https://azure.microsoft.com/support/plans/).

## <a name="who-can-cancel-a-subscription"></a>Ki mondhat le előfizetést?

Az alábbi táblázat ismerteti az előfizetés lemondásához szükséges engedélyeket.

|Előfizetés típusa     |Ki mondhatja le  |
|---------|---------|
|Az Azure webhelyen keresztül az Azure-ba való regisztrációkor létrehozott előfizetések. Erre példa az [ingyenes Azure-fiók](https://azure.microsoft.com/offers/ms-azr-0044p/), a [használatalapú fizetést használó fiók](https://azure.microsoft.com/offers/ms-azr-0003p/) és a [Visual Studio-előfizetés](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/). |  A fiókadminisztrátor és az előfizetés tulajdonosai  |
|[Microsoft Nagyvállalati Szerződés](https://azure.microsoft.com/pricing/enterprise-agreement/) és [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/)     |  A fióktulajdonos és az előfizetés tulajdonosai       |
|[Azure-csomag](https://azure.microsoft.com/offers/ms-azr-0017g/) és [Azure-csomag DevTesthez](https://azure.microsoft.com/offers/ms-azr-0148g/)     |  Az előfizetés tulajdonosai      |


## <a name="cancel-subscription-in-the-azure-portal"></a>Előfizetés lemondása az Azure Portalon

1. Válassza ki előfizetését az [Azure Portal Előfizetések oldalán](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Válassza ki a lemondani kívánt előfizetést.
1. Válassza az **Áttekintés** lehetőséget, majd az **Előfizetés lemondása** elemet.
    ![A Lemondás gombot megjelenítő képernyőkép](./media/cancel-azure-subscription/cancel_ibiza.png)
1. Kövesse az utasításokat a lemondás befejezéséhez.

> [!NOTE]
> A partnerek felfüggeszthetik vagy megszüntethetik az előfizetést az ügyfél kérésére, illetve fizetéselmaradás vagy csalás esetén. További információ: [Előfizetés felfüggesztése vagy lemondása](/partner-center/create-a-new-subscription#suspend-or-cancel-a-subscription).

## <a name="cancel-a-support-plan"></a>Támogatási terv lemondása

Ha az Azure-webhelyen vagy Azure Portalon keresztül vásárolt támogatási tervet, vagy Microsoft Ügyfélszerződés alapján rendelkezik eggyel, lemondhatja a támogatási tervet. Ha a támogatási csomagot Microsoft-képviselőn vagy -partneren keresztül vásárolta meg, segítségért vegye fel vele a kapcsolatot. 

1. Lépjen a **Költségkezelés + számlázás** menüponthoz az Azure Portalon.
1. A **Számlázás** területen válassza az **Ismétlődő díjak** elemet.
1. A támogatási csomag sor elemének jobb oldalán válassza a három pontot ( **...** ), majd válassza ki **Az automatikus megújítás kikapcsolása** lehetőséget.

## <a name="what-happens-after-subscription-cancellation"></a>Mi történik az előfizetés lemondása után?

A lemondás után a számlázás azonnal leáll. Akár 10 percet is igénybe vehet azonban, hogy a lemondás a Portalon is megjelenjen. Ha az előfizetést a számlázási időszak közepén mondja le, az utolsó számlát az időszak lejárta után, a szokásos számlázási időpontban küldjük el.

A szolgáltatások a lemondás után le lesznek tiltva. Ez azt jelenti, hogy virtuális gépei felszabadultak, az ideiglenes IP-címek szabadok, a tárterület pedig csak olvasható.

Az előfizetés lemondása után a Microsoft 30–90 napot vár adatai végleges törlésével, hogy szükség esetén hozzájuk tudjon férni, vagy arra az esetre, ha meggondolná magát. Adatai tárolásáért nem számolunk fel díjat. További tudnivalókért lásd a [Microsoft Adatvédelmi központ az adatok kezelését](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409) ismertető cikkét.

## <a name="delete-free-trial-subscription"></a>Az ingyenes próbaverziós előfizetés törlése

Ha ingyenes próbaverziós előfizetéssel rendelkezik, nem kell 30 napot várnia az előfizetés automatikus törléséig. A lemondás után *három nappal* törölheti az előfizetését. Az **Előfizetés törlése** az előfizetés lemondása után három nappal válik elérhetővé.

1. Várjon három napot az előfizetés megszakításának időpontjától számítva.
1. Válassza ki az előfizetését az Azure Portal [Előfizetések](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) oldalán.
1. Válassza ki a törölni kívánt előfizetést.
1. Válassza az **Áttekintés** lehetőséget, majd az **Előfizetés törlése** elemet.

## <a name="delete-other-subscriptions"></a>Egyéb előfizetések törlése

A manuálisan törölhető előfizetés csak az ingyenes próbaverziós előfizetés lehet. Az összes többi előfizetés-típust, beleértve az utólagos elszámolású előfizetéseket is, csak az [előfizetés megszakítási](#cancel-subscription-in-the-azure-portal) folyamata törli. Más szóval nem lehet közvetlenül törölni az előfizetést, kivéve, ha az ingyenes próbaverziós előfizetés. Az előfizetés lemondása után azonban létrehozhat egy [Azure-támogatási kérelmet](https://go.microsoft.com/fwlink/?linkid=2083458) , amely arra kéri, hogy azonnal törölje az előfizetést.

## <a name="reactivate-a-subscription"></a>Előfizetés újraaktiválása

Ha véletlenül megszakítja az előfizetését az utólagos elszámolású díjszabással, akkor [újraaktiválhatja azt a Azure Portal](subscription-disabled.md).

Ha előfizetése nem használatalapú fizetéses előfizetés, akkor a lemondás utáni 90 napon belül újraaktiválhatja előfizetését, ha felveszi a kapcsolatot az ügyfélszolgálattal.

## <a name="why-dont-i-see-the-cancel-subscription-option-on-the-azure-portal"></a>Miért nem látom az Előfizetés lemondása lehetőséget az Azure Portalon? 

Előfordulhat, hogy nem rendelkezik az előfizetés lemondásához szükséges engedélyekkel. Tekintse meg a [Ki mondhat le előfizetést?](#who-can-cancel-a-subscription) szakaszt, amelyből megtudhatja, hogy ki mondhatja le a különböző típusú előfizetéseket.

## <a name="how-do-i-delete-my-azure-account"></a>Hogyan törölhetem az Azure-fiókomat?

*El kell távolítani a fiókomat, beleértve az összes személyes adatot. Már megszakítottam az aktív (ingyenes próbaverziós) előfizetéseket. Nincs aktív előfizetésem, és szeretném teljesen törölni a fiókomat*.

* Ha a szervezete biztosítja az Azure Active Directory-fiókot az Ön számára, akkor az Azure AD-rendszergazda tudja törölni a fiókot. Ezt követően a szolgáltatások le lesznek tiltva. Ez azt jelenti, hogy virtuális gépei felszabadultak, az ideiglenes IP-címek szabadok, a tárterület pedig csak olvasható. Összefoglalva: a lemondás után a számlázás azonnal leáll.

* Ha nem a szervezete biztosítja az Azure AD-fiókot az Ön számára, lemondhatja és törölheti az Azure-előfizetéseket, majd eltávolíthatja a hitelkártyáját a fiókból. A művelet nem törli a fiókot, azonban működésképtelenné teszi. Egy lépéssel tovább is mehet, és törölheti a társított Microsoft-fiókot is, ha azt nem használja semmilyen más célra.

## <a name="how-do-i-cancel-a-visual-studio-professional-account"></a>Hogyan mondható le egy Visual Studio Professional-fiók?

Tekintse meg a [megújításról és lemondásról](/visualstudio/subscriptions/faq/admin/renewal-cancellation) szóló cikket. Ha rendelkezik bármilyen Visual Studio Azure-előfizetéssel, ezeket is le kell mondania, illetve törölnie kell.

## <a name="next-steps"></a>További lépések

- Ha szükséges, újraaktiválhatja az utólagos elszámolású előfizetést a [Azure Portal](subscription-disabled.md).