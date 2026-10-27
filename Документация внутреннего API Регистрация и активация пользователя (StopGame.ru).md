---


---

<h1 id="документация-внутреннего-api-регистрация-и-активация-пользователя-stopgame.ru">Документация внутреннего API: Регистрация и активация пользователя (<a href="http://StopGame.ru">StopGame.ru</a>)</h1>
<p><strong>Статус документа:</strong> Исследовательский кейс.</p>
<h2 id="оглавление">Оглавление</h2>
<ul>
<li><a href="#%D0%BE-%D0%B4%D0%BE%D0%BA%D1%83%D0%BC%D0%B5%D0%BD%D1%82%D0%B5-%D0%B8-%D0%BC%D0%B5%D1%82%D0%BE%D0%B4%D0%BE%D0%BB%D0%BE%D0%B3%D0%B8%D1%8F">О документе и методология</a></li>
<li><a href="#%D1%88%D0%B0%D0%B3-0-%D0%BF%D1%80%D0%B5%D0%B4%D0%B2%D0%B0%D1%80%D0%B8%D1%82%D0%B5%D0%BB%D1%8C%D0%BD%D0%BE%D0%B5-%D1%83%D1%81%D0%BB%D0%BE%D0%B2%D0%B8%D0%B5-yandex-smartcaptcha">Шаг 0: Предварительное условие (Yandex SmartCaptcha)</a></li>
<li><a href="#%D1%88%D0%B0%D0%B3-1-%D1%80%D0%B5%D0%B3%D0%B8%D1%81%D1%82%D1%80%D0%B0%D1%86%D0%B8%D1%8F-%D0%BD%D0%BE%D0%B2%D0%BE%D0%B3%D0%BE-%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D1%82%D0%B5%D0%BB%D1%8F">Шаг 1: Регистрация нового пользователя</a></li>
<li><a href="#%D1%88%D0%B0%D0%B3-2-%D0%B0%D0%BA%D1%82%D0%B8%D0%B2%D0%B0%D1%86%D0%B8%D1%8F-%D0%B0%D0%BA%D0%BA%D0%B0%D1%83%D0%BD%D1%82%D0%B0">Шаг 2: Активация аккаунта</a></li>
<li><a href="#%D0%BE%D1%82%D0%BA%D1%80%D1%8B%D1%82%D1%8B%D0%B5-%D0%B2%D0%BE%D0%BF%D1%80%D0%BE%D1%81%D1%8B-%D1%82%D1%80%D0%B5%D0%B1%D1%83%D0%B5%D1%82-%D1%83%D1%82%D0%BE%D1%87%D0%BD%D0%B5%D0%BD%D0%B8%D1%8F-%D1%83-sme">Открытые вопросы (Требует уточнения у SME)</a></li>
</ul>
<hr>
<h2 id="о-документе-и-методология">О документе и методология</h2>
<p>Документ описывает внутренний API портала StopGame для создания и активации аккаунтов.</p>
<p><strong>Как я собирала данные:</strong><br>
Я использовала браузерные DevTools (вкладка Network) и проанализировала полный цикл регистрации:</p>
<ul>
<li>Изучила цепочку запросов и интеграцию с Yandex SmartCaptcha.</li>
<li>Зафиксировала форматы данных (<code>multipart/form-data</code>).</li>
<li>Провела симуляцию ошибок через консоль браузера, чтобы обойти клиентскую валидацию.</li>
<li>Сформулировала вопросы для Backend-разработчика (SME).</li>
</ul>
<hr>
<h2 id="шаг-0-предварительное-условие-yandex-smartcaptcha">Шаг 0: Предварительное условие (Yandex SmartCaptcha)</h2>
<p>Перед регистрацией пользователь проходит капчу. Сайт использует Yandex SmartCaptcha.</p>
<p><strong>Что делает браузер:</strong></p>
<ol>
<li>Показывает виджет с ползунком.</li>
<li>Получает токены после успешной проверки.</li>
<li>Отправляет токены в запросе на регистрацию в поля <code>smart-token</code> и <code>smart-captcha-token</code>.</li>
</ol>
<p><em>Как настроить виджет — в <a href="https://yandex.cloud/ru/docs/smartcaptcha/">официальной документации Yandex SmartCaptcha</a>.</em></p>
<hr>
<h2 id="шаг-1-регистрация-нового-пользователя">Шаг 1: Регистрация нового пользователя</h2>
<p>Метод создает новый аккаунт. Перед вызовом обязательна проверка капчи.</p>
<blockquote>
<p><strong>Особенность:</strong> В запросе нет пароля. Сервер сам генерирует пароль и присылает его в приветственном письме.</p>
</blockquote>
<p><strong>Метод:</strong> <code>POST</code><br>
<strong>Эндпоинт:</strong> <code>https://stopgame.ru/ajax/auth/register</code></p>
<h3 id="заголовки-запроса-headers">Заголовки запроса (Headers)</h3>

<table>
<thead>
<tr>
<th align="left">Заголовок</th>
<th align="left">Обязательный</th>
<th align="left">Описание</th>
<th align="left">Пример значения</th>
</tr>
</thead>
<tbody>
<tr>
<td align="left"><code>Content-Type</code></td>
<td align="left">Да</td>
<td align="left">Формат данных.</td>
<td align="left"><code>multipart/form-data</code></td>
</tr>
<tr>
<td align="left"><code>X-CSRF-Token</code></td>
<td align="left">Да</td>
<td align="left">Токен безопасности. Браузер получает его при загрузке формы.</td>
<td align="left"><code>n8m_LN7GaAZ09...</code></td>
</tr>
</tbody>
</table><h3 id="тело-запроса-payload">Тело запроса (Payload)</h3>

<table>
<thead>
<tr>
<th align="left">Параметр</th>
<th align="left">Тип</th>
<th align="left">Обязательный</th>
<th align="left">Ограничения</th>
<th align="left">Пример значения</th>
</tr>
</thead>
<tbody>
<tr>
<td align="left"><code>login</code></td>
<td align="left">string</td>
<td align="left">Да</td>
<td align="left">Должен быть уникальным.</td>
<td align="left"><code>gamer_one</code></td>
</tr>
<tr>
<td align="left"><code>email</code></td>
<td align="left">string</td>
<td align="left">Да</td>
<td align="left">Валидный адрес.</td>
<td align="left"><code>gamer@example.ru</code></td>
</tr>
<tr>
<td align="left"><code>email_confirmation</code></td>
<td align="left">string</td>
<td align="left">Да</td>
<td align="left">Должен совпадать с <code>email</code>.</td>
<td align="left"><code>gamer@example.ru</code></td>
</tr>
<tr>
<td align="left"><code>website</code></td>
<td align="left">string</td>
<td align="left">Нет</td>
<td align="left">Ловушка для ботов (honeypot). Оставлять пустым.</td>
<td align="left"><em>(пустая строка)</em></td>
</tr>
<tr>
<td align="left"><code>smart-token</code></td>
<td align="left">string</td>
<td align="left">Да</td>
<td align="left">Основной токен капчи. <em>(Уточнить у SME)</em></td>
<td align="left"><code>dD0xNzky...</code></td>
</tr>
<tr>
<td align="left"><code>smart-captcha-token</code></td>
<td align="left">string</td>
<td align="left">Да</td>
<td align="left">Дополнительный токен капчи. <em>(Уточнить у SME)</em></td>
<td align="left"><code>dD0xNzky...</code></td>
</tr>
</tbody>
</table><h3 id="пример-запроса-curl">Пример запроса (cURL)</h3>
<pre class=" language-bash"><code class="prism  language-bash">curl -X POST <span class="token string">'https://stopgame.ru/ajax/auth/register'</span> \
-H <span class="token string">'Content-Type: multipart/form-data; boundary=----WebKitFormBoundaryXYZ'</span> \
-H <span class="token string">'X-CSRF-Token: n8m_LN7GaAZ09...'</span> \
--form <span class="token string">'login="gamer_one"'</span> \
--form <span class="token string">'email="gamer@example.com"'</span> \
--form <span class="token string">'email_confirmation="gamer@example.com"'</span> \
--form <span class="token string">'website=""'</span> \
--form <span class="token string">'smart-token="dD0xNzkv..."'</span> \
--form <span class="token string">'smart-captcha-token="dD0xNzkv..."'</span>
</code></pre>
<h3 id="ответы-responses">Ответы (Responses)</h3>
<p><strong>Успешный ответ (200 OK)</strong><br>
Сервер создает аккаунт. В заголовках ответа (<code>Response Headers</code>) устанавливает сессионные куки (<code>Set-Cookie</code>).</p>
<pre class=" language-json"><code class="prism  language-json"><span class="token punctuation">{</span>
  <span class="token string">"success"</span><span class="token punctuation">:</span> <span class="token boolean">true</span>
<span class="token punctuation">}</span>
</code></pre>
<hr>
<h2 id="шаг-2-активация-аккаунта">Шаг 2: Активация аккаунта</h2>
<p>После регистрации сервер отправляет письмо со ссылкой:<br>
<code>https://stopgame.ru/?utm_source=user_activation_email&amp;utm_medium=email#auth/activate/&lt;code&gt;</code></p>
<p>В ссылке есть hash-фрагмент (<code>#</code>). Браузер не отправляет запрос напрямую. Он загружает главную страницу. Скрипт на странице (SPA) считывает код из адресной строки и делает фоновый (AJAX) запрос на активацию.</p>
<p><strong>Метод:</strong> <code>POST</code><br>
<strong>Эндпоинт:</strong> <code>https://stopgame.ru/ajax/auth/activate</code></p>
<h3 id="заголовки-запроса-headers-1">Заголовки запроса (Headers)</h3>

<table>
<thead>
<tr>
<th align="left">Заголовок</th>
<th align="left">Обязательный</th>
<th align="left">Описание</th>
<th align="left">Пример значения</th>
</tr>
</thead>
<tbody>
<tr>
<td align="left"><code>Content-Type</code></td>
<td align="left">Да</td>
<td align="left">Формат данных.</td>
<td align="left"><code>multipart/form-data</code></td>
</tr>
<tr>
<td align="left"><code>X-CSRF-Token</code></td>
<td align="left">Да</td>
<td align="left">Токен безопасности.</td>
<td align="left"><code>zASVDD...</code></td>
</tr>
</tbody>
</table><h3 id="тело-запроса-payload-1">Тело запроса (Payload)</h3>

<table>
<thead>
<tr>
<th align="left">Параметр</th>
<th align="left">Тип</th>
<th align="left">Обязательный</th>
<th align="left">Описание и ограничения</th>
<th align="left">Пример значения</th>
</tr>
</thead>
<tbody>
<tr>
<td align="left"><code>code</code></td>
<td align="left">string</td>
<td align="left">Да</td>
<td align="left">32-символьная hex-строка из письма.</td>
<td align="left"><code>a84acc277c1d33b09d9d8bd986ea2191</code></td>
</tr>
</tbody>
</table><h3 id="ответы-responses-1">Ответы (Responses)</h3>
<p><strong>Успешный ответ (200 OK)</strong></p>
<pre class=" language-json"><code class="prism  language-json"><span class="token punctuation">{</span>
  <span class="token string">"success"</span><span class="token punctuation">:</span> <span class="token boolean">true</span>
<span class="token punctuation">}</span>
</code></pre>
<hr>
<h2 id="открытые-вопросы-требует-уточнения-у-sme">Открытые вопросы (Требует уточнения у SME)</h2>
<p><strong>1. Обработка ошибок:</strong></p>
<ul>
<li>Сервер возвращает <code>200 OK</code> даже при ошибках (например, при занятом логине). Это финальное решение или баг? Стоит ли разработчикам игнорировать HTTP-статусы и проверять только поле <code>success</code>?</li>
<li>Какие статус-коды и ответы вы отдаете при внутренних сбоях сервера (5xx)?</li>
</ul>
<p><strong>2. Безопасность:</strong></p>
<ul>
<li>Где браузер берет <code>X-CSRF-Token</code> перед отправкой формы? Сколько времени живет этот токен?</li>
<li>Обязательны ли куки (например, от DDoS-Guard) для успешного запроса? Или серверу достаточно заголовков <code>X-CSRF-Token</code> и <code>Content-Type</code>?</li>
</ul>
<p><strong>3. Валидация:</strong></p>
<ul>
<li>Поле <code>website</code> — это honeypot? Обязаны ли сторонние приложения передавать его пустым?</li>
<li>Зачем нужны два токена капчи (<code>smart-token</code> и <code>smart-captcha-token</code>)? Обязательно ли передавать оба?</li>
</ul>
<p><strong>4. Бизнес-логика:</strong></p>
<ul>
<li>Есть ли метод для повторной отправки письма, если 24 часа истекли?</li>
<li>Что вернет сервер, если пользователь попытается войти до активации аккаунта?</li>
</ul>
<pre><code></code></pre>

