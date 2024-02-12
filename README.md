# Работа с файлами

### Общий обзор работы с файлами в Node.js.

#### Введение в Модуль `fs`

-   В Node.js основной модуль для работы с файловой системой - это `fs`.
-   Этот модуль позволяет выполнять различные операции с файлами, такие как чтение, запись, удаление, и многое другое.

#### Синхронные и Асинхронные Операции

-   Node.js предлагает два вида методов для работы с файлами: синхронные и асинхронные.
-   **Синхронные методы** блокируют дальнейшее выполнение кода до окончания текущей операции. Они удобны для скриптов, где последовательность операций критична.
-   **Асинхронные методы** работают на основе коллбэков, промисов или async/await, позволяя Node.js продолжать обработку других задач во время выполнения операции с файлом.

#### Основные Операции с Файлами

1. **Чтение из файла** – получение данных из файла.
2. **Запись в файл** – создание нового файла или изменение существующего.
3. **Удаление файла** – удаление файла из файловой системы.
4. **Получение информации о файле** – доступ к метаданным файла, таким как размер, дата создания и т.д.
5. **Работа с потоками** – для обработки больших файлов без загрузки их целиком в память.

### Синхронные и асинхронные операции

#### Синхронное Чтение и Запись Файлов

1. **Чтение из файла синхронно**:

    - Используется метод `readFileSync`.
    - Пример:

        ```javascript
        const fs = require("fs");

        try {
            const data = fs.readFileSync("path/to/file.txt", "utf8");
            console.log(data);
        } catch (err) {
            console.error(err);
        }
        ```

    - Здесь `readFileSync` читает содержимое файла и возвращает его. Если возникает ошибка (например, файл не найден), исключение будет перехвачено и обработано в блоке `catch`.

2. **Запись в файл синхронно**:

    - Используется метод `writeFileSync`.
    - Пример:
        ```javascript
        try {
            fs.writeFileSync("path/to/file.txt", "Hello, World!");
            console.log("Файл успешно записан");
        } catch (err) {
            console.error(err);
        }
        ```
    - `writeFileSync` записывает данные в файл. Если файл не существует, он будет создан.

#### Асинхронное Чтение и Запись Файлов

1. **Чтение из файла асинхронно**:

    - Используется метод `readFile`.
    - Пример:
        ```javascript
        fs.readFile("path/to/file.txt", "utf8", (err, data) => {
            if (err) {
                console.error(err);
                return;
            }
            console.log(data);
        });
        ```
    - `readFile` принимает путь к файлу и коллбэк, который вызывается после завершения чтения. Коллбэк получает два аргумента: ошибку (если она произошла) и данные файла.

2. **Запись в файл асинхронно**:

    - Используется метод `writeFile`.
    - Пример:
        ```javascript
        fs.writeFile("path/to/file.txt", "Hello, World!", (err) => {
            if (err) {
                console.error(err);
                return;
            }
            console.log("Файл успешно записан");
        });
        ```
    - Аналогично `readFile`, `writeFile` принимает путь к файлу, данные для записи и коллбэк, который вызывается после завершения операции записи.

#### Основные операции с файлами

##### Открытие файла (`open`, `openSync`).

###### Синхронное открытие файла (`openSync`)

-   **Метод `openSync`** от модуля `fs` используется для синхронного открытия файла.
-   Он блокирует выполнение последующего кода до завершения открытия файла.
-   Метод возвращает **файловый дескриптор** (целое число), который представляет открытый файл и используется для дальнейших операций с файлом.

**Пример использования:**

```javascript
const fs = require("fs");

try {
    // Открываем файл синхронно
    const fd = fs.openSync("path/to/file.txt", "r");
    console.log(`Файл открыт, дескриптор файла: ${fd}`);

    // Дальнейшие операции с файлом...

    // Закрываем файл
    fs.closeSync(fd);
} catch (err) {
    console.error(`Ошибка при открытии файла: ${err}`);
}
```

-   В этом примере мы используем `'r'` в качестве второго аргумента для `openSync`, что означает открытие файла для чтения.

###### Асинхронное открытие файла (`open`)

-   **Метод `open`** предназначен для асинхронного открытия файла.
-   Он не блокирует выполнение кода, вместо этого использование коллбэка позволяет обработать результат открытия файла после его выполнения.
-   Как и в случае с `openSync`, метод возвращает файловый дескриптор.

**Пример использования:**

```javascript
fs.open("path/to/file.txt", "r", (err, fd) => {
    if (err) {
        console.error(`Ошибка при открытии файла: ${err}`);
        return;
    }

    console.log(`Файл открыт, дескриптор файла: ${fd}`);

    // Дальнейшие операции с файлом...

    // Закрываем файл
    fs.close(fd, (err) => {
        if (err) console.error(`Ошибка при закрытии файла: ${err}`);
    });
});
```

-   В этом примере `'r'` также указывает на открытие файла для чтения.
-   Важно не забывать закрывать файл после завершения работы с ним, используя `closeSync` для синхронного закрытия или `close` для асинхронного.

##### Чтение из файла (`read`, `readSync`, `createReadStream`).

###### Синхронное чтение из файла (`readSync`)

-   **`readSync`** - это синхронный метод чтения из файла.
-   Он блокирует выполнение последующего кода до тех пор, пока чтение файла не будет завершено.
-   Метод возвращает прочитанные данные и часто используется в сценариях, где требуется простое и последовательное выполнение операций.

**Пример использования:**

```javascript
const fs = require("fs");

try {
    const fd = fs.openSync("path/to/file.txt", "r");
    const bufferSize = 1024;
    const buffer = Buffer.alloc(bufferSize);
    let bytesRead;

    // Читаем из файла
    bytesRead = fs.readSync(fd, buffer, 0, bufferSize, 0);
    console.log(buffer.toString("utf8", 0, bytesRead));

    // Закрываем файл
    fs.closeSync(fd);
} catch (err) {
    console.error(`Ошибка при чтении файла: ${err}`);
}
```

-   В этом примере мы используем `Buffer` для хранения прочитанных данных.

###### Асинхронное чтение из файла (`read`)

-   **`read`** - асинхронный метод чтения из файла.
-   Он позволяет продолжить выполнение других операций во время чтения файла, используя коллбэк для обработки результатов.

**Пример использования:**

```javascript
fs.open("path/to/file.txt", "r", (err, fd) => {
    if (err) {
        console.error(err);
        return;
    }

    const bufferSize = 1024;
    const buffer = Buffer.alloc(bufferSize);

    // Читаем из файла
    fs.read(fd, buffer, 0, bufferSize, 0, (err, bytesRead, buffer) => {
        if (err) {
            console.error(`Ошибка при чтении файла: ${err}`);
            return;
        }
        console.log(buffer.toString("utf8", 0, bytesRead));
    });

    // Закрываем файл
    fs.close(fd, (err) => {
        if (err) console.error(`Ошибка при закрытии файла: ${err}`);
    });
});
```

###### Чтение из файла через поток (`createReadStream`)

-   **`createReadStream`** - создаёт читаемый поток данных.
-   Это идеальный метод для работы с большими файлами, так как он позволяет читать данные частями, не загружая весь файл в память сразу.

**Пример использования:**

```javascript
const readStream = fs.createReadStream("path/to/file.txt", "utf8");

readStream.on("data", (chunk) => {
    console.log(`Получена часть данных: ${chunk}`);
});

readStream.on("end", () => {
    console.log("Чтение файла завершено.");
});

readStream.on("error", (err) => {
    console.error(`Ошибка при чтении файла: ${err}`);
});
```

-   В этом примере мы читаем файл по частям (`chunk`), что особенно полезно при работе с большими файлами.

##### Запись в файл (`write`, `writeSync`, `createWriteStream`).

###### Синхронная запись в файл (`writeSync`)

-   **`writeSync`** - это синхронный метод записи данных в файл.
-   Он блокирует выполнение последующего кода до завершения операции записи.
-   Этот метод идеально подходит для простых задач, где последовательное выполнение операций важнее асинхронной обработки.

**Пример использования:**

```javascript
const fs = require("fs");

try {
    const fd = fs.openSync("path/to/file.txt", "w");
    const data = "Пример текста для записи в файл";

    // Записываем данные в файл
    fs.writeSync(fd, data);

    // Закрываем файл
    fs.closeSync(fd);
    console.log("Данные успешно записаны в файл.");
} catch (err) {
    console.error(`Ошибка при записи в файл: ${err}`);
}
```

###### Асинхронная запись в файл (`write`)

-   **`write`** - асинхронный метод записи в файл.
-   Этот метод позволяет продолжать выполнение других задач в приложении, пока происходит запись данных.

**Пример использования:**

```javascript
fs.open("path/to/file.txt", "w", (err, fd) => {
    if (err) {
        console.error(err);
        return;
    }

    const data = "Пример текста для асинхронной записи в файл";

    // Записываем данные в файл
    fs.write(fd, data, (err) => {
        if (err) {
            console.error(`Ошибка при записи в файл: ${err}`);
            return;
        }
        console.log("Данные успешно записаны в файл.");

        // Закрываем файл
        fs.close(fd, (err) => {
            if (err) console.error(`Ошибка при закрытии файла: ${err}`);
        });
    });
});
```

###### Запись в файл через поток (`createWriteStream`)

-   **`createWriteStream`** создает поток для записи данных в файл.
-   Это особенно полезно для записи больших объемов данных, так как позволяет передавать данные по частям, не загружая их полностью в память.

**Пример использования:**

```javascript
const writeStream = fs.createWriteStream("path/to/file.txt");

writeStream.write("Первая часть данных\n");
writeStream.write("Вторая часть данных\n");
writeStream.end("Завершающие данные");

writeStream.on("finish", () => {
    console.log("Запись в файл завершена.");
});

writeStream.on("error", (err) => {
    console.error(`Ошибка при записи файла: ${err}`);
});
```

-   Здесь данные записываются в файл порциями, а `finish` срабатывает, когда весь поток данных успешно записан.

##### Получение информации о файле (`stat`, `statSync`).

###### Асинхронное получение информации о файле (`stat`)

-   **`stat`** — асинхронный метод, который используется для получения информации о файле.
-   Он не блокирует выполнение других операций во время своей работы, что делает его подходящим для использования в многопоточных приложениях.

**Пример использования:**

```javascript
const fs = require("fs");

fs.stat("path/to/file.txt", (err, stats) => {
    if (err) {
        console.error(`Ошибка при получении информации о файле: ${err}`);
        return;
    }

    console.log(stats);
    console.log(`Размер файла: ${stats.size}`);
    console.log(`Дата создания файла: ${stats.birthtime}`);
    console.log(`Файл является директорией? ${stats.isDirectory()}`);
    console.log(`Файл является обычным файлом? ${stats.isFile()}`);
});
```

-   `stats` — это объект, содержащий информацию о файле, такую как размер, даты создания и изменения, а также методы для определения типа файла (например, `isFile` или `isDirectory`).

###### Синхронное получение информации о файле (`statSync`)

-   **`statSync`** — синхронный аналог `stat`.
-   Этот метод блокирует выполнение кода до тех пор, пока не будет получена вся информация о файле.

**Пример использования:**

```javascript
try {
    const stats = fs.statSync("path/to/file.txt");

    console.log(stats);
    console.log(`Размер файла: ${stats.size}`);
    console.log(`Дата создания файла: ${stats.birthtime}`);
    console.log(`Файл является директорией? ${stats.isDirectory()}`);
    console.log(`Файл является обычным файлом? ${stats.isFile()}`);
} catch (err) {
    console.error(`Ошибка при получении информации о файле: ${err}`);
}
```

##### Закрытие файла (`close`, `closeSync`).

###### Асинхронное закрытие файла (`close`)

-   **`close`** — это асинхронный метод, используемый для закрытия файла.
-   Он принимает файловый дескриптор (полученный, например, с помощью методов `open` или `openSync`) и коллбэк, который вызывается после закрытия файла.

**Пример использования:**

```javascript
const fs = require("fs");

fs.open("path/to/file.txt", "r", (err, fd) => {
    if (err) {
        console.error(err);
        return;
    }

    // Операции с файлом...

    // Закрываем файл
    fs.close(fd, (err) => {
        if (err) {
            console.error(`Ошибка при закрытии файла: ${err}`);
        } else {
            console.log("Файл успешно закрыт");
        }
    });
});
```

-   Здесь файл сначала открывается для чтения, после чего закрывается асинхронно.

###### Синхронное закрытие файла (`closeSync`)

-   **`closeSync`** — синхронный аналог `close`.
-   Этот метод блокирует выполнение кода до тех пор, пока файл не будет закрыт.

**Пример использования:**

```javascript
try {
    const fd = fs.openSync("path/to/file.txt", "r");

    // Операции с файлом...

    // Закрываем файл
    fs.closeSync(fd);
    console.log("Файл успешно закрыт");
} catch (err) {
    console.error(`Ошибка при работе с файлом: ${err}`);
}
```

-   В этом примере файл сначала открывается синхронно, а затем закрывается.

# Глобальные объекты и функции

#### Введение в глобальные объекты

##### Что такое глобальные объекты?

Глобальные объекты в Node.js – это объекты, которые доступны в любом месте вашего приложения. Они предоставляют функционал, который можно использовать без необходимости импортировать модули или объявлять переменные. Эти объекты находятся в глобальной области видимости, что означает их доступность из любого модуля вашего приложения.

Примеры глобальных объектов в Node.js включают:

-   `global`: Корневой объект глобальной области видимости, аналогичный объекту `window` в браузерах.
-   `process`: Объект, предоставляющий информацию и контроль над текущим процессом Node.js.
-   `console`: Для вывода информации в консоль.
-   `setTimeout`, `clearTimeout`, `setInterval`, `clearInterval`: Методы для управления таймерами.
-   `Buffer`: Для работы с двоичными данными.
-   `__dirname` и `__filename`: Переменные, предоставляющие пути к текущему каталогу и файлу.

##### Видимость и использование глобальных объектов

Глобальные объекты доступны в любой части вашего Node.js приложения. Это делает их очень удобными, так как они обеспечивают доступ к важным функциональным возможностям без необходимости дополнительных импортов или объявлений.

Однако существуют некоторые важные моменты при работе с глобальными объектами:

1. **Избегайте загрязнения глобальной области видимости**: Добавление собственных объектов или переопределение существующих глобальных объектов может привести к конфликтам и ошибкам в приложении.
2. **Осторожное использование**: Некоторые глобальные объекты, такие как `global` или `process`, могут содержать чувствительные данные или методы, способные влиять на работу всего приложения.
3. **Понимание контекста**: Например, `__dirname` и `__filename` полезны для получения путей к текущему каталогу и файлу, но их значения будут различаться в зависимости от того, в каком модуле они используются.

#### Основные глобальные объекты

##### `__filename` и `__dirname`: понимание и использование.

В Node.js, `__filename` и `__dirname` являются двумя важными глобальными переменными, которые предоставляют информацию о местоположении текущего исполняемого файла. Они играют ключевую роль в управлении путями файлов и директорий в вашем приложении.

###### `__filename`

-   **Определение**: `__filename` представляет собой полный путь к файлу, который исполняется в данный момент.
-   **Использование**: Эта переменная часто используется, когда необходимо получить точное местоположение исполняемого файла. Например, это может быть полезно для создания путей к другим файлам или ресурсам относительно текущего файла.

**Пример:**

```javascript
console.log(`Путь к текущему файлу: ${__filename}`);
```

Этот код выводит абсолютный путь к файлу, из которого он выполняется.

###### `__dirname`

-   **Определение**: `__dirname` представляет собой полный путь к директории, в которой находится текущий исполняемый файл.
-   **Использование**: Это особенно полезно, когда вам нужно построить пути к другим файлам или директориям относительно директории текущего файла. Например, при загрузке модулей, чтении файлов или указании пути к статическим ресурсам.

**Пример:**

```javascript
const path = require("path");

// Построение пути к файлу конфигурации, находящемуся в той же директории
const configPath = path.join(__dirname, "config.json");
console.log(`Путь к файлу конфигурации: ${configPath}`);
```

Здесь `path.join` используется для безопасного соединения `__dirname` с именем файла, формируя путь к файлу `config.json`, который находится в той же директории, что и текущий файл.

##### `console`: логирование и отладка.

###### Введение в `console`

-   Модуль `console` в Node.js используется для вывода сообщений в консоль. Это ключевой инструмент для логирования информации, предупреждений и ошибок, что необходимо для отладки кода.

###### Основные Методы `console`

1. **`console.log()`**:

    - Самый часто используемый метод для логирования общих сообщений.
    - Пример:
        ```javascript
        console.log("Это информационное сообщение");
        ```
    - Выводит сообщение в стандартный вывод, обычно это терминал или консоль разработчика.

2. **`console.error()`**:

    - Используется для логирования ошибочных или предупреждающих сообщений.
    - Пример:
        ```javascript
        console.error("Ошибка! Что-то пошло не так.");
        ```
    - Сообщения выводятся в стандартный поток ошибок, что помогает отличать их от обычных логов.

3. **`console.warn()`**:

    - Специально для предупреждающих сообщений.
    - Пример:
        ```javascript
        console.warn("Предупреждение: используйте эту функцию осторожно!");
        ```
    - Помогает выделить важные предупреждения среди большого количества логов.

4. **`console.table()`**:

    - Для вывода табличных данных, что упрощает их чтение и анализ.
    - Пример:
        ```javascript
        console.table({ name: "Алекс", age: 30 });
        ```
    - Отображает объекты и массивы в виде удобной таблицы.

5. **`console.time()` и `console.timeEnd()`**:

    - Эти методы используются для измерения времени выполнения определенных частей кода.
    - Пример:
        ```javascript
        console.time("Метка времени");
        // Выполняемый код
        console.timeEnd("Метка времени");
        ```
    - Помогают оценить производительность и найти узкие места в коде.

###### Использование `console` для Отладки

-   `console` - это мощный инструмент для отладки. С его помощью можно легко отслеживать ход выполнения программы и идентифицировать ошибки.
-   Рекомендуется активно использовать логирование при разработке, но перед выходом в продакшн убедитесь, что отладочные логи убраны или минимизированы, чтобы не перегружать вывод и не раскрывать чувствительные данные.

##### `process`: доступ к информации о текущем процессе Node.js.

###### Введение в `process`

-   Объект `process` в Node.js предоставляет информацию и контроль над текущим процессом Node.js.
-   Этот объект является глобальным и доступен без необходимости его импортировать, что делает его удобным инструментом для взаимодействия с операционной системой и средой исполнения.

###### Основные Возможности `process`

1. **Доступ к аргументам командной строки**:

    - `process.argv` – массив, содержащий аргументы командной строки.
    - Пример использования:
        ```javascript
        const args = process.argv.slice(2);
        console.log(args);
        ```
    - Позволяет скриптам Node.js получать и обрабатывать аргументы, переданные при их запуске.

2. **Управление окружением исполнения**:

    - `process.env` – объект, содержащий переменные окружения.
    - Пример:
        ```javascript
        const envVar = process.env.MY_ENV_VAR;
        console.log(`Значение переменной окружения: ${envVar}`);
        ```
    - Используется для чтения и установки переменных окружения, что особенно важно для конфигурации приложения в разных средах.

3. **Информация о текущем процессе**:

    - `process.pid` – идентификатор процесса.
    - `process.platform` – платформа, на которой запущен Node.js (например, `linux`, `win32`).
    - Пример:
        ```javascript
        console.log(`PID: ${process.pid}`);
        console.log(`Платформа: ${process.platform}`);
        ```
    - Полезно для мониторинга и управления процессами, а также для написания платформо-зависимого кода.

4. **Управление жизненным циклом процесса**:

    - `process.exit()` – завершает текущий процесс.
    - `process.on('exit', callback)` – позволяет выполнить код перед выходом из процесса.
    - Пример:
        ```javascript
        process.on("exit", () => {
            console.log("Процесс завершается...");
        });
        ```
    - Важно для корректного завершения процессов и освобождения ресурсов.

5. **Потоки ввода-вывода**:

    - `process.stdout`, `process.stdin`, `process.stderr` – потоки для стандартного ввода, вывода и ошибок.
    - Примеры использования:
        - Вывод в консоль: `process.stdout.write("Привет, мир!\n");`
        - Чтение ввода: `process.stdin.on('data', data => { console.log(`Вы ввели: ${data}`); });`
    - Эти потоки используются для интерактивных приложений, логирования и обработки ошибок.

##### Обзор других глобальных объектов.

###### Введение в Глобальные Объекты Node.js

-   В дополнение к `console` и `process`, Node.js предоставляет несколько других глобальных объектов, которые выполняют различные функции и доступны во всем приложении без необходимости импорта.

###### Основные Глобальные Объекты

1. **`global`**:

    - Аналог объекта `window` в браузерах.
    - Является корневым объектом глобальной области видимости.
    - Пример:
        ```javascript
        global.myGlobalVar = "Пример глобальной переменной";
        console.log(myGlobalVar); // Выведет: Пример глобальной переменной
        ```
    - Используется для определения переменных и функций, доступных везде в приложении.

2. **`Buffer`**:

    - Используется для работы с двоичными данными.
    - Не требует импорта для использования.
    - Пример:
        ```javascript
        const buffer = Buffer.from("Привет, мир", "utf-8");
        console.log(buffer); // Выводит двоичное представление строки
        ```
    - Особенно полезен для обработки потоков данных и взаимодействия с файловой системой.

3. **`__dirname` и `__filename`**:

    - Предоставляют пути к текущему каталогу и файлу соответственно.
    - `__dirname` – директория текущего исполняемого файла.
    - `__filename` – имя текущего исполняемого файла.
    - Пример:
        ```javascript
        console.log(__dirname); // Путь к текущей директории
        console.log(__filename); // Путь к текущему файлу
        ```
    - Используются для создания относительных путей в приложении.

4. **`setTimeout`, `clearTimeout`, `setInterval`, `clearInterval`**:

    - Методы для управления таймерами.
    - `setTimeout` и `setInterval` используются для задания задержек и интервалов.
    - `clearTimeout` и `clearInterval` для их отмены.
    - Пример:
        ```javascript
        const timerId = setTimeout(
            () => console.log("Задержка выполнена"),
            1000,
        );
        clearTimeout(timerId); // Отменяет задержку
        ```
    - Полезны для управления асинхронными операциями и повторяющимися задачами.

### Глобальные функции в Node.js

Node.js включает в себя ряд глобальных функций, которые доступны в любой части приложения без необходимости импорта. Эти функции предоставляют базовые возможности, важные для различных аспектов разработки.

#### Таймеры и их управление

##### Введение в Таймеры

-   Таймеры в Node.js предоставляют механизмы для управления временем в асинхронном коде. Они включают функции для установки задержек и создания повторяющихся задач, а также их отмены.

##### `setTimeout` и `clearTimeout`

1. **`setTimeout(func, delay, [...args])`**:

    - Используется для задания задержки перед выполнением функции `func`.
    - `delay` - время в миллисекундах до вызова функции.
    - `[...args]` - аргументы, передаваемые в функцию `func`.
    - Возвращает идентификатор таймера, который можно использовать для его отмены.
    - Пример:
        ```javascript
        const greet = () => console.log("Привет!");
        const timerId = setTimeout(greet, 2000); // Выполнится через 2 секунды
        ```

2. **`clearTimeout(timerId)`**:

    - Отменяет таймер, установленный с помощью `setTimeout`.
    - `timerId` - идентификатор таймера, возвращенный `setTimeout`.
    - Пример:
        ```javascript
        clearTimeout(timerId); // Отменяет таймер, т.е. функция greet не будет вызвана
        ```

##### `setInterval` и `clearInterval`

1. **`setInterval(func, interval, [...args])`**:

    - Запускает функцию `func` повторно через каждый интервал времени `interval` (в миллисекундах).
    - `[...args]` - аргументы, передаваемые в функцию `func` при каждом вызове.
    - Возвращает идентификатор интервала, который можно использовать для его отмены.
    - Пример:
        ```javascript
        const logMessage = () => console.log("Сообщение каждые 3 секунды");
        const intervalId = setInterval(logMessage, 3000);
        ```

2. **`clearInterval(intervalId)`**:

    - Отменяет повторяющийся таймер, установленный с помощью `setInterval`.
    - `intervalId` - идентификатор интервала, возвращенный `setInterval`.
    - Пример:
        ```javascript
        clearInterval(intervalId); // Прекращает повторение функции logMessage
        ```

# Создание приложения

Приложение будет взаимодействовать с Postman для выполнения операций с файлами.

Давайте разделим наше приложение на отдельные части кода и файлы для более четкого понимания и легкости в управлении.

### Создание Основного Серверного Файла `server.js`

Этот файл будет являться точкой входа в наше приложение и создавать HTTP-сервер.

```javascript
// server.js
const http = require("http");
const { handleRequest } = require("./requestHandler");

const server = http.createServer(handleRequest);

server.listen(3000, () => {
    console.log("Сервер запущен на порту 3000");
});
```

### Обработка Запросов в Файле `requestHandler.js`

Этот файл будет отвечать за обработку входящих HTTP-запросов.

```javascript
// requestHandler.js
const fs = require("fs");
const url = require("url");

const handleRequest = (req, res) => {
    const parsedUrl = url.parse(req.url, true);
    const path = parsedUrl.pathname;
    const trimmedPath = path.replace(/^\/+|\/+$/g, "");

    switch (trimmedPath) {
        case "readfile":
            readFileHandler(res);
            break;
        case "writefile":
            writeFileHandler(req, res);
            break;
        default:
            notFoundHandler(res);
    }
};

const readFileHandler = (res) => {
    fs.readFile("example.txt", "utf8", (err, data) => {
        if (err) {
            res.writeHead(500);
            res.end("Не удалось прочитать файл");
        } else {
            res.writeHead(200);
            res.end(data);
        }
    });
};

const writeFileHandler = (req, res) => {
    let body = "";
    req.on("data", (chunk) => {
        body += chunk.toString();
    });
    req.on("end", () => {
        fs.writeFile("example.txt", body, (err) => {
            if (err) {
                res.writeHead(500);
                res.end("Не удалось записать файл");
            } else {
                res.writeHead(200);
                res.end("Файл успешно записан");
            }
        });
    });
};

const notFoundHandler = (res) => {
    res.writeHead(404);
    res.end("Страница не найдена");
};

module.exports = { handleRequest };
```

### Объяснение Работы Кода

1. **Сервер (`server.js`)**:

    - Импортирует встроенный модуль `http` и модуль обработки запросов `requestHandler`.
    - Создает HTTP-сервер, который слушает порт 3000.
    - При получении запроса, вызывает функцию `handleRequest` из `requestHandler`.

2. **Обработчик запросов (`requestHandler.js`)**:

    - Импортирует модули `fs` и `url`.
    - Функция `handleRequest` анализирует URL и определяет, какой обработчик вызвать (`readFileHandler`, `writeFileHandler` или `notFoundHandler`).
    - `readFileHandler` читает содержимое файла `example.txt` и отправляет его клиенту.
    - `writeFileHandler` получает данные из POST-запроса и записывает их в `example.txt`.
    - `notFoundHandler` обрабатывает все остальные запросы, отправляя ответ 404.

### Тестирование с Postman

1. **Чтение файла**:

    - Отправьте GET-запрос на `http://localhost:3000/readfile`.
    - Проверьте ответ сервера в Postman.

2. **Запись в файл**:

    - Отправьте POST-запрос на `http://localhost:3000/writefile` с текстом в теле запроса.
    - Проверьте, что текст записан в `example.txt`.
