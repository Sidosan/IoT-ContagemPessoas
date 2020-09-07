# Servidor IoT-Debian com Docker Compose=> VerneMQ e MongoDB

Após analise da necessidade do projeto e pensando na escalabilidade, o servidor IoT foi planejado com a ferramenta Dock como gerenciador e facilitador da escalabilidade. 

Usei 2 imagens dentro do Docker Compose, VerneMQ(Broker MQTT) e MongoDB(Banco de Dados).

Nesse artigo vou descrever o passa a passo das instações e configuração até o momento(07/09/2020).


## Instalação do Linux:

A versão ultilizada é o Debian 10.5(buster).

A principal configuração para rodar corretamente o Docker é a partição.
Deveremos Escolher a opção “Partição /home separada”:
<img src=https://servidordebian.org/_media/pt/buster/install/12-part-3.png>


## Instalação do Docker:

O pacote de instalação do Docker disponível no repositório oficial do Debian pode não ser a versão mais recente. Para garantir que recebamos a versão mais recente, instalaremos o Docker a partir do repositório oficial do Docker. Para fazer isso, adicionaremos uma nova fonte de pacote, adicionaremos a chave GPG do Docker para garantir que os downloads sejam válidos e, em seguida, instalaremos o pacote.

Primeiro, atualize sua lista existente de pacotes:

``$ sudo apt update``

Em seguida, instale alguns pacotes de pré-requisitos que permitem aptusar pacotes sobre HTTPS:

sudo apt install apt-transport-https ca-certificates curl gnupg2 software-properties-common
Em seguida, adicione a chave GPG para o repositório oficial do Docker ao seu sistema:

``$ curl -fsSL https://download.docker.com/linux/debian/gpg | sudo apt-key add -``

Adicione o repositório Docker às fontes APT:

``$ sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/debian $(lsb_release -cs) stable"``

Em seguida, atualize o banco de dados de pacotes com os pacotes Docker do repo recém-adicionado:

``$ sudo apt update``

Certifique-se de que está prestes a instalar a partir do repositório Docker em vez do repositório Debian padrão:

``$ apt-cache policy docker-ce``

Você verá uma saída como esta, embora o número da versão do Docker possa ser diferente:

**Saída de apt-cache policy docker-ce:**

```
docker-ce:
  Installed: (none)
  Candidate: 5:18.09.7~3-0~debian-buster
  Version table:
     5:18.09.7~3-0~debian-buster 500
        500 https://download.docker.com/linux/debian buster/stable amd64 Packages
        
```

Observe o ``docker-ce`` se foi está instalado e certifique que o ``Candidate`` foi instalado em repositório Docker para Debian 10 (buster).

**Por fim, instale o Docker:**

``sudo apt install docker-ce``

O Docker agora está instalado, o daemon foi iniciado e o processo habilitado para iniciar na inicialização. Verifique se ele está funcionando:

``sudo systemctl status docker``

A saída será semelhante à seguinte, mostrando que o serviço está ativo e em execução:


**saida:**
```
● docker.service - Docker Application Container Engine
   Loaded: loaded (/lib/systemd/system/docker.service; enabled; vendor preset: enabled)
   Active: active (running) since Mon 2019-07-08 15:11:19 UTC; 58s ago
     Docs: https://docs.docker.com
 Main PID: 5709 (dockerd)
    Tasks: 8
   Memory: 31.6M
   CGroup: /system.slice/docker.service
           └─5709 /usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock
```

A instalação do Docker oferece não apenas o serviço Docker (daemon), mas também o  ``docker command line utility`` ou cliente Docker.

Certifique a linha ``Active: active`` se tiver diferente, senta e chora porque eu tive que reinstalar o Debian 3x pra fazer essa merda funcionar.

Mas eu descobrir que tinha reparticionado errado, então fica a dica.



## Instalação Docker Compose:

Verifique a versão atual e, se necessário, atualize-a no comando a seguir:

``sudo curl -L https://github.com/docker/compose/releases/download/1.25.3/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose``

A seguir, definiremos as permissões:

``sudo chmod +x /usr/local/bin/docker-compose``

Em seguida, verificaremos se a instalação foi bem-sucedida verificando a versão:

``docker-compose --version``

Isso imprimirá a versão que instalamos:

**Saida:**

```docker-compose version 1.25.3, build d4d1b42b```

Sem segredo, essa etapa é mel na chupeta.

## Instalação do VerneMQ by Docker:

O Diabo mora nessa instalação, confesso que nao me lembro bem como foi a instalação, deu muito erros e tentei diversas vezes, mas vou tentar descrever o que lembro.

tentei com 3 repositorios:

``helm``
``npm``
``apt``

Indicado é ``helm``, porque o fabricante recomenda, mas na minha maquina virtual não rodou...

Primeiro instale e configure o Helm de acordo com a [documentação](https://helm.sh/docs/intro/). 
Em seguida, adicione o repositório VerneMQ no Helm:

``helm repo add vernemq https://vernemq.github.io/docker-vernemq``

Agora você pode instalar o VerneMQ:

``helm install vernemq/vernemq``

Se não conseguir, senta e chora novamente...
Brincadeira! tente outro repositorio.

## Configurar o VerneMQ by Docker:
Pra voce configurar o VerneMQ e outros programa no docker, existe varios meios, eu fiz por scripts .YAML

Porque eu fiz isso ao invez de linha de comando?! 

Não sei, sou idiota que vai no caminho mais dificil.

Basicamente esse primeiro arquivo configura:([Clique aqui](https://github.com/Sidosan/IoT-ContagemPessoas/blob/master/stack-v1.yaml))
- porta 1883 e 8888 para uso do VerneMQ
- ativa VerneMQ para uso do docker
- aceita clientes anonimos.

Para subir o arquivo utilize o comando:

``docker-compose -f stack-v1.yaml up``

**Teste o Broker:**

Usei 2 programas para Brokers Clientes:

-MQTT BOX (http://workswithweb.com/mqttbox.html)

-MQTT Explorer (http://mqtt-explorer.com/)

Também testei aplicativos Android.

Pegue o IP da maquina ou se estive rodando em uma maquina local é só inserir:
``localhost:1883``

Exemplo IP externo:
``192.168.xx.xx:1883``

Como não temos nenhuma autentificação vai conseguir se conectar facilmente.

Agora é só Brincar com o MQTT!















