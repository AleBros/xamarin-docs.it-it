---
title: Creazione di risorse per le diverse schermate
ms.topic: article
ms.prod: xamarin
ms.assetid: 3D17DE45-115C-7192-5685-44F8EEE07DCC
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 10/12/2017
ms.openlocfilehash: aeb7115e3c7521f6679e8802eb759d7e56ba1cfe
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="creating-resources-for-varying-screens"></a>Creazione di risorse per le diverse schermate

Android stesso viene eseguito su dispositivi diversi, ognuno dei quali dispone di una vasta gamma di densità di schermata risoluzioni e dimensioni dello schermo. Android eseguirà il ridimensionamento e di ridimensionamento per rendere l'applicazione di funzionare in questi dispositivi, ma potrebbe verificarsi un'esperienza utente ottimale. Ad esempio, le immagini potrebbero apparire sfocate, le immagini possono occupare spazio sullo schermo troppa (o non è sufficiente) che determina la posizione degli elementi dell'interfaccia utente nel layout verrà si sovrappongono o troppo distanti.

<a name="Concepts" />

## <a name="concepts"></a>Concetti

Alcuni concetti e termini sono importanti per comprendere come per supportare più schermate.

- **La dimensione dello schermo** &ndash; la quantità di spazio fisico per la visualizzazione dell'applicazione

- **Schermata di densità** &ndash; il numero di pixel in qualsiasi area specificata sullo schermo. L'unità di misura tipica è punti per pollice (dpi).

- **Risoluzione** &ndash; il numero totale di pixel sullo schermo. Quando si sviluppano applicazioni, la risoluzione non è importante quanto densità e dimensioni dello schermo.

- **Indipendente dalla densità di pixel (dp)** &ndash; virtuale unità di misura per consentire di layout per progettare indipendente di densità. Per convertire dp in pixel sullo schermo viene utilizzata la formula seguente:

    px &equals; dp &times; dpi &divide; 160

- **Orientamento** &ndash; l'orientamento dello schermo è considerato orizzontale quando è maggiore dell'altezza. Al contrario, l'orientamento verticale è la schermata di altezza maggiore della larghezza. L'orientamento cambiare nel corso della durata di un'applicazione utente ruota il dispositivo.

Si noti che le prime tre di questi concetti sono correlate tra &ndash; un aumento della risoluzione senza aumentare la densità aumenterà le dimensioni dello schermo. Se per aumentare la densità e risoluzione, tuttavia le dimensioni dello schermo possono rimarranno invariate. La relazione tra dimensioni dello schermo, densità e la risoluzione complicare il supporto della molto rapidamente.

Per gestire questa complessità, il framework Android preferita utilizzare *independent densità di pixel (dp)* per layout dello schermo. Usando pixel indipendenti densità, elementi dell'interfaccia utente verranno visualizzato per l'utente abbia le stesse dimensioni fisiche nelle schermate con densità diverse.

<a name="Supporting_Various_Screen_Sizes_and_Densities" />

## <a name="supporting-various-screen-sizes-and-densities"></a>Supporto di diverse dimensioni dello schermo e densità

Android gestisce la maggior parte del lavoro per eseguire il rendering i layout in modo corretto per ogni configurazione dello schermo. Tuttavia, esistono alcune azioni che possono essere eseguite per aiutare il sistema.

L'utilizzo di densità independent pixel anziché pixel effettivi nei layout è sufficiente nella maggior parte dei casi per garantire l'indipendenza di densità.
Android verrà ridimensionato il drawables in fase di esecuzione per le dimensioni appropriate.
Tuttavia, è possibile che questa scalabilità comportano la bitmap di approssimazione. Per evitare questo problema, potrebbe essere necessario fornire le risorse alternative per le diverse densità. Durante la progettazione di dispositivi per più risoluzioni e densità schermata risulta più semplice per iniziare con la risoluzione più alta o densità immagini e quindi scalare verso il basso. Ciò impedirà qualsiasi distorsione che può determinare il ridimensionamento o sfocatura.

<a name="Declare_the_Screen_Size_the_Application_Supports" />

### <a name="declare-the-screen-size-the-application-supports"></a>Dichiarare le dimensioni dello schermo supportati nell'applicazione

La dichiarazione delle dimensioni dello schermo garantisce che solo i dispositivi supportati possono scaricare l'applicazione. Questa operazione viene eseguita impostando il [supporta schermate](http://developer.android.com/guide/topics/manifest/supports-screens-element.html) elemento il **AndroidManifest.xml** file. Questo elemento viene usato per specificare le dimensioni dello schermo sono supportate dall'applicazione. È considerata supportato se l'applicazione può correttamente una schermata specificata del layout a schermo. Tramite questo elemento del manifesto, l'applicazione non appariranno nella [ *Google Play* ](https://play.google.com/) per i dispositivi che non soddisfano le specifiche dello schermo. Tuttavia, l'applicazione verrà comunque eseguito sui dispositivi con schermate non supportate, ma il layout potrebbe apparire sfocati e livello.

A tale scopo in xamarin, è necessario aggiungere prima un **AndroidManifest.xml** file al progetto se non esiste già:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Manifesto Android](resources-for-varying-screens-images/01-android-manifest-vs-sml.png)](resources-for-varying-screens-images/01-android-manifest-vs.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Manifesto Android](resources-for-varying-screens-images/01-android-manifest-xs-sml.png)](resources-for-varying-screens-images/01-android-manifest-xs.png)

-----


**AndroidManifest.xml** viene aggiunto per il **proprietà** directory. Il file viene quindi modificato per includere [supporta schermate](http://developer.android.com/guide/topics/manifest/supports-screens-element.html):

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![L'aggiunta di schermate di supporto](resources-for-varying-screens-images/02-adding-supports-screens-vs-sml.png)](resources-for-varying-screens-images/02-adding-supports-screens-vs.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![L'aggiunta di schermate di supporto](resources-for-varying-screens-images/02-adding-supports-screens-xs-sml.png)](resources-for-varying-screens-images/02-adding-supports-screens-xs.png)

-----


<a name="Provide_Alternate_Layouts_for_Different_Screen_Sizes" />

### <a name="provide-alternate-layouts-for-different-screen-sizes"></a>Specificare layout alternativi per dimensioni dello schermo diverse

Sebbene Android verrà ridimensionata in base a dimensioni dello schermo, questo potrebbe non essere sufficiente, in alcuni casi. Potrebbe essere auspicabile per aumentare le dimensioni di alcuni elementi dell'interfaccia utente in una schermata di dimensioni maggiore, o per modificare il posizionamento di elementi dell'interfaccia utente per schermi più piccoli.

A partire dalla 13 livello API (Android 3.2), le dimensioni dello schermo sono deprecate a favore dell'utilizzo di sw*N*qualificatore del punto di distribuzione. Questo nuovo qualificatore dichiara che richiede la quantità di spazio su un layout specifico. È consigliabile che le applicazioni che devono essere eseguiti su Android 3.2 o versioni successive devono utilizzare questi qualificatori più recenti.

Ad esempio, se un layout è richiesto un 700dp minima di larghezza dello schermo, il layout alternativo verrebbero in una cartella **layout sw700dp**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Cartella layout per la larghezza dello schermo 700dp](resources-for-varying-screens-images/03-layout-sw700dp-vs.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

![Cartella layout per la larghezza dello schermo 700dp](resources-for-varying-screens-images/03-layout-sw700dp-xs.png)

-----


Come indicazione generale, ecco alcuni numeri per vari dispositivi:

- **Telefono tipico** &ndash; 320dp: un telefono tipico

- **Un tablet 5"/"tweener"dispositivo** &ndash; 480dp: ad esempio la nota Samsung

- **Un tablet 7"** &ndash; 600dp: ad esempio il Barnes &amp; Nook Noble

- **Un tablet 10"** &ndash; 720dp: ad esempio il Motorola Xoom

Per le applicazioni che i livelli di API di destinazione fino a 12 (Android 3.1), i layout devono andare nelle directory che utilizzano i qualificatori **small**/**normale**/**grandi dimensioni**  / **xlarge** come generalizzazioni di diverse dimensioni dello schermo che sono disponibili nella maggior parte dei dispositivi. Ad esempio, nell'immagine seguente, esistono risorse alternative per le quattro dimensioni dello schermo diverse:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Risorse alternative quattro dimensioni dello schermo](resources-for-varying-screens-images/04-layout-large-vs.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

![Risorse alternative quattro dimensioni dello schermo](resources-for-varying-screens-images/04-layout-large-xs.png)

-----

Di seguito è riportato un confronto di come i qualificatori di dimensioni dello schermo di precedenti pre-API livello 13 vengono confrontate con densità independent pixel:

- è 426dp x 320dp **small**

- è 470dp x 320dp **normale**

- è 640dp x 480dp **grandi dimensioni**

- è 960dp x 720dp **xlarge**

La schermata più recente dimensioni qualificatori nel livello API 13 e backup hanno una precedenza maggiore i qualificatori schermata precedente dei livelli di API 12 e inferiore.
Per le applicazioni che utilizzerà il vecchio e i nuovi livelli di API, è necessario creare le risorse alternative utilizzando entrambi i set di qualificatori, come illustrato nella schermata seguente:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Risorse alternative con entrambi i qualificatori](resources-for-varying-screens-images/05-both-qualifiers-vs.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

![Risorse alternative con entrambi i qualificatori](resources-for-varying-screens-images/05-both-qualifiers-xs.png)

-----


<a name="Provide_Different_Bitmaps_for_Different_Screen_Densities" />

### <a name="provide-different-bitmaps-for-different-screen-densities"></a>Prevedere bitmap diverse densità dello schermo diverse

Anche se Android ridimensionerà bitmap in base alle esigenze per un dispositivo, le bitmap se stessi non possono modo elegante scalabilità verso l'alto o verso il basso: diventi sfocato o fuzzy. Fornendo una bitmap appropriata per la densità della schermata sarà possibile limitare il problema.

Ad esempio, l'immagine seguente è riportato un esempio di problemi di layout e l'aspetto che possono verificarsi quando densità specifica le risorse non disponibili.

![Schermate senza risorse densità](resources-for-varying-screens-images/06-density-not-provided.png)

Confrontare un layout in cui è stato progettato con risorse specifiche di densità:

![Schermate con densità specifiche risorse](resources-for-varying-screens-images/07-density-specific-resources.png)

<a name="Create_Varying_Density_Resources_with_Android_Asset_Studio" />

### <a name="create-varying-density-resources-with-android-asset-studio"></a>Creare risorse densità diverse con Asset Android Studio

La creazione di queste mappe di bit delle diverse densità può risultare un po' difficile. Di conseguenza, Google ha creato un'utilità online che può ridurre alcune la necessità di necessarie per la creazione di tali bitmap chiamato il [ **Android Studio Asset**](https://romannurik.github.io/AndroidAssetStudio/).

[![Asset Android Studio](resources-for-varying-screens-images/08-android-asset-studio-sml.png)](resources-for-varying-screens-images/08-android-asset-studio.png)

Consente di questo sito Web con la creazione di bitmap destinate le quattro densità schermata comuni, fornendo un'immagine. Android Asset verrà quindi creare le bitmap con alcune personalizzazioni e quindi consentire il download come file zip.

<a name="Tips_for_Multiple_Screens" />

## <a name="tips-for-multiple-screens"></a>Suggerimenti per più schermate

Android viene eseguito su un elevatissimo numero di dispositivi e la combinazione di densità dello schermo e dimensioni dello schermo può sembrare molto complesse. I suggerimenti seguenti possono aiutare a ridurre al minimo lo sforzo necessario per supportare vari dispositivi:

- **Solo progettazione e sviluppo per i quali è necessario** &ndash; esistono molti di voi diversi dispositivi, ma alcuni esistono in fattori di forma rari che potrebbero richiedere un notevole sforzo per progettare e sviluppare per. Il [ **densità e dimensioni dello schermo** ](http://developer.android.com/resources/dashboard/screens.html) dashboard è una pagina fornita da Google che fornisce dati sulla scomposizione della matrice di densità di dimensioni/schermata dello schermo. Questa suddivisione fornisce informazioni dettagliate sull'attività di sviluppo sul supporto di schermate.

- **Utilizzo dei punti di distribuzione anziché di pixel** -pixel diventano problematiche come modifiche di densità dello schermo. Non impostare come hardcoded i valori pixel. Evitare di pixel a favore di punto di distribuzione (densità independent pixel).

- **Evitare** [AbsoluteLayout](https://developer.xamarin.com/api/type/Android.Widget.AbsoluteLayout/)
  **ovunque possibili** &ndash; è deprecato in livello API 3 (Android 1.5) e comporterà fragile layout. Non deve essere utilizzato. In alternativa, provare a utilizzare widget layout più flessibile, ad esempio [ **LinearLayout**](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/), [ **RelativeLayout**](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/), o il nuovo [ **GridLayout**](https://developer.xamarin.com/api/type/Android.Widget.GridLayout/).

- **Selezionare un orientamento layout come predefinita** &ndash; , ad esempio, invece di fornire le risorse alternative **layout terreni** e **layout porta**, inserire le risorse per orizzontale in **layout**e le risorse per verticale in **layout porta**.

- **Utilizzare LayoutParams per altezza e larghezza** - quando si definiscono gli elementi dell'interfaccia utente in un file di layout XML, un'applicazione Android utilizzando il **wrap_content** e **fill_parent** valori avrà esito positivo più verificare un aspetto corretto in diversi dispositivi rispetto all'utilizzo di unità indipendenti pixel o densità. Tali valori causano Android a scalabilità delle risorse di bitmap come appropriato. Per questo motivo, stesso unità indipendenti dal densità sono meglio riservate quando specificando i margini e spaziatura interna di elementi dell'interfaccia utente.

<a name="Testing_Multiple_Screens" />

## <a name="testing-multiple-screens"></a>Test più schermate

Un'applicazione Android deve essere testato tutte le configurazioni che saranno supportate. Idealmente i dispositivi devono essere verificati nei dispositivi effettivi stessi ma in molti casi non è possibile o fattibile.
In questo caso sarà utile l'uso dell'emulatore e il programma di installazione di dispositivi virtuali Android per ogni configurazione del dispositivo.

Android SDK fornisce alcuni emulatore interfacce possono essere utilizzate per creare del AVD verrà replicate le dimensioni, densità e la risoluzione di molti dispositivi.
Molti fornitori di hardware forniscono allo stesso modo interfacce per i propri dispositivi.

Un'altra opzione consiste nell'utilizzare i servizi di un servizio di test di terze parti.
Questi servizi accetta un file APK, viene eseguito in dispositivi diversi e quindi fornire commenti e suggerimenti del funzionamento dell'applicazione.
