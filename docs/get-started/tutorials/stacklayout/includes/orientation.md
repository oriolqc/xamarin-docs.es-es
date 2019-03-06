# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. En **MainPage.xaml**, modifique la declaración de [`StackLayout`](xref:Xamarin.Forms.StackLayout) de modo que alinee sus elementos secundarios en horizontal, en lugar de en vertical:

    ```xaml
    <StackLayout Margin="20,35,20,25"
                 Orientation="Horizontal">
        <Label Text="The StackLayout has its Margin property set, to control the rendering position of the StackLayout." />
        <Label Text="The Padding property can be set to specify the distance between the StackLayout and its children." />
        <Label Text="The Spacing property can be set to specify the distance between views in the StackLayout." />
    </StackLayout>
    ```

    Este código establece la propiedad [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) en [`Horizontal`](xref:Xamarin.Forms.StackOrientation.Horizontal).

1. En la barra de herramientas de Visual Studio, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador remoto de iOS elegido:

    [![Captura de pantalla de vistas secundarias en un diseño de pilas orientado horizontalmente en iOS y Android](../images/orientation.png "Diseño de pila que contiene instancias de etiquetas orientadas horizontalmente")](../images/orientation-large.png#lightbox "Diseño de pila que contiene instancias de etiquetas orientadas horizontalmente")

    Fíjese en que las instancias [`Label`](xref:Xamarin.Forms.Label) dentro de [`StackLayout`](xref:Xamarin.Forms.StackLayout) ahora están alineadas horizontalmente, en lugar de verticalmente.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. En **MainPage.xaml**, modifique la declaración de [`StackLayout`](xref:Xamarin.Forms.StackLayout) de modo que alinee sus elementos secundarios en horizontal, en lugar de en vertical:

    ```xaml
    <StackLayout Margin="20,35,20,25"
                 Orientation="Horizontal">
        <Label Text="The StackLayout has its Margin property set, to control the rendering position of the StackLayout." />
        <Label Text="The Padding property can be set to specify the distance between the StackLayout and its children." />
        <Label Text="The Spacing property can be set to specify the distance between views in the StackLayout." />
    </StackLayout>
    ```

    Este código establece la propiedad [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) en [`Horizontal`](xref:Xamarin.Forms.StackOrientation.Horizontal).

1. En la barra de herramientas de Visual Studio para Mac, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador de iOS elegido:

    [![Captura de pantalla de vistas secundarias en un diseño de pilas orientado horizontalmente en iOS y Android](../images/orientation.png "Diseño de pila que contiene instancias de etiquetas orientadas horizontalmente")](../images/orientation-large.png#lightbox "Diseño de pila que contiene instancias de etiquetas orientadas horizontalmente")

    Fíjese en que las instancias [`Label`](xref:Xamarin.Forms.Label) dentro de [`StackLayout`](xref:Xamarin.Forms.StackLayout) ahora están alineadas horizontalmente, en lugar de verticalmente.
