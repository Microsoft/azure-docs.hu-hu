---
title: SAP SuccessFactors-attribútumok referenciája
description: Megtudhatja, hogy a SuccessFactors mely attribútumait támogatja a SuccessFactors-HR-vezérelt kiépítés
services: active-directory
author: cmmdesai
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 08/05/2020
ms.author: chmutali
ms.reviewer: celested
ms.openlocfilehash: 0d1a25f67d3550e79b2339ee060d37acaea0c1c4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99255542"
---
# <a name="sap-successfactors-attribute-reference"></a>SAP SuccessFactors-attribútumok referenciája

Ebben a cikkben a következőkkel kapcsolatos információkat talál:

- [SuccessFactors-entitások és-attribútumok](#supported-successfactors-entities-and-attributes)
- [Alapértelmezett attribútumok leképezése](#default-attribute-mapping)

## <a name="supported-successfactors-entities-and-attributes"></a>Támogatott SuccessFactors-entitások és-attribútumok

Az alábbi táblázat rögzíti az alapértelmezett SuccessFactors-attribútumok listáját a következő két üzembe helyezési alkalmazásban:

- [SuccessFactors Active Directory a felhasználók üzembe helyezése](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
- [SuccessFactors az Azure AD-felhasználók üzembe helyezéséhez](../saas-apps/sap-successfactors-inbound-provisioning-cloud-only-tutorial.md)

További attribútumok esetében tekintse meg az [SAP SuccessFactors-integrációs referenciát](./sap-successfactors-integration-reference.md#retrieving-additional-attributes) a séma kiterjesztéséhez. 

| \# | SuccessFactors entitás                  | SuccessFactors attribútum     | Művelettípus |
|----|----------------------------------------|------------------------------|----------------|
| 1  | PerPerson                              | personIdExternal             | Olvasás           |
| 2  | PerPerson                              | Számú personid                     | Olvasás           |
| 3  | PerPerson                              | perPersonUuid                | Olvasás           |
| 4  | PerPersonal                            | displayName                  | Olvasás           |
| 5  | PerPersonal                            | firstName                    | Olvasás           |
| 6  | PerPersonal                            | gender                       | Olvasás           |
| 7  | PerPersonal                            | lastName                     | Olvasás           |
| 8  | PerPersonal                            | middleName                   | Olvasás           |
| 9  | PerPersonal                            | preferredName                | Olvasás           |
| 10 | User                                   | addressLine1                 | Olvasás           |
| 11 | User                                   | addressLine2                 | Olvasás           |
| 12 | User                                   | addressLIne3                 | Olvasás           |
| 13 | User                                   | businessPhone                | Olvasás           |
| 14 | User                                   | cellPhone                    | Olvasás           |
| 15 | User                                   | city                         | Olvasás           |
| 16 | User                                   | ország                      | Olvasás           |
| 17 | User                                   | custom01                     | Olvasás           |
| 18 | User                                   | custom02                     | Olvasás           |
| 19 | User                                   | custom03                     | Olvasás           |
| 20 | User                                   | custom04                     | Olvasás           |
| 21 | User                                   | custom05                     | Olvasás           |
| 22 | User                                   | custom06                     | Olvasás           |
| 23 | User                                   | custom07                     | Olvasás           |
| 24 | User                                   | custom08                     | Olvasás           |
| 25 | User                                   | custom09                     | Olvasás           |
| 26 | User                                   | custom10                     | Olvasás           |
| 27 | User                                   | custom11                     | Olvasás           |
| 28 | User                                   | custom12                     | Olvasás           |
| 29 | User                                   | custom13                     | Olvasás           |
| 30 | User                                   | custom14                     | Olvasás           |
| 31 | User                                   | empId                        | Olvasás           |
| 32 | User                                   | homePhone                    | Olvasás           |
| 33 | User                                   | jobFamily                    | Olvasás           |
| 34 | User                                   | Becenév                     | Olvasás           |
| 35 | User                                   | állapot                        | Olvasás           |
| 36 | User                                   | timeZone                     | Olvasás           |
| 37 | User                                   | username                     | Olvasás           |
| 38 | User                                   | Irányítószám                      | Olvasás           |
| 39 | PerPhone                               | areaCode                     | Olvasás           |
| 40 | PerPhone                               | Országhívószám                  | Olvasás           |
| 41 | PerPhone                               | kiterjesztés                    | Olvasás           |
| 42 | PerPhone                               | Telefonszám                  | Olvasás           |
| 43 | PerPhone                               | phoneType                    | Olvasás           |
| 44 | PerEmail                               | emailAddress                 | Olvasás, írás    |
| 45 | PerEmail                               | emailType                    | Olvasás           |
| 46 | EmpEmployment                          | firstDateWorked              | Olvasás           |
| 47 | EmpEmployment                          | lastDateWorked               | Olvasás           |
| 48 | EmpEmployment                          | userId                       | Olvasás           |
| 49 | EmpEmployment                          | isContingentWorker           | Olvasás           |
| 50 | EmpJob                                 | countryOfCompany             | Olvasás           |
| 51 | EmpJob                                 | emplStatus                   | Olvasás           |
| 52 | EmpJob                                 | endDate                      | Olvasás           |
| 53 | EmpJob                                 | startDate                    | Olvasás           |
| 54 | EmpJob                                 | jobTitle                     | Olvasás           |
| 55 | EmpJob                                 | pozíció                     | Olvasás           |
| 65 | EmpJob                                 | customString13               | Olvasás           |
| 56 | EmpJob                                 | managerId                    | Olvasás           |
| 57 | EmpJob \. részleghez                   | Részleghez                 | Olvasás           |
| 58 | EmpJob \. részleghez                   | businessUnitId               | Olvasás           |
| 59 | EmpJob- \. Vállalat                        | cég                      | Olvasás           |
| 60 | EmpJob- \. Vállalat                        | companyId                    | Olvasás           |
| 61 | EmpJob \. vállalati \. CountryOfRegistration | twoCharCountryCode           | Olvasás           |
| 62 | EmpJob \. CostCenter                     | costCenter                   | Olvasás           |
| 63 | EmpJob \. CostCenter                     | costCenterId                 | Olvasás           |
| 64 | EmpJob \. CostCenter                     | costCenterDescription        | Olvasás           |
| 65 | EmpJob \. részleg                     | Részleg                   | Olvasás           |
| 66 | EmpJob \. részleg                     | departmentId                 | Olvasás           |
| 67 | EmpJob \. divízió                       | osztály                     | Olvasás           |
| 68 | EmpJob \. divízió                       | divisionId                   | Olvasás           |
| 69 | EmpJob \. JobCode                        | jobCode                      | Olvasás           |
| 70 | EmpJob \. JobCode                        | jobCodeId                    | Olvasás           |
| 71 | EmpJob \. helye                       | LocationName                 | Olvasás           |
| 72 | EmpJob \. helye                       | officeLocationAddress        | Olvasás           |
| 73 | EmpJob \. helye                       | officeLocationCity           | Olvasás           |
| 74 | EmpJob \. helye                       | officeLocationCustomString4  | Olvasás           |
| 75 | EmpJob \. helye                       | officeLocationZipCode        | Olvasás           |
| 76 | EmpJob \. PayGrade                       | payGrade                     | Olvasás           |
| 77 | EmpEmploymentTermination               | activeEmploymentsCount       | Olvasás           |
| 78 | EmpEmploymentTermination               | latestTerminationDate        | Olvasás           |

## <a name="default-attribute-mapping"></a>Alapértelmezett attribútumok leképezése

Az alábbi táblázat az alapértelmezett attribútum-hozzárendelést tartalmazza a fent felsorolt SuccessFactors-attribútumok és az AD/Azure AD-attribútumok között. Az Azure AD kiépítési alkalmazás "leképezése" paneljén módosíthatja ezt az alapértelmezett leképezést, hogy tartalmazza a fenti listából származó attribútumokat is. 

| \# | SuccessFactors entitás                  | SuccessFactors attribútum | Alapértelmezett AD/Azure AD-attribútumok leképezése   | Feldolgozási Megjegyzés                                                                            |
|----|----------------------------------------|--------------------------|-----------------------------------------|----------------------------------------------------------------------------------------------|
| 1  | PerPerson                              | personIdExternal         | Alkalmazottkód                              | Egyező attribútumként használatos                                                                   |
| 2  | PerPerson                              | perPersonUuid            | \[Nincs leképezve \- forrás-horgonyként használva\] | A kezdeti szinkronizálás során a kiépítési szolgáltatás összekapcsolja a personUuid a meglévő objectGuid\.  |
| 3  | PerPersonal                            | displayName              | displayName                             | NA                                                                                           |
| 4  | PerPersonal                            | firstName                | givenName                               | NA                                                                                           |
| 5  | PerPersonal                            | lastName                 | sn                                      | NA                                                                                           |
| 6  | User                                   | addressLine1             | streetAddress                           | NA                                                                                           |
| 7  | User                                   | city                     | l                                       | NA                                                                                           |
| 8  | User                                   | ország                  | co                                      | NA                                                                                           |
| 9  | User                                   | állapot                    | st                                      | NA                                                                                           |
| 10 | User                                   | username                 | samAccountName                          | NA                                                                                           |
| 11 | User                                   | Irányítószám                  | postalCode                              | NA                                                                                           |
| 12 | PerEmail                               | emailAddress             | Levelezés                                    | NA                                                                                           |
| 13 | EmpJob                                 | jobTitle                 | cím                                   | NA                                                                                           |
| 14 | EmpJob                                 | managerId                | manager                                 | NA                                                                                           |
| 15 | EmpJob \. vállalati \. CountryOfRegistration | twoCharCountryCode       | c                                       | NA                                                                                           |
| 16 | EmpJob \. részleg                     | Részleg               | Részleg                              | NA                                                                                           |
| 17 | EmpJob \. divízió                       | osztály                 | cég                                 | NA                                                                                           |
| 18 | EmpJob \. helye                       | officeLocationAddress    | streetAddress                           | NA                                                                                           |
| 19 | EmpJob \. helye                       | officeLocationZipCode    | postalCode                              | NA                                                                                           |
| 20 | EmpEmploymentTermination               | activeEmploymentsCount   | accountEnabled                          | Ha a activeEmploymentsCount = 0, tiltsa le a account\.                                           |
