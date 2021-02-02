---
title: Oktatóanyag – LDAP-beállítások konfigurálása a Azure Active Directory Domain Serviceshoz | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan konfigurálhatja a biztonságos Lightweight Directory Access Protocol (LDAPs) szolgáltatást egy Azure Active Directory Domain Services felügyelt tartományhoz.
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 6da1d285440daa5d1d5a230905a77057728d4ae6
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99256542"
---
# <a name="tutorial-configure-secure-ldap-for-an-azure-active-directory-domain-services-managed-domain"></a>Oktatóanyag: biztonságos LDAP konfigurálása Azure Active Directory Domain Services felügyelt tartományhoz

A Azure Active Directory Domain Services (Azure AD DS) felügyelt tartománysal való kommunikációhoz a Lightweight Directory Access Protocol (LDAP) használatos. Alapértelmezés szerint az LDAP-forgalom nincs titkosítva, ami biztonsági szempontból fontos a sok környezetben.

Az Azure AD DS használatával a felügyelt tartományt biztonságos Lightweight Directory Access Protocol (LDAPs) használatára állíthatja be. A biztonságos LDAP használata esetén a rendszer titkosítja a forgalmat. Secure LDAP más néven LDAP SSL (SSL)/Transport Layer Security (TLS) protokollon keresztül.

Ebből az oktatóanyagból megtudhatja, hogyan konfigurálhat LDAPs-t egy Azure AD DS felügyelt tartományhoz.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Digitális tanúsítvány létrehozása az Azure AD DS használatával
> * Biztonságos LDAP engedélyezése az Azure-ban AD DS
> * Biztonságos LDAP konfigurálása a nyilvános interneten való használatra
> * Biztonságos LDAP-kötés és-tesztelés felügyelt tartományhoz

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt [hozzon létre egy fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következő erőforrásokra és jogosultságokra van szüksége:

* Aktív Azure-előfizetés.
    * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Az előfizetéshez társított Azure Active Directory bérlő, vagy egy helyszíni címtárral vagy egy csak felhőalapú címtárral van szinkronizálva.
    * Ha szükséges, [hozzon létre egy Azure Active Directory bérlőt][create-azure-ad-tenant] , vagy [rendeljen hozzá egy Azure-előfizetést a fiókjához][associate-azure-ad-tenant].
* Egy Azure Active Directory Domain Services felügyelt tartomány engedélyezve és konfigurálva van az Azure AD-bérlőben.
    * Ha szükséges, [hozzon létre és konfiguráljon egy Azure Active Directory Domain Services felügyelt tartományt][create-azure-ad-ds-instance].
* A számítógépre telepített *LDP.exe* eszköz.
    * Ha szükséges, [telepítse a Távoli kiszolgálófelügyelet eszközei (RSAT)][rsat] *Active Directory Domain Services és az LDAP* szolgáltatáshoz.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Ebben az oktatóanyagban a Azure Portal használatával konfigurálja a felügyelt tartomány biztonságos LDAP-szolgáltatását. Első lépésként jelentkezzen be a [Azure Portalba](https://portal.azure.com).

## <a name="create-a-certificate-for-secure-ldap"></a>Tanúsítvány létrehozása a biztonságos LDAP-hez

A biztonságos LDAP használatához digitális tanúsítvány használatos a kommunikáció titkosításához. A rendszer ezt a digitális tanúsítványt alkalmazza a felügyelt tartományra, és lehetővé teszi, hogy az eszközök, például a *LDP.exe* biztonságos titkosított kommunikációt használjanak az adatlekérdezés során. A felügyelt tartományhoz való biztonságos LDAP-hozzáféréshez kétféleképpen hozhat létre tanúsítványt:

* Egy nyilvános hitelesítésszolgáltatótól (CA) vagy vállalati HITELESÍTÉSSZOLGÁLTATÓTÓL származó tanúsítvány.
    * Ha a szervezete egy nyilvános HITELESÍTÉSSZOLGÁLTATÓTÓL szerzi be a tanúsítványokat, szerezze be a biztonságos LDAP-tanúsítványt a nyilvános HITELESÍTÉSSZOLGÁLTATÓTÓL. Ha vállalati HITELESÍTÉSSZOLGÁLTATÓT használ a szervezetben, szerezze be a biztonságos LDAP-tanúsítványt a vállalati HITELESÍTÉSSZOLGÁLTATÓTÓL.
    * A nyilvános HITELESÍTÉSSZOLGÁLTATÓK csak akkor működnek, ha egyéni DNS-nevet használ a felügyelt tartományhoz. Ha a felügyelt tartomány DNS-tartományneve a *. onmicrosoft.com*-ben ér véget, nem hozhat létre digitális tanúsítványt az ezzel az alapértelmezett tartománnyal való kapcsolódás biztosításához. A Microsoft tulajdonosa a *. onmicrosoft.com* tartomány, így a nyilvános hitelesítésszolgáltatók nem bocsátanak ki tanúsítványt. Ebben az esetben hozzon létre egy önaláírt tanúsítványt, és használja azt a biztonságos LDAP konfigurálásához.
* Önaláírt tanúsítvány, amelyet Ön saját maga hoz létre.
    * Ez a módszer tesztelési célokra alkalmas, és ez az oktatóanyag mutatja.

A kért vagy létrehozott tanúsítványnak meg kell felelnie az alábbi követelményeknek. A felügyelt tartomány problémákba ütközik, ha a Secure LDAP-t érvénytelen tanúsítvánnyal engedélyezi:

* **Megbízható kiállító** – a tanúsítványt a felügyelt tartományhoz csatlakozó számítógépeknek megbízható LDAP használatával kell kibocsátania. Ez a hatóság lehet egy nyilvános HITELESÍTÉSSZOLGÁLTATÓ vagy egy, a számítógépek által megbízhatónak minősített vállalati HITELESÍTÉSSZOLGÁLTATÓ.
* **Élettartam** – a tanúsítványnak érvényesnek kell lennie legalább a következő 3-6 hónapra. Secure LDAP a felügyelt tartományhoz való hozzáférés megszakad, ha a tanúsítvány lejár.
* **Tulajdonos neve** – a tanúsítvány tulajdonosának neve csak a felügyelt tartomány lehet. Ha például a tartomány neve *aaddscontoso.com*, a tanúsítvány tulajdonosának a következőnek kell lennie: **. aaddscontoso.com*.
    * A tanúsítvány DNS-nevének vagy tulajdonosának alternatív nevének helyettesítő tanúsítványnak kell lennie ahhoz, hogy a biztonságos LDAP megfelelően működjön a Azure AD Domain Services. A tartományvezérlők véletlenszerű neveket használnak, és eltávolíthatók vagy hozzáadhatók, így biztosítható, hogy a szolgáltatás továbbra is elérhető maradjon.
* **Kulcshasználat** – a tanúsítványt a *digitális aláírásokhoz* és a *kulcsfontosságú titkosítási* kell konfigurálni.
* **Tanúsítvány célja** – a tanúsítványnak érvényesnek kell lennie a TLS-kiszolgáló hitelesítéséhez.

Több eszköz áll rendelkezésre az önaláírt tanúsítványok, például az OpenSSL, a MakeCert, a [New-SelfSignedCertificate][New-SelfSignedCertificate] parancsmag stb. létrehozásához.

Ebben az oktatóanyagban hozzunk létre egy önaláírt tanúsítványt a biztonságos LDAP-hez a [New-SelfSignedCertificate][New-SelfSignedCertificate] parancsmag használatával.

Nyisson meg egy PowerShell-ablakot **rendszergazdaként** , és futtassa a következő parancsokat. Cserélje le a *$dnsName* változót a saját felügyelt tartománya által használt DNS-névre, például *aaddscontoso.com*:

```powershell
# Define your own DNS name used by your managed domain
$dnsName="aaddscontoso.com"

# Get the current date to set a one-year expiration
$lifetime=Get-Date

# Create a self-signed certificate for use with Azure AD DS
New-SelfSignedCertificate -Subject *.$dnsName `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.$dnsName, $dnsName
```

A következő példa kimenete azt mutatja, hogy a tanúsítvány létrehozása sikeres volt, és a helyi tanúsítványtárolóban (*LocalMachine\MY*) tárolódik:

```output
PS C:\WINDOWS\system32> New-SelfSignedCertificate -Subject *.$dnsName `
>>   -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
>>   -Type SSLServerAuthentication -DnsName *.$dnsName, $dnsName.com

   PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\MY

Thumbprint                                Subject
----------                                -------
959BD1531A1E674EB09E13BD8534B2C76A45B3E6  CN=aaddscontoso.com
```

## <a name="understand-and-export-required-certificates"></a>A szükséges tanúsítványok ismertetése és exportálása

A biztonságos LDAP használatához a hálózati forgalom a nyilvános kulcsokra épülő infrastruktúra (PKI) használatával titkosítva van.

* A rendszer a felügyelt tartományra alkalmazza a **titkos** kulcsot.
    * Ez a titkos kulcs a biztonságos LDAP-forgalom *visszafejtésére* szolgál. A titkos kulcsot csak a felügyelt tartományra kell alkalmazni, és nem kell széles körben terjeszteni az ügyfélszámítógépekre.
    * A titkos kulcsot tartalmazó tanúsítvány a-t használja *. PFX* -fájlformátum.
    * A tanúsítvány titkosítási algoritmusának *TripleDES-SHA1* értékűnek kell lennie.
* A rendszer egy **nyilvános** kulcsot alkalmaz az ügyfélszámítógépekre.
    * Ez a nyilvános kulcs a biztonságos LDAP-forgalom *titkosítására* szolgál. A nyilvános kulcs terjeszthető az ügyfélszámítógépekre.
    * A titkos kulcs nélküli tanúsítványok a-t használják *. CER* -fájlformátum.

Ez a két kulcs, a *privát* és a *nyilvános* kulcs, győződjön meg arról, hogy csak a megfelelő számítógépek tudnak kommunikálni egymással. Ha nyilvános HITELESÍTÉSSZOLGÁLTATÓT vagy vállalati HITELESÍTÉSSZOLGÁLTATÓT használ, akkor a titkos kulcsot tartalmazó tanúsítvánnyal rendelkezik, amely felügyelt tartományra is alkalmazható. A nyilvános kulcsot már ismerni és megbízhatónak kell tekinteni az ügyfélszámítógépek számára.

Ebben az oktatóanyagban létrehozott egy önaláírt tanúsítványt a titkos kulccsal, ezért exportálnia kell a megfelelő magán-és nyilvános összetevőket.

### <a name="export-a-certificate-for-azure-ad-ds"></a>Tanúsítvány exportálása az Azure AD DS

Ahhoz, hogy az előző lépésben létrehozott digitális tanúsítványt a felügyelt tartományhoz használhassa, exportálja a tanúsítványt a-ba *.* A titkos kulcsot tartalmazó pfx-tanúsítványfájl.

1. A *Futtatás* párbeszédpanel megnyitásához válassza a **Windows**  +  **R** -kulcsok elemet.
1. Nyissa meg a Microsoft Management Console (MMC) beépülő **modult** a *Futtatás* párbeszédpanelen, majd kattintson **az OK gombra**.
1. A **felhasználói fiókok felügyelete** üzenetben válassza az **Igen** lehetőséget az MMC rendszergazdaként való indításához.
1. A **fájl** menüben válassza a **beépülő modul hozzáadása/eltávolítása...** lehetőséget.
1. A **Tanúsítványkezelő beépülő modul** varázslóban válassza a **számítógépfiók** lehetőséget, majd kattintson a **Tovább gombra**.
1. A **számítógép kiválasztása** lapon válassza a **helyi számítógép: (az a számítógép, amelyen ez a konzol fut)**, majd kattintson a **Befejezés gombra**.
1. A **beépülő modul hozzáadása/eltávolítása** párbeszédpanelen kattintson az **OK** gombra a Tanúsítványok beépülő modul MMC-hez való hozzáadásához.
1. Az MMC ablakban bontsa ki a **konzol gyökerét**. Válassza ki a **tanúsítványok (helyi számítógép)** elemet, majd bontsa ki a **személyes** csomópontot, majd a **tanúsítványok** csomópontot.

    ![Nyissa meg a személyes tanúsítványok tárolót a Microsoft Management Console-ban](./media/tutorial-configure-ldaps/open-personal-store.png)

1. Megjelenik az előző lépésben létrehozott önaláírt tanúsítvány, például *aaddscontoso.com*. Kattintson a jobb gombbal a tanúsítványra, majd válassza az **összes feladat > exportálás..** . lehetőséget.

    ![Tanúsítvány exportálása a Microsoft Management Console-ban](./media/tutorial-configure-ldaps/export-cert.png)

1. A **Tanúsítvány exportálása varázslóban** válassza a **tovább** lehetőséget.
1. A tanúsítvány titkos kulcsát exportálni kell. Ha a titkos kulcs nem szerepel az exportált tanúsítványban, akkor a felügyelt tartomány biztonságos LDAP-engedélyezésének művelete meghiúsul.

    A **titkos kulcs exportálása** lapon válassza **az Igen lehetőséget, exportálja a titkos kulcsot**, majd kattintson a **tovább** gombra.
1. A felügyelt tartományok csak a t támogatják *.* A titkos kulcsot tartalmazó pfx-tanúsítvány fájlformátuma. Ne exportálja a tanúsítványt *. CER* -tanúsítvány fájlformátuma a titkos kulcs nélkül.

    Az **Exportálás fájlformátuma** lapon válassza a **személyes információcsere – PKCS #12 (. PFX)** az exportált tanúsítvány fájlformátuma. Jelölje be az *összes tanúsítvány belefoglalása a minősítési útvonalon* jelölőnégyzetet, ha lehetséges:

    ![Válassza ki a tanúsítvány exportálásának lehetőségét a PKCS 12 (. PFX) fájlformátum](./media/tutorial-configure-ldaps/export-cert-to-pfx.png)

1. Mivel ez a tanúsítvány az adatvisszafejtéshez használatos, alaposan meg kell határoznia a hozzáférést. A tanúsítvány használatához jelszó használható. A megfelelő jelszó nélkül nem alkalmazható a tanúsítvány a szolgáltatásra.

    A **Biztonság** lapon válassza a **jelszó megadását** a védelméhez *. PFX* -tanúsítványfájl. A titkosítási algoritmusnak *TripleDES-SHA1* értékűnek kell lennie. Adja meg és erősítse meg a jelszót, majd kattintson a **tovább** gombra. Ezt a jelszót a következő szakaszban lehet használni a felügyelt tartomány biztonságos LDAP-szolgáltatásának engedélyezéséhez.
1. Az **exportálandó fájl** lapon adja meg a fájl nevét és helyét, ahová exportálni szeretné a tanúsítványt, például *C:\Users\accountname\azure-AD-DS.pfx*. Jegyezze fel a jelszavát és helyét *.* A következő lépésekben a pfx-fájlnak ezt az információt kell megadnia.
1. Az Áttekintés lapon válassza a **Befejezés** lehetőséget a tanúsítvány exportálásához *. PFX* -tanúsítványfájl. A tanúsítvány sikeres exportálását megerősítő párbeszédpanel jelenik meg.
1. Hagyja nyitva az MMC-t a következő szakaszban való használatra.

### <a name="export-a-certificate-for-client-computers"></a>Tanúsítvány exportálása ügyfélszámítógépek számára

Az ügyfélszámítógépeknek megbízhatóan kell megbízniuk a biztonságos LDAP-tanúsítvány kiállítójának, hogy LDAPs használatával lehessen csatlakozni a felügyelt tartományhoz. Az ügyfélszámítógépeknek tanúsítványra van szükségük ahhoz, hogy sikeresen titkosítsák az Azure AD DS által visszafejtett adataikat. Ha nyilvános HITELESÍTÉSSZOLGÁLTATÓT használ, a számítógépnek automatikusan meg kell bíznia ezeket a tanúsítvány-kiállítók számára, és rendelkeznie kell egy megfelelő tanúsítvánnyal.

Ebben az oktatóanyagban önaláírt tanúsítványt használ, és létrehozott egy tanúsítványt, amely tartalmazza a titkos kulcsot az előző lépésben. Most exportálja, majd telepítse az önaláírt tanúsítványt a megbízható tanúsítványtárolóba az ügyfélszámítógépen:

1. Lépjen vissza az MMC a *tanúsítványok (helyi számítógép) > személyes > tanúsítványok* tárolóba. Megjelenik az előző lépésben létrehozott önaláírt tanúsítvány, például *aaddscontoso.com*. Kattintson a jobb gombbal a tanúsítványra, majd válassza az **összes feladat > exportálás..** . lehetőséget.
1. A **Tanúsítvány exportálása varázslóban** válassza a **tovább** lehetőséget.
1. Mivel nem szükséges az ügyfelek titkos kulcsa, a **titkos kulcs exportálása** oldalon válassza a nem lehetőséget **, ne exportálja a titkos kulcsot**, majd kattintson a **tovább** gombra.
1. Az **Exportálás fájlformátuma** lapon válassza a **Base-64 kódolású X. 509 (. CER)** az exportált tanúsítvány fájlformátuma:

    ![Válassza a tanúsítvány exportálásának lehetőségét a Base-64 kódolású X. 509 (. CER) fájlformátum](./media/tutorial-configure-ldaps/export-cert-to-cer-file.png)

1. Az **exportálandó fájl** lapon adja meg a fájl nevét és helyét, ahová exportálni szeretné a tanúsítványt, például *C:\Users\accountname\azure-AD-DS-Client.cer*.
1. Az Áttekintés lapon válassza a **Befejezés** lehetőséget a tanúsítvány exportálásához *. CER* -tanúsítványfájl. A tanúsítvány sikeres exportálását megerősítő párbeszédpanel jelenik meg.

A *. A CER* -tanúsítványfájl mostantól terjeszthető olyan ügyfélszámítógépekre, amelyeknek meg kell bízniuk a felügyelt tartományhoz való biztonságos LDAP-kapcsolattal. Telepítse a tanúsítványt a helyi számítógépre.

1. Nyissa meg a fájlkezelőt, és keresse meg azt a helyet, ahová a fájlt mentette *. CER* -tanúsítványfájl, például *C:\Users\accountname\azure-AD-DS-Client.cer*.
1. Kattintson a jobb gombbal a elemre *. CER* -tanúsítványfájl, majd válassza a **tanúsítvány telepítése** lehetőséget.
1. A **tanúsítvány importálása varázslóban** válassza a tanúsítvány tárolása a *helyi gépen* lehetőséget, majd válassza a Next ( **tovább**) lehetőséget:

    ![Válassza ki a tanúsítványt a helyi számítógép tárolójába való importálásának lehetőségét.](./media/tutorial-configure-ldaps/import-cer-file.png)

1. Ha a rendszer kéri, válassza az **Igen** lehetőséget a számítógép módosításának engedélyezéséhez.
1. Válassza ki a tanúsítványtároló **automatikus kiválasztását a tanúsítvány típusa alapján**, majd kattintson a **tovább** gombra.
1. Az Áttekintés lapon válassza a **Befejezés** lehetőséget az importálásához *. CER* -tanúsítvány. a tanúsítvány sikeres importálása után A rendszer megerősítő párbeszédpanelt jelenít meg.

## <a name="enable-secure-ldap-for-azure-ad-ds"></a>Biztonságos LDAP engedélyezése az Azure-ban AD DS

A titkos kulcsot tartalmazó és exportált digitális tanúsítvánnyal, valamint a kapcsolat megbízhatóságához beállított ügyfélszámítógépen engedélyezze a biztonságos LDAP szolgáltatást a felügyelt tartományon. A biztonságos LDAP felügyelt tartományon való engedélyezéséhez hajtsa végre a következő konfigurációs lépéseket:

1. A [Azure Portal](https://portal.azure.com)írja be a *tartományi szolgáltatások* kifejezést az **erőforrások keresése** mezőbe. A keresési eredmények közül válassza a **Azure ad Domain Services** lehetőséget.
1. Válassza ki a felügyelt tartományt, például *aaddscontoso.com*.
1. Az Azure AD DS ablak bal oldalán válassza a **Secure LDAP** lehetőséget.
1. Alapértelmezés szerint a felügyelt tartományhoz való biztonságos LDAP-hozzáférés le van tiltva. **Secure LDAP** váltása az **engedélyezéshez**.
1. A felügyelt tartományhoz való Secure LDAP az interneten keresztüli hozzáférés alapértelmezés szerint le van tiltva. Ha engedélyezi a nyilvános biztonságos LDAP-hozzáférést, a tartománya feltehetően az interneten keresztül fellépő jelszó-kényszerített támadásokra. A következő lépésben a hálózati biztonsági csoport úgy van konfigurálva, hogy csak a szükséges forrás IP-címtartományok elérését zárolja.

    Az **engedélyezéshez** kapcsolja be **a biztonságos LDAP-hozzáférés engedélyezése az interneten keresztül lehetőséget** .

1. Válassza ki a mappa ikont a mellett **. PFX-fájl biztonságos LDAP-tanúsítvánnyal**. Tallózással keresse meg a elérési útját *. PFX* -fájl, majd válassza ki a titkos kulcsot tartalmazó előző lépésben létrehozott tanúsítványt.

    > [!IMPORTANT]
    > Ahogy azt a tanúsítványra vonatkozó követelmények előző szakasza is jelezte, nem használhat tanúsítványokat nyilvános HITELESÍTÉSSZOLGÁLTATÓTÓL az alapértelmezett *. onmicrosoft.com* tartománnyal. A Microsoft tulajdonosa a *. onmicrosoft.com* tartomány, így a nyilvános hitelesítésszolgáltatók nem bocsátanak ki tanúsítványt.
    >
    > Győződjön meg arról, hogy a tanúsítvány megfelelő formátumú. Ha nem, az Azure platform tanúsítvány-ellenőrzési hibákat hoz létre a biztonságos LDAP engedélyezésekor.

1. Adja meg a **visszafejteni kívánt jelszót.** Az előző lépésben beállított pfx-fájl, ha a tanúsítványt exportálták *. PFX* -fájl.
1. A biztonságos LDAP engedélyezéséhez válassza a **Mentés** lehetőséget.

    ![Biztonságos LDAP engedélyezése felügyelt tartományhoz a Azure Portal](./media/tutorial-configure-ldaps/enable-ldaps.png)

Megjelenik egy értesítés arról, hogy a biztonságos LDAP konfigurálva van a felügyelt tartományhoz. A felügyelt tartomány többi beállítása addig nem módosítható, amíg a művelet be nem fejeződik.

A felügyelt tartomány biztonságos LDAP-szolgáltatásának engedélyezése néhány percet vesz igénybe. Ha az Ön által megadott biztonságos LDAP-tanúsítvány nem felel meg a szükséges feltételeknek, a felügyelt tartomány biztonságos LDAP-hitelesítésének művelete meghiúsul.

A hiba gyakori okai a következők: Ha a tartománynév helytelen, a tanúsítvány titkosítási algoritmusa nem *TripleDES-SHA1*, vagy a tanúsítvány hamarosan lejár, vagy már lejárt. Újra létrehozhatja a tanúsítványt érvényes paraméterekkel, majd engedélyezheti a biztonságos LDAP használatát a frissített tanúsítvánnyal.

## <a name="change-an-expiring-certificate"></a>Lejáró tanúsítvány módosítása

1. Hozzon létre egy helyettesítő Secure LDAP-tanúsítványt a [biztonságos LDAP-tanúsítvány létrehozásához](#create-a-certificate-for-secure-ldap)szükséges lépések követésével.
1. Ha a helyettesítő tanúsítványt az Azure AD DSra szeretné alkalmazni, az Azure AD DS bal oldali menüjében válassza Azure Portal a **Secure LDAP** lehetőséget, majd válassza a **tanúsítvány módosítása** lehetőséget.
1. Terjessze a tanúsítványt minden olyan ügyfélre, amely biztonságos LDAP használatával csatlakozik. 

## <a name="lock-down-secure-ldap-access-over-the-internet"></a>Biztonságos LDAP-hozzáférés zárolása az interneten keresztül

Ha az interneten keresztül engedélyezi a biztonságos LDAP-hozzáférést a felügyelt tartományhoz, biztonsági fenyegetést hoz létre. A felügyelt tartomány elérhető az internetről a 636-as TCP-porton. Javasoljuk, hogy a felügyelt tartományhoz való hozzáférést a környezet adott ismert IP-címeire korlátozza. Az Azure-beli hálózati biztonsági csoport szabálya használható a biztonságos LDAP-hozzáférés korlátozására.

Hozzon létre egy szabályt, amely engedélyezi a bejövő biztonságos LDAP-hozzáférést a 636-as TCP-porton az IP-címek megadott készletéről. Az alacsonyabb prioritású alapértelmezett *DenyAll* -szabály az internetről érkező összes többi bejövő forgalomra vonatkozik, így csak a megadott címek érhetik el a felügyelt TARTOMÁNYT biztonságos LDAP használatával.

1. A Azure Portal válassza az *erőforráscsoportok* lehetőséget a bal oldali navigációs sávon.
1. Válassza ki az erőforráscsoportot, például *myResourceGroup*, majd válassza ki a hálózati biztonsági csoportot, például a *aaads-NSG*.
1. Megjelenik a meglévő bejövő és kimenő biztonsági szabályok listája. A hálózati biztonsági csoport ablak bal oldalán válassza a **beállítások > a bejövő biztonsági szabályok** lehetőséget.
1. Válassza a **Hozzáadás** lehetőséget, majd hozzon létre egy szabályt a *636*-es *TCP* -port engedélyezéséhez. A fokozott biztonság érdekében válassza ki a forrást *IP-címként* , majd adja meg a saját érvényes IP-címét vagy tartományát a szervezet számára.

    | Beállítás                           | Érték        |
    |-----------------------------------|--------------|
    | Forrás                            | IP-címek |
    | Forrás IP-címeinek/CIDR tartományai | Érvényes IP-cím vagy tartomány a környezet számára |
    | Forrásporttartományok                | *            |
    | Cél                       | Bármely          |
    | Célporttartományok           | 636          |
    | Protokoll                          | TCP          |
    | Művelet                            | Engedélyezés        |
    | Prioritás                          | 401          |
    | Name                              | AllowLDAPS   |

1. Ha elkészült, kattintson a **Hozzáadás** gombra a szabály mentéséhez és alkalmazásához.

    ![Hálózati biztonsági csoport szabályának létrehozása az LDAP-hozzáférés biztonságossá tételéhez az interneten keresztül](./media/tutorial-configure-ldaps/create-inbound-nsg-rule-for-ldaps.png)

## <a name="configure-dns-zone-for-external-access"></a>DNS-zóna konfigurálása külső hozzáféréshez

Az interneten keresztüli biztonságos LDAP-hozzáférés használatával frissítse a DNS-zónát, hogy az ügyfélszámítógépek megtalálják ezt a felügyelt tartományt. A *Secure LDAP külső IP-cím* a felügyelt tartomány **Tulajdonságok** lapján jelenik meg:

![A felügyelt tartomány Secure LDAP külső IP-címének megtekintése a Azure Portal](./media/tutorial-configure-ldaps/ldaps-external-ip-address.png)

Konfigurálja a külső DNS-szolgáltatót egy olyan gazda rekord létrehozásához, mint például az *LDAPS*, hogy feloldja ezt a külső IP-címet. Ha először szeretné tesztelni a gépet a gépen, létrehozhat egy bejegyzést a Windows-gazdagépek fájljában. A gazdagépek fájljának a helyi gépen való sikeres szerkesztéséhez nyissa meg rendszergazdaként a *jegyzettömböt* , majd nyissa meg a *C:\Windows\System32\drivers\etc\hosts* fájlt.

A következő példában szereplő DNS-bejegyzés a külső DNS-szolgáltatóval vagy a helyi gazdagépek fájljával oldja fel a *LDAPS.aaddscontoso.com* a *168.62.205.103* külső IP-címére irányuló forgalmat:

```
168.62.205.103    ldaps.aaddscontoso.com
```

## <a name="test-queries-to-the-managed-domain"></a>Lekérdezések tesztelése a felügyelt tartományba

A felügyelt tartományhoz való kapcsolódáshoz és az LDAP-alapú kereséshez használja az *LDP.exe* eszközt. Ez az eszköz a Távoli kiszolgálófelügyelet eszközei (RSAT) csomagban található. További információ: [install Távoli kiszolgálófelügyelet eszközei][rsat].

1. Nyissa meg *LDP.exe* , és kapcsolódjon a felügyelt tartományhoz. Válassza a **kapcsolat**, majd a **Csatlakoztatás...** lehetőséget.
1. Adja meg az előző lépésben létrehozott felügyelt tartomány Secure LDAP DNS-tartománynevét (például *LDAPS.aaddscontoso.com*). A Secure LDAP használatához állítsa a **portot** *636*-re, majd jelölje be az **SSL** jelölőnégyzetet.
1. A felügyelt tartományhoz való kapcsolódáshoz kattintson **az OK gombra** .

Ezután kötést a felügyelt tartományhoz. A felhasználók (és a szolgáltatásfiókok) nem hajthatnak végre LDAP egyszerű kötéseket, ha letiltotta az NTLM-jelszó kivonatának szinkronizálását a felügyelt tartományon. Az NTLM jelszó-kivonatok szinkronizálásának letiltásával kapcsolatos további információkért lásd: [a felügyelt tartomány biztonságossá tétele][secure-domain].

1. Válassza a **kapcsolatok** menüpontot, majd válassza a **kötés...** lehetőséget.
1. Adja meg a felügyelt tartományhoz tartozó felhasználói fiók hitelesítő adatait. Adja meg a felhasználói fiók jelszavát, majd adja meg a tartományt (például *aaddscontoso.com*).
1. A **kötés típusa** beállításnál válassza a *kötés a hitelesítő adatokkal* lehetőséget.
1. Kattintson **az OK gombra** a felügyelt tartományhoz való kötéshez.

A felügyelt tartományban tárolt objektumok megtekintéséhez:

1. Válassza a **nézet** menü lehetőséget, majd a **fa** elemet.
1. Hagyja üresen a *BaseDN* mezőt, majd kattintson **az OK gombra**.
1. Válasszon egy tárolót, például *AADDC-felhasználók* elemet, majd kattintson a jobb gombbal a tárolóra, és válassza a **Keresés** lehetőséget.
1. Hagyja meg a beállított előre megadott mezőket, majd válassza a **Futtatás** lehetőséget. A lekérdezés eredményei megjelennek a jobb oldali ablakban, ahogy az a következő példában látható:

    ![Objektumok keresése a felügyelt tartományban LDP.exe használatával](./media/tutorial-configure-ldaps/ldp-query.png)

Egy adott tároló közvetlen lekérdezéséhez a **nézet > fa** menüjében megadhat egy **BaseDN** , például *ou = AADDC-felhasználók, DC = AADDSCONTOSO, DC = com* vagy *ou = AADDC számítógépek, DC = AADDSCONTOSO, DC = com*. További információ a lekérdezések formázásáról és létrehozásáról: az [LDAP-lekérdezés alapjai][ldap-query-basics].

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a számítógép helyi gazdagépek fájljához hozzáadott egy DNS-bejegyzést az oktatóanyag kapcsolatának teszteléséhez, távolítsa el ezt a bejegyzést, és adjon hozzá egy formális rekordot a DNS-zónához. Ha el szeretné távolítani a bejegyzést a helyi gazdagépek fájljából, hajtsa végre a következő lépéseket:

1. A helyi gépen nyissa meg a *jegyzettömböt* rendszergazdaként
1. Tallózással keresse meg és nyissa meg a *C:\Windows\System32\drivers\etc\hosts* fájlt.
1. Törölje a hozzáadott rekordhoz tartozó sort, például: `168.62.205.103    ldaps.aaddscontoso.com`

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Digitális tanúsítvány létrehozása az Azure AD DS használatával
> * Biztonságos LDAP engedélyezése az Azure-ban AD DS
> * Biztonságos LDAP konfigurálása a nyilvános interneten való használatra
> * Biztonságos LDAP-kötés és-tesztelés felügyelt tartományhoz

> [!div class="nextstepaction"]
> [Jelszó-kivonat szinkronizálásának konfigurálása hibrid Azure AD-környezethez](tutorial-configure-password-hash-sync.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[secure-domain]: secure-your-domain.md

<!-- EXTERNAL LINKS -->
[rsat]: /windows-server/remote/remote-server-administration-tools
[ldap-query-basics]: /windows/desktop/ad/creating-a-query-filter
[New-SelfSignedCertificate]: /powershell/module/pkiclient/new-selfsignedcertificate
