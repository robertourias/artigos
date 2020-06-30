# Windows Terminal

O Windows Terminal finalmente chegou para trazer uma melhor experiência para nossa vida via CLI, algo que era algo distante para usuários Windows.

Enquanto o Mac e Linux possuem diversas customizações para seus terminais, o Windows parecia ter parado no tempo com aquela tela azul do Power Shell.

Embora houvessem alternativas como o Hyper.js e CMDER, é sempre bom ter algo oficial, suportado pela Microsoft, e convenhamos, qual a dificuldade em suportar temas e customizações?

#### Antes

![Power Shell](https://baltaio.blob.core.windows.net/blog/windows-terminal-000.png "Power Shell")

#### Depois

![Windows Terminal](https://baltaio.blob.core.windows.net/blog/windows-terminal-001.PNG "Windows Terminal")

#### Visual Studio Code

![Visual Studio Code](https://baltaio.blob.core.windows.net/blog/windows-terminal-003.PNG "Visual Studio Code")

## Instalação

Caso ainda não possua o Windows Terminal instalado, você pode fazer seu Download em uma das fontes oficiais listadas abaixo.

- [GitHub](https://github.com/microsoft/terminal)
- [Microsoft Store](https://www.microsoft.com/pt-br/p/windows-terminal/9n0dx20hk701?activetab=pivot:overviewtab)

### Políticas do Power Shell

Em alguns casos, durante a execução de comandos NPM no Windows, você poderá receber um erro com a seguinte mensagem: _"O arquivo XXXX não pode ser carregado porque a execução de scripts foi desabilitada neste sistema"_

Isto ocorre pois as políticas de execução do Power Shell em seu sistema não estão habilitada. Para resolver este problema, feche todos os terminais abertos e abra um novo Power Shell em modo administrador (Clicar com botão direito do mouse sobre o ícone do Power Shell e acessar a opção **Executar como Administrador**).

Nesta nova janela que se abriu, como administrador, execute o seguinte comando:

Set-ExecutionPolicy -ExecutionPolicy RemoteSigned

Feche novamente a janela e reabra o Power Shell ou qualquer terminal que esteja utilizando. Desta vez não precisa ser como administrador. O erro deve parar de acontecer.

## Posh-git e Oh-my-posh

Para esta customização vamos precisar de dois módulos adicionais, o **Posh-git** e **Oh-my-posh**, que podem ser instalados utilizando os comandos abaixo no Windows Terminal.

```ps
Install-Module posh-git -Scope CurrentUser
Install-Module oh-my-posh -Scope CurrentUser
```

Enquanto o **Posh-git** vai nos dar várias informações sobre o repositório atual, como branch, commits entre outros, o **Oh-my-posh** vai tornar tudo mais bonito.

## Alterando o perfil

Com os módulos instalados, vamos configurar o perfil do terminal para sempre carregar estes módulos durante sua inicialização.

> Para este passo, precisaremos do [Visual Studio Code](https://balta.io/blog/dotnet-instalacao-configuracao-e-primeiros-passos) instalado.

Execute o seguinte código para abrir seu perfil atual no VS Code em modo de edição.

```
code $PROFILE
```

Em seguida, adicione as seguintes linhas ao arquivo:

```ps
Import-Module posh-git
Import-Module oh-my-posh
Set-Theme Paradox
```

O que fizemos foi carregar os dois módulos instalados e definir o tema padrão como **Paradox**. Você pode conferir a [lista completa de temas aqui](https://github.com/JanDeDobbeleer/oh-my-posh).

## Fontes

O **Oh-my-posh** utiliza alguns ícones para exibir a branch atual do repositório, dentre outros, então precisamos instalar alguma fonte que dê suporte ao mesmo.

Particularmente, prefiro a **Source Code Pro for Powerline**, mas a nova fonte, lançada recentemente pela Microsoft, chamada **Cascadia** também é muito bonita. Aí vai do seu gosto.

- [Fonte Cascadia](https://github.com/microsoft/cascadia-code/releases)
- [Fonte Source Code Pro](https://github.com/powerline/fonts/tree/master/SourceCodePro)

Feita a instalação das fontes, abra um novo Windows Terminal (Caso não esteja aberto) e pressione <kbd>CTRL</kbd>+<kbd>,</kbd> para exibir as configurações do mesmo.

Então realize a alteração da configuração <code>fontFace</code> conforme mostrado abaixo:

##### Versão Source Code Pro

```
"fontFace": "Source Code Pro for Powerline",
```

##### Versão Cascadia Code

```
"fontFace":  "Cascadia Code PL"
```

## Configurações adicionais

Em adicional, eu gosto de sempre limpar a tela do terminal em seu início, e isto pode ser feito utilizando a linha <code>Clear-Host</code> como mostrado abaixo. Também podemos adicionar uma mensagem de boas vindas utilizando o <code>Write-Host</code>.

Para finalizar, podemos trocar o nome da variável que exibe o caminho atual, para ter algo mais curto, sem o nome do usuário/computador por exemplo. Isto é feito utilizando o <code>\$DefaultUser</code>.

Execute novamente o código em um terminal para abrir seu perfil atual no VS Code em modo de edição. 
```
code $PROFILE
```
Em seguida, acrescente as seguintes linhas ao arquivo:

```ps
Clear-Host
Write-Host "balta.io"
$DefaultUser = 'balta'
```

Pronto, você agora tem um lindo terminal a nível Mac e Linux para ostentar.

## Fontes

- [Post do Scott Hanselman](https://www.hanselman.com/blog/HowToMakeAPrettyPromptInWindowsTerminalWithPowerlineNerdFontsCascadiaCodeWSLAndOhmyposh.aspx)
