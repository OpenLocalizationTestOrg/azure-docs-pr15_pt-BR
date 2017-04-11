<properties 
    pageTitle="Suaves Streaming Tutorial de aplicativo da Windows Store | Microsoft Azure" 
    description="Saiba como usar os serviços de mídia do Azure para criar um aplicativo da Windows Store de c# com um controle de XML MediaElement fluxo suave reprodução conteúdo." 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"  
    ms.author="juliako"/>



#<a name="how-to-build-a-smooth-streaming-windows-store-application"></a>Como criar uma suave Streaming aplicativo da Windows Store

Suave Streaming cliente SDK para Windows 8 permite que os desenvolvedores criem aplicativos da Windows Store que podem reproduzir conteúdo de Streaming suave sob demanda e ao vivo. Além da reprodução básica de suavização Streaming de conteúdo, que o SDK também fornece recursos avançados, como proteção de Microsoft PlayReady, qualidade de áudio de nível de restrição, DVR Live, transmitir mudança, aguardando atualizações de status (como alterações de nível de qualidade) e eventos de erro e assim por diante. Para obter mais informações dos recursos com suporte, consulte as [notas de versão](http://www.iis.net/learn/media/smooth-streaming/smooth-streaming-client-sdk-for-windows-8-release-notes). Para obter mais informações, consulte [estrutura de Player para o Windows 8](http://playerframework.codeplex.com/). 

Este tutorial contém quatro lições:

1. Criar um aplicativo de repositório de Streaming de suavização básica
2. Adicionar um controle deslizante para controlar o andamento de mídia
3. Selecione fluxos de Streaming suave
4. Selecione as faixas Streaming suave

##<a name="prerequisites"></a>Pré-requisitos

- Windows 8 32 bits ou 64 bits. Você pode obter o [Windows 8 Enterprise avaliação](http://msdn.microsoft.com/evalcenter/jj554510.aspx) do MSDN.
- Visual Studio 2012 ou Visual Studio 2012 Express (ou uma versão posterior). Você pode obter a versão de avaliação do [aqui](http://www.microsoft.com/visualstudio/11/downloads).
- [Microsoft suave Streaming cliente SDK para Windows 8](http://visualstudiogallery.msdn.microsoft.com/04423d13-3b3e-4741-a01c-1ae29e84fea6?SRC=Homehttp://visualstudiogallery.msdn.microsoft.com/04423d13-3b3e-4741-a01c-1ae29e84fea6?SRC=Home).


A solução completa para cada lição pode ser baixada do exemplos de código de desenvolvedor do MSDN (Galeria de código): 

- [Lição 1](http://code.msdn.microsoft.com/Smooth-Streaming-Client-0bb1471f) - uma suave simples no Windows 8 Streaming Media Player, 
- [Lição 2](http://code.msdn.microsoft.com/A-simple-Windows-8-Smooth-ee98f63a) - um simples no Windows 8 suave Streaming Media Player com uma barra deslizante controlar, 
- [Lição 3](http://code.msdn.microsoft.com/A-Windows-8-Smooth-883c3b44) - uma suave do Windows 8 Streaming Media Player com seleção de fluxo,  
- [Lição 4](http://code.msdn.microsoft.com/A-Windows-8-Smooth-aa9e4907) - uma suave do Windows 8 Streaming Media Player com seleção de controle.

##<a name="lesson-1-create-a-basic-smooth-streaming-store-application"></a>Lição 1: Criar um aplicativo de repositório de Streaming de suavização básico

Nesta lição, você criará um aplicativo da Windows Store com um controle MediaElement para reproduzir fluxo suave conteúdo.  O aplicativo em execução é semelhante a:

![Exemplo de aplicativo da Windows Store suave Streaming][PlayerApplication]
 
Para obter mais informações sobre como desenvolver o aplicativo da Windows Store, consulte [desenvolver ótimos aplicativos para o Windows 8](http://msdn.microsoft.com/windows/apps/br229512.aspx). Esta lição contém os seguintes procedimentos:

1.  Criar um projeto da Windows Store
2.  Design de interface do usuário (XAML)
3.  Modificar o arquivo code-behind
4.  Compilar e testar o aplicativo

**Para criar um projeto da Windows Store**

1.  Execute o Visual Studio 2012 ou posterior.
2.  No menu **arquivo** , clique em **novo**e, em seguida, clique em **projeto**.
3.  Na caixa de diálogo Novo projeto, digite ou selecione os valores a seguir:

Nome|Valor
---|---
Grupo de modelo|Instalado/modelos/Visual c# / Windows Store
Modelo|Aplicativo em branco (XAML)
Nome|SSPlayer
Local|C:\SSTutorials
Nome da solução|SSPlayer
Criar diretório para solução|(selecionado)

4.  Clique em **Okey**.

**Para adicionar uma referência para o SDK do cliente Streaming suave**

1.  Do Solution Explorer, clique com botão direito **SSPlayer**e, em seguida, clique em **Adicionar referência**.
2.  Digite ou selecione os valores a seguir:

Nome|Valor
---|---
Grupo de referência|Extensões do Windows
Referência|Selecione Microsoft suave Streaming SDK cliente para o Windows 8 e o pacote do Microsoft Visual C++ Runtime
    
3.  Clique em **Okey**. 

Depois de adicionar as referências, você deve selecionar a plataforma de destino (x64 ou x86), adicionar referências não funcionará para configuração de plataforma Any CPU.  No solution explorer, você verá a marca de aviso amarela para esses adicionou referências.

**Para criar a interface de usuário do player**

1.  Do Solution Explorer, clique duas vezes **MainPage** para abri-lo no modo de exibição design.
2.  Localize o ** &lt;grade&gt; ** e ** &lt;/Grid&gt; ** marcas o arquivo XAML e, em seguida, cole o seguinte código entre as duas marcas:

        <Grid.RowDefinitions>
            <RowDefinition Height="20"/>    <!-- spacer -->
            <RowDefinition Height="50"/>    <!-- media controls -->
            <RowDefinition Height="100*"/>  <!-- media element -->
            <RowDefinition Height="80*"/>   <!-- media stream and track selection -->
            <RowDefinition Height="50"/>    <!-- status bar -->
        </Grid.RowDefinitions>
        
        <StackPanel Name="spMediaControl" Grid.Row="1" Orientation="Horizontal">
            <TextBlock x:Name="tbSource" Text="Source :  " FontSize="16" FontWeight="Bold" VerticalAlignment="Center" />
            <TextBox x:Name="txtMediaSource" Text="http://ecn.channel9.msdn.com/o9/content/smf/smoothcontent/elephantsdream/Elephants_Dream_1024-h264-st-aac.ism/manifest" FontSize="10" Width="700" Margin="0,4,0,10" />
            <Button x:Name="btnSetSource" Content="Set Source" Width="111" Height="43" Click="btnSetSource_Click"/>
            <Button x:Name="btnPlay" Content="Play" Width="111" Height="43" Click="btnPlay_Click"/>
            <Button x:Name="btnPause" Content="Pause"  Width="111" Height="43" Click="btnPause_Click"/>
            <Button x:Name="btnStop" Content="Stop"  Width="111" Height="43" Click="btnStop_Click"/>
            <CheckBox x:Name="chkAutoPlay" Content="Auto Play" Height="55" Width="Auto" IsChecked="{Binding AutoPlay, ElementName=mediaElement, Mode=TwoWay}"/>
            <CheckBox x:Name="chkMute" Content="Mute" Height="55" Width="67" IsChecked="{Binding IsMuted, ElementName=mediaElement, Mode=TwoWay}"/>
        </StackPanel>

        <StackPanel Name="spMediaElement" Grid.Row="2" Height="435" Width="1072"
                    HorizontalAlignment="Center" VerticalAlignment="Center">
            <MediaElement x:Name="mediaElement" Height="356" Width="924" MinHeight="225"
                          HorizontalAlignment="Center" VerticalAlignment="Center" 
                          AudioCategory="BackgroundCapableMedia" />
            <StackPanel Orientation="Horizontal">
                <Slider x:Name="sliderProgress" Width="924" Height="44"
                        HorizontalAlignment="Center" VerticalAlignment="Center"
                        PointerPressed="sliderProgress_PointerPressed"/>
                <Slider x:Name="sliderVolume" 
                        HorizontalAlignment="Right" VerticalAlignment="Center" Orientation="Vertical" 
                        Height="79" Width="148" Minimum="0" Maximum="1" StepFrequency="0.1" 
                        Value="{Binding Volume, ElementName=mediaElement, Mode=TwoWay}" 
                        ToolTipService.ToolTip="{Binding Value, RelativeSource={RelativeSource Mode=Self}}"/>
            </StackPanel>
        </StackPanel>

        <StackPanel Name="spStatus" Grid.Row="4" Orientation="Horizontal">
            <TextBlock x:Name="tbStatus" Text="Status :  " 
               FontSize="16" FontWeight="Bold" VerticalAlignment="Center" HorizontalAlignment="Center" />
            <TextBox x:Name="txtStatus" FontSize="10" Width="700" VerticalAlignment="Center"/>
        </StackPanel>

    O controle MediaElement é usado para mídia de reprodução. O controle deslizante denominado sliderProgress vai ser usado na próxima lição para controlar o andamento de mídia.

3.  Pressione **CTRL + S** para salvar o arquivo.

O controle MediaElement não suporta Streaming suave conteúdo de prontos. Para habilitar o suporte de Streaming suave, você deve registrar o manipulador de fluxo de bytes Streaming suave por extensão de nome de arquivo e tipo MIME.  Para registrar, use o método MediaExtensionManager.RegisterByteStremHandler do namespace botão.

Nesse arquivo XAML, alguns manipuladores de eventos estão associados com os controles.  Você deve definir esses manipuladores de eventos.

**Para modificar o arquivo code-behind**

1.  Do Solution Explorer, clique com botão direito **MainPage**e, em seguida, clique em **Exibir código**.
2.  Na parte superior do arquivo, adicione o seguinte usando instrução:

        using Windows.Media;

3.  No início da classe **MainPage** , adicione o membro de dados a seguir:

        private MediaExtensionManager extensions = new MediaExtensionManager();

4.  No final do construtor **MainPage** , adicione as duas linhas a seguintes:

        extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "text/xml");
        extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "application/vnd.ms-sstr+xml");
        
5.  No final da classe **MainPage** , após o seguinte código:

        #region UI Button Click Events
        private void btnPlay_Click(object sender, RoutedEventArgs e)
        {
            mediaElement.Play();
            txtStatus.Text = "MediaElement is playing ...";
        }
        
        private void btnPause_Click(object sender, RoutedEventArgs e)
        {
            mediaElement.Pause();
            txtStatus.Text = "MediaElement is paused";
        }
        
        private void btnSetSource_Click(object sender, RoutedEventArgs e)
        {
            sliderProgress.Value = 0;
            mediaElement.Source = new Uri(txtMediaSource.Text);
        
            if (chkAutoPlay.IsChecked == true)
            {
                txtStatus.Text = "MediaElement is playing ...";
            }
            else
            {
                txtStatus.Text = "Click the Play button to play the media source.";
            }
        }
        
        private void btnStop_Click(object sender, RoutedEventArgs e)
        {
            mediaElement.Stop();
            txtStatus.Text = "MediaElement is stopped";
        }
        
        private void sliderProgress_PointerPressed(object sender, PointerRoutedEventArgs e)
        {
            txtStatus.Text = "Seek to position " + sliderProgress.Value;
            mediaElement.Position = new TimeSpan(0, 0, (int)(sliderProgress.Value));
        }
        #endregion

    Manipulador de eventos sliderProgress_PointerPressed é definido aqui.  Há mais works fazer para que ele funcione, que será abordado na próxima lição deste tutorial.
6.  Pressione **CTRL + S** para salvar o arquivo.

O terminado que arquivo code-behind deve ter esta aparência:

![Codeview no Visual Studio de suavização Streaming aplicativo da Windows Store][CodeViewPic]

**Para compilar e testar o aplicativo**

1.  No menu **BUILD** , clique em **Gerenciador de configuração**.
2.  Alterar a **plataforma da solução ativa** para corresponder sua plataforma de desenvolvimento.
3.  Pressione **F6** para compilar o projeto. 
4.  Pressione **F5** para executar o aplicativo.
5.  Na parte superior do aplicativo, você pode usar a URL de Streaming suave padrão ou insira um diferente. 
6.  Clique em **Definir fonte**. Como **Reprodução automática** está ativado por padrão, a mídia deverá ser reproduzido automaticamente.  Você pode controlar a mídia usando os botões **Pausar** e **Parar** , **Reproduzir**.  Você pode controlar o volume de mídia usando o controle deslizante vertical.  No entanto o controle deslizante horizontal para controlar o andamento de mídia ainda não está totalmente implementado. 

Você concluiu lesson1.  Nesta lição, você usar um controle MediaElement para reprodução de conteúdo de Streaming suave.  Na próxima lição, você irá adicionar um controle deslizante para controlar o andamento do conteúdo Streaming suave.


##<a name="lesson-2-add-a-slider-bar-to-control-the-media-progress"></a>Lição 2: Adicionar um controle deslizante para controlar o andamento de mídia
Lição 1, você criou um aplicativo da Windows Store com um controle MediaElement XAML para reprodução de conteúdo de mídia de Streaming suave.  Ele vem algumas funções básicas de mídia como iniciar, parar e pausar.  Nesta lição, você irá adicionar um controle de barra deslizante para o aplicativo.

Neste tutorial, usaremos um temporizador atualize a posição do controle deslizante com base na posição atual do controle MediaElement.  O controle deslizante início e fim de tempo também precisam ser atualizados no caso de conteúdo ao vivo.  Isso pode ser tratado melhor no evento de atualização de origem adaptativa.

Fontes de mídia são objetos que geram dados de mídia.  A resolução de fonte leva um fluxo de bytes ou URL e cria a fonte de mídia apropriada para esse conteúdo.  O Solucionador de origem é o modo padrão para os aplicativos criar fontes de mídia. 

Esta lição contém os seguintes procedimentos:

1.  Registrar o manipulador de Streaming suave 
2.  Adicione os manipuladores de evento no nível de Gerenciador de fonte adaptativa
3.  Adicione os manipuladores de evento no nível de fonte adaptativa
4.  Adicionar manipuladores de eventos de MediaElement
5.  Adicionar o controle deslizante relacionado código de barras
6.  Compilar e testar o aplicativo

**Para registrar o manipulador de fluxo de bytes Streaming suave e passar o propertyset**

1.  Do Solution Explorer, clique com botão direito **MainPage**e, em seguida, clique em **Exibir código**.
2.  No início do arquivo, adicione o seguinte usando instrução:

        using Microsoft.Media.AdaptiveStreaming;

3.  No início da classe MainPage, adicione os seguintes membros de dados:

        private Windows.Foundation.Collections.PropertySet propertySet = new Windows.Foundation.Collections.PropertySet();             
        private IAdaptiveSourceManager adaptiveSourceManager;
    
4.  Dentro do construtor **MainPage** , adicione o seguinte código após a **isso. Inicializar Components();** linha e as linhas de código de registro gravadas na lição anterior:
    
        // Gets the default instance of AdaptiveSourceManager which manages Smooth 
        //Streaming media sources.
        adaptiveSourceManager = AdaptiveSourceManager.GetDefault();
        // Sets property key value to AdaptiveSourceManager default instance.
        // {A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}" must be hardcoded.
        propertySet["{A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}"] = adaptiveSourceManager;
    
5.  Dentro do construtor **MainPage** , modificar os dois métodos de RegisterByteStreamHandler para adicionar o quarto parâmetros:

        // Registers Smooth Streaming byte-stream handler for ".ism" extension and, 
        // "text/xml" and "application/vnd.ms-ss" mime-types and pass the propertyset. 
        // http://*.ism/manifest URI resources will be resolved by Byte-stream handler.
        extensions.RegisterByteStreamHandler(
            "Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", 
            ".ism", 
            "text/xml", 
            propertySet );
        extensions.RegisterByteStreamHandler(
            "Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", 
            ".ism", 
            "application/vnd.ms-sstr+xml", 
        propertySet);

6.  Pressione **CTRL + S** para salvar o arquivo.

**Para adicionar o manipulador de evento no nível do Gerenciador de fonte adaptativa**

1.  Do Solution Explorer, clique com botão direito **MainPage**e, em seguida, clique em **Exibir código**.
2.  Dentro da classe **MainPage** , adicione o membro de dados a seguir:

        private AdaptiveSource adaptiveSource = null;

3.  No final da classe **MainPage** , adicione o seguinte manipulador de eventos:
    
        #region Adaptive Source Manager Level Events
        private void mediaElement_AdaptiveSourceOpened(AdaptiveSource sender, AdaptiveSourceOpenedEventArgs args)
        {
            adaptiveSource = args.AdaptiveSource;
        }
        #endregion Adaptive Source Manager Level Events

4.  No final do construtor **MainPage** , adicione a seguinte linha para assinar o evento adaptativa fonte:
    
    + = adaptiveSourceManager.AdaptiveSourceOpenedEvent AdaptiveSourceOpenedEventHandler(mediaElement_AdaptiveSourceOpened) novo;

5.  Pressione **CTRL + S** para salvar o arquivo.

**Para adicionar manipuladores de evento no nível de fonte adaptativa**

1.  Do Solution Explorer, clique com botão direito **MainPage**e, em seguida, clique em **Exibir código**.
2.  Dentro da classe **MainPage** , adicione o membro de dados a seguir:
    
        private AdaptiveSourceStatusUpdatedEventArgs adaptiveSourceStatusUpdate; 
        private Manifest manifestObject;
    
3.  No final da classe **MainPage** , adicione os seguintes manipuladores de eventos:

        #region Adaptive Source Level Events
        private void mediaElement_ManifestReady(AdaptiveSource sender, ManifestReadyEventArgs args)
        {
            adaptiveSource = args.AdaptiveSource;
            manifestObject = args.AdaptiveSource.Manifest;
        }
        
        private void mediaElement_AdaptiveSourceStatusUpdated(AdaptiveSource sender, AdaptiveSourceStatusUpdatedEventArgs args)
        {
            adaptiveSourceStatusUpdate = args;
        }
        
        private void mediaElement_AdaptiveSourceFailed(AdaptiveSource sender, AdaptiveSourceFailedEventArgs args)
        {
            txtStatus.Text = "Error: " + args.HttpResponse;
        }
        #endregion Adaptive Source Level Events

4.  No final do método **mediaElement AdaptiveSourceOpened** , adicione o seguinte código para assinar os eventos:
    
        adaptiveSource.ManifestReadyEvent +=
                    mediaElement_ManifestReady;
        adaptiveSource.AdaptiveSourceStatusUpdatedEvent += 
            mediaElement_AdaptiveSourceStatusUpdated;
        adaptiveSource.AdaptiveSourceFailedEvent += 
            mediaElement_AdaptiveSourceFailed;
    
5.  Pressione **CTRL + S** para salvar o arquivo.

Os mesmos eventos estão disponíveis no adaptativa Manager nível da fonte, que pode ser usada para lidar com a funcionalidade comum para todos os elementos de mídia no aplicativo. Cada AdaptiveSource inclui seus próprios eventos e todos os eventos de AdaptiveSource serão propagados em AdaptiveSourceManager.

**Para adicionar manipuladores de eventos do elemento de mídia**

1.  Do Solution Explorer, clique com botão direito **MainPage**e, em seguida, clique em **Exibir código**.
2.  No final da classe **MainPage** , adicione os seguintes manipuladores de eventos:
    
        #region Media Element Event Handlers 
        private void MediaOpened(object sender, RoutedEventArgs e)
        {
            txtStatus.Text = "MediaElement opened";
        }
        
        private void MediaFailed(object sender, ExceptionRoutedEventArgs e)
        {
            txtStatus.Text= "MediaElement failed: " + e.ErrorMessage;
        }
        
        private void MediaEnded(object sender, RoutedEventArgs e)
        {
            txtStatus.Text ="MediaElement ended.";
        }
        #endregion Media Element Event Handlers

3.  No final do construtor **MainPage** , adicione o seguinte código em subscrito os eventos:
    
        mediaElement.MediaOpened += MediaOpened;
        mediaElement.MediaEnded += MediaEnded;
        mediaElement.MediaFailed += MediaFailed;

4.  Pressione **CTRL + S** para salvar o arquivo.

**Para adicionar a barra deslizante relacionados código**

1.  Do Solution Explorer, clique com botão direito **MainPage**e, em seguida, clique em **Exibir código**.
2.  No início do arquivo, adicione o seguinte usando instrução:
    
        using Windows.UI.Core;

3.  Dentro da classe **MainPage** , adicione os seguintes membros de dados:
    
        public static CoreDispatcher _dispatcher;
        private DispatcherTimer sliderPositionUpdateDispatcher;
    
4.  No final do construtor **MainPage** , adicione o seguinte código:

        _dispatcher = Window.Current.Dispatcher;
        PointerEventHandler pointerpressedhandler = new PointerEventHandler(sliderProgress_PointerPressed);
        sliderProgress.AddHandler(Control.PointerPressedEvent, pointerpressedhandler, true);    
    
5.  No final da classe **MainPage** , adicione o seguinte código:
    
        #region sliderMediaPlayer
        private double SliderFrequency(TimeSpan timevalue)
        {
            long absvalue = 0;
            double stepfrequency = -1;
        
            if (manifestObject != null)
            {
                absvalue = manifestObject.Duration - (long)manifestObject.StartTime;
            }
            else
            {
                absvalue = mediaElement.NaturalDuration.TimeSpan.Ticks;
            }
        
            TimeSpan totalDVRDuration = new TimeSpan(absvalue);
        
            if (totalDVRDuration.TotalMinutes >= 10 && totalDVRDuration.TotalMinutes < 30)
            {
               stepfrequency = 10;
            }
            else if (totalDVRDuration.TotalMinutes >= 30 
                     && totalDVRDuration.TotalMinutes < 60)
            {
                stepfrequency = 30;
            }
            else if (totalDVRDuration.TotalHours >= 1)
            {
                stepfrequency = 60;
            }
        
            return stepfrequency;
        }
        
        void updateSliderPositionoNTicks(object sender, object e)
        {
            sliderProgress.Value = mediaElement.Position.TotalSeconds;
        }
        
        public void setupTimer()
        {
            sliderPositionUpdateDispatcher = new DispatcherTimer();
            sliderPositionUpdateDispatcher.Interval = new TimeSpan(0, 0, 0, 0, 300);
            startTimer();
        }

        public void startTimer()
        {
            sliderPositionUpdateDispatcher.Tick += updateSliderPositionoNTicks;
            sliderPositionUpdateDispatcher.Start();
        }
        
        // Slider start and end time must be updated in case of live content
        public async void setSliderStartTime(long startTime)
        {
            await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
            {
                TimeSpan timespan = new TimeSpan(adaptiveSourceStatusUpdate.StartTime);
                double absvalue = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero);
                sliderProgress.Minimum = absvalue;
            });
        }
        
        // Slider start and end time must be updated in case of live content
        public async void setSliderEndTime(long startTime)
        {
            await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
            {
                TimeSpan timespan = new TimeSpan(adaptiveSourceStatusUpdate.EndTime);
                double absvalue = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero);
                sliderProgress.Maximum = absvalue;
            });
        }
        #endregion sliderMediaPlayer

    **Observação:** CoreDispatcher é usado para fazer alterações para o segmento de interface do usuário do não Thread de interface do usuário. No caso de gargalo no segmento do distribuidor, desenvolvedor pode optar por usar distribuidor fornecida por ele pretende atualizar elemento de interface do usuário.  Por exemplo:
    
        await sliderProgress.Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () => { TimeSpan 
          timespan = new TimeSpan(adaptiveSourceStatusUpdate.EndTime); 
        double absvalue  = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero); 
          sliderProgress.Maximum = absvalue; }); 
        

6.  No final do método **mediaElement_AdaptiveSourceStatusUpdated** , adicione o seguinte código:
    
        setSliderStartTime(args.StartTime);
        setSliderEndTime(args.EndTime);

7.  No final do método **MediaOpened** , adicione o seguinte código:
    
    sliderProgress.StepFrequency = SliderFrequency(mediaElement.NaturalDuration.TimeSpan); sliderProgress.Width = mediaElement.Width; setupTimer();

8.  Pressione **CTRL + S** para salvar o arquivo.

**Para compilar e testar o aplicativo**

1. Pressione **F6** para compilar o projeto. 
2.  Pressione **F5** para executar o aplicativo.
3.  Na parte superior do aplicativo, você pode usar a URL de Streaming suave padrão ou insira um diferente. 
4.  Clique em **Definir fonte**. 
5.  Teste a barra deslizante.

Você concluiu a lição 2.  Nesta lição você adicionou um controle deslizante ao aplicativo. 

##<a name="lesson-3-select-smooth-streaming-streams"></a>Lição 3: Selecione fluxos de Streaming suave
Streaming suave é capaz de transmitir conteúdo com várias faixas de áudio de idioma que estão selecionável pelos visualizadores.  Nesta lição, você irá habilitar visualizadores selecionar fluxos. Esta lição contém os seguintes procedimentos:

1. Modificar o arquivo XAML
2. Modificar o arquivo de behand de código
3. Compilar e testar o aplicativo


**Para modificar o arquivo XAML**

1. Do Solution Explorer, clique com botão direito **MainPage**e, em seguida, clique em **View Designer**.
2. Localize &lt;Grid.RowDefinitions&gt;e modificar o RowDefinitions para que eles se parece com:

        <Grid.RowDefinitions>            
            <RowDefinition Height="20"/>
            <RowDefinition Height="50"/>
            <RowDefinition Height="100"/>
            <RowDefinition Height="80"/>
            <RowDefinition Height="50"/>
        </Grid.RowDefinitions>

3. Dentro do &lt;grade&gt;&lt;/Grid&gt; marcas, adicione o seguinte código para definir um controle de caixa de listagem, para que os usuários podem ver a lista de fluxos de disponíveis e selecione fluxos:

        <Grid Name="gridStreamAndBitrateSelection" Grid.Row="3">
            <Grid.RowDefinitions>
                <RowDefinition Height="300"/>
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="250*"/>
                <ColumnDefinition Width="250*"/>
            </Grid.ColumnDefinitions>
            <StackPanel Name="spStreamSelection" Grid.Row="1" Grid.Column="0">
                <StackPanel Orientation="Horizontal">
                    <TextBlock Name="tbAvailableStreams" Text="Available Streams:" FontSize="16" VerticalAlignment="Center"></TextBlock>
                    <Button Name="btnChangeStreams" Content="Submit" Click="btnChangeStream_Click"/>
                </StackPanel>
                <ListBox x:Name="lbAvailableStreams" Height="200" Width="200" Background="Transparent" HorizontalAlignment="Left" 
                    ScrollViewer.VerticalScrollMode="Enabled" ScrollViewer.VerticalScrollBarVisibility="Visible">
                    <ListBox.ItemTemplate>
                        <DataTemplate>
                            <CheckBox Content="{Binding Name}" IsChecked="{Binding isChecked, Mode=TwoWay}" />
                        </DataTemplate>
                    </ListBox.ItemTemplate>
                </ListBox>
            </StackPanel>
        </Grid>

4. Pressione **CTRL + S** para salvar as alterações.


**Para modificar o arquivo code-behind**

1. Do Solution Explorer, clique com botão direito **MainPage**e, em seguida, clique em **Exibir código**.
2. Dentro do espaço de SSPlayer, adicione uma nova classe: #region classe fluxo
    
        public class Stream
        {
            private IManifestStream stream;
            public bool isCheckedValue;
            public string name;
    
            public string Name
            {
                get { return name; }
                set { name = value; }
            }
    
            public IManifestStream ManifestStream
            {
                get { return stream; }
                set { stream = value; }
            }
    
            public bool isChecked
            {
                get { return isCheckedValue; }
                set
                {
                    // mMke the video stream always checked.
                    if (stream.Type == MediaStreamType.Video)
                    {
                        isCheckedValue = true;
                    }
                    else
                    {
                        isCheckedValue = value;
                    }
                }
            }
    
            public Stream(IManifestStream streamIn)
            {
                stream = streamIn;
                name = stream.Name;
            }
        }
        #endregion class Stream

3. No início da classe MainPage, adicione as seguintes definições de variável:

        private List<Stream> availableStreams;
        private List<Stream> availableAudioStreams;
        private List<Stream> availableTextStreams;
        private List<Stream> availableVideoStreams;

4. Dentro da classe MainPage, adicione o seguinte região:

        #region stream selection
        ///<summary>
        ///Functionality to select streams from IManifestStream available streams
        /// </summary>
        
        // This function is called from the mediaElement_ManifestReady event handler 
        // to retrieve the streams and populate them to the local data members.
        public void getStreams(Manifest manifestObject)
        {
            availableStreams = new List<Stream>();
            availableVideoStreams = new List<Stream>();
            availableAudioStreams = new List<Stream>();
            availableTextStreams = new List<Stream>();
        
            try
            {
                for (int i = 0; i<manifestObject.AvailableStreams.Count; i++)
                {
                    Stream newStream = new Stream(manifestObject.AvailableStreams[i]);
                    newStream.isChecked = false;
        
                    //populate the stream lists based on the types
                    availableStreams.Add(newStream);
        
                    switch (newStream.ManifestStream.Type)
                    {
                        case MediaStreamType.Video:
                            availableVideoStreams.Add(newStream);
                            break;
                        case MediaStreamType.Audio:
                            availableAudioStreams.Add(newStream);
                            break;
                        case MediaStreamType.Text:
                            availableTextStreams.Add(newStream);
                            break;
                    }
        
                    // Select the default selected streams from the manifest.
                    for (int j = 0; j<manifestObject.SelectedStreams.Count; j++)
                    {
                        string selectedStreamName = manifestObject.SelectedStreams[j].Name;
                        if (selectedStreamName.Equals(newStream.Name))
                        {
                            newStream.isChecked = true;
                            break;
                        }
                    }
                }
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }
        }
        
        // This function set the list box ItemSource
        private async void refreshAvailableStreamsListBoxItemSource()
        {
            try
            {
                //update the stream check box list on the UI
                await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, ()
                    => { lbAvailableStreams.ItemsSource = availableStreams; });
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }
        }
        
        // This function creates a selected streams list
        private void createSelectedStreamsList(List<IManifestStream> selectedStreams)
        {
            bool isOneVideoSelected = false;
            bool isOneAudioSelected = false;
        
            // Only one video stream can be selected
            for (int j = 0; j<availableVideoStreams.Count; j++)
            {
                if (availableVideoStreams[j].isChecked && (!isOneVideoSelected))
                {
                    selectedStreams.Add(availableVideoStreams[j].ManifestStream);
                    isOneVideoSelected = true;
                }
            }
        
            // Select the frist video stream from the list if no video stream is selected
            if (!isOneVideoSelected)
            {
                availableVideoStreams[0].isChecked = true;
                selectedStreams.Add(availableVideoStreams[0].ManifestStream);
            }
        
            // Only one audio stream can be selected
            for (int j = 0; j<availableAudioStreams.Count; j++)
            {
                if (availableAudioStreams[j].isChecked && (!isOneAudioSelected))
                {
                    selectedStreams.Add(availableAudioStreams[j].ManifestStream);
                    isOneAudioSelected = true;
                    txtStatus.Text = "The audio stream is changed to " + availableAudioStreams[j].ManifestStream.Name;
                }
            }
        
            // Select the frist audio stream from the list if no audio steam is selected.
            if (!isOneAudioSelected)
            {
                availableAudioStreams[0].isChecked = true;
                selectedStreams.Add(availableAudioStreams[0].ManifestStream);
            }
        
            // Multiple text streams are supported.
            for (int j = 0; j < availableTextStreams.Count; j++)
            {
                if (availableTextStreams[j].isChecked)
                {
                    selectedStreams.Add(availableTextStreams[j].ManifestStream);
                }
            }
        }
        
        // Change streams on a smooth streaming presentation with multiple video streams.
        private async void changeStreams(List<IManifestStream> selectStreams)
        {
            try
            {
                IReadOnlyList<IStreamChangedResult> returnArgs =
                    await manifestObject.SelectStreamsAsync(selectStreams);
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }
        }
        #endregion stream selection

5. Localize o método mediaElement_ManifestReady, anexe o código a seguir no final da função:
    
        getStreams(manifestObject);
        refreshAvailableStreamsListBoxItemSource();

    Para que quando MediaElement manifesto estiver pronto, o código obtém uma lista dos fluxos disponíveis e preenche a caixa de listagem de interface do usuário com a lista.

6. Dentro da classe MainPage, localize a interface do usuário botões clique região de eventos e, em seguida, adicione a seguinte definição de função:

        private void btnChangeStream_Click(object sender, RoutedEventArgs e)
        {
            List<IManifestStream> selectedStreams = new List<IManifestStream>();

            // Create a list of the selected streams
            createSelectedStreamsList(selectedStreams);

            // Change streams on the presentation
            changeStreams(selectedStreams);
        }

**Para compilar e testar o aplicativo**

1. Pressione **F6** para compilar o projeto. 
2.  Pressione **F5** para executar o aplicativo.
3.  Na parte superior do aplicativo, você pode usar a URL de Streaming suave padrão ou insira um diferente. 
4.  Clique em **Definir fonte**. 
5.  O idioma padrão é audio_eng. Tente alternar entre audio_eng e audio_es. Toda vez que, que você selecione um novo fluxo, você deve clicar no botão Enviar.

Você concluiu a lição 3.  Nesta lição, você adicionar a funcionalidade para escolher fluxos.

##<a name="lesson-4-select-smooth-streaming-tracks"></a>Lição 4: Selecione as faixas Streaming suave
Uma apresentação Streaming suave pode conter vários arquivos de vídeo codificados com níveis de qualidade diferente (taxas de bits) e resoluções. Nesta lição, você irá habilitar aos usuários selecionar faixas. Esta lição contém os seguintes procedimentos:

1. Modificar o arquivo XAML
2. Modificar o arquivo de behand de código
3. Compilar e testar o aplicativo

**Para modificar o arquivo XAML**

1. Do Solution Explorer, clique com botão direito **MainPage**e, em seguida, clique em **View Designer**.
2. Localize o &lt;grade&gt; marca com o nome **gridStreamAndBitrateSelection**, anexe o código a seguir no final da marca:

        <StackPanel Name="spBitRateSelection" Grid.Row="1" Grid.Column="1">
         <StackPanel Orientation="Horizontal">
             <TextBlock Name="tbBitRate" Text="Available Bitrates:" FontSize="16" VerticalAlignment="Center"/>
             <Button Name="btnChangeTracks" Content="Submit" Click="btnChangeTrack_Click" />
         </StackPanel>
         <ListBox x:Name="lbAvailableVideoTracks" Height="200" Width="200" Background="Transparent" HorizontalAlignment="Left" 
                  ScrollViewer.VerticalScrollMode="Enabled" ScrollViewer.VerticalScrollBarVisibility="Visible">
             <ListBox.ItemTemplate>
                 <DataTemplate>
                     <CheckBox Content="{Binding Bitrate}" IsChecked="{Binding isChecked, Mode=TwoWay}"/>
                 </DataTemplate>
             </ListBox.ItemTemplate>
         </ListBox>
        </StackPanel>

3. Pressione **CTRL + S** para salvar as alterações de he


**Para modificar o arquivo code-behind**

1. Do Solution Explorer, clique com botão direito **MainPage**e, em seguida, clique em **Exibir código**.
2. Dentro do espaço de SSPlayer, adicione uma nova classe:
    
        #region class Track
        public class Track
        {
            private IManifestTrack trackInfo;
            public string _bitrate;
            public bool isCheckedValue;
    
            public IManifestTrack TrackInfo
            {
                get { return trackInfo; }
                set { trackInfo = value; }
            }
    
            public string Bitrate
            {
                get { return _bitrate; }
                set { _bitrate = value; }
            }
    
            public bool isChecked
            {
                get { return isCheckedValue; }
                set
                {
                    isCheckedValue = value;
                }
            }
    
            public Track(IManifestTrack trackInfoIn)
            {
                trackInfo = trackInfoIn;
                _bitrate = trackInfoIn.Bitrate.ToString();
            }
            //public Track() { }
        }
        #endregion class Track

3. No início da classe MainPage, adicione as seguintes definições de variável:
    
        private List<Track> availableTracks;

4. Dentro da classe MainPage, adicione o seguinte região:
    
        #region track selection
        /// <summary>
        /// Functionality to select video streams
        /// </summary>

        /// This Function gets the tracks for the selected video stream
        public void getTracks(Manifest manifestObject)
        {
            availableTracks = new List<Track>();

            IManifestStream videoStream = getVideoStream();
            IReadOnlyList<IManifestTrack> availableTracksLocal = videoStream.AvailableTracks;
            IReadOnlyList<IManifestTrack> selectedTracksLocal = videoStream.SelectedTracks;

            try
            {
                for (int i = 0; i < availableTracksLocal.Count; i++)
                {
                    Track thisTrack = new Track(availableTracksLocal[i]);
                    thisTrack.isChecked = true;

                    for (int j = 0; j < selectedTracksLocal.Count; j++)
                    {
                        string selectedTrackName = selectedTracksLocal[j].Bitrate.ToString();
                        if (selectedTrackName.Equals(thisTrack.Bitrate))
                        {
                            thisTrack.isChecked = true;
                            break;
                        }
                    }
                    availableTracks.Add(thisTrack);
                }
            }
            catch (Exception e)
            {
                txtStatus.Text = e.Message;
            }
        }

        // This function gets the video stream that is playing
        private IManifestStream getVideoStream()
        {
            IManifestStream videoStream = null;
            for (int i = 0; i < manifestObject.SelectedStreams.Count; i++)
            {
                if (manifestObject.SelectedStreams[i].Type == MediaStreamType.Video)
                {
                    videoStream = manifestObject.SelectedStreams[i];
                    break;
                }
            }
            return videoStream;
        }

        // This function set the UI list box control ItemSource
        private async void refreshAvailableTracksListBoxItemSource()
        {
            try
            {
                // Update the track check box list on the UI 
                await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, ()
                    => { lbAvailableVideoTracks.ItemsSource = availableTracks; });
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }        
        }

        // This function creates a list of the selected tracks.
        private void createSelectedTracksList(List<IManifestTrack> selectedTracks)
        {
            // Create a list of selected tracks
            for (int j = 0; j < availableTracks.Count; j++)
            {
                if (availableTracks[j].isCheckedValue == true)
                {
                    selectedTracks.Add(availableTracks[j].TrackInfo);
                }
            }
        }

        // This function selects the tracks based on user selection 
        private void changeTracks(List<IManifestTrack> selectedTracks)
        {
            IManifestStream videoStream = getVideoStream();
            try
            {
                videoStream.SelectTracks(selectedTracks);
            }
            catch (Exception ex)
            {
                txtStatus.Text = ex.Message;
            }
        }
        #endregion track selection

5. Localize o método mediaElement_ManifestReady, anexe o código a seguir no final da função:

        getTracks(manifestObject);
        refreshAvailableTracksListBoxItemSource();

6. Dentro da classe MainPage, localize a interface do usuário botões clique região de eventos e, em seguida, adicione a seguinte definição de função:

        private void btnChangeStream_Click(object sender, RoutedEventArgs e)
        {
            List<IManifestStream> selectedStreams = new List<IManifestStream>();

            // Create a list of the selected streams
            createSelectedStreamsList(selectedStreams);

            // Change streams on the presentation
            changeStreams(selectedStreams);
        }

**Para compilar e testar o aplicativo**

1. Pressione **F6** para compilar o projeto. 
2.  Pressione **F5** para executar o aplicativo.
3.  Na parte superior do aplicativo, você pode usar a URL de Streaming suave padrão ou insira um diferente. 
4.  Clique em **Definir fonte**. 
5.  Por padrão, todas as faixas do fluxo de vídeo são selecionadas. Para testar as alterações de taxa de bits, você pode selecionar a taxa de bits mais baixa disponível e, em seguida, selecione a taxa de bits mais alta disponível. Você deve clicar em enviar após cada alteração.  Você pode ver as alterações de qualidade do vídeo.

Você concluiu lição 4.  Nesta lição, você adicionar a funcionalidade para escolher faixas.


##<a name="media-services-learning-paths"></a>Caminhos de aprendizado de serviços de mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="other-resources"></a>Outros recursos:
- [Como criar um aplicativo de suavização Streaming Windows 8 JavaScript com recursos avançados](http://blogs.iis.net/cenkd/archive/2012/08/10/how-to-build-a-smooth-streaming-windows-8-javascript-application-with-advanced-features.aspx)
- [Visão geral técnica Streaming suave](http://www.iis.net/learn/media/on-demand-smooth-streaming/smooth-streaming-technical-overview)

[PlayerApplication]: ./media/media-services-build-smooth-streaming-apps/SSClientWin8-1.png
[CodeViewPic]: ./media/media-services-build-smooth-streaming-apps/SSClientWin8-2.png
 
