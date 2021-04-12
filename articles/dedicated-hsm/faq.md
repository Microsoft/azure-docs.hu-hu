---
title: Gyakori kérdések – Azure dedikált HSM | Microsoft Docs
description: Válaszok az Azure dedikált hardveres biztonsági modullal kapcsolatos gyakori kérdésekre, például az alapszintű információkra, az együttműködésre, a magas rendelkezésre állásra és a támogatásra.
services: dedicated-hsm
author: keithp
manager: rkarlin
tags: azure-resource-manager
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/25/2021
ms.author: keithp
ms.openlocfilehash: 8768acc8d4720a750c4c7f7e0f40340aeeff279d
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449052"
---
# <a name="frequently-asked-questions-faq"></a>Gyakori kérdések (GYIK)

Választ kaphat a Microsoft Azure dedikált HSM-vel kapcsolatos gyakori kérdésekre.

## <a name="the-basics"></a>Az alapok

### <a name="q-what-is-a-hardware-security-module-hsm"></a>K: Mi az a hardveres biztonsági modul (HSM)?

A hardveres biztonsági modul (HSM) a titkosítási kulcsok védelméhez és kezeléséhez használt fizikai számítástechnikai eszköz. A HSM-ben tárolt kulcsok titkosítási műveletekhez használhatók. A kulcsfontosságú anyagok biztonságban maradnak a jogosulatlan, illetéktelen hozzáférést bizonyító hardveres moduloknál. A HSM csak a hitelesített és engedélyezett alkalmazások számára engedélyezi a kulcsok használatát. A kulcs anyaga soha nem hagyja a HSM védelmi határt.

### <a name="q-what-is-the-azure-dedicated-hsm-offering"></a>K: Mi az Azure dedikált HSM-ajánlat?

Az Azure dedikált HSM egy felhőalapú szolgáltatás, amely olyan Azure-adatközpontokban üzemeltetett HSM biztosít, amelyek közvetlenül kapcsolódnak az ügyfél virtuális hálózatához. Ezek a HSM dedikált [Thales Luna 7 HSM](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms) hálózati készülékek. Ezeket közvetlenül egy ügyfél magánhálózati IP-címére helyezik üzembe, és a Microsoft nem rendelkezik hozzáféréssel a HSM titkosítási funkcióihoz. Csak az ügyfél rendelkezik teljes körű rendszergazdai és titkosítási szabályozással az eszközökön. Az ügyfelek felelősek az eszköz kezeléséért, és a teljes körű tevékenység-naplókat közvetlenül az eszközről szerezhetik be. A dedikált HSM segítségével az ügyfelek megfelelnek a megfelelőségi/szabályozási követelményeknek, például a FIPS 140-2-as szint 3, a HIPAA, a PCI-DSS és a eIDAS és sok más.

### <a name="q-what-hardware-is-used-for-dedicated-hsm"></a>K: Milyen hardvert használ a dedikált HSM?

A Microsoft a Thales-szel együttműködve továbbítja az Azure dedikált HSM szolgáltatást. A használt eszköz a [Thales Luna 7 HSM Model A790](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms). Ez az eszköz nem csupán az [FIPS 140-2 3. szintű](https://csrc.nist.gov/publications/detail/fips/140/2/final) hitelesített belső vezérlőprogramot biztosít, de a kis késleltetésű, nagy teljesítményű és nagy kapacitású 10 partíción keresztül is biztosít. 

### <a name="q-what-is-an-hsm-used-for"></a>K: mit használ a HSM?

A HSM olyan titkosítási kulcsok tárolására szolgálnak, amelyek titkosítási funkciókhoz, például a TLS-hez (szállítási réteg biztonsága), az adattitkosításhoz, a PKI-hez (nyilvános kulcsokra épülő infrastruktúrához), a DRM (digitális Rights Management) és az aláírási dokumentumokhoz

### <a name="q-how-does-dedicated-hsm-work"></a>K: Hogyan működik a dedikált HSM?

Az ügyfelek a PowerShell vagy a parancssori felület használatával HSM bizonyos régiókban. Az ügyfél határozza meg, hogy a HSM melyik virtuális hálózathoz kapcsolódjon, és a kiépítés után a HSM elérhető lesz a kijelölt alhálózatban az ügyfél magánhálózati IP-címe területen lévő hozzárendelt IP-címeken. Ezután az ügyfelek csatlakozhatnak a HSM az SSH használatával a berendezések kezeléséhez és felügyeletéhez, a HSM-ügyfélkapcsolatok beállításához, a HSM inicializálásához, a partíciók létrehozásához, a szerepkörök hozzárendeléséhez, valamint a szerepkörök, például a partíciós tisztviselő, a kriptográfiai vezető és a titkosítási Ezután az ügyfél a Thales által biztosított HSM-ügyféleszközök/SDK/szoftver használatával titkosítási műveleteket hajthat végre az alkalmazásaikban.

### <a name="q-what-software-is-provided-with-the-dedicated-hsm-service"></a>K: milyen szoftvereket biztosít a dedikált HSM szolgáltatás?

A Thales a Microsoft által kiosztott összes szoftvert a HSM-eszköz számára biztosítja. A szoftver a [Thales Customer Support portálon](https://supportportal.thalesgroup.com/csm)érhető el. A dedikált HSM szolgáltatást használó ügyfeleket regisztrálni kell a Thales-támogatáshoz, és rendelkeznie kell egy ügyfél-AZONOSÍTÓval, amely lehetővé teszi a megfelelő szoftverek elérését és letöltését. A támogatott ügyfélszoftver a 7,2-es verzió, amely kompatibilis a FIPS 140-2 3. szint ellenőrzött belső vezérlőprogram-verziójának 7.0.3. 

### <a name="q-what-extra-costs-may-be-incurred-with-dedicated-hsm-service"></a>K: milyen többletköltségek merülhetnek fel a dedikált HSM szolgáltatással?

A dedikált HSM szolgáltatás használata esetén a következő elemek extra költséggel járnak. 
* A dedikált, helyszíni biztonságimásolat-készítő eszköz használata a dedikált HSM szolgáltatással valósítható meg, azonban ez további költségekkel jár, és közvetlenül a Thales-től származik.
* A dedikált HSM 10 partíciós licenccel rendelkezik. Ha egy ügyfél több partíciót is igényel, ez a további, a Thales-ból közvetlenül forrásból származó licencek díját is magában fogja.
* A dedikált HSM hálózati infrastruktúrát (VNET, VPN Gateway stb.) és erőforrásokat (például virtuális gépeket) igényel az eszközök konfigurálásához. Ezek a további erőforrások további költségekkel járnak, és nem szerepelnek a dedikált HSM szolgáltatás díjszabásában.

### <a name="q-does-azure-dedicated-hsm-offer-password-based-and-ped-based-authentication"></a>K: az Azure dedikált HSM a jelszó-és a PED-alapú hitelesítést kínálja?

Jelenleg az Azure dedikált HSM csak jelszó-alapú hitelesítéssel biztosít HSM.

### <a name="q-will-azure-dedicated-hsm-host-my-hsms-for-me"></a>K: az Azure dedikált HSM üzemelteti a HSM?

A Microsoft csak a Thales Luna 7 HSM Model A790 kínálja a dedikált HSM szolgáltatáson keresztül, és nem tud ügyfél által biztosított eszközöket üzemeltetni.

### <a name="q-does-azure-dedicated-hsm-support-payment-pineft-features"></a>K: az Azure dedikált HSM támogatja a fizetési (PIN/EFT) funkciókat?

Az Azure dedikált HSM szolgáltatás a Thales Luna 7 HSM használja. Ezek az eszközök nem támogatják a fizetési HSM-specifikus funkciókat (például a PIN-kódot vagy az EFT-t) vagy a minősítéseket. Ha szeretné, hogy az Azure dedikált HSM szolgáltatás a jövőben is támogassa a fizetési HSM, továbbítsa a Microsoft-fiók képviselőjének visszajelzését.

### <a name="q-which-azure-regions-is-dedicated-hsm-available-in"></a>K: mely Azure-régiók érhetők el a dedikált HSM-ben?

Március 2019-én a dedikált HSM az alább felsorolt 14 régióban érhető el. További régiókat terveztek, és a Microsoft-fiók képviselőjén keresztül is megtekinthetők.

* USA keleti régiója
* USA 2. keleti régiója
* USA nyugati régiója
* USA 2. nyugati régiója
* USA déli középső régiója
* Délkelet-Ázsia
* Kelet-Ázsia
* Közép-India
* Dél-India
* Kelet-Japán
* Nyugat-Japán
* Észak-Európa
* Nyugat-Európa
* Az Egyesült Királyság déli régiója
* Az Egyesült Királyság nyugati régiója
* Közép-Kanada
* Kelet-Kanada
* Kelet-Ausztrália
* Délkelet-Ausztrália
* Észak-Svájc
* Nyugat-Svájc
* USA-beli államigazgatás – Virginia
* USA-beli államigazgatás – Texas

## <a name="interoperability"></a>Együttműködési lehetőség

### <a name="q-how-does-my-application-connect-to-a-dedicated-hsm"></a>K: hogyan kapcsolódik az alkalmazás egy dedikált HSM-hez?

A Thales által biztosított HSM ügyféleszközök/SDK/szoftver használatával titkosítási műveleteket hajthat végre az alkalmazásaiból. A szoftver a [Thales Customer Support portálon](https://supportportal.thalesgroup.com/csm)érhető el. A dedikált HSM szolgáltatást használó ügyfeleket regisztrálni kell a Thales-támogatáshoz, és rendelkeznie kell egy ügyfél-AZONOSÍTÓval, amely lehetővé teszi a megfelelő szoftverek elérését és letöltését.

### <a name="q-can-an-application-connect-to-dedicated-hsm-from-a-different-vnet-in-or-across-regions"></a>K: csatlakozhatnak egy alkalmazás a dedikált HSM-hez egy másik VNET a vagy az egész régióban?

Igen, az [VNET](../virtual-network/virtual-network-peering-overview.md) -társítást kell használnia egy régión belül a virtuális hálózatok közötti kapcsolat létesítéséhez. Régiók közötti kapcsolat esetén [VPN Gatewayt](../vpn-gateway/vpn-gateway-about-vpngateways.md)kell használnia.

### <a name="q-can-i-synchronize-dedicated-hsm-with-on-premises-hsms"></a>K: szinkronizálhatom a dedikált HSM-et helyszíni HSM?

Igen, a helyszíni HSM a dedikált HSM használatával szinkronizálhatja. A [pont-pont típusú VPN vagy pont – hely](../vpn-gateway/vpn-gateway-about-vpngateways.md) kapcsolat használható a helyszíni hálózattal létesített kapcsolat létesítéséhez.

### <a name="q-can-i-encrypt-data-used-by-other-azure-services-using-keys-stored-in-dedicated-hsm"></a>K: titkosítható más Azure-szolgáltatások által használt adat a dedikált HSM-ben tárolt kulcsok használatával?

Nem. Az Azure dedikált HSM csak a virtuális hálózaton belülről érhetők el.

### <a name="q-can-i-import-keys-from-an-existing-on-premises-hsm-to-dedicated-hsm"></a>K: importálhatók kulcsok egy meglévő helyszíni HSM-ből a dedikált HSM-be?

Igen, ha helyszíni Thales Luna 7 HSM rendelkezik. Több módszer is létezik. Tekintse meg a [Thales HSM dokumentációját](https://thalesdocs.com/gphsm/luna/7.2/docs/network/Content/Home_network.htm).

### <a name="q-what-operating-systems-are-supported-by-dedicated-hsm-client-software"></a>K: milyen operációs rendszereket támogat a dedikált HSM-ügyfélszoftver?

* Windows, Linux, Solaris, AIX, HP-UX, FreeBSD
* Virtuális: VMware, Hyper-V, Xen, KVM

### <a name="q-how-do-i-configure-my-client-application-to-create-a-high-availability-configuration-with-multiple-partitions-from-multiple-hsms"></a>K: Hogyan az ügyfélalkalmazás konfigurálásával, hogy magas rendelkezésre állású konfigurációt hozzon létre több HSM több partícióval?

A magas rendelkezésre állás érdekében be kell állítania a HSM-ügyfélalkalmazás konfigurációját, hogy az egyes HSM-partíciókat használja. Tekintse meg a Thales HSM ügyfélszoftver dokumentációját.

### <a name="q-what-authentication-mechanisms-are-supported-by-dedicated-hsm"></a>K: milyen hitelesítési mechanizmusokat támogat a dedikált HSM?

Az Azure dedikált HSM a [Thales Luna 7 HSM Model A790](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms) -eszközöket használja, és támogatja a jelszó-alapú hitelesítést.

### <a name="q-what-sdks-apis-client-software-is-available-to-use-with-dedicated-hsm"></a>K: milyen SDK-kat, API-kat és ügyfélszoftvereket használhat dedikált HSM-sel?

PKCS # 11, Java (JCA/KVV), Microsoft CAPI és CNG, OpenSSL

### <a name="q-can-i-importmigrate-keys-from-luna-56-hsms-to-azure-dedicated-hsms"></a>K: importálható vagy áttelepíthetők kulcsok a Luna 5/6 HSM az Azure dedikált HSM?

Igen. A megfelelő Thales áttelepítési útmutatóért forduljon a Thales képviselőjéhez. 

## <a name="using-your-hsm"></a>A HSM használata

### <a name="q-how-do-i-decide-whether-to-use-azure-key-vault-or-azure-dedicated-hsm"></a>K: Hogyan döntse el, hogy használja-e a Azure Key Vault vagy az Azure dedikált HSM-et?

Az Azure dedikált HSM a HSM-t használó, az Azure-ba helyileg áthelyezett vállalatok számára megfelelő választás. A dedikált HSM egy olyan lehetőséget mutat be, amely minimális módosításokat hajt végre az alkalmazások áttelepítéséhez. Ha egy Azure-beli virtuális gépen vagy webalkalmazásban futó alkalmazás kódjában titkosítási műveleteket hajtanak végre, a dedikált HSM-t használhatják. Általánosságban elmondható, hogy a IaaS-ben (infrastruktúra-szolgáltatásként) futó, zsugorodó burkolt szoftverek a HSM támogató támogatás használhatja a HSM kiosztását, például az Application Gateway-t vagy a Traffic Managert a kulcsnélküli TLS-hez, a ADCS (Active Directory tanúsítványszolgáltatásokhoz) vagy más hasonló PKI-eszközökhöz, a dokumentum-aláíráshoz használt eszközöket/alkalmazásokat, valamint a TDE (transzparens adatbázis-titkosítást), a HSM FŐkulcs SQL Serverával egy EKM (Extensible Key Management) szolgáltató használatával A Azure Key Vault alkalmas a "felhőbe szervezett" alkalmazásokhoz vagy olyan REST-alapú titkosításhoz, ahol az ügyféladatokat a Pásti (platform as Service) vagy az SaaS (szoftveres szolgáltatás), például az Office 365-ügyfél kulcsa, a Azure Information Protection, a Azure Disk Encryption Azure Data Lake Store, az ügyfél által felügyelt kulccsal való titkosítás, az Azure Storage-titkosítás és az ügyfél által felügyelt kulcs segítségével.

### <a name="q-what-usage-scenarios-best-suit-azure-dedicated-hsm"></a>K: milyen használati forgatókönyvek felelnek meg legjobban az Azure dedikált HSM-nek?

Az Azure dedikált HSM a legmegfelelőbb az áttelepítési forgatókönyvekhez. Ez azt jelenti, hogy ha olyan helyszíni alkalmazásokat telepít át az Azure-ba, amelyek már használják a HSM-t. Ez alacsony súrlódású lehetőséget biztosít az Azure-ba való Migrálás során az alkalmazás minimális változásaival. Ha a titkosítási műveletek az Azure-beli virtuális gépen vagy a webalkalmazásban futó alkalmazás kódjában történnek, akkor a dedikált HSM is használható. Általánosságban elmondható, hogy a IaaS-ben (infrastruktúra-szolgáltatásként) futó, a HSM-t támogató, összecsomagolt szoftverek a következőt használják:

* Application Gateway vagy a Traffic Manager a kulcsnélküli TLS-hez
* ADCS (Active Directory tanúsítványszolgáltatás)
* Hasonló PKI-eszközök
* Dokumentumok aláírásához használt eszközök/alkalmazások
* Kód aláírása
* TDE (transzparens adatbázis-titkosítás) konfigurált SQL Server (IaaS) a HSM főkulcsával egy EKM (Extensible Key Management) szolgáltató használatával

### <a name="q-can-dedicated-hsm-be-used-with-office-365-customer-key-azure-information-protection-azure-data-lake-store-disk-encryption-azure-storage-encryption-azure-sql-tde"></a>K: a dedikált HSM használható az Office 365-ügyfél kulcsa, a Azure Information Protection, a Azure Data Lake Store, a lemezes titkosítás, az Azure Storage-titkosítás, az Azure SQL-TDE használatával?

Nem. A dedikált HSM közvetlenül az ügyfél magánhálózati IP-címére van kiépítve, így az nem érhető el más Azure-vagy Microsoft-szolgáltatások számára.

## <a name="administration-access-and-control"></a>Felügyelet, hozzáférés és vezérlés

### <a name="q-does-the-customer-get-full-exclusive-control-over-the-hsms-with-dedicated-"></a>K: az ügyfél teljes kizárólagos felügyeletet kap a HSM, dedikált "?

Igen. Az egyes HSM-készülékek teljes mértékben egyetlen ügyfél számára vannak kijelölve, és az üzembe helyezést követően senki más nem rendelkezik rendszergazdai felügyelettel, és a rendszergazda jelszava megváltozott.

### <a name="q-what-level-of-access-does-microsoft-have-to-my-hsm"></a>K: milyen szintű hozzáférésre van a Microsoft a HSM-hez?

A Microsoft nem rendelkezik rendszergazdai vagy titkosítási szabályozással a HSM-en. A Microsoft az alapszintű telemetria, például a hőmérséklet és az összetevők állapotát a soros portos kapcsolaton keresztül figyeli. Ez lehetővé teszi, hogy a Microsoft proaktív értesítést nyújtson az állapottal kapcsolatos problémákról. Ha szükséges, az ügyfél le tudja tiltani ezt a fiókot.

### <a name="q-what-is-the-tenant-admin-account-microsoft-uses-i-am-used-to-the-admin-user-being-admin-on-thales-luna-hsms"></a>K: Mi az a "bérlői rendszergazda" fiók, amelyet a Microsoft használ, a rendszergazda felhasználó a "rendszergazda" a Thales Luna HSM?

A HSM-eszköz alapértelmezés szerint a rendszergazdai fiókkal, a szokásos alapértelmezett jelszóval rendelkezik. A Microsoft nem tudta használni az alapértelmezett jelszavakat, miközben bármely eszköz olyan készletben található, amelyet az ügyfelek kiépítenek. Ez nem felel meg a szigorú biztonsági követelményeknek. Emiatt erős jelszót állítunk be, amelyet a rendszer a kiépítési időpontban elvet. Emellett a kiépítési idő alatt új felhasználót hozunk létre a "bérlői rendszergazda" nevű rendszergazdai szerepkörben. Ez a felhasználó rendelkezik az alapértelmezett jelszóval, és az ügyfelek az újonnan kiosztott eszközre való első bejelentkezéskor megváltoztatják az első műveletet. Ez a folyamat magas fokú biztonságot nyújt, és megtartja az ügyfelekre vonatkozó egyetlen adminisztratív irányítás iránti ígéretét. Érdemes megjegyezni, hogy a "bérlői rendszergazda" felhasználó felhasználhatja a rendszergazda felhasználói jelszó alaphelyzetbe állítását, ha az ügyfél szívesebben használja ezt a fiókot. 

### <a name="q-can-microsoft-or-anyone-at-microsoft-access-keys-in-my-dedicated-hsm"></a>K: a Microsoft vagy bármely személy hozzáférhet a Microsoft hozzáférési kulcsaihoz a dedikált HSM-ben?

Nem. A Microsoft nem rendelkezik hozzáféréssel az ügyfél által lefoglalt dedikált HSM-ben tárolt kulcsokhoz.

### <a name="q-can-i-upgrade-softwarefirmware-on-hsms-allocated-to-me"></a>K: frissíthetem a szoftvereket és a belső vezérlőprogramot a HSM kiosztott rendszeren?

Az ügyfél teljes körű felügyeleti felügyelettel rendelkezik, beleértve a szoftverek és a belső vezérlőprogram frissítését, ha a különböző belső vezérlőprogram-verziókban bizonyos funkciókra van szükség. A módosítások végrehajtása előtt forduljon a Microsofthoz a frissítéssel kapcsolatban HSMRequest@microsoft.com  

### <a name="q-how-do-i-manage-dedicated-hsm"></a>K: Hogyan a dedikált HSM kezelése?

A dedikált HSM úgy kezelheti, hogy az SSH-val fér hozzájuk.

### <a name="q-how-do-i-manage-partitions-on-the-dedicated-hsm"></a>K: Hogyan a dedikált HSM-ben lévő partíciók kezelése?

A Thales HSM ügyfélszoftver a HSM és a partíciók kezelésére szolgál.

### <a name="q-how-do-i-monitor-my-hsm"></a>K: Hogyan a HSM figyelését?

Az ügyfél a syslog és az SNMP használatával teljes hozzáférést biztosít a HSM-tevékenységek naplóihoz. Az ügyfeleknek be kell állítania egy syslog-kiszolgálót vagy SNMP-kiszolgálót, hogy megkapják a naplókat vagy az eseményeket a HSM.

### <a name="q-can-i-get-full-access-log-of-all-hsm-operations-from-dedicated-hsm"></a>K: teljes hozzáférési naplót kapok a dedikált HSM-ből származó összes HSM-műveletről?

Igen. Naplókat küldhet a HSM-berendezésből egy syslog-kiszolgálónak

## <a name="high-availability"></a>Magas rendelkezésre állás

### <a name="q-is-it-possible-to-configure-high-availability-in-the-same-region-or-across-multiple-regions"></a>K: lehet magas rendelkezésre állást konfigurálni ugyanabban a régióban vagy több régióban?

Igen. A magas rendelkezésre állás konfigurálása és beállítása a Thales által biztosított HSM-ügyfélszoftverben történik. A helyek közötti vagy pont-pont típusú VPN-kapcsolattal azonos VNET vagy más, az adott régióban vagy régiókban található HSM, illetve a VNET kapcsolódó helyszíni HSM azonos magas rendelkezésre állású konfigurációhoz adhatók hozzá. Fontos megjegyezni, hogy ez csak a kulcsfontosságú anyagokat szinkronizálja, és nem adott konfigurációs elemeket, például a szerepköröket.

### <a name="q-can-i-add-hsms-from-my-on-premises-network-to-a-high-availability-group-with-azure-dedicated-hsm"></a>K: Hozzáadhatok HSM a helyszíni hálózatról egy magas rendelkezésre állású csoportba az Azure dedikált HSM használatával?

Igen. Meg kell felelniük a [Thales Luna 7 HSM](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms) magas rendelkezésre állási követelményeinek.

### <a name="q-can-i-add-luna-56-hsms-from-on-premises-networks-to-a-high-availability-group-with-azure-dedicated-hsm"></a>K: hozzáadhatom a Luna 5/6 HSM a helyszíni hálózatokból egy magas rendelkezésre állású csoportba az Azure dedikált HSM használatával?

Nem.

### <a name="q-how-many-hsms-can-i-add-to-the-same-high-availability-configuration-from-one-single-application"></a>K: hány HSM adhatok hozzá ugyanahhoz a magas rendelkezésre állású konfigurációhoz egyetlen alkalmazásból?

egy HEKTÁRos csoport 16 tagja rendelkezik a teljes körű teszteléssel, amely kiváló eredménnyel jár.

## <a name="support"></a>Támogatás

### <a name="q-what-is-the-sla-for-dedicated-hsm-service"></a>K: mi a dedikált HSM szolgáltatás SLA-ja?

A dedikált HSM szolgáltatáshoz nem biztosítunk meghatározott rendelkezésre állási garanciát. A Microsoft biztosítja az eszköz hálózati szintű elérését, ezért a standard Azure hálózati SLA-kat is alkalmazza.

### <a name="q-how-are-the-hsms-used-in-azure-dedicated-hsm-protected"></a>K: hogyan használhatók az Azure dedikált HSM által védett HSM?

Az Azure-adatközpontok kiterjedt fizikai és eljárási biztonsági ellenőrzésekkel rendelkeznek. A dedikált HSM mellett az adatközpont egy további korlátozott hozzáférési területe is található. Ezek a területek további fizikai hozzáférés-vezérléssel és videokamera-felügyelettel rendelkeznek a fokozott biztonsághoz.

### <a name="q-what-happens-if-there-is-a-security-breach-or-hardware-tampering-event"></a>K: mi történik, ha biztonsági vagy hardverhiba-hamisítási esemény van?

A dedikált HSM-szolgáltatás a [Thales Luna 7 HSM](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms) -készülékeket használja. Ezek az eszközök támogatják a fizikai és a logikai illetéktelen módosítások észlelését. Ha a rendszer soha nem módosít eseményt, a HSM automatikusan nulla értékűek lesznek.

### <a name="q-how-do-i-ensure-that-keys-in-my-dedicated-hsms-are-not-lost-due-to-error-or-a-malicious-insider-attack"></a>K: Hogyan ellenőrizze, hogy a dedikált HSM lévő kulcsok nem vesznek-e el hiba vagy rosszindulatú belső támadás miatt?

Erősen ajánlott helyszíni HSM biztonsági mentési eszközt használni a HSM rendszeres időközönkénti biztonsági mentésének elvégzéséhez a vész-helyreállítás érdekében. Egy HSM biztonsági mentési eszközhöz csatlakoztatott helyszíni munkaállomáshoz egyenrangú vagy helyek közötti VPN-kapcsolatot kell használnia.

### <a name="q-how-do-i-get-support-for-dedicated-hsm"></a>K: Hogyan támogatást kap a dedikált HSM-hez?

A támogatást a Microsoft és a Thales egyaránt támogatja.  Ha problémája van a hardverrel vagy a hálózati hozzáféréssel, adjon meg egy támogatási kérést a Microsofttal, és ha problémája van a HSM-konfigurációval, a szoftverekkel és az alkalmazás-fejlesztéssel kapcsolatos támogatási kérést a Thales. Ha meghatározatlan probléma merül fel, adjon meg egy támogatási kérést a Microsofttal, majd a Thales igény szerint is elvégezheti. 

### <a name="q-how-do-i-get-the-client-software-documentation-and-access-to-integration-guidance-for-the-thales-luna-7-hsm"></a>K: Hogyan az ügyfélszoftvert, a dokumentációt és az integrációs útmutatóhoz való hozzáférést a Thales Luna 7 HSM-hez?

A szolgáltatáshoz való regisztráció után meg kell adni egy Thales-ügyfél-azonosítót, amely lehetővé teszi a regisztrációt a Thales-ügyfél támogatási portálján. Ez lehetővé teszi az összes szoftver és dokumentáció elérését, valamint a támogatási kérések közvetlen Thales való engedélyezését.

### <a name="q-if-there-is-a-security-vulnerability-found-and-a-patch-is-released-by-thales-who-is-responsible-for-upgradingpatching-osfirmware"></a>K: ha talál biztonsági rést, és a Thales kiadott egy javítást, ki felelős az operációs rendszer/belső vezérlőprogram frissítéséhez/javításához?

A Microsoft nem tud csatlakozni az ügyfeleknek kiosztott HSM. Az ügyfeleknek frissíteniük kell a HSM, és javítaniuk kell a javítást.

### <a name="q-what-if-i-need-to-reboot-my-hsm"></a>K: mi a teendő, ha újra kell indítani a HSM-et?

A HSM parancssori újraindítási lehetőséggel rendelkezik, azonban olyan problémák merülnek fel, amelyekben az újraindítás időnként leáll, és ezért javasolt a legbiztonságosabb újraindításra, ha a Microsofttal egy támogatási kérést küld, hogy az eszköz fizikailag újrainduljon. 

## <a name="cryptography-and-standards"></a>Titkosítás és szabványok

### <a name="q-is-it-safe-to-store-encryption-keys-for-my-most-important-data-in-dedicated-hsm"></a>K: biztonságos a legfontosabb adathoz tartozó titkosítási kulcsok tárolása a dedikált HSM-ben?

Igen, a dedikált HSM-céltartalékok Thales a Luna 7 HSM, amelyek az [FIPS 140-2-es szint-3](https://csrc.nist.gov/publications/detail/fips/140/2/final) hitelesítéssel rendelkeznek. 

### <a name="q-what-cryptographic-keys-and-algorithms-are-supported-by-dedicated-hsm"></a>K: milyen titkosítási kulcsokat és algoritmusokat támogat a dedikált HSM?

A dedikált HSM szolgáltatás kiépíti a Luna 7 HSM-es készülékekre vonatkozó Thales. Számos kriptográfiai kulcsot és algoritmust támogatnak, többek között a következőket: Full Suite B-támogatás

* Aszimmetrikus
  * RSA
  * DSA
  * Diffie-Hellman
  * Elliptikus görbe
  * Titkosítás (ECDSA, ECDH, Ed25519, ECIES) névvel ellátott, felhasználó által definiált és Brainpool görbékkel, KCDSA
* Szimmetrikus
  * AES – GCM
  * Triple DES
  * DES
  * ARIA, VETŐMAG
  * RC2
  * RC4
  * RC5
  * CAST
  * Kivonatoló/Message Digest/HMAC: SHA-1, SHA-2, SM3
  * Kulcs származtatása: SP 800-108 számláló üzemmód
  * Fő körbefuttatás: SP 800 – 38F
  * Véletlenszám-generálás: FIPS 140-2 jóváhagyott DRBG (SP 800-90 CTR mód), amely megfelel a BSI DRG. 4

### <a name="q-is-dedicated-hsm-fips-140-2-level-3-validated"></a>K: a dedikált HSM FIPS 140-2 3. szint érvényesítve van?

Igen. A dedikált HSM szolgáltatás kiépíti a [Thales Luna 7 HSM Model A790](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms) -berendezéseket, amelyek [FIPS 140-2 3. szintűek](https://csrc.nist.gov/publications/detail/fips/140/2/final) .

### <a name="q-what-do-i-need-to-do-to-make-sure-i-operate-dedicated-hsm-in-fips-140-2-level-3-validated-mode"></a>K: mit kell tennem ahhoz, hogy a dedikált HSM-et az FIPS 140-2 3. szintű, ellenőrzött módban üzemeltetem?

A dedikált HSM-szolgáltatás a Luna 7 HSM-es készülékekre vonatkozó Thales. Ezek az eszközök a FIPS 140-2 3. szintű hitelesített HSM. Az alapértelmezett telepített konfiguráció, operációs rendszer és belső vezérlőprogram szintén FIPS-hitelesítéssel van elválasztva. Nem kell semmilyen műveletet végrehajtania a FIPS 140-2 3. szintű megfelelőségének megfelelően.

### <a name="q-how-does-a-customer-ensure-that-when-an-hsm-is-deprovisioned-all-the-key-material-is-wiped-out"></a>K: Hogyan gondoskodik az ügyfél arról, hogy ha egy HSM kiépítése megtörtént, a rendszer törli az összes lényeges anyagot?

A megszüntetés megkezdése előtt az ügyfélnek a HSM-Thales megadott HSM-eszközök használatával nulláznie kell a HSM-et.

## <a name="performance-and-scale"></a>Teljesítmény és méret

### <a name="q-how-many-cryptographic-operations-are-supported-per-second-with-dedicated-hsm"></a>K: hány titkosítási műveletet támogatott másodpercenként a dedikált HSM-sel?

A dedikált HSM-rendelkezések Thales Luna 7 HSM. Íme egy összefoglaló az egyes műveletek maximális teljesítményéről: 

* RSA-2048:10 000 tranzakció másodpercenként
* ECC-P256:20 000 tranzakció/másodperc
* AES-GCM: 17 000 tranzakció/másodperc

### <a name="q-how-many-partitions-can-be-created-in-dedicated-hsm"></a>K: hány partíciót lehet létrehozni a dedikált HSM-ben?

A [Thales Luna 7 HSM-modell A790](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms) a szolgáltatás díja 10 partícióra vonatkozó licencet tartalmaz. Az eszköz legfeljebb 100 partíciót tartalmaz, és az ehhez a korláthoz tartozó partíciók további licencelési költségekkel járnak, és szükség van egy új licencfájl telepítésére az eszközön.

### <a name="q-how-many-keys-can-be-supported-in-dedicated-hsm"></a>K: hány kulcsot lehet támogatni a dedikált HSM-ben?

A kulcsok maximális száma a rendelkezésre álló memória függvénye. A Thales Luna 7 Model A790 használatban van 32MB. Aszimmetrikus kulcsok használata esetén a következő számok is érvényesek a fő párokra.

* RSA-2048-19 000
* ECC-P256-91 000

A kapacitás a kulcs létrehozási sablonjában és a partíciók számában meghatározott fő attribútumoktól függően változhat.
