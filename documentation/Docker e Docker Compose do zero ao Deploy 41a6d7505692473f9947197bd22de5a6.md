# Docker e Docker Compose do zero ao Deploy

> [Vídeo](https://www.youtube.com/watch?v=yb2udL9GG2U) ministrado pelo [Full Cycle](https://www.youtube.com/channel/UCMUoZehUZBhLb8XaTc8TQrA).

> [Instalação](https://docs.docker.com/engine/install/) do Docker.

- **O que são containers?**

    > *Um container é padrão de unidade de software que empacota código e todas as dependências de uma aplicação fazendo que a mesma seja executada rapidamente de forma confiável de um ambiente computacional para o outro.*

    Os containers pode ser comparados a máquinas virtuais como demonstrado abaixo.

    ![Docker%20e%20Docker%20Compose%20do%20zero%20ao%20Deploy%2041a6d7505692473f9947197bd22de5a6/Untitled.png](Docker%20e%20Docker%20Compose%20do%20zero%20ao%20Deploy%2041a6d7505692473f9947197bd22de5a6/Untitled.png)

    - **Virtual Machine**: Nesse caso é instalado uma imagem de virtualização (que de forma "grosseira" um cópia da configurações do seu hardware, também conhecido como Hypervisor), onde pode ser instalado diversos sistemas operacionais. Alguns exemplos de VM são: [VirtualBox](https://www.virtualbox.org/), [VMWare](https://www.vmware.com/br.html), etc.
    - **Container**:Nesse há um sistema operacional que serve como host (que é nomeado de Host Operationg System), onde é instalado o Docker (ou Container Engine) que gerencia os containers. O Container Engine simplesmente serve para gerenciar o uso dos recursos da máquina para cada container (que são basicamente aplicações isoladas e em execução).
- **Como funciona os containers?**

    O nosso **sistema operacional** executa diversos **P** (processos) que em execução utilizam 3 recursos importantes do sistema operacional.

    - o **namespace** (que serve para isolar o processo, para que este não possa visualizar nenhum outro **P**, para que dessa forma não afete nenhum outro **P**).

        ![Docker%20e%20Docker%20Compose%20do%20zero%20ao%20Deploy%2041a6d7505692473f9947197bd22de5a6/Untitled%201.png](Docker%20e%20Docker%20Compose%20do%20zero%20ao%20Deploy%2041a6d7505692473f9947197bd22de5a6/Untitled%201.png)

        É importante ressaltar que um **P** em um namespace pode ter diversos **SP** (subprocesso) que podem sim se comunicar, pois eles são filhos de um **P**, ou seja, eles estão dentro de um **P** pai.

        ![Docker%20e%20Docker%20Compose%20do%20zero%20ao%20Deploy%2041a6d7505692473f9947197bd22de5a6/Untitled%202.png](Docker%20e%20Docker%20Compose%20do%20zero%20ao%20Deploy%2041a6d7505692473f9947197bd22de5a6/Untitled%202.png)

        Dessa forma podemos isolar alguns recursos dos processos como:

        - Pid (Process ID)
        - User
        - Network
        - File System
    - o **Cgroups** (que isola e controla os recursos dos computadores). Sendo seu papel gerenciar o uso dos processos, dando limites de uso dos recursos para cada um dos processos que solicitam esses recursos.

        ![Docker%20e%20Docker%20Compose%20do%20zero%20ao%20Deploy%2041a6d7505692473f9947197bd22de5a6/Untitled%203.png](Docker%20e%20Docker%20Compose%20do%20zero%20ao%20Deploy%2041a6d7505692473f9947197bd22de5a6/Untitled%203.png)

        Como o exemplo abaixo exemplifica.

        ![Docker%20e%20Docker%20Compose%20do%20zero%20ao%20Deploy%2041a6d7505692473f9947197bd22de5a6/Untitled%204.png](Docker%20e%20Docker%20Compose%20do%20zero%20ao%20Deploy%2041a6d7505692473f9947197bd22de5a6/Untitled%204.png)

    - o **File System** (que utiliza o OFS, ou Overlay File System), que por sua vez é um sistema de arquivos que trabalha em camadas.

        ![Docker%20e%20Docker%20Compose%20do%20zero%20ao%20Deploy%2041a6d7505692473f9947197bd22de5a6/Untitled%205.png](Docker%20e%20Docker%20Compose%20do%20zero%20ao%20Deploy%2041a6d7505692473f9947197bd22de5a6/Untitled%205.png)

        Dessa forma é aproveitado pedaços de outros sistema para permitir a utilização mínima de recursos próprios, tornando assim um container muito leve.

        ![Docker%20e%20Docker%20Compose%20do%20zero%20ao%20Deploy%2041a6d7505692473f9947197bd22de5a6/Untitled%206.png](Docker%20e%20Docker%20Compose%20do%20zero%20ao%20Deploy%2041a6d7505692473f9947197bd22de5a6/Untitled%206.png)

        Na imagem acima é demonstrado dois containers, um alocando uma aplicação na versão 1 e contendo 2 dependências, e o outro container contendo a mesma aplicação mas na versão 2. A imagem ilustra que o container com a versão 2 da aplicação não carrega as dependências, ele apenas se vincula com o container da versão 1. Assim quando é carregado a aplicação ele carrega a aplicação da versão 2 e aproveita as suas dependências no container da versão 1.

    Entendo isso podemos dizer que os container são baseado em imagens, onde os containers são construidos a partir de uma imagem (que são basicamente moldes).

    ![Docker%20e%20Docker%20Compose%20do%20zero%20ao%20Deploy%2041a6d7505692473f9947197bd22de5a6/Untitled%207.png](Docker%20e%20Docker%20Compose%20do%20zero%20ao%20Deploy%2041a6d7505692473f9947197bd22de5a6/Untitled%207.png)

    Dessa forma é possível imaginar como os recursos de containers também são compartilhado entre eles. Para melhorar a explicação observe o exemplo abaixo.

    ![Docker%20e%20Docker%20Compose%20do%20zero%20ao%20Deploy%2041a6d7505692473f9947197bd22de5a6/Untitled%208.png](Docker%20e%20Docker%20Compose%20do%20zero%20ao%20Deploy%2041a6d7505692473f9947197bd22de5a6/Untitled%208.png)

    Se já tivermos um **Scratch** (uma imagem em branco) que foi inserido o sistema operacional **Ubuntu**, que por sua vez contêm o **Bash** e o **ssh.d** e também é carregado um **MyApp:v1** (aplicação na versão 1). Quando formos carregar outra aplicação não é necessário carregar todo o restante novamente, pois ele já reaproveita a imagem para recolher os recursos necessários.
    Outra explicação para ilustrar melhor isso é: cada componente desse é um bloco de arquivo ou camada e pode ser copiado para ser reaproveitado em outro lugar.

    Dessa forma cada com componente de um container é independente, ou seja, caso haja alguma vunerabilidade em algum componente, a correção não afetará o restante.

    Assim uma imagem é um conjunto de componentes que são inseridos juntos.

    ![Docker%20e%20Docker%20Compose%20do%20zero%20ao%20Deploy%2041a6d7505692473f9947197bd22de5a6/Untitled%209.png](Docker%20e%20Docker%20Compose%20do%20zero%20ao%20Deploy%2041a6d7505692473f9947197bd22de5a6/Untitled%209.png)

    A figura acime ilustra uma imagem (MyAppImage:v1) contendo um Scratch, um Ubuntu, um Bash, um ssh.d e um MyApp:v1.

    Dessa forma vale ressaltar novamente que cada container é criado a partir de uma imagem e quando executado se torna um processo que já roda no sistema operacional. E esse processo é divido em três partes: **namespaces**, **Cgroups** e **File System**.

    Outro ponto interessante é, que a imagensm que servem como base para a criação de  um container possuem um estado imutável, ou seja, a imagem contida no container não se modifica. E quando é "levantado" um container, ele se baseia numa imagem para sua criação e anexa junto uma camada (chamada de **Read/Write**) de acesso que permite alterações no container, mas quando encerrado o container as alterações são perdidas. Assim se necessário guardar as modificações do container podemos fazer um **commit**.

- **O que é um commit no Docker?**

    É a geração de uma cópia ou nova versão de um container.

    ![Docker%20e%20Docker%20Compose%20do%20zero%20ao%20Deploy%2041a6d7505692473f9947197bd22de5a6/Untitled%2010.png](Docker%20e%20Docker%20Compose%20do%20zero%20ao%20Deploy%2041a6d7505692473f9947197bd22de5a6/Untitled%2010.png)

- **Onde fica as imagens Docker?**

    Essa imagens ficam num repositório (como o Github) chamado de **IMAGE REGISTRY**.

    ![Docker%20e%20Docker%20Compose%20do%20zero%20ao%20Deploy%2041a6d7505692473f9947197bd22de5a6/Untitled%2011.png](Docker%20e%20Docker%20Compose%20do%20zero%20ao%20Deploy%2041a6d7505692473f9947197bd22de5a6/Untitled%2011.png)

- **Como é criado um container?**

    Através do Dockerfile é rodado instruções que são executadas para montar uma imagem. No começo dessas instruções é utilizada uma imagem de referência através do comando `FROM` que busca essa imagem no **IMAGE REGISTRY** através de um **pull**.

    ![Docker%20e%20Docker%20Compose%20do%20zero%20ao%20Deploy%2041a6d7505692473f9947197bd22de5a6/Untitled%2012.png](Docker%20e%20Docker%20Compose%20do%20zero%20ao%20Deploy%2041a6d7505692473f9947197bd22de5a6/Untitled%2012.png)

    Ou ao mesmo tempo pode ser feito um **build** que faz um **push** na imagem e a armazena no seu repositório de imagens.

    ![Docker%20e%20Docker%20Compose%20do%20zero%20ao%20Deploy%2041a6d7505692473f9947197bd22de5a6/Untitled%2013.png](Docker%20e%20Docker%20Compose%20do%20zero%20ao%20Deploy%2041a6d7505692473f9947197bd22de5a6/Untitled%2013.png)

    O funcionamento desse versionalização do Docker é muito semelhante com o uso do git e [Github](https://tableless.com.br/tudo-que-voce-queria-saber-sobre-git-e-github-mas-tinha-vergonha-de-perguntar/#:~:text=O%20Github%20%C3%A9%20um%20servi%C3%A7o,funcionalidades%20extras%20aplicadas%20ao%20git.&text=Al%C3%A9m%20disso%2C%20quase%20todos%20os,mesmo%20enviando%20c%C3%B3digo%20e%20corre%C3%A7%C3%B5es.), mas ao invés de código é transferido imagens.

- **Como o Docker funciona?**

    Ele funciona em cima do Docker Host, que por sua vez possui um daemon que roda uma API que serve o Docker Clinte.

    Toda vez que é dado um comando para o Docker, é então recebido o comando pelo Docker Cliente que este se conecta com o Docker Host que este chama as APIs do Docker Host que executa os comandos.

    No mesmo momento que temos o docker executando. Há também um cache de imagens, para cachear todas as imagens em uso, para que assim não haja a necessidade de baixar ou carregar os recursos que precisam ser reaproveitados. O Cache de imagens é baixado do DOCKER REGISTRY.

    O Docker Host têm também um gerenciamento de volumes, o que permite compartilhar registro ou volumes da máquina host com os containers.

    Há também um gerrenciamento de network no Docker Host, para possibilitar a comunicação da máquina host com os container, e também a comunicação entre os containers.

    ![Docker%20e%20Docker%20Compose%20do%20zero%20ao%20Deploy%2041a6d7505692473f9947197bd22de5a6/Untitled%2014.png](Docker%20e%20Docker%20Compose%20do%20zero%20ao%20Deploy%2041a6d7505692473f9947197bd22de5a6/Untitled%2014.png)

- **Como começar com Docker?**

    Primeiramente têm que ter [instalado o Docker](https://docs.docker.com/engine/install/) para prosseguir os seguintes passos.

    Podemos começar testando o funcionamento do docker com o seguinte comando.

    ```docker
    docker run hello-world
    ```

    Depois pode-se ir maia a fundo e executar uma imagem do ubuntu para começar a se familiarizar com o Docker Client.

    ```docker
    docker run ubuntu bash
    ```

    Para visualizar os containers em execução basta executar o seguinte comando.

    ```docker
    docker ps
    ```

    Mas se for necessário visualizar todos os container basta utilizar a flag `-a` (all), como o exemplo a seguir.

    ```docker
    docker ps -a
    ```

- **Como acessar a porta de um container?**

    Basta utilizar a flag `-p` (port) e explicitar em qual porta do host será direciona para a porta do container, como o exemplo abaixo demonstra.

    ```docker
    docker run -p 8080:80 nginx
    ```

- **Como executar um container em background?**

    Por padrão quando executamos um container estamos executando um processo, e assim o docker fica monitorando o processo e apresentando no terminal. Assim o terminal utilizado para "subir" um container ficar inutilizavél, e para que isso não aconteça utilizamos a tag `-d` (detached), como o exemplo a seguir.

    ```docker
    docker run -d -p 8080:80 nginx
    ```

- **Como parar um container?**

    Para "derrubar" um container é utilizado o seguinte comando.

    ```docker
    	docker stop trusting_tharp
    ```

- **Como iniciar um container já existente?**

    Para iniciar um container parado e já existente utilizamos o seguinte comando.

    ```docker
    docker start trusting_thard
    ```

- **Como remover um container?**

    Para remover um container é utilizado o seguinte comando.

    ```docker
    docker rm trusting_thard
    ```

- **Como remover uma imagem Docker?**

    Para remover um container utiliza-se o comando a seguite.

    ```docker
    docker rmi nginx
    ```

- **Como nomear um container?**

    Nomear um container é importante para facilitar na execução de comandos referentes ao container. Para nomea-lo utilizamos a flag —name como demonstrado no seguinte comando.

    ```docker
    docker --name my-nginx -p 8080:80 nginx 
    ```

- **Como executar comandos em um container?**

    Executar comandos diretamente para um container basta utilizar o comando `exec` do Docker como demonstrado a seguir.

    ```docker
    docker exec my-nginx ls
    ```

- **Como acessar um container?**

    É importante citar que às vezes é importante acessar um container para configurá-lo ou até mesmo editá-lo no momento de uso do container, e para fazer isso basta utilizar o comando `exec` mas passando o parâmentro `-it` (interative) e o parâmetro `bash`. Dessa forma podemos acessar o container através do `bash`, como demonstrado a seguir

    ```docker
    docker exec -it my-nginx bash
    ```

    Assim podemos interagir com o container como se fosse o próprio host.

    Para sair do container basta digitar o comando

- **Como listar as imagens do existentes no host?**

    Para listar as imagens basta digitar o seguinte comando.

    ```docker
    docker images
    ```

- **O que é um Docker file?**

    É um arquivo que contém instruções sobre a criação de uma imagem, como demonstrado abaixo.

    ```docker
    FROM golang:1.14
    COPY . .
    RUN go build main.go
    EXPOSE 8000
    ENTRYPOINT ["./main"]
    ```

    - `FROM` define a imagem que deve ser uilizada como base.
    - `COPY` define arquivos que devem ser copiados e onde devem ser colados.
    - `RUN` define os comandos a serem executados. Por exemplo: `apt-get install`.
    - `EXPOSE` define a porta que será utilizada para comunicação entre o host e a imagem, e também comunicação entre containers.
    - `ENTRYPOINT` define um arquivo a ser executado quando for "levantado" uma container a partir da imagem.

    Para realizar o build do arquivo Dockerfile é preciso executar o comando build, como exemplificado a seguir.

    ```docker
    docker build -t fulano/my-image-golang .
    ```

    Dessa forma é gerado uma imagem Docker a partir do Dockerfile.

    Após isso basta executar o comando run como o exemplo a seguir.

    ```docker
    docker run --name my-container-golang -d -p 3000:8080 my-image-golang
    ```

    ```docker
    docker tag my-image-golang fulano/my-image-golang
    ```

- **Como fazer o push de uma imagem Docker?**

    Após criar uma imagem é necessário garantir o login com o Docker Hub através do comando `docker login`, e em seguida digitar o ID e o password.

    Quando temos imagens fora do padrão, antes de realizar o push é aconselhável utilizar o comando tag para definir uma tag para a imagem, como exemplificado a seguir.

    Após isso basta digitar o seguinte comando.

    ```docker
    docker push fulano/my-image-golang
    ```

    Assim quando acessado o Docker Hub pode-se observar que a imagem foi atribuida a um repositório, como demonstrado abaixo.

    ![Docker%20e%20Docker%20Compose%20do%20zero%20ao%20Deploy%2041a6d7505692473f9947197bd22de5a6/Untitled%2015.png](Docker%20e%20Docker%20Compose%20do%20zero%20ao%20Deploy%2041a6d7505692473f9947197bd22de5a6/Untitled%2015.png)

    Assim qualquer outro usuário de Docker terá o mesmo ambiente de desenvolvimento.

- **Como fazer o pull de uma imagem Docker?**

    Para realizar um pull é mais simples ainda, basta observar o comando a seguir para se guiar.

    ```docker
    docker pull fulano/my-image-golang
    ```

- **O que é Docker Compose?**

    É uma ferramenta que gerencia de forma inteligente diversos containers Docker através de um único arquivo `docker-compose.yaml`.

- **Como funciona o Docker Compose?**

    Primeiro têm que existir um arquivo `docker-compose.yaml` dentro do seu repositório, como exemplificado abaixo.

    ```docker
    version: "3.9"
    services:
      webservergo:
        build: .
        ports:
          - 3000:8080
      nginx:
        image: nginx
        volumes:
          - ./nginx:/usr/share/nginx/html
        ports:
          - 8080:80
      redis:
        image: redis
        expose: 
          - 6379
    ```

    - `version` indica a versão do Docker Compose a ser utilizada.
    - `services` indica os serviçoes
    - `build` indica que será realizado um build de uma imagem
    - `image` indica a imagem a ser utilizada.
    - `volumes` indica o gerenciamento dos volumes do host e imagem. Dessa forma é definido que tudo que for mudado dentro da pasta do host é mudado na pasta do container e vice-versa.
    - `ports` indica o gerenciamento de portas do host e imagem. Dessa forma é definido que quando acessado a porta definida no host é direcionado para a porta definida do container.
    - `expose` expõe a porta `6379` do container.

    E para buildar esse aquivo basta digitar o seguinte comando.

    ```docker
    docker-compose up
    ```

    Se for necessário "derrubar" os container e o gerenciamento basta executar o seguinte comando.

    ```docker
    docker-compose down
    ```

- **Como fazer o deploy de um projeto com Docker?**

    Além da forma simples (mas não tão eficiênte) é instalar o Docker em um servidor e subir a aplicação lá. Mas atualmente existe diversos Cloud Providers como [Azure](https://azure.microsoft.com/pt-br/free/search/?&ef_id=CjwKCAiAkJKCBhAyEiwAKQBCktgoVVLpzaee3d67PnJA-fKdkC52LIoH_yCURa4gGRgXUXhs7KlrdxoCW_UQAvD_BwE:G:s&OCID=AID2100014_SEM_CjwKCAiAkJKCBhAyEiwAKQBCktgoVVLpzaee3d67PnJA-fKdkC52LIoH_yCURa4gGRgXUXhs7KlrdxoCW_UQAvD_BwE:G:s), [AWS Fargate](https://aws.amazon.com/pt/fargate/?whats-new-cards.sort-by=item.additionalFields.postDateTime&whats-new-cards.sort-order=desc&fargate-blogs.sort-by=item.additionalFields.createdDate&fargate-blogs.sort-order=desc), [Google Cloud Run](https://cloud.google.com/run?utm_source=google&utm_medium=cpc&utm_campaign=latam-BR-all-pt-dr-BKWS-all-all-trial-b-dr-1009897-LUAC0008673&utm_content=text-ad-none-any-DEV_c-CRE_434184323500-ADGP_Hybrid%20%7C%20BKWS%20-%20MIX%20%7C%20Txt%20~%20Compute_Cloud%20Run-KWID_43700053588659804-kwd-677335471379&utm_term=KW_%2Bgoogle%20%2Bcloud%20%2Brun-ST_%2BGoogle%20%2BCloud%20%2BRun&gclid=CjwKCAiAkJKCBhAyEiwAKQBCkuBGywx4Iu55NuwP0ZEa6qCAsPPbjbrHZJXib7JEbDrfOmAbC-LGMxoChYIQAvD_BwE&gclsrc=aw.ds) ou para quem precisa de uma infraestrutura mais robusta pode utilizar o [Kubernetes.io](https://kubernetes.io/pt/).

    Para facilitar há um [Cloud Code](https://cloud.google.com/code?hl=pt-br) do VSCode que bascimente é uma extensão do Google Cloud sendo necessário apenas o gcloud instalado no computador.

- **Quais são os obstáculos mais comuns?**

    Alguns obstáculos percebidos durante a criação dessas anotações foram o erro `docker: Cannot connect to the Docker daemon at unix:///var/run/docker.sock. Is the docker daemon running?.` Para resolver este erro basta executar o docker com o seguinte comando `systemctl start docker`, e para facilitar no futuro é válido definir o usuário atual como parte do grupo docker com o comando `gpasswd -a $USER docker`, onde o `$USER` é o usuário atual.

    Outros problemas comuns encontrados na utilização do Docker basta acessar a [documentação](https://docs.docker.com/) ou a [comunidade](https://www.docker.com/docker-community) para conseguir ajuda.

> [Imagem](https://hub.docker.com/repository/docker/thazsobral/golang-redis-nginx-example) criado no Docker Hub.

![Docker%20e%20Docker%20Compose%20do%20zero%20ao%20Deploy%2041a6d7505692473f9947197bd22de5a6/Untitled%2016.png](Docker%20e%20Docker%20Compose%20do%20zero%20ao%20Deploy%2041a6d7505692473f9947197bd22de5a6/Untitled%2016.png)