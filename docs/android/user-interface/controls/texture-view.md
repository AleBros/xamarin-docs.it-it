---
title: TextureView
ms.topic: article
ms.prod: xamarin
ms.assetid: DD1F3D68-5DD8-4644-8A13-08AE7719DE30
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 05/30/2017
ms.openlocfilehash: d2d9c455f2ddd652a76177527586673901edd012
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="textureview"></a>TextureView

La `TextureView` classe è una vista che utilizza rendering con accelerazione hardware 2D per consentire a un video o un flusso di contenuto OpenGL da visualizzare. Ad esempio, la schermata seguente mostra il `TextureView` la visualizzazione di un feed in tempo reale da dispositivo foto/video del dispositivo:

[![Schermata di esempio di un'immagine in tempo reale da dispositivo foto/video del dispositivo](texture-view-images/22-textureviewcamera.png)](texture-view-images/22-textureviewcamera.png#lightbox)

A differenza di `SurfaceView` (classe), che può essere utilizzato anche per visualizzare OpenGL o contenuto video, il TextureView non viene eseguito in una finestra separata.
Pertanto, `TextureView` è in grado di supportare le trasformazioni, Visualizza come qualsiasi altra visualizzazione. Ad esempio, la rotazione di una `TextureView` può essere eseguita impostando semplicemente relativo `Rotation` proprietà, la trasparenza impostando relativo `Alpha` proprietà e così via.

Pertanto, con la `TextureView` ora possiamo eseguire operazioni come visualizzazione di un flusso in tempo reale tra la camera e trasformarlo, come illustrato nel codice seguente:

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

Il codice precedente crea un `TextureView` istanza dell'attività `OnCreate` (metodo) e imposta l'attività come il `TextureView`del `SurfaceTextureListener`. Sia il `SurfaceTextureListener`, implementa l'attività di `TextureView.ISurfaceTextureListener` interfaccia. Verrà chiamato dal sistema di `OnSurfaceTextAvailable` metodo quando il `SurfaceTexture` è pronto per l'utilizzo. In questo metodo, si accettano le `SurfaceTexture` che è passato e impostarla su una trama di anteprima della camera. Quindi si hanno la possibilità di eseguire le normali operazioni basate sulla visualizzazione, ad esempio impostando il `Rotation` e `Alpha`, come illustrato nell'esempio precedente. L'applicazione risulta, in esecuzione in un dispositivo, è illustrato di seguito:

[![Esempio di app in esecuzione in un dispositivo, che visualizza un'immagine](texture-view-images/17-textureviewdemo.png)](texture-view-images/17-textureviewdemo.png#lightbox)

Utilizzare il `TextureView`, accelerazione hardware deve essere abilitato, che verrà visualizzato per impostazione predefinita a partire da 14 livello API. Inoltre, poiché in questo esempio viene utilizzata la fotocamera, sia il `android.permission.CAMERA` autorizzazione e `android.hardware.camera` funzionalità deve essere impostata nel **AndroidManifest.xml**.



## <a name="related-links"></a>Collegamenti correlati

- [TextureViewDemo (sample)](https://developer.xamarin.com/samples/monodroid/TextureViewDemo/)
- [Introduzione a Sandwich gelato](http://www.android.com/about/ice-cream-sandwich/)
- [Piattaforma 4.0 Android](http://developer.android.com/sdk/android-4.0.html)
