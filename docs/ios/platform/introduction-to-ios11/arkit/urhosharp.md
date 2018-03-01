---
title: Con ARKit UrhoSharp
ms.topic: article
ms.prod: xamarin
ms.assetid: 877AF974-CC2E-48A2-8E1A-0EF9ABF2C92D
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 08/01/2016
ms.openlocfilehash: 4c6928af364870541581b784b7c4e9e7717eb2db
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="using-arkit-with-urhosharp"></a>Con ARKit UrhoSharp

Con l'introduzione di [ARKit](https://developer.apple.com/arkit/), Apple ha reso semplice per gli sviluppatori di creare applicazioni realtà aumentata. ARKit può tenere traccia della posizione esatta del dispositivo e rilevare superfici diversi nel mondo e sarà quindi compito dello sviluppatore per combinare i dati provengano da ARKit nel codice.

[UrhoSharp](~/graphics-games/urhosharp/index.md) fornisce un'API 3D completa e facili da utilizzare che consente di creare applicazioni 3D.   Entrambi questi possono essere combinati insieme, ARKit per fornire le informazioni sul mondo fisiche e Urho per eseguire il rendering dei risultati.

Questa pagina viene descritto come connettere questi due mondi insieme per creare applicazioni di grande realtà aumentata.


## <a name="the-basics"></a>Nozioni di base

Ciò che si desidera eseguire è presentano contenuto 3D nella parte superiore del mondo, come illustrato dall'iPhone.   L'idea è di blend i contenuti provenienti dalla fotocamera del telefono con il contenuto 3D e come l'utente del telefono sposta nella stanza per garantire che l'oggetto 3D è quello fa parte di tale spazio - questa operazione viene eseguita mediante l'ancoraggio gli oggetti in questo mondo.

![Figura animata in ARKit](urhosharp-images/image1.gif)


Si utilizzerà la libreria Urho per caricare l'asset 3D e posizionarli nel mondo e ARKit verrà usato per ottenere il flusso video provenienti dalla fotocamera, nonché la posizione del telefono nel mondo.   Come l'utente passa con il suo telefono, si utilizzerà le modifiche nel percorso per aggiornare il sistema di coordinate in cui il motore Urho viene visualizzato.

In questo modo, quando si posiziona un oggetto nello spazio 3D e si sposta, la posizione dell'oggetto 3D riflette la posizione e il percorso in cui è stato inserito.

## <a name="setting-up-your-application"></a>Configurazione dell'applicazione

### <a name="ios-application-launch"></a>Avvio dell'applicazione iOS

L'applicazione iOS deve creare e avviare il contenuto 3D, farlo mediante la creazione di un'implementazione di una sottoclasse del [ `Urho.Application` ](https://developer.xamarin.com/api/type/Urho.Application/) e fornire il codice di programma di installazione eseguendo l'override di `Start` metodo.  Si tratta di scena in popolata con dati evento gestori sono del programma di installazione e così via.

È stata introdotta un' `Urho.ArkitApp` classe che rappresenta una sottoclasse `Urho.Application` e nella relativa `Start` metodo provvederà.   È sufficiente per eseguire il Urho esistente dell'applicazione è modificare la classe base per essere di tipo `Urho.ArkitApp` e si dispone di un'applicazione che verrà eseguita la scena urho nel mondo.

### <a name="the-arkitapp-class"></a>La classe ArkitApp

Questa classe fornisce un set di impostazioni predefinite pratiche, entrambi una scena con alcuni oggetti chiave e l'elaborazione di eventi ARKit appena vengono recapitati dal sistema operativo.

Il programma di installazione viene eseguita `Start` metodo virtuale.   Quando si esegue l'override di questo metodo in una sottoclasse, è necessario assicurarsi che per la concatenazione al padre con `base.Start()` in un'implementazione personalizzata.

Il `Start` metodo consente di impostare la scena, viewport, fotocamera e una luce direzionale e superfici come proprietà pubbliche:

- un [ `Scene` ](https://developer.xamarin.com/api/type/Urho.Scene/) per contenere gli oggetti,
- un direzionale [ `Light` ](https://developer.xamarin.com/api/type/Urho.Light/) con ombreggiature, e il cui percorso è disponibile tramite il `LightNode` proprietà
- un [ `Camera` ](https://developer.xamarin.com/api/type/Urho.Camera/) i cui componenti vengono aggiornati quando ARKit offre un aggiornamento per l'applicazione e
- un [ `ViewPort` ](https://developer.xamarin.com/api/type/Urho.Viewport/) la visualizzazione dei risultati.


### <a name="your-code"></a>Il codice

È quindi necessario per creare una sottoclasse di `ArkitApp` classe ed eseguire l'override di `Start` metodo.   La prima operazione che deve eseguire il metodo è catena fino al `ArkitApp.Start` chiamando `base.Start()`.  Successivamente, è possibile utilizzare qualsiasi dell'impostazione della proprietà da ArkitApp per aggiungere oggetti alla scena, personalizzare le luci, ombreggiature o eventi che si desidera gestire.

L'esempio ARKit/UrhoSharp carica un personaggio con trame e riproduce l'animazione, con l'implementazione seguente:

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

E che è piuttosto semplice che è necessario fare a questo punto per il contenuto 3D visualizzato in realtà aumentata.

Urho utilizza formati personalizzati per i modelli 3D e le animazioni, pertanto è necessario esportare le risorse in questo formato.   È possibile utilizzare strumenti come il [aggiuntivo Blender Urho3D](https://github.com/reattiva/Urho3D-Blender) e [UrhoAssetImporter](https://github.com/EgorBo/UrhoAssetImporter) in grado di convertire gli asset da formati più diffusi come DBX, DAE, OBJ, Blend, 3D-Max nel formato richiesto dal Urho.

Per ulteriori informazioni sulla creazione di applicazioni 3D tramite Urho, visitare il [Introduzione a UrhoSharp](~/graphics-games/urhosharp/introduction.md) Guida.

## <a name="arkitapp-in-depth"></a>ArkitApp in profondità

> [!NOTE]
> In questa sezione è destinata agli sviluppatori che si desidera personalizzare il comportamento predefinito di UrhoSharp e ARKit o per una visione più sul funzionamento dell'integrazione.   Non è necessario leggere questa sezione.

L'API ARKit è piuttosto semplice, di creare e configurare un [ARSession](https://developer.apple.com/documentation/arkit/arsession) oggetto cui quindi iniziare a recapitare [ARFrame](https://developer.apple.com/documentation/arkit/arframe) oggetti.   Che contengono sia l'immagine acquisita dalla fotocamera, nonché la posizione del mondo reale prevista del dispositivo.

Si verrà composizione le immagini vengono inviate dalla fotocamera a Microsoft con il contenuto 3D e regolare la fotocamera in UrhoSharp in modo che corrisponda il possibilità nel percorso del dispositivo e posizione.

Il diagramma seguente illustra ciò che avviene `ArkitApp` classe:

[ ![Diagramma di classi e le schermate di ArkitApp](urhosharp-images/image2.png)](urhosharp-images/image2.png)

### <a name="rendering-the-frames"></a>I frame di rendering

L'idea è semplice, combinare il video proveniente dalla videocamera con la grafica 3D per produrre l'immagine combinato.     Si otterrà una serie di queste immagini acquisite in sequenza e si verrà combinare l'input con la scena Urho.

È il modo più semplice per inserire un [ `RenderPathCommand` ](https://developer.xamarin.com/api/type/Urho.RenderPathCommand/) in principale [ `RenderPath` ](https://developer.xamarin.com/api/type/Urho.RenderPath/).  Si tratta di un set di comandi che vengono eseguite per disegnare un singolo frame.  Questo comando verrà compilati il viewport con qualsiasi trama che è passare ad esso.    Impostiamo questa sul frame prima che si trova processo e della definizione effettiva viene eseguita in th **ARRenderPath.xml** file caricato a questo punto.

Tuttavia, stiamo affrontare questi due mondi fondere due problemi:


1. In iOS, le trame GPU deve disporre di una soluzione che è una potenza di due, ma i frame che è possibile ottenere dalla fotocamera non dispone di risoluzione di una potenza di due, ad esempio: 1280x720.
2. I frame vengono codificati in [YUV](https://en.wikipedia.org/wiki/YUV) formato, rappresentato da due immagini - luminanza e crominanza.

I frame YUV disponibili in due diverse risoluzioni.  immagine di diverse da 1280x720 che rappresentano luminanza (fondamentalmente un'immagine in scala di grigi) e quantità 640x360 inferiori per il componente crominanza:

![Immagine che illustra la combinazione Y e i componenti UV](urhosharp-images/image3.png)


Per disegnare un'immagine colorata completa utilizzando OpenGL ES è necessario scrivere uno shader di piccole dimensioni che richiede luminanza (componente Y) e crominanza (UV piani) dagli slot di trama.  In UrhoSharp abbiano nomi - "sDiffMap" e "sNormalMap" e convertirli in formato RGB:

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

Per eseguire il rendering di trama che non dispone di una potenza di due risoluzione è necessario che definiamo Texture2D con i parametri seguenti:

```chsarp
// texture for UV-plane;
cameraUVtexture = new Texture2D();
cameraUVtexture.SetNumLevels(1);
cameraUVtexture.SetSize(640, 360, Graphics.LuminanceAlphaFormat, TextureUsage.Dynamic);
cameraUVtexture.FilterMode = TextureFilterMode.Bilinear;
cameraUVtexture.SetAddressMode(TextureCoordinate.U, TextureAddressMode.Clamp);
cameraUVtexture.SetAddressMode(TextureCoordinate.V, TextureAddressMode.Clamp);
```

Pertanto è in grado di eseguire il rendering di immagini acquisite come sfondo ed eseguire il rendering di qualsiasi scena sovrastante mutante sconvolgere simili a quelli presenti.

### <a name="adjusting-the-camera"></a>La regolazione della fotocamera

Il `ARFrame` gli oggetti contengono anche la posizione del dispositivo stimato.  È ora è necessario spostare la fotocamera gioco ARFrame - prima ARKit non è stato ologrammi un grande vantaggio traccia orientamento del dispositivo (operazioni di rollback, del passo e rotazione) e rendering ologramma aggiunto sopra il video -, ma se si sposta il dispositivo un bit - verrà dello sfasamento.

Che si verifica perché sensori incorporati, ad esempio Giroscopio non sono in grado di rilevare i movimenti, possono essere utilizzate solo l'accelerazione.  Analisi ARKit ogni funzionalità frame ed estrae punti per tenere traccia e pertanto in grado di fornire un accurato trasformano una matrice contenente i dati di spostamento e la rotazione.

Ad esempio, ecco come ottenere la posizione corrente:

```csharp
var row = arCamera.Transform.Row3;
CameraNode.Position = new Vector3(row.X, row.Y, -row.Z);
```

Utilizziamo `-row.Z` poiché ARKit utilizza un sistema di coordinate da destra.


### <a name="plane-detection"></a>Rilevamento di piano

È in grado di rilevare i piani orizzontali ARKit e questa possibilità consente di interagire con il mondo reale, ad esempio, è possibile inserire il mutante in una tabella o un piano. Il modo più semplice a tale scopo è utilizzare HitTest (metodo) (raycasting). Converte le coordinate dello schermo (0,5, 0,5 corrisponde al centro) nelle coordinate del mondo reale (0; 0; 0 è la posizione del primo frame).

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

è possibile inserire il mutante su una superficie orizzontale a seconda di dove sullo schermo del dispositivo che toccare è:

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

![Figura animata modifica piani quando si sposta di visualizzazione](urhosharp-images/image4.gif)

### <a name="realistic-lighting"></a>Illuminazione realistico

A seconda delle condizioni di illuminazione reali, la scena virtuale deve essere più chiaro o scuro per adattarlo meglio circostanti. ARFrame contiene una proprietà LightEstimate che è possibile utilizzare per regolare la luce Urho, questa operazione viene eseguita come segue:


    var ambientIntensity = (float) frame.LightEstimate.AmbientIntensity / 1000f;
    var zone = Scene.GetComponent<Zone>();
    zone.AmbientColor = Color.White * ambientIntensity;


### <a name="beyond-ios---hololens"></a>Oltre a iOS - HoloLens

UrhoSharp [viene eseguita in tutti i principali sistemi operativi](~/graphics-games/urhosharp/platform/index.md), pertanto è possibile riutilizzare il codice esistente in un' posizione.

HoloLens è una delle piattaforme più interessanti per che cui è in esecuzione.   Ciò significa che è possibile passare facilmente tra iOS e HoloLens delle applicazioni straordinario realtà aumentata utilizzando UrhoSharp.

È possibile trovare l'origine MutantDemo in [github.com/EgorBo/ARKitXamarinDemo](https://github.com/EgorBo/ARKitXamarinDemo).


## <a name="related-links"></a>Collegamenti correlati

- [UrhoSharp](~/graphics-games/urhosharp/index.md)
- [ARKitXamarinDemo (with UrhoSharp) (sample)](https://github.com/EgorBo/ARKitXamarinDemo)
