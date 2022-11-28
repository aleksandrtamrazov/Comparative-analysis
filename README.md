# Comparative-analysis
Сравнительный анализ подходов к организации взаимодействия в приложении

# Motivation
Я пишу эту статью для своего доклада и для того, чтобы самому поглубже погрузиться в архитектуру приложений

# About
In this article, I will touch on several options for organizing the interaction of various parts of our application - these are callback functions (callbacks), generators (yeild) and the message exchange bus (eventemitter).

В этой статье я затрону несколько вариантов организации взаимодействия различных частей frontend приложений — это callback-функции (callbacks), генераторы (yield) и шина обмена сообщениями (eventemitter).

# How it works

Я попытаюсь сравнить эти три метода обработки цепочки синхронных/асинхронных событий по следующим критериям и на основе этих критериев определить для каждого, какую область применения он имеет - то есть какие задачи с его помощью удобнее или более эффективные, а какие нет.

![](https://github.com/aleksandrtamrazov/Comparative-analysis/blob/master/img/mainTable.png)

Каждый ответ будет иметь небольшой пример кода JS, демонстрирующий указанное поведение. Ответ по каждому критерию может содержать два варианта (callback могут использоваться для организации как синхронного, так и асинхронного взаимодействия), в этом случае следует рассматривать оба варианта.

# Main

### Callback

Колбэк-функция (или обратный вызов) - это функция, переданная в другую функцию в качестве аргумента, которая затем вызывается по завершению какого-либо действия.

Коллбэк простая констукция в программировании и она выполняет довольно много полезных действий. Основным плюсом использовать callback-функции - это способ расширения функционала, элемент проектирования функций. Однако callback-функции бывают как синхронные, так и ассинхронные. И тут у них большая разница. ..... (дописать тут немного)

#### callback/sync
Синхронные callback-функции нужны для функциональной композиций. Композиция функций - способ сделать из нескольких фукций одну функцию. Ярким примером является функция pipe, которая используется очень часто. 

Пример функции pipe

```js
const reverse = (string) => string.split('').reverse().join('') // Пример синхронной callback-функции

const uppercase = (string) => strign.toUpperCase(string)

const pipe = (...fns) => (x) => fns.reduсe((v, f) => f(v), x) // пример замыкания
```

Функция pipe принимает callback функции, агрегирует их и на каждую букву в строке вызывает функции.

С помощью такого подхода эффективно решать многие синхронные задачи. Pipe применятся 

#### callback/async

Ассинхнронные операции выполняются callback функциями для того чтобы вернуть значение из функции. Вместо того, чтобы сразу же вернуть какой-то результат, как делает большинство функций, эти использующие обратные вызовы функции требуют время для получения результата.

Этот подход нужен для выполнения ассинхронных операции, таких как работа с файловой системой и запросов на сервер. Например вы хотите сначала прочитать файл с файловой системы, а затем выполнить код функции, но как наверняка узнать когда закончить чтение файла? Ответ: никак. наша функция должна исполниться сразу же после асинхронной операции чтения. Например у нас есть функция readFile, которая читает файл с диск. Далее мы хотим чтобы в консоле после чтения файла появилось сообщение об успехе или не успехе.

```js
readFile('~/Desktop/myFile.txt', log)

function log(err) {
  if(err) {
    console.error('Ошибка чтения!')
  } else {
    console.log('Файл успешно прочитан!')
  }
}

console.log('Операция чтения начата!')
```
Попробуйте ответить сами: какой сообщение мы увидим в консоли раньше?

Такой подход к написанию кода на JavaScript пораждает некоторые проблемы, такие как callback-hell. Решение нашлось в Promise, а далее и в async/await. Не буду подробно останавливаться на этом. Но callback, Promise, async/await используются в js-коде очень часто, и причем повсеместно. Любой разработчик должен их понимать.

Рассказать как работает Event loop

Event loop - это способ о

```js

const callback = () => console.log('I`m from async callback');

setTimeout(callback, 2000);

console.log('I`m from sync code')

// I`m from sync code
// I`m from async callback

```

Код не блокирует цикл событий поэтому

Но если много-чего, то настанет callback-hell

Такой способ реализаций ассинхронных событий устарел после появления Promise

#### callback/stateful

Подход statefull в JavaScript говорит о том, что функция которую мы создаем использует внешнее состояние которое мы инициальзировали выше. А именно она изменяет наше состояние.

```js
const state = 1;

const increment = () => {
  return state += 1;
}

increment(); // 2
```

Такой подход в конструировании функций очень наглядно видно при работе с замыканием.

```js
const getMessage = (msg) => {
  const result = msg;

  return function() {
    return result;
  }
}

var logMessage = getMessage('Hello');
console.log(logMessage()); // Hello
```

#### callback/stateless

Подход stateless в JavaScript говорит о том, что функция которую мы создаем не использует внешнее состояние которое мы инициальзировали выше.

```js
const state = 1;

const decrement = (num) => {
  return num += 1;
}

decrement(state); // 2
```

#### callback/pull
Концепция pull/push систем основывается на паттерне Producer/Consumer.
Довольно широко эти подходы используются в рективном программировании. RxJs
Эти два подхода описывают как производитель данных (Producer) может взаимодействовать с потребителем данных (Consumer)

Pull системы - это системы в которых Consumer сам определяет как и в какой момент использовать данные, Producer не знает куда и когда будут доставлены данные
Каждая функция JS - это pull система. Функция является источником данных, а код который ее вызывает, является потребителем данных извлекая(pulling) единственно возвращаемое значение из этой функции.

Есть также функции итераторы и генераторы(function*), которые также являются pull системами. [pull and push](http://reactivex.io/rxjs/manual/overview.html)

```js
const producer = () => ({ name: 'Alex', age: 24 })
const consumer = () => { let state = producer() }
```

#### callback/push

Что такое Пуш? В системах Push Производитель определяет, когда отправлять данные Потребителю. Потребитель не знает, когда он получит эти данные.

Промисы — наиболее распространенный тип системы Push в JavaScript на сегодняшний день. Промис (Производитель) предоставляет разрешенное значение зарегистрированным обратным вызовам (Потребителям), но, в отличие от функций, именно Промис отвечает за точное определение того, когда это значение «проталкивается» в обратные вызовы.

```js
let producer = () => new Promise((resolve, reject) => {
  let count = 0;
  setInterval(() => {
    resolve(count++);
  }, 1000);
});

let consumer = data => { console.log(data); };

producer().then(consumer) // 0
```

Давайте попробуем разобрать middleware redux-thunk, как стуктуру построенную на callback-функция, которая помогает нам исполнять ассинхронные запросы в react-redux приложениях с архитектурой flux.

вот исходный код функции 

```js
function createThunkMiddleware(extraArgument) {
  const middleware =
    ({ dispatch, getState }) =>
    next =>
    action => {
      if (typeof action === 'function') {
        return action(dispatch, getState, extraArgument)
      }

      return next(action)
    }
  return middleware
}
```

Итак, сама по себе библиотека redux-thunk небольшая, главная функция createThunkMiddleware умещается в 17 строк. В ней совмешается подходы синхронного колбека ввиде принимаемого диспатч, далее есть подход stateless, тк функция не использует никакое внешннее состояние, а также пулл-подход так как сама выбирает в какой момент использовать данные.

Как реакт с помощью счетчика понимает когда нужно отрендерить компонент


### Yield

функции-генераторы

Генераторы очень странная и загадочная часть языка JS. Некоторые разработчики видят их сложными. Вы можете быть успешным разработчиком и никогда не чувстовать необходимости в их использовании. Тогда зачем же они нужны? Попробую ответить на этот вопрос. Генераторы низкоуровневая констукция, они как будто инструменты для создания инструментов, которые решают наши повседневные проблемы, но если смотреть на них изолированно, может показаться что они вообще не нужны. Однако это не так.

#### Ленивые итераторы

Генераторы можно использовать для ленивых итераций. Если вы знакомы с протоколом "Перебора", этот функционал появлся в 2015 году, тогда вы знаете что они тоже относятся к низкоуровневым констукциям. Эти протоколы говорят движку JS, что данный обект можно итерировать или использовать spread оператор для него. 

```js
function* culturalAchievements() {
  yield 'Amazing professional people';
  yield 'The lovely office';
  yield 'Friendly atmosphere';
}

for (achievement of culturalAchievements()) {
    console.log(`Axenix is known for: ${achievement}`);
}
```

Далее давайте расмотрим такую ситуацию, например, ....

```js
const array = [1, 2, 3, 4, 5]


```

такой подход к проетированию функции-генератора называется паттерн трансдюсер

Я не буду вдоваться в подробности генераторов, они могут быть очень полезны, с их помощью можно переписать множется функций высшего порядка таких как map flatMap filter pop pipe 

Попробуем переписать функцию filter из примера коллбеков и посмотрим на разницу подходов

```js
function* filter(predicate, array) {
  for (value of array) {
    if (predicate(value)) {
      yield value
    }
  }
}

console.log(...filter()) // Поскольку функции генераторы возвращает итераторы, то используя оператор spread мы получим нужный нам массив
```

Так например, стейт менеджер redux-saga, использует для выполнения ассинхроных операций, паттерн сага, а именно функции-генератора.

Код который может выглядеть так

```js
fetchProducts(url).then((val) => {
  console.log(val)
})
```
будет выглядеть так

```js
let value = fetchProducts(url)
console.log(value)
```

Это очень удобно, и делает код визуально синхронным, как и async/await

вот код из под капота redux saga 

```js
function next(arg, isErr) {
  try {
    let result
    if (isErr) {
      result = iterator.throw(arg)
      // user handled the error, we can clear bookkept values
      sagaError.clear()
    } else if (shouldCancel(arg)) {
      /**
        getting TASK_CANCEL automatically cancels the main task
        We can get this value here
        - By cancelling the parent task manually
        - By joining a Cancelled task
      **/
      mainTask.status = CANCELLED
      /**
        Cancels the current effect; this will propagate the cancellation down to any called tasks
      **/
      next.cancel()
      /**
        If this Generator has a `return` method then invokes it
        This will jump to the finally block
      **/
      result = is.func(iterator.return) ? iterator.return(TASK_CANCEL) : { done: true, value: TASK_CANCEL }
    } else if (shouldTerminate(arg)) {
      // We get TERMINATE flag, i.e. by taking from a channel that ended using `take` (and not `takem` used to trap End of channels)
      result = is.func(iterator.return) ? iterator.return() : { done: true }
    } else {
      result = iterator.next(arg)
    }

    if (!result.done) {
      digestEffect(result.value, parentEffectId, next)
    } else {
      /**
        This Generator has ended, terminate the main task and notify the fork queue
      **/
      if (mainTask.status !== CANCELLED) {
        mainTask.status = DONE
      }
      mainTask.cont(result.value)
    }
  } catch (error) {
    if (mainTask.status === CANCELLED) {
      throw error
    }
    mainTask.status = ABORTED

    mainTask.cont(error, true)
  }
}
```

например redux-saga 



# Contributing
The main purpose of this repository is to continue evolving me and you. I'll accept any help. You are welcome!
