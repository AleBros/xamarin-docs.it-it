---
title: Modificare i parametri della memoria Java per la finestra di progettazione Android
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 62FAF21C-8090-4AF3-9D88-05A4CFCAFFDC
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/02/2018
ms.openlocfilehash: 691be280b80e379863cc09d0f1bba0ff5882cf21
ms.sourcegitcommit: a7febc19102209b21e0696256c324f366faa444e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2018
ms.locfileid: "34732921"
---
# <a name="adjusting-java-memory-parameters-for-the-android-designer"></a>Modificare i parametri della memoria Java per la finestra di progettazione Android

I parametri di memoria predefiniti che vengono utilizzati quando si avvia il `java` elaborare per la finestra di progettazione Android potrebbe non essere compatibile con alcune configurazioni di sistema.

A partire da Xamarin Studio 5.7.2.7 (e versioni successive, Visual Studio per Mac) e Visual Studio Tools per Xamarin 3.9.344, queste impostazioni possono essere personalizzate in base al progetto.

## <a name="new-android-designer-properties-and-corresponding-java-options"></a>Nuove proprietà della finestra di progettazione Android e le opzioni di linguaggio

I seguenti nomi di proprietà corrispondono di java indicato [opzione della riga di comando](http://docs.oracle.com/javase/7/docs/technotes/tools/windows/java.html)

- **AndroidDesignerJavaRendererMinMemory** Xms -

- **AndroidDesignerJavaRendererMaxMemory** - Xmx

- **AndroidDesignerJavaRendererPermSize** - XX: MaxPermSize


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1.  Aprire la soluzione in Visual Studio.

2.  Selezionare ciascun progetto Android uno alla volta in Esplora soluzioni e fare clic su [Mostra tutti i file](https://msdn.microsoft.com/en-us/library/4afxey9h.aspx) due volte su ogni progetto. È possibile ignorare i progetti che non contengano `.axml` file di layout. Questo passaggio garantisce che ogni directory di progetto contiene un `.csproj.user` file.

3.  Uscire da Visual Studio.

4.  Individuare il `.csproj.user` file per ognuno dei progetti dal passaggio 2.

5.  Modificare ciascun `.csproj.user` file in un editor di testo.

6.  Aggiungere uno o più delle nuove proprietà di memoria della finestra di progettazione Android all'interno di un `<PropertyGroup>` elemento. È possibile utilizzare un oggetto esistente `<PropertyGroup>` o crearne uno nuovo. Ecco un esempio completo `.csproj.user` file che include tutti gli 3 attributi è impostato su valori predefiniti:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <Project ToolsVersion="12.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
       <PropertyGroup>
         <ProjectView>ProjectFiles</ProjectView>
       </PropertyGroup>
       <PropertyGroup>
         <AndroidDesignerJavaRendererMinMemory>128m</AndroidDesignerJavaRendererMinMemory>
         <AndroidDesignerJavaRendererMaxMemory>750m</AndroidDesignerJavaRendererMaxMemory>
         <AndroidDesignerJavaRendererPermSize>350m</AndroidDesignerJavaRendererPermSize>
       </PropertyGroup>
    </Project>
    ```

7.  Salvare e chiudere tutte aggiornato `.csproj.user` file.

8.  Riavviare Visual Studio e riaprire la soluzione.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1.  Aprire la soluzione in Visual Studio per Mac garantire la directory di soluzione contenente un `.userprefs` file.

2.  Chiudere Visual Studio per Mac.

3.  Individuare il `.userprefs` file nella directory della soluzione.

4.  Modificare il `.userprefs` file in un editor di testo.

5.  Individuare l'elemento XML esistente con il formato seguente. L'ultima parte del nome di questo elemento corrisponderà al nome del progetto: "AndroidApplication1" in questo esempio:

    ```xml
    <MonoDevelop.Ide.ItemProperties.AndroidApplication1 ... >
    ```

6.  Se il `<MonoDevelop.Ide.ItemProperties.AndroidApplication1 ... >` l'elemento non esiste, crearla in un punto qualsiasi all'interno di inclusione `<Properties>` elemento. Assicurarsi di sostituire "AndroidApplication1" con il nome del progetto.

7.  Aggiungere uno o più delle nuove proprietà di memoria della finestra di progettazione Android come attributi dell'elemento. Ecco un esempio completo `.userprefs` file che include tutti gli 3 attributi è impostato su valori predefiniti:

    ```xml
    <Properties StartupItem="AndroidApplication1\AndroidApplication1.csproj">
      <MonoDevelop.Ide.Workspace ActiveConfiguration="Debug" PreferredExecutionTarget="Android.SelectDevice" />
      <MonoDevelop.Ide.Workbench />
      <MonoDevelop.Ide.DebuggingService.Breakpoints>
        <BreakpointStore />
      </MonoDevelop.Ide.DebuggingService.Breakpoints>
      <MonoDevelop.Ide.DebuggingService.PinnedWatches />
      <MonoDevelop.Ide.ItemProperties.AndroidApplication1 AndroidDesignerJavaRendererMinMemory="128m" AndroidDesignerJavaRendererMaxMemory="750m" AndroidDesignerJavaRendererPermSize="350m" />
    </Properties>
    ```

8.  Ripetere i passaggi da 5 a 7 per ogni progetto nella soluzione che contiene gli eventuali Android `.axml` file di layout. (Ovvero, aggiungere uno `<MonoDevelop.Ide.ItemProperties.ProjectName>` elemento per ogni progetto.)

9.  Salvare e chiudere il `.userprefs` file.

10. Riavviare Visual Studio per Mac e riaprire la soluzione.

-----

