.. Copyright 2009 Luciano G. S. Ramalho; alguns direitos reservados
   Este trabalho é distribuído sob a licença Creative Commons 3.0 BY-SA  
   (Atribuição-Compartilhamento pela mesma Licença 3.0). 
   Resumindo, você pode:
     - copiar, distribuir e exibir o texto e ilustrações
     - criar obras derivadas
   Sob as seguintes condições:
     - Atribuição: Você deve dar crédito ao autor original, mantendo este
       aviso em todos os arquivos derivados
     - Compartilhamento pela mesma Licença: se você alterar, transformar ou
       derivar outro trabalho a partir deste, você pode distribuir o trabalho
       resultante somente sob a mesma licença, ou uma similar e compatível

=====================================================================
Introdução ao Django
=====================================================================

- Luciano Ramalho, Oficinas Turing

- luciano@ramalho.org

---------------------------------------------------------------------
Algumas características do Django
---------------------------------------------------------------------

- fácil de aprender, porém robusto, flexível e extensível

- foi criado num site de notícias, e suporta um workflow muito ágil

  - programadores criam o modelo de dados
  
  - jornalistas criam conteúdo, via interface administrativa
  
  - em paralelo, programadores e designers constroem o site público

- é o framework mais popular da linguagem Python, desde 2009 pelo menos

.. xxx completar

----------------------------------
Exemplo: estrutura de um projeto
----------------------------------

.. image:: _static/django-proj-dir.*
   :align: right

- o projeto *pizza*: sistemas web para uma pizzaria de bairro

- formado por duas aplicações:

    - ``entrega``: sistema interno para receber e despachar pedidos
    
    - ``portal``: site público da pizzaria

- ``pizza/`` e demais arquivos marcados com o selo foram criados pelo comando::

    $ django-admin.py startproject pizza
        


-----------------------------------------
Exemplo: estrutura de um projeto (cont.)
-----------------------------------------


.. image:: _static/django-proj-dir.*
   :align: right

- ``pizza/``: o diretório raiz do projeto

    - ``entrega/``: o diretório da aplicação ``entrega``
        
    - ``portal/``: diretório da aplicação ``portal``
    
    - ``static/``: diretório de arquivos estáticos do projeto (.css, .gif)
    
    - ``templates/``: diretório de templates do projeto

    - ``__init__.py``: módulo vazio para marcar o diretório ``pizza/`` como um :term:`package`

    - ``manage.py``: script administrativo

    - ``settings.py``: configurações do projeto
    
    - ``urls.py``: mapeamento de cada tipo de URL para a :term:`view` correspondente

    
------------------------------------
Exemplo: estrutura de uma aplicação
------------------------------------

.. image:: _static/django-app-dir.*
   :align: right

- diretório ``pizza/entrega/``

    - diretório de uma das aplicações que formam o sistema

    - ``pizza/entrega/`` e demais arquivos marcados com a estrela foram criados via::
    
        $ ./manage.py startapp entrega

- arquivos da aplicação

    - ``__init__.py``: módulo vazio para marcar o diretório ``entrega/`` como um :term:`package`

    - ``admin.py``: configurações da interface administrativa
    
    - ``models.py``: modelos de dados (classes de persistência)
    
    - ``views.py``: funções de tratamento de requisições
    

