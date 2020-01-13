---
title: Ricerca per categorie risolvere un errore PathTooLongException?
description: Questo articolo illustra come risolvere un PathTooLongException che può verificarsi durante la compilazione di un'app.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 60EE1C8D-BE44-4612-B3B5-70316D71B1EA
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/29/2018
ms.openlocfilehash: ffe88546ff58387865d71268bd64ec05c8aec3c5
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73026794"
---
# <a name="how-do-i-resolve-a-pathtoolongexception-error"></a>Ricerca per categorie risolvere un errore PathTooLongException?

## <a name="cause"></a>Causa

I nomi di percorso generati in un progetto Xamarin.Android possono essere piuttosto lunghi.
Ad esempio, è possibile generare un percorso simile al seguente durante una compilazione:

**C:\\alcuni\\directory\\soluzione\\progetto\\obj\\debug\\__library_projects__\\Xamarin.Forms. Platform. Android\\library_project_imports\\asset**

In Windows (dove la lunghezza massima per un percorso è [260 caratteri](https://msdn.microsoft.com/library/windows/desktop/aa365247.aspx)), è possibile che venga generato un **PathTooLongException** durante la compilazione del progetto se un percorso generato supera la lunghezza massima. 

## <a name="fix"></a>Correggi

Per impostazione predefinita, la proprietà `UseShortFileNames` MSBuild è impostata su `True` per aggirare l'errore. Quando questa proprietà è impostata su `True`, il processo di compilazione utilizza nomi di percorso più brevi per ridurre la probabilità di produrre un **PathTooLongException**.
Ad esempio, quando `UseShortFileNames` è impostato su `True`, il percorso precedente viene abbreviato in un percorso simile al seguente:

**C:\\alcuni\\directory\\soluzione\\progetto\\obj\\debug\\LP\\1\\JL\\asset**

Per impostare questa proprietà manualmente, aggiungere la seguente proprietà MSBuild al file Project **. csproj** :

```xml
<PropertyGroup>
    <UseShortFileNames>True</UseShortFileNames>
</PropertyGroup>
```

Se l'impostazione di questo flag non corregge l'errore **PathTooLongException** , un altro approccio consiste nel specificare una [radice di output intermedia comune](https://blogs.msdn.microsoft.com/kirillosenkov/2015/04/04/using-a-common-intermediate-and-output-directory-for-your-solution/) per i progetti nella soluzione impostando `IntermediateOutputPath` nel file Project **. csproj** . Provare a usare un percorso relativamente breve. Esempio:

```xml
<PropertyGroup>
    <IntermediateOutputPath>C:\Projects\MyApp</IntermediateOutputPath>
</PropertyGroup>
```

Per ulteriori informazioni sull'impostazione delle proprietà di compilazione, vedere [processo di compilazione](~/android/deploy-test/building-apps/build-process.md).
