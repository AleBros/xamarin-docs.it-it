---
title: Creazione di risorse per schermi diversi
ms.prod: xamarin
ms.assetid: 3D17DE45-115C-7192-5685-44F8EEE07DCC
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/28/2018
ms.openlocfilehash: cbd392dcae173eb3baf0fb8f0c3c4ec7c0da23a1
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73025123"
---
# <a name="creating-resources-for-varying-screens"></a>Creazione di risorse per schermate diverse

Android viene eseguito in molti dispositivi diversi, ognuno con una vasta gamma di risoluzioni, dimensioni dello schermo e densità dello schermo. Android eseguirà il ridimensionamento e il ridimensionamento per far funzionare l'applicazione su questi dispositivi, ma ciò può comportare un'esperienza utente non ottimale. Ad esempio, le immagini possono apparire sfocate o posizionate come previsto in una vista.

## <a name="concepts"></a>Concetti

Alcuni termini e concetti sono importanti da comprendere per supportare più schermate.

- **Dimensioni dello schermo** &ndash; la quantità di spazio fisico per la visualizzazione dell'applicazione

- **Densità dello schermo** &ndash; il numero di pixel in qualsiasi area specificata sullo schermo. L'unità di misura tipica è dpi (punti per pollice).

- **Risoluzione** &ndash; il numero totale di pixel sullo schermo. Quando si sviluppano applicazioni, la risoluzione non è altrettanto importante delle dimensioni e della densità dello schermo.

- Il **pixel a densità indipendente (DP)** &ndash; un'unità virtuale di misura per consentire la progettazione di layout indipendenti dalla densità. Questa formula viene usata per convertire il DP in pixel dello schermo:

    px &equals; dp &times; dpi &divide; 160

- L' **orientamento** &ndash; orientamento dello schermo viene considerato orizzontale quando è più ampio rispetto a quello di altezza. Al contrario, l'orientamento verticale è quando lo schermo è più alto di quello di larghezza. L'orientamento può variare durante il ciclo di vita di un'applicazione mentre l'utente ruota il dispositivo.

Si noti che i primi tre di questi concetti sono correlati tra loro &ndash; l'aumento della risoluzione senza aumentare la densità aumenterà le dimensioni dello schermo. Tuttavia, se si aumentano sia la densità che la risoluzione, le dimensioni dello schermo possono rimanere invariate. Questa relazione tra dimensioni dello schermo, densità e risoluzione complica rapidamente il supporto dello schermo.

Per gestire questa complessità, il Framework Android preferisce usare i *pixel indipendenti dalla densità (DP)* per i layout dello schermo. Usando i pixel indipendenti dalla densità, gli elementi dell'interfaccia utente vengono visualizzati all'utente per avere le stesse dimensioni fisiche sulle schermate con densità diverse.

## <a name="supporting-various-screen-sizes-and-densities"></a>Supporto di diverse dimensioni e densità dello schermo

Android gestisce la maggior parte del lavoro per il rendering corretto dei layout per ogni configurazione dello schermo. Tuttavia, esistono alcune azioni che possono essere intraprese per facilitare il sistema.

L'uso di pixel indipendenti dalla densità anziché dei pixel effettivi nei layout è sufficiente nella maggior parte dei casi per garantire l'indipendenza della densità.
Android eseguirà il ridimensionamento di drawables in fase di esecuzione fino alle dimensioni appropriate.
Tuttavia, è possibile che la scalabilità provochi la sfocatura delle bitmap. Per ovviare a questo problema, fornire risorse alternative per le diverse densità. Quando si progettano dispositivi per più risoluzioni e densità dello schermo, risulta più semplice iniziare con le immagini di risoluzione o densità più elevate e quindi ridurle.

### <a name="declare-the-supported-screen-size"></a>Dichiarare le dimensioni dello schermo supportate

Dichiarando le dimensioni dello schermo si garantisce che solo i dispositivi supportati possano scaricare l'applicazione. Questa operazione viene eseguita impostando l'elemento [Supports-screens](https://developer.android.com/guide/topics/manifest/supports-screens-element.html) nel file **file AndroidManifest. XML** . Questo elemento viene utilizzato per specificare le dimensioni dello schermo supportate dall'applicazione. Una schermata specificata viene considerata supportata se l'applicazione può inserire correttamente i layout per la schermata di riempimento. Utilizzando questo elemento manifesto, l'applicazione non verrà visualizzata in [*Google Play*](https://play.google.com/) per i dispositivi che non soddisfano le specifiche dello schermo. Tuttavia, l'applicazione verrà comunque eseguita nei dispositivi con schermate non supportate, ma i layout possono apparire sfocate e pixel.

Le sei schermate supportate sono dichiarate nel file **Proprietà/file AndroidManifest. XML** della soluzione:

<!-- markdownlint-disable MD001 -->

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[Manifesto ![Android](resources-for-varying-screens-images/01-android-manifest-sml.w1581.png)](resources-for-varying-screens-images/01-android-manifest.w1581.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[Manifesto ![Android](resources-for-varying-screens-images/01-android-manifest-sml.m761.png)](resources-for-varying-screens-images/01-android-manifest.m761.png#lightbox)

-----

Modificare **file AndroidManifest. XML** in modo da includere le [schermate supportate](https://developer.android.com/guide/topics/manifest/supports-screens-element.html):

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
          android:versionCode="1"
          android:versionName="1.0"
          package="HelloWorld.HelloWorld">
      <uses-sdk android:minSdkVersion="21" android:targetSdkVersion="27" />
      <supports-screens android:resizable="true"
                        android:smallScreens="true"
                        android:normalScreens="true"
                        android:largeScreens="true" />
      <application android:allowBackup="true"
                   android:icon="@mipmap/ic_launcher"
                   android:label="@string/app_name"
                   android:roundIcon="@mipmap/ic_launcher_round"
                   android:supportsRtl="true" android:theme="@style/AppTheme">
  </application>
</manifest>
```

### <a name="provide-alternate-layouts-for-different-screen-sizes"></a>Fornire layout alternativi per diverse dimensioni dello schermo

I layout alternativi consentono di personalizzare una visualizzazione per le dimensioni dello schermo specifico, modificando il posizionamento o le dimensioni degli elementi dell'interfaccia utente del componente.

A partire da API Level 13 (Android 3,2), le dimensioni dello schermo sono deprecate a favore dell'uso del qualificatore SW*N*DP. Questo nuovo qualificatore dichiara la quantità di spazio necessaria per un determinato layout. Si consiglia di usare questi qualificatori più recenti per le applicazioni che devono essere eseguite in Android 3,2 o versioni successive.

Se, ad esempio, un layout richiede almeno 700 DP della larghezza dello schermo, il layout alternativo verrà inserito in una cartella **layout-sw700dp**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Cartella layout per la larghezza dello schermo 700 DP](resources-for-varying-screens-images/03-layout-sw700dp-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

![Cartella layout per la larghezza dello schermo 700 DP](resources-for-varying-screens-images/03-layout-sw700dp-xs.png)

-----

Come riferimento, di seguito sono riportati alcuni numeri per i diversi dispositivi:

- **Telefono tipico** &ndash; 320 DP: un telefono tipico

- **5 "Tablet/" Tweener "dispositivo** &ndash; 480 DP: come la nota Samsung

- **A 7 "tablet** &ndash; 600 DP: ad esempio Barnes &amp; Noble Nook

- **10 "tablet** &ndash; 720 DP: come Motorola XOOM

Per le applicazioni destinate a livelli API fino a 12 (Android 3,1), i layout devono essere inseriti in directory che usano i qualificatori **small**/**normal**/**large**/**XLarge** come generalizzazioni delle diverse dimensioni dello schermo disponibili nella maggior parte dei dispositivi. Nell'immagine seguente, ad esempio, sono disponibili risorse alternative per le quattro diverse dimensioni dello schermo:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Risorse alternative per quattro dimensioni della schermata](resources-for-varying-screens-images/04-layout-large-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

![Risorse alternative per quattro dimensioni della schermata](resources-for-varying-screens-images/04-layout-large-xs.png)

-----

Di seguito viene illustrato un confronto tra il confronto tra i qualificatori precedenti delle dimensioni dello schermo di livello precedente all'API e i pixel indipendenti dalla densità:

- 426 DP x 320 DP è di **dimensioni ridotte**

- 470 DP x 320 DP è **normale**

- 640 DP x 480 DP è di **grandi dimensioni**

- 960 DP x 720 DP è **XLarge**

I qualificatori delle dimensioni dello schermo più recenti nel livello API 13 e superiore hanno una precedenza maggiore rispetto ai qualificatori dello schermo precedente dei livelli API 12 e inferiori.
Per le applicazioni che estendono il vecchio e il nuovo livello API, potrebbe essere necessario creare risorse alternative usando entrambi i set di qualificatori, come illustrato nello screenshot seguente:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Risorse alternative con entrambi i qualificatori](resources-for-varying-screens-images/05-both-qualifiers-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

![Risorse alternative con entrambi i qualificatori](resources-for-varying-screens-images/05-both-qualifiers-xs.png)

-----

### <a name="provide-different-bitmaps-for-different-screen-densities"></a>Fornire diverse bitmap per diverse densità dello schermo

Anche se Android ridimensiona le bitmap in modo necessario per un dispositivo, le bitmap stesse potrebbero non essere ridimensionate in modo elegante: possono diventare fuzzy o sfocate. La creazione di bitmap appropriate per la densità dello schermo consente di attenuare il problema.

Ad esempio, l'immagine seguente è un esempio di problemi di layout e aspetto che possono verificarsi quando non vengono fornite le risorse di densità-specifica.

![Schermate senza risorse di densità](resources-for-varying-screens-images/06-density-not-provided.png)

Confrontare questo valore con un layout progettato con risorse specifiche della densità:

![Screenshot con risorse specifiche della densità](resources-for-varying-screens-images/07-density-specific-resources.png)

### <a name="create-varying-density-resources-with-android-asset-studio"></a>Creare risorse di densità variabili con Android asset Studio

La creazione di queste bitmap di diverse densità può essere un po' noioso. In questo modo, Google ha creato un'utilità online che può ridurre alcuni dei tedio necessari per la creazione di queste bitmap, denominato [**Android asset Studio**](https://romannurik.github.io/AndroidAssetStudio/).

[![Android asset Studio](resources-for-varying-screens-images/08-android-asset-studio-sml.png)](resources-for-varying-screens-images/08-android-asset-studio.png#lightbox)

Questo sito Web consente di creare bitmap destinate a quattro densità di schermate comuni fornendo un'unica immagine. Android asset Studio creerà quindi le bitmap con alcune personalizzazioni e quindi le consentirà di scaricarle come file zip.

## <a name="tips-for-multiple-screens"></a>Suggerimenti per più schermate

Android viene eseguito su un numero sconcertante di dispositivi e la combinazione di dimensioni dello schermo e densità dello schermo può sembrare travolgente. I suggerimenti seguenti consentono di ridurre al minimo il lavoro necessario per supportare diversi dispositivi:

- **È possibile progettare e sviluppare solo per gli elementi necessari** &ndash; sono presenti molti dispositivi diversi, ma alcuni sono presenti in rari fattori che possono richiedere un lavoro significativo per la progettazione e lo sviluppo di. Il dashboard [**Dimensioni schermo e densità**](https://developer.android.com/resources/dashboard/screens.html) è una pagina offerta da Google che fornisce i dati sulla suddivisione delle dimensioni dello schermo o della matrice di densità dello schermo. Questa suddivisione fornisce informazioni dettagliate su come eseguire attività di sviluppo per supportare le schermate.

- **Usare DPS anziché pixel** -i pixel diventano fastidiosi quando cambiano la densità dello schermo. Non impostare come hardcoded i valori dei pixel. Evitare i pixel a favore di DP (pixel indipendenti dalla densità).

- **Evitare** [AbsoluteLayout](xref:Android.Widget.AbsoluteLayout)
  **laddove possibile** &ndash; è deprecato in API Level 3 (Android 1,5) e comporterà un layout fragile. Non deve essere usato. Provare invece a usare widget di layout più flessibili, ad esempio [**LinearLayout**](xref:Android.Widget.LinearLayout), [**sul relativelayout**](xref:Android.Widget.RelativeLayout)o il nuovo [**GridLayout**](xref:Android.Widget.GridLayout).

- **Selezionare un orientamento del layout come predefinito** &ndash; ad esempio, anziché fornire le risorse alternative **layout-Land** e **layout-Port**, inserire le risorse per il panorama orizzontale nel **layout**e le risorse per Portrait in **layout-Port**.

- **Usare LayoutParams per l'altezza e la larghezza** : quando si definiscono gli elementi dell'interfaccia utente in un file di layout XML, un'applicazione Android che usa i valori **wrap_content** e **fill_parent** avrà maggiore successo, assicurerà una corretta ricerca tra dispositivi diversi rispetto all'uso di unità pixel o indipendenti dalla densità. Questi valori di dimensione fanno sì che Android ridimensiona le risorse bitmap nel modo appropriato. Per questo stesso motivo, le unità indipendenti dalla densità sono particolarmente riservate quando si specificano i margini e la spaziatura interna degli elementi dell'interfaccia utente.

## <a name="testing-multiple-screens"></a>Test di più schermate

Un'applicazione Android deve essere testata rispetto a tutte le configurazioni che saranno supportate. Idealmente, i dispositivi devono essere testati sui dispositivi effettivi, ma in molti casi questa operazione non è possibile o pratica.
In questo caso, sarà utile l'uso della configurazione dell'emulatore e dei dispositivi virtuali Android per ogni configurazione del dispositivo.

Il Android SDK fornisce alcune interfacce dell'emulatore che possono essere usate per creare AVD replica le dimensioni, la densità e la risoluzione di molti dispositivi.
Molti fornitori di hardware forniscono in modo analogo le interfacce per i dispositivi.

Un'altra opzione consiste nell'usare i servizi di un servizio di test di terze parti.
Questi servizi utilizzeranno un APK, lo eseguiranno su molti dispositivi diversi e quindi forniranno un feedback sul funzionamento dell'applicazione.
