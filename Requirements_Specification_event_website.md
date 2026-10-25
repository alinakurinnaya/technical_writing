---


---

<h1 id="тз-для-команды-разработки-сайт-для-записи-на-ивент">ТЗ для команды разработки: сайт для записи на ивент</h1>
<h2 id="что-делаем">1. Что делаем</h2>
<p>Сайт, где посетитель читает описание ивента и записывается на него. Админ видит список участников, редактирует его, отмечает посещение и выгружает данные.</p>
<p>Делаем MVP: без платежей, рассылок и CRM. Всё, что не мешает основной записи и админскому учёту, уходит в «позже».</p>
<h2 id="что-входит-в-mvp-а-что-нет">2. Что входит в MVP, а что нет</h2>
<p><strong>MVP:</strong></p>
<ul>
<li>Публичная страница ивента с описанием.</li>
<li>Форма записи и подтверждение о ней.</li>
<li>Вход в админку.</li>
<li>Список участников, CRUD, отметка посещения.</li>
<li>Экспорт списка.</li>
<li>Валидация и защита от дублей.</li>
</ul>
<p><strong>Вне MVP:</strong></p>
<ul>
<li>Онлайн-оплата.</li>
<li>Личный кабинет участника.</li>
<li>Email/SMS-рассылки и напоминания.</li>
<li>Интеграции с Google Calendar, Telegram, CRM.</li>
<li>Вместимость и автозакрытие записи.</li>
<li>Дедлайн записи.</li>
<li>Поиск и фильтр в админке.</li>
<li>Самостоятельная отписка участника.</li>
<li>Несколько ивентов в UI.</li>
<li>Роли сложнее, чем «админ / не админ».</li>
<li>Форматированный Excel-отчёт.</li>
</ul>
<h2 id="приоритеты">3. Приоритеты</h2>

<table>
<thead>
<tr>
<th>Фича</th>
<th>Приоритет</th>
</tr>
</thead>
<tbody>
<tr>
<td>Публичная страница ивента</td>
<td>P0</td>
</tr>
<tr>
<td>Форма записи</td>
<td>P0</td>
</tr>
<tr>
<td>Валидация и защита от дублей</td>
<td>P0</td>
</tr>
<tr>
<td>Вход в админку</td>
<td>P0</td>
</tr>
<tr>
<td>Список участников</td>
<td>P0</td>
</tr>
<tr>
<td>CRUD участников</td>
<td>P0</td>
</tr>
<tr>
<td>Отметка посещения</td>
<td>P0</td>
</tr>
<tr>
<td>Экспорт CSV</td>
<td>P0</td>
</tr>
<tr>
<td>Поиск и фильтр в админке</td>
<td>P1</td>
</tr>
<tr>
<td>Вместимость и автозакрытие записи</td>
<td>P1</td>
</tr>
<tr>
<td>Дедлайн записи</td>
<td>P1</td>
</tr>
<tr>
<td>Самостоятельная отписка</td>
<td>P2</td>
</tr>
<tr>
<td>Email-уведомления</td>
<td>P2</td>
</tr>
<tr>
<td>Несколько ивентов</td>
<td>P2</td>
</tr>
</tbody>
</table><h2 id="роли">4. Роли</h2>
<p>С точки зрения доступа ролей две: анонимный посетитель и админ.</p>

<table>
<thead>
<tr>
<th>Роль</th>
<th>Аутентификация</th>
<th>Может</th>
<th>Не может</th>
</tr>
</thead>
<tbody>
<tr>
<td>Посетитель</td>
<td>Нет</td>
<td>Смотреть ивент, отправить форму записи.</td>
<td>Открыть админку, видеть других участников, править свою запись</td>
</tr>
<tr>
<td>Админ</td>
<td>Логин и пароль</td>
<td>Смотреть список, добавлять / править / удалять участников, менять статус посещения, выгружать CSV</td>
<td>—</td>
</tr>
</tbody>
</table><h2 id="публичная-часть">5. Публичная часть</h2>
<h3 id="страница-ивента">5.1. Страница ивента</h3>
<p>Показываем:</p>
<ul>
<li>название;</li>
<li>дату и время;</li>
<li>место или ссылку на онлайн-площадку;</li>
<li>описание;</li>
<li>кнопку «Записаться».</li>
</ul>
<p>Поведение кнопки:</p>
<ul>
<li>Запись открыта — кнопка активна.</li>
<li>Запись закрыта — кнопка неактивна, рядом текст «Запись закрыта».</li>
</ul>
<h3 id="форма-записи">5.2. Форма записи</h3>
<p>Поля:</p>

<table>
<thead>
<tr>
<th>Поле</th>
<th>Обязательное</th>
<th>Ограничения</th>
</tr>
</thead>
<tbody>
<tr>
<td>Имя</td>
<td>Да</td>
<td>1–100 символов</td>
</tr>
<tr>
<td>Email</td>
<td>Да</td>
<td>Валидный email, уникален в рамках ивента</td>
</tr>
<tr>
<td>Телефон</td>
<td>Нет</td>
<td>Строка</td>
</tr>
<tr>
<td>Комментарий</td>
<td>Нет</td>
<td>До 500 символов</td>
</tr>
</tbody>
</table><p>Кнопка: «Записаться».</p>
<p>После успешной записи показываем:</p>
<ul>
<li>«Вы записаны на ивент».</li>
<li>Сводку: имя, email, название ивента.</li>
</ul>
<p>Ошибки:</p>
<ul>
<li>Пустое обязательное поле — подсветить и написать, что заполнить.</li>
<li>Некорректный email — «Введите корректный email».</li>
<li>Повторная запись с тем же email — «Похоже, вы уже записались на этот ивент».</li>
<li>Техническая ошибка — «Не удалось записаться. Попробуйте ещё раз».</li>
</ul>
<h2 id="админка">6. Админка</h2>
<h3 id="вход">6.1. Вход</h3>
<ul>
<li>Отдельная страница <code>/admin</code>.</li>
<li>Логин и пароль, пароль храним хешем.</li>
<li>Без входа админские страницы недоступны.</li>
<li>Для MVP достаточно одного админа.</li>
</ul>
<h3 id="список-участников">6.2. Список участников</h3>
<p>Одна таблица на текущий ивент. Сортировка по имени, по алфавиту.</p>

<table>
<thead>
<tr>
<th>Колонка</th>
<th>Источник</th>
<th>Можно менять из списка</th>
</tr>
</thead>
<tbody>
<tr>
<td>№</td>
<td>Порядковый номер в текущей выборке</td>
<td>—</td>
</tr>
<tr>
<td>Имя</td>
<td><code>participant.name</code></td>
<td>Да, через «Редактировать»</td>
</tr>
<tr>
<td>Email</td>
<td><code>participant.email</code></td>
<td>Да, через «Редактировать»</td>
</tr>
<tr>
<td>Телефон</td>
<td><code>participant.phone</code></td>
<td>Да, через «Редактировать»</td>
</tr>
<tr>
<td>Дата записи</td>
<td><code>participant.created_at</code>, формат <code>YYYY-MM-DD HH:mm</code>, часовой пояс браузера</td>
<td>Нет</td>
</tr>
<tr>
<td>Статус</td>
<td><code>participant.attendance_status</code></td>
<td>Да, выпадающий список прямо в строке</td>
</tr>
<tr>
<td>Комментарий</td>
<td><code>participant.comment</code></td>
<td>Да, через «Редактировать»</td>
</tr>
<tr>
<td>Действия</td>
<td>—</td>
<td>«Редактировать», «Удалить»</td>
</tr>
</tbody>
</table><p>Пустой телефон и пустой комментарий показываем как «—», а не пустую ячейку.</p>
<h3 id="crud-участников">6.3. CRUD участников</h3>
<p>Админ умеет:</p>
<ul>
<li><strong>Добавить</strong> участника вручную. Поля те же: имя, уникальный email, телефон, комментарий.</li>
<li><strong>Посмотреть</strong> список и карточку участника.</li>
<li><strong>Изменить</strong> имя, email, телефон, комментарий, статус посещения.</li>
<li><strong>Удалить</strong> участника — с подтверждением «Удалить участника?».</li>
</ul>
<p>Статус посещения:</p>
<ul>
<li><code>pending</code> — ещё не отмечен.</li>
<li><code>attended</code> — пришёл.</li>
<li><code>not_attended</code> — не пришёл.</li>
</ul>
<h3 id="экспорт">6.4. Экспорт</h3>
<p>Кнопка «Экспорт» скачивает CSV.</p>
<p>Колонки: имя, email, телефон, дата записи, статус посещения, комментарий.</p>
<p>Что учесть:</p>
<ul>
<li>UTF-8, разделитель <code>;</code>.</li>
<li>Имя файла: <code>event_participants_YYYY-MM-DD.csv</code>.</li>
<li>Экспортируем всех участников текущего ивента.</li>
<li>Дата записи в CSV — UTC с суффиксом <code>Z</code>.</li>
</ul>
<h2 id="данные">7. Данные</h2>
<h3 id="сущности">7.1. Сущности</h3>
<p>Храним в реляционной БД. Все <code>datetime</code> — в UTC. Идентификаторы — <code>uuid</code>.</p>
<p><strong>Event</strong></p>

<table>
<thead>
<tr>
<th>Поле</th>
<th>Тип</th>
<th>NULL</th>
<th>Ограничения</th>
</tr>
</thead>
<tbody>
<tr>
<td>id</td>
<td>uuid</td>
<td>Нет</td>
<td>PK</td>
</tr>
<tr>
<td>title</td>
<td>string</td>
<td>Нет</td>
<td>1–200 символов</td>
</tr>
<tr>
<td>description</td>
<td>text</td>
<td>Нет</td>
<td>—</td>
</tr>
<tr>
<td>starts_at</td>
<td>datetime</td>
<td>Нет</td>
<td>UTC</td>
</tr>
<tr>
<td>location</td>
<td>string</td>
<td>Нет</td>
<td>1–300 символов</td>
</tr>
<tr>
<td>registration_open</td>
<td>boolean</td>
<td>Нет</td>
<td>Default <code>true</code>. Меняется вручную, автозакрытие по вместимости — P1</td>
</tr>
<tr>
<td>capacity</td>
<td>integer</td>
<td>Да</td>
<td>P1, в MVP не используется. Если <code>NULL</code> — без ограничения по числу участников</td>
</tr>
<tr>
<td>created_at</td>
<td>datetime</td>
<td>Нет</td>
<td>Default <code>now()</code>, UTC</td>
</tr>
</tbody>
</table><p><strong>Participant</strong></p>

<table>
<thead>
<tr>
<th>Поле</th>
<th>Тип</th>
<th>NULL</th>
<th>Ограничения</th>
</tr>
</thead>
<tbody>
<tr>
<td>id</td>
<td>uuid</td>
<td>Нет</td>
<td>PK</td>
</tr>
<tr>
<td>event_id</td>
<td>uuid</td>
<td>Нет</td>
<td>FK → <code>event.id</code>, <code>ON DELETE CASCADE</code></td>
</tr>
<tr>
<td>name</td>
<td>string</td>
<td>Нет</td>
<td>1–100 символов</td>
</tr>
<tr>
<td>email</td>
<td>string</td>
<td>Нет</td>
<td>Валидный email; UNIQUE(<code>event_id</code>, <code>email</code>)</td>
</tr>
<tr>
<td>phone</td>
<td>string</td>
<td>Да</td>
<td>—</td>
</tr>
<tr>
<td>comment</td>
<td>text</td>
<td>Да</td>
<td>До 500 символов</td>
</tr>
<tr>
<td>attendance_status</td>
<td>enum</td>
<td>Нет</td>
<td><code>pending</code> / <code>attended</code> / <code>not_attended</code>; default <code>pending</code></td>
</tr>
<tr>
<td>unsubscribe_token</td>
<td>string</td>
<td>Да</td>
<td>P2, в MVP не используется. UNIQUE, если не <code>NULL</code></td>
</tr>
<tr>
<td>created_at</td>
<td>datetime</td>
<td>Нет</td>
<td>Default <code>now()</code>, UTC</td>
</tr>
<tr>
<td>updated_at</td>
<td>datetime</td>
<td>Нет</td>
<td>Default <code>now()</code>, обновляется при PATCH</td>
</tr>
</tbody>
</table><p><strong>Admin</strong></p>

<table>
<thead>
<tr>
<th>Поле</th>
<th>Тип</th>
<th>NULL</th>
<th>Ограничения</th>
</tr>
</thead>
<tbody>
<tr>
<td>id</td>
<td>uuid</td>
<td>Нет</td>
<td>PK</td>
</tr>
<tr>
<td>login</td>
<td>string</td>
<td>Нет</td>
<td>UNIQUE</td>
</tr>
<tr>
<td>password_hash</td>
<td>string</td>
<td>Нет</td>
<td>bcrypt или argon2</td>
</tr>
</tbody>
</table><h3 id="ограничения">7.2. Ограничения</h3>
<ul>
<li>Email уникален в рамках одного ивента.</li>
<li>Нельзя записаться, если <code>registration_open = false</code>.</li>
<li>Удаление участника не ломает список и экспорт.</li>
</ul>
<h2 id="маршруты-—-ориентир-для-реализации">8. Маршруты — ориентир для реализации</h2>
<p>Это не жёсткое требование, а удобная схема для кодинга. В MVP один ивент, <code>:id</code> — его идентификатор, добавление нескольких ивентов не потребует переделки маршрутов.</p>

<table>
<thead>
<tr>
<th>Метод</th>
<th>Маршрут</th>
<th>Тело запроса</th>
<th>Доступ</th>
</tr>
</thead>
<tbody>
<tr>
<td>GET</td>
<td><code>/event/:id</code></td>
<td>—</td>
<td>Все</td>
</tr>
<tr>
<td>POST</td>
<td><code>/api/events/:id/register</code></td>
<td><code>{ name, email, phone?, comment? }</code></td>
<td>Все</td>
</tr>
<tr>
<td>POST</td>
<td><code>/api/unsubscribe</code></td>
<td><code>{ token }</code> — P2, в MVP нет</td>
<td>Все</td>
</tr>
<tr>
<td>POST</td>
<td><code>/api/admin/login</code></td>
<td><code>{ login, password }</code></td>
<td>Все</td>
</tr>
<tr>
<td>POST</td>
<td><code>/api/admin/logout</code></td>
<td>—</td>
<td>Админ</td>
</tr>
<tr>
<td>GET</td>
<td><code>/api/admin/events/:id/participants</code></td>
<td>—</td>
<td>Админ</td>
</tr>
<tr>
<td>POST</td>
<td><code>/api/admin/events/:id/participants</code></td>
<td><code>{ name, email, phone?, comment? }</code></td>
<td>Админ</td>
</tr>
<tr>
<td>PATCH</td>
<td><code>/api/admin/participants/:id</code></td>
<td><code>{ name?, email?, phone?, comment? }</code></td>
<td>Админ</td>
</tr>
<tr>
<td>DELETE</td>
<td><code>/api/admin/participants/:id</code></td>
<td>—</td>
<td>Админ</td>
</tr>
<tr>
<td>PATCH</td>
<td><code>/api/admin/participants/:id/attendance</code></td>
<td><code>{ attendance_status }</code></td>
<td>Админ</td>
</tr>
<tr>
<td>GET</td>
<td><code>/api/admin/events/:id/participants/export</code></td>
<td>—</td>
<td>Админ</td>
</tr>
</tbody>
</table><h2 id="нефункциональные-требования">9. Нефункциональные требования</h2>
<ul>
<li>Вёрстка адаптивная: страница ивента и админка нормально работают на телефоне и ноутбуке.</li>
<li>Валидация — и на клиенте, и на сервере.</li>
<li>Пароль админа не храним открытым текстом.</li>
<li>Админские маршруты защищены авторизацией.</li>
<li>Ошибки показываем человеческим языком, без стектрейсов.</li>
<li>Ошибки на сервере логируем.</li>
<li>Время храним в UTC, отображаем в часовом поясе браузера, в CSV — UTC с суффиксом <code>Z</code>.</li>
<li>CSV корректно открывается в Excel и Google Sheets.</li>
<li>MVP рассчитан на небольшой ивент — требований к высокой нагрузке нет.</li>
</ul>
<h2 id="как-проверяем">10. Как проверяем</h2>
<h3 id="definition-of-done">10.1. Definition of Done</h3>
<p>MVP готов, когда всё что ниже — правда:</p>
<ul>
<li class="task-list-item"><input type="checkbox" class="task-list-item-checkbox" disabled=""> Посетитель открыл <code>/event/:id</code> → увидел название, дату, место, описание и кнопку «Записаться».</li>
<li class="task-list-item"><input type="checkbox" class="task-list-item-checkbox" disabled=""> Посетитель отправил форму с валидными данными → строка появилась в БД, в ответ пришло подтверждение.</li>
<li class="task-list-item"><input type="checkbox" class="task-list-item-checkbox" disabled=""> Повторная запись с тем же email на этот ивент не создаёт дубль и показывает понятное сообщение.</li>
<li class="task-list-item"><input type="checkbox" class="task-list-item-checkbox" disabled=""> Без входа любой админский URL редиректит на логин.</li>
<li class="task-list-item"><input type="checkbox" class="task-list-item-checkbox" disabled=""> Админ вошёл → увидел список участников: может добавить, изменить, удалить запись и поставить статус посещения.</li>
<li class="task-list-item"><input type="checkbox" class="task-list-item-checkbox" disabled=""> Админ нажал «Экспорт» → скачался CSV, который открывается в Excel и Google Sheets без ошибок.</li>
<li class="task-list-item"><input type="checkbox" class="task-list-item-checkbox" disabled=""> Пройдены все сценарии из 10.2.</li>
<li class="task-list-item"><input type="checkbox" class="task-list-item-checkbox" disabled=""> Есть короткая инструкция для админа: вход, добавление участника, отметка посещения, выгрузка CSV.</li>
</ul>
<h3 id="тестовые-сценарии">10.2. Тестовые сценарии</h3>
<p><strong>Публичная страница</strong></p>
<ol>
<li>Открыть <code>/event/:id</code> → видны название, дата, место, описание, кнопка «Записаться».</li>
<li><code>registration_open = false</code> → кнопка неактивна, рядом текст «Запись закрыта».</li>
</ol>
<p><strong>Запись</strong></p>
<ol start="3">
<li>Отправить валидную форму → подтверждение «Вы записаны на ивент», строка в <code>participant</code>.</li>
<li>Отправить форму без имени → ошибка валидации, запись не создаётся.</li>
<li>Отправить форму с <code>email = "abc"</code> → ошибка «Введите корректный email».</li>
<li>Отправить форму с email, который уже есть у этого ивента → «Похоже, вы уже записались на этот ивент», новой строки нет.</li>
<li>Отправить форму при <code>registration_open = false</code> (через прямой POST, минуя UI) → 4xx, запись не создаётся.</li>
</ol>
<p><strong>Админка</strong></p>
<ol start="8">
<li>Открыть <code>/admin</code> без входа → редирект на логин.</li>
<li>Ввести неверный пароль → ошибка, доступа нет.</li>
<li>Войти с верным паролем → список участников текущего ивента.</li>
<li>Добавить участника вручную → появляется в списке.</li>
<li>Изменить имя участника → изменение видно в списке и сохраняется после перезагрузки.</li>
<li>Удалить участника → подтверждение, после — его нет в списке и в БД.</li>
<li>Поставить статус «Пришёл» → виден в списке, сохраняется после перезагрузки.</li>
</ol>
<p><strong>Экспорт</strong></p>
<ol start="15">
<li>Нажать «Экспорт» → скачивается CSV, имя файла <code>event_participants_YYYY-MM-DD.csv</code>.</li>
<li>Открыть CSV в Excel и Google Sheets → колонки на месте, кириллица не бьётся, разделитель корректный.</li>
<li>Данные в CSV совпадают с текущим списком в админке.</li>
</ol>

