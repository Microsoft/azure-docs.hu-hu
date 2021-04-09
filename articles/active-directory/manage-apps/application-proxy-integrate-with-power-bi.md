---
title: Távoli hozzáférés engedélyezése Power BI Azure-AD Application Proxy
description: Ismerteti a helyszíni Power BI és az Azure AD Application Proxy integrálásának alapjait.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/25/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro, has-adal-ref
ms.collection: M365-identity-device-management
ms.openlocfilehash: e9b0cce11abe1076d26ac8d4c4dc57c9b57c4737
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105625581"
---
# <a name="enable-remote-access-to-power-bi-mobile-with-azure-ad-application-proxy"></a>A Power BI Mobile távoli elérésének engedélyezése Azure AD-alkalmazásproxyval

Ez a cikk azt ismerteti, hogyan használható az Azure AD Application Proxy annak engedélyezéséhez, hogy a Power BI Mobile App Power BI jelentéskészítő kiszolgáló (PBIRS) és a SQL Server Reporting Services (SSRS) 2016-es vagy újabb verziójához kapcsolódjon. Ezen integráció révén a vállalati hálózaton kívüli felhasználók hozzáférhetnek a Power BI-jelentésekhez a Power BI Mobile alkalmazásból, és az Azure AD-hitelesítéssel is védhetők. Ez a védelem olyan [biztonsági előnyöket](application-proxy-security.md#security-benefits) foglal magában, mint például a feltételes hozzáférés és a többtényezős hitelesítés.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy már telepítette a Report Services és az [enabled Application proxy](application-proxy-add-on-premises-application.md)szolgáltatást.

- Az alkalmazásproxy engedélyezéséhez telepítenie kell egy összekötőt egy Windows-kiszolgálón, és el kell töltenie az [előfeltételeket](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) , hogy az összekötő képes legyen kommunikálni az Azure ad-szolgáltatásokkal.
- A Power BI közzétételekor azt javasoljuk, hogy ugyanazokat a belső és külső tartományokat használja. Az egyéni tartományokkal kapcsolatos további tudnivalókért tekintse meg az [Egyéni tartományok használata az Application proxyban](./application-proxy-configure-custom-domain.md)című témakört.
- Ez az integráció a **Power bi Mobile iOS-és Android-** alkalmazásokhoz érhető el.

## <a name="step-1-configure-kerberos-constrained-delegation-kcd"></a>1. lépés: a Kerberos által korlátozott delegálás konfigurálása (KCD)

A Windows-hitelesítést használó helyszíni alkalmazások esetében egyszeri bejelentkezést (SSO) érhet el a Kerberos hitelesítési protokoll és a Kerberos által korlátozott delegálás (KCD) nevű funkció használatával. Ha be van állítva, a KCD lehetővé teszi, hogy az alkalmazásproxy-összekötő beszerezzen egy Windows-jogkivonatot a felhasználó számára akkor is, ha a felhasználó nem jelentkezett be közvetlenül a Windowsba. További információ a KCD: a [Kerberos által korlátozott delegálás áttekintése](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj553400(v=ws.11)) és a [Kerberos által korlátozott delegálás az alkalmazásokba való egyszeri bejelentkezéshez](application-proxy-configure-single-sign-on-with-kcd.md).

A Reporting Services oldalán nem sok mindent kell konfigurálni. Győződjön meg arról, hogy érvényes egyszerű szolgáltatásnév (SPN) van a megfelelő Kerberos-hitelesítés engedélyezéséhez. Győződjön meg arról is, hogy a Reporting Services-kiszolgáló engedélyezve van az egyeztetéses hitelesítéshez.

A Reporting Services KCD beállításához folytassa a következő lépésekkel.

### <a name="configure-the-service-principal-name-spn"></a>Egyszerű szolgáltatásnév (SPN) konfigurálása

Az SPN egy egyedi azonosító egy Kerberos-hitelesítést használó szolgáltatáshoz. Győződjön meg arról, hogy rendelkezik megfelelő HTTP SPN-vel a jelentéskészítő kiszolgálón. Információk a megfelelő egyszerű szolgáltatásnév (SPN) konfigurálásáról a jelentéskészítő kiszolgálón: [Egyszerű szolgáltatásnév (SPN) regisztrálása egy jelentéskészítő kiszolgálóhoz](/sql/reporting-services/report-server/register-a-service-principal-name-spn-for-a-report-server).
A Setspn parancs a-L kapcsolóval való futtatásával ellenőrizheti, hogy az egyszerű szolgáltatásnév hozzá lett-e adva. További információ a parancsról: [Setspn](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spn-setspn-syntax.aspx).

### <a name="enable-negotiate-authentication"></a>Egyeztetéses hitelesítés engedélyezése

Ha engedélyezni szeretné a jelentéskészítő kiszolgáló számára a Kerberos-hitelesítés használatát, konfigurálja a jelentéskészítő kiszolgáló hitelesítési típusát RSWindowsNegotiate. Konfigurálja a beállítást a rsreportserver.config fájl használatával.

```xml
<AuthenticationTypes>
    <RSWindowsNegotiate />
    <RSWindowsKerberos />
    <RSWindowsNTLM />
</AuthenticationTypes>
```

További információk: [Reporting Services konfigurációs fájl módosítása](/sql/reporting-services/report-server/modify-a-reporting-services-configuration-file-rsreportserver-config) és [Windows-hitelesítés konfigurálása egy jelentéskészítő kiszolgálón](/sql/reporting-services/security/configure-windows-authentication-on-the-report-server).

### <a name="ensure-the-connector-is-trusted-for-delegation-to-the-spn-added-to-the-reporting-services-application-pool-account"></a>Győződjön meg arról, hogy az összekötő megbízható a Reporting Services alkalmazáskészlet-fiókhoz hozzáadott egyszerű szolgáltatásnév delegálásához.
Konfigurálja úgy a KCD, hogy az Azure AD Application Proxy szolgáltatás delegálja a felhasználói identitásokat a Reporting Services alkalmazáskészlet-fiókjába. Konfigurálja a KCD-t úgy, hogy engedélyezze az alkalmazásproxy összekötője számára a Kerberos-jegyek lekérését az Azure AD által hitelesített felhasználók számára. Ezt követően a kiszolgáló továbbítja a környezetet a célalkalmazás vagy a Reporting Services szolgáltatásnak ebben az esetben.

A KCD konfigurálásához ismételje meg az alábbi lépéseket minden összekötő-gépen:

1. Jelentkezzen be tartományi rendszergazdaként egy tartományvezérlőre, majd nyissa meg **Active Directory felhasználókat és számítógépeket**.
2. Keresse meg a számítógépet, amelyen az összekötő fut.
3. Kattintson duplán a számítógépre, majd válassza a **Delegálás** lapot.
4. Állítsa a delegálási beállításokat **A számítógépen csak a megadott szolgáltatások delegálhatók** értékre. Ezután válassza **a bármely hitelesítési protokoll használata** lehetőséget.
5. Válassza a **Hozzáadás**, majd a **Felhasználók vagy számítógépek** lehetőséget.
6. Adja meg a Reporting Services szolgáltatáshoz használt szolgáltatásfiókot. Ehhez a fiókhoz adta hozzá a SPN-t a Reporting Services konfigurálása során.
7. Kattintson az **OK** gombra. A módosítások mentéséhez kattintson újra az **OK** gombra.

További információ: [Kerberos által korlátozott delegálás az alkalmazásokba való egyszeri bejelentkezéshez az Application proxy használatával](application-proxy-configure-single-sign-on-with-kcd.md).

## <a name="step-2-publish-report-services-through-azure-ad-application-proxy"></a>2. lépés: a Reporting Services közzététele az Azure AD Application Proxy

Most már készen áll az Azure AD-alkalmazásproxy konfigurálására.

1. Jelentéskészítési szolgáltatások közzététele az Application proxyn keresztül a következő beállításokkal. Az alkalmazások alkalmazásproxyval történő közzétételével kapcsolatos részletes utasításokért tekintse meg az [Alkalmazások közzététele az Azure AD-alkalmazásproxyval](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad) szakaszt.
   - **Belső URL-cím**: adja meg annak a jelentéskészítő kiszolgálónak az URL-címét, amelyet az összekötő elérhet a vállalati hálózaton. Győződjön meg arról, hogy az URL-cím elérhető arról a kiszolgálóról, amelyre az összekötő telepítve van. Az ajánlott eljárásnak megfelelően legfelső szintű tartományt használjon (például a `https://servername/` tartományt) az alkalmazásproxyval közzétett alútvonalakkal kapcsolatos problémák elkerülése érdekében. Például használja a `https://servername/` URL-címet a `https://servername/reports/` vagy a `https://servername/reportserver/` helyett.
     > [!NOTE]
     > A jelentéskészítő kiszolgálóhoz biztonságos HTTPS-kapcsolat használatát javasoljuk. Útmutatásért tekintse meg [az SSL-kapcsolatok natív módú jelentéskészítő kiszolgálón való konfigurálását](/sql/reporting-services/security/configure-ssl-connections-on-a-native-mode-report-server) ismertető szakaszt.
   - **Külső URL-cím**: adja meg a nyilvános URL-címet, amelyhez a Power bi Mobile alkalmazás csatlakozni fog. Ha egyéni tartományt használ, akkor például így nézhet ki: `https://reports.contoso.com`. Egyéni tartomány használatához töltse fel a tartomány tanúsítványát, és irányítson át egy DNS-rekordot az alkalmazás alapértelmezett msappproxy.net tartományára. A részletes lépésekért tekintse meg [az egyéni tartományok Azure AD-alkalmazásproxyban történő használatát](application-proxy-configure-custom-domain.md) ismertető szakaszt.

   - **Előhitelesítési módszer**: Azure Active Directory

2. Az alkalmazás közzététele után konfigurálja az egyszeri bejelentkezési beállításokat az alábbi lépések segítségével:

   a. A portálon az alkalmazás lapján válassza az **Egyszeri bejelentkezés** lehetőséget.

   b. Az **Egyszeri bejelentkezési mód** esetében válassza az **Integrált Windows-hitelesítés** lehetőséget.

   c. Állítsa a **Belső alkalmazás egyszerű szolgáltatásneve** értékét a korábban beállított értékre.

   d. Válassza ki az összekötő **Delegált bejelentkezési identitását** a felhasználók nevében történő használathoz. További információkért tekintse meg [a különböző helyszíni és felhőidentitások használatát](application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities) ismertető szakaszt.

   e. Kattintson a **Mentés** gombra a módosítások mentéséhez.

Az alkalmazás beállításának befejezéséhez nyissa meg **a felhasználók és csoportok** szakaszt, és rendelje hozzá a felhasználókat az alkalmazás eléréséhez.

## <a name="step-3-modify-the-reply-uris-for-the-application"></a>3. lépés: a válasz URI-azonosítójának módosítása az alkalmazáshoz

Mielőtt a Power BI Mobile App csatlakozhat és hozzáférhessen a Report Services szolgáltatáshoz, konfigurálnia kell az alkalmazás regisztrációját, amelyet a 2. lépésben automatikusan hozott létre.

1. Az Azure Active Directory **Áttekintés** lapján válassza az **Alkalmazásregisztrációk** lehetőséget.
2. A **minden alkalmazás** lapon keresse meg a 2. lépésben létrehozott alkalmazást.
3. Válassza ki az alkalmazást, majd válassza a **Hitelesítés** lehetőséget.
4. Adja hozzá az alábbi Átirányítási URI-kat a használt platformtól függően.

   Power BI Mobile **iOS**-hez készült alkalmazás konfigurálásakor adja hozzá a következő átirányítási URI-k nyilvános ügyfélként (Mobile & Desktop):
   - `msauth://code/mspbi-adal%3a%2f%2fcom.microsoft.powerbimobile`
   - `msauth://code/mspbi-adalms%3a%2f%2fcom.microsoft.powerbimobilems`
   - `mspbi-adal://com.microsoft.powerbimobile`
   - `mspbi-adalms://com.microsoft.powerbimobilems`

   Power BI Mobile **Androidhoz** készült alkalmazás konfigurálásakor adja hozzá a következő átirányítási URI-k nyilvános ügyfélként (Mobile & Desktop):
   - `urn:ietf:wg:oauth:2.0:oob`
   - `mspbi-adal://com.microsoft.powerbimobile`
   - `msauth://com.microsoft.powerbim/g79ekQEgXBL5foHfTlO2TPawrbI%3D`
   - `msauth://com.microsoft.powerbim/izba1HXNWrSmQ7ZvMXgqeZPtNEU%3D`

   > [!IMPORTANT]
   > Ahhoz, hogy az alkalmazás megfelelően működjön, hozzá kell adni az Átirányítási URI-kat. Ha az alkalmazást Power BI Mobile iOS és Android rendszerhez is konfigurálja, adja hozzá a nyilvános ügyfél (Mobile & Desktop) típusú átirányítási URI-t az iOS-hez konfigurált átirányítási URI-k listájához: `urn:ietf:wg:oauth:2.0:oob` .

## <a name="step-4-connect-from-the-power-bi-mobile-app"></a>4. lépés: a Power BI Mobile alkalmazásból való kapcsolat

1. A Power BI Mobile alkalmazásban kapcsolódjon a Reporting Services-példányhoz. Ehhez adja meg az Application proxyn keresztül közzétett alkalmazás **külső URL-címét** .

   ![Power BI Mobile alkalmazás külső URL-címmel](media/application-proxy-integrate-with-power-bi/app-proxy-power-bi-mobile-app.png)

2. Válassza a **Kapcsolódás** lehetőséget. A rendszer átirányítja a Azure Active Directory bejelentkezési oldalára.

3. Adjon meg érvényes hitelesítő adatokat a felhasználóhoz, és válassza a **Bejelentkezés** lehetőséget. Ekkor megjelennek a Reporting Services-kiszolgáló elemei.

## <a name="step-5-configure-intune-policy-for-managed-devices-optional"></a>5. lépés: az Intune-szabályzat konfigurálása a felügyelt eszközökhöz (nem kötelező)

A Microsoft Intune segítségével kezelheti a vállalat munkaerő által használt ügyfélalkalmazások felügyeletét. Az Intune lehetővé teszi olyan képességek használatát, mint például az adattitkosítás és a további hozzáférési követelmények. Az alkalmazások Intune-nal történő kezelésével kapcsolatos további információkért lásd: az Intune app Management. Az alábbi lépéseket követve engedélyezheti, hogy a Power BI Mobile Application működjön az Intune-szabályzattal.

1. Nyissa meg **Azure Active Directory** , majd az **alkalmazások regisztrációját**.
2. Válassza ki a natív ügyfélalkalmazás regisztrálása során a 3. lépésben konfigurált alkalmazást.
3. Az alkalmazás lapján válassza az API- **engedélyek** lehetőséget.
4. Kattintson **az engedély hozzáadása** lehetőségre.
5. A **saját szervezet által használt API**-k területen keressen rá a "Microsoft Mobile Application Management" kifejezésre, és válassza ki.
6. Adja hozzá a **DeviceManagementManagedApps. READWRITE** engedélyt az alkalmazáshoz
7. Kattintson a **rendszergazdai jóváhagyás megadása** lehetőségre az alkalmazáshoz való hozzáférés engedélyezéséhez.
8. Konfigurálja a kívánt Intune- [szabályzatot az alkalmazás-védelmi házirendek létrehozásához és hozzárendeléséhez](/intune/app-protection-policies).

## <a name="troubleshooting"></a>Hibaelhárítás

Ha az alkalmazás a jelentés több percnél hosszabb betöltésére tett kísérlet után egy hibaüzenetet ad vissza, előfordulhat, hogy módosítania kell az időtúllépési beállítást. Alapértelmezés szerint az alkalmazásproxy olyan alkalmazásokat támogat, amelyek akár 85 másodpercet is igénybe vesznek a kérelmek megválaszolására. A beállítás 180 másodpercre való meghosszabbításához válassza ki az alkalmazáshoz **tartozó alkalmazásproxy** -beállítások lapon a háttérbeli időtúllépést. A gyors és megbízható jelentések létrehozásával kapcsolatos tippekért tekintse meg [Power bi jelentések – ajánlott eljárások](/power-bi/power-bi-reports-performance)című témakört.

Az Azure AD Application Proxy használatával lehetővé teheti, hogy a Power BI Mobile App a helyszíni Power BI jelentéskészítő kiszolgálóhoz kapcsolódjon, és nem támogatott olyan feltételes hozzáférési szabályzatok használata, amelyeken a Microsoft Power BI alkalmazás jóváhagyott ügyfélalkalmazás.

## <a name="next-steps"></a>Következő lépések

- [Natív ügyfélalkalmazások engedélyezése a proxy alkalmazásokkal való kommunikációhoz](application-proxy-configure-native-client-application.md)
- [Helyszíni jelentéskészítő kiszolgálói jelentések és KPI-k megtekintése a Power BI-mobilalkalmazásokban](/power-bi/consumer/mobile/mobile-app-ssrs-kpis-mobile-on-premises-reports)
