---
title: Dynamics 365 for Customer Engagement létrehozása & PowerApps-ajánlathoz Microsoft AppSource (Azure Marketplace).
description: Dynamics 365 for Customer Engagement létrehozása & PowerApps-ajánlathoz Microsoft AppSource (Azure Marketplace).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: vamahtan
ms.author: vamahtan
ms.date: 04/20/2021
ms.openlocfilehash: d8b3a2da7ccbbbf866763dbe5c8c59b00e7abd10
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107820418"
---
# <a name="how-to-create-a-dynamics-365-for-customer-engagement--powerapps-offer"></a>Dynamics 365 for Customer Engagement powerapps& ajánlat létrehozása

Ez a cikk azt ismerteti, hogyan hozhat létre Dynamics 365 for Customer Engagement-& PowerApps-ajánlathoz. A Dynamics 365 összes ajánlata a minősítési folyamatunkon megy keresztül. A próbaverzióval a felhasználók üzembe helyezhetik a megoldást egy élő Dynamics 365-környezetben.

Mielőtt elkezdené, hozzon létre [](partner-center-portal/create-account.md) egy kereskedelmi piactéri fiókot a Partnerközpont, és győződjön meg arról, hogy az regisztrálva van a kereskedelmi piactér programjában.

## <a name="before-you-begin"></a>Előkészületek

Tekintse [át a Dynamics 365-ajánlat megtervezését ismertető témakört.](marketplace-dynamics-365.md) Ez ismerteti az ajánlat műszaki követelményeit, és felsorolja azokat az információkat és eszközöket, amelyekre szüksége lesz a létrehozásakor.

## <a name="create-a-new-offer"></a>Új ajánlat létrehozása

1. Jelentkezzen be a [Partnerközpont.](https://partner.microsoft.com/dashboard/home)
2. A bal oldali navigációs menüben válassza a **Kereskedelmi piactér – áttekintés**  >  **lehetőséget.**
3. Az Áttekintés lapon válassza az **+ Új ajánlat** Dynamics  >  **365 for Customer Engagement & PowerAppsben.**

    :::image type="content" source="media/dynamics-365/new-offer-dynamics-365-customer-engagement.png" alt-text="Megjeleníti a bal oldali panel menüpontját és az &quot;Új ajánlat&quot; gombot a Customer Engagement kiválasztásával.":::

> [!IMPORTANT]
> Az ajánlat közzététele után az abban a Partnerközpont szerkeszti a Microsoft AppSource az ajánlat újbóli közzététele után. A módosítás után mindig tegye közzé újra az ajánlatot.

## <a name="new-offer"></a>Új ajánlat

Adjon meg egy **ajánlatazonosítót.** Ez a fiók minden ajánlatának egyedi azonosítója.

- Ez az azonosító az ajánlat webcímében és a Azure Resource Manager látható.
- Csak kisbetűket és számokat használjon. Az azonosító kötőjeleket és aláhúzásjeleket tartalmazhat, szóközök nélkül, és legfeljebb 50 karakter hosszú lehet. Ha például a közzétevő azonosítója , és a `testpublisherid` **test-offer-1** címet adja meg, az ajánlat webcíme `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1` lesz.
- A Létrehozás lehetőséget választva az ajánlatazonosító nem **módosítható.**

Adjon meg egy **Ajánlat aliast.** Ez az ajánlat neve a Partnerközpont.

- Ez a név nem használatos az AppSource-on. Ez eltér az ajánlat nevétől és az ügyfelek számára megjelenő egyéb értékektől.
- A Létrehozás lehetőséget választva ez a név nem **módosítható.**

Az **ajánlat létrehozásához** válassza a Létrehozás lehetőséget. Partnerközpont Megnyílik az **Ajánlat beállítása** lap.

## <a name="alias"></a>Alias

Adjon meg egy leíró nevet, amely kizárólag az ajánlatra való hivatkozáshoz Partnerközpont. Az ajánlat aliasa (amely előre ki van töltve az ajánlat létrehozásakor megadott adatokkal) nem lesz használva a piactéren, és eltér az ügyfelek számára megjelenő ajánlatnévtől. Ha később frissíteni szeretné az ajánlat nevét, tekintse meg az [Ajánlat termékoldalát.](dynamics-365-customer-engage-offer-listing.md)

## <a name="setup-details"></a>Beállítás részletei

A **Hogyan szeretné, hogy a** lehetséges ügyfelek használják ezt a termékoldali ajánlatot? beállításnál válassza ki az ajánlathoz használni kívánt lehetőséget.

- **Szerezze be most (ingyenes)** – Ajánlata ingyenesen elérhető az ügyfelek számára.
- **Ingyenes próbaverzió (termékoldal)** – Listázása az ügyfeleknek egy ingyenes próbaverzióra mutató hivatkozással. Az ajánlatlista ingyenes próbaverzióit a szolgáltatása hozta létre, kezeli és konfigurálta, és nem rendelkezik a Microsoft által kezelt előfizetésekkel.

    > [!NOTE]
    > Az alkalmazás a próbahivatkozáson keresztül kapott jogkivonatokkal csak a Azure Active Directory (Azure AD) használatával szerezheti be a felhasználói adatokat, így automatizálhatja a fiók létrehozását az alkalmazásban. A Microsoft-fiókok nem támogatottak a token használatával való hitelesítéshez.

- **Kapcsolatfelvétel –** Gyűjtse össze az ügyfél kapcsolattartási adatait az Ügyfélkapcsolat-kezelő (CRM) rendszer csatlakoztatásával. A rendszer engedélyt kér az ügyféltől az adataik megosztására. Ezeket az ügyféladatokat, valamint az ajánlat nevét, azonosítóját és az ajánlatot megtaláló Marketplace-forrást a konfigurált CRM-rendszernek küldi el a rendszer. A CRM konfigurálásával kapcsolatos további információkért lásd: [Érdeklődők .](#customer-leads)

## <a name="test-drive"></a>Teszt meghajtó

A teszt meghajtó remek módja annak, hogy bemutassa ajánlatát a potenciális ügyfeleknek, mert adott számú órán keresztül hozzáférést biztosít számukra egy előre konfigurált környezethez. A tesztverziós ajánlat magasabb konverziós rátát eredményez, és magas minősítésű érdeklődőket eredményez. További információ: Mi [az a teszt meghajtó?](what-is-test-drive.md).

> [!TIP]
> A teszt meghajtó eltér az ingyenes próbaverziótól. Kínálhat teszt meghajtót, ingyenes próbaverziót, vagy mindkettőt. Mindkettő adott időtartamra biztosítja az Ön megoldását az ügyfelek számára. A tesztmeghajtók azonban egy gyakorlati, önkiszolgáló bemutatót is tartalmaznak a termék legfontosabb funkcióiról és előnyeiről, amelyek egy valós megvalósítási forgatókönyvben bemutatva vannak.

Teszt meghajtó engedélyezéséhez jelölje be **a Teszt** meghajtó engedélyezése jelölőnégyzetet, és válassza a Teszt meghajtó típusa **lehetőséget.** A teszt meghajtót később fogja konfigurálni. A teszt meghajtóval az ajánlatot egy CRM-rendszerhez is konfigurálnia kell az ügyfelek érdeklődők számára (lásd a következő szakaszt). Ha el szeretné távolítani a teszt meghajtót az ajánlatból, törölje ezt a jelölőnégyzetet.

## <a name="customer-leads"></a>Érdeklődők az ügyfelek számára

[!INCLUDE [Connect lead management](partner-center-portal/includes/connect-lead-management.md)]

További információkért lásd a kereskedelmi [piactéri ajánlatában található érdeklődőket.](partner-center-portal/commercial-marketplace-get-customer-leads.md)

Válassza **a Piszkozat** mentése lehetőséget, mielőtt továbblép a bal oldali navigációs menü Tulajdonságok **lapfülére.**

## <a name="next-steps"></a>Következő lépések

- [Ajánlat tulajdonságainak konfigurálása](dynamics-365-customer-engage-properties.md)
- [Ajánlatlistákra vonatkozó ajánlott eljárások](gtm-offer-listing-best-practices.md)