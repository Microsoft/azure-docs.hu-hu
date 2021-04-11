---
title: Ajánlott eljárások a fürt biztonságához
titleSuffix: Azure Kubernetes Service
description: Ismerje meg az Azure Kubernetes Service-ben (ak) a fürt biztonságának és frissítéseinek kezelésével kapcsolatos ajánlott eljárásokat.
services: container-service
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: ea63db2d8868a1333ae264c9cfdf31d0b7397a83
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105153"
---
# <a name="best-practices-for-cluster-security-and-upgrades-in-azure-kubernetes-service-aks"></a>Ajánlott eljárások a fürtök biztonságához és frissítéséhez az Azure Kubernetes szolgáltatásban (ak)

A fürtök az Azure Kubernetes szolgáltatásban (ak) való kezelése során kulcsfontosságú szempont a számítási feladatok és az adatok biztonsága. Ha a több-bérlős fürtöket logikai elkülönítéssel futtatja, akkor különösen az erőforrás-és munkaterhelések elérését kell biztosítania. A legújabb Kubernetes és Node OS biztonsági frissítések alkalmazásával csökkentheti a támadás kockázatát.

Ebből a cikkből megtudhatja, hogyan védheti meg az AK-fürtöt. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Az API Server-hozzáférés biztonságossá tételéhez használja a Azure Active Directory és a Kubernetes szerepköralapú hozzáférés-vezérlést (Kubernetes RBAC).
> * Biztonságos tároló hozzáférése a csomópont erőforrásaihoz.
> * Frissítsen egy AK-fürtöt a legújabb Kubernetes-verzióra.
> * Tartsa naprakészen a csomópontokat, és automatikusan alkalmazza a biztonsági javításokat.

Olvassa el a [tárolók képkezelésével][best-practices-container-image-management] és a [Pod biztonsággal][best-practices-pod-security]kapcsolatos ajánlott eljárásokat is.

Az [Azure Kubernetes Services integrációját a Security Center][security-center-aks] segítségével is felhasználhatja a fenyegetések észlelésére és az AK-fürtök biztonságossá tételére vonatkozó javaslatok megtekintésére.

## <a name="secure-access-to-the-api-server-and-cluster-nodes"></a>Biztonságos hozzáférés az API-kiszolgálóhoz és a fürtcsomópontokhöz

> **Útmutatás az ajánlott eljárásokhoz** 
>
> A fürt biztonságossá tételének egyik legfontosabb módja a Kubernetes API-kiszolgálóhoz való hozzáférés biztosítása. Az API-kiszolgálóhoz való hozzáférés szabályozásához integrálja a Kubernetes-RBAC a Azure Active Directory (Azure AD) szolgáltatással. Ezekkel a vezérlőkkel ugyanúgy védheti az AK-t, mint az Azure-előfizetésekhez való hozzáférést.

A Kubernetes API-kiszolgáló egyetlen kapcsolódási pontot biztosít a fürtökön belüli műveletek végrehajtásához. Az API-kiszolgálóhoz való hozzáférés biztonságossá tételéhez és naplózásához korlátozza a hozzáférést, és adja meg a lehető legalacsonyabb jogosultsági szinteket. Habár ez a megközelítés nem egyedi a Kubernetes, különösen fontos, ha logikailag elkülöníti az AK-fürtöt a több-bérlős használatra.

Az Azure AD egy nagyvállalati használatra kész Identitáskezelés megoldást kínál, amely az AK-fürtökkel integrálható. Mivel a Kubernetes nem biztosít Identitáskezelés-kezelési megoldást, lehet, hogy az API-kiszolgálóhoz való hozzáférés részletességének korlátozására a rendszer lenyomja az adatokat. Az Azure AD-vel integrált, az AK-ban működő fürtökkel a meglévő felhasználói és csoportfiókok használatával hitelesítheti a felhasználókat az API-kiszolgálón.

![Azure Active Directory-integráció az AK-fürtökhöz](media/operator-best-practices-cluster-security/aad-integration.png)

Az Kubernetes RBAC és az Azure AD-Integration használatával biztonságossá teheti az API-kiszolgálót, és megadhatja a hatókörön belüli erőforrásokhoz, például egyetlen névtérhez szükséges minimális engedélyeket. Különböző Azure AD-felhasználókat vagy-csoportokat adhat meg különböző Kubernetes-szerepkörökkel. A részletes engedélyekkel korlátozhatja az API-kiszolgáló elérését, és a végrehajtott műveletek egyértelmű naplózási nyomvonalát is megadhatja.

Az ajánlott eljárás a *csoportok* használata a fájlokhoz és mappákhoz való hozzáférés biztosításához az egyéni identitások helyett. Ha például egy *Azure ad-csoporttagság segítségével* kívánja összekötni a felhasználókat, hogy az egyéni *felhasználók* helyett Kubernetes a szerepköröket. A felhasználó csoporttagság változásakor az AK-fürtön való hozzáférési engedélyeik ennek megfelelően változnak. 

Addig is tegyük fel, hogy az egyes felhasználókat közvetlenül egy szerepkörhöz, és a feladat funkciójának módosításaihoz kötik. Az Azure AD-csoporttagság frissítése közben az AK-fürt engedélyei nem fognak megjelenni. Ebben az esetben a felhasználó a szükségesnél több jogosultsággal ér véget.

További információ az Azure AD-integrációról, a Kubernetes RBAC és az Azure RBAC: [ajánlott eljárások a hitelesítéshez és engedélyezéshez az AK-ban][aks-best-practices-identity].

## <a name="secure-container-access-to-resources"></a>Biztonságos tároló hozzáférése az erőforrásokhoz

> **Útmutatás az ajánlott eljárásokhoz** 
> 
> Korlátozza a tárolók által végrehajtható műveletekhez való hozzáférést. Adja meg a legkevesebb engedélyt, és kerülje a gyökér-hozzáférés vagy a privilegizált eszkaláció használatának elkerülését.

Ugyanúgy kell megadnia a felhasználóknak vagy a csoportoknak a minimálisan szükséges jogosultságokat, ha a tárolókat csak a szükséges műveletekre és folyamatokra korlátozza. A támadás kockázatának csökkentése érdekében ne konfigurálja az alkalmazásokat és a tárolókat, amelyek fokozott jogosultságokat vagy gyökérszintű hozzáférést igényelnek. 

Adja meg például a következőt `allowPrivilegeEscalation: false` : Pod manifest. Ezek a beépített Kubernetes *Pod biztonsági környezetek* lehetővé teszik további engedélyek megadását, például a felhasználó vagy csoport futtatását, vagy az elérhető Linux-funkciókat. További ajánlott eljárások: [biztonságos Pod-hozzáférés az erőforrásokhoz][pod-security-contexts].

A tárolói műveletek még részletesebb szabályozásához használhatja a beépített linuxos biztonsági funkciókat is, például a *AppArmor* és a *seccompot*. 
1. Adja meg a linuxos biztonsági szolgáltatásokat a csomópont szintjén.
1. A funkciók egy Pod manifest használatával valósíthatók meg. 

A beépített linuxos biztonsági funkciók csak Linux-csomópontokon és hüvelyeken érhetők el.

> [!NOTE]
> Jelenleg a Kubernetes-környezetek nem teljesen biztonságosak az ellenséges, több-bérlős használatra. A további biztonsági funkciók, például a *AppArmor*, a *seccompot*, a *Pod biztonsági HÁZIRENDEK* vagy a Kubernetes-RBAC a csomópontok számára hatékonyan blokkolják a támadásokat. 
>
>Az ellenséges több-bérlős számítási feladatok futtatásakor az igaz biztonság érdekében csak a Hypervisort bízza meg. A Kubernetes biztonsági tartománya a teljes fürtvé válik, nem önálló csomópontként. 
>
> Az ilyen típusú ellenséges több-bérlős munkaterhelések esetében fizikailag elkülönített fürtöket kell használnia.

### <a name="app-armor"></a>Alkalmazás-Armor

A tároló műveleteinek korlátozásához használhatja a [AppArmor][k8s-apparmor] Linux kernel biztonsági modulját. A AppArmor a mögöttes AK Node operációs rendszer részeként érhető el, és alapértelmezés szerint engedélyezve van. Olyan AppArmor-profilokat hozhat létre, amelyek korlátozzák az olvasási, írási vagy végrehajtási műveleteket, illetve a rendszerfunkciókat, például a csatlakoztatási fájlrendszereket. Az alapértelmezett AppArmor-profilok a különböző `/proc` és `/sys` helyekhez való hozzáférést korlátozzák, és lehetővé teszik a tárolók logikai elkülönítését az alapul szolgáló csomópontról. A AppArmor minden Linux rendszeren futó alkalmazás esetében működik, nem csak Kubernetes hüvelyekben.

![AppArmor-profilok használata egy AK-fürtben a tároló műveleteinek korlátozásához](media/operator-best-practices-container-security/apparmor.png)

A AppArmor működés közbeni megtekintéséhez a következő példa egy olyan profilt hoz létre, amely megakadályozza a fájlok írását. 
1. [SSH][aks-ssh] -t egy AK-csomóponthoz.
1. Hozzon létre egy *deny-Write. profil* nevű fájlt.
1. Illessze be a következő tartalmat:

    ```
    #include <tunables/global>
    profile k8s-apparmor-example-deny-write flags=(attach_disconnected) {
      #include <abstractions/base>
  
      file,
      # Deny all file writes.
      deny /** w,
    }
    ```

A AppArmor-profilok hozzáadása a `apparmor_parser` parancs használatával történik. 
1. Adja hozzá a profilt a AppArmor-hez.
1. Adja meg az előző lépésben létrehozott profil nevét:

    ```console
    sudo apparmor_parser deny-write.profile
    ```

    Ha a profil helyesen van elemezve és alkalmazva a AppArmor, nem jelenik meg a kimenet, és a rendszer visszaküldi a parancssorba.

1. A helyi gépről hozzon létre egy " *Kaba-AppArmor. YAML*" nevű Pod-jegyzékfájlt. Ez a jegyzékfájl:
    * Meghatározza a következőhöz tartozó jegyzetet: `container.apparmor.security.beta.kubernetes` .
    * Az előző lépésekben létrehozott *deny-Write* profilra hivatkozik.

    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: hello-apparmor
      annotations:
        container.apparmor.security.beta.kubernetes.io/hello: localhost/k8s-apparmor-example-deny-write
    spec:
      containers:
      - name: hello
        image: mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
        command: [ "sh", "-c", "echo 'Hello AppArmor!' && sleep 1h" ]
    ```

1. Az üzembe helyezett Pod használatával ellenőrizze, hogy a *Hello-AppArmor* Pod a következőképpen van-e *Letiltva*:

    ```
    $ kubectl get pods

    NAME             READY   STATUS    RESTARTS   AGE
    aks-ssh          1/1     Running   0          4m2s
    hello-apparmor   0/1     Blocked   0          50s
    ```

További információ a AppArmor: [AppArmor-profilok a Kubernetes-ben][k8s-apparmor].

### <a name="secure-computing"></a>Biztonságos számítástechnika

Míg a AppArmor bármely Linux-alkalmazás esetében működik, a [seccompot (*mp* ure *comp* uting)][seccomp] a folyamat szintjén működik. A seccompot egy Linux kernel biztonsági modul is, és natív módon támogatja az AK-csomópontok által használt Docker-futtatókörnyezet. A seccompot segítségével korlátozhatja a tárolók folyamatának hívásait. Az ajánlott eljáráshoz igazíthatja a tároló minimális engedélyét kizárólag a futtatásához:
* Definiálás az engedélyezni vagy megtagadni kívánt műveletek szűrésével.
* Megjegyzés a pod YAML-jegyzéken belül a seccompot szűrővel való hozzárendeléshez. 

A seccompot működés közbeni megtekintéséhez hozzon létre egy szűrőt, amely megakadályozza a fájlok engedélyeinek módosítását. 
1. [SSH][aks-ssh] -t egy AK-csomóponthoz.
1. Hozzon létre egy */var/lib/kubelet/seccomp/Prevent-chmod* nevű seccompot-szűrőt.
1. Illessze be a következő tartalmat:

    ```json
    {
      "defaultAction": "SCMP_ACT_ALLOW",
      "syscalls": [
        {
          "name": "chmod",
          "action": "SCMP_ACT_ERRNO"
        },
        {
          "name": "fchmodat",
          "action": "SCMP_ACT_ERRNO"
        },
        {
          "name": "chmodat",
          "action": "SCMP_ACT_ERRNO"
        }
      ]
    }
    ```

    A 1,19-es és újabb verziókban a következőket kell konfigurálnia:

    ```json
    {
      "defaultAction": "SCMP_ACT_ALLOW",
      "syscalls": [
        {
          "names": ["chmod","fchmodat","chmodat"],
          "action": "SCMP_ACT_ERRNO"
        }
      ]
    }
    ```

1. A helyi gépről hozzon létre egy *AK-seccompot. YAML* nevű Pod-jegyzéket, és illessze be az alábbi tartalmat. Ez a jegyzékfájl:
    * Meghatározza a következőhöz tartozó jegyzetet: `seccomp.security.alpha.kubernetes.io` .
    * Az előző lépésben létrehozott *megakadályozás-chmod* szűrőre hivatkozik.

    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: chmod-prevented
      annotations:
        seccomp.security.alpha.kubernetes.io/pod: localhost/prevent-chmod
    spec:
      containers:
      - name: chmod
        image: mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
        command:
          - "chmod"
        args:
         - "777"
         - /etc/hostname
      restartPolicy: Never
    ```

    A 1,19-es és újabb verziókban a következőket kell konfigurálnia:

    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: chmod-prevented
    spec:
      securityContext:
        seccompProfile:
          type: Localhost
          localhostProfile: prevent-chmod
      containers:
      - name: chmod
        image: mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
        command:
          - "chmod"
        args:
         - "777"
         - /etc/hostname
      restartPolicy: Never
    ```

1. A minta Pod üzembe helyezése a [kubectl Apply][kubectl-apply] parancs használatával:

    ```console
    kubectl apply -f ./aks-seccomp.yaml
    ```

1. A pod állapot megtekintése a [kubectl Get hüvely][kubectl-get] parancs használatával. 
    * A pod hibát jelez. 
    * Az `chmod` seccompot szűrő nem futtatja a parancsot, ahogy az a következő példában látható:    

    ```
    $ kubectl get pods

    NAME                      READY     STATUS    RESTARTS   AGE
    chmod-prevented           0/1       Error     0          7s
    ```

További információ az elérhető szűrőkkel kapcsolatban: [seccompot biztonsági profilok a Docker-hez][seccomp].

## <a name="regularly-update-to-the-latest-version-of-kubernetes"></a>Rendszeres frissítés a Kubernetes legújabb verziójára

> **Útmutatás az ajánlott eljárásokhoz** 
> 
> Az új funkciók és hibajavítások naprakészen tartása érdekében rendszeresen frissítse a Kubernetes-verziót az AK-fürtben.

A Kubernetes a hagyományos infrastruktúra-platformoknál gyorsabb ütemben bocsátja ki az új funkciókat. A Kubernetes frissítései a következők:
* Új funkciók
* Hibák vagy biztonsági javítások 

Az új funkciók általában az *Alpha* és a *Beta* állapotba kerülnek, mielőtt azok *stabilak* lesznek. A stabil, általánosan elérhető és éles használatra ajánlott. A Kubernetes új funkciójának kiadási ciklusa lehetővé teszi a Kubernetes frissítését anélkül, hogy rendszeresen megtapasztalja a változások megszakítását vagy a telepítések és sablonok módosítását.

Az AK támogatja a Kubernetes három kisebb verzióját. Az új másodlagos javítás verziójának bevezetése után a rendszer kivonja a legrégebbi másodlagos verziót és a javítások támogatott kiadásait. A másodlagos Kubernetes frissítései rendszeres időközönként történnek. Ha a támogatáson belül szeretne maradni, ellenőrizze, hogy van-e irányítási folyamata a szükséges frissítések ellenőrzéséhez. További információ: [támogatott Kubernetes-verziók (ak][aks-supported-versions]).

A fürthöz elérhető verziók ellenőrzését a következő példában látható módon tekintheti meg az az [AK Get-Upgrades][az-aks-get-upgrades] parancs használatával:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

Ezután az az [AK upgrade][az-aks-upgrade] paranccsal frissítheti az AK-fürtöt. A frissítési folyamat biztonságos:
* A kordonok és a csatornák egyszerre egy csomóponttal rendelkeznek.
* Beütemezi a hüvelyeket a többi csomóponton.
* Üzembe helyez egy új csomópontot, amely a legújabb operációs rendszer-és Kubernetes verziót futtatja.

>[!IMPORTANT]
> Tesztelje az új alverziókat egy fejlesztői tesztkörnyezetben, és ellenőrizze, hogy a számítási feladatok kifogástalanok maradnak-e az új Kubernetes-verzióval. 
>
> A Kubernetes a számítási feladatokhoz tartozó API-k (például az 1,16-es verzióban) elavulttá válhatnak. Új verziók éles környezetben való üzembe helyezése esetén érdemes lehet [több Node-készletet használni külön verziókban](use-multiple-node-pools.md) , és egyenként frissíteni az egyes készleteket, hogy fokozatosan a frissítést a fürtön keresztül lehessen felépíteni. Ha több fürtöt futtat, egyszerre egy fürtöt kell frissítenie, hogy fokozatosan figyelje a hatásokat vagy a módosításokat.
>
>```azurecli-interactive
>az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version KUBERNETES_VERSION
>```

További információ az AK-beli Frissítésekről: az [AK-ban támogatott Kubernetes-verziók][aks-supported-versions] és az AK- [fürtök frissítése][aks-upgrade].

## <a name="process-linux-node-updates-and-reboots-using-kured"></a>Linux-csomópontok frissítésének és újraindításának feldolgozása a kured használatával

> **Útmutatás az ajánlott eljárásokhoz** 
> 
> Amíg az AK automatikusan letölti és telepíti a biztonsági javításokat az egyes Linux-csomópontokon, nem indul automatikusan újra. 
> 1. A szolgáltatással `kured` figyelheti a függőben lévő újraindításokat.
> 1. Biztonságosan kiürítheti a csomópontot, és lecsepegtetheti a csomópontot, hogy a csomópont újrainduljon.
> 1. Alkalmazza a frissítéseket.
> 1. Az operációs rendszer szempontjából a lehető legbiztonságosabb legyen. 

A Windows Server-csomópontok esetében rendszeresen hajtson végre egy AK-os frissítési műveletet a kihelyezett és a kiüríthető csomópontok biztonsága érdekében

Minden este a Linux-csomópontok a distro frissítési csatornán keresztül kapják meg a biztonsági javításokat. A rendszer automatikusan konfigurálja ezt a viselkedést, mivel a csomópontok egy AK-fürtben vannak telepítve. A munkaterhelések megszakadásának és lehetséges hatásának csökkentése érdekében a csomópontok nem indulnak el automatikusan, ha biztonsági javítást vagy kernel-frissítést igényel.

A nyílt forráskódú [kured (KUbernetes reboot Daemon)][kured] Project by Weaveworks figyeli a függőben lévő csomópontok újraindítását. Ha egy Linux-csomópont újraindítást igénylő frissítéseket alkalmaz, a csomópont biztonságos kiosztása és kivonása a fürt más csomópontjain lévő hüvelyek áthelyezésére és bevezetésére történik. A csomópont újraindítása után a rendszer visszaadja a fürtöt, és a Kubernetes folytatja a pod ütemezést. A fennakadások csökkentése érdekében a segítségével egyszerre csak egy csomópontot lehet újraindítani `kured` .

![Az AK-csomópont újraindítási folyamata a kured használatával](media/operator-best-practices-cluster-security/node-reboot-process.png)

Ha még szorosabban szeretné vezérelni az újraindításokat, `kured` integrálhatja a Prometheus-t, hogy megakadályozza az újraindítást, ha más karbantartási események vagy fürtbeli problémák vannak folyamatban. Ez az integráció csökkenti a bonyolultságot a csomópontok újraindításával, amikor aktívan más problémákkal kapcsolatos hibaelhárítást végez.

A csomópont-újraindítások kezelésével kapcsolatos további információkért lásd: [biztonsági és kernel-frissítések alkalmazása a csomópontokra az AK-ban][aks-kured].

## <a name="next-steps"></a>Következő lépések

Ez a cikk az AK-fürt biztonságossá tételére koncentrál. Ezen területek némelyikének megvalósításához tekintse meg a következő cikkeket:

* [Azure Active Directory integrálása AK-val][aks-aad]
* [AK-fürt frissítése a Kubernetes legújabb verziójára][aks-upgrade]
* [Biztonsági frissítések és csomópont-újraindítások feldolgozása a kured][aks-kured]

<!-- EXTERNAL LINKS -->
[kured]: https://github.com/weaveworks/kured
[k8s-apparmor]: https://kubernetes.io/docs/tutorials/clusters/apparmor/
[seccomp]: https://kubernetes.io/docs/concepts/policy/pod-security-policy/#seccomp
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- INTERNAL LINKS -->
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[aks-supported-versions]: supported-kubernetes-versions.md
[aks-upgrade]: upgrade-cluster.md
[aks-best-practices-identity]: concepts-identity.md
[aks-kured]: node-updates-kured.md
[aks-aad]: ./azure-ad-integration-cli.md
[best-practices-container-image-management]: operator-best-practices-container-image-management.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[pod-security-contexts]: developer-best-practices-pod-security.md#secure-pod-access-to-resources
[aks-ssh]: ssh.md
[security-center-aks]: ../security-center/defender-for-kubernetes-introduction.md
