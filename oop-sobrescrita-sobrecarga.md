# Orientação a objetos: Sobrescrita e Sobrecarga de métodos

Um dos recursos mais importantes da orientação a objetos é a possibilidade de sobrescrever ou sobrecarregar métodos e propriedades. Neste artigo vamos ver na prática como isto acontece.

## Sobrescrita

O ato de sobrescrever um método ou propriedade significa dar uma nova forma ao mesmo, uma nova versão. Vimos isto [no artigo anterior](https://balta.io/blog/orientacao-a-objetos) onde falamos sobre os pilares da orientação a objetos.

A sobrescrita de no C# é dada pelo uso em conjunto do modificador **virtual** e **override**. Sempre que marcamos uma propriedade ou método como **virtual**, significa que o mesmo pode ser sobrescrito.

A execução desta sobrescrita então é dada pela palavra reservada **override**, como podemos ver no exemplo abaixo.

