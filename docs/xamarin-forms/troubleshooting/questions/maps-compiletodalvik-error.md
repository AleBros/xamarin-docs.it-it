---
title: "perché My Xamarin.Forms . Il progetto Android di Maps non riesce con errore di primo livello di COMPILETODALVIK imprevisto? "
ms. Topic: risoluzione dei problemi di MS. prod: Novell MS. AssetID: C0251EB1-F509-47AD-98D6-846AF46425E5 ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 04/25/2017 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="why-does-my-xamarinformsmaps-android-project-fail-with-compiletodalvik-unexpected-top-level-error"></a>Perché My Xamarin.Forms . Il progetto Android di Maps non riesce con un errore di livello superiore imprevisto COMPILETODALVIK?

Questo errore può essere visualizzato nel riquadro degli errori di Visual Studio per Mac o nella finestra di output di compilazione di Visual Studio; nei progetti Android usando Xamarin.Forms . Mappe.

Questo problema viene risolto in genere aumentando le dimensioni dell'heap Java per il progetto Novell. Android. Per aumentare le dimensioni dell'heap, attenersi alla procedura seguente:

## <a name="visual-studio"></a>Visual Studio

1. Fare clic con il pulsante destro del mouse sul progetto Android & aprire le opzioni del progetto.
2. Vai a **Opzioni Android-> avanzate**
3. Nella casella di testo dimensioni heap Java immettere 1G.
4. Ricompilare il progetto.

![Screenshot delle opzioni del progetto di Visual Studio](maps-compiletodalvik-error-images/vsjavaheap.png "Opzioni di compilazione Android in Visual Studio")

## <a name="visual-studio-for-mac"></a>Visual Studio per Mac

1. Fare clic con il pulsante destro del mouse sul progetto Android & aprire le opzioni del progetto.
2. Passare a **Build-> Android Build-> Advanced**
3. Nella casella di testo dimensioni heap Java immettere 1G.
4. Ricompilare il progetto.  

![Screenshot delle opzioni del progetto Visual Studio per Mac](maps-compiletodalvik-error-images/xsjavaheap.png "Opzioni di compilazione Android in Visual Studio per Mac")
