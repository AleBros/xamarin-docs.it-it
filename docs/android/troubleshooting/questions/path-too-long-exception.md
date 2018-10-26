---
title: Come si risolve un errore PathTooLongException?
description: Questo articolo illustra come risolvere un errore PathTooLongException che possono verificarsi durante la compilazione di un'app.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 60EE1C8D-BE44-4612-B3B5-70316D71B1EA
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/29/2018
ms.openlocfilehash: 4cb3e13ebbe3d9e8aed153528a35ab16c92e2145
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108704"
---
# <a name="how-do-i-resolve-a-pathtoolongexception-error"></a>Come si risolve un errore PathTooLongException?

## <a name="cause"></a>Causa

I nomi di percorso generato in un progetto xamarin. Android possono essere piuttosto lunghi.
Ad esempio, un percorso simile al seguente è stato possibile generare durante una compilazione:

**C:\\alcuni\\Directory\\soluzione\\Project\\obj\\Debug\\__library_projects__ \\ Xamarin.Forms.Platform.Android\\library_project_imports\\Asset**

In Windows (in cui la lunghezza massima per un percorso viene [260 caratteri](https://msdn.microsoft.com/library/windows/desktop/aa365247.aspx)), un **PathTooLongException** potrebbe essere generati durante la compilazione del progetto se un percorso generato supera la lunghezza massima. 

## <a name="fix"></a>Correzione

A partire da xamarin. Android 8.0, il `UseShortFileNames` proprietà MSBuild può essere impostata per ovviare a questo errore. Quando questa proprietà è impostata su `True` (il valore predefinito è `False`), il processo di compilazione Usa nomi di percorso più brevi per ridurre la probabilità di produrre un **PathTooLongException**.
Ad esempio, quando `UseShortFileNames` è impostata su `True`, il percorso precedente viene accorciato al percorso che è simile al seguente:

**C:\\alcuni\\Directory\\soluzione\\Project\\obj\\Debug\\lp\\1\\jl\\Asset**

Per impostare questa proprietà, aggiungere la proprietà MSBuild seguente al progetto **file con estensione csproj** file:

```xml
<PropertyGroup>
    <UseShortFileNames>True</UseShortFileNames>
</PropertyGroup>
```

Se si imposta questo flag non risolve il **PathTooLongException** errore, un altro approccio consiste nello specificare un [radice di output intermedio comune](https://blogs.msdn.microsoft.com/kirillosenkov/2015/04/04/using-a-common-intermediate-and-output-directory-for-your-solution/) per i progetti nella soluzione impostando `IntermediateOutputPath` nel Project **file con estensione csproj** file. Provare a usare un percorso relativamente breve. Ad esempio:

```xml
<PropertyGroup>
    <IntermediateOutputPath>C:\Projects\MyApp</IntermediateOutputPath>
</PropertyGroup>
```

Per altre informazioni sull'impostazione delle proprietà di compilazione, vedere [processo di compilazione](~/android/deploy-test/building-apps/build-process.md).
