---
title: Modifica dei parametri di memoria Java per Android Designer
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 62FAF21C-8090-4AF3-9D88-05A4CFCAFFDC
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/02/2018
ms.openlocfilehash: 9c564789f704180e9acc9f96dcba5e7d6eb20634
ms.sourcegitcommit: 6be6374664cd96a7d924c2e0c37aeec4adf8be13
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2018
ms.locfileid: "51617241"
---
# <a name="adjusting-java-memory-parameters-for-the-android-designer"></a>Modifica dei parametri di memoria Java per Android Designer

I parametri di memoria predefiniti che vengono usati quando si avvia il `java` elaborare per Android designer potrebbe essere incompatibile con alcune configurazioni di sistema.

A partire da Xamarin Studio 5.7.2.7 (e versioni successive, Visual Studio per Mac) e Visual Studio Tools per Xamarin 3.9.344, queste impostazioni possono essere personalizzate in base al progetto.

## <a name="new-android-designer-properties-and-corresponding-java-options"></a>Nuove proprietà della finestra di progettazione Android e le opzioni Java

I nomi delle proprietà seguenti corrispondono a java indicato [opzione della riga di comando](http://docs.oracle.com/javase/7/docs/technotes/tools/windows/java.html)

- **AndroidDesignerJavaRendererMinMemory** Xms -

- **AndroidDesignerJavaRendererMaxMemory** - Xmx

- **AndroidDesignerJavaRendererPermSize** - XX: MaxPermSize


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1.  Aprire la soluzione in Visual Studio.

2.  Selezionare ciascun progetto Android uno alla volta in Esplora soluzioni e fare clic su [Mostra tutti i file](https://docs.microsoft.com/en-us/previous-versions/visualstudio/visual-studio-2008/4afxey9h(v=vs.90)) due volte su ogni progetto. È possibile ignorare i progetti che non contengono alcuna `.axml` i file di layout. Questo passaggio garantisce che ogni directory di progetto contiene un `.csproj.user` file.

3.  Uscire da Visual Studio.

4.  Individuare il `.csproj.user` file per ognuno dei progetti dal passaggio 2.

5.  Modificare ognuno `.csproj.user` file in un editor di testo.

6.  Aggiungere una o tutte le nuove proprietà di memoria della finestra di progettazione Android all'interno di un `<PropertyGroup>` elemento. È possibile usare un oggetto esistente `<PropertyGroup>` o crearne uno nuovo. Ecco un esempio completo `.csproj.user` file che include tutte le 3 attributi impostati sui valori predefiniti:

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

7.  Salvare e chiudere tutte le aggiornato `.csproj.user` file.

8.  Riavviare Visual Studio e riaperto la soluzione.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1.  Aprire la soluzione in Visual Studio per Mac per assicurarsi che la directory della soluzione contiene un `.userprefs` file.

2.  Uscire da Visual Studio per Mac.

3.  Individuare il `.userprefs` file nella directory della soluzione.

4.  Modificare il `.userprefs` file in un editor di testo.

5.  Individuare l'elemento XML esistente con il formato seguente. L'ultima parte di questo nome di elemento corrisponderà al nome del progetto: "AndroidApplication1" in questo esempio:

    ```xml
    <MonoDevelop.Ide.ItemProperties.AndroidApplication1 ... >
    ```

6.  Se il `<MonoDevelop.Ide.ItemProperties.AndroidApplication1 ... >` l'elemento non esiste, crearla in un punto qualsiasi all'interno che li racchiude `<Properties>` elemento. Assicurarsi di sostituire "AndroidApplication1" con il nome del progetto.

7.  Aggiungere una o tutte le nuove proprietà della memoria della finestra di progettazione Android come attributi nell'elemento. Ecco un esempio completo `.userprefs` file che include tutte le 3 attributi impostati sui valori predefiniti:

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

8.  Ripetere i passaggi da 5 a 7 per ogni progetto Android nella soluzione che contiene gli eventuali `.axml` i file di layout. (Vale a dire, aggiungerne uno `<MonoDevelop.Ide.ItemProperties.ProjectName>` (elemento) per ogni progetto.)

9.  Salvare e chiudere il `.userprefs` file.

10. Riavviare Visual Studio per Mac e riaprire la soluzione.

-----

