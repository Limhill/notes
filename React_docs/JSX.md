# JSX

### Цель: Понять что такое JSX и можно ли использовать Реакт без него.

Все Реакт-компоненты по сути создаются при помощи метода 

```js
React.createElement(componentName, { propKey: propValue }, children)
```

JSX - это синтаксический сахар для метода React.createElement. Пишется в виде обычных HMTL-тегов, а
вложенные в них элементы и есть children.

Фактически, запись 
```js
const MyComponent = () => {
  return (
    <div>Some text here</div>
  );
}
```
Это то же самое, что и:
```js
const MyComponent = () => {
  return (
    React.createElement('div', {}, 'Some text here')
  );
}
```

>Интересный факт. Если писать через JSX, то при попытке вернуть из функционального компонента несколько 
children, Реакт выдаст ошибку. А если написать то же самое, но через React.createElement - то не выдаст.

JSX превращается в React.createElement при помощи Babel. 

### Вывод: JSX является не более чем синтаксическим сахаром и Реакт можно использовать без него. Но нужен ли нам такой Реакт? :D
