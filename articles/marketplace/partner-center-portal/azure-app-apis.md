---
title: A partner Center beküldési API-ját az Azure-alkalmazások előkészítéséhez a Microsoft kereskedelmi piactéren
description: Ismerje meg, hogy milyen előfeltételeket kell használni a partner Center beküldési API Azure-alkalmazásokhoz való használatához a kereskedelmi piactéren a Microsoft partner Centerben.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: 9863ed24da9e427f885a4794bda7e103b0c1cc8e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96455447"
---
# <a name="partner-center-submission-api-to-onboard-azure-apps-in-partner-center"></a>A partner Center beküldési API-ját az Azure Apps szolgáltatásba a partner Centerben

A *partner Center beküldési API* használatával programozott módon kérdezheti le, hozhat létre és tehet közzé Azure-ajánlatokat.  Ez az API akkor hasznos, ha a fiók számos ajánlatot kezel, és szeretné automatizálni és optimalizálni az ajánlatok beküldési folyamatát.

## <a name="api-prerequisites"></a>API-előfeltételek

Néhány programozott eszközre van szükség ahhoz, hogy használni lehessen a partner Center API-t az Azure-termékekhez: 

- egy Azure Active Directory alkalmazás.
- egy Azure Active Directory (Azure AD) hozzáférési jogkivonat.

### <a name="step-1-complete-prerequisites-for-using-the-partner-center-submission-api"></a>1. lépés: a partner Center beküldési API használatának előfeltételei

Mielőtt elkezdené a kód írását a partner Center beküldési API meghívásához, győződjön meg arról, hogy végrehajtotta a következő előfeltételeket.

- Önnek (vagy szervezetének) rendelkeznie kell egy Azure AD-címtárral, és [globális rendszergazdai](../../active-directory/roles/permissions-reference.md) engedéllyel kell rendelkeznie a címtárhoz. Ha már használja a Microsoft Microsoft 365 vagy más üzleti szolgáltatásait, akkor már rendelkezik Azure AD-címtárral. Ellenkező esetben [új Azure ad-t hozhat létre a partner Centerben](/windows/uwp/publish/associate-azure-ad-with-partner-center#create-a-brand-new-azure-ad-to-associate-with-your-partner-center-account) , külön díj nélkül.

- Hozzá kell [rendelnie egy Azure ad-alkalmazást a partner Center-fiókjához](/windows/uwp/monetize/create-and-manage-submissions-using-windows-store-services#associate-an-azure-ad-application-with-your-windows-partner-center-account) , és meg kell szereznie a bérlő azonosítóját, ügyfél-azonosítóját és kulcsát. Ezekre az értékekre szüksége lesz az Azure AD hozzáférési token beszerzéséhez, amelyet a Microsoft Store beküldési API-hoz tartozó hívásokban fog használni.

#### <a name="how-to-associate-an-azure-ad-application-with-your-partner-center-account"></a>Azure AD-alkalmazás hozzárendelése a partner Center-fiókhoz

A Microsoft Store beküldési API használatához hozzá kell rendelnie egy Azure AD-alkalmazást a partner Center-fiókjához, le kell kérnie az alkalmazás bérlői AZONOSÍTÓját és ügyfél-AZONOSÍTÓját, és elő kell készítenie egy kulcsot. Az Azure AD-alkalmazás azt az alkalmazást vagy szolgáltatást jelöli, amelyről meg szeretné hívni a partner Center beküldési API-ját. Szüksége lesz a bérlői AZONOSÍTÓra, az ügyfél-AZONOSÍTÓra és a kulcsra az API-hoz továbbított Azure AD hozzáférési jogkivonat beszerzéséhez.

>[!Note]
>Ezt a feladatot csak egyszer kell végrehajtania. A bérlő azonosítója, az ügyfél azonosítója és kulcsa után bármikor újra felhasználhatja őket, amikor új Azure AD-hozzáférési tokent kell létrehoznia.

1. A partner Centerben [társítsa a szervezete partner Center-fiókját a szervezet Azure ad-címtárával](/windows/uwp/publish/associate-azure-ad-with-partner-center).
1. Ezután a partner Center **Fiókbeállítások** szakaszának **felhasználók** lapján adja hozzá azt az alkalmazást vagy szolgáltatást jelölő [Azure ad-alkalmazást](/windows/uwp/publish/add-users-groups-and-azure-ad-applications#add-azure-ad-applications-to-your-partner-center-account) , amelyet a partner Center-fiókhoz való hozzáféréshez használni fog. Győződjön meg arról, hogy az alkalmazást a **kezelő** szerepkörhöz rendeli. Ha az alkalmazás még nem létezik az Azure AD-címtárban, [létrehozhat egy új Azure ad-alkalmazást a partner Centerben](/windows/uwp/publish/add-users-groups-and-azure-ad-applications#create-a-new-azure-ad-application-account-in-your-organizations-directory-and-add-it-to-your-partner-center-account).
1. Térjen vissza a **felhasználók** lapra, kattintson az Azure ad-alkalmazás nevére, hogy megnyissa az alkalmazás beállításait, és másolja le a **bérlő azonosítóját** és az **ügyfél-azonosító** értékeit.
1. Kattintson az **új kulcs hozzáadása** lehetőségre. A következő képernyőn másolja le a **kulcs** értékét. Ezt az információt később nem érheti el, miután elhagyja ezt a lapot. További információ: [Az Azure ad-alkalmazás kulcsainak kezelése](/windows/uwp/publish/add-users-groups-and-azure-ad-applications#manage-keys).

### <a name="step-2-obtain-an-azure-ad-access-token"></a>2. lépés: Azure AD hozzáférési jogkivonat beszerzése

Mielőtt bármelyik módszert meghívja a partner Center beküldési API-ban, először be kell szereznie egy Azure AD hozzáférési jogkivonatot, amelyet az API egyes módszereinek **engedélyezési** fejlécébe kell átadnia. A hozzáférési token beszerzése után 60 percet is igénybe vehet, mielőtt lejár. A jogkivonat lejárta után frissítheti a jogkivonatot, így továbbra is használhatja azt az API-hoz való jövőbeli hívásokban.

A hozzáférési jogkivonat beszerzéséhez kövesse az [ügyfél hitelesítő adataival](../../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md) megjelenő, a szolgáltatás által `HTTP POST` a `https://login.microsoftonline.com/<tenant_id>/oauth2/token` végpontnak küldendő utasításokat. Példa erre a kérelemre:

JSONCopy
```Json
POST https://login.microsoftonline.com/<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded; charset=utf-8

grant_type=client_credentials
&client_id=<your_client_id>
&client_secret=<your_client_secret>
&resource= https://api.partner.microsoft.com
```

A  `POST URI` és a *client_id* és a *client_secret* paraméterek tenant_id értékének megadásához adja meg a bérlői azonosítót, az ügyfél-azonosítót és az alkalmazás azon kulcsát, amelyet a partner Centerből az előző szakaszban beolvasott. Az *erőforrás* -paraméterhez meg kell adnia a következőt: `https://api.partner.microsoft.com` .

### <a name="step-3-use-the-microsoft-store-submission-api"></a>3. lépés: a Microsoft Store beküldési API használata

Az Azure AD hozzáférési jogkivonattal metódusokat hívhat a partner Center beküldési API-ban. A kiküldetések létrehozásához vagy frissítéséhez általában több metódust kell meghívni a partner Center beküldési API-ban egy adott sorrendben. Az egyes forgatókönyvekkel és az egyes módszerek szintaxisával kapcsolatos információkért tekintse meg a betöltés API-hencegés című témakört.

https://apidocs.microsoft.com/services/partneringestion/

## <a name="next-steps"></a>Következő lépések

* Ismerje meg, hogyan hozhat létre egy Azure-beli [virtuális gép technikai eszközét](../create-azure-container-technical-assets.md)
* Ismerje meg, hogyan hozhat létre [Azure Container-ajánlatot](../create-azure-container-offer.md)