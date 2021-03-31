---
title: 'Azure AD Connect Sync: a szinkronizálás megismerése és testreszabása | Microsoft Docs'
description: Ismerteti, hogyan működik Azure AD Connect a szinkronizálás, és hogyan szabható testre.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ee4bf802-045b-4da0-986e-90aba2de58d6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/08/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: cea26cb119f64679807bc6c5eaadb41b341e5d5a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89662388"
---
# <a name="azure-ad-connect-sync-understand-and-customize-synchronization"></a>Azure AD Connect szinkronizálás: a szinkronizálás megismerése és testreszabása
A Azure Active Directory Connect synchronization Services (Azure AD Connect Sync) a Azure AD Connect fő összetevője. Gondoskodik a helyszíni környezet és az Azure AD közötti személyazonossági adatok szinkronizálásával kapcsolatos összes műveletről. A Azure AD Connect Sync az Azure Active Directory-összekötővel konfigurált, az rSync, a Azure AD-szinkronizáló és a Forefront Identity Manager utódja.

Ez a témakör a **Azure ad Connect Sync** (más néven a **szinkronizálási motor**) kezdőlapja, és felsorolja a hozzá kapcsolódó egyéb témakörökre mutató hivatkozásokat. Azure AD Connectre mutató hivatkozásokat a helyszíni [identitások integrálása Azure Active Directory segítségével](whatis-hybrid-identity.md)című témakörben talál.

A szinkronizálási szolgáltatás két összetevőből áll, a helyszíni **Azure ad Connect Sync** összetevőből és az Azure ad-ben a **Azure ad Connect Sync Service** szolgáltatásban megnevezett szolgáltatási oldalból.

## <a name="azure-ad-connect-sync-topics"></a>Azure AD Connect szinkronizálási témakörök
| Témakör | Mit takar, és mikor kell olvasni |
| --- | --- |
| **Azure AD Connect szinkronizálás alapjai** | |
| [Az architektúra megismerése](concept-azure-ad-connect-sync-architecture.md) |Azok számára, akik újak a Szinkronizáló motorban, és szeretnének többet megtudni az architektúráról és a felhasznált feltételekről. |
| [Technikai kulcsfogalmak](how-to-connect-sync-technical-concepts.md) |Az architektúrával foglalkozó témakör rövid változata, amely röviden ismerteti a használt feltételeket. |
| [Azure AD Connect-topológiák](plan-connect-topologies.md) |A szinkronizálási motor által támogatott különböző topológiákat és forgatókönyveket ismerteti. |
| **Egyéni konfiguráció** | |
| [A telepítővarázsló ismételt futtatása](how-to-connect-installation-wizard.md) |Ismerteti, hogy milyen lehetőségek érhetők el a Azure AD Connect telepítővarázsló ismételt futtatásakor. |
| [A deklaratív üzembe helyezés ismertetése](concept-azure-ad-connect-sync-declarative-provisioning.md) |A deklaratív kiépítés nevű konfigurációs modellt ismerteti. |
| [A deklaratív üzembe helyezési kifejezések ismertetése](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md) |A deklaratív kiépítés során használt kifejezés nyelvének szintaxisát ismerteti. |
| [Az alapértelmezett konfiguráció ismertetése](concept-azure-ad-connect-sync-default-configuration.md) |Ismerteti a beépített szabályokat és az alapértelmezett konfigurációt. Azt is leírja, hogyan működnek együtt a szabályok a beépített forgatókönyvek működéséhez. |
| [A felhasználók és a névjegyek ismertetése](concept-azure-ad-connect-sync-user-and-contacts.md) |Folytatja az előző témakört, és leírja, hogyan működik együtt a felhasználók és a kapcsolatok konfigurációja, különösen többerdős környezetben. |
| [Az alapértelmezett konfiguráció módosítása](how-to-connect-sync-change-the-configuration.md) |Bemutatja, hogyan végezheti el a közös konfiguráció módosítását az attribútumok folyamataira. |
| [Az alapértelmezett konfiguráció módosításának ajánlott eljárásai](how-to-connect-sync-best-practices-changing-default-configuration.md) |A támogatási korlátozások és a beépített konfiguráció módosítása. |
| [Szűrés konfigurálása](how-to-connect-sync-configure-filtering.md) |Ismerteti azokat a különböző lehetőségeket, amelyekkel korlátozható, hogy mely objektumok legyenek szinkronizálva az Azure AD-vel, és részletes útmutató a beállítások konfigurálásához. |
| **Funkciók és forgatókönyvek** | |
| [Véletlen törlések megakadályozása](how-to-connect-sync-feature-prevent-accidental-deletes.md) |Ismerteti a *véletlen törlés megakadályozása* funkciót és annak konfigurálását. |
| [Scheduler](how-to-connect-sync-feature-scheduler.md) |A beépített ütemező, amely az Adatimportálási,-szinkronizálási és-exportálási műveleteket ismerteti. |
| [Jelszó-kivonatolási szinkronizálás implementálása](how-to-connect-password-hash-synchronization.md) |A jelszó-szinkronizálás működésének, megvalósításának és működésének és hibaelhárításának módját ismerteti. |
| [Eszközvisszaíró](how-to-connect-device-writeback.md) |Ismerteti, hogyan működik az visszaírási az Azure AD Connect. |
| [Címtárbővítmények](how-to-connect-sync-feature-directory-extensions.md) |Ismerteti, hogyan bővíthető az Azure AD-séma a saját egyéni attribútumaival. |
| [Microsoft 365 PreferredDataLocation](how-to-connect-sync-feature-preferreddatalocation.md) |Leírja, hogyan helyezheti el a felhasználó Microsoft 365 erőforrásait a felhasználóval megegyező régióban. |
| **Szinkronizálási szolgáltatás** | |
| [Az Azure AD Connect szinkronizálási szolgáltatás jellemzői](how-to-connect-syncservice-features.md) |A szinkronizálási szolgáltatás oldalát és az Azure AD szinkronizálási beállításainak módosítását ismerteti. |
| [Ismétlődő attribútum rugalmassága](how-to-connect-syncservice-duplicate-attribute-resiliency.md) |Ismerteti, hogyan lehet engedélyezni és használni a **userPrincipalName** és a **ProxyAddresses** duplikált attribútum-értékek rugalmasságát. |
| **Műveletek és felhasználói felület** | |
| [Szinkronizálási szolgáltatáskezelő](how-to-connect-sync-service-manager-ui.md) |Ismerteti a Synchronization Service Manager felhasználói felületet, beleértve a [műveleteket](how-to-connect-sync-service-manager-ui-operations.md), [összekötőket](how-to-connect-sync-service-manager-ui-connectors.md), [metaverse Designer](how-to-connect-sync-service-manager-ui-mvdesigner.md)és [metaverse keresési](how-to-connect-sync-service-manager-ui-mvsearch.md) lapokat. |
| [Üzemeltetési feladatok és szempontok](./how-to-connect-sync-staging-server.md) |Leírja a működési problémákat, például a vész-helyreállítást. |
| **kézikönyv...** | |
| [Az Azure AD-fiók alaphelyzetbe állítása](how-to-connect-azureadaccount.md) |A Azure AD Connect Sync szolgáltatásból az Azure AD-be való kapcsolódáshoz használt szolgáltatásfiók hitelesítő adatainak alaphelyzetbe állítása. |
| **További információk és hivatkozások** | |
| [Portok](reference-connect-ports.md) |Felsorolja, hogy mely portokat kell megnyitni a Szinkronizáló motor és a helyszíni címtárak és az Azure AD között. |
| [Az Azure Active Directoryval szinkronizált attribútumok](reference-connect-sync-attributes-synchronized.md) |Felsorolja az összes, a helyszíni AD és az Azure AD között szinkronizált attribútumot. |
| [Functions – referencia](reference-connect-sync-functions-reference.md) |A deklaratív kiépítés során elérhető összes függvény listája. |

## <a name="additional-resources"></a>További források
* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)