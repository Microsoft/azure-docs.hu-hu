---
title: Integráció az Office 365 Outlookmal
description: A levelezést, névjegyeket és naptárakat kezelő feladatokat és munkafolyamatokat automatizálhatja az Office 365 Outlookban Azure Logic Apps használatával
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: article
ms.date: 11/13/2020
tags: connectors
ms.openlocfilehash: 87a9c8a38ede7008d658068329c8c72e7e3c4117
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101095468"
---
# <a name="manage-email-contacts-and-calendars-in-office-365-outlook-by-using-azure-logic-apps"></a>E-mailek, partneradatok és naptárak kezelése az Office 365 Outlookban az Azure Logic Apps használatával

A [Azure Logic apps](../logic-apps/logic-apps-overview.md) és az [Office 365 Outlook Connector](/connectors/office365connector/)használatával a logikai alkalmazások létrehozásával automatizált feladatokat és munkafolyamatokat hozhat létre, amelyek a munkahelyi vagy iskolai fiókját kezelik. Automatizálhatók például a következő feladatok:

* Az e-mailek lekérése, küldése és megválaszolása.
* Értekezletek ütemezhetnek a naptárban.
* Névjegyek hozzáadása és szerkesztése.

Bármely eseményindítóval elindíthatja a munkafolyamatot, például amikor új e-mail érkezik, amikor egy naptári elem frissül, vagy ha egy esemény egy különbségi szolgáltatásban (például Salesforce) történik. Használhatja az eseményindító eseményre válaszoló műveleteket, például e-mailek küldését vagy új naptári esemény létrehozását.

## <a name="prerequisites"></a>Előfeltételek

* Olyan Outlook-fiók, amelyben [munkahelyi vagy iskolai fiókkal](https://www.office.com/)jelentkezik be. Ha rendelkezik @outlook.com vagy @hotmail.com fiókkal, használja helyette az [Outlook.com-összekötőt](../connectors/connectors-create-api-outlook.md) . Ha egy másik felhasználói fiókkal, például egy szolgáltatásfiók-fiókkal szeretne csatlakozni az Outlookhoz, tekintse meg a [Kapcsolódás más fiókok használatával](#connect-using-other-accounts)című témakört.

* Azure-fiók és -előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Az a logikai alkalmazás, amelyhez el szeretné érni az Outlook-fiókját. Ha a munkafolyamatot Office 365 Outlook-triggerrel szeretné elindítani, [üres logikai alkalmazásra](../logic-apps/quickstart-create-first-logic-app-workflow.md)van szükség. Ha Office 365 Outlook-műveletet szeretne hozzáadni a munkafolyamathoz, a logikai alkalmazásnak már rendelkeznie kell egy triggerrel.

## <a name="add-a-trigger"></a>Eseményindító hozzáadása

Az [eseményindító](../logic-apps/logic-apps-overview.md#logic-app-concepts) egy olyan esemény, amely elindítja a munkafolyamatot a logikai alkalmazásban. Ez a példa a logikai alkalmazás egy "lekérdezés" eseményindítót használ, amely a megadott intervallum és gyakoriság alapján ellenőrzi az e-mail-fiókjában lévő frissített naptári eseményeket.

1. A [Azure Portalban](https://portal.azure.com)nyissa meg az üres logikai alkalmazást a Logic app Designerben.

1. A keresőmezőbe írja be `office 365 outlook` szűrőként a kifejezést. Ez a példa **egy közelgő esemény hamarosan történő megkezdését** választja ki.
   
   ![Válassza ki az triggert a logikai alkalmazás indításához](./media/connectors-create-api-office365-outlook/office365-trigger.png)

1. Ha nem rendelkezik aktív internetkapcsolattal az Outlook-fiókjával, a rendszer felszólítja, hogy jelentkezzen be, és hozza létre a kapcsolódást. Ha egy másik felhasználói fiókkal, például egy szolgáltatásfiók-fiókkal szeretne csatlakozni az Outlookhoz, tekintse meg a [Kapcsolódás más fiókok használatával](#connect-using-other-accounts)című témakört. Egyéb esetben adja meg az trigger tulajdonságaira vonatkozó adatokat.

   > [!NOTE]
   > A szolgáltatás nem jár le a visszavonás előtt, még akkor is, ha módosítja a bejelentkezési hitelesítő adatait. További információ: [konfigurálható jogkivonatok élettartama Azure Active Directoryban](../active-directory/develop/active-directory-configurable-token-lifetimes.md).

   Ez a példa kiválasztja azt a naptárat, amelyet a trigger ellenőriz, például:

   ![Az trigger tulajdonságainak konfigurálása](./media/connectors-create-api-office365-outlook/select-calendar.png)

1. A triggerben állítsa be a **gyakoriság** és az **intervallum** értékét. Más elérhető trigger-tulajdonságok (például az **időzóna**) hozzáadásához válassza ki ezeket a tulajdonságokat az **új paraméter hozzáadása** listából.

   Ha például azt szeretné, hogy a trigger 15 percenként ellenőrizze a naptárat, állítsa a **gyakoriságot** **percre**, és állítsa be az **intervallumot** a következőre: `15` . 

   ![Az trigger gyakoriságának és intervallumának megadása](./media/connectors-create-api-office365-outlook/calendar-settings.png)

1. A tervező eszköztárán válassza a **Mentés** lehetőséget.

Most adjon hozzá egy műveletet, amely az eseményindító elindítása után fut. Hozzáadhatja például a Twilio- **üzenetküldés** műveletet, amely szöveget küld, ha egy naptári esemény 15 percen belül elindul.

## <a name="add-an-action"></a>Művelet hozzáadása

A [művelet](../logic-apps/logic-apps-overview.md#logic-app-concepts) egy olyan művelet, amelyet a logikai alkalmazás munkafolyamata futtat. Ez a példa logikai alkalmazás létrehoz egy új névjegyet az Office 365 Outlookban. A névjegy létrehozásához használhatja a másik triggerből vagy műveletből származó kimenetet is. Tegyük fel például, hogy a logikai alkalmazás a Dynamics 365 triggert használja a **rekordok létrehozásakor**. Hozzáadhatja az Office 365 Outlook **Create contact** műveletet, és a Salesforce trigger kimeneteit használva létrehozhatja az új kapcsolattartót.

1. A [Azure Portalban](https://portal.azure.com)nyissa meg a logikai alkalmazást a Logic app Designerben.

1. Ha egy műveletet a munkafolyamat utolsó lépéseként szeretne hozzáadni, válassza az **új lépés** lehetőséget. 

   A lépések közötti művelet hozzáadásához vigye a mutatót a lépések közötti nyíl fölé. Válassza ki a **+** megjelenő pluszjelet (), majd válassza a **művelet hozzáadása** lehetőséget.

1. A keresőmezőbe írja be `office 365 outlook` szűrőként a kifejezést. Ez a példa kiválasztja a **kapcsolat létrehozása** elemet.

   ![Válassza ki a logikai alkalmazásban futtatandó műveletet](./media/connectors-create-api-office365-outlook/office365-actions.png) 

1. Ha nem rendelkezik aktív internetkapcsolattal az Outlook-fiókjával, a rendszer felszólítja, hogy jelentkezzen be, és hozza létre a kapcsolódást. Ha egy másik felhasználói fiókkal, például egy szolgáltatásfiók-fiókkal szeretne csatlakozni az Outlookhoz, tekintse meg a [Kapcsolódás más fiókok használatával](#connect-using-other-accounts)című témakört. Egyéb esetben adja meg a művelet tulajdonságainak adatait.

   > [!NOTE]
   > A szolgáltatás nem jár le a visszavonás előtt, még akkor is, ha módosítja a bejelentkezési hitelesítő adatait. További információ: [konfigurálható jogkivonatok élettartama Azure Active Directoryban](../active-directory/develop/active-directory-configurable-token-lifetimes.md).

   Ez a példa kiválasztja azt a Névjegytár mappát, ahol a művelet létrehozza az új kapcsolattartót, például:

   ![A művelet tulajdonságainak konfigurálása](./media/connectors-create-api-office365-outlook/select-contacts-folder.png)

   Ha más elérhető művelet-tulajdonságokat szeretne hozzáadni, válassza ki ezeket a tulajdonságokat az **új paraméter hozzáadása** listáról.

1. A tervező eszköztárán válassza a **Mentés** lehetőséget.

<a name="connect-using-other-accounts"></a>

## <a name="connect-using-other-accounts"></a>Kapcsolat más fiókok használatával

Ha olyan fiókkal próbál csatlakozni az Outlookhoz, amely az Azure-ba jelenleg be van jelentkezve, akkor előfordulhat, hogy [egyszeri bejelentkezéses (SSO)](../active-directory/manage-apps/what-is-single-sign-on.md) hibák jelentkeznek. Ez a probléma akkor fordul elő, ha egy fiókkal jelentkezik be a Azure Portalba, de egy másik fiókot használ a csatlakozás létrehozásához. A tervező elvárja, hogy a Azure Portalba bejelentkezett fiókot használja. A probléma megoldásához a következő lehetőségek közül választhat:

* Állítsa be a másik fiókot a logikai alkalmazás erőforráscsoport **közreműködői** szerepkörével.

  1. A logikai alkalmazás erőforráscsoport menüjében válassza a **hozzáférés-vezérlés (iam)** lehetőséget. Állítsa be a másik fiókot a **közreműködő** szerepkörrel. 
  
     További információ: Azure- [szerepkörök kiosztása a Azure Portal használatával](../role-based-access-control/role-assignments-portal.md).

  1. Miután beállította ezt a szerepkört, jelentkezzen be a Azure Portalba azzal a fiókkal, amely már közreműködői engedélyekkel rendelkezik. Mostantól használhatja ezt a fiókot az Outlookhoz való kapcsolódás létrehozásához.

* Állítsa be a másik fiókot úgy, hogy a munkahelyi vagy iskolai fiókja "Send as" engedélyekkel rendelkezik.

   Ha rendszergazdai jogosultságokkal rendelkezik, a szolgáltatásfiók postaládájában állítsa be a munkahelyi vagy iskolai fiókját a **Send as** vagy az **Send** as (küldés másként) engedélyekkel. További információkért lásd: [postaláda-engedélyek megadása egy másik felhasználó számára – rendszergazdai Súgó](/microsoft-365/admin/add-users/give-mailbox-permissions-to-another-user). Ezután a munkahelyi vagy iskolai fiókkal hozhatja létre a kapcsolatokat. Most olyan eseményindítók vagy műveletek esetén, ahol megadhatja a küldőt, használhatja a szolgáltatásfiók e-mail-címét.

   Például az **e-mailek küldése** művelet opcionális paraméterrel rendelkezik, **a (küldés másként)** lehetőséggel, amelyet hozzáadhat a művelethez, és használhatja a szolgáltatásfiók e-mail-címét feladóként. A paraméter hozzáadásához kövesse az alábbi lépéseket:

   1. Az **E-mail küldése** műveletben nyissa meg a **paraméter hozzáadása** listát, és válassza a **from (küldés másként)** paramétert.

   1. Ha a paraméter megjelenik a műveletben, adja meg a szolgáltatásfiók e-mail-címét.

## <a name="connector-reference"></a>Összekötő-referencia

Az összekötővel kapcsolatos technikai részleteket, például az eseményindítókat, a műveleteket és a korlátozásokat az összekötő hencegő fájlja ismerteti, lásd az [összekötő hivatkozási oldalát](/connectors/office365/). 

## <a name="next-steps"></a>Következő lépések

* További Logic Apps- [Összekötők](../connectors/apis-list.md) megismerése
