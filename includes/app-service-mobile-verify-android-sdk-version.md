Devido ao desenvolvimento contínuo, a versão do SDK Android instalada no Android Studio pode não coincidir com a versão no código. O SDK do Android referenciados neste tutorial é versão 23, o mais recente no momento da gravação. O número da versão pode aumentar à medida que aparecem novas versões do SDK e recomendamos usando a versão mais recente disponível.

Dois sintomas de incompatibilidade de versão são:

1. Quando você construir ou reconstruir o projeto, você pode receber mensagens de erro de Gradle como "**Falha ao encontrar destino Google Inc.:Google APIs:n**".

2. Objetos Android padrão no código que resolva com base em `import` instruções podem gerar mensagens de erro.

Se um desses aparecer, a versão do SDK do Android instalado no Android Studio pode não coincidir com o destino SDK do projeto baixado.  Para verificar a versão, faça as seguintes alterações:


1. No Android Studio, clique em **Ferramentas** => **Android** => **Gerenciador de SDK**. Se você não instalou a versão mais recente da plataforma SDK, clique para instalá-lo. Anote o número da versão.

2. Na guia do Explorador de projeto, em **Gradle Scripts**, abra o arquivo **build.gradle (modeule: aplicativo)**. Certifique-se de que a **compileSdkVersion** e **buildToolsVersion** estão definidos para a versão mais recente do SDK instalada. As marcas podem ter esta aparência:
 
            compileSdkVersion 'Google Inc.:Google APIs:23'
            buildToolsVersion "23.0.2"
    
3. No Explorador de projeto Studio Android com o botão direito no nó do projeto, escolha **Propriedades**e, na coluna à esquerda, escolha **Android**. Certifique-se de que o **Destino de compilação de projeto** está definido como a mesma versão SDK a **targetSdkVersion**.

4. No Android Studio, o arquivo de manifesto já não é usado para especificar o destino SDK e a versão mínima do SDK, ao contrário de maiusculas e minúsculas com o Eclipse.
