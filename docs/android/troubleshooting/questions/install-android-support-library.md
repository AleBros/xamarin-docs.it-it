---
title: Come si possono installare manualmente le librerie di supporto Android necessarie per i pacchetti Xamarin.Android.Support?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: A9CB8CA8-8A6D-405E-B84C-A16CE452C0F7
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 99571e0b62592597bb1fffdc8d3ed8336fe050b2
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73026933"
---
# <a name="how-can-i-manually-install-the-android-support-libraries-required-by-the-xamarinandroidsupport-packages"></a>Come si possono installare manualmente le librerie di supporto Android necessarie per i pacchetti Xamarin.Android.Support?

## <a name="example-steps-for-xamarinandroidsupportv4"></a>Passaggi di esempio per Xamarin.Android.Support.v4 

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Scaricare il pacchetto Xamarin.Android.Support NuGet desiderato (ad esempio installandolo con il gestore di pacchetti NuGet).

Utilizzare `ildasm` per verificare la versione di android_m2repository.zip necessaria per il pacchetto NuGet:Use to check which version of **android_m2repository.zip** the NuGet package needs:

```cmd
ildasm /caverbal /text /item:Xamarin.Android.Support.v4 packages\Xamarin.Android.Support.v4.23.4.0.1\lib\MonoAndroid403\Xamarin.Android.Support.v4.dll | findstr SourceUrl
```

Output di esempio:

```cmd
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
```

Scarica **\_android m2repository.zip** da Google utilizzando l'URL restituito da **ildasm**. In alternativa, è possibile verificare quale versione del repository di _supporto Android_ attualmente è stato installato in Gestione SDK di Android:

!["Android SDK Manager che mostra Android Support Repository versione 32 installata"](install-android-support-library-images/sdk-extras.png)

Se la versione corrisponde a quella necessaria per il pacchetto NuGet, non è necessario scaricare nulla di nuovo. È invece possibile comprimere nuovamente la directory **m2repository** esistente che si trova in **extra\\android** nel _percorso SDK_ (come illustrato nella parte superiore della finestra di Gestione SDK di Android).

Calcolare l'hash MD5 dell'URL restituito da **ildasm**. Formattare la stringa risultante per utilizzare tutte le lettere maiuscole e senza spazi. Ad esempio, `$url` regolare la variabile in base alle esigenze e quindi eseguire le due righe seguenti (in base al codice originale di [C, da Xamarin.Android](https://github.com/xamarin/xamarin-android/blob/8e8a4dd90f26eb39172876cc52181b6639e20524/src/Xamarin.Android.Build.Tasks/Tasks/GetAdditionalResourcesFromAssemblies.cs#L208)) in PowerShell:

```powershell
$url = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip"
(([System.Security.Cryptography.MD5]::Create()).ComputeHash([System.Text.Encoding]::UTF8.GetBytes($url)) | %{ $_.ToString("X02") }) -join ""
```

Output di esempio:

```powershell
F16A3455987DBAE5783F058F19F7FCDF
```

Copiare **android\_m2repository.zip** nella cartella **\\cerprimice\\\\ %LOCALAPPDATA% Xamarin.** Rinominare il file per utilizzare l'hash MD5 del passaggio di calcolo dell'hash MD5 precedente. Ad esempio:

**%LOCALAPPDATA%\\Xamarin\\\\zip F16A3455987DBAE5783F058F19F7FCDF.zip**

(Facoltativo) Decomprimere il file nel **contenuto\\\\\\ %LOCALAPPDATA% Xamarin Xamarin.Android.Support.v4\\\\23.4.0.0** (creazione di una sottodirectory del contenuto **\\m2repository).** Se si ignora questo passaggio, la prima compilazione che utilizza la libreria richiederà più tempo perché sarà necessario completare questo passaggio.
Il numero di versione per la sottodirectory (**23.4.0.0** in questo esempio) non è esattamente lo stesso della versione del pacchetto NuGet. È possibile `ildasm` utilizzare per trovare il numero di versione corretto:You can use to find the correct version number:

```cmd
ildasm /caverbal /text /item:Xamarin.Android.Support.v4 packages\Xamarin.Android.Support.v4.23.4.0.1\lib\MonoAndroid403\Xamarin.Android.Support.v4.dll | findstr /C:"string 'Version'"
```

Output di esempio:

```cmd
property string 'Version' = string('23.4.0.0')}
property string 'Version' = string('23.4.0.0')}
property string 'Version' = string('23.4.0.0')}
```

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

Scaricare il pacchetto Xamarin.Android.Support NuGet desiderato (ad esempio installandolo con il gestore di pacchetti NuGet).

Fare doppio clic sull'assembly _Xamarin.Android.Support.v4_ nella sezione _Riferimenti_ del progetto Android in Visual Studio per Mac per aprire l'assembly nel Browser assembly. Assicurarsi che l'elenco a discesa _Lingua_ sia impostato su _C,_ quindi selezionare l'assembly _Xamarin.Android.Support.v4_ di primo livello dall'albero di navigazione di Assembly Browser. Individuare `SourceUrl` la proprietà sotto `IncludeAndroidResourcesFrom` `JavaLibraryReference` uno degli attributi or:

```csharp
[assembly: IncludeAndroidResourcesFrom ("./", PackageName = "Xamarin.Android.Support.v4", SourceUrl = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip", EmbeddedArchive = "m2repository/com/android/support/support-v4/23.4.0/support-v4-23.4.0.aar", Version = "23.4.0.0")]
```

Scarica **\_android m2repository.zip** da `SourceUrl` Google utilizzando il restituito da **ildasm**. In alternativa, è possibile verificare quale versione del repository di _supporto Android_ attualmente è stato installato in Gestione SDK di Android:

!["Android SDK Manager che mostra Android Support Repository versione 32 installata"](install-android-support-library-images/sdk-extras.png)

Se la versione corrisponde a quella necessaria per il pacchetto NuGet, non è necessario scaricare nulla di nuovo. È invece possibile comprimere nuovamente la directory **m2repository** esistente che si trova in **extra/android** nel _percorso SDK_ (come illustrato nella parte superiore della finestra di Gestione SDK di Android).

Calcolare l'hash MD5 dell'URL restituito da **ildasm**. Formattare la stringa risultante per utilizzare tutte le lettere maiuscole e senza spazi. Ad esempio, modificare la stringa URL in base alle esigenze e quindi eseguire il comando seguente in un prompt dei comandi **di Terminal.app:**

```bash
echo -n "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip" | md5 | tr '[:lower:]' '[:upper:]'
```

Un'altra opzione `csharp` consiste nell'utilizzare l'interprete per eseguire [lo stesso codice C , che Xamarin.Android stesso utilizza](https://github.com/xamarin/xamarin-android/blob/8e8a4dd90f26eb39172876cc52181b6639e20524/src/Xamarin.Android.Build.Tasks/Tasks/GetAdditionalResourcesFromAssemblies.cs#L208).
A tale scopo, `url` regolare la variabile in base alle esigenze e quindi eseguire il comando seguente in un prompt dei comandi **di Terminal.app:**

```bash
csharp -e 'var url = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip"; string.Concat((System.Security.Cryptography.MD5.Create().ComputeHash(System.Text.Encoding.UTF8.GetBytes(url))).Select(b => b.ToString("X02")))'
```

Output di esempio:

```bash
F16A3455987DBAE5783F058F19F7FCDF
```

Copia **\_android m2repository.zip** nella cartella **$HOME/.local/share/Xamarin/zips/.** Rinominare il file per utilizzare l'hash MD5 del passaggio di calcolo dell'hash MD5 precedente. Ad esempio:

**$HOME/.local/share/Xamarin/zips/F16A3455987DBAE5783F058F19F7FCDF.zip**

(Facoltativo) Decomprimere il file in: 

**$HOME/.local/share/Xamarin/Xamarin.Android.Support.v4/23.4.0.0/content/**

(creazione di una sottodirectory **content/m2repository).** Se si ignora questo passaggio, la prima compilazione che utilizza la libreria richiederà più tempo perché sarà necessario completare questo passaggio.

Il numero di versione per la sottodirectory (**23.4.0.0** in questo esempio) non è esattamente lo stesso della versione del pacchetto NuGet. Come nel passaggio **ildasm** precedente, è possibile utilizzare il Browser assembly in Visual Studio per Mac per trovare il numero di versione corretto. Cercare la `Version` proprietà in `IncludeAndroidResourcesFrom` uno `JavaLibraryReference` degli attributi or:

```csharp
[assembly: IncludeAndroidResourcesFrom ("./", PackageName = "Xamarin.Android.Support.v4", SourceUrl = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip", EmbeddedArchive = "m2repository/com/android/support/support-v4/23.4.0/support-v4-23.4.0.aar", Version = "23.4.0.0")]
```

-----

## <a name="additional-references"></a>Riferimenti aggiuntivi

- [Bug 43245](https://bugzilla.xamarin.com/show_bug.cgi?id=43245) – Impreciso "Download non riuscito. Si {0} prega di scaricare {1} e metterlo nella directory." e messaggi di errore{0}"Installare il pacchetto: ' ' disponibile nel programma di installazione SDK" relativi ai pacchetti Xamarin.Android.Support

### <a name="next-steps"></a>Passaggi successivi

Questo documento illustra il comportamento corrente a partire da agosto 2016. La tecnica descritta in questo documento non fa parte della suite di test stabile per Xamarin, quindi potrebbe rompersi in futuro.

Per ulteriore assistenza, per contattarci, o se questo problema rimane anche dopo aver utilizzato le informazioni di cui sopra, si prega di vedere Quali opzioni di [supporto sono disponibili per Xamarin?](~/cross-platform/troubleshooting/support-options.md) per informazioni sulle opzioni di contatto, suggerimenti, nonché come presentare un nuovo bug, se necessario.
