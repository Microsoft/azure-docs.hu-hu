---
title: Az Azure Sentinelhez való Active Directory Alsid összekötése | Microsoft Docs
description: Megtudhatja, hogyan használhatja a Active Directory-összekötő Alsid a Alsid-naplók Azure Sentinelbe való lekéréséhez. Megtekintheti a munkafüzetek Alsid, riasztásokat hozhat létre, és javíthatja a vizsgálatot.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: 654ecb65068e4321b85594d96e8ca7a7f73cde7e
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2021
ms.locfileid: "99566732"
---
# <a name="connect-your-alsid-for-active-directory-ad-to-azure-sentinel"></a>A Alsid Active Directory (AD) és az Azure Sentinel összekötése

> [!IMPORTANT]
> Az Active Directory Connector Alsid jelenleg **előzetes** verzióban érhető el. Tekintse meg a kiegészítő [használati feltételeket a Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) előzetes verziókra vonatkozó további jogi feltételekhez, amelyek olyan Azure-szolgáltatásokra vonatkoznak, amelyek a bétaverzióban, az előzetes verzióban vagy más esetben még nem jelent meg általánosan elérhetővé.

Ez a cikk azt ismerteti, hogyan csatlakoztatható a Alsid az AD-megoldáshoz az Azure Sentinel szolgáltatáshoz. A Active Directory adatösszekötő Alsid egyszerűen csatlakoztathatja az AD-naplókhoz tartozó Alsid az Azure Sentinel használatával, így megtekintheti a munkafüzetek adatait, lekérdezheti, hogy egyéni riasztásokat hozzon létre, és hogyan építheti be a vizsgálat javítására. A Alsid for AD és az Azure Sentinel közötti integráció a telepített Log Analytics ügynökkel rendelkező syslog-kiszolgáló használatát teszi lehetővé. Egy Kusto függvényen alapuló, egyéni kialakítású naplózási elemzőt is használ.

> [!NOTE]
> Az Azure Sentinel-t futtató munkaterület földrajzi helye tárolja az adatmennyiséget.

## <a name="prerequisites"></a>Előfeltételek

- Írási engedéllyel kell rendelkeznie az Azure Sentinel munkaterületen.

- Az AD-megoldás Alsid úgy kell konfigurálni, hogy a syslog használatával exportálja a naplókat.

## <a name="send-alsid-for-ad-logs-to-azure-sentinel-via-the-syslog-agent"></a>Az AD-naplók Alsid küldése az Azure Sentinelnek a syslog-ügynök használatával

Konfigurálja a Alsid az AD-hez, hogy a syslog-ügynökön keresztül továbbítsa a syslog-üzeneteket az Azure Sentinel-munkaterületre.

1. Az Azure Sentinel navigációs menüjében válassza az **adatösszekötők** lehetőséget.

1. Az **adatösszekötők katalógusában** válassza ki a **Active Directory (előzetes verzió) összekötő Alsid** , majd **nyissa meg az összekötő lapot**.

1. Kövesse a Active Directory-összekötő lapjának **Alsid** utasításait:

    1. Syslog-kiszolgáló konfigurálása

        1. Ha még nem rendelkezik ilyennel, hozzon létre egy Linux syslog-kiszolgálót a Alsid for AD számára, hogy naplókat küldjön a szolgáltatásnak. Az Azure Sentinel a **rsyslog** és a **syslog-ng** démonokat is támogatja. 

        1. Konfigurálja a syslog-kiszolgálót úgy, hogy az AD-naplók kimeneti Alsid külön fájlban adja meg.

    1. A Alsid konfigurálása az AD-hoz a naplóknak a syslog-kiszolgálóra való küldéséhez

        1. A **Alsid az ad** Portalon lépjen a *rendszer*, a *konfiguráció* és a *syslog* elemre. Innen létrehozhat egy új syslog-riasztást a syslog-kiszolgáló felé. A távoli kiszolgáló esetében használja a Linux-ügynököt futtató Linux-gép IP-címét.

        1. Ellenőrizze, hogy a naplók megfelelően vannak-e összegyűjtve a kiszolgálón egy külön fájlban (Ehhez használja a Alsid for AD-ben a *syslog* riasztási konfigurációjának **tesztelése a konfigurációs** gomb segítségével).

    1. A Linux rendszerhez készült Log Analytics-ügynök telepítése és előkészítése

        - Válasszon egy Azure Linux rendszerű virtuális gépet vagy egy nem Azure-beli Linux-gépet (fizikai vagy virtuális). Kövesse a képernyőn megjelenő hivatkozásokat és utasításokat. További részletekért tekintse [meg a Linux-gép vagy-berendezés konfigurálása](connect-syslog.md#configure-your-linux-machine-or-appliance) című témakört.

    1. A Log Analytics ügynök által gyűjtött naplók konfigurálása

        - Válassza ki a létesítményeket és a megszakításokat a munkaterület speciális beállítások konfigurációjában.

            1. Kattintson a **munkaterület megnyitása speciális beállítások konfigurációs >** hivatkozásra az összekötő lapon.

            1. A **Speciális beállítások** képernyőn válassza az **adatelemek** , majd az **egyéni naplók** lehetőséget.

            1. Jelölje be az **alábbi konfiguráció alkalmazása a Linux rendszerű gépeken** jelölőnégyzetet, majd kattintson a **Hozzáadás** gombra.

            1. Kattintson a **fájl kiválasztása** lehetőségre a syslog-kiszolgálót futtató linuxos gépről származó Alsid feltöltéséhez, majd kattintson a **tovább** gombra.

            1. Győződjön meg arról, hogy a **rekord elválasztójának beállítása** **új sor** , majd kattintson a **tovább** gombra.

            1. Válassza a **Linux** lehetőséget, majd adja meg a syslog-fájl elérési útját, majd kattintson a **+** Next ( **tovább**) gombra.

            1. A név mezőbe írja be a *AlsidForADLog* a _CL utótag előtt, majd kattintson a **kész** gombra.
    
Akár 20 percet is igénybe vehet, mielőtt a naplók elkezdenek megjelenni Log Analyticsban.

## <a name="find-your-data"></a>Az adatai megkeresése

A sikeres kapcsolatok létrejötte után az adat megjelenik a **naplók** területen a *AlsidForADLog_CL* tábla **egyéni naplók** területén.

Ez az adatösszekötő egy Kusto függvény alapján egy elemzőtől függ, hogy a várt módon működjön-e. A következő lépésekkel állíthatja be a **afad_parser** Kusto függvényt a lekérdezésekben és a munkafüzetekben való használatra.

1. Az Azure Sentinel navigációs menüjében válassza a **naplók** lehetőséget.

1. Másolja a következő lekérdezést, és illessze be a lekérdezési ablakba.
    ```kusto
    let CodenameTable=datatable(Codename: string, Explanation: string) [
    "test-checker-codename", "This is a test checker",
    "", "Not an alert",
    "C-ADM-ACC-USAGE", "Recent use of the default administrator account",
    "C-UNCONST-DELEG", "Dangerous delegation",
    "C-PASSWORD-DONT-EXPIRE", "Accounts with never expiring passwords",
    "C-USERS-CAN-JOIN-COMPUTERS", "Users allowed to join computers to the domain",
    "C-CLEARTEXT-PASSWORD", "Potential clear-text password",
    "C-PROTECTED-USERS-GROUP-UNUSED", "Protected Users group not used",
    "C-PASSWORD-POLICY", "Weak password policies are applied on users",
    "C-GPO-HARDENING", "Domain without computer-hardening GPOs",
    "C-LAPS-UNSECURE-CONFIG", "Local administrative account management",
    "C-AAD-CONNECT", "Verify permissions related to AAD Connect accounts",
    "C-AAD-SSO-PASSWORD", "Verify AAD SSO account password last change",
    "C-GPO-SD-CONSISTENCY", "Verify sensitive GPO objects and files permissions",
    "C-DSHEURISTICS", "Domain using a dangerous backward-compatibility configuration",
    "C-DOMAIN-FUNCTIONAL-LEVEL", "Domains have an outdated functional level",
    "C-DISABLED-ACCOUNTS-PRIV-GROUPS", "Disabled accounts in privileged groups",
    "C-DCSHADOW", "Rogue domain controllers",
    "C-DC-ACCESS-CONSISTENCY", "Domain controllers managed by illegitimate users",
    "C-DANGEROUS-TRUST-RELATIONSHIP", "Dangerous trust relationship",
    "C-DANGEROUS-SENSITIVE-PRIVILEGES", "Dangerous sensitive privileges",
    "C-DANG-PRIMGROUPID", "User Primary Group ID",
    "C-BAD-PASSWORD-COUNT", "Brute-force attack detection",
    "C-ADMINCOUNT-ACCOUNT-PROPS", "AdminCount attribute set on standard users",
    "C-ACCOUNTS-DANG-SID-HISTORY", "Accounts having a dangerous SID History attribute",
    "C-ABNORMAL-ENTRIES-IN-SCHEMA", "Dangerous rights in AD's schema",
    "C-GPOLICY-DISABLED-UNLINKED", "Unlinked, disabled or orphan GPO",
    "C-KERBEROS-CONFIG-ACCOUNT", "Kerberos configuration on user account",
    "C-KRBTGT-PASSWORD", "KDC password last change",
    "C-LAPS-UNSECURE-CONFIG", "Local administrative account management",
    "C-NATIVE-ADM-GROUP-MEMBERS", "Native administrative group members",
    "C-NETLOGON-SECURITY", "Unsecured configuration of Netlogon protocol",
    "C-OBSOLETE-SYSTEMS", "Computers running an obsolete OS",
    "C-PASSWORD-NOT-REQUIRED", "Account that might have an empty password",
    "C-PKI-WEAK-CRYPTO", "Use of weak cryptography algorithms into Active Directory PKI",
    "C-PRE-WIN2000-ACCESS-MEMBERS", "Accounts using a pre-Windows 2000 compatible access control",
    "C-PRIV-ACCOUNTS-SPN", "Privileged accounts running Kerberos services",
    "C-REVER-PWD-GPO", "Reversible passwords in GPO",
    "C-ROOTOBJECTS-SD-CONSISTENCY", "Root objects permissions allowing DCSync-like attacks",
    "C-SDPROP-CONSISTENCY", "Ensure SDProp consistency",
    "C-SENSITIVE-CERTIFICATES-ON-USER", "Ensure SDProp consistency",
    "C-SLEEPING-ACCOUNTS", "Sleeping accounts",
    "C-USER-PASSWORD", "User account using old password",
    "C-USERS-REVER-PWDS", "Reversible passwords"
    ];
    let Common = AlsidForADLog_CL
    | parse RawData with
                         Time:datetime  " "
                         Host:string  " "
                         Product:string "["
                         PID:int "]: \""
                         MessageType:int "\" \""
                         AlertID:int "\" \""
                         Forest:string "\" \""
                         Domain:string "\" "
                         DistinctPart:string;
    let Deviances = Common
    | where MessageType == 0 | parse DistinctPart with "\""
                         Codename:string "\" \""
                         Severity:string "\" \""
                         ADObject:string "\" \""
                         DevianceID:string "\" \""
                         ProfileID:string "\" \""
                         ReasonCodename:string "\" \""
                         EventID:string "\""
                         Attributes:string "\r\n";
    let Changes = Common
    | where MessageType == 1
    | parse kind=regex DistinctPart with "\""
                         ADObject:string "\" \""
                         EventID:string "\" \""
                         EventType:string "\" "
                         Attributes:string "\r?\n";
    union Changes, Deviances
    | project-away DistinctPart, Product, _ResourceId, _SubscriptionId
    | lookup kind=leftouter CodenameTable on Codename;
    ```

1. Kattintson a **Save (Mentés** ) legördülő listára, és kattintson a **Save (Mentés**) gombra. A **Mentés** panelen

    1. A **név** mezőben adja meg a **afad_parser**.

    1. A **Mentés másként** területen válassza a **függvény** lehetőséget.

    1. A **függvény aliasneve** területen adja meg a **afad_parser**.

    1. A **Kategória** mezőben adja meg a **függvények** értéket.

    1. Kattintson a **Mentés** gombra.

    A Function apps általában 10 – 15 percet vesz igénybe.

Most már készen áll arra, hogy lekérdezze a Alsid az AD-adatokat a `afad_parser` lekérdezési ablak felső sorába való beírásával.

További lekérdezési mintákért tekintse meg az összekötő oldal **következő lépések** lapját.

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtudhatta, hogyan csatlakoztatható a Alsid az AD-hez az Azure Sentinel szolgáltatáshoz. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- Ismerje meg, hogyan [érheti el az adatait és a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.
