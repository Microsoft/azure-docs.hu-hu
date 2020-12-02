---
title: A Blobok hozzáférésének engedélyezése a AzCopy & Azure Active Directory használatával | Microsoft Docs
description: Azure Active Directory (Azure AD) segítségével hitelesítő adatokat adhat meg a AzCopy-műveletekhez.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 11/03/2020
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: b13b5e1e27e9717066ff8f1aa8e245e8d9f54bbb
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498115"
---
# <a name="authorize-access-to-blobs-with-azcopy-and-azure-active-directory-azure-ad"></a>A Blobok hozzáférésének engedélyezése a AzCopy és a Azure Active Directory (Azure AD) használatával

Az Azure AD-vel az engedélyezési hitelesítő adatokkal rendelkező AzCopy adhat meg. Így nem kell minden parancshoz hozzáfűzni egy közös hozzáférésű aláírás (SAS) tokent. 

Először ellenőrizze a szerepkör-hozzárendeléseket. Ezután válassza ki az engedélyezni kívánt _rendszerbiztonsági tag_ típusát. A [felhasználói identitás](../../active-directory/fundamentals/add-users-azure-active-directory.md), a [felügyelt identitás](../../active-directory/managed-identities-azure-resources/overview.md)és az [egyszerű szolgáltatásnév](../../active-directory/develop/app-objects-and-service-principals.md) a rendszerbiztonsági tag mindegyik típusa.

A felhasználói identitás minden olyan felhasználó, aki rendelkezik identitással az Azure AD-ben. Ez a legegyszerűbb rendszerbiztonsági tag az engedélyezéshez. A felügyelt identitások és egyszerű szolgáltatások nagyszerű lehetőségeket biztosítanak, ha a AzCopy-t olyan parancsfájlon belül szeretné használni, amely felhasználói beavatkozás nélkül fut. A felügyelt identitások jobban illeszkednek az Azure-beli virtuális gépekről futtatott parancsfájlok számára, és az egyszerű szolgáltatásnév a helyszínen futó parancsfájlok számára is alkalmasabb. 

További információ a AzCopy-ről: [Ismerkedés a AzCopy](storage-use-azcopy-v10.md)szolgáltatással.

## <a name="verify-role-assignments"></a>Szerepkör-hozzárendelések ellenőrzése

A szükséges engedély szintje attól függ, hogy fájlokat tölt fel, vagy csak letölti őket.

Ha csak le szeretné tölteni a fájlokat, ellenőrizze, hogy a [Storage blob-Adatolvasó](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader) szerepkör hozzá van-e rendelve a felhasználói identitáshoz, a felügyelt identitáshoz vagy az egyszerű szolgáltatáshoz.

Ha fájlokat szeretne feltölteni, ellenőrizze, hogy a következő szerepkörök egyike van-e hozzárendelve a rendszerbiztonsági tag számára:

- [Storage-blobadatok közreműködője](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- [Storage-blobadatok tulajdonosa](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)

Ezeket a szerepköröket a következő hatókörök bármelyikében lehet hozzárendelni a rendszerbiztonsági tag számára:

- Tároló (fájlrendszer)
- Tárfiók
- Erőforráscsoport
- Előfizetés

A szerepkörök ellenőrzésével és hozzárendelésével kapcsolatos információkért lásd: [a Azure Portal használata Azure-szerepkör hozzárendeléséhez a blob-és üzenetsor-információk eléréséhez](./storage-auth-aad-rbac-portal.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE]
> Ne feledje, hogy az Azure-szerepkör-hozzárendelések akár öt percet is igénybe vehetnek.

Ha a rendszerbiztonsági tag hozzá van adva a célként megadott tároló vagy könyvtár hozzáférés-vezérlési listájához (ACL), nem kell rendelkeznie a rendszerbiztonsági tag számára hozzárendelt szerepkörök valamelyikével. Az ACL-ben a rendszerbiztonsági tag írási engedélyre van szüksége a célhely könyvtárához, és a tárolón és minden szülő könyvtáron végre kell hajtania az engedélyeket.

További információ: [hozzáférés-vezérlési modell Azure Data Lake Storage Gen2ban](../blobs/data-lake-storage-access-control-model.md).

## <a name="authorize-a-user-identity"></a>Felhasználói identitás engedélyezése

Miután meggyőződött arról, hogy a felhasználói identitás megkapta a szükséges engedélyezési szintet, nyisson meg egy parancssort, írja be a következő parancsot, majd nyomja le az ENTER billentyűt.

```azcopy
azcopy login
```

Ha hibaüzenetet kap, próbálja meg annak a szervezetnek a bérlői AZONOSÍTÓját, amelyhez a Storage-fiók tartozik.

```azcopy
azcopy login --tenant-id=<tenant-id>
```

Cserélje le a `<tenant-id>` helyőrzőt annak a szervezetnek a bérlői azonosítójával, amelyhez a Storage-fiók tartozik. A bérlő AZONOSÍTÓjának megkereséséhez válassza a Azure Portal **Azure Active Directory > tulajdonságok > a könyvtár azonosítóját** .

Ez a parancs egy hitelesítő kódot és egy webhely URL-címét adja vissza. Nyissa meg a webhelyet, adja meg a kódot, majd válassza a **tovább** gombot.

![Tároló létrehozása](media/storage-use-azcopy-v10/azcopy-login.png)

Ekkor megjelenik egy bejelentkezési ablak. Ebben az ablakban jelentkezzen be az Azure-fiókjába az Azure-fiókja hitelesítő adataival. Miután sikeresen bejelentkezett, lezárhatja a böngészőablakot, és megkezdheti a AzCopy használatát.

<a id="service-principal"></a>

## <a name="authorize-a-managed-identity"></a>Felügyelt identitás engedélyezése

Ez nagyszerű megoldás, ha a AzCopy-t olyan parancsfájlon belül szeretné használni, amely felhasználói beavatkozás nélkül fut, és a szkript egy Azure-beli virtuális gépről (VM) fut. Ha ezt a beállítást használja, nem kell semmilyen hitelesítő adatot tárolnia a virtuális gépen.

A fiókba olyan rendszerszintű felügyelt identitást használhat, amelyet engedélyezett a virtuális gépen, vagy a virtuális géphez rendelt, felhasználó által hozzárendelt felügyelt identitás ügyfél-AZONOSÍTÓjának, Objektumazonosítóának vagy erőforrás-AZONOSÍTÓjának használatával.

Ha többet szeretne megtudni a rendszerszintű felügyelt identitás engedélyezéséről vagy a felhasználó által hozzárendelt felügyelt identitás létrehozásáról, tekintse [meg a felügyelt identitások konfigurálása az Azure-erőforrásokhoz a Azure Portal használatával](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm)című témakört.

#### <a name="authorize-by-using-a-system-wide-managed-identity"></a>Engedélyezés a rendszerszintű felügyelt identitás használatával

Először is győződjön meg arról, hogy engedélyezte a rendszerszintű felügyelt identitást a virtuális gépen. Tekintse meg a [rendszer által hozzárendelt felügyelt identitást](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity).

Ezután a parancssorba írja be a következő parancsot, majd nyomja le az ENTER billentyűt.

```azcopy
azcopy login --identity
```

#### <a name="authorize-by-using-a-user-assigned-managed-identity"></a>Engedélyezés felhasználó által hozzárendelt felügyelt identitás használatával

Először is győződjön meg arról, hogy engedélyezte a felhasználó által hozzárendelt felügyelt identitást a virtuális gépen. Tekintse meg a [felhasználó által hozzárendelt felügyelt identitást](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#user-assigned-managed-identity).

Ezután a parancssorba írja be a következő parancsok bármelyikét, majd nyomja le az ENTER billentyűt.

```azcopy
azcopy login --identity --identity-client-id "<client-id>"
```

Cserélje le a `<client-id>` helyőrzőt a felhasználó által hozzárendelt felügyelt identitás ügyfél-azonosítójával.

```azcopy
azcopy login --identity --identity-object-id "<object-id>"
```

Cserélje le a `<object-id>` helyőrzőt a felhasználó által hozzárendelt felügyelt identitás objektum-azonosítójával.

```azcopy
azcopy login --identity --identity-resource-id "<resource-id>"
```

Cserélje le a `<resource-id>` helyőrzőt a felhasználó által hozzárendelt felügyelt identitás erőforrás-azonosítójával.

## <a name="authorize-a-service-principal"></a>Egyszerű szolgáltatásnév engedélyezése

Ez nagyszerű megoldás, ha a AzCopy-t olyan parancsfájlon belül szeretné használni, amely felhasználói beavatkozás nélkül fut, különösen ha a helyszínen fut. Ha az Azure-ban futó virtuális gépeken szeretné futtatni a AzCopy-t, a felügyelt szolgáltatás identitása könnyebben felügyelhető. További információért lásd a jelen cikk [felügyelt identitás engedélyezése](#authorize-a-managed-identity) című szakaszát.

A szkript futtatása előtt legalább egy alkalommal be kell jelentkeznie, hogy AzCopy biztosítson az egyszerű szolgáltatásnév hitelesítő adataival.  Ezeket a hitelesítő adatokat egy biztonságos és titkosított fájlban tárolja a rendszer, így a parancsfájlnak nem kell megadnia a bizalmas adatokat.

Bejelentkezhet a fiókjába egy ügyfél titkos kódjával vagy egy olyan tanúsítvány jelszavával, amely társítva van a szolgáltatásnév alkalmazásának regisztrálásához.

Ha többet szeretne megtudni az egyszerű szolgáltatásnév létrehozásáról, tekintse meg a [How to: use The Portal for Azure ad-alkalmazás és egyszerű szolgáltatásnév, amely hozzáfér az erőforrásokhoz](../../active-directory/develop/howto-create-service-principal-portal.md).

Az egyszerű szolgáltatásokkal kapcsolatos további tudnivalókért tekintse meg az [alkalmazás-és szolgáltatásnév objektumait Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md)

#### <a name="authorize-a-service-principal-by-using-a-client-secret"></a>Egyszerű szolgáltatásnév engedélyezése az ügyfél titkos kódjának használatával

Először állítsa be a `AZCOPY_SPA_CLIENT_SECRET` környezeti változót az egyszerű szolgáltatásnév alkalmazás-regisztrációjának ügyfél-titkára.

> [!NOTE]
> Ügyeljen rá, hogy ezt az értéket adja meg a parancssorból, és ne az operációs rendszer környezeti változó beállításait. Így az érték csak az aktuális munkamenet számára érhető el.

Ez a példa bemutatja, hogyan teheti ezt meg a PowerShellben.

```azcopy
$env:AZCOPY_SPA_CLIENT_SECRET="$(Read-Host -prompt "Enter key")"
```

> [!NOTE]
> Érdemes lehet egy parancssort használni, ahogy az ebben a példában is látható. Így a jelszó nem jelenik meg a konzolon a parancs előzményeiben.  

Ezután írja be a következő parancsot, majd nyomja le az ENTER billentyűt.

```azcopy
azcopy login --service-principal  --application-id application-id --tenant-id=tenant-id
```

Cserélje le a `<application-id>` helyőrzőt a szolgáltatásbeli tag alkalmazás-regisztrációjának alkalmazás-azonosítójával. Cserélje le a `<tenant-id>` helyőrzőt annak a szervezetnek a bérlői azonosítójával, amelyhez a Storage-fiók tartozik. A bérlő AZONOSÍTÓjának megkereséséhez válassza a Azure Portal **Azure Active Directory > tulajdonságok > a könyvtár azonosítóját** . 

#### <a name="authorize-a-service-principal-by-using-a-certificate"></a>Egyszerű szolgáltatásnév engedélyezése tanúsítvány használatával

Ha inkább a saját hitelesítő adatait szeretné használni az engedélyezéshez, feltöltheti a tanúsítványt az alkalmazás regisztrálásához, majd ezt a tanúsítványt használhatja a bejelentkezéshez.

A tanúsítványnak az alkalmazás regisztrálásához való feltöltése mellett a tanúsítvány másolatát is el kell végezni a gépre vagy a virtuális gépre, ahol a AzCopy futni fog. A tanúsítvány ezen másolatának a következőnek kell lennie:. PFX vagy. PEM formátuma, és tartalmaznia kell a titkos kulcsot. A titkos kulcsnak jelszóval védettnek kell lennie. Ha Windows rendszert használ, és a tanúsítvány csak tanúsítványtárolóban létezik, ügyeljen arra, hogy a tanúsítványt egy PFX-fájlba exportálja (beleértve a titkos kulcsot is). Útmutatásért lásd: [export-PfxCertificate](/powershell/module/pkiclient/export-pfxcertificate)

Ezután állítsa a `AZCOPY_SPA_CERT_PASSWORD` környezeti változót a tanúsítvány jelszavára.

> [!NOTE]
> Ügyeljen rá, hogy ezt az értéket adja meg a parancssorból, és ne az operációs rendszer környezeti változó beállításait. Így az érték csak az aktuális munkamenet számára érhető el.

Ebből a példából megtudhatja, hogyan teheti ezt a feladatot a PowerShellben.

```azcopy
$env:AZCOPY_SPA_CERT_PASSWORD="$(Read-Host -prompt "Enter key")"
```

Ezután írja be a következő parancsot, majd nyomja le az ENTER billentyűt.

```azcopy
azcopy login --service-principal --certificate-path <path-to-certificate-file> --tenant-id=<tenant-id>
```

Cserélje le a `<path-to-certificate-file>` helyőrzőt a tanúsítványfájl relatív vagy teljesen minősített elérési útjára. A AzCopy menti a tanúsítvány elérési útját, de nem menti a tanúsítvány másolatát, ezért ügyeljen arra, hogy a tanúsítvány a helyén maradjon. Cserélje le a `<tenant-id>` helyőrzőt annak a szervezetnek a bérlői azonosítójával, amelyhez a Storage-fiók tartozik. A bérlő AZONOSÍTÓjának megkereséséhez válassza a Azure Portal **Azure Active Directory > tulajdonságok > a könyvtár azonosítóját** .

> [!NOTE]
> Érdemes lehet egy parancssort használni, ahogy az ebben a példában is látható. Így a jelszó nem jelenik meg a konzolon a parancs előzményeiben. 

<a id="managed-identity"></a>


## <a name="next-steps"></a>További lépések

- További információ a AzCopy-ről: [Ismerkedés a AzCopy](storage-use-azcopy-v10.md)

- Ha kérdése van, problémája vagy általános visszajelzése van, küldje el őket [a GitHub](https://github.com/Azure/azure-storage-azcopy) oldalon.