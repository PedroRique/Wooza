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
<h3 id="métodos-de-tagueamento">Métodos de Tagueamento</h3>
<p>Vamos agora entender quais são os padrões Wooza para tagueamento. É de extrema importância seguir eles à risca, pois no GTM existem gatilhos para essas nomenclaturas específicas.</p>
<h3 id="data-attributes">1. Data Attributes</h3>
<p>Bem como os atributos existentes para que os QA’s façam suas validações de forma mais inteligente (data-test) também temos os nossos atributos e precisamos que sejam inseridos nos elementos mais importantes das páginas. São eles:<br>
<code>data-gtm-event-category</code>, <code>data-gtm-event-action</code> e <code>data-gtm-event-label</code>.</p>
<p>Além dos atributos, também é necessário adicionar uma classe no elemento. Apenas para diferenciarmos um elemento tagueado de um não tagueado. A classe padrão que utilizamos hoje é <code>gtm-element-event</code>.</p>
<p>Vamos supor então que o botão de assine já, principal CTA da página, tenha de ser tagueado. Provavelmente, a estrutura será próxima disso:</p>
<pre><code>&lt;button class="gtm-element-event" 
    data-gtm-event-category="categoria"
    data-gtm-event-action="acao"
    data-gtm-event-label="rotulo"
&gt;Assine Já&lt;/button&gt;
</code></pre>
<h3 id="data-layer">2. Data Layer</h3>
<p>Conhecido também como camada de dados, o dataLayer está presente em todas as páginas que os scripts do google estão presentes. O GTM se encarrega de iniciá-lo. Seu padrão de eventos é o seguinte:</p>
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

