---
title: Azure Active Directory tevékenységjelentések – előzetes verzió | Microsoft Docs
description: A bejelentkezési tevékenységre vonatkozó jelentések bemutatása a Azure Active Directory portálon
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/19/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: dc9aa77b3fdc3cda94670545f847bb9de31e1160
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718952"
---
# <a name="azure-active-directory-sign-in-activity-reports---preview"></a>Azure Active Directory tevékenységjelentések megtekintése – előzetes verzió

Rendszergazdaként ön szeretné tudni, hogyan működik az it-környezete. A rendszer állapotára vonatkozó információk segítségével felmérheti, hogy kell-e válaszolnia a lehetséges problémákra, és ha igen, hogyan. 

A cél eléréséhez a Azure Active Directory portál három tevékenységnaplóhoz biztosít hozzáférést:

- **Bejelentkezések** – Információk a bejelentkezésekkel és az erőforrások felhasználók által való felhasználását.
- **[Naplózás](concept-audit-logs.md)** – A bérlőre alkalmazott változásokkal kapcsolatos információk, például felhasználók és csoportkezelés, vagy a bérlői erőforrásokra alkalmazott frissítések.
- **[Kiépítés](concept-provisioning-logs.md)** – A kiépítési szolgáltatás által végrehajtott tevékenységek, például egy csoport létrehozása a ServiceNow-ban vagy egy Workdayből importált felhasználó.


A klasszikus bejelentkezési jelentés Azure Active Directory interaktív felhasználói bejelentkezések áttekintését biztosítja. Emellett három további, jelenleg előzetes verzióban elérhető bejelentkezési jelentéshez is hozzáférhet:

- Nem interaktív felhasználói bejelentkezések

- Szolgáltatásnév-bejelentkezések

- Az Azure-erőforrások bejelentkezésének felügyelt identitásai

Ez a cikk áttekintést nyújt a bejelentkezési tevékenységről szóló jelentésről, amely az Azure-erőforrások nem interaktív, alkalmazás- és felügyelt identitásai bejelentkezésének előzetesét biztosítja. További információ az előzetes verziójú funkciók nélküli bejelentkezési jelentésről: Bejelentkezési tevékenységre vonatkozó jelentések a [Azure Active Directory portálon.](concept-sign-ins.md)



## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdi használni ezt a funkciót, tudnia kell a választ a következőre:

- Ki férhet hozzá az adatokhoz?

- Milyen Azure AD-licencre van szükség a bejelentkezési tevékenységhez való hozzáféréshez?

### <a name="who-can-access-the-data"></a>Ki férhet hozzá az adatokhoz?

- A Biztonsági rendszergazda, a Biztonsági olvasó és a Jelentésolvasó szerepkörben

- Globális rendszergazdák

- Bármely (nem rendszergazda jogosultságú) felhasználó hozzáfér a saját bejelentkezéseihez 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Milyen Azure AD-licencre van szükség a bejelentkezési tevékenységhez való hozzáféréshez?

A bejelentkezési tevékenységek prémium szintű Azure AD bérlőjéhez társított licenccel kell rendelkezik. A [kiadás frissítését](../fundamentals/active-directory-get-started-premium.md) prémium szintű Azure Active Directory első Azure Active Directory lásd: Getting started with prémium szintű Azure Active Directory (Ismerkedés a Azure Active Directory kiadással. A prémium szintű licencre való frissítés után az adatok csak néhány napig fognak jelenni a jelentésekben adattevékenységek nélkül.



## <a name="sign-ins-report"></a>Bejelentkezési jelentés

A bejelentkezési jelentés a következő kérdésekre ad választ:

- Mi a felhasználó, alkalmazás vagy szolgáltatás bejelentkezési mintája?
- Hány felhasználó, alkalmazás vagy szolgáltatás jelentkezett be egy héten keresztül?
- Milyen állapotúak ezek a bejelentkezések?


A bejelentkezések jelentés panelen válthat a következők között:

- **Interaktív felhasználói bejelentkezések** – Bejelentkezések, amelyekben a felhasználó hitelesítési tényezőt ( például jelszót, MFA-alkalmazáson keresztüli választ, biometrikus tényezőt vagy QR-kódot) kínál.

- **Nem interaktív felhasználói bejelentkezések** – Az ügyfél által a felhasználó nevében végzett bejelentkezések. Ezek a bejelentkezések nem igényelnek felhasználói beavatkozást vagy hitelesítési tényezőt. Ilyen például az olyan frissítési és hozzáférési jogkivonatokkal történő hitelesítés és engedélyezés, amelyekhez a felhasználónak nem kell hitelesítő adatokat megadnia.

- **Szolgáltatásnév-bejelentkezések** – Bejelentkezések olyan alkalmazások és szolgáltatásbiztonsági tagokkal, amelyek nem foglalnak magukban semmilyen felhasználót. Ezekben a bejelentkezésekben az alkalmazás vagy szolgáltatás saját nevében biztosít hitelesítő adatokat az erőforrások hitelesítéséhez vagy eléréséhez.

- **Az Azure-erőforrások felügyelt** identitásai – Bejelentkezések az Azure által felügyelt titkos okkal felügyelt Azure-erőforrások alapján. További információ: [Mik az Azure-erőforrások felügyelt identitásai?](../managed-identities-azure-resources/overview.md) 


![A bejelentkezések jelentéstípusai](./media/concept-all-sign-ins/sign-ins-report-types.png)












## <a name="user-sign-ins&quot;></a>Felhasználói bejelentkezések

A Bejelentkezések panel minden lapján az alábbi alapértelmezett oszlopok láthatók. Egyes lapok további oszlopokat tartalmaznak:

- Bejelentkezés dátuma

- Kérés azonosítója

- Felhasználónév vagy felhasználói azonosító

- Alkalmazásnév vagy alkalmazásazonosító

- A bejelentkezés állapota

- A bejelentkezéshez használt eszköz IP-címe



### <a name=&quot;interactive-user-sign-ins&quot;></a>Interaktív felhasználói bejelentkezések


Az interaktív felhasználói bejelentkezések olyan bejelentkezések, amelyekben a felhasználó hitelesítési tényezőt biztosít az Azure AD-nek, vagy közvetlenül kommunikál az Azure AD-val vagy egy segítő alkalmazással, például a Microsoft Authenticator alkalmazással. A felhasználók által megadott tényezők közé tartoznak a jelszavak, az MFA-problémákra adott válaszok, a biometrikus tényezők vagy a felhasználó által az Azure AD-nek vagy egy segítő alkalmazásnak megadott QR-kódok.

> [!NOTE]
> Ez a jelentés az Azure AD-be összevont identitásszolgáltatók összevont bejelentkezését is tartalmazza.  



> [!NOTE] 
> Az interaktív felhasználói bejelentkezési jelentés a Microsoft Exchange-ügyfelek néhány nem interaktív bejelentkezését tartalmazta. Bár ezek a bejelentkezések nem interaktívak voltak, az interaktív felhasználói bejelentkezési jelentésben is szerepeltek a további láthatóság érdekében. Miután a nem interaktív felhasználói bejelentkezési jelentés 2020 novemberében nyilvános előzetes verzióba került, a nem interaktív bejelentkezési eseménynaplók átkerültek a nem interaktív felhasználói bejelentkezési jelentésbe a nagyobb pontosság érdekében. 


**Jelentésméret:** kicsi <br> 
**Példák:**

- A felhasználó felhasználónevet és jelszót ad meg az Azure AD bejelentkezési képernyőjén.

- A felhasználó egy SMS MFA-feladatot ad át.

- A felhasználó biometrikus kézmozdulattal oldhatja fel Windows rendszerű számítógépét a Vállalati Windows Hello.

- A felhasználó egy SAML-helyességi feltételt AD FS Azure AD-be.


Az alapértelmezett mezőkön kívül az interaktív bejelentkezési jelentés a következő adatokat is megjeleníti: 

- A bejelentkezési hely

- A feltételes hozzáférés alkalmazása



A listanézetet az eszköztár **Oszlopok** elemére kattintva lehet testre szabni.

![Interaktív felhasználói bejelentkezési oszlopok](./media/concept-all-sign-ins/columns-interactive.png &quot;Interaktív felhasználói bejelentkezési oszlopok")





A nézet testreszabásával további mezőket jelenít meg, vagy eltávolíthatja a már megjelenített mezőket.

![Minden interaktív oszlop](./media/concept-all-sign-ins/all-interactive-columns.png)


Válasszon ki egy elemet a listanézetben, hogy részletesebb információkat kapjon a kapcsolódó bejelentkezésről.

![Bejelentkezési tevékenység](./media/concept-all-sign-ins/interactive-user-sign-in-details.png "Interaktív felhasználói bejelentkezések")



### <a name="non-interactive-user-sign-ins"></a>Nem interaktív felhasználói bejelentkezések

A nem interaktív felhasználói bejelentkezések olyan bejelentkezések, amelyek ügyfélalkalmazás vagy operációsrendszer-összetevők által, egy felhasználó nevében történő bejelentkezések. Az interaktív felhasználói bejelentkezéshez hasonló a bejelentkezések is a felhasználó nevében történik. Az interaktív felhasználói bejelentkezésekkel ellentétben ezek a bejelentkezések nem követelik meg, hogy a felhasználó hitelesítési tényezőt adjon meg. Ehelyett az eszköz vagy az ügyfélalkalmazás jogkivonat vagy kód használatával hitelesít vagy fér hozzá egy erőforráshoz egy felhasználó nevében. A felhasználó általában úgy fogja érzékelni, hogy ezek a bejelentkezések a felhasználó tevékenységének hátterében történnek.


**Jelentés mérete:** Nagy <br>
**Példák:** 

- Az ügyfélalkalmazások OAuth 2.0 frissítési jogkivonattal szereznek be hozzáférési jogkivonatot.

- Az ügyfél OAuth 2.0-hitelesítési kódot használ a hozzáférési jogkivonat lekért és a jogkivonat frissítésére.

- A felhasználó egyszeri bejelentkezést (SSO) végez egy web- vagy Windows-alkalmazásba egy Azure AD-hez csatlakozott számítógépen.

- A felhasználó bejelentkezik egy második Microsoft Office, miközben egy mobileszközön van munkamenete a FOCI (ügyfél-azonosítók) használatával.




Az alapértelmezett mezőkön kívül a nem interaktív bejelentkezések jelentés a következő adatokat is megjeleníti: 

- Erőforrás-azonosító

- Csoportosított bejelentkezések száma




A jelentésben megjelenő mezők nem szabhatók testre.


![Letiltott oszlopok](./media/concept-all-sign-ins/disabled-columns.png "Letiltott oszlopok")

Az adatok könnyebb kivonatolása érdekében a nem interaktív bejelentkezési események csoportosítva vannak. Az ügyfelek gyakran hoznak létre rövid időn belül számos nem interaktív bejelentkezést ugyanannak a felhasználónak a nevében, amelyek ugyanazokkal a jellemzőkkel rendelkeznek, kivéve a bejelentkezés megkísérlését. Előfordulhat például, hogy egy ügyfél óránként egyszer kap hozzáférési jogkivonatot egy felhasználó nevében. Ha a felhasználó vagy az ügyfél nem módosítja az állapotot, az IP-cím, az erőforrás és minden egyéb információ ugyanaz lesz minden hozzáférési jogkivonat kérése esetén. Ha az Azure AD több olyan bejelentkezést is naplóz, amelyek nem azonosak az időtől és a dátumtól, akkor ezek a bejelentkezések ugyanabba az entitásba lesznek összesítve egyetlen sorban. A több azonos bejelentkezést tartalmazó sorok (a kiadott dátum és idő kivételével) 1-esnél nagyobb értéket fognak a # bejelentkezések oszlopban. A sort kibontva láthatja az összes különböző bejelentkezést és azok időbélyegét. A nem interaktív felhasználók összesíti a bejelentkezéseket, ha a következő adatok megegyeznek:


- Alkalmazás

- Felhasználó

- IP-cím

- Állapot

- Erőforrás-azonosító


A következőket teheti:

- Bontsa ki a csomópontot a csoport egyes elemeinek a ásához.  

- Kattintson egy adott elemre az összes részlet megtekintése 


![Nem interaktív felhasználói bejelentkezési adatok](./media/concept-all-sign-ins/non-interactive-sign-ins-details.png)




## <a name="service-principal-sign-ins"></a>Szolgáltatásnév-bejelentkezések

Az interaktív és nem interaktív felhasználói bejelentkezésekkel ellentétben a szolgáltatásnév-bejelentkezések nem járnak felhasználóval. Ehelyett bármely nem felhasználói fiókkal, például alkalmazásokkal vagy szolgáltatásnévvel jelentkeznek be (kivéve a felügyelt identitással való bejelentkezést, amely csak a felügyelt identitás bejelentkezési jelentésében szerepel). Ezekben a bejelentkezésekben az alkalmazás vagy szolgáltatás biztosítja a saját hitelesítő adatait, például egy tanúsítványt vagy egy titkos alkalmazás titkos adatokat az erőforrások hitelesítéséhez vagy eléréséhez.


**Jelentés mérete:** Nagy <br>
**Példák:**

- A szolgáltatásnév tanúsítvánnyal hitelesíti és hozzáfér a Microsoft Graph. 

- Az alkalmazások titkos ügyféltitkot használnak az OAuth-ügyfél hitelesítő adatainak folyamatában való hitelesítéshez. 


A jelentés alapértelmezett listanézete a következőt jeleníti meg:

- Bejelentkezés dátuma

- Kérés azonosítója

- Egyszerű szolgáltatásnév vagy -azonosító

- Állapot

- IP-cím

- Erőforrás neve

- Erőforrás-azonosító

- Bejelentkezések száma

A jelentésben megjelenő mezők nem szabhatók testre.

![Letiltott oszlopok](./media/concept-all-sign-ins/disabled-columns.png "Letiltott oszlopok")

A szolgáltatásnév bejelentkezési naplóiban található adatok könnyebb kivonatolása érdekében a szolgáltatásnév bejelentkezési eseményei csoportosítva vannak. Az ugyanazon entitásból azonos feltételek mellett történő bejelentkezések egyetlen sorban vannak összesítve. A sort kibontva láthatja az összes különböző bejelentkezést és azok időbélyegét. A rendszer összesíti a bejelentkezéseket a szolgáltatásnév-jelentésben, ha a következő adatok megegyeznek:

- Egyszerű szolgáltatásnév vagy -azonosító

- Állapot

- IP-cím

- Erőforrás neve vagy azonosítója

A következőket teheti:

- Bontsa ki a csomópontot a csoport egyes elemeinek a ásához.  

- Kattintson egy adott elemre az összes részlet megtekintése 


![Oszlop részletei](./media/concept-all-sign-ins/service-principals-sign-ins-view.png "Oszlop részletei")




## <a name="managed-identity-for-azure-resources-sign-ins"></a>Azure-erőforrások felügyelt identitásának bejelentkezései 

Az Azure-erőforrások felügyelt identitásának bejelentkezései olyan erőforrások által végzett bejelentkezések, amelyek titkos adatait az Azure kezeli a hitelesítő adatok egyszerűbb kezelése érdekében.

**Jelentés mérete:** Kis <br> 
**Példák:**

A felügyelt hitelesítő adatokkal virtuális gépek az Azure AD használatával szereznek be hozzáférési jogkivonatot.   


A jelentés alapértelmezett listanézete a következőt jeleníti meg:


- Felügyelt identitás azonosítója

- Felügyelt identitás neve

- Erőforrás

- Erőforrás-azonosító

- Csoportosított bejelentkezések száma

A jelentésben megjelenő mezők nem szabhatók testre.

Az adatok könnyebb kivonatolása érdekében az Azure-erőforrások felügyelt identitása bejelentkezési naplókat használ, a nem interaktív bejelentkezési események csoportosítva vannak. Az ugyanattól az entitástól származó bejelentkezéseket a rendszer egyetlen sorban összesíti. A sort kibontva láthatja az összes különböző bejelentkezést és azok időbélyegét. A rendszer összesíti a bejelentkezéseket a felügyelt identitások jelentésében, ha az összes alábbi adat megegyezik:

- Felügyelt identitás neve vagy azonosítója

- Állapot

- IP-cím

- Erőforrás neve vagy azonosítója

Jelöljön ki egy elemet a listanézetben a csomópont alatt csoportosított összes bejelentkezés megjelenítéséhez.

Jelöljön ki egy csoportosított elemet a bejelentkezés részleteinek a megtekintése előtt. 


## <a name="sign-in-error-code"></a>Bejelentkezési hibakód

Ha egy bejelentkezés sikertelen, a kapcsolódó naplóelem Alapszintű **információk** szakaszában további információt kaphat az okról. 

![Részletes információ nézet képernyőképe.](./media/concept-all-sign-ins/error-code.png)
 
Bár a naplóelem a hiba okát is tartalmazza, előfordulhatnak olyan esetek, amikor további információkat kap a bejelentkezési [hibakeresési eszközével.](https://login.microsoftonline.com/error) Ha például elérhető, ez az eszköz biztosítja a szervizelés lépéseit.  

![Hibakód-keresési eszköz](./media/concept-all-sign-ins/error-code-lookup-tool.png)



## <a name="filter-sign-in-activities"></a>A bejelentkezési tevékenységek szűrése

Szűrő beállításával leszűkítheti a visszaadott bejelentkezési adatok hatókörét. Az Azure AD számos további beállítható szűrőt biztosít. A szűrő beállításakor mindig fordítson különös figyelmet  a beállított Dátumtartomány szűrőre. A megfelelő dátumtartomány-szűrő biztosítja, hogy az Azure AD csak azokat az adatokat adja vissza, amelyek igazán fontosak.     

A **Dátumtartomány** szűrővel időkeretet határozhat meg a visszaadott adatokhoz.
Lehetséges értékek:

- Egy hónap

- Hét nap

- 24 óra

- Egyéni

![Dátumtartomány-szűrő](./media/concept-all-sign-ins/date-range-filter.png)





### <a name="filter-user-sign-ins"></a>Felhasználói bejelentkezések szűrése

Az interaktív és nem interaktív bejelentkezések szűrője ugyanaz. Emiatt az interaktív bejelentkezéshez konfigurált szűrő megmarad nem interaktív bejelentkezések esetén, és fordítva. 






## <a name="access-the-new-sign-in-activity-reports"></a>Az új bejelentkezési tevékenység jelentésének elérése 

A bejelentkezési tevékenységről szóló jelentés a Azure Portal egyszerű módszert kínál az előnézeti jelentés be- és kikapcsolása érdekében. Ha engedélyezve vannak az előzetes verziójú jelentések, egy új menüt kap, amely hozzáférést biztosít a bejelentkezési tevékenységek jelentéstípushoz.     


Az új bejelentkezési jelentések elérése nem interaktív és alkalmazás-bejelentkezésekkel: 

1. Az [Azure Portalon](https://portal.azure.com) válassza az **Azure Active Directory** lehetőséget.

    ![Az Azure AD kiválasztása](./media/concept-all-sign-ins/azure-services.png)

2. A **Figyelés szakaszban** kattintson a **Bejelentkezések elemre.**

    ![Bejelentkezések kiválasztása](./media/concept-all-sign-ins/sign-ins.png)

3. Kattintson az Előnézet **sávra.**

    ![Új nézet engedélyezése](./media/concept-all-sign-ins/enable-new-preview.png)

4. Az alapértelmezett nézetre való visszaváltáshoz kattintson ismét az Előnézet **sávra.** 

    ![Klasszikus nézet visszaállítása](./media/concept-all-sign-ins/switch-back.png)







## <a name="download-sign-in-activity-reports"></a>Bejelentkezési tevékenységre vonatkozó jelentések letöltése

Bejelentkezési tevékenységről szóló jelentés letöltésekor a következő igaz:

- A bejelentkezési jelentést CSV- vagy JSON-fájlként töltheti le.

- Legfeljebb 100 ezer rekordot tölthet le. Ha további adatokat szeretne letölteni, használja a reporting API-t.

- A letöltés az Ön által kiválasztott szűrőn alapul.

- A letölthető rekordok számát a jelentésmegőrzési [szabályzatok Azure Active Directory korlátozza.](reference-reports-data-retention.md) 


![Jelentések letöltése](./media/concept-all-sign-ins/download-reports.png "Jelentések letöltése")


Minden CSV-letöltés hat különböző fájlból áll:

- Interaktív bejelentkezések

- Az interaktív bejelentkezések hitelesítési részletei

- Nem interaktív bejelentkezések

- A nem interaktív bejelentkezések hitelesítési részletei

- Szolgáltatásnév-bejelentkezések

- Azure-erőforrások felügyelt identitásának bejelentkezései

Minden JSON-letöltés négy különböző fájlból áll:

- Interaktív bejelentkezések (hitelesítési részleteket tartalmaz)

- Nem interaktív bejelentkezések (hitelesítési részleteket tartalmaz)

- Szolgáltatásnév-bejelentkezések

- Azure-erőforrások felügyelt identitásának bejelentkezései

![Fájlok letöltése](./media/concept-all-sign-ins/download-files.png "Fájlok letöltése")




## <a name="next-steps"></a>Következő lépések

* [Bejelentkezési tevékenység jelentésének hibakódja](reference-sign-ins-error-codes.md)
* [Azure AD adatmegőrzési szabályzatok](reference-reports-data-retention.md)
* [Az Azure AD-jelentések késései](reference-reports-latencies.md)
