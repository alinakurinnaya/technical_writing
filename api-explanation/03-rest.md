---


---

<p><a href="./00-introduction.md">←Введение и оглавдение</a></p>
<h1 id="rest-революция-простоты">REST: революция простоты</h1>
<p>SOAP удовлетворил корпорации своими контрактами и транзакциями, но вебу был нужен другой подход. Лёгкий, читаемый, работающий с HTTP напрямую, а не использующий его просто как транспорт. В 2000 году Рой Филдинг описал REST и на следующие 15 лет сделал его “золотым стандартом”.</p>
<h2 id="что-такое-rest">Что такое REST</h2>
<p>REST (Representational State Transfer) - архитектурный стиль, а не протокол или стандарт. Филдинг сформулировал его как набор ограничений: клиент-серверная архитектура, отсутствие состояния (statelessness), кэшируемость, единообразный интерфейс, многоуровневость. Если все выполнены - API «RESTful». Если нет, то мы имеем дело с просто HTTP-API.</p>
<p>Ключевая идея: мы работаем с ресурсами, а не вызываем действия. URL - адрес ресурса, HTTP-метод - то, что мы с ним делаем. Никаких <code>getUser</code> и <code>SOAPAction</code>. Сам адрес и метод уже говорят обо всём.</p>
<p>Аналогия с библиотекой. Вы приходите не с просьбой «выполни функцию выдачи книги», а с адресом книги: «полка 3, 12-ая слева». Библиотекарь понимает, что вы хотите, по адресу и по тому, что вы делаете: берёте, ставите на место, спрашиваете, есть ли ещё. Нужны только объекты и операции над ними, никаких «функций» и «методов».</p>
<h2 id="пример-запроса">Пример запроса</h2>
<p>Та же задача: получить пользователя с <code>id=42</code>.</p>
<pre class=" language-http"><code class="prism  language-http">GET /users/42 HTTP/1.1
<span class="token header-name keyword">Host:</span> api.example.com
<span class="token header-name keyword">Accept:</span> application/json
<span class="token header-name keyword">Authorization:</span> Bearer abc123
</code></pre>
<p>Ответ:</p>
<pre class=" language-http"><code class="prism  language-http"><span class="token response-status">HTTP/1.1 <span class="token property">200 OK</span></span>
<span class="token header-name keyword">Content-Type:</span> application/json
{
 "id": 42,
 "name": "Alice",
 "email": "alice@example.com"
}
</code></pre>
<p>Сравним с SOAP:</p>
<pre class=" language-http"><code class="prism  language-http">&lt;!-- SOAP, запрос --&gt;
POST /UserService
<span class="token header-name keyword">SOAPAction:</span> "getUser"
&lt;soap:Envelope&gt;
 &lt;soap:Header&gt;
 &lt;auth:Credentials&gt;
 &lt;auth:Token&gt;abc123&lt;/auth:Token&gt;
 &lt;/auth:Credentials&gt;
 &lt;/soap:Header&gt;
 &lt;soap:Body&gt;
 &lt;getUser&gt;&lt;id&gt;42&lt;/id&gt;&lt;/getUser&gt;
 &lt;/soap:Body&gt;
&lt;/soap:Envelope&gt;
</code></pre>
<p>Три главных отличия от всего RPC-подхода:</p>
<ol>
<li><strong>Адрес говорит, что мы хотим.</strong> <code>/users/42</code> - это ресурс, не нужно читать тело, чтобы понять запрос.</li>
<li><strong>Метод говорит, что мы делаем.</strong> <code>GET</code> - читаем, никакого <code>SOAPAction</code>. Семантика HTTP используется по назначению.</li>
<li><strong>Тело крошечное или отсутствует.</strong> Всё, что нужно, находится в адресе и методе.</li>
</ol>
<h2 id="ресурсы-вместо-действий">Ресурсы вместо действий</h2>
<p>Это главный прорыв. В RPC и SOAP мы называли действия: <code>getUser</code>, <code>createOrder</code>, <code>deleteComment</code>. В REST мы называем ресурсы: <code>/users/42</code>, <code>/orders</code>, <code>/comments/7</code>.</p>
<p><strong>Ресурс</strong> - это всё, к чему можно обратиться по адресу: отдельная сущность (<code>/users/42</code>), коллекция (<code>/users</code>), связь между сущностями (<code>/users/42/posts</code>). Ресурс может быть и абстрактным - например, результат поиска (<code>/search?q=...</code>). Главное, что у него есть адрес, и с ним можно что-то сделать через HTTP-метод.</p>
<p>Разница видна на полном наборе операций:</p>

<table>
<thead>
<tr>
<th>Что делаем</th>
<th>RPC / SOAP</th>
<th>REST</th>
</tr>
</thead>
<tbody>
<tr>
<td>Получить пользователя</td>
<td><code>POST /getUser</code></td>
<td><code>GET /users/42</code></td>
</tr>
<tr>
<td>Создать пользователя</td>
<td><code>POST /createUser</code></td>
<td><code>POST /users</code></td>
</tr>
<tr>
<td>Обновить пользователя</td>
<td><code>POST /updateUser</code></td>
<td><code>PUT /users/42</code></td>
</tr>
<tr>
<td>Удалить пользователя</td>
<td><code>POST /deleteUser</code></td>
<td><code>DELETE /users/42</code></td>
</tr>
<tr>
<td>Получить посты пользователя</td>
<td><code>POST /getUserPosts</code></td>
<td><code>GET /users/42/posts</code></td>
</tr>
</tbody>
</table><p>Обратим внимание: в REST один ресурс это четыре разных действия через четыре разных метода. В RPC/SOAP это четыре разных эндпоинта с глаголами в названии. В этом и кроются принципиально разные модели мышления.</p>
<h2 id="семантика-http">Семантика HTTP</h2>
<p><strong>Идемпотентность</strong> - свойство, при котором повторный запрос даёт тот же результат, что и первый. <code>PUT</code> и <code>DELETE</code> идемпотентны: если повторить их десять раз, ничего не сломается. <code>POST</code> - нет: два одинаковых запроса создадут две сущности.</p>
<p>Расмотрим подробнее запросы:</p>
<ul>
<li><code>GET</code> - прочитать. Не меняет состояние, можно кэшировать и безопасно повторять, то есть он идемпотентен.</li>
<li><code>POST</code> - создать. Он не идемпотентен: два одинаковых запроса создадут две сущности.</li>
<li><code>PUT</code> - заменить целиком. Идемпотентен: повторишь и получишь тот же результат.</li>
<li><code>PATCH</code> - изменить частично. Может быть идемпотентным, а может и нет, это зависит от семантики операции.</li>
<li><code>DELETE</code> - удалить. Идемпотентен.</li>
</ul>
<p>И статус-коды:</p>
<ul>
<li><code>200 OK</code> - успех.</li>
<li><code>201 Created</code> - создано (ответ на <code>POST</code>).</li>
<li><code>204 No Content</code> - успех без тела (часто ответ на <code>DELETE</code>).</li>
<li><code>400 Bad Request</code> - запрос сформирован неверно: отсутствует обязательное поле, неправильный тип данных и т.д.</li>
<li><code>404 Not Found</code> - ресурса нет.</li>
<li><code>500 Internal Server Error</code> - сервер сломался.</li>
</ul>
<p>Методы и статус-коды - не изобретение REST, а стандарт HTTP. Поэтому их понимает всё, что работает с HTTP: прокси знают, что <code>GET</code> можно кэшировать, балансировщики - что <code>POST</code> и <code>DELETE</code> ведут себя по-разному, браузеры - что <code>GET</code> безопасно повторять.</p>
<h2 id="statelessness">Statelessness</h2>
<p>У REST есть ограничение: сервер не хранит состояние клиента между запросами. Каждый запрос приходит с токеном авторизации, с нужными параметрами, и сервер не помнит, что было раньше.</p>
<p>Но это дало то, что любой сервер из кластера может обработать любой запрос. Не нужно привязывать клиента к конкретной машине и масштабирование становится простым.</p>
<h2 id="снова-про-минусы">Снова про минусы</h2>
<p><strong>Over-fetching.</strong> Мобильному приложению трафик дорог, но когда клиент просит <code>/users/42</code>, он получает весь объект с 30 полями, из которых нужны два.</p>
<p><strong>Under-fetching.</strong> Если мобильному приложению нужны пользователь, его посты и комментарии к постам, то REST заставляет делать три запроса:</p>
<pre class=" language-http"><code class="prism  language-http">GET /users/42
GET /users/42/posts
GET /posts/7/comments
</code></pre>
<p>Это значит, что на медленной сети будет заметная задержка.</p>
<p><strong>Сложные операции плохо ложатся на ресурсы.</strong> «Перевести деньги со счёта A на счёт B» - это не <code>PUT /accounts/A</code> и не <code>POST /transfers</code>. Это действие, которое меняет два ресурса сразу. Приходится придумывать обходные эндпоинты, и чистота REST размывается.</p>
<p><strong>Версионирование.</strong> Пока API публичный, контракт неизбежно меняется. Куда девать версию - в URL (<code>/v1/users</code>) или в заголовок - спорят до сих пор.</p>
<h2 id="почему-rest-всё-таки-победил">Почему REST всё-таки победил</h2>
<p>REST не требовал ни нового протокола, ни специальных инструментов. HTTP уже был везде: в браузерах, прокси, балансировщиках, CDN. REST просто начал использовать то, что и так работало. Плюс его читаемость: запрос <code>GET /users/42</code> понятен и человеку, и машине без документации. Это и сделало его стандартом для публичных API.<p>
<p>У REST нет встроенного способа описывать контракт. Его добавляют отдельно, чаще всего через OpenAPI: файл, в котором описаны все эндпоинты, параметры и ответы. Но это не часть REST, а надстройка. О ней - подробнее в следующих главах.</p>
<h2 id="куда-это-привело">Куда это привело</h2>
<p>REST стал золотым стандартом. Публичные API - GitHub, Stripe, Twitter - построены на нём. Но боль over- и under-fetching никуда не делась, и на ней выросли новые подходы.</p>
<p>Сравним. Клиенту нужно три ресурса.</p>
<p>Три REST запроса:</p>
<pre class=" language-http"><code class="prism  language-http">GET /users/42
GET /users/42/posts
GET /posts/7/comments
</code></pre>
<p>То, что придёт на смену, позволит одним запросом указать, какие поля и связи нужны:</p>
<pre class=" language-graphql"><code class="prism  language-graphql">POST /graphql
<span class="token punctuation">{</span>
  user<span class="token punctuation">(</span><span class="token attr-name">id</span><span class="token punctuation">:</span> <span class="token number">42</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
    name
    posts <span class="token punctuation">{</span>
      title
      comments <span class="token punctuation">{</span> text <span class="token punctuation">}</span>
    <span class="token punctuation">}</span>
  <span class="token punctuation">}</span>
<span class="token punctuation">}</span>
</code></pre>
<p>Один запрос выдаёт все данные нужных полей. Об этом будет следующая статья.</p>
<h2 id="главная-мысль">Главная мысль</h2>
<p>REST заменил «вызов действия» на «работу с ресурсом». Адрес говорит, что мы хотим, метод HTTP - что делаем, статус-код - чем это закончилось. Это сделало API простыми, кэшируемыми и совместимыми с любой инфраструктурой. Но у простоты есть цена: over-fetching, under-fetching и плохая поддержка сложных операций.</p>
<p><strong>Что запомнить:</strong></p>
<ul>
<li>REST - архитектурный стиль, а не протокол.</li>
<li>Его ресурсы в URL, а действия в HTTP-методах.</li>
<li>HTTP-методы и статус-коды используются по назначению.</li>
<li>Statelessness: каждый запрос самодостаточен.</li>
<li>Плюсы: простота, кэширование, работа с любой инфраструктурой.</li>
<li>Минусы: over-fetching, under-fetching, сложные операции, версионирование.</li>
<li>REST - золотой стандарт публичных API, но не единственный подход.</li>
</ul>
<p><strong>Дальше:</strong> <a href="04-graphql.md">GraphQL: запрос от клиента →</a></p>

