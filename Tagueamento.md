---


---

<h2 id="documentação-de-tagueamento">Documentação de Tagueamento</h2>
<p>Nunca ouviu falar sobre tagueamento? Não se preocupe, é bem mais simples do que você imagina. Vou começar introduzindo alguns conceitos básicos para então te ensinar como fazemos aqui na Wooza. Vem comigo!</p>
<h3 id="conceitos-básicos">Conceitos básicos</h3>
<p><strong>Google Analytics (GA)</strong>: Ferramenta que mede as métricas do site, como pageviews, cliques e conversões.</p>
<p><strong>Google Tag Manager (GTM)</strong>: O nosso querido GTM, responsável por gerenciar todos os scripts da página. Antes dele, cada pageview era disparado utilizando uma função do GA e se fazia necessário a cada página do site.</p>
<p><strong>Pageviews</strong>: Visualização de uma página, assim conseguimos mensurar as páginas mais acessadas, e fazer o funil das telas da modal, por exemplo (/dados-pessoais, /endereco, /pagamento etc).</p>
<p><strong>Eventos</strong>: Acontecimentos na página, podendo ser uma resposta de uma API ou um abrir de modal. Tudo que para o nosso negócio seja interessante saber.</p>
<p><strong>Datalayer</strong>: Array global inicializado pelos scripts do google. Não é um array qualquer, pois não podemos manipulá-lo além de fazer novas inserções. Como se trata de um array, podemos utilizar o método <code>push</code> para isso.</p>
<p><strong>Tagueamento</strong>: Inserir um marcador no código para que as informações cheguem até o GA. Esse marcador pode ser: disparar um evento, disparar um pageview, adicionar uma informação no dataLayer</p>
<p><strong>Tagbook</strong>: Conhecido também como  <strong>documentação técnica</strong> ou até mesmo <em>tagging plan</em>. Consiste em um documento onde estará especificado tudo o que você precisa implementar.</p>
<h3 id="métodos-de-tagueamento">Métodos de Tagueamento</h3>
<p>Vamos agora entender quais são os padrões Wooza para tagueamento. É de extrema importância seguir eles à risca, pois no GTM existem gatilhos para essas nomenclaturas específicas.</p>
<h4 id="data-attributes">1. Data Attributes</h4>
<p>Bem como os atributos existentes para que os QA’s façam suas validações de forma mais inteligente (data-test) também temos os nossos atributos e precisamos que sejam inseridos nos elementos mais importantes das páginas. Utilizamos esse método para elementos <strong>clicáveis</strong>, como botões, menu de navegação etc.<br>
São eles:<br>
<code>data-gtm-event-category</code>, <code>data-gtm-event-action</code> e <code>data-gtm-event-label</code>.</p>
<p>Além dos atributos, também é necessário adicionar uma classe no elemento. Apenas para diferenciarmos um elemento tagueado de um não tagueado. A classe padrão que utilizamos hoje é <code>gtm-element-event</code>.</p>
<p>Vamos supor então que o botão de assine já, principal CTA da página, tenha de ser tagueado. Provavelmente, a estrutura será próxima disso:</p>
<pre><code>&lt;button class="gtm-element-event" 
    data-gtm-event-category="categoria"
    data-gtm-event-action="acao"
    data-gtm-event-label="rotulo"
&gt;Assine Já&lt;/button&gt;
</code></pre>
<h4 id="data-layer">2. Data Layer</h4>
<p>Conhecido também como camada de dados, o dataLayer está presente em todas as páginas que os scripts do google estão presentes. O GTM se encarrega de iniciá-lo. Utilizamos esse método para elementos <strong>não clicáveis</strong>, como respostas de API, aparecimento de um elemento na tela etc. Seu padrão de eventos é o seguinte:</p>
<pre><code>dataLayer.push({
	event: "event",
	eventCategory: "categoria",
	eventAction: "acao",
	eventLabel: "rotulo"
});
</code></pre>
<p>Porém, o dataLayer não se limite apenas a disparo de eventos. Informações do negócio podem ser passadas para ele e assim alcançar o GA. Digamos que eu queira saber qual modalidade foi escolhida, como só temos certeza da modalidade na tela de endereço, nessa tela (no sucesso da requisição da tela dados pessoais) vou enviar essa informação.</p>
<pre><code>dataLayer.push({
	modalidade: "Migração"
});
</code></pre>
<p>Isso funciona porque o dataLayer é acumulativo. O que significa que ele sempre adiciona novas propriedade em um grande objeto ou as atualiza com a última informação que possui. Então nesse caso, o objeto por debaixo dos panos e que o GTM vai ler está assim:</p>
<pre><code>{
	event: "event",
	eventCategory: "categoria",
	eventAction: "acao",
	eventLabel: "rotulo",
	modalidade: "Migração"
}
</code></pre>
<p>Lembrando que o dataLayer é uma variável global da página, então pode ser acessado de qualquer lugar do código.</p>
<h3 id="padrões">Padrões</h3>
<p>Geralmente, os nomes dos eventos e o valor de suas propriedades vão estar em <code>kebab-case</code>.  As propriedades em si são JavaScript, então por conversão usa-se <code>camelCase</code>.<br>
A exceção se da para valores que são definidos pelo back-end, como por exemplo, o nome do plano, sua franquia etc. Para esses casos apenas passe o valor para o dataLayer como ele é.</p>
<p>Quase sempre usamos uma <code>service</code> para realizar “disparos” (é como chamamos um push no dataLayer) com o mínimo de organização. Não saia disparando em qualquer lugar.</p>
<h3 id="terminologia-dos-eventos">Terminologia dos Eventos</h3>
<p>Dito como é feito tecnicamente, vamos para a parte semântica do tagueamento. Vamos dar nome aos bois!</p>
<p>Todo evento tem os seguintes componentes:</p>
<ul>
<li>Categoria</li>
<li>Ação</li>
<li>Rótulo (opcional, mas fortemente recomendado)</li>
<li>Valor (opcional, não utilizamos na Wooza)</li>
</ul>
<p><strong>Categoria (category)</strong>: Uma categoria é um nome que você fornece como forma de agrupar objetos que deseja analisar. Normalmente, você usará o mesmo nome de categoria várias vezes no mesmo projeto.</p>
<p><strong>Ação (action)</strong>: Agora pense que o grupo que você criou utilizando a categoria pode ser subdividido em mais grupos. Dessa forma estamos criando um aninhamento dos eventos. Tenha certeza que na hora de analisar isso vai facilitar muito!</p>
<p><strong>Rótulo (label)</strong>: Por fim, com os rótulos, você pode fornecer informações adicionais para os eventos que deseja analisar, como o texto do botão ou a franquia do card.</p>
<h4 id="exemplos">Exemplos</h4>
<p>Vamos ao mais comum e útil evento que temos. Cliques no plano. Uma vez que nosso GA é unico para a Landing Page e para a Modal podemos agrupar os eventos dessa forma. Para fornecer mais informações dentro de um mesmo nome, podemos utilizar o separador <code>:</code>.</p>
<blockquote>
<p>category: <code>lp:planosclarocontrole</code><br>
action: <code>card:4GB</code><br>
label: <code>contratar</code></p>
</blockquote>
<p>Repare que adicionei a informação do <em>hostname</em> e ainda a franquia do plano. Isso ajuda muito pois existem cenários com mais de uma LP, e certamente existem vários cards em cada uma delas.</p>
<p>Para fixarmos bem, vamos a mais um exemplo hipotético, dessa vez em um cenário de consulta de cep. Digamos que o CEP não foi encontrado e precisamos informar isso ao GA.</p>
<blockquote>
<p>category: <code>modal:contreclaro</code><br>
action: <code>cep</code><br>
label: <code>nao-encontrado</code></p>
</blockquote>
<p>Podemos ainda informar qual foi o CEP digitado no campo de rótulo, por exemplo. Mas isso implicaria em enviar uma informação do usuário ao GA, o que nos leva para os cuidados com o tagueamento!</p>
<h3 id="atenção">ATENÇÃO</h3>
<p><strong>Não</strong> dispare eventos assim que a <strong>página carregar</strong>! Isso influencia em uma outra taxa que temos na ferramenta, a taxa de rejeição. Caso haja extrema necessidade é possível fazer, mas com a ajuda do responsável pelo GTM.</p>
<p><strong>Não</strong> envie informações <strong>pessoais</strong>. E-mail, telefone etc são dados sensíveis e não contribuem para a análise final (e mesmo se contribuírem não é o propósito da ferramenta, deixaremos isso para o banco de dados).</p>
<p><strong>Não</strong> começe a fazer se ainda não tiver em mãos do <strong>tagbook</strong>! Esse documento te respalda quando eles inventarem alguma coisa nova ou até mesmo mudarem de ideia quanto ao tagueamento no meio do desenvolvimento. Sim, isso acontece.</p>
<blockquote>
<p>Ah, e se você ouvir alguma coisa sobre <strong>comércio eletrônico</strong> não precisa ficar com medo, são apenas pushes no dataLayer com um formato específico que, como combinamos, vai estar especificado em uma documentação.</p>
</blockquote>
<h3 id="considerações-finais">Considerações Finais</h3>
<p>Você pode estar se perguntando o porquê disso tudo, mas te garanto que do outro lado existe um profissional que só quer fazer o trabalho dele da melhor forma possível, então quero te <strong>contextualizar</strong> para que faça mais sentido e assim te motive a querer ajudar!</p>
<p>Toda <strong>informação</strong> quer vai ao <strong>dataLayer</strong> vai se tornar uma arma na mão do analista. Quando informamos que o usuário escolheu boleto em vez de cartão de crédito nessa camada de dados, podemos medir qual das duas opções tem maior conversão (vende mais) e a partir disso pensar em como melhorar as vendas da opção menos desejada para que no total cresçamos. Não é incrível?</p>
<p><strong>Taguear um botão</strong> nos permite dizer qual a porcentagem dos usuários realiza essa ação. Se entendermos que ele é pouquíssimo clicado, aquela demanda do seu board “Ajustar Layout do botão” pode desaparecer. Depois me agradece ;)</p>
<p>As aplicações são <strong>muitas</strong> e eu levaria dias para escrever e você noites para ler, então, resumidamente, entenda. Cuide do seu produto, queria sempre melhorá-lo, entenda das regras de negócio e se preocupe com o usuário final. Isso pode te levar a um outro nível de profissional e como dizem por aí, você vai estar em OTO PATA MAR.</p>

