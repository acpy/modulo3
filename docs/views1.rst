.. Copyright 2012 Luciano G. S. Ramalho; alguns direitos reservados
   Este trabalho é distribuído sob a licença Creative Commons 3.0 BY-NC-SA  
   (Atribuição-Compartilhamento pela mesma Licença 3.0-Uso não comercial). 
   Resumindo, você pode:
     - copiar, distribuir e exibir o texto e ilustrações
     - criar obras derivadas
   Sob as seguintes condições:
     - Atribuição: Você deve dar crédito ao autor original, mantendo este
       aviso em todos os arquivos derivados
     - Uso não comercial — Você não pode usar esta obra para fins comerciais  
     - Compartilhamento pela mesma Licença: se você alterar, transformar ou
       derivar outro trabalho a partir deste, você pode distribuir o trabalho
       resultante somente sob a mesma licença, ou uma similar e compatível
   Para fazer uso comercial deste conteúdo, entre em contato com o autor,
   Luciano Ramalho, para obter outra licença de uso.

===========================================
Views, URLs e templates: o básico
===========================================

.. contents:: Conteúdo


--------------------------------------
Views genéricas
--------------------------------------

Vamos começar o tema das views apresentando as views genéricas que vêm prontas com o Django. A documentação do Django considera as views genéricas um tópico avançado, mas temos três ótimos motivos para começar por elas:

1. usando as views genéricas não precisamos escrever código Python para tratar *requests*, e podemos praticar rapidamente a configuração de URLs e a programação de templates, que são as principais novidades deste capítulo

2. conhecendo bem as views genéricas você evita "reinventar a roda" e escrever código desnecessariamente, seguindo os princípios :term:`DRY` e :term:`KISS`

3. mesmo quando as views genéricas incluídas no Django não resolverem o seu problema, você poderá se inspirar em suas convenções para criar as suas próprias views parametrizadas, tornando mais flexível a sua aplicação e seguindo o princípio :term:`DRY`

O mecanismo de views genéricas mudou radicalmente no Django 1.3: antes essas views eram implementadas como funções, agora são classes, facilitando o reuso do código por composição e especialização.

O lugar para começar a estudar as views genéricas baseadas em classe é o *topic guide*: https://docs.djangoproject.com/en/dev/topics/class-based-views/

A referência oficial fica em:
https://docs.djangoproject.com/en/dev/ref/class-based-views/

A referência do jeito antigo de fazer views genéricas é:
https://docs.djangoproject.com/en/dev/ref/generic-views/


----------------------------------
Localização dos templates
----------------------------------

.. image:: _static/templates-dir.*
   :align: right

- a busca por templates no sistema de arquivos é feita por funções configuradas em ``settings.py``::

    TEMPLATE_LOADERS = (
        'django.template.loaders.filesystem.load_template_source',
        'django.template.loaders.app_directories.load_template_source',
        # 'django.template.loaders.eggs.load_template_source',
    )
    
- a função ``loaders.app_directories.load_template_source`` permite que cada aplicação tenha seu próprio diretório de templates

- as *generic views* por convenção procuram templates em locais como: ``«aplicação»/«modelo»_detail.html``

- assim, a melhor forma de organizar os templates no sistema de arquivos é em diretórios como segue (sim, «aplicação» aparece duas vezes)::

    «projeto»/«aplicação»/«templates»/«aplicação»/*.html 

-----------------------------------
Configuração das URLs
-----------------------------------

- Django usa expressões regulares configuradas no módulo ``urls.py`` para analisar as URLs das requisições e invocar a *view* apropriada para cada padrão de URL

- em um projeto modular, recomenda-se que cada aplicação tenha seu próprio módulo ``«aplicação»/urls.py``, estes são incluídos no ``urls.py`` principal na raiz do projeto::

    urlpatterns = patterns('',
        (r'^cat/', include('biblio.catalogo.urls')),
        (r'^admin/doc/', include('django.contrib.admindocs.urls')),
        (r'^admin/', include(admin.site.urls)),
        (r'^db/(.*)', databrowse.site.root),
    )

- em ``«aplicação»/urls.py`` a análise dos caminhos de URLs continua::

    urlpatterns = patterns('',
        url(r'^$', list_detail.object_list, livros_info),
        url(r'^livro/(?P<object_id>\d+)/$', list_detail.object_detail, livros_info),
    )

- no exemplo acima, a URL ``http://exemplo.com/cat/`` aciona a *view* ``object_list``

- no mesmo exemplo, a URL ``http://exemplo.com/cat/livro/3/`` aciona ``object_detail`` 

-------------------------------------------
Configuração de *views* genéricas
-------------------------------------------

- ``urls.py`` é o único código Python necessário para uma *generic view* funcionar; por exemplo, veja o módulo ``biblio/catalogo/urls.py``:

.. code-block:: python
    :linenos:

    from django.conf.urls.defaults import *
    from django.views.generic import list, detail
    
    from biblio.catalogo.models import Livro
        
    urlpatterns = patterns('',
        url(r'^$', list.ListView.as_view(model=Livro)),
        url(r'^livro/(?P<pk>\d+)/$', detail.DetailView.as_view(model=Livro)),
    )
    
- **linha 2:** importação dos módulos ``views.generic.list`` e ``views.generic.detail``

- **linha 4:** importação do model que vai fornecer dados para as *generic views*

- **linha 7:** configuração da *generic view* para listar todos os livros

- **linha 8:** o grupo nomeado ``(?P<pk>\d+)`` será acessado pela *view* como um parâmetro nomeado como ``pk``

.. _primeiro-template:

----------------------------------------
Primeiro template: ``livro_list.html``
----------------------------------------

- o caminho do template para a view genérica ``list_detail.object_list`` segue a convenção ``«aplicação»/«modelo»_list.html``, em caixa baixa; os nomes da aplicação e do modelo são obtidos por introspecção do parâmetro ``queryset``

- o contexto do template inclui a variável ``object_list``, referência ao parâmetro ``queryset``

.. code-block:: html
    :linenos:

    <h1>Livros</h1>

    <table border="1">
      <tr><th>ISBN</th><th>Título</th></tr>
      {% for livro in object_list %}
        <tr>
          <td>{{ livro.isbn }}</td>
          <td>
            <a href="{{ livro.get_absolute_url }}">{{ livro.titulo }}</a>
          </td>
        </tr>
      {% endfor %}
    </table>


----------------------------------------
Segundo template: ``livro_detail.html``
----------------------------------------

- o nome do template para a view genérica ``list_detail.object_detail`` segue a convenção ``«aplicação»/«modelo»_detail.html``, sempre em caixa baixa

- o contexto do template inclui a variável ``object``, referência ao objeto localizado através de ``queryset.get(id=object_id)``

.. code-block:: html
    :linenos:

    <h1>Ficha catalográfica</h1>
    
    <dl>
        <dt>Título</dt>
            <dd>{{ object.titulo }}</dd>
        <dt>ISBN</dt>
            <dd>{{ object.isbn }}</dd>
    </dl>
    
------------------------------------------------
Modelo de classes das views genéricas simples
------------------------------------------------

.. image:: _static/generic-views-base.*
   :align: center
   :width: 600px

------------------------------------------------
Modelo de classes das views genéricas *list*
------------------------------------------------

.. image:: _static/generic-views-list.*
   :align: center
   :width: 800px

------------------------------------------------
Modelo de classes das views genéricas *detail*
------------------------------------------------

.. image:: _static/generic-views-list.*
   :align: center
   :width: 800px


    
---------------------------------------------
O problema do caminho da aplicação nas URLs
---------------------------------------------

O funcionamento das *views* genéricas de listagem/detalhe dependem do método ``get_absolute_url`` para produzir os links da listagem para a página de detalhe. Eis uma implementação fácil de entender::

    class Livro(models.Model):
        '...'   
        def get_absolute_url(self):
            return '/cat/livro/%s/' % self.id

Este código é simples, mas viola o princípio :term:`DRY`, pois o prefixo `cat/` da URL está definido no módulo ``urls.py`` do projeto::

    urlpatterns = patterns('',
        '...'
        (r'^cat/', include('biblio.catalogo.urls')),
        '...'    
    )


Isto significa que se um administrador decidir mudar o prefixo das URLs da aplicação ``catalogo``, o método ``get_absolute_url`` do livro deixará de funcionar. 


-----------------------------------------------------
Solução: views nomeadas e o *decorator* ``permalink``
-----------------------------------------------------

A solução do problema envolve duas alterações, ambas dentro da aplicação ``catalogo``:

1. no módulo ``urls.py`` da aplicação, a configuração da view de detalhe recebe um nome (último argumento na linha 4 do trecho abaixo):

.. code-block:: python
    :linenos:
    
    urlpatterns = patterns('',
        url(r'^$', list_detail.object_list, livros_info), 
        url(r'^livro/(?P<object_id>\d+)/$', list_detail.object_detail, 
            livros_info, 'catalogo-livro-detalhe'),
    )

2. no módulo ``models.py`` da aplicação, o método ``get_absolute_url`` recebe o :term:`decorator` ``permalink`` e é alterado para devolver uma tupla no formato ``(«nome-da-view-url», «parâmetros-posicionais», «parâmetros-nomeados»)``::

    class Livro(models.Model):
        '...'   
        @models.permalink
        def get_absolute_url(self):
            #return '/cat/livro/%s/' % self.id
            return ('catalogo-livro-detalhe', (), {'object_id':self.id})

------------------------------------------------
*Views* genéricas incluídas com o Django (1)
------------------------------------------------

- as *generic views* ficam todas no pacote ``django.views.generic``

- *generic views* para listagem/detalhe (acabamos de ver)

    - ``django.views.generic.list.ListView``

    - ``django.views.generic.detail.DetailView``
    
- *generic views* “simples”

    - ``django.views.generic.base.TemplateView``
    
    - ``django.views.generic.base.RedirectView``
    
- *generic views* para criar/alterar/deletar objetos

    - ``django.views.generic.edit.FormView``
    
    - ``django.views.generic.edit.CreateView``
    
    - ``django.views.generic.edit.UpdateView``

    - ``django.views.generic.edit.DeleteView``


------------------------------------------------
*Views* genéricas incluídas com o Django (2)
------------------------------------------------

- *generic views* para navegar por arquivos cronológicos
    
    - ``django.views.generic.date.ArchiveIndexView``
    
    - ``django.views.generic.date.YearArchiveView``
    
    - ``django.views.generic.date.MonthArchiveView``
    
    - ``django.views.generic.date.WeekArchiveView``
    
    - ``django.views.generic.date.DayArchiveView``
    
    - ``django.views.generic.date.TodayArchiveView``
    
    - ``django.views.generic.date.DateDetailView``


----------------------------------------------
Principais funções para configuração de URLs
----------------------------------------------

Usadas em ``urls.py``:

    ``patterns(prefixo, url1, url2, ...)``
        Define uma sequência de padrões de URLs. O prefixo serve para abreviar as referências às views em forma de strings, sendo pre-pendado a todas as views do conjunto. Não tem utilidade quando se usa referências diretas às views.
        Os demais argumentos são chamadas de ``url``, ou tuplas formadas por item na ordem exata dos parâmetros da função ``url`` (ver abaixo).
        Sequências de padrões de URLs podem ser concatenadas.
    
    ``url(regex, ref_view, extra_dict=None, name='')``
        Define um padrão de URL vinculado a uma view. Os parâmetros são:
        
        ``regex``
            Expressão regular que será aplicada à URL. Grupos anônimos (ex. ``(+\d)``) são passados para a view como parâmetros posicionais, em ordem. Grupos nomeados (ex. ``(?P<object_id>\d+)``) são passados como parâmetros nomeados. A melhor prática é usar sempre grupos nomeados para reduzir o acoplamento da configuração com a definição da view.
            
        ``ref_view``
            Referência a uma view. Pode ser uma string ou uma referência real à função da view. No segundo caso, é preciso importar a função no topo do módulo ``urls.py``.
            
        ``extra_dict``
            Dicionário com valores adicionais a serem passados à view. Opcional.
            
        ``name``
            Nome da view, para referência reversa.
  
