---
title: Ricerca per categorie risolvere un errore PathTooLongException?
description: Questo articolo illustra come risolvere un PathTooLongException che può verificarsi durante la compilazione di un'app.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 60EE1C8D-BE44-4612-B3B5-70316D71B1EA
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/29/2018
ms.openlocfilehash: 915f557db7955dc7b8b9f1bc5e014a683740052b
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760807"
---
# <a name="how-do-i-resolve-a-pathtoolongexception-error"></a>Ricerca per categorie risolvere un errore PathTooLongException?

## <a name="cause"></a>Causa

I nomi di percorso generati in un progetto Novell. Android possono essere piuttosto lunghi.
Ad esempio, è possibile generare un percorso simile al seguente durante una compilazione:

**C:\\alcuni\\progetti\\dellasoluzione\\directoryobj\\debug__library_projects__Novell. Forms. Platform. Android\\\\\\\\assetlibrary_project_imports\\**

In Windows (dove la lunghezza massima per un percorso è [260 caratteri](https://msdn.microsoft.com/library/windows/desktop/aa365247.aspx)), è possibile che venga generato un **PathTooLongException** durante la compilazione del progetto se un percorso generato supera la lunghezza massima. 

## <a name="fix"></a>Correzione

La `UseShortFileNames` proprietà MSBuild è impostata su `True` per aggirare l'errore per impostazione predefinita. Quando questa proprietà è impostata su `True`, il processo di compilazione utilizza nomi di percorso più brevi per ridurre la probabilità di produrre un **PathTooLongException**.
Ad esempio, quando `UseShortFileNames` è impostato su `True`, il percorso precedente viene abbreviato in un percorso simile al seguente:

**C:\\alcune\\risorse\\del\\progettodisoluzione\\directory obj debugLP1\\JL\\\\\\\\**

Per impostare questa proprietà manualmente, aggiungere la seguente proprietà MSBuild al file Project **. csproj** :

```xml
<PropertyGroup>
    <UseShortFileNames>True</UseShortFileNames>
</PropertyGroup>
```

Se l'impostazione di questo flag non corregge l'errore **PathTooLongException** , un altro approccio consiste nel specificare una [radice di output intermedia comune](https://blogs.msdn.microsoft.com/kirillosenkov/2015/04/04/using-a-common-intermediate-and-output-directory-for-your-solution/) per i progetti nella `IntermediateOutputPath` soluzione impostando nel file Project **. csproj** . Provare a usare un percorso relativamente breve. Ad esempio:

```xml
<PropertyGroup>
    <IntermediateOutputPath>C:\Projects\MyApp</IntermediateOutputPath>
</PropertyGroup>
```

Per ulteriori informazioni sull'impostazione delle proprietà di compilazione, vedere [processo di compilazione](~/android/deploy-test/building-apps/build-process.md).
