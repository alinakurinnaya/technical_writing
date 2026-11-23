---


---

<p><a href="./00-introduction.md">←Введение и оглавдение</a></p>
<h1 id="soap-тяжёлая-артиллерия-корпораций">SOAP: тяжёлая артиллерия корпораций</h1>
<p>В первой статье мы рассмотрели, как RPC пытался сделать вызов функции по сети. Работало с оговорками: одна точка входа, слабая совместимость, никакого формального контракта. Корпорациям этого было мало. Им нужны были транзакции, безопасность и строгие правила. Так появился SOAP.</p>
<h2 id="что-такое-soap">Что такое SOAP</h2>
<p>SOAP (Simple Object Access Protocol) - протокол обмена структурированными сообщениями. Как и XML-RPC, он передаёт данные в формате XML по HTTP. Но в отличие от XML-RPC, SOAP - это не просто «вызов метода в конверте». Это целая экосистема: контракт, стандарты безопасности, надёжная доставка, транзакции.</p>
<p>Если XML-RPC - это открытка с текстом, то SOAP - официальное письмо в конверте с печатью, подписью и вложением в трёх экземплярах.</p>
<h3 id="почему-xml-rpc-не-хватило">Почему XML-RPC не хватило</h3>
<p>XML-RPC был простым - и это его ограничивало. В нём не было места для метаданных: ни токена авторизации, ни идентификатора транзакции, ни заголовка для маршрутизации. Всё, что можно было передать - имя метода и аргументы. Для межбанковского перевода этого мало: нужно подтвердить, что транзакция атомарна, зашифровать сообщение целиком (а не только канал передачи), подписать отправителя. XML-RPC такое не умел.</p>
<p>SOAP появился для решения этой проблемы. Он оставил старую идею вызова метода по сети, но добавил слои, которых не было раньше.</p>
<h2 id="пример-запроса">Пример запроса</h2>
<p>Задача та же, что и в прошлой статье: получить пользователя с <code>id=42</code>. Сравним SOAP с XML-RPC из прошлой статьи:</p>
<pre class=" language-http"><code class="prism  language-http">&lt;!-- XML-RPC, запрос --&gt;
POST /RPC2 HTTP/1.0
<span class="token header-name keyword">Content-Type:</span> text/xml<span class="token text/xml">

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>methodCall</span><span class="token punctuation">&gt;</span></span>
  <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>methodName</span><span class="token punctuation">&gt;</span></span>get_user<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>methodName</span><span class="token punctuation">&gt;</span></span>
  <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>params</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>param</span><span class="token punctuation">&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>value</span><span class="token punctuation">&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>int</span><span class="token punctuation">&gt;</span></span>42<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>int</span><span class="token punctuation">&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>value</span><span class="token punctuation">&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>param</span><span class="token punctuation">&gt;</span></span>
  <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>params</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>methodCall</span><span class="token punctuation">&gt;</span></span>
</span></code></pre>
<pre class=" language-http"><code class="prism  language-http">&lt;!-- SOAP, запрос --&gt;
POST /UserService HTTP/1.1
<span class="token header-name keyword">Content-Type:</span> text/xml; charset=utf-8
<span class="token header-name keyword">SOAPAction:</span> "http://example.com/users/getUser"<span class="token text/xml">

<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">soap:</span>Envelope</span><span class="token punctuation">&gt;</span></span>
  <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">soap:</span>Header</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">auth:</span>Credentials</span><span class="token punctuation">&gt;</span></span>
      <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">auth:</span>Token</span><span class="token punctuation">&gt;</span></span>abc123<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span><span class="token namespace">auth:</span>Token</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span><span class="token namespace">auth:</span>Credentials</span><span class="token punctuation">&gt;</span></span>
  <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span><span class="token namespace">soap:</span>Header</span><span class="token punctuation">&gt;</span></span>
  <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">soap:</span>Body</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>getUser</span><span class="token punctuation">&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>id</span><span class="token punctuation">&gt;</span></span>42<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>id</span><span class="token punctuation">&gt;</span></span><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>getUser</span><span class="token punctuation">&gt;</span></span>
  <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span><span class="token namespace">soap:</span>Body</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span><span class="token namespace">soap:</span>Envelope</span><span class="token punctuation">&gt;</span></span>
</span></code></pre>
<h3 id="три-дополнительных-слоя">Три дополнительных слоя</h3>
<p><code>Envelope</code> - обязательная обёртка - говорит: «это SOAP-сообщение». <code>Header</code> - метаданные (в нашем примере там токен авторизации) - место для того, что не относится к самому вызову: транзакции, маршрутизация. А <code>Body</code> - только сам вызов. Такое разделение позволяет обрабатывать <code>Header</code> отдельно: например, прокси-сервер может проверить токен, не разбирая <code>Body</code>. Именно эти слои дали SOAP то, чего не было у XML-RPC: формальный контракт и встроенные стандарты.</p>
<h3 id="что-такое-soapaction">Что такое SOAPAction</h3>
<p><code>SOAPAction</code> - заголовок, который говорит серверу, какой метод вызывается. Он существует потому, что в теле запроса метод описан в XML, а серверу удобно знать имя метода раньше, чем он распарсит тело. Например, чтобы направить запрос нужному обработчику или проверить права доступа, не разбирая весь XML.</p>
<h2 id="контракт-wsdl">Контракт: WSDL</h2>
<p>Главное отличие SOAP от предшественников - <strong>строгий контракт, описанный в отдельном файле</strong>. Он называется <strong>WSDL (Web Services Description Language)</strong> - это XML-документ, в котором описан сервис: какие методы у него есть, какие принимает параметры, что возвращает, по какому адресу живёт.</p>
<p>Вот фрагмент WSDL для нашего сервиса:</p>
<pre class=" language-xml"><code class="prism  language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>definitions</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>UserService<span class="token punctuation">"</span></span>
  <span class="token attr-name">targetNamespace</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://example.com/users<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>

  <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>message</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>GetUserRequest<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>part</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>id<span class="token punctuation">"</span></span> <span class="token attr-name">type</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>xsd:int<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
  <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>message</span><span class="token punctuation">&gt;</span></span>

  <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>message</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>GetUserResponse<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>part</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>user<span class="token punctuation">"</span></span> <span class="token attr-name">type</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>tns:User<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
  <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>message</span><span class="token punctuation">&gt;</span></span>

  <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>portType</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>UserServicePort<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>operation</span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>getUser<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
      <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>input</span> <span class="token attr-name">message</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>tns:GetUserRequest<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
      <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>output</span> <span class="token attr-name">message</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>tns:GetUserResponse<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>operation</span><span class="token punctuation">&gt;</span></span>
  <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>portType</span><span class="token punctuation">&gt;</span></span>

  <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>binding</span> <span class="token attr-name">type</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>tns:UserServicePort<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span><span class="token namespace">soap:</span>binding</span><span class="token style-attr language-css"><span class="token attr-name"> <span class="token attr-name">style</span></span><span class="token punctuation">="</span><span class="token attr-value">document</span><span class="token punctuation">"</span></span>
      <span class="token attr-name">transport</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>http://schemas.xmlsoap.org/soap/http<span class="token punctuation">"</span></span><span class="token punctuation">/&gt;</span></span>
  <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>binding</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>definitions</span><span class="token punctuation">&gt;</span></span>
</code></pre>
<p>Что это даёт: клиент может <strong>сгенерировать код автоматически</strong> из WSDL. Не писать вручную, не угадывать структуру запроса - просто скачать контракт и получить готовый класс:</p>
<pre class=" language-java"><code class="prism  language-java">UserService service <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">UserService</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
User user <span class="token operator">=</span> service<span class="token punctuation">.</span><span class="token function">getUser</span><span class="token punctuation">(</span><span class="token number">42</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
</code></pre>
<p>Это было огромным шагом вперёд. Контракт стал машиночитаемым. Сервер и клиент больше не могли «разойтись» в понимании структуры данных.</p>
<h3 id="чем-wsdl-отличается-от-idl">Чем WSDL отличается от IDL</h3>
<p>По идее WSDL похож на IDL из CORBA: тоже описывает интерфейс, тоже из него генерируется код. Разница в том, что WSDL - это <strong>сам XML-документ</strong>, а не отдельный язык. Его можно открыть в браузере, положить на сервер рядом с сервисом, прочитать программно. WSDL-файл лежит по адресу, и клиент может <strong>скачать его автоматически</strong> - как документацию, которую машина умеет читать.</p>
<p>В CORBA IDL был отдельным языком, и для каждой платформы был свой компилятор IDL. В SOAP WSDL - просто XML, и его может прочитать любой инструмент, умеющий работать с XML.</p>
<h2 id="не-только-вызовы">Не только вызовы</h2>
<p>Поверх SOAP выросло целое семейство стандартов WS-*. Каждый решал свою корпоративную задачу:</p>
<ul>
<li><strong>WS-Security</strong> - подписи, шифрование, токены на уровне сообщения, а не транспорта.</li>
<li><strong>WS-ReliableMessaging</strong> - гарантированная доставка: сообщение не потеряется, не задублируется.</li>
<li><strong>WS-AtomicTransaction</strong> - распределённые транзакции: либо всё прошло, либо ничего.</li>
<li><strong>WS-Policy</strong> - правила: какие требования предъявляются к клиенту.</li>
</ul>
<p>Именно из-за этого SOAP прижился там, где ошибка стоит дорого: в банках, биллинге, госсекторе.</p>
<h2 id="а-минусы-всё-те-же...">А минусы всё те же…</h2>
<p><strong>Тяжесть.</strong> Каждый запрос XML содержит многослойные обёртки. Простой <code>getUser(42)</code> превращается в 20 строк вместо одной. Сообщения в разы больше, чем в JSON.</p>
<p><strong>Сложность инструментов.</strong> WSDL-файлы огромные, генераторы кода капризные, отладка через curl почти невозможна, нужны специальные инструменты. Чтобы поднять SOAP-сервис, нужны фреймворки Apache CXF, Axis, .NET WCF.</p>
<p><strong>Хрупкость стандартов.</strong> WS-* стандартов десятки, и не все реализации совместимы. Два вроде бы корректных SOAP-сервиса иногда просто не могли договориться.</p>
<p><strong>Плохая дружба с вебом.</strong> Как и XML-RPC, SOAP использует один endpoint и POST для всего. Но если XML-RPC был хотя бы лёгким, SOAP добавил сверху XML-обёртки, которые сделали сообщения ещё тяжелее. Кэширование по URL не работает и семантика HTTP (GET, PUT, DELETE) не используется.</p>
<h2 id="куда-это-привело">Куда это привело</h2>
<p>SOAP прочно занял нишу, где нужны транзакции и формальные контракты: банковские API, интеграция ERP-систем, госуслуги.</p>
<p>Но вебу нужен был другой подход. Лёгкий, читаемый, работающий с семантикой HTTP напрямую. Сравним:</p>
<p>SOAP - действие в конверте …</p>
<pre><code>POST /UserService
SOAPAction: "getUser"

&lt;soap:Envelope&gt;
  &lt;soap:Body&gt;
    &lt;getUser&gt;&lt;id&gt;42&lt;/id&gt;&lt;/getUser&gt;
  &lt;/soap:Body&gt;
&lt;/soap:Envelope&gt;
</code></pre>
<p>… заменится ресурсом по адресу:</p>
<pre><code>GET /users/42
</code></pre>
<p>Он сам говорит, что мы хотим, а метод HTTP - что мы делаем. Больше об этом - в следующей статья.</p>
<h2 id="главная-мысль">Главная мысль</h2>
<p>SOAP решил не выходящие за рамки XML проблемы RPC: дал формальный контракт (WSDL), встроенную безопасность и транзакции. Но добавил ещё больше объёма, сложности и плохой совместимости с вебом. В корпоративной среде он остался, в вебе - уступил место REST.</p>
<p><strong>Что запомнить:</strong></p>
<ul>
<li>SOAP - XML-сообщения с обязательными обёртками <code>Envelope</code>, <code>Header</code>, <code>Body</code>.</li>
<li>WSDL - контракт, из которого генерируется клиентский код.</li>
<li>WS-* - семейство стандартов для безопасности, транзакций, доставки.</li>
<li>Плюсы: строгий контракт, встроенные корпоративные функции.</li>
<li>Минусы: тяжесть, сложные инструменты, плохое кэширование.</li>
<li>Жив там, где важнее надёжность, чем простота: банки, биллинг, госсектор.</li>
</ul>
<p><strong>Дальше:</strong> <a href="03-rest.md">REST: революция простоты</a></p>

