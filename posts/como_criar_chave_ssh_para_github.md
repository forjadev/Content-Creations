# Como criar e registrar uma chave SSH para o GitHub
> Autor: [Junio Santos](https://github.com/imnotjuniodev)

#### Índice
- [O que é](#O%20que%20é)
	- [Pré-requisitos](#Pré-requisitos)
- [Instalando OpenSSH no seu computador](#Instalando%20OpenSSH%20no%20seu%20computador)
- [Criando a chave SSH](#Criando%20a%20chave%20SSH)
- [Adicionando no GitHub](#Adicionando%20no%20GitHub)
- [Registrando no dispositivo](#Registrando%20no%20dispositivo)

## O que é
OpenSSH é uma ferramenta que auxilia em conexões remotas utilizando o protocolo SSH, servindo como um meio de autenticação prático onde não é necessário senhas e usernames. Aplique esse conceito ao Github (ou GitLab) e voce poderá clonar seus projetos e eles automaticamente autenticar com a sua conta, podendo manter o repositório sincronizado com maior segurança e sem necessidade de senha.

> Se voce utiliza o **Github Desktop** não é necessário configurar alguma chave SSH, mas é bom ter configurado caso em algum momento precise utilizar Git fora da GUI

## Pré-requisitos: 
- **openSSH**
- **Git**
 
## Instalando OpenSSH no seu computador
> Caso ja openssh instalado em seu pc, pode pular para [Criando a Chave 
SSH](#Criando%20a%20chave%20SSH)

{% collapsible MacOS %}  
MacOS já vem com o git instalando por padrão, e openssh já vem embutido sem a necessidade de fazer qlqer download extra.  
Caso queira confirmar que realmente está tudo certo basta executar:
```sh
brew install git
```
{% endcollapsible %}

{% collapsible Linux %}  
Para conferir se já está disponível no seu computador utilize o comando:
```sh
if command -v /usr/bin/ssh-agent &> /dev/null; then echo "ta tudo certo"; fi
```
Se de fato apareceu `ta tudo certo` no teu terminal significa que ta tudo certo e pode pular essa parte.

```sh
# -- ubuntu/mint/debian
sudo apt install openssh-client

# -- fedora
sudo dnf install openssh

# -- openSUSE
sudo zypper install openssh

# -- arch
sudo pacman -S openssh
```
{% endcollapsible %}

{% collapsible Windows %}  
Uma das mais maneiras mais fáceis para conseguir gerenciar sua chave SSH no Windows é utilizando o **Git Bash**

Caso não tenha instalado basta baixar direto do site do Git acessando: https://git-scm.com/download/win  

- Clique no primeiro link para baixar
    ![image](https://imgur.com/2rYMvBm.png)

- Durante a instalação selecione a opção padrão _**Use bundled OpenSSH**_
{% endcollapsible %}

## Criando a chave SSH
Daqui em diante o processo será o mesmo tanto para Linux quanto para Windows  
OBS: **Usuários de Windows** devem prosseguir utilizando **Git Bash**  

Para gerar uma nova chave usaremos o `ssh-keygen` e passaremos alguns parametros para reforçar a segurança como:  

| Parâmetro | Descrição |
| --------- | --------- |
| -f | Indica onde a chave será armazenada e qual nome ela terá. Gera dois arquivos, um privado e um público (com extensão .pub). |
| -t | Escolhe o algoritmo de criptografia a ser usado. O mais seguro atualmente é ed25519. Mais informações sobre outros algoritmos e comparações podem ser encontradas [aqui](link). |
| -b | Define o tamanho em bits da chave. Quanto maior, mais seguro. Por precaução, utilizaremos o maior disponível. |
| -E | Especifica o tipo de hash no qual a chave será armazenada. O mais seguro disponível é sha512, e é isso que usaremos. |
| -C | Define o comentário que será o identificador da chave. Neste caso, será o mesmo e-mail usado no GitHub, desde que não seja o e-mail privado. Se o seu e-mail principal for privado, o GitHub gera um e-mail para você. Basta [clicar aqui](link) e copiar o e-mail que fica abaixo da opção "Keep my email addresses private" (deve ser algo como: 541234+<usuario>@users.noreply.github.com). |


Com isso esclarecido, basta inserir todo o comando abaixo para prosseguirmos.
```sh
ssh-keygen \
	-b 4096    \
	-E sha512  \
	-t ed25519 \
	-f ~/.ssh/gitKey \
	-C "seu@email.com"
```
> lembre-se, o email não pode ficar entre `<` e `>`, deve ser identico ao utilizado no GitHub

Ao entrar com o comando irá aparecer uma tela pedindo uma senha. Como essa chave será utilizada apenas para validar o acesso ao github não é necessário, então aperte `Enter` para prosseguir.

Se formos ver a pasta ssh usando `ls ~/.ssh/` irá notar que foram gerados dois arquivos: `gitKey` e `gitKey.pub`  
A `gitKey` é a que contem os dados sensíveis e **nunca deve ser compartilhada** em hipotese alguma, pois é somente com ela permanecendo desconhecida que a `gitKey.pub` terá alguma validade e deverá gerar outra.
Por outro lado, a `gitKey.pub` pode ser mostrada sem problema algum e será ela que iremos mandar para o GitHub para autenticar com o nosso computador.  

Para ler o arquivo `gitKey.pub` basta executar:
```sh
cat ~/.ssh/gitKey.pub
```

esse arquivo terá que começar com: `ssh-ed25519` e terminar com o email utilizado no Git.

![img](https://imgur.com/vyrDLuj.png)

e apenas copie essa linha pois será necessária para a próxima etapa.

## Adicionando no GitHub

Após copiar nossa chave pública do SSH, abra a página de chaves no site do Github ([ou clicando aqui](https://github.com/settings/keys)) e aperte o botão **New SSH Key**

![img](https://imgur.com/BLH6ZHQ.png)

E basta dar um nome que seja fácil para voce identificar de qual computador está configurado, em **Key type** usar _Authentication key_ e por fim inserir a _chave pública_ inteira no campo **Key**

![img](https://imgur.com/TrST1xg.png)

## Registrando no dispositivo

Quanto ao GitHub já está tudo certinho, **PORÉMMM...* ainda temos que registrar a nossa chave no nosso próprio computador

> Ué, mas nós criamos a chave e até registrei no Github, não é só usar?

Por padrão as chaves geradas não são registradas pois não são em todos os casos que SSH é utilizado apenas localmente, por ser um arquivo ela pode ser exportada para outro sistema e só lá ser adicionada, por conta disso é de nossa responsabilidade fazermos esse registro
Um meio de verificar se a chave SSH está funcionado é mandando uma requisição para o GitHub, vamos testar:

![img](https://imgur.com/9aRsxIU.png)


Vish...  `Permission Denied` não é nada bom... 
Para resolver isso temos que invocar o `ssh-agent` e `ssh-add` que vieram junto com a instalação do openssh que fizemos no começo do post.  

Antes, vamos instanciar o ssh-agent utilizando o comando:

```sh
# bash/zsh:
eval "$(ssh-agent -s)"

# fish
eval $(ssh-agent -c)
```

e voce provavelmente deve ver uma mensagem parecida com `Agent pid 16916`
Agora sim, só nos resta registramos nossa **chave privada**:
```sh
ssh-add ~/.ssh/githubKey
```

e novamente uma mensagem de confirmação deve ser exibida:

```
Identity added: /home/ManoelGomes/.ssh/gitKey (<your git email>)

# ou a mensagem abaixo se for um usuário da maçã
Identity added: /Users/ManoelGomes/.ssh/gitKey (<your git email>)
```

E chegamos ao momento final ~~
Vamos tentar conectar no server do GitHub para ver se nossa máquina está ou não autenticada:

![img](https://imgur.com/WuDoYpy.png)

Agora está funcionando!!
Caso continue dando o erro de *acesso negado*, repita as etapas de adicionar a chave que funcionará.

---

E a partir de agora toda vez que voce for clonar algum repositório, esqueça o modo HTTP e use apenas SSH e nunca mais terá que inserir seu login e senha quando for trabalhar em algum outro projeto 

![img](https://i.imgur.com/QplPuVK.png)

E é isso! também não se esqueça de configurar a chave GPG para assinar cada commit que você fez!
E qualquer dúvida é só mandar aqui nos comentarios ;)
