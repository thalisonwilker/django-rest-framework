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

O Django REST framework é um kit de ferramentas poderoso e flexível para a construção de Web APIs.

Alguns motivos pelos quais você pode querer usar o REST framework:

* A API navegável pela Web (Browsable API) é um enorme ganho de usabilidade para os desenvolvedores.
* [Políticas de autenticação][authentication], incluindo pacotes para [OAuth1a][oauth1-section] e [OAuth2][oauth2-section].
* [Serialização][serializers] com suporte a fontes de dados [ORM][modelserializer-section] e [não-ORM][serializer-section].
* Totalmente customizável — use simplesmente [views baseadas em funções][functionview-section] tradicionais se você não precisar dos recursos [mais][generic-views] [avançados][viewsets] [como routers][routers].
* Documentação abrangente e [ótimo suporte da comunidade][group].
* Utilizado e aprovado por empresas reconhecidas internacionalmente, incluindo [Mozilla][mozilla], [Red Hat][redhat], [Heroku][heroku] e [Eventbrite][eventbrite].

---

## Requisitos

O REST framework requer o seguinte:

* Django (5.2, 6.0, 6.1)
* Python (3.10, 3.11, 3.12, 3.13, 3.14, 3.15)

Nós **recomendamos fortemente** e oferecemos suporte oficial apenas para a versão mais recente de correção (patch release) de cada série do Python e do Django.

Os seguintes pacotes são opcionais:

* [PyYAML][pyyaml], [uritemplate][uritemplate] (5.1+, 3.0.0+) - Suporte para geração de schemas.
* [Markdown][markdown] (3.3.0+) - Suporte a Markdown para a API navegável.
* [Pygments][pygments] (2.7.0+) - Adiciona realce de sintaxe ao processamento de Markdown.
* [django-filter][django-filter] (1.0.1+) - Suporte a filtros.
* [django-guardian][django-guardian] (1.1.1+) - Suporte a permissões em nível de objeto.

## Instalação

Instale usando o `pip`, incluindo quaisquer pacotes opcionais que desejar...

```bash
pip install djangorestframework
pip install markdown       # Suporte a Markdown para a API navegável.
pip install django-filter  # Suporte a filtros
```

...ou clone o projeto a partir do GitHub:

```bash
git clone https://github.com/encode/django-rest-framework
```

Adicione `'rest_framework'` à sua configuração `INSTALLED_APPS`:

```python
INSTALLED_APPS = [
    # ...
    "rest_framework",
]
```

Se você planeja usar a API navegável, provavelmente também desejará adicionar as views de login e logout do REST framework. Adicione o seguinte ao seu arquivo `urls.py` raiz:

```python
urlpatterns = [
    # ...
    path("api-auth/", include("rest_framework.urls"))
]
```

Observe que o caminho da URL pode ser qualquer um que você desejar.

## Exemplo

Vamos dar uma olhada em um exemplo rápido de como usar o REST framework para construir uma API simples baseada em modelos.

Criaremos uma API de leitura e escrita para acessar informações sobre os usuários do nosso projeto.

Quaisquer configurações globais para uma API do REST framework são mantidas em um único dicionário de configuração chamado `REST_FRAMEWORK`. Comece adicionando o seguinte ao seu módulo `settings.py`:

```python
REST_FRAMEWORK = {
    # Use as permissões padrão `django.contrib.auth` do Django,
    # ou permita acesso somente leitura para usuários não autenticados.
    "DEFAULT_PERMISSION_CLASSES": [
        "rest_framework.permissions.DjangoModelPermissionsOrAnonReadOnly"
    ]
}
```

Não se esqueça de verificar se você também adicionou `rest_framework` ao seu `INSTALLED_APPS`.

Estamos prontos para criar nossa API agora.
Aqui está o módulo `urls.py` raiz do nosso projeto:

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

Agora você pode abrir a API no seu navegador em [http://127.0.0.1:8000/](http://127.0.0.1:8000/) e visualizar a sua nova API de 'usuários'. Se você usar o controle de login no canto superior direito, também poderá adicionar, criar e excluir usuários do sistema.

## Início Rápido (Quickstart)

Mal pode esperar para começar? O [guia de início rápido][quickstart] é a forma mais rápida de começar e construir APIs com o REST framework.

## Desenvolvimento

Consulte as [Diretrizes de contribuição][contributing] para obter informações sobre como clonar o repositório, executar a suíte de testes e ajudar a manter a base de código do REST framework.

## Suporte

Para suporte, consulte o [grupo de discussão do REST framework][group], entre no canal `#restframework` em `irc.libera.chat` ou faça uma pergunta no [Stack Overflow][stack-overflow], certificando-se de incluir a tag ['django-rest-framework'][django-rest-framework-tag].

## Segurança

**Por favor, relate problemas de segurança enviando um e-mail para security@encode.io**.

Os mantenedores do projeto trabalharão com você para resolver quaisquer problemas necessários, antes de qualquer divulgação pública.

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

