---
title: Utilizzo risorse Android
ms.prod: xamarin
ms.assetid: 70ECDDC9-FA40-03B4-BF04-E7CFFFE4260D
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/13/2018
ms.openlocfilehash: 337a1ed82010658adce40e8946ed1b0361fb6094
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="using-android-assets"></a>Utilizzo risorse Android

_Asset_ offrono un modo per includere file arbitrari come testo, xml, i tipi di carattere, musica e video nell'applicazione. Se si tenta di includere tali file come "resources", Android verranno elaborati in un sistema di risorse e non sarà in grado di ottenere i dati non elaborati. Se si desidera accedere ai dati invariati, gli asset sono un modo per eseguire l'operazione.

Asset aggiunti al progetto verrà visualizzato come un file system in grado di leggere da tramite l'applicazione [AssetManager](https://developer.xamarin.com/api/type/Android.Content.Res.AssetManager/).
In questo video dimostrativo semplice, per aggiungere un asset di file di testo al progetto, verrà letto tramite `AssetManager`e visualizzarli in un oggetto TextView.


## <a name="add-asset-to-project"></a>Aggiungere risorse al progetto

Asset passare il `Assets` cartella del progetto. Aggiungere un nuovo file di testo a questa cartella denominata `read_asset.txt`. Inserire del testo in essa contenuti, ad esempio "proviene da un asset"!.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Visual Studio deve avere impostato la **azione di compilazione** per il file **AndroidAsset**:

![Impostazione dell'azione di compilazione per AndroidAsset](android-assets-images/asset-properties-vs.png) 

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Visual Studio per Mac necessario impostare il **azione di compilazione** per il file **AndroidAsset**:

[![Impostazione dell'azione di compilazione per AndroidAsset](android-assets-images/asset-properties-xs-sml.png)](android-assets-images/asset-properties-xs.png#lightbox)

-----

Selezionando il corretto **BuildAction** assicura che il file verrà inserito nel file APK in fase di compilazione.


## <a name="reading-assets"></a>La lettura delle risorse

Gli asset vengono letti utilizzando un [AssetManager](https://developer.xamarin.com/api/type/Android.Content.Res.AssetManager/). Un'istanza del `AssetManager` è disponibile tramite l'accesso di [asset](https://developer.xamarin.com/api/property/Android.Content.Context.Assets/) proprietà in un `Android.Content.Context`, ad esempio un'attività.
Nel codice seguente, si apre il nostro **read_asset.txt** asset, leggere il contenuto e visualizzarlo in un oggetto TextView.

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

![Schermata di esempio](android-assets-images/screenshot.png)


## <a name="related-links"></a>Collegamenti correlati

- [AssetManager](https://developer.xamarin.com/api/type/Android.Content.Res.AssetManager/)
- [Context](https://developer.xamarin.com/api/type/Android.Content.Context/)
