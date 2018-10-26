---
title: IOS UrhoSharp e supporto per tvOS
description: Questo documento vengono illustrate iOS e tvOS supporto per UrhoSharp. Viene descritto come creare un progetto, configurare e avviare Urho e seguire un incorporamento personalizzato di Urho.
ms.prod: xamarin
ms.assetid: 7B06567E-E789-4EA1-A2A9-F3B2212EDD23
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: f15ae458c6bd613b59700908ad7c121315e377ab
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108275"
---
# <a name="urhosharp-ios-and-tvos-support"></a>IOS UrhoSharp e supporto per tvOS

Sebbene Urho è una libreria di classi portabile e consente la stessa API da usare nell'intera piattaforma diverse per la logica del gioco, devi comunque inizializzare Urho nel driver specifici della piattaforma e in alcuni casi, è possibile sfruttare le funzionalità specifiche della piattaforma .

Nelle pagine seguenti, si supponga che `MyGame` è un sublcass del `Application` classe.

## <a name="ios-and-tvos"></a>iOS e tvOS

**Architetture supportate:** armv7, arm64, i386

## <a name="creating-a-project"></a>Creazione di un progetto

Creare un progetto iOS e aggiungere dati alla directory delle risorse e assicurarsi che tutti i file abbiano **BundleResource** come la **azione di compilazione**.

![Il programma di installazione del progetto](ios-images/image-4.png "aggiungere dati alla directory delle risorse")

## <a name="configuring-and-launching-urho"></a>Configurando e avviando Urho

Aggiungere usando le istruzioni per la `Urho` e `Urho.iOS` spazi dei nomi e quindi aggiungere il codice per l'inizializzazione Urho, nonché l'avvio dell'applicazione:

```csharp
new MyGame().Run();
```

Si noti che poiché iOS prevede `FinishedLaunching` per completare, è necessario accodare la chiamata a `Run()` per l'esecuzione dopo il completamento del metodo, si tratta di un idioma comune:

```csharp
public override bool FinishedLaunching(UIApplication app, NSDictionary options)
{
    LaunchGame();
    return true;
}

async void LaunchGame()
{
    await Task.Yield();
    new SamplyGame().Run();
}
```

È importante disabilitare le ottimizzazioni PNG perché query optimizer PNG iOS predefinito genera immagini utilizzabili Urho non attualmente correttamente

## <a name="custom-embedding-of-urho"></a>Incorporamento personalizzato di Urho

È possibile in alternativa alla presenza di Urho occupi la schermata dell'intera applicazione, e per usarlo come un componente dell'applicazione, è possibile creare un `UrhoSurface` che è un `UIView` che è possibile incorporare nell'applicazione esistente.

Questo è ciò che occorre eseguire:

```csharp
var view = new UrhoSurface () {
    Frame = new CGRect (100,100,200,200),
    BackgroundColor = UIColor.Red
}
window.AddSubview (view);
```

Ospiterà la classe Urho, pertanto, quindi si voglia agire in:

```csharp
new MyGame().Run ();
```

