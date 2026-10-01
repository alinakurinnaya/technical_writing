---


---

<p><a href="00-introduction.md">← Введение и оглавление</a></p>
<h1 id="как-программы-учились-общаться-эра-rpc">Как программы учились общаться: эра RPC</h1>
<blockquote>
<p><strong>Ключевые термины главы:</strong> <a href="#rpc">RPC</a> · <a href="#stub">клиентский стаб</a> · <a href="#skeleton">серверный скелет</a> · <a href="#serialization">сериализация</a> · <a href="#idl">IDL</a> · <a href="#orb">ORB</a> · <a href="#com">COM</a></p>
</blockquote>
<p>Прежде чем API стали наборами ресурсов и HTTP-методов, разработчики мыслили по-другому. Им хотелось вызывать удалённую функцию так же просто, как локальную. Эта идея породила RPC и на несколько лет определила, как выглядели первые API.</p>
<h2 id="что-такое-rpc">Что такое RPC</h2>
<p><a id="rpc"></a><strong>RPC (Remote Procedure Call)</strong> - удалённый вызов процедуры. Клиент вызывает функцию, которая физически выполняется на другом компьютере.</p>
<p>Вот локальный вызов:</p>
<pre class=" language-python"><code class="prism  language-python">user <span class="token operator">=</span> get_user<span class="token punctuation">(</span><span class="token number">42</span><span class="token punctuation">)</span>
</code></pre>
<p>А это удалённый вызов через RPC - почти такой же:</p>
<pre class=" language-python"><code class="prism  language-python">user <span class="token operator">=</span> remote<span class="token punctuation">.</span>get_user<span class="token punctuation">(</span><span class="token number">42</span><span class="token punctuation">)</span>
</code></pre>
<p>Разница в том, что происходит «под капотом»: аргумент <code>42</code> упаковывается в сообщение, летит по сети, сервер выполняет функцию и возвращает результат. Программисту не видно, что вызов удалённый.</p>
<p>Аналогия - телефонный звонок. Вы набираете номер, говорите, ждёте ответ. Вам не важно, как устроена АТС.</p>
<h3 id="как-устроена-«магия»">Как устроена «магия»</h3>
<p>Чтобы вызов выглядел как локальный, между программистом и сетью стоит прослойка из двух частей:</p>
<ul>
<li><a id="stub"></a><strong>Клиентский стаб (stub)</strong> - генерируемая заглушка, которая выглядит как обычная функция <code>get_user</code>. Но внутри она не выполняет логику, а <strong>сериализует</strong> (упаковывает) аргументы в байты и отправляет их по сети.</li>
<li><a id="skeleton"></a><strong>Серверный скелет (skeleton)</strong> - принимает эти байты, <strong>десериализует</strong> их обратно в аргументы и вызывает настоящую функцию на сервере.</li>
</ul>
<p><a id="serialization"></a><strong>Сериализация</strong> превращает объекты в поток байтов, который можно передать. У разных RPC-технологий сериализация устроена по-разному: где-то это XML, где-то JSON, где-то бинарный формат. Но идея одна.</p>
<h3 id="ловушка-«прозрачности»">Ловушка «прозрачности»</h3>
<p>Главная идея RPC - <strong>сделать удалённый вызов неотличимым от локального</strong>. Это удобно, но у такого подхода есть свои проблемы.</p>
<p>Локальный вызов занимает наносекунды и почти не сбоит. Удалённый вызов может быть долгим, упасть из-за обрыва сети или выполниться дважды. Но код выглядит одинаково - и программист не видит, что перед ним не функция, а сеть.</p>
<p>Эта ловушка будет преследовать RPC все годы его существования: от CORBA до gRPC. И именно из-за неё индустрия в какой-то момент решит, что с сетью надо работать <strong>как с сетью</strong>, и не делать вызовы похожими на локальные. Больше об этом - в главе про REST.</p>
<h2 id="первые-реализации-и-как-они-выглядели">Первые реализации и как они выглядели</h2>
<h3 id="corba">CORBA</h3>
<p>CORBA (Common Object Request Broker Architecture) - попытка сделать RPC независимым от языка и платформы. В начале 90-х была проблема: программы на C++ не умели вызывать код на Java, а программы на Java - код на Smalltalk. CORBA была решением: пусть клиент и сервер пишут на чём угодно, лишь бы они договорились об общем описании интерфейса.</p>
<p>Для этого использовали <a id="idl"></a><strong>IDL (Interface Definition Language)</strong> - отдельный язык, на котором описывают, какие методы существуют и какие у них параметры:</p>
<pre class=" language-idl"><code class="prism  language-idl">interface UserService {
    User get_user(in long id);
};
</code></pre>
<p>Из этого файла <strong>генерировали код</strong> для каждой стороны: для клиента - заглушку (stub), для сервера - скелет (skeleton). Программист писал только <code>UserService</code> на своём языке, а всю «проводку» через сеть делал сгенерированный код.</p>
<p>Связывал клиента и сервер отдельный компонент - <a id="orb"></a><strong>ORB (Object Request Broker)</strong>. Это посредник, который знал, где живёт нужный объект, и умел доставить вызов. У каждой платформы был свой ORB, и они должны были уметь договариваться между собой.</p>
<p>Звучит хорошо, но на практике клиент на C++ мог вызвать сервер на Java - но только если обе стороны собраны из одного и того же IDL. Изменился IDL - нужно пересобрать обе стороны. К тому же сам ORB был тяжёлым, дорогим в настройке и плохо работал за пределами своей экосистемы.</p>
<h3 id="dcom">DCOM</h3>
<p>Это решение Microsoft, привязанное к Windows. Вызов выглядел как работа с COM-объектом:</p>
<pre class=" language-cpp"><code class="prism  language-cpp">IUserService<span class="token operator">*</span> svc<span class="token punctuation">;</span>
<span class="token function">CoCreateInstance</span><span class="token punctuation">(</span>CLSID_UserService<span class="token punctuation">,</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">,</span> <span class="token operator">&amp;</span>svc<span class="token punctuation">)</span><span class="token punctuation">;</span>
User<span class="token operator">*</span> user <span class="token operator">=</span> svc<span class="token operator">-</span><span class="token operator">&gt;</span><span class="token function">GetUser</span><span class="token punctuation">(</span><span class="token number">42</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
</code></pre>
<p><a id="com"></a><strong>COM</strong> - это технология Microsoft для сборки программ из бинарных компонентов. Клиент создавал объект по уникальному идентификатору (<a id="clsid"></a><strong>CLSID</strong>) и вызывал его методы через <strong>интерфейс</strong> - фиксированный набор методов, форма которого известна заранее. Внутри объект мог быть написан на чём угодно: на C++, Visual Basic или Delphi. Клиенту это было неважно - он видел только интерфейс. Объект мог жить на другой машине, а вызов всё равно выглядел как локальный.</p>
<h3 id="xml-rpc">XML-RPC</h3>
<p>Это самый простой из трёх вариантов - хотя по коду так и не скажешь. XML-RPC появился в 1998 году как ответ на тяжеловесность CORBA и DCOM: никакого IDL, генерации кода, брокеров. Всё, что нужно, - HTTP и XML.</p>
<p>Идея простая: <strong>вызов метода - это обычный HTTP-запрос</strong>. Имя метода, параметры и результат упаковываются в XML. Вот как выглядел реальный запрос на получение пользователя с <code>id=42</code>:</p>
<pre class=" language-http"><code class="prism  language-http">POST /RPC2 HTTP/1.0
<span class="token header-name keyword">Content-Type:</span> text/xml<span class="token text/xml">

<span class="token prolog">&lt;?xml version="1.0"?&gt;</span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>methodCall</span><span class="token punctuation">&gt;</span></span>
  <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>methodName</span><span class="token punctuation">&gt;</span></span>get_user<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>methodName</span><span class="token punctuation">&gt;</span></span>
  <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>params</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>param</span><span class="token punctuation">&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>value</span><span class="token punctuation">&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>int</span><span class="token punctuation">&gt;</span></span>42<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>int</span><span class="token punctuation">&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>value</span><span class="token punctuation">&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>param</span><span class="token punctuation">&gt;</span></span>
  <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>params</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>methodCall</span><span class="token punctuation">&gt;</span></span>
</span></code></pre>
<p>Ответ:</p>
<pre class=" language-xml"><code class="prism  language-xml"><span class="token prolog">&lt;?xml version="1.0"?&gt;</span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>methodResponse</span><span class="token punctuation">&gt;</span></span>
  <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>params</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>param</span><span class="token punctuation">&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>value</span><span class="token punctuation">&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>struct</span><span class="token punctuation">&gt;</span></span>
      <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>member</span><span class="token punctuation">&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>name</span><span class="token punctuation">&gt;</span></span>id<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>name</span><span class="token punctuation">&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>value</span><span class="token punctuation">&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>int</span><span class="token punctuation">&gt;</span></span>42<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>int</span><span class="token punctuation">&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>value</span><span class="token punctuation">&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>member</span><span class="token punctuation">&gt;</span></span>
      <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>member</span><span class="token punctuation">&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>name</span><span class="token punctuation">&gt;</span></span>name<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>name</span><span class="token punctuation">&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>value</span><span class="token punctuation">&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>string</span><span class="token punctuation">&gt;</span></span>Alice<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>string</span><span class="token punctuation">&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>value</span><span class="token punctuation">&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>member</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>struct</span><span class="token punctuation">&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>value</span><span class="token punctuation">&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>param</span><span class="token punctuation">&gt;</span></span>
  <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>params</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>methodResponse</span><span class="token punctuation">&gt;</span></span>
</code></pre>
<p>Почему XML? В конце 90-ых это был модный и «человекочитаемый» формат: сообщение можно было открыть в блокноте и понять, что происходит. По сравнению с бинарными протоколами CORBA и DCOM это было огромным шагом для отладки. Но платой за это был объём: чтобы передать число <code>42</code>, нужно написать <code>&lt;int&gt;42&lt;/int&gt;</code>.</p>
<p>Что здесь важно: URL у всех вызовов один и тот же - <code>/RPC2</code>. И <code>get_user</code>, и <code>get_posts</code>, и любой другой метод идут по этому адресу. Что именно мы хотим сделать, написано не в URL, а в теле запроса.</p>
<p>Из-за этого HTTP работает как труба: доставляет байты от клиента к серверу, но никак не участвует в смысле запроса. Метод <code>POST</code> выбран не потому, что мы что-то создаём, а просто потому, что он позволяет передать тело. Всё остальное протоколу безразлично.</p>
<p>Чтобы получить посты того же пользователя, клиент шлёт ещё один запрос в тот же <code>/RPC2</code>:</p>
<pre class=" language-xml"><code class="prism  language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>methodCall</span><span class="token punctuation">&gt;</span></span>
  <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>methodName</span><span class="token punctuation">&gt;</span></span>get_posts<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>methodName</span><span class="token punctuation">&gt;</span></span>
  <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>params</span><span class="token punctuation">&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>param</span><span class="token punctuation">&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>value</span><span class="token punctuation">&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>int</span><span class="token punctuation">&gt;</span></span>42<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>int</span><span class="token punctuation">&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>value</span><span class="token punctuation">&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>param</span><span class="token punctuation">&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>params</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>methodCall</span><span class="token punctuation">&gt;</span></span>
</code></pre>
<p>Важно: у нас уже две операции - <code>get_user</code> и <code>get_posts</code>. Это разные действия, хотя речь об одном и том же пользователе. Единицей работы в RPC является <strong>функция</strong>. Именно это отличает его от подходов, где в центре стоит ресурс, а действия над ним - уже следствие.</p>
<h2 id="минусы-этих-подходов">Минусы этих подходов</h2>
<p><strong>Жёсткая связность.</strong> Клиент знает точное имя метода и сигнатуру. Если сервер переименует <code>get_user</code> в <code>fetch_user</code> - старые клиенты сломаются: они пошлют <code>get_user</code>, а сервер ответит «метод не найден». И никакой способ узнать об этом заранее, кроме чтения документации, у клиента нет.</p>
<p><strong>Одна точка входа и слепой HTTP.</strong> Все методы идут в <code>/RPC2</code> - URL не говорит, что происходит. Нельзя сказать «дай мне ресурс по адресу» - можно только «выполни метод». А раз URL у всех запросов одинаковый и метод всегда <code>POST</code>, инфраструктура не понимает, что перед ней: чтение или запись, можно ли кэшировать ответ, безопасно ли повторить запрос. Кэш не работает, прокси не помогает, семантика HTTP не используется.</p>
<p><strong>Платформенная привязка.</strong> CORBA и DCOM требовали генерированного кода и работали гладко только внутри своей экосистемы. Выйти за эти рамки было почти невозможно.</p>
<h2 id="куда-это-привело">Куда это привело</h2>
<p>Разница между RPC и тем, что придёт на смену, станет видна на одном сравнении.</p>
<p>RPC-стиль - про действие:</p>
<pre><code>POST /RPC2
methodName: get_user
params: id=42
</code></pre>
<p>REST-стиль - про ресурс:</p>
<pre><code>GET /users/42
</code></pre>
<p>Во втором случае URL сам говорит, что мы хотим. Метод HTTP GET говорит, что мы ничего не меняем. Никакого «methodName» - потому что ресурс и есть метод. Следующая статья будет именно об этом подходе.</p>
<h2 id="главная-мысль">Главная мысль</h2>
<p>Первые API были RPC: «вызови функцию на другой машине». Это работало, но давало жёсткую связность, одну точку входа и плохую совместимость с вебом. Ограничения RPC подтолкнули индустрию к ресурсному подходу.</p>
<p><strong>Что запомнить:</strong></p>
<ul>
<li>RPC - это вызов функции по сети, а не работа с ресурсом.</li>
<li>Ранние типы RPC: CORBA, DCOM, XML-RPC.</li>
<li>В XML-RPC все методы идут в один URL, имя метода - в теле.</li>
<li>Главные минусы: связность, одна точка входа, плохое кэширование.</li>
<li>RPC не забыт окончательно, он вернётся в gRPC.</li>
</ul>
<hr>
<p><strong>Дальше:</strong> <a href="02-soap.md">SOAP: тяжёлая артиллерия корпораций →</a></p>

