---
title: A Azure Active Directory és az SAP SuccessFactors integrációs referenciája
description: Technikai részletes betekintést az SAP SuccessFactors – HR-vezérelt kiépítés
services: active-directory
author: cmmdesai
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 01/19/2021
ms.author: chmutali
ms.openlocfilehash: ed97600ca1802629f81f93f4f51c92ad4b1c9bd1
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99256221"
---
# <a name="how-azure-active-directory-provisioning-integrates-with-sap-successfactors"></a>A Azure Active Directory kiépítés integrálása az SAP SuccessFactors 

[Azure Active Directory a felhasználó-kiépítési szolgáltatás](../app-provisioning/user-provisioning.md) integrálható az [SAP SuccessFactors Employee Central](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) szolgáltatással a felhasználók identitási életciklusának kezeléséhez. A Azure Active Directory három előre elkészített integrációt kínál: 

* [SuccessFactors a helyszíni Active Directory a felhasználó üzembe helyezése](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
* [SuccessFactors Azure Active Directory a felhasználók üzembe helyezése](../saas-apps/sap-successfactors-inbound-provisioning-cloud-only-tutorial.md)
* [SuccessFactors visszaírási](../saas-apps/sap-successfactors-writeback-tutorial.md)

Ez a cikk bemutatja, hogyan működik az integráció, és hogyan szabhatja testre a különböző HR-forgatókönyvek kiépítési viselkedését. 

## <a name="establishing-connectivity"></a>Kapcsolat létesítése 
Az Azure AD kiépítési szolgáltatás alapszintű hitelesítést használ az Employee Central OData API-végpontokhoz való kapcsolódáshoz. Az SuccessFactors kiépítési alkalmazásának beállításakor a *rendszergazdai hitelesítő adatok* szakaszban a *bérlői URL* -cím paraméterrel konfigurálhatja az [API-adatközpont URL-címét](https://apps.support.sap.com/sap/support/knowledge/en/2215682). 

Az Azure AD kiépítési szolgáltatás és a SuccessFactors közötti kapcsolat további biztonságossá tételéhez az alábbi lépések végrehajtásával adhatja hozzá az Azure AD IP-tartományokat a SuccessFactors IP-engedélyezési listájában:

1. Töltse le az Azure nyilvános felhő [legújabb IP-tartományait](https://www.microsoft.com/download/details.aspx?id=56519) 
1. Nyissa meg a fájlt, és keresse meg a **AzureActiveDirectory** címke kifejezést. 

   >[!div class="mx-imgBorder"] 
   >![Azure AD IP-címtartomány](media/sap-successfactors-integration-reference/azure-active-directory-ip-range.png)

1. Másolja az elem *addressPrefixes* belül felsorolt összes IP-címtartományt, és használja a tartományt az IP-címek korlátozási listájának létrehozásához.
1. A CIDR értékeinek lefordítása IP-tartományokra.  
1. Jelentkezzen be a SuccessFactors felügyeleti portálra, és adja hozzá az IP-tartományokat az engedélyezési listához. Tekintse meg a 2253200-es SAP- [támogatási megjegyzést](https://apps.support.sap.com/sap/support/knowledge/en/2253200). Ezután [megadhatja az IP-tartományokat](https://answers.sap.com/questions/12882263/whitelisting-sap-cloud-platform-ip-address-range-i.html) ebben az eszközben. 

## <a name="supported-entities"></a>Támogatott entitások
A SuccessFactors összes felhasználója esetében az Azure AD kiépítési szolgáltatása lekéri a következő entitásokat. Minden entitás ki van bővítve a OData API *$Expand* lekérdezési paraméter használatával. Tekintse meg az alábbi *lekérési szabály* oszlopot. Egyes entitások alapértelmezés szerint ki vannak bontva, míg egyes entitások kibontása csak akkor történik meg, ha egy adott attribútum szerepel a leképezésben. 

| \# | SuccessFactors entitás                  | OData csomópont     | Lekérési szabály |
|----|----------------------------------------|------------------------------|------------------|
| 1  | PerPerson                              | *gyökérszintű csomópont*                  | Mindig           |
| 2  | PerPersonal                            | personalInfoNav              | Mindig           |
| 3  | PerPhone                               | phoneNav                     | Mindig           |
| 4  | PerEmail                               | emailNav                     | Mindig           |
| 5  | EmpEmployment                          | employmentNav                | Mindig           |
| 6  | User                                   | employmentNav/userNav        | Mindig           |
| 7  | EmpJob                                 | employmentNav/jobInfoNav     | Mindig           |
| 8  | EmpEmploymentTermination               | activeEmploymentsCount       | Mindig           |
| 9  | Felhasználó felettese                         | employmentNav/userNav/Manager/empInfo | Mindig  |
| 10 | FOCompany                              | employmentNav/jobInfoNav/companyNav | Csak akkor, ha `company` vagy `companyId` attribútum van leképezve |
| 11 | FODepartment                           | employmentNav/jobInfoNav/departmentNav | Csak akkor, ha `department` vagy `departmentId` attribútum van leképezve |
| 12 | FOBusinessUnit                         | employmentNav/jobInfoNav/businessUnitNav | Csak akkor, ha `businessUnit` vagy `businessUnitId` attribútum van leképezve |
| 13 | FOCostCenter                           | employmentNav/jobInfoNav/costCenterNav | Csak akkor, ha `costCenter` vagy `costCenterId` attribútum van leképezve |
| 14 | FODivision                             | employmentNav/jobInfoNav/divisionNav  | Csak akkor, ha `division` vagy `divisionId` attribútum van leképezve |
| 15 | FOJobCode                              | employmentNav/jobInfoNav/jobCodeNav  | Csak akkor, ha `jobCode` vagy `jobCodeId` attribútum van leképezve |
| 16 | FOPayGrade                             | employmentNav/jobInfoNav/payGradeNav  | Csak akkor `payGrade` , ha az attribútum le van képezve |
| 17 | FOLocation                             | employmentNav/jobInfoNav/locationNav  | Csak akkor `location` , ha az attribútum le van képezve |
| 18 | FOCorporateAddressDEFLT                | employmentNav/jobInfoNav/addressNavDEFLT  | Ha a leképezés a következő attribútumok egyikét tartalmazza: `officeLocationAddress,  officeLocationCity, officeLocationZipCode` |
| 19 | FOEventReason                          | employmentNav/jobInfoNav/eventReasonNav  | Csak akkor `eventReason` , ha az attribútum le van képezve |
| 20 | EmpGlobalAssignment                    | employmentNav/empGlobalAssignmentNav | Csak akkor `assignmentType` , ha le van képezve |
| 21 | EmploymentType-lista                | employmentNav/jobInfoNav/employmentTypeNav | Csak akkor `employmentType` , ha le van képezve |
| 22 | EmployeeClass-lista                 | employmentNav/jobInfoNav/employeeClassNav | Csak akkor `employeeClass` , ha le van képezve |
| 23 | EmplStatus-lista                    | employmentNav/jobInfoNav/emplStatusNav | Csak akkor `emplStatus` , ha le van képezve |
| 24 | AssignmentType-lista                | employmentNav/empGlobalAssignmentNav/assignmentTypeNav | Csak akkor `assignmentType` , ha le van képezve |

## <a name="how-full-sync-works"></a>A teljes szinkronizálás működése
Az attribútum-leképezés alapján a teljes szinkronizálás során az Azure AD-létesítési szolgáltatás az alábbi "GET" OData API-lekérdezést küldi le az összes aktív felhasználó tényleges adatának beolvasásához. 

> [!div class="mx-tdCol2BreakAll"]
>| Paraméter | Leírás |
>| ----------|-------------|
>| OData API-gazda | Https hozzáfűzése a *bérlői URL-címhez*. Például: `https://api4.successfactors.com` |
>| OData API-végpont | `/odata/v2/PerPerson` |
>| OData $format lekérdezési paraméter | `json` |
>| OData $filter lekérdezési paraméter | `(personEmpTerminationInfoNav/activeEmploymentsCount ge 1) and (lastModifiedDateTime le <CurrentExecutionTime>)` |
>| OData $expand lekérdezési paraméter | A paraméter értéke a leképezett attribútumoktól függ. Például: `employmentNav/userNav,employmentNav/jobInfoNav,personalInfoNav,personEmpTerminationInfoNav,phoneNav,emailNav,employmentNav/jobInfoNav/companyNav/countryOfRegistrationNav,employmentNav/jobInfoNav/divisionNav,employmentNav/jobInfoNav/departmentNav` |
>| OData customPageSize lekérdezési paramétere | `100` |

> [!NOTE]
> Az első teljes szinkronizálás során az Azure AD kiépítési szolgáltatása nem kéri le az inaktív/megszakított feldolgozói adataikat.

Az egyes SuccessFactors-felhasználók esetében a kiépítési szolgáltatás a leképezésben definiált egyező attribútum használatával keres egy fiókot a célhelyen (Azure AD/helyszíni Active Directory). Például: Ha a *personIdExternal* az *AlkalmazottKód* értékre van beállítva, és az egyező attribútumként van megadva, akkor a kiépítési szolgáltatás a *personIdExternal* értéket használja az *AlkalmazottKód* -szűrővel rendelkező felhasználó kereséséhez. Ha egy felhasználói egyezés található, akkor frissíti a célként megadott attribútumokat. Ha nem talál egyezést, egy új bejegyzést hoz létre a célhelyen. 

Ha ellenőrizni szeretné, hogy a OData API-végpontja mely adatait adja vissza egy adott `personIdExternal` frissítéshez, frissítse az `SuccessFactorsAPIEndpoint` alábbi API-LEKÉRDEZÉST az API-adatközpont-kiszolgáló URL-címével, és használjon olyan eszközt, mint a [Poster](https://www.postman.com/downloads/) a lekérdezés meghívásához. 

```
https://[SuccessFactorsAPIEndpoint]/odata/v2/PerPerson?$format=json&
$filter=(personIdExternal in '[personIdExternalValue]')&
$expand=employmentNav/userNav,employmentNav/jobInfoNav,personalInfoNav,personEmpTerminationInfoNav,
phoneNav,phoneNav/phoneTypeNav,emailNav,employmentNav/jobInfoNav/businessUnitNav,employmentNav/jobInfoNav/companyNav,
employmentNav/jobInfoNav/companyNav/countryOfRegistrationNav,employmentNav/jobInfoNav/costCenterNav,
employmentNav/jobInfoNav/divisionNav,employmentNav/jobInfoNav/departmentNav,employmentNav/jobInfoNav/jobCodeNav,
employmentNav/jobInfoNav/locationNav,employmentNav/jobInfoNav/locationNav/addressNavDEFLT,employmentNav/jobInfoNav/payGradeNav,
employmentNav/empGlobalAssignmentNav,employmentNav/empGlobalAssignmentNav/assignmentTypeNav,employmentNav/jobInfoNav/emplStatusNav,
employmentNav/jobInfoNav/employmentTypeNav,employmentNav/jobInfoNav/employeeClassNav,employmentNav/jobInfoNav/eventReasonNav
```

## <a name="how-incremental-sync-works"></a>A növekményes szinkronizálás működése

A teljes szinkronizálás után az Azure AD kiépítési szolgáltatása fenntartja `LastExecutionTimestamp` és felhasználja a növekményes módosítások beolvasásához szükséges különbözeti lekérdezések létrehozását. Az egyes SuccessFactors entitásokban (például,,, és) lévő timestamp attribútumokat a `lastModifiedDateTime` `startDate` `endDate` `latestTerminationDate` rendszer kiértékeli, hogy látható-e a változás a és a között `LastExecutionTimestamp` `CurrentExecutionTime` . Ha igen, akkor a bejegyzés változását a rendszer hatékonynak tekinti és dolgozza fel szinkronizálásra. 

## <a name="reading-attribute-data"></a>Attribútum-információk olvasása

Amikor az Azure AD-létesítési szolgáltatás lekérdezi a SuccessFactors, egy JSON-eredményhalmazt kér le. A JSON-eredményhalmaz számos attribútumot tartalmaz, amelyeket az Employee Central tárol. Alapértelmezés szerint a kiépítési séma úgy van konfigurálva, hogy csak az attribútumok egy részhalmazát kérje le. 

További attribútumok lekéréséhez kövesse az alábbi lépéseket:
    
1. Keresse meg a **vállalati alkalmazások**  ->  **SuccessFactors az alkalmazás**  ->    ->  -hozzárendelés **szerkesztése kiépítési**  ->  **attribútum leképezése lapot**.
1. Görgessen le, és kattintson a **Speciális beállítások megjelenítése** elemre.
1. Kattintson az **attribútumok szerkesztése SuccessFactors** elemre. 

   > [!NOTE] 
   > Ha az **attribútumok szerkesztése SuccessFactors** beállítás nem jelenik meg a Azure Portalban, használja az URL-címet az *https://portal.azure.com/?Microsoft_AAD_IAM_forceSchemaEditorEnabled=true* oldal eléréséhez. 

1. A nézet **API-kifejezés** oszlopa az összekötő által használt JSONPath-kifejezéseket jeleníti meg.

   >[!div class="mx-imgBorder"] 
   >![API-kifejezés](media/sap-successfactors-integration-reference/jsonpath-api-expressions.png#lightbox)  

1. Szerkesztheti a meglévő JSONPath értékét, vagy hozzáadhat egy új attribútumot érvényes JSONPath-kifejezéssel a sémához. 

A következő szakasz felsorolja a JSONPath-értékek szerkesztésének gyakori forgatókönyveit. 

## <a name="handling-different-hr-scenarios"></a>Különböző HR-forgatókönyvek feldolgozása

A JSONPath a JSON lekérdezési nyelve, amely az XML XPath-hoz hasonló. Az XPath-hoz hasonlóan a JSONPath lehetővé teszi az adatok JSON-adattartalomból való kinyerését és szűrését.

A JSONPath-átalakítás használatával testre szabhatja az Azure AD-kiépítési alkalmazás viselkedését egyéni attribútumok lekérésére és olyan forgatókönyvek kezelésére, mint például a Felhelyezés, a feldolgozó átalakítása és a globális hozzárendelés. 

Ez a szakasz ismerteti, hogyan szabhatja testre a kiépítési alkalmazást a következő HR-forgatókönyvekhez: 
* [További attribútumok beolvasása](#retrieving-additional-attributes)
* [Egyéni attribútumok beolvasása](#retrieving-custom-attributes)
* [A Worker átalakítási forgatókönyvének feldolgozása](#handling-worker-conversion-scenario)
* [Az újrafelvételi forgatókönyvek feldolgozása](#handling-rehire-scenario)
* [Globális hozzárendelési forgatókönyvek kezelésére](#handling-global-assignment-scenario)
* [Egyidejű feladatok kezelésére szolgáló forgatókönyv](#handling-concurrent-jobs-scenario)

### <a name="retrieving-additional-attributes"></a>További attribútumok beolvasása

Az Azure AD SuccessFactors alapértelmezett kiépítési alkalmazási sémája [90 + előre definiált attribútumokkal](sap-successfactors-attribute-reference.md)rendelkezik. Ha további SuccessFactors-attribútumokat szeretne hozzáadni a létesítési sémához, kövesse az alábbi lépéseket: 

1. Az alábbi OData-lekérdezéssel kérhet le egy érvényes tesztelési felhasználó adatait az Employee Central szolgáltatásból. 

   ```
    https://[SuccessFactorsAPIEndpoint]/odata/v2/PerPerson?$format=json&
    $filter=(personIdExternal in '[personIdExternalValue]')&
    $expand=employmentNav/userNav,employmentNav/jobInfoNav,personalInfoNav,personEmpTerminationInfoNav,
    phoneNav,phoneNav/phoneTypeNav,emailNav,employmentNav/jobInfoNav/businessUnitNav,employmentNav/jobInfoNav/companyNav,
    employmentNav/jobInfoNav/companyNav/countryOfRegistrationNav,employmentNav/jobInfoNav/costCenterNav,
    employmentNav/jobInfoNav/divisionNav,employmentNav/jobInfoNav/departmentNav,employmentNav/jobInfoNav/jobCodeNav,
    employmentNav/jobInfoNav/locationNav,employmentNav/jobInfoNav/locationNav/addressNavDEFLT,employmentNav/jobInfoNav/payGradeNav,
    employmentNav/empGlobalAssignmentNav,employmentNav/empGlobalAssignmentNav/assignmentTypeNav,employmentNav/jobInfoNav/emplStatusNav,
    employmentNav/jobInfoNav/employmentTypeNav,employmentNav/jobInfoNav/employeeClassNav,employmentNav/jobInfoNav/eventReasonNav
   ```

1. Az attribútumhoz rendelt alkalmazott központi entitás meghatározása
   * Ha az attribútum a *EmpEmployment* entitás részét képezi, keresse meg az attribútumot az *employmentNav* csomópont alatt. 
   * Ha az attribútum a *felhasználói* entitás része, keresse meg az attribútumot a *employmentNav/userNav* csomópont alatt.
   * Ha az attribútum a *EmpJob* entitás részét képezi, keresse meg az attribútumot a *employmentNav/jobInfoNav* csomópont alatt. 
1. Hozza létre az attribútumhoz társított JSON-útvonalat, és adja hozzá ezt az új attribútumot a SuccessFactors-attribútumok listájához. 
   * 1. példa: tegyük fel, hogy hozzá kívánja adni a *okToRehire* attribútumot, amely a *employmentNav* entitás részét képezi, majd használja a JSONPath  `$.employmentNav.results[0].okToRehire`
   * 2. példa: tegyük fel, hogy hozzá szeretné adni az *userNav* entitás részét képező *időzóna-időzónát*, majd használja a JSONPath`$.employmentNav.results[0].userNav.timeZone`
   * 3. példa: tegyük fel, hogy hozzá kívánja adni a *flsaStatus* attribútumot, amely a *jobInfoNav* entitás részét képezi, majd használja a JSONPath `$.employmentNav.results[0].jobInfoNav.results[0].flsaStatus`
1. Mentse a sémát. 
1. Indítsa újra az üzembe helyezést.

### <a name="retrieving-custom-attributes"></a>Egyéni attribútumok beolvasása

Alapértelmezés szerint a következő egyéni attribútumok előre definiálva vannak az Azure AD SuccessFactors üzembe helyezési alkalmazásban: 
* *custom01 – custom15* a felhasználó (userNav) entitásból
* *customString1 – customString15* a EmpEmployment (employmentNav) nevű entitásból, amelynek neve *empNavCustomString1-empNavCustomString15*
* *customString1 – customString15* a EmpJobInfo (jobInfoNav) nevű entitásból, amelynek neve *empJobNavCustomString1-empNavJobCustomString15*

Tegyük fel, hogy az alkalmazott központi példányában a *EmpJobInfo* *customString35* attribútuma tárolja a hely leírását. Ezt az értéket át szeretné állítani Active Directory *physicalDeliveryOfficeName* attribútumra. Az attribútum hozzárendelésének konfigurálásához használja az alábbi lépéseket: 

1. Szerkessze a SuccessFactors attribútum listáját, és vegyen fel egy *empJobNavCustomString35* nevű új attribútumot.
1. Állítsa be a JSONPath API-kifejezést ehhez az attribútumhoz a következőként: `$.employmentNav.results[0].jobInfoNav.results[0].customString35`
1. Mentse és töltse be újra a megfeleltetés változását a Azure Portalban.  
1. Az attribútum-hozzárendelés panelen a Térkép *EmpJobNavCustomString35* *physicalDeliveryOfficeName*.
1. Mentse a leképezést.

A forgatókönyv kiterjesztése: 
* Ha a *custom35* attribútumot a *felhasználói* entitásból szeretné leképezni, akkor használja a JSONPath `$.employmentNav.results[0].userNav.custom35`
* Ha a *customString35* attribútumot a *EmpEmployment* entitásból szeretné leképezni, akkor használja a JSONPath `$.employmentNav.results[0].customString35`

### <a name="handling-worker-conversion-scenario"></a>A Worker átalakítási forgatókönyvének feldolgozása

A munkavégzők átalakítása a meglévő teljes körű alkalmazott átalakítása egy alvállalkozó vagy egy vállalkozó számára teljes munkaidőben. Ebben a forgatókönyvben az Employee Central új *EmpEmployment* entitást hoz létre, és egy új *felhasználói* entitást hoz létre ugyanahhoz a *személy* entitáshoz. Az előző *EmpEmployment* entitásban beágyazott *felhasználói* entitás NULL értékűre van állítva. Ha úgy szeretné kezelni ezt a forgatókönyvet, hogy az új munkafolyamati adatai megjelenjenek az átalakítás során, az alábbi lépésekkel tömegesen frissítheti a kiépítési alkalmazás sémáját:  

1. Nyissa meg az SuccessFactors üzembe helyezési alkalmazásának attribútum-leképezési paneljét. 
1. Görgessen le, és kattintson a **Speciális beállítások megjelenítése** elemre.
1. Az itt található hivatkozásra kattintva **megtekintheti** a séma-szerkesztőt. 

   >![A képernyőfelvételen a séma-szerkesztőt megnyitó hivatkozás látható.](media/sap-successfactors-integration-reference/review-schema.png#lightbox)

1. A **Letöltés** hivatkozásra kattintva mentheti a séma másolatát a Szerkesztés előtt. 

   >![Képernyőfelvétel: a letöltési lehetőséget tartalmazó séma-szerkesztő, amely a séma egy példányának mentésére szolgál.](media/sap-successfactors-integration-reference/download-schema.png#lightbox)
1. A sémakezelő szerkesztőben nyomja le a CTRL-H billentyűt a Find-replace vezérlő megnyitásához.
1. A keresés szövegmezőbe másolja és illessze be az értéket. `$.employmentNav.results[0]`
1. A csere szövegmezőbe másolja és illessze be az értéket `$.employmentNav.results[?(@.userNav != null)]` . Jegyezze fel az `!=` operátort körülvevő szóközt, ami fontos a JSONPath kifejezés sikeres feldolgozásához. 
   >![Keresés – csere – konverzió](media/sap-successfactors-integration-reference/find-replace-conversion-scenario.png#lightbox)
1. Kattintson az "összes cseréje" lehetőségre a séma frissítéséhez. 
1. Mentse a sémát. 
1. A fenti folyamat az alábbi módon frissíti az összes JSONPath-kifejezést: 
   * Régi JSONPath: `$.employmentNav.results[0].jobInfoNav.results[0].departmentNav.name_localized`
   * Új JSONPath: `$.employmentNav.results[?(@.userNav != null)].jobInfoNav.results[0].departmentNav.name_localized`
1. Indítsa újra az üzembe helyezést. 

### <a name="handling-rehire-scenario"></a>Az újrafelvételi forgatókönyvek feldolgozása

Az újrafelvételek feldolgozására általában két lehetőség áll rendelkezésre: 
* 1. lehetőség: új személy profil létrehozása az Employee Central-ban
* 2. lehetőség: a meglévő személy profiljának újbóli felhasználása az Employee Central-ban

Ha a HR-folyamat az 1. lehetőséget használja, akkor a létesítési sémához nem szükséges módosítás. Ha a HR-folyamat 2. lehetőséget használ, akkor az Employee Central új *EmpEmployment* entitást hoz létre, és egy új *felhasználói* entitást hoz létre ugyanahhoz a *személy* entitáshoz. Az átalakítási forgatókönyvtől eltérően az előző *EmpEmployment* entitásban lévő *felhasználói* entitás nem null értékre van állítva. 

A rehires forgatókönyv (2. lehetőség) kezeléséhez, hogy a legújabb foglalkoztatási adatai megjelenjenek a felhasználható profilok esetében, az alábbi lépésekkel tömegesen frissítheti a kiépítési alkalmazás sémáját:  

1. Nyissa meg az SuccessFactors üzembe helyezési alkalmazásának attribútum-leképezési paneljét. 
1. Görgessen le, és kattintson a **Speciális beállítások megjelenítése** elemre.
1. Az itt található hivatkozásra kattintva **megtekintheti** a séma-szerkesztőt.   
1. A **Letöltés** hivatkozásra kattintva mentheti a séma másolatát a Szerkesztés előtt.   
1. A sémakezelő szerkesztőben nyomja le a CTRL-H billentyűt a Find-replace vezérlő megnyitásához.
1. A keresés szövegmezőbe másolja és illessze be az értéket. `$.employmentNav.results[0]`
1. A csere szövegmezőbe másolja és illessze be az értéket `$.employmentNav.results[-1:]` . Ez a JSONPath-kifejezés a legújabb *EmpEmployment* -rekordot adja vissza.   
1. Kattintson az "összes cseréje" lehetőségre a séma frissítéséhez. 
1. Mentse a sémát. 
1. A fenti folyamat az alábbi módon frissíti az összes JSONPath-kifejezést: 
   * Régi JSONPath: `$.employmentNav.results[0].jobInfoNav.results[0].departmentNav.name_localized`
   * Új JSONPath: `$.employmentNav.results[-1:].jobInfoNav.results[0].departmentNav.name_localized`
1. Indítsa újra az üzembe helyezést. 

Ez a séma-módosítás a Worker átalakítási forgatókönyvet is támogatja. 

### <a name="handling-global-assignment-scenario"></a>Globális hozzárendelési forgatókönyvek kezelésére

Ha az alkalmazotti központ egy felhasználóját a globális hozzárendeléshez dolgozza fel, a SuccessFactors egy új *EmpEmployment* -entitást ad hozzá, és beállítja a *ASSIGNMENTCLASS* a "ga" értékre. Emellett új *felhasználói* entitást is létrehoz. Így a felhasználónak most a következőket kell tennie:
* Egy *EmpEmployment*  +  *felhasználói* entitás, amely a Kezdőlap hozzárendelésnek felel meg, a *assignmentClass* beállítás értéke "St" és 
* Egy másik *EmpEmployment*  +  *felhasználói* entitás, amely megfelel a globális hozzárendelésnek a "ga" értékre beállított *assignmentClass*

A standard hozzárendelés és a globális hozzárendelés felhasználói profilhoz tartozó attribútumok beolvasásához kövesse az alábbi lépéseket: 

1. Nyissa meg az SuccessFactors üzembe helyezési alkalmazásának attribútum-leképezési paneljét. 
1. Görgessen le, és kattintson a **Speciális beállítások megjelenítése** elemre.
1. Az itt található hivatkozásra kattintva **megtekintheti** a séma-szerkesztőt.   
1. A **Letöltés** hivatkozásra kattintva mentheti a séma másolatát a Szerkesztés előtt.   
1. A sémakezelő szerkesztőben nyomja le a CTRL-H billentyűt a Find-replace vezérlő megnyitásához.
1. A keresés szövegmezőbe másolja és illessze be az értéket. `$.employmentNav.results[0]`
1. A csere szövegmezőbe másolja és illessze be az értéket `$.employmentNav.results[?(@.assignmentClass == 'ST')]` . 
1. Kattintson az "összes cseréje" lehetőségre a séma frissítéséhez. 
1. Mentse a sémát. 
1. A fenti folyamat az alábbi módon frissíti az összes JSONPath-kifejezést: 
   * Régi JSONPath: `$.employmentNav.results[0].jobInfoNav.results[0].departmentNav.name_localized`
   * Új JSONPath: `$.employmentNav.results[?(@.assignmentClass == 'ST')].jobInfoNav.results[0].departmentNav.name_localized`
1. Töltse be újra az alkalmazás attribútum-hozzárendelési paneljét. 
1. Görgessen le, és kattintson a **Speciális beállítások megjelenítése** elemre.
1. Kattintson az **attribútumok szerkesztése SuccessFactors** elemre.
1. Adja hozzá az új attribútumokat a globális hozzárendelési adat beolvasásához. Például: Ha egy globális hozzárendelési profillal társított részleg nevét szeretné beolvasni, adja hozzá a *globalAssignmentDepartment* attribútumot a JSONPath kifejezéshez `$.employmentNav.results[?(@.assignmentClass == 'GA')].jobInfoNav.results[0].departmentNav.name_localized` . 
1. Most már két részleg értékeit is elvégezheti Active Directory attribútumokra, vagy szelektíven átadhat egy értéket a kifejezés-hozzárendelés használatával. Példa: az alábbi kifejezés azt állítja be, hogy az AD *Department* attribútum értéke *globalAssignmentDepartment* , ha van, akkor az értéket a normál hozzárendeléshez társított *részlegre* állítja be. 
   * `IIF(IsPresent([globalAssignmentDepartment]),[globalAssignmentDepartment],[department])`

1. Mentse a leképezést. 
1. Indítsa újra az üzembe helyezést. 

### <a name="handling-concurrent-jobs-scenario"></a>Egyidejű feladatok kezelésére szolgáló forgatókönyv

Ha az alkalmazotti központ egyik felhasználója egyidejűleg/több feladatot is tartalmaz, két *EmpEmployment* és *felhasználói* entitás létezik, és a *assignmentClass* beállítása "St". A mindkét feladathoz tartozó attribútumok lekéréséhez kövesse az alábbi lépéseket: 

1. Nyissa meg az SuccessFactors üzembe helyezési alkalmazásának attribútum-leképezési paneljét. 
1. Görgessen le, és kattintson a **Speciális beállítások megjelenítése** elemre.
1. Kattintson az **attribútumok szerkesztése SuccessFactors** elemre.
1. Tegyük fel, hogy az 1. feladatokhoz és a 2. feladatokhoz tartozó részleget szeretné lekérni. Az előre definiált attribútum *részleg* már beolvassa az első feladatokhoz tartozó részleg értékét. Megadhat egy *secondJobDepartment* nevű új attribútumot, és a JSONPath kifejezést a következőre állíthatja `$.employmentNav.results[1].jobInfoNav.results[0].departmentNav.name_localized`
1. Most már két részleg értékeit is elvégezheti Active Directory attribútumokra, vagy szelektíven átadhat egy értéket a kifejezés-hozzárendelés használatával. 
1. Mentse a leképezést. 
1. Indítsa újra az üzembe helyezést. 

## <a name="writeback-scenarios"></a>Visszaírási-forgatókönyvek

Ez a szakasz a különböző írási forgatókönyveket ismerteti. Azt javasolja, hogy az e-mail-cím és a telefonszám beállítása a SuccessFactors-ben milyen konfigurációs módszereket javasol.

### <a name="supported-scenarios-for-phone-and-email-write-back"></a>A telefonos és e-mail-írási műveletek támogatott forgatókönyvei 

| \# | Forgatókönyvre vonatkozó követelmény | Elsődleges e-mail <br> jelölő értéke | Céges telefon <br> elsődleges jelző értéke | Mobiltelefon <br> elsődleges jelző értéke | Céges telefon <br> leképezés | Mobiltelefon <br> leképezés |
|--|--|--|--|--|--|--|
| 1 | * Csak a vállalati e-mailek beállítása elsődlegesként. <br> * Ne állítson be telefonszámokat. | true | true | hamis | \[Nincs beállítva\] | \[Nincs beállítva\] | 
| 2 | * A SuccessFactors az üzleti levelezés és a vállalati telefon elsődleges <br> * Az Azure AD-telefonszámot mindig az üzleti telefonra és a mobil mobiltelefonra kell átvenni. | true | true | hamis | telephoneNumber | mobil | 
| 3 | * A SuccessFactors, az üzleti levelezés és a mobil telefon elsődleges <br> * Az Azure AD telefonszámának folyamatos átvitele a telefonra és a mobilról a mobiltelefonra | true | hamis | true |  telephoneNumber | mobil | 
| 4 | * A SuccessFactors Business e-mail-címe elsődleges <br> * Az Azure AD-ben ellenőrizze, hogy megtalálható-e a munkahelyi telefonszám, ha van ilyen, majd ellenőrizze, hogy a Mobile Number is jelen van-e, jelölje meg a munkahelyi telefonszámot elsődlegesként, ha a Mobiltelefonszám nem található | true | Kifejezés-hozzárendelés használata: `IIF(IsPresent([telephoneNumber]), IIF(IsPresent([mobile]),"false", "true"), "false")` | Kifejezés-hozzárendelés használata: `IIF(IsPresent([mobile]),"false", "true")` | telephoneNumber | mobil | 
| 5 | * A SuccessFactors Business e-mail-címe és a vállalati telefon elsődleges. <br> * Az Azure AD-ben, ha a Mobile elérhető, állítsa be vállalati telefonként, máskülönben használja az telephoneNumber-t. | true | true | hamis | `IIF(IsPresent([mobile]), [mobile], [telephoneNumber])` | \[Nincs beállítva\] | 

* Ha nincs leképezés a telefonszámra a write-back attribútumban – leképezés, akkor a rendszer csak az e-maileket tartalmazza a visszaíráshoz.
* Előfordulhat, hogy az alkalmazottak központi, üzleti e-mail-címe és telefonszáma nem érhető el az új bérlet bevezetéséhez. Ha a vállalati e-mailek és a vállalati telefon elsődlegesként való megadása kötelező a bevezetéskor, a vállalati telefonokra és az e-mailekre vonatkozó próbabábu-értéket megadhatja az új bérlet létrehozásakor, amelyet végül a write-back alkalmazás fog frissíteni.
 
### <a name="unsupported-scenarios-for-phone-and-email-write-back"></a>Nem támogatott helyzetek a telefonos és e-mailek visszaírásához

* Az Employee Central szolgáltatásban a személyes e-mailek és a személyes telefon bevezetése elsődlegesként van beállítva. A visszaírási alkalmazás nem tudja beállítani ezt a beállítást, és nem állíthatja be elsődlegesként az üzleti levelezést és a vállalati telefont.
* Az alkalmazottak középső régiójában a vállalati telefon elsődlegesként van beállítva. A write-back alkalmazás nem változtathatja meg ezt, és nem állíthatja be elsődlegesként a mobiltelefont.
* A visszaírási alkalmazás nem tudja olvasni az elsődleges jelző aktuális beállításait, és ugyanazokat az értékeket használja az írási művelethez. Az attribútumban konfigurált jelző értékek mindig használatban lesznek. 

## <a name="next-steps"></a>Következő lépések

* [Megtudhatja, hogyan konfigurálhatja a SuccessFactors Active Directory kiépítés céljából](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
* [Ismerje meg, hogyan konfigurálhatja a visszaírási a SuccessFactors-be](../saas-apps/sap-successfactors-writeback-tutorial.md)
* [További információ a bejövő kiépítés támogatott SuccessFactors attribútumairól](sap-successfactors-attribute-reference.md)










