<properties
    pageTitle="Unidade rolo um tutorial bola"
    description="Etapas para criar a unidade clássica implementar um jogo de bola que é um pré-requisito para todos os tutoriais de unidade de contrato de celular"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />

#<a id="unity-roll-a-ball"></a>Criar unidade implementar um jogo de bola

Este tutorial percorre as principais etapas para um ligeiramente modificada [unidade rolo um tutorial bola](http://unity3d.com/learn/tutorials/projects/roll-ball-tutorial). Este jogo de amostra consiste em um objeto de Esférico 'player' que é controlado pelo usuário aplicativo e o objetivo do jogo é 'coletar' objetos peças por se unindo o objeto de player com esses objetos de peças. Isso pressupõe familiaridade básica com o ambiente do editor de unidade. Se você tiver problemas, em seguida, consulte o tutorial completo. 

### <a name="setting-up-the-game"></a>Configurando o jogo
As etapas a seguir são do [tutorial de unidade](https://unity3d.com/learn/tutorials/projects/roll-a-ball/set-up?playlist=17141)

1. Abra o **Editor de unidade** e clique em **novo**. 
    
    ![][51] 
    
2. Forneça um **nome de projeto** & **local**, selecione **3D** e clique em **Criar projeto**.
    
    ![][52]

3. Salvar a cena padrão que acabou de criar como parte do novo projeto como com o nome **MiniGame** dentro de um novo ** \_cenas** pasta em pasta de **ativos** :
    
    ![][53]

4. Criar um **objeto 3D -> plano** como o campo de jogo e renomear este objeto de plano de **base**

    ![][1]

5. Redefina o componente de transformação para esse objeto de **Terra** para que fique na origem. 

    ![][3]

6. Desmarque a opção **Mostrar grade** menu **largue as** para o objeto de **Terra** .

    ![][4]

7. Atualizar o componente de **escala** para o objeto de **Terra** sejam [X = 2, Y = 1, Z = 2]. 

    ![][5]

8. Adicionar um novo **objeto 3D -> círculo** ao projeto e renomeie este objeto círculo como **Player**. 

    ![][6]

9. Selecione o objeto de **Player** e clique em **Redefinir transformação** semelhante ao objeto plano. 

10. Atualização **Transformação -> Posição -> coordenadas Y** componente para Y Player como 0,5.  

    ![][7]

11. Crie uma nova pasta chamada **materiais** no projeto onde vamos criar material para o player de cores. 

12. Crie um novo **Material** chamado de **plano de fundo** nesta pasta. 

    ![][8]

13. Atualize a cor do material atualizando a propriedade **Albedo** dele. Você pode selecionar os valores RGB de [0,32,64]. 

    ![][9]

14. Arraste esse material para o modo de exibição de cena para aplicar cor ao objeto **Terra** . 

    ![][10]

17. Finalmente atualizar o **Transformação -> rotação -> Y** para 60 no objeto luz direcional para clareza. 

    ![][12]

### <a name="moving-the-player"></a>Movendo o player
As etapas a seguir são do [tutorial de unidade](https://unity3d.com/learn/tutorials/projects/roll-a-ball/moving-the-player?playlist=17141)

1. Adicione um componente de **RigidBody** ao objeto **Player** . 

    ![][13]

2. Crie uma nova pasta chamada **Scripts** do projeto. 

3. Clique em **Adicionar componente -> Novo Script -> c# Script**. Nomeie **PlayerController**e clique em **criar e adicionar**. Isso criará e anexar um script ao objeto Player.  

    ![][14]

5. Mova esse script sob a pasta de **Scripts** do projeto. 

6. Abrir o script para edição no editor de scripts favorito, atualize o código de script com o seguinte código e salvá-lo. 

        using UnityEngine;
        using System.Collections;
        
        public class PlayerController : MonoBehaviour 
        {
            public float speed;
            private Rigidbody rb;
            void Start ()
            {
                rb = GetComponent<Rigidbody>();
            }
            void FixedUpdate ()
            {
                float moveHorizontal = Input.GetAxis ("Horizontal");
                float moveVertical = Input.GetAxis ("Vertical");
                Vector3 movement = new Vector3 (moveHorizontal, 0.0f, moveVertical);
                rb.AddForce (movement * speed);
            }
        }
    
8. Observe que o script acima usa uma propriedade de **velocidade** . No editor de unidade, atualize a propriedade speed para 10.  

    ![][15]

9. Clique em **Reproduzir** no Editor de unidade. Agora você deve ser capaz de controlar a bola usando o teclado e ele deve girar e mover-se. 

### <a name="moving-the-camera"></a>Movendo a câmera
As etapas a seguir são do [tutorial de unidade](https://unity3d.com/learn/tutorials/projects/roll-a-ball/moving-the-camera?playlist=17141) e irá vincular a **Câmera principal** para o objeto de **Player** . 

1. Atualizar o **Transform.Position** para ser X = 0, Y = 10.5, Z =-10.  
2. Atualizar o **Transform.Rotation** para ser X = 45, Y = 0, Z = 0.  

    ![][16]

2. Adicionar um novo script chamado **CameraController** para o **MainCamera** e mova-o na pasta Scripts. 

    ![][17]

3. Abra o script para edição e adicione o seguinte código nele:

        using UnityEngine;
        using System.Collections;
        
        public class CameraController : MonoBehaviour {
        
            public GameObject player;
        
            private Vector3 offset;
        
            void Start ()
            {
                offset = transform.position - player.transform.position;
            }
            
            void LateUpdate ()
            {
                transform.position = player.transform.position + offset;
            }
        }
    
5. No ambiente de unidade - arraste a variável de Player no slot Player para o objeto de câmera principal, para que os dois estão associados entre si. 

    ![][18]

6. Agora se você pressionar reproduzir no editor de unidade e girar o objeto Player bola você verá a câmera acompanhá-lo em movimento.  

### <a name="setting-up-the-play-area"></a>Configuração da área de reprodução
As etapas a seguir são do [tutorial de unidade](https://unity3d.com/learn/tutorials/projects/roll-a-ball/setting-up-the-play-area?playlist=17141). Vamos criar as paredes ao redor do zero, para que o objeto de Player bola não solte fora da área de reprodução no seu movimento. 

1. Clique em **criar -> Criar vazia -> jogo objeto** e chame-a como **paredes**

    ![][19]

2. Sob esse objeto paredes - criar um novo **objeto 3D -> cubo** e nomeie "Parede Oeste". 

    ![][20]

3. Atualize a **Transformação -> posição** e **Transformação -> escala** para esse objeto de parede Oeste. 

    ![][21]

4. Duplicar parede Oeste para criar uma **parede Leste** com a posição de transformação atualizados e escala. 

    ![][22]

5. Duplicar parede Leste para criar uma **parede do Norte** com a posição de transformação atualizado & escala. 

    ![][23]

6. Duplicar parede América do Norte e crie uma **parede Sul** com a posição de transformação atualizado & escala. 

    ![][24]

### <a name="creating-collectible-objects"></a>Criando objetos de peças
As etapas a seguir são do [tutorial de unidade](https://unity3d.com/learn/tutorials/projects/roll-a-ball/creating-collectables?playlist=17141). Vamos criar alguns objetos de aparência atraentes que serão formam o conjunto de objetos peças que o objeto de Player bola precisa coletar por se unindo com eles. 

1. Criar um novo **objeto de cubo 3D** e nomeie retirada. 

2. Ajustar a **Transformação -> rotação** & **Transformação -> escala** do objeto retirado. 

    ![][25]

3. Criar e anexar um **Novo c# Script** chamado **rotação** ao objeto retirado. Certifique-se de colocar o script na pasta Scripts. 

    ![][26]

4. Abra esse script para edição e atualizá-lo para ser o seguinte: 

        using UnityEngine;
        using System.Collections;
        
        public class Rotator : MonoBehaviour {
        
            void Update () 
            {
                transform.Rotate (new Vector3 (15, 30, 45) * Time.deltaTime);
            }
        }

5. O modo de reprodução no Editor de unidade e seu objetos retirada Mostrar acertar agora ser girando em seu eixo.

6. Criar uma nova pasta chamada **Prefabs** 

    ![][27]

7. Arraste o objeto **retirada** e coloque-o na pasta Prefabs.

    ![][28]

8. Crie um novo **objeto de jogo vazio** chamado **picapes**. Redefina sua posição para origem e, em seguida, arraste o objeto retirado sob esse objeto jogo.  

    ![][29]

9. Duplicar o objeto **retirada** e afaste-lo no objeto **Terra** em torno do objeto **Player** atualizando os valores de **X e Z do Transform.Position** adequadamente. 

    ![][30]

10. Crie um **novo material** chamado **retirada** e atualizá-lo para ser vermelho na cor atualizando a **propriedade de Albedo** semelhantes às que fez para atualizar o objeto de terra. 

    ![][31]

11. Aplique o material a todos os objetos de retirada 4.

    ![][32]

### <a name="collecting-the-pickup-objects"></a>Coletar os objetos retirados
As etapas a seguir são do [tutorial de unidade](https://unity3d.com/learn/tutorials/projects/roll-a-ball/collecting-pick-up-objects?playlist=17141). Atualizaremos o Player para que ele seja capaz de 'coletar' os objetos retirados por se unindo com eles. 

1. Abra o script **PlayerController** anexado ao objeto Player para edição e atualizá-lo para o seguinte:  

        using UnityEngine;
        using System.Collections;
        
        public class PlayerController : MonoBehaviour {
        
            public float speed;
        
            private Rigidbody rb;
        
            void Start ()
            {
                rb = GetComponent<Rigidbody>();
            }
        
            void FixedUpdate ()
            {
                float moveHorizontal = Input.GetAxis ("Horizontal");
                float moveVertical = Input.GetAxis ("Vertical");
        
                Vector3 movement = new Vector3 (moveHorizontal, 0.0f, moveVertical);
        
                rb.AddForce (movement * speed);
            }
        
            void OnTriggerEnter(Collider other) 
            {
                if (other.gameObject.CompareTag ("Pick Up"))
                {
                    other.gameObject.SetActive (false);
                }
            }
        }

2. Criar uma nova **marca** chamado **Escolha para cima** (ele deve corresponder Novidades no script)  

    ![][33]
    
    ![][34]

3. Aplica essa **marca** ao objeto retirada Prefab. 

    ![][35]

4. Habilite **IsTrigger** a caixa de seleção para o objeto Prefab.

    ![][36]

5. Adicione um corpo rígido retirada Prefab objeto. Para otimizar o desempenho atualizaremos a collider estático que usamos para um collider dinâmico. 

    ![][37]
  
6. Por fim, verifique a propriedade de **IsKinematic** para o objeto prefab. 

    ![][38]

7. Visitas **Reproduzir** no editor de unidade e você poderá jogo este **Reverter uma bola** movendo o objeto de Player usando teclas do teclado para entrada de direção. 

### <a name="updating-the-game-for-mobile-play"></a>Atualizando o jogo para reproduzir móvel
As seções acima concluíram o tutorial básico de unidade. Agora, vamos modificar o jogo para torná-lo dispositivo móvel amigável. Observe que usamos entrada do teclado para o jogo até o momento para teste. Agora, vamos modificá-la para que podemos controlar o player usando o movimento do telefone ou seja usando o acelerômetro como a entrada. 

Abra o script **PlayerController** para edição e atualize o método de **FixedUpdate** para usar o movimento do acelerômetro para mover o objeto Player. 

        void FixedUpdate()
        {
            //float moveHorizontal = Input.GetAxis("Horizontal");
            //float moveVertical = Input.GetAxis("Vertical");
            //Vector3 movement = new Vector3(moveHorizontal, 0.0f, moveVertical);
            rb.AddForce(Input.acceleration.x * Speed, 0, -Input.acceleration.z * Speed);
        }

Esse tutorial conclui uma criação de jogo básica com unidade e você pode implantar isso em um dispositivo de sua escolha para jogar. 

<!-- Images -->
[1]: ./media/mobile-engagement-unity-roll-a-ball/1.png  
[2]: ./media/mobile-engagement-unity-roll-a-ball/2.png
[3]: ./media/mobile-engagement-unity-roll-a-ball/3.png
[4]: ./media/mobile-engagement-unity-roll-a-ball/4.png
[5]: ./media/mobile-engagement-unity-roll-a-ball/5.png
[6]: ./media/mobile-engagement-unity-roll-a-ball/6.png
[7]: ./media/mobile-engagement-unity-roll-a-ball/7.png
[8]: ./media/mobile-engagement-unity-roll-a-ball/8.png
[9]: ./media/mobile-engagement-unity-roll-a-ball/9.png  
[10]: ./media/mobile-engagement-unity-roll-a-ball/10.png    
[11]: ./media/mobile-engagement-unity-roll-a-ball/11.png    
[12]: ./media/mobile-engagement-unity-roll-a-ball/12.png
[13]: ./media/mobile-engagement-unity-roll-a-ball/13.png
[14]: ./media/mobile-engagement-unity-roll-a-ball/14.png
[15]: ./media/mobile-engagement-unity-roll-a-ball/15.png
[16]: ./media/mobile-engagement-unity-roll-a-ball/16.png
[17]: ./media/mobile-engagement-unity-roll-a-ball/17.png
[18]: ./media/mobile-engagement-unity-roll-a-ball/18.png
[19]: ./media/mobile-engagement-unity-roll-a-ball/19.png    
[20]: ./media/mobile-engagement-unity-roll-a-ball/20.png    
[21]: ./media/mobile-engagement-unity-roll-a-ball/21.png    
[22]: ./media/mobile-engagement-unity-roll-a-ball/22.png    
[23]: ./media/mobile-engagement-unity-roll-a-ball/23.png    
[24]: ./media/mobile-engagement-unity-roll-a-ball/24.png    
[25]: ./media/mobile-engagement-unity-roll-a-ball/25.png    
[26]: ./media/mobile-engagement-unity-roll-a-ball/26.png    
[27]: ./media/mobile-engagement-unity-roll-a-ball/27.png    
[28]: ./media/mobile-engagement-unity-roll-a-ball/28.png    
[29]: ./media/mobile-engagement-unity-roll-a-ball/29.png    
[30]: ./media/mobile-engagement-unity-roll-a-ball/30.png    
[31]: ./media/mobile-engagement-unity-roll-a-ball/31.png    
[32]: ./media/mobile-engagement-unity-roll-a-ball/32.png    
[33]: ./media/mobile-engagement-unity-roll-a-ball/33.png    
[34]: ./media/mobile-engagement-unity-roll-a-ball/34.png    
[35]: ./media/mobile-engagement-unity-roll-a-ball/35.png    
[36]: ./media/mobile-engagement-unity-roll-a-ball/36.png    
[37]: ./media/mobile-engagement-unity-roll-a-ball/37.png    
[38]: ./media/mobile-engagement-unity-roll-a-ball/38.png    
[51]: ./media/mobile-engagement-unity-roll-a-ball/new-project.png
[52]: ./media/mobile-engagement-unity-roll-a-ball/new-project-properties.png
[53]: ./media/mobile-engagement-unity-roll-a-ball/save-scene.png

    
    
    
    
    
    
    
    
    
    
    
    
