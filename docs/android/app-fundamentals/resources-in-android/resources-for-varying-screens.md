---
title: Creazione di risorse per schermi diversi
ms.prod: xamarin
ms.assetid: 3D17DE45-115C-7192-5685-44F8EEE07DCC
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/28/2018
ms.openlocfilehash: 63cbe556783ffe22512ff5312817d522120bd15e
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2019
ms.locfileid: "57670571"
---
# <a name="creating-resources-for-varying-screens"></a>Creazione di risorse per schermi diversi

Android stessa viene eseguito su dispositivi diversi, ognuno dei quali dispone di un'ampia gamma di soluzioni, le dimensioni dello schermo e densità dello schermo. Android eseguirà la scalabilità e il ridimensionamento per rendere l'applicazione di funzionare in questi dispositivi, ma ciò potrebbe comportare un'esperienza utente ottimale. Ad esempio, le immagini possono essere visualizzate sfocate o non può essere posizionati nel modo previsto in una vista.


## <a name="concepts"></a>Concetti

Alcuni termini e concetti sono importanti per comprendere per supportare più schermate.

- **Dimensioni dello schermo** &ndash; la quantità di spazio fisico per la visualizzazione dell'applicazione

- **Schermata di densità** &ndash; il numero di pixel in qualsiasi area specificata sullo schermo. L'unità di misura tipico è punti per pollice (dpi).

- **Risoluzione** &ndash; il numero totale di pixel sullo schermo. Quando si sviluppano applicazioni, la risoluzione non è tanto importante quanto densità e dimensioni dello schermo.

- **Pixel indipendenti dalla densità (dp)** &ndash; un'unità di misura virtuale per consentire indipendente della densità del layout da progettare. Questa formula viene utilizzata per convertire dp in pixel sullo schermo:

    px &equals; dp &times; dpi &divide; 160

- **Orientamento** &ndash; l'orientamento dello schermo è considerato orizzontale quando è maggiore dell'altezza. Al contrario, indica l'orientamento verticale quando lo schermo è alto alla larghezza. L'orientamento è possibile modificare la durata di un'applicazione come l'utente ruota il dispositivo.

Si noti che i primi tre di questi concetti sono correlati tra &ndash; un aumento della risoluzione senza aumentando la densità aumenterà le dimensioni dello schermo. Se entrambe le densità e la risoluzione sono aumentate, tuttavia le dimensioni dello schermo possono rimanere invariata. Questa relazione tra dimensioni dello schermo, la densità e risoluzione complicare rapidamente supporto dello schermo.

Per aiutare a gestire questa complessità, preferisce usare il framework di Android *pixel indipendenti dalla densità (dp)* per layout di schermata. Usando pixel indipendenti dalla densità, elementi dell'interfaccia utente verranno visualizzato per l'utente abbia le stesse dimensioni fisiche nelle schermate con densità diverse.


## <a name="supporting-various-screen-sizes-and-densities"></a>Supporto di diverse dimensioni dello schermo e densità

Android gestisce la maggior parte del lavoro per eseguire il rendering i layout in modo corretto per ogni configurazione della schermata. Esistono tuttavia alcune azioni che possono essere eseguite come supporto del sistema.

L'uso di pixel indipendenti dalla densità invece di pixel effettivi nei layout è sufficiente nella maggior parte dei casi per garantire l'indipendenza di densità.
Android ridimensionerà la drawable in fase di esecuzione per le dimensioni appropriate.
Tuttavia, è possibile che la scalabilità genererà le bitmap venga visualizzato sfocato. Per risolvere questo problema, fornire risorse alternative per la densità diverse. Quando i dispositivi di progettazione per densità dello schermo e risoluzioni più, si dimostreranno più semplice per iniziare la risoluzione superiore o densità di immagini e quindi ridurle.


### <a name="declare-the-supported-screen-size"></a>Dichiarare le dimensioni dello schermo supportate

Dichiarare le dimensioni dello schermo assicura che solo i dispositivi supportati possono scaricare l'applicazione. Questa operazione viene eseguita impostando il [supporta le schermate](https://developer.android.com/guide/topics/manifest/supports-screens-element.html) elemento il **androidmanifest. XML** file. Questo elemento viene usato per specificare le dimensioni dello schermo sono supportate dall'applicazione. Una determinata schermata è considerata supportato se l'applicazione può posizionare in modo corretto il layout a schermo. Tramite questo elemento del manifesto, l'applicazione non appariranno nella [ *Google Play* ](https://play.google.com/) per i dispositivi che non soddisfano le specifiche dello schermo. Tuttavia, l'applicazione verrà comunque eseguito sui dispositivi con schermate non supportate, ma il layout potrebbe sembrare sfocati e disturbate.

Schermata supportata sixes vengono dichiarati nel **Properites/AndroidManifest.xml** file della soluzione:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Android Manifest](resources-for-varying-screens-images/01-android-manifest-sml.w1581.png)](resources-for-varying-screens-images/01-android-manifest.w1581.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![Android Manifest](resources-for-varying-screens-images/01-android-manifest-sml.m761.png)](resources-for-varying-screens-images/01-android-manifest.m761.png#lightbox)

-----

Modificare **androidmanifest. XML** includere [supporta schermate](https://developer.android.com/guide/topics/manifest/supports-screens-element.html):

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

### <a name="provide-alternate-layouts-for-different-screen-sizes"></a>Specificare layout alternativi per diverse dimensioni dello schermo


Layout alternativi consentono di personalizzare una vista per una dimensione dello schermo specifico, modifica il posizionamento o le dimensioni degli elementi dell'interfaccia componente.

A partire da 13 a livello di API (Android 3.2), le dimensioni dello schermo sono deprecate a favore dell'uso del software*N*il qualificatore del punto di distribuzione. Questo nuovo qualificatore dichiara che richiede la quantità di spazio su un layout specifico. È consigliabile che le applicazioni che devono essere eseguiti su Android 3.2 o versioni successive devono usare questi qualificatori più recente.

Se, ad esempio, un layout richiesto un minimo 700 dp di larghezza dello schermo, il layout alternativo verrà inserito in una cartella **layout sw700dp**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Cartella di layout per la larghezza della schermata 700 dp](resources-for-varying-screens-images/03-layout-sw700dp-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

![Cartella di layout per la larghezza della schermata 700 dp](resources-for-varying-screens-images/03-layout-sw700dp-xs.png)

-----


Come indicazione generale, ecco alcuni numeri per vari dispositivi:

- **Telefono tipico** &ndash; 320 dp: un telefono tipico

- **Un tablet 5"/"tweener"periferica** &ndash; 480 dp: ad esempio la nota di Samsung

- **Un tablet 7"** &ndash; 600 dp: ad esempio il Barnes &amp; Nook Noble

- **10" tablet** &ndash; 720 dp: ad esempio il Motorola Xoom

Per le applicazioni che fino a 12 (Android 3.1), i livelli di API di destinazione i layout dovrebbero trovarsi nella directory che utilizzano i qualificatori **piccola**/**normale**/**grandi dimensioni**  / **: molto grandi** come generalizzazioni delle varie dimensioni dello schermo che sono disponibili nella maggior parte dei dispositivi. Ad esempio, nell'immagine seguente, esistono risorse alternative per i quattro diverse dimensioni dello schermo:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Risorse alternative per quattro dimensioni dello schermo](resources-for-varying-screens-images/04-layout-large-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

![Risorse alternative per quattro dimensioni dello schermo](resources-for-varying-screens-images/04-layout-large-xs.png)

-----

Di seguito è riportato un confronto delle differenze tra i qualificatori di dimensioni dello schermo di livello 13 pre-API precedenti su pixel indipendenti dalla densità:

- 426 dp x 320 dp è **small**

- 470 dp x 320 dp è **normale**

- 640 dp x 480 dp è **grandi dimensioni**

- 960 dp x 720 dp è **: molto grandi**

La schermata più recente dimensioni qualificatori nel livello API 13 e backup hanno una precedenza maggiore i qualificatori schermata precedente dei livelli API 12 e versioni precedenti.
Per le applicazioni che bloccheranno il vecchio e i nuovi livelli di API, è necessario creare risorse alternative con entrambi i set di qualificatori, come illustrato nello screenshot seguente:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Risorse alternative con entrambi i qualificatori](resources-for-varying-screens-images/05-both-qualifiers-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

![Risorse alternative con entrambi i qualificatori](resources-for-varying-screens-images/05-both-qualifiers-xs.png)

-----



### <a name="provide-different-bitmaps-for-different-screen-densities"></a>Fornire immagini bitmap diverse per densità dello schermo diverse

Sebbene Android ridimensionerà immagini bitmap in base alle esigenze per un dispositivo, le bitmap a se stessi non possono modo elegante scalare verso l'alto o verso il basso: potrebbe diventano sfocati o fuzzy. Fornire le bitmap appropriate per la densità dello schermo verrà attenuare questo problema.

Ad esempio, l'immagine seguente è riportato un esempio di layout e l'aspetto dei problemi che possono verificarsi quando densità-specificare le risorse non disponibili.

![Screenshot senza risorse densità](resources-for-varying-screens-images/06-density-not-provided.png)

Confrontare questo con un layout in cui è stato progettato per le risorse specifiche delle densità:

![Schermate con densità specifiche risorse](resources-for-varying-screens-images/07-density-specific-resources.png)


### <a name="create-varying-density-resources-with-android-asset-studio"></a>Creare risorse di densità diverse con Android Asset Studio

La creazione di tali bitmap di densità diverse possa essere alquanto noiosa. Di conseguenza, Google ha creato un'utilità online che consentono di ridurre in parte la noia coinvolto con la creazione di tali bitmap denominato il [ **Android Asset Studio**](https://romannurik.github.io/AndroidAssetStudio/).

[![Android Asset Studio](resources-for-varying-screens-images/08-android-asset-studio-sml.png)](resources-for-varying-screens-images/08-android-asset-studio.png#lightbox)

Questo sito Web consentirà la creazione di bitmap destinate le quattro densità dello schermo più comuni, fornendo un'immagine. Android Asset Studio verrà quindi creare le bitmap con alcune personalizzazioni e quindi consentire loro di essere scaricati come file zip.


## <a name="tips-for-multiple-screens"></a>Suggerimenti per più schermate

Android viene eseguito su un elevatissimo numero di dispositivi e la combinazione di dimensioni dello schermo e densità dello schermo può sembrare eccessiva. I suggerimenti seguenti possono aiutare a ridurre al minimo lo sforzo necessario per supportare vari tipi di dispositivi:

- **Solo progettare e sviluppare adatto alle proprie necessità** &ndash; sono disponibili molti dispositivi diversi, ma alcuni esistono rari fattori di forma che possono richiedere una notevole impegno per progettare e sviluppare per. Il [ **dimensioni dello schermo e densità** ](https://developer.android.com/resources/dashboard/screens.html) dashboard è una pagina fornita da Google che fornisce dati sulla scomposizione della matrice densità dello schermo/schermo. Questa suddivisione fornisce informazioni dettagliate sull'attività di sviluppo al supporto di schermate.

- **Usare punti di distribuzione anziché pixel** -pixel diventano problematico quando vengono modificati densità dello schermo. Non impostare come hardcoded i valori dei pixel. Evitare di pixel a favore di punto di distribuzione (in pixel indipendenti dalla densità).

- **Evitare** [AbsoluteLayout](https://developer.xamarin.com/api/type/Android.Widget.AbsoluteLayout/)
  **ovunque possibile** &ndash; il è deprecata in API di livello 3 (1,5 Android) e provocherà nei layout fragili. Non deve essere utilizzato. Provare invece a usare i widget di layout più flessibile, ad esempio [ **LinearLayout**](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/), [ **RelativeLayout**](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/), o al nuovo [ **GridLayout**](https://developer.xamarin.com/api/type/Android.Widget.GridLayout/).

- **Selezionare un orientamento del layout come il valore predefinito** &ndash; ad esempio, invece che fornisce le risorse alternative **layout land** e **layout-port**, inserire le risorse per nel Landscape **layout**e le risorse di verticale in **layout-port**.

- **Usare LayoutParams per altezza e larghezza** : quando si definiscono gli elementi dell'interfaccia utente in un file di layout XML, un'applicazione Android usando il **wrap_content** e **fill_parent** valori avranno successo maggiore assicurare un aspetto corretto su dispositivi diversi rispetto all'uso di pixel o unità indipendenti dalla densità. Questi valori della dimensione causano Android per ridimensionare le risorse di bitmap come appropriato. Per questo stesso motivo, sono meglio riservate unità indipendenti dalla densità per i casi che specifica i margini e spaziatura elementi dell'interfaccia utente.


## <a name="testing-multiple-screens"></a>Test più schermate

Un'applicazione Android deve essere testata con tutte le configurazioni che saranno supportate. In teoria i dispositivi devono essere testati nei dispositivi effettivi stessi ma in molti casi ciò non è possibile o pratica.
In questo caso, sarà utile l'uso dell'emulatore e il programma di installazione di dispositivi virtuali Android per ogni configurazione del dispositivo.

Android SDK fornisce alcuni emulatore interfacce possono essere utilizzate per creare Avd replicherà le dimensioni, densità e la risoluzione di molti dispositivi.
Allo stesso modo molti dei fornitori di hardware offrono interfacce per i dispositivi.

Un'altra opzione consiste nell'usare i servizi di un servizio di test di terze parti.
Questi servizi accetta un file APK, eseguirlo su dispositivi diversi e quindi fornire commenti e suggerimenti come l'applicazione ha funzionata.
