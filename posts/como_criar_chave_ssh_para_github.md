# Como criar e registrar uma chave SSH para o GitHub
> Autor: [Junio Santos](https://github.com/imnotjuniodev)

#### Índice
- ![O que é](#O%20que%20é)
	- ![Pré-requisitos](#Pré-requisitos)
- ![Instalando OpenSSH no seu computador](#Instalando%20OpenSSH%20no%20seu%20computador)
	- ![MacOS](#Instalando%20OpenSSH%20no%20seu%20computador#MacOS)
	- ![Linux](#Instalando%20OpenSSH%20no%20seu%20computador#Linux)
	- ![Windows](#Instalando%20OpenSSH%20no%20seu%20computador#Linux)
- ![Criando a chave SSH](#Criando%20a%20chave%20SSH)
- ![Adicionando no GitHub](#Adicionando%20no%20GitHub)
- ![Registrando no dispositivo](#Registrando%20no%20dispositivo)

## O que é
OpenSSH é uma ferramenta que auxilia em conexões remotas utilizando o protocolo SSH, servindo como um meio de autenticação prático onde não é necessário senhas e usernames. Aplique esse conceito ao Github (ou GitLab) e voce poderá clonar seus projetos e eles automaticamente autenticar com a sua conta, podendo manter o repositório sincronizado com maior segurança e sem necessidade de senha.

> Se voce utiliza o **Github Desktop** não é necessário configurar alguma chave SSH, mas é bom ter configurado caso em algum momento precise utilizar Git fora da GUI

## Pré-requisitos: 
- **openSSH**
- **Git**
 
## Instalando OpenSSH no seu computador
> Caso ja openssh instalado em seu pc, basta pular para [[#Configuração]]

### Mac:
```sh
$ brew install git 
```

### Linux:
Para conferir se já está disponível no seu computador utilize o comando:
```sh
$ if command -v /usr/bin/ssh-agent &> /dev/null; then echo "ta tudo certo"; fi
```
Se de fato apareceu `ta tudo certo` no teu terminal significa que ta tudo certo e pode pular essa parte.

 ubuntu/debian:
```sh
$ sudo apt install openssh-client
```
 fedora:
```sh
$ sudo dnf install openssh
```
 openSUSE:
```sh
$ sudo zypper install openssh
```
 arch:
```sh
$ sudo pacman -S openssh
```

### Windows:
Uma das mais maneiras mais fáceis para conseguir gerenciar sua chave SSH no Windows é utilizando o **Git Bash**

Caso não tenha instalado basta baixar direto do site do Git acessando: https://git-scm.com/download/win  

- Clique no primeiro link para baixar
    
    ![Página de Download do Git](https://github.com/forjadev/blog/assets/53125029/89beff5c-020f-4e30-8175-7cc26fe8cfb3)

- Durante a instalação selecione a opção padrão _Use bundled OpenSSH_
    
    ![Janela de instalação do Git selecionado na opção de Use bundled OpenSSH](https://github.com/forjadev/blog/assets/53125029/3fb28b33-24b8-48f3-b8ab-c7cac2c07dd5)

## Criando a chave SSH
Daqui em diante o processo será o mesmo tanto para Linux quanto para Windows  
OBS: **Usuários de Windows** devem prosseguir utilizando __Git Bash__  

Para gerar uma nova chave usaremos o `ssh-keygen` e passaremos alguns parametros para reforçar a segurança como:  
<table>
  <tr>
    <th>Parâmetro</th>
    <th>Descrição</th>
  </tr>
  <tr>
    <td><code>-f</code></td>
    <td>Indicando aonde queremos que seja armazenada a chave e qual nome ela terá. No final será gerado dois arquivos, sendo um privado e um público (que terá a extensão <code>.pub</code>).</td>
  </tr>
  <tr>
    <td><code>-t</code></td>
    <td>Qual algoritmo de encriptografia vamos usar, o mais seguro atualmente é ed25519, sobre as outras chaves e comparações você encontra <a href="https://goteleport.com/blog/comparing-ssh-keys/">clicando aqui</a> para saber mais.</td>
  </tr>
  <tr>
    <td><code>-b</code></td>
    <td>Tamanho em bits (quanto maior, mais seguro). Por precaução utilizaremos a maior quantidade disponível.</td>
  </tr>
  <tr>
    <td><code>-E</code></td>
    <td>Qual o tipo de Hash será armazenada essa chave, o mais seguro disponível é sha512 e será esse que iremos usar.</td>
  </tr>
  <tr>
    <td><code>-C</code></td>
    <td>Por fim o comentário que será o identificador da chave, que neste caso será o <em>mesmo email usado no GitHub</em> e que <strong>não seja o email privado</strong>. Caso seu email primário seja privado, o GitHub gera para todo usuário um email para você, basta <a href="https://github.com/settings/emails">clicar aqui</a> e copiar o email que fica em baixo da opção <strong>Keep my email addresses private</strong> (o formato padrão é: <code>541234+<usuario>@users.noreply.github.com</code>).</td>
  </tr>
</table>

Com isso esclarecido, basta inserir todo o comando abaixo para prosseguirmos.
```sh
$ ssh-keygen \
	-f ~/.ssh/gitKey \
	-t ed25519 \
	-b 4096    \
	-E sha512  \
	-C "<your git email>" \
```  

Ao entrar com o comando irá aparecer uma tela pedindo uma senha. Como essa chave será utilizada apenas para validar o acesso ao github não é necessário, então aperte `Enter` para prosseguir.

Se formos ver a pasta ssh usando `ls ~/.ssh/` irá notar que foram gerados dois arquivos: `gitKey` e `gitKey.pub`  
A `gitKey` é a que contem os dados sensíveis e **nunca deve ser compartilhada** em hipotese alguma, pois é somente com ela permanecendo desconhecida que a `gitKey.pub` terá alguma validade e deverá gerar outra.
Por outro lado, a `gitKey.pub` pode ser mostrada sem problema algum e será ela que iremos mandar para o GitHub para autenticar com o nosso computador.  

Para ler o arquivo `gitKey.pub` basta usar o comando:
```sh
cat ~/.ssh/gitKey.pub
```

e ela tem que começar com: `ssh-ed25519 ` e terminar com o email utilizado no Git.  
![image](https://github.com/forjadev/blog/assets/53125029/be38c227-65a4-4daa-8d83-f4966cfa019d)

## Adicionando no GitHub

Após copiar nossa chave pública do SSH, abra a página de chaves no site do Github ([ou clique aqui](https://github.com/settings/keys)) e aperte o botão **New SSH Key**

![image](https://github.com/forjadev/blog/assets/53125029/53485dad-1cd7-43f2-8d73-cdaf87e06837)

E basta dar um nome que seja fácil para voce identificar de qual computador está configurado, em **Key type** usar _Authentication key_ e por fim inserir a _chave pública_ inteira no campo **Key**

![image](https://github.com/forjadev/blog/assets/53125029/7e2e6a53-f53e-4cfc-bbbd-ea93dcb58725)

## Registrando no dispositivo

Quanto ao GitHub já está tudo certinho, **PORÉMMM...* ainda temos que registrar a nossa chave no nosso próprio computador

> Ué, mas nós criamos a chave e até registrei no Github, não é só usar?

Por padrão as chaves geradas não são registradas pois não são em todos os casos que SSH é utilizado apenas localmente, por ser um arquivo ela pode ser exportada para outro sistema e só lá ser adicionada, por conta disso é de nossa responsabilidade fazermos esse registro
Um meio de verificar se a chave SSH está funcionado é mandando uma requisição para o GitHub, vamos testar:

![image](https://github.com/forjadev/blog/assets/53125029/fcf574a0-bc04-48f8-89cd-ac9b05caf1ec)

Vish...  `Permission Denied` não é nada bom... 
Para resolver isso temos que invocar o `ssh-agent` e `ssh-add` que vieram junto com a instalação do openssh que fizemos no começo do post.  

Primeiro temos que instanciar o ssh-agent utilizando o comando:

---
bash/zsh:
```sh
$ eval "$(ssh-agent -s)"
```

fish:
```fish
$ eval $(ssh-agent -c)
```
---

e voce provavelmente deve ver uma mensagem parecida com essa:

```
Agent pid 16916
```

Agora sim, só nos resta registramos nossa **chave privada**:
```sh
$ ssh-add ~/.ssh/githubKey
```

e novamente uma mensagem de confirmação deve ser exibida:

```
Identity added: /home/ManoelGomes/.ssh/gitKey (<your git email>)

# ou isso abaixo se for um usuário da maça:
Identity added: /Users/ManoelGomes/.ssh/gitKey (<your git email>)
```

E chegou o momento final ~~
Vamos novamente tentar conectar no server do GitHub para ver se nossa máquina está ou não autenticada

![image](https://github.com/forjadev/blog/assets/53125029/8a269ab7-3e64-48bc-b47c-650fa3acae5e)

Agora está funcionando!! 
Caso continue dando o erro de *acesso negado*, repita as etapas de adicionar a chave que funcionará.

E a partir de agora toda vez que voce for clonar algum repositório, esqueça o modo HTTP e use apenas SSH e nunca mais terá que inserir seu login e senha quando for trabalhar com algum outro projeto 

![image](https://github.com/forjadev/blog/assets/53125029/dc2d8380-84bf-43c5-a67b-b63de83d1aef)

E é isso! também não se esqueça de configurar a chave GPG para assinar cada commit que você fez!
E qualquer dúvida é só mandar aqui nos comentarios ;)
