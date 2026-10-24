---
hide:
  - navigation
---

<style>
.promo li a {
    float: left;
    width: 130px;
    height: 20px;
    text-align: center;
    margin: 10px 30px;
    padding: 150px 0 0 0;
    background-position: 0 50%;
    background-size: 130px auto;
    background-repeat: no-repeat;
    font-size: 120%;
    color: black;
}
.promo li {
    list-style: none;
}
</style>

<div class="badges">
    <iframe src="https://ghbtns.com/github-btn.html?user=encode&amp;repo=django-rest-framework&amp;type=watch&amp;count=true" class="github-star-button" allowtransparency="true" frameborder="0" scrolling="0" width="110px" height="20px"></iframe>

    <a href="https://github.com/encode/django-rest-framework/actions/workflows/main.yml">
        <img src="https://github.com/encode/django-rest-framework/actions/workflows/main.yml/badge.svg" class="status-badge">
    </a>

    <a href="https://pypi.org/project/djangorestframework/">
        <img src="https://img.shields.io/pypi/v/djangorestframework.svg" class="status-badge">
    </a>
</div>

---

<h1 style="position: absolute;
    width: 1px;
    height: 1px;
    padding: 0;
    margin: -1px;
    overflow: hidden;
    clip: rect(0,0,0,0);
    border: 0;">Django REST Framework</h1>

![Django REST Framework](img/logo-light.png#only-light)
![Django REST Framework](img/logo-dark.png#only-dark)

Bem-vindo ao Django REST framework, seu kit de ferramentas poderosas e flexíveis para você criar APIs Web com Python e Django.

Alguns motivos pelos quais você pode se interessar a usar o REST framework:

* A API Navegável (Browsable API) vem com uma interface prática e super amigável, pensada para tornar a vida dos desenvolvedores muito mais fácil.
* [Autenticação flexível e completa][authentication], com suporte nativo e integração fácil a padrões como [OAuth1a][oauth1-section] e [OAuth2][oauth2-section].
* [Serialização poderosa][serializers] que se adapta perfeitamente tanto aos modelos do [ORM do Django][modelserializer-section] quanto a fontes de dados [não-ORM][serializer-section].
* Totalmente modular e customizável — comece de forma simples com [views baseadas em funções][functionview-section] e escale para recursos avançados como [views genéricas][generic-views], [ViewSets][viewsets] e [roteadores automáticos (Routers)][routers].
* Documentação rica e detalhada, acompanhada de uma [comunidade ativa e acolhedora][group] para tirar dúvidas.
* Testado em produção e confiado por gigantes da tecnologia, como [Mozilla][mozilla], [Red Hat][redhat], [Heroku][heroku] e [Eventbrite][eventbrite].

---

## Requisitos

Antes de começar, certifique-se de que seu ambiente atenda aos requisitos mínimos:

* **Django**: versões 5.2, 6.0 e 6.1
* **Python**: versões 3.10, 3.11, 3.12, 3.13, 3.14 e 3.15

!!! tip "Dica de estabilidade e segurança"
    Recomendamos fortemente manter o Python e o Django sempre atualizados no último *patch release* oficial da versão escolhida para garantir estabilidade e correções de segurança.

---

### Pacotes Opcionais

O Django REST framework já vem pronto para uso, mas você pode expandir suas funcionalidades com bibliotecas opcionais conforme as necessidades do seu projeto:

* **Documentação e Schemas (OpenAPI)** — **[PyYAML][pyyaml]** (`5.1+`) e **[uritemplate][uritemplate]** (`3.0.0+`): Geram schemas estruturados automaticamente para integrar com Swagger, Redoc e ferramentas de documentação interativa.
* **Interface da API Navegável Aprimorada** — **[Markdown][markdown]** (`3.3.0+`) e **[Pygments][pygments]** (`2.7.0+`): Adicionam suporte a formatação rica em Markdown e realce colorido de sintaxe aos blocos de código e respostas JSON.
* **Filtros e Buscas Dinâmicas** — **[django-filter][django-filter]** (`1.0.1+`): Permite criar filtros poderosos na URL (ex: `?categoria=livros&preco_max=50`) com pouquíssimas linhas de código.
* **Controle de Acesso Granular** — **[django-guardian][django-guardian]** (`1.1.1+`): Gerenciamento avançado de permissões por objeto (ex: permitir que um usuário edite apenas o post que ele mesmo criou).


## Instalação

A maneira mais rápida e recomendada de começar é instalando via `pip`. Você também pode instalar juntos os pacotes opcionais que fizerem sentido para o seu projeto:

```bash
pip install djangorestframework
pip install markdown       # Suporte a Markdown para a API navegável.
pip install django-filter  # Suporte a filtros
```

Ou, se preferir, você também pode clonar o projeto diretamente do GitHub:

```bash
git clone https://github.com/encode/django-rest-framework
```

Depois de instalar, basta registrar o `'rest_framework'` na lista de `INSTALLED_APPS` do seu `settings.py`:

```python
INSTALLED_APPS = [
    # ...
    "rest_framework",
]
```

Para aproveitar ao máximo a interface visual da API Navegável (incluindo login e logout pelo próprio navegador), adicione as seguintes rotas ao seu arquivo `urls.py` principal:

```python
urlpatterns = [
    # ...
    path("api-auth/", include("rest_framework.urls"))
]
```

*Dica: o caminho `'api-auth/'` é apenas uma sugestão — você pode usar o prefixo de URL que preferir no seu projeto.*

## Exemplo Prático

Que tal vermos o framework em ação? Vamos construir juntos uma API simples e completa, com suporte a leitura e escrita, para gerenciar os usuários do nosso projeto.

Toda a configuração global da sua API fica organizada em um único dicionário chamado `REST_FRAMEWORK`. Para começar, adicione esta configuração básica no seu `settings.py`:

```python
REST_FRAMEWORK = {
    # Use as permissões padrão `django.contrib.auth` do Django,
    # ou permita acesso somente leitura para usuários não autenticados.
    "DEFAULT_PERMISSION_CLASSES": [
        "rest_framework.permissions.DjangoModelPermissionsOrAnonReadOnly"
    ]
}
```

Lembre-se apenas de conferir se o `'rest_framework'` já está registrado no seu `INSTALLED_APPS`!

Tudo pronto para estruturar nossa API! Aqui está como fica o `urls.py` principal conectando os modelos, serializadores, views e rotas:

```python
from django.urls import path, include
from django.contrib.auth.models import User
from rest_framework import routers, serializers, viewsets


# Serializadores definem a representação da API.
class UserSerializer(serializers.HyperlinkedModelSerializer):
    class Meta:
        model = User
        fields = ["url", "username", "email", "is_staff"]


# ViewSets definem o comportamento da view.
class UserViewSet(viewsets.ModelViewSet):
    queryset = User.objects.all()
    serializer_class = UserSerializer


# Routers oferecem uma forma simples de determinar automaticamente a configuração de URLs.
router = routers.DefaultRouter()
router.register(r"users", UserViewSet)

# Configura a nossa API usando roteamento automático de URLs.
# Adicionalmente, incluímos URLs de login para a API navegável.
urlpatterns = [
    path("", include(router.urls)),
    path("api-auth/", include("rest_framework.urls", namespace="rest_framework")),
]
```

Prontinho! Agora é só abrir a API no seu navegador em [http://127.0.0.1:8000/](http://127.0.0.1:8000/) para explorar os seus novos endpoints de usuários. Fazendo login pelo botão no canto superior direito, você poderá criar, editar e excluir registros com total facilidade direto pela interface web!

## Início Rápido (Quickstart)

Animado para colocar a mão na massa? O nosso [Guia de Início Rápido][quickstart] é o caminho mais direto para você aprender o fluxo completo e criar sua primeira API em poucos minutos!

## Desenvolvimento

Quer fazer parte da evolução do projeto? Dê uma olhada nas nossas [Diretrizes de Contribuição][contributing] para aprender como configurar o ambiente local, rodar a suíte de testes e enviar suas próprias melhorias.

## Suporte da Comunidade

Ficou com alguma dúvida ou travou em algo? A comunidade está sempre pronta para ajudar! Participe do [grupo de discussão oficial][group], troque uma ideia no canal `#restframework` na Libera.Chat ou faça sua pergunta no [Stack Overflow][stack-overflow] usando a tag ['django-rest-framework'][django-rest-framework-tag].

## Segurança

Se você encontrar qualquer vulnerabilidade ou questão de segurança, **por favor relate enviando um e-mail para security@encode.io**.

A equipe de mantenedores trabalhará diretamente com você para analisar e resolver a situação com prioridade, antes de qualquer divulgação pública.

## Licença

Copyright © 2011-presente, [Encode OSS Ltd](https://www.encode.io/).
Todos os direitos reservados.

Redistribution and use in source and binary forms, with or without
modification, are permitted provided that the following conditions are met:

* Redistributions of source code must retain the above copyright notice, this
  list of conditions and the following disclaimer.

* Redistributions in binary form must reproduce the above copyright notice,
  this list of conditions and the following disclaimer in the documentation
  and/or other materials provided with the distribution.

* Neither the name of the copyright holder nor the names of its
  contributors may be used to endorse or promote products derived from
  this software without specific prior written permission.

THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS" AND
ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED
WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE
DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE LIABLE
FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL
DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR
SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER
CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY,
OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE
OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.

[mozilla]: https://www.mozilla.org/en-US/about/
[redhat]: https://www.redhat.com/
[heroku]: https://www.heroku.com/
[eventbrite]: https://www.eventbrite.co.uk/about/
[pyyaml]: https://pypi.org/project/PyYAML/
[uritemplate]: https://pypi.org/project/uritemplate/
[markdown]: https://pypi.org/project/Markdown/
[pygments]: https://pypi.org/project/Pygments/
[django-filter]: https://pypi.org/project/django-filter/
[django-guardian]: https://github.com/django-guardian/django-guardian
[index]: .
[oauth1-section]: api-guide/authentication/#django-rest-framework-oauth
[oauth2-section]: api-guide/authentication/#django-oauth-toolkit
[serializer-section]: api-guide/serializers#serializers
[modelserializer-section]: api-guide/serializers#modelserializer
[functionview-section]: api-guide/views#function-based-views

[quickstart]: tutorial/quickstart.md

[generic-views]: api-guide/generic-views.md
[viewsets]: api-guide/viewsets.md
[routers]: api-guide/routers.md
[serializers]: api-guide/serializers.md
[authentication]: api-guide/authentication.md

[contributing]: community/contributing.md

[group]: https://groups.google.com/forum/?fromgroups#!forum/django-rest-framework
[stack-overflow]: https://stackoverflow.com/
[django-rest-framework-tag]: https://stackoverflow.com/questions/tagged/django-rest-framework
[security-mail]: mailto:rest-framework-security@googlegroups.com

