<properties
    pageTitle="Modo de exibição SAML retornado pelo serviço de controle de acesso (Java)"
    description="Saiba como exibir SAML retornado pelo serviço de controle de acesso em aplicativos Java hospedados no Azure."
    services="active-directory" 
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor="" />

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm" />

# <a name="how-to-view-saml-returned-by-the-azure-access-control-service"></a>Como exibir SAML retornado pelo serviço de controle de acesso do Azure

Este guia mostrará como exibir o subjacente segurança declaração SAML Markup Language () retornada ao aplicativo por serviço de controle de acesso para Azure (ACS). Guia se baseia no tópico [como autenticar usuários da Web com o Azure acesso controle serviço usando Eclipse][] , fornecendo código que exibe as informações de SAML. O aplicativo concluído terá aparência semelhante à seguinte.

![Exemplo de saída SAML][saml_output]

Para obter mais informações sobre ACS, consulte a seção [próximas etapas](#next_steps) .

> [AZURE.NOTE]
> O filtro de controle de serviços do Azure acesso é uma visualização de tecnologia da comunidade. Como software de pré-lançamento, ele não é anteriormente suportado pela Microsoft.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as tarefas deste guia, conclua a amostra como [autenticar usuários da Web com o Azure acesso controle serviço usando Eclipse][] e usá-lo como ponto de partida para este tutorial.

## <a name="add-the-jspwriter-library-to-your-build-path-and-deployment-assembly"></a>Adicionar a biblioteca de JspWriter ao seu conjunto de caminho e implantação de compilação

Adicione a biblioteca que contém a classe **javax.servlet.jsp.JspWriter** para seu conjunto de caminho e implantação de compilação. Se você estiver usando o Tomcat, a biblioteca é **jsp-API. jar**, que está localizado na pasta de **biblioteca** Apache.

1. No Explorador de projeto do Eclipse, **MyACSHelloWorld**de atalho, clique em **Criar caminho**, clique em **Configurar construir caminho**, clique na guia **bibliotecas** e clique em **Adicionar JARs externos**.
2. Na caixa de diálogo **Seleção JAR** , navegue ao vidro necessário, selecioná-la e clique em **Abrir**.
3. Com a caixa de diálogo **Propriedades de MyACSHelloWorld** ainda aberta, clique em **Assembly de implantação**.
4. Na caixa de diálogo **Assembly de implantação da Web** , clique em **Adicionar**.
5. Na caixa de diálogo **Nova diretiva de Assembly** , clique em **Java construir caminho entradas** e clique em **Avançar**.
6. Selecione a biblioteca apropriada e clique em **Concluir**.
7. Clique **Okey** para fechar a caixa de diálogo de **Propriedades de MyACSHelloWorld** .

## <a name="modify-the-jsp-file-to-display-saml"></a>Modifique o arquivo JSP para exibir SAML

Modificar **JSP** para usar o código a seguir.

    <%@ page language="java" contentType="text/html; charset=UTF-8"
        pageEncoding="UTF-8"%>
        <%@ page import="javax.xml.parsers.*"
                 import="javax.xml.transform.*"
                 import="org.w3c.dom.*"
                 import="java.io.*"
                 import="javax.xml.transform.stream.*"
                 import="javax.xml.transform.dom.*"
                 import="javax.xml.xpath.*"
                 import="javax.servlet.jsp.JspWriter" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Sample ACS Filter</title>
    </head>
    <body>
        <h3>SAML information from sample ACS program</h3>
        <%!
        void displaySAMLInfo(Node node, String parent, JspWriter out)
        {
        
            try
            {
                String nodeName;
                int nChild, i;
                
                nodeName = node.getNodeName();
                out.println("<br>");
                out.println("<u>Examining <b>" + parent + nodeName + "</b></u><br>");
                   
                   // Attributes.
                   NamedNodeMap attribsMap = node.getAttributes();
                   if (null != attribsMap)
                   {
                         for (i=0; i < attribsMap.getLength(); i++)
                         {
                                Node attrib = attribsMap.item(i);
                                out.println("Attribute: <b>" + attrib.getNodeName() + "</b>: " + attrib.getNodeValue()  + "<br>");
                         }
                   }
                   
                   // Child nodes.
                   NodeList list = node.getChildNodes();
                   if (null != list)
                   {
                          nChild = list.getLength();
                          if (nChild > 0)
                          {                    
    
                                 // If it is a text node, just print the text.
                                 if (list.item(0).getNodeName() == "#text")
                                 {
                                     out.println("Text value: <b>" + list.item(0).getTextContent() + "</b><br>");
                                 }
                                 else
                                 {
                                     // Print out the child node names.
                                     out.print("Contains " + nChild + " child node(s): ");   
                                     for (i=0; i < nChild; i++)
                                     {
                                        Node temp = list.item(i);
                                        
                                        out.print("<b>" + temp.getNodeName() + "</b>");
                                        if (i < nChild - 1)
                                        {
                                            // Separate the names.
                                            out.print(", ");
                                        }
                                        else
                                        {
                                            // Finish the sentence.
                                            out.print(".");
                                        }
                                            
                                     }
                                     out.println("<br>");
                                     
                                     // Process the child nodes.
                                     for (i=0; i < nChild; i++)
                                     {
                                        Node temp = list.item(i);
                                        displaySAMLInfo(temp, parent + nodeName + "\\", out);
                                     }
                               }
                          }
                      }
                  }
                catch (Exception e)
                {
                    System.out.println("Exception encountered.");
                    e.printStackTrace();            
                }
            }
        %>
    
        <%
        try 
        {
            String data  = (String) request.getAttribute("ACSSAML");
            
            DocumentBuilder docBuilder;
            Document doc = null;
            DocumentBuilderFactory docBuilderFactory = DocumentBuilderFactory.newInstance();
            docBuilderFactory.setIgnoringElementContentWhitespace(true);
            docBuilder = docBuilderFactory.newDocumentBuilder();
            byte[] xmlDATA = data.getBytes();
            
            ByteArrayInputStream in = new ByteArrayInputStream(xmlDATA); 
            doc = docBuilder.parse(in);
            doc.getDocumentElement().normalize();
            
            // Iterate the child nodes of the doc.
            NodeList list = doc.getChildNodes();
    
            for (int i=0; i < list.getLength(); i++)
            {
                displaySAMLInfo(list.item(i), "", out);
            }
        }
        catch (Exception e) 
        {
            out.println("Exception encountered.");
            e.printStackTrace();
        }
        
        %>
    </body>
    </html>

## <a name="run-the-application"></a>Executar o aplicativo

1. Executar o aplicativo no emulador computador ou implantar em Azure, usando as etapas documentadas como [autenticar usuários da Web com o Azure acesso controle serviço usando Eclipse][].
2. Iniciar um navegador e abra seu aplicativo web. Depois de fazer logon no aplicativo, você verá informações de SAML, incluindo a declaração de segurança fornecida pelo provedor de identidade.

## <a name="next-steps"></a>Próximas etapas

Para explorar melhor funcionalidade do ACS e para experimentar situações mais sofisticadas, consulte [2.0 de serviço de controle de acesso][].

[Prerequisites]: #pre
[Modify the JSP file to display SAML]: #modify_jsp
[Add the JspWriter library to your build path and deployment assembly]: #add_library
[Run the application]: #run_application
[Next steps]: #next_steps
[Serviço de controle de acesso 2.0]: http://go.microsoft.com/fwlink/?LinkID=212360
[Como autenticar usuários da Web com o serviço de controle de acesso Azure usando Eclipse]: ../active-directory-java-authenticate-users-access-control-eclipse
[saml_output]: ./media/active-directory-java-view-saml-returned-by-access-control/SAML_Output.png
 