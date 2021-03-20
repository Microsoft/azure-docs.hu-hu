---
title: A feltételes hozzáféréshez felügyelt eszköz szükséges – Azure Active Directory
description: Megtudhatja, hogyan konfigurálhat olyan Azure Active Directory (Azure AD) eszköz-alapú feltételes hozzáférési szabályzatokat, amelyek felügyelt eszközöket igényelnek a felhőalapú alkalmazásokhoz való hozzáféréshez.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 10/16/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: c11b58954eefda67f981d618b04ab2bd69fa6b43
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93077761"
---
# <a name="how-to-require-managed-devices-for-cloud-app-access-with-conditional-access"></a>Útmutató: felügyelt eszközök megkövetelése a Cloud app Accesshez feltételes hozzáféréssel

A mobil-első, Felhőbeli első világában Azure Active Directory (Azure AD) lehetővé teszi az alkalmazások és a szolgáltatások egyszeri bejelentkezését, bárhonnan. A jogosult felhasználók számos eszközről, például mobilról és személyes eszközökről is hozzáférhetnek a felhőalapú alkalmazásaihoz. Azonban számos környezetben van legalább néhány alkalmazás, amelyet csak olyan eszközök férhetnek hozzá, amelyek megfelelnek a biztonsági és megfelelőségi szabványoknak. Ezeket az eszközöket felügyelt eszközöknek is nevezzük. 

Ez a cikk azt ismerteti, hogyan konfigurálhat olyan feltételes hozzáférési szabályzatokat, amelyek a felügyelt eszközöket igénylik bizonyos felhőalapú alkalmazások eléréséhez a környezetben. 

## <a name="prerequisites"></a>Előfeltételek

A felügyelt eszközök Felhőbeli alkalmazásokhoz való hozzáférésének megkövetelése az **Azure ad feltételes hozzáférés** és az **Azure ad-eszközök felügyeletével** együtt. Ha még nem ismeri ezen területek valamelyikét, először olvassa el a következő témaköröket:

- **[Feltételes hozzáférés Azure Active Directoryban](./overview.md)** – ez a cikk a feltételes hozzáférés és a kapcsolódó terminológia fogalmi áttekintését tartalmazza.
- Az **[eszközkezelés bemutatása Azure Active Directoryban](../devices/overview.md)** – ez a cikk áttekintést nyújt azokról a különböző lehetőségekről, amelyekkel az eszközöket a szervezeti vezérlés alatt szerezheti be. 
- A **Windows 10-es Creators Update (1703-es verzió)** vagy újabb verziók Chrome-támogatásához telepítse a [Windows 10-es fiókok bővítményt](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji). Erre a bővítményre akkor van szükség, ha egy feltételes hozzáférési házirend eszköz-specifikus adatokat igényel.

>[!NOTE] 
> Azt javasoljuk, hogy az Azure AD-alapú feltételes hozzáférési szabályzat használatával a kezdeti eszköz hitelesítése után a legjobb kényszerítést kapja meg. Ez magában foglalja a záró munkameneteket, ha az eszköz nem felel meg a megfelelőségi és az eszköz kódjának.


## <a name="scenario-description"></a>Forgatókönyv leírása

A biztonság és a termelékenység egyensúlyának elsajátítása kihívást jelent. A támogatott eszközöknek a Felhőbeli erőforrások eléréséhez való elterjedése segíti a felhasználók termelékenységének növelését. A flip oldalon valószínűleg nem szeretné, hogy a környezet bizonyos erőforrásaihoz olyan eszközök férhessenek hozzá, amelyek ismeretlen védelmi szinttel rendelkeznek. Az érintett erőforrások esetében meg kell követelnie, hogy a felhasználók csak felügyelt eszköz használatával férhessenek hozzájuk. 

Az Azure AD feltételes hozzáférésével ezt a követelményt egyetlen, hozzáférést biztosító szabályzattal kezelheti:

- A kiválasztott felhőalapú alkalmazások
- A kiválasztott felhasználók és csoportok esetében
- Felügyelt eszköz megkövetelése

## <a name="managed-devices"></a>Felügyelt eszközök  

Egyszerű feltételek esetén a felügyelt eszközök olyan eszközök, amelyek *valamilyen* szervezeti szabályozás alatt állnak. Az Azure AD-ben a felügyelt eszköz előfeltétele, hogy regisztrálva legyen az Azure AD-ben. Az eszköz regisztrálása eszköz-objektum formájában hozza létre az eszköz identitását. Ezt az objektumot az Azure használja az eszköz állapotával kapcsolatos információk nyomon követésére. Azure AD-rendszergazdaként ezt az objektumot már használhatja az eszköz állapotának bekapcsolásához (engedélyezéséhez vagy letiltásához).
  
:::image type="content" source="./media/require-managed-devices/32.png" alt-text="Képernyőfelvétel az Azure-beli eszköz panelről D. az elemek engedélyezése és letiltása kiemelve." border="false":::

Az Azure AD-ben regisztrált eszköz beszerzéséhez három lehetősége van: 

- **Azure ad-beli regisztrált eszközök** – az Azure ad-ben regisztrált személyes eszközök beszerzése
- **Azure ad-hez csatlakoztatott eszközök** – olyan szervezeti Windows 10-es eszközök beszerzéséhez, amelyek nem csatlakoznak az Azure ad-ben regisztrált helyszíni ad-hez. 
- **Hibrid Azure ad-hez csatlakoztatott eszközök** – a Windows 10 vagy az Azure ad-ben regisztrált helyszíni ad-hez csatlakoztatott, régebbi verziójú eszköz beszerzése.

Ezt a három lehetőséget a cikk a [Mi az eszköz identitása](../devices/overview.md) című cikkben tárgyalja?

Felügyelt eszközként a regisztrált eszköznek egy **hibrid Azure ad-hez csatlakoztatott eszköznek** vagy egy **megfelelőként megjelölt eszköznek** kell lennie.  

:::image type="content" source="./media/require-managed-devices/47.png" alt-text="Képernyőkép az Azure A D engedélyezési paneljéről. A hozzáférés engedélyezése beállítás be van jelölve, ahogyan az eszközök megfelelőségének és A hibrid Azure A D-vel való csatlakozásának jelölőnégyzetei." border="false":::
 
## <a name="require-hybrid-azure-ad-joined-devices"></a>Hibrid Azure AD-hez csatlakoztatott eszközök megkövetelése

A feltételes hozzáférési szabályzatban kiválaszthatja a **hibrid Azure ad-hez csatlakoztatott eszköz megkövetelése** lehetőséget, hogy a kiválasztott felhőalapú alkalmazások csak felügyelt eszköz használatával legyenek elérhetők. 

:::image type="content" source="./media/require-managed-devices/10.png" alt-text="Képernyőkép az Azure A D engedélyezési paneljéről. A hozzáférés engedélyezése beállítás ki van választva. Az eszközök hibrid Azure-ra való csatlakoztatását igénylő jelölőnégyzet is be van jelölve." border="false":::

Ez a beállítás csak olyan Windows 10 vagy régebbi rendszerű eszközökre vonatkozik, mint a Windows 7 vagy a Windows 8, amelyek egy helyszíni AD-hez csatlakoznak. Ezeket az eszközöket csak hibrid Azure AD-csatlakozással regisztrálhatja az Azure AD-vel, amely egy [automatizált folyamat](../devices/hybrid-azuread-join-plan.md) , amellyel regisztrálható a Windows 10-es eszköz. 

:::image type="content" source="./media/require-managed-devices/45.png" alt-text="A név, az engedélyezett állapot, az O, a verzió, a csatlakozás típusa, a tulajdonos, a M D M és az eszköz megfelelő állapotának listázása. A megfelelő állapot nem." border="false":::

Mi teszi a hibrid Azure AD-hez csatlakoztatott eszköz felügyelt eszközét?  A helyszíni AD-hez csatlakoztatott eszközök esetében feltételezhető, hogy az eszközök vezérlését felügyeleti megoldások, például **Configuration Manager** vagy **Csoportházirend (GP)** használatával kényszerítik ki. Mivel az Azure AD nem tudja meghatározni, hogy a módszerek bármelyikét alkalmazták-e egy eszközre, a hibrid Azure AD-hez csatlakoztatott eszközök viszonylag gyenge mechanizmust igényelnek a felügyelt eszközök megköveteléséhez. Önnek rendszergazdaként kell megítélnie, hogy a helyszíni tartományhoz csatlakoztatott eszközökön alkalmazott módszerek elég erősek-e ahhoz, hogy felügyelt eszközöket képezzenek, ha az adott eszköz hibrid Azure AD-hez csatlakoztatott eszköz is.

## <a name="require-device-to-be-marked-as-compliant"></a>Eszköz megfelelőként való megjelölésének megkövetelése

Az *eszköz megfelelőként való megjelölésének* lehetősége a felügyelt eszközök igénylésének legerősebb formája.

:::image type="content" source="./media/require-managed-devices/11.png" alt-text="Képernyőkép az Azure A D engedélyezési paneljéről. A hozzáférés engedélyezése beállítás ki van választva. Az eszköz megfelelőként való megjelölését igénylő jelölőnégyzet is be van jelölve." border="false":::

Ehhez a beállításhoz az szükséges, hogy az eszköz regisztrálva legyen az Azure AD-ben, és a következő módon legyen megjelölve:
         
- Intune
- Egy külső gyártótól származó mobileszköz-felügyeleti (MDM) rendszer, amely az Azure AD-integráción keresztül kezeli a Windows 10-es eszközöket. A harmadik féltől származó MDM rendszerek nem Windows 10 rendszerű eszközökön használhatók.
 
:::image type="content" source="./media/require-managed-devices/46.png" alt-text="A név, az engedélyezett állapot, az O, a verzió, a csatlakozás típusa, a tulajdonos, a M D M és az eszköz megfelelő állapotának listázása. A megfelelőségi állapot ki van emelve." border="false":::

A megfelelőként megjelölt eszközök esetében feltételezhető, hogy: 

- A munkaerő által a vállalat adataihoz való hozzáféréshez használt mobileszközök kezelése
- A munkaerő által használt mobil alkalmazások kezelése
- A céges adatok védelméhez segítséget nyújt a munkatársak hozzáférésének és megosztásának szabályozása
- Az eszköz és az alkalmazásai megfelelnek a vállalat biztonsági követelményeinek

### <a name="scenario-require-device-enrollment-for-ios-and-android-devices"></a>Forgatókönyv: eszközök beléptetésének megkövetelése iOS-és Android-eszközökön

Ebben az esetben a contoso úgy döntött, hogy Microsoft 365 erőforrásokhoz való összes mobil hozzáférését regisztrált eszközt kell használnia. Az összes felhasználó már be van jelentkezni az Azure AD hitelesítő adataival, és rendelkezik a hozzájuk rendelt licenccel prémium szintű Azure AD P1 vagy P2 és Microsoft Intune.

A szervezeteknek a következő lépéseket kell végrehajtaniuk ahhoz, hogy megkövetelje a regisztrált mobileszköz használatát.

1. Jelentkezzen be a **Azure Portal** globális rendszergazdaként, biztonsági rendszergazdaként vagy feltételes hozzáférést biztosító rendszergazdaként.
1. Keresse meg **Azure Active Directory**  >  **biztonsági**  >  **feltételes hozzáférését**.
1. Válassza az **új szabályzat** lehetőséget.
1. Adjon nevet a szabályzatnak. Javasoljuk, hogy a szervezetek értelmes szabványt hozzanak létre a szabályzatok nevében.
1. A **hozzárendelések** alatt válassza a **felhasználók és csoportok** lehetőséget.
   1. A **Belefoglalás** területen válassza ki az **összes felhasználó** elemet, illetve azokat a **felhasználókat és csoportokat** , amelyekre alkalmazni szeretné a szabályzatot. 
   1. Válassza a **Kész** lehetőséget.
1. A **Cloud apps vagy a műveletek** területen  >  válassza az **Office 365** lehetőséget.
1. A **feltételek** területen válassza az **eszközök platformok** elemet.
   1. Állítsa **az** **Igen** értékre.
   1. **Android** és **iOS** is.
1. A **hozzáférés-vezérlés**  >  **megadása** területen válassza ki a következő beállításokat:
   - **Eszköz megfelelőként való megjelölésének megkövetelése**
1. Erősítse meg a beállításokat, és állítsa be az engedélyezési **szabályzatot** **bekapcsolva** értékre.
1. Válassza a **Létrehozás** lehetőséget a szabályzat létrehozásához és engedélyezéséhez.

### <a name="known-behavior"></a>Ismert viselkedés

Az [OAuth folyamat](../develop/v2-oauth2-device-code.md)használatakor a felügyelt eszközök engedélyezési vezérlésének vagy az eszköz állapotának megkövetelése nem támogatott. Ennek az az oka, hogy a hitelesítést végző eszköz nem tudja megadni az eszköz állapotát a kódot biztosító eszköz számára, és a tokenben lévő eszköz állapota zárolva van a hitelesítést végző eszközön. Használja helyette a multi-Factor Authentication engedélyezésének megkövetelése vezérlőt.

Windows 7, iOS, Android, macOS és néhány külső webböngészőben az Azure AD az eszközt az Azure AD-vel való regisztráláskor kiépített ügyféltanúsítvány használatával azonosítja. Amikor a felhasználó először jelentkezik be a böngészőben, a rendszer a felhasználótól kéri a tanúsítvány kiválasztását. A felhasználónak ki kell választania ezt a tanúsítványt, mielőtt továbbra is használhassa a böngészőt.

## <a name="next-steps"></a>Következő lépések

[Értékelje ki a feltételes hozzáférési szabályzatok hatását, mielőtt a rendszer széles körben engedélyezi a csak jelentési módot](concept-conditional-access-report-only.md).
