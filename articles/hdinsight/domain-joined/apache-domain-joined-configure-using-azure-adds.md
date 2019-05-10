---
title: Vállalati biztonsági csomag konfigurációját az Active Directory Domain Servicest – Azure HDInsight segítségével
description: Megtudhatja, hogyan állíthatja be, és a HDInsight vállalati biztonsági csomag fürt konfigurálása Azure Active Directory Domain Services használatával.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.custom: seodec18
ms.date: 04/23/2019
ms.openlocfilehash: b084790bf5a4edfed74dd95a40c11eec26d34dbe
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2019
ms.locfileid: "65415464"
---
# <a name="configure-a-hdinsight-cluster-with-enterprise-security-package-by-using-azure-active-directory-domain-services"></a>HDInsight-fürt konfigurálása Enterprise Security Package-dzsel az Azure Active Directory Domain Services használatával

Vállalati biztonsági csomag (ESP) fürtök többfelhasználós hozzáférést biztosítanak az Azure HDInsight-fürtökön. ESP HDInsight-fürtöket, hogy a tartományi felhasználók tartományi hitelesítő adataik használatával a fürtökkel hitelesítéséhez és a big data-feladatokat futtatni egy tartományhoz csatlakozik.

Ebből a cikkből elsajátíthatja egy HDInsight-fürt konfigurálása ESP az Azure Active Directory Domain Services (Azure AD DS) használatával.

> [!NOTE]  
> ESP el általánosan elérhető a HDInsight 3.6-os és 4.0-s típusok fürtöt: Az Apache Spark, interaktív, az Apache Hadoop és HBase. ESP az Apache Kafka-fürt típusát az előzetes verzióban.

## <a name="enable-azure-ad-ds"></a>Engedélyezze az Azure AD-DS-ben

> [!NOTE]  
> Csak a bérlői rendszergazdák a jogosultság az Azure AD-tartományi szolgáltatások engedélyezése. Ha a fürttárolóhoz-e az Azure Data Lake Storage (ADLS) Gen1 és Gen2, le kell tiltania a multi-factor Authentication (MFA) csak a felhasználók számára, aki a fürtöt alapszintű Kerberos-hitelesítés használatával kell. Használhat [megbízható IP-címek](../../active-directory/authentication/howto-mfa-mfasettings.md#trusted-ips) vagy [feltételes hozzáférési](../../active-directory/conditional-access/overview.md) többtényezős hitelesítés letiltása adott felhasználókra vonatkozóan csak amikor érnek el a HDInsight-fürt virtuális hálózat IP-címtartományt. Ha használja a feltételes hozzáférés győződjön meg arról, hogy az AD a szolgáltatási végpont engedélyezve van a HDInsight virtuális hálózaton.
>
> Ha a fürt tároló Azure Blob Storage (WASB), ne tiltsa le az MFA.

Azure ad-Tartományi engedélyezése előfeltétele az ESP használata egy HDInsight-fürt létrehozása előtt. További információkért lásd: [engedélyezése az Active Directory Domain Servicest az Azure portal használatával](../../active-directory-domain-services/active-directory-ds-getting-started.md). 

Az Azure AD-Tartományi engedélyezve van, minden felhasználó és objektumok indítsa el az alapértelmezés szerint az Azure AD-Tartományi szinkronizálása az Azure Active Directory (AAD). Az objektumok száma az Azure ad-ben a szinkronizálási műveletet hossza függ. A szinkronizálás eltarthat pár nappal a több száz, több ezer objektumot tartalmaz. 

Kiválaszthatja a szinkronizálni csak azokat a csoportokat, amelyek a HDInsight-fürtökbe való hozzáférésre van szükségük. Ez a beállítás csak bizonyos csoportokat a szinkronizálás nevezzük *szinkronizálás hatóköre*. Lásd: [konfigurálása hatókörrel rendelkező Azure AD-ből a felügyelt tartományhoz való szinkronizálás](../../active-directory-domain-services/active-directory-ds-scoped-synchronization.md) útmutatást.

Secure LDAP engedélyezése, ha a tanúsítvány helyezni a tartománynév a tulajdonos neve és a tulajdonos alternatív neveként. Például, ha a tartománynév *contoso100.onmicrosoft.com*, győződjön meg arról, hogy pontos neve létezik a tanúsítvány tulajdonos neve és a tulajdonos alternatív neveként. További információkért lásd: [a felügyelt tartomány secure LDAP konfigurálása az Azure AD-DS a](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md). Az alábbiakban egy példát egy önaláírt tanúsítvány létrehozása, és rendelkezik a tartomány nevét (*contoso100.onmicrosoft.com*) a tulajdonos neve és a DnsName (tulajdonos alternatív neve):

```powershell
$lifetime=Get-Date
New-SelfSignedCertificate -Subject contoso100.onmicrosoft.com `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.contoso100.onmicrosoft.com, contoso100.onmicrosoft.com
```

## <a name="check-azure-ad-ds-health-status"></a>Az Azure AD-DS állapot ellenőrzése
Megtekintheti az Azure Active Directory Domain Services állapotának **állapotfigyelő** alatt a **kezelés** kategória. Győződjön meg arról, az Azure AD-Tartományi zöld (fut) és a szinkronizálás befejeződött.

![Az Azure Active Directory Domain Services állapota](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png)

## <a name="create-and-authorize-a-managed-identity"></a>Hozzon létre, és a egy felügyelt identitás engedélyezése

A **felhasználó által hozzárendelt felügyelt identitás** egyszerűsítése és biztonságos domain services műveletek szolgál. A HDInsight Domain Services közreműködői szerepkör hozzárendelése a felügyelt identitást, amikor azt is olvassa el, létrehozása, módosítása és törlési műveletek a tartományi szolgáltatások. A HDInsight vállalati biztonsági csomag egyes domain services műveletek, például a szervezeti egységek és az egyszerű szolgáltatások létrehozásához szükséges. Felügyelt identitások bármely előfizetés hozható létre. További információk az általános felügyelt identitások, lásd: [felügyelt identitások az Azure-erőforrások](../../active-directory/managed-identities-azure-resources/overview.md). Felügyelt identitások munka az Azure HDInsight további információkért lásd: [által felügyelt identitásokat az Azure HDInsight](../hdinsight-managed-identities.md).

ESP fürtök beállításához hozzon létre egy felhasználó által hozzárendelt felügyelt identitás, ha már nincs. Lásd: [létrehozás, list, delete vagy egy az Azure portal használatával felügyelt felhasználó által hozzárendelt identitások szerepkör hozzárendelése](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) útmutatást. Ezt követően rendelje hozzá a **HDInsight Domain Services közreműködői** a felügyelt identitás (AAD-Tartományi rendszergazdai jogosultságok szükségesek, hogy a szerepkör-hozzárendelés) az Azure AD-Tartományi hozzáférés-vezérlés a szerepkört.

![Az Azure Active Directory tartományi szolgáltatások hozzáférés-vezérlés](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

Hozzárendelés a **HDInsight Domain Services közreműködői** szerepkör biztosítja, hogy ez az identitás megfelelő (nevében) domain services műveletek, például a szervezeti egységek létrehozása, törlés, a szervezeti egységek, stb. az AAD-DS-tartományról a végezhetők el.

A felügyelt identitás létrehozása és a megfelelő szerepkört kap, után az AAD-Tartományi rendszergazda beállíthatja ki használhatja a felügyelt identitást. A felhasználók a felügyelt identitás beállítása esetén a rendszergazda kell a felügyelt identitás kiválasztása a portálon, majd kattintson **hozzáférés-vezérlés (IAM)** alatt **áttekintése**. Ezután a jobb oldali rendeljen a **felügyelt identitások üzemeltetője** azon felhasználók vagy csoportok HDInsight ESP-fürtök létrehozása a kívánt szerepkört. Például az AAD-Tartományi rendszergazda rendelhet hozzá ezt a szerepkört a **MarketingTeam** csoportot ehhez a **sjmsi** felügyelt identitás, az alábbi képen látható módon. Ez biztosítja, hogy a megfelelő emberek számára a szervezeten belüli hozzáférjenek a felügyelt identitást ESP-fürtök létrehozása céljából használja.

![HDInsight által felügyelt identitás operátor szerepkör-hozzárendelés](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-managed-identity-operator-role-assignment.png)

## <a name="networking-considerations"></a>Hálózati megfontolások

> [!NOTE]  
> Az Azure AD-Tartományi telepíteni kell egy Azure Resource Manager (ARM) alapú virtuális hálózatban. A klasszikus virtuális hálózatok nem támogatottak az Azure AD-DS-ben. Tekintse meg [engedélyezése az Active Directory Domain Servicest az Azure portal használatával](../../active-directory-domain-services/active-directory-ds-getting-started-network.md) további részletekért.

Miután engedélyezte az Azure AD-Tartományi, egy helyi tartomány neve szolgáltatás (DNS) kiszolgáló fut, az AD virtuális gépeken (VM). Az Azure AD-Tartományi Virtual Network (VNET) ezen egyéni DNS-kiszolgálók használatára konfigurálja. Keresse meg a megfelelő IP-címeket, válasszon **tulajdonságok** alatt a **kezelés** alatt felsorolt kategória és tekintse meg az IP-címek **IP-cím virtuális hálózaton**.

![Keresse meg az IP-címek a helyi DNS-kiszolgálók](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-dns.png)

Kiválasztásával ezeket egyéni IP-címek használata az Azure AD-Tartományi virtuális hálózat DNS-kiszolgálók konfigurációjának módosítása **DNS-kiszolgálók** alatt a **beállítások** kategória. Kattintson a Tovább gombra a választógomb **egyéni**, az alábbi mezőben adja meg az első IP-címet, és kattintson a **mentése**. Adjon hozzá további IP-címeket használja ugyanazokat a lépéseket.

![A virtuális hálózat DNS-konfiguráció frissítése](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-vnet-configuration.png)

Az Azure AD-DS-példánya és a HDInsight-fürt is helyezni, az azonos Azure virtuális hálózat könnyebbé válik. Eltérő virtuális hálózatokba használni szeretne, úgy, hogy a tartományvezérlő HDI virtuális gépek láthatók társviszonyba állítása kell ezekhez a virtuális hálózatokhoz. További információkért lásd: [virtuális hálózatok közötti társviszony](../../virtual-network/virtual-network-peering-overview.md). 

Miután a virtuális hálózatok társviszonyban állnak, konfigurálása a HDInsight VNET az egyéni DNS-kiszolgálót használ, és adjon meg az Azure AD-Tartományi magánhálózati IP-címek, a DNS-kiszolgáló címei. Ha mindkét virtuális hálózaton az azonos DNS-kiszolgálók, az egyéni tartomány nevét feloldja a megfelelő IP-címhez, és HDInsight elérhető lesz. Például ha a tartomány nevét a "contoso.com" majd elvégezte a lépést, pingelése "contoso.com" Azure AD-Tartományi IP jobb kell feloldhatónak lennie. 

![Egyéni DNS-kiszolgálók beállítása a társított virtuális hálózaton](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-peered-vnet-configuration.png)

Ha a hálózati biztonsági csoportok (NSG) szabályai a HDInsight-alhálózat használ, akkor ajánlott engedélyezni a [szükséges IP-címek](../hdinsight-extend-hadoop-virtual-network.md) listáinak bejövő és kimenő forgalom számára. 

**Teszteléséhez** a hálózatkezelés megfelelően van beállítva, ha egy windows rendszerű virtuális gép csatlakozzon a HDInsight virtuális hálózat/alhálózat, és pingelje a tartomány nevét (azt kell feloldhatónak lennie egy IP-cím), majd futtassa **ldp.exe** Azure Active Directory-tartományi szolgáltatások eléréséhez. Majd **a windows rendszerű virtuális gép csatlakoztatása a tartományhoz, győződjön meg arról, hogy** , amely az összes szükséges távoli eljáráshívások sikeres legyen, az ügyfél és kiszolgáló között. Is **nslookup** , erősítse meg a storage-fiók vagy bármilyen külső DB használható (például külső Hive-metaadattár vagy Ranger DB) hálózati hozzáférést.
Meg kell győződnie arról, hogy minden a [szükséges portok](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772723(v=ws.10)#communication-to-domain-controllers) szerepel az engedélyezési listán az AAD-DS-ben alhálózat hálózati biztonsági csoport szabályok vonatkoznak, ha az AAD-DS védi egy NSG-t. Ha a tartományhoz való csatlakozás a Windows virtuális gép létrejött, akkor folytassa a következő lépéssel és ESP-fürtök létrehozása.

## <a name="create-a-hdinsight-cluster-with-esp"></a>ESP-HDInsight-fürt létrehozása

Az előző lépések megfelelően beállítása után a következő lépés az a HDInsight-fürt létrehozása a ESP engedélyezve van. Amikor létrehoz egy HDInsight-fürtöt, engedélyezheti a vállalati biztonsági csomag a **egyéni** fülre. Ha inkább egy Azure Resource Manager-sablon központi telepítéshez használni szeretne, egyszer használja a portál felülete, és töltse le az előre kitöltött sablon az utolsó "Összegzés" lapon későbbi felhasználásra.

> [!NOTE]  
> Az ESP fürt nevének első hat karakterét környezetében egyedinek kell lennie. Például ha több ESP fürt eltérő virtuális hálózatokba tartozó, válasszák egy elnevezési convension, amely biztosítja, hogy a fürt nevét az első hat karakterek egyedi.

![Az Azure HDInsight vállalati biztonsági csomag tartomány ellenőrzése](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate.png)

Miután engedélyezte a ESP, az Azure AD-Tartományi kapcsolatos gyakori konfigurációs hibáinak lehet automatikusan észlelte és érvényesítve. Ezek a hibák kijavítása, után, és folytassa a következő lépés: 

![Az Azure HDInsight vállalati biztonsági csomag nem sikerült ellenőrizni a tartományt](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-create-cluster-esp-domain-validate-failed.png)

ESP egy HDInsight-fürtöt hoz létre, amikor meg kell adnia a következő paraméterekkel:

- **Fürt rendszergazdai felhasználói**: Válassza ki a fürt rendszergazdája, a szinkronizált Azure AD-DS-ben. A tartományi fiók a szinkronizált és elérhető az Azure AD-Tartományi már kell lennie.

- **A fürt hozzáféréscsoportokat**: A biztonsági csoportok segít a felhasználóknak szeretne szinkronizálni, és a fürt hozzáféréssel rendelkezik az Azure AD-Tartományi elérhetőnek kell lennie. Ha például HiveUsers csoport. További információkért lásd: [hozzon létre egy csoportot, és tagokat vehet fel a az Azure Active Directoryban](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

- **LDAPS URL-CÍM**: Ez például akkor ldaps://contoso.com:636.

Az alábbi képernyőfelvételen egy sikeres konfigurálása az Azure Portalon:

![Az Azure HDInsight ESP Active Directory Domain Services konfigurálása](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).

A létrehozott felügyelt identitás lehet kiválasztani az a felhasználó által hozzárendelt felügyelt identitás legördülő listából új fürt létrehozása során.

![Az Azure HDInsight ESP Active Directory Domain Services konfigurálása](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-identity-managed-identity.png).

## <a name="next-steps"></a>További lépések

* Hive-házirendek konfigurálása és Hive-lekérdezések futtatása: [Apache Hive-házirendek konfigurálása a HDInsight-fürtök ESP](apache-domain-joined-run-hive.md).
* Az SSH használatával csatlakozhat a HDInsight-fürtök ESP, lásd: [az SSH használata a Linux-alapú Apache Hadooppal a HDInsight Linux, Unix vagy OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
