# docker-elk-filebeat
Filebeat => Logstash

El contenedor con Filebeat enviará los logs del resto de los contnedores del host a un servidor logstash.
 El repo original es: https://github.com/willfarrell/docker-filebeat
 
 El host y puerto de logstash se configura en filebeat.yml.
 
 Para ejecutar este contenedor:
 
    docker-compose up -d
    
Logstash debe ser configurado de la siguiente manera:
    
    input {
      beats {
        port => 5000
      }
    }
    ## Add your filters / logstash plugins configuration here

    filter {
    
      if [type] == "filebeat-docker-logs" {
    
        grok {
          match => { 
            "message" => "\[%{WORD:containerName}\] %{GREEDYDATA:message_remainder}"
          }
        }
    
        mutate {
          replace => { "message" => "%{message_remainder}" }
        }
    
        mutate {
          remove_field => [ "message_remainder" ]
        }
    
      }
    
    }
    
    output {
        elasticsearch {
            hosts => "elasticsearch:9200"
        }
    #stdout { codec => rubydebug }
    }
