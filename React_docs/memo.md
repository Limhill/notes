# React.memo

### Цель: познакомиться с функцией React.memo и понять когда и для чего она используется

React.memo - это по сути компонент высшего порядка. Используется для того, чтобы избежать лишних рендеров и улучшить
производительность.

Представим что у нас есть компонент App, который внутри себя содержит компонент Component. В качестве пропсов компонент 
Component получает список list.

```jsx
const Component = (({list}) => {
        return (
        list.map((item) => (<div key={item}>{item}</div>))
    )
})

const App = () => {
    const [state, setState] = useState(0);
    const list = [1, 2, 3, 4, 5];

    return (
        <div>
            <Component list={list} />
            <h1>App state: {state}</h1>
            <button onClick={() => setState(state + 1)}>Change App state</button>
        </div>
    )
}
```

Если сейчас тыкать кнопку и изменять состояние App, то вместе с ним, каждый раз будет перерендериваться и Component.
Это очень плохо, если внутри App будет не один компонент, а например сотня, т.к. производительность будет проседать уже 
весьма ощутимо.

Для того чтобы избежать лишних рендеров при неизменных пропсах и придумали React.memo. Обернув в него компонент,
получим неизменное так называемое "мемоизированное значение". И если пропсы у компонента не изменяются, то
он не будет ререндерится при обновлении состояния у родительского компонента. 

```jsx
const Component = (({list}) => {
        return (
        list.map((item) => (<div key={item}>{item}</div>))
    )
})

const MemoizedComponent = React.memo(Component)

const App = () => {
    const [state, setState] = useState(0);
    const list = [1, 2, 3, 4, 5];

    return (
        <div>
            <MemoizedComponent list={list} />
            <h1>App state: {state}</h1>
            <button onClick={() => setState(state + 1)}>Change App state</button>
        </div>
    )
}
```

Синтаксис можно варьировать и использовать 2 способа.

```jsx
// Представим что есть компонент Component и нам надо его мемоизировать. 
const Component = ({name}) => {
    return <h1>Hello {name}!</h1>
}

//Способ 1.
const MyComponent = React.memo(Component) // <-- это валидно. Мемоизированный компонент - MyComponent будем 
// использовать дальше.

//Способ 2.
const Component = React.memo(({name}) => { // <-- это также валидно, не создаётся новый комопонент.
    return <h1>Hello {name}!</h1>
})
```


### Вывод: React.memo довольно удобная штука, особенно при дорогостоящих вычислениях или в специфических ситуациях, когда не хочется рендерить дочерние компоненты повторно при повторном рендере родителя.