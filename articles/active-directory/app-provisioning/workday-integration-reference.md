---
title: Azure Active Directory és a munkanap integrációs referenciája
description: Technikai részletes bemutató munkanapokon – HR-alapú kiépítés
services: active-directory
author: cmmdesai
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 01/18/2021
ms.author: chmutali
ms.openlocfilehash: 38a93d5db6f8331da6e7afdef3e91b2764537459
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954021"
---
# <a name="how-azure-active-directory-provisioning-integrates-with-workday"></a>A Azure Active Directory kiépítés munkanapokkal való integrálása

[Azure Active Directory a felhasználó kiépítési szolgáltatása](../app-provisioning/user-provisioning.md) a [munkanap HCM](https://www.workday.com) -vel integrálódik a felhasználók identitási életciklusának kezelésére. A Azure Active Directory három előre elkészített integrációt kínál: 

* [A helyszíni Active Directory a felhasználók üzembe helyezése](../saas-apps/workday-inbound-tutorial.md)
* [A felhasználók üzembe helyezésének Azure Active Directory](../saas-apps/workday-inbound-cloud-only-tutorial.md)
* [Workday Writeback](../saas-apps/workday-writeback-tutorial.md)

Ez a cikk bemutatja, hogyan működik az integráció, és hogyan szabhatja testre a különböző HR-forgatókönyvek kiépítési viselkedését. 

## <a name="establishing-connectivity"></a>Kapcsolat létesítése 

### <a name="restricting-workday-api-access-to-azure-ad-endpoints"></a>Az Azure AD-végpontokhoz való munkanap API-hozzáférés korlátozása
Az Azure AD kiépítési szolgáltatás alapszintű hitelesítést használ a munkanapokat használó webszolgáltatások API-végpontjaihoz való kapcsolódáshoz.  

Az Azure AD kiépítési szolgáltatás és a munkanap közötti kapcsolat további biztonságossá tételéhez korlátozhatja a hozzáférést, így a kijelölt integrációs rendszer felhasználója csak a munkanapokhoz tartozó API-kat fér hozzá az engedélyezett Azure AD IP-tartományokból. Kérjük, vegye fel a munkahelye rendszergazdájával a következő konfigurációt a munkanap bérlőn. 

1. Töltse le az Azure nyilvános felhő [legújabb IP-tartományait](https://www.microsoft.com/download/details.aspx?id=56519) . 
1. Nyissa meg a fájlt, és keresse meg a **AzureActiveDirectory** címke kifejezést. 

   >[!div class="mx-imgBorder"] 
   >![Azure AD IP-címtartomány](media/sap-successfactors-integration-reference/azure-active-directory-ip-range.png)

1. Másolja az elem *addressPrefixes* belül felsorolt összes IP-címtartományt, és a tartomány használatával hozza létre az IP-címlista listáját.
1. Jelentkezzen be a munkanap-felügyeleti portálra. 
1. Az **IP-címtartományok fenntartása** feladat elérése új IP-címtartomány létrehozásához az Azure-adatközpontok számára. Vesszővel tagolt listaként határozza meg az IP-tartományokat (CIDR-jelölés használatával).  
1. Az új hitelesítési házirend létrehozásához nyissa meg a **hitelesítési házirendek kezelése** feladatot. A hitelesítési házirendben a hitelesítési engedélyezési lista segítségével megadhatja az Azure AD IP-tartományát, valamint azt a biztonsági csoportot, amely az adott IP-tartományból elérhető lesz. Mentse a módosításokat. 
1. A módosítások megerősítéséhez nyissa meg az **összes függőben lévő hitelesítési házirend módosítása feladat aktiválása** feladatot.

### <a name="limiting-access-to-worker-data-in-workday-using-constrained-security-groups"></a>Munkavégző adataihoz való hozzáférés korlátozása munkanapokon korlátozott biztonsági csoportok használatával

A [munkanap-integrációs rendszer felhasználójának](../saas-apps/workday-inbound-tutorial.md#configure-integration-system-user-in-workday) alapértelmezett lépései a hozzáférést biztosítanak a munkahelyen lévő összes felhasználó lekéréséhez. Bizonyos integrációs helyzetekben érdemes korlátozni a hozzáférést, hogy a csak bizonyos felügyeleti szervezetekhez tartozó felhasználókat a Get_Workers API-hívás adja vissza, amelyet a munkanap Azure AD-összekötő dolgoz fel. 

Ezt a követelményt a munkanap rendszergazdája és a korlátozott integrációs rendszerek biztonsági csoportjai konfigurálásával is teljesítheti. Ha további információra van szüksége ennek elvégzéséről, tekintse meg [ezt a munkanapon belüli közösségi cikket](https://community.workday.com/forums/customer-questions/620393) (a *munkanapokra vonatkozó közösségi bejelentkezési hitelesítő adatok szükségesek a cikk eléréséhez*)

A korlátozott ISSG (integrációs rendszer biztonsági csoportok) használatával történő hozzáférés korlátozására szolgáló stratégia különösen a következő esetekben hasznos: 
* **Többfázisú** bevezetési forgatókönyv: nagy munkanapokat tartalmazó Bérlővel rendelkezik, és megtervezi, hogy az Azure ad automatikus kiépítésének időszakos bevezetését elvégezze. Ebben a forgatókönyvben, nem pedig azon felhasználók kizárása, akik nem az aktuális fázis hatókörében vannak az Azure AD-hatóköri szűrőkkel, javasoljuk, hogy a korlátozott ISSG konfigurálja úgy, hogy csak a hatókörrel rendelkező munkatársak láthassák az Azure AD-t.
* **Több üzembe helyezési feladat forgatókönyve**: nagy munkanapokat tartalmazó Bérlővel és több ad-tartománnyal rendelkezik, amelyek mindegyike különböző üzleti egységeket/részlegeket vagy vállalatokat támogat. Ennek a topológiának a támogatásához több munkafolyamatot szeretne futtatni az Azure AD-kiépítési feladatokhoz, és minden egyes feladathoz egy adott munkacsoportot kell kiépíteni. Ebben a forgatókönyvben, ahelyett, hogy az Azure AD-hatóköri szűrők használatával kizárják a munkavégző adatokat, javasoljuk, hogy a korlátozott ISSG konfigurálja úgy, hogy csak a megfelelő munkavégző adatokat láthassa az Azure AD. 

### <a name="workday-test-connection-query"></a>Munkaidő-tesztelési kapcsolatok lekérdezése

A munkanapokhoz való csatlakozás teszteléséhez az Azure AD a következő *Get_Workers* munkanapokat tartalmazó webszolgáltatások kérelmét küldi el. 

```XML
<!-- Test connection query tries to retrieve one record from the first page -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below with the UTC time corresponding to the test connection event -->
<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_Criteria>
    <p1:Transaction_Log_Criteria_Data>
      <p1:Transaction_Date_Range_Data>
        <p1:Updated_From>2021-01-19T02:28:50.1491022Z</p1:Updated_From>
        <p1:Updated_Through>2021-01-19T02:28:50.1491022Z</p1:Updated_Through>
      </p1:Transaction_Date_Range_Data>
    </p1:Transaction_Log_Criteria_Data>
    <p1:Exclude_Employees>true</p1:Exclude_Employees>
    <p1:Exclude_Contingent_Workers>true</p1:Exclude_Contingent_Workers>
    <p1:Exclude_Inactive_Workers>true</p1:Exclude_Inactive_Workers>
  </p1:Request_Criteria>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:28:50.1491022Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:28:50.1491022Z</p1:As_Of_Entry_DateTime>
    <p1:Page>1</p1:Page>
    <p1:Count>1</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
  </p1:Response_Group>
</Get_Workers_Request>
```

## <a name="how-full-sync-works"></a>A teljes szinkronizálás működése

A munkanapokon alapuló kiépítés kontextusában a **teljes szinkronizálás** arra a folyamatra utal, amely az összes identitást beolvassa a munkahelyről, és meghatározza, hogy az egyes feldolgozói objektumokra milyen kiépítési szabályok vonatkoznak. A teljes szinkronizálás akkor történik meg, amikor az első alkalommal bekapcsolja az üzembe helyezést, valamint a Azure Portal vagy a Graph API-k használatával történő *kiépítés újraindítását* is. 

Az Azure AD a következő *Get_Workers* munkanap webszolgáltatások kérelmét küldi el a feldolgozói adatokat. A lekérdezés megkeresi a munkanap tranzakciós naplóját minden, a teljes szinkronizálási műveletnek megfelelő időponttal rendelkező feldolgozói bejegyzésnél. 

```XML
<!-- Workday full sync query -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below with the UTC time corresponding to full sync run -->
<!-- Count specifies the number of records to return in each page -->
<!-- Response_Group flags derived from provisioning attribute mapping -->

<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_Criteria>
    <p1:Transaction_Log_Criteria_Data>
      <p1:Transaction_Type_References>
        <p1:Transaction_Type_Reference>
          <p1:ID p1:type="Business_Process_Type">Hire Employee</p1:ID>
        </p1:Transaction_Type_Reference>
        <p1:Transaction_Type_Reference>
          <p1:ID p1:type="Business_Process_Type">Contract Contingent Worker</p1:ID>
        </p1:Transaction_Type_Reference>
      </p1:Transaction_Type_References>
    </p1:Transaction_Log_Criteria_Data>
  </p1:Request_Criteria>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:29:16.0094202Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:29:16.0094202Z</p1:As_Of_Entry_DateTime>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
    <p1:Include_Employment_Information>1</p1:Include_Employment_Information>
    <p1:Include_Organizations>1</p1:Include_Organizations>
    <p1:Exclude_Organization_Support_Role_Data>1</p1:Exclude_Organization_Support_Role_Data>
    <p1:Exclude_Location_Hierarchies>1</p1:Exclude_Location_Hierarchies>
    <p1:Exclude_Cost_Center_Hierarchies>1</p1:Exclude_Cost_Center_Hierarchies>
    <p1:Exclude_Company_Hierarchies>1</p1:Exclude_Company_Hierarchies>
    <p1:Exclude_Matrix_Organizations>1</p1:Exclude_Matrix_Organizations>
    <p1:Exclude_Pay_Groups>1</p1:Exclude_Pay_Groups>
    <p1:Exclude_Regions>1</p1:Exclude_Regions>
    <p1:Exclude_Region_Hierarchies>1</p1:Exclude_Region_Hierarchies>
    <p1:Exclude_Funds>1</p1:Exclude_Funds>
    <p1:Exclude_Fund_Hierarchies>1</p1:Exclude_Fund_Hierarchies>
    <p1:Exclude_Grants>1</p1:Exclude_Grants>
    <p1:Exclude_Grant_Hierarchies>1</p1:Exclude_Grant_Hierarchies>
    <p1:Exclude_Business_Units>1</p1:Exclude_Business_Units>
    <p1:Exclude_Business_Unit_Hierarchies>1</p1:Exclude_Business_Unit_Hierarchies>
    <p1:Exclude_Programs>1</p1:Exclude_Programs>
    <p1:Exclude_Program_Hierarchies>1</p1:Exclude_Program_Hierarchies>
    <p1:Exclude_Gifts>1</p1:Exclude_Gifts>
    <p1:Exclude_Gift_Hierarchies>1</p1:Exclude_Gift_Hierarchies>
    <p1:Include_Management_Chain_Data>1</p1:Include_Management_Chain_Data>
    <p1:Include_Transaction_Log_Data>1</p1:Include_Transaction_Log_Data>
    <p1:Include_Additional_Jobs>1</p1:Include_Additional_Jobs>
  </p1:Response_Group>
</Get_Workers_Request>
```
A *Response_Group* csomópont segítségével határozható meg, hogy mely feldolgozó attribútumok legyenek beolvasni a munkanapokból. Az *Response_Group* csomópontban található egyes jelölők leírását a munkanapokat [Get_Workers API dokumentációjában](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v35.2/Get_Workers.html#Worker_Response_GroupType)találja. 

A *Response_Group* csomópontban megadott egyes jelző értékeket a MUNKANAP Azure ad kiépítési alkalmazásban konfigurált attribútumok alapján számítjuk ki. A jelző értékeinek beállításához használt feltételeknél tekintse meg a *támogatott entitások* című szakaszt. 

A fenti lekérdezéshez tartozó *Get_Workers* válasz a munkavégző rekordok és a lapok számának számát tartalmazza.

```XML
  <wd:Response_Results>
    <wd:Total_Results>509</wd:Total_Results>
    <wd:Total_Pages>17</wd:Total_Pages>
    <wd:Page_Results>30</wd:Page_Results>
    <wd:Page>1</wd:Page>
  </wd:Response_Results>
```
Az eredményhalmaz következő oldalának lekéréséhez a következő *Get_Workers* lekérdezés az oldalszámot adja meg paraméterként a *Response_Filterban*.

```XML
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:29:16.0094202Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:29:16.0094202Z</p1:As_Of_Entry_DateTime>
    <p1:Page>2</p1:Page>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
```
Az Azure AD-kiépítési szolgáltatás az összes oldalt feldolgozza, és a teljes szinkronizálás során minden hatékony feldolgozón keresztül ismétlődik. A munkanapokból importált összes munkavégző bejegyzés esetében:
* Az [XPath-kifejezés](workday-attribute-reference.md) az attribútumok munkanapokból való lekérésére lett alkalmazva.
* Az attribútum-hozzárendelési és a megfeleltetési szabályok érvényesek, és 
* A szolgáltatás meghatározza, hogy milyen műveletet kell végrehajtani a célhelyen (Azure AD/AD). 

A feldolgozás befejezése után a a teljes szinkronizáláshoz tartozó időbélyeget menti a vízjelként. Ez a vízjel a növekményes szinkronizálási ciklus kiindulási pontjaként szolgál. 

## <a name="how-incremental-sync-works"></a>A növekményes szinkronizálás működése

A teljes szinkronizálást követően az Azure AD kiépítési szolgáltatás fenntartja `LastExecutionTimestamp` és felhasználja a növekményes módosítások beolvasásához szükséges különbözeti lekérdezések létrehozásához. A növekményes szinkronizálás során az Azure AD a következő típusú lekérdezéseket küldi el munkanapokra: 

* [Manuális frissítések lekérdezése](#query-for-manual-updates)
* [Lekérdezés a hatékony keltezésű frissítésekhez és a megszakításokhoz](#query-for-effective-dated-updates-and-terminations)
* [Jövőbeli bérletek lekérdezése](#query-for-future-dated-hires)

### <a name="query-for-manual-updates"></a>Manuális frissítések lekérdezése

A következő *Get_Workers* a kérelmeket a legutóbbi végrehajtás és az aktuális végrehajtási idő között történt manuális frissítésekre vonatkozóan kérdezi le. 

```xml
<!-- Workday incremental sync query for manual updates -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below with the UTC time corresponding to last execution and current execution time -->
<!-- Count specifies the number of records to return in each page -->
<!-- Response_Group flags derived from provisioning attribute mapping -->

<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_Criteria>
    <p1:Transaction_Log_Criteria_Data>
      <p1:Transaction_Date_Range_Data>
        <p1:Updated_From>2021-01-19T02:29:16.0094202Z</p1:Updated_From>
        <p1:Updated_Through>2021-01-19T02:49:06.290136Z</p1:Updated_Through>
      </p1:Transaction_Date_Range_Data>
    </p1:Transaction_Log_Criteria_Data>
  </p1:Request_Criteria>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:49:06.290136Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:49:06.290136Z</p1:As_Of_Entry_DateTime>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
    <p1:Include_Employment_Information>1</p1:Include_Employment_Information>
    <p1:Include_Organizations>1</p1:Include_Organizations>
    <p1:Exclude_Organization_Support_Role_Data>1</p1:Exclude_Organization_Support_Role_Data>
    <p1:Exclude_Location_Hierarchies>1</p1:Exclude_Location_Hierarchies>
    <p1:Exclude_Cost_Center_Hierarchies>1</p1:Exclude_Cost_Center_Hierarchies>
    <p1:Exclude_Company_Hierarchies>1</p1:Exclude_Company_Hierarchies>
    <p1:Exclude_Matrix_Organizations>1</p1:Exclude_Matrix_Organizations>
    <p1:Exclude_Pay_Groups>1</p1:Exclude_Pay_Groups>
    <p1:Exclude_Regions>1</p1:Exclude_Regions>
    <p1:Exclude_Region_Hierarchies>1</p1:Exclude_Region_Hierarchies>
    <p1:Exclude_Funds>1</p1:Exclude_Funds>
    <p1:Exclude_Fund_Hierarchies>1</p1:Exclude_Fund_Hierarchies>
    <p1:Exclude_Grants>1</p1:Exclude_Grants>
    <p1:Exclude_Grant_Hierarchies>1</p1:Exclude_Grant_Hierarchies>
    <p1:Exclude_Business_Units>1</p1:Exclude_Business_Units>
    <p1:Exclude_Business_Unit_Hierarchies>1</p1:Exclude_Business_Unit_Hierarchies>
    <p1:Exclude_Programs>1</p1:Exclude_Programs>
    <p1:Exclude_Program_Hierarchies>1</p1:Exclude_Program_Hierarchies>
    <p1:Exclude_Gifts>1</p1:Exclude_Gifts>
    <p1:Exclude_Gift_Hierarchies>1</p1:Exclude_Gift_Hierarchies>
    <p1:Include_Management_Chain_Data>1</p1:Include_Management_Chain_Data>
    <p1:Include_Additional_Jobs>1</p1:Include_Additional_Jobs>
  </p1:Response_Group>
</Get_Workers_Request>
```

### <a name="query-for-effective-dated-updates-and-terminations"></a>Lekérdezés a hatékony keltezésű frissítésekhez és a megszakításokhoz

A következő *Get_Workers* a legutóbbi végrehajtás és az aktuális végrehajtási idő között megjelenő, érvényben lévő frissítések lekérdezéseit kérdezi le. 

```xml
<!-- Workday incremental sync query for effective-dated updates -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below with the UTC time corresponding to last execution and current execution time -->
<!-- Count specifies the number of records to return in each page -->
<!-- Response_Group flags derived from provisioning attribute mapping -->

<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_Criteria>
    <p1:Transaction_Log_Criteria_Data>
      <p1:Transaction_Date_Range_Data>
        <p1:Effective_From>2021-01-19T02:29:16.0094202Z</p1:Effective_From>
        <p1:Effective_Through>2021-01-19T02:49:06.290136Z</p1:Effective_Through>
      </p1:Transaction_Date_Range_Data>
    </p1:Transaction_Log_Criteria_Data>
  </p1:Request_Criteria>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:49:06.290136Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:49:06.290136Z</p1:As_Of_Entry_DateTime>
    <p1:Page>1</p1:Page>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
    <p1:Include_Employment_Information>1</p1:Include_Employment_Information>
    <p1:Include_Organizations>1</p1:Include_Organizations>
    <p1:Exclude_Organization_Support_Role_Data>1</p1:Exclude_Organization_Support_Role_Data>
    <p1:Exclude_Location_Hierarchies>1</p1:Exclude_Location_Hierarchies>
    <p1:Exclude_Cost_Center_Hierarchies>1</p1:Exclude_Cost_Center_Hierarchies>
    <p1:Exclude_Company_Hierarchies>1</p1:Exclude_Company_Hierarchies>
    <p1:Exclude_Matrix_Organizations>1</p1:Exclude_Matrix_Organizations>
    <p1:Exclude_Pay_Groups>1</p1:Exclude_Pay_Groups>
    <p1:Exclude_Regions>1</p1:Exclude_Regions>
    <p1:Exclude_Region_Hierarchies>1</p1:Exclude_Region_Hierarchies>
    <p1:Exclude_Funds>1</p1:Exclude_Funds>
    <p1:Exclude_Fund_Hierarchies>1</p1:Exclude_Fund_Hierarchies>
    <p1:Exclude_Grants>1</p1:Exclude_Grants>
    <p1:Exclude_Grant_Hierarchies>1</p1:Exclude_Grant_Hierarchies>
    <p1:Exclude_Business_Units>1</p1:Exclude_Business_Units>
    <p1:Exclude_Business_Unit_Hierarchies>1</p1:Exclude_Business_Unit_Hierarchies>
    <p1:Exclude_Programs>1</p1:Exclude_Programs>
    <p1:Exclude_Program_Hierarchies>1</p1:Exclude_Program_Hierarchies>
    <p1:Exclude_Gifts>1</p1:Exclude_Gifts>
    <p1:Exclude_Gift_Hierarchies>1</p1:Exclude_Gift_Hierarchies>
    <p1:Include_Management_Chain_Data>1</p1:Include_Management_Chain_Data>
    <p1:Include_Additional_Jobs>1</p1:Include_Additional_Jobs>
  </p1:Response_Group>
</Get_Workers_Request>
```

### <a name="query-for-future-dated-hires"></a>Jövőbeli bérletek lekérdezése

Ha a fenti lekérdezések bármelyike egy jövőbeli bérletet ad vissza, akkor a következő *Get_Workers* kérést használjuk a jövőbeli új bérlettel kapcsolatos információk beolvasására. Az új bérlet *wid* attribútuma a keresés végrehajtásához használatos, a tényleges dátum pedig a hires dátumra és időpontra van állítva. 

```xml
<!-- Workday incremental sync query to get new hire data effective as on hire date/first day of work -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below hire date/first day of work -->
<!-- Count specifies the number of records to return in each page -->
<!-- Response_Group flags derived from provisioning attribute mapping -->

<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_References>
    <p1:Worker_Reference>
      <p1:ID p1:type="WID">7bf6322f1ea101fd0b4433077f09cb04</p1:ID>
    </p1:Worker_Reference>
  </p1:Request_References>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-02-01T08:00:00+00:00</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-02-01T08:00:00+00:00</p1:As_Of_Entry_DateTime>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
    <p1:Include_Employment_Information>1</p1:Include_Employment_Information>
    <p1:Include_Organizations>1</p1:Include_Organizations>
    <p1:Exclude_Organization_Support_Role_Data>1</p1:Exclude_Organization_Support_Role_Data>
    <p1:Exclude_Location_Hierarchies>1</p1:Exclude_Location_Hierarchies>
    <p1:Exclude_Cost_Center_Hierarchies>1</p1:Exclude_Cost_Center_Hierarchies>
    <p1:Exclude_Company_Hierarchies>1</p1:Exclude_Company_Hierarchies>
    <p1:Exclude_Matrix_Organizations>1</p1:Exclude_Matrix_Organizations>
    <p1:Exclude_Pay_Groups>1</p1:Exclude_Pay_Groups>
    <p1:Exclude_Regions>1</p1:Exclude_Regions>
    <p1:Exclude_Region_Hierarchies>1</p1:Exclude_Region_Hierarchies>
    <p1:Exclude_Funds>1</p1:Exclude_Funds>
    <p1:Exclude_Fund_Hierarchies>1</p1:Exclude_Fund_Hierarchies>
    <p1:Exclude_Grants>1</p1:Exclude_Grants>
    <p1:Exclude_Grant_Hierarchies>1</p1:Exclude_Grant_Hierarchies>
    <p1:Exclude_Business_Units>1</p1:Exclude_Business_Units>
    <p1:Exclude_Business_Unit_Hierarchies>1</p1:Exclude_Business_Unit_Hierarchies>
    <p1:Exclude_Programs>1</p1:Exclude_Programs>
    <p1:Exclude_Program_Hierarchies>1</p1:Exclude_Program_Hierarchies>
    <p1:Exclude_Gifts>1</p1:Exclude_Gifts>
    <p1:Exclude_Gift_Hierarchies>1</p1:Exclude_Gift_Hierarchies>
    <p1:Include_Management_Chain_Data>1</p1:Include_Management_Chain_Data>
    <p1:Include_Additional_Jobs>1</p1:Include_Additional_Jobs>
  </p1:Response_Group>
</Get_Workers_Request>
```

## <a name="retrieving-worker-data-attributes"></a>Feldolgozói adatok attribútumainak beolvasása

A *Get_Workers* API egy feldolgozóhoz tartozó különböző adatkészleteket adhat vissza. A kiépítési sémában konfigurált [XPath API-kifejezéstől](workday-attribute-reference.md) függően az Azure ad kiépítési szolgáltatás határozza meg, hogy a munkanapokból milyen adatkészletek legyenek lekérdezve. Ennek megfelelően a *Response_Group* jelzők a *Get_Workers* kérelemben vannak beállítva. 

Az alábbi táblázat útmutatást nyújt az adott adathalmaz lekéréséhez használandó konfiguráció hozzárendeléséhez. 

| \# | Munkanap entitás                       | Alapértelmezés szerint tartalmazza | A nem alapértelmezett entitások beolvasásához a leképezésben megadni kívánt XPATH-minta             |
|----|--------------------------------------|---------------------|-------------------------------------------------------------------------------|
| 1  | Személyes adattárolás                        | Yes                 | WD: Worker \_ -adatfeldolgozó/WD: személyes \_                                             |
| 2  | Foglalkoztatási adatszolgáltatások                      | Yes                 | WD: Worker \_ -adatfeldolgozási/WD: foglalkoztatási \_ adatszolgáltatások                                           |
| 3  | További feladatok adatai                  | Yes                 | WD: Worker \_ -adatok/WD: \_ Working-adatok/WD: Worker \_ Job \_ adatfeldolgozási feladatok \[ @wd:Primary \_ = 0\]|
| 4  | Szervezeti adatszolgáltatások                    | Yes                 | WD: Worker \_ -adattárolás/WD: szervezeti \_ adatszolgáltatások                                         |
| 5  | Felügyeleti lánc adatvédelme                | Yes                 | WD: Worker \_ -adatkezelési/WD: felügyeleti \_ lánc \_ adatvédelme                                    |
| 6  | Felügyeleti szervezet             | Yes                 | FELÜGYELETI                                                                 |
| 7  | Vállalat                              | Yes                 | VÁLLALATI                                                                     |
| 8  | Business Unit (Vállalati egység)                        | No                  | "üzleti \_ egység"                                                              |
| 9  | Üzleti egység hierarchiája              | No                  | "üzleti \_ egység \_ hierarchiája"                                                   |
| 10 | Vállalati hierarchia                    | No                  | "vállalati \_ hierarchia"                                                          |
| 11 | Cost Center                          | Nem                  | "COST \_ Center"                                                                |
| 12 | Cost Center-hierarchia                | No                  | "COST \_ Center- \_ hierarchia"                                                     |
| 13 | Alaptőke                                 | No                  | ALAPTŐKE                                                                        |
| 14 | Alap-hierarchia                       | No                  | "alap- \_ hierarchia"                                                             |
| 15 | Ajándékozási                                 | No                  | AJÁNDÉKOZÁSI                                                                        |
| 16 | Gift-hierarchia                       | No                  | "GIFT \_ hierarchia"                                                             |
| 17 | Engedély                                | No                  | Grant                                                                       |
| 18 | Hierarchia megadása                      | No                  | " \_ hierarchia megadása"                                                            |
| 19 | Üzleti hely hierarchiája              | No                  | "üzleti \_ telephelyi \_ hierarchia"                                                   |
| 20 | Mátrix-szervezet                  | No                  | MÁTRIX                                                                      |
| 21 | Fizetési csoport                            | No                  | "fizetési \_ csoport"                                                                  |
| 22 | Programok                             | No                  | PROGRAMOK                                                                    |
| 23 | Program-hierarchia                    | No                  | "PROGRAM- \_ hierarchia"                                                          |
| 24 | Régió                               | No                  | "régió- \_ hierarchia"                                                           |
| 25 | Hely hierarchiája                   | No                  | "LOCATION \_ hierarchia"                                                         |
| 26 | Fiók létesítési adatkészlete            | No                  | WD: Worker \_ -adattárolás/WD: fiók \_ kiépítési \_ adatvédelme                                |
| 27 | Háttér-ellenőrzési érték                | No                  | WD: Worker \_ -adattárolás/WD: háttér- \_ ellenőrzési \_ adatgyűjtés                                    |
| 28 | Juttatási jogosultsági adatszolgáltatások             | No                  | WD: Worker-adatkezelési \_ /WD: juttatási \_ jogosultságok \_                                 |
| 29 | Beléptetési adatbevitel              | No                  | WD: Worker \_ -adattárolás/WD: juttatás \_ beléptetési \_ adatkészlete                                  |
| 30 | Pályaválasztási adatfeldolgozás                          | No                  | WD: Worker \_ -adattárolás/WD: karrier- \_ adatfeldolgozás                                               |
| 31 | Kompenzációs adatszolgáltatások                    | No                  | WD: Worker \_ -adatfeldolgozó/WD: kompenzációs \_ adatszolgáltatások                                         |
| 32 | Függőben lévő feldolgozó adóhatóságának adatvédelme | No                  | WD: Worker \_ -adatfeldolgozó/WD: feltételes \_ \_ \_ \_ feldolgozó adóhatóság űrlapjának \_ típusa \_       |
| 33 | Fejlesztési adatelem                | No                  | WD: Worker-adatkezelés \_ /WD: fejlesztési \_ adatelem \_                                    |
| 34 | Alkalmazotti szerződések adatkészlete              | No                  | WD: Worker \_ -vagy WD- \_ \_ adatfeldolgozási szerződések – adatkezelés                                  |
| 35 | Alkalmazottak felülvizsgálati adatai                 | No                  | WD: Worker \_ adatai/WD: Employee \_ felülvizsgálati \_ adatai                                     |
| 36 | Visszajelzés érkezett               | No                  | WD: Worker \_ -adatfeldolgozási/WD: visszajelzés \_ fogadva \_                                   |
| 37 | Worker Goal-adat                     | No                  | WD: Worker \_ -adat/WD: Worker \_ Goal- \_ adat                                         |
| 38 | Fényképek                           | No                  | WD: Worker \_ -adatfeldolgozási/WD: fényképek \_                                                |
| 39 | Minősítési adatkészletek                   | No                  | WD: Worker \_ -vagy WD: minősítési \_ adategység                                        |
| 40 | Kapcsolódó személyek adatainak                 | No                  | WD: Worker \_ -adat/WD: kapcsolódó \_ személyek \_ adatainak                                     |
| 41 | Szerepkör-adat                            | No                  | WD: Worker \_ -adat/WD: szerepkör- \_ adat                                                 |
| 42 | Szaktudás                           | No                  | WD: Worker \_ -adattárolás/WD: skill- \_ adatkezelés                                                |
| 43 | Öröklési profilok              | No                  | WD: Worker \_ -adattárolás/WD: öröklési \_ profilok \_                                  |
| 44 | Tehetség-értékelési adatszolgáltatások               | No                  | WD: Worker-adatkezelési \_ /WD: Talent \_ Assessment- \_ adatszolgáltatások                                   |
| 45 | Felhasználói fiókadatok                    | No                  | WD: Worker-adatkezelési \_ /WD: felhasználói \_ fiókadatok \_                                        |
| 46 | Munkavégző dokumentumra vonatkozó adatfeldolgozás                 | No                  | WD: Worker-adatfeldolgozói \_ /WD: Worker \_ Document- \_ adatként                                     |

>[!NOTE]
>A táblázatban felsorolt minden egyes munkanapokat egy **tartományi biztonsági házirend** védi a munkanapokon. Ha nem tudja lekérni az entitáshoz társított összes attribútumot a jobb oldali XPATH beállítása után, egyeztessen a munkanap rendszergazdájával, és győződjön meg arról, hogy a megfelelő tartományi biztonsági házirend konfigurálva van a kiépítési alkalmazáshoz társított integrációs rendszer felhasználója számára. Például a *szaktudás* *beolvasásához a hozzáférésre* van szükség a munkanapokon, a munkahelyen, a *szaktudásban és a tapasztalatokban*. 

Íme néhány példa arra, hogy miként lehet kiterjeszteni a munkanap-integrációt a konkrét követelmények kielégítésére. 

**1\. példa**

Tegyük fel, hogy a következő adatkészleteket szeretné lekérni a munkahelyről, és ezeket a kiépítési szabályokban kell használni:

* Költséghely
* Cost Center-hierarchia
* Fizetési csoport

A fenti adatkészleteket a rendszer alapértelmezés szerint nem tartalmazza. Az adatkészletek beolvasása:
1. Jelentkezzen be a Azure Portalba, és nyissa meg a munkanapokat az AD/Azure AD-beli felhasználói üzembe helyezési alkalmazáshoz. 
1. A kiépítés panelen szerkessze a leképezéseket, és nyissa meg a munkanapokhoz tartozó attribútumok listáját a speciális szakaszban. 
1. Adja hozzá a következő attribútumok definícióit, és jelölje meg a "kötelező" kifejezést. Ezek az attribútumok nem lesznek leképezve az AD vagy az Azure AD egyik attribútumára sem. Az összekötők a Cost Center, a Cost Center-hierarchia és a fizetési csoport adatainak beolvasására szolgáló jelekként szolgálnak. 

     > [!div class="mx-tdCol2BreakAll"]
     >| Attribútum neve | XPATH API kifejezés |
     >|---|---|
     >| CostCenterHierarchyFlag  |  WD: Worker/WD: Worker_Data/WD: Organization_Data/WD: Worker_Organization_Data [WD: Organization_Data/WD: Organization_Type_Reference/WD: ID [ @wd:type = ' Organization_Type_ID '] = ' COST_CENTER_HIERARCHY ']/wd:Organization_Reference/@wd:Descriptor |
     >| CostCenterFlag  |  WD: Worker/WD: Worker_Data/WD: Organization_Data/WD: Worker_Organization_Data [WD: Organization_Data/WD: Organization_Type_Reference/WD: ID [ @wd:type = ' Organization_Type_ID '] = ' COST_CENTER ']/WD: Organization_Data/WD: Organization_Code/Text () |
     >| PayGroupFlag  |  WD: Worker/WD: Worker_Data/WD: Organization_Data/WD: Worker_Organization_Data [WD: Organization_Data/WD: Organization_Type_Reference/WD: ID [ @wd:type = ' Organization_Type_ID '] = ' PAY_GROUP ']/WD: Organization_Data/WD: Organization_Reference_ID/Text () |

1. Ha a Cost Center és a pay Group adatkészlete elérhető a *Get_Workers* válaszban, az alábbi XPath-értékekkel kérheti le a költséghely nevét, a költséghely kódját és a fizetési csoportot. 

     > [!div class="mx-tdCol2BreakAll"]
     >| Attribútum neve | XPATH API kifejezés |
     >|---|---|
     >| CostCenterName  | WD: Worker/WD: Worker_Data/WD: Organization_Data/WD: Worker_Organization_Data/WD: Organization_Data [ wd:Organization_Type_Reference/@wd:Descriptor = "Cost Center"]/WD: Organization_Name/Text () |
     >| CostCenterCode | WD: Worker/WD: Worker_Data/WD: Organization_Data/WD: Worker_Organization_Data/WD: Organization_Data [ wd:Organization_Type_Reference/@wd:Descriptor = "Cost Center"]/WD: Organization_Code/Text () |
     >| PayGroup | WD: Worker/WD: Worker_Data/WD: Organization_Data/WD: Worker_Organization_Data/WD: Organization_Data [ wd:Organization_Type_Reference/@wd:Descriptor = ' Pay Group ']/WD: Organization_Name/Text () |

**2\. példa**

Tegyük fel, hogy egy felhasználóhoz tartozó tanúsítványokat szeretne beolvasni. Ez az információ a *minősítési* adatkészlet részeként érhető el. Ha ezt az adatkészletet a *Get_Workers* válasz részeként szeretné lekérni, használja a következő XPath-t: 

`wd:Worker/wd:Worker_Data/wd:Qualification_Data/wd:Certification/wd:Certification_Data/wd:Issuer/text()`

**3\. példa**

Tegyük fel, hogy egy feldolgozóhoz rendelt *kiépítési csoportokat* szeretne lekérdezni. Ezek az adatok a *fiók létesítési* adatkészletének részeként érhetők el. Ha ezt az adatkészletet a *Get_Workers* válasz részeként szeretné lekérni, használja a következő XPath-t: 

`wd:Worker/wd:Worker_Data/wd:Account_Provisioning_Data/wd:Provisioning_Group_Assignment_Data[wd:Status='Assigned']/wd:Provisioning_Group/text()`

## <a name="next-steps"></a>Következő lépések

* [Megtudhatja, hogyan konfigurálhatja a munkanapokat Active Directory kiépítés céljából](../saas-apps/workday-inbound-tutorial.md)
* [Megtudhatja, hogyan konfigurálhatja az írást munkanapokba](../saas-apps/workday-writeback-tutorial.md)
* [További információ a bejövő kiépítés támogatott munkanapokhoz tartozó attribútumairól](workday-attribute-reference.md)

