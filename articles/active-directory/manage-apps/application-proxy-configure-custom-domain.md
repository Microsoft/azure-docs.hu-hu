---
title: Egyéni tartományok az Azure-ban AD Application Proxy
description: Egyéni tartományok konfigurálása és kezelése az Azure AD Application Proxyban.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 10/24/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: dadb23b9a8d00d484a9c42db9007d8c5b94b4c78
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107306789"
---
# <a name="configure-custom-domains-with-azure-ad-application-proxy"></a>Egyéni tartományok konfigurálása az Azure AD-alkalmazásproxyval

Amikor Azure Active Directory Application Proxyon keresztül tesz közzé alkalmazást, létrehoz egy külső URL-címet a felhasználók számára. Ez az URL-cím az alapértelmezett tartományi *yourtenant.msappproxy.net* kapja meg. Ha például közzétesz egy, a *contoso* nevű bérlőn a *költségek* nevű alkalmazást, a külső URL-cím *https: \/ /expenses-contoso.msappproxy.net*. Ha a *msappproxy.net* helyett saját tartománynevet szeretne használni, beállíthatja az alkalmazás egyéni tartományát. 

## <a name="benefits-of-custom-domains"></a>Az egyéni tartományok előnyei

Hasznos lehet egyéni tartományokat beállítani az alkalmazásaihoz, amikor csak lehetséges. Az egyéni tartományok használatának bizonyos okai a következők:

- Az alkalmazások közötti kapcsolatok a vállalati hálózaton kívül is működnek. Egyéni tartomány nélkül, ha az alkalmazás nem rögzített belső hivatkozásokat tartalmaz az Application proxyn kívüli tárolókra, és a hivatkozások nem oldhatók fel külsőleg, akkor a rendszer megtöri. Ha a belső és külső URL-címek megegyeznek, ezzel elkerülhető a probléma. Ha nem tud egyéni tartományokat használni, tekintse meg az [Azure ad Application proxy közzétett alkalmazások átirányítása hardcoded hivatkozásait](./application-proxy-configure-hard-coded-link-translation.md) a probléma megoldásának egyéb módjaira. 
  
- A felhasználók könnyebben használhatóvá válnak, mivel a hálózaton belül vagy kívül is elérhetik az alkalmazást ugyanazzal az URL-címmel. Nem kell különböző belső és külső URL-címeket tanulniuk, vagy követniük a jelenlegi helyüket. 

- Megadhatja a saját arculatát, és létrehozhatja a kívánt URL-címeket. Az egyéni tartomány segíthet felépíteni a felhasználók bizalmát, mivel a felhasználók ismerős nevet látnak és használhatnak a *msappproxy.net* helyett.

- Egyes konfigurációk csak egyéni tartományokkal működnek. Előfordulhat például, hogy egyéni tartományokra van szüksége a Security Assertion Markup Language (SAML) szolgáltatást használó alkalmazásokhoz, például ha Active Directory összevonási szolgáltatások (AD FS) (AD FS) használ, de nem tudja használni a WS-Federation-t. További információ: [jogcímbarát alkalmazások használata az Application proxyban](application-proxy-configure-for-claims-aware-applications.md). 

Ha nem teszi lehetővé a belső és külső URL-címek egyeztetését, nem annyira fontos, hogy egyéni tartományokat használjon, de továbbra is használhatja a többi előnyt. 

## <a name="dns-configuration-options"></a>DNS-konfigurációs beállítások

A DNS-konfiguráció beállításához több lehetőség is rendelkezésre áll a követelményektől függően:

### <a name="same-internal-and-external-url-different-internal-and-external-behavior"></a>Azonos belső és külső URL-cím, különböző belső és külső viselkedés 

Ha nem szeretné, hogy a belső felhasználók az alkalmazásproxy használatával legyenek átirányítva, beállíthat egy *megosztott agyi DNS-t*. A megosztott DNS-infrastruktúra a belső gazdagépeket egy belső tartománynév-kiszolgálóra és külső gazdagépekre irányítja a névfeloldáshoz. 

![Maszkolt DNS](./media/application-proxy-configure-custom-domain/split-brain-dns.png)

### <a name="different-internal-and-external-urls"></a>Különböző belső és külső URL-címek 

Ha a belső és külső URL-címek eltérőek, nem kell konfigurálnia a megosztott agyi viselkedést, mert a felhasználói útválasztást az URL határozza meg. Ebben az esetben csak a külső DNS-t módosítja, és a külső URL-címet az alkalmazásproxy-végpontra irányítja. 

Amikor kijelöl egy külső URL-cím egyéni tartományát, egy információs sáv megjeleníti a külső DNS-szolgáltatóhoz hozzáadandó CNAME-bejegyzést. Ezt az információt bármikor megtekintheti az **alkalmazás** alkalmazásproxy lapján.

## <a name="set-up-and-use-custom-domains"></a>Egyéni tartományok beállítása és használata

Ha a helyszíni alkalmazást egyéni tartomány használatára szeretné konfigurálni, egy ellenőrzött Azure Active Directory egyéni tartományra, egy, az egyéni tartomány PFX-tanúsítványára, valamint egy, a konfigurálandó helyszíni alkalmazásra van szükség. 

### <a name="create-and-verify-a-custom-domain"></a>Egyéni tartomány létrehozása és ellenőrzése

Egyéni tartomány létrehozása és ellenőrzése:

1. A Azure Active Directory területen válassza az **Egyéni tartománynevek** lehetőséget a bal oldali navigációs sávon, majd válassza az **egyéni tartomány hozzáadása** lehetőséget. 
1. Adja meg az egyéni tartománynevet, és válassza a **tartomány hozzáadása** lehetőséget. 
1. A tartomány lapon másolja a tartomány TXT-rekordjának adatait. 
1. Nyissa meg a tartományregisztráló, és hozzon létre egy új TXT-rekordot a tartományhoz a másolt DNS-adatok alapján.
1. A tartomány regisztrálása után az Azure Active Directory tartomány lapján válassza az **ellenőrzés** lehetőséget. A tartomány állapotának **ellenőrzése** után a tartományt használhatja az összes Azure ad-konfigurációban, beleértve az alkalmazásproxy-t is. 

További részletekért lásd az [Egyéni tartománynév hozzáadása a Azure Active Directory portál használatával](../fundamentals/add-custom-domain.md)című témakört.

### <a name="configure-an-app-to-use-a-custom-domain"></a>Alkalmazás konfigurálása egyéni tartomány használatára

Az alkalmazás közzététele az Application proxyn keresztül egyéni tartománnyal:

1. Új alkalmazás esetén a Azure Active Directory válassza a **vállalati alkalmazások** lehetőséget a bal oldali navigációs sávon. Válassza az **Új alkalmazás** lehetőséget. A helyszíni **alkalmazások** szakaszban válassza **a helyszíni alkalmazás hozzáadása** lehetőséget. 
   
   Ha egy alkalmazás már **vállalati alkalmazásokban** van, válassza ki a listából, majd válassza ki a bal oldali navigációs felületen a **alkalmazásproxy** elemet. 

2. Az alkalmazásproxy beállításai lapon adjon meg egy **nevet** , ha saját helyszíni alkalmazást ad hozzá.

3.  A **belső URL-cím** mezőben adja meg az alkalmazás belső URL-címét.
   
4. A **külső URL-cím** mezőben adja le a listát, és válassza ki a használni kívánt egyéni tartományt.
   
5. Válassza a **Hozzáadás** lehetőséget.
   
   ![Egyéni tartomány kiválasztása](./media/application-proxy-configure-custom-domain/application-proxy.png)
   
6. Ha a tartomány már rendelkezik tanúsítvánnyal, a **tanúsítvány** mező a tanúsítvány adatait jeleníti meg. Ellenkező esetben válassza a **tanúsítvány** mezőt.
   
   ![Kattintson ide a tanúsítvány feltöltéséhez](./media/application-proxy-configure-custom-domain/certificate.png)
   
7. Az **SSL-tanúsítvány** lapon keresse meg és válassza ki a pfx-tanúsítványfájl. Adja meg a tanúsítványhoz tartozó jelszót, majd kattintson a **tanúsítvány feltöltése** lehetőségre. A tanúsítványokkal kapcsolatos további információkért tekintse meg az [Egyéni tartományok tanúsítványainak](#certificates-for-custom-domains) című szakaszt. Ha a tanúsítvány érvénytelen, vagy probléma van a jelszóval, hibaüzenet jelenik meg. Az [alkalmazásproxy gyakori kérdéseiben](application-proxy-faq.yml#application-configuration) néhány olyan hibaelhárítási lépés található, amelyet kipróbálhat.
   
   ![Tanúsítvány feltöltése](./media/application-proxy-configure-custom-domain/ssl-certificate.png)
   
   > [!TIP] 
   > Az egyéni tartományoknak csak egyszer kell feltölteniük a tanúsítványát. Ezt követően a rendszer automatikusan alkalmazza a feltöltött tanúsítványt, ha az egyéni tartományt használja más alkalmazásokhoz.
   
8. Ha hozzáadta a tanúsítványt, az **alkalmazásproxy** lapon válassza a **Mentés** lehetőséget. 
   
9. Az **alkalmazásproxy** oldalon található információs sávban jegyezze fel a DNS-zónához hozzáadandó CNAME-bejegyzést. 
   
   ![CNAME DNS-bejegyzés hozzáadása](./media/application-proxy-configure-custom-domain/dns-info.png)
   
10. Kövesse a [DNS-rekordok és-rekordhalmazok kezelése a Azure Portal használatával](../../dns/dns-operations-recordsets-portal.md) című témakör utasításait, és adjon hozzá egy DNS-rekordot, amely átirányítja az új külső URL-címet a *msappproxy.net* tartományhoz.
   
11. Annak ellenőrzéséhez, hogy a DNS-rekord megfelelően van-e konfigurálva, az [nslookup](https://social.technet.microsoft.com/wiki/contents/articles/29184.nslookup-for-beginners.aspx) paranccsal ellenőrizze, hogy a külső URL-cím elérhető-e, és hogy a *msapproxy.net* tartomány aliasként jelenik-e meg.

Az alkalmazás mostantól az egyéni tartomány használatára van beállítva. Győződjön meg arról, hogy a tesztelés vagy a kibocsátás előtt a felhasználókat az alkalmazáshoz rendeli. 

Egy alkalmazás tartományának módosításához válasszon egy másik tartományt az **alkalmazás alkalmazásproxy** lapján található **külső URL-cím** legördülő listából. Szükség esetén töltse fel a frissített tartomány tanúsítványát, és frissítse a DNS-rekordot. Ha nem látja a kívánt egyéni tartományt a **külső URL-cím** legördülő listájában, akkor előfordulhat, hogy a rendszer nem ellenőrzi.

Az alkalmazásproxy részletes ismertetése: [oktatóanyag: helyszíni alkalmazás hozzáadása a táveléréshez az Application proxyn keresztül Azure Active Directory](application-proxy-add-on-premises-application.md).

## <a name="certificates-for-custom-domains"></a>Egyéni tartományokhoz tartozó tanúsítványok

A tanúsítvány létrehozza a biztonságos TLS-kapcsolatokat az egyéni tartományhoz. 

### <a name="certificate-formats"></a>Tanúsítványok formátuma

PFX-tanúsítványt kell használnia annak biztosításához, hogy az összes szükséges közbenső tanúsítvány megtalálható legyen. A tanúsítványnak tartalmaznia kell a titkos kulcsot.

A leggyakoribb tanúsítvány-aláírási módszerek (például a tulajdonos alternatív neve (SAN)) támogatottak. 

Helyettesítő tanúsítványokat használhat, ha a helyettesítő karakter megfelel a külső URL-címnek. Helyettesítő tanúsítványokat kell használnia [helyettesítő alkalmazásokhoz](application-proxy-wildcard.md). Ha azt szeretné, hogy a tanúsítvány az altartományokhoz is hozzáférhessen, akkor az altartomány helyettesítő karaktereit a tulajdonos alternatív neveiként kell hozzáadnia ugyanabban a tanúsítványban. Például a. *\* Adventure-Works.com* tanúsítványa nem fog működni a *\* . apps.Adventure-Works.com* esetében, ha a *\* . apps.Adventure-Works.com* nevet adja a tulajdonos alternatív neveként. 

A saját nyilvános kulcsokra épülő infrastruktúrája (PKI) által kiadott tanúsítványokat használhatja, ha a tanúsítványlánc telepítve van az ügyféleszközök számára. Az Intune ezeket a tanúsítványokat a felügyelt eszközökön is üzembe helyezheti. A nem felügyelt eszközökön manuálisan kell telepítenie ezeket a tanúsítványokat. 

Nem ajánlott privát legfelső szintű HITELESÍTÉSSZOLGÁLTATÓT használni, mivel a privát legfelső szintű HITELESÍTÉSSZOLGÁLTATÓT is le kell küldeni az ügyfélszámítógépekre, ami számos kihívást jelenthet.

### <a name="certificate-management"></a>Tanúsítványkezelés

Az összes tanúsítványkezelő az egyes alkalmazások oldalain keresztül történik. Nyissa meg **az alkalmazás alkalmazásproxy lapját** a **tanúsítvány** mező eléréséhez.

A tanúsítvány feltöltése után a rendszer automatikusan alkalmazza azokat az **új** alkalmazásokra is, amelyek ugyanazt a tanúsítványt használják. A meglévő alkalmazásokhoz tartozó tanúsítványt újra fel kell töltenie a bérlőben.

Egy tanúsítvány lejáratakor figyelmeztető üzenet jelenik meg, amely egy másik tanúsítvány feltöltését kéri. Ha a tanúsítványt visszavonják, a felhasználók biztonsági figyelmeztetést kaphatnak az alkalmazáshoz való hozzáféréskor. Egy alkalmazás tanúsítványának frissítéséhez navigáljon az **alkalmazás alkalmazásproxy** lapjára, válassza a **tanúsítvány** lehetőséget, és töltsön fel egy új tanúsítványt. Ha a régi tanúsítványt más alkalmazások nem használják, azt a rendszer automatikusan törli. 

## <a name="next-steps"></a>Következő lépések

* [Engedélyezze az egyszeri bejelentkezést](application-proxy-configure-single-sign-on-with-kcd.md) a közzétett alkalmazásokban az Azure ad-hitelesítéssel.
* [Feltételes hozzáférés](../conditional-access/concept-conditional-access-cloud-apps.md) a közzétett felhőalapú alkalmazásokhoz.