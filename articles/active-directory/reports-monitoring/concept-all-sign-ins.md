---
title: Azure Active Directory bejelentkezési tevékenység jelentései – előzetes verzió | Microsoft Docs
description: A bejelentkezési tevékenység jelentéseinek bemutatása a Azure Active Directory portálon
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 03/16/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: b17e7f34da1428eec219a393417b0a2a7b461078
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "106552733"
---
# <a name="azure-active-directory-sign-in-activity-reports---preview"></a>Azure Active Directory bejelentkezési tevékenységgel kapcsolatos jelentések – előzetes verzió

A Azure Active Directory (Azure AD) jelentéskészítési architektúrája a következő összetevőkből áll:

- **Tevékenység** 
    - **Bejelentkezések** – információ arról, ha a felhasználók, alkalmazások és felügyelt erőforrások bejelentkeznek az Azure ad-be és az erőforrások elérésére.
    - **Naplók**  -  A [naplók](concept-audit-logs.md) rendszertevékenységi információkat biztosítanak a felhasználókról és a csoport kezeléséről, a felügyelt alkalmazásokról és a címtárbeli tevékenységekről.
- **Biztonság** 
    - **Kockázatos bejelentkezések** – a [kockázatos bejelentkezés](../identity-protection/overview-identity-protection.md) egy olyan bejelentkezési kísérlet, amely nem a felhasználói fiók legitim tulajdonosa.
    - **Kockázatnak** kitett felhasználók – a [kockázatos felhasználók](../identity-protection/overview-identity-protection.md) egy olyan felhasználói fiókra vonatkozó jelző, amely esetleg sérült.

A Azure Active Directory klasszikus bejelentkezések jelentése az interaktív felhasználói bejelentkezések áttekintését nyújtja. Emellett három további, előzetes verzióban elérhető bejelentkezési jelentéshez férhet hozzá:

- Nem interaktív felhasználói bejelentkezések

- Egyszerű szolgáltatások bejelentkezései

- Az Azure erőforrás-bejelentkezések felügyelt identitásai

Ez a cikk áttekintést nyújt a bejelentkezési tevékenységről a nem interaktív, alkalmazás-és felügyelt identitások előzetes verziójával az Azure-erőforrásokhoz való bejelentkezéshez. Az előzetes verziójú funkciók nélküli bejelentkezési jelentéssel kapcsolatos információkért tekintse meg a  [Azure Active Directory-portálon a bejelentkezési tevékenységek jelentéseit](concept-sign-ins.md).



## <a name="prerequisites"></a>Előfeltételek

A szolgáltatás használatának megkezdése előtt ismernie kell a következő válaszokat:

- Ki férhet hozzá az adatokhoz?

- Milyen Azure AD-licencre van szükség a bejelentkezési tevékenységhez való hozzáféréshez?

### <a name="who-can-access-the-data"></a>Ki férhet hozzá az adatokhoz?

- A biztonsági rendszergazda, a biztonsági olvasó és a jelentéskészítő-olvasó szerepkörrel rendelkező felhasználók

- Globális rendszergazdák

- Bármely (nem rendszergazda jogosultságú) felhasználó hozzáfér a saját bejelentkezéseihez 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Milyen Azure AD-licencre van szükség a bejelentkezési tevékenységhez való hozzáféréshez?

A bérlőnek hozzá kell rendelnie prémium szintű Azure AD licenccel a bejelentkezési tevékenységek megtekintéséhez. A Azure Active Directory kiadásának frissítéséhez tekintse meg a [prémium szintű Azure Active Directory első lépéseivel foglalkozó](../fundamentals/active-directory-get-started-premium.md) témakört. A frissítés előtt néhány napig is eltarthat, hogy az adatai megjelenjenek a jelentésekben, miután a frissítés előtt adattevékenység nélküli prémium szintű licencre frissít.



## <a name="sign-ins-report"></a>Bejelentkezések jelentés

A bejelentkezések jelentés a következő kérdésekre ad választ:

- Mi a felhasználó, alkalmazás vagy szolgáltatás bejelentkezési mintája?
- Hány felhasználó, alkalmazás vagy szolgáltatás jelentkezett be egy hétre?
- Milyen állapotúak ezek a bejelentkezések?


A bejelentkezési jelentés panelen válthat a következők közül:

- **Interaktív felhasználói bejelentkezések** – olyan bejelentkezések, amelyekben a felhasználó hitelesítési tényezőt, például jelszót, egy MFA-alkalmazás, egy biometrikus tényező vagy egy QR-kód válaszát adja meg.

- **Nem interaktív felhasználói bejelentkezések** – az ügyfél által a felhasználó nevében végrehajtott bejelentkezések. Ezek a bejelentkezések nem igényelnek semmilyen interakciót vagy hitelesítési tényezőt a felhasználótól. Például a hitelesítés és az engedélyezés olyan frissítési és hozzáférési jogkivonatok használatával, amelyek nem igénylik a felhasználónak a hitelesítő adatok megadását.

- **Egyszerű szolgáltatás-bejelentkezések** – olyan alkalmazások és egyszerű szolgáltatások, amelyek nem tartalmaznak felhasználót. Ezekben a bejelentkezésekben az alkalmazás vagy szolgáltatás hitelesítő adatokat szolgáltat a saját nevében az erőforrások hitelesítéséhez vagy eléréséhez.

- **Felügyelt identitások az Azure-erőforrások számára** az Azure által felügyelt titkos kulcsokkal rendelkező Azure-erőforrások által végzett bejelentkezésekhez. További információ: [Mi az Azure-erőforrások felügyelt identitása?](../managed-identities-azure-resources/overview.md) 


![Bejelentkezési jelentések típusai](./media/concept-all-sign-ins/sign-ins-report-types.png)












## <a name="user-sign-ins&quot;></a>Felhasználói bejelentkezések

A bejelentkezések panel minden lapja az alábbi alapértelmezett oszlopokat jeleníti meg. Egyes lapok további oszlopokkal rendelkeznek:

- Bejelentkezés dátuma

- Kérés azonosítója

- Felhasználónév vagy felhasználói azonosító

- Alkalmazás neve vagy alkalmazás-azonosítója

- A bejelentkezés állapota

- A bejelentkezéshez használt eszköz IP-címe



### <a name=&quot;interactive-user-sign-ins&quot;></a>Interaktív felhasználói bejelentkezések


Az interaktív felhasználói bejelentkezések olyan bejelentkezések, amelyekben a felhasználó hitelesítési tényezőt biztosít az Azure AD-hez, vagy közvetlenül az Azure AD-vel vagy egy segítő alkalmazással, például a Microsoft Authenticator alkalmazással kommunikál. A felhasználók által biztosított tényezők közé tartoznak a jelszavak, az MFA-kihívásokra, a biometrikus tényezőkre, valamint a felhasználók által az Azure AD-be vagy egy segítő alkalmazásba küldött QR-kódokra adott válaszok.

> [!NOTE]
> Ez a jelentés az Azure AD-ba összevont identitás-szolgáltatók összevont bejelentkezéseit is tartalmazza.  



Megjegyzés: az interaktív felhasználói bejelentkezések jelentésében a Microsoft Exchange-ügyfelektől nem interaktív bejelentkezések is szerepelnek. Habár ezek a bejelentkezések nem interaktívak voltak, az interaktív felhasználói bejelentkezések jelentésében további láthatóságot is tartalmaz. Ha a nem interaktív felhasználói bejelentkezések jelentésének nyilvános előzetes verziója 2020 novemberében lépett fel, akkor a nem interaktív bejelentkezési eseménynaplók a nem interaktív felhasználói bejelentkezési jelentésbe kerültek a nagyobb pontosság érdekében. 


**Jelentés mérete:** kicsi <br> 
**Példák**

- A felhasználók felhasználónevet és jelszót biztosítanak az Azure AD bejelentkezési képernyőjén.

- Egy felhasználó SMS MFA-kihívást ad át.

- A felhasználó biometrikus kézmozdulatot biztosít a Windows Hello for Business rendszerű számítógép zárolásának feloldásához.

- A felhasználók egy AD FS SAML-kijelentéssel összevonták az Azure AD-t.


Az alapértelmezett mezőkön kívül az interaktív bejelentkezési jelentés a következőket is megjeleníti: 

- A bejelentkezési hely

- A feltételes hozzáférés alkalmazása megtörtént-e



A listanézetet az eszköztár **Oszlopok** elemére kattintva lehet testre szabni.

![Interaktív felhasználói bejelentkezési oszlopok](./media/concept-all-sign-ins/columns-interactive.png &quot;Interaktív felhasználói bejelentkezési oszlopok")





A nézet testreszabásával további mezőket jeleníthet meg, vagy eltávolíthat olyan mezőket, amelyek már láthatók.

![Minden interaktív oszlop](./media/concept-all-sign-ins/all-interactive-columns.png)


Válassza ki a lista nézet egyik elemét, hogy részletesebb információkat kapjon a kapcsolódó bejelentkezésről.

![Bejelentkezési tevékenység](./media/concept-all-sign-ins/interactive-user-sign-in-details.png "Interaktív felhasználói bejelentkezések")



### <a name="non-interactive-user-sign-ins"></a>Nem interaktív felhasználói bejelentkezések

A nem interaktív felhasználói bejelentkezések olyan bejelentkezések, amelyeket egy adott ügyfélalkalmazás vagy operációsrendszer-összetevő hajtott végre egy felhasználó nevében. Az interaktív felhasználói bejelentkezésekhez hasonlóan ezek a bejelentkezések a felhasználók nevében hajthatók végre. Az interaktív felhasználói bejelentkezésekkel ellentétben ezek a bejelentkezések nem igénylik, hogy a felhasználó hitelesítési tényezőt adjon meg. Ehelyett az eszköz vagy az ügyfélalkalmazás egy jogkivonat vagy kód használatával hitelesíti vagy fér hozzá egy erőforráshoz egy felhasználó nevében. Általánosságban elmondható, hogy a felhasználó a felhasználó tevékenységének hátterében történik.


**Jelentés mérete:** Nagy <br>
**Példák:** 

- Az ügyfélalkalmazás egy OAuth 2,0 frissítési jogkivonatot használ a hozzáférési jogkivonat beszerzéséhez.

- Az ügyfél egy OAuth 2,0 hitelesítési kódot használ a hozzáférési jogkivonat és a frissítési jogkivonat lekéréséhez.

- A felhasználók egyszeri bejelentkezést (SSO-t) végeznek egy webes vagy Windows-alkalmazásba az Azure AD-hez csatlakoztatott SZÁMÍTÓGÉPeken.

- A felhasználó bejelentkezik egy második Microsoft Office alkalmazásba, miközben a FOCI (az ügyfél-azonosítók családja) használó mobileszköz-munkamenettel rendelkezik.




Az alapértelmezett mezőkön kívül a nem interaktív bejelentkezések jelentés is a következőket jeleníti meg: 

- Erőforrás-azonosító

- Csoportosított bejelentkezések száma




A jelentésben látható mezők nem szabhatók testre.


![Letiltott oszlopok](./media/concept-all-sign-ins/disabled-columns.png "Letiltott oszlopok")

Az adatelemzés egyszerűbbé tétele érdekében a nem interaktív bejelentkezési események vannak csoportosítva. Az ügyfelek gyakran sok nem interaktív bejelentkezést hoznak létre ugyanazon felhasználó nevében rövid idő alatt, amely ugyanazokat a tulajdonságokat osztja meg, kivéve a bejelentkezési kísérlet idejét. Előfordulhat például, hogy egy ügyfél óránként egyszer kap hozzáférési jogkivonatot egy felhasználó nevében. Ha a felhasználó vagy az ügyfél nem változtatja meg az állapotot, az IP-cím, az erőforrás és minden egyéb információ megegyezik az egyes hozzáférési jogkivonat-kérelmek esetében. Ha az Azure AD több olyan bejelentkezést naplóz, amely az idő és a dátum kivételével azonos, akkor ezek a bejelentkezések ugyanabból az entitásból lesznek összesítve egyetlen sorba. Több azonos bejelentkezéssel rendelkező sor (kivéve a kiállított dátumot és időt) a # Sign-ins oszlopban 1-nél nagyobb érték lesz. A sor kibontásával megjelenítheti az összes különböző bejelentkezést és a különböző időbélyegeket. A bejelentkezéseket a nem interaktív felhasználók összesítik, ha az alábbi adatokat tartalmazza:


- Alkalmazás

- Felhasználó

- IP-cím

- Állapot

- Erőforrás-azonosító


A következőket teheti:

- Csomópont kibontásával megtekintheti a csoportok egyes elemeit.  

- Az összes adat megjelenítéséhez kattintson egy adott elemre. 


![Nem interaktív felhasználói bejelentkezés részletei](./media/concept-all-sign-ins/non-interactive-sign-ins-details.png)




## <a name="service-principal-sign-ins"></a>Egyszerű szolgáltatások bejelentkezései

Az interaktív és a nem interaktív felhasználói bejelentkezésekkel ellentétben az egyszerű szolgáltatásnév-bejelentkezések nem tartalmaznak felhasználót. Ehelyett a nem felhasználói fiókok, például az alkalmazások vagy a egyszerű szolgáltatások (a felügyelt identitások bejelentkezését kivéve, amelyek csak a felügyelt identitású bejelentkezések jelentésben szerepelnek) bejelentkeznek. Ezekben a bejelentkezésekben az alkalmazás vagy szolgáltatás a saját hitelesítő adatait (például tanúsítvány vagy alkalmazás titkát) biztosítja az erőforrások hitelesítéséhez vagy eléréséhez.


**Jelentés mérete:** Nagy <br>
**Példák:**

- Egy egyszerű szolgáltatásnév tanúsítványt használ a Microsoft Graph hitelesítéséhez és eléréséhez. 

- Az alkalmazás egy ügyfél-titkos kulcsot használ a OAuth-ügyfél hitelesítő adatainak folyamatában való hitelesítéshez. 


Ez a jelentés egy alapértelmezett listanézet, amely a következőket jeleníti meg:

- Bejelentkezés dátuma

- Kérés azonosítója

- Egyszerű szolgáltatásnév vagy azonosító

- Állapot

- IP-cím

- Erőforrás neve

- Erőforrás-azonosító

- Bejelentkezések száma

A jelentésben látható mezők nem szabhatók testre.

![Letiltott oszlopok](./media/concept-all-sign-ins/disabled-columns.png "Letiltott oszlopok")

Annak érdekében, hogy könnyebb legyen megemészteni az egyszerű szolgáltatásnév bejelentkezési naplóiban lévő adatkivonatokat, a szolgáltatás egyszerű bejelentkezési eseményei vannak csoportosítva. Ugyanazokból az entitásokból származó bejelentkezések egyetlen sorba vannak összesítve. A sor kibontásával megjelenítheti az összes különböző bejelentkezést és a különböző időbélyegeket. A bejelentkezések az egyszerű szolgáltatásnév jelentésében vannak összesítve, ha a következő adatértékek teljesülnek:

- Egyszerű szolgáltatásnév vagy azonosító

- Állapot

- IP-cím

- Erőforrás neve vagy azonosítója

A következőket teheti:

- Csomópont kibontásával megtekintheti a csoportok egyes elemeit.  

- Kattintson egy egyedi elemre, és tekintse meg az összes adatot 


![Oszlop részletei](./media/concept-all-sign-ins/service-principals-sign-ins-view.png "Oszlop részletei")




## <a name="managed-identity-for-azure-resources-sign-ins"></a>Felügyelt identitás az Azure-erőforrások bejelentkezéséhez 

Az Azure-erőforrások felügyelt identitása olyan bejelentkezések, amelyeket az Azure által kezelt, a hitelesítő adatok kezelésének egyszerűsítése érdekében a titkos kulcsokkal rendelkező erőforrások hajtottak végre.

**Jelentés mérete:** Kis <br> 
**Példák**

A felügyelt hitelesítő adatokkal rendelkező virtuális gépek az Azure AD használatával kapnak hozzáférési jogkivonatot.   


Ez a jelentés egy alapértelmezett listanézet, amely a következőket jeleníti meg:


- Felügyelt identitás azonosítója

- Felügyelt identitás neve

- Erőforrás

- Erőforrás-azonosító

- Csoportosított bejelentkezések száma

A jelentésben látható mezők nem szabhatók testre.

Annak érdekében, hogy könnyebb legyen az adatelemzés, az Azure-erőforrásokhoz tartozó felügyelt identitások bejelentkezve, a nem interaktív bejelentkezési események csoportosítva lesznek. Ugyanabból az entitásból származó bejelentkezések egyetlen sorba vannak összesítve. A sor kibontásával megjelenítheti az összes különböző bejelentkezést és a különböző időbélyegeket. A rendszer összesíti a bejelentkezéseket a felügyelt identitások jelentésben, ha a következő adatértékek mindegyike megfelel:

- Felügyelt identitás neve vagy azonosítója

- Állapot

- IP-cím

- Erőforrás neve vagy azonosítója

Válassza ki a lista nézet egyik elemét, és jelenítse meg az összes olyan bejelentkezést, amelyek egy csomópont alatt vannak csoportosítva.

Válasszon egy csoportosított elemet a bejelentkezés összes adatának megtekintéséhez. 


## <a name="filter-sign-in-activities"></a>A bejelentkezési tevékenységek szűrése

A szűrő beállításával leszűkítheti a visszaadott bejelentkezési adatmennyiséget. Az Azure AD a további beállítható szűrők széles választékát kínálja. A szűrő beállításakor a beállított **dátumtartomány** -szűrőnek mindig különös figyelmet kell fordítania. A megfelelő dátumtartomány-szűrő biztosítja, hogy az Azure AD csak azokat az információkat adja vissza, amelyekről valóban érdeklik.     

A **dátumtartomány** -szűrő lehetővé teszi, hogy meghatározza a visszaadott adatok időkeretét.
Lehetséges értékek:

- Egy hónap

- Hét nap

- Huszonnégy óra

- Egyéni

![Dátumtartomány-szűrő](./media/concept-all-sign-ins/date-range-filter.png)





### <a name="filter-user-sign-ins"></a>Felhasználói bejelentkezések szűrése

Az interaktív és nem interaktív bejelentkezések szűrője azonos. Emiatt az interaktív bejelentkezésekhez konfigurált szűrő megmarad a nem interaktív bejelentkezésekhez, és fordítva. 






## <a name="access-the-new-sign-in-activity-reports"></a>Hozzáférés az új bejelentkezési tevékenység jelentéseihez 

A Azure Portal a bejelentkezések tevékenységéről szóló jelentés egyszerű módszert biztosít az előzetes jelentés be-és kikapcsolására. Ha az előzetes verziójú jelentések engedélyezve vannak, egy új menüt kap, amely hozzáférést biztosít az összes bejelentkezési tevékenység jelentés típusához.     


Az új bejelentkezési jelentések nem interaktív és alkalmazás-bejelentkezésekkel való elérése: 

1. Az [Azure Portalon](https://portal.azure.com) válassza az **Azure Active Directory** lehetőséget.

    ![Az Azure AD kiválasztása](./media/concept-all-sign-ins/azure-services.png)

2. A **figyelés** szakaszban kattintson a **bejelentkezések** elemre.

    ![Bejelentkezések kiválasztása](./media/concept-all-sign-ins/sign-ins.png)

3. Kattintson az **előnézet** sávra.

    ![Új nézet engedélyezése](./media/concept-all-sign-ins/enable-new-preview.png)

4. Ha vissza szeretne térni az alapértelmezett nézetre, kattintson ismét az **előnézeti** sávra. 

    ![Klasszikus nézet visszaállítása](./media/concept-all-sign-ins/switch-back.png)







## <a name="download-sign-in-activity-reports"></a>Bejelentkezési tevékenységgel kapcsolatos jelentések letöltése

A bejelentkezési tevékenységek jelentésének letöltésekor a következők teljesülnek:

- A bejelentkezési jelentést CSV-ként vagy JSON-fájlként is letöltheti.

- Akár 100-K rekordok is letölthetők. Ha több adatfájlt szeretne letölteni, használja a jelentéskészítési API-t.

- A letöltés a kiválasztott szűrő alapján történik.

- A letöltendő rekordok számát a [Azure Active Directory jelentés adatmegőrzési szabályzata](reference-reports-data-retention.md)korlátozza. 


![Jelentések letöltése](./media/concept-all-sign-ins/download-reports.png "Jelentések letöltése")


Minden CSV-Letöltés hat különböző fájlból áll:

- Interaktív bejelentkezések

- Az interaktív bejelentkezések hitelesítési adatai

- Nem interaktív bejelentkezések

- A nem interaktív bejelentkezések hitelesítési adatai

- Egyszerű szolgáltatások bejelentkezései

- Felügyelt identitás az Azure-erőforrások bejelentkezéséhez

Minden JSON-Letöltés négy különböző fájlból áll:

- Interaktív bejelentkezések (beleértve az Auth részleteit)

- Nem interaktív bejelentkezések (beleértve az Auth részleteit)

- Egyszerű szolgáltatások bejelentkezései

- Felügyelt identitás az Azure-erőforrások bejelentkezéséhez

![Fájlok letöltése](./media/concept-all-sign-ins/download-files.png "Fájlok letöltése")




## <a name="next-steps"></a>Következő lépések

* [Bejelentkezési tevékenység jelentésének hibakódja](reference-sign-ins-error-codes.md)
* [Az Azure AD adatmegőrzési szabályzatai](reference-reports-data-retention.md)
* [Azure AD-jelentés késései](reference-reports-latencies.md)
