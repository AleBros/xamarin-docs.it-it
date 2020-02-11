---
title: Supporto di UrhoSharp Mac
description: Questo documento viene descritto il supporto di macOS di UrhoSharp. Descrive come creare un progetto e viene fornito un collegamento al codice di esempio.
ms.prod: xamarin
ms.assetid: 95FFBD36-14E9-4C17-B1E8-9A04E81E824D
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: ee0a03d168b6e628893b18a27d73b46d3fa2fbc2
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832664"
---
# <a name="urhosharp-mac-support"></a>Supporto di UrhoSharp Mac

_Funzionalità e il programma di installazione specifiche di Mac_

Sebbene Urho è una libreria di classi portabile e consente la stessa API da usare nell'intera piattaforma diverse per la logica del gioco, devi comunque inizializzare Urho nel driver specifici della piattaforma e in alcuni casi, è possibile sfruttare le funzionalità specifiche della piattaforma .

Nelle pagine seguenti, si supponga che `MyGame` è una sottoclasse del `Application` classe.

## <a name="macos"></a>macOS

**Architetture supportate:** x86/x86-64 per a 32 bit e a 64 bit.

## <a name="creating-a-project"></a>Creazione di un progetto

Creare un progetto console, fare riferimento a Urho NuGet e quindi assicurarsi che sia possibile individuare gli asset (la directory che contiene la directory dati).

```csharp
DesktopUrhoInitializer.AssetsDirectory = "../Assets";
new MyGame().Run();
```

## <a name="example"></a>Esempio

[Esempio completo](https://github.com/xamarin/urho-samples/tree/master/FeatureSamples/Cocoa)
