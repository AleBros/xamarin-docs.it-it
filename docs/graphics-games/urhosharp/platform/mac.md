---
title: Supporto UrhoSharp Mac
description: Il programma di installazione specifiche di Mac e funzionalità per UrhoSharp.
ms.prod: xamarin
ms.assetid: 95FFBD36-14E9-4C17-B1E8-9A04E81E824D
author: charlespetzold
ms.author: chape
ms.date: 03/29/2017
ms.openlocfilehash: c7210cbd5586df9018c2779bf0b92aa7c1c56e89
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2018
---
# <a name="urhosharp-mac-support"></a>Supporto UrhoSharp Mac

_Funzionalità e installazione di Mac specifico_

Mentre Urho è una libreria di classi portabile e consente la stessa API da utilizzare nella piattaforma diversi per la logica di gioco, è comunque necessario inizializzare Urho nel driver specifico della piattaforma in alcuni casi, è possibile sfruttare le funzionalità specifiche della piattaforma .

Nelle pagine, si supponga che `MyGame` è una sottoclasse di `Application` classe.

## <a name="macos"></a>macOS

**Architetture supportate:** x86/x86-64 per a 32 bit e a 64 bit.

## <a name="creating-a-project"></a>Creazione di un progetto

Creare un progetto console, fare riferimento a Urho NuGet e quindi assicurarsi che sia possibile individuare le risorse (la directory contenente la directory dei dati).

```csharp
DesktopUrhoInitializer.AssetsDirectory = "../Assets";
new MyGame().Run();
```

## <a name="example"></a>Esempio

[Esempio completo](https://github.com/xamarin/urho-samples/tree/master/FeatureSamples/Cocoa)


