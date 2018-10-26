---
title: Tipi di carattere
ms.prod: xamarin
ms.assetid: 3F543FC5-FDED-47F8-8D2C-481FCC98BFDA
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 09/0/2018
ms.openlocfilehash: fb0e1c42efc6bb51dcd899c7e6017007a82d5c7f
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105935"
---
# <a name="fonts"></a>Tipi di carattere

## <a name="overview"></a>Panoramica

A partire da 26 a livello di API, i tipi di carattere da trattare come risorse, come un layout consente di Android SDK o drawable. Il [Android NuGet 26 della libreria di supporto](https://www.nuget.org/packages/Xamarin.Android.Support.Compat/26.1.0.1) verrà esegue il backporting nuovo tipo di carattere dell'API per le app destinate a livello API 14 o successivo.

Dopo aver destinata ad API 26 o installare la libreria di supporto Android v26, esistono due modi per usare i tipi di carattere in un'applicazione Android:

1. **Creare un pacchetto il tipo di carattere come una risorsa di Android** &ndash; ciò garantisce che il tipo di carattere è sempre disponibile per l'applicazione, ma aumenterà le dimensioni dell'APK. 
2. **Scaricare i tipi di carattere** &ndash; Android supporta anche il download di un tipo di carattere da un _provider del tipo di carattere_. Il provider del tipo di carattere controlla se il tipo di carattere è già presente nel dispositivo. Se necessario, il tipo di carattere verrà scaricato e memorizzato nella cache del dispositivo. Questo tipo di carattere può essere condivisi tra più applicazioni.

I tipi di carattere simile (o un tipo di carattere che può avere diversi stili diversi) possa essere raggruppato in _famiglie di caratteri_. Ciò consente agli sviluppatori di specificare determinati attributi del tipo di carattere, ad esempio di peso relativo, e Android selezionerà automaticamente il tipo di carattere appropriato dalla famiglia di caratteri.

La libreria di supporto Android v26 verrà esegue il backporting supporto per i tipi di carattere a livello di API 26. Quando la destinazione i livelli API precedenti, è necessario dichiarare la `app` spazio dei nomi XML e denominare i vari attributi del tipo di carattere usando la `android:` dello spazio dei nomi e il `app:` dello spazio dei nomi. Se solo il `android:` viene usato lo spazio dei nomi, quindi i tipi di carattere non saranno visualizzati i dispositivi che eseguono livello API 25 o inferiore. Ad esempio, questo frammento di codice XML dichiara una nuova [ _famiglia di caratteri_ ](#font_families) risorsa che funzionerà nel livello API 14 e versioni successiva:

```xml
<?xml version="1.0" encoding="utf-8"?>
<font-family 
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto">

     <font  android:font="@font/sourcesanspro_regular" 
            android:fontStyle="normal" 
            android:fontWeight="400"
            app:font="@font/sourcesanspro_regular" 
            app:fontStyle="normal" 
            app:fontWeight="400" />

</font-family>
```

Purché i tipi di carattere vengono forniti da un'applicazione Android in modo corretto, può essere applicati a un widget dell'interfaccia utente impostando il [ `fontFamily` attributo](https://developer.android.com/reference/android/widget/TextView.html#attr_android:fontFamily). Ad esempio, il frammento di codice seguente viene illustrato come visualizzare un tipo di carattere in un controllo TextView:

```xml
<TextView
    android:text="The quick brown fox jumped over the lazy dog."
    android:fontFamily="@font/sourcesanspro_regular"
    app:fontFamily="@font/sourcesanspro_regular"
    android:textAppearance="?android:attr/textAppearanceLarge"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" />
```

Questa Guida verrà innanzitutto illustrato come utilizzare i tipi di carattere come una risorsa di Android e quindi passare a illustrare come scaricare i tipi di carattere in fase di esecuzione.

## <a name="fonts-as-a-resource"></a>Tipi di carattere come una risorsa

Creazione del pacchetto di un tipo di carattere in un file APK Android assicura che sia sempre disponibile per l'applicazione. Un file del tipo di carattere (entrambi una. TTF o un oggetto. File OTF) viene aggiunto a un'applicazione xamarin. Android come qualsiasi altra risorsa, copiando i file in una sottodirectory il **risorse** nella cartella di un progetto xamarin. Android. Le risorse di tipi di carattere vengono mantenute in un **font** sottodirectory delle **risorse** cartella del progetto. 

> [!NOTE]
> I tipi di carattere deve avere una **Build Action** dei **AndroidResource** o verranno non inserite nell'APK finale. L'azione di compilazione deve essere impostata automaticamente dall'IDE.

Quando sono presenti molti file di caratteri simili (ad esempio, lo stesso carattere con pesi diversi o stili) è possibile raggrupparle in una famiglia di caratteri.

<a name="font_families" />

### <a name="font-families"></a>Famiglie di caratteri

Una famiglia di caratteri è un set di tipi di carattere dotati di stili e pesi diversi. Ad esempio, potrebbero essere presenti file del tipo di carattere per i tipi di carattere grassetto o corsivo. La famiglia di caratteri è definita da `font` elementi in un file XML che viene mantenuta nel **risorse/tipo di carattere** directory. Ogni famiglia di caratteri deve avere i propri file XML.

Per creare una famiglia di caratteri, aggiungere prima tutti i tipi di carattere per il **risorse/tipo di carattere** cartella. Creare quindi un nuovo file XML nella cartella del tipo di carattere per la famiglia di caratteri. Il nome del file XML non ha alcuna relazione o affinità per i tipi di carattere viene fatto riferimento. il file di risorse può essere qualsiasi nome di file di risorse Android legali. Questo file XML avranno una radice `font-family` elemento che contiene uno o più `font` elementi. Ogni `font` elemento dichiara gli attributi di un tipo di carattere.

Il codice XML seguente è riportato un esempio di una famiglia di caratteri per il _origini nomi alternativi del soggetto Pro_ carattere che definisce molti pesi di diverso tipo di carattere. Il valore viene salvato come file nei **risorse/tipo di carattere** cartella denominata **sourcesanspro.xml**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<font-family xmlns:android="http://schemas.android.com/apk/res/android"
             xmlns:app="http://schemas.android.com/apk/res-auto">
    <font android:font="@font/sourcesanspro_regular" 
          android:fontStyle="normal" 
          android:fontWeight="400"
          app:font="@font/sourcesanspro_regular" 
          app:fontStyle="normal" 
          app:fontWeight="400" />
    <font android:font="@font/sourcesanspro_bold" 
          android:fontStyle="normal" 
          android:fontWeight="800" 
          app:font="@font/sourcesanspro_bold" 
          app:fontStyle="normal" 
          app:fontWeight="800" />
    <font android:font="@font/sourcesanspro_italic" 
          android:fontStyle="italic" 
          android:fontWeight="400"
          app:font="@font/sourcesanspro_italic" 
          app:fontStyle="italic" 
          app:fontWeight="400" />
</font-family>
```

Il `fontStyle` attributo è possibili due valori:

* **Normal** &ndash; un tipo di carattere normale
* **corsivo** &ndash; un carattere corsivo

Il `fontWeight` attributo corrisponde al foglio di stile CSS `font-weight` attributo e si intende lo spessore del tipo di carattere. Si tratta di un valore compreso nell'intervallo da 100 a 900. Nell'elenco seguente descrive i valori di peso comuni del tipo di carattere e il relativo nome:

* **Thin** &ndash; 100
* **Estremamente leggero** &ndash; 200
* **Light** &ndash; 300
* **Normal** &ndash; 400
* **Medium** &ndash; 500
* **Semi Bold** &ndash; 600
* **Bold** &ndash; 700
* **Grassetto extra** &ndash; 800
* **Nero** &ndash; 900

Dopo aver definita una famiglia di caratteri, può essere utilizzato in modo dichiarativo impostando il `fontFamily`, `textStyle`, e `fontWeight` attributi nel file di layout.  Ad esempio il frammento XML seguente imposta un tipo di carattere 600 peso (normale) e uno stile del testo in corsivo:

```xml
<TextView
    android:text="Sans Source Pro semi-bold italic, 600 weight, italic"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:fontFamily="@font/sourcesanspro"
    android:textAppearance="?android:attr/textAppearanceLarge"
    android:gravity="center_horizontal"
    android:fontWeight="600"
    android:textStyle="italic"
    />
```

### <a name="programmatically-assigning-fonts"></a>Assegnazione a livello di programmazione di tipi di carattere

I tipi di carattere può essere impostate a livello di programmazione tramite le [ `Resources.GetFont` ](https://developer.android.com/reference/android/content/res/Resources.html#getFont(int)) metodo per recuperare un [ `Typeface` ](https://developer.android.com/reference/android/graphics/Typeface.html) oggetto. Molte visualizzazioni hanno un `TypeFace` proprietà che può essere usata per assegnare il tipo di carattere per il widget. Questo frammento di codice mostra come impostare a livello di codice il tipo di carattere in un controllo TextView:

```csharp
Android.Graphics.Typeface typeface = this.Resources.GetFont(Resource.Font.caveat_regular);
textView1.Typeface = typeface;
textView1.Text = "Changed the font";
```

Il `GetFont` metodo lo caricherà automaticamente il primo tipo di carattere all'interno di una famiglia di caratteri.  Per caricare un tipo di carattere che corrisponde a uno stile specifico, usare il `Typeface.Create` (metodo). Questo metodo tenterà di caricare un tipo di carattere che corrisponde a uno stile specificato. Ad esempio, questo frammento di codice tenterà di caricare un bold `Typeface` da una famiglia di caratteri che è definita nell'oggetto **risorse/tipi di carattere**:

```csharp
var typeface = Typeface.Create("<FONT FAMILY NAME>", Android.Graphics.TypefaceStyle.Bold);
textView1.Typeface = typeface;
```

## <a name="downloading-fonts"></a>Download di tipi di carattere

Invece di packaging i tipi di carattere come una risorsa applicazione, Android è possibile scaricare i tipi di carattere da un'origine remota. Ciò avrà l'effetto di ridurre le dimensioni dell'APK auspicabile. 

I tipi di carattere vengono scaricati con l'assistenza di un _provider di tipo di carattere_. Si tratta di un provider di contenuti specializzato che gestisce il download e la memorizzazione nella cache dei tipi di carattere per tutte le applicazioni nel dispositivo. Android 8.0 è incluso un provider del tipo di carattere per i tipi di carattere da scaricare il [Font Google Repository](http://fonts.google.com). Questo provider di tipo di carattere predefinito viene eseguito il backport per il livello API 14 con v26 la libreria di supporto per Android.

Quando un'app effettua una richiesta per un tipo di carattere, il provider del tipo di carattere verrà prima di tutto verificare se il tipo di carattere è già presente nel dispositivo. In caso contrario tenterà quindi di scaricare il tipo di carattere. Se il tipo di carattere non può essere scaricato, quindi Android userà il carattere di sistema predefinito. Dopo aver scaricato il tipo di carattere, è disponibile per tutte le applicazioni sul dispositivo, non solo le app che ha effettuato la richiesta iniziale.

Quando viene effettuata una richiesta per il download di un tipo di carattere, l'app non direttamente una query del provider del tipo di carattere. Al contrario, le app usano un'istanza del [ `FontsContract` ](https://developer.android.com/reference/android/provider/FontsContract.html) API (o il [ `FontsContractCompat` ](https://developer.android.com/reference/android/support/v4/provider/FontsContractCompat.html) se viene utilizzato il 26 Library di supporto).  

Android 8.0 supporta i caratteri in corso in due modi diversi:

1. **Dichiarare i tipi di carattere scaricabile come una risorsa** &ndash; un'app può dichiarare tipi di carattere scaricabile per Android tramite i file di risorse XML. Tali file conterrà tutti i metadati da scaricare in modo asincrono i tipi di carattere quando l'app viene avviata e memorizzarli nella cache nel dispositivo Android.
2. **A livello di programmazione** &ndash; API nel livello API Android 26 consentono a un'applicazione per il download a livello di codice, i tipi di carattere mentre è in esecuzione l'applicazione. App verranno creata una `FontRequest` per un determinato tipo di carattere, quindi passare questo oggetto per il `FontsContract` classe. Il `FontsContract` accetta il `FontRequest` e recupera il tipo di carattere da un _provider del tipo di carattere_. Android verrà scaricato in modo sincrono il tipo di carattere. Un esempio di creazione di un `FontRequest` verrà illustrato più avanti in questa Guida.

Indipendentemente da quale approccio viene usata, è possibile scaricare i file di risorse che devono essere aggiunto all'applicazione xamarin. Android prima i tipi di carattere. In primo luogo, il tipo di carattere deve essere dichiarati in un file XML nel **risorse/tipo di carattere** directory come parte di una famiglia di caratteri. Questo frammento di codice è riportato un esempio di come i tipi di carattere da scaricare il [raccolta Open Source di Google i tipi di carattere](https://fonts.google.com) usando il provider del tipo di carattere predefinito che viene fornito con Android 8.0 (o libreria di supporto v26):

```xml
<?xml version="1.0" encoding="utf-8"?>
<font-family xmlns:android="http://schemas.android.com/apk/res/android"
             xmlns:app="http://schemas.android.com/apk/res-auto"
             android:fontProviderAuthority="com.google.android.gms.fonts" 
             android:fontProviderPackage="com.google.android.gms" 
             android:fontProviderQuery="VT323" 
             android:fontProviderCerts="@array/com_google_android_gms_fonts_certs"
             app:fontProviderAuthority="com.google.android.gms.fonts" 
             app:fontProviderPackage="com.google.android.gms" 
             app:fontProviderQuery="VT323"
             app:fontProviderCerts="@array/com_google_android_gms_fonts_certs"
>
</font-family>
```

Il `font-family` elemento contiene gli attributi seguenti, dichiarare le informazioni che Android richiede di scaricare i tipi di carattere:

1. **fontProviderAuthority** &ndash; l'autorità del Provider del tipo di carattere da utilizzare per la richiesta.
2. **fontPackage** &ndash; il pacchetto per il Provider del tipo di carattere da utilizzare per la richiesta. Ciò consente di verificare l'identità del provider.
3. **fontQuery** &ndash; questa è una stringa che consenta il provider del tipo di carattere di individuare il tipo di carattere richiesto. Informazioni dettagliate sulla query di tipo di carattere sono specifiche del provider del tipo di carattere. Il [ `QueryBuilder` ](https://github.com/xamarin/monodroid-samples/blob/master/android-o/DownloadableFonts/DownloadableFonts/QueryBuilder.cs) classe il [tipi di carattere scaricabile](https://github.com/xamarin/monodroid-samples/blob/master/android-o/DownloadableFonts/) app di esempio fornisce alcune informazioni sul formato di query per i tipi di carattere della raccolta di origine aprire Google i tipi di carattere.
4. **fontProviderCerts** &ndash; con l'elenco dei set di hash per i certificati che il provider deve essere firmato con una matrice di risorse.

Una volta definiti i tipi di carattere, potrebbe essere necessario fornire informazioni sul _i certificati di tipo di carattere_ coinvolti con il download.

### <a name="font-certificates"></a>Certificati di tipo di carattere

Se il provider del tipo di carattere non è già preinstallato nel dispositivo o se l'app Usa il `Xamarin.Android.Support.Compat` , la libreria Android richiede i certificati di sicurezza del provider del tipo di carattere. Questi certificati verranno elencati in un file di risorse di matrice che viene mantenuto nel **risorse/valori** directory. 

Ad esempio, il codice XML seguente è denominato **Resources/values/fonts_cert.xml** e archivia i certificati per il provider del tipo di carattere Google: 

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <array name="com_google_android_gms_fonts_certs">
        <item>@array/com_google_android_gms_fonts_certs_dev</item>
        <item>@array/com_google_android_gms_fonts_certs_prod</item>
    </array>
    <string-array name="com_google_android_gms_fonts_certs_dev">
        <item>
            MIIEqDCCA5CgAwIBAgIJANWFuGx90071MA0GCSqGSIb3DQEBBAUAMIGUMQswCQYDVQQGEwJVUzETMBEGA1UECBMKQ2FsaWZvcm5pYTEWMBQGA1UEBxMNTW91bnRhaW4gVmlldzEQMA4GA1UEChMHQW5kcm9pZDEQMA4GA1UECxMHQW5kcm9pZDEQMA4GA1UEAxMHQW5kcm9pZDEiMCAGCSqGSIb3DQEJARYTYW5kcm9pZEBhbmRyb2lkLmNvbTAeFw0wODA0MTUyMzM2NTZaFw0zNTA5MDEyMzM2NTZaMIGUMQswCQYDVQQGEwJVUzETMBEGA1UECBMKQ2FsaWZvcm5pYTEWMBQGA1UEBxMNTW91bnRhaW4gVmlldzEQMA4GA1UEChMHQW5kcm9pZDEQMA4GA1UECxMHQW5kcm9pZDEQMA4GA1UEAxMHQW5kcm9pZDEiMCAGCSqGSIb3DQEJARYTYW5kcm9pZEBhbmRyb2lkLmNvbTCCASAwDQYJKoZIhvcNAQEBBQADggENADCCAQgCggEBANbOLggKv+IxTdGNs8/TGFy0PTP6DHThvbbR24kT9ixcOd9W+EaBPWW+wPPKQmsHxajtWjmQwWfna8mZuSeJS48LIgAZlKkpFeVyxW0qMBujb8X8ETrWy550NaFtI6t9+u7hZeTfHwqNvacKhp1RbE6dBRGWynwMVX8XW8N1+UjFaq6GCJukT4qmpN2afb8sCjUigq0GuMwYXrFVee74bQgLHWGJwPmvmLHC69EH6kWr22ijx4OKXlSIx2xT1AsSHee70w5iDBiK4aph27yH3TxkXy9V89TDdexAcKk/cVHYNnDBapcavl7y0RiQ4biu8ymM8Ga/nmzhRKya6G0cGw8CAQOjgfwwgfkwHQYDVR0OBBYEFI0cxb6VTEM8YYY6FbBMvAPyT+CyMIHJBgNVHSMEgcEwgb6AFI0cxb6VTEM8YYY6FbBMvAPyT+CyoYGapIGXMIGUMQswCQYDVQQGEwJVUzETMBEGA1UECBMKQ2FsaWZvcm5pYTEWMBQGA1UEBxMNTW91bnRhaW4gVmlldzEQMA4GA1UEChMHQW5kcm9pZDEQMA4GA1UECxMHQW5kcm9pZDEQMA4GA1UEAxMHQW5kcm9pZDEiMCAGCSqGSIb3DQEJARYTYW5kcm9pZEBhbmRyb2lkLmNvbYIJANWFuGx90071MAwGA1UdEwQFMAMBAf8wDQYJKoZIhvcNAQEEBQADggEBABnTDPEF+3iSP0wNfdIjIz1AlnrPzgAIHVvXxunW7SBrDhEglQZBbKJEk5kT0mtKoOD1JMrSu1xuTKEBahWRbqHsXclaXjoBADb0kkjVEJu/Lh5hgYZnOjvlba8Ld7HCKePCVePoTJBdI4fvugnL8TsgK05aIskyY0hKI9L8KfqfGTl1lzOv2KoWD0KWwtAWPoGChZxmQ+nBli+gwYMzM1vAkP+aayLe0a1EQimlOalO762r0GXO0ks+UeXde2Z4e+8S/pf7pITEI/tP+MxJTALw9QUWEv9lKTk+jkbqxbsh8nfBUapfKqYn0eidpwq2AzVp3juYl7//fKnaPhJD9gs=
        </item>
    </string-array>
    <string-array name="com_google_android_gms_fonts_certs_prod">
        <item>
            MIIEQzCCAyugAwIBAgIJAMLgh0ZkSjCNMA0GCSqGSIb3DQEBBAUAMHQxCzAJBgNVBAYTAlVTMRMwEQYDVQQIEwpDYWxpZm9ybmlhMRYwFAYDVQQHEw1Nb3VudGFpbiBWaWV3MRQwEgYDVQQKEwtHb29nbGUgSW5jLjEQMA4GA1UECxMHQW5kcm9pZDEQMA4GA1UEAxMHQW5kcm9pZDAeFw0wODA4MjEyMzEzMzRaFw0zNjAxMDcyMzEzMzRaMHQxCzAJBgNVBAYTAlVTMRMwEQYDVQQIEwpDYWxpZm9ybmlhMRYwFAYDVQQHEw1Nb3VudGFpbiBWaWV3MRQwEgYDVQQKEwtHb29nbGUgSW5jLjEQMA4GA1UECxMHQW5kcm9pZDEQMA4GA1UEAxMHQW5kcm9pZDCCASAwDQYJKoZIhvcNAQEBBQADggENADCCAQgCggEBAKtWLgDYO6IIrgqWbxJOKdoR8qtW0I9Y4sypEwPpt1TTcvZApxsdyxMJZ2JORland2qSGT2y5b+3JKkedxiLDmpHpDsz2WCbdxgxRczfey5YZnTJ4VZbH0xqWVW/8lGmPav5xVwnIiJS6HXk+BVKZF+JcWjAsb/GEuq/eFdpuzSqeYTcfi6idkyugwfYwXFU1+5fZKUaRKYCwkkFQVfcAs1fXA5V+++FGfvjJ/CxURaSxaBvGdGDhfXE28LWuT9ozCl5xw4Yq5OGazvV24mZVSoOO0yZ31j7kYvtwYK6NeADwbSxDdJEqO4k//0zOHKrUiGYXtqw/A0LFFtqoZKFjnkCAQOjgdkwgdYwHQYDVR0OBBYEFMd9jMIhF1Ylmn/Tgt9r45jk14alMIGmBgNVHSMEgZ4wgZuAFMd9jMIhF1Ylmn/Tgt9r45jk14aloXikdjB0MQswCQYDVQQGEwJVUzETMBEGA1UECBMKQ2FsaWZvcm5pYTEWMBQGA1UEBxMNTW91bnRhaW4gVmlldzEUMBIGA1UEChMLR29vZ2xlIEluYy4xEDAOBgNVBAsTB0FuZHJvaWQxEDAOBgNVBAMTB0FuZHJvaWSCCQDC4IdGZEowjTAMBgNVHRMEBTADAQH/MA0GCSqGSIb3DQEBBAUAA4IBAQBt0lLO74UwLDYKqs6Tm8/yzKkEu116FmH4rkaymUIE0P9KaMftGlMexFlaYjzmB2OxZyl6euNXEsQH8gjwyxCUKRJNexBiGcCEyj6z+a1fuHHvkiaai+KL8W1EyNmgjmyy8AW7P+LLlkR+ho5zEHatRbM/YAnqGcFh5iZBqpknHf1SKMXFh4dd239FJ1jWYfbMDMy3NS5CTMQ2XFI1MvcyUTdZPErjQfTbQe3aDQsQcafEQPD+nqActifKZ0Np0IS9L9kR/wbNvyz6ENwPiTrjV2KRkEjH78ZMcUQXg0L3BYHJ3lc69Vs5Ddf9uUGGMYldX3WfMBEmh/9iFBDAaTCK
        </item>
    </string-array>
</resources>
```

Con questi file di risorse posto, l'app è in grado di scaricare i tipi di carattere.

### <a name="declaring-downloadable-fonts-as-resources"></a>La dichiarazione di tipi di carattere scaricabile come risorse

Elencando i tipi di carattere scaricabile nel **androidmanifest. XML**, Android scaricherà i tipi di carattere in modo asincrono al primo avvio dell'app. Il tipo di carattere le stesse sono elencati in un file di risorse di matrice, simile alla seguente: 

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <array name="downloadable_fonts" translatable="false">
        <item>@font/vt323</item>
    </array>
</resources>
```

Per scaricare questi tipi di carattere, essi devono essere dichiarati **androidmanifest. XML** aggiungendo `meta-data` come elemento figlio di `application` elemento. Ad esempio, se i tipi di carattere scaricabile vengono dichiarati in un file di risorse al **Resources/values/downloadable_fonts.xml**, quindi questo frammento di codice dovrà essere aggiunto al manifesto: 

```xml
<meta-data android:name="downloadable_fonts" android:resource="@array/downloadable_fonts" />
```

### <a name="downloading-a-font-with-the-font-apis"></a>Download di un tipo di carattere con le API del tipo di carattere

È possibile scaricare a livello di codice un tipo di carattere creando un [ `FontRequest` ](https://developer.android.com/reference/android/support/v4/provider/FontRequest.html) oggetto e passandolo al `FontContractCompat.RequestFont` (metodo). Il `FontContractCompat.RequestFont` metodo prima di tutto controllare per verificare se il tipo di carattere presente nel dispositivo e quindi se necessario verranno in modo asincrono query il provider del tipo di carattere e provare a scaricare il tipo di carattere per l'app. Se `FontRequest` è in grado di scaricare il tipo di carattere Android userà il carattere di sistema predefinito. 

Oggetto `FontRequest` oggetto contiene informazioni che verranno utilizzate dal provider del tipo di carattere per individuare e scaricare un tipo di carattere. Oggetto `FontRequest` richiede quattro tipi di informazioni:

1. **Tipo di carattere Provider autorità** &ndash; l'autorità del Provider del tipo di carattere da utilizzare per la richiesta.
2. **Pacchetto del tipo di carattere** &ndash; il pacchetto per il Provider del tipo di carattere da utilizzare per la richiesta. Ciò consente di verificare l'identità del provider.
3. **Query di tipo di carattere** &ndash; questa è una stringa che consenta il provider del tipo di carattere di individuare il tipo di carattere richiesto. Informazioni dettagliate sulla query di tipo di carattere sono specifiche del provider del tipo di carattere. I dettagli della stringa sono specifici del provider del tipo di carattere. Il [ `QueryBuilder` ](https://github.com/xamarin/monodroid-samples/blob/master/android-o/DownloadableFonts/DownloadableFonts/QueryBuilder.cs) classe il [tipi di carattere scaricabile](https://github.com/xamarin/monodroid-samples/blob/master/android-o/DownloadableFonts/) app di esempio fornisce alcune informazioni sul formato di query per i tipi di carattere della raccolta di origine aprire Google i tipi di carattere.
4. **I certificati del Provider del tipo di carattere** &ndash; con l'elenco dei set di hash per i certificati il provider deve essere firmato con una matrice di risorse. 

Questo frammento di codice è riportato un esempio di un'istanza di un nuovo `FontRequest` oggetto:

```csharp
FontRequest request = new FontRequest("com.google.android.gms.fonts", "com.google.android.gms", <FontToDownload>, Resource.Array.com_google_android_gms_fonts_certs);
```

Nel frammento precedente `FontToDownload` è una query che consenta il tipo di carattere dalla raccolta Open Source di Google i tipi di carattere. 

Prima di passare il `FontRequest` per il `FontContractCompat.RequestFont` (metodo), sono presenti due oggetti che devono essere creati:

* **`FontsContractCompat.FontRequestCallback`** &ndash; Si tratta di una classe astratta che deve essere esteso. Si tratta di un callback che verrà richiamato quando `RequestFont` viene completata. Un'app xamarin. Android deve creare una sottoclasse `FontsContractCompat.FontRequestCallback` ed eseguire l'override di `OnTypefaceRequestFailed` e `OnTypefaceRetrieved`, fornendo le azioni da intraprendere quando il download non riesce o riesce rispettivamente.
* **`Handler`** &ndash; Si tratta di un `Handler` che verrà usato da `RequestFont` per scaricare il tipo di carattere in un thread, se necessario. I tipi di carattere dovrebbe **non** essere scaricato nel thread dell'interfaccia utente.

Questo frammento di codice è riportato un esempio di un C# classe che scarica in modo asincrono un tipo di carattere da raccolta Open Source di Google i tipi di carattere. Implementa il `FontRequestCallback` interfaccia e genera un C# evento quando `FontRequest` ha terminato. 

```csharp
public class FontDownloadHelper : FontsContractCompat.FontRequestCallback
{
    // A very simple font query; replace as necessary
    public static readonly String FontToDownload = "Courgette";

    Android.OS.Handler Handler = null;

    public event EventHandler<FontDownloadEventArg> FontDownloaded = delegate
    {
        // just an empty delegate to avoid null reference exceptions.  
    };


    public void DownloadFonts(Context context)
    {
        FontRequest request = new FontRequest("com.google.android.gms.fonts", "com.google.android.gms",FontToDownload , Resource.Array.com_google_android_gms_fonts_certs);
        FontsContractCompat.RequestFont(context, request, this, GetHandlerThreadHandler());
    }

    public override void OnTypefaceRequestFailed(int reason)
    {
        base.OnTypefaceRequestFailed(reason);
        FontDownloaded(this, new FontDownloadEventArg(null));
    }

    public override void OnTypefaceRetrieved(Android.Graphics.Typeface typeface)
    {
        base.OnTypefaceRetrieved(typeface);
        FontDownloaded(this, new FontDownloadEventArg(typeface));
    }

    Handler GetHandlerThreadHandler()
    {
        if (Handler == null)
        {
            HandlerThread handlerThread = new HandlerThread("fonts");
            handlerThread.Start();
            Handler = new Handler(handlerThread.Looper);
        }
        return Handler;
    }
}

public class FontDownloadEventArg : EventArgs
{
    public FontDownloadEventArg(Android.Graphics.Typeface typeface)
    {
        Typeface = typeface;
    }
    public Android.Graphics.Typeface Typeface { get; private set; }
    public bool RequestFailed
    {
        get
        {
            return Typeface != null;
        }
    }
}
```

Usare questo helper, un nuovo `FontDownloadHelper` viene creato e viene assegnato un gestore eventi:  

```csharp
var fontHelper = new FontDownloadHelper();

fontHelper.FontDownloaded += (object sender, FontDownloadEventArg e) => 
{
    //React to the request
};
fontHelper.DownloadFonts(this); // this is an Android Context instance.
```

## <a name="summary"></a>Riepilogo

Questa guida ha illustrato le nuove API di Android 8.0 per supportare i tipi di carattere scaricabile e i tipi di carattere come risorse. È stato illustrato come incorporare i tipi di carattere esistente in un file APK e usarli in un layout. È stato inoltre illustrato come Android 8.0 supporta i tipi di carattere download da un provider del tipo di carattere, a livello di programmazione o mediante la dichiarazione del tipo di carattere i metadati nei file di risorse.

## <a name="related-links"></a>Collegamenti correlati

- [fontFamily](https://developer.android.com/reference/android/widget/TextView.html#attr_android:fontFamily)
- [FontConfig](https://developer.android.com/reference/android/text/FontConfig.html)
- [FontRequest](https://developer.android.com/reference/android/support/v4/provider/FontRequest.html)
- [FontsContractCompat](https://developer.android.com/reference/android/support/v4/provider/FontsContractCompat.html)
- [Resources.GetFont](https://developer.android.com/reference/android/content/res/Resources.html#getFont(int))
- [Carattere tipografico](https://developer.android.com/reference/android/graphics/Typeface.html)
- [Podpora androidu NuGet della libreria 26](https://www.nuget.org/packages/Xamarin.Android.Support.Compat/)
- [Usando tipi di carattere in Android](https://www.youtube.com/watch?v=TfB-TsLFJdM)
- [Specifica di spessore carattere CSS](https://www.w3.org/TR/css-fonts-3/#font-weight-numeric-values)
- [Raccolta di tipi di carattere di Google Open Source](https://fonts.google.com/)
- [Origine Sans Pro](https://fonts.google.com/specimen/Source+Sans+Pro)
