---
title: Azure Key Vault hitelesítés alapjai
description: A Key Vault hitelesítési modelljének működése
author: msmbaldwin
ms.author: mbaldwin
ms.date: 04/15/2021
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.openlocfilehash: f0cd2fb341dd790a7628cc3cf6a5bdd87d7c3687
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107753415"
---
# <a name="key-vault-authentication-fundamentals"></a>Key Vault – hitelesítési alapok

Azure Key Vault segítségével biztonságosan tárolhatja és kezelheti az alkalmazás hitelesítő adatait, például a titkos kulcsokat, kulcsokat és tanúsítványokat egy központi és biztonságos felhőtárban. Key Vault szükségtelenné, hogy a hitelesítő adatokat az alkalmazásokban tárolja. Az alkalmazások futásidőben hitelesíthetők Key Vault hitelesítő adatok lekéréséhez.

Rendszergazdaként szorosan szabályozhatja, hogy mely felhasználók és alkalmazások férhetnek hozzá a kulcstartóhoz, és korlátozhatja és naplóozhatja az ő műveleteit. Ez a dokumentum a kulcstartó-hozzáférési modell alapvető fogalmait ismerteti. Ez bevezető szintű ismereteket biztosít, és bemutatja, hogyan hitelesíthet egy felhasználót vagy alkalmazást a Key Vaultban az elsőtől a végéig.

## <a name="required-knowledge"></a>Szükséges ismeretek

Ez a dokumentum feltételezi, hogy ön ismeri az alábbi fogalmakat. Ha egyik fogalmat sem ismeri, a folytatás előtt kövesse a súgóhivatkozásokat.

* Azure Active Directory [hivatkozás](../../active-directory/fundamentals/active-directory-whatis.md)
* Rendszerbiztonsági tag [hivatkozás](./authentication.md#app-identity-and-security-principals)

## <a name="key-vault-configuration-steps-summary"></a>Key Vault konfigurációs lépések összegzése

1. Regisztrálja a felhasználót vagy az alkalmazást a Azure Active Directory rendszerbiztonsági tagként.
1. Konfigurálja a rendszerbiztonsági tag szerepkör-hozzárendelését a Azure Active Directory.
1. Kulcstartó-hozzáférési szabályzatok konfigurálása a rendszerbiztonsági tag számára.
1. Konfigurálja Key Vault tűzfal hozzáférését a kulcstartóhoz (nem kötelező).
1. Tesztelje a rendszerbiztonsági tag kulcstartó-hozzáférését.

## <a name="register-a-user-or-application-in-azure-active-directory-as-a-security-principal"></a>Felhasználó vagy alkalmazás regisztrálása a Azure Active Directory rendszerbiztonsági tagként

Amikor egy felhasználó vagy alkalmazás kérést tesz a Key Vaulthoz, a kérést először hitelesítenie kell a Azure Active Directory. Ahhoz, hogy ez működjön, a felhasználót vagy az alkalmazást rendszerbiztonsági tagként Azure Active Directory regisztrálni a szolgáltatásban.

Kövesse az alábbi dokumentációs hivatkozásokat, hogy megértse, hogyan regisztrálhat egy felhasználót vagy alkalmazást a Azure Active Directory.
**Mindenképpen hozzon létre jelszót a felhasználói regisztrációhoz, valamint titkos ügyfél-titkos adatokat vagy ügyfél-tanúsítványt az alkalmazásokhoz.**

* Felhasználó regisztrálása a Azure Active Directory [hivatkozásban](../../active-directory/fundamentals/add-users-azure-active-directory.md)
* Alkalmazás regisztrálása a Azure Active Directory [hivatkozásban](../../active-directory/develop/quickstart-register-app.md)

## <a name="assign-your-security-principal-a-role"></a>Szerepkör hozzárendelése a rendszerbiztonsági taghoz

Az Azure szerepköralapú hozzáférés-vezérlésével (Azure RBAC) engedélyeket rendelhet a rendszerbiztonsági taghoz. Ezeket az engedélyeket szerepkör-hozzárendelésnek nevezzük.

A kulcstartó kontextusában ezek a szerepkör-hozzárendelések határozzák meg a rendszerbiztonsági tag kulcstartó felügyeleti síkhoz (más néven vezérlősíkhoz) való hozzáférési szintjét. Ezek a szerepkör-hozzárendelések nem biztosítanak közvetlen hozzáférést az adatsík titkos kulcsaihoz, hanem hozzáférést biztosítanak a kulcstartó tulajdonságainak kezeléséhez. Az Olvasó szerepkörhöz rendelt  felhasználók vagy alkalmazások például nem módosíthatnak a Key Vault tűzfalbeállításain, míg a közreműködői szerepkörhöz rendelt felhasználók vagy alkalmazások is.  Egyik szerepkör sem fog közvetlen hozzáféréssel a titkos kulcsokon, kulcsokon és tanúsítványokon végrehajtani a műveleteket, például az értékük létrehozásához vagy leolvasáshoz, amíg hozzá nem rendeli őket a kulcstartó adatsíkhoz való hozzáféréshez. Ezt a következő lépésben fedi le.

>[!IMPORTANT]
> Bár a Közreműködő vagy Tulajdonos szerepkörű felhasználók alapértelmezés szerint nem férhetnek hozzá a Key Vaultban tárolt titkos kulcsok műveleteihez, a Közreműködő és a Tulajdonos szerepkör engedélyekkel rendelkezik a kulcstartóban tárolt titkos kulcsok hozzáférési szabályzatának hozzáadásához vagy eltávolításához. Ezért az ezekkel a szerepkör-hozzárendelésekkel egy felhasználó hozzáférést adhat magának a kulcstartóban tárolt titkos kulcsok eléréséhez. Ezért azt javasoljuk, hogy csak rendszergazdák férnek hozzá a Közreműködő vagy a Tulajdonos szerepkörhöz. Azok a felhasználók és alkalmazások, akik csak titkos kulcsokat kell lekérni a Key Vaultból, Olvasó szerepkört kell biztosítani. **További részleteket a következő szakaszban talál.**

>[!NOTE]
> Amikor szerepkör-hozzárendelést rendel egy felhasználóhoz Azure Active Directory Azure Active Directory bérlői szinten, ez az engedélykészlet a hozzárendelés hatókörébe tartozó összes előfizetésre, erőforráscsoportra és erőforrásra szűkül. A legkisebb jogosultságok rendszerbiztonsági tagjának követnie kell ezt a szerepkör-hozzárendelést egy részletesebb hatókörben. Hozzárendelhet például egy felhasználót olvasói szerepkörhöz az előfizetés szintjén, és egy Tulajdonos szerepkört egyetlen kulcstartóhoz. Egy előfizetés, erőforráscsoport vagy kulcstartó Identitás-hozzáférés-kezelési (IAM)-beállításai között részletesebb hatókörben is el lehet látni egy szerepkör-hozzárendelést.

* További információ az Azure-szerepkörök [hivatkozásról](../../role-based-access-control/built-in-roles.md)
* További információ a szerepkör-hozzárendelések hozzárendelésével vagy [eltávolításával](../../role-based-access-control/role-assignments-portal.md) kapcsolatban

## <a name="configure-key-vault-access-policies-for-your-security-principal"></a>Kulcstartó-hozzáférési szabályzatok konfigurálása a rendszerbiztonsági tag számára

Mielőtt hozzáférést ad a felhasználóknak és az alkalmazásoknak a kulcstartó eléréséhez, fontos megérteni a kulcstartón elvégezhető különböző műveleteket. A kulcstartó-műveleteknek két fő típusa van: a felügyeleti sík (más néven vezérlősík) és az adatsík-műveletek.

Ez a táblázat több példát mutat be a felügyeleti sík és az adatsík által vezérelt különböző műveletekre. A kulcstartó tulajdonságait megváltoztató műveletek felügyeletisík-műveletek. A kulcstartóban tárolt titkos kulcsok értékét megváltoztató vagy lekért műveletek adatsíkműveletek.

|Felügyeleti sík műveletei (példák)|Adatsíkműveletek (példák)|
| --- | --- |
| Create Key Vault | Kulcs, titkos kulcs és tanúsítvány létrehozása
| Törlés Key Vault | Kulcs, titkos kulcs és tanúsítvány törlése
| Szerepkör-hozzárendelések hozzáadása Key Vault eltávolítása | Kulcsok, titkos kulcsok, tanúsítványok értékeinek listása és lekérte
| Hozzáférési szabályzatok hozzáadása Key Vault eltávolítása | Kulcsok, titkos kulcsok, tanúsítványok biztonsági mentése és visszaállítása
| Tűzfalbeállítások Key Vault módosítása | Kulcsok, titkos kulcsok és tanúsítványok megújítása
| Helyreállítási Key Vault módosítása | Helyreállíthatóan törölt kulcsok, titkos kulcsok, tanúsítványok végleges törlése vagy helyreállítása
| Diagnosztikai Key Vault beállításainak módosítása

### <a name="management-plane-access--azure-active-directory-role-assignments"></a>Felügyeleti sík hozzáférése & Azure Active Directory szerepkör-hozzárendelések

Azure Active Directory szerepkör-hozzárendelések hozzáférést kiosztása a felügyeletisík-műveletek kulcstartón való végrehajtásához. Ezt a hozzáférést általában a felhasználók biztosítják, nem az alkalmazások számára. A felhasználó szerepkör-hozzárendelésének módosításával korlátozhatja, hogy a felhasználó milyen felügyeletisík-műveleteket hajthat végre.

Ha például egy felhasználót Key Vault olvasó szerepkörhöz rendel egy felhasználóhoz, láthatja a kulcstartó tulajdonságait, például a hozzáférési szabályzatokat, de nem engedélyezi számukra a módosításokat. Egy felhasználó és egy Tulajdonos szerepkör hozzárendelésével teljes hozzáférést kap a kulcstartó felügyeleti síkjának beállításainak módosításához.

A szerepkör-hozzárendelések vezérlése a Key Vault Access Control (IAM) panelen történik. Ha azt szeretné, hogy egy adott felhasználó olvasó vagy több kulcstartó-erőforrás rendszergazdája legyen, létrehozhat egy szerepkör-hozzárendelést a tároló, az erőforráscsoport vagy az előfizetés szintjén, és a szerepkör-hozzárendelés a hozzárendelés hatókörébe tartozó összes erőforráshoz hozzá lesz adva.

Az adatsík-hozzáférés, illetve a kulcsokon, titkos kulcsokon és a key vaultban tárolt tanúsítványokon műveletek végrehajtásához való hozzáférés kétféleképpen hozzáadható.

### <a name="data-plane-access-option-1-classic-key-vault-access-policies"></a>1. adatsík-hozzáférési lehetőség: Klasszikus Key Vault hozzáférési szabályzatok

A kulcstartó-hozzáférési szabályzatok hozzáférést engedélyeznek a felhasználóknak és az alkalmazásoknak adatsíkműveletek végrehajtásához egy kulcstartón.

> [!NOTE]
> Ez a hozzáférési modell nem kompatibilis az Azure RBAC-val az alább dokumentált Key Vaulthoz (2. lehetőség). Ki kell választania egyet. Ezt a lehetőséget akkor is meg lehet majd tenni, ha a kulcstartó Hozzáférési szabályzat lapra kattint.

A klasszikus hozzáférési szabályzatok részletesek, ami azt jelenti, hogy engedélyezheti vagy megtagadhatja az egyes felhasználók vagy alkalmazások azon képességét, hogy egyéni műveleteket hajtsanak végre egy kulcstartón belül. Íme, néhány példa:

* Az 1. rendszerbiztonsági tag bármilyen kulcsműveletet végrehajthat, de nem engedélyezett a titkos kulcsokkal vagy tanúsítványokkal kapcsolatos műveletek végrehajtása.
* A 2. rendszerbiztonsági tag listolhatja és beolvashatja az összes kulcsot, titkos kulcsot és tanúsítványt, de nem hajthat végre létrehozási, törlési vagy megújítási műveleteket.
* A 3. rendszerbiztonsági tag biztonsági mentést készít és visszaállít minden titkos ot, de maguk nem tudják olvasni a titkos kulcsok értékét.

A klasszikus hozzáférési szabályzatok azonban nem engedélyezik az objektumszintű engedélyeket, és a hozzárendelt engedélyek egy adott kulcstartó hatókörére vannak alkalmazva. Ha például a "Secret Get" hozzáférési szabályzat engedélyt adja egy adott kulcstartóban található rendszerbiztonsági tagnak, a rendszerbiztonsági tag le tudja kérni az adott kulcstartón belüli összes titkos kulcsot. Ez a "Titkos kulcs le kérni" engedély azonban nem terjed ki automatikusan más kulcstartókra, és explicit módon kell hozzárendelni.

> [!IMPORTANT]
> A klasszikus kulcstartó-hozzáférési szabályzatok és Azure Active Directory szerepkör-hozzárendelések egymástól függetlenek. A rendszerbiztonsági tag előfizetési szinten való közreműködői szerepkörrel való hozzárendelése nem teszi lehetővé automatikusan, hogy a rendszerbiztonsági tag adatsíkműveleteket hajtson végre az előfizetés hatókörén belül minden kulcstartón. A rendszerbiztonsági tagnak továbbra is meg kell adni a jogosultságot, vagy hozzáférési szabályzati engedélyt kell adni magának az adatsík-műveletek végrehajtásához.

### <a name="data-plane-access-option-2--azure-rbac-for-key-vault"></a>2. adatsík-hozzáférési lehetőség: Azure RBAC Key Vault

A kulcstartó adatsíkhoz való hozzáférés megadásának egy új módja a kulcstartó azure-beli szerepköralapú hozzáférés-vezérlése (Azure RBAC) révén.

> [!NOTE]
> Ez a hozzáférési modell nem kompatibilis a fent bemutatott klasszikus Key Vault-hozzáférési szabályzatokkal. Ki kell választania egyet. Ezt a lehetőséget akkor is meg lehet majd tenni, ha a kulcstartó Hozzáférési szabályzat lapra kattint.

Key Vault szerepkör-hozzárendelések olyan beépített Azure-beli szerepkör-hozzárendelések halmazai, amelyek a kulcsok, titkos kulcsok és tanúsítványok eléréséhez használt általános engedélykészleteket foglalják magukban. Ez az engedélymodell olyan további képességeket is lehetővé tesz, amelyek a klasszikus kulcstartó-hozzáférési szabályzatmodellben nem érhetők el.

* Az Azure RBAC-engedélyek nagy méretekben is kezelhetők, ha lehetővé teszi, hogy a felhasználók előfizetés, erőforráscsoport vagy egyéni kulcstartószinten rendelik hozzá ezeket a szerepköröket. A felhasználó az adatsíkra vonatkozó engedélyekkel fog rendelkezni az Azure RBAC-hozzárendelés hatókörébe tartozó összes kulcstartóhoz. Így nincs szükség egyéni hozzáférési házirend-engedélyek felhasználónként/alkalmazásonkénti hozzárendelésére kulcstartónként.

* Az Azure RBAC-engedélyek kompatibilisek a Privileged Identity Management PIM-el. Ez lehetővé teszi, hogy az olyan kiemelt szerepkörökhöz is konfigurálja a hozzáférés-vezérlést, mint Key Vault rendszergazda. Ez a legjobb biztonsági eljárás, és a kulcstartókhoz való állandó hozzáférés megszüntetésével követi a legkevesebb jogosultságot.

A következő dokumentumokban további információt Key Vault Azure RBAC-ről:

* Azure RBAC Key Vault [hivatkozáshoz](rbac-guide.md)
* Azure RBAC a Key Vault szerepkörökhöz [hivatkozás](../../role-based-access-control/built-in-roles.md#key-vault-administrator)

## <a name="configure-key-vault-firewall"></a>A Key Vault konfigurálása

Alapértelmezés szerint a Key Vault lehetővé teszi, hogy a nyilvános internetről származó forgalom egy nyilvános végponton keresztül elérjék a kulcstartót. További biztonsági rétegként konfigurálhatja a Azure Key Vault tűzfalat a Key Vault nyilvános végponthoz való hozzáférés korlátozására.

A Key Vault tűzfalának engedélyezéséhez kattintson a Key Vault portálJának Hálózat lapjára, és jelölje be a Hozzáférés engedélyezése innen: "Privát végpont és kiválasztott hálózatok" választógombot. Ha úgy dönt, hogy engedélyezi a Key Vault tűzfalát, az alábbi módokon engedélyezheti a forgalmat a Key Vault tűzfalán keresztül.

* Adja hozzá az IPv4-címeket a Key Vault tűzfalának engedélyezőlistához. Ez a lehetőség statikus IP-címmel és alkalmazásokkal működik a legjobban.

* Adjon hozzá egy virtuális hálózatot a Key Vault tűzfalához. Ez a lehetőség olyan Azure-erőforrások esetén működik a legjobban, amelyek dinamikus IP-címekkel, például ip-Virtual Machines. Hozzáadhat Azure-erőforrásokat egy virtuális hálózathoz, és hozzáadhatja a virtuális hálózatot a Key Vault tűzfalának engedélyezőlistához. Ez a lehetőség egy szolgáltatásvégpontot használ, amely egy magánhálózati IP-cím a virtuális hálózaton belül. Ez egy további védelmi réteget biztosít, így a Key Vault és a virtuális hálózat közötti forgalom nem lesz a nyilvános interneten keresztül irányítva. A szolgáltatásvégponttal kapcsolatos további információkért tekintse meg a következő dokumentációt. [Link](./network-security.md)

* Adjon hozzá egy privát kapcsolati kapcsolatot a kulcstartóhoz. Ez a lehetőség közvetlenül csatlakoztatja a virtuális hálózatot a kulcstartó egy adott példányához, és hatékonyan behozza a kulcstartót a virtuális hálózatba. A privát végpont key vaulthoz való konfigurálásával kapcsolatos további információkért tekintse meg a következő [hivatkozást](./private-link-service.md)

## <a name="test-your-service-principals-ability-to-access-key-vault"></a>A szolgáltatásnév Kulcstartó-hozzáférési képességének tesztelése

Miután követte a fenti lépéseket, beállíthatja és lekérni a titkos kulcsokat a kulcstartóból.

### <a name="authentication-process-for-users-examples"></a>Felhasználók hitelesítési folyamata (példák)

* A felhasználók bejelentkeznek a Azure Portal key vault használatára. [Key Vault portál rövid útmutatója](./quick-create-portal.md)

* A felhasználó az Azure CLI használatával használhatja a Key Vaultot. [Key Vault Azure CLI – rövid útmutató](./quick-create-cli.md)

* A felhasználó a Azure PowerShell kulcstartót használhatja. [Key Vault Azure PowerShell rövid útmutató](./quick-create-powershell.md)

### <a name="azure-active-directory-authentication-process-for-applications-or-services-examples"></a>Azure Active Directory alkalmazások vagy szolgáltatások hitelesítési folyamatának engedélyezése (példák)

* Az alkalmazás egy titkos ügyfél-azonosítót és egy ügyfél-azonosítót biztosít egy függvényben egy Azure Active Directory jogkivonat le Azure Active Directory le. 

* Az alkalmazás tanúsítványt biztosít a Azure Active Directory lekérni. 

* Az Azure-erőforrások MSI-hitelesítéssel szereznek be Azure Active Directory jogkivonatot. 

* További információ az MSI-hitelesítési [hivatkozásról](../../active-directory/managed-identities-azure-resources/overview.md)

### <a name="authentication-process-for-application-python-example"></a>Alkalmazás hitelesítési folyamata (Python-példa)

Az alábbi kódmintával tesztelheti, hogy az alkalmazás lekérhet-e titkos kódot a kulcstartóból a konfigurált szolgáltatásnévvel.

>[!NOTE]
>Ez a minta csak bemutatási és tesztelési célokra szolgál. **NE HASZNÁLJON TITKOS ÜGYFÉLOLDALI HITELESÍTÉST ÉLES KÖRNYEZETBEN** Ez nem biztonságos tervezési gyakorlat. Ajánlott eljárásként használjon ügyfél-tanúsítványt vagy MSI-hitelesítést.

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

A key vault-hitelesítéssel kapcsolatos további információkért tekintse meg a következő dokumentumot. [Key Vault-hitelesítés](./authentication.md)
