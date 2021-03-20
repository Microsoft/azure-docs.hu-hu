---
title: Enterprise State Roaming GYIK – Azure Active Directory
description: Gyakori kérdések az ESR-ről
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb889298a09c30a629c69442ebf31bc735af31d1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96173124"
---
# <a name="settings-and-data-roaming-faq"></a>Beállítások és adatroaming GYIK

Ez a cikk bizonyos kérdésekre ad választ, amelyekkel a rendszergazdák a beállítások és az alkalmazásadatok szinkronizálásával rendelkezhetnek.

## <a name="what-data-roams"></a>Milyen adatközpontok vannak?

**Windows-beállítások**: a Windows operációs rendszerbe beépített számítógép-beállítások. Ezek általában olyan beállítások, amelyek személyre szabják a számítógépet, és a következő széles kategóriákat tartalmazzák:

* *Téma*, amely magában foglalja az asztali témát és a tálca beállításait is.
* *Az Internet Explorer beállításai* az utoljára megnyitott lapokkal és kedvencekkel együtt.
* A *Microsoft Edge böngésző beállításai*, például a kedvencek és az olvasások listája.
* *Jelszavak*, például internetes jelszavak, Wi-Fi profilok és egyebek.
* *Nyelvi beállítások*, beleértve egyebek között a billentyűzetkiosztások beállításait, a rendszer nyelvét, és a dátum és idő beállításait.
* *Könnyű kezelés funkciók*, például kontrasztos témák, Narrátor és Nagyító.
* *Egyéb Windows-beállítások*, például az egér beállításai.

> [!NOTE]
> Ez a cikk a Microsoft Edge korábbi HTML-alapú böngészőre vonatkozik, amely a Windows 10 2015-es verziójában indult el. A cikk nem vonatkozik az új, 2020. január 15-én kiadott Microsoft Edge Chromium-alapú böngészőre. Az új Microsoft Edge szinkronizálási működésével kapcsolatos további információkért tekintse meg a [Microsoft Edge Sync](/deployedge/microsoft-edge-enterprise-sync)című cikket.

**Alkalmazásadatok**: az univerzális Windows-alkalmazások a beállításokat egy barangoló mappába írhatják, és a mappába írt összes adattal automatikusan szinkronizálva lesznek. Az egyes alkalmazás-fejlesztőknek köszönhetően az alkalmazások kihasználhatják ezt a képességet. A barangolást használó univerzális Windows-alkalmazások fejlesztésével kapcsolatos további információkért tekintse meg az [AppData Storage API](/windows/uwp/design/app-settings/store-and-retrieve-app-data) és a [Windows 8 AppData barangolás fejlesztői blogját](https://blogs.windows.com/windowsdeveloper/2016/05/04/roaming-app-data-and-the-user-experience/).

## <a name="what-account-is-used-for-settings-sync"></a>Milyen fiókot használ a beállítások szinkronizálásához?

A Windows 8,1 rendszerben a beállítások szinkronizálása mindig a fogyasztói Microsoft-fiókokat használja. A vállalati felhasználók a Active Directory tartományi fiókjához Microsoft-fiók csatlakozhatnak a beállítások szinkronizálásának eléréséhez. A Windows 10 rendszerben ez a csatlakoztatott Microsoft-fiók funkció egy elsődleges/másodlagos fiók keretrendszerével van lecserélve.

Az elsődleges fiók a Windowsba való bejelentkezéshez használt fiókként van meghatározva. Ez lehet egy Microsoft-fiók, egy Azure Active Directory (Azure AD) fiók, egy helyszíni Active Directory fiók vagy egy helyi fiók. Az elsődleges fiókon kívül a Windows 10-es felhasználók egy vagy több másodlagos felhőalapú fiókot is hozzáadhatnak az eszközhöz. A másodlagos fiók általában egy Microsoft-fiók, egy Azure AD-fiók vagy egy másik fiók, például a Gmail vagy a Facebook. Ezek a másodlagos fiókok olyan további szolgáltatásokhoz biztosítanak hozzáférést, mint az egyszeri bejelentkezés és a Windows áruház, de nem képesek a beállítások szinkronizálásának bekapcsolására.

A Windows 10-es verzióban csak az eszköz elsődleges fiókja használható a beállítások szinkronizálásához (lásd: [Hogyan verziófrissítés Microsoft-fiók beállítások szinkronizálása a Windows 8 rendszerben Azure ad-beállítások szinkronizálása a Windows 10](enterprise-state-roaming-faqs.md#how-do-i-upgrade-from-microsoft-account-settings-sync-in-windows-8-to-azure-ad-settings-sync-in-windows-10)rendszerben).

Az eszközön lévő különböző felhasználói fiókok nem keverik az adathalmazt. A beállítások szinkronizálásának két szabálya van:

* A Windows-beállítások mindig az elsődleges fiókkal lesznek barangolva.
* Az alkalmazásadatok az alkalmazás megvásárlásához használt fiókkal lesznek felcímkézve. Csak az elsődleges fiókkal címkézett alkalmazások lesznek szinkronizálva. Az alkalmazás tulajdonosi címkézésének meghatározása akkor történik meg, ha egy alkalmazás a Windows áruházban vagy a mobileszköz-kezelésen (MDM) keresztül van betöltve.

Ha egy alkalmazás tulajdonosa nem azonosítható, az elsődleges fiókkal fog barangolni. Ha egy eszköz a Windows 8 vagy a Windows 8,1 rendszerről a Windows 10-es verzióra van frissítve, az összes alkalmazás a Microsoft-fiók által beszerzettként lesz megjelölve. Ennek az az oka, hogy a legtöbb felhasználó a Windows áruházon keresztül szerzi be az alkalmazásokat, és a Windows 10 előtt nem támogatott az Azure AD-fiókok Windows áruházbeli támogatása. Ha egy alkalmazás offline licenccel van telepítve, az alkalmazás az eszköz elsődleges fiókjának használatával lesz címkézve.

> [!NOTE]
> A vállalati tulajdonban lévő és az Azure AD-hez csatlakozó Windows 10-es eszközök többé nem tudnak a Microsoft-fiókjaikat tartományi fiókhoz csatlakoztatni. Microsoft-fiók csatlakoztatása egy tartományi fiókhoz, és az összes felhasználó adatszinkronizálása a Microsoft-fiók (azaz a csatlakoztatott Microsoft-fiók és Active Directory funkciókon keresztüli Microsoft-fiók roaming) el lesz távolítva a csatlakoztatott Active Directory vagy Azure AD-környezethez csatlakozó Windows 10-es eszközökről.

## <a name="how-do-i-upgrade-from-microsoft-account-settings-sync-in-windows-8-to-azure-ad-settings-sync-in-windows-10"></a>Hogyan a Windows 8 rendszerről az Azure AD-beállítások szinkronizálására a Windows 10 rendszerben Microsoft-fiók beállítások szinkronizálásával?

Ha a Windows 8,1 rendszert futtató Active Directory tartományhoz csatlakoztatott Microsoft-fiók csatlakozik, a beállításokat a Microsoft-fiókon keresztül szinkronizálja. A Windows 10-es verzióra való frissítés után továbbra is szinkronizálja a felhasználói beállításokat Microsoft-fiók, ha Ön egy tartományhoz csatlakoztatott felhasználó, és a Active Directory tartomány nem csatlakozik az Azure AD-hez.

Ha a helyszíni Active Directory tartomány csatlakozik az Azure AD-hez, az eszköz a csatlakoztatott Azure AD-fiókkal kísérli meg a beállítások szinkronizálását. Ha az Azure AD-rendszergazda nem engedélyezi a Enterprise State Roaming, a csatlakoztatott Azure AD-fiók nem fogja megszüntetni a beállítások szinkronizálását. Ha Ön Windows 10 rendszerű felhasználó, és Azure AD-identitással jelentkezik be, akkor a Windows-beállítások szinkronizálása azonnal megtörténik, amint a rendszergazda engedélyezi a beállítások szinkronizálását az Azure AD-n keresztül.

Ha a vállalati eszközön bármilyen személyes adatait tárolta, vegye figyelembe, hogy a Windows operációs rendszer és az alkalmazásadatok megkezdik az Azure AD-vel való szinkronizálást. Ez az alábbi következményekkel jár:

* A személyes Microsoft-fiók beállításai a munkahelyi vagy iskolai Azure AD-fiókok beállításaitól függetlenül lesznek elsodródva. Ennek az az oka, hogy a Microsoft-fiók és az Azure AD-beállítások szinkronizálása mostantól külön fiókokat használ.
* Az Azure AD-n keresztül szinkronizáljuk a személyes adatokat, például a Wi-Fi jelszavakat, a webes hitelesítő adatokat és az Internet Explorer azon kedvenceit, amelyek korábban szinkronizálva lettek egy csatlakoztatott Microsoft-fiók használatával.

## <a name="how-do-microsoft-account-and-azure-ad-enterprise-state-roaming-interoperability-work"></a>Hogyan működik a Microsoft-fiók és az Azure AD Enterprise State Roaming együttműködési képesség?

A Windows 10 november 2015-es vagy újabb kiadásaiban Enterprise State Roaming csak egyetlen fiók esetében támogatott. Ha munkahelyi vagy iskolai Azure AD-fiókkal jelentkezik be a Windowsba, az összes adatszinkronizálás az Azure AD-n keresztül történik. Ha személyes Microsoft-fiók használatával jelentkezik be a Windowsba, az összes adatszinkronizálás a Microsoft-fiók keresztül történik. Az univerzális AppData csak az elsődleges bejelentkezési fiókot fogja használni az eszközön, és csak akkor barangol, ha az alkalmazás licence az elsődleges fiók tulajdonosa. A másodlagos fiókok tulajdonában lévő alkalmazásokhoz tartozó univerzális AppData nem lesz szinkronizálva.

## <a name="do-settings-sync-for-azure-ad-accounts-from-multiple-tenants"></a>Több bérlőről származó Azure AD-fiókok esetében is szinkronizálja a beállításokat?

Ha a különböző Azure AD-bérlők több Azure AD-fiókja ugyanazon az eszközön található, frissítenie kell az eszköz beállításjegyzékét az Azure Rights Management szolgáltatással való kommunikációra minden egyes Azure AD-bérlő esetében.  

1. Keresse meg az egyes Azure AD-bérlők GUID azonosítóját. Nyissa meg a Azure Portal, és válasszon ki egy Azure AD-bérlőt. A bérlő GUID-azonosítója a kiválasztott bérlő tulajdonságlapján található (a https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties) címkével ellátott **CÍMTÁR-azonosítóval**. 
2. A GUID azonosító megadása után hozzá kell adnia a **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\SettingSync\WinMSIPC\<tenant ID GUID>** beállításkulcsot.
   A **bérlői azonosító GUID** kulcsában hozzon létre egy új, **AllowedRMSServerUrls** nevű Multi-String értéket (reg-multi-sz). Az adatmennyiség beállításnál határozza meg az eszköz által használt többi Azure-bérlő licencelési terjesztési pontjának URL-címeit.
3. A licencelési terjesztési pontok URL-címei a **Get-AadrmConfiguration** parancsmagnak a AADRM modulból való futtatásával találhatók meg. Ha a **LicensingIntranetDistributionPointUrl** és a **LicensingExtranetDistributionPointUrl** értéke eltérő, akkor mindkét értéket meg kell adni. Ha az értékek megegyeznek, csak egyszer kell megadnia az értéket.

## <a name="what-are-the-roaming-settings-options-for-existing-windows-desktop-applications"></a>Milyen barangolási beállítások állnak rendelkezésre a meglévő Windows asztali alkalmazásokhoz?

A roaming csak univerzális Windows-alkalmazásokhoz használható. Két lehetőség áll rendelkezésre a barangolás engedélyezésére egy meglévő Windows asztali alkalmazásban:

* Az [asztali híd](/windows/msix/desktop/source-code-overview) segítséget nyújt a meglévő Windows asztali alkalmazások univerzális Windows-platform való bekapcsolásához. Innen az Azure AD-alkalmazások adatroamingjának kihasználásához minimális programkód-módosításra lesz szükség. Az asztali híd alkalmazás-identitással biztosítja az alkalmazásait, ami szükséges ahhoz, hogy az alkalmazások adatbarangolása elérhető legyen a meglévő asztali alkalmazások számára.
* A [felhasználói élmény virtualizálása (UE-V)](/previous-versions//dn458947(v=vs.85)) segítségével egyéni beállítási sablont hozhat létre a meglévő Windows asztali alkalmazásokhoz, és engedélyezheti a roaming használatát a Win32-alkalmazások számára. Ehhez a beállításhoz nincs szükség az alkalmazás fejlesztői számára az alkalmazás kódjának megváltoztatására. Az UE-V a Microsoft asztali optimalizációs csomag megvásárlása esetén a helyszíni Active Directory barangolásra korlátozódik.

A rendszergazdák úgy konfigurálhatják az UE-V-t, hogy a Windows asztali alkalmazáshoz tartozó adatátvitelt a Windows operációs rendszer beállításainak és az univerzális alkalmazásadatok az [UE-v csoportházirendekkel](/microsoft-desktop-optimization-pack/uev-v2/configuring-ue-v-2x-with-group-policy-objects-both-uevv2)való megváltoztatásával, beleértve a következőket:

* Központi Windows-beállítások csoportházirendje
* Ne szinkronizálja a Windows-alkalmazások csoportházirendjét
* Az Internet Explorer barangolása az alkalmazások szakaszban

A jövőben a Microsoft megvizsgálhatja, hogy miként lehet a Windowsba szorosan integrálni az UE-V-t, és kiterjeszteni az UE-V-t a beállításokra az Azure AD-felhőben.

## <a name="can-i-store-synced-settings-and-data-on-premises"></a>Tárolhatom a szinkronizált beállításokat és a helyszíni adatszolgáltatásokat?

Enterprise State Roaming az összes szinkronizált adattartalmat a Microsoft-felhőben tárolja. Az UE-V helyszíni barangolási megoldást kínál.

## <a name="who-owns-the-data-thats-being-roamed"></a>Kik rendelkeznek a barangolva lévő adattal?

A vállalatok a Enterprise State Roaming használatával barangoltak. Az Azure-adatközpontokban tároljuk az adattárolást. Az összes felhasználói adatok titkosítva vannak mind a felhőben, mind a felhőben, az Azure Rights Management szolgáltatás használatával Azure Information Protection. Ez a Microsoft-fiók-alapú beállítások szinkronizálásának továbbfejlesztése, amely csak bizonyos bizalmas adatokat titkosít, például a felhasználói hitelesítő adatokat, mielőtt elhagyja az eszközt.

A Microsoft elkötelezte magát az ügyféladatok védelme mellett. A vállalati felhasználók beállításait a rendszer automatikusan titkosítja az Azure Rights Management szolgáltatás, mielőtt elhagyja a Windows 10-es eszközt, így egyetlen másik felhasználó sem tudja beolvasni ezeket az adatfájlokat. Ha szervezete fizetős előfizetéssel rendelkezik az Azure Rights Management szolgáltatáshoz, más védelmi funkciókat is használhat, például a dokumentumok nyomon követését és visszavonását, a bizalmas adatokat tartalmazó e-mailek automatikus védelmét és a saját kulcsok kezelését (a "saját kulcs használata", más néven BYOK). További információ ezekről a funkciókról és a védelmi szolgáltatás működéséről: [Mi az az Azure Rights Management](/azure/information-protection/what-is-information-protection).

## <a name="can-i-manage-sync-for-a-specific-app-or-setting"></a>Kezelhetem a szinkronizálást egy adott alkalmazáshoz vagy beállításhoz?

A Windows 10 rendszerben nincs MDM vagy Csoportházirend beállítás a barangolás letiltásához egy adott alkalmazás esetében. A bérlői rendszergazdák letilthatják az AppData-szinkronizálást a felügyelt eszközökön lévő összes alkalmazásra vonatkozóan, de az alkalmazáson belüli és az alkalmazások szintjén nem lehet finomabb vezérlés.

## <a name="how-can-i-enable-or-disable-roaming"></a>Hogyan engedélyezhető vagy tiltható le a barangolás?

A **Beállítások** alkalmazásban lépjen a **fiókok**  >  **szinkronizálása beállítások menüpontra**. Ezen a lapon megtekintheti, hogy melyik fiókot használja a rendszer a barangolási beállításokhoz, és engedélyezheti vagy letilthatja a barangolni kívánt beállítások egyedi csoportjait.

## <a name="what-is-microsofts-recommendation-for-enabling-roaming-in-windows-10"></a>Mi a Microsoft javaslata a barangolás engedélyezésére a Windows 10 rendszerben?

A Microsoft néhány különböző elérhető barangolási megoldással rendelkezik, beleértve a barangoló felhasználói profilokat, az UE-V-t és a Enterprise State Roaming.  A Microsoft elkötelezte magát arra, hogy a Windows jövőbeli verzióiban Enterprise State Roaming a beruházásokat. Ha a szervezete nem áll készen vagy kényelmesen az adatáthelyezés a felhőbe, javasoljuk, hogy elsődleges barangolási technológiaként használja az UE-V-t. Ha a szervezete a meglévő Windows asztali alkalmazások barangolásos támogatását igényli, de a felhőre szeretne áttérni, javasoljuk, hogy mindkét Enterprise State Roaming és az UE-V-t használja. Bár az UE-V és a Enterprise State Roaming nagyon hasonló technológiák, nem zárják ki egymást. Kiegészítik egymást, így biztosítva, hogy a szervezet biztosítja a felhasználók számára szükséges barangolási szolgáltatásokat.  

A Enterprise State Roaming és az UE-V együttes használatakor a következő szabályok érvényesek:

* Enterprise State Roaming az eszköz elsődleges barangoló ügynöke. Az UE-V a "Win32 Gap" kiegészítésére használatos.
* A Windows-beállításokhoz és a modern UWP-alkalmazásokhoz tartozó UE-V roamingot le kell tiltani az UE-V csoportházirendek használatakor. Ezeket már a Enterprise State Roaming tárgyalja.

## <a name="how-does-enterprise-state-roaming-support-virtual-desktop-infrastructure-vdi"></a>Hogyan támogatja a Enterprise State Roaming a virtuális asztali infrastruktúrát (VDI)?

A Enterprise State Roaming támogatott a Windows 10 ügyfél-SKU-on, de nem a kiszolgálói SKU-on. Ha az ügyfél virtuális gépe egy Hypervisort futtató gépen fut, és távolról bejelentkezik a virtuális gépre, az adatai barangolva lesznek. Ha több felhasználó használja ugyanazt az operációs rendszert, és a felhasználók távolról jelentkeznek be egy kiszolgálóra a teljes asztali élményhez, előfordulhat, hogy a barangolás nem működik. Az utóbbi munkamenet-alapú forgatókönyv nem támogatott hivatalosan.

## <a name="what-happens-when-my-organization-purchases-a-subscription-that-includes-azure-rights-management-after-using-roaming"></a>Mi történik, ha a szervezetem olyan előfizetést vásárol, amely tartalmazza az Azure Rights Managementt a barangolás használata után?

Ha a szervezet már használ barangolást a Windows 10-es verzióban az Azure Rights Management korlátozott használatú ingyenes előfizetéssel, az Azure Rights Management Protection szolgáltatást tartalmazó [fizetős előfizetés](https://azure.microsoft.com/pricing/details/information-protection/) megvásárlása nem befolyásolja a barangolási funkció funkcióit, és a rendszergazda nem fogja módosítani a szükséges konfigurációs módosításokat.

## <a name="known-issues"></a>Ismert problémák

Az ismert problémák listájáért tekintse meg a [hibaelhárítási](enterprise-state-roaming-troubleshooting.md) szakaszban található dokumentációt. 

## <a name="next-steps"></a>Következő lépések 

Áttekintést a [vállalati állapot barangolásának áttekintése](enterprise-state-roaming-overview.md) című témakörben talál.