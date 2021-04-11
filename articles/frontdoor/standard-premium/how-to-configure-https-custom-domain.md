---
title: HTTPS beállítása az egyéni tartományhoz Azure-beli bejárati ajtó standard/prémium SKU-konfigurációjában
description: Ebből a cikkből megtudhatja, hogyan lehet egyéni tartományt bevezetni az Azure-ba a standard/prémium SKU-ra.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: amsriva
ms.openlocfilehash: 3f3b4d3e431d9e24549bdb8caa4b2d17f547c82c
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106064052"
---
# <a name="configure-https-on-a-front-door-standardpremium-sku-preview-custom-domain-using-the-azure-portal"></a>HTTPS konfigurálása a bejárati ajtó standard/prémium SKU (előzetes verzió) egyéni tartományához a Azure Portal használatával

> [!NOTE]
> Ez a dokumentáció az Azure bejárati ajtó standard/Premium (előzetes verzió) verziójához készült. Információt keres az Azure bejárati ajtóról? Megtekintés [itt](../front-door-overview.md).

Az Azure bejárati ajtó standard/Premium lehetővé teszi, hogy alapértelmezés szerint az egyéni tartomány hozzáadásakor biztonságos TLS-kézbesítést biztosítson az alkalmazásai számára. Ha az egyéni tartományon belül a HTTPS protokollt használja, gondoskodjon arról, hogy a bizalmas adatok biztonságosan legyenek kézbesítve a TLS/SSL-titkosítással, amikor az interneten keresztül küldi el. Amikor a böngésző HTTPS-protokollal kapcsolódik egy webhelyhez, ellenőrzi a webhely biztonsági tanúsítványát, és megállapítja, hogy azt arra jogosult hitelesítésszolgáltató adta-e ki. Ez az eljárás védelmet nyújt webalkalmazásai számára a támadásokkal szemben.

Az Azure bejárati ajtó standard/Premium verziója az Azure által felügyelt tanúsítványokat és az ügyfél által felügyelt tanúsítványokat is támogatja. Az Azure bejárati ajtaja alapértelmezés szerint automatikusan engedélyezi a HTTPS-t az összes egyéni tartományhoz az Azure által felügyelt tanúsítványok használatával. Az Azure által felügyelt tanúsítványok beszerzéséhez nincs szükség további lépésekre. A rendszer létrehoz egy tanúsítványt a tartomány-ellenőrzési folyamat során. Saját tanúsítványát is használhatja az Azure bejárati ajtó standard/prémium integrálásával a Key Vault.

> [!IMPORTANT]
> Az Azure bejárati ajtó standard/Premium (előzetes verzió) jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: a [**Microsoft Azure előzetes verziójának kiegészítő használati feltételei**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

* Az egyéni tartományhoz tartozó HTTPS konfigurálásához először létre kell hoznia egy Azure Door standard/Premium profilt. További információ: gyors üzembe helyezés Azure-beli előtérben [standard/prémium szintű profil létrehozása](create-front-door-portal.md).

* Ha még nem rendelkezik egyéni tartománnyal, először az egyiket kell megvásárolnia egy tartományi szolgáltatóval. Példákért lásd az [egyéni tartománynév vásárlásáról](../../app-service/manage-custom-dns-buy-domain.md) szóló részt.

* Ha az Azure-t használja a [DNS-tartományok](../../dns/dns-overview.md)üzemeltetéséhez, akkor a tartományi szolgáltató tartománynevét (DNS) delegálnia kell egy Azure DNS. További információ: [tartomány delegálása Azure DNSra](../../dns/dns-delegate-domain-azure-dns.md). Ha azonban tartományi szolgáltatót használ a DNS-tartomány kezeléséhez, a DNS-alapú TXT-rekordok megadásával manuálisan kell érvényesíteni a tartományt.

## <a name="azure-managed-certificates"></a>Azure által felügyelt tanúsítványok

1. Az Azure bejárati ajtó standard/prémium profiljának beállításai területen válassza a **tartományok** elemet, majd válassza a **+ Hozzáadás** lehetőséget egy új tartomány hozzáadásához.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-new-custom-domain.png" alt-text="A tartományi konfiguráció kezdőlapjának képernyőképe.":::

1. A **tartomány hozzáadása** lapon a *DNS-kezelés* lapon válassza az **Azure Managed DNS** lehetőséget. 

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-domain-azure-managed.png" alt-text="Képernyőfelvétel: az Azure Managed DNS szolgáltatással kiválasztott tartomány hozzáadása lap.":::

1. Ellenőrizze és rendelje hozzá az egyéni tartományt egy végponthoz az [egyéni tartomány](how-to-add-custom-domain.md)engedélyezése című témakör lépéseit követve.

1. Ha az egyéni tartomány sikeresen hozzá lett rendelve a végponthoz, az Azure által felügyelt tanúsítvány bekerül a bejárati ajtóba. A folyamat végrehajtása eltarthat néhány percig.

## <a name="using-your-own-certificate"></a>Saját tanúsítvány használata

Dönthet úgy is, hogy saját TLS-tanúsítványt használ. Ezt a tanúsítványt egy Azure Key Vaultba kell importálni, mielőtt használni lehetne az Azure bejárati ajtó standard/Premium kiadásával. Lásd: [tanúsítvány importálása](../../key-vault/certificates/tutorial-import-certificate.md) Azure Key Vaultba. 

#### <a name="prepare-your-azure-key-vault-account-and-certificate"></a>Az Azure Key Vault-fiók és a tanúsítvány előkészítése
 
1. Ugyanazzal az előfizetéssel kell rendelkeznie egy futó Azure Key Vault-fiókkal, mint az Azure bejárati ajtó standard/Premium, amelyhez engedélyezni szeretné az egyéni HTTPS-t. Ha még nem rendelkezik Azure Key Vault-fiókkal, hozzon létre egyet.

    > [!WARNING]
    > Az Azure bejárati ajtaja jelenleg csak az előfizetésben lévő Key Vault fiókokat támogatja, mint a bejárati ajtó konfigurálása. Ha egy másik előfizetéshez tartozó Key Vault választ, mint az Azure bejárati ajtó standard/Premium, a rendszer meghibásodást okoz.

1. Ha már rendelkezik tanúsítvánnyal, feltöltheti közvetlenül a Azure Key Vault-fiókjába. Ellenkező esetben hozzon létre egy új tanúsítványt közvetlenül a Azure Key Vault segítségével az egyik olyan partner-hitelesítésszolgáltató hatóságtól, amely Azure Key Vault integrálódik a szolgáltatással. **Titkos** tanúsítvány helyett a tanúsítványt **tanúsítvány** -objektumként töltse fel.

    > [!NOTE]
    > Saját TLS/SSL-tanúsítvány esetén a bejárati ajtó nem támogatja az EC titkosítási algoritmusokkal rendelkező tanúsítványokat.

#### <a name="register-azure-front-door"></a>Azure-beli bejárati ajtó regisztrálása

Regisztrálja az Azure-hoz tartozó egyszerű szolgáltatást a Azure Active Directory a PowerShellen keresztüli alkalmazásként.

> [!NOTE]
> Ehhez a művelethez globális rendszergazdai jogosultságok szükségesek, és bérlőn csak **egyszer** kell végrehajtani.

1. Ha szükséges, telepítse az [Azure PowerShell](/powershell/azure/install-az-ps) bővítményt a PowerShellhez a helyi számítógépen.

1. Futtassa a PowerShellben az alábbi parancsot:

     `New-AzADServicePrincipal -ApplicationId "205478c0-bd83-4e1b-a9d6-db63a3e1e1c8"`              

#### <a name="grant-azure-front-door-access-to-your-key-vault"></a>Azure-beli előtérben való hozzáférés biztosítása a kulcstartóhoz
 
Adja meg az Azure-előfizetési engedélyt a Azure Key Vault fiókban található tanúsítványok eléréséhez.

1. A Key Vault-fiók beállítások területén válassza a **hozzáférési szabályzatok** lehetőséget. Ezután válassza az **új hozzáadása** elemet az új szabályzat létrehozásához.

1. A **rendszerbiztonsági tag kiválasztása lapon** keressen rá a **205478c0-bd83-4e1b-a9d6-db63a3e1e1c8** kifejezésre, és válassza a * * Microsoft. AzureFrontDoor-CDN * * elemet. Kattintson a **Kiválasztás** elemre.

1. A **titkos engedélyek** területen válassza a **beolvasás** lehetőséget, hogy a bejárati ajtó beolvassa a tanúsítványt.

1. A **tanúsítvány engedélyei** területen válassza a **beolvasás** lehetőséget, hogy a bejárati ajtó beolvassa a tanúsítványt.

1. Válassza az **OK** lehetőséget. 

#### <a name="select-the-certificate-for-azure-front-door-to-deploy"></a>Tanúsítvány kiválasztása az Azure-beli bejárati ajtó üzembe helyezéséhez
 
1. Térjen vissza az Azure bejárati ajtó standard/prémium csomagra a portálon.

1. Navigáljon a **titkok** menüpontra a *Beállítások* területen, majd válassza a **tanúsítvány hozzáadása** elemet.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-certificate.png" alt-text="Képernyőkép az Azure elülső Ajtójának titkos kezdőlapján.":::

1. A **tanúsítvány hozzáadása** lapon jelölje be a jelölőnégyzetet ahhoz a tanúsítványhoz, amelyet hozzá szeretne adni az Azure bejárati ajtó standard/Premium-hoz. Hagyja meg a verzió kijelölését "legutóbbi" értékként, majd válassza a **Hozzáadás** lehetőséget. 

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-certificate-page.png" alt-text="Képernyőkép a tanúsítvány hozzáadása oldalról.":::

1. Ha a tanúsítvány kiépítés sikeresen megtörtént, akkor használhatja azt új egyéni tartomány hozzáadásakor.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/successful-certificate-provisioned.png" alt-text="Képernyőfelvétel: a tanúsítvány sikeresen hozzá lett adva a titkokhoz.":::

1. Az új egyéni tartomány hozzáadásához navigáljon a *Beállítások* **területen,** majd válassza a **+ Hozzáadás** lehetőséget. A **tartomány hozzáadása** lapon válassza a "saját tanúsítvány használata (BYOC)" lehetőséget a *https*-hez. A *Secret (titkos kulcs*) beállításnál válassza ki a legördülő listából a használni kívánt tanúsítványt. 

    > [!NOTE]
    > A kiválasztott tanúsítványnak köznapi névnek (CN) kell lennie, mint a hozzáadott egyéni tartománynak.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-custom-domain-https.png" alt-text="Képernyőkép az egyéni tartomány hozzáadása lapon a HTTPS használatával.":::

1. A tanúsítvány érvényesítéséhez kövesse a képernyőn megjelenő lépéseket. Ezután rendelje hozzá az újonnan létrehozott egyéni tartományt egy végponthoz az [Egyéni tartományi útmutató létrehozása](how-to-add-custom-domain.md) című témakörben leírtak szerint.

#### <a name="change-from-azure-managed-to-bring-your-own-certificate-byoc"></a>Váltás az Azure által felügyelt eszközről saját tanúsítvány létrehozásához (BYOC)

1. Egy meglévő Azure-beli felügyelt tanúsítvány felhasználó által felügyelt tanúsítványra való módosításához válassza ki a tanúsítvány állapotát a **tanúsítvány részletei** lap megnyitásához.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/domain-certificate.png" alt-text="Képernyőfelvétel a tanúsítványok állapotáról a tartományok kezdőlapján." lightbox="../media/how-to-configure-https-custom-domain/domain-certificate-expanded.png":::

1. A **tanúsítvány részletei** lapon megváltoztathatja az "Azure Managed" és a "saját tanúsítvány használata (BYOC)" beállítást. Ezután kövesse az előzőekben ismertetett lépéseket a tanúsítvány kiválasztásához. Válassza a **frissítés** lehetőséget a társított tanúsítvány tartományhoz való módosításához.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/certificate-details-page.png" alt-text="A tanúsítvány részletei lap képernyőképe.":::

## <a name="next-steps"></a>Következő lépések

Ismerje meg az [Azure bejárati ajtó standard/prémium szintű gyorsítótárazását](concept-caching.md).
