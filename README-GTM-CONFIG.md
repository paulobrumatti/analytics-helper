# Configuração do GTM

Este documento descreve os passos para a utilização da biblioteca analytics-helper em conjunto com o Google Tag Manager, e as configurações necessárias.

## Tag principal

O arquivo final, presente na pasta *build*, seja ele o arquivo de exemplo disponível neste repositório, ou uma versão personalizada gerada via Gulp, deverá ser copiado integralmente para uma Tag Custom HTML.

Em configurações avançadas, a opção de executar uma única vez por página deverá ser selecionada.

![Configuração uma por página](documentation-images/once_per_page.png)

As tags que utilizarem o objeto analyticsHelper devem configurar esta tag principal como requisito na seção *sequência de tags*, para garantir que o objeto estará definido antes do uso.


## Acionadores

Três acionadores do tipo *evento personalizado* devem ser criados.

Os nomes dos eventos serão os nomes definidos nos campos: *exceptionEvent*, *customNamePageview* e *customNameEvent*.

Por padrão, os valores destes campos são respectivamente: *gtm_dataQuality_event*, *ga_pageview* e *ga_event*.

![Triggers](documentation-images/event_name.png)

## Tags de template

Três tags de Universal Analytics devem ser criadas, uma para cada acionador criado acima.

Estas tags devem ser preenchidas com as variáveis de camada de dados listadas na próxima seção. Estes são os campos mínimos, as tags podem ser estendidas para incluir dimensões personalizadas, dados de ecommerce e outras configurações.

### Template de Pageview
![Tag de template de Pageview](documentation-images/tag_pageview.png)

### Template de Evento
![Tag de template de Evento](documentation-images/tag_event.png)

### Template de Timing
![Tag de template de Timing](documentation-images/tag_timing.png)

### Template de Data Quality
![Tag de template de DataQuality](documentation-images/tag_dataquality.png)


## Variáveis

As variáveis padrão *Container ID* e *Debug Mode* devem ser habilitadas, pois elas são utilizadas pelo código da *tag principal*.

A tabela a seguir descreve todas as variáveis do tipo *variável de camada de dados* que deverão ser criadas para o uso nas tags de template do Google Analytics:

| Nome da variável de Camada de Dados | Tag que utiliza | Campo do template |
| - | - | - |
| eventCategory | Tag de Evento | Categoria |
| eventAction | Tag de Evento | Ação |
| eventLabel | Tag de Evento | Rótulo |
| eventValue | Tag de Evento | Valor |
| eventNoInteraction | Tag de Evento | Hit de não-interação |
| timingCategory | Tag de Evento | Categoria |
| timingVariable | Tag de Evento | Variável |
| timingValue | Tag de Evento | Valor |
| timingLabel | Tag de Evento | Rótulo |
| path | Tag de Pageview | Fields to Set -> page |
| dataQuality.category | Tag de Data Quality | Categoria |
| dataQuality.action | Tag de Data Quality | Ação |
| dataQuality.label | Tag de Data Quality | Rótulo |
| dataQuality.selector | Tag de Data Quality | Dimensões Personalizadas -> 1 |
| dataQuality.event | Tag de Data Quality | Dimensões Personalizadas -> 2 |

Caso utilize a opção waitQueue (habilitada por padrão), a seguinte variável de *javascript personalizado* deverá ser criada e adicionada ao campo *hitCallback* em *Fields to Set* nas tags de template de Google Analytics.

```javascript
function () {
  return function () {
    analyticsHelper.internal.sentPageview = true;
    while (analyticsHelper.internal.eventQueue.length) {
      analyticsHelper.event.apply(analyticsHelper, analyticsHelper.internal.eventQueue.shift());
    }
    while (analyticsHelper.internal.timingQueue.length) {
      analyticsHelper.timing.apply(analyticsHelper, analyticsHelper.internal.timingQueue.shift());
    }
  };
}
```


