---
title: Hozzáférés Azure AD alkalmazás alkalmazásproxy-alkalmazásokhoz a Teams szolgáltatásban | Microsoft Docs
description: Az Azure AD Application Proxy használatával elérheti a helyszíni alkalmazást a Microsoft Teams szolgáltatáson keresztül.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/05/2017
ms.author: kenwith
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1c44716f045340022c871501609cf582015ba20f
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99256609"
---
# <a name="access-your-on-premises-applications-through-microsoft-teams"></a>A helyszíni alkalmazások elérése a Microsoft Teams használatával

Azure Active Directory Application Proxy egyszeri bejelentkezést biztosít a helyszíni alkalmazásokhoz, függetlenül attól, hogy hol vannak. A Microsoft csapata egy helyen egyszerűsíti együttműködési erőfeszítéseit. A két együttes integrációja azt jelenti, hogy a felhasználók bármilyen helyzetben dolgozhatnak a csapattársakkal.

A felhasználók hozzáadhatnak felhőalapú alkalmazásokat a csapatok csatornái számára a [lapok használatával](https://support.office.com/article/Video-Using-Tabs-7350a03e-017a-4a00-a6ae-1c9fe8c497b3?ui=en-US&rs=en-US&ad=US), de mi a helyzet a SharePoint-webhelyekkel vagy a helyszínen üzemeltetett tervezési eszközzel? A megoldás az alkalmazásproxy. Az Application proxyn keresztül közzétett alkalmazásokat ugyanazok a külső URL-címek használatával vehetik igénybe, mint az alkalmazások távoli eléréséhez. Mivel az alkalmazásproxy hitelesítése Azure Active Directoryon keresztül történik, a felhasználók egyszeri bejelentkezési élményt kapnak.

## <a name="install-the-application-proxy-connector-and-publish-your-app"></a>Telepítse az alkalmazásproxy-összekötőt, és tegye közzé az alkalmazást

Ha még nem tette meg, [konfigurálja a bérlőhöz tartozó alkalmazásproxy-t, és telepítse az összekötőt](application-proxy-add-on-premises-application.md). Ezt követően [közzéteheti](application-proxy-add-on-premises-application.md) a helyszíni alkalmazást a táveléréshez. Az alkalmazás közzétételekor jegyezze fel a külső URL-címet, mert az alkalmazás a Teams szolgáltatáshoz való hozzáadására szolgál.

Ha már közzétette az alkalmazásokat, de nem emlékszik a külső URL-címekre, tekintse meg őket a [Azure Portalban](https://portal.azure.com). Jelentkezzen be, majd navigáljon **Azure Active Directory**  >  **vállalati alkalmazások**  >  **minden alkalmazás** > válassza ki az alkalmazást > **alkalmazásproxy**.

## <a name="add-your-app-to-teams"></a>Alkalmazás hozzáadása a csapatokhoz

Miután közzétette az alkalmazást az Application proxyn keresztül, tájékoztassa a felhasználókat arról, hogy közvetlenül a csapatuk csatornáján is felvehetik a lapot, majd az alkalmazás a csapatban mindenki számára elérhető lesz. A következő három lépést kell követnie:

1. Navigáljon ahhoz a Teams-csatornához, amelyhez hozzá szeretné adni az alkalmazást, és válassza a **+** lapot a lap hozzáadásához.

   ![Válassza a + elemet egy lap felvételéhez a csapatokban](./media/application-proxy-integrate-with-teams/add-tab.png)

1. A lapon válassza a **webhely** lehetőséget.

   ![Válassza ki a webhelyet a lap hozzáadása képernyőn](./media/application-proxy-integrate-with-teams/website.png)

1. Adja meg a lap nevét, és állítsa be az alkalmazásproxy külső URL-címére mutató URL-címet.

   ![Nevezze el a lapot, és adja hozzá a külső URL-címet](./media/application-proxy-integrate-with-teams/tab-name-url.png)

Ha egy csapat tagja felveszi a fület, az megjelenik a csatornán mindenki számára. Az alkalmazáshoz hozzáféréssel rendelkező felhasználók egyszeri bejelentkezéses hozzáférést kapnak a Microsoft Teams szolgáltatáshoz használt hitelesítő adatokhoz. Azok a felhasználók, akik nem rendelkeznek hozzáféréssel az alkalmazáshoz, megtekinthetik a csapat lapjait, de csak akkor vannak letiltva, ha nem ad nekik engedélyeket a helyszíni alkalmazáshoz és az alkalmazás Azure Portal közzétett verziójához.

## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogyan [tehet közzé helyszíni SharePoint-webhelyeket](application-proxy-integrate-with-sharepoint-server.md) alkalmazásproxy használatával.
- Konfigurálja az alkalmazásokat úgy, hogy az [Egyéni tartományokat](application-proxy-configure-custom-domain.md) használják külső URL-címként.
