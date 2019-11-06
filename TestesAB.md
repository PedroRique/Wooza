<h2 id="como-implementar-testes-ab">Como implementar Testes AB de Redirecionamento</h2>
<p>Na Wooza, utilizamos a ferramenta Google Optimize. Ela permite realizar <strong>Testes AB</strong> de muitas formas, inclusive conta com um editor HTML que faz modificações na página e coloca em <strong>produção</strong>, acredite!</p>
<p>Uma das formas de configurar o teste é o que chamamos de <strong>Teste de Redirecionamento</strong>. Consiste em adicionar um <strong>parâmetro</strong> na URL da versão variante e, no código, realizar as modificações caso esse parâmetro exista e seja igual ao valor estipulado.</p>
<p>O parâmetro padrão da empresa é a letra <code>v</code>. Caso não exista <code>v</code> ou seu valor seja vazio estamos na versão original e nenhuma alteração de testes deve estar visível para o usuário.</p>
<p>Ex: <a href="http://dominio.com/?v=4">dominio.com/?v=4</a></p>
<p>Se a sua aplicação tem um serviço responsável pela captura dos parâmetros, utilize-o.</p>
<p>Entendemos que cada teste é uma nova versão, portanto, esse valor é incrementado a cada teste.</p>
<h2 id="tipos-de-implementação">Tipos de implementação</h2>
<h3 id="visual"><strong>1. Visual</strong></h3>
<p>Testes que mudam apenas o estilo, onde mudanças no CSS já produzem o efeito necessário.</p>
<p>Validamos o parâmetro <code>v</code> e aplicamos uma <strong>classe</strong> no elemento (ou no <em>container</em> “pai”). Ex: “version-4”.</p>
<pre><code>.container
	...
	&amp;.version-4
		.classe1
			...
		.classe2
			...
</code></pre>
<h3 id="estrutura"><strong>2. Estrutura</strong></h3>
<p>São testes que precisamos alterar a <strong>estrutura</strong> do HTML. Verificamos a variável <code>v</code>, capturada do serviço de parâmetros, e renderizamos a estrutura desejada. Exemplo:</p>
<pre><code>&lt;div ng-if="this.params.v === '4'"&gt;
...
&lt;/div&gt;
</code></pre>
<pre><code>&lt;div ng-if="this.params.v !== '4'"&gt;
...
&lt;/div&gt;
</code></pre>
<h3 id="funcionalidade"><strong>3. Funcionalidade</strong></h3>
<p>Quando o teste muda o funcionamento da aplicação.</p>
<p>Realizamos validações no arquivo de controle utilizando a mesma variável <code>v</code>. Exemplo:</p>
<pre><code>function onInit() {
	...
	var version = ParametersService.v;
	if (version == 4) {
		executaFuncionalidadeNova();
	}
	...
}
</code></pre>
<pre><code>function executaFuncionalidadeNova() {
	...
}
</code></pre>
<h2 id="pontos-de-atenção">Pontos de atenção</h2>
<p><strong>1. As alterações de uma versão são exclusivas para seu <em>v</em>:</strong> Modificações do teste 4, por exemplo, não podem ser visíveis no teste 5.</p>
<p><strong>2. Novas alterações que surgirem no decorrer do teste:</strong> O desenvolvedor é responsável por analisar se a implementação que ele fez na versão A vai impactar ou quebrar alguma coisa na versão B.</p>
<p><strong>3. Realizar limpeza do código:</strong> Após o resultado oficial do teste, é de responsabilidade do desenvolvedor remover todas as validações daquele teste em específico. Cuidado para não interferir em outros testes que estão em desenvolvimento ou em produção.</p>
<p><strong>4. Testes automatizados:</strong> Os QA’s tem uma série de rotinas com testes automatizados, como o teste pode mudar a estrutura da página temos que ficar cientes que na versão B o teste terá que ser feito manualmente.</p>

