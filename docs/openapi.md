---
title:       Access-токен потрібно періодично оновлювати, оскільки він має обмежений строк дії.
language_tabs:
toc_footers: []
includes: []
search: true
highlight_theme: darkula
---






    # **Вступ**
      API від Київстар Відкритий Телеком (КВТ) дозволяє використовувати можливості Київстар для розробки нових продуктів, створення додатків та автоматизації бізнес-процесів з мінімальним показником Time to First Successful API Call.
      У цій API-документації є вся необхідна інформація, щоб зробити інтеграцію з продуктами КВТ (від опису ендпойнтів до можливих відповідей та помилок).
      Вона постійно оновлюється та завжди є актуальною. Також документація є інтерактивною — можна зробити запит прямо з неї та отримати відповідь із сервера.
    # **Автентифікація**
    ## **Client ID, Client Secret і access-токен**
      Client ID та Client Secret — це ідентифікатор та пароль до кожного з ваших додатків. З ними ви зможете згенерувати access-токен, який дозволяє звертатися до серверів платформи КВТ.
      Access-токен потрібно періодично оновлювати, оскільки він має обмежений строк дії.

> openapi: 3.1.0

> info:

>   title: Київстар Відкритий Телеком API

>   version: 'Closed Beta'

>   description: |-

      **Зверніть увагу**. Одразу після реєстрації, вам буде доступний лише моковий додаток. Його Client ID та Client Secret підходитимуть лише до мокового сервера.
      Додаток, що дозволить звертатися до базового серверу, з'явиться в вашому особистому кабінеті, якщо у вас буде «ранній доступ».
      Заявку на такий доступ можна подати в <a href="https://api-market.kyivstar.ua" target="_blank">особистому кабінеті</a>.
    ## **Як отримати access-токен**
      Перш ніж робити запити до платформи КВТ, зареєструйтеся в <a href="https://api-market.kyivstar.ua" target="_blank">особистому кабінеті</a>.
      Після реєстрації отримайте Client ID та Client Secret в особистому кабінеті на сторінці відповідного додатка. Їх можна використати для запиту access-токена. Якщо запит успішний, КВТ поверне access-токен (дійсний протягом 8 годин).
      Отримавши access-токен, необхідно його передавати в Authorization Header з кожним API запитом. Таким чином ви робите безпечну та авторизовану транзакцію.
      <br>
      ![процес автентифікації](/api/public/auth_kot_get_token.png)
      <br>
      <br>
      <details>
      <summary>Отримати токен у терміналі (curl)</summary>
      ```bash
      curl 'https://<server_url>/idp/oauth2/token' -X POST -d 'grant_type=client_credentials' --user <your_client_id>:<your_client_secret>
      ```
      </details>
      <br>
      <details>
      <summary>Отримати токен за допомогою Python</summary>

      ```python
      import requests
      from base64 import b64encode
      import json
      # SET HERE YOUR url, clientId and secret
      authServerTokenUrl = "https://<server_url>/idp/oauth2/token"
      clientId = <your_client_id>
      clientSecret = <your_client_secret>
      def authenticate(authUrl, username, password):
        encoded = (username + ":" + password).encode("utf-8")
        userAndPass = b64encode(encoded).decode("ascii")
        headers = { 'Authorization' : 'Basic %s' %  userAndPass }
        payload = 'grant_type=client_credentials'
        headers = {
          'Content-Type': 'application/x-www-form-urlencoded',
          'Authorization': 'Basic %s' %  userAndPass
        }
        response = requests.request("POST", authUrl, headers=headers, data = payload)
        data = json.loads(response.text.encode('utf8'))
        return data["access_token"]
      authToken = authenticate(authServerTokenUrl, clientId, clientSecret)
      print("Auth token: " + authToken)
      ```
      </details>

    ## **Як оновити access-токен**
      Строк дії виданого access-токена закінчується через 8 годин (28799 секунд).
      Вам необхідно розробити своє програмне забезпечення таким чином, щоб ваш код міг відстежити коли строк дії access-токена минає і що його потрібно оновити.
      Це можна відстежити за допомогою поля "expires_in", що повертається разом з access-токеном.
      Також ви можете використовувати помилку з кодом 401, щоб відстежувати недійсність access-токена.
      <br>
      ![процес автентифікації](/api/public/auth_kot_renew_token.png)
      <br>
    # **Сервери**
      Окрім сервера з реальними даними, АРІ КВТ надає можливість скористатися моковим сервером.
      Щоб вибрати сервер, виберіть відповідний пункт у списку на нашій документації або вкажіть потрібний URL у коді.
      * **Базовий сервер** - повертає реальні дані. Виклики на цей сервер тарифікуються в звичайному порядку.
      * **Моковий сервер** - повертає дані, які мають структуру, ідентичну до реальної, проте не мають жодного сенсу з точки зору змісту. Виклики на цей сервер ніяк не вплинуть на стан вашого рахунку. Таким чином ви можете провести інтеграцію з продуктами КВТ на цих даних і бути впевненими, що інтеграцію на реальних даних буде налаштовано вірно. **Зверніть увагу**, що Client ID і Client Secret для мокового сервера відрізняються від тих, що використовуються для базового серверу.
      <br> Для викликів до мокового серверу, потрібно використовувати приклади, що вказані в документації.

    # **Як робити виклики з інтерактивної документації?**
    1. Виберіть сервер, до якого хочете надіслати запит.
    2. Візьміть відповідні Client ID та Client secret зі сторінки додатка в особистому кабінеті, підставте їх у блок аутентифікації та натисніть кнопку "Get Token".
    3. Оберіть продукт, підставте відповідні значення для виклику та натисніть кнопку "Try", щоб отримати відповідь.
    **Зверніть увагу**: якщо ви вибрали моковий сервер, використовуйте приклади викликів, які вказані в документації.

    # **Як користуватися пісочницею**
    1. Виберіть сервер пісочниці.
    2. Візьміть відповідні Client ID та Client secret зі сторінки додатка в особистому кабінеті, підставте їх у блок аутентифікації та натисніть кнопку "Get Token".
    3. Оберіть продукт, підставте відповідні значення для виклику та натисніть кнопку "Try", щоб отримати відповідь.
    **Зверніть увагу**: також, ви можете робити виклики за вашого ПЗ або Postman та ін. Кількість викликів обмежена, залишок можна подивитися на сторінці Пісочниці в особистому кабінеті.
servers:
  - url: '/mock/rest/{version}'
    description: URL мокового сервера (мокові дані, не тарифікується)
    x-kot-title: Mock
    variables:
      version:
        default: v1beta
        description: Api version
  - url: '/sandbox/rest/{version}'
    description: URL пісочниці (реальні дані, не тарифікується, обмежується квотами та прив'язаними номерами)
    x-kot-title: Sandbox
    variables:
      version:
        default: v1beta
        description: Api version
  - url: '/rest/{version}'
    description: URL базового сервера (реальні дані, тарифікується)
    x-kot-title: Production
    variables:
      version:
        default: v1beta
        description: Api version
tags:
  - name: Програмовані SMS
    description: Доступно на умовах передплати в [особистому кабінеті](https://api-market.kyivstar.ua)
  - name: Розсилка SMS на свій список клієнтів
    description: Розсилка SMS на свій список клієнтів
  - name: Viber Повідомлення
    description: "Розсилка Viber – це зручний та функціональний інструмент, щоб розповісти клієнтам про акції та знижки, повідомити про актуальні часи роботи чи стан замовлення, нагадати про візит. У Viber-повідомлення можна додати зображення та кнопку, що вестиме на ваш товар чи послугу.
      Щоб отримати детальні умови користування, залиште заявку в [особистому кабінеті](https://api-market.kyivstar.ua)"
  - name: Антифрод API
    description: Щоб отримати детальні умови користування, залиште заявку в [особистому кабінеті](https://api-market.kyivstar.ua)
paths:
  /subscribers/{phoneNumber}/verify-sim:
    parameters:
      - schema:
          $ref: '#/components/schemas/subscriber-phone-number'
        name: phoneNumber
        in: path
        required: true
        description: Мобільний номер у міжнародному форматі
        examples:
          example1:
            summary: "SIM активна, не замінена"
            value: '380670000200'
          example2:
            summary: "SIM активна, замінена"
            value: '380670001200'
          example3:
            summary: "Невірний формат телефонного номера"
            value: '000000400'
          example4:
            summary: "SIM неактивна"
            value: '380670000204'
          example5:
            summary: "Не абонент Київстар"
            value: '380500000404'
          example6:
            summary: "Денний ліміт запитів досягнений"
            value: '380670000429'
    post:
      x-kot-code: verify-sim_POST
      summary: Перевірка заміни SIM карти
      operationId: post-v1beta-subscriber-verify-sim
      tags: [ "Антифрод API" ]
      responses:
        '200':
          description: OK
          content:
            application/json:
              schema:
                type: object
                properties:
                  simChanged:
                    type: integer
                    description: Індикатор заміни SIM карти. 1 - так, 0 - ні.
                  isActive:
                    type: integer
                    description: Індикатор активності SIM карти. 1 - так, 0 - ні.
        '400':
          description: Bad Request
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/base-error-body'
              examples:
                missing_activationHours:
                  value:
                    errorCode: 0
                    errorMsg: "Error: 'ActivationHours' is required"
        '401':
          description: Unauthorized
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/non-authorized'
              examples:
                Unauthorized:
                  value:
                    error:
                      code: 401
                      status: Unauthorized
                      request: ad30594292f7959683a410bf1add088e
                      message: The request could not be authorized
        '404':
          description: Not Found
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/base-error-body'
              examples:
                MSISDN_NOT_BELONGS_TO_KS:
                  value:
                    errorCode: 160
                    errorMsg: NOT_FOUND # subscriber does not belong to Kyivstar
        '422':
          description: Unprocessable Entity
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/base-error-body'
              examples:
                MSISDN_INCORRECT:
                  value:
                    errorCode: 213
                    errorMsg: MSISDN_INCORRECT
        '429':
          description: Too Many Requests
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/base-error-body'
              examples:
                DAY_THRESHOLD_EXCEEDED:
                  value:
                    errorCode: 105
                    errorMsg: DAY_THRESHOLD_EXCEEDED
        '500':
          description: Internal Server Error
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/base-error-body'
        '504':
          description: Gateway Timeout
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/base-error-body'
              examples:
                TIMEOUT:
                  value:
                    errorCode: 111
                    errorMsg: TIMEOUT
      description: |
        Перевірка заміни SIM карти протягом певного проміжку часу повертає значення, чи була замінена SIM за вказаний період часу та чи є вона активною на момент запиту (1 — так, 0 — ні). Детальніше про продукт <a href="https://kyivstar.ua/uk/business/open-api/imsi" target="_blank">тут</a>.
      x-kot-description: |
        Перевірка заміни SIM карти протягом певного проміжку часу повертає значення, чи була замінена SIM за вказаний період часу та чи є вона активною на момент запиту (1 — так, 0 — ні).
      security:
        - OAuth2: []
      requestBody:
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/subscriber-verify-sim'
            examples:
              Валідний період часу:
                value:
                  activationHours: 48
              Невалідний період часу:
                value:
                  activationHours: 200
        description: Використовуйте наведені приклади для отримання можливих відповідей від мокового сервера.
      parameters:
        - schema:
            type: string
            default: application/json
            maxLength: 0
          in: header
          name: Content-Type
          description: ''
  /subscribers/{phoneNumber}/check-sim-count:
    parameters:
      - schema:
          $ref: '#/components/schemas/subscriber-phone-number'
        name: phoneNumber
        in: path
        required: true
        description: Мобільний номер у міжнародному форматі
        examples:
          example1:
            summary: "1 SIM карта"
            value: '380670000200'
          example2:
            summary: "2 SIM карти"
            value: '380670001200'
          example3:
            summary: "Невірний формат телефонного номера"
            value: '000000400'
          example4:
            summary: "Не абонент Київстар"
            value: '380670000404'
    post:
      x-kot-code: check-sim-count_POST
      summary: Перевірка кількості SIM карт протягом певного проміжку часу
      operationId: post-v1beta-subscriber-check-sim-count
      tags: [ "Антифрод API" ]
      responses:
        '200':
          description: OK
          content:
            application/json:
              schema:
                type: object
                properties:
                  simCount:
                    type: integer
                    description: Кількість SIM карт в телефоні протягом вказаного періоду.
        '400':
          description: Bad Request
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/base-error-body'
              examples:
                missing_activation:
                  value:
                    errorCode: 0
                    errorMsg: "Error: 'DaysCount' is required"
        '401':
          description: Unauthorized
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/non-authorized'
              examples:
                Unauthorized:
                  value:
                    error:
                      code: 401
                      status: Unauthorized
                      request: ad30594292f7959683a410bf1add088e
                      message: The request could not be authorized
        '404':
          description: Not Found
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/base-error-body'
              examples:
                NOT_FOUND:
                  value:
                    errorCode: 160
                    errorMsg: NOT_FOUND
        '422':
          description: Unprocessable Entity
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/base-error-body'
              examples:
                MSISDN_INCORRECT:
                  value:
                    errorCode: 100
                    errorMsg: MSISDN_INCORRECT
        '429':
          description: Too Many Requests
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/base-error-body'
              examples:
                DAY_THRESHOLD_EXCEEDED:
                  value:
                    errorCode: 105
                    errorMsg: DAY_THRESHOLD_EXCEEDED
        '500':
          description: Internal Server Error
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/base-error-body'
        '504':
          description: Gateway Timeout
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/base-error-body'
              examples:
                TIMEOUT:
                  value:
                    errorCode: 111
                    errorMsg: TIMEOUT
      description: |
        Перевірка кількості SIM карт протягом певного проміжку часу. Повертає значення кількості SIM карт за номером телефону абонента за вказаний період часу (1-120 днів). Мінімальне значення - 1, максимальне значення - 10 (в абонента було 10 або більше SIM карт за період).
      security:
        - OAuth2: [ ]
      requestBody:
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/subscriber-check-sim-count'
            examples:
              Валідний період:
                value:
                  daysCount: 5
              Невалідний період:
                value:
                  daysCount: 200
        description: Використовуйте наведені приклади для отримання можливих відповідей від мокового сервера.
      parameters:
        - schema:
            type: string
            default: application/json
            maxLength: 0
          in: header
          name: Content-Type
          description: ''
  '/subscribers/{phoneNumber}/device-check':
    parameters:
      - schema:
          $ref: '#/components/schemas/subscriber-phone-number'
        name: phoneNumber
        in: path
        required: true
        description: Мобільний номер у міжнародному форматі
        examples:
          example1:
            summary: Активний номер
            value: 38(067)00-00-200
          example2:
            summary: Невірний формат телефонного номера
            value: '000000400'
      - name: IMEI
        schema:
          type: string
        in: query
        required: false
        description: Ідентифікатор IMEI
        examples:
          example1:
            summary: Повне співпадіння
            value: "033333333333333"
          example2:
            summary: Співпадіння з іншим пристроєм абонента
            value: "011111111111111"
          example3:
            summary: Неспівпадіння
            value: "055555555555555"
          example4:
            summary: Некоректні параметри
            value: '000000400'
      - name: daysPeriod
        schema:
          type: integer
        in: query
        required: false
        description: Період активності пристрою (в днях). Мінімальне значення - 1, максимальне - 120.
        examples:
          example1:
            summary: Пристрій активний 72 доби
            value: 72
          example2:
            summary: Некоректний параметр daysPeriod
            value: '1000000'
    get:
      x-kot-code: device-check_GET
      summary: 'Верифікація IMEI ідентифікатора пристрою користувача'
      operationId: get-v1beta-subscriber-device-check
      tags: [ "Антифрод API" ]
      responses:
        '200':
          description: OK
          content:
            application/json:
              schema:
                type: object
                properties:
                  description:
                    type: string
                  matchCode:
                    type: string

        '400':
          description: Bad Request
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/base-error-body'
              examples:
                missing_activation:
                  value:
                    errorCode: 0
                    errorMsg: "Malformed body"
        '401':
          description: Unauthorized
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/non-authorized'
              examples:
                Unauthorized:
                  value:
                    error:
                      code: 401
                      status: Unauthorized
                      request: ad30594292f7959683a410bf1add088e
                      message: The request could not be authorized
        '404':
          description: Not Found
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/base-error-body'
              examples:
                NOT_FOUND:
                  value:
                    errorCode: 160
                    errorMsg: NOT_FOUND
        '422':
          description: Unprocessable Entity
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/base-error-body'
              examples:
                MSISDN_INCORRECT:
                  value:
                    errorCode: 100
                    errorMsg: MSISDN_INCORRECT
        '500':
          description: Internal Server Error
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/base-error-body'
      description: |
        Верифікація IMEI ідентифікатора пристрою користувача.
        Використовуйте один будь-який query-string параметр (IMEI або daysPeriod) для запиту.
      security:
        - OAuth2: [ ]
      parameters:
        - schema:
            type: string
            default: application/json
            maxLength: 0
          example: "application/json"
          in: header
          name: Content-Type
          description: ''
  '/subscribers/{phoneNumber}/lifetime-check':
    parameters:
      - schema:
          $ref: '#/components/schemas/subscriber-phone-number'
        name: phoneNumber
        in: path
        required: true
        description: Мобільний номер у міжнародному форматі
        examples:
          example1:
            summary: Активний номер
            value: 38(067)00-00-200
          example2:
            summary: Невірний формат телефонного номера
            value: '000000400'
    post:
      x-kot-code: lifetime-check_POST
      summary: 'Перевірка активації\зміни SIM-карти абонента'
      operationId: post-v1beta-subscriber-lifetime-check-info
      tags: [ "Антифрод API" ]
      responses:
        '200':
          description: OK
          content:
            application/json:
              schema:
                type: object
                properties:
                  lastMonthActivated:
                    type: boolean
                  description:
                    type: string
        '400':
          description: Bad Request
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/base-error-body'
              examples:
                missing_activation:
                  value:
                    errorCode: 0
                    errorMsg: "Malformed body"
        '401':
          description: Unauthorized
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/non-authorized'
              examples:
                Unauthorized:
                  value:
                    error:
                      code: 401
                      status: Unauthorized
                      request: ad30594292f7959683a410bf1add088e
                      message: The request could not be authorized
        '404':
          description: Not Found
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/base-error-body'
              examples:
                NOT_FOUND:
                  value:
                    errorCode: 160
                    errorMsg: NOT_FOUND
        '422':
          description: Unprocessable Entity
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/base-error-body'
              examples:
                MSISDN_INCORRECT:
                  value:
                    errorCode: 100
                    errorMsg: MSISDN_INCORRECT
        '500':
          description: Internal Server Error
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/base-error-body'
      description: |
        Запит на перевірку часу активації\зміни SIM-карти за останні 30 днів.
      security:
        - OAuth2: [ ]
      parameters:
        - schema:
            type: string
            default: application/json
            maxLength: 0
          in: header
          name: Content-Type
          description: ''
  /sms:
    post:
      x-kot-code: sms_POST
      summary: Відправка SMS
      operationId: post-sms
      tags: [ "Програмовані SMS" ]
      responses:
        '200':
          description: Виклик повертає message ID для перевірки статуса доставки SMS.
          headers:
            X-Reserved-Tarification-Units:
              schema:
                type: number
                description: Кількість SMS сегментів зарезервованих для даного запиту
          content:
            application/json:
              schema:
                type: object
                properties:
                  msgId:
                    type: string
                  reservedSmsSegments:
                    type: number
                    description: Кількість SMS сегментів зарезервованих для даного запиту
        '401':
          description: Unauthorized
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/non-authorized'
              examples:
                Unauthorized:
                  value:
                    error:
                      code: 401
                      status: Unauthorized
                      request: ad30594292f7959683a410bf1add088e
                      message: The request could not be authorized
        '422':
          description: Unprocessable Entity
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/base-error-body'
      description: Запит на відправку програмованого SMS. Ініціює надсилання SMS та повертає ідентифікатор повідомлення, яким можна скористатися, щоб отримати статус доставки.
        [Детальніше](https://kyivstar.ua/uk/business/open-api/transactional-sms)

        <br> <br> <div style="background-color:var(--light-blue); border-radius:4px; padding:16px; color:var(--fg); line-height:24px;"> **Зверніть увагу!** <br> Щоб надіслати цей запит, вам потрібно отримати альфа-імʼя. Для цього пройдіть верифікацію та подайте заявку в <a href="https://api-market.kyivstar.ua" target="_blank">особистому кабінеті</a> в налаштуваннях API-продукту. Термін погодження — **до 2 тижнів**.</div>
      x-kot-description: Запит на відправку програмованого SMS. Ініціює надсилання SMS та повертає ідентифікатор повідомлення, яким можна скористатися, щоб отримати статус доставки.
      x-badges:
        - color: primary-color # <<< Allowed values (red, green, orange, blue, primary-color)
          label: "Потрібне альфа-імʼя"
      security:
        - OAuth2: []
      requestBody:
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/sms'
            examples:
              Успішний запит:
                value:
                  from: 'messagedesk'
                  to: '38 (067) 000-02-00'
                  text: 'Hello World!'
              Повідомлення з обмеженим часом життя повідомлення:
                value:
                  from: 'messagedesk'
                  to: '38 (067) 000-02-00'
                  text: 'Hello World!'
                  messageTtlSec: 3600
              Заборонений номер отримувача:
                value:
                  from: 'messagedesk'
                  to: '38 (050) 001-01-01'
                  text: 'Hello World!'
              Невірний формат номера отримувача:
                value:
                  from: 'messagedesk'
                  to: '000000400'
                  text: 'Hello World!'
              Запит з обмеженням розміру повідомлення:
                value:
                  from: 'messagedesk'
                  to: '38 (067) 000-02-00'
                  text: 'Це повідомлення великого розміру, що потребує два (2) сегменти для відправки повідомлення'
                  maxSegments: 1
        description: Message parameters
    parameters: []
  '/sms/{msgId}':
    get:
      x-kot-code: sms_GET
      summary: Статус доставки
      operationId: get-sms
      tags: ["Програмовані SMS"]
      responses:
        '200':
          description: Статус доставки повідомлення у форматі RFC3339
          content:
            application/json:
              schema:
                type: object
                properties:
                  msgId:
                    type: string
                  status:
                    type: string
                  date:
                    type: string
        '401':
          description: Unauthorized
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/non-authorized'
      description: Виклик повертає статус доставки SMS за message ID, який є у відповіді на запит відправки повідомлення.
      security:
        - OAuth2: []
    parameters:
      - schema:
          type: string
        name: msgId
        in: path
        required: true
        examples:
          example1:
            summary: "Існуючий ідентифікатор повідомлення"
            value: '20200000-0000-0000-0000-380670000200'
          example2:
            summary: "Помилковий ідентифікатор повідомлення"
            value: '20200000-0000-0000-0000-380670000404'
  '/subscribers/{phoneNumber}/score':
    parameters:
      - schema:
          $ref: '#/components/schemas/subscriber-phone-number'
        name: phoneNumber
        in: path
        required: true
        description: Мобільний номер у міжнародному форматі
        examples:
          example1:
            summary: "Скор бал доступний"
            value: '380670000200'
          example2:
            summary: "Скор бал недоступний"
            value: '380670700200'
          example3:
            summary: "Невірний формат телефонного номера"
            value: '000000400'
          example4:
            summary: "Денний ліміт запитів досягнений"
            value: '380670000429'
    get:
      x-kot-code: score_GET
      summary: Фінансовий скоринг абонента
      tags: [ "Антифрод API" ]
      responses:
        '200':
          description: OK
          content:
            application/json:
              schema:
                type: object
                properties:
                  score:
                    type: integer
              examples: {}
        '401':
          description: Unauthorized
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/non-authorized'
        '404':
          description: Скор бал недоступний
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/base-error-body'
              examples:
                SCORE_IS_NOT_AVAILABLE:
                  value:
                    errorCode: 700
                    errorMsg: Score is not available
        '429':
          description: Too Many Requests
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/base-error-body'
              examples:
                DAY_THRESHOLD_EXCEEDED:
                  value:
                    errorCode: 103
                    errorMsg: DAY_THRESHOLD_EXCEEDED
        '422':
          description: Incorrect parameters
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/base-error-body'
      operationId: get-subscriber-subscriber-score
      description: Даний API повертає фінансовий скоринговий бал абонента Київстар за певною предиктивною моделлю. Щоб отримати предиктивну модель, котра якомога краще підійде вашій компанії, зверніться до support-api-market@kyivstar.net та подайте запит на розробку предиктивної моделі. [Детальніше](https://kyivstar.ua/uk/business/open-api/financial-scoring)
      x-kot-description: Даний API повертає фінансовий скоринговий бал абонента Київстар за певною предиктивною моделлю. Щоб отримати предиктивну модель, котра якомога краще підійде вашій компанії, зверніться до support-api-market@kyivstar.net та подайте запит на розробку предиктивної моделі.
      x-badges: # <<< Extension
        - color: primary-color  # <<< Allowed values (red, green, orange, blue, primary-color)
          label: 'Потрібна скорингова модель'
      security:
        - OAuth2: []
      parameters:
        - schema:
            type: string
            required: true
          in: query
          name: modelId
          examples:
            example1:
              summary: "Модель загального користування"
              value: '7'
            example2:
              summary: "Попередньо замовлена модель"
              value: '3'
            example3:
              summary: "Недоступна модель скорингу"
              value: '13'
          description: Номер предиктивної моделі скорингу. Модель з номером 7 є попередньо налаштованою моделлю загального користування
  /viber/transaction:
    post:
      x-kot-code: viber-transaction_POST
      summary: Транзакційне повідомлення
      operationId: post-viber-transaction
      tags: [ "Viber Повідомлення" ]
      responses:
        '200':
          description: Повертає message ID для перевірки статуса доставки Viber повідомлення.
          content:
            application/json:
              schema:
                type: object
                properties:
                  mid:
                    type: string
        '401':
          description: Unauthorized
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/non-authorized'
              examples:
                Unauthorized:
                  value:
                    error:
                      code: 401
                      status: Unauthorized
                      request: ad30594292f7959683a410bf1add088e
                      message: The request could not be authorized
        '422':
          description: Unprocessable Entity
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/base-error-body'
      description: |
        Запит на відправку транзакційного (сервісного) повідомлення Viber. Ініціює надсилання повідомлення Viber та повертає ідентифікатор повідомлення, яким можна скористатися, щоб отримати статус доставки.
        Транзакційними (сервісними) вважаються такі повідомлення Viber, які відповідають узгодженому шаблону.
        У випадку розбіжності із узгодженим шаблоном повідомлення тарифікується як рекламне.
        [Детальніше](https://kyivstar.ua/business/products/viber-own-data#sms)
        <br> <br> <div style="background-color:var(--light-blue); border-radius:4px; padding:16px; color:var(--fg); line-height:24px;"> **Зверніть увагу!** <br> Щоб надіслати цей запит, вам потрібно отримати Viber альфа-імʼя. Для цього пройдіть верифікацію та подайте заявку в <a href="https://api-market.kyivstar.ua" target="_blank">особистому кабінеті</a> в налаштуваннях API-продукту. Термін погодження — **до 2 тижнів**.</div>
      x-kot-description: |
        Запит на відправку транзакційного (сервісного) повідомлення Viber. Ініціює надсилання повідомлення Viber та повертає ідентифікатор повідомлення, яким можна скористатися, щоб отримати статус доставки.
        Транзакційними (сервісними) вважаються такі повідомлення Viber, які відповідають узгодженому шаблону.
        У випадку розбіжності із узгодженим шаблоном повідомлення тарифікується як рекламне.
      x-badges: # <<< Extension
        - color: primary-color  # <<< Allowed values (red, green, orange, blue, primary-color)
          label: 'Потрібне альфа-імʼя'
      security:
        - OAuth2: [ ]
      requestBody:
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/viber-transaction'
            examples:
              Успішний запит:
                value:
                  destination: '380670000202'
                  contentExtended:
                    txt: 'Hello World!'
                  ttl: 7000
              Успішний запит (tag):
                value:
                  destination: '380670000202'
                  contentExtended:
                    txt: 'Hello World!'
                    tag: 'AnyOptionalTag'
              Невірний формат номера отримувача:
                value:
                  destination: '000000400'
                  contentExtended:
                    txt: 'Hello World!'
                    tag: 'tag'
        description: Message parameters
    parameters: [ ]
  /viber/promotion:
    post:
      x-kot-code: viber-promotion_POST
      summary: Рекламне повідомлення
      operationId: post-viber-promo
      tags: [ "Viber Повідомлення" ]
      responses:
        '200':
          description: Повертає message ID для перевірки статуса доставки Viber повідомлення.
          content:
            application/json:
              schema:
                type: object
                properties:
                  mid:
                    type: string
        '401':
          description: Unauthorized
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/non-authorized'
              examples:
                Unauthorized:
                  value:
                    error:
                      code: 401
                      status: Unauthorized
                      request: ad30594292f7959683a410bf1add088e
                      message: The request could not be authorized
        '422':
          description: Unprocessable Entity
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/base-error-body'
      description: |
        Запит на відправку рекламного повідомлення Viber. Ініціює надсилання повідомлення Viber та повертає ідентифікатор повідомлення, яким можна скористатися, щоб отримати статус доставки.
        Рекламні повідомлення можуть містити текст, картинку, кнопку-посилання.
        [Детальніше про продукт, альфа-імена та шаблони Viber](https://kyivstar.ua/business/products/viber-own-data#sms)
        <br> <br> <div style="background-color:var(--light-blue); border-radius:4px; padding:16px; color:var(--fg); line-height:24px;"> **Зверніть увагу!** <br> Щоб надіслати цей запит, вам потрібно отримати Viber альфа-імʼя. Для цього пройдіть верифікацію та подайте заявку в <a href="https://api-market.kyivstar.ua" target="_blank">особистому кабінеті</a> в налаштуваннях API-продукту. Термін погодження — **до 2 тижнів**.</div>
      x-kot-description: |
        Запит на відправку рекламного повідомлення Viber. Ініціює надсилання повідомлення Viber та повертає ідентифікатор повідомлення, яким можна скористатися, щоб отримати статус доставки.
        Рекламні повідомлення можуть містити текст, картинку, кнопку-посилання.
      x-badges:    # <<< Extension
        - color: primary-color  # <<< Allowed values (red, green, orange, blue, primary-color)
          label: 'Потрібне альфа-імʼя'
      security:
        - OAuth2: [ ]
      requestBody:
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/viber-promotion'
            examples:
              Успішний запит:
                value:
                  destination: '380670000202'
                  contentType: "text/plain"
                  contentExtended:
                    txt: 'Hello World!'
                  ttl: 7000
              Успішний запит (tag):
                value:
                  destination: '380670000202'
                  contentType: "text/plain"
                  contentExtended:
                    txt: 'Hello World!'
                    tag: 'AnyOptionalTag'
              Успішний запит (image):
                value:
                  destination: '380331000202'
                  contentType: "image/plain"
                  contentExtended:
                    tag: 'AnyOptionalTag'
                    img: 'https://api-gateway.kyivstar.ua/api/public/auth_kot_get_token.png'
              Успішний запит (image button):
                value:
                  destination: '380332000202'
                  contentType: "text/img/button"
                  contentExtended:
                    txt: 'Hello World!'
                    tag: 'AnyOptionalTag'
                    img: 'https://api-gateway.kyivstar.ua/api/public/auth_kot_get_token.png'
                    caption: 'Click to see documentation'
                    action: 'https://api-gateway.kyivstar.ua/#overview'
              Невірний формат номера отримувача:
                value:
                  destination: '000000400'
                  contentType: "text/plain"
                  contentExtended:
                    txt: 'Hello World!'
                    tag: 'tag'
        description: Message parameters
    parameters: [ ]
  '/viber/status/{mid}':
    parameters:
      - schema:
          type: string
        name: mid
        in: path
        required: true
        examples:
          example1:
            summary: "Існуючий ідентифікатор повідомлення"
            value: '20200000-0000-0000-0000-380670000200'
          example2:
            summary: "Помилковий ідентифікатор повідомлення"
            value: '20200000-0000-0000-0000-380670000404'
    get:
      x-kot-code: viber-status_GET
      summary: Статус доставки
      operationId: 'get-viber-message-msgId'
      description: |
        Виклик повертає статус доставки повідомлення Viber за message ID, який є у відповіді на запит відправки повідомлення.
      tags: [ "Viber Повідомлення" ]
      security:
        - OAuth2: [ ]
      responses:
        '200':
          description: Статус доставки повідомлення у форматі RFC3339
          content:
            application/json:
              schema:
                type: object
                properties:
                  mid:
                    type: string
                  status:
                    type: string
                  date:
                    type: string
              examples: { }
        '401':
          description: Unauthorized
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/non-authorized'
  /promo:
    post:
      x-kot-code: promo-create
      tags:
        - Розсилка SMS на свій список клієнтів
      summary: Створення розсилки
      description: Запит на створення розсилки SMS на свій список клієнтів. Ініціює створення чернетки розсилки та додає текст. Зверніть увагу, для створення розсилки необхідне альфа-імʼя. Запит повертає ідентифікатор, поточний і можливі подальші статуси розсилки, кількість змінних у тексті повідомлення, ідентифікатор автора розсилки.
      operationId: ''
      requestBody:
        description: Параметри розсилки
        content:
          application/json:
            schema:
              type: object
              properties:
                from:
                  type: string
                  description: Альфа-ім'я відправника
                text:
                  type: string
                  description: Шаблон параметризованого повідомлення
            example:
              from: barbershop
              text: Дякуємо за візит ${1} ${2}
      responses:
        '200':
          description: Розсилка успішно створена
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/promo"
        '401':
          description: Unauthorized
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/non-authorized'
              examples:
                unauthorized:
                  value:
                    errorCode: 401
                    status: Unauthorized
                    reqId: ad30594292f7959683a410bf1add088e
                    message: The request could not be authorized
        '500':
          description: Помилка на сервісі розсилок
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/base-error-body"
              examples:
                service_unavailable:
                  value:
                    "reqId": "3742ad2c8453bdc6ec709fb696b51a94"
                    "errorCode": 1010009
                    "errorMsg": "An error occurred while handling the promo"
        '503':
          description: Сервіс розсилок тимчасово не доступний
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/base-error-body"
              examples:
                service_is_not_active:
                  value:
                    "reqId": "b337f60a97675a4c57345e8414e007e6"
                    "errorCode": 1010003
                    "errorMsg": "The service is not ACTIVE for the provided EDRPOU"
      security:
        - OAuth2: []
    get:
      x-kot-code: get-all-promos
      tags:
        - Розсилка SMS на свій список клієнтів
      summary: Отримання списку розсилок
      description: Запит на отримання усіх розсилок, створених через АРІ
      operationId: ''
      parameters:
        - name: pageSize
          schema:
            type: integer
          in: query
          required: true
          description: Кількість елементів на сторінці
          examples:
            example1:
              summary: Повне співпадіння
              value: 10
        - name: pageNumber
          schema:
            type: integer
          in: query
          required: true
          description: Номер сторінки
          examples:
            example1:
              summary: Повне співпадіння
              value: 0
        - name: startDateFrom
          schema:
            type: string
          in: query
          required: false
          description: Фільтрація по даті (початок)
          examples:
            example1:
              summary: Повне співпадіння
              value: "2022-08-01T00:00:00Z"
        - name: startDateTo
          schema:
            type: string
          in: query
          required: false
          description: Фільтрація по даті (кінець)
          examples:
            example1:
              summary: Повне співпадіння
              value: "2023-09-25T00:01:00Z"
        - name: sortBy
          schema:
            type: string
          in: query
          required: false
          description: Назва поля по якому провести сортування
          examples:
            example1:
              summary: Повне співпадіння
              value: startDate
            example2:
              summary: Повне співпадіння
              value: createDate
        - name: sortDirection
          schema:
            type: string
          in: query
          required: false
          description: Напрямок сортування
          examples:
            example1:
              summary: Повне співпадіння
              value: DESC
            example2:
              summary: Повне співпадіння
              value: ASC
      responses:
        '200':
          description: Список розсилок
          content:
            application/json:
              schema:
                type: object
                properties:
                  promos:
                    type: array
                    items:
                      type: object
                      schema:
                        $ref: "#/components/schemas/promo"
                  totalPages:
                    type: integer
                    description: Доступна кількість сторінок
                  totalElements:
                    type: integer
                    description: Загальна кількість створених промо
                  number:
                    type: integer
                    description: Номер поточної сторінки
                  size:
                    type: integer
                    description: Розмір поточної сторінки
              example:
                totalPages: 1
                totalElements: 1
                number: 1
                size: 1
                promos:
                  - id: "00000000-0000-0000-0000-000000000200"
                    authorUsername: "user@user.com"
                    status: "DRAFT"
                    startDate: "2023-09-21T07:57:01"
                    endDate: "2023-09-22T07:57:01"
                    textToSend: "Дякуємо за візит $1 $2"
                    textUa: "Дякуємо за візит $1 $2"
                    nextAvailableStatuses: ["WAITING"]
                    messageContent:
                      messageParamCount: 2
      security:
        - OAuth2: [ ]
  /promo/{promoUUID}:
    get:
      x-kot-code: promo-get-status
      tags:
        - Розсилка SMS на свій список клієнтів
      summary: Отримання інформації про розсилку
      description: Запит на отримання поточної інформації про розсилку. Запит повертає поточний стан розсилки та можливі подальші стани.
      operationId: '1'
      responses:
        '200':
          description: Інформація про розсилку
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/promo"
        '401':
          description: Unauthorized
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/non-authorized'
              examples:
                unauthorized:
                  value:
                      errorCode: 401
                      status: Unauthorized
                      reqId: ad30594292f7959683a410bf1add088e
                      message: The request could not be authorized
        '404':
          description: Not Found
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/base-error-body'
              examples:
                promo_not_found:
                  value:
                    "reqId": "3742ad2c8453bdc6ec709fb696b51a94"
                    errorCode: 0
                    errorMsg: NOT_FOUND
        '500':
          description: Помилка на сервісі розсилок
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/base-error-body"
              examples:
                service_unavailable:
                  value:
                    "reqId": "3742ad2c8453bdc6ec709fb696b51a94"
                    "errorCode": 1010009
                    "errorMsg": "An error occurred while handling the promo"
        '503':
          description: Сервіс розсилок тимчасово не доступний
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/base-error-body"
              examples:
                service_is_not_active:
                  value:
                    "reqId": "b337f60a97675a4c57345e8414e007e6"
                    "errorCode": 1010003
                    "errorMsg": "The service is not ACTIVE for the provided EDRPOU"
      security:
        - OAuth2: []
    parameters:
      - name: promoUUID
        in: path
        required: true
        schema:
          $ref: "#/components/schemas/promoUUID"
        examples:
          example1:
            value: '10000000-0000-0000-0000-000000000200'
            summary: Чернетка розсилки
          example2:
            value: '20000000-0000-0000-0000-000000000200'
            summary: Активна розсилка
          example3:
            value: '30000000-0000-0000-0000-000000000200'
            summary: Cервіс розсилки не доступний
          example4:
            value: '40000000-0000-0000-0000-000000000200'
            summary: Неочікувана помилка
          example5:
            value: '50000000-0000-0000-0000-000000000200'
            summary: Закінчена розсилка
          example6:
            value: '60000000-0000-0000-0000-000000000200'
            summary: Призупинена розсилка
  /promo/{promoUUID}/audience:
    post:
      x-kot-code: promo-add-audience
      tags:
        - Розсилка SMS на свій список клієнтів
      summary: Додавання аудиторії
      description: Запит на додавання аудиторії та параметрів зі списку власних номерів для розсилки SMS. Зверніть увагу, номери додаються частинами до 20 тисяч записів в одному запиті. Запит повертає унікальну назву списку номерів телефонів, на які буде здійснюватись розсилка.
      operationId: '2'
      requestBody:
        content:
          application/json:
            schema:
              type: object
              properties:
                audience:
                  description: Власний список номерів, на які буде здійснюватись розсилка
                  type: array
                  items:
                    type: object
                    properties:
                      params:
                        description: Значення параметрів, які будуть використані у тексті повідомлення
                        type: array
                        items:
                          type: string
                          example: John
                        example:
                          - John
                          - Smith
                      phoneNumber:
                        description: Телефонний номер отримувача в довільному форматі
                        type: string
                        example: '380671234200'
                  example:
                    - params:
                        - John
                        - Smith
                      phoneNumber: '380671234200'
                    - params:
                        - John
                        - Constantine
                      phoneNumber: '380676666200'
            example:
              audience:
                - params:
                    - John
                    - Smith
                  phoneNumber: '380671234200'
                - params:
                    - John
                    - Constantine
                  phoneNumber: '380676666200'
      security:
        - OAuth2: []
      responses:
        '200':
          description: Інформація про додану аудиторію
          content:
            application/json:
              schema:
                properties:
                  name:
                    type: string
                    description: Унікальний ідентифікатор списку номерів
        '401':
          description: Unauthorized
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/non-authorized'
              examples:
                unauthorized:
                  value:
                    errorCode: 401
                    status: Unauthorized
                    reqId: ad30594292f7959683a410bf1add088e
                    message: The request could not be authorized
        '500':
          description: Помилка на сервісі розсилок
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/base-error-body"
              examples:
                service_unavailable:
                  value:
                    "reqId": "3742ad2c8453bdc6ec709fb696b51a94"
                    "errorCode": 1010009
                    "errorMsg": "An error occurred while handling the promo"
        '503':
          description: Сервіс розсилок тимчасово не доступний
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/base-error-body"
              examples:
                service_is_not_active:
                  value:
                    "reqId": "b337f60a97675a4c57345e8414e007e6"
                    "errorCode": 1010003
                    "errorMsg": "The service is not ACTIVE for the provided EDRPOU"
    parameters:
      - name: promoUUID
        in: path
        required: true
        schema:
          $ref: "#/components/schemas/promoUUID"
  /promo/{promoUUID}/status/{status}:
    put:
      x-kot-code: promo-change-status
      tags:
        - Розсилка SMS на свій список клієнтів
      summary: Управління статусом розсилки
      description: |-
        Запит на зміну поточного статусу розсилки: переведення в активний стан, припинення активної розсилки. Запит повертає поточний стан розсилки та можливі подальші стани.

        Можливі варіанти встановлення статусу розсилки:

        - WAITING - встановлюється для постановки розсилки в чергу на виконання. Після початку розсилка автоматично буде переведена в статус ACTIVE
        - SUSPENDED - встановлюється для зупинки розсилки
      operationId: '3'
      responses:
        '200':
          description: Інформація про стан розсилки
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/promo"
        '401':
          description: Unauthorized
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/non-authorized'
              examples:
                Unauthorized:
                  value:
                    errorCode: 401
                    status: Unauthorized
                    reqId: ad30594292f7959683a410bf1add088e
                    message: The request could not be authorized
        '404':
          description: Not Found
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/base-error-body'
              examples:
                promo_not_found:
                  value:
                    "reqId": "3742ad2c8453bdc6ec709fb696b51a94"
                    errorCode: 0
                    errorMsg: NOT_FOUND
        '500':
          description: Помилка на сервісі розсилок
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/base-error-body"
              examples:
                service_unavailable:
                  value:
                    "reqId": "3742ad2c8453bdc6ec709fb696b51a94"
                    "errorCode": 1010009
                    "errorMsg": "An error occurred while handling the promo"
        '503':
          description: Сервіс розсилок тимчасово не доступний
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/base-error-body"
              examples:
                service_is_not_active:
                  value:
                    "reqId": "b337f60a97675a4c57345e8414e007e6"
                    "errorCode": 1010003
                    "errorMsg": "The service is not ACTIVE for the provided EDRPOU"
      security:
        - OAuth2: []
    parameters:
      - name: promoUUID
        in: path
        required: true
        schema:
            type: string
            format: uuid
        examples:
          success:
            value: '20000000-0000-0000-0000-000000000200'
      - name: status
        in: path
        required: true
        schema:
          type: string
        examples:
          waiting:
            value: WAITING
  /promo/{promoUUID}/statistics:
    get:
      x-kot-code: promo-get-statistics
      tags:
        - Розсилка SMS на свій список клієнтів
      summary: Отримання статистики по розсилці
      description: Запит повертає агреговану статистику для послуги "Розсилка SMS на свій список клієнтів".
      operationId: '4'
      responses:
        '200':
          description: Статистика розсилки
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/promoStat"
      security:
        - OAuth2: [ ]
    parameters:
      - name: promoUUID
        in: path
        required: true
        schema:
          type: string
          format: uuid
          example: 20000000-0000-0000-0000-000000000200
components:
  schemas:
    subscriber-verify-sim:
      title: subscriber-verify-sim
      type: object
      properties:
        activationHours:
          type: integer
          example: 48
          description: Період заміни SIM картки (в годинах). Мінімальне значення - 24, максимальне - 120. Підставте відповідне значення, щоб отримати відповідь чи замінювалась SIM карта у зазначений період.
      required:
        - activationHours
    subscriber-check-sim-count:
      title: subscriber-check-sim-count
      type: object
      properties:
        dayCount:
          type: integer
          example: 100
          description: Період перевірки кількості SIM карток (в днях). Мінімальне значення - 1, максимальне - 120. Підставте відповідне значення, щоб отримати відповідь чи замінювалась SIM карта у зазначений період.
      required:
        - dayCount
    subscriber-phone-number:
      type: string
      minLength: 9
    sms:
      title: sms
      type: object
      description: ''
      properties:
        from:
          type: string
          description: |
            Альфанумеричне ім'я (альфа-ім'я) відправника. Враховуйте регістр альфа-імені, наприклад "Alpha" та "alpha" - є різними альфа-іменами
          example: messagedesk
          examples:
            example1:
              summary: "Альфа-імʼя для Sandbox серверу"
              value: messagedesk
          x-kot-suggestions:
            source: org-product-parameters
            product-parameter-code: alphaName
            display-value-property: value
            params:
              statusCode: approved
        to:
          type: string
          example: '38 (067) 000-02-00'
          minLength: 9
          description: Телефонний номер отримувача
          x-kot-suggestions:
            source: org-phone-numbers
        text:
          type: string
          example: 'your OTP: 32145'
          description: Вміст повідомлення
        maxSegments:
          type: number
          description: Максимальна кількість SMS-повідомлень на API-виклик
          example: 1
          enum:
            - 1
            - 2
            - 3
            - 4
            - 5
            - 6
        messageTtlSec:
          type: integer
          description: Час життя SMS-повідомлення в секундах
          minimum: 0
          maximum: 86400
          default: 86400
      required:
        - from
        - to
        - text
    viber-transaction:
      title: viber transaction
      type: object
      description: ''
      required:
        - destination
        - contentExtended
      properties:
        destination:
          type: string
          example: '38 (067) 000-02-00'
          minLength: 9
          description: Телефонний номер отримувача
        contentExtended:
          type: object
          description: 'Вміст повідомлення'
          required:
            - txt
          properties:
            txt:
              type: string
              example: 'your OTP: 32145'
              description: 'Вміст повідомлення'
            tag:
              type: string
              example: 'someTag'
              description: 'Тег повідомлення'
        ttl:
          type: integer
          minimum: 30
          maximum: 1209600
          default: 1209600
          description: Час життя повідомлення в секундах. Від 30 до 1209600 сек (14 днів)
    viber-promotion:
      title: viber promotion
      type: object
      description: ''
      required:
        - destination
        - contentType
        - contentExtended
      properties:
        destination:
          type: string
          example: '38 (067) 000-02-00'
          minLength: 9
          description: Телефонний номер отримувача
        contentType:
          type: string
          example: 'text/img/button'
          description: 'Вміст повідомлення'
        contentExtended:
          type: object
          description: 'Тип повідомлення'
          properties:
            txt:
              type: string
              example: 'your OTP: 32145'
              description: 'Вміст повідомлення'
            img:
              type: string
              example: 'https://api-gateway.kyivstar.ua/api/public/auth_kot_get_token.png'
              description: 'Посилання на медіа контент вкладений у повідомлення'
            caption:
              type: string
              example: 'Як отримати access-токен'
              description: 'Заголовок медіа повідомлення'
            action:
              type: string
              example: 'https://api-gateway.kyivstar.ua/#overview'
              description: 'Посилання медіа повідомлення'
            tag:
              type: string
              example: 'someTag'
              description: 'Тег повідомлення'
        ttl:
          type: integer
          minimum: 30
          maximum: 1209600
          default: 1209600
          description: Час життя повідомлення в секундах. Від 30 до 1209600 сек (14 днів)
    promo:
      type: object
      properties:
        id:
          type: string
          format: uuid
          example: 10000000-0000-0000-0000-000000000200
          description: Унікальний ідентифікатор розсилки
        authorUsername:
          type: string
          example: user@user.com
          description: Ідентифікатор автора
        status:
          type: string
          example: DRAFT
          description: Поточний статус розсилки
        startDate:
          type: string
          description: Дата початку розсилки
        endDate:
          type: string
          description: Дата завершення розсилки
        textToSend:
          type: string
          example: Дякуємо за візит $1 $2
          description: Шаблон параметризованого повідомлення
        textUa:
          type: string
          example: Дякуємо за візит $1 $2
          description: Шаблон параметризованого повідомлення українською мовою
        nextAvailableStatuses:
          description: Статус, в який можливо перевести розсилку
          type: array
          items:
            type: string
          example: [WAITING]
        messageContent:
          description: Дані щодо змінних, які використовуються для розсилки
          type: object
          properties:
            messageParamCount:
              description: Кількість змінних у вмісті повідомлення
              type: integer
              format: int32
              example: 2
        messageTtlSec:
          type: integer
          description: Час життя SMS-повідомлення в секундах
          minimum: 0
          maximum: 86400
    promoStat:
      type: object
      properties:
        sentCount:
          type: integer
          format: int32
          example: 0
          description: Кількість відправлених повідомлень
        deliveriesCount:
          type: integer
          format: int32
          example: 0
          description: Кількість доставлених повідомлень
        unmatchedCount:
          type: integer
          format: int32
          example: 0
          description: Кількість невідповідних повідомлень
        deliveriesPortionsCount:
          type: integer
          format: int32
          example: 0
          description: Кількість частин доставлених повідомлень
        deliveriesUnmatchedPortionsCount:
          type: integer
          format: int32
          example: 0
          description: Кількість частин невідповідних доставлених повідомлень
        deliveriesInternalPortionsCount:
          type: integer
          format: int32
          example: 0
          description: Кількість внутрішніх частин доставлених повідомлень
        deliveriesExternalPortionsCount:
          type: integer
          format: int32
          example: 0
          description: Кількість зовнішніх частин доставлених повідомлень
        undeliveredCount:
          type: integer
          format: int32
          example: 0
          description: Кількість недоставлених повідомлень
        unknownStatusCount:
          type: integer
          format: int32
          example: 0
          description: Кількість повідомлень із невідомим статусом
        canceledByContactPolicyCount:
          type: integer
          format: int32
          example: 0
          description: Кількість скасованих повідомлень через політику контакту
        seenCount:
          type: integer
          format: int32
          example: 0
          description: Кількість переглянутих повідомлень
        blacklistedCount:
          type: integer
          format: int32
          example: 0
          description: Кількість чорних списків
        declinedCount:
          type: integer
          format: int32
          example: 0
          description: Кількість відхилених повідомлень
        expiredCount:
          type: integer
          format: int32
          example: 0
          description: Кількість закінчених повідомлень
        wasNotSent:
          type: integer
          format: int32
          example: 20
          description: Кількість повідомлень, які не були відправлені
    promoUUID:
      type: string
      format: uuid
      example: 10000000-0000-0000-0000-000000000200
    non-authorized:
      description: unauthorized
      properties:
        error:
          type: object
          properties:
            code:
              type: integer
            status:
              type: string
            message:
              type: string
            request:
              type: string
      type: object
      x-examples:
        Unauthorized:
          error:
            code: 401
            status: Unauthorized
            request: ad30594292f7959683a410bf1add088e
            message: The request could not be authorized
    base-error-body:
      title: base-error-body
      type: object
      properties:
        errorCode:
          type: integer
        errorMsg:
          type: string
          example: 'error message'
        additionalInfoUrl:
          type: string
          example: 'link to FAQ'
    subscriber-match-device-info:
      title: subscriber-match-device-info
      type: object
      properties:
        IMEI:
          type: string
          description: Міжнародний ідентифікатор мобільного обладнання
        IMSI:
          type: string
          description: "Міжнародний ідентифікатор користувача мобільного зв'язку"
        lat:
          type: number
          description: Географічна широта пристрою абонента
        lng:
          type: number
          description: Географічна довгота пристрою абонента
    subscriber-location-match-info:
      title: subscriber-location-match-info
      type: object
      properties:
        lat:
          type: number
          description: Географічна широта пристрою абонента
        lng:
          type: number
          description: Географічна довгота пристрою абонента
  securitySchemes:
    OAuth2:
      type: oauth2
      x-client-id: mock_client_id
      x-client-secret: mock_client_secret
      flows:
        clientCredentials:
          $ref: 'openapi-vars.yaml#/OAuthFlows/clientCredentials'
      description: 'API КВТ використовує OAuth 2.0 з [Client credential flow](https://www.oauth.com/oauth2-servers/access-tokens/client-credentials/).
      <br><br>Client ID та Client Secret можна знайти в <a href="https://api-market.kyivstar.ua" target="_blank">особистому кабінеті</a> на сторінці додатку.<br>
      Використовуйте заповнені Client ID та Client Secret для мокового сервера'
