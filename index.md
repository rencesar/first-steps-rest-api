---
layout: slide
---
<section data-markdown><script type="text/template">

{% include iftech_logo.html %}

## Rest API com Django Simples e Prático

<br>

[Renato César Lira Borges](https://github.com/rencesar)

29 de Novembro de 2018

</script></section>

<section data-markdown><script type="text/template">

## O que iremos abordar?

1. O que são Rest APIs
1. Metodos HTTPs
1. Códigos HTTPs
1. Como desenhar uma Rest API
1. Tipos de autenticação
1. Serializers
1. Views

</script></section>

<section data-markdown><script type="text/template">

## BUT WAIT!!
<img class="plain" width=50% src={{ "/images/whoareyou.gif" | prepend: site.baseurl }}>

</script></section>

<section data-markdown><script type="text/template">

## Quem sou eu

Renato César, Desenvolvedor de Software há mais de 3 anos.
Atualmente trabalho remotamente para [Broadsheet](https://www.broadsheet.com.au/) e curso Sistemas para Internet no IFPB.

</script></section>

<section data-markdown><script type="text/template">

## REST API?!
<img class="plain" width=50% src={{ "/images/masoqeisso.jpg" | prepend: site.baseurl }}>

</script></section>

<section data-markdown><script type="text/template">
### Representational State Transfer (REST)

Funciona como um comunicador entre qualquer aplicação e seu backend.

<img class="plain" width=40% src={{ "/images/rest-api-1.png" | prepend: site.baseurl }}>

</script></section>

<section data-markdown><script type="text/template">
## Para que serve
<br>

Para possibilitar uma comunicação entre diferentes streams (frameworks/linguagens), exemplo: 

* Aplicação movel (Kotlin)
* Front-end  (React)
* Back-end (Python)

</script></section>

<section data-markdown><script type="text/template">

## Como funciona

1. Cliente faz uma `request` `GET` para `myapi.com/movies/`
2. Servidor recebe requisição
3. Vai ao banco de dados pega os filmes
4. Retorna os filmes em `JSON` `response HTTP 200 (OK)`

</script></section>

<section data-markdown><script type="text/template">

## Conceitos de uma REST API

* Interface Uniforme
* Cliente Servidor
* Stateless
* Cacheable
* Sistema em camadas
* Código em demanda

</script></section>

<section data-markdown><script type="text/template">

## Interface Uniforme

Quando um desenvolvedor se familiarizar com um de suas APIs, ele deve ser capaz de replicar a mesma ideia para as outras APIs.

</script></section>

<section data-markdown><script type="text/template">

## Cliente - Servidor

Cliente e servidor operam separadamente. O único meio de recurso do cliente para o servidor é atraves de URIs.

</script></section>

<section data-markdown><script type="text/template">

## Stateless

Nenhum conteúdo de estado (state) deve ser mantido no servidor entre requisições, apenas o cliente irá gerenciar o estado (state). Não existe sessão nem histórico. Os requests funcionam de forma independente.

</script></section>

<section data-markdown><script type="text/template">

## Cacheable

Caching podem ser aplicados tanto no cliente como no servidor.

</script></section>

<section data-markdown><script type="text/template">

## Sistema em camadas

O seu serviço pode ser separado em múltiplas camadas sem que seu cliente saiba ou sofra impacto com isso.

</script></section>

<section data-markdown><script type="text/template">

## Código em demanda

O seu serviço pode retornar pedaços de códigos para serem execultados no cliente.

</script></section>

<section data-markdown><script type="text/template">

## HTTP códigos

<img class="plain" width=70% src={{ "/images/http-codes.jpg" | prepend: site.baseurl }}>


</script></section>

<section data-markdown><script type="text/template">

## HTTP methods

<table>
    <tbody>
        <tr>
            <td>GET</td>
            <td>O método `GET` solicita a representação de um recurso específico.</td>
        </tr>
        <tr>
            <td>HEAD</td>
            <td> Idêntica ao método `GET`, porém sem conter o corpo da resposta.</td>
        </tr>
        <tr>
            <td>POST</td>
            <td>Utilizado para submeter uma entidade a um recurso específico.</td>
        </tr>
        <tr>
            <td>PUT</td>
            <td>O método `PUT` substitui dados de um objeto atual por outros.</td>
        </tr>
        <tr>
            <td>DELETE</td>
            <td>O método `DELETE` remove um recurso específico.</td>
        </tr>
        <tr>
            <td>PATCH</td>
            <td>O método `PATCH` é utilizado para aplicar modificações parciais em um recurso.</td>
        </tr>
    </tbody>
</table>


</script></section>

<section data-markdown><script type="text/template">

## Endpoints

<img class="plain" width=70% src={{ "/images/endpoint.png" | prepend: site.baseurl }}>


</script></section>

<section data-markdown><script type="text/template">

## Autenticação vs Autorização

1. Autenticação

    * Verificar credenciais (login)
    * Verificar se você é você

2. Autorização

    * Verificar se com certas credenciais contém autorização de acesso

</script></section>

<section data-markdown><script type="text/template">

## Tipos de Autenticações

1. [Basic Authentication](https://tools.ietf.org/html/rfc2617)
2. [HMAC (hash based message authentication)](https://tools.ietf.org/html/rfc2617)
3. [OAuth](https://tools.ietf.org/html/rfc6749)
4. [JWT](https://tools.ietf.org/html/rfc7519)


click nos links e será redirecinado para as RFCs

</script></section>

<section data-markdown><script type="text/template">

## Serialização

```
class BookSerializer(serializers.Serializer):
    id = serializers.IntegerField(read_only=True)
    title = serializers.CharField(required=False, allow_blank=True, max_length=100)
    text = serializers.CharField(style={'base_template': 'textarea.html'})
```

</script></section>

<section data-markdown><script type="text/template">

## Resultado da Serialização

```
book = Book(id=10, title='Some title', text='First Chapter \n ...')
serializer = BookSerializer(book)
serializer.data
# {'id': 10, 'title': 'Some title', 'text': 'First Chapter \n ...'}
```

</script></section>

<section data-markdown><script type="text/template">

## Exemplo de ApiView

Considerando a mudança de Serializer para ModelSerializer e Objetos para Models.

```
@api_view(['GET', 'POST'])
def snippet_list(request):
    """
    List all code snippets, or create a new snippet.
    """
    if request.method == 'GET':
        books = Book.objects.all()
        serializer = BookSerializer(snippets, many=True)
        return Response(serializer.data)

    elif request.method == 'POST':
        serializer = BookSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)
```

</script></section>

<section data-markdown><script type="text/template">

## Referencias e materiais extras

* https://www.django-rest-framework.org/
* https://blog.restcase.com/restful-api-authentication-basics/
* https://tableless.com.br/entendendo-tokens-jwt/
* https://testdriven.io/dockerizing-django-with-postgres-gunicorn-and-nginx
* https://www.getpostman.com/
* https://medium.com/@marcosrabaioli/django-rest-framework-e-django-oauth-toolkit-c71f8920db08
* https://restfulapi.net/

</script></section>
<section data-markdown><script type="text/template">

## Renato César

[Github](https://github.com/rencesar)

[LinkedIn](https://linkedin.com/in/renatocesarlira/)

</script></section>
