---
title: IOS UrhoSharp e supporto per tvOS
description: Questo documento vengono illustrate iOS e tvOS supporto per UrhoSharp. Viene descritto come creare un progetto, configurare e avviare Urho ed eseguire un embed personalizzato di Urho.
ms.prod: xamarin
ms.assetid: 7B06567E-E789-4EA1-A2A9-F3B2212EDD23
author: charlespetzold
ms.author: chape
ms.date: 03/29/2017
ms.openlocfilehash: 7e8975b6885f6c902634e05aafca0b8ee60a981c
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34783975"
---
# <a name="urhosharp-ios-and-tvos-support"></a>IOS UrhoSharp e supporto per tvOS

Mentre Urho è una libreria di classi portabile e consente la stessa API da utilizzare nella piattaforma diversi per la logica di gioco, è comunque necessario inizializzare Urho nel driver specifico della piattaforma in alcuni casi, è possibile sfruttare le funzionalità specifiche della piattaforma .

Nelle pagine, si supponga che `MyGame` è un sublcass del `Application` classe.

## <a name="ios-and-tvos"></a>iOS e tvOS

**Architetture supportate:** armv7 arm64, i386

## <a name="creating-a-project"></a>Creazione di un progetto

Creare un progetto iOS e quindi aggiungere dati a directory delle risorse e assicurarsi che tutti i file abbiano **BundleResource** come il **azione di compilazione**.

![Il programma di installazione del progetto](ios-images/image-4.png "aggiungere dati alla directory delle risorse")

## <a name="configuring-and-launching-urho"></a>La configurazione e avvio Urho

Aggiungere l'utilizzo di istruzioni per la `Urho` e `Urho.iOS` gli spazi dei nomi e quindi aggiungere il codice per l'inizializzazione Urho, nonché di avviare l'applicazione:

```csharp
new MyGame().Run();
```

Si noti che poiché prevede iOS `FinishedLaunching` per completare, è necessario accodare la chiamata a `Run()` per l'esecuzione dopo il completamento del metodo, si tratta di un idioma comune:

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

È importante disabilitare le ottimizzazioni PNG perché query optimizer PNG iOS predefinito genera immagini utilizzabile Urho non attualmente correttamente

## <a name="custom-embedding-of-urho"></a>Incorporamento personalizzato di Urho

È possibile in alternativa alla Urho assumere la schermata intera applicazione e per utilizzarlo come un componente dell'applicazione, è possibile creare un `UrhoSurface` che è un `UIView` che è possibile includere nell'applicazione esistente.

Questo è ciò che si desidera eseguire:

```csharp
var view = new UrhoSurface () {
    Frame = new CGRect (100,100,200,200),
    BackgroundColor = UIColor.Red
}
window.AddSubview (view);
```

Questo ospiterà così la classe Urho, quindi si voglia agire in:

```csharp
new MyGame().Run ();
```

