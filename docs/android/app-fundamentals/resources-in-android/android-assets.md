---
title: Uso di asset Android
ms.prod: xamarin
ms.assetid: 70ECDDC9-FA40-03B4-BF04-E7CFFFE4260D
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/13/2018
ms.openlocfilehash: 1e9a71de7725c8382e133d85977407bcc859fc58
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50114860"
---
# <a name="using-android-assets"></a>Uso di asset Android

_Gli asset_ offrono un modo per includere file arbitrari, ad esempio testo, xml, i tipi di carattere, musica e video nell'applicazione. Se si tenta di includere tali file come "resources", Android li elabora in un sistema di risorse e non sarà in grado di ottenere i dati non elaborati. Se si desidera accedere ai dati invariati, gli asset sono un modo per eseguire questa operazione.

Asset aggiunti al progetto verrà visualizzato esattamente come un file system in grado di leggere dall'applicazione usando [AssetManager](https://developer.xamarin.com/api/type/Android.Content.Res.AssetManager/).
Questo semplice esempio, si intende aggiungere un asset di file di testo per il progetto, lo legge con `AssetManager`e visualizzarli in un controllo TextView.


## <a name="add-asset-to-project"></a>Aggiungi Asset al progetto

Gli asset di andare `Assets` cartella del progetto. Aggiungere un nuovo file di testo in questa cartella denominata `read_asset.txt`. Inserire del testo, ad esempio "Leggevo da un asset!".

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Visual Studio dovrebbe avere impostato il **Build Action** per questo file per **AndroidAsset**:

![Impostazione azione di compilazione su AndroidAsset](android-assets-images/asset-properties-vs.png) 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Visual Studio per Mac deve avere impostato il **Build Action** per questo file per **AndroidAsset**:

[![Impostazione azione di compilazione su AndroidAsset](android-assets-images/asset-properties-xs-sml.png)](android-assets-images/asset-properties-xs.png#lightbox)

-----

Selezionando i valori corretti **BuildAction** assicura che i file verrà inserito in file APK in fase di compilazione.


## <a name="reading-assets"></a>La lettura di asset

Gli asset vengono letti utilizzando un [AssetManager](https://developer.xamarin.com/api/type/Android.Content.Res.AssetManager/). Un'istanza del `AssetManager` è disponibile tramite l'accesso il [asset](https://developer.xamarin.com/api/property/Android.Content.Context.Assets/) proprietà in un `Android.Content.Context`, ad esempio un'attività.
Nel codice seguente, apriamo nostri **read_asset.txt** asset, leggere il contenuto e visualizzarlo utilizzando un TextView.

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

Eseguire l'applicazione e si dovrebbe vedere gli argomenti seguenti:

![Screenshot di esempio](android-assets-images/screenshot.png)


## <a name="related-links"></a>Collegamenti correlati

- [AssetManager](https://developer.xamarin.com/api/type/Android.Content.Res.AssetManager/)
- [Contesto](https://developer.xamarin.com/api/type/Android.Content.Context/)
