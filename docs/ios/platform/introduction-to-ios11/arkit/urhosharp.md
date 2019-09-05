---
title: Uso di ARKit con UrhoSharp in Novell. iOS
description: Questo documento descrive come configurare un'app ARKit in Novell. iOS, quindi esamina come viene eseguito il rendering dei frame, come modificare la fotocamera, come rilevare i piani, come usare l'illuminazione e altro ancora. Viene inoltre illustrato UrhoSharp e la scrittura di codice per HoloLens.
ms.prod: xamarin
ms.assetid: 877AF974-CC2E-48A2-8E1A-0EF9ABF2C92D
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 08/01/2017
ms.openlocfilehash: 4c23caade91a1a46d6b2b9bb2425a5bdead40030
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70289239"
---
# <a name="using-arkit-with-urhosharp-in-xamarinios"></a>Uso di ARKit con UrhoSharp in Novell. iOS

Con l'introduzione di [ARKit](https://developer.apple.com/arkit/), Apple ha reso semplice per gli sviluppatori la creazione di applicazioni di realtà aumentata. ARKit è in grado di tenere traccia della posizione esatta del dispositivo e di rilevare le varie superfici del mondo ed è quindi lo sviluppatore a fondere i dati provenienti da ARKit nel codice.

[UrhoSharp](~/graphics-games/urhosharp/index.md) fornisce un'API 3D completa e facile da usare per la creazione di applicazioni 3D.   Entrambi possono essere combinati, ARKit per fornire le informazioni fisiche sul mondo e Urho per eseguire il rendering dei risultati.

In questa pagina viene illustrato come connettere questi due mondi per creare applicazioni di realtà potenziate.

## <a name="the-basics"></a>Nozioni di base

Si tratta di un contenuto 3D presente in tutto il mondo, come visto da iPhone/iPad.   L'idea è quella di combinare il contenuto proveniente dalla fotocamera del dispositivo con il contenuto 3D e, quando l'utente del dispositivo si sposta nella stanza, per garantire che l'oggetto 3D si comporti come se facesse parte di tale spazio. questa operazione viene eseguita mediante l'ancoraggio degli oggetti in questo mondo.

![Figura animata in ARKit](urhosharp-images/image1.gif)

Utilizzeremo la libreria Urho per caricare le risorse 3D e inserirle nel mondo e utilizzeremo ARKit per ottenere il flusso video proveniente dalla fotocamera, oltre alla posizione del telefono nel mondo.   Quando l'utente si sposta con il proprio telefono, verranno usate le modifiche apportate al percorso per aggiornare il sistema di coordinate visualizzato dal motore Urho.

In questo modo, quando si inserisce un oggetto nello spazio 3D e si sposta l'utente, la posizione dell'oggetto 3D riflette la posizione e la posizione in cui è stata posizionata.

## <a name="setting-up-your-application"></a>Configurazione dell'applicazione

### <a name="ios-application-launch"></a>Avvio dell'applicazione iOS

L'applicazione iOS deve creare e avviare il contenuto 3D. a tale scopo, creare un'implementazione di una sottoclasse di `Urho.Application` e fornire il codice di configurazione eseguendo l'override `Start` del metodo.  Questa è la posizione in cui la scena viene popolata con i dati, i gestori eventi sono impostati e così via.

È stata introdotta `Urho.ArkitApp` una classe che le `Urho.Application` sottoclassi e il `Start` relativo metodo esegue il grosso carico.   È sufficiente fare in modo che l'applicazione Urho esistente modifichi la classe di base in modo che sia `Urho.ArkitApp` di tipo e che si disponga di un'applicazione che eseguirà la scena Urho nel mondo.

### <a name="the-arkitapp-class"></a>Classe ArkitApp

Questa classe fornisce un set di valori predefiniti pratici, sia una scena con alcuni oggetti chiave che l'elaborazione degli eventi ARKit così come vengono recapitati dal sistema operativo.

Il programma di installazione viene svolto `Start` nel metodo virtuale.   Quando si esegue l'override di questo metodo nella sottoclasse, è necessario assicurarsi di concatenare al padre `base.Start()` usando nella propria implementazione.

Il `Start` metodo imposta la scena, il viewport, la fotocamera e una luce direzionale e li presenta come proprietà pubbliche:

- oggetto `Scene` che consente di conservare gli oggetti.
- direzionale `Light` con ombreggiature e la cui posizione è disponibile tramite la `LightNode` proprietà
- oggetto `Camera` i cui componenti vengono aggiornati quando ARKit recapita un aggiornamento all'applicazione e
- oggetto `ViewPort` che Visualizza i risultati.

### <a name="your-code"></a>Codice

È quindi necessario sottoclassare `ArkitApp` la classe ed eseguire `Start` l'override del metodo.   La prima cosa che il metodo deve eseguire è concatenare a `ArkitApp.Start` `base.Start()`chiamando.  Successivamente, è possibile usare una qualsiasi delle proprietà impostate da ArkitApp per aggiungere gli oggetti alla scena, personalizzare le luci, le ombre o gli eventi che si vuole gestire.

L'esempio ARKit/UrhoSharp carica un carattere animato con trame e riproduce l'animazione, con l'implementazione seguente:

```csharp
public class MutantDemo : ArkitApp
{
    [Preserve]
    public MutantDemo(ApplicationOptions opts) : base(opts) { }

    Node mutantNode;

    protected override void Start()
    {
        base.Start ();

        // Mutant
        mutantNode = Scene.CreateChild();
        mutantNode.Rotation = new Quaternion(x: 0, y:15, z:0);
        mutantNode.Position = new Vector3(0, -1f, 2f); /*two meters away*/
        mutantNode.SetScale(0.5f);

        var mutant = mutantNode.CreateComponent<AnimatedModel>();
        mutant.Model = ResourceCache.GetModel("Models/Mutant.mdl");
        mutant.Material = ResourceCache.GetMaterial("Materials/mutant_M.xml");

        var animation = mutantNode.CreateComponent<AnimationController>();
        animation.Play("Animations/Mutant_HipHop1.ani", 0, true, 0.2f);
    }
}
```

Questo è tutto ciò che è necessario fare a questo punto per visualizzare il contenuto 3D in realtà aumentata.

Urho usa formati personalizzati per le animazioni e i modelli 3D, quindi è necessario esportare gli asset in questo formato.   È possibile usare strumenti come il [componente aggiuntivo Urho3D Blender](https://github.com/reattiva/Urho3D-Blender) e [UrhoAssetImporter](https://github.com/EgorBo/UrhoAssetImporter) che possono convertire questi asset da formati diffusi come dbx, DAE, obj, Blend, 3D-Max nel formato richiesto da Urho.

Per altre informazioni sulla creazione di applicazioni 3D con Urho, vedere la Guida [introduttiva a UrhoSharp](~/graphics-games/urhosharp/introduction.md) .

## <a name="arkitapp-in-depth"></a>ArkitApp in profondità

> [!NOTE]
> Questa sezione è destinata agli sviluppatori che vogliono personalizzare l'esperienza predefinita di UrhoSharp e ARKit o vogliono ottenere informazioni più dettagliate sul funzionamento dell'integrazione.   Non è necessario leggere questa sezione.

L'API ARKit è piuttosto semplice. si crea e si configura un oggetto [ARSession](https://developer.apple.com/documentation/arkit/arsession) che quindi inizia a consegnare oggetti [ARFrame](https://developer.apple.com/documentation/arkit/arframe) .   Questi contengono sia l'immagine acquisita dalla fotocamera che la posizione reale stimata del dispositivo.

Verranno comunicate le immagini recapitate dalla fotocamera a Microsoft con il contenuto 3D e si modificherà la fotocamera in UrhoSharp in modo che corrisponda alle probabilità nella posizione e nella posizione del dispositivo.

Il diagramma seguente Mostra gli elementi che avvengono nella `ArkitApp` classe:

[![Diagramma delle classi e delle schermate in ArkitApp](urhosharp-images/image2.png)](urhosharp-images/image2.png#lightbox)

### <a name="rendering-the-frames"></a>Rendering dei frame

L'idea è semplice, combinando il video proveniente dalla fotocamera con la grafica 3D per produrre l'immagine combinata.     Si otterrà una serie di queste immagini acquisite in sequenza e si mescolerà questo input con la scena Urho.

Il modo più semplice per eseguire questa operazione consiste nell'inserire `RenderPathCommand` un oggetto nella `RenderPath`principale.  Si tratta di un set di comandi eseguiti per creare un singolo frame.  Questo comando consente di riempire il viewport con qualsiasi trama passata.    Questa operazione viene configurata sul primo frame che viene elaborato e la definizione effettiva viene eseguita nel file **ARRenderPath. XML** caricato a questo punto.

Tuttavia, sono stati affrontati due problemi per unire questi due mondi:


1. In iOS, le trame GPU devono avere una risoluzione che è una potenza di due, ma i frame che verranno riportati dalla fotocamera non hanno una risoluzione che è una potenza di due, ad esempio: 1280x720.
2. I frame sono codificati in formato [YUV](https://en.wikipedia.org/wiki/YUV) , rappresentati da due immagini: luma e Chroma.

I frame YUV sono disponibili in due risoluzioni diverse.  un'immagine 1280x720 che rappresenta la luminanza (fondamentalmente un'immagine di scala grigia) e un 640x360 molto più piccolo per il componente cromatura:

![Immagine che illustra la combinazione di componenti Y e UV](urhosharp-images/image3.png)


Per creare un'immagine colorata completa usando OpenGL ES è necessario scrivere un piccolo shader che accetta la luminanza (componente Y) e cromatura (piani UV) dagli slot di trama.  In UrhoSharp i nomi sono "sDiffMap" e "sNormalMap" e vengono convertiti in formato RGB:

```csharp
mat4 ycbcrToRGBTransform = mat4(
    vec4(+1.0000, +1.0000, +1.0000, +0.0000),
    vec4(+0.0000, -0.3441, +1.7720, +0.0000),
    vec4(+1.4020, -0.7141, +0.0000, +0.0000),
    vec4(-0.7010, +0.5291, -0.8860, +1.0000));

vec4 ycbcr = vec4(texture2D(sDiffMap, vTexCoord).r,
                    texture2D(sNormalMap, vTexCoord).ra, 1.0);
gl_FragColor = ycbcrToRGBTransform * ycbcr;
```

Per eseguire il rendering della trama che non ha una potenza di due soluzioni, è necessario definire Texture2D con i parametri seguenti:

```chsarp
// texture for UV-plane;
cameraUVtexture = new Texture2D();
cameraUVtexture.SetNumLevels(1);
cameraUVtexture.SetSize(640, 360, Graphics.LuminanceAlphaFormat, TextureUsage.Dynamic);
cameraUVtexture.FilterMode = TextureFilterMode.Bilinear;
cameraUVtexture.SetAddressMode(TextureCoordinate.U, TextureAddressMode.Clamp);
cameraUVtexture.SetAddressMode(TextureCoordinate.V, TextureAddressMode.Clamp);
```

In questo modo, possiamo eseguire il rendering delle immagini acquisite come sfondo ed eseguire il rendering di qualsiasi scena al di sopra del mutatore spaventoso.

### <a name="adjusting-the-camera"></a>Regolazione della fotocamera

Gli `ARFrame` oggetti contengono anche la posizione stimata del dispositivo.  A questo punto è necessario spostare la fotocamera del gioco ARFrame, prima di ARKit, non è stato un grosso problema per tenere traccia dell'orientamento del dispositivo (Roll, pitch e imbardata) ed eseguire il rendering di un ologramma aggiunto sopra il video, ma se si sposta il dispositivo gli ologrammi a bit verranno spostati.

Ciò accade perché i sensori incorporati, ad esempio giroscopio, non sono in grado di tenere traccia dei movimenti, ma possono solo accelerare.  ARKit analizza ogni frame ed estrae i punti di funzionalità da rilevare e quindi è in grado di fornire una matrice di trasformazione accurata contenente i dati di spostamento e rotazione.

Ad esempio, questo è il modo in cui è possibile ottenere la posizione corrente:

```csharp
var row = arCamera.Transform.Row3;
CameraNode.Position = new Vector3(row.X, row.Y, -row.Z);
```

`-row.Z` Usiamo perché ARKit usa un sistema di coordinate di destra.


### <a name="plane-detection"></a>Rilevamento del piano

ARKit è in grado di rilevare i piani orizzontali e questa possibilità consente di interagire con il mondo reale. ad esempio, è possibile inserire il mutatore in una tabella reale o in un piano. Il modo più semplice per eseguire questa operazione consiste nell'usare il metodo HitTest (Raycasting). Converte le coordinate dello schermo (0,5; 0,5 è il centro) nelle coordinate del mondo reale (0; 0; 0 è la posizione del primo frame).

```chsarp
protected Vector3? HitTest(float screenX = 0.5f, float screenY = 0.5f)
{
    var result = ARSession.CurrentFrame.HitTest(new CGPoint(screenX, screenY),
        ARHitTestResultType.ExistingPlaneUsingExtent)?.FirstOrDefault();
    if (result != null)
    {
        var row = result.WorldTransform.Row3;
        return new Vector3(row.X, row.Y, -row.Z);
    }
    return null;
}
```

a questo punto è possibile posizionare il mutatore su una superficie orizzontale a seconda della posizione in cui si tocca la schermata del dispositivo:

```chsarp
void OnTouchEnd(TouchEndEventArgs e)
{
    float x = e.X / (float)Graphics.Width;
    float y = e.Y / (float)Graphics.Height;
    var pos = HitTest(x, y);
    if (pos != null)
    mutantNode.Position = pos.Value;
}
```

![Figura animata modifica di piani come spostamento di visualizzazione](urhosharp-images/image4.gif)

### <a name="realistic-lighting"></a>Illuminazione realistica

A seconda delle condizioni di illuminazione del mondo reale, la scena virtuale dovrebbe essere più leggera o più scura per una migliore corrispondenza con l'ambiente. ARFrame contiene una proprietà LightEstimate che è possibile usare per modificare la luce di ambiente Urho. questa operazione viene eseguita in questo modo:

```csharp
var ambientIntensity = (float) frame.LightEstimate.AmbientIntensity / 1000f;
var zone = Scene.GetComponent<Zone>();
zone.AmbientColor = Color.White * ambientIntensity;
```

### <a name="beyond-ios---hololens"></a>Oltre iOS-HoloLens

UrhoSharp [viene eseguito in tutti i principali sistemi operativi](~/graphics-games/urhosharp/platform/index.md), quindi è possibile riutilizzare il codice esistente altrove.

HoloLens è una delle piattaforme più interessanti su cui è in esecuzione.   Ciò significa che è possibile passare agevolmente tra iOS e HoloLens per creare applicazioni di realtà aumentata straordinarie usando UrhoSharp.

È possibile trovare l'origine MutantDemo in [github.com/EgorBo/ARKitXamarinDemo](https://github.com/EgorBo/ARKitXamarinDemo).


## <a name="related-links"></a>Collegamenti correlati

- [UrhoSharp](~/graphics-games/urhosharp/index.md)
- [ARKitXamarinDemo (with UrhoSharp) (esempio)](https://github.com/EgorBo/ARKitXamarinDemo)
