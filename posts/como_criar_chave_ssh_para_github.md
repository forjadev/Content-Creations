# O que é
Resumidamente openSSH é uma ferramenta que auxilia em conexões remotas utilizando o protocolo SSH, encriptografando 
> Se voce utiliza o **Github Desktop** não é necessário configurar alguma chave SSH, mas é bom ter configurado caso em algum momento precise utilizar Git fora da GUI

## Pré-requisitos: 
- **Ter o openSSH instalado no seu computador**
 
## Linux:
Para conferir se já está disponível no seu computador utilize o comando:
```sh
if [[ /usr/bin/ssh-agent &> /dev/null ]]; then; echo "ta tudo certo"
```
Se de fato apareceu `ta tudo certo` no teu terminal significa que ta tudo certo e pode pular essa parte.

 ubuntu/debian:
```sh
sudo apt install openssh-client
```
 fedora:
```sh
sudo dnf install openssh
```
 openSUSE:
```sh
sudo zypper install openssh
```
 arch:
```sh
sudo pacman -S openssh
```

## Windows:
Uma das mais maneiras mais fáceis para conseguir gerenciar sua chave SSH no Windows é utilizando o **Git Bash**

Caso não tenha instalado basta baixar direto do site do Git acessando: https://git-scm.com/download/win  

- Clique no primeiro link para baixar
    
    ![Página de Download do Git](https://github.com/forjadev/blog/assets/53125029/89beff5c-020f-4e30-8175-7cc26fe8cfb3)

- Durante a instalação selecione a opção padrão _Use bundled OpenSSH_
    
    ![Janela de instalação do Git selecionado na opção de Use bundled OpenSSH](https://github.com/forjadev/blog/assets/53125029/3fb28b33-24b8-48f3-b8ab-c7cac2c07dd5)

# Instalação
- Daqui em diante o processo será o mesmo tanto para Linux quanto para ...
- ...
