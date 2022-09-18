## React Testing Library (RTL)

Эта библиотека предназначена для тестирования React компонентов. Не претендует на то, чтобы быть заменой Jest,
хотя и имеет весьма схожие методы (да и вообще юзает сам Jest под капотом).

Разница у RTL и Jest в том, что RTL - тестирует как бы "со стороны пользователя". То есть проверяет 
отрендерилось ли в компоненте то, что надо, не активны ли те кнопочки, которые не должны быть активны,
сработал ли вызов API (и отрендерились ли данные после этого вызова) итп. 

В общем то, что пользователь может пощупать ручками и увидеть глазками.

### Цель: Научиться писать тесты при помощи этой библиотеки.

### Как это работает.

Перед тем как запускать тесты какого-то компонента, нужно сначала отрендерить этот самый компонент.
Для этого используется функция render.

```js
import React from 'react';
import { render, screen } from '@testing-library/react';
import App from './App';

describe('App', () => {
  test('renders App component', () => {
    render(<App />);
    
    // Здесь уже пойдут методы RTL, например:
    expect(screen.getByText('Search:')).toBeInTheDocument
  })
})
```

Существует 3 основных метода проверки наличия чего-либо в компоненте:
- getBy - Служит для проверки того, что элемент ЕСТЬ в компоненте
- queryBy - Служит для проверки того, что элемента НЕТ в компоненте
- findBy - Служит для проверки того, что элемента сначала НЕТ, а потом он ПОЯВЛЯЕТСЯ в компоненте

>Я так понимаю, что первые 2 метода придумали потому, что нельзя определить какой именно аргумент будет 
передан в метод expect (а ведь этот метод вообще Jest-овский) и потому непонятно как ограничивать, где юзать
toBeNull, а где toBeInTheDocument. Ну и обратная совместимость похоже свою роль сыграла, как всегда.

##### Как определить какой конкретно метод из группы использовать?
Например: Есть у нас кнопка в компоненте с надписью "Поехали!". Как проверить её наличие? Ну вроде надо
использовать метод getBy. 
А какой конкретно, если их там дохера? Вот лист:
- getByText
- getByRole
- getByLabelText
- getByPlaceholderText
- getByAltText
- getByDisplayValue

Можно по тексту, можно по роли, а можно и по value, если оно конечно у кнопочки задано...
Для решения этого вопроса умные люди написали про [приоритет методов](https://testing-library.com/docs/queries/about/#priority)

##### Проверка интерактивности в компоненте

Для этого есть библиотеки fireEvent и userEvent.
Обе содержат набор функций для манипуляций с компонентой (type, click, tab, hover) и симулируют поведение 
пользователя. userEvent была написана поверх fireEvent и поэтому она рекомендуется к использованию.

Разница у них например в том, что при симуляции набора текста, fireEvent.change() триггерит только changeEvent, 
в то время как userEvent триггерит ещё и keyDown, keyPress и keyUp ивенты, таким образом полностью повторяя 
поведение реального пользователя.

Или вот:

```js
describe('Search', () => {
  test('calls the onChange callback handler', () => {
    const onChange = jest.fn()

    render(
      <Search value={''} onChange={onChange}>
        Search:
      </Search>
    );

    userEvent.type(screen.getByRole('textbox'), 'JavaScript');
    
    // В случае использования fireEvent был бы другой синтаксис у строки сверху и функция onChange
    // была бы вызвана всего один раз
    
    expect(onChange).toHaveBeenCalledTimes(10);
    
    /* А вот когда реальный пользователь воодит текст, функция отработает 10 раз, по нажатию на каждую клавишу. */
  })
})
```
 Кроме того, можно например замокать целый вызов API!

```tsx
jest.mock('axios');
const mockedAxios = axios as jest.Mocked<typeof axios>

describe('App', () => {
  test('fetches stories from an API and displays them', async () => {
    const stories = [
      { objectID: '1', title: 'Hello' },
      { objectID: '2', title: 'React' },
    ];

    const promise = Promise.resolve({ data: { hits: stories } })

    mockedAxios.get.mockImplementationOnce(() => promise);

    render(<App />);

    await userEvent.click(screen.getByRole('button'));

    const items = await screen.findAllByRole('listitem');

    expect(items).toHaveLength(2);
  });


  test('fetches stories from an API and fails', async () => {
    mockedAxios.get.mockImplementationOnce(() => Promise.reject(new Error()));
    render(<App />);
    await userEvent.click(screen.getByRole('button'));
    const message = await screen.findByText(/Something went wrong/);
    expect(message).toBeInTheDocument();
  });
});
```

### Вывод

Как видно из примеров, библиотечка позволяет тестить содержимое компонентов, вызовы API и срабатывание
различных функций и кнопочек. Рендер короче говоря.

Остаётся открытым вопрос **покрытия тестами компонентов**, потому что в теории можно накарябать столько 
тестов, что сам потом рад не будешь.

Но штука очевидно весьма полезная и в связке с Jest позволяет заполонить тестами всё свободное пространство.



