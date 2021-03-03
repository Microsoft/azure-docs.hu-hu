---
title: Elsődleges frissítési jogkivonat (PRT) és Azure AD – Azure Active Directory
description: Mi a szerepe, és hogyan kezeljük az elsődleges frissítési tokent (PRT) Azure Active Directory?
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46cc8ef1158c02190f905cbe8eb1d12ea7be50a2
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101644935"
---
# <a name="what-is-a-primary-refresh-token"></a>Mi az az elsődleges frissítési jogkivonat?

Az elsődleges frissítési jogkivonat (PRT) a Windows 10, a Windows Server 2016 és az újabb verziók, az iOS és az Android rendszerű eszközök Azure AD-hitelesítésének fő összetevője. Ez egy olyan JSON Web Token (JWT), amelyet kifejezetten a Microsoft első féltől származó token-közvetítőknek bocsátottak ki, hogy az egyszeri bejelentkezést (SSO) engedélyezzék az adott eszközökön használt alkalmazások között. Ez a cikk részletesen ismerteti, hogyan történik a PRT kibocsátása, használata és védelme a Windows 10-es eszközökön.

Ez a cikk azt feltételezi, hogy már ismeri az Azure AD-ben elérhető különböző eszközök állapotát, valamint azt, hogy az egyszeri bejelentkezés hogyan működik a Windows 10 rendszerben. További információ az Azure AD-eszközökről: [Mi az Eszközkezelés a Azure Active Directory?](overview.md)

## <a name="key-terminology-and-components"></a>Kulcsfontosságú terminológia és összetevők

A következő Windows-összetevők kulcsszerepet játszanak a PRT igénylésében és használatában:

* **Felhőalapú hitelesítési szolgáltató** (CloudAP): a CloudAP a Windows-bejelentkezéshez használt modern hitelesítési szolgáltató, amely ellenőrzi, hogy a felhasználók Windows 10-es eszközre jelentkeznek-e. A CloudAP olyan beépülő modul-keretrendszert biztosít, amelyet az identitás-szolgáltatók az identitás-szolgáltató hitelesítő adatait használó Windows-hitelesítés engedélyezéséhez használhatnak.
* **Web Account Manager** (WAM): a WAM az alapértelmezett jogkivonat-átvitelszervező a Windows 10-es eszközökön. A WAM egy beépülő modul-keretrendszert is biztosít, amelyet az identitás-szolgáltatók építhetnek be, és lehetővé teszik az egyszeri bejelentkezést az adott identitás-szolgáltatóra támaszkodó alkalmazásaikban.
* **Azure ad CloudAP beépülő modul**: a CloudAP-keretrendszerre épülő Azure ad-specifikus beépülő modul, amely a felhasználói hitelesítő adatokat ellenőrzi az Azure ad-ben a Windows bejelentkezés során.
* **Azure ad WAM beépülő modul**: a WAM-keretrendszerre épülő Azure ad-specifikus beépülő modul, amely lehetővé teszi az egyszeri bejelentkezést az Azure ad-t használó alkalmazásokhoz a hitelesítéshez.
* **Dsreg**: egy Azure ad-specifikus összetevő a Windows 10 rendszerben, amely az eszköz regisztrációs folyamatát kezeli az összes eszköz állapotára vonatkozóan.
* **Platformmegbízhatósági modul** (TPM): a TPM egy eszközbe épített hardver-összetevő, amely hardveres biztonsági funkciókat biztosít a felhasználók és az eszközök titkos kulcsaihoz. További részletekért tekintse meg a következő cikket: [platformmegbízhatósági modul Technology áttekintése](/windows/security/information-protection/tpm/trusted-platform-module-overview).

## <a name="what-does-the-prt-contain"></a>Mit tartalmaz a PRT?

A PRT az Azure AD frissítési jogkivonatában általánosan tartalmazott jogcímeket tartalmaz. Emellett a PRT-ben bizonyos, az eszközre vonatkozó jogcímek is szerepelnek. Ezek a következők:

* **Eszköz azonosítója**: egy adott eszköz felhasználója számára kap egy PRT-t. Az eszköz-azonosító jogcím `deviceID` meghatározza azt az eszközt, amelyet a PRT a felhasználó számára adott ki. Ezt a jogcímet később a PRT-n keresztül beszerzett jogkivonatoknak adják ki. Az eszköz-azonosító jogcím az eszköz állapotára vagy megfelelőségére vonatkozó feltételes hozzáférés engedélyezésének meghatározására szolgál.
* **Munkamenetkulcs**: a munkamenetkulcs egy titkosított szimmetrikus kulcs, amelyet az Azure ad hitelesítési szolgáltatás generál, amelyet a PRT részeként állítottak ki. A munkamenet-kulcs igazolja a tulajdonjogot, ha egy PRT-t használ a más alkalmazásokhoz tartozó jogkivonatok beszerzéséhez.

### <a name="can-i-see-whats-in-a-prt"></a>Megtekinthetem a PRT-ket?

A PRT egy olyan átlátszatlan blob, amely az Azure AD-ből származik, és a tartalmakat nem ismerik egyetlen ügyfél-összetevő sem. A PRT-k belsejében nem látható.

## <a name="how-is-a-prt-issued"></a>Hogyan lett kibocsátva a PRT?

Az eszköz regisztrációja az eszközön alapuló hitelesítés előfeltétele az Azure AD-ben. A PRT-ket csak regisztrált eszközökön adják ki a felhasználók számára. További részletes információk az eszközök regisztrálásáról: a [Windows Hello for Business és az eszközök regisztrációja](/windows/security/identity-protection/hello-for-business/hello-how-it-works-device-registration). Az eszköz regisztrálása során a dsreg összetevő két titkosítási kulcspár-készletet hoz létre:

* Eszköz kulcsa (dkpub/dkpriv)
* Átviteli kulcs (tkpub/tkpriv)

A titkos kulcsok az eszköz TPM-hez vannak kötve, ha az eszköz rendelkezik érvényes és működő TPM-mel, míg a nyilvános kulcsokat az eszköz regisztrálási folyamata során az Azure AD-be küldik. Ezek a kulcsok az eszköz állapotának ellenőrzéséhez használhatók a PRT-kérelmekben.

A PRT-t a Windows 10-es eszköz felhasználói hitelesítése során két forgatókönyvben adják ki:

* **Azure ad-hez csatlakoztatott** vagy **hibrid Azure ad-csatlakozás**: a rendszer a Windows-bejelentkezés során kiállítja a PRT-t, amikor egy felhasználó bejelentkezik a szervezeti hitelesítő adataival. A PRT-ket minden Windows 10 támogatott hitelesítő adat (például a jelszó és a vállalati Windows Hello) adja ki. Ebben az esetben az Azure AD CloudAP beépülő modul a PRT elsődleges szolgáltatója.
* **Azure ad-beli regisztrált eszköz**: a rendszer akkor bocsát ki egy PRT-t, amikor egy felhasználó másodlagos munkahelyi fiókot ad hozzá a Windows 10-es eszközhöz. A felhasználók kétféleképpen adhatnak hozzá Windows 10-es fiókokat –  
   * Fiók hozzáadása a **fiók használata mindenhol ezen az eszközön** az alkalmazásba való bejelentkezés után (például Outlook)
   * Fiók hozzáadása a **Beállítások**  >  **fiókjainak**  >  **hozzáférés munkahelyi vagy iskolai**  >  **kapcsolathoz**

Az Azure AD-ban regisztrált eszközök esetében az Azure AD WAM beépülő modul a PRT elsődleges szolgáltatója, mivel a Windows bejelentkezés nem történik meg ezzel az Azure AD-fiókkal.

> [!NOTE]
> a harmadik féltől származó identitás-szolgáltatóknak támogatnia kell az WS-Trust protokollt a PRT-kiadásoknak a Windows 10-es eszközökön való engedélyezéséhez. A WS-Trust nélkül a PRT nem adható ki a hibrid Azure AD-hez csatlakoztatott vagy az Azure AD-hez csatlakoztatott eszközökön lévő felhasználóknak. Az ADFS csak usernamemixed-végpontokra van szükség. Az ADFS/Services/Trust/2005/windowstransport és az ADFS/Services/Trust/13/windowstransport egyaránt engedélyezve kell lennie csak intranetes végpontoknak, és a webalkalmazás-proxyn keresztül nem szabad az extranetes végpontok számára **elérhetővé** tenni.

## <a name="what-is-the-lifetime-of-a-prt"></a>Mi a PRT élettartama?

A kiadást követően a PRT 14 napig érvényes, és folyamatosan megújul, amíg a felhasználó aktívan használja az eszközt.  

## <a name="how-is-a-prt-used"></a>Hogyan használják a PRT-ket?

A PRT-ket a Windows két fő összetevője használja:

* **Azure ad-CloudAP beépülő modul**: a Windows-bejelentkezés során az Azure ad CloudAP beépülő modulja a felhasználó által megadott hitelesítő adatok használatával kér egy PRT-t az Azure ad-ből. Emellett gyorsítótárazza a PRT-t, hogy engedélyezze a gyorsítótárazott bejelentkezést, ha a felhasználó nem fér hozzá internetkapcsolathoz.
* **Azure ad WAM beépülő modul**: amikor a felhasználók megpróbálnak hozzáférni az alkalmazásokhoz, az Azure ad WAM beépülő modul a PRT használatával engedélyezi az SSO-t a Windows 10 rendszeren. Az Azure AD WAM beépülő modulja a PRT-vel kéri az olyan alkalmazásokhoz való frissítési és hozzáférési jogkivonatokat, amelyek a WAM-t használják a jogkivonat-kérelmekre. Emellett lehetővé teszi az egyszeri bejelentkezést a böngészőkben a PRT-nek a böngészőalapú kérelmekbe való beadásával. A Windows 10-es böngésző egyszeri bejelentkezését a Microsoft Edge (natív módon) és a Chrome (a [Windows 10 fiókjain](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji?hl=en) vagy az [Office Online](https://chrome.google.com/webstore/detail/office/ndjpnladcallmjemlbaebfadecfhkepb?hl=en) -bővítményeken keresztül) támogatja.

## <a name="how-is-a-prt-renewed"></a>Hogyan újítják meg a PRT-ket?

A PRT két különböző módon megújul:

* **Azure ad CloudAP beépülő modul 4 óránként**: a CloudAP beépülő modul a Windows bejelentkezés során 4 óránként megújítja a PRT-t. Ha a felhasználó ebben az időszakban nem rendelkezik internetkapcsolattal, a CloudAP beépülő modul megújítja a PRT-t, miután az eszköz csatlakozik az internethez.
* **Azure ad WAM beépülő modul az alkalmazási jogkivonat-kérelmek során**: a WAM beépülő modul lehetővé teszi az egyszeri bejelentkezést a Windows 10-es eszközökön az alkalmazások csendes jogkivonat-kérelmének engedélyezésével. A WAM beépülő modul két különböző módon tudja megújítani a PRT-ket a következő jogkivonat-kérelmekben:
   * Egy alkalmazás a WAM-t csendes hozzáférési jogkivonatra kéri, de az alkalmazáshoz nem érhető el frissítési jogkivonat. Ebben az esetben a WAM a PRT-t használja az alkalmazáshoz tartozó token igényléséhez, és egy új PRT-t kap vissza a válaszban.
   * Egy alkalmazás a WAM-t egy hozzáférési jogkivonathoz kéri, de a PRT érvénytelen, vagy az Azure AD-nek további engedélyre van szüksége (például Azure AD Multi-Factor Authentication). Ebben az esetben a WAM olyan interaktív bejelentkezést kezdeményez, amely megköveteli a felhasználótól, hogy ismételt hitelesítést végezzen, vagy további ellenőrzéseket nyújtson, és egy új PRT-t állítanak ki sikeres hitelesítéssel.

ADFS-környezetben a PRT megújításához nem szükséges a tartományvezérlő közvetlen megfigyelési vonala. A PRT-megújítás csak a/ADFS/Services/Trust/2005/usernamemixed és az/ADFS/Services/Trust/13/usernamemixed végpontok használatát igényli a proxyn WS-Trust protokoll használatával.

A jelszó-hitelesítéshez a Windows átviteli végpontok csak akkor szükségesek, ha a jelszó módosul, és nem a PRT megújításához.

### <a name="key-considerations"></a>Fő szempontok

* A PRT-t csak a natív alkalmazás hitelesítése során kell kiállítani és megújítani. Egy PRT-t nem újítanak meg vagy állítanak ki egy böngésző-munkamenet során.
* Az Azure AD-hez csatlakoztatott és hibrid Azure AD-hez csatlakoztatott eszközökön a CloudAP beépülő modul a PRT elsődleges szolgáltatója. Ha a PRT-t egy WAM-alapú jogkivonat-kérelem során újítják meg, a rendszer visszaküldi a PRT-t a CloudAP beépülő modulnak, amely ellenőrzi a PRT-nek az Azure AD-vel való érvényességét az elfogadás előtt.

## <a name="how-is-the-prt-protected"></a>Hogyan történik a PRT védelme?

A PRT-t a felhasználó által a szolgáltatásba bejelentkezett eszközhöz kötéssel védi. Az Azure AD és a Windows 10 a következő módszerekkel teszi lehetővé a PRT-védelmet:

* Az **első bejelentkezés során**: az első bejelentkezéskor a rendszer a PRT-ket az eszköz regisztrálása során kriptográfiailag generált kulcs használatával állítja ki. Egy érvényes és működő TPM-mel rendelkező eszközön a TPM védi az eszköz kulcsát, ami megakadályozza a kártékony hozzáférést. A rendszer nem bocsát ki egy PRT-t, ha a megfelelő eszköz kulcsának aláírása nem érvényesíthető.
* A **jogkivonat-kérelmek és a megújítás során**: Ha egy PRT-t bocsát ki, az Azure ad egy titkosított munkamenetkulcsot is kiad az eszközhöz. Az eszköz regisztrációjának részeként a rendszer az Azure AD-ben generált és eljuttatott nyilvános átviteli kulccsal (tkpub) titkosítja. Ezt a munkamenetkulcsot csak a TPM által védett privát átviteli kulccsal (tkpriv) lehet visszafejteni. A munkamenetkulcs az Azure AD-be küldött kérelmekre vonatkozó, az összes kérelemre vonatkozó (POP) kulcs.  A munkamenet-kulcsot a TPM is védi, és más operációs rendszer-összetevő sem fér hozzá. A jogkivonat-kérelmeket vagy a PRT-megújítási kérelmeket a TPM-en keresztül biztonságosan aláírja a rendszer, ezért nem lehet illetéktelenül módosítani. Az Azure AD érvényteleníti az eszközről érkező olyan kéréseket, amelyeket nem a megfelelő munkamenetkulcs írta alá.

A kulcsoknak a TPM-mel való biztonságossá tételével a PRT-ket a kulcsokat ellopó vagy a PRT-t visszajátszó rosszindulatú szereplőkkel szembeni biztonsága fokozza.  Így a TPM használata nagy mértékben fokozza az Azure AD-hez csatlakoztatott, a hibrid Azure AD-hez csatlakoztatott és az Azure AD által regisztrált eszközök biztonságát a hitelesítő adatok ellopása ellen. A teljesítmény és a megbízhatóság érdekében a TPM 2,0 a Windows 10 összes Azure AD-eszköz regisztrációs forgatókönyvének ajánlott verziója. A Windows 10 1903-es frissítésének elindításával az Azure AD nem használja a TPM 1,2-et a fenti kulcsok bármelyikéhez a megbízhatósági problémák miatt. 

### <a name="how-are-app-tokens-and-browser-cookies-protected"></a>Hogyan védi az alkalmazás-jogkivonatokat és a böngésző cookie-jait?

**Alkalmazás-jogkivonatok**: Ha egy alkalmazás a WAM-n keresztül kér jogkivonatot, az Azure ad egy frissítési tokent és egy hozzáférési jogkivonatot bocsát ki A WAM azonban csak az alkalmazás hozzáférési jogkivonatát adja vissza, és a gyorsítótárban lévő frissítési tokent a felhasználó adatvédelmi alkalmazásprogramozási felületének (DPAPI) kulcsával titkosítja. A WAM biztonságosan használja a frissítési tokent, ha a munkamenet-kulccsal aláírja a kérelmeket a további hozzáférési tokenek kijavításához. A DPAPI kulcsot egy Azure ad-alapú szimmetrikus kulcs védi az Azure AD-ben. Amikor az eszköznek vissza kell fejtenie a felhasználói profilt a DPAPI kulccsal, az Azure AD biztosítja a DPAPI által titkosított kulcsot, amely a CloudAP beépülő modult kéri a TPM visszafejtésére. Ez a funkció biztosítja a frissítési tokenek védelmének egységességét, és elkerüli a saját védelmi mechanizmusait megvalósító alkalmazásokat.  

**Böngészőbeli cookie-k**: a Windows 10-es verzióban az Azure ad támogatja a böngésző egyszeri bejelentkezését az Internet Explorerben és a Microsoft Edge-ben a Windows 10-es fiókok bővítmény használatával, a Google Chrome-ban. A biztonsági rendszer nem csak a cookie-k védelmére, hanem a cookie-k elküldésekor használt végpontokra is épül. A böngésző cookie-jai ugyanúgy vannak védve, mint a PRT-k, a munkamenet-kulcs használatával aláírhatja és megvédheti a cookie-kat.

Amikor a felhasználó egy böngészőbeli interakciót kezdeményez, a böngésző (vagy bővítmény) egy COM natív ügyfél-gazdagépet hív meg. A natív ügyfél gazdagépe biztosítja, hogy a lap az egyik engedélyezett tartományból származik. A böngésző más paramétereket is küldhet a natív ügyfél-gazdagép számára, beleértve az egymást megnyitó gazdagépet is, de a natív ügyfél gazdagépe garantálja az állomásnév érvényesítését. A natív ügyfél gazdagépe egy PRT-cookie-t kér a CloudAP beépülő modulból, amely létrehozza és aláírja a TPM által védett munkamenetkulcsot. Mivel a PRT-cookie-t a munkamenetkulcs aláírja, nagyon nehéz megbirkózni a szolgáltatással. Ez a PRT-cookie az Azure AD-hez tartozó kérelem fejlécében található, amely ellenőrzi, hogy az eszköz származik-e. Ha a Chrome böngészőt használja, csak a natív ügyfél-gazdagép jegyzékfájljában explicit módon meghatározott bővítmény hivatkozhat arra, hogy tetszőleges kiterjesztéseket hozzon a kérelmekbe. Miután az Azure AD érvényesíti a PRT cookie-t, egy munkamenet-cookie-t ad ki a böngészőnek. Ez a munkamenet-cookie a PRT-vel kiadott azonos munkamenetkulcs-kulcsot is tartalmazza. A későbbi kérések során a rendszer ellenőrzi, hogy a munkamenetkulcs valóban a cookie-t az eszközhöz köti-e, és megakadályozza a többitől való visszajátszást.

## <a name="when-does-a-prt-get-an-mfa-claim"></a>Mikor szerezhet be MFA-jogcímet a PRT?

A PRT-ket adott helyzetekben egy multi-Factor Authentication-(MFA-) jogcímet kaphat. Ha egy MFA-alapú PRT-t használ az alkalmazások jogkivonatának igénylésére, az MFA-jogcímet az alkalmazás jogkivonatai továbbítják. Ez a funkció zökkenőmentes felhasználói élményt nyújt a felhasználóknak azáltal, hogy megakadályozza az MFA-kihívásokat minden olyan alkalmazáshoz, amelyhez szükséges. A PRT a következő módokon szerezhet be MFA-jogcímet:

* **Bejelentkezés a vállalati Windows Hello** szolgáltatásban: a Windows Hello for Business helyettesíti a jelszavakat, és kriptográfiai kulcsokkal biztosítja az erős kétfaktoros hitelesítést. A vállalati Windows Hello egy eszköz felhasználója, és maga az MFA kiépítését igényli. Amikor egy felhasználó bejelentkezik a vállalati Windows Hello szolgáltatásba, a felhasználó által használt PRT MFA-jogcímet kap. Ez a forgatókönyv az intelligens kártyákkal bejelentkező felhasználókra is vonatkozik, ha az intelligens kártya hitelesítése MFA-jogcímet hoz létre az ADFS-től.
   * Mivel a vállalati Windows Hello a többtényezős hitelesítésnek minősül, a rendszer frissíti az MFA-jogcímet, amikor a PRT frissül, így az MFA időtartama folyamatosan ki lesz terjesztve, amikor a felhasználók bejelentkeznek a vállalati WIndows Hello szolgáltatásba
* **MFA a WAM interaktív bejelentkezés során**: a WAM-on keresztüli jogkivonat-kérelem során, ha a felhasználónak az alkalmazás eléréséhez az MFA-t kell tennie, a jelen interakció során megújított PRT-ket az MFA-jogcímek alapján kell kinyomtatni.
   * Ebben az esetben az MFA-jogcím nem frissül folyamatosan, így az MFA időtartama a címtárban beállított élettartamon alapul.
   * Amikor egy korábbi meglévő PRT-t és RT-t használ az alkalmazáshoz való hozzáféréshez, a PRT és RT a hitelesítés első bizonyítéka lesz. Egy új IDŐPONTBAN lesz szükség egy második bizonyítékra és egy nem nyomtatható MFA-jogcímre. Ez egy új PRT-t és RT-t is kiállít.
* **MFA az eszköz regisztrálása során**: Ha egy rendszergazda konfigurálta az eszköz beállításait az Azure ad-ben, hogy a többtényezős hitelesítés [megkövetelése az eszközök regisztrálásához](device-management-azure-portal.md#configure-device-settings), a felhasználónak MFA-t kell tennie a regisztráció befejezéséhez. A folyamat során a felhasználónak kiállított PRT a regisztráció során kapott MFA-jogcímet adja meg. Ez a funkció csak az összekapcsolási műveletet végző felhasználóra vonatkozik, nem pedig az adott eszközre bejelentkező többi felhasználóra.
   * A WAM interaktív bejelentkezéshez hasonlóan az MFA-jogcím nem frissül folyamatosan, így az MFA időtartama a címtárban beállított élettartamon alapul.

A Windows 10 minden hitelesítő adathoz megtartja a PRT-ket tartalmazó particionált listát. Tehát van egy PRT a vállalati Windows Hello-hez, jelszóhoz vagy intelligens kártyához. Ez a particionálás biztosítja, hogy az MFA-jogcímek elkülönítése a használt hitelesítő adatok alapján történjen, és ne legyen összekeverve a jogkivonat-kérelmekben.

## <a name="how-is-a-prt-invalidated"></a>Hogyan lett érvénytelenítve a PRT?

A PRT-t a következő esetekben érvényteleníti a rendszer:

* **Érvénytelen felhasználó**: Ha egy felhasználót törölnek vagy letiltanak az Azure ad-ben, a PRT érvénytelenné válik, és nem használható jogkivonatok beszerzésére az alkalmazások számára. Ha egy törölt vagy letiltott felhasználó már be van jelentkezve egy eszközre, a gyorsítótárazott bejelentkezés a következő helyre fog bejelentkezni, amíg a CloudAP nem ismeri a érvénytelen állapotot. Miután a CloudAP megállapítja, hogy a felhasználó érvénytelen, blokkolja a további bejelentkezéseket. A rendszer automatikusan letiltja a felhasználótól a bejelentkezést olyan új eszközökre, amelyek nem rendelkeznek a hitelesítő adataik gyorsítótárazásával.
* **Érvénytelen eszköz**: Ha egy eszköz törölve vagy le van tiltva az Azure ad-ben, az eszközön beszerzett PRT érvénytelenné válik, és nem használható jogkivonatok beszerzésére más alkalmazások számára. Ha egy felhasználó már be van jelentkezve egy érvénytelen eszközre, akkor továbbra is megteheti. Az eszközön lévő összes jogkivonat érvénytelenné válik, és a felhasználó nem rendelkezik SSO-val az adott eszközről származó erőforrásokhoz.
* **Jelszó módosítása**: miután egy felhasználó megváltoztatta a jelszavát, az Azure ad érvényteleníti az előző jelszóval kapott PRT-t. A jelszó módosításának eredménye a felhasználó számára új PRT beszerzése. Ez az érvénytelenítés két különböző módon történhet:
   * Ha a felhasználó új jelszavával jelentkezik be a Windowsba, a CloudAP elveti a régi PRT-t, és az Azure AD-t kéri új PRT-nek az új jelszóval való kibocsátására. Ha a felhasználó nem rendelkezik internetkapcsolattal, az új jelszót nem lehet érvényesíteni, a Windows megkövetelheti a felhasználónak a régi jelszavának megadását.
   * Ha a felhasználó a régi jelszavával jelentkezett be, vagy a Windowsba való bejelentkezés után módosította a jelszavát, a rendszer a régi PRT-t használja a WAM-alapú jogkivonat-kérelmekhez. Ebben az esetben a rendszer felszólítja a felhasználót, hogy ismételt hitelesítést végezzen a WAM-jogkivonat kérelmében, és új PRT-t állít ki.
* **TPM-problémák**: Előfordulhat, hogy egy eszköz TPM-je akadozik vagy meghibásodik, ami a TPM által védett kulcsok elérhetőségét eredményezi. Ebben az esetben az eszköz nem tudja lekérni a PRT-t vagy jogkivonatokat kérelmezni egy meglévő PRT-vel, mivel nem képes bizonyítani a titkosítási kulcsok birtoklását. Ennek eredményeképpen minden meglévő PRT-t érvénytelenítettek az Azure AD. Ha a Windows 10 hibát észlel, egy helyreállítási folyamatot kezdeményez, hogy újra regisztrálja az eszközt az új titkosítási kulcsokkal. A hibrid Azure ad-csatlakozással – a kezdeti regisztrációhoz hasonlóan – a helyreállítás csendesen, felhasználói beavatkozás nélkül történik. Az Azure AD-hez csatlakoztatott vagy az Azure AD-ban regisztrált eszközök esetében a helyreállítást olyan felhasználónak kell végrehajtania, aki rendszergazdai jogosultságokkal rendelkezik az eszközön. Ebben a forgatókönyvben a helyreállítási folyamatot egy Windows-prompt kezdeményezi, amely végigvezeti a felhasználót az eszköz sikeres helyreállításában.

## <a name="detailed-flows"></a>Részletes folyamatok

A következő diagramok illusztrálják a PRT-ket kiállító és megújítási, valamint egy alkalmazás hozzáférési jogkivonatának igényléséhez szükséges adatokat. Emellett ezek a lépések azt is ismertetik, hogyan lesznek alkalmazva a fent említett biztonsági mechanizmusok ezen interakciók során.

### <a name="prt-issuance-during-first-sign-in"></a>PRT-kiadás az első bejelentkezéskor

![PRT-kiadás az első bejelentkezéskor – részletes folyamat](./media/concept-primary-refresh-token/prt-initial-sign-in.png)

> [!NOTE]
> Az Azure AD-hez csatlakoztatott eszközökön ez az Exchange szinkron módon történik, mielőtt a felhasználó be tudja jelentkezni a Windows rendszerbe. A hibrid Azure AD-hez csatlakoztatott eszközökön a helyszíni Active Directory az elsődleges szolgáltató. Így a felhasználó csak a TGT beolvasására vár, amíg a PRT-kiadás aszinkron módon történik. Ez a forgatókönyv nem vonatkozik az Azure AD-beli regisztrált eszközökre, mivel a bejelentkezés nem Azure AD-beli hitelesítő adatokat használ.

| Lépés | Leírás |
| :---: | --- |
| A | A felhasználó a bejelentkezési felhasználói felületen írja be a jelszavát. A LogonUI átadja a hitelesítő adatokat egy Auth-pufferben az LSA-nak, ami viszont belsőleg továbbítja a CloudAP. A CloudAP továbbítja ezt a kérést a CloudAP beépülő modulnak. |
| B | A CloudAP beépülő modul egy tartományi felderítési kérelmet kezdeményez a felhasználó identitás-szolgáltatójának azonosításához. Ha a felhasználó bérlője rendelkezik összevonási szolgáltatói beállítással, az Azure AD az összevonási szolgáltató metaadat-(MEX-) végpontját adja vissza. Ha nem, az Azure AD azt adja vissza, hogy a felhasználó felügyeli az Azure AD-vel való hitelesítést. |
| C | Ha a felhasználó felügyelt, a CloudAP az Azure AD-ből kapja meg az alkalomból. Ha a felhasználó összevont, a CloudAP beépülő modul SAML-jogkivonatot kér az összevonási szolgáltatótól a felhasználó hitelesítő adataival. Amint megkapta az SAML-tokent, az Azure AD-től egy alkalomra kér. |
| T | A CloudAP beépülő modul létrehozza a hitelesítési kérést a felhasználó hitelesítő adataival, és egy ügynök hatókörével aláírja a kérést az eszköz kulcsával (dkpriv), és elküldi az Azure AD-nek. Összevont környezetekben a CloudAP beépülő modul az összevonási szolgáltató által visszaadott SAML-tokent használja a felhasználói hitelesítő adatok helyett. |
| E | Az Azure AD ellenőrzi a felhasználói hitelesítő adatokat, az egyszeres és az eszköz aláírását, ellenőrzi, hogy az eszköz érvényes-e a bérlőben, és kiadja a titkosított PRT-ket. A PRT-vel együtt az Azure AD egy szimmetrikus kulcsot is kiad, az Azure AD által titkosított munkamenetkulcs (tkpub) használatával. Emellett a munkamenetkulcs a PRT-ben is be van ágyazva. Ez a munkamenetkulcs a PRT-vel kapcsolatos további kérések esetében igazolható (PoP) kulcsként működik. |
| F | A CloudAP beépülő modul a titkosított PRT-t és a munkamenet-kulcsot átadja a CloudAP. CloudAP kérje a TPM-t a munkamenetkulcs visszafejtéséhez a átviteli kulccsal (tkpriv), majd a TPM saját kulcsával újra titkosítja azt. A CloudAP a titkosított munkamenet kulcsát a saját gyorsítótárában tárolja a PRT-vel együtt. |

### <a name="prt-renewal-in-subsequent-logons"></a>PRT-megújítás a későbbi bejelentkezésekben

![PRT-megújítás a későbbi bejelentkezésekben](./media/concept-primary-refresh-token/prt-renewal-subsequent-logons.png)

| Lépés | Leírás |
| :---: | --- |
| A | A felhasználó a bejelentkezési felhasználói felületen írja be a jelszavát. A LogonUI átadja a hitelesítő adatokat egy Auth-pufferben az LSA-nak, ami viszont belsőleg továbbítja a CloudAP. A CloudAP továbbítja ezt a kérést a CloudAP beépülő modulnak. |
| B | Ha a felhasználó korábban bejelentkezett a felhasználóba, a Windows megkezdi a gyorsítótárazott bejelentkezést, és ellenőrzi a hitelesítő adatokat, hogy naplózza a felhasználót a alkalmazásban. A CloudAP beépülő modul 4 óránként aszinkron módon megújítja a PRT-megújítást. |
| C | A CloudAP beépülő modul egy tartományi felderítési kérelmet kezdeményez a felhasználó identitás-szolgáltatójának azonosításához. Ha a felhasználó bérlője rendelkezik összevonási szolgáltatói beállítással, az Azure AD az összevonási szolgáltató metaadat-(MEX-) végpontját adja vissza. Ha nem, az Azure AD azt adja vissza, hogy a felhasználó felügyeli az Azure AD-vel való hitelesítést. |
| T | Ha a felhasználó összevont, a CloudAP beépülő modul SAML-jogkivonatot kér az összevonási szolgáltatótól a felhasználó hitelesítő adataival. Amint megkapta az SAML-tokent, az Azure AD-től egy alkalomra kér. Ha a felhasználó felügyelete alatt áll, a CloudAP közvetlenül az Azure AD-ből kapja meg az alkalomból. |
| E | A CloudAP beépülő modul létrehozza a hitelesítési kérést a felhasználó hitelesítő adataival, és a meglévő PRT-vel aláírja a kérést a munkamenetkulcs használatával, és elküldi azt az Azure AD-nek. Összevont környezetekben a CloudAP beépülő modul az összevonási szolgáltató által visszaadott SAML-tokent használja a felhasználói hitelesítő adatok helyett. |
| F | Az Azure AD ellenőrzi, hogy a rendszer összehasonlítja-e a munkamenetkulcs aláírását a PRT-ben beágyazott munkamenetkulcs alapján, ellenőrzi, hogy az eszköz érvényes-e a bérlőben, és kiad egy új PRT-t. Ahogy korábban is látható, a PRT újra hozzá van csatolva a (tkpub) átviteli kulccsal titkosított munkamenetkulcs-kulcshoz. |
| G | A CloudAP beépülő modul a titkosított PRT-t és a munkamenet-kulcsot átadja a CloudAP. A CloudAP arra kéri a TPM-t, hogy visszafejtse a munkamenetkulcsot a (tkpriv) átviteli kulccsal, majd a TPM saját kulcsával titkosítsa azt. A CloudAP a titkosított munkamenet kulcsát a saját gyorsítótárában tárolja a PRT-vel együtt. |

> [!NOTE]
> A PRT-ket külsőleg lehet megújítani anélkül, hogy VPN-kapcsolatra lenne szükség, ha a usernamemixed-végpontok külsőleg engedélyezve vannak.

### <a name="prt-usage-during-app-token-requests"></a>PRT-használat az alkalmazás-jogkivonat-kérelmek során

![PRT-használat az alkalmazás-jogkivonat-kérelmek során](./media/concept-primary-refresh-token/prt-usage-app-token-requests.png)

| Lépés | Leírás |
| :---: | --- |
| A | Az alkalmazások (például az Outlook, a OneNote stb.) egy jogkivonat-kérelmet kezdeményeznek a WAM számára. A WAM ezt követően kéri az Azure AD WAM beépülő modult a jogkivonat-kérelem kiszolgálására. |
| B | Ha az alkalmazás frissítési jogkivonata már elérhető, az Azure AD WAM beépülő modul használatával kérhet hozzáférési tokent. Az eszközök kötésének igazolásához a WAM beépülő modul a munkamenet-kulccsal aláírja a kérést. Az Azure AD érvényesíti a munkamenetkulcsot, és kiadja a hozzáférési jogkivonatot és egy új frissítési jogkivonatot az alkalmazáshoz, amelyet a munkamenetkulcs titkosít. A WAM beépülő modul kéri a Cloud AP beépülő modult, hogy visszafejtse a jogkivonatokat, ami viszont azt kéri, hogy a TPM a munkamenetkulcs használatával legyen visszafejtve, ami a WAM beépülő modult is beolvassa Ezután a WAM beépülő modul csak a hozzáférési jogkivonatot biztosítja az alkalmazásnak, miközben újra titkosítja a frissítési tokent a DPAPI, és a saját gyorsítótárában tárolja azokat.  |
| C |  Ha az alkalmazás frissítési jogkivonata nem érhető el, az Azure AD WAM beépülő modul a PRT használatával kér hozzáférési jogkivonatot. Ahhoz, hogy igazolni lehessen a tulajdonjogot, a WAM beépülő modul aláírja a PRT-t tartalmazó kérelmet a munkamenetkulcsot. Az Azure AD ellenőrzi a munkamenetkulcs aláírását a PRT-ben beágyazott munkamenetkulcs összehasonlításával, ellenőrzi, hogy az eszköz érvényes-e, és az alkalmazáshoz hozzáférési jogkivonatot és frissítési jogkivonatot állít ki. Az Azure AD továbbá egy új PRT-t (frissítési ciklus alapján) is kiadhat, és mindegyiket a munkamenetkulcs titkosítja. |
| T | A WAM beépülő modul kéri a Cloud AP beépülő modult, hogy visszafejtse a jogkivonatokat, ami viszont azt kéri, hogy a TPM a munkamenetkulcs használatával legyen visszafejtve, ami a WAM beépülő modult is beolvassa Ezután a WAM beépülő modul csak a hozzáférési jogkivonatot biztosítja az alkalmazásnak, miközben újra titkosítja a frissítési tokent a DPAPI, és a saját gyorsítótárában tárolja azt. A WAM beépülő modul az alkalmazásra vonatkozó frissítési tokent fogja használni. A WAM beépülő modul az új PRT-t is visszaadja a Cloud AP beépülő modulhoz, amely ellenőrzi a PRT-t az Azure AD-ben, mielőtt frissíti azt a saját gyorsítótárába. A Cloud AP beépülő modul az új PRT-ket fogja használni. |
| E | A WAM biztosítja az újonnan kiállított hozzáférési tokent a WAM számára, ami viszont visszaadja a hívó alkalmazásnak|

### <a name="browser-sso-using-prt"></a>Böngészőalapú egyszeri bejelentkezés a PRT használatával

![Böngészőalapú egyszeri bejelentkezés a PRT használatával](./media/concept-primary-refresh-token/browser-sso-using-prt.png)

| Lépés | Leírás |
| :---: | --- |
| A | A felhasználó hitelesítő adataival bejelentkezik a Windowsba a PRT beszerzéséhez. Miután a felhasználó megnyitta a böngészőt, a böngésző (vagy a bővítmény) betölti az URL-címeket a beállításjegyzékből. |
| B | Amikor egy felhasználó megnyit egy Azure AD bejelentkezési URL-címet, a böngésző vagy a bővítmény érvényesíti az URL-címet a beállításjegyzékből beszerzett adatokkal. Ha egyeznek, a böngésző meghívja a natív ügyfél-gazdagépet a jogkivonat lekéréséhez. |
| C | A natív ügyfél gazdagépe ellenőrzi, hogy az URL-címek szerepelnek-e a Microsoft Identity Providers (Microsoft-fiók vagy az Azure AD) szolgáltatásban, kinyeri az URL-címről érkező egyszeres hívást, és meghívja a CloudAP beépülő modult a PRT-cookie-hoz |
| T | A CloudAP beépülő modul létrehozza a PRT cookie-t, majd bejelentkezik a TPM-kötésű munkamenetkulcs használatával, és visszaküldi a natív ügyfél-gazdagépre. |
| E | A natív ügyfél-gazdagép visszaadja ezt a PRT-cookie-t a böngészőnek, amely magában foglalja az x-MS-RefreshTokenCredential nevű kérelem fejlécének részeként, és az Azure AD-től kér jogkivonatokat. |
| F | Az Azure AD ellenőrzi a munkamenet-kulcs aláírását a PRT-cookie-ban, érvényesíti az időpontot, ellenőrzi, hogy az eszköz érvényes-e a bérlőben, és kiadja a weboldalhoz tartozó azonosító jogkivonatot, valamint a böngészőhöz tartozó titkosított munkamenet-cookie-t. |

> [!NOTE]
> A fenti lépésekben ismertetett böngészőalapú SSO-folyamat nem alkalmazható olyan munkamenetek esetén, mint például az InPrivate a Microsoft Edge-ben vagy a Google Chrome-ban (a Microsoft accounts bővítmény használatakor).

## <a name="next-steps"></a>Következő lépések

A PRT-vel kapcsolatos problémák elhárításáról további információt a [hibrid Azure Active Directory csatlakoztatott Windows 10 és Windows Server 2016 rendszerű eszközök hibaelhárítása](troubleshoot-hybrid-join-windows-current.md)című cikkben talál.
