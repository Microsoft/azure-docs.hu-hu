---
title: Dynamics 365 for Customer Engagement ajánlat Előfeltételek |} Az Azure Marketplace-en
description: Az előfeltételek az Azure-alkalmazások közzététele az Azure Marketplace-en kínálnak.
services: Dynamics 365 for Customer Engagement offer, Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: pabutler
ms.openlocfilehash: 0b14180c894977d822aa30ea5f46a2e21e247dc1
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64942319"
---
# <a name="dynamics-365-for-customer-engagement-prerequisites"></a>Dynamics 365 for Customer Engagement Előfeltételek

Ez a cikk egy Dynamics 365 Customer Engagement-alkalmazás ajánlat az AppSource-piactér közzétételi technikai és üzleti előfeltételeit ismerteti.  Ha még nem tette meg, tekintse át a [Office 365-höz, a Dynamics 365, a PowerApps és a Power BI ajánlat közzétételi útmutató](../../appsource-offer-publishing-guide.md).


## <a name="technical-requirements"></a>Technikai követelmények

A Dynamics 365 Customer Engagement-alkalmazás meg kell felelnie a [Microsoft AppSource app felülvizsgálati irányelveit](https://smp-cdn-prod.azureedge.net/documents/AppsourceGuidelines/Microsoft%20AppSource%20app%20review%20guidelines_v5.pdf), amely tartalmazza az alábbi követelményeknek:


|              Követelmény             |        Leírás           |
|            ---------------           |      ---------------         |
| Azure Active Directory-integráció   | Az alkalmazás engedélyeznie kell az Azure Active Directory összevont egyszeri bejelentkezés (AAD összevont egyszeri bejelentkezés) hozzájárult engedélyezve van. További információkért lásd: [beszerzése az AppSource Certified for Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-get-appsource-certified). |
| Integráció a Microsoft Cloud services (nem kötelező) | Ez a funkció szükség, ha az alkalmazás egyéb Microsoft Cloud services, mint például a machine learning a Microsoft Power BI, a Microsoft Flow vagy a Microsoft Azure services vagy a cognitive services kell integrálható. |
| A fókuszban lévő üzleti            |  Az alkalmazás kell összpontosíthat olyan jól definiált üzleti folyamat vagy probléma elsődlegesen üzleti ügyfelek és engedélyezése a felhasználók számára, hogy jelentkezzen be a munkahelyi hitelesítő adatokat (felhasználónév és jelszó).  |
| Ingyenes próbaidőszak és próbaverziója |  Egy ügyfél az alkalmazás korlátozott ideig ingyenes használatához képesnek kell lennie: a "Letöltés most" az ingyenes alkalmazásai, egy "ingyenes" a megadott időtartam, a "Próbálja ki a" demonstrator vagy a beállítás a "velem a kapcsolatot" kérelem.  |
| Nem/alacsony konfiguráció                 | Az alkalmazás könnyen és gyorsan konfigurálása és beállítása (nem szükséges testreszabás vagy fejlesztési) kell lennie.  |
| Ügyfélszolgálat                     | Az alkalmazás használniuk kell egy támogatási hivatkozás, amelyben ügyfelek megtalálják a segítséget a támogatása.  |
| Rendelkezésre állás/üzemidő                  | Az alkalmazás legalább 99,9 %-os hasznos üzemidővel kell rendelkeznie. |
|  |  |


## <a name="business-requirements"></a>Üzleti feltételek

Az üzleti követelmények a következők a következő eljárási, és a jogi szerződéses kötelezettségek:

* Regisztrálva kell lennie a a [Microsoft Partner Network (MPN)](https://partners.microsoft.com/PartnerProgram/simplifiedenrollment.aspx) regisztrált Felhőbeli piactér kiadói el. Ha nincs regisztrálva, kövesse a [válnak a Felhőbeli piactér kiadói](../../become-publisher.md).  (A harmadik lépés, használja inkább a [AppSource partner jelölési űrlapot](https://appsource.microsoft.com/partners/signup)). 

    >[!NOTE]
    >A Cloud Partner portálra való bejelentkezéshez ugyanazt a Microsoft Developer Center regisztrációs fiókot kell használnia. Az Azure piactér-i ajánlatainak közzétételéhez egyetlen Microsoft-fiókkal kell rendelkeznie. Ez a fiók nem lehet adott egyes szolgáltatásokat vagy ajánlatokat.

* Appsource-ban nem kínál a kereskedelmi engedélyezett közzétételi lehetőség, mert az aktuális rendezés és a számlázási infrastruktúra kell használnia további redundáns tárterületről és módosítások nélkül.
* Ön felelős az ügyfelek számára elérhetővé tétele az műszaki támogatást, olyan üzletileg ésszerű módon. Ez a támogatás ingyenes, fizetős, vagy közösségi megközelítések keresztül lehet.
* Ön felelős a szoftver- és külső függőségek licencelése.
* Érdemes hozott létre a társított marketinganyag, például egy hivatalos alkalmazás neve, leírása (HTML formátumban), embléma rendszerképeket PNG formátum (40 x 40, 90 x 90, 115 x 115 és 255 x 115 képpont), és a használati feltételeket és adatvédelmi szabályzata.  


## <a name="next-steps"></a>További lépések

Miután teljesítette ezeket a követelményeket, [egy Dynamics 365 Customer Engagement ajánlat létrehozása](./cpp-create-offer.md) 
