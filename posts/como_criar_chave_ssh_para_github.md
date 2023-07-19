# Como criar e registrar uma chave SSH para o GitHub
> Autor: [Junio Santos](https://github.com/imnotjuniodev)

#### Índice
- ![O que é](#O%20que%20é)
	- ![Pré-requisitos](#Pré-requisitos)
- ![Instalando OpenSSH no seu computador](#Instalando%20OpenSSH%20no%20seu%20computador)
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
> Caso ja openssh instalado em seu pc, pode pular para [Criando a Chave SSH](#Criando%20a%20chave%20SSH)

<details>
	<summary><strong>
	MacOS:
	</strong></summary>
```sh
$ brew install git 
```
</details>
<details>
	<summary><strong>
	Linux:
	</strong></summary>
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

</details>
<details>
	<summary><strong>
	Windows:
	</strong></summary>
	
Uma das mais maneiras mais fáceis para conseguir gerenciar sua chave SSH no Windows é utilizando o **Git Bash**

Caso não tenha instalado basta baixar direto do site do Git acessando: https://git-scm.com/download/win  

- Clique no primeiro link para baixar
    ![image](https://github.com/forjadev/Content-Creations/assets/53125029/8a5bd2fe-61de-4be0-97d4-3c3b2bd7d31f)

- Durante a instalação selecione a opção padrão _**Use bundled OpenSSH**_
    
 <!--   <img alt="Janela de instalação do Git selecionado na opção de Use bundled OpenSSH" src="https://github.com/forjadev/Content-Creations/assets/53125029/3fb28b33-24b8-48f3-b8ab-c7cac2c07dd5"> -->
  
</details>

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

<img src="https://github.com/forjadev/Content-Creations/assets/53125029/5071f6d1-e2b9-4f31-a6c0-1823e6ef7608">

## Adicionando no GitHub

Após copiar nossa chave pública do SSH, abra a página de chaves no site do Github ([ou clique aqui](https://github.com/settings/keys)) e aperte o botão **New SSH Key**

<img src="https://github.com/forjadev/Content-Creations/assets/53125029/789963af-2fea-409e-97fc-9826bceb33bd">

E basta dar um nome que seja fácil para voce identificar de qual computador está configurado, em **Key type** usar _Authentication key_ e por fim inserir a _chave pública_ inteira no campo **Key**

<img src="https://github.com/forjadev/Content-Creations/assets/53125029/5fc2f631-c199-406c-a379-bfe24553d80d">

## Registrando no dispositivo

Quanto ao GitHub já está tudo certinho, **PORÉMMM...* ainda temos que registrar a nossa chave no nosso próprio computador

> Ué, mas nós criamos a chave e até registrei no Github, não é só usar?

Por padrão as chaves geradas não são registradas pois não são em todos os casos que SSH é utilizado apenas localmente, por ser um arquivo ela pode ser exportada para outro sistema e só lá ser adicionada, por conta disso é de nossa responsabilidade fazermos esse registro
Um meio de verificar se a chave SSH está funcionado é mandando uma requisição para o GitHub, vamos testar:


<img src="https://github.com/forjadev/Content-Creations/assets/53125029/d15fe93b-2996-47a3-996d-238e9c229fb7" alt="img">


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

<img src="https://github.com/forjadev/Content-Creations/assets/53125029/ca3f9864-f1c2-4976-b40e-52705ef36b2d" alt="img">

Agora está funcionando!! 
Caso continue dando o erro de *acesso negado*, repita as etapas de adicionar a chave que funcionará.

E a partir de agora toda vez que voce for clonar algum repositório, esqueça o modo HTTP e use apenas SSH e nunca mais terá que inserir seu login e senha quando for trabalhar com algum outro projeto 

<img src="https://github.com/forjadev/Content-Creations/assets/53125029/06171466-8473-4a43-881d-13ffac73e60c" alt="img">

E é isso! também não se esqueça de configurar a chave GPG para assinar cada commit que você fez!
E qualquer dúvida é só mandar aqui nos comentarios ;)
