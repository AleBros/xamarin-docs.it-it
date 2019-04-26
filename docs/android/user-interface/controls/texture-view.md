---
title: TextureView
ms.prod: xamarin
ms.assetid: DD1F3D68-5DD8-4644-8A13-08AE7719DE30
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/30/2017
ms.openlocfilehash: 8f20666ddf5f74ff9451a22e289790eb14071f90
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61076136"
---
# <a name="textureview"></a>TextureView

Il `TextureView` classe è una vista che utilizza con accelerazione hardware rendering 2D per consentire a un video o un flusso di contenuto OpenGL da visualizzare. Ad esempio, lo screenshot seguente mostra il `TextureView` la visualizzazione di un feed live dalla fotocamera del dispositivo:

[![Screenshot di esempio di un'immagine in tempo reale dalla fotocamera del dispositivo](texture-view-images/22-textureviewcamera.png)](texture-view-images/22-textureviewcamera.png#lightbox)

A differenza di `SurfaceView` classe, che può essere usato anche per visualizzare contenuto video o OpenGL, il TextureView non viene eseguito in una finestra separata.
Pertanto, `TextureView` è in grado di supportare le trasformazioni di visualizzazione come qualsiasi altra visualizzazione. Ad esempio, la rotazione una `TextureView` può essere eseguita impostando semplicemente relativi `Rotation` proprietà, la trasparenza impostando relativo `Alpha` proprietà e così via.

Pertanto, con la `TextureView` è ora possibile eseguire operazioni come visualizzato di un flusso in tempo reale tra la camera e e transform, come illustrato nel codice seguente:

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

Il codice precedente crea una `TextureView` istanza dell'attività `OnCreate` metodo e imposta l'attività come la `TextureView`del `SurfaceTextureListener`. Sia la `SurfaceTextureListener`, l'attività implementa il `TextureView.ISurfaceTextureListener` interfaccia. Verrà chiamato dal sistema di `OnSurfaceTextAvailable` metodo quando il `SurfaceTexture` è pronto per l'uso. In questo metodo, prendiamo il `SurfaceTexture` che viene passato e impostarlo a trama di anteprima della camera. Saremo quindi liberi di eseguire le normali operazioni basate sulla visualizzazione, ad esempio impostando il `Rotation` e `Alpha`, come illustrato nell'esempio precedente. L'applicazione risulta, in esecuzione in un dispositivo, è illustrato di seguito:

[![Esempio di app in esecuzione in un dispositivo, viene visualizzata un'immagine](texture-view-images/17-textureviewdemo.png)](texture-view-images/17-textureviewdemo.png#lightbox)

Usare il `TextureView`, l'accelerazione hardware deve essere abilitato, che sarà per impostazione predefinita al momento della stesura di livello API 14. Inoltre, dal momento che questo esempio Usa la fotocamera, sia la `android.permission.CAMERA` autorizzazione e il `android.hardware.camera` funzionalità deve essere impostata nel **androidmanifest. XML**.



## <a name="related-links"></a>Collegamenti correlati

- [TextureViewDemo (sample)](https://developer.xamarin.com/samples/monodroid/TextureViewDemo/)
- [Introduzione a Ice Cream Sandwich](http://www.android.com/about/ice-cream-sandwich/)
- [Piattaforma Android 4.0](https://developer.android.com/sdk/android-4.0.html)
