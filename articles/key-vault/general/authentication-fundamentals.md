---
title: A Azure Key Vault hitelesítési alapjai
description: Tudnivalók a Key Vault hitelesítési modelljének működéséről
author: ShaneBala-keyvault
ms.author: sudbalas
ms.date: 09/25/2020
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.openlocfilehash: 25f00024fb7371fd08bf6c4ceec3177cfaca029b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103572808"
---
# <a name="key-vault-authentication-fundamentals"></a>Key Vault – hitelesítési alapok

A Azure Key Vault lehetővé teszi az alkalmazások hitelesítő adatainak, például a titkok, kulcsok és tanúsítványok biztonságos tárolását és kezelését egy központi és biztonságos Felhőbeli tárházban. Key Vault szükségtelenné teszi a hitelesítő adatok tárolását az alkalmazásokban. Az alkalmazások a hitelesítő adatok lekéréséhez futtatás közben Key Vault a hitelesítést.

Rendszergazdaként szigorúan szabályozhatja, hogy mely felhasználók és alkalmazások férhetnek hozzá a kulcstartóhoz, és korlátozhatja és naplózhatja az általuk végrehajtott műveleteket. Ez a dokumentum ismerteti a Key Vault-hozzáférési modell alapvető fogalmait. Az útmutató bevezető szintű ismereteket nyújt, és bemutatja, hogyan hitelesítheti a felhasználót vagy alkalmazást a Key vaultban a kezdéstől a befejezésig.

## <a name="required-knowledge"></a>Szükséges ismeretek

Ez a dokumentum azt feltételezi, hogy már ismeri a következő fogalmakat. Ha nem ismeri ezeket a fogalmakat, a továbblépés előtt kövesse a Súgó hivatkozásait.

* Azure Active Directory [hivatkozás](../../active-directory/fundamentals/active-directory-whatis.md)
* Rendszerbiztonsági tag [hivatkozása](./authentication.md#app-identity-and-security-principals)

## <a name="key-vault-configuration-steps-summary"></a>Key Vault konfigurációs lépések összegzése

1. A felhasználó vagy alkalmazás regisztrálása Azure Active Directory rendszerbiztonsági tagként.
1. Konfigurálja a rendszerbiztonsági tag szerepkör-hozzárendelését Azure Active Directory-ben.
1. Key Vault-hozzáférési szabályzatok konfigurálása a rendszerbiztonsági tag számára.
1. Key Vault tűzfal hozzáférésének konfigurálása a kulcstartóhoz (nem kötelező).
1. Tesztelje a rendszerbiztonsági tag képességét a Key Vault elérésére.

## <a name="register-a-user-or-application-in-azure-active-directory-as-a-security-principal"></a>Felhasználó vagy alkalmazás regisztrálása Azure Active Directory rendszerbiztonsági tagként

Amikor egy felhasználó vagy alkalmazás a Key vaultnak küld egy kérést, a kérést először Azure Active Directory hitelesítenie kell. Ahhoz, hogy ez működjön, a felhasználónak vagy alkalmazást Azure Active Directory rendszerbiztonsági tagként kell regisztrálni.

Az alábbi dokumentációs hivatkozásokat követve megismerheti, hogyan regisztrálhat egy felhasználót vagy alkalmazást a Azure Active Directoryban.
**Győződjön meg arról, hogy jelszót hoz létre a felhasználói regisztrációhoz, valamint az ügyfél titkos vagy ügyféltanúsítvány-alapú hitelesítő adatait az alkalmazásokhoz.**

* Felhasználó regisztrálása Azure Active Directory [hivatkozásban](../../active-directory/fundamentals/add-users-azure-active-directory.md)
* Alkalmazás regisztrálása Azure Active Directory [hivatkozásban](../../active-directory/develop/quickstart-register-app.md)

## <a name="assign-your-security-principal-a-role"></a>Rendszerbiztonsági tag szerepkör kiosztása

Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) segítségével engedélyeket rendelhet a rendszerbiztonsági tagokhoz. Ezeket az engedélyeket szerepkör-hozzárendeléseknek nevezzük.

A Key Vault esetében ezek a szerepkör-hozzárendelések határozzák meg a rendszerbiztonsági tag hozzáférési szintjét a Key Vault felügyeleti síkjával (más néven a vezérlési síkon). Ezek a szerepkör-hozzárendelések nem biztosítanak közvetlen hozzáférést az adatsíkok titkos kulcsaihoz, de hozzáférést biztosítanak a Key Vault tulajdonságainak kezeléséhez. Például egy **olvasó szerepkörhöz** rendelt felhasználó vagy alkalmazás nem módosítható a Key Vault tűzfalbeállítások módosításával, míg a **közreműködő szerepkörhöz** rendelt felhasználó vagy alkalmazás módosításokat végezhet. Egyik szerepkör sem lesz közvetlen hozzáférése a titkokkal, kulcsokkal és tanúsítványokkal kapcsolatos műveletekhez, például az értékek létrehozásához vagy lekéréséhez, amíg hozzá nem fér a Key Vault adatsíkjával. Ezt a következő lépésben tárgyaljuk.

>[!IMPORTANT]
> Habár a közreműködő vagy a tulajdonos szerepkörrel rendelkező felhasználók nem férhetnek hozzá a Key vaultban tárolt titkos kulcsokhoz, a közreműködői és a tulajdonosi szerepkörökhöz, engedélyeket biztosítanak a Key vaultban tárolt titkos kódokhoz való hozzáférési szabályzatok hozzáadásához vagy eltávolításához. Ezért a szerepkör-hozzárendelésekkel rendelkező felhasználók hozzáférést biztosíthatnak a kulcstartóban lévő titkos kulcsokhoz. Ezért azt javasoljuk, hogy csak a rendszergazdák férhessenek hozzá a közreműködő vagy a tulajdonos szerepköreihez. Azoknak a felhasználóknak és alkalmazásoknak, amelyeknek csak a Key vaultból kell beolvasniuk a titkot, meg kell adni az olvasó szerepkört. **További részleteket a következő szakaszban talál.**

>[!NOTE]
> Ha Azure Active Directory bérlői szinten rendel hozzá egy szerepkör-hozzárendelést egy felhasználóhoz, ez az engedély a hozzárendelés hatókörén belül minden előfizetésre, erőforrás-csoportra és erőforrásra kiterjed. Ahhoz, hogy a legkevesebb jogosultsággal rendelkező rendszerbiztonsági tag legyen, részletesebben is elvégezheti ezt a szerepkör-hozzárendelést. Például hozzárendelhet egy felhasználót egy olvasói szerepkörhöz az előfizetés szintjén, és egy tulajdonosi szerepkört egyetlen kulcstartóhoz. Az előfizetés, az erőforráscsoport vagy a kulcstartó identitás-hozzáférés-kezelés (IAM) beállításainak megadásával részletesebben is elvégezheti a szerepkör-hozzárendelést.

* További információ az Azure roles [linkről](../../role-based-access-control/built-in-roles.md)
* További információ a szerepkör [-hozzárendelések hozzárendeléséről](../../role-based-access-control/role-assignments-portal.md) vagy eltávolításáról

## <a name="configure-key-vault-access-policies-for-your-security-principal"></a>Key Vault-hozzáférési szabályzatok konfigurálása a rendszerbiztonsági tag számára

Mielőtt hozzáférést adna a felhasználóknak és az alkalmazásoknak a Key Vault eléréséhez, fontos megérteni a kulcstartón végrehajtható különböző típusú műveleteket. A Key Vault-műveletek két fő típusa, a felügyeleti sík (más néven a vezérlési sík) és az adatsíkok műveletei.

Ez a táblázat a felügyeleti sík és az adatsík által vezérelt különböző műveletekre mutat be néhány példát. A Key Vault tulajdonságait módosító műveletek a felügyeleti sík műveletei. A Key vaultban tárolt titkos kulcsok értékét megváltoztató vagy lekérő műveletek adatsík-műveletek.

|Felügyeleti sík műveletei (példák)|Adatsík-műveletek (példák)|
| --- | --- |
| Key Vault létrehozása | Kulcs, titok, tanúsítvány létrehozása
| Key Vault törlése | Kulcs, titok, tanúsítvány törlése
| Key Vault szerepkör-hozzárendelések hozzáadása vagy eltávolítása | Kulcsok, titkos kódok és tanúsítványok értékének listázása és lekérése
| Key Vault hozzáférési szabályzatok hozzáadása vagy eltávolítása | Kulcsok, titkos kódok és tanúsítványok biztonsági mentése és visszaállítása
| Key Vault tűzfal beállításainak módosítása | Kulcsok, titkos kódok és tanúsítványok megújítása
| Key Vault helyreállítási beállítások módosítása | A helyreállított kulcsok, titkok és tanúsítványok kiürítése vagy helyreállítása
| Key Vault diagnosztikai naplók beállításainak módosítása

### <a name="management-plane-access--azure-active-directory-role-assignments"></a>Felügyeleti sík hozzáférési & Azure Active Directory szerepkör-hozzárendelések

Azure Active Directory szerepkör-hozzárendelések hozzáférést biztosítanak a felügyeleti sík műveleteinek elvégzéséhez a kulcstartón. Ezt a hozzáférést általában a felhasználók számára biztosítjuk, nem pedig az alkalmazásoknak. A felhasználók szerepkör-hozzárendelésének módosításával korlátozhatja, hogy a felhasználók milyen felügyeleti síkon hajthatnak végre műveleteket.

Ha például egy felhasználóhoz Key Vault olvasói szerepkört rendel egy felhasználóhoz, akkor a kulcstároló tulajdonságai megtekinthetők, például a hozzáférési szabályzatok, de nem teszik lehetővé a módosítások elvégzését. Felhasználó kiosztása esetén a tulajdonosi szerepkör lehetővé teszi számukra a teljes hozzáférést a Key Vault felügyeleti síkja beállításainak módosításához.

A szerepkör-hozzárendelések a Key Vault Access Control (IAM) panelen vannak szabályozva. Ha azt szeretné, hogy egy adott felhasználó hozzáférjen az olvasóhoz vagy több Key Vault-erőforrás rendszergazdájához, létrehozhat egy szerepkör-hozzárendelést a tár, az erőforráscsoport vagy az előfizetés szintjén, és a szerepkör-hozzárendelés a hozzárendelés hatókörében lévő összes erőforráshoz hozzá lesz adva.

A Key vaultban tárolt kulcsok, titkos kódok és tanúsítványok műveletekhez való hozzáférését kétféleképpen lehet felvenni.

### <a name="data-plane-access-option-1-classic-key-vault-access-policies"></a>1. adatsíkon való hozzáférési lehetőség: klasszikus Key Vault hozzáférési szabályzatok

A Key Vault-hozzáférési házirendek biztosítják a felhasználók és az alkalmazások számára, hogy adatsík-műveleteket végezzenek a kulcstartón.

> [!NOTE]
> Ez a hozzáférési modell nem kompatibilis az Azure RBAC a Key vaulthoz (2. lehetőség) az alábbiakban dokumentált módon. Ki kell választania egyet. Ha a Key Vault hozzáférési szabályzat fülére kattint, lehetősége lesz a kijelölés elvégzésére.

A klasszikus hozzáférési szabályzatok részletesek, ami azt jelenti, hogy engedélyezheti vagy megtagadhatja, hogy az egyes felhasználók vagy alkalmazások képesek legyenek egyéni műveleteket végezni a kulcstartón belül. Íme, néhány példa:

* Az 1. biztonsági tag bármely kulcsfontosságú műveletet végrehajthat, de nem végezhet titkos vagy tanúsítvány-műveletet.
* A 2. biztonsági tag megtekintheti és beolvashatja az összes kulcsot, titkot és tanúsítványt, de nem végezhet létrehozási, törlési vagy megújítási műveleteket.
* A 3. biztonsági tag képes az összes titok biztonsági mentésére és helyreállítására, de nem tudja beolvasni a titkok értékét.

A klasszikus hozzáférési szabályzatok azonban nem engedélyezik a felhasználónkénti szintű engedélyeket, és a hozzárendelt engedélyek az egyes kulcstartók hatókörére vonatkoznak. Ha például a "titkos kulcs lekérése" hozzáférési házirend engedélyt ad egy rendszerbiztonsági tag számára egy adott kulcstartóban, a rendszerbiztonsági tag képes az adott kulcstartón belüli összes titok beszerzésére. Azonban ez a "titkos kód" engedély nem terjeszthető ki automatikusan más kulcstartóra, és explicit módon kell hozzárendelni őket.

> [!IMPORTANT]
> A klasszikus kulcstartó-hozzáférési házirendek és Azure Active Directory szerepkör-hozzárendelések egymástól függetlenek. A rendszerbiztonsági tag "közreműködő" szerepkörének előfizetési szinten való kiosztása nem teszi lehetővé automatikusan, hogy a rendszerbiztonsági tag az előfizetés hatókörén belül minden kulcstartón adatsík-műveleteket végezzen. A rendszerbiztonsági tag számára továbbra is meg kell adni, vagy hozzáférési szabályzatot kell megadni az adatsík-műveletek végrehajtásához.

### <a name="data-plane-access-option-2--azure-rbac-for-key-vault-preview"></a>Adatsík hozzáférési lehetőség 2: Azure RBAC for Key Vault (előzetes verzió)

A Key Vault adatsíkjával való hozzáférés engedélyezésének új módja az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használata a Key vaulthoz.

> [!NOTE]
> Ez a hozzáférési modell nem kompatibilis a fentiekben bemutatott Key Vault klasszikus hozzáférési szabályzatokkal. Ki kell választania egyet. Ha a Key Vault hozzáférési szabályzat fülére kattint, lehetősége lesz a kijelölés elvégzésére.

Key Vault a szerepkör-hozzárendelések olyan Azure beépített szerepkör-hozzárendelések, amelyek a kulcsok, titkos kódok és tanúsítványok eléréséhez szükséges engedélyek közös készleteit foglalják magukban. Ez az engedélyezési modell olyan további képességeket is lehetővé tesz, amelyek nem érhetők el a klasszikus kulcstartó-hozzáférési házirend modelljében.

* Az Azure RBAC-engedélyek méretezhetők úgy, hogy lehetővé teszik a felhasználók számára, hogy az előfizetéshez, az erőforráscsoporthoz vagy az egyes kulcstartók szintjén rendelik hozzá ezeket a szerepköröket. A felhasználók az Azure RBAC-hozzárendelés hatókörén belül minden kulcstartóhoz hozzáférhetnek az adatsíkon. Így nem kell egyéni hozzáférési szabályzatot rendelni felhasználónként/alkalmazásként a Key vaultban.

* Az Azure RBAC engedélyei Privileged Identity Management vagy PIM-kompatibilisek. Ez lehetővé teszi az igény szerinti hozzáférés-vezérlés konfigurálását olyan Kiemelt szerepkörökhöz, mint a Key Vault rendszergazda. Ez a legjobb biztonsági gyakorlat, és a legkevesebb jogosultságot követi a kulcstartók folyamatos hozzáférésének megszüntetésével.

Ha többet szeretne megtudni a Key Vault Azure RBAC, tekintse meg a következő dokumentumokat:

* Azure-RBAC Key Vault- [hivatkozáshoz](./secure-your-key-vault.md#management-plane-and-azure-rbac)
* Azure-RBAC Key Vault-szerepkörökhöz [hivatkozás](../../role-based-access-control/built-in-roles.md#key-vault-administrator)

## <a name="configure-key-vault-firewall"></a>Key Vault tűzfal konfigurálása

Alapértelmezés szerint a Key Vault lehetővé teszi a nyilvános internetről érkező forgalom számára, hogy egy nyilvános végponton keresztül küldje el a kulcstartót. További biztonsági réteg esetén beállíthatja a Azure Key Vault tűzfalat a Key Vault nyilvános végponthoz való hozzáférés korlátozására.

A Key Vault-tűzfal engedélyezéséhez kattintson a hálózat fülre a Key Vault portálon, majd a választógombra kattintva engedélyezze a hozzáférést a következő helyről: "privát végpont és kiválasztott hálózatok". Ha a Key Vault-tűzfal engedélyezését választja, akkor a Key Vault tűzfalon keresztül engedélyezheti a forgalmat.

* Adjon hozzá IPv4-címeket a Key Vault tűzfal engedélyezési listájához. Ez a beállítás a statikus IP-címmel rendelkező alkalmazások esetében ideális megoldás.

* Adjon hozzá egy virtuális hálózatot a Key Vault-tűzfalhoz. Ez a beállítás olyan Azure-erőforrások esetében használható, amelyek dinamikus IP-címekkel rendelkeznek, például Virtual Machines. Azure-erőforrásokat adhat hozzá egy virtuális hálózathoz, és hozzáadhatja a virtuális hálózatot a Key Vault tűzfal engedélyezési listájához. Ez a beállítás egy szolgáltatási végpontot használ, amely a virtuális hálózaton belüli magánhálózati IP-cím. Ez egy további védelmi réteget biztosít, így a Key Vault és a virtuális hálózat közötti adatforgalom a nyilvános interneten keresztül irányítható. A szolgáltatási végpontról a következő dokumentációban olvashat bővebben. [hivatkozás](./network-security.md)

* Adjon hozzá egy privát kapcsolati kapcsolatot a kulcstartóhoz. Ez a lehetőség közvetlenül csatlakoztatja a virtuális hálózatot a Key Vault egy adott példányához, és így hatékonyan hozza létre a kulcstartót a virtuális hálózaton belül. A privát végpontok kulcstartóval való konfigurálásával kapcsolatos további tudnivalókért tekintse meg a következő [hivatkozást](./private-link-service.md) :

## <a name="test-your-service-principals-ability-to-access-key-vault"></a>Az egyszerű szolgáltatásnév hozzáférésének tesztelése

Ha követte a fenti lépéseket, a Key vaultból beállíthatók és beolvashatók a titkos kulcsok.

### <a name="authentication-process-for-users-examples"></a>Hitelesítési folyamat a felhasználók számára (példák)

* A Key Vault használatához a felhasználók bejelentkezhetnek a Azure Portalba. [Key Vault portál rövid útmutatója](./quick-create-portal.md)

* A felhasználók az Azure CLI használatával használhatják a Key vaultot. [Key Vault Azure CLI rövid útmutató](./quick-create-cli.md)

* A felhasználó a Azure PowerShell használatával használhatja a Key vaultot. [Key Vault Azure PowerShell gyors útmutató](./quick-create-powershell.md)

### <a name="azure-active-directory-authentication-process-for-applications-or-services-examples"></a>Alkalmazások és szolgáltatások hitelesítési folyamatának Azure Active Directory (példák)

* Az alkalmazások az ügyfél titkos és ügyfél-AZONOSÍTÓját biztosítják egy Azure Active Directory token beszerzéséhez. 

* Az alkalmazások tanúsítványokat biztosítanak Azure Active Directory token beszerzéséhez. 

* Az Azure-erőforrások MSI-hitelesítéssel Azure Active Directory jogkivonat beszerzéséhez. 

* További információ az MSI Authentication [hivatkozásról](../../active-directory/managed-identities-azure-resources/overview.md)

### <a name="authentication-process-for-application-python-example"></a>Az alkalmazás hitelesítési folyamata (Python-példa)

Az alábbi mintakód segítségével tesztelheti, hogy az alkalmazás képes-e titkos kulcsot beolvasni a kulcstartóból a konfigurált egyszerű szolgáltatásnév használatával.

>[!NOTE]
>Ez a minta csak demonstrációs és tesztelési célokra szolgál. ne **használja az ügyfél titkos hitelesítését éles** környezetben Ez nem biztonságos tervezési gyakorlat. Ajánlott eljárásként használjon ügyféltanúsítványt vagy MSI-hitelesítést.

```python
from azure.identity import ClientSecretCredential
from azure.keyvault.secrets import SecretClient

tenant_id = "{ENTER YOUR TENANT ID HERE}"                          ##ENTER AZURE TENANT ID
vault_url = "https://{ENTER YOUR VAULT NAME}.vault.azure.net/"     ##ENTER THE URL OF YOUR KEY VAULT
client_id = "{ENTER YOUR CLIENT ID HERE}"                          ##ENTER THE CLIENT ID OF YOUR SERVICE PRINCIPAL
cert_path = "{ENTER YOUR CLIENT SECRET HERE}"                      ##ENTER THE CLIENT SECRET OF YOUR SERVICE PRINCIPAL

def main():

    #AUTHENTICATION TO Azure Active Directory USING CLIENT ID AND CLIENT CERTIFICATE (GET Azure Active Directory TOKEN)
    token = ClientSecretCredential(tenant_id=tenant_id, client_id=client_id, client_secret=client_secret)

    #AUTHENTICATION TO KEY VAULT PRESENTING Azure Active Directory TOKEN
    client = SecretClient(vault_url=vault_url, credential=token)

    #CALL TO KEY VAULT TO GET SECRET
    #ENTER NAME OF A SECRET STORED IN KEY VAULT
    secret = client.get_secret('{SECRET_NAME}')

    #GET PLAINTEXT OF SECRET
    print(secret.value)

#CALL MAIN()
if __name__ == "__main__":
    main()
```

## <a name="next-steps"></a>Következő lépések

A Key Vault-hitelesítés részletesebb megismeréséhez tekintse meg a következő dokumentumot. [Key Vault-hitelesítés](./authentication.md)
