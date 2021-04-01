---
title: Bérlői azonosító, objektumazonosító és partneri társítás részleteinek megkeresése az Azure Marketplace-en
description: A bérlői azonosító, az objektumazonosító és a partneri társítás részleteinek megkeresése az Azure Marketplace-en található előfizetés-AZONOSÍTÓhoz.
ms.service: marketplace
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 10/09/2020
ms.openlocfilehash: 2b1ba0779649c4955987c7dae9802cefaba89b79
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97109341"
---
# <a name="find-tenant-id-object-id-and-partner-association-details"></a>Bérlői azonosító, objektumazonosító és partneri társítás részleteinek megkeresése

Ez a cikk azt ismerteti, hogyan lehet megkeresni a bérlő AZONOSÍTÓját, az objektumazonosító és a partneri társítás részleteit, valamint a megfelelő előfizetési azonosítókat.

Ha szeretné, hogy a rendszer képernyőképeket kapjon ezekről az elemekről Azure Cloud Shell a segítség hibakereséséhez, ugorjon le a [bérlő, az objektum és a partner-azonosító társításának kereséséhez a hibakereséshez](#find-ids-for-debugging).

>[!Note]
> Csak az előfizetés tulajdonosa jogosult ezeknek a lépéseknek a végrehajtására.

## <a name="find-tenant-id"></a>Bérlő AZONOSÍTÓjának megkeresése

1. Nyissa meg az [Azure Portal](https://ms.portal.azure.com/).
2. Válassza a **Azure Active Directory** lehetőséget.

    :::image type="content" source="media/tenant-and-object-id/icon-azure-ad.png" alt-text="A Azure Portal Azure Active Directory ikonja.":::

3. Válassza az **Áttekintés** lehetőséget. A bérlői AZONOSÍTÓnak az **alapszintű információ** területen kell megjelennie.

    :::image type="content" source="media/tenant-and-object-id/select-groups-1.png" alt-text="Válassza ki a csoportokat a Azure Portal.":::

## <a name="find-subscriptions-and-roles"></a>Előfizetések és szerepkörök keresése

1. Lépjen a Azure Portalra, és válassza a **Azure Active Directory** lehetőséget a fenti 1. és 2. lépésben feljegyzett módon.
2. Válassza az **Előfizetések** lehetőséget.

    :::image type="content" source="media/tenant-and-object-id/icon-azure-subscriptions-1.png" alt-text="Az előfizetések ikonja Azure Portalban.":::

3. Előfizetések és szerepkörök megtekintése.

    :::image type="content" source="media/tenant-and-object-id/subscriptions-screen-1.png" alt-text="A Azure Portal előfizetések képernyője.":::

## <a name="find-partner-id"></a>Partner AZONOSÍTÓjának megkeresése

1. Navigáljon az előfizetések lapra az előző szakaszban leírtak szerint.
2. Válasszon egy előfizetést.
3. A **számlázás** területen válassza a **partner adatai** elemet.

    :::image type="content" source="media/tenant-and-object-id/menu-partner-information.png" alt-text="Partneri információk a bal oldali navigációs menüben.":::

## <a name="find-user-object-id"></a>Felhasználó keresése (objektumazonosító)

1. Jelentkezzen be az [Office 365 felügyeleti portálra](https://portal.office.com/adminportal/home) a megfelelő rendszergazdai jogokkal rendelkező fiókkal a kívánt bérlőn.
2. A bal oldali menüben bontsa ki a **felügyeleti központok** szakaszt alul, majd válassza a Azure Active Directory lehetőséget a felügyeleti konzol megnyitásához egy új böngészőablakban.
3. Válassza a **Felhasználók** lehetőséget.
4. Tallózással keresse meg vagy keresse meg a kívánt felhasználót, majd válassza ki a fiók nevét a felhasználói fiók profiljára vonatkozó információk megtekintéséhez.
5. Az objektumazonosító a jobb oldali identitás szakaszban található.

    :::image type="content" source="media/tenant-and-object-id/azure-ad-admin-center.png" alt-text="Azure Active Directory felügyeleti központ.":::

6. A **szerepkör-hozzárendelések** kereséséhez válassza a bal oldali menüben a **hozzáférés-vezérlés (iam)** lehetőséget, majd a **szerepkör-hozzárendeléseket**.

    :::image type="content" source="https://docs.microsoft.com/azure/role-based-access-control/media/role-assignments-portal/role-assignments.png" alt-text="Szerepkör-hozzárendelések az Azure-erőforrásokhoz.":::

## <a name="find-ids-for-debugging"></a>Hibakeresési azonosítók keresése

Ez a szakasz azt ismerteti, hogyan keresheti meg a bérlő, az objektum és a partner-azonosító társítását hibakeresési célból.

1. Nyissa meg az [Azure Portal](https://ms.portal.azure.com/).
2. A jobb felső sarokban található PowerShell ikonra kattintva nyissa meg Azure Cloud Shell.

    :::image type="content" source="media/tenant-and-object-id/icon-azure-cloud-shell-1.png" alt-text="A képernyő jobb felső részén található PowerShell-ikon.":::

3. Válassza a **PowerShell** lehetőséget.

    :::image type="content" source="media/tenant-and-object-id/icon-powershell.png" alt-text="Válassza ki a PowerShell-hivatkozást.":::

4. Válassza ki az **előfizetés** mezőt, és válassza ki azt, amelyhez a partner kapcsolódik, majd **hozzon létre egy tárolót**. Ez egy egyszeri művelet; Ha már beállította a tárterületet, folytassa a következő lépéssel.

    :::image type="content" source="media/tenant-and-object-id/create-storage-window.png" alt-text="Válassza a tároló létrehozása gombot.":::

5. A tároló létrehozása (vagy már) a Azure Cloud Shell ablakot nyitja meg.

    :::image type="content" source="media/tenant-and-object-id/cloud-shell-window-1.png" alt-text="A Azure Cloud Shell ablak.":::

6. A partnerek társításával kapcsolatos részletekért telepítse a bővítményt a következő paranccsal:<br>`az extension add --name managementpartner`.<br>Azure Cloud Shell fogja megjegyezni, hogy a bővítmény már telepítve van-e:

    :::image type="content" source="media/tenant-and-object-id/cloud-shell-window-2.png" alt-text="A bővítményt megjelenítő Azure Cloud Shell ablak már telepítve van.":::

7. A következő parancs használatával keresse meg a partner adatait:<br>`az managementpartner show --partner-id xxxxxx`<br>Példa: `az managementpartner show --partner-id 4760962`.<br>Pattintsa a parancs eredményét a képernyőképre, amely a következőhöz hasonlóan fog kinézni:

    :::image type="content" source="media/tenant-and-object-id/partner-id-sample-screenshot.png" alt-text="Minta képernyő, amely az előző parancs eredményét jeleníti meg a jelentésrészek AZONOSÍTÓjának megtekintéséhez.":::

>[!NOTE]
>Ha több előfizetéshez is szükség van egy képernyőképre, ezzel a paranccsal válthat az előfizetések között:<br>`az account set --subscription "My Demos"`

## <a name="next-steps"></a>Következő lépések

- [Támogatott országok és régiók](sell-from-countries.md)