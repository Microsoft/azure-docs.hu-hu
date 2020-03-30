---
title: Csatlakozás az Azure Analysis Services szolgáltatáshoz .odc fájllal | Microsoft dokumentumok
description: Megtudhatja, hogy miként hozhat létre Office-adatkapcsolati fájlt, amelyhez az Azure-beli Analysis Services-kiszolgálóhoz csatlakozhat, és adatokat szerezhet be.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: a4e2dc09c54b39d58b0193e7ac9f585236886722
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73572673"
---
# <a name="create-an-office-data-connection-file"></a>Office-adatkapcsolati fájl létrehozása

Ebben a cikkben található információ arról szól, hogyan hozhat létre Office-adatkapcsolati fájlt az Azure Analysis Services kiszolgálóhoz való csatlakozáshoz az Excel 2016 16.0.7369.2117-es vagy korábbi verziószámáról, illetve az Excel 2013-ból. Frissített [MSOLAP.7 szolgáltatóra](analysis-services-data-providers.md) is szükség van.


1. Másolja az alábbi mintakapcsolati fájlt, és illessze be egy szövegszerkesztőbe. 

2. A `odc:ConnectionString`alkalmazásban módosítsa a következő tulajdonságokat:

    *   Az `Data Source=asazure://<region>.asazure.windows.net/<servername>;` `<region>` Analysis Services kiszolgáló régiójára `<servername>` és a kiszolgáló nevére váltva.

    *   Az `Initial Catalog=<database>;` `<database>` adatbázis nevének módosítása.

3. A `<odc:CommandText>Model</odc:CommandText>` `Model` modell vagy perspektíva nevének módosítása. 

4. Mentse a fájlt `.odc` a C:\Users\\*felhasználónév*\Documents\My Data Sources mappába.

5. Kattintson a jobb gombbal a fájlra, majd kattintson **a Megnyitás az Excel programban parancsra.** Vagy az Excelben kattintson az **Adatok** menüszalag **Meglévő kapcsolatok**gombjára, jelölje ki a fájlt, majd kattintson a **Megnyitás gombra.**



**Minta kapcsolatfájl**
```
<html xmlns:o="urn:schemas-microsoft-com:office:office"
xmlns="https://www.w3.org/TR/REC-html40">

<head>
<meta http-equiv=Content-Type content="text/x-ms-odc; charset=utf-8">
<meta name=ProgId content=ODC.Cube>
<meta name=SourceType content=OLEDB>
<meta name=Catalog content="Database">
<meta name=Table content=Model>
<title>AzureAnalysisServicesConnection</title>
<xml id=docprops><o:DocumentProperties
  xmlns:o="urn:schemas-microsoft-com:office:office"
  xmlns="https://www.w3.org/TR/REC-html40">
  <o:Name>SampleAzureAnalysisServices</o:Name>
 </o:DocumentProperties>
</xml><xml id=msodc><odc:OfficeDataConnection
  xmlns:odc="urn:schemas-microsoft-com:office:odc"
  xmlns="https://www.w3.org/TR/REC-html40">
  <odc:Connection odc:Type="OLEDB">
   <odc:ConnectionString>Provider=MSOLAP.7;Data Source=asazure://<region>.asazure.windows.net/<servername>;Initial Catalog=<database>;</odc:ConnectionString>
   <odc:CommandType>Cube</odc:CommandType>
   <odc:CommandText>Model</odc:CommandText>
  </odc:Connection>
 </odc:OfficeDataConnection>
</xml>
<style>
<!--
    .ODCDataSource
    {
    behavior: url(dataconn.htc);
    }
-->
</style>
 
</head>

<body onload='init()' scroll=no leftmargin=0 topmargin=0 rightmargin=0 style='border: 0px'>
<table style='border: solid 1px threedface; height: 100%; width: 100%' cellpadding=0 cellspacing=0 width='100%'> 
  <tr> 
    <td id=tdName style='font-family:arial; font-size:medium; padding: 3px; background-color: threedface'> 
      &nbsp; 
    </td> 
     <td id=tdTableDropdown style='padding: 3px; background-color: threedface; vertical-align: top; padding-bottom: 3px'>

      &nbsp; 
    </td> 
  </tr> 
  <tr> 
    <td id=tdDesc colspan='2' style='border-bottom: 1px threedshadow solid; font-family: Arial; font-size: 1pt; padding: 2px; background-color: threedface'>

      &nbsp; 
    </td> 
  </tr> 
  <tr> 
    <td colspan='2' style='height: 100%; padding-bottom: 4px; border-top: 1px threedhighlight solid;'> 
      <div id='pt' style='height: 100%' class='ODCDataSource'></div> 
    </td> 
  </tr> 
</table> 

  
<script language='javascript'> 

function init() { 
  var sName, sDescription; 
  var i, j; 
  
  try { 
    sName = unescape(location.href) 
  
    i = sName.lastIndexOf(".") 
    if (i>=0) { sName = sName.substring(1, i); } 
  
    i = sName.lastIndexOf("/") 
    if (i>=0) { sName = sName.substring(i+1, sName.length); } 

    document.title = sName; 
    document.getElementById("tdName").innerText = sName; 

    sDescription = document.getElementById("docprops").innerHTML; 
  
    i = sDescription.indexOf("escription>") 
    if (i>=0) { j = sDescription.indexOf("escription>", i + 11); } 

    if (i>=0 && j >= 0) { 
      j = sDescription.lastIndexOf("</", j); 

      if (j>=0) { 
          sDescription = sDescription.substring(i+11, j); 
        if (sDescription != "") { 
            document.getElementById("tdDesc").style.fontSize="x-small"; 
          document.getElementById("tdDesc").innerHTML = sDescription; 
          } 
        } 
      } 
    } 
  catch(e) { 

    } 
  } 
</script> 

</body> 
 
</html>

```



