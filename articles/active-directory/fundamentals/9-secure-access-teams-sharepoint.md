---
title: Biztonságos külső hozzáférés a Microsoft Teams, a SharePoint és a OneDrive szolgáltatáshoz Azure Active Directory
description: Microsoft 365 szolgáltatásokhoz való biztonságos hozzáférés a teljes külső hozzáférési biztonság részeként.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f373c849cd542769fec7a10f20dad04e742c9591
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102565138"
---
# <a name="secure-external-access-to-microsoft-teams-sharepoint-and-onedrive-for-business"></a>Biztonságos külső hozzáférés a Microsoft Teams, a SharePoint és a OneDrive for Business szolgáltatáshoz 

A Microsoft Teams, a SharePoint és a OneDrive for Business három olyan legelterjedtebb módszer, amellyel a külső felhasználókkal közösen dolgozhat és oszthat meg tartalmakat. Ha a "jóváhagyott" metódusok túlságosan korlátozóak, a felhasználók a jóváhagyott metódusokon kívül esnek a tartalom e-mailben történő elküldésével, vagy nem biztonságos külső folyamatok és alkalmazások (például személyes DropBox vagy OneDrive) beállításával. A cél az, hogy egyensúlyt kell biztosítania a biztonsági igényeknek a könnyű együttműködés révén.

Ebből a cikkből megtudhatja, hogyan határozhat meg és konfigurálhat külső együttműködést a Microsoft Teams és a SharePoint szolgáltatással való üzleti céljaihoz.

## <a name="governance-begins-in-azure-active-directory"></a>A szabályozás kezdete Azure Active Directory

A Microsoft 365 megosztását részben a [külső identitások szabályozzák | Külső együttműködési beállítások](https://aad.portal.azure.com/) a Azure Active Directoryban (Azure ad). Ha a külső megosztás le van tiltva vagy korlátozott az Azure AD-ben, akkor felülbírálja a Microsoft 365 konfigurált megosztási beállításokat. Ez alól kivételt képez, hogy ha az Azure AD B2B-integráció nincs engedélyezve, a SharePoint és a OneDrive is konfigurálható úgy, hogy az egyszeri jelszó (OTP) használatával támogassa az alkalmi megosztást.

![Képernyőkép a külső együttműködési beállításokról](media/secure-external-access/9-external-collaboration-settings.png)

### <a name="guest-user-access"></a>Vendégfelhasználói hozzáférés

Három választási lehetőség áll rendelkezésre a vendég felhasználói hozzáféréshez, amely azt szabályozza, hogy a vendég felhasználók Mikor láthatják a meghívást. 

Ha meg szeretné akadályozni, hogy a vendég felhasználói megtekinthessék a többi vendég felhasználó adatait, és a csoporttagság enumerálása is megtörténjen, válassza a vendég felhasználók számára a címtár-objektumok tulajdonságainak és tagságának korlátozott elérését.

### <a name="guest-invite-settings"></a>Vendég Meghívási beállításai

Ezek a beállítások határozzák meg, hogy kik hívhatják meg a vendégeket, és hogyan hívhatják meg a vendégeket. Ezek a beállítások csak akkor engedélyezettek, ha a B2B-integráció engedélyezve van.

Azt javasoljuk, hogy engedélyezze a rendszergazdák és felhasználók számára a vendég Meghívási szerepkörét. Ez a beállítás lehetővé teszi a szabályozott együttműködési folyamatok beállítását, ahogy az az alábbi példában is látható.

* A csapat tulajdonosa elküld egy jegyet a vendég meghívó szerepkörhöz, és

   * Minden vendég meghívásért felelős lesz.

   * Beleegyezik abba, hogy ne adjon hozzá közvetlenül felhasználókat a mögöttes SharePointhoz

   * A szolgáltatás a rendszeres hozzáférési felülvizsgálatok elvégzésére, valamint a szükséges hozzáférés visszavonására alkalmas.

* Középen a következő műveleteket végzi el

   * Lehetővé teszi a külső megosztást a betanítás befejezését követően a kért szerepkör megadásával.

   * Hozzárendeli az Azure AD P2-licencet a Microsoft 365 csoport tulajdonosához a hozzáférési felülvizsgálatok engedélyezéséhez.
   * Létrehoz egy Microsoft 365 csoportos hozzáférési felülvizsgálatot.

   * Ellenőrzi, hogy a hozzáférési felülvizsgálatok történnek-e.

   * A közvetlenül a mögöttes SharePointhoz hozzáadott felhasználókat távolítja el.

 Állítsa be **az E-mail egyszeri PIN-kód engedélyezése a vendégeknek (előzetes verzió) és a vendég önkiszolgáló bejelentkezés engedélyezése felhasználói folyamatokon keresztül** **Igen értéket**. Ez a beállítás az Azure AD külső együttműködési beállításaival való integráció előnyeit veszi igénybe.

### <a name="collaboration-restrictions"></a>Együttműködési korlátozások

Az együttműködési korlátozások között három lehetőség közül választhat. Az üzleti követelmények határozzák meg, hogy melyiket kell választania.

* A **meghívások bármely tartományba való küldésének engedélyezése** : bármely felhasználó meghívható az együttműködésre.

* **A megadott tartományokra irányuló meghívások megtagadása** azt jelenti, hogy a felhasználón kívüli felhasználók is meghívhatják az együttműködést.

* **A meghívások engedélyezése csak a megadott tartományokban** azt jelenti, hogy a megadott tartományokon kívüli bármely felhasználó nem hívható meg. 

## <a name="govern-access-in-teams"></a>Hozzáférés szabályozása a Teams szolgáltatásban

[A csapatok különbséget tesznek a külső felhasználók (a szervezeten kívüli személyek) és a vendég felhasználók (a vendég fiókok) között](/microsoftteams/communicate-with-users-from-other-organizations?WT.mc_id=TeamsAdminCenterCSH%e2%80%8b). Az együttműködési beállításokat a [csapatok felügyeleti portálján](https://admin.teams.microsoft.com/company-wide-settings/external-communications) , a szervezeti szintű beállítások területen kezelheti. 

> [!NOTE]
> A Azure Active Directory külső identitások együttműködési beállításai szabályozzák a hatályos engedélyeket. Növelheti a csapatok korlátozásait, de nem csökkentheti őket az Azure AD-ben beállított értékekkel.

* **Külső hozzáférési beállítások**. Alapértelmezés szerint a csoportok engedélyezik a külső hozzáférést, ami azt jelenti, hogy a szervezet képes kommunikálni az összes külső tartománnyal. Ha csak a csapatok számára szeretné korlátozni vagy engedélyezni a megadott tartományokat, ezt itt teheti meg.

* **Vendég hozzáférés**. A vendég hozzáférés szabályozza, hogy a vendég felhasználói milyen műveleteket végezhetnek a csapatokban.

A külső hozzáférés csapatokban való kezelésével kapcsolatos további tudnivalókért tekintse meg a következő forrásokat.

* [Külső hozzáférés kezelése a Microsoft Teams szolgáltatásban](/microsoftteams/manage-external-access)

* [Microsoft 365 Identity models és Azure Active Directory](/microsoft-365/enterprise/about-microsoft-365-identity)

* [Identitási modellek és hitelesítés a Microsoft Teams szolgáltatásban](/MicrosoftTeams/identify-models-authentication)

* [A Microsoft Teams érzékenységi címkéi](/MicrosoftTeams/sensitivity-labels)

## <a name="govern-access-in-sharepoint-and-onedrive"></a>Hozzáférés szabályozása a SharePointban és a OneDrive

A SharePoint-rendszergazdák számos beállítással érhetők el az együttműködéshez. Az egész szervezetre kiterjedő beállításokat a SharePoint felügyeleti központból kezelheti. A beállítások az egyes SharePoint-webhelyekhez módosíthatók. Javasoljuk, hogy a szervezetre kiterjedő beállítások a minimálisan szükséges biztonsági szinteken legyenek, és szükség esetén növelje a biztonságot az adott helyeken. Magas kockázatú projekt esetében például előfordulhat, hogy bizonyos tartományokra korlátozni szeretné a felhasználókat, és le kell tiltania a tagok meghívásának lehetőségét.

### <a name="integrating-sharepoint-and-one-drive-with-azure-ad-b2b"></a>A SharePoint és az egyik meghajtó integrálása az Azure AD B2B-vel

A külső együttműködés szabályozásának általános stratégiájának részeként javasoljuk, hogy [engedélyezze a SharePoint-és OneDrive-integráció előzetes verzióját az Azure ad B2B-szel](/sharepoint/sharepoint-azureb2b-integration-preview) .

Az Azure AD B2B biztosítja a vendég felhasználók hitelesítését és felügyeletét. A SharePoint és a OneDrive integrációja esetén az [Azure ad B2B egyszeri PIN-kód](../external-identities/one-time-passcode.md) a fájlok, mappák, listaelemek, dokumentumtárak és webhelyek külső megosztására használatos. Ez a szolgáltatás frissített élményt nyújt a meglévő [biztonságos külső megosztási felhasználói felületről](/sharepoint/what-s-new-in-sharing-in-targeted-release).

> [!NOTE]
> Ha engedélyezi az Azure AD B2B-integráció előzetes verzióját, akkor a SharePoint és a OneDrive megosztását az Azure AD szervezeti kapcsolatok beállításai szabályozzák, például a **tagok meghívhatják** és **meghívhatják a vendégeket is**.

### <a name="sharing-policies"></a>Házirendek megosztása

A *külső megosztás* beállítható a SharePoint és a OneDrive esetében is. A OneDrive korlátozása nem lehet nagyobb, mint a SharePoint-beállítások.

 ![A SharePoint és a OneDrive külső megosztási beállításainak képernyőképe](media/secure-external-access/9-sharepoint-settings.png)

Az Azure AD B2B-vel való SharePoint-integráció megváltoztatja, hogy a vezérlőelemek hogyan működnek együtt a fiókokkal.

* **Bárki**. Nem ajánlott

   * Az integrációs állapottól függetlenül, a bárkire mutató hivatkozások engedélyezése esetén a rendszer nem alkalmazza az Azure-szabályzatokat az ilyen típusú hivatkozások használata esetén. 

   * A szabályozott együttműködés egyik forgatókönyvében ne engedélyezze ezt a funkciót. 
   > [!NOTE]
   > Előfordulhat, hogy olyan helyzetet talál, ahol engedélyezni szeretné ezt a beállítást egy adott helyhez, ebben az esetben pedig itt engedélyezheti, és megadhatja a nagyobb korlátozást az egyes helyeken.

* **Új és meglévő vendégek**. Ajánlott, ha engedélyezve van az integráció.

   * **Az Azure ad B2B-integráció** engedélyezése esetén az új és a meglévő vendégeknek egy Azure ad B2B-fiókkal kell rendelkezniük, amely az Azure ad-szabályzatokkal kezelhető.

   * Az **Azure ad B2B-integráció engedélyezése nélkül** az új vendégeknek nem lesz létrehozva Azure ad B2B-fiókja, és nem kezelhetők az Azure ad-ből. Annak megadása, hogy a meglévő vendégek rendelkeznek-e Azure AD B2B-fiókkal, a vendég létrehozásának módjától függ.

* **Meglévő vendégek**. Akkor ajánlott, ha nincs engedélyezve az integráció.

   * Ezzel a beállítással a felhasználók csak a címtárában lévő más felhasználókkal oszthatnak meg.

* **Csak a szervezeten belüli személyek**. Nem ajánlott, ha a külső felhasználókkal való együttműködésre van szükség.

   * Az integrációs állapottól függetlenül a felhasználók csak a szervezetben lévő felhasználókkal oszthatnak meg felhasználókat. 

* **A külső megosztás korlátozása tartomány alapján**. Alapértelmezés szerint a SharePoint engedélyezi a külső hozzáférést, ami azt jelenti, hogy a megosztás minden külső tartomány esetében engedélyezett. Ha korlátozni vagy engedélyezni szeretné a SharePoint-alapú adott tartományokat, itt is megteheti.

* **Csak bizonyos biztonsági csoportokban lévő felhasználók számára engedélyezze a külső megosztást**.  Ez a beállítás korlátozza, hogy kik oszthatnak meg tartalmat a SharePointban és a OneDrive, míg az Azure AD-beli beállítás minden alkalmazásra érvényes. Ha korlátozni szeretné a megosztható megosztást, akkor lehet hasznos, ha szeretné megkövetelni, hogy a felhasználók betanítsák a biztonságos megosztást, majd a befejezést követően vegye fel őket a jóváhagyott megosztási biztonsági csoportba. Ha ez a beállítás be van jelölve, és a felhasználók nem férhetnek hozzá a "jóváhagyott megosztó" kifejezéshez, előfordulhat, hogy nem jóváhagyott módszereket találnak a megosztáshoz. 

* **Lehetővé teszi a vendégek számára a nem saját elemek megosztását**. Javasoljuk, hogy hagyja letiltva.

* Az **ellenőrző kódokat használó felhasználóknak ennyi nap után kell újrahitelesíteniük magukat (az alapértelmezett érték 30)**. Javasoljuk, hogy engedélyezze ezt a beállítást.

### <a name="access-controls"></a>Hozzáférés-vezérlés

A hozzáférés-vezérlés beállítás a szervezet összes felhasználóját érinteni fogja. Mivel előfordulhat, hogy nem tudja szabályozni, hogy a külső felhasználók rendelkeznek-e megfelelő eszközökkel, itt nem fogjuk kezelni ezeket a vezérlőket. 

* **Tétlen munkamenet-kijelentkezés**. Javasoljuk, hogy engedélyezze ezt a vezérlőt, amely lehetővé teszi, hogy a nem felügyelt eszközökön egy adott időtartam után figyelmeztesse és kijelentkezzen a felhasználókat. Beállíthatja a tétlenségi időszakot és a figyelmeztetést. 

* **Hálózati hely**. A vezérlő beállítása azt jelenti, hogy csak a szervezet tulajdonában lévő IP-címeket engedélyezheti a hozzáféréshez. Külső csoportmunka-forgatókönyvekben ezt csak akkor állítsa be, ha az összes külső partner csak a hálózaton belül vagy a VPN-en keresztül fér hozzá az erőforrásokhoz.

### <a name="file-and-folder-links"></a>Fájlok és mappák hivatkozásai

A SharePoint felügyeleti központban azt is beállíthatja, hogy a fájl-és mappa-hivatkozások hogyan legyenek megosztva. Ezeket a beállításokat az egyes helyekhez is megadhatja. 

 ![A fájl-és mappa-hivatkozás beállításainak képernyőképe](media/secure-external-access/9-file-folder-links.png)

Ha engedélyezte az Azure AD B2B-integrációt, a fájlok és mappák megosztásával a szervezeten kívüli felhasználók a fájlok és a mappák megosztásakor a VÁLLALATKÖZI felhasználót fogják létrehozni.

Azt javasoljuk, hogy az alapértelmezett hivatkozási típust **csak a szervezeten belüli személyeknek** állítsa be, a **szerkesztéshez** pedig az alapértelmezett engedélyeket. Így biztosíthatja, hogy az elemek elgondolkodva legyenek megosztva. Ezután testre szabhatja ezt a beállítást az egyes helyekhez tartozó alapértelmezett beállításokhoz, amelyek megfelelnek az adott együttműködési igényeknek.

### <a name="anyone-links"></a>Bárki hivatkozás

Nem javasoljuk, hogy engedélyezzen bárkire mutató hivatkozásokat. Ha ezt teszi, javasoljuk, hogy állítson be egy lejáratot, és ügyeljen rá, hogy megtekintse az engedélyeket. Ha úgy dönt, hogy csak a fájlok és mappák engedélyeinek megtekintése lehetőséget választja, a felhasználók nem tudják módosítani a szerkesztési jogosultságokat tartalmazó hivatkozásokat.

Ha többet szeretne megtudni a SharePoint külső elérésének szabályozásáról, tekintse meg a következőket:

* [A SharePoint külső megosztásának áttekintése](/sharepoint/external-sharing-overview)

* [SharePoint-és OneDrive-integráció az Azure AD B2B-vel](/sharepoint/sharepoint-azureb2b-integration-preview)

#### <a name="next-steps"></a>Következő lépések

Tekintse meg a következő cikkeket az erőforrásokhoz való külső hozzáférés biztonságossá tételéhez. Javasoljuk, hogy a felsorolt sorrendben végezze el a műveleteket.

1. [A külső hozzáférés biztonsági helyzetének meghatározása](1-secure-access-posture.md)

2. [Aktuális állapot felderítése](2-secure-access-current-state.md)

3. [Irányítási terv létrehozása](3-secure-access-plan.md)

4. [Biztonsági csoportok használata](4-secure-access-groups.md)

5. [Áttérés az Azure AD B2B-re](5-secure-access-b2b.md)

6. [Biztonságos hozzáférés a jogosultságok kezelésével](6-secure-access-entitlement-managment.md)

7. [Biztonságos hozzáférés feltételes hozzáférési szabályzatokkal](7-secure-access-conditional-access.md)

8. [Biztonságos hozzáférés érzékenységi címkékkel](8-secure-access-sensitivity-labels.md)

9. [Biztonságos hozzáférés a Microsoft Teams, a OneDrive és a SharePoint](9-secure-access-teams-sharepoint.md) rendszerhez (itt van.)