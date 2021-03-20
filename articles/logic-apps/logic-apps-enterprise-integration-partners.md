---
title: Kereskedelmi partnerek hozzáadása a B2B-integrációhoz
description: Hozzon létre kereskedelmi partnereket az integrációs fiókjában a Azure Logic Apps-nal való használathoz
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 8e3805fae5bf6cc5ad8cf759d3ba75220c6ddbd8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91565071"
---
# <a name="add-trading-partners-to-integration-accounts-for-azure-logic-apps"></a>Kereskedelmi partnerek hozzáadása az Azure Logic Apps integrációs fiókjaihoz

[Azure Logic apps](../logic-apps/logic-apps-overview.md)a logikai alkalmazásokkal rendelkező [integrációs fiókkal](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) hozhat létre automatizált vállalatközi (B2B) integrációs munkafolyamatokat. A szervezet és mások képviseletéhez kereskedelmi partnereket hozhat létre és adhat hozzá az integrációs fiókjához. A partnerek olyan entitások, amelyek vállalatközi tranzakciókban vesznek részt, és üzeneteket váltanak egymással.

A partnerek létrehozása előtt mindenképpen beszéljen és ossza meg a partnerekkel kapcsolatos információkat a másik által küldött üzenetek azonosításával és ellenőrzésével. Miután elfogadja ezeket a részleteket, készen áll arra, hogy partnereket hozzon létre az integrációs fiókjában.

## <a name="partner-roles-in-integration-accounts"></a>Partneri szerepkörök az integrációs fiókokban

A partnerekkel cserélt üzenetek részleteinek meghatározásához hozzon létre és adjon hozzá [szerződéseket](../logic-apps/logic-apps-enterprise-integration-agreements.md) az integrációs fiókjához. A szerződések legalább két partnert igényelnek az integrációs fiókban. A szervezet mindig a *fogadó partner* a szerződésben. Az a szervezet, amely üzeneteket cserél a szervezete számára a *vendég partner*. A vendég partner lehet egy másik vállalat vagy akár egy részleg is a saját szervezetében. A partnerek hozzáadása után létrehozhat egy szerződést.

Egy megállapodásban megadhatja a fogadó partner szemszögéből érkező bejövő és kimenő üzenetek kezelésére vonatkozó adatokat. A bejövő üzenetek esetében a **fogadási beállítások** határozzák meg, hogy a gazda partner hogyan fogadja az üzeneteket a szerződésben szereplő vendég partnertől. A kimenő üzenetek esetében a **küldési beállítások** határozzák meg, hogy a gazda partner hogyan küldjön üzeneteket a vendég partnernek.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* [Integrációs fiók](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) partnerek, szerződések és egyéb B2B-összetevők tárolásához. Ezt az integrációs fiókot hozzá kell rendelni az Azure-előfizetéséhez.

## <a name="create-partner"></a>Partner létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Az Azure fő menüjében válassza a **minden szolgáltatás** lehetőséget. A keresőmezőbe írja be az "integráció" kifejezést, majd válassza az **integrációs fiókok** elemet.

   ![Integrációs fiókok kiválasztása](./media/logic-apps-enterprise-integration-partners/find-integration-accounts.png)

1. Az **integrációs fiókok** területen válassza ki azt az integrációs fiókot, amelyhez hozzá szeretné adni a partnereit.

   ![Integrációs fiók kiválasztása](./media/logic-apps-enterprise-integration-partners/select-integration-account.png)

1. Válassza ki a **partnerek** csempét.

   ![Képernyőkép, amely a partnerek csempét jeleníti meg.](./media/logic-apps-enterprise-integration-partners/choose-partners.png)

1. A **partnerek** területen válassza a **Hozzáadás** lehetőséget. A **Partner hozzáadása** területen adja meg a partner adatait az alábbi táblázatban leírtak szerint.

   ![Válassza a "Hozzáadás" lehetőséget, és adja meg a partner adatait](./media/logic-apps-enterprise-integration-partners/add-partners.png)

   | Tulajdonság | Kötelező | Leírás |
   |----------|----------|-------------|
   | **Név** | Yes | A partner neve |
   | **Selejtező** | Yes | Az a hitelesítő szervezet, amely egyedi üzleti identitásokat biztosít a szervezeteknek, például **D-U-N-S (Dun & bradstreettől)**. <p>A partnerek kölcsönösen meghatározott üzleti identitást választhatnak. Ezekben az esetekben válassza a **kölcsönösen meghatározott** EDIFACT vagy **kölcsönösen definiált (X12)** beállítást a X12 számára. <p>A RosettaNet esetében válassza a csak a **Duns** lehetőséget, amely a standard. |
   | **Érték** | Yes | Egy érték, amely azonosítja a logikai alkalmazások által fogadott dokumentumokat. <p>A RosettaNet esetében ennek az értéknek egy kilenc számjegyű számnak kell lennie, amely megfelel a DUNS számának. |
   ||||

   > [!NOTE]
   > A RosettaNet-t használó partnereink számára további információkat adhat meg, ha először létrehozza ezeket a partnereket, majd [később szerkeszti őket](#edit-partner).

1. Ha elkészült, válassza az **OK** gombot.

   Az új partner most már megjelenik a **partnerek** listáján. A **partnerek** csempéje is frissíti a partnerek aktuális számát.

   ![Új partner](./media/logic-apps-enterprise-integration-partners/new-partner.png)

<a name="edit-partner"></a>

## <a name="edit-partner"></a>Partner szerkesztése

1. A [Azure Portalban](https://portal.azure.com)keresse meg és válassza ki az integrációs fiókját.
Válassza ki a **partnerek** csempét.

   ![Válassza a "partnerek" csempét](./media/logic-apps-enterprise-integration-partners/edit.png)

1. A **partnerek** területen válassza ki a szerkeszteni kívánt partnert, és válassza a **Szerkesztés** lehetőséget. A **Szerkesztés** területen hajtsa végre a módosításokat.

   ![A módosítások elvégzése és mentése](./media/logic-apps-enterprise-integration-partners/edit-partner.png)

   A RosettaNet a **RosettaNet-partner tulajdonságai** területen megadhatja ezt a további információt:

   | Tulajdonság | Kötelező | Leírás |
   |----------|----------|-------------|
   | **Partner besorolása** | No | A partner szervezeti típusa |
   | **Ellátási lánc kódja** | No | A partner ellátási láncának kódja, például "információs technológia" vagy "elektronikus összetevők" |
   | **Kapcsolattartó neve** | No | A partner kapcsolattartójának neve |
   | **E-mail** | No | A partner e-mail-címe |
   | **Fax** | No | A partner faxszáma |
   | **Telefon** | No | A partner telefonszáma |
   ||||

1. Ha elkészült, kattintson **az OK** gombra a módosítások mentéséhez.

## <a name="delete-partner"></a>Partner törlése

1. A [Azure Portalban](https://portal.azure.com)keresse meg és válassza ki az integrációs fiókját. Válassza ki a **partnerek** csempét.

   ![Képernyőkép, amely megjeleníti a partnereknek a kiválasztott partnerek csempéjét.](./media/logic-apps-enterprise-integration-partners/choose-partners-to-delete.png)

1. A **partnerek** területen válassza ki a törölni kívánt partnert. Válassza a **Törlés** elemet.

   ![Partner törlése](./media/logic-apps-enterprise-integration-partners/delete-partner.png)

## <a name="next-steps"></a>Következő lépések

* További információ a [szerződésekről](../logic-apps/logic-apps-enterprise-integration-agreements.md)