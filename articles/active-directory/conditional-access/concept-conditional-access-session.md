---
title: Munkamenet-vezérlőelemek a feltételes hozzáférési házirendben – Azure Active Directory
description: Az Azure AD feltételes hozzáférési szabályzatában szereplő munkamenet-vezérlőelemek
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2a0089d246169ad4215075662500794e7143940e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90601809"
---
# <a name="conditional-access-session"></a>Feltételes hozzáférés: munkamenet

Egy feltételes hozzáférési szabályzaton belül a rendszergazdák a munkamenet-vezérlők használatával korlátozott élményeket biztosíthatnak bizonyos felhőalapú alkalmazásokban.

![Feltételes hozzáférési szabályzat többtényezős hitelesítést igénylő engedélyezési vezérlővel](./media/concept-conditional-access-session/conditional-access-session.png)

## <a name="application-enforced-restrictions"></a>Alkalmazás által kényszerített korlátozások

A szervezetek használhatják ezt a vezérlőt arra, hogy az Azure AD-t az eszköz adatainak átadására kérje a kiválasztott felhőalapú alkalmazások számára. Az eszköz adatai lehetővé teszik a felhőalapú alkalmazások számára, hogy megismerjék, hogy a kapcsolat kompatibilis vagy tartományhoz csatlakoztatott eszközről kezdeményezhető-e. Ez a vezérlő csak a SharePoint Online-t és az Exchange Online-t támogatja a kiválasztott felhőalapú alkalmazásokként. Ha bejelöli, a felhőalapú alkalmazás az eszköz információi alapján biztosítja a felhasználók számára az eszköz állapotától függően korlátozott vagy teljes körű felhasználói élményt.

Az alkalmazás által kényszerített korlátozások használatával és konfigurálásával kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Korlátozott hozzáférés engedélyezése a SharePoint Online-ban](/sharepoint/control-access-from-unmanaged-devices)
- [Korlátozott hozzáférés engedélyezése az Exchange Online-ban](https://aka.ms/owalimitedaccess)

## <a name="conditional-access-application-control"></a>Feltételes hozzáférésű alkalmazás vezérlője

A feltételes hozzáférést biztosító alkalmazás-vezérlő fordított proxy-architektúrát használ, és egyedi módon integrálva van az Azure AD feltételes hozzáférésével. Az Azure AD feltételes hozzáférés használatával bizonyos feltételek alapján kényszerítheti a szervezet alkalmazásaihoz való hozzáférés-vezérlést. A feltételek határozzák meg, hogy kik (felhasználók vagy csoportok) és mi (mely Felhőbeli alkalmazások) és hol (mely helyekről és hálózatokból) a feltételes hozzáférési szabályzat érvényes. A feltételek meghatározása után átirányíthatja a felhasználókat [Microsoft Cloud app Securityba](/cloud-app-security/what-is-cloud-app-security) , ahol a hozzáférés-és munkamenet-vezérlők alkalmazásával védelemmel láthatja el az feltételes hozzáférést biztosító alkalmazás-vezérlő az adataikat.

Feltételes hozzáférést biztosító alkalmazás-vezérlő lehetővé teszi a felhasználói alkalmazások hozzáférésének és munkameneteinek valós idejű figyelését és vezérlését a hozzáférési és munkamenet-házirendek alapján. A hozzáférési és munkamenet-szabályzatok a Cloud App Security-portálon találhatók a szűrők további pontosításához és a felhasználó által végrehajtandó műveletek beállításához. A hozzáférési és munkamenet-szabályzatokkal a következőket teheti:

- Az adatok kiszűrése megakadályozása: letilthatja a bizalmas dokumentumok letöltését, kivágását, másolását és nyomtatását, például nem felügyelt eszközökön.
- Védelem letöltése: a bizalmas dokumentumok letöltésének letiltása helyett megkövetelheti a dokumentumok címkézését és védelmét Azure Information Protection. Ez a művelet biztosítja, hogy a dokumentum védve legyen, és a felhasználói hozzáférés egy potenciálisan kockázatos munkamenetben van korlátozva.
- Címkézetlen fájlok feltöltésének megakadályozása: a bizalmas fájlok feltöltése, terjesztése és mások általi használata előtt fontos, hogy a fájl megfelelő címkével és védelemmel legyen ellátva. Gondoskodhat arról, hogy a nem címkézett fájlok legyenek feltöltve a bizalmas tartalommal, amíg a felhasználó nem osztályozza a tartalmat.
- Felhasználói munkamenetek figyelése a megfelelőség érdekében: a rendszer figyeli a kockázatos felhasználókat, amikor bejelentkeznek az alkalmazásokba, és a műveleteiket a munkameneten belül naplózzák. Megvizsgálhatja és elemezheti a felhasználói viselkedést, hogy megértse, hol és milyen feltételek mellett alkalmazza a rendszer a munkamenet-házirendeket a jövőben.
- Hozzáférés letiltása: a különböző kockázati tényezőktől függően részletesen letilthatja az egyes alkalmazások és felhasználók hozzáférését. Például letilthatja őket, ha Ügyféltanúsítványok formájában használják az ügyféltanúsítványt.
- Egyéni tevékenységek blokkolása: egyes alkalmazások olyan egyedi forgatókönyvekkel rendelkeznek, amelyek kockázatokat hordoznak, például a Microsoft Teams vagy a Slack-alkalmazásokban bizalmas tartalmú üzeneteket küldenek. Ezekben a forgatókönyvekben a bizalmas tartalmak üzeneteinek vizsgálatára és valós idejű blokkolására van lehetőség.

További információkért tekintse meg a [Kiemelt alkalmazások feltételes hozzáférést biztosító alkalmazás-vezérlő üzembe helyezése](/cloud-app-security/proxy-deployment-aad)című cikket.

## <a name="sign-in-frequency"></a>Bejelentkezési gyakoriság

A bejelentkezési gyakoriság határozza meg azt az időtartamot, ameddig a felhasználónak újra be kell jelentkeznie, amikor egy erőforráshoz próbál hozzáférni.

A bejelentkezési gyakoriság beállítása olyan alkalmazásokkal működik, amelyek a szabványok alapján OAUTH2 vagy OIDC protokollokat implementáltak. A Microsoft natív alkalmazásai a Windows, Mac és Mobile rendszerekhez, beleértve a következő webalkalmazásokat is, amelyek megfelelnek a beállításnak.

- Word, Excel, PowerPoint online
- OneNote online
- Office.com
- Microsoft 365 felügyeleti portál
- Exchange Online
- SharePoint és OneDrive
- Csapat webes ügyfele
- Dynamics CRM Online
- Azure Portal

További információ: a [hitelesítési munkamenetek kezelésének konfigurálása feltételes hozzáféréssel](howto-conditional-access-session-lifetime.md#user-sign-in-frequency).

## <a name="persistent-browser-session"></a>Állandó böngésző-munkamenet

Az állandó böngésző-munkamenet lehetővé teszi a felhasználók számára, hogy a böngészőablak bezárása és újbóli megnyitása után is bejelentkezve maradjanak.

További információ: a [hitelesítési munkamenetek kezelésének konfigurálása feltételes hozzáféréssel](howto-conditional-access-session-lifetime.md#persistence-of-browsing-sessions).

## <a name="next-steps"></a>Következő lépések

- [Feltételes hozzáférés – közös szabályzatok](concept-conditional-access-policy-common.md)

- [Csak jelentési mód](concept-conditional-access-report-only.md)
