---
title: Lapos fájlok kódolása vagy dekódolása
description: A Azure Logic Apps a vállalati integrációs szolgáltatásban a Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 05/09/2020
ms.openlocfilehash: aebce8f284ed4bb21d99efffc8dd6d0c51b39533
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87001485"
---
# <a name="encode-and-decode-flat-files-in-azure-logic-apps-by-using-the-enterprise-integration-pack"></a>Egybesimított fájlok kódolása és dekódolása az Azure Logic Appsben az Enterprise Integration Packkel

Mielőtt a vállalatközi (B2B) forgatókönyvben üzleti partnernek küldi az XML-tartalmat, érdemes lehet először kódolni a tartalmat. A logikai alkalmazások létrehozásával a Flat **file** Connector használatával kódolhatja és dekódolhatja a lapos fájlokat. A logikai alkalmazás ezt az XML-tartalmat különböző forrásokból, például a kérelem eseményindítóból, egy másik alkalmazásból vagy más, [Azure Logic apps által támogatott összekötőből](../connectors/apis-list.md)is elérheti. További információ: [Mi az Azure Logic apps](logic-apps-overview.md)?

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nincs előfizetése, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Az a logikai alkalmazás, amelyben a **Flat file** Connectort és egy olyan triggert szeretne használni, amely elindítja a logikai alkalmazás munkafolyamatát. A **sima fájl** összekötője csak műveleteket biztosít, nem eseményindítókat. Használhatja az triggert vagy egy másik műveletet, amely az XML-tartalmat a logikai alkalmazásba kódolja kódolásra vagy dekódolásra. Ha most ismerkedik a Logic apps szolgáltatással, tekintse át a gyors útmutató [: az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)című útmutatót.

* Az Azure-előfizetéshez társított [integrációs fiók](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) , amely [ahhoz a logikai alkalmazáshoz](./logic-apps-enterprise-integration-create-integration-account.md#link-account) van társítva, ahol a **Flat file** Connectort használni szeretné. A logikai alkalmazásnak és az integrációs fióknak ugyanazon a helyen vagy az Azure-régióban kell lennie.

* Egy egyszerű fájl [sémája](logic-apps-enterprise-integration-schemas.md) , amelyet az XML-tartalom kódolásához vagy dekódolásához töltött be az integrációs fiókba

* Legalább két olyan [kereskedelmi partner](logic-apps-enterprise-integration-partners.md) , amelyet már definiált az integrációs fiókjában

## <a name="add-flat-file-encode-action"></a>Egybesimított fájlt kódoló művelet hozzáadása

1. A [Azure Portalban](https://portal.azure.com)nyissa meg a logikai alkalmazást a Logic app Designerben.

1. A logikai alkalmazás trigger vagy művelet területén válassza az **új lépés**  >  **művelet hozzáadása** lehetőséget. Ez a példa a kérelem triggert használja, amely a **http-kérelem fogadásakor**, valamint a logikai alkalmazáson kívüli bejövő kérések kezelésével foglalkozik.

   > [!TIP]
   > JSON-séma megadása nem kötelező. Ha a bejövő kérelemben minta-adattartalom szerepel, válassza a minta hasznos adatok **használata a séma létrehozásához** lehetőséget, adja meg a minta hasznos adatait, majd válassza a **kész** lehetőséget. A séma megjelenik a **kérelem törzsének JSON-sémája** mezőben.

1. A **válasszon műveletet** területen adja meg a parancsot `flat file` . A műveletek listából válassza a következő műveletet: **egyszerű fájl kódolása**

   ![A "sima fájl kódolása" művelet kiválasztása](./media/logic-apps-enterprise-integration-flatfile/select-flat-file-encoding.png)

1. Kattintson a szövegmezőbe, hogy **megjelenjen a dinamikus** tartalmak listája. A listából a **http-kérelem fogadása** szakaszban válassza ki a **Body (törzs** ) tulajdonságot, amely tartalmazza a kérelem törzsének kimenetét az triggerből, valamint a kódolni kívánt tartalmat.

   ![A dinamikus tartalmak listájából kódolni kívánt tartalom kiválasztása](./media/logic-apps-enterprise-integration-flatfile/select-content-to-encode.png)

   > [!TIP]
   > Ha nem jelenik meg a **törzs** tulajdonság a dinamikus tartalmak listájában, válassza a **továbbiak** lehetőséget a **http-kérelem fogadása** című szakaszban.
   > Közvetlenül is megadhatja a dekódolni kívánt tartalmat a **Content (tartalom** ) mezőben.

1. A **séma neve** listából válassza ki azt a sémát, amelyet a társított integrációs fiókban használ a kódoláshoz, például:

   ![A kódoláshoz használandó séma kiválasztása](./media/logic-apps-enterprise-integration-flatfile/select-schema-for-encoding.png)

   > [!NOTE]
   > Ha a listában nem jelenik meg séma, az integrációs fiók nem tartalmaz a kódoláshoz használandó sémafájl-fájlokat. Töltse fel az integrációs fiókjához használni kívánt sémát.

1. Mentse a logikai alkalmazást. Az összekötő teszteléséhez tegyen egy kérést a HTTPS-végpontra, amely megjelenik a kérés trigger **http post URL-címe** tulajdonságában, és adja meg a kérelem törzsében kódolni kívánt XML-tartalmat.

Ezzel befejezte a sima fájl kódolási műveletének beállítását. Egy valós alkalmazásban előfordulhat, hogy egy üzletági (LOB) alkalmazásban szeretné tárolni a kódolt adatmennyiséget, például a Salesforce-t. Vagy elküldheti a kódolt adategységeket egy kereskedelmi partnernek. Ha a kimenetet a kódolási műveletből Salesforce vagy a kereskedelmi partnernek szeretné elküldeni, használja a [Azure Logic Appsben elérhető többi összekötőt](../connectors/apis-list.md).

## <a name="add-flat-file-decode-action"></a>Egybesimított fájlt dekódoló művelet hozzáadása

1. A [Azure Portalban](https://portal.azure.com)nyissa meg a logikai alkalmazást a Logic app Designerben.

1. A logikai alkalmazás trigger vagy művelet területén válassza az **új lépés**  >  **művelet hozzáadása** lehetőséget. Ez a példa a kérelem triggert használja, amely a **http-kérelem fogadásakor**, valamint a logikai alkalmazáson kívüli bejövő kérések kezelésével foglalkozik.

   > [!TIP]
   > JSON-séma megadása nem kötelező. Ha a bejövő kérelemben minta-adattartalom szerepel, válassza a minta hasznos adatok **használata a séma létrehozásához** lehetőséget, adja meg a minta hasznos adatait, majd válassza a **kész** lehetőséget. A séma megjelenik a **kérelem törzsének JSON-sémája** mezőben.

1. A **válasszon műveletet** területen adja meg a parancsot `flat file` . A műveletek listából válassza a következő műveletet: **egyszerű fájl dekódolása**

   ![A "lapos fájl dekódolása" művelet kiválasztása](./media/logic-apps-enterprise-integration-flatfile/select-flat-file-decoding.png)

1. Kattintson a szövegmezőbe, hogy **megjelenjen a dinamikus** tartalmak listája. A listából a **http-kérelem fogadása** szakaszban válassza ki a **Body (törzs** ) tulajdonságot, amely tartalmazza a kérelem törzsének kimenetét az triggerből és a dekódolni kívánt tartalmat.

   ![Tartalom kiválasztása a dinamikus tartalmak listájából való dekódoláshoz](./media/logic-apps-enterprise-integration-flatfile/select-content-to-decode.png)

   > [!TIP]
   > Ha nem jelenik meg a **törzs** tulajdonság a dinamikus tartalmak listájában, válassza a **továbbiak** lehetőséget a **http-kérelem fogadása** című szakaszban. Közvetlenül is megadhatja a dekódolni kívánt tartalmat a **Content (tartalom** ) mezőben.

1. A **séma neve** listából válassza ki azt a sémát, amelyet a társított integrációs fiókban használ a dekódoláshoz, például:

   ![A dekódoláshoz használandó séma kiválasztása](./media/logic-apps-enterprise-integration-flatfile/select-schema-for-decoding.png)

   > [!NOTE]
   > Ha a listában nem jelenik meg séma, az integrációs fiók nem tartalmaz a dekódoláshoz használandó sémafájl-fájlokat. Töltse fel az integrációs fiókjához használni kívánt sémát.

1. Mentse a logikai alkalmazást. Az összekötő teszteléséhez tegyen fel egy kérést a HTTPS-végpontra, amely megjelenik a kérelem trigger **http post URL** tulajdonságában, és adja meg a dekódolni kívánt XML-tartalmat a kérelem törzsében.

Ezzel elkészült a lapos fájl dekódolása művelet beállításával. Egy valós alkalmazásban érdemes lehet egy üzletági (LOB) alkalmazásban (például Salesforce) tárolni a dekódolású adatmennyiséget. Vagy elküldheti a dekódolású adategységeket egy kereskedelmi partnernek. A dekódolási művelet kimenetének Salesforce vagy kereskedelmi partnernek történő elküldéséhez használja a [Azure Logic Appsban elérhető többi összekötőt](../connectors/apis-list.md).

## <a name="next-steps"></a>Következő lépések

* További információ a [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)
