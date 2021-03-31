---
title: A feltételes hozzáférési szabályzatokból kizárt felhasználók kezelése – Azure AD
description: Ismerje meg, hogyan használhatja a Azure Active Directory (Azure AD) hozzáférési felülvizsgálatokat a feltételes hozzáférési házirendből kizárt felhasználók kezeléséhez
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 426e28048ae370919529ea710717a3a3867d999d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97746252"
---
# <a name="use-azure-ad-access-reviews-to-manage-users-excluded-from-conditional-access-policies"></a>Az Azure AD hozzáférési felülvizsgálatok használata a feltételes hozzáférési házirendből kizárt felhasználók felügyeletéhez

Az ideális világban minden felhasználó a hozzáférési szabályzatokat követve gondoskodik a szervezet erőforrásaihoz való hozzáférésről. Bizonyos esetekben azonban előfordulhat, hogy kivételeket kell elvégeznie. Ez a cikk néhány példát mutat be olyan helyzetekben, amikor szükség lehet a kizárásra. Ön, mint rendszergazda, kezelheti ezt a feladatot, elkerülheti a házirend-kivételek felügyeletét, és igazolhatja, hogy ezek a kivételek rendszeresen áttekinthetők Azure Active Directory (Azure AD) hozzáférési felülvizsgálatok használatával.

>[!NOTE]
> Az Azure AD hozzáférési felülvizsgálatok használatához érvényes prémium szintű Azure AD P2, Enterprise Mobility + Security E5 fizetett vagy próbaverziós licenc szükséges. További információ: [Azure Active Directory kiadások](../fundamentals/active-directory-whatis.md).

## <a name="why-would-you-exclude-users-from-policies"></a>Miért zárja ki a felhasználókat a szabályzatokból?

Tegyük fel, hogy rendszergazdaként úgy dönt, hogy az [Azure ad feltételes hozzáférését](../conditional-access/concept-conditional-access-policy-common.md) használja a többtényezős hitelesítés (MFA) megköveteléséhez, és korlátozza a hitelesítési kérelmeket bizonyos hálózatokra vagy eszközökre. Az üzembe helyezés megtervezése során kiderül, hogy nem minden felhasználó tud megfelelni ezekkel a követelményeknek. Előfordulhat például, hogy a távoli irodákból dolgozó felhasználók nem a belső hálózat részét képezik. Előfordulhat, hogy a nem támogatott eszközök használatával csatlakozó felhasználókat is el kell fogadnia, miközben az eszközök cseréjére várnak. Röviden, az üzleti igényeknek megfelelően a felhasználóknak be kell jelentkezniük, és el kell végezniük a munkájukat, hogy a feltételes hozzáférési szabályzatokból kizárják őket.

Egy másik példa, hogy a feltételes hozzáférés [nevesített helyeinek](../conditional-access/location-condition.md) használatával megadhatja azokat az országokat és régiókat, amelyekről nem kívánja engedélyezni a felhasználók számára a bérlőhöz való hozzáférést.

![Nevesített helyszínek a feltételes hozzáférésben](./media/conditional-access-exclusion/named-locations.png)

Sajnos előfordulhat, hogy egyes felhasználók továbbra is érvényes okból jelentkezhetnek be ezekből a letiltott országokból/régiókból. Előfordulhat például, hogy a felhasználók munkavégzésre és a vállalati erőforrásokhoz való hozzáférésre van szükségük. Ebben az esetben az ezen országokat/régiókat blokkoló feltételes hozzáférési szabályzat egy Felhőbeli biztonsági csoportot használhat a kizárt felhasználók számára a szabályzatból. Azok a felhasználók, akiknek utazás közben kell hozzáférni, hozzáadhatják magukat a csoporthoz az [Azure ad önkiszolgáló csoport kezelése](../enterprise-users/groups-self-service-management.md)használatával.

Egy másik példa lehet arra, hogy egy feltételes hozzáférési szabályzat [blokkolja az örökölt hitelesítést a felhasználók túlnyomó többsége számára](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/07/azure-ad-conditional-access-support-for-blocking-legacy-auth-is-in-public-preview/). Ha azonban vannak olyan felhasználók, akik örökölt hitelesítési módszereket használnak az erőforrások Office 2010 vagy IMAP/SMTP/POP-alapú ügyfeleken keresztüli eléréséhez, akkor kizárhatja ezeket a felhasználókat a régi hitelesítési módszereket blokkoló szabályzatból.

>[!NOTE]
>A Microsoft nyomatékosan javasolja, hogy az örökölt protokollok használatát letiltsa a bérlőben a biztonsági helyzet javítása érdekében.

## <a name="why-are-exclusions-challenging"></a>Miért vitatják meg a kizárásokat?

Az Azure AD-ben egy feltételes hozzáférési szabályzatot is beállíthat a felhasználók körére. A kizárásokat az Azure AD-szerepkörök, egyéni felhasználók vagy vendégek lehetőség kiválasztásával is konfigurálhatja. Ne feledje, hogy ha a kizárások konfigurálva vannak, a szabályzat célja nem kényszeríthető ki a kizárt felhasználókra. Ha a kizárások a felhasználók listájával vagy az örökölt helyszíni biztonsági csoportok használatával vannak konfigurálva, a kizárások korlátozottan láthatók lesznek. Ennek eredményeképpen:

- Előfordulhat, hogy a felhasználók nem tudják, hogy kizárják őket.

- A felhasználók csatlakozhatnak a biztonsági csoporthoz, hogy megkerüljék a szabályzatot.

- A kizárt felhasználók jogosultak lehetnek a kizárásra, de előfordulhat, hogy a továbbiakban nem jogosultak rá.

Gyakran előfordul, hogy amikor először konfigurálja a kizárást, a szabályzat megkerülését végző felhasználók listáját láthatja. Az idő múlásával egyre több felhasználó kerül be a kizárásba, és a lista növekszik. Egy ponton át kell tekintenie a listát, és meg kell győződnie arról, hogy ezek a felhasználók továbbra is jogosultak a kizárásra. A kizárási lista a technikai szempontból viszonylag egyszerű, de az üzleti döntések meghozatalát is lehetővé teszi, és hogyan gondoskodik arról, hogy az összes naplózható legyen? Ha azonban egy Azure AD-csoport használatával konfigurálja a kizárást, a hozzáférési felülvizsgálatokat kompenzáló vezérlőként, a láthatóság eléréséhez és a kizárt felhasználók számának csökkentéséhez használhatja.

## <a name="how-to-create-an-exclusion-group-in-a-conditional-access-policy"></a>Kizárási csoport létrehozása feltételes hozzáférési házirendben

Az alábbi lépéseket követve hozzon létre egy új Azure AD-csoportot és egy feltételes hozzáférési szabályzatot, amely nem vonatkozik erre a csoportra.

### <a name="create-an-exclusion-group"></a>Kizárási csoport létrehozása

1. Jelentkezzen be az Azure Portalra.

2. A bal oldali navigációs sávon kattintson a **Azure Active Directory** , majd a **csoportok** elemre.

3. A felső menüben kattintson az **új csoport** elemre a csoport ablaktábla megnyitásához.

4. A **csoport típusa** listában válassza a **Biztonság** elemet. Adja meg a nevet és a leírást.

5. Ügyeljen arra, hogy a **tagság** típusa legyen **hozzárendelve**.

6. Válassza ki a kizárási csoport részét képező felhasználókat, majd kattintson a **Létrehozás** gombra.

![Új csoport ablaktábla Azure Active Directory](./media/conditional-access-exclusion/new-group.png)

### <a name="create-a-conditional-access-policy-that-excludes-the-group"></a>Hozzon létre egy feltételes hozzáférési szabályzatot, amely kizárja a csoportot

Mostantól létrehozhat egy olyan feltételes hozzáférési szabályzatot, amely ezt a kizárási csoportot használja.

1. A bal oldali navigációs sávon kattintson a **Azure Active Directory** elemre, majd kattintson a **feltételes hozzáférés** elemre a **házirendek** panel megnyitásához.

2. Az **új** ablaktábla megnyitásához kattintson az **új házirend** elemre.

3. Adjon meg egy nevet.

4. A hozzárendelések területen kattintson a **felhasználók és csoportok** elemre.

5. A **beágyazás** lapon válassza a **minden felhasználó** lehetőséget.

6. A **kizárás** lapon jelölje be a **felhasználók és csoportok** jelölőnégyzetet, majd kattintson a **kizárt felhasználók kiválasztása** lehetőségre.

7. Válassza ki a létrehozott kizárási csoportot.

   > [!NOTE] 
   > Ajánlott eljárásként célszerű kizárni legalább egy rendszergazdai fiókot a szabályzatból, ha tesztelni szeretné, hogy ne zárja ki a bérlőből.

8. Folytassa a feltételes hozzáférési házirend beállításával a szervezeti követelmények alapján.

![A feltételes hozzáférés területen válassza a kizárt felhasználók ablaktáblát](./media/conditional-access-exclusion/select-excluded-users.png)
  
Ismerkedjen meg két példával, amelyekkel a hozzáférési felülvizsgálatok segítségével kezelheti a feltételes hozzáférési szabályzatok kizárásait.

## <a name="example-1-access-review-for-users-accessing-from-blocked-countriesregions"></a>1. példa: a letiltott országokból/régiókból hozzáférő felhasználók hozzáférési felülvizsgálata

Tegyük fel, hogy van egy feltételes hozzáférési szabályzata, amely blokkolja bizonyos országok/régiók hozzáférését. Tartalmaz egy csoportot, amely ki van zárva a szabályzatból. Az alábbiakban egy ajánlott hozzáférési felülvizsgálatot talál, ahol a csoport tagjait vizsgálják felül.

> [!NOTE] 
> Hozzáférési felülvizsgálatok létrehozásához globális rendszergazdai vagy felhasználói rendszergazdai szerepkörre van szükség.

1. A felülvizsgálat minden héten megtörténik.

2. Soha nem fejeződik be, hogy a legnaprakészebb legyen a kizárási csoport.

3. A csoport minden tagja a felülvizsgálat hatókörében lesz.

4. Minden felhasználónak meg kell határoznia, hogy továbbra is szüksége van a blokkolt országokból/régiókból való hozzáférésre, ezért a csoport tagjának is kell lennie.

5. Ha a felhasználó nem válaszol a felülvizsgálati kérelemre, a rendszer automatikusan eltávolítja őket a csoportból, és nem fog tudni hozzáférni a bérlőhöz, miközben ezen országokba/régiókba utaznak.

6. Engedélyezheti az e-mailes értesítéseket, hogy a felhasználók tisztában legyenek a hozzáférési felülvizsgálat kezdetével és befejezésével.

    ![Hozzáférés-felülvizsgálati ablaktábla létrehozása az 1. példában](./media/conditional-access-exclusion/create-access-review-1.png)

## <a name="example-2-access-review-for-users-accessing-with-legacy-authentication"></a>2. példa: a korábbi hitelesítéssel hozzáférő felhasználók hozzáférési felülvizsgálata

Tegyük fel, hogy van egy feltételes hozzáférési szabályzata, amely blokkolja a felhasználók hozzáférését a régi hitelesítéssel és a régebbi verziójú ügyfélprogramokkal, és olyan csoportot tartalmaz, amely ki van zárva a szabályzatból. Az alábbiakban egy ajánlott hozzáférési felülvizsgálatot talál, ahol a csoport tagjait vizsgálják felül.

1. A felülvizsgálatnak ismétlődő felülvizsgálatra van szüksége.

2. A csoport összes tagját felül kell vizsgálni.

3. Konfigurálható úgy, hogy az üzleti egység tulajdonosait kilistázza a kiválasztott felülvizsgálók számára.

4. Automatikusan alkalmazza az eredményeket, és távolítsa el azokat a felhasználókat, akik nem hagyták jóvá a korábbi hitelesítési módszerek használatát.

5. Hasznos lehet a javaslatok lehetővé tétele, hogy a nagyméretű csoportok áttekintő tagjai könnyedén megtudják a döntéseiket.

6. E-mail-értesítések engedélyezése, hogy a felhasználók értesítést kapjanak a hozzáférési felülvizsgálat megkezdéséről és befejezéséről.

    ![Hozzáférési felülvizsgálati ablaktábla létrehozása a 2. példához](./media/conditional-access-exclusion/create-access-review-2.png)

>[!IMPORTANT] 
>Ha sok kizárási csoporttal rendelkezik, és ezért több hozzáférési felülvizsgálatra van szükség, most már van egy API a Microsoft Graph Beta-végponton, amely lehetővé teszi a programozott módon történő létrehozását és kezelését. Első lépésként tekintse meg az [Azure ad hozzáférési felülvizsgálatok API-referenciáját](/graph/api/resources/accessreviews-root?view=graph-rest-beta) , és [példát az Azure ad hozzáférési felülvizsgálatok Microsoft Graph használatával történő lekérésére](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/td-p/236096).

## <a name="access-review-results-and-audit-logs"></a>Hozzáférési felülvizsgálati eredmények és naplók

Most, hogy mindent megtesz a hely, a csoport, a feltételes hozzáférési szabályzat és a hozzáférési felülvizsgálatok terén, itt az idő, hogy figyelje és nyomon kövesse a felülvizsgálatok eredményeit.

1. A Azure Portal nyissa meg a **hozzáférési felülvizsgálatok** panelt.

2. Nyissa meg a kizárási csoport kezeléséhez létrehozott vezérlőt és programot.

3. Az **eredmények** lehetőségre kattintva megtekintheti, hogy ki hagyta jóvá a listát, és ki lett távolítva.

    ![A hozzáférési felülvizsgálatok eredményeinek megjelenítése, akit jóváhagytak](./media/conditional-access-exclusion/access-reviews-results.png)

4. Ezután kattintson a **naplók** elemre a felülvizsgálat során végrehajtott műveletek megtekintéséhez.

    ![Hozzáférési felülvizsgálatok naplófájljainak listázása műveletek](./media/conditional-access-exclusion/access-reviews-audit-logs.png)

Rendszergazdaként biztos lehet abban, hogy a kizárási csoportok a szabályzatokhoz való kezelése időnként elkerülhetetlen. A csoportok karbantartásával azonban rendszeresen, a vállalat tulajdonosa vagy a felhasználók is megtekinthetik őket, és az Azure AD hozzáférési felülvizsgálatokkal könnyebben ellenőrizhetők a változások.

## <a name="next-steps"></a>Következő lépések

- [Csoportok vagy alkalmazások hozzáférési felülvizsgálatának létrehozása](create-access-review.md)
- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)