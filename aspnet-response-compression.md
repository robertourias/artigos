# ASP.NET Response Compression

O recurso de compressão de resposta está presente no ASP.NET de forma geral, o que significa que ele pode ser utilizado em projetos MVC, Razor Pages, Websites e APIs.

## O que é a compressão de respostas?

Sempre que fazemos uma requisição para uma aplicação, seja ela ASP.NET, PHP, Java, ou qualquer outra, a mesma é processada e um retorno nos é enviado.

Tanto no trabalho com APIs quanto na geração de conteúdo dinâmico (HTML) no servidor, o retorno sempre será um texto, hora HTML, hora JSON.

Todo texto que sai do servidor por sua vez tem um tamanho, que pode ser diminuído utilizando uma **compressão**, assim como fazemos com os arquivos .ZIP em nossos sistemas operacionais.

Habilitar a compressão de resposta reduz o tamanho do retorno do servidor, o que consome menos banda, torna as aplicações mais rápidas de serem trafegadas e até aumentam o [rankeamento do site](https://web.dev/uses-text-compression/).

Em resumo, basicamente o conteúdo a ser retornado é **zipado** no servidor e o browser **descompacta** ele antes de fazer a leitura.

Sempre que um browser possuir a capacidade de processar uma resposta compactada, ele enviará o cabeçalho <code>Accept-Encoding</code> informando os dados da compressão.

Assim que o servidor recebe uma requisição com o cabeçalho mencionado, ele adiciona o item <code>Content-Encoding</code> no cabeçalho da resposta.

Abaixo estão os possíveis valores para o cabeçalho da requisição a ser comprimida.

| Accept-Encoding            | Descrição                        |
| -------------------------- | -------------------------------- |
| br **<sup>(Padrão)</sup>** | Brotli Compressed Data Format    |
| gzip                       | Formato GZip                     |
| deflate                    | Deflate Compression              |
| identity                   | Resposta NÃO DEVE ser comprimida |
| \*                         | Qualquer um disponível           |

Existe um [lista de tipos de compressores](https://developer.mozilla.org/pt-BR/docs/Web/HTTP/Headers/Accept-Encoding), porém, podemos criar um totalmente customizado.

### Compressão nativa

Alguns servidores como IIS e Nginx possuem uma compressão nativa para arquivos como JS, CSS, HTML, JSON e XML. Caso seu servidor já ofereça compressão, não é necessário utilizar esta compressão adicional do ASP.NET.

Em adicional, é desnecessário comprimir imagens. Os formatos mais comuns como PNG e JPG por exemplo, já são compactados. O mesmo vale para arquivos muito pequenos, menores de 1KB por exemplo.

Nestes casos, a relação custo X benefício entre a compressão e tráfego dos arquivos acaba não compensando.
