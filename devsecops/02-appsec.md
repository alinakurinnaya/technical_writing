---


---

<h1 id="безопасность-приложений-appsec">Безопасность приложений (AppSec)</h1>
<p>В этой главе - практики и инструменты для поиска уязвимостей на этапе разработки.</p>
<h2 id="термины-в-этом-файле">Термины в этом файле</h2>
<ul>
<li><a href="#sast-static-application-security-testing">SAST</a></li>
<li><a href="#dast-dynamic-application-security-testing">DAST</a></li>
<li><a href="#iast-interactive-application-security-testing">IAST</a></li>
<li><a href="#sca-software-composition-analysis">SCA</a></li>
<li><a href="#secrets-detection">Secrets Detection</a></li>
</ul>
<hr>
<h2 id="sast-static-application-security-testing">SAST (Static Application Security Testing)</h2>
<p><strong>Определение.</strong> Метод автоматического анализа исходного кода, байт-кода или бинарных файлов приложения <strong>без его запуска</strong> на предмет наличия уязвимостей. Также известен как <strong>статический анализ кода</strong>.</p>
<p><strong>Почему это важно.</strong> SAST работает на самых ранних этапах, ещё в среде разработки или на этапе проверки изменений, что позволяет находить уязвимости до того, как код попадёт в основную ветку. Это снижает стоимость исправления и даёт разработчику мгновенную обратную связь. Инструменты SAST легко интегрируются в конвейер сборки.</p>
<p><strong>Пример.</strong> Инструмент вроде SonarQube или Semgrep находит в коде конкатенацию пользовательского ввода в SQL-запрос, что является классической инъекцией. Разработчик видит проблему сразу при коммите, а не через недели после релиза.</p>
<p><strong>Связанные термины.</strong> <a href="#dast-dynamic-application-security-testing">DAST</a> · <a href="#sca-software-composition-analysis">SCA</a></p>
<p><strong>Источники.</strong> OWASP DevSecOps Guideline · OWASP Source Code Analysis Tools</p>
<h2 id="dast-dynamic-application-security-testing">DAST (Dynamic Application Security Testing)</h2>
<p><strong>Определение.</strong> Метод тестирования безопасности <strong>работающего приложения</strong> с позиции внешнего атакующего («чёрный ящик»). DAST-инструменты отправляют вредоносные полезные нагрузки и анализируют ответы приложения, не имея доступа к исходному коду.</p>
<p><strong>Почему это важно.</strong> DAST выявляет уязвимости, которые проявляются только во время выполнения: ошибки валидации ввода, проблемы аутентификации, ошибки конфигурации сервера. Этот метод особенно полезен для проверки API и веб-приложений в среде, близкой к рабочей.</p>
<p><strong>Пример.</strong> Инструмент OWASP ZAP имитирует атаки на запущенное веб-приложение, отправляя специально сформированные HTTP-запросы, чтобы обнаружить SQL-инъекции или межсайтовый скриптинг (XSS). DAST видит реальное поведение системы, но не показывает, в какой строке кода кроется причина.</p>
<p><strong>Связанные термины.</strong> <a href="#sast-static-application-security-testing">SAST</a> · <a href="#iast-interactive-application-security-testing">IAST</a></p>
<p><strong>Источники.</strong> OWASP DevSecOps Guideline · OWASP ZAP</p>
<h2 id="iast-interactive-application-security-testing">IAST (Interactive Application Security Testing)</h2>
<p><strong>Определение.</strong> Гибридный метод тестирования безопасности, который анализирует приложение <strong>изнутри во время выполнения</strong>. В код внедряется агент (сенсорный модуль), который отслеживает поведение приложения, поток данных и управляющие связи в реальном времени, пока приложение выполняет тесты или с ним взаимодействует пользователь.</p>
<p><strong>Почему это важно.</strong> IAST сочетает преимущества SAST и DAST: он видит конкретные строки кода (как SAST), но только для тех путей, которые реально выполнялись (как DAST). Это даёт меньше ложных срабатываний и позволяет находить уязвимости в режиме реального времени прямо в среде разработки.</p>
<p><strong>Пример.</strong> При запуске автоматических тестов агент IAST отслеживает, как пользовательские данные проходят через приложение, и обнаруживает, что API-ключ хранится в открытом виде в коде, или что данные передаются без SSL/TLC-шифрования. Результат появляется мгновенно, без необходимости ждать завершения сканирования.</p>
<p><strong>Связанные термины.</strong> <a href="#sast-static-application-security-testing">SAST</a> · <a href="#dast-dynamic-application-security-testing">DAST</a></p>
<p><strong>Источники.</strong> OWASP DevSecOps Guideline · OWASP IAST</p>
<h2 id="sca-software-composition-analysis">SCA (Software Composition Analysis)</h2>
<p><strong>Определение.</strong> Процесс автоматического обнаружения, каталогизации и анализа всех <strong>сторонних и open-source компонентов</strong> (библиотек, зависимостей), используемых в приложении. SCA-инструменты проверяют эти компоненты на наличие известных уязвимостей, а также отслеживают лицензионные риски.</p>
<p><strong>Почему это важно.</strong> Значительная часть современного кода - это open-source библиотеки. Уязвимость в одной транзитивной зависимости может скомпрометировать всё приложение. SCA позволяет выявлять такие риски до того, как они попадут в рабочую среду.</p>
<p><strong>Пример.</strong> Уязвимость Log4Shell (CVE-2021-44228) жила в библиотеке Log4j, которую большинство разработчиков подключали транзитивно, через другие фреймворки. SAST не увидел бы эту проблему в исходном коде приложения, но SCA обнаружил бы её, просканировав манифест зависимостей.</p>
<p><strong>Связанные термины.</strong> <a href="#sbom-software-bill-of-materials">SBOM</a> · <a href="#sast-static-application-security-testing">SAST</a></p>
<p><strong>Источники.</strong> OWASP Software Composition Analysis · OWASP DevSecOps Guideline</p>
<h2 id="secrets-detection">Secrets Detection</h2>
<p><strong>Определение.</strong> Практика автоматического поиска API-ключей, паролей, токенов, приватных ключей и строк подключения в исходном коде, конфигурационных файлах и истории коммитов. Инструменты Secrets Detection ищут как структурированные форматы (например, ключи AWS или GitHub), так и строки с высокой энтропией, которые могут оказаться случайно сгенерированными ключами.</p>
<p><strong>Почему это важно.</strong> Секрет, попавший в репозиторий, считается скомпрометированным навсегда: он остаётся в истории коммитов, даже если файл удалён. Злоумышленники сканируют публичные репозитории автоматически. Поэтому Secrets Detection встраивают в проверки перед коммитом (pre-commit hooks), CI/CD и сканирование истории, чтобы перехватить утечку до того, как она станет публичной.</p>
<p><strong>Пример.</strong> Разработчик случайно коммитит файл <code>.env</code> с ключом <code>AWS_SECRET_ACCESS_KEY</code>. Инструмент вроде Gitleaks или TruffleHog перехватывает коммит локально (pre-commit hook) и блокирует его, не давая секрету попасть в удалённый репозиторий. Если утечка уже произошла, инструмент помечает секрет как «требующий отзыва», и его необходимо немедленно инвалидировать.</p>
<p><strong>Связанные термины.</strong> <a href="#sast-static-application-security-testing">SAST</a> · <a href="#sca-software-composition-analysis">SCA</a></p>
<p><strong>Источники.</strong> OWASP DevSecOps Guideline (Secrets Management) · GitLab Secret Detection · Gitleaks, TruffleHog, detect-secrets</p>

