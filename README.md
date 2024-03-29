# docker-in-ryzentosh
Como usar o Docker em uma máquina AMD rodando o MacOS (Testado no Sonoma 14.4.1).

Eu criei este guia para mostrar uma forma eficiente de usar o Docker em um Ryzentosh, eu rodava ele no VirtualBox 6.1.40 (O último a suportar AMD) que não recebe mais suporte e seu uso necessita do desligamento do System Integrity Protection, agora com o QEMU + UTM existem atualizações e suporte regulares, além de funcionar com todos os recursos de segurança ativados.

# Primeiros Passos

## Gerenciador de Pacotes Brew
O Brew é um gerenciador de pacotes para MacOS, vamos usa-lo para baixar o Docker, instale o Brew diretamente do [site oficial](https://brew.sh/).
Após isso rode os seguintes comandos um de cada vez:

```
brew update
brew upgrade
brew install qemu
brew install docker
brew install docker-compose
```

## UTM
Agora que instalamos o Docker e o QEMU (nossa camada de emulação) vamos configurar uma VM, instale o [UTM](https://mac.getutm.app/) (uma interface gráfica para o QEMU).

## ISO Linux
Com o UTM instalado baixe um sistema Linux de sua preferência, recomendo o [Ubuntu Server](https://ubuntu.com/download/server).

# Criação da máquina virtual
Abra o UTM e crie uma VM com sua ISO, após isso instale o Docker nessa máquina seguindo a [Documentação Oficial](https://docs.docker.com/desktop/install/linux-install/).
Nas configurações de rede da máquina use Bridge com layer2.

## Dicas
- Veja a [Documentação do Post-Install do Docker](https://docs.docker.com/engine/install/linux-postinstall/), para configurar seu usuário (opcional).
- Se estiver instalando o Ubuntu Server, NÃO instale o Docker em snap, você terá problemas para realizar os ajustes finais.

# Ajustes finos (opcional)
Aqui darei algumas dicas para integrar esse Docker da VM no Docker do seu MacOS.

## Use o SSH para se conectar ao shell Linux pelo Terminal do MacOS
Abra o terminal do seu linux e digite:

```
ip addr
```

Localize o IP de sua máquina virtual, abra o Terminal do MacOS e digite:

```
ssh nome-do-seu-usuario@ip-da-maquina-virtual
```

## Configurando o Daemon do Docker
No shell do seu Linux crie este diretório e use o nano para criar um arquivo de configuração:

```
mkdir -p /etc/systemd/system/docker.service.d
nano /etc/systemd/system/docker.service.d/options.conf
```

Ao abir o nano adicione essas linhas no arquivo recém criado:
```
[Service]
ExecStart=
ExecStart=/usr/bin/dockerd -H unix:// -H tcp://0.0.0.0:2375
```

Rode os seguites comandos para reiniciar o Docker:

```
systemctl daemon-reload
systemctl restart docker
```

Para testarmos se deu certo digite no Terminal do seu MacOS:

```
DOCKER_HOST=tcp://ip-da-maquina-virtual:2375 docker info
```

Se receber um output com informações da máquina virtual prossiga, caso o contrário revise os passos.

No terminal do MacOS digite: 

```
echo "export DOCKER_HOST=tcp://remote-docker-host-ip:2375" >> ~/.zshrc
exit
```

OBS: Nas configurações do UTM podemos desabilitar seu icone na dock, afinal usaremos a VM via terminal de qualquer forma, explore todas as opções dessa ferramenta.

O resultado final deve ser o Docker do Mac completamente integrado a sua VM, sempre que for usar o Docker ligue ela.

https://github.com/nukhes/docker-macos-ryzentosh/assets/79018158/dc2a4506-f320-4644-a26b-07966ffa35ce




