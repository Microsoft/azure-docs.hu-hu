---
title: Arculat hozzáadása a szervezet bejelentkezési oldalához – Azure AD
description: Útmutatás a szervezet arculatának a Azure Active Directory bejelentkezési oldalra való felvételéhez.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 06/24/2020
ms.author: ajburnle
ms.reviewer: kexia
ms.custom: it-pro, seodec18, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b69daf9e9fbbb3f6d6597a8e8349dc8bec61fbf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103008774"
---
# <a name="add-branding-to-your-organizations-azure-active-directory-sign-in-page"></a>Arculat hozzáadása a szervezet Azure Active Directory bejelentkezési lapjához
A szervezet emblémájának és egyéni színsémáinak használatával egységes megjelenést és működést biztosíthat a Azure Active Directory (Azure AD) bejelentkezési oldalain. A bejelentkezési oldalai akkor jelennek meg, amikor a felhasználók bejelentkeznek a szervezet webalapú alkalmazásaiba, például a Microsoft 365ba, amely az Azure AD-t használja az identitás-szolgáltatóként.

>[!NOTE]
>Az egyéni védjegyezés hozzáadásához prémium szintű Azure Active Directory 1 vagy Premium 2 licenc szükséges. További információ a licencelésről és a kiadásokról: [regisztráció a prémium szintű Azure ADra](active-directory-get-started-premium.md).<br><br>A prémium szintű Azure AD kiadásai a Azure Active Directory világméretű példányát használó kínai vásárlók számára érhetők el. A 21Vianet által üzemeltetett Azure-szolgáltatás jelenleg nem támogatja a prémium szintű Azure AD kiadásait Kínában. További információért lépjen velünk kapcsolatba az [Azure Active Directory fórumon](https://feedback.azure.com/forums/169401-azure-active-directory/) keresztül.

## <a name="customize-your-azure-ad-sign-in-page"></a>Az Azure AD bejelentkezési oldalának testreszabása
Testreszabhatja az Azure AD bejelentkezési lapjait, amelyek akkor jelennek meg, amikor a felhasználók bejelentkeznek a szervezet bérlői-specifikus alkalmazásaiba, például `https://outlook.com/contoso.com` vagy egy tartományi változó átadásakor, például: `https://passwordreset.microsoftonline.com/?whr=contoso.com` .

Az egyéni arculat nem jelenik meg azonnal, amikor a felhasználók olyan webhelyekre látogatnak, mint a www- \. Office.com. Ehelyett a felhasználónak be kell jelentkeznie a testreszabott arculat megjelenése előtt. Miután a felhasználó bejelentkezett, a márkaépítés 15 percet vagy hosszabb időt is igénybe vehet. 

> [!NOTE]
> Az összes védjegyezési elem nem kötelező. Ha például egy háttérkép nélküli szalagcím-emblémát ad meg, a bejelentkezési oldal a célhelyen lévő alapértelmezett háttérképként jeleníti meg a logót (például Microsoft 365).<br><br>Emellett a bejelentkezési oldal arculata nem veszi át a személyes Microsoft-fiókokat. Ha a felhasználók vagy az üzleti vendégek személyes Microsoft-fiók bejelentkeznek, a bejelentkezési oldal nem tükrözi a szervezet arculatát.

### <a name="to-customize-your-branding"></a>A branding testreszabása
1. A címtár eléréséhez globális rendszergazdai fiókkal jelentkezzen be az [Azure portálra](https://portal.azure.com/).

2. Válassza a **Azure Active Directory** lehetőséget, majd válassza a **vállalati védjegyezés** lehetőséget, majd válassza a **Konfigurálás** lehetőséget.

    ![Contoso – vállalati védjegyezés lap, kijelölt beállítás](media/customize-branding/company-branding-configure-button.png)

3. A **vállalati védjegyezés konfigurálása** lapon adja meg a következő információk bármelyikét vagy mindegyikét.

    >[!IMPORTANT]
    >Az ezen a lapon hozzáadott összes egyéni rendszerkép rendelkezik képmérettel (képpont) és potenciálisan fájlmérettel (KB), korlátozásokkal. Ezeknek a korlátozásoknak a miatt a legvalószínűbb, hogy a megfelelő méretű rendszerképek létrehozásához egy Fényképszerkesztőt kell használnia.

    - **Általános beállítások**

        ![A vállalati védjegyezés beállítása lap, az általános beállításokkal befejeződött](media/customize-branding/configure-company-branding-general-settings.png)

        - **Nyelv.** A nyelv automatikusan alapértelmezettként van beállítva, és nem módosítható.
        
        - **A bejelentkezési oldal háttérképe.** Válasszon ki egy. png vagy. jpg képfájlt, amelyet a bejelentkezési oldalain háttérként szeretne megjeleníteni. A rendszerkép a böngésző közepére lesz rögzítve, és a megtekinthető terület méretét fogja méretezni. Nem választhat olyan képet, amely nagyobb, mint 1920 × 1080 képpont, vagy a fájl mérete meghaladja a 300 KB-ot.
        
            Azt javasoljuk, hogy a képeket erős tárgyú fókusz nélkül használja, például egy átlátszatlan fehér mező jelenik meg a képernyő közepén, és a megtekinthető terület méreteitől függően a kép bármely részét lefedi.

        - **Szalagcím emblémája** Válassza ki az embléma egy. png vagy. jpg verzióját, amely a bejelentkezési oldalon jelenik meg, miután a felhasználó bejelentkezett a Felhasználónév és a **saját alkalmazások** portál oldalon.
            
            A rendszerkép nem lehet nagyobb, mint 60 képpont vagy nagyobb, mint 280 képpont, és a fájl nem lehet nagyobb, mint 10KB. Javasoljuk, hogy transzparens képet használjon, mivel előfordulhat, hogy a háttér nem felel meg az embléma hátterének. Azt javasoljuk továbbá, hogy ne adja meg a kitöltés körét a képen, vagy hogy az embléma kicsi legyen. 

        - **Felhasználónév-emlékeztető.** Írja be a felhasználók számára megjelenő tipp szövegét, ha elfelejtik a felhasználónevét. A szövegnek Unicode formátumúnak kell lennie, hivatkozás vagy kód nélkül, és nem lehet hosszabb 64 karakternél. Ha a vendégek bejelentkeznek az alkalmazásba, javasoljuk, hogy ne adja hozzá ezt a tippet.

        - **A bejelentkezési oldal szövege és formázása.** Írja be a bejelentkezési oldal alján megjelenő szöveget. Ezt a szöveget használhatja további információk, például telefonszámok vagy jogi nyilatkozatok közlésére. A szövegnek Unicode-nak kell lennie, és nem lehet hosszabb 1024 karakternél.

           Testreszabhatja a beírt bejelentkezési oldal szövegét. Új bekezdés megkezdéséhez használja kétszer az ENTER billentyűt. A szövegformázást úgy is megváltoztathatja, hogy félkövér, dőlt, aláhúzás vagy kattintható hivatkozást tartalmazzon. A szöveg formázásának hozzáadásához használja a következő szintaxist: 

          > Hivatkozás ```[text](link)``` 
          
          > Félkövér: ``` **text** ``` vagy ``` __text__ ``` 
          
          > Dőlt betű: ``` *text* ``` vagy ``` _text_ ``` 
          
          > Aláhúzása ``` ++text++ ``` 

    - **Speciális beállítások**
            
        ![Vállalati védjegyezés konfigurálása oldal, speciális beállításokkal befejezve](media/customize-branding/configure-company-branding-advanced-settings.png)   

        - **A bejelentkezési oldal háttérszíne** Adja meg a hexadecimális színeket (például a fehér #FFFFFF), amely a háttérkép helyett az alacsony sávszélességű kapcsolat helyzetekben fog megjelenni. Javasoljuk, hogy a szalagcím emblémájának vagy a szervezet színének elsődleges színét használja.

        - **Szögletes embléma képe** Válassza ki a szervezete emblémájának. png (preferált) vagy. jpg képét, amely a felhasználók számára az új Windows 10 Enterprise-eszközök telepítési folyamata során jelenik meg. Ez a rendszerkép csak Windows-hitelesítéshez használatos, és csak olyan bérlők esetében jelenik meg, amelyek a [Windows]( /windows/deployment/windows-autopilot/windows-10-autopilot) Autopilot szolgáltatást használják a központi telepítéshez, vagy a jelszó-beviteli lapokat más Windows 10-es környezetekben. Bizonyos esetekben előfordulhat, hogy a beleegyező párbeszédpanelen is megjelenik.
        
            A rendszerkép mérete nem lehet nagyobb, mint 240x240 képpont, és a fájlméretnek 10 KB-nál kisebbnek kell lennie. Javasoljuk, hogy transzparens képet használjon, mivel előfordulhat, hogy a háttér nem felel meg az embléma hátterének. Azt javasoljuk továbbá, hogy ne adja meg a kitöltés körét a képen, vagy hogy az embléma kicsi legyen.
    
        - **Szögletes embléma képe, sötét téma.** Ugyanaz, mint a fenti négyzetes embléma képe. Az embléma képe a négyzetes embléma képére helyezi a sötét háttérrel való használatkor, például a Windows 10 Azure AD-hoz csatlakoztatott képernyőkkel a beépített felület (OOBE) alatt.  Ha az embléma fehér, sötét kék és fekete hátterű, akkor nem kell hozzáadnia ezt a képet. 
        
        - **A bejelentkezve maradó beállítás megjelenítése.** Dönthet úgy, hogy a felhasználók továbbra is bejelentkeznek az Azure AD-be, amíg explicit módon ki nem jelentkezik. Ha a **nem** lehetőséget választja, akkor ez a beállítás rejtett, és a felhasználóknak minden alkalommal be kell jelentkezniük, amikor a böngésző be van zárva és újra meg lett nyitva.

            Ez a funkció csak az alapértelmezett branding objektumon érhető el, és nem az adott nyelvspecifikus objektumon. Ha többet szeretne megtudni arról, hogyan konfigurálhatja és elháríthatja a bejelentkezést, tekintse meg [Az Azure ad-fiókokra vonatkozó "Stay bejelentkezve?"](keep-me-signed-in.md) című témakört.
        
            >[!NOTE]
            >A SharePoint Online és az Office 2010 egyes funkciói attól függenek, hogy a felhasználók bejelentkezve tudnak-e maradni. Ha ennél a lehetőségnél a **Nem** értéket adja meg, a felhasználóinak további váratlan bejelentkezési felszólítások jelenhetnek meg.
   

3. Miután végzett a branding hozzáadásával, válassza a **Mentés** lehetőséget.

    Ha ez a folyamat létrehozza az első egyéni védjegyezési konfigurációt, az alapértelmezett lesz a bérlő számára. Ha további beállításokkal rendelkezik, kiválaszthatja az alapértelmezett konfigurációt.
    
    >[!IMPORTANT]
    >Ha további vállalati arculati konfigurációkat szeretne hozzáadni a bérlőhöz, a **contoso-Company branding** oldalon **új nyelvet** kell választania. Ekkor megnyílik a **vállalati védjegyezés konfigurálása** oldal, ahol követheti a fenti lépéseket.

## <a name="update-your-custom-branding"></a>Egyéni arculat frissítése
Az egyéni védjegyezés létrehozása után visszaléphet, és módosíthatja a kívánt bármit.

### <a name="to-edit-your-custom-branding"></a>Egyéni védjegyezés szerkesztése
1. A címtár eléréséhez globális rendszergazdai fiókkal jelentkezzen be az [Azure portálra](https://portal.azure.com/).

2. Válassza a **Azure Active Directory** lehetőséget, majd válassza a **vállalati védjegyezés** lehetőséget, majd válassza a **Konfigurálás** lehetőséget.

    ![Contoso – vállalati védjegyezési oldal, alapértelmezett konfigurációval megjelenítve](media/customize-branding/company-branding-default-config.png)

3. A **vállalati védjegyezés konfigurálása** lapon a jelen cikk az [Azure ad bejelentkezési oldalának testreszabása](#customize-your-azure-ad-sign-in-page) lapján található leírások alapján adja hozzá, távolítsa el vagy módosítsa a szükséges információkat.

4. Kattintson a **Mentés** gombra.

   A bejelentkezési oldal arculatán alkalmazott módosítások megjelenítése akár egy órát is igénybe vehet.

## <a name="add-language-specific-company-branding-to-your-directory"></a>Nyelvspecifikus vállalati arculat hozzáadása a címtárhoz
Az eredeti konfiguráció nyelvét nem módosíthatja az alapértelmezett nyelvről. Ha azonban más nyelven kell konfigurálnia a konfigurációt, létrehozhat egy új konfigurációt.

### <a name="to-add-a-language-specific-branding-configuration"></a>Nyelvspecifikus arculati konfiguráció hozzáadása

1. A címtár eléréséhez globális rendszergazdai fiókkal jelentkezzen be az [Azure portálra](https://portal.azure.com/).

2. Válassza a **Azure Active Directory** lehetőséget, majd válassza a **vállalati védjegyezés** lehetőséget, majd válassza az **új nyelv** lehetőséget.

    ![Contoso – vállalati védjegyezési oldal, új nyelvi beállítás kiemelve](media/customize-branding/company-branding-new-language.png)

3. A **vállalati védjegyezés konfigurálása** lapon válassza ki a nyelvet (például francia), majd adja hozzá a lefordított adatokat a jelen cikk [Azure ad bejelentkezési oldalának testreszabása lapján](#customize-your-azure-ad-sign-in-page) található leírások alapján.

4. Kattintson a **Mentés** gombra.

    A **contoso – vállalati védjegyezési** oldal frissítései az új francia konfiguráció megjelenítéséhez.

    ![Contoso – vállalati védjegyezési oldal, az új nyelvi konfiguráció látható](media/customize-branding/company-branding-french-config.png)

## <a name="add-your-custom-branding-to-pages"></a>Egyéni arculat hozzáadása lapokhoz
Adja hozzá az egyéni arculatot a lapokhoz az URL-cím végének szöveggel való módosításával `?whr=yourdomainname` . Ez a módosítás több oldalon is működik, beleértve a Multi-Factor Authentication (MFA) telepítési oldalát, az önkiszolgáló jelszó-visszaállítás (SSPR) telepítési lapját és a bejelentkezési lapot.

**Példák:**

**Eredeti URL-cím:**https://aka.ms/MFASetup<br>
**Egyéni URL-cím:**`https://account.activedirectory.windowsazure.com/proofup.aspx?whr=contoso.com`

**Eredeti URL-cím:**https://aka.ms/SSPR<br>
**Egyéni URL-cím:**`https://passwordreset.microsoftonline.com/?whr=contoso.com`
