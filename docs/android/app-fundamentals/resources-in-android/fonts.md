---
title: Tipi di carattere
ms.prod: xamarin
ms.assetid: 3F543FC5-FDED-47F8-8D2C-481FCC98BFDA
ms.technology: xamarin-android
author: topgenorth
ms.author: toopge
ms.date: 04/13/2018
ms.openlocfilehash: 086576ea7d806bb0768fbe4563df7fca99244ccb
ms.sourcegitcommit: bc39d85b4585fcb291bd30b8004b3f7edcac4602
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2018
ms.locfileid: "31044821"
---
# <a name="fonts"></a>Tipi di carattere

## <a name="overview"></a>Panoramica

A partire da livello API 26, Android SDK consente i tipi di carattere deve essere trattato come risorse, come un layout o drawables. Il [Android supporto libreria 26 NuGet](https://www.nuget.org/packages/Xamarin.Android.Support.Compat/26.1.0.1) verrà backport il nuovo tipo di carattere dell'API per le app destinate a livello di API 14 o versione successiva.

Dopo aver interessati a 26 API o installando v26 la libreria di supporto per Android, esistono due modi per utilizzare i tipi di carattere in un'applicazione Android:

1. **Il tipo di carattere come risorsa Android pacchetto** &ndash; questo modo si garantisce che il tipo di carattere sia sempre disponibile per l'applicazione, ma aumenterà le dimensioni del file APK. 
2. **Scaricare i tipi di carattere** &ndash; Android supporta inoltre il download di un tipo di carattere da un _provider carattere_. Il provider di tipi di carattere controlla se nel dispositivo è già il tipo di carattere. Se necessario, il tipo di carattere verrà scaricato e memorizzato nella cache del dispositivo. Questo tipo di carattere può essere condiviso tra più applicazioni.

Tipi di carattere simili (o un tipo di carattere che può avere diversi stili diversi) può essere raggruppato in _famiglie_. Ciò consente agli sviluppatori di specificare determinati attributi del tipo di carattere, ad esempio il peso, e Android verrà selezionato automaticamente il tipo di carattere appropriato dalla famiglia di caratteri.

V26 la libreria di supporto Android verrà backport supporto per i tipi di carattere per il livello di API 26. Se la destinazione i precedenti livelli di API, è necessario dichiarare il `app` spazio dei nomi XML e di assegnare i vari attributi di tipo di carattere utilizzando il `android:` dello spazio dei nomi e `app:` dello spazio dei nomi. Se solo il `android:` viene utilizzato lo spazio dei nomi, quindi i tipi di carattere non saranno visualizzati i dispositivi che eseguono il livello API 25 o inferiore. Questo frammento XML dichiara, ad esempio, un nuovo [ _famiglia di caratteri_ ](#font_families) risorse di livello API 14 e versioni successiva:

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

Come tipi di carattere forniti da un'applicazione Android in modo corretto, può essere applicati a un widget dell'interfaccia utente impostando il [ `fontFamily` attributo](https://developer.android.com/reference/android/widget/TextView.html#attr_android:fontFamily). Ad esempio, il frammento di codice seguente viene illustrato come visualizzare un tipo di carattere in un oggetto TextView:

```xml
<TextView
    android:text="The quick brown fox jumped over the lazy dog."
    android:fontFamily="@font/sourcesanspro_regular"
    app:fontFamily="@font/sourcesanspro_regular"
    android:textAppearance="?android:attr/textAppearanceLarge"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" />
```

Questa Guida verrà innanzitutto illustrato come utilizzare i tipi di carattere come risorsa di Android e passare quindi illustrato come scaricare i tipi di carattere in fase di esecuzione.

## <a name="fonts-as-a-resource"></a>Tipi di carattere come una risorsa

Assemblaggio di un tipo di carattere in un file APK di Android garantisce che sia sempre disponibile per l'applicazione. Un file di caratteri (entrambi una. TTF o un oggetto. File OTF) viene aggiunto a un'applicazione di xamarin come qualsiasi altra risorsa, copiando i file in una sottodirectory nella **risorse** cartella di un progetto xamarin. Risorse di tipi di carattere vengono mantenute un **carattere** sottodirectory del **risorse** nella cartella del progetto. 

> [!NOTE]
> I tipi di carattere deve essere un **azione di compilazione** di **AndroidResource** o non sono verranno inserite nell'APK finale. L'operazione di compilazione deve essere impostato automaticamente dall'IDE.

Quando sono presenti molti file di caratteri simili (ad esempio, lo stesso carattere con diversi fattori di ponderazione o stili) è possibile raggrupparli in una famiglia di caratteri.

<a name="font_families" />

### <a name="font-families"></a>Famiglie di caratteri

Una famiglia di caratteri è un set di tipi di carattere con stili e il peso. Ad esempio, potrebbero essere presenti i file del tipo di carattere per i tipi di carattere grassetto o corsivo. La famiglia di caratteri è definita da `font` elementi in un file XML che viene mantenuto nel **risorse/tipo di carattere** directory. Ogni famiglia di caratteri deve avere i propri file XML.

Per creare una famiglia di caratteri, prima di aggiungere tutti i tipi di carattere per il **risorse/tipo di carattere** cartella. Creare quindi un nuovo file XML nella cartella del carattere per la famiglia di caratteri. Il nome del file XML non dispone di alcuna affinità o la relazione per i tipi di carattere viene fatto riferimento; il file di risorse può essere qualsiasi nome di file di risorse di Android valido. Il file XML contiene una radice `font-family` elemento che contiene uno o più `font` elementi. Ogni `font` elemento dichiara gli attributi di un tipo di carattere.

Il codice XML seguente è riportato un esempio di una famiglia di caratteri per il _origini SAN Pro_ carattere che definisce molti pesi di diverso tipo di carattere. Viene salvato come file di **risorse/tipo di carattere** cartella denominata **sourcesanspro.xml**:

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

Il `fontStyle` attributo dispone di due valori possibili:

* **normale** &ndash; un tipo di carattere normale
* **corsivo** &ndash; un tipo di carattere corsivo

Il `fontWeight` attributo corrisponde al foglio di stile CSS `font-weight` attributo e si intende lo spessore del carattere. Si tratta di un valore compreso nell'intervallo da 100 a 900. Nell'elenco seguente vengono descritti i valori di peso comuni del tipo di carattere e il nome:

* **Thin** &ndash; 100
* **Molto chiaro** &ndash; 200
* **Luce** &ndash; 300
* **Normale** &ndash; 400
* **Media** &ndash; 500
* **Semi grassetto** &ndash; 600
* **Grassetto** &ndash; 700
* **Nero** &ndash; 800
* **Nero** &ndash; 900

Dopo avere definita una famiglia di caratteri, può essere utilizzato in modo dichiarativo mediante l'impostazione di `fontFamily`, `textStyle`, e `fontWeight` attributi nel file di layout.  Ad esempio il frammento XML seguente imposta un tipo di carattere 400 peso (normale) e uno stile di testo in corsivo:

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

Tipi di carattere possibile impostare a livello di codice utilizzando il [ `Resources.GetFont` ](https://developer.android.com/reference/android/content/res/Resources.html#getFont(int)) metodo per recuperare un [ `Typeface` ](https://developer.android.com/reference/android/graphics/Typeface.html) oggetto. Molte visualizzazioni hanno un `TypeFace` proprietà che può essere utilizzato per assegnare il tipo di carattere per il widget. Frammento di codice seguente viene illustrato come impostare a livello di codice il tipo di carattere in un oggetto TextView:

```csharp
Android.Graphics.Typeface typeface = this.Resources.GetFont(Resource.Font.caveat_regular);
textView1.Typeface = typeface;
textView1.Text = "Changed the font";
```

Il `GetFont` metodo caricherà automaticamente il primo tipo di carattere all'interno di una famiglia di caratteri.  Per caricare un tipo di carattere che corrisponde a uno stile specifico, utilizzare il `Typeface.Create` metodo. Questo metodo tenterà di caricare un tipo di carattere che corrisponde allo stile specificato. Ad esempio, questo frammento di codice tenterà di caricare in grassetto `Typeface` oggetto da una famiglia di caratteri che è definita in **risorse/tipi di carattere**:

```csharp
var typeface = Typeface.Create("<FONT FAMILY NAME>", Android.Graphics.TypefaceStyle.Bold);
textView1.Typeface = typeface;
```

## <a name="downloading-fonts"></a>Download di tipi di carattere

Invece di creazione del pacchetto dei tipi di carattere come una risorsa dell'applicazione, Android è possibile scaricare i tipi di carattere da un'origine remota. Ciò avrà l'effetto della riduzione delle dimensioni dell'APK auspicabile. 

Tipi di carattere vengono scaricati con l'assistenza di un _provider carattere_. Si tratta di un provider di contenuti specializzato che gestisce il download e memorizzazione nella cache dei tipi di carattere per tutte le applicazioni nel dispositivo. 8.0 Android include un provider di tipi di carattere per i tipi di carattere da scaricare il [Google carattere Repository](http://fonts.google.com). Questo provider di tipi di carattere predefinito è backported a livello di API 14 con v26 la libreria di supporto per Android.

Quando un'app esegue una richiesta per un tipo di carattere, il provider di tipi di carattere verrà innanzitutto verificare se nel dispositivo è già il tipo di carattere. In caso contrario, tenterà di scaricare il tipo di carattere. Se il tipo di carattere non può essere scaricato, quindi Android verrà utilizzato il carattere di sistema predefinito. Dopo aver scaricato il tipo di carattere, è disponibile per tutte le applicazioni nel dispositivo, non solo dell'applicazione che ha effettuato la richiesta iniziale.

Quando viene effettuata una richiesta per il download di un tipo di carattere, l'app non direttamente una query il provider di tipi di carattere. App utilizza invece un'istanza di [ `FontsContract` ](https://developer.android.com/reference/android/provider/FontsContract.html) API (o [ `FontsContractCompat` ](https://developer.android.com/reference/android/support/v4/provider/FontsContractCompat.html) se viene utilizzato il 26 libreria di supporto).  

8.0 Android supporta i caratteri in corso in due modi diversi:

1. **Dichiarare i tipi di carattere trasferibili come risorsa** &ndash; un'app può dichiarare i tipi di carattere scaricabile per Android tramite file di risorse XML. Questi file conterrà tutti i metadati che è necessario scaricare in modo asincrono i tipi di carattere quando l'app viene avviata e memorizzarli nella cache nel dispositivo Android.
2. **A livello di programmazione** &ndash; API nel livello API Android 26 consentono di scaricare i tipi di carattere a livello di programmazione, mentre l'applicazione è in esecuzione. App creerà un `FontRequest` per un determinato tipo di carattere, quindi passare questo oggetto per il `FontsContract` classe. Il `FontsContract` accetta il `FontRequest` e recupera il tipo di carattere da un _provider carattere_. Android in modo sincrono scaricherà il tipo di carattere. Un esempio di creazione di un `FontRequest` verrà illustrato più avanti in questa Guida.

Indipendentemente da quale approccio è usato, è possibile scaricare i file di risorse che devono essere aggiunte all'applicazione xamarin prima i tipi di carattere. In primo luogo, il tipo di carattere deve essere dichiarato in un file XML di **risorse/tipo di carattere** directory come parte di una famiglia di caratteri. Questo frammento è riportato un esempio di come i tipi di carattere da scaricare il [raccolta di tipi di carattere di Google Open Source](https://fonts.google.com) utilizzando il provider di tipi di carattere predefinito che viene fornito con Android 8.0 (o libreria di supporto v26):

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

Il `font-family` elemento contiene i seguenti attributi, le informazioni che è necessario per scaricare i tipi di carattere Android di dichiarazione:

1. **fontProviderAuthority** &ndash; l'autorità del Provider di tipi di carattere da utilizzare per la richiesta.
2. **fontPackage** &ndash; il pacchetto per il Provider di tipi di carattere da utilizzare per la richiesta. Viene utilizzato per verificare l'identità del provider.
3. **fontQuery** &ndash; è una stringa che consentirà il provider di tipi di carattere di individuare il tipo di carattere richiesto. Dettagli sulla query di tipo di carattere sono specifici del provider di tipi di carattere. Il [ `QueryBuilder` ](https://github.com/xamarin/monodroid-samples/blob/master/android-o/DownloadableFonts/DownloadableFonts/QueryBuilder.cs) classe il [tipi di carattere trasferibili](https://github.com/xamarin/monodroid-samples/blob/master/android-o/DownloadableFonts/) app di esempio fornisce alcune informazioni sul formato di query per i tipi di carattere da Google caratteri aprire raccolta di origine.
4. **fontProviderCerts** &ndash; con l'elenco dei set di hash per i certificati che il provider deve essere firmato con una matrice di risorse.

Una volta definiti i tipi di carattere, potrebbe essere necessario fornire informazioni di _i certificati di tipo di carattere_ coinvolti con il download.

### <a name="font-certificates"></a>Certificati di tipo di carattere

Se il provider di tipi di carattere non è stato preinstallato sul dispositivo o se l'app Usa la `Xamarin.Android.Support.Compat` libreria Android richiede i certificati di sicurezza del provider di tipi di carattere. Questi certificati verranno elencati in un file di risorse di matrice che viene mantenuto in **risorse/valori** directory. 

Ad esempio, il seguente codice XML è denominato **Resources/values/fonts_cert.xml** e archivia i certificati per il provider di tipi di carattere Google: 

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

Questi file di risorse sul posto, l'applicazione è in grado di scaricare i tipi di carattere.

### <a name="declaring-downloadable-fonts-as-resources"></a>Dichiarazione di tipi di carattere trasferibili come risorse

Elencando i tipi di carattere scaricabile di **AndroidManifest.XML**, Android scaricherà i tipi di carattere in modo asincrono al primo avvio dell'app. Il tipo di carattere le stesse sono elencati in un file di risorse di matrice, simile al seguente: 

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <array name="downloadable_fonts" translatable="false">
        <item>@font/vt323</item>
    </array>
</resources>
```

Per scaricare questi tipi di carattere, essi devono essere dichiarati **AndroidManifest.XML** aggiungendo `meta-data` come elemento figlio di `application` elemento. Ad esempio, se i tipi di carattere scaricabile vengono dichiarati in un file di risorse in **Resources/values/downloadable_fonts.xml**, quindi questo frammento di codice avrebbero dovuto essere aggiunto al manifesto: 

```xml
<meta-data android:name="downloadable_fonts" android:resource="@array/downloadable_fonts" />
```

### <a name="downloading-a-font-with-the-font-apis"></a>Download di un tipo di carattere con le API di tipo di carattere

È possibile scaricare a livello di programmazione un tipo di carattere creando un [ `FontRequest` ](https://developer.android.com/reference/android/support/v4/provider/FontRequest.html) oggetto e passando che per il `FontContractCompat.RequestFont` metodo. Il `FontContractCompat.RequestFont` metodo sarà controllare innanzitutto se il tipo di carattere esistente nel dispositivo e quindi se necessario verrà in modo asincrono query il provider di tipi di carattere e provare a scaricare il tipo di carattere per l'app. Se `FontRequest` è riuscito a scaricare il tipo di carattere, Android verrà utilizzato il carattere di sistema predefinito. 

Oggetto `FontRequest` oggetto contiene informazioni che verranno utilizzate dal provider di tipi di carattere per individuare e scaricare un tipo di carattere. Oggetto `FontRequest` richiede quattro tipi di informazioni:

1. **Tipo di carattere Provider autorità** &ndash; l'autorità del Provider di tipi di carattere da utilizzare per la richiesta.
2. **Tipo di carattere pacchetto** &ndash; il pacchetto per il Provider di tipi di carattere da utilizzare per la richiesta. Viene utilizzato per verificare l'identità del provider.
3. **Query di tipo di carattere** &ndash; è una stringa che consentirà il provider di tipi di carattere di individuare il tipo di carattere richiesto. Dettagli sulla query di tipo di carattere sono specifici del provider di tipi di carattere. I dettagli della stringa sono specifici del provider di tipi di carattere. Il [ `QueryBuilder` ](https://github.com/xamarin/monodroid-samples/blob/master/android-o/DownloadableFonts/DownloadableFonts/QueryBuilder.cs) classe il [tipi di carattere trasferibili](https://github.com/xamarin/monodroid-samples/blob/master/android-o/DownloadableFonts/) app di esempio fornisce alcune informazioni sul formato di query per i tipi di carattere da Google caratteri aprire raccolta di origine.
4. **Tipo di carattere Provider certificati** &ndash; con l'elenco dei set di hash per i certificati che il provider deve essere firmato con una matrice di risorse. 

Questo frammento è riportato un esempio di un'istanza di un nuovo `FontRequest` oggetto:

```csharp
FontRequest request = new FontRequest("com.google.android.gms.fonts", "com.google.android.gms", <FontToDownload>, Resource.Array.com_google_android_gms_fonts_certs);
```

Nel frammento precedente `FontToDownload` è una query che consentirà il tipo di carattere dalla raccolta di tipi di carattere di Google Open Source. 

Prima di passarla il `FontRequest` per il `FontContractCompat.RequestFont` (metodo), sono presenti due oggetti che devono essere creati:

* **`FontsContractCompat.FontRequestCallback`** &ndash; Questa è una classe astratta che deve essere esteso. È un callback che verrà richiamato quando `RequestFont` è terminato. Un'app xamarin deve creare una sottoclasse `FontsContractCompat.FontRequestCallback` ed eseguire l'override di `OnTypefaceRequestFailed` e `OnTypefaceRetrieved`, fornendo le azioni da intraprendere quando il download non riesce o riesce rispettivamente.
* **`Handler`** &ndash; Si tratta di un `Handler` che verrà usato da `RequestFont` per scaricare il tipo di carattere in un thread, se necessario. Tipi di carattere deve **non** scaricate nel thread UI.

Questo frammento è riportato un esempio di una classe c# che scarica in modo asincrono un carattere dalla raccolta di tipi di carattere di Google Open Source. Implementa il `FontRequestCallback` di interfaccia e genera un evento c# quando `FontRequest` è terminata. 

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

Per usare questo supporto, un nuovo `FontDownloadHelper` viene creato e viene assegnato un gestore eventi:  

```csharp
var fontHelper = new FontDownloadHelper();

fontHelper.FontDownloaded += (object sender, FontDownloadEventArg e) => 
{
    //React to the request
};
fontHelper.DownloadFonts(this); // this is an Android Context instance.
```

## <a name="summary"></a>Riepilogo

Questa guida illustrate le nuove API di Android 8.0 per supportare i tipi di carattere scaricabile e i tipi di carattere come risorse. È stato illustrato come incorporare i tipi di carattere in un file APK e il loro utilizzo in un layout. È stato inoltre illustrato come 8.0 Android supporta i caratteri in corso di un provider di tipi di carattere, a livello di programmazione o mediante la dichiarazione di tipo di carattere i metadati nel file di risorse.

## <a name="related-links"></a>Collegamenti correlati

- [fontFamily](https://developer.android.com/reference/android/widget/TextView.html#attr_android:fontFamily)
- [FontConfig](https://developer.android.com/reference/android/text/FontConfig.html)
- [FontRequest](https://developer.android.com/reference/android/support/v4/provider/FontRequest.html)
- [FontsContractCompat](https://developer.android.com/reference/android/support/v4/provider/FontsContractCompat.html)
- [Resources.GetFont](https://developer.android.com/reference/android/content/res/Resources.html#getFont(int))
- [Carattere tipografico](https://developer.android.com/reference/android/graphics/Typeface.html)
- [Supporto Android libreria 26 NuGet](https://www.nuget.org/packages/Xamarin.Android.Support.Compat/)
- [Utilizzo di tipi di carattere in Android](https://www.youtube.com/watch?v=TfB-TsLFJdM)
- [Specifica di spessore carattere CSS](https://www.w3.org/TR/css-fonts-3/#font-weight-numeric-values)
- [Raccolta di tipi di carattere di Google Open Source](https://fonts.google.com/)
- [Origine Sans Pro](https://fonts.google.com/specimen/Source+Sans+Pro)
