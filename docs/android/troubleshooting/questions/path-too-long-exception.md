---
title: Risoluzione di un errore PathTooLongException
description: In questo articolo illustra come risolvere un PathTooLongException che possono verificarsi durante la compilazione di un'app.
ms.topic: article
ms.prod: xamarin
ms.assetid: 60EE1C8D-BE44-4612-B3B5-70316D71B1EA
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/20/2018
ms.openlocfilehash: f4554178648d1257049d1c21cdc3e141acdb3de7
ms.sourcegitcommit: d450ae06065d8f8c80f3588bc5a614cfd97b5a67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/21/2018
---
# <a name="how-do-i-resolve-a-pathtoolongexception-error"></a>Risoluzione di un errore PathTooLongException

## <a name="cause"></a>Causa

I nomi di percorso generato in un progetto xamarin. Android possono essere piuttosto lunghi.
Ad esempio, un percorso simile al seguente è stato generato durante una compilazione:

**C:\\Some\\Directory\\Solution\\Project\\obj\\Debug\\__library_projects__\\Xamarin.Forms.Platform.Android\\library_project_imports\\assets**

In Windows (dove la lunghezza massima per un percorso [260 caratteri](https://msdn.microsoft.com/library/windows/desktop/aa365247.aspx)), un **PathTooLongException** è possibile produrre durante la compilazione del progetto se un percorso generato supera la lunghezza massima. 

## <a name="fix"></a>Correzione

A partire da 8.0 xamarin. Android, il `UseShortFileNames` proprietà MSBuild può essere impostata per ovviare a questo errore. Quando questa proprietà è impostata su `True` (il valore predefinito è `False`), il processo di compilazione utilizza i nomi di percorso più breve per ridurre la probabilità di produrre un **PathTooLongException**.
Ad esempio, quando `UseShortFileNames` è impostata su `True`, il percorso precedente viene ridotto al percorso che è simile al seguente:

**C:\\alcuni\\Directory\\soluzione\\progetto\\obj\\Debug\\lp\\1\\jl\\Asset**

Per impostare questa proprietà, aggiungere la proprietà MSBuild seguente al progetto **csproj** file:

```xml
<PropertyGroup>
    <UseShortFileNames>True</UseShortFileNames>
</PropertyGroup>
```

Per ulteriori informazioni sull'impostazione delle proprietà di compilazione, vedere [processo di compilazione](~/android/deploy-test/building-apps/build-process.md).
