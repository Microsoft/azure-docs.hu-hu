---
title: Fejlesztői ajánlott eljárások – Pod Security az Azure Kubernetes Servicesben (ak)
description: Ismerje meg az Azure Kubernetes szolgáltatásban (ak) található hüvelyek biztonságossá tételét ismertető fejlesztői ajánlott eljárásokat
services: container-service
ms.topic: conceptual
ms.date: 07/28/2020
ms.author: zarhoads
ms.openlocfilehash: 1c7143b6d3479cf3083cfc730301c68dcf4eb705
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92900813"
---
# <a name="best-practices-for-pod-security-in-azure-kubernetes-service-aks"></a>Ajánlott eljárások a pod Security számára az Azure Kubernetes szolgáltatásban (ak)

Az Azure Kubernetes szolgáltatásban (ak) lévő alkalmazások fejlesztése és futtatása során kulcsfontosságú szempont a hüvelyek biztonsága. Az alkalmazásokat úgy kell megtervezni, hogy a lehető legkevesebb jogosultsággal rendelkezzenek. A magánjellegű adatvédelmet a felhasználók szem előtt tartja. Nem szeretné, hogy a hitelesítő adatok, például az adatbázis-kapcsolati karakterláncok, kulcsok, titkos kódok és a külvilág számára elérhető tanúsítványok, ahol a támadók kihasználhatják ezeket a titkokat rosszindulatú célokra. Ne adja hozzá őket a kódhoz, vagy ágyazza be őket a tároló-lemezképbe. Ez a megközelítés veszélyezteti a kitettséget, és korlátozza a hitelesítő adatok elforgatásának képességét, mivel a tároló lemezképeit újra kell építeni.

Ez az ajánlott eljárási cikk a hüvelyek az AK-ban való védelmére összpontosít. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * A pod biztonsági környezet használata a folyamatokhoz és szolgáltatásokhoz való hozzáférés korlátozására vagy a jogosultságok kiterjesztésére
> * Hitelesítés más Azure-erőforrásokkal a pod által felügyelt identitások használatával
> * Hitelesítő adatok kérése és beolvasása egy digitális tárból, például Azure Key Vault

Emellett elolvashatja a [fürt biztonságával][best-practices-cluster-security] és a [tárolók rendszerképének kezelésével][best-practices-container-image-management]kapcsolatos ajánlott eljárásokat is.

## <a name="secure-pod-access-to-resources"></a>Biztonságos Pod-hozzáférés az erőforrásokhoz

**Ajánlott eljárási útmutató** – egy másik felhasználóként vagy csoportként való futtatásra, valamint a mögöttes csomópont-folyamatok és-szolgáltatások elérésének korlátozására, a pod biztonsági környezet beállításainak megadása. Rendelje hozzá a minimálisan szükséges jogosultságokat.

Ahhoz, hogy az alkalmazások megfelelően fussanak, a hüvelyeket definiált felhasználóként vagy csoportként kell futtatni, és nem *root*-ként. A `securityContext` for a pod vagy a Container lehetővé teszi olyan beállítások megadását, mint például a *runAsUser* vagy a *fsGroup* , hogy a megfelelő engedélyeket tegyük fel. Csak a szükséges felhasználói vagy csoportos engedélyeket rendelje hozzá, és ne használja a biztonsági környezetet a további engedélyek feltételezéséhez. A *runAsUser*, a jogosultság-eszkaláció és az egyéb linuxos képességek beállításai csak Linux-csomópontokon és hüvelyeken érhetők el.

Ha nem legfelső szintű felhasználóként fut, a tárolók nem köthetők a 1024-es alatti privilegizált portokhoz. Ebben az esetben a Kubernetes Services segítségével elrejtheti azt a tényt, hogy egy alkalmazás egy adott porton fut.

A pod biztonsági környezet további képességeket és engedélyeket is meghatározhat a folyamatok és szolgáltatások eléréséhez. A következő általános biztonsági környezeti definíciók állíthatók be:

* a **allowPrivilegeEscalation** határozza meg, hogy a pod lehet-e *root* jogosultságot feltételezni. Tervezze meg az alkalmazásait, hogy ezt a beállítást mindig *hamis* értékre állítsa.
* A **Linux-funkciók** lehetővé teszik, hogy a pod hozzáférjen az alapul szolgáló csomópont-folyamatokhoz Ügyeljen a képességek kiosztására. Rendelje hozzá a minimálisan szükséges jogosultságokat. További információ: Linux- [képességek][linux-capabilities].
* A **SELinux labels** egy Linux kernel biztonsági modul, amely lehetővé teszi hozzáférési szabályzatok definiálását a szolgáltatások, a folyamatok és a fájlrendszer eléréséhez. Ismét rendelje hozzá a minimálisan szükséges jogosultságokat. További információ: [SELinux-beállítások a Kubernetes-ben][selinux-labels]

A következő példában a pod YAML manifest beállítja a biztonsági környezet beállításait a következők meghatározásához:

* A pod a *1000* felhasználói azonosítóként fut, és az *2000* -es azonosítójú csoport tagja.
* Nem lehet kibővíteni a jogosultságokat a használatra `root`
* Lehetővé teszi a Linux-képességek elérését a hálózati adapterekhez és a gazdagép valós idejű (hardveres) órájához.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  securityContext:
    fsGroup: 2000
  containers:
    - name: security-context-demo
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
      securityContext:
        runAsUser: 1000
        allowPrivilegeEscalation: false
        capabilities:
          add: ["NET_ADMIN", "SYS_TIME"]
```

Működjön együtt a fürt üzemeltetőjével, és határozza meg, hogy milyen biztonsági környezeti beállításokat kell megadnia. Tervezze meg az alkalmazásait a további engedélyek minimalizálásához és a pod eléréséhez. A AppArmor és a seccompot (biztonságos számítástechnika) használatával további biztonsági funkciók is korlátozhatók a hozzáférés korlátozására. További információ: az [erőforrásokhoz való hozzáférés biztonságossá tétele][apparmor-seccomp].

## <a name="limit-credential-exposure"></a>Hitelesítőadat-expozíció korlátozása

**Ajánlott eljárási útmutató** – ne adjon meg hitelesítő adatokat az alkalmazás kódjában. Felügyelt identitások használata az Azure-erőforrásokhoz, hogy a pod kérjen hozzáférést más erőforrásokhoz. A digitális tárolókat, például a Azure Key Vaulteket is fel kell használni a digitális kulcsok és a hitelesítő adatok tárolására és lekérésére. A hüvelyben felügyelt identitások kizárólag Linux-hüvelyekhez és csak a tárolók rendszerképeihez használhatók.

Ha korlátozni szeretné az alkalmazás kódjában elérhető hitelesítő adatok kockázatát, ne használja a rögzített vagy a megosztott hitelesítő adatokat. A hitelesítő adatok vagy kulcsok nem szerepelhetnek közvetlenül a kódban. Ha ezek a hitelesítő adatok elérhetők, az alkalmazást frissíteni és újra kell telepíteni. A jobb megoldás az, hogy a hüvelyek saját identitását és módszerét saját maguk hitelesítsék, vagy automatikusan beolvassák a hitelesítő adatokat egy digitális tárból.

### <a name="use-azure-container-compute-upstream-projects"></a>Az Azure Container számítási feladatátviteli projektjeinek használata

> [!IMPORTANT]
> Az Azure technikai támogatási szolgálata nem támogatja a társított AK nyílt forráskódú projektjeit. A felhasználók saját maguk telepíthetnek fürtökbe, és visszajelzéseket gyűjthetnek a Közösségtől.

A következő [társított AK nyílt forráskódú projektek][aks-associated-projects] lehetővé teszik a hüvelyek automatikus hitelesítését, illetve a digitális tárolóban lévő hitelesítő adatok és kulcsok kérését. Ezeket a projekteket az Azure Container számítási főcsapata tartja karban, és a [rendelkezésre álló projektek átfogóbb listájának](https://github.com/Azure/container-compute-upstream/blob/master/README.md#support)részét képezi.

 * [Azure Active Directory Pod-identitás][aad-pod-identity]
 * [Azure Key Vault-szolgáltató a Secrets Store CSI-illesztőprogramhoz](https://github.com/Azure/secrets-store-csi-driver-provider-azure#usage)

#### <a name="use-pod-managed-identities"></a>A pod által felügyelt identitások használata

Az Azure-erőforrások felügyelt identitása lehetővé teszi, hogy a pod hitelesítse magát az azt támogató Azure-szolgáltatásokkal, például a Storage vagy az SQL használatával. A pod olyan Azure-identitáshoz van rendelve, amely lehetővé teszi, hogy a hitelesítés Azure Active Directory és digitális jogkivonatot kapjon. Ez a digitális jogkivonat olyan egyéb Azure-szolgáltatásokhoz is bemutatható, amelyek azt ellenőrizzék, hogy a pod jogosult-e a szolgáltatás elérésére és a szükséges műveletek elvégzésére. Ez a módszer azt jelenti, hogy az adatbázis-kapcsolatok sztringek esetében nem szükségesek titkos kódok, például:. A pod felügyelt identitás egyszerűsített munkafolyamata az alábbi ábrán látható:

:::image type="content" source="media/developer-best-practices-pod-security/basic-pod-identity.svg" alt-text="Egyszerűsített munkafolyamat a pod által felügyelt identitáshoz az Azure-ban":::

Felügyelt identitás esetén az alkalmazás kódjának nem kell tartalmaznia a szolgáltatásokhoz, például az Azure Storage-hoz való hozzáféréshez szükséges hitelesítő adatokat. Mivel mindegyik Pod a saját identitásával hitelesít, így ellenőrizheti és ellenőrizheti a hozzáférést. Ha az alkalmazása más Azure-szolgáltatásokhoz kapcsolódik, akkor a felügyelt identitások használatával korlátozhatja a hitelesítő adatok újrafelhasználását és a kitettség kockázatát.

A pod-identitásokkal kapcsolatos további információkért lásd: [AK-fürtök beállítása a pod felügyelt identitások és az alkalmazásai használatára][aad-pod-identity]

#### <a name="use-azure-key-vault-with-secrets-store-csi-driver"></a>Azure Key Vault használata a Secrets Store CSI-illesztőprogrammal

A pod Identity projekt használata lehetővé teszi a hitelesítést az Azure-szolgáltatások támogatásával. Az Azure-erőforrásokhoz felügyelt identitás nélküli saját szolgáltatások vagy alkalmazások esetén a hitelesítő adatok vagy kulcsok használatával továbbra is hitelesíthető. A titkos tartalmak tárolására a digitális tároló használható.

Ha az alkalmazásoknak hitelesítő adatokra van szükségük, kommunikálnak a digitális tárolóval, lekérik a legújabb titkos tartalmakat, majd csatlakoznak a szükséges szolgáltatáshoz. Azure Key Vault lehet ez a digitális tároló. A következő ábrán látható az egyszerűsített munkafolyamat, amely a Azure Key Vault a pod felügyelt identitások használatával beolvassa a hitelesítő adatokat.

:::image type="content" source="media/developer-best-practices-pod-security/basic-key-vault.svg" alt-text="Egyszerűsített munkafolyamat a hitelesítő adatok beolvasásához a Key Vault Pod által felügyelt identitás használatával":::

A Key Vault a titkokat, például a hitelesítő adatokat, a Storage-fiók kulcsait vagy a tanúsítványokat tárolja és rendszeresen elforgatja. A [Secrets Store CSI-illesztőprogram Azure Key Vault szolgáltatójának](https://github.com/Azure/secrets-store-csi-driver-provider-azure#usage)használatával integrálhatja Azure Key Vaultt egy AK-fürttel. A Secrets Store CSI-illesztőprogram lehetővé teszi, hogy az AK-fürt natív módon beolvassa a titkos tartalmat a Key Vaultból, és biztonságosan biztosítsa azokat csak a kérelmező Pod számára. Működjön együtt a fürt üzemeltetőjével, hogy a Secrets Store CSI-illesztőprogramot az AK munkavégző csomópontokra telepítse. A pod felügyelt identitással hozzáférést kérhet Key Vaulthoz, és beolvashatja a Secrets Store CSI-illesztőprogramon keresztül szükséges titkos tartalmat.

A Secrets Store-ban található Azure Key Vault a 1,16-es vagy újabb Kubernetes-verziót használó Linux-csomópontokhoz és hüvelyekhez használható. A Windows-csomópontok és a hüvelyek esetében a 1,18-es vagy újabb Kubernetes-verzió szükséges.

## <a name="next-steps"></a>Következő lépések

Ez a cikk a hüvely biztonságossá tételére koncentrál. Ezen területek némelyikének megvalósításához tekintse meg a következő cikkeket:

* [Felügyelt identitások használata Azure-erőforrásokhoz AK-val][aad-pod-identity]
* [Azure Key Vault integrálása AK-val][aks-keyvault-csi-driver]

<!-- EXTERNAL LINKS -->
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity#demo
[aks-keyvault-csi-driver]: https://github.com/Azure/secrets-store-csi-driver-provider-azure#usage
[linux-capabilities]: http://man7.org/linux/man-pages/man7/capabilities.7.html
[selinux-labels]: https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.18/#selinuxoptions-v1-core
[aks-associated-projects]: https://awesomeopensource.com/projects/aks?categoryPage=11

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-container-image-management]: operator-best-practices-container-image-management.md
[aks-pod-identities]: operator-best-practices-identity.md#use-pod-identities
[apparmor-seccomp]: operator-best-practices-cluster-security.md#secure-container-access-to-resources
