---
title: Come è possibile installare manualmente le librerie di supporto Android necessari per i pacchetti deselezionati?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: A9CB8CA8-8A6D-405E-B84C-A16CE452C0F7
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: e760a87cbd1e0220ed5cf3a350d3539ffe29650e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="how-can-i-manually-install-the-android-support-libraries-required-by-the-xamarinandroidsupport-packages"></a>Come è possibile installare manualmente le librerie di supporto Android necessari per i pacchetti deselezionati?

## <a name="example-steps-for-xamarinandroidsupportv4"></a>Procedure di esempio per Xamarin.Android.Support.v4 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Scaricare il pacchetto NuGet deselezionati desiderato (ad esempio per l'installazione con Gestione pacchetti NuGet).

Utilizzare `ildasm` per verificare la versione di **android_m2repository.zip** il pacchetto NuGet deve:

```cmd
ildasm /caverbal /text /item:Xamarin.Android.Support.v4 packages\Xamarin.Android.Support.v4.23.4.0.1\lib\MonoAndroid403\Xamarin.Android.Support.v4.dll | findstr SourceUrl
```
Output di esempio:

```cmd
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
```

Scaricare **android\_m2repository.zip** da Google utilizzando l'URL restituito da **ildasm**. In alternativa, è possibile controllare quale versione di _Repository supporto Android_ attualmente installato in Android SDK Manager:

!["Che mostra Repository di supporto Android versione 32 installato android SDK Manager"](install-android-support-library-images/sdk-extras.png)

Se la versione corrisponda a quello che necessario per il pacchetto NuGet, quindi non è necessario scaricare un nuovo valore. È possibile invece comprimere nuovamente esistente **m2repository** directory che si trova sotto **extra\\android** nel _percorso SDK_ (come illustrato nella parte superiore di Android Finestra di gestione SDK).

Calcolare l'hash MD5 dell'URL restituito da **ildasm**. Formattare la stringa risulta per utilizzare tutte lettere maiuscole e senza spazi. Ad esempio, regolare il `$url` variabile come necessario e quindi eseguire le seguenti 2 righe (in base a [il codice c# originale da xamarin](https://github.com/xamarin/xamarin-android/blob/8e8a4dd90f26eb39172876cc52181b6639e20524/src/Xamarin.Android.Build.Tasks/Tasks/GetAdditionalResourcesFromAssemblies.cs#L208)) in PowerShell:

```powershell
$url = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip"
(([System.Security.Cryptography.MD5]::Create()).ComputeHash([System.Text.Encoding]::UTF8.GetBytes($url)) | %{ $_.ToString("X02") }) -join ""
```
Output di esempio:

```powershell
F16A3455987DBAE5783F058F19F7FCDF
```

Copia **android\_m2repository.zip** nel **% LOCALAPPDATA %\\Xamarin\\passa\\**  cartella. Rinominare il file per utilizzare il valore hash MD5 dall'hash MD5 precedente il passaggio di calcolo. Ad esempio:

**%LOCALAPPDATA%\\Xamarin\\zips\\F16A3455987DBAE5783F058F19F7FCDF.zip**

(Facoltativo) Decomprimere il file in **% LOCALAPPDATA %\\Xamarin\\Xamarin.Android.Support.v4\\23.4.0.0\\contenuto\\**  (creazione di un **contenuto\\m2repository** sottodirectory). Se si ignora questo passaggio, quindi la prima compilazione che utilizza la libreria richiederà più tempo perché è necessario completare questo passaggio.
Il numero di versione per la sottodirectory (**23.4.0.0** in questo esempio) non è piuttosto quella della versione del pacchetto NuGet. È possibile utilizzare `ildasm` per trovare il numero di versione corretto:

```cmd
ildasm /caverbal /text /item:Xamarin.Android.Support.v4 packages\Xamarin.Android.Support.v4.23.4.0.1\lib\MonoAndroid403\Xamarin.Android.Support.v4.dll | findstr /C:"string 'Version'"
```
Output di esempio:

```cmd
property string 'Version' = string('23.4.0.0')}
property string 'Version' = string('23.4.0.0')}
property string 'Version' = string('23.4.0.0')}
```

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Scaricare il pacchetto NuGet deselezionati desiderato (ad esempio per l'installazione con Gestione pacchetti NuGet).

Fare doppio clic su di _Xamarin.Android.Support.v4_ assembly sotto il _riferimenti_ sezione del progetto Android in Visual Studio per Mac aprire l'assembly nel Visualizzatore Assembly. Verificare che il _Language_ elenco a discesa è impostato su _c#_ e selezionare il primo livello _Xamarin.Android.Support.v4_ assembly dall'albero di navigazione del Browser di Assembly. Individuare il `SourceUrl` proprietà in uno del `IncludeAndroidResourcesFrom` o `JavaLibraryReference` attributi:

```csharp
[assembly: IncludeAndroidResourcesFrom ("./", PackageName = "Xamarin.Android.Support.v4", SourceUrl = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip", EmbeddedArchive = "m2repository/com/android/support/support-v4/23.4.0/support-v4-23.4.0.aar", Version = "23.4.0.0")]
```

Scaricare **android\_m2repository.zip** da Google usando il `SourceUrl` restituito da **ildasm**. In alternativa, è possibile controllare quale versione di _Repository supporto Android_ attualmente installato in Android SDK Manager:

!["Che mostra Repository di supporto Android versione 32 installato android SDK Manager"](install-android-support-library-images/sdk-extras.png)

Se la versione corrisponda a quello che necessario per il pacchetto NuGet, quindi non è necessario scaricare un nuovo valore. È possibile invece comprimere nuovamente esistente **m2repository** directory che si trova sotto **extras/android** nel _percorso SDK_ (come illustrato nella parte superiore della finestra di Android SDK Manager) .

Calcolare l'hash MD5 dell'URL restituito da **ildasm**. Formattare la stringa risulta per utilizzare tutte lettere maiuscole e senza spazi. Ad esempio, modificare la stringa URL come necessario e quindi eseguire il comando seguente in un **Terminal.app** prompt dei comandi:

```bash
echo -n "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip" | md5 | tr '[:lower:]' '[:upper:]'
```

Un'altra opzione consiste nell'utilizzare il `csharp` interprete per eseguire [lo stesso codice c# che utilizza xamarin stesso](https://github.com/xamarin/xamarin-android/blob/8e8a4dd90f26eb39172876cc52181b6639e20524/src/Xamarin.Android.Build.Tasks/Tasks/GetAdditionalResourcesFromAssemblies.cs#L208).
A tale scopo, modificare il `url` variabile come necessario e quindi eseguire il comando seguente in un **Terminal.app** prompt dei comandi:

```bash
csharp -e 'var url = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip"; string.Concat((System.Security.Cryptography.MD5.Create().ComputeHash(System.Text.Encoding.UTF8.GetBytes(url))).Select(b => b.ToString("X02")))'
```
Output di esempio:

```bash
F16A3455987DBAE5783F058F19F7FCDF
```

Copia **android\_m2repository.zip** per il **$HOME/.local/share/Xamarin/zips/** cartella. Rinominare il file per utilizzare il valore hash MD5 dall'hash MD5 precedente il passaggio di calcolo. Ad esempio:

**$HOME/.local/share/Xamarin/zips/F16A3455987DBAE5783F058F19F7FCDF.zip**

(Facoltativo) Decomprimere il file in: 

**$HOME/.local/share/Xamarin/Xamarin.Android.Support.v4/23.4.0.0/content/**

(creazione di un **contenuto/m2repository** sottodirectory). Se si ignora questo passaggio, quindi la prima compilazione che utilizza la libreria richiederà più tempo perché è necessario completare questo passaggio.

Il numero di versione per la sottodirectory (**23.4.0.0** in questo esempio) non è piuttosto quella della versione del pacchetto NuGet. Ad esempio il **ildasm** passaggio precedente, è possibile utilizzare il Visualizzatore Assembly in Visual Studio per Mac per trovare il numero di versione corretto. Cercare il `Version` proprietà in uno del `IncludeAndroidResourcesFrom` o `JavaLibraryReference` attributi:

```csharp
[assembly: IncludeAndroidResourcesFrom ("./", PackageName = "Xamarin.Android.Support.v4", SourceUrl = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip", EmbeddedArchive = "m2repository/com/android/support/support-v4/23.4.0/support-v4-23.4.0.aar", Version = "23.4.0.0")]
```

-----


## <a name="additional-references"></a>Riferimenti aggiuntivi

- [Bug 43245](https://bugzilla.xamarin.com/show_bug.cgi?id=43245) – che sia impreciso "Download non riuscito. Scaricare {0} e inserirlo nella directory \\{1 \\}." e "installare pacchetti: '{0}' disponibile nel programma di installazione SDK" messaggi di errore correlati ai pacchetti deselezionati

### <a name="next-steps"></a>Passaggi successivi

Questo documento viene illustrato il comportamento corrente a partire da agosto 2016. La tecnica descritta in questo documento non è parte del gruppo di test stabile per Xamarin, pertanto è stato interrotto in futuro.

Per ulteriore assistenza, contattare Microsoft, o se il problema rimane anche dopo l'utilizzo di queste informazioni, vedere [quali opzioni di supporto sono disponibili per Xamarin?](~/cross-platform/troubleshooting/support-options.md) per informazioni sulle opzioni di contatto, i suggerimenti, nonché come Se necessario, archiviare un nuovo bug.

