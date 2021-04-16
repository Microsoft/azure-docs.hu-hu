---
title: A feltételes hozzáférési szabályzatból kizárt felhasználók kezelése – Azure AD
description: Megtudhatja, hogyan használhatja Azure Active Directory (Azure AD) hozzáférési felülvizsgálatokat a feltételes hozzáférési szabályzatok hatálya alól kizárt felhasználók kezelésére
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
ms.openlocfilehash: d4b2ac36ad1140968fd17db0bed0b60a8aca6a02
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532675"
---
# <a name="use-azure-ad-access-reviews-to-manage-users-excluded-from-conditional-access-policies"></a>Az Azure AD hozzáférési felülvizsgálatok használata a feltételes hozzáférési szabályzatok hatálya alól kizárt felhasználók kezelésére

Egy ideális világban minden felhasználó követi a hozzáférési szabályzatokat a szervezet erőforrásaihoz való biztonságos hozzáférés érdekében. Előfordulhatnak azonban olyan üzleti esetek, amelyekhez kivételeket kell tenni. Ez a cikk néhány olyan helyzetet mutat be, amelyben kizárásra lehet szükség. Rendszergazdaként Kezelheti ezt a feladatot, elkerülheti a szabályzati kivételek felügyeletét, és igazolhatja az auditorok számára, hogy ezeket a kivételeket rendszeresen felülvizsgálják az Azure Active Directory (Azure AD) hozzáférési felülvizsgálatok használatával.

>[!NOTE]
> Az Azure AD prémium szintű Azure AD-hozzáférési felülvizsgálatok Enterprise Mobility + Security P2, Enterprise Mobility + Security E5 vagy próbaverziós licencre van szükség. További információ: Azure Active Directory [kiadása.](../fundamentals/active-directory-whatis.md)

## <a name="why-would-you-exclude-users-from-policies"></a>Miért zárna ki felhasználókat a szabályzatokból?

Tegyük fel, hogy rendszergazdaként úgy dönt, hogy az [Azure AD](../conditional-access/concept-conditional-access-policy-common.md) feltételes hozzáférés használatával követeli meg a többtényezős hitelesítést (MFA), és a hitelesítési kérelmeket adott hálózatokra vagy eszközökre korlátozza. Az üzembe helyezés tervezése során felismeri, hogy nem minden felhasználó felel meg ezeknek a követelményeknek. Előfordulhat például, hogy olyan felhasználókkal rendelkezik, akik távoli irodákból dolgoznak, nem pedig a belső hálózat részei. Előfordulhat, hogy a nem támogatott eszközökkel csatlakozó felhasználóknak is el kell fogadniuk az eszközök cseréjét. Röviden, a vállalatnak szüksége van ezekre a felhasználókra a bejelentkezéshez és a munkájukhoz, hogy kizárja őket a feltételes hozzáférési szabályzatból.

Egy másik példa, hogy [](../conditional-access/location-condition.md) a feltételes hozzáférésben elnevezett helyeket használ azon országok és régiók megadásához, amelyektől nem szeretné engedélyezni a felhasználók számára a bérlőjükhöz való hozzáférést.

![Elnevezett helyek a feltételes hozzáférésben](./media/conditional-access-exclusion/named-locations.png)

Sajnos egyes felhasználók továbbra is érvényes okkal jelentkeznek be ezekben a blokkolt országokban/régiókban. Előfordulhat például, hogy a felhasználók a munkahelyükre utaznak, és vállalati erőforrásokat kell elérniük. Ebben az esetben az ezen országok/régiók blokkolását biztosító feltételes hozzáférési szabályzat használhat felhőbiztonsági csoportot a szabályzatból kizárt felhasználók számára. Azok a felhasználók, akiknek utazás közben van szükségük hozzáférésre, hozzáadhatják magukat a csoporthoz az [Azure AD önkiszolgáló csoportkezelésének használatával.](../enterprise-users/groups-self-service-management.md)

Egy másik példa lehet az, hogy egy feltételes hozzáférési szabályzat blokkolja az örökölt hitelesítést a felhasználók [túlnyomó többsége számára.](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/07/azure-ad-conditional-access-support-for-blocking-legacy-auth-is-in-public-preview/) Ha azonban vannak olyan felhasználói, akik örökölt hitelesítési módszereket kell használniuk az erőforrások Office 2010- vagy IMAP-/SMTP-/POP-alapú ügyfeleken keresztüli eléréséhez, kizárhatja ezeket a felhasználókat a régi hitelesítési módszereket letiltó házirendből.

>[!NOTE]
>A Microsoft határozottan javasolja, hogy a biztonsági helyzet javítása érdekében tiltsa le az örökölt protokollok használatát a bérlőben.

## <a name="why-are-exclusions-challenging"></a>Miért nehéz a kirekesztés?

Az Azure AD-ban a feltételes hozzáférési szabályzatok felhasználók egy halmazának hatókörét is beállíthatja. Kizárásokat az Azure AD-szerepkörök, egyéni felhasználók vagy vendégek kiválasztásával is konfigurálhat. Ne feledje, hogy kizárások konfigurálásakor a szabályzat szándéka nem kényszeríthető ki kizárt felhasználókra. Ha a kizárások felhasználók listájával vagy örökölt helyszíni biztonsági csoportokkal vannak konfigurálva, a kizárások korlátozottan lesznek láthatóak. Ennek eredményeképpen:

- Előfordulhat, hogy a felhasználók nem tudják, hogy ki vannak zárva.

- A felhasználók a szabályzat megkerülése érdekében csatlakozhatnak a biztonsági csoporthoz.

- Előfordulhat, hogy a kizárt felhasználók korábban jogosultak a kizárásra, de előfordulhat, hogy már nem jogosultak rá.

A kizárás első konfigurálásakor gyakran előfordul, hogy a szabályzatot megkerülő felhasználók listája rövid listára kerül. Idővel egyre több felhasználó lesz hozzáadva a kizáráshoz, és nő a lista. Egy bizonyos ponton át kell vizsgálnia a listát, és meg kell erősítenie, hogy ezek a felhasználók továbbra is jogosultak a kizárásra. Technikai szempontból viszonylag egyszerű lehet a kizárási lista kezelése, de ki hozza meg az üzleti döntéseket, és hogyan győződhet meg arról, hogy mind naplózható? Ha azonban a kizárást egy Azure AD-csoporttal konfigurálja, a hozzáférési felülvizsgálatokat kompenzáló vezérlőként használhatja a láthatóság érdekében, és csökkentheti a kizárt felhasználók számát.

## <a name="how-to-create-an-exclusion-group-in-a-conditional-access-policy"></a>Kizárási csoport létrehozása feltételes hozzáférési szabályzatban

Kövesse az alábbi lépéseket egy új Azure AD-csoport és egy feltételes hozzáférési szabályzat létrehozásához, amely nem vonatkozik az adott csoportra.

### <a name="create-an-exclusion-group"></a>Kizárási csoport létrehozása

1. Jelentkezzen be az Azure Portalra.

2. A bal oldali navigációs sávon **kattintson** a Azure Active Directory majd a **Csoportok elemre.**

3. A felső menüben kattintson az Új **csoport elemre a** csoport panel megnyitásához.

4. A Csoport **típusa listában** válassza a Biztonság **lehetőséget.** Adjon meg egy nevet és egy leírást.

5. Ügyeljen arra, hogy a Tagság **típusa legyen** **Hozzárendelt.**

6. Válassza ki azokat a felhasználókat, akik a kizárási csoport tagjai lesznek, majd kattintson a **Létrehozás gombra.**

![Új csoport panel a Azure Active Directory](./media/conditional-access-exclusion/new-group.png)

### <a name="create-a-conditional-access-policy-that-excludes-the-group"></a>Hozzon létre egy feltételes hozzáférési szabályzatot, amely kizárja a csoportot

Most létrehozhat egy feltételes hozzáférési szabályzatot, amely ezt a kizárási csoportot használja.

1. A bal oldali navigációs sávon kattintson **a** Azure Active Directory majd a **Feltételes** hozzáférés elemre a Szabályzatok **panel megnyitásához.**

2. Kattintson **az Új szabályzat elemre** az Új panel **megnyitásához.**

3. Adjon meg egy nevet.

4. A Hozzárendelések alatt kattintson **a Felhasználók és csoportok elemre.**

5. A **Beszúrás lapon** válassza a **Minden felhasználó lehetőséget.**

6. A Kizárás **lapon jelölje** be a Felhasználók és csoportok jelölőnégyzetet, majd **kattintson** a Kizárt felhasználók **kiválasztása elemre.**

7. Válassza ki a létrehozott kizárási csoportot.

   > [!NOTE] 
   > Ajánlott eljárásként ajánlott kizárni legalább egy rendszergazdai fiókot a szabályzatból a tesztelés során, hogy biztosan ne legyen kizárva a bérlőből.

8. Folytassa a feltételes hozzáférési szabályzat szervezeti követelmények alapján való beállításával.

![A Kizárt felhasználók panel kiválasztása a Feltételes hozzáférés panelen](./media/conditional-access-exclusion/select-excluded-users.png)
  
Két példát mutatunk be, amelyekben hozzáférési felülvizsgálatokkal kezelheti a kizárásokat a feltételes hozzáférési szabályzatok használatával.

## <a name="example-1-access-review-for-users-accessing-from-blocked-countriesregions"></a>1. példa: Hozzáférési felülvizsgálat a letiltott országból/régiókból hozzáférő felhasználók számára

Tegyük fel, hogy rendelkezik egy feltételes hozzáférési szabályzatot, amely letiltja bizonyos országok/régiók hozzáférését. Tartalmaz egy csoportot, amely ki van zárva a szabályzatból. Íme egy javasolt hozzáférési felülvizsgálat, ahol a csoport tagjait felülvizsgálják.

> [!NOTE] 
> A globális rendszergazda vagy felhasználói rendszergazdai szerepkör szükséges a hozzáférési felülvizsgálatok létrehozásához.

1. A felülvizsgálat minden héten megtörténik.

2. Soha nem ér véget annak érdekében, hogy a kizárási csoport mindig a legfrissebb maradjon.

3. A csoport minden tagja a felülvizsgálat hatókörében lesz.

4. Minden felhasználónak igazolnia kell, hogy továbbra is szüksége van hozzáférésre ezekről a blokkolt országokról/régiókról, ezért továbbra is a csoport tagjainak kell lennie.

5. Ha a felhasználó nem válaszol a felülvizsgálati kérésre, a rendszer automatikusan eltávolítja a csoportból, és a továbbiakban nem lesz hozzáférése a bérlőhöz, miközben az adott országokra/régiókra utazik.

6. Engedélyezze az e-mail-értesítéseket, hogy a felhasználók értesítést küldenek a hozzáférési felülvizsgálat kezdetével és befejezésével kapcsolatban.

    ![Hozzáférési felülvizsgálati panel létrehozása ( 1. példa)](./media/conditional-access-exclusion/create-access-review-1.png)

## <a name="example-2-access-review-for-users-accessing-with-legacy-authentication"></a>2. példa: Hozzáférési felülvizsgálat az örökölt hitelesítéssel hozzáférő felhasználók számára

Tegyük fel, hogy rendelkezik egy feltételes hozzáférési szabályzattal, amely letiltja az örökölt hitelesítést és régebbi ügyfélverziókat használó felhasználók hozzáférését, és tartalmaz egy, a szabályzatból kizárt csoportot. Íme egy javasolt hozzáférési felülvizsgálat, ahol a csoport tagjait felülvizsgálják.

1. Ennek a felülvizsgálatnak ismétlődő felülvizsgálatnak kell lennie.

2. A csoport minden tagja felülvizsgálatra lenne szükség.

3. Konfigurálható úgy, hogy a kiválasztott felülvizsgálóként sorolja fel az üzleti egységek tulajdonosait.

4. Alkalmazza automatikusan az eredményeket, és távolítsa el a nem jóváhagyott felhasználókat, hogy továbbra is használják az örökölt hitelesítési módszereket.

5. Hasznos lehet, ha javaslatokat tesz, hogy a nagy csoportok felülvizsgálói könnyen meghozhatják a döntéseiket.

6. Engedélyezze az e-mail-értesítéseket, hogy a felhasználók értesítést kapnak a hozzáférési felülvizsgálat kezdetével és befejezésével kapcsolatban.

    ![Hozzáférési felülvizsgálati panel létrehozása a 2. példához](./media/conditional-access-exclusion/create-access-review-2.png)

>[!IMPORTANT] 
>Ha számos kizárási csoporttal rendelkezik, és ezért több hozzáférési felülvizsgálatot kell létrehoznia, most már rendelkezik egy API-val a Microsoft Graph beta végpontban, amely lehetővé teszi azok programozott módon történő létrehozását és kezelését. Első lépésekért tekintse meg az [Azure AD](/graph/api/resources/accessreviewsv2-root?view=graph-rest-beta&preserve-view=true) hozzáférési felülvizsgálatok API-referenciáját és az Azure AD hozzáférési felülvizsgálatok lekért példáját az [Microsoft Graph.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/td-p/236096)

## <a name="access-review-results-and-audit-logs"></a>Hozzáférés-ellenőrzési eredmények és auditnaplók

Most, hogy mindene megvan, a csoport, a feltételes hozzáférési szabályzat és a hozzáférési felülvizsgálatok, ideje figyelni és nyomon követni az értékelés eredményeit.

1. A Azure Portal nyissa meg **a Hozzáférési felülvizsgálatok panelt.**

2. Nyissa meg a kizárási csoport kezeléséhez létrehozott vezérlőt és programot.

3. Az **Eredmények elemre** kattintva láthatja, hogy ki maradjon a listában, és ki lett eltávolítva.

    ![A hozzáférési felülvizsgálatok eredményei azt mutatják, hogy ki lett jóváhagyva](./media/conditional-access-exclusion/access-reviews-results.png)

4. Ezután kattintson az **Auditnaplók** elemre a felülvizsgálat során végzett műveletek megtekintéséhez.

    ![Hozzáférési felülvizsgálatok auditnapló-listázási műveletek](./media/conditional-access-exclusion/access-reviews-audit-logs.png)

Rendszergazdaként tudja, hogy a kizárási csoportok szabályzatokkal való kezelése néha elkerülhetetlen. Az Azure AD hozzáférési felülvizsgálatokkal azonban egyszerűbbé válik ezeknek a csoportoknak a karbantartása és rendszeres áttekintése a vállalat tulajdonosa vagy maguk a felhasználók számára, valamint a változások naplózása.

## <a name="next-steps"></a>Következő lépések

- [Csoportok vagy alkalmazások hozzáférési felülvizsgálatának létrehozása](create-access-review.md)
- [Mi az a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)