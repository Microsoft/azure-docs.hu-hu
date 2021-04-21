---
title: Ajánlott fürtbiztonsági eljárások
titleSuffix: Azure Kubernetes Service
description: Ismerje meg a fürtüzemeltető ajánlott eljárásait a fürtbiztonság és -frissítések kezeléséhez a Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: 5cb103d843aafbb7f72c03d65b45fe3a84f8d1cd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782980"
---
# <a name="best-practices-for-cluster-security-and-upgrades-in-azure-kubernetes-service-aks"></a>Ajánlott fürtbiztonsági és frissítési eljárások Azure Kubernetes Service (AKS)

A fürtök AKS-Azure Kubernetes Service kezelése során a számítási feladatok és az adatbiztonság kulcsfontosságú szempont. Ha logikai elkülönítést használó több-bérlős fürtöt futtat, különösen az erőforrások és a számítási feladatok hozzáférésének biztonságossá kell adása. A legújabb Kubernetes- és csomópont-operációsrendszer-biztonsági frissítések alkalmazásával minimalizálhatja a támadás kockázatát.

Ez a cikk az AKS-fürt biztonságossá ezzel a feladatokkal foglalkozik. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Az Azure Active Directory és a Kubernetes szerepköralapú hozzáférés-vezérlés (Kubernetes RBAC) használatával biztosíthatja az API-kiszolgálók hozzáférését.
> * Tárolók biztonságos elérése a csomópont-erőforrásokhoz.
> * Frissítsen egy AKS-fürtöt a legújabb Kubernetes-verzióra.
> * A csomópontok naprakészen tartása és a biztonsági javítások automatikus alkalmazása.

A tároló rendszerkép-kezelésével és a podok biztonságával kapcsolatos ajánlott eljárásokat is [elolvashatja.][best-practices-pod-security] [][best-practices-container-image-management]

Az Azure [Kubernetes Services][security-center-aks] és a Security Center is használhatók a fenyegetések észleléséhez és az AKS-fürtök biztonságossá tétele érdekében tett javaslatok megtekintéséhez.

## <a name="secure-access-to-the-api-server-and-cluster-nodes"></a>Biztonságos hozzáférés az API-kiszolgálóhoz és a fürtcsomópontokhoz

> **Útmutatás az ajánlott eljárásokhoz** 
>
> A fürt biztonságának egyik legfontosabb módja a Kubernetes API-kiszolgálóhoz való hozzáférés biztosítása. Az API-kiszolgálóhoz való hozzáférés szabályozásához integrálja a Kubernetes RBAC-t a Azure Active Directory (Azure AD) használatával. Ezekkel a vezérlőkkel ugyanúgy biztosíthatja az AKS-t, mint az Azure-előfizetések biztonságos hozzáférését.

A Kubernetes API-kiszolgáló egyetlen csatlakozási pontot biztosít a fürtön belüli műveletek végrehajtásához szükséges kérések számára. Az API-kiszolgálóhoz való hozzáférés biztonságossá és naplózásához korlátozza a hozzáférést, és adja meg a lehető legalacsonyabb engedélyszinteket. bár ez a megközelítés nem egyedi a Kubernetesben, különösen akkor fontos, ha logikailag elkülönítette az AKS-fürtöt több-bérlős használatra.

Az Azure AD egy nagyvállalati használatra kész identitáskezelési megoldást biztosít, amely integrálható az AKS-fürtökhöz. Mivel a Kubernetes nem biztosít identitáskezelési megoldást, előfordulhat, hogy a rendszer nem tudja részletesen korlátozni az API-kiszolgálóhoz való hozzáférést. Az AKS-be integrált Azure AD-fürtök esetében meglévő felhasználói és csoportfiókok használatával hitelesítheti a felhasználókat az API-kiszolgálón.

![Azure Active Directory AKS-fürtök integrációja](media/operator-best-practices-cluster-security/aad-integration.png)

A Kubernetes RBAC és az Azure AD-integráció használatával biztonságossá teszi az API-kiszolgálót, és meg tudja adni a hatókörrel rendelkező erőforráskészlethez szükséges minimális engedélyeket, például egyetlen névteret. Különböző Azure AD-felhasználók vagy -csoportok számára különböző Kubernetes-szerepköröket adhat meg. A részletes engedélyekkel korlátozhatja az API-kiszolgálóhoz való hozzáférést, és egyértelmű auditálást nyújthat a végrehajtott műveletekről.

Az ajánlott eljárás az, ha csoportok használatával biztosít hozzáférést a fájlokhoz és mappákhoz az egyéni identitások helyett.  Például egy Azure *AD-csoporttagság* használatával a felhasználókat az egyes felhasználók helyett Kubernetes-szerepkörökhöz *kötheti.* A felhasználó csoporttagságának változását az AKS-fürtre vonatkozó hozzáférési engedélyei ennek megfelelően módosítják. 

Addig is tegyük fel, hogy az egyes felhasználót közvetlenül egy szerepkörhöz köti, és megváltozik a feladat függvénye. Bár az Azure AD-csoporttagságok frissülnek, az AKS-fürtre vonatkozó engedélyük nem. Ebben a forgatókönyvben a felhasználó a szükségesnél több engedéllyel fog rendelkezni.

További információ az Azure AD-integrációról, a Kubernetes RBAC-ről és az Azure RBAC-ről: Ajánlott eljárások az [AKS-beli hitelesítéshez és engedélyezéshez.][aks-best-practices-identity]

## <a name="secure-container-access-to-resources"></a>Tárolók erőforrásokhoz való hozzáférésének biztosítása

> **Útmutatás az ajánlott eljárásokhoz** 
> 
> Korlátozza a hozzáférést a tárolók által végrehajtható műveletekre. Adja meg a legkevesebb engedélyt, és kerülje a gyökér szintű hozzáférés vagy az emelt szintű eszkalálás használatát.

Ugyanúgy, ahogyan a felhasználóknak vagy csoportoknak meg kell adni a minimálisan szükséges jogosultságokat, a tárolókat is csak a szükséges műveletekre és folyamatokra kell korlátozni. A támadás kockázatának minimalizálása érdekében kerülje az eszkalált jogosultságokat vagy gyökér hozzáférést igénylő alkalmazások és tárolók konfigurálását. 

Például állítsa be a `allowPrivilegeEscalation: false` halmazt a podjegyzékben. Ezekkel a beépített Kubernetes *podbiztonsági* környezetekkel további engedélyeket határozhat meg, például a futtatás felhasználóját vagy csoportját, vagy az elérhetővé tehető Linux-képességeket. További ajánlott eljárásokért lásd: [Podok erőforrásokhoz való biztonságos elérése.][pod-security-contexts]

A tárolóműveletek még részletesebb vezérléséhez olyan beépített Linux biztonsági funkciókat is használhat, mint az *AppArmor* és *a seccomp.* 
1. Linuxos biztonsági funkciók definiálása csomópontszinten.
1. Funkciók megvalósítása podjegyzéken keresztül. 

A beépített Linux biztonsági funkciók csak Linux-csomópontokon és podokon érhetők el.

> [!NOTE]
> A Kubernetes-környezetek jelenleg nem teljesen biztonságosak a több-bérlős használathoz. A további biztonsági funkciók, például az *AppArmor,* *a seccomp,* a *podbiztonsági* szabályzatok vagy a Kubernetes RBAC a csomópontok számára hatékonyan letiltják a biztonsági réseket. 
>
>A valódi biztonság érdekében a rosszindulatú több-bérlős számítási feladatok futtatásakor csak a hipervizorban bízzon meg. A Kubernetes biztonsági tartománya lesz a teljes fürt, nem pedig egy különálló csomópont. 
>
> Az ilyen típusú több-bérlős számítási feladatokhoz fizikailag elkülönített fürtök használata érdemes.

### <a name="app-armor"></a>App Armor

A tárolóműveletek korlátozására használhatja az [AppArmor][k8s-apparmor] Linux kernelbiztonsági modult. Az AppArmor a mögöttes AKS-csomópont operációs rendszerének részeként érhető el, és alapértelmezés szerint engedélyezve van. Olyan AppArmor-profilokat hoz létre, amelyek korlátozzák az olvasási, írási vagy végrehajtási műveleteket, vagy rendszerfunkciókat, például a fájlrendszerek csatlakoztatását. Az alapértelmezett AppArmor-profilok korlátozzák a különböző és helyekhez való hozzáférést, és lehetőséget biztosítanak a tárolók logikai elkülönítéséhez a `/proc` `/sys` mögöttes csomóponttól. Az AppArmor nem csak a Kubernetes-podok, de minden Linuxon futó alkalmazáshoz használható.

![Az AKS-fürtökben használt AppArmor-profilok a tárolóműveletek korlátozására](media/operator-best-practices-container-security/apparmor.png)

Az AppArmor műveletének megtekintése érdekében az alábbi példa egy olyan profilt hoz létre, amely megakadályozza a fájlokba való írást. 
1. [SSH-t][aks-ssh] egy AKS-csomóponthoz.
1. Hozzon létre egy *deny-write.profile nevű fájlt.*
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

Az AppArmor-profilok az paranccsal vannak `apparmor_parser` hozzáadva. 
1. Adja hozzá a profilt az AppArmorhoz.
1. Adja meg az előző lépésben létrehozott profil nevét:

    ```console
    sudo apparmor_parser deny-write.profile
    ```

    Ha a profil helyesen van elemezve, és az AppArmorra van alkalmazva, akkor nem fog kimenetet látni, és visszakerül a parancssorba.

1. A helyi gépen hozzon létre egy *podjegyzéket aks-apparmor.yaml névvel.* Ez a jegyzékfájl:
    * Egy jegyzetet határoz meg a `container.apparmor.security.beta.kubernetes` számára.
    * Az előző lépésekben létrehozott *deny-write* profilra hivatkozik.

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

1. A pod üzembe helyezése után ellenőrizze, hogy a *hello-apparmor* pod blokkoltként *jelenik-e meg:*

    ```
    $ kubectl get pods

    NAME             READY   STATUS    RESTARTS   AGE
    aks-ssh          1/1     Running   0          4m2s
    hello-apparmor   0/1     Blocked   0          50s
    ```

További információ az AppArmorról: [AppArmor-profilok a Kubernetesben.][k8s-apparmor]

### <a name="secure-computing"></a>Biztonságos számítástechnika

Míg az AppArmor bármely Linux-alkalmazással működik, a [seccomp (*secure* *comp* uting)][seccomp] folyamatszinten működik. A Seccomp egy Linux kerneles biztonsági modul is, amelyet natív módon támogat az AKS-csomópontok által használt Docker-runtime. A seccompgal korlátozhatja a tárolófolyamat-hívásokat. Igazodjon az ajánlott eljáráshoz, ha a tárolónak csak minimálisan szükséges engedélyt ad a futtatáshoz:
* A definiálás a szűrőkkel szűri az engedélyezni vagy megtagadni szükséges műveleteket.
* Egy pod YAML-jegyzékfájlon belüli jegyzetekkel társítva a seccomp szűrővel. 

A seccomp in action (scomp művelet) funkcióhoz hozzon létre egy szűrőt, amely megakadályozza a fájl engedélyeinek megváltoztatását. 
1. [SSH-hoz][aks-ssh] egy AKS-csomóponthoz.
1. Hozzon létre egy */var/lib/kubelet/seccomp/prevent-chmod nevű scomp szűrőt.*
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

    Az 1.19-es és újabb verziókban a következőket kell konfigurálnia:

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

1. A helyi gépen hozzon létre egy *aks-seccomp.yaml* nevű podjegyzéket, és illessze be a következő tartalmat. Ez a jegyzékfájl:
    * Egy jegyzetet határoz meg a `seccomp.security.alpha.kubernetes.io` számára.
    * Az előző lépésben létrehozott *prevent-chmod* szűrőre hivatkozik.

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

    Az 1.19-es és újabb verziókban a következőket kell konfigurálnia:

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

1. A mintapod üzembe helyezése a [kubectl apply paranccsal:][kubectl-apply]

    ```console
    kubectl apply -f ./aks-seccomp.yaml
    ```

1. Tekintse meg a pod állapotát a [kubectl get pods paranccsal.][kubectl-get] 
    * A pod hibát jelez. 
    * A seccomp szűrő megakadályozza a parancs futtatását, ahogy az az alábbi `chmod` példakimenetben látható:    

    ```
    $ kubectl get pods

    NAME                      READY     STATUS    RESTARTS   AGE
    chmod-prevented           0/1       Error     0          7s
    ```

További információ az elérhető szűrőkről: Biztonsági profilok [a Dockerhez.][seccomp]

## <a name="regularly-update-to-the-latest-version-of-kubernetes"></a>Rendszeres frissítés a Kubernetes legújabb verziójára

> **Útmutatás az ajánlott eljárásokhoz** 
> 
> Az új funkciók és hibajavítások legújabb verziójának érdekében rendszeresen frissítse a Kubernetes verzióját az AKS-fürtben.

A Kubernetes gyorsabban ad ki új funkciókat, mint a hagyományos infrastruktúraplatformok. A Kubernetes-frissítések a következők:
* Új funkciók
* Hiba- vagy biztonsági javítások 

Az új funkciók általában az *alfa* és a *béta* állapot között mozognak, mielőtt *stabilsá válnak.* Ha a stabil, a általánosan elérhető és ajánlott az éles környezetben való használathoz. A Kubernetes új funkció-kiadási ciklusa lehetővé teszi a Kubernetes frissítését anélkül, hogy rendszeresen ütközne a használvó változásokba, vagy módosítja az üzemelő példányokat és sablonokat.

Az AKS a Kubernetes három alverzióját támogatja. Az új aljavításverzió bevezetése után a rendszer visszavonja a legrégebbi alverziót és a támogatott javításokat. A kisebb Kubernetes-frissítések rendszeres időközönként történnek. Annak érdekében, hogy a támogatáson belül maradjon, ellenőrizze a szükséges frissítéseket egy cégirányítási folyamattal. További információ: [Supported Kubernetes versions AKS (Támogatott Kubernetes-verziók, AKS).][aks-supported-versions]

A fürthöz elérhető verziók ellenőrzéséhez használja az [az aks get-upgrades][az-aks-get-upgrades] parancsot az alábbi példában látható módon:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

Ezután az [az aks upgrade][az-aks-upgrade] paranccsal frissítheti az AKS-fürtöt. A frissítési folyamat biztonságosan:
* Egyszerre csak egy csomópontot ürít és ürít ki.
* Podokat ütemez a többi csomóponton.
* Üzembe helyez egy új csomópontot, amely a legújabb operációs rendszert és Kubernetes-verziókat futtatja.

>[!IMPORTANT]
> Tesztelje az új alverziókat egy fejlesztési tesztkörnyezetben, és ellenőrizze, hogy a számítási feladat kifogástalan állapotú-e az új Kubernetes-verzióval. 
>
> A Kubernetes kiveheti az API-kat (például az 1.16-os verzióban), amelyekre a számítási feladatok támaszkodnak. Amikor új verziókat hoz létre [](use-multiple-node-pools.md) az éles környezetben, érdemes lehet több csomópontkészletet használni külön verziókhoz, és egyesével frissíteni az egyes készleteket, hogy a frissítést fokozatosan váltsa át a fürtön. Ha több fürtöt futtat, egyszerre csak egy fürtöt frissítsen a hatás vagy a változások fokozatos figyelése érdekében.
>
>```azurecli-interactive
>az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version KUBERNETES_VERSION
>```

Az AKS frissítésekkel kapcsolatos további információkért lásd: [Támogatott Kubernetes-verziók az AKS-ban][aks-supported-versions] és [AKS-fürt frissítése.][aks-upgrade]

## <a name="process-linux-node-updates-and-reboots-using-kured"></a>Linux-csomópontfrissítések és -újraindítások feldolgozása a virtuális gép használatával

> **Útmutatás az ajánlott eljárásokhoz** 
> 
> Bár az AKS minden Linux-csomóponton automatikusan letölti és telepíti a biztonsági javításokat, nem indítja újra automatikusan. 
> 1. A `kured` függőben lévő újraindítások figyeléshez használható.
> 1. Biztonságosan rendszerelheti és ürítheti a csomópontot, hogy a csomópont újrainduljon.
> 1. Alkalmazza a frissítéseket.
> 1. Az operációs rendszer tekintetében a lehető legbiztonságosabbnak kell lennie. 

Windows Server-csomópontok esetén rendszeresen végezzen el egy AKS-frissítési műveletet a podok biztonságos elkábellel történő üzembe helyezéséhez és ürítéséhez, valamint a frissített csomópontok üzembe helyezéséhez.

Az AKS Linux-csomópontjai minden este a disztribúció frissítési csatornáján keresztül kapják meg a biztonsági javításokat. Ezt a viselkedést a rendszer automatikusan konfigurálja a csomópontok AKS-fürtben való üzembe helyezésekor. A megszakítások és a futó számítási feladatokra gyakorolt esetleges hatások minimalizálása érdekében a csomópontok nem indulnak újra automatikusan, ha egy biztonsági javítás vagy kernelfrissítés ezt igényli.

A Weaveworks által tervezett nyílt forráskódú [(KUbernetes REboot Daemon)][kured] projekt a csomópont függőben lévő újraindítását figyeli. Amikor egy Linux-csomópont újraindítást igénylő frissítéseket alkalmaz, a csomópont biztonságosan el van különülve és ki van ürítve a podok áthelyezéséhez és ütemezéséhez a fürt más csomópontjaira. A csomópont újraindítása után a rendszer ismét hozzáadja a fürthöz, és a Kubernetes folytatja a podütemezést. A megszakítások minimalizálása érdekében a egyszerre csak egy csomópontot engedélyez a számára, hogy `kured` újrainduljon.

![Az AKS-csomópont újraindítási folyamata a következővel:](media/operator-best-practices-cluster-security/node-reboot-process.png)

Ha még jobban szeretné szabályozni az újraindításokat, integrálhatja a Prometheusszal, hogy megakadályozza az újraindításokat, ha más karbantartási események vagy fürt problémák `kured` vannak folyamatban. Ez az integráció csökkenti a csomópontok újraindításával kapcsolatos bonyodalmakat, miközben Ön aktívan hárít el más problémákat.

A csomópont-újraindítások kezelésével kapcsolatos további információkért lásd: Biztonsági és kernelfrissítések alkalmazása csomópontokra [az AKS-ban.][aks-kured]

## <a name="next-steps"></a>Következő lépések

Ez a cikk az AKS-fürt biztonságossá tára volt. Ezen területek némelyikének megvalósításához tekintse meg a következő cikkeket:

* [Integráció Azure Active Directory AKS-sel][aks-aad]
* [AKS-fürt frissítése a Kubernetes legújabb verziójára][aks-upgrade]
* [Biztonsági frissítések feldolgozása és csomópont-újraindítások a rendszer által][aks-kured]

<!-- EXTERNAL LINKS -->
[kured]: https://github.com/weaveworks/kured
[k8s-apparmor]: https://kubernetes.io/docs/tutorials/clusters/apparmor/
[seccomp]: https://kubernetes.io/docs/concepts/policy/pod-security-policy/#seccomp
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- INTERNAL LINKS -->
[az-aks-get-upgrades]: /cli/azure/aks#az_aks_get_upgrades
[az-aks-upgrade]: /cli/azure/aks#az_aks_upgrade
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
