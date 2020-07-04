# Alparservice portal style guide
Este é o guia de estilo oficial para portais desenvolvidos pela Alparservice. Um guia de estilo tem como objetivo evitar erros a curto, médio e a longo, além de padronizar o desenvolvimento dos projetos, o que ocasiona maior legibilidade para futuras manutenções no código.

## Prioridades

### Prioridade A: Essencial

Regras que ajudam a evitar erros e perdas significativas de performance; portanto, aprenda e respeite-as a todo custo. Exceções podem existir, mas devem ser muito raras e só podem ser feitas por pessoas com conhecimento especializado.

### Prioridade B: Evite erros 

### Prioridade C: Recomendações

### Prioridade D: Padrões e semânticas

## Prioridade A

### Não edite widgets padrões
Assim como qualquer outro recurso da servicenow, evite editar widgtes padrões da ferramenta. Uma vez que um widget padrão é modificado, o mesmo deixará de receber atualizações - muitas vezes necessárias - da servicenow. Por segurança, a servicenow impede que alguns widgets padrões sejam editados, porém não são todos que possuem essa trava.

E qual é o impacto disso?

Tente imaginar que um widget padrão - que consome uma API da servicenow - foi alterado por você para que um simples texto fosse ajustado e se adequasse às necessidades do seu projeto, é sabido que este widget não receberá mais atualizações da servicenow. 

Mas o que acontece com as dependências deste widget, como a API REST que ele consome, por exemplo? Existe a possibilidade de que esta API seja modificada pela servicenow, ao passo de que todos os widgets que fazem uso deste recurso, também precisem ser alterados para que se adequem ao ajuste na API. De praxe, a servicenow faria essa alteração de forma automática durante o processo de atualização. Porém, o widget que você alterou não será atualizado e não receberá esse ajuste necessário da servicenow, o que provavelmente iria ocasionar algum erro durante a execução do widget.

Imagine que muitas vezes um widget possui inúmeras outras dependências para que continue funcionando, como angular providers, diretivas, templates e até mesmo outros widgets. Se qualquer uma dessas dependências forem alteradas, o widget dependente pode deixar de funcionar, sem o ajuste adequado.

Além de ocasionar possíveis erros, você pode estar deixando de usufruir de outas vantagens, como recursos novos que podem vir em atualizações e suporte da servicenow.

Obs: Isso não se aplica somente para widgets, a ideia é a mesma para qualquer outro recurso da servicenow.

### Seu  código deve ser compatível com todos os navegadores
A partir da versão Orlando, a servicenow deixará de suportar a versão 11 do Internet Explorer, por tanto, não precisaremos mais nos preocupar com ele.

Por tanto, ainda existem navegadores que não suportam todos os recursos mais atualizados do javascript.

Durante o desenvolvimento, garanta que que o seu código, tanto CSS, quanto Javascript, serão compatíveis com todos os navegadores usados pela companhia. Para isto, crie o hábito de perguntar ao cliente quais são os navegadores usados pela empresa antes de realizar algum desenvolvimento.

Se ele insistir em dizer de que não pode deixar de usar o IE 11, apresente-o a este  [artigo](https://hi.service-now.com/kb_view.do?sysparm_article=KB0683275).

Sites como [Can I Use](https://caniuse.com/) podem ser uteis para validar quais navegadores são compatíveis com algum recurso (css ou js).

### O portal deve ser responsivo

Erros não se dão somente por conta de códigos javascript. Se um usuário não está conseguindo acessar todos os recursos da sua página através do celular, isso também é um erro, visto a servicenow dispões de ferramentas para que essa adaptação seja feita.

Garanta que toda página desenvolvida seja testada em telas menores, tanto quanto é testada no Desktop, uma vez que muitos dos acessos - dependendo do projeto, é claro - vêm através de dispositivo móveis.

Vale lembrar que o celular não é o único dispositivo com tela menor que a de um desktop, também existem telas intermediárias a estas, como phablets e tables. Então tenha certeza de que seu widget esteja funcionado em todos os breadkpoints do bootstrap.

### Não sobrescreva as dependências do Service Portal

Apesar de toda a flexibilidade de que existe no desenvolvimento de portais na servicenow, o service portal depende de alguns recursos e plugins de terceiros para que continue em pleno funcionamento.

Importar essas dependências mais de uma vez ou importar uma versão mais atualizada, pode ocasionar erros em muitos widgets e páginas. Por tanto, antes de importar alguma dependência ao portal, garanta que a servicenow já não tenha essa funcionalidade instalada.

Algumas das dependências que já vêm instaladas no portal:

- Bootrasp 3.3.4
- Font Awesome 4.7.0
- Select2 3.5.3
- jquery-2.2.3
- angular_1.5.11
- bootstrap-iconpicker 1.7.0
- moment
- bootstrap-datetimepicker 3.1.3
- Sortable 1.4.2
- Underscore.js 1.8.3
- tinyMCE
- Autosize 3.0.17
- bootstrap-slider 6.1.2

### Traga somente o necessário nas APIs

Se você está fazendo o uso da API padrão de tabelas da servicenow, tenha certeza de que está trazendo somente os campos necessários através do campo sysparm_fields.

Não subestime a utilidade deste parâmetro, se ele não é informado, a performance da consulta pode se degradar bastante, uma vez que o registro pertence a uma tabela com muitos campos, como a tabela task.

Esta prática também deve ser aplicada a qualquer outro tipo de API.

### Não execute funções na primeira chamada do server script

Faça o seguinte teste:

 1. Adicione este código na primeira linha do server script de qualquer widget: `gs.sleep(5000)`. Isso fará com que o servidor aguarde 5s antes de continuar sua execução.
 2. Agora atualize a página onde este widget está instanciado.

Você notará que TODA a página ficará em branco por pelo menos 5 segundos. Isso quer dizer que a servicenow não renderiza o widget na tela logo que o server é executado. O portal só começa a apresentar itens em tela depois que o servidor de todos os widgets da página são executados.

Quando estamos falando de widgets, sabemos que podemos executar o código do server script quantas vezes forem necessárias, certo? Então não execute funções demoradas na primeira execução do server script.

    ## Exemplo 1: Evite isso ##
	
	// server.js 
	data.itens = getItens()
    data.categorias = getCategorias()
	
	## Exemplo 2: Faça mais disso ##
    
    // server.js	
	if (!input) return;
	if (input.action == 'getDados') {
		data.itens = getItens()
	    data.categorias = getCategorias()
	} 

No primeiro exemplo, a página não seria renderizada antes que a busca pelos itens e categorias fossem finalizadas. Ou seja, teríamos um tempo maior com a tela em branco.

Diferente do segundo exemplo, onde as informações só serão buscadas depois que a página já tiver sido carregada.

Dica: Enquanto está sendo executado a chamada no servidor para buscar os dados, deixe um loader ou uma mensagem na tela para que o usuário saiba que os dados estão sendo carregados.

## Prioridade B

### Componentize!

[Terminar] Para deixar o portal o mais dinâmico possível, componentize tudo que possa ser componentizado.

Cada widget no portal deveria ser responsável por apenas - existem exceções - uma funcionalidade. 

Pense no seguinte cenário: você recebeu uma demanda onde precisa desenvolver uma tela com os seguintes requisitos: 

 1. Listar os registros de uma tabela da servicenow
 2. Na frente de cada registro deve ter um botão para exclusão
 3. Ao clicar em um registro deve abrir um modal para visualizar o formulário do mesmo 
 4. A lista deve ter a opção de ser extraída em formato excel

É possível criar um único widget que atenda a todos estes requisitos? Claro que é. Mas também é possível criar um widget para cada funcionalidade, de forma que elas possam ser reutilizadas em outras páginas.

#### Widget para excluir registro

Este widget poderia ter um código em seu client que fique "escutando" o evento de apagar registro. E quando este evento for disparado, um modal de confirmação ser exibido.

#### Widget para exibir formulário de registro

Este widget poderia ter um código em seu client que fique "escutando" o evento de exibir registro. E quando este evento for disparado, um modal com o formulário é exibido.

#### Serviço para exportar tabela em excel

Ao contrário das outras funcionalidades, exportar uma tabela em excel não precisa de um código HTML, tampouco, um código CSS. Por isso, não é necessário criar um widget só para fazer isso.

O ideal para isto seria a criação de um serviço, onde o mesmo poderá ser injetado no client script de qualquer widget do portal.

#### Widget para listar os registros

Este será o widget responsável por exibir a listagem dos registros e por chamar as outras dependências.

Ao clicar em algum registro, o widget deve disparar o evento de exibição de registro e isso abrirá o modal que está configurado no outro widget "exibir formulário de registro".

Ao clicar em excluir, o widget deve disparar o evento para excluir registro e isso abrirá o modal de confirmação que está no widget "excluir registro".

Ao clicar em exportar, o widget deve chamar o método do serviço que extrai uma tabela em relatório.

Para que os widgets possam se comunicar entre si através de eventos, todos eles precisam estar na página. Não existe uma melhor prática de como fazer isso, então você pode colocá-los na página através do page designer ou embedando os widgets no widget dependente.


### Cuidado ao usar setTimeout

É muito comum o uso de setTimeout para contornar algum mau funcionamento no código. Não use setTimeout sempre que você não entender o porquê de algo não estar funcionando, busque saber a causa raiz do problema.

O motivo mais comum de usarem setTimeout indevidamente é para fazer a manipulação de algum elemento HTML do widget que está dentro de algum ng-if ou ng-repeat, por exemplo. Então o setTimeout é usado para aguardar que o angular faça a renderização deste elemento. Isso pode ser um problema, uma vez que não sabemos quanto tempo o angular irá finalizar a renderização dos elementos da tela.

Se você está usando o setTimeout para poder executar algum plugin jQuery ou alguma funcionalidade javascript do bootstrap, considere usar o $timeout.

#### $timeout

$timeout é um serviço do angular que força a propagação de todo o model antes de executar a função de callback. 

No exemplo, o desenvolvedor precisa aguardar que o input seja renderizado em tela para que possa aplicar a máscara do plugin jQuery Mask.

    ## Em vez disso. 
    
    client.js
    function ($element) {
    	 setTimeout(function () {
			$($element).find('input.date').mask('00/00/0000');
		}, 500);
    }
    
    ## Faça isso
    
    client.js
    function ($element, $timeout) {
    	 $timeout(function () {
			$($element).find('input.date').mask('00/00/0000');
		});
    }

Note que no código recomendado não é preciso adivinhar/informar um tempo x de espera.

### Faça uso do $element

[Terminar] Sabemos que é muito comum fazer o uso de plugins jQuery dentro do um widget, plugins estes que na maioria das vezes fazem a manipulação de algum elemento do DOM. Mas como garantir de que estamos manipulando o HTML somente do widget em que estamos trabalhando, visto que o jQuery funciona em um contexto que abrange toda a página?

    ## Em vez disso. 
    
    client.js
    function () {
    	$('.modal').modal('show');
    }
    
    ## Faça isso
    
    client.js
    function ($element, $timeout) {
    	 $($element).find('.modal').modal('show');
    }

### Não execute integrações no server side

### Jamais deixe fixo a URL da instância

### Não use flushcache

### Trate todos os erros em APIs

### Cuidados com datas

## Prioridade C

### spUtil.isMobile

É muito comum desenvolvedores buscarem códigos na internet para verificar se o usuário está acessando o portal através de um dispositivo móvel. O serviço spUtil já possui um método para fazer essa validação.
	
    client.js
    function (spUtil) {
		var isMobile = spUtil.isMobile()
	}


### spUtil.localeMap
A servicenow possui abreviações fora do convencional para definir as linguagens. "PT-br", por exemplo, é "pb". Se durante o desenvolvimento você precisar saber o código no padrão internacional da língua que está na sessão do usuário, o método localeMap poderá ser usado.

    client.js
    function (spUtil) {
		var pbLang = spUtil.localeMap.pb;
		console.log(pbLang); 
		// log: pt-br 
	}
###  Buscar parâmetros da URL

É muito comum a necessidade de fazer a leitura de algum parâmetro passado na URL e os widgets já dispõe de meios para fazer essa consulta, tanto no client quanto no server. Por tanto, não use outros meios para fazer isto.

    server.js
    function () {
		var sysId = $sp.getParameter('sys_id')
	}
	
	client.js
    function ($location) {
		var sysId = $location.search().sys_id;
	}

### Padronize o tamanho dos elementos do portal

[Terminar] Não usar números aleatórios, ex: 19px, 82%

### Não repita CSS 

[Terminar] Se você tiver que escrever um código css em mais de um widget, considere colocar este código na hierarquia acima, até que contemple todos os widgets.
É possível adicionar CSS no portal, tema, página, instância do widget e no widget.

### Não usar !important

### Use table-responsive

### Não usar estilos inine
[Terminar] Jamais adicionar style à tags dos widgets ou templates.

### Não importar script direto no html

### Sempre usar cols!!

### Prefira criar serviços no lugar de ui scripts

### Cuidados ao usar jQuery

## Prioridade D

### Sempre idente o código

### Padronize nome de widgets

### Padronize nome de páginas

### Não use a tag br para fazer a função de margem

### Não use a tag p para exibir textos que não são parágrafos

### Não usar font-weigth: bold

### Remover logs
Lembre-se sempre de remover qualquer tipo de log desnecessário após finalizar o desenvolvimento de algum componente para o portal.
Tenha certeza de que não deixou nenhum debugger ou console.log ao fechar o update set.

### Adicionar ponto e virgula

### Col sempre dentro de row