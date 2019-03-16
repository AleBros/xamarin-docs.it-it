---
title: Uso di ARKit con UrhoSharp in xamarin. IOS
description: Questo documento descrive come configurare un'app ARKit in xamarin. IOS, quindi esamina la modalità di rendering frame, come regolare la fotocamera, come rilevare i piani, come lavorare con illuminazione e altro ancora. Viene inoltre illustrato UrhoSharp e scrittura di codice per HoloLens.
ms.prod: xamarin
ms.assetid: 877AF974-CC2E-48A2-8E1A-0EF9ABF2C92D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/01/2017
ms.openlocfilehash: b02ecc8a40f6ff8a1862d50202439d369003a53d
ms.sourcegitcommit: 650458de1d362cd7de174cacef7838f0e74426f3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/15/2019
ms.locfileid: "57981601"
---
# <a name="using-arkit-with-urhosharp-in-xamarinios"></a>Uso di ARKit con UrhoSharp in xamarin. IOS

Con l'introduzione del [ARKit](https://developer.apple.com/arkit/), Apple ha reso semplice per gli sviluppatori di creare applicazioni di realtà aumentata. ARKit può tenere traccia della posizione esatta del dispositivo e rilevare diverse superfici in tutto il mondo, e quindi spetta allo sviluppatore di unire i dati risultanti dal ARKit nel codice.

[UrhoSharp](~/graphics-games/urhosharp/index.md) fornisce una API 3D completa e facile da usare che è possibile usare per creare applicazioni 3D.   Entrambi questi possono essere combinati insieme, ARKit per fornire le informazioni sul mondo fisiche e Urho per eseguire il rendering dei risultati.

Questa pagina illustra come connettere questi due mondi insieme per creare applicazioni eccezionali realtà aumentata.


## <a name="the-basics"></a>Nozioni di base

Ciò che si desidera effettuare è presentano contenuto 3D nella parte superiore del mondo, come illustrato dall'iPhone/iPad.   L'idea consiste nell'unire il contenuto proveniente dalla fotocamera del dispositivo con il contenuto 3D e l'utente del dispositivo viene spostato nella stanza per assicurarsi che l'oggetto 3D è quello fa parte di tale spazio a posteriori, questa operazione viene eseguita mediante l'ancoraggio gli oggetti in questo mondo.

![Figura animata in ARKit](urhosharp-images/image1.gif)


La libreria Urho verranno usati per caricare l'asset 3D e inserirli in tutto il mondo e ARKit verranno usati per ottenere il flusso video provenienti dalla fotocamera oltre la posizione del telefono in tutto il mondo.   Quando l'utente passa con il suo telefono, si userà le modifiche nel percorso per aggiornare il sistema di coordinate che il motore Urho è visualizzata.

In questo modo, quando si posiziona un oggetto nello spazio 3D e l'utente si sposta, la posizione dell'oggetto 3D riflette la posizione e il percorso in cui è stato inserito.

## <a name="setting-up-your-application"></a>Configurazione dell'applicazione

### <a name="ios-application-launch"></a>Avvio applicazione iOS

L'applicazione iOS deve creare e avviare il contenuto 3D, eseguire questa operazione tramite la creazione di un'implementazione di una sottoclasse del [ `Urho.Application` ](https://developer.xamarin.com/api/type/Urho.Application/) e fornire il codice di programma di installazione eseguendo l'override di `Start` (metodo).  Si tratta di dove scena Ottiene popolata con dati, evento gestori sono di installazione e così via.

È stata introdotta un' `Urho.ArkitApp` classe che rappresenti le sottoclassi `Urho.Application` e nella relativa `Start` metodo svolge il grosso.   È sufficiente eseguire il Urho esistente dell'applicazione è modificare la classe base sia di tipo `Urho.ArkitApp` e si dispone di un'applicazione che eseguirà il urho scena in tutto il mondo.

### <a name="the-arkitapp-class"></a>La classe ArkitApp

Questa classe fornisce un set di pratiche impostazioni predefinite, entrambi una scena con alcuni oggetti chiave, l'elaborazione di eventi ARKit nonché come vengono recapitate dal sistema operativo.

Il programma di installazione viene eseguita `Start` metodo virtuale.   Quando si esegue l'override di questo metodo in una sottoclasse, è necessario assicurarsi che alla catena per l'elemento padre tramite `base.Start()` nella propria implementazione.

Il `Start` metodo consente di impostare la scena, viewport, fotocamera e una luce direzionale ed evidenzia quelli come proprietà pubbliche:

- una [ `Scene` ](https://developer.xamarin.com/api/type/Urho.Scene/) per contenere gli oggetti,
- un direzionale [ `Light` ](https://developer.xamarin.com/api/type/Urho.Light/) con ombreggiature e il cui percorso è disponibile tramite il `LightNode` proprietà
- una [ `Camera` ](https://developer.xamarin.com/api/type/Urho.Camera/) i cui componenti vengono aggiornati quando ARKit offre un aggiornamento per l'applicazione e
- una [ `ViewPort` ](https://developer.xamarin.com/api/type/Urho.Viewport/) visualizzano i risultati.


### <a name="your-code"></a>Il codice

È quindi necessario per creare una sottoclasse di `ArkitApp` classe ed eseguire l'override di `Start` (metodo).   Prima di tutto necessario eseguire il metodo è catena fino al `ArkitApp.Start` chiamando `base.Start()`.  Successivamente, è possibile usare qualsiasi dell'impostazione della proprietà da ArkitApp per aggiungere gli oggetti nella scena, è possibile personalizzare le luci, shadows o eventi che si desidera gestire.

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

E questo è davvero che tutto questo è necessario eseguire a questo punto per disporre il contenuto 3D visualizzato in realtà aumentata.

Urho utilizza formati personalizzati per i modelli 3D e le animazioni, pertanto è necessario esportare le risorse in tale formato.   È possibile usare strumenti come il [componente aggiuntivo Blender Urho3D](https://github.com/reattiva/Urho3D-Blender) e [UrhoAssetImporter](https://github.com/EgorBo/UrhoAssetImporter) in grado di convertire questi asset da formati comuni, ad esempio DBX, DAE, OBJ, Blend, 3D-Max nel formato richiesto dal Urho.

Per altre informazioni sulla creazione di applicazioni 3D tramite Urho, visitare il [Introduzione a UrhoSharp](~/graphics-games/urhosharp/introduction.md) Guida.

## <a name="arkitapp-in-depth"></a>ArkitApp in profondità

> [!NOTE]
> In questa sezione è destinata agli sviluppatori che vogliono personalizzare il comportamento predefinito di UrhoSharp e ARKit oppure da un Acquisisci informazioni dettagliate sul funzionamento dell'integrazione.   Non è necessario leggere questa sezione.

L'API ARKit è piuttosto semplice, si crea e configura un [ARSession](https://developer.apple.com/documentation/arkit/arsession) dell'oggetto che quindi inizia a distribuire [ARFrame](https://developer.apple.com/documentation/arkit/arframe) oggetti.   Contengono entrambi l'immagine acquisita dalla fotocamera oltre la posizione del mondo reale stimata del dispositivo.

Si verrà comporre le immagini vengono recapitate dalla fotocamera a noi con i nostri materiali 3D e regolare la fotocamera nel UrhoSharp in modo che corrisponda le probabilità che la posizione del dispositivo e posizione.

Il diagramma seguente illustra che cosa ha luogo `ArkitApp` classe:

[![Diagramma di classi e le schermate di ArkitApp](urhosharp-images/image2.png)](urhosharp-images/image2.png#lightbox)

### <a name="rendering-the-frames"></a>I fotogrammi di rendering

L'idea è semplice, combinare il video provenienti dalla fotocamera con la grafica 3D per produrre l'immagine combinato.     Si otterrà una serie di queste immagini acquisite nella sequenza e si verrà combinare questo input con la scena Urho.

Il modo più semplice per farlo consiste nell'inserire un [ `RenderPathCommand` ](https://developer.xamarin.com/api/type/Urho.RenderPathCommand/) in principale [ `RenderPath` ](https://developer.xamarin.com/api/type/Urho.RenderPath/).  Si tratta di un set di comandi che vengono eseguiti per disegnare un singolo frame.  Questo comando verrà compilati il riquadro di visualizzazione con qualsiasi trama che viene passato ad esso.    Impostiamo questa sul primo frame che è processo e della definizione effettiva viene eseguita in th **ARRenderPath.xml** file che viene caricato in questa fase.

Tuttavia, stiamo affrontare due problemi per fondere insieme questi due mondi:


1. In iOS, le trame GPU deve avere una risoluzione che è una potenza di due, ma i frame che otteniamo dalla fotocamera non è risoluzione che sono una potenza di due, ad esempio: 1280x720.
2. I frame sono codificati nel [YUV](https://en.wikipedia.org/wiki/YUV) formato, rappresentato da due immagini - luminanza e croma.

I frame YUV possono essere di due risoluzioni diverse.  un'immagine di 1280 x 720 che rappresenta la luminanza (fondamentalmente un'immagine in scala di grigi) e molto altro 640x360 inferiori per il componente di cromatura:

![Immagine che illustra combinazione Y e i componenti UV](urhosharp-images/image3.png)


Per disegnare un'immagine colorata completa con OpenGL ES è necessario scrivere un piccolo shader che accetta gli slot di trama luminanza (componente Y) e crominanza (UV piani).  In UrhoSharp abbiano nomi - "sDiffMap" e "sNormalMap" e convertirli in formato RGB:

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

Per eseguire il rendering la trama non è una potenza di due risoluzione è necessario definire Texture2D con i parametri seguenti:

```chsarp
// texture for UV-plane;
cameraUVtexture = new Texture2D();
cameraUVtexture.SetNumLevels(1);
cameraUVtexture.SetSize(640, 360, Graphics.LuminanceAlphaFormat, TextureUsage.Dynamic);
cameraUVtexture.FilterMode = TextureFilterMode.Bilinear;
cameraUVtexture.SetAddressMode(TextureCoordinate.U, TextureAddressMode.Clamp);
cameraUVtexture.SetAddressMode(TextureCoordinate.V, TextureAddressMode.Clamp);
```

In questo modo siamo in grado di eseguire il rendering di immagini acquisite come sfondo ed eseguire il rendering di qualsiasi scena sopra di esso come quello scary mutanti.

### <a name="adjusting-the-camera"></a>Regolazione della fotocamera

Il `ARFrame` oggetti contengono anche la posizione del dispositivo stimato.  È a questo punto, occorre spostare fotocamera gioco ARFrame - prima ARKit non era un grande vantaggio track orientamento del dispositivo (esegue il rollup, passo e rotazione) e rendering ologramma aggiunto sopra il video -, ma se si sposta il dispositivo un po' - vntana verrà dello sfasamento.

In questo caso poiché non sono in grado di tenere traccia dei movimenti sensori incorporati, ad esempio il Giroscopio, questi possono solo l'accelerazione.  Analisi ARKit ogni funzionalità frame ed estrae i punti per tenere traccia ed è pertanto in grado di fornire un'accurata trasformano matrice contenente i dati di spostamento e rotazione.

Ad esempio, si tratta come è possibile ottenere la posizione corrente:

```csharp
var row = arCamera.Transform.Row3;
CameraNode.Position = new Vector3(row.X, row.Y, -row.Z);
```

Usiamo `-row.Z` perché ARKit utilizza un sistema di coordinate di destra.


### <a name="plane-detection"></a>Rilevamento di piano

È in grado di rilevare i piani orizzontali ARKit e questa possibilità consente di interagire con il mondo reale, ad esempio, non è possibile inserire il mutante in una tabella reale o un piano. Il modo più semplice per eseguire questa operazione è usare il metodo di HitTest (raycasting). Converte le coordinate dello schermo (0,5, 0,5 corrisponde al punto centrale) nelle coordinate reali (0, 0; 0 indica la posizione del primo frame).

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

a questo punto possiamo inseriamo il mutante su una superficie orizzontale a seconda di dove sullo schermo del dispositivo che toccare è:

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

![Figura animata la modifica piani durante la migrazione di visualizzazione](urhosharp-images/image4.gif)

### <a name="realistic-lighting"></a>Illuminazione realistico

A seconda delle condizioni di illuminazione reale, la scena virtuale deve essere più chiaro o scuro per adattarlo meglio dell'ambiente circostante. ARFrame contiene una proprietà LightEstimate che è possibile usare per regolare l'illuminazione ambientale Urho, questa operazione viene eseguita come segue:

```csharp
var ambientIntensity = (float) frame.LightEstimate.AmbientIntensity / 1000f;
var zone = Scene.GetComponent<Zone>();
zone.AmbientColor = Color.White * ambientIntensity;
```

### <a name="beyond-ios---hololens"></a>Oltre a iOS - HoloLens

UrhoSharp [viene eseguito in tutti i sistemi operativi principali](~/graphics-games/urhosharp/platform/index.md), pertanto è possibile riutilizzare il codice esistente in un' posizione.

HoloLens è una delle piattaforme più interessanti di che cui è in esecuzione.   Ciò significa che è possibile passare facilmente tra iOS e HoloLens per creare fantastiche applicazioni di realtà aumentata uso di UrhoSharp.

È possibile trovare l'origine MutantDemo alla [github.com/EgorBo/ARKitXamarinDemo](https://github.com/EgorBo/ARKitXamarinDemo).


## <a name="related-links"></a>Collegamenti correlati

- [UrhoSharp](~/graphics-games/urhosharp/index.md)
- [ARKitXamarinDemo (con UrhoSharp) (esempio)](https://github.com/EgorBo/ARKitXamarinDemo)
