---
title: Peremhálózati tároló beállításjegyzékének engedélyezése Azure Stack Edge Pro GPU-eszközön
description: Ismerteti, hogyan lehet engedélyezni a helyi peremhálózati tároló beállításjegyzékét Azure Stack Edge Pro GPU-eszközön.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 56b691b2755b5e248b16e338f8fd82864f5bf218
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105560335"
---
# <a name="enable-edge-container-registry-on-your-azure-stack-edge-pro-gpu-device"></a>Edge Container Registry engedélyezése a Azure Stack Edge Pro GPU-eszközön

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Ez a cikk bemutatja, hogyan engedélyezheti a peremhálózati tároló beállításjegyzékét, és hogyan használhatja azt a Kubernetes-fürtön belül az Azure Stack Edge Pro-eszközön. A cikkben használt példa részletesen ismerteti, hogyan kell leküldeni egy rendszerképet a forrás-beállításjegyzékből, ebben az esetben a Microsoft Container registryben az Azure Stack Edge-eszköz, a peremhálózati tároló beállításjegyzékének beállításjegyzékében.

### <a name="about-edge-container-registry"></a>Tudnivalók az Edge Container registryről

A tárolóban tárolt számítási alkalmazások tároló lemezképeken futnak, és ezek a lemezképek a beállításjegyzékben tárolódnak. A beállításjegyzékek lehetnek nyilvánosak, például a Docker hub, a Private vagy a Cloud Provider felügyelt, például a Azure Container Registry. További információ: a beállításjegyzékek [, adattárak és rendszerképek](../container-registry/container-registry-concepts.md).

Az Edge Container Registry egy tárházat biztosít a Azure Stack Edge Pro-eszközén. Ezt a beállításjegyzéket használhatja a privát tárolók képeinek tárolására és kezelésére.

A többcsomópontos környezetekben a tárolók lemezképeit le lehet tölteni, és egyszer kell leküldeni a peremhálózati tároló beállításjegyzékbe. Az összes Edge-alkalmazás használhatja a peremhálózati tároló beállításjegyzékét a későbbi üzembe helyezésekhez.


## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzákezd, győződjön meg az alábbiakról:

1. Egy Azure Stack Edge Pro-eszközhöz férhet hozzá.

1. Aktiválta Azure Stack Edge Pro-eszközét a [Azure stack Edge Pro aktiválása](azure-stack-edge-gpu-deploy-activate.md)című témakörben leírtak szerint.

1. Engedélyezte a számítási szerepkört az eszközön. Egy Kubernetes-fürt is létrejött az eszközön, amikor az eszközön a számítás [konfigurálása az Azure stack Edge Pro-eszközön](azure-stack-edge-gpu-deploy-configure-compute.md)című témakör útmutatása szerint konfigurálta az eszközt.

1. A Kubernetes API-végpont a helyi webes felhasználói felület **eszköz** lapján található. További információkért tekintse meg az [KUBERNETES API-végpont beolvasása](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints)című témakör utasításait.

1. Egy [támogatott operációs rendszerrel](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device)rendelkező ügyfélszoftverhez férhet hozzá. Windows-ügyfél használata esetén a rendszernek a PowerShell 5,0-es vagy újabb verzióját kell futtatnia az eszköz eléréséhez.

    1. Ha szeretné lekérni és leküldeni a saját tároló lemezképeit, győződjön meg arról, hogy a rendszeren telepítve van a Docker-ügyfél. Windows-ügyfél használata esetén [telepítse a Docker Desktopot Windows](https://docs.docker.com/docker-for-windows/install/)rendszeren.  


## <a name="enable-container-registry-as-add-on"></a>Tároló beállításjegyzékének engedélyezése bővítményként

Az első lépés a peremhálózati tároló beállításjegyzékének engedélyezése bővítményként.

1. [Kapcsolódjon az eszköz PowerShell-felületéhez](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface). 

1. A tároló-beállításjegyzék bővítményként való engedélyezéséhez írja be a következőt: 

    `Set-HcsKubernetesContainerRegistry`
    
    A művelet végrehajtása több percet is igénybe vehet.

    Az alábbi példában a parancs kimenete látható:  
            
    ```powershell
    [10.128.44.40]: PS>Set-HcsKubernetesContainerRegistry
    Operation completed successfully. Use Get-HcsKubernetesContainerRegistryInfo for credentials    
    ```
            
1. A tároló-beállításjegyzék részleteinek beszerzéséhez írja be a következőt:

    `Get-HcsKubernetesContainerRegistryInfo`

    Itt látható a következő parancs mintája:  
    
    ```powershell
    [10.128.44.40]: PS> Get-HcsKubernetesContainerRegistryInfo
                
    Endpoint                                   IPAddress    Username     Password
    --------                                   ---------    --------     --------
    ecr.dbe-hw6h1t2.microsoftdatabox.com:31001 10.128.44.41 ase-ecr-user i3eTsU4zGYyIgxV
    ``` 

1. Jegyezze fel a felhasználónevet és a jelszót a kimenetében `Get-HcsKubernetesContainerRegistryInfo` . A rendszer ezeket a hitelesítő adatokat használja a peremhálózati tároló beállításjegyzékbe való bejelentkezéshez, miközben képeket küld.         


## <a name="manage-container-registry-images"></a>Tároló beállításjegyzék-lemezképek kezelése

Előfordulhat, hogy a Azure Stack Edge-eszközön kívülről szeretné elérni a tároló-beállításjegyzéket. Előfordulhat, hogy a beállításjegyzékben is szeretné leküldeni vagy lekérni a lemezképeket.

Kövesse az alábbi lépéseket a peremhálózati tároló beállításjegyzékének eléréséhez:

1. Szerezze be a peremhálózati tároló beállításjegyzékének végpontjának adatait.
    1. Az eszköz helyi felhasználói felületén válassza az **eszköz** lehetőséget.
    1. Keresse meg a **peremhálózati tároló beállításjegyzékének végpontját**.
        ![Peremhálózati tároló beállításjegyzék-végpontja az eszköz lapján](media/azure-stack-edge-gpu-edge-container-registry/get-edge-container-registry-endpoint-1.png) 
    1. Másolja ezt a végpontot, és hozzon létre egy megfelelő DNS-bejegyzést az `C:\Windows\System32\Drivers\etc\hosts` ügyfél fájljában, hogy csatlakozhasson a peremhálózati tároló beállításjegyzék-végponthoz. 

        <IP address of the Kubernetes main node>    <Edge container registry endpoint> 
        
        ![DNS-bejegyzés hozzáadása az Edge Container Registry-végponthoz](media/azure-stack-edge-gpu-edge-container-registry/add-domain-name-service-entry-hosts-1.png)    

1. Töltse le a peremhálózati tároló beállításjegyzékbeli tanúsítványát a helyi felhasználói felületen. 
    1. Az eszköz helyi felhasználói felületén válassza a **tanúsítványok** lehetőséget.
    1. Keresse meg az **Edge Container Registry-tanúsítvány** bejegyzését. A bejegyzéstől jobbra kattintson a **Letöltés** gombra a peremhálózati tároló beállításjegyzékbeli tanúsítványának letöltéséhez az ügyfél rendszerén, amelyet az eszköz eléréséhez fog használni. 

        ![Edge Container Registry-végpont tanúsítványának letöltése](media/azure-stack-edge-gpu-edge-container-registry/download-edge-container-registry-endpoint-certificate-1.png)  

1. Telepítse a letöltött tanúsítványt az ügyfélen. Ha Windows-ügyfelet használ, kövesse az alábbi lépéseket: 
    1. Válassza ki a tanúsítványt, és a **tanúsítvány importálása varázslóban** válassza a hely tárolása **helyi gépként** lehetőséget. 

        ![1. tanúsítvány telepítése](media/azure-stack-edge-gpu-edge-container-registry/install-certificate-1.png) 
    
    1. Telepítse a tanúsítványt a helyi gépre a megbízható legfelső szintű tárolóban. 

        ![A 2. tanúsítvány telepítése](media/azure-stack-edge-gpu-edge-container-registry/install-certificate-2.png) 

1. A tanúsítvány telepítése után indítsa újra a Docker-ügyfelet a rendszeren.

1. Jelentkezzen be a peremhálózati tároló beállításjegyzékbe. Típus:

    `docker login <Edge container registry endpoint> -u <username> -p <password>`

    Adja meg a peremhálózati tároló beállításjegyzékének végpontját az **eszközök** lapon, valamint a kimenetében kapott felhasználónevet és jelszót `Get-HcsKubernetesContainerRegistryInfo` . 

1. A Docker leküldéses vagy lekéréses parancsaival leküldheti vagy lehívhatja a tárolók lemezképeit a tároló beállításjegyzékből.
 
    1. Egy rendszerkép lekérése a Microsoft Container Registry rendszerképből. Típus:
        
        `docker pull <Full path to the container image in the Microsoft Container Registry>`
       
    1. Hozzon létre egy aliast a felhúzott rendszerképből a beállításjegyzék teljes elérési útjával.

        `docker tag <Path to the image in the Microsoft container registry> <Path to the image in the Edge container registry/Image name with tag>`

    1. Küldje le a rendszerképet a beállításjegyzékbe.
    
        `docker push <Path to the image in the Edge container registry/Image name with tag>`

    1. Futtassa a beállításjegyzékbe leküldeni kívánt rendszerképet.
    
        `docker run -it --rm -p 8080:80 <Path to the image in the Edge container registry/Image name with tag>`

        Íme egy példa a lekéréses és leküldéses parancsok kimenetére:

        ```powershell
        PS C:\WINDOWS\system32> docker login ecr.dbe-hw6h1t2.microsoftdatabox.com:31001 -u ase-ecr-user -p 3bbo2sOtDe8FouD
        WARNING! Using --password via the CLI is insecure. Use --password-stdin.
        Login Succeeded
        PS C:\WINDOWS\system32> docker pull mcr.microsoft.com/oss/nginx/nginx:1.17.5-alpine
        1.17.5-alpine: Pulling from oss/nginx/nginx
        Digest: sha256:5466bbc0a989bd1cd283c0ba86d9c2fc133491ccfaea63160089f47b32ae973b
        Status: Image is up to date for mcr.microsoft.com/oss/nginx/nginx:1.17.5-alpine
        mcr.microsoft.com/oss/nginx/nginx:1.17.5-alpine
        PS C:\WINDOWS\system32> docker tag mcr.microsoft.com/oss/nginx/nginx:1.17.5-alpine ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx:2.0
        PS C:\WINDOWS\system32> docker push ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx:2.0
        The push refers to repository [ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx]
        bba7d2385bc1: Pushed
        77cae8ab23bf: Pushed
        2.0: digest: sha256:b4c0378c841cd76f0b75bc63454bfc6fe194a5220d4eab0d75963bccdbc327ff size: 739
        PS C:\WINDOWS\system32> docker run -it --rm -p 8080:80 ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx:2.0
        2020/11/10 00:00:49 [error] 6#6: *1 open() "/usr/share/nginx/html/favicon.ico" failed (2: No such file or directory), client: 172.17.0.1, server: localhost, request: "GET /favicon.ico HTTP/1.1", host: "localhost:8080", referrer: "http://localhost:8080/"
        172.17.0.1 - - [10/Nov/2020:00:00:49 +0000] "GET /favicon.ico HTTP/1.1" 404 555 "http://localhost:8080/" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/86.0.4240.183 Safari/537.36" "-"
        ^C
        PS C:\WINDOWS\system32>    
        ```
    
1. Tallózással keresse meg `http://localhost:8080` a futó tárolót. Ebben az esetben a-t futtató Nginx webkiszolgáló jelenik meg.

    ![A futó tároló megtekintése](media/azure-stack-edge-gpu-edge-container-registry/view-running-container-1.png)

    A tároló leállításához és eltávolításához nyomja meg a gombot `Control+C` .

 

## <a name="use-edge-container-registry-images-via-kubernetes-pods"></a>Az Edge Container Registry-lemezképek használata Kubernetes-hüvelyeken keresztül

Most már üzembe helyezheti a peremhálózati tároló beállításjegyzékében leküldett rendszerképet a Kubernetes-hüvelyből.

1. A rendszerkép üzembe helyezéséhez a *kubectl*-on keresztül kell konfigurálnia a fürt elérését. Hozzon létre egy névteret, egy felhasználót, adja meg a felhasználói hozzáférést a névtérhez, és szerezzen be egy *konfigurációs* fájlt. Ellenőrizze, hogy tud-e csatlakozni a Kubernetes hüvelyéhez. 
    
    Kövesse a [Kubernetes-fürt csatlakoztatása és kezelése a Azure stack Edge Pro GPU-eszközön a kubectl segítségével](azure-stack-edge-gpu-create-kubernetes-cluster.md)című témakör összes lépését. 

    Itt látható egy minta kimenet egy olyan névtérhez az eszközön, amelyről a felhasználó hozzáférhet a Kubernetes-fürthöz.

    ```powershell
    [10.128.44.40]: PS>New-HcsKubernetesNamespace -Namespace myecr
    [10.128.44.40]: PS>New-HcsKubernetesUser -UserName ecruser
    apiVersion: v1
    clusters:
    - cluster:
        certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUN5RENDQWJDZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRJd01URXdOVEF6TkRJek1Gb1hEVE13TVRFd016QXpOREl6TUZvd0ZURVRNQkVnNjOVRLWndCQ042cm1XQms2eXFwcXI1MUx6bApTaXMyTy91UEJ2YXNSSUUzdzgrbmEwdG1aTERZZ2F6MkQwMm42Q29mUmtyUTR2d1lLTnR1MlpzR3pUdz0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=
        server: https://compute.dbe-hw6h1t2.microsoftdatabox.com:6443
        name: kubernetes
        ===================CUT=========================================CUT==============
        client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMwRENDQWJpZ0F3SUJBZ0lJYmVWRGJSTzZ3ell3RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TURFeE1EVXdNelF5TXpCYUZ3MHlNVEV4TURreU16UTRNal
        ===================CUT=========================================CUT==============
        DMVUvN3lFOG5UU3k3b2VPWitUeHdzCjF1UDByMjhDZ1lCdHdRY0ZpcFh1blN5ak16dTNIYjhveFI2V3VWWmZldFFKNElKWEFXOStVWGhKTFhyQ2x4bUcKWHRtbCt4UU5UTzFjQVNKRVZWVDd6Tjg2ay9kSU43S3JIVkdUdUxlUDd4eGVjV2VRcWJrZEVScUsxN0liTXpiVApmbnNxc0dobEdmLzdmM21kTGtyOENrcWs5TU5aM3MvUVIwRlFCdk94ZVpuUlpTeDVGbUR5S1E9PQotLS0tLUVORCBSU0EgUFJJVkFURSBLRVktLS0tLQo=

    [10.128.44.40]: PS>Grant-HcsKubernetesNamespaceAccess -Namespace myecr -UserName ecruser
    [10.128.44.40]: PS>kubectl get pods -n "myecr"
    No resources found.
    PS C:\WINDOWS\system32>
    ```  

2. A rendszerkép lekérési titka már be van állítva az eszköz összes Kubernetes-névterében. A titkos kulcsokat a parancs használatával szerezheti be `get secrets` . Itt látható egy mintakimenet:

    ```powershell
    PS C:\WINDOWS\system32> .\kubectl.exe get secrets -n myecr
    NAME                  TYPE                                  DATA   AGE
    ase-ecr-credentials   kubernetes.io/dockerconfigjson        1      99m
    default-token-c7kww   kubernetes.io/service-account-token   3      107m
    sec-smbcredentials    microsoft.com/smb                     2      99m
    PS C:\WINDOWS\system32>   
    ```    

3. A kubectl használatával helyezzen üzembe egy Pod-et a névtérben. Használja a következőt `yaml` . 

    Cserélje le a rendszerképet: `<image-name>` Ha a képet leküldi a tároló-beállításjegyzékbe. Tekintse meg a névterekben található titkokat a imagePullSecrets használatával a következő névvel: `ase-ecr-credentials` .
    
    ```yml
    apiVersion: v1
    kind: Pod
    metadata:
      name: nginx
    spec:
      containers:
      - name: nginx
        image: ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx:2.0
        imagePullPolicy: Always
      imagePullSecrets:
      - name: ase-ecr-credentials
    ```

4. Alkalmazza az üzembe helyezést az Apply parancs használatával létrehozott névtérben. Ellenőrizze, hogy a tároló fut-e. Itt látható egy mintakimenet:
   
    ```yml
    PS C:\Windows\System32> .\kubectl.exe apply -f .\deployment.yml -n myecr
    pod/nginx configured
    PS C:\Windows\System32> .\kubectl.exe get pods -n myecr
    NAME    READY   STATUS    RESTARTS   AGE
    nginx   1/1     Running   0          27m
    PS C:\Windows\System32>
    ```

## <a name="delete-container-registry-images"></a>Tároló beállításjegyzék-lemezképek törlése

Az Edge Container Registry Storage a Azure Stack Edge Pro-eszközön belüli helyi megosztáson fut, amelyet az eszközön rendelkezésre álló tárterület korlátoz. Az Ön felelőssége, hogy törölje a nem használt Docker-rendszerképeket a tároló-beállításjegyzékből a Docker HTTP v2 API használatával ( https://docs.docker.com/registry/spec/api/) ).  

Egy vagy több tároló lemezképének eltávolításához kövesse az alábbi lépéseket:

1. Állítsa be a rendszerkép nevét a törölni kívánt képre.

    ```powershell
    PS C:\WINDOWS\system32> $imageName="nginx"    
    ```

1. A tároló-beállításjegyzék felhasználónevének és jelszavának beállítása PS-hitelesítő adatként

    ```powershell
    PS C:\WINDOWS\system32> $username="ase-ecr-user"
    PS C:\WINDOWS\system32> $password="3bbo2sOtDe8FouD"
    PS C:\WINDOWS\system32> $securePassword = ConvertTo-SecureString $password -AsPlainText -Force
    PS C:\WINDOWS\system32> $credential = New-Object System.Management.Automation.PSCredential ($username, $securePassword)    
    ```

1. A képhez társított címkék listázása

    ```powershell
    PS C:\WINDOWS\system32> $tags = Invoke-RestMethod -Credential $credential -Uri "https://ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/v2/nginx/tags/list" | Select-Object -ExpandProperty tags
    PS C:\WINDOWS\system32> $tags
    2.0
    PS C:\WINDOWS\system32> $tags = Invoke-RestMethod -Credential $credential -Uri "https://ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/v2/$imageName/tags/list" | Select-Object -ExpandProperty tags
    PS C:\WINDOWS\system32> $tags
    2.0
    PS C:\WINDOWS\system32>    
    ```
  
1. A törölni kívánt címkéhez társított kivonatoló felsorolása. Ez $tagst használ a fenti parancs kimenetében. Ha több címkével rendelkezik, válassza ki az egyiket, és használja a következő parancsban.

    ```powershell
    PS C:\WINDOWS\system32> $response = Invoke-WebRequest -Method Head -Credential $credential -Uri "https://ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/v2/$imageName/manifests/$tags" -Headers @{ 'Accept' = 'application/vnd.docker.distribution.manifest.v2+json' }
    PS C:\WINDOWS\system32> $digest = $response.Headers['Docker-Content-Digest']
    PS C:\WINDOWS\system32> $digest
    sha256:b4c0378c841cd76f0b75bc63454bfc6fe194a5220d4eab0d75963bccdbc327ff
    PS C:\WINDOWS\system32>    
    ```
1. Törölje a képet a rendszerkép kivonatának használatával: címke

    ```powershell
    PS C:\WINDOWS\system32> Invoke-WebRequest -Method Delete -Credential $credential -Uri "https://ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/v2/$imageName/manifests/$digest" | Select-Object -ExpandProperty StatusDescription    
    ```

A nem használt lemezképek törlése után a nem hivatkozott képekhez társított területet automatikusan visszaállítja egy olyan folyamat, amely éjjel fut. 

## <a name="next-steps"></a>Következő lépések

- [Állapot nélküli alkalmazás üzembe helyezése a Azure stack Edge Pro](./azure-stack-edge-gpu-deploy-stateless-application-kubernetes.md)-ban.