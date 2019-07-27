---
title: Novell. Android TextureView
ms.prod: xamarin
ms.assetid: DD1F3D68-5DD8-4644-8A13-08AE7719DE30
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/30/2017
ms.openlocfilehash: 589cf1787f5dc3090cbfb1165e91d8ef58df37a6
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510156"
---
# <a name="xamarinandroid-textureview"></a>Novell. Android TextureView

La `TextureView` classe è una vista che usa il rendering 2D con accelerazione hardware per consentire la visualizzazione di un flusso di contenuto video o OpenGL. Ad esempio, lo screenshot seguente mostra la `TextureView` visualizzazione di un feed live dalla fotocamera del dispositivo:

[![Schermata di esempio di un'immagine live dalla fotocamera del dispositivo](texture-view-images/22-textureviewcamera.png)](texture-view-images/22-textureviewcamera.png#lightbox)

A differenza della `SurfaceView` classe, che può essere usata anche per visualizzare il contenuto OpenGL o video, TextureView non viene sottoposto a rendering in una finestra separata.
Pertanto, `TextureView` è in grado di supportare le trasformazioni di visualizzazione come qualsiasi altra visualizzazione. Ad esempio, la rotazione `TextureView` di un oggetto può essere eseguita semplicemente `Rotation` impostando la relativa proprietà, la `Alpha` relativa trasparenza impostando la relativa proprietà e così via.

Con il `TextureView` è quindi possibile eseguire operazioni come la visualizzazione di un flusso live dalla fotocamera e la relativa trasformazione, come illustrato nel codice seguente:

```csharp
public class TextureViewActivity : Activity,
    TextureView.ISurfaceTextureListener
{
    Camera _camera;
    TextureView _textureView;
       
    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);
        _textureView = new TextureView (this);
        _textureView.SurfaceTextureListener = this;
           
        SetContentView (_textureView);
    }
       
    public void OnSurfaceTextureAvailable (
        Android.Graphics.SurfaceTexture surface,
        int width, int height)
    {
        _camera = Camera.Open ();
        var previewSize = _camera.GetParameters ().PreviewSize;
        _textureView.LayoutParameters =
            new FrameLayout.LayoutParams (previewSize.Width,
                previewSize.Height, (int)GravityFlags.Center);

        try {
            _camera.SetPreviewTexture (surface);
            _camera.StartPreview ();
        } catch (Java.IO.IOException ex) {
            Console.WriteLine (ex.Message);
        }
           
        // this is the sort of thing TextureView enables
        _textureView.Rotation = 45.0f;
        _textureView.Alpha = 0.5f;
    }
    …
}
```

Il codice precedente crea un' `TextureView` istanza nel `OnCreate` metodo dell'attività e `TextureView`imposta `SurfaceTextureListener`l'attività come. Per essere `SurfaceTextureListener`, l'attività implementa l' `TextureView.ISurfaceTextureListener` interfaccia. Il sistema chiamerà il `OnSurfaceTextAvailable` metodo quando è pronto per l' `SurfaceTexture` uso. In questo metodo, si accetta il `SurfaceTexture` passato e lo si imposta sulla trama di anteprima della fotocamera. È quindi possibile eseguire normali operazioni basate sulla visualizzazione, ad esempio l' `Rotation` impostazione di e `Alpha`, come nell'esempio precedente. L'applicazione risultante, in esecuzione in un dispositivo, è illustrata di seguito:

[![Esempio di app in esecuzione in un dispositivo, che visualizza un'immagine](texture-view-images/17-textureviewdemo.png)](texture-view-images/17-textureviewdemo.png#lightbox)

Per usare, `TextureView`è necessario abilitare l'accelerazione hardware, che sarà per impostazione predefinita a partire dal livello API 14. Inoltre, poiché in questo esempio viene usata la fotocamera, `android.permission.CAMERA` sia l'autorizzazione `android.hardware.camera` che la funzionalità devono essere impostate in **file AndroidManifest. XML**.



## <a name="related-links"></a>Collegamenti correlati

- [TextureViewDemo (esempio)](https://developer.xamarin.com/samples/monodroid/TextureViewDemo/)
- [Introduzione a Ice Cream Sandwich](http://www.android.com/about/ice-cream-sandwich/)
- [Piattaforma Android 4,0](https://developer.android.com/sdk/android-4.0.html)
