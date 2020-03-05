---
title: Novell. Android TextureView
ms.prod: xamarin
ms.assetid: DD1F3D68-5DD8-4644-8A13-08AE7719DE30
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/30/2017
ms.openlocfilehash: 2ffa544789e0d605a241c8e038c790650a7fc6a3
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2020
ms.locfileid: "78292147"
---
# <a name="xamarinandroid-textureview"></a>Novell. Android TextureView

La classe `TextureView` è una vista che usa il rendering 2D con accelerazione hardware per consentire la visualizzazione di un flusso di contenuto video o OpenGL. Ad esempio, lo screenshot seguente mostra il `TextureView` visualizzare un feed live dalla fotocamera del dispositivo:

[![screenshot di esempio di un'immagine live dalla fotocamera del dispositivo](texture-view-images/22-textureviewcamera.png)](texture-view-images/22-textureviewcamera.png#lightbox)

A differenza della classe `SurfaceView`, che può essere usata anche per visualizzare il contenuto OpenGL o video, TextureView non viene sottoposto a rendering in una finestra separata.
Pertanto, `TextureView` è in grado di supportare le trasformazioni di visualizzazione come qualsiasi altra visualizzazione. Ad esempio, la rotazione di un `TextureView` può essere eseguita semplicemente impostando la relativa proprietà `Rotation`, la relativa trasparenza impostando la relativa proprietà `Alpha` e così via.

Con il `TextureView` ora è possibile eseguire operazioni quali la visualizzazione di un flusso live dalla fotocamera e la trasformazione, come illustrato nel codice seguente:

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

Il codice precedente crea un'istanza di `TextureView` nel metodo `OnCreate` dell'attività e imposta l'attività come `SurfaceTextureListener`di `TextureView`. Per essere il `SurfaceTextureListener`, l'attività implementa l'interfaccia `TextureView.ISurfaceTextureListener`. Il sistema chiamerà il metodo `OnSurfaceTextAvailable` quando il `SurfaceTexture` è pronto per l'uso. In questo metodo si accetta la `SurfaceTexture` che viene passata e la si imposta sulla trama di anteprima della fotocamera. È quindi possibile eseguire normali operazioni basate sulla visualizzazione, ad esempio impostando il `Rotation` e `Alpha`, come nell'esempio precedente. L'applicazione risultante, in esecuzione in un dispositivo, è illustrata di seguito:

[![esempio di app in esecuzione in un dispositivo, visualizzazione di un'immagine](texture-view-images/17-textureviewdemo.png)](texture-view-images/17-textureviewdemo.png#lightbox)

Per usare la `TextureView`, è necessario abilitare l'accelerazione hardware, che verrà usata per impostazione predefinita a partire dal livello API 14. Inoltre, poiché in questo esempio viene utilizzata la fotocamera, sia l'autorizzazione `android.permission.CAMERA` che la funzionalità `android.hardware.camera` devono essere impostate in **file AndroidManifest. XML**.

## <a name="related-links"></a>Collegamenti correlati

- [TextureViewDemo (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/textureviewdemo)/)
