---
title: 'Azure AD Connect: automatikus frissítés | Microsoft Docs'
description: Ez a témakör a Azure AD Connect Sync beépített automatikus frissítési funkcióját ismerteti.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6b395e8f-fa3c-4e55-be54-392dd303c472
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/09/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3ec237af8cd0c79d5a7b62aad0bc6521e5cf3d7e
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106059241"
---
# <a name="azure-ad-connect-automatic-upgrade"></a>Azure AD Connect: automatikus frissítés
Azure AD Connect az automatikus frissítés egy olyan szolgáltatás, amely rendszeresen ellenőrzi a Azure AD Connect újabb verzióit. Ha a kiszolgáló engedélyezve van az automatikus frissítéshez, és egy újabb verzió található, amelyhez a kiszolgáló jogosult, a rendszer automatikus frissítést hajt végre az újabb verzióra.
Vegye figyelembe, hogy biztonsági okokból az automatikus frissítést végrehajtó ügynök ellenőrzi a letöltött verzió digitális aláírása alapján a Azure AD Connect új buildjét.

## <a name="overview"></a>Áttekintés
Győződjön meg arról, hogy a Azure AD Connect telepítésének mindig naprakésznek kell lennie, az **automatikus frissítési** funkcióval még soha nem volt egyszerűbb. Ez a funkció alapértelmezés szerint engedélyezve van az expressz telepítésekhez és az rSync-frissítésekhez. Új verzió kiadásakor a rendszer automatikusan frissíti a telepítést.
Az automatikus frissítés alapértelmezés szerint engedélyezve van a következőhöz:

* Az expressz beállítások telepítése és az rSync frissítése.
* Az SQL Express LocalDB használata, amely az expressz beállítások mindig a használata. Az SQL Express használatával az LocalDB is használható.
* Az Active Directory-fiók az az alapértelmezett MSOL_-fiók, amelyet az expressz beállítások és az rSync használatával hoztak létre.
* Kevesebb mint 100 000 objektum található a metaverse-ben.

Az automatikus frissítés aktuális állapotát a PowerShell-parancsmaggal lehet megtekinteni `Get-ADSyncAutoUpgrade` . A következő állapotokkal rendelkezik:

| Állapot | Megjegyzés |
| --- | --- |
| Engedélyezve |Az automatikus frissítés engedélyezve van. |
| Felfüggesztve |Csak a rendszeren állítható be. A rendszer **jelenleg nem** jogosult automatikus frissítések fogadására. |
| Disabled (Letiltva) |Az automatikus frissítés le van tiltva. |

Az **engedélyezett** és a **letiltott** érték közötti váltás a következővel: `Set-ADSyncAutoUpgrade` . Csak a rendszeren kell beállítani a **felfüggesztett** állapotot.  A 1.1.750.0 előtt a Set-ADSyncAutoUpgrade parancsmag letiltja az automatikus frissítést, ha az automatikus frissítés állapota felfüggesztve értékre lett állítva. Ez a funkció mostantól megváltozott, így nem blokkolja az autoupgrade funkciót.

Az automatikus frissítés a Azure AD Connect Healtht használja a frissítési infrastruktúrához. Az automatikus frissítés működéséhez győződjön meg arról, hogy megnyitotta az URL-címeket a proxykiszolgálóhoz **Azure ad Connect Health** az [Office 365 URL-címek és IP-címtartományok](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)dokumentációjában leírtaknak megfelelően.


Ha a **synchronization Service Manager** felhasználói felülete fut a kiszolgálón, akkor a rendszer felfüggeszti a frissítést, amíg a felhasználói felület le nem zárul.

## <a name="troubleshooting"></a>Hibaelhárítás
Ha a kapcsolódási példány nem a várt módon frissül, kövesse az alábbi lépéseket, hogy megtudja, mi lehet a probléma.

Először is ne várja meg az automatikus frissítést, hogy az első nap új verziót szabadítson fel. A frissítés megkezdése előtt szándékos véletlenszerűség történt, ezért ne legyen riasztás, ha a telepítés nem azonnal frissül.

Ha úgy gondolja, hogy valami nem megfelelő, először futtassa a parancsot, `Get-ADSyncAutoUpgrade` hogy az automatikus frissítés engedélyezve legyen.

Ha az állapot fel van függesztve, a használatával `Get-ADSyncAutoUpgrade -Detail` megtekintheti az okát.  A felfüggesztési ok bármilyen sztringet tartalmazhat, de általában a UpgradeResult karakterlánc értékét fogja tartalmazni, azaz a vagy a értéket `UpgradeNotSupportedNonLocalDbInstall` `UpgradeAbortedAdSyncExeInUse` .  Egy összetett értéket is vissza lehet adni, például: `UpgradeFailedRollbackSuccess-GetPasswordHashSyncStateFailed` .

Olyan eredmény is lehetséges, amely nem UpgradeResult, azaz "AADHealthEndpointNotDefined" vagy "DirSyncInPlaceUpgradeNonLocalDb".

Ezután győződjön meg arról, hogy megnyitotta a szükséges URL-címeket a proxyban vagy a tűzfalban. Az automatikus frissítés a Azure AD Connect Health használja az [Áttekintés](#overview)című témakörben leírtak szerint. Ha proxyt használ, győződjön meg róla, hogy az állapot [proxykiszolgáló](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy)használatára van konfigurálva. Tesztelje az Azure AD-vel való [rendszerállapot-kapcsolatot](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) is.

Az Azure AD-vel való kapcsolat ellenőrzése után itt az ideje, hogy megtekintse az eseménynaplókat. Indítsa el az eseménynaplót, és keresse meg az **alkalmazás** eseménynaplóját. Adjon hozzá egy eseménynapló-szűrőt a forrás **Azure ad Connect frissítéshez** és az eseményazonosító tartomány **300-399**.  
![Képernyőkép, amely megjeleníti az "aktuális napló szűrése" ablakot az "Event Sources" és az "include/kizár" eseményazonosító mező kiemelésével.](./media/how-to-connect-install-automatic-upgrade/eventlogfilter.png)  

Most már láthatja az automatikus frissítés állapotával kapcsolatos eseménynaplókat.  
![Eseménynapló-szűrő az automatikus frissítéshez](./media/how-to-connect-install-automatic-upgrade/eventlogresult.png)  

Az eredmény kódja egy előtaggal rendelkezik, amely áttekintést nyújt az állapotról.

| Eredmény-kód előtagja | Leírás |
| --- | --- |
| Siker |A telepítés frissítése sikeresen megtörtént. |
| UpgradeAborted |Egy ideiglenes feltétel leállította a frissítést. Újból próbálkozik újra, és a várt érték az, hogy később sikeres lesz. |
| UpgradeNotSupported |A rendszer olyan konfigurációval rendelkezik, amely blokkolja a rendszer automatikus frissítését. A rendszer megpróbálja megtekinteni, hogy az állapot változik-e, de a várt érték az, hogy a rendszert manuálisan kell frissíteni. |

Itt találja a leggyakoribb üzenetek listáját. Nem sorolja fel az összeset, de az eredményről tájékoztató üzenetnek Egyértelműnek kell lennie a problémával kapcsolatban.

| Eredmény üzenet | Leírás |
| --- | --- |
| **UpgradeAborted** | |
| UpgradeAbortedCouldNotSetUpgradeMarker |Nem lehet írni a beállításjegyzékbe. |
| UpgradeAbortedInsufficientDatabasePermissions |A beépített rendszergazdák csoport nem rendelkezik engedéllyel az adatbázishoz. A probléma megoldásához frissítsen manuálisan a Azure AD Connect legújabb verziójára. |
| UpgradeAbortedInsufficientDiskSpace |Nincs elegendő lemezterület a verziófrissítés támogatásához. |
| UpgradeAbortedSecurityGroupsNotPresent |Nem található és nem oldható fel a Szinkronizáló motor által használt összes biztonsági csoport. |
| UpgradeAbortedServiceCanNotBeStarted |Az NT szolgáltatás **Microsoft Azure ad szinkronizálása** nem indult el. |
| UpgradeAbortedServiceCanNotBeStopped |Nem sikerült leállítani az NT szolgáltatás **Microsoft Azure ADának szinkronizálását** . |
| UpgradeAbortedServiceIsNotRunning |Az NT szolgáltatás **Microsoft Azure ad szinkronizálása** nem fut. |
| UpgradeAbortedSyncCycleDisabled |Az [ütemező](how-to-connect-sync-feature-scheduler.md) SyncCycle beállítás le van tiltva. |
| UpgradeAbortedSyncExeInUse |A [szinkronizálási Service Manager felhasználói felülete](how-to-connect-sync-service-manager-ui.md) nyitva van a kiszolgálón. |
| UpgradeAbortedSyncOrConfigurationInProgress |A telepítővarázsló fut, vagy a szinkronizálás az ütemező szolgáltatáson kívül van ütemezve. |
| **UpgradeNotSupported** | |
| UpgradeNotSupportedCustomizedSyncRules |Saját egyéni szabályokat adott hozzá a konfigurációhoz. |
| UpgradeNotSupportedInvalidPersistedState |A telepítés nem expressz beállítások vagy az rSync frissítése. |
| UpgradeNotSupportedNonLocalDbInstall |Nem SQL Server Express LocalDB-adatbázist használ. |
|UpgradeNotSupportedLocalDbSizeExceeded|A helyi adatbázis mérete nagyobb vagy egyenlő, mint 8 GB|
|UpgradeNotSupportedAADHealthUploadDisabled|Az állapot-adatfeltöltés le lett tiltva a portálról|

## <a name="next-steps"></a>Következő lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).
