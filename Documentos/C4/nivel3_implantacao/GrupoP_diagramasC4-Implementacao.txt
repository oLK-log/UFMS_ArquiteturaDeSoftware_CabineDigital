workspace "Cabine Digital UFMS" "Modelo C4 - Contexto - Cabine Digital"

    !identifiers hierarchical

    model {
        participante = person "Participante" "Participante do evento que tira foto e responde questionarios"
        organizador = person "Organizador de Eventos" "Configura os eventos, as moldura e questionarios"
        admin = person "Administrador" "Gerencia o sistema, contas dos organizadores e inicia o evento"
        
        emailSystem = softwareSystem "Sistema de E-mail Externo" "Serviço de envio de e-mail da Hostinger Reach"{
            tags "External"
        }
        
        autenticUser = softwareSystem "Passaport UFMS" "Serviço de autenticação/cadastro dos usuários Administrador e Organizador de Eventos"{
            tags "External"
        }
        
        cabineDigital = softwareSystem "Sistema Cabine Digital UFMS" "Sistema para capturar dados e fotos em eventos da UFMS" {
            
            mobileApp = container "Aplicação Front Mobile" "Utilizado para tirar as fotos e responder questionários"{
                technology "Java - Kotlin"
                tags "Mobile"
            }
            
            webApp = container "Aplicação Front Web" "Painel ADM"{
                technology "JavaScript - React"
                tags "Web"
            }
            
            api = container "API - Backend" "Fornece o código principal, a lógica de negócio e gerência dados"{
                technology "Sprint Boot"
                tags "API"
            }
            
            database = container "Banco de Dados" "Armazena dados do programa - usuários, eventos, fotos, questionarios/respostas"{
                technology "PostgreSQL"
                tags "Database"
            }
            
        }
        
        deploymentEnvironment "Producao" {
            
            tablet = deploymentNode "Tablet do Evento" "Um tablet Android no local do evento" {
                technology "Android OS"
                tags "Mobile Device"
                containerInstance cabineDigital.mobileApp
            }
            userComputer = deploymentNode "Computador do Usuário" "O navegador web no computador do Organizador ou Admin" {
                technology "Desktop Browser"
                tags "User Device"
                containerInstance cabineDigital.webApp
            }
            apiServer = deploymentNode "Servidor de Aplicação" "Servidor em nuvem" {
                technology "Servidor Linux"
                tags "Infrastructure"
                containerInstance cabineDigital.api
            }
            dbServer = deploymentNode "Servidor de Banco de Dados" "Serviço de banco de dados gerenciado" {
                technology "PostgreSQL"
                tags "Infrastructure"
                containerInstance cabineDigital.database
            }
            emailSystem -> participante "Envia e-mail para"
        
            tablet -> apiServer "Faz chamadas [JSON/HTTPS]"
        
            userComputer -> apiServer "Faz chamadas [JSON/HTTPS]"
        
            apiServer -> dbServer "Lê e escreve [JDBC]"
        }

        admin -> cabineDigital "Administra o sistema e usuários"
        cabineDigital -> emailSystem "Envia e-mail(foto) para participante usando"
        emailSystem -> participante "Envia e-mail para"
        organizador -> cabineDigital.mobileApp "inicia evento"
        
        participante -> cabineDigital.mobileApp "Tira fotos e responde questionário durante o evento"
        organizador -> cabineDigital.webApp "gerencia evento e questionários"
        admin -> cabineDigital.webApp "gerencia eventos e organizadores"
        
        cabineDigital.webApp -> cabineDigital.api "Faz chamadas"
        cabineDigital.mobileApp -> cabineDigital.api "Faz chamadas"
        
        cabineDigital.api -> autenticUser "autentica usuários"
        cabineDigital.api -> cabineDigital.database "Lê e escreve/armazena dados"
        cabineDigital.api -> emailSystem "Envia e-mails por"
        
        
    }

    views {
        SystemContext cabineDigital "Contexto" "Diagrama C4 nível 1 utilizado como estudo de caso na disciplina Arquitetura de Software. - UFMS" {
            include *
            autolayout lr
        }

        container cabineDigital "Containers" "Diagrama C4 nível 2 utilizado como estudo na disciplina Arquitetura de Software - UFMS" {
            include *
            autolayout lr
        }
        
        deployment cabineDigital "Producao" "Implantacao" "Diagrama de Implantação do C4 nível 3 - modos como contêiner roda no ambiente de Produção." {
            include *
            autolayout tb 
        }

        styles {
            element "Element" {
                color #ffffff
                fontSize 22
            }
            element "Person" {
                shape Person
                background #0a60ff
                color #ffffff
            }
            element "Software System" {
                background #1168bd
                color #ffffff
            }
            element "External" {
                background #999999
                color #ffffff
            }
            element "Container" {
                background #438dd5
                color #ffffff
            }
            element "Database" {
                shape Cylinder
            }
            relationship "Relationship" {
                thickness 2
                color #777777
            }
            element "Web" {
                shape WebBrowser
            }
            element "Mobile" {
                shape MobileDevicePortrait
            }
            element "Mobile Device" {
                shape MobileDevicePortrait
            }
            element "User Device" {
                shape WebBrowser
            }
            element "Infrastructure" {
                shape Hexagon
                background #232F3E
            }
        }
    }

}