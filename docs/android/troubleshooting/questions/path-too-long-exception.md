---
title: Come si risolve un errore PathTooLongException?
description: In questo articolo viene illustrato come risolvere un PathTooLongException che può verificarsi durante la compilazione di un'app.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 60EE1C8D-BE44-4612-B3B5-70316D71B1EA
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/29/2018
ms.openlocfilehash: ffe88546ff58387865d71268bd64ec05c8aec3c5
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73026794"
---
# <a name="how-do-i-resolve-a-pathtoolongexception-error"></a>Come si risolve un errore PathTooLongException?

## <a name="cause"></a>Causa

I nomi dei percorsi generati in un progetto Xamarin.Android possono essere piuttosto lunghi.
Ad esempio, durante una compilazione potrebbe essere generato un percorso simile al seguente:For example, a path like the following could be generated during a build:

**C:\\\\Alcuni\\\\risorse\\di\\library_project_imports\\Xamarin.Forms.Platform.Android\\\\del__library_projects__\\progetto di soluzione directory**

In Windows (dove la lunghezza massima per un percorso è [260 caratteri](https://msdn.microsoft.com/library/windows/desktop/aa365247.aspx)), è possibile produrre un **PathTooLongException** durante la compilazione del progetto se un percorso generato supera la lunghezza massima. 

## <a name="fix"></a>Fix

La `UseShortFileNames` proprietà MSBuild `True` è impostata su per aggirare questo errore per impostazione predefinita. Quando questa proprietà `True`è impostata su , il processo di compilazione utilizza nomi di percorso più brevi per ridurre la probabilità di produrre **un'eccezione PathTooLongException**.
Ad esempio, `UseShortFileNames` quando `True`è impostato su , il percorso precedente viene abbreviato in percorso analogo al seguente:

**C:\\\\Alcuni\\\\\\risorse jl\\\\di\\progetto\\di soluzione Directory obj Debug\\lp 1**

Per impostare manualmente questa proprietà, aggiungere la seguente proprietà MSBuild al file **con estensione csproj** del progetto:

```xml
<PropertyGroup>
    <UseShortFileNames>True</UseShortFileNames>
</PropertyGroup>
```

Se l'impostazione di questo flag non corregge l'errore **PathTooLongException,** un altro approccio consiste nello specificare una radice di [output intermedia comune](https://blogs.msdn.microsoft.com/kirillosenkov/2015/04/04/using-a-common-intermediate-and-output-directory-for-your-solution/) per i progetti nella soluzione impostando `IntermediateOutputPath` nel file con estensione **csproj** del progetto. Provare a utilizzare un percorso relativamente breve. Ad esempio:

```xml
<PropertyGroup>
    <IntermediateOutputPath>C:\Projects\MyApp</IntermediateOutputPath>
</PropertyGroup>
```

Per ulteriori informazioni sull'impostazione delle proprietà di compilazione, vedere Processo di [compilazione](~/android/deploy-test/building-apps/build-process.md).
