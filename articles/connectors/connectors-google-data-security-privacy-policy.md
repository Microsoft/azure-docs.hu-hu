---
title: Adatbiztonsági és adatvédelmi szabályzatok a Google-összekötők számára
description: Ismerje meg, hogy milyen hatással van a Google biztonsági és adatvédelmi szabályzatok a Google-összekötőre, például a Gmailre, Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: conceptual
ms.date: 06/05/2020
ms.openlocfilehash: 2a5204be638f108b40e431b148c9cb97788c4a52
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91400757"
---
# <a name="data-security-and-privacy-policies-for-google-connectors-in-azure-logic-apps"></a>Adatbiztonsági és adatvédelmi szabályzatok a Google-összekötők számára Azure Logic Apps

**2020. május 1-től** a Google [adatbiztonsági és adatvédelmi szabályzatai](https://www.blog.google/technology/safety-security/project-strobe/) miatti változások befolyásolhatják a [Gmail-összekötőt](/connectors/gmail/)használó logikai alkalmazás-munkafolyamatokat. Ha a Logic apps a Gmail-összekötőt egy Gmail-beli felhasználói fiókkal (a vagy a-val végződő e-mail-címmel @gmail.com @googlemail.com ) használja, a Logic apps csak meghatározott [Google által jóváhagyott eseményindítókat, műveleteket és összekötőket](#approved-connectors)használhat.

> [!NOTE]
> Ha a Logic apps a Gmail-összekötőt a G-Suite üzleti fiókkal (az egyéni tartománnyal rendelkező e-mail-címmel) használja, akkor a logikai alkalmazások nem érintik, és nem korlátozzák a Gmail-összekötő használatát.

## <a name="affected-logic-apps"></a>Érintett logikai alkalmazások

Ha a Gmail-összekötőt használó logikai alkalmazások rendelkeznek, e-mailt fog kapni a potenciálisan érintett logikai alkalmazásokról. A nem megfelelő munkafolyamatok azonban a **2020. június 15**-én lesznek letiltva. A következő műveletek bármelyikét elvégezheti:

* Frissítse az érintett logikai alkalmazásokat a [jelen témakörben ismertetett lépéseket követve](#update-affected-workflows). Létre kell hoznia egy Google ügyfélalkalmazás-alkalmazást, amely egy ügyfél-azonosítót és egy ügyfél-titkot biztosít a Gmail-triggerben vagy-műveletben való hitelesítéshez.

* Frissítse az érintett Logic apps-alkalmazásokat, hogy csak a [Google által jóváhagyott összekötőket](#approved-connectors) használják a letiltott logikai alkalmazások újbóli engedélyezése előtt.

<a name="approved-connectors"></a>

## <a name="google-approved-connectors"></a>Google által jóváhagyott összekötők

Ebben a szabályzatban, ha egy Gmail fogyasztói fiókot használ, akkor a Gmail-összekötőt csak a Google által jóváhagyott szolgáltatásokkal használhatja, amelyek változhatnak. Mérnöki csapatunk továbbra is együttműködik a Google-szel, hogy további szolgáltatásokat vegyen fel a listára. Jelenleg itt láthatók a Google által jóváhagyott eseményindítók, műveletek és összekötők, amelyek a Gmail-összekötővel azonos logikai alkalmazás-munkafolyamatban, a Gmail-alapú felhasználói fiók használata esetén is használhatók:

* Beépített eseményindítók és műveletek Logic Apps: batch, Control, adatműveletek, dátum idő, lapos fájl, folyadék, kérelem, ütemterv, változók és XML

  A Google által nem jóváhagyott beépített eseményindítók és műveletek (például HTTP, Azure Functions, Azure Logic Apps és mások) nem felelnek meg a logikai alkalmazásnak a Gmail-összekötővel, mert az alkalmazás bárhonnan küldhet vagy fogadhat adatokból.

* Google Services: gmail, Google Naptár, Google Contacts, Google Drive, Google Sheets és Google feladatok

* Jóváhagyott Microsoft-szolgáltatások: Dynamics 365, Excel online, Microsoft Teams, Microsoft 365, OneDrive és SharePoint Online

* Ügyfelek által felügyelt adatforrásokhoz kapcsolódó összekötők: FTP, RSS, SFTP, SMTP és SQL Server

## <a name="non-compliant-examples"></a>Nem megfelelő példák

Íme néhány példa, amely a Gmail-összekötőt a Google által nem jóváhagyott beépített eseményindítókkal és műveletekkel vagy felügyelt összekötővel használja:

* Ez a logikai alkalmazás a Gmail-összekötőt használja a HTTP beépített triggerrel:

  ![Nem megfelelő logikai alkalmazás – 1. példa](./media/connectors-google-data-security-privacy-policy/not-compliant-logic-app-1.png)
  
  A logikai alkalmazás a Google Calendar Connectort is használja, amely jóvá van hagyva.

* Ez a logikai alkalmazás a Gmail-összekötőt használja az Azure Blob Storage-összekötővel:

  ![Nem megfelelő logikai alkalmazás – 2. példa](./media/connectors-google-data-security-privacy-policy/not-compliant-logic-app-2.png)

* Ez a logikai alkalmazás a Gmail-összekötőt használja a Twitter-összekötővel:

  ![Nem megfelelő logikai alkalmazás – 3. példa](./media/connectors-google-data-security-privacy-policy/not-compliant-logic-app-3.png)

A legfrissebb információkért tekintse [meg a Gmail-összekötő műszaki útmutatójának dokumentációját](/connectors/gmail/).

<a name="update-affected-workflows"></a>

## <a name="steps-for-affected-logic-apps"></a>Az érintett Logic Apps-alkalmazások lépései

Ha a Gmail-összekötőt egy Gmail-alapú felhasználói fiókkal és a Google által nem jóváhagyott összekötővel kell használnia egy logikai alkalmazásban, létrehozhat saját Google-alkalmazást személyes vagy belső használatra a vállalatában. Ebben a forgatókönyvben az alábbi lépéseket kell végrehajtania:

1. Hozzon létre egy Google-ügyfélprogramot a [Google API-konzol](https://console.developers.google.com)használatával.

1. A Gmail-összekötőben használja az ügyfél-azonosítót és az ügyfél titkos értékét a Google-ügyfélprogramból.

További információ: a Gmail- [összekötő műszaki útmutatója](/connectors/gmail/#authentication-and-bring-your-own-application).

### <a name="create-google-client-app"></a>Google ügyfélalkalmazás létrehozása

Ha egy projektet szeretne beállítani az ügyfélalkalmazás számára, használja a [Google API Console varázslót](https://console.developers.google.com/start/api?id=gmail&credential=client_key) , és kövesse az utasításokat. A részletes lépésekért tekintse át a [Gmail-összekötő technikai dokumentációjában](/connectors/gmail/#authentication-and-bring-your-own-application)található utasításokat.

Ha elkészült, a képernyő úgy néz ki, mint például a saját **Ügyfél-azonosítója** és az **ügyfél titkos** értékei, amelyeket később a logikai alkalmazásban használhat.

![Ügyfél-azonosító és ügyfél-titok a Google-ügyfélalkalmazás számára](./media/connectors-google-data-security-privacy-policy/google-api-console.png)

### <a name="use-client-app-settings-in-logic-app"></a>Ügyfélalkalmazások beállításainak használata a logikai alkalmazásban

Az ügyfél-azonosító és az ügyfél titkos kódjának a Gmail-triggerben vagy-műveletben való használatához kövesse az alábbi lépéseket:

1. A [Azure Portalban](https://portal.azure.com)nyissa meg a logikai alkalmazást a Logic app Designerben.

1. Ha új Gmail-triggert vagy-műveletet ad hozzá, és egy teljesen új kapcsolattal hoz létre, folytassa a következő lépéssel. Ellenkező esetben a Gmail-triggerben vagy a műveletben válassza a **kapcsolat**  >  **hozzáadása új**, például a következőt:

   ![Válassza a "kapcsolatok módosítása" > "új hozzáadása" lehetőséget.](./media/connectors-google-data-security-privacy-policy/change-gmail-connection.png)

1. Adja meg a kapcsolatok adatait, például:

   ![Adja meg a kapcsolatok adatait](./media/connectors-google-data-security-privacy-policy/authentication-type-bring-your-own.png)

   | Tulajdonság | Érték | Leírás |
   |----------|-------|-------------|
   | **Hitelesítés típusa** | **Saját alkalmazás használata** | Azt határozza meg, hogy a hitelesítéshez saját ügyfélalkalmazás fog használni. |
   | **Ügyfél-azonosító** | <*ügyfél-azonosító*> | A Google-ügyfélalkalmazás ügyfél-azonosítója |
   | **Ügyfél titka** | <*ügyfél – titok*> | Az ügyfél titkos kulcsa a Google-ügyfélprogramból |
   ||||

1. Ha elkészült, válassza **a bejelentkezés** lehetőséget.

   Megjelenik egy lap, amely megjeleníti a létrehozott ügyfélalkalmazás-alkalmazást. Ha Gmail-alapú fogyasztói fiókot használ, akkor előfordulhat, hogy egy olyan oldalt kap, amely azt mutatja, hogy a Google nem ellenőrzi az ügyfélalkalmazás használatát, és felszólítja, hogy először engedélyezze a Google-fiókhoz való hozzáférést.

   ![Rákérdezés a Google-fiókhoz való hozzáférésre](./media/connectors-google-data-security-privacy-policy/allow-access-authorized-domain.png)

1. Ha szükséges, válassza az **Engedélyezés** lehetőséget.

   Mostantól korlátozás nélkül is használhatja a Gmail-összekötőt a logikai alkalmazásban.

## <a name="next-steps"></a>Következő lépések

További információ a [Gmail-összekötőről](/connectors/gmail/)

