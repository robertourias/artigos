# Windows Terminal

O Windows Terminal finalmente chegou para trazer uma melhor experiência para nossa vida via CLI, algo que era algo distante para usuários Windows.

Enquanto o Mac e Linux possuem diversas customizações para seus terminais, o Windows parecia ter parado no tempo com aquela tela azul do Power Shell.

Embora houvessem alternativas como o Hyper.js e CMDER, é sempre bom ter algo oficial, suportado pela Microsoft, e convenhamos, qual a dificuldade em suportar temas e customizações?

#### Antes

![Power Shell](https://baltaio.blob.core.windows.net/blog/windows-terminal-000.png "Power Shell")

#### Depois

![Windows Terminal](https://baltaio.blob.core.windows.net/blog/windows-terminal-001.PNG "Windows Terminal")

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

Para esta customização vamos precisar de dois módulos adicionais, o **Posh-git** e **Oh-my-posh**, que pode mser instalados utilizando os comandos abaixo no Windows Terminal.

```ps
Install-Module posh-git -Scope CurrentUser
Install-Module oh-my-posh -Scope CurrentUser
```

```
code $PROFILE
```

```ps
Import-Module posh-git
Import-Module oh-my-posh
Set-Theme Paradox
```

https://github.com/microsoft/cascadia-code/releases
https://github.com/powerline/fonts/tree/master/SourceCodePro

```
"fontFace": "Source Code Pro for Powerline",
```

```ps
Import-Module posh-git
Import-Module oh-my-posh
Set-Theme Paradox
Clear-Host
Write-Host "balta.io"
$DefaultUser = 'balta'
```
