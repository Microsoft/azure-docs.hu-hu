---
title: Szabályzatok biztonságossá és használata
description: Tudnivalók az Azure-beli virtuális gépek biztonságával és szabályzatával kapcsolatban.
author: cynthn
ms.service: virtual-machines
ms.subservice: security
ms.workload: infrastructure-services
ms.date: 11/27/2018
ms.author: cynthn
ms.topic: conceptual
ms.openlocfilehash: 840045da33938d4c1cd725fd5a99bf1b8014f6b1
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107748465"
---
# <a name="secure-and-use-policies-on-virtual-machines-in-azure"></a>Szabályzatok biztonsága és használata virtuális gépeken az Azure-ban

Fontos, hogy a virtuális gép (VM) biztonságban legyen a futtatott alkalmazások számára. A virtuális gépek biztonságossá tétele magában foglalhat egy vagy több Azure-szolgáltatást és -funkciót, amelyek a virtuális gépek biztonságos hozzáférését és az adatok biztonságos tárolását biztosítják. Ez a cikk olyan információkat tartalmaz, amelyek segítségével biztonságban tarthatja virtuális gépét és alkalmazásait.

## <a name="antimalware"></a>Kártevőirtó

A felhőalapú környezetek modern fenyegetési környezete dinamikus, így nő a nyomás a hatékony védelem fenntartására, hogy megfeleljen a megfelelőségi és biztonsági követelményeknek. [Microsoft Antimalware Azure-hoz](../security/fundamentals/antimalware.md) való telepítés ingyenes valós idejű védelmi képesség, amely segít a vírusok, kémprogramok és más kártékony szoftverek azonosításában és eltávolításában. A riasztások konfigurálhatóak úgy, hogy értesítést adjanak, ha az ismert kártevő vagy nemkívánatos szoftverek megkísérlik telepíteni magát vagy futni a virtuális gépen. Linux vagy Windows Server 2008 rendszert futtató virtuális gépeken nem támogatott.

## <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](../security-center/security-center-introduction.md) segít megelőzni, észlelni és reagálni a virtuális gépeket fenyegető fenyegetésekre. Security Center azure-előfizetések integrált biztonsági monitorozását és szabályzatkezelését biztosítja, segít észlelni az egyébként észrevétlenül maradó fenyegetéseket, és a biztonsági megoldások széles ökoszisztémáját használja.

Security Center a virtuális gép üzemelő példányán alkalmazható az azure-beli virtuális gépek bejövő forgalmának zárolása, ezáltal csökken a támadásoknak való kitettség, miközben könnyű hozzáférést biztosít a virtuális gépekhez való csatlakozáshoz, amikor szükség van rá. Ha az "időben" beállítás engedélyezve van, és a felhasználó hozzáférést kér egy virtuális géphez, a Security Center ellenőrzi, hogy a felhasználó milyen engedélyekkel rendelkezik a virtuális géphez. Ha megfelelő engedélyekkel rendelkeznek, a rendszer jóváhagyja a kérést, és Security Center automatikusan úgy konfigurálja a hálózati biztonsági csoportokat (NSG-ket), hogy korlátozott ideig engedélyezték a bejövő forgalmat a kiválasztott portokra. Az idő lejárta után a Security Center visszaállítja az NSG-ket a korábbiakba. 

## <a name="encryption"></a>Titkosítás

A felügyelt lemezekhez két titkosítási módszer áll rendelkezésre. Titkosítás az operációs rendszer szintjén, amely Azure Disk Encryption titkosítás platformszinten, amely kiszolgálóoldali titkosítás.

### <a name="server-side-encryption"></a>Kiszolgálóoldali titkosítás

Az Azure-beli felügyelt lemezek alapértelmezés szerint automatikusan titkosítják az adatokat, amikor a felhőben tőrzik. A kiszolgálóoldali titkosítás védi az adatokat, és segít a szervezeti biztonsági és megfelelőségi kötelezettségvállalások teljesítésében. Az Azure felügyelt lemezei 256 bites [AES-titkosítással](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)transzparensen titkosítják az adatokat, amely az egyik legerősebb elérhető blokktitkosítás, és FIPS 140-2 szabványnak megfelelő.

A titkosítás nem befolyásolja a felügyelt lemezek teljesítményét. A titkosítás nem jár többletköltséggel.

A felügyelt lemez titkosítása platform által felügyelt kulcsokkal is kezelhető, de saját kulcsokkal is kezelhető. Ha úgy dönt, hogy a titkosítást a saját kulcsokkal kezeli, megadhat egy felhasználó által kezelt kulcsot, amely *a* felügyelt lemezeken található összes adat titkosításához és visszafejtéséhez használható. 

A kiszolgálóoldali titkosítással kapcsolatos további információkért tekintse meg a Windows vagy [Linux](./disk-encryption.md) rendszeren elérhető [cikkeket.](./disk-encryption.md)

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

A Windows rendszerű virtuális [gépek](windows/disk-encryption-overview.md) és [a Linux](linux/disk-encryption-overview.md) rendszerű virtuális gépek fokozott biztonsága és megfelelősége érdekében az Azure-beli virtuális lemezek titkosíthatóak. A Windows rendszerű virtuális gépeken lévő virtuális lemezek titkosítása a BitLocker használatával történik. A Linux rendszerű virtuális gépeken lévő virtuális lemezek titkosítva vannak a dm-crypt használatával. 

Az Azure-beli virtuális lemezek titkosítása díjmentes. A titkosítási kulcsokat a Azure Key Vault szoftveres védelemmel tárolják, vagy a kulcsokat a FIPS 140-2 2. szintű 2. szabványnak megfelelő hardveres biztonsági modulokban (HSM-ekkel) importálhatja vagy hozhatja létre. Ezek a titkosítási kulcsok a virtuális géphez csatolt virtuális lemezek titkosítására és visszafejtéséhez használhatók. Ön továbbra is ön irányíthatja ezeket a titkosítási kulcsokat, és naplózhatja azok használatát. A Azure Active Directory szolgáltatásnév biztonságos mechanizmust biztosít a titkosítási kulcsok kiállításához a virtuális gépek be- és kikapcsolt állapotban.

## <a name="key-vault-and-ssh-keys"></a>Key Vault és SSH-kulcsok

A titkos kulcsok és tanúsítványok erőforrásokként modellelve és a következő által [Key Vault.](../key-vault/general/basic-concepts.md) Az azure Azure PowerShell kulcstartókat hozhat létre [Windows](windows/key-vault-setup.md) rendszerű virtuális gépekhez és az Azure [CLI-vel Linux rendszerű virtuális gépekhez.](linux/key-vault-setup.md) A titkosításhoz kulcsokat is létrehozhat.

A kulcstartó-hozzáférési szabályzatok külön biztosítják a kulcsokra, titkos kulcsokra és tanúsítványokra vonatkozó engedélyeket. Egy adott felhasználó számára hozzáférést engedélyezhet például kizárólag a kulcsokhoz, de a titkos kulcsokhoz nem. A kulcsok, titkos kulcsok és tanúsítványok hozzáférése ugyanakkor tárolószinten engedélyezett. Más szóval a [kulcstartó-hozzáférési szabályzat](../key-vault/general/security-overview.md) nem támogatja az objektumszintű engedélyeket.

Amikor virtuális gépekhez csatlakozik, nyilvános kulcsú titkosítást kell használnia a bejelentkezés biztonságosabb módja érdekében. Ez a folyamat magában foglalja egy nyilvános és titkos kulcscserét a Secure Shell (SSH) paranccsal, amely felhasználónév és jelszó helyett saját magát hitelesíti. A jelszavak ki vannak téve a találgatásos támadásoknak, különösen az internetes virtuális gépek, például webkiszolgálók esetén. A Secure Shell- (SSH-) kulcspárokkal létrehozhat egy [Linux](linux/mac-create-ssh-keys.md) rendszerű virtuális gépet, amely SSH-kulcsokat használ a hitelesítéshez, így nincs szükség jelszavakra a bejelentkezéshez. SSH-kulcsokkal windowsos virtuális gépről [linuxos](linux/ssh-from-windows.md) virtuális géphez is csatlakozhat.

## <a name="managed-identities-for-azure-resources"></a>Azure-erőforrások felügyelt identitásai

A felhőalapú alkalmazások készítése során általános kihívást jelenti a hitelesítő adatok a kódban való kezelése, amelyekkel az alkalmazás magát a felhőalapú szolgáltatásokban hitelesíti. A hitelesítő adatok biztonságának megőrzése fontos feladat. Ideális esetben a hitelesítő adatok soha nem jelennek meg a fejlesztői munkaállomásokon, és a verziókövetési rendszerbe sem kerülnek be. Az Azure Key Vault módot kínál a hitelesítő adatok, titkos kódok és egyéb kulcsok biztonságos tárolására, azonban a kódnak hitelesítenie kell magát a Key Vaultban az adatok lekéréséhez. 

Az Azure Active Directory (Azure AD) Azure-erőforrások felügyelt identitásai szolgáltatása megoldást kínál erre a problémára. A szolgáltatás automatikusan felügyelt identitást biztosít az Azure-szolgáltatások számára az Azure AD-ben. Ezzel az identitással bármely, az Azure AD-hitelesítést támogató szolgáltatásban, többek között a Key Vaultban is elvégezheti a hitelesítést anélkül, hogy a hitelesítő adatok a kódban szerepelnének.  A virtuális gépen futó kód két végpontról kérhet le jogkivonatot, amelyek csak a virtuális gépen belülről érhetők el. A szolgáltatással kapcsolatos részletesebb információkért tekintse meg az [Azure-erőforrások felügyelt identitásának áttekintését](../active-directory/managed-identities-azure-resources/overview.md) ismertető oldalt.   

## <a name="policies"></a>Házirendek

[Az Azure-szabályzatokkal](../governance/policy/overview.md) meghatározhatja a kívánt viselkedést a szervezet [Windows](./windows/policy.md) rendszerű virtuális gépei és Linux rendszerű [virtuális gépei számára.](./linux/policy.md) Szabályzatok használatával a szervezetek különböző konvenciókat és szabályokat kényszeríthet ki a teljes vállalatban. A kívánt viselkedés kényszerítése segíthet csökkenteni a kockázatokat, miközben hozzájárul a szervezet sikeréhez.

## <a name="azure-role-based-access-control"></a>Azure-beli szerepköralapú hozzáférés-vezérlés

Az [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../role-based-access-control/overview.md)használatával elkülönítheti a csapaton belüli feladatokat, és csak olyan mennyiségű hozzáférést adhat a virtuális gép felhasználóinak, amelyekre a feladataik elvégzéséhez szükségük van. Ahelyett, hogy mindenki számára korlátlan engedélyeket ad a virtuális gépen, csak bizonyos műveleteket engedélyezhet. A virtuális gép hozzáférés-vezérlését [](../role-based-access-control/role-assignments-portal.md)a következő Azure Portal konfigurálhatja az [Azure CLI](/cli/azure/role)vagy[a Azure PowerShell.](../role-based-access-control/role-assignments-powershell.md)


## <a name="next-steps"></a>Következő lépések
- Kövesse a virtuális gépek biztonságának Linux vagy [Windows](../security/fundamentals/overview.md) rendszeren Azure Security Center használatával történő figyelése [lépéseit.](/previous-versions/azure/virtual-machines/tutorial-azure-security)