---
title: 'Azure AD Connect: csoport visszaírási'
description: Ez a cikk a Azure AD Connect csoport visszaírási ismerteti.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 06/11/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c506c87ad5901754175f18e6b50bc6ed46a3c19
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98246910"
---
# <a name="azure-ad-connect-group-writeback"></a>Azure AD Connect csoport visszaírási

A csoportok visszaírási lehetővé teszi, hogy az ügyfelek a hibrid igényeknek megfelelően használják a Felhőbeli csoportokat. Ha a Microsoft 365 csoportok funkciót használja, akkor ezeket a csoportokat a helyszíni Active Directoryban lehet megjeleníteni. Ez a lehetőség **csak** akkor érhető el, ha a helyszíni Active Directoryban Exchange van jelen.

## <a name="pre-requisites"></a>Előfeltételek
A csoport visszaírási engedélyezéséhez a következő előfeltételeknek kell teljesülniük.
- Prémium szintű Azure Active Directory a bérlőhöz tartozó licenceket.
- Egy konfigurált hibrid üzembe helyezés a helyszíni Exchange-szervezet és a Microsoft 365 között, és annak ellenőrzése, hogy megfelelően működik-e.
- Telepítette a helyszíni Exchange támogatott verzióját
- Konfigurált egyszeri bejelentkezés Azure Active Directory Connect használatával 

## <a name="enable-group-writeback"></a>Csoportvisszaírás engedélyezése
A csoport visszaírási engedélyezéséhez kövesse az alábbi lépéseket:

1. Nyissa meg a Azure AD Connect varázslót, válassza a **Konfigurálás** lehetőséget, majd kattintson a **tovább** gombra.
2. Válassza a **szinkronizálási beállítások testreszabása lehetőséget** , majd kattintson a **tovább** gombra.
3. A **Kapcsolódás az Azure ad-hoz** lapon adja meg a hitelesítő adatait. Kattintson a **Tovább** gombra.
4. A **választható szolgáltatások** lapon ellenőrizze, hogy a korábban konfigurált beállítások továbbra is ki vannak-e jelölve.
5. Válassza a **csoport visszaírási** elemet, majd kattintson a **tovább** gombra.
6. A **visszaírási lapon** válasszon ki egy Active Directory szervezeti egységet (OU) a Microsoft 365ból a helyszíni szervezetbe szinkronizált objektumok tárolásához, majd kattintson a **tovább** gombra.
7. A konfigurálásra **kész** lapon kattintson a **Konfigurálás** elemre.
8. A varázsló befejezése után kattintson a **Kilépés** elemre a konfiguráció befejezése lapon.
9. Nyissa meg a Windows PowerShellt rendszergazdaként a Azure Active Directory Connect-kiszolgálón, és futtassa a következő parancsokat.

```Powershell
$AzureADConnectSWritebackAccountDN =  <MSOL_ account DN>
Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\AdSyncConfig.psm1"

# To grant the <MSOL_account> permission to all domains in the forest:
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN $AzureADConnectSWritebackAccountDN

# To grant the <MSOL_account> permission to specific OU (eg. the OU chosen to writeback Office 365 Groups to):
$GroupWritebackOU = <DN of OU where groups are to be written back to>
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN $AzureADConnectSWritebackAccountDN -ADObjectDN $GroupWritebackOU
```

Az Microsoft 365-csoportok konfigurálásával kapcsolatos további információkért lásd: [Microsoft 365 csoportok konfigurálása helyszíni Exchange hybridtel](/exchange/hybrid-deployment/set-up-microsoft-365-groups#enable-group-writeback-in-azure-ad-connect).

## <a name="disabling-group-writeback"></a>Csoport visszaírási letiltása
A csoport visszaírási letiltásához kövesse az alábbi lépéseket: 


1. Indítsa el a Azure Active Directory Connect varázslót, és navigáljon a további feladatok lapra. Jelölje be a **szinkronizálási beállítások testreszabása** feladatot, és kattintson a **tovább** gombra.
2. A **választható szolgáltatások** lapon szüntesse meg a csoport visszaírási.  Figyelmeztetést kap, amely tájékoztatja, hogy a csoportok törlődnek.  Kattintson a **Yes** (Igen) gombra.
   >[!IMPORTANT]
   > A csoport visszaírási letiltásával bármely, a szolgáltatás által korábban létrehozott csoport törölhető a helyi Active Directory a következő szinkronizálási ciklusban. 

   ![Jelölőnégyzet bejelölése](media/how-to-connect-group-writeback/group2.png)
  
3. Kattintson a **Tovább** gombra.
4. Kattintson a **Configure** (Konfigurálás) elemre.

 >[!NOTE]
 > A csoport visszaírási letiltásával a teljes importálási és teljes szinkronizációs jelzőket "true" értékre állítja be az Azure Active Directory-összekötőn, így a szabály a következő szinkronizálási cikluson keresztül propagálja a szabályt, és törli azokat a csoportokat, amelyek korábban visszaírásra kerültek a Active Directory.

## <a name="next-steps"></a>Következő lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).
