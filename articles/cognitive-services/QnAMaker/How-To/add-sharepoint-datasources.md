---
title: SharePoint-fájlok – QnA Maker
description: Biztonságos SharePoint-adatforrásokat adhat hozzá a tudásbázishoz, hogy a tudásbázist a Active Directory által védett kérdésekkel és válaszokkal gazdagítsa.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 0832b54e02cabecb0b1f0e7af600b8adc621a8b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "99584770"
---
# <a name="add-a-secured-sharepoint-data-source-to-your-knowledge-base"></a>Biztonságos SharePoint-adatforrás hozzáadása a tudásbázishoz

A Tudásbázisban biztonságos felhőalapú SharePoint-adatforrásokat adhat hozzá, hogy a tudásbázist a Active Directoryekkel biztonságossá tehető kérdésekkel és válaszokkal gazdagítsa.

Amikor biztonságos SharePoint-dokumentumot ad hozzá a tudásbázishoz, a QnA Maker Managernek Active Directory engedélyt kell kérnie QnA Maker. Ha ezt az engedélyt a Active Directory managerből QnA Maker a SharePointhoz való hozzáféréshez, azt nem kell újból megadni. A Tudásbázis minden további dokumentuma nem igényel hitelesítést, ha ugyanazon a SharePoint-erőforráson van.

Ha a QnA Maker Tudásbázis-kezelő nem a Active Directory Manager, akkor a folyamat befejezéséhez kommunikálni kell a Active Directory kezelőjével.

## <a name="prerequisites"></a>Előfeltételek

* A felhőalapú SharePoint-QnA Maker az engedélyeket Microsoft Graph használja. Ha a SharePoint helyszíni, nem tud kinyerni a SharePointból, mert Microsoft Graph nem fogja tudni meghatározni az engedélyeket.
* URL-formátum – QnA Maker csak a SharePoint URL-címeket támogatja, amelyek megosztásra és formátumra vannak létrehozva `https://\*.sharepoint.com`

## <a name="add-supported-file-types-to-knowledge-base"></a>Támogatott fájltípusok hozzáadása a tudásbázishoz

A SharePoint-webhelyekről minden QnA Maker által támogatott [fájltípust](../index.yml) hozzáadhat a tudásbázishoz. Előfordulhat, hogy [engedélyeket](#permissions) kell megadnia, ha a fájl erőforrása védett.

1. A könyvtár és a SharePoint-webhely között válassza ki a fájl három pont menüjét `...` .
1. Másolja a fájl URL-címét.

   ![A SharePoint-fájl URL-címének lekéréséhez válassza ki a fájl három pont menüjét, majd másolja az URL-címet.](../media/add-sharepoint-datasources/get-sharepoint-file-url.png)

1. A QnA Maker portál **Beállítások** lapján adja hozzá az URL-címet a tudásbázishoz.

### <a name="images-with-sharepoint-files"></a>Képek SharePoint-fájlokkal

Ha a fájlok tartalmaznak képeket, azok nincsenek kibontva. A képet a QnA Maker-portálról is hozzáadhatja a fájl QnA-párokba való kibontása után.

Adja hozzá a képet a következő Markdown szintaxissal:

```markdown
![Explanation or description of image](URL of public image)
```

A szögletes zárójelben lévő szöveg, amely `[]` ismerteti a képet. A zárójelben lévő URL-cím a `()` közvetlen hivatkozás a képre.

Ha teszteli a QnA pár az interaktív teszt panelen, a QnA Maker portálon megjelenik a rendszerkép a Markdown szövege helyett. Ez ellenőrzi, hogy a rendszerkép nyilvánosan lekérhető-e az ügyfél-alkalmazásból.

## <a name="permissions"></a>Engedélyek

Engedélyek megadása akkor történik, ha egy SharePoint-kiszolgálóról származó biztonságos fájlt ad hozzá egy tudásbázishoz. Attól függően, hogy a SharePoint hogyan van beállítva, és a fájlt hozzáadó személy engedélyei a következőket tehetik szükségessé:

* nincs további lépés – a fájlt hozzáadó személy rendelkezik a szükséges engedélyekkel.
* a Tudásbázis- [kezelő](#knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal) és a [Active Directory Manager](#active-directory-manager-grant-file-read-access-to-qna-maker)lépései.

Tekintse meg az alább felsorolt lépéseket.

### <a name="knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal"></a>Tudásbázis-kezelő: SharePoint-adatforrás hozzáadása a QnA Maker portálon

Ha a **QnA Maker-kezelő** biztonságos SharePoint-dokumentumot ad hozzá egy tudásbázishoz, a Tudásbázis-kezelő egy, a Active Directory Manager által elvégzendő engedély kérését kezdeményezi.

A kérelem egy előugró ablaktal kezdődik, hogy hitelesítse Active Directory-fiókját.

![Felhasználói fiók hitelesítése](../media/add-sharepoint-datasources/authenticate-user-account.png)

Miután a QnA Maker Manager kiválasztja a fiókot, a Azure Active Directory rendszergazdája értesítést kap arról, hogy engedélyezni kell a QnA Maker alkalmazást (nem a QnA Maker Managert) a SharePoint-erőforráshoz való hozzáféréshez. A Azure Active Directory Managernek ezt minden SharePoint-erőforráshoz el kell végeznie, de az adott erőforrásban nem minden dokumentumhoz.

### <a name="active-directory-manager-grant-file-read-access-to-qna-maker"></a>Active Directory Manager: olvasási hozzáférés biztosítása a fájlhoz QnA Maker

A Active Directory Managernek (nem a QnA Maker Managernek) hozzáférést kell biztosítania a QnA Makerhoz a SharePoint-erőforrás eléréséhez. Ehhez válassza [ezt a hivatkozást](https://login.microsoftonline.com/common/oauth2/v2.0/authorize?response_type=id_token&scope=Files.Read%20Files.Read.All%20Sites.Read.All%20User.Read%20User.ReadBasic.All%20profile%20openid%20email&client_id=c2c11949-e9bb-4035-bda8-59542eb907a6&redirect_uri=https%3A%2F%2Fwww.qnamaker.ai%3A%2FCreate&state=68) , hogy engedélyezze a QnA Maker Portal SharePoint Enterprise alkalmazásnak a fájl olvasási engedélyeit.

![Azure Active Directory Manager engedélyezi az engedélyt interaktív módon](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)

<!--
The Active Directory manager must grant QnA Maker access either by application name, `QnAMakerPortalSharePoint`, or by application ID, `c2c11949-e9bb-4035-bda8-59542eb907a6`.
-->
<!--
### Grant access from the interactive pop-up window

The Active Directory manager will get a pop-up window requesting permissions to the `QnAMakerPortalSharePoint` app. The pop-up window includes the QnA Maker Manager email address that initiated the request, an `App Info` link to learn more about **QnAMakerPortalSharePoint**, and a list of permissions requested. Select **Accept** to provide those permissions.

![Azure Active Directory manager grants permission interactively](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)
-->
<!--

### Grant access from the App Registrations list

1. The Active Directory manager signs in to the Azure portal and opens **[App registrations list](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ApplicationsListBlade)**.

1. Search for and select the **QnAMakerPortalSharePoint** app. Change the second filter box from **My apps** to **All apps**. The app information will open on the right side.

    ![Select QnA Maker app in App registrations list](../media/add-sharepoint-datasources/select-qna-maker-app-in-app-registrations.png)

1. Select **Settings**.

    [![Select Settings in the right-side blade](../media/add-sharepoint-datasources/select-settings-for-qna-maker-app-registration.png)](../media/add-sharepoint-datasources/select-settings-for-qna-maker-app-registration.png#lightbox)

1. Under **API access**, select **Required permissions**.

    ![Select 'Settings', then under 'API access', select 'Required permission'](../media/add-sharepoint-datasources/select-required-permissions-in-settings-blade.png)

1. Do not change any settings in the **Enable Access** window. Select **Grant Permission**.

    [![Under 'Grant Permission', select 'Yes'](../media/add-sharepoint-datasources/grant-app-required-permissions.png)](../media/add-sharepoint-datasources/grant-app-required-permissions.png#lightbox)

1. Select **YES** in the pop-up confirmation windows.

    ![Grant required permissions](../media/add-sharepoint-datasources/grant-required-permissions.png)
-->
### <a name="grant-access-from-the-azure-active-directory-admin-center"></a>Hozzáférés biztosítása a Azure Active Directory felügyeleti központból

1. A Active Directory Manager bejelentkezik a Azure Portalba, és megnyitja a **[vállalati alkalmazásokat](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps)**.

1. Keresse meg a `QnAMakerPortalSharePoint` válassza ki a QnA Maker alkalmazást.

    [![QnAMakerPortalSharePoint keresése a vállalati alkalmazások listájában](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png)](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png#lightbox)

1. A **Biztonság** területen lépjen az **engedélyek** elemre. Válassza **a rendszergazdai jóváhagyás megadása a szervezet számára** lehetőséget.

    [![Active Directory rendszergazda számára válassza a hitelesített felhasználó lehetőséget.](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png)](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png#lightbox)

1. Válasszon olyan Sign-On fiókot, amely jogosult a Active Directory engedélyeinek megadására.




## <a name="add-sharepoint-data-source-with-apis"></a>SharePoint-adatforrás hozzáadása API-kkal

Az Azure Blob Storage használatával az alábbi lépéseket követve adhat hozzá legújabb SharePoint-tartalmakat az API-n keresztül: 
1.  Töltse le helyileg a SharePoint-fájlokat. Az API-t hívó felhasználónak hozzáféréssel kell rendelkeznie a SharePointhoz. 
1.  Töltse fel őket az Azure Blob Storage-ba. Ez egy biztonságos közös hozzáférést hoz létre az [sas-token használatával.](../../../storage/common/storage-sas-overview.md#how-a-shared-access-signature-works) 
1. Adja át a SAS-jogkivonattal generált blob URL-címet a QnA Maker APInak. Ahhoz, hogy a kérdés megválaszolja a fájlokat, az QnA Maker API URL végén fel kell vennie az utótag fájltípusát "&ext = PDF" vagy "&ext = doc" néven.


<!--
## Get SharePoint File URI

Use the following steps to transform the SharePoint URL into a sharing token.

1. Encode the URL using [base64](https://en.wikipedia.org/wiki/Base64).

1. Convert the base64-encoded result to an unpadded base64url format with the following character changes.

    * Remove the equal character, `=` from the end of the value.
    * Replace `/` with `_`.
    * Replace `+` with `-`.
    * Append `u!` to be beginning of the string.

1. Sign in to Graph explorer and run the following query, where `sharedURL` is ...:

    ```
    https://graph.microsoft.com/v1.0/shares/<sharedURL>/driveitem
    ```

    Get the **@microsoft.graph.downloadUrl** and use this as `fileuri` in the QnA Maker APIs.

### Add or update a SharePoint File URI to your knowledge base

Use the **@microsoft.graph.downloadUrl** from the previous section as the `fileuri` in the QnA Maker API for [adding a knowledge base](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase) or [updating a knowledge base](/rest/api/cognitiveservices/qnamaker/knowledgebase/update). The following fields are mandatory: name, fileuri, filename, source.

```
{
    "name": "Knowledge base name",
    "files": [
        {
            "fileUri": "<@microsoft.graph.downloadURL>",
            "fileName": "filename.xlsx",
            "source": "<SharePoint link>"
        }
    ],
    "urls": [],
    "users": [],
    "hostUrl": "",
    "qnaList": []
}
```



## Remove QnA Maker app from SharePoint authorization

1. Use the steps in the previous section to find the Qna Maker app in the Active Directory admin center.
1. When you select the **QnAMakerPortalSharePoint**, select **Overview**.
1. Select **Delete** to remove permissions.

-->

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Együttműködés a Tudásbázisban](../index.yml)
