---
title: Uso di asset Android
ms.prod: xamarin
ms.assetid: 70ECDDC9-FA40-03B4-BF04-E7CFFFE4260D
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/13/2018
ms.openlocfilehash: 9c8db5ad7bcb012befb2fa8dcd1ecd13fa355a55
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73025424"
---
# <a name="using-android-assets"></a>Uso di asset Android

Gli _Asset_ forniscono un modo per includere file arbitrari come testo, XML, caratteri, musica e video nell'applicazione. Se si tenta di includere questi file come "risorse", verranno elaborati dal sistema di risorse di Android e non sarà possibile ottenere i dati non elaborati. Se si desidera accedere ai dati invariati, le risorse sono un modo per eseguire questa operazione.

Gli asset aggiunti al progetto vengono visualizzati come un file system in grado di leggere dall'applicazione usando [AssetManager](xref:Android.Content.Res.AssetManager).
In questa semplice demo, verrà aggiunto un asset di file di testo al progetto, letto con `AssetManager`e visualizzato in un oggetto TextView.

## <a name="add-asset-to-project"></a>Aggiungi asset al progetto

Gli asset vengono inseriti nella cartella `Assets` del progetto. Aggiungere un nuovo file di testo a questa cartella denominata `read_asset.txt`. Inserire del testo come "I provenienti da un asset!".

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

In Visual Studio è necessario impostare l' **azione di compilazione** per questo file su **AndroidAsset**:

![Impostazione dell'azione di compilazione su AndroidAsset](android-assets-images/asset-properties-vs.png) 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Visual Studio per Mac necessario impostare l' **azione di compilazione** per questo file su **AndroidAsset**:

[![impostare l'azione di compilazione su AndroidAsset](android-assets-images/asset-properties-xs-sml.png)](android-assets-images/asset-properties-xs.png#lightbox)

-----

Selezionando il **BuildAction** corretto si garantisce che il file venga incluso nel file apk in fase di compilazione.

## <a name="reading-assets"></a>Lettura degli asset

Gli asset vengono letti usando un [AssetManager](xref:Android.Content.Res.AssetManager). Un'istanza del `AssetManager` è disponibile accedendo alla proprietà [assets](xref:Android.Content.Context.Assets) su un `Android.Content.Context`, ad esempio un'attività.
Nel codice seguente si apre l'asset **read_asset. txt** , si legge il contenuto e lo si visualizza usando un oggetto TextView.

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    // Create a new TextView and set it as our view
    TextView tv = new TextView (this);
    
    // Read the contents of our asset
    string content;
    AssetManager assets = this.Assets;
    using (StreamReader sr = new StreamReader (assets.Open ("read_asset.txt")))
    {
        content = sr.ReadToEnd ();
    }

    // Set TextView.Text to our asset content
    tv.Text = content;
    SetContentView (tv);
}
```

## <a name="running-the-application"></a>Esecuzione dell'applicazione

Eseguire l'applicazione e dovrebbe essere visualizzato quanto segue:

![Schermata di esempio](android-assets-images/screenshot.png)

## <a name="related-links"></a>Collegamenti correlati

- [AssetManager](xref:Android.Content.Res.AssetManager)
- [Contesto](xref:Android.Content.Context)
