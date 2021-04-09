---
title: Mik azok a hozzáférési felülvizsgálatok? – Azure Active Directory | Microsoft Docs
description: A Azure Active Directory hozzáférési felülvizsgálatok segítségével szabályozhatja a csoporttagság és az alkalmazások elérését, hogy megfeleljen a szervezet irányítási, kockázatkezelési és megfelelőségi kezdeményezéseinek.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.subservice: compliance
ms.date: 10/29/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.custom: contperf-fy21q1
ms.openlocfilehash: 22bfef17f68b2e83e4f7462d8e9af6fb8aacc284
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98246740"
---
# <a name="what-are-azure-ad-access-reviews"></a>Mik azok az Azure AD-hozzáférési felülvizsgálatok?

A Azure Active Directory (Azure AD) hozzáférési felülvizsgálatok lehetővé teszik a szervezetek számára a csoporttagságok hatékony kezelését, a vállalati alkalmazások elérését és a szerepkör-hozzárendeléseket. A felhasználó hozzáférését rendszeresen felül lehet vizsgálni, hogy csak a megfelelő személyek férhessenek hozzájuk.

Íme egy videó, amely a hozzáférési felülvizsgálatok gyors áttekintését nyújtja:

>[!VIDEO https://www.youtube.com/embed/kDRjQQ22Wkk]

## <a name="why-are-access-reviews-important"></a>Miért fontos a hozzáférési felülvizsgálatok?

Az Azure AD lehetővé teszi, hogy a szervezeten belüli és külső felhasználókkal közösen működjön együtt a felhasználókkal. A felhasználók csatlakozhatnak a csoportokhoz, meghívhatják a vendégeket, csatlakozhatnak a felhőalapú alkalmazásokhoz, és távolról dolgozhatnak a munkahelyi vagy személyes eszközökről. Az önkiszolgáló szolgáltatás használatának kényelme a jobb hozzáférés-kezelési képességek szükségességét eredményezte.

- Ahogy az új alkalmazottak csatlakoznak, hogyan gondoskodnak arról, hogy rendelkezzenek a hatékony munkavégzéshez szükséges hozzáféréssel?
- Ahogy a csapatok áthelyezik a csapatot, vagy elhagyják a vállalatot, hogyan gondoskodnak arról, hogy a régi hozzáférésük el lett távolítva?
- A túlzott hozzáférési jogosultságok a biztonsághoz vezethetnek.
- A túlzott hozzáférési jogosultság is vezethet a vizsgálati eredményekhez, mivel azok nem szabályozzák a hozzáférést.
- Proaktív módon kell felvennie az erőforrás-tulajdonosokat annak érdekében, hogy rendszeresen ellenőrizzék, ki férhet hozzá az erőforrásaihoz.

## <a name="when-should-you-use-access-reviews"></a>Mikor érdemes használni a hozzáférési felülvizsgálatokat?

- **Túl sok felhasználó van a Kiemelt szerepkörökben:** Érdemes megfigyelni, hogy hány felhasználó rendelkezik rendszergazdai hozzáféréssel, hány globális rendszergazda, és ha vannak olyan meghívott vendégek vagy partnerek, akik nem lettek eltávolítva a felügyeleti feladatok elvégzése után. A szerepkör-hozzárendelési felhasználókat újra hitelesítheti az [Azure ad-szerepkörökben](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) , például a globális rendszergazdák vagy az [Azure-erőforrások szerepkörei](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) , például a felhasználói hozzáférés RENDSZERGAZDÁJA a [Azure ad Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) felületén.
- **Ha az automatizálás nem lehetséges:** Szabályokat hozhat létre a biztonsági csoportokhoz vagy Microsoft 365 csoportokhoz tartozó dinamikus tagsághoz, de mi történik, ha a HR-információ nem az Azure AD-ban van, vagy ha a felhasználóknak továbbra is hozzáférésre van szükségük a csoport elhagyása után Ezután létrehozhat egy felülvizsgálatot az adott csoporton annak biztosítása érdekében, hogy a hozzáférést igénylő felhasználók továbbra is hozzáférhessenek.
- Egy **csoport új célra való használata esetén:** Ha olyan csoporttal rendelkezik, amely szinkronizálva lesz az Azure AD-vel, vagy ha azt tervezi, hogy engedélyezi az alkalmazás Salesforce a mindenki számára az értékesítési csoport csoportban, akkor érdemes megkérnie a csoport tulajdonosát, hogy tekintse át a csoport tagságát, mielőtt a csoportot más kockázati tartalomban használják.
- **Üzleti szempontból kritikus fontosságú adathozzáférés:** bizonyos erőforrások esetében előfordulhat, hogy meg kell kérnie az azon kívüli személyeket, hogy rendszeresen kijelentkezzenek, és indokolják, hogy miért van hozzáférésük a naplózáshoz.
- **A szabályzatok kivételi listájának fenntartása:** Az ideális világban minden felhasználó a hozzáférési házirendeket követve gondoskodik a szervezet erőforrásaihoz való hozzáférésről. Bizonyos esetekben azonban előfordulhat, hogy kivételeket kell elvégeznie. Rendszergazdaként kezelheti ezt a feladatot, elkerülheti a házirend-kivételek felügyeletét, és biztosíthatja a könyvvizsgálók számára, hogy a kivételeket rendszeresen felülvizsgálják.
- **Kérje meg a csoport tulajdonosait, hogy erősítse meg, hogy továbbra is szükségük van a csoportok** tagjaira: Előfordulhat, hogy az alkalmazottak hozzáférése automatizálható a helyszíni identitás-és hozzáférés-kezelés (IAM), de nem meghívott vendégek számára. Ha egy csoport hozzáférést biztosít a vendégek számára az üzleti szempontból kényes tartalmakhoz, akkor a tulajdonos feladata annak megerősítése, hogy a vendégek még mindig jogos üzleti igényekkel rendelkeznek.
- **Az értékelések rendszeres időközönként ismétlődnek:** A felhasználók ismétlődő hozzáférési felülvizsgálatok állíthatók be, például hetente, havonta, negyedévente vagy évente, a felülvizsgálók pedig az egyes felülvizsgálatok elején lesznek értesítve. A felülvizsgálók elfogadják vagy megtagadhatják a hozzáférést egy felhasználóbarát felülettel és az intelligens javaslatok segítségével.

>[!NOTE]
>Ha készen áll a hozzáférési felülvizsgálatok kipróbálására, tekintse meg a [csoportok vagy alkalmazások hozzáférési felülvizsgálatának létrehozását ismertetőt](create-access-review.md) .

## <a name="where-do-you-create-reviews"></a>Hol hozhat létre felülvizsgálatokat?

Attól függően, hogy mit szeretne áttekinteni, az Azure AD hozzáférési felülvizsgálatok, az Azure ad Enterprise-alkalmazások (előzetes verzió) vagy az Azure AD PIM használatával hozhatja létre a hozzáférési felülvizsgálatot.

| Felhasználók hozzáférési jogai | A felülvizsgálók lehetnek | Felülvizsgálat létrehozva | Felülvizsgáló felülete |
| --- | --- | --- | --- |
| Biztonsági csoport tagjai</br>Office-csoporttagok | Megadott véleményezők</br>Csoport tulajdonosainak</br>Önálló felülvizsgálat | Azure AD hozzáférési felülvizsgálatok</br>Azure AD-csoportokkal | Hozzáférési panel |
| Hozzárendelve egy csatlakoztatott alkalmazáshoz | Megadott véleményezők</br>Önálló felülvizsgálat | Azure AD hozzáférési felülvizsgálatok</br>Azure AD Enterprise-alkalmazások (előzetes verzió) | Hozzáférési panel |
| Azure AD-szerepkör | Megadott véleményezők</br>Önálló felülvizsgálat | [Azure AD PIM](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Azure Portal |
| Azure-erőforrás szerepkör | Megadott véleményezők</br>Önálló felülvizsgálat | [Azure AD PIM](../privileged-identity-management/pim-resource-roles-start-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Azure Portal |

## <a name="license-requirements"></a>Licenckövetelmények

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

### <a name="how-many-licenses-must-you-have"></a>Hány licencre van szükség?

A címtár legalább annyi prémium szintű Azure AD P2-licencet igényel, mint a következő feladatokat végző alkalmazottak száma:

-   Azok a felhasználók, akik felülvizsgálók vannak hozzárendelve
-   Az önellenőrzést végző tagok felhasználói
-   A tagok a hozzáférési felülvizsgálatot végző csoport tulajdonosai
-   A felhasználók mint a hozzáférési felülvizsgálatot végző alkalmazások tulajdonosai

A vendég felhasználói számára a licencelési igények a használt licencelési modelltől függenek. Az alábbi vendég felhasználói tevékenységek azonban prémium szintű Azure AD P2-használatnak tekintendők:

-   Felülvizsgáló hozzárendelt vendég felhasználók
-   Önellenőrzést végző vendég felhasználók
-   Hozzáférési felülvizsgálatot végző csoport tulajdonosai
-   A vendég felhasználói mint a hozzáférési felülvizsgálatot végző alkalmazások tulajdonosai


Prémium szintű Azure AD P2-licencek **nem** szükségesek a globális rendszergazdai vagy felhasználói rendszergazdai szerepkörrel rendelkező felhasználók számára, akik hozzáférési felülvizsgálatokat állítanak be, konfigurálják a beállításokat, vagy alkalmazhatják a döntéseket a felülvizsgálatok alapján.

Az Azure AD vendég felhasználói hozzáférése havi aktív felhasználók (MAU) számlázási modellen alapul, amely az 1:5 arányú számlázási modellt váltja fel. További információ: az [Azure ad külső identitások díjszabása](../external-identities/external-identities-pricing.md).

További információ a licencekről: [licencek kiosztása vagy eltávolítása a Azure Active Directory portál használatával](../fundamentals/license-users-groups.md).

### <a name="example-license-scenarios"></a>Példa licencelési forgatókönyvekre

Íme néhány példa a licencekre, amelyek segítségével meghatározhatja a szükséges licencek számát.

| Eset | Számítás | Licencek száma |
| --- | --- | --- |
| A rendszergazda létrehoz egy hozzáférési felülvizsgálatot az A csoportban a 75-felhasználók és 1 csoport tulajdonosa számára, és a csoport tulajdonosát a véleményezőként rendeli hozzá. | 1 a csoport tulajdonosának licence felülvizsgáló | 1 |
| A rendszergazda az 500-es és 3 csoportbeli tulajdonosok hozzáférési felülvizsgálatát hozza létre a B csoport számára, és a 3 csoport tulajdonosait felülvizsgáló rendeli hozzá. | 3 licenc minden csoport tulajdonosának felülvizsgáló | 3 |
| A rendszergazda 500 felhasználóval hoz létre hozzáférési felülvizsgálatot a B csoport számára. Saját felülvizsgálatot tesz lehetővé. | 500 licenc az egyes felhasználókhoz, mint önfelülvizsgáló | 500 |
| A rendszergazda a C csoport hozzáférési felülvizsgálatát 50 tag felhasználókkal és 25 vendég felhasználóval hozza létre. Saját felülvizsgálatot tesz lehetővé. | 50 licenc az egyes felhasználókhoz, mint önfelülvizsgáló. * | 50 |
| A rendszergazda a D csoporthoz tartozó hozzáférési felülvizsgálatot hoz létre 6 tagú felhasználóval és 108 vendég felhasználóval. Saját felülvizsgálatot tesz lehetővé. | 6 licenc minden felhasználóhoz, mint önfelülvizsgáló. A vendég felhasználóinak havi aktív felhasználó (MAU) alapján kell fizetniük. Nincs szükség további licencekre. *  | 6 |

\* Az Azure AD külső identitások (vendég felhasználó) díjszabása a havi aktív felhasználók (MAU) alapján történik, amely a naptári hónapon belül a hitelesítési tevékenységgel rendelkező egyedi felhasználók száma. Ez a modell a 1:5 arányú számlázási modellt váltja fel, amely legfeljebb öt vendéget engedélyez a bérlő minden prémium szintű Azure AD licence számára. Ha a bérlő egy előfizetéshez van csatolva, és külső identitási funkciókat használ a vendég felhasználókkal való együttműködésre, akkor a MAU-alapú számlázási modell alapján automatikusan számlázunk. További információ: az Azure AD külső identitások számlázási modellje.

## <a name="next-steps"></a>Következő lépések

- [Csoportok vagy alkalmazások hozzáférési felülvizsgálatának létrehozása](create-access-review.md)
- [Hozzáférési felülvizsgálat létrehozása Azure AD rendszergazdai szerepkörrel rendelkező felhasználókhoz](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)
- [Csoportok vagy alkalmazások hozzáférésének ellenőrzése](perform-access-review.md)
- [Csoportok vagy alkalmazások hozzáférési felülvizsgálatának befejezése](complete-access-review.md)
