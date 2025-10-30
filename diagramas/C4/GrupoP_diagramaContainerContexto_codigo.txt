workspace "Cabine Digital UFMS" "Modelo C4 - Contexto - Cabine Digital"

    !identifiers hierarchical

    model {
        participante = person "Participante" "Participante do evento que tira foto e responde questionarios"
        organizador = person "Organizador de Eventos" "Configura os eventos, as moldura e questionarios"
        admin = person "Administrador" "Gerencia o sistema, contas dos organizadores e inicia o evento"
        
        emailSystem = softwareSystem "Sistema de E-mail" "Sistema para enviar e-mail com a foto"{
            tags "External"
        }
        
        cabineDigital = softwareSystem "Sistema Cabine Digital UFMS" "Sistema para capturar dados e fotos em eventos" {
            
            mobileApp = container "Tablet(Mobile)" "Utilizado para tirar as fotos e responder questionários"{
                technology "React Native"
                tags "Mobile"
            }
            
            webApp = container "Aplicação Front" "Painel ADM"{
                technology "React"
                tags "Web"
            }
            
            api = container "API - Backend" "Fornece o código principal, a lógica de negócio e gerência dados"{
                technology "Node.js"
                tags "API"
            }
            
            database = container "Banco de Dados" "Armazena dados do programa - usuários, eventos, fotos, questionarios/respostas"{
                technology "Node.js"
                tags "API"
            }
        }

        participante -> cabineDigital "Tira fotos e responde questionário"
        organizador -> cabineDigital "Gerencia eventos e questionários"
        admin -> cabineDigital "Administra o sistema e usuários"
        cabineDigital -> emailSystem "Envia e-mail com a foto"
        
        participante -> cabineDigital.mobileApp "Usa"
        organizador -> cabineDigital.webApp "Usa"
        admin -> cabineDigital.webApp "Usa"
        
        cabineDigital.webApp -> cabineDigital.api "Faz chamadas"
        cabineDigital.mobileApp -> cabineDigital.api "Faz chamadas"
        
        cabineDigital.api -> cabineDigital.database "Lê e escreve"
        cabineDigital.api -> emailSystem "Envia e-mails"
    }

    views {
        systemContext cabineDigital "Contexto" {
            include *
            autolayout lr
        }

        container cabineDigital "Containers" {
            include *
            autolayout lr
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
        }
    }

}