# Виртуальный DOM

<h3> Цель: 

1. Понять что такое виртуальный DOM и как Реакт использует его для рендера UI.

2. Понять чем виртуальный DOM отличается от Shadow DOM и от реального DOM.  

</h3>

### Что такое виртуальный DOM?

Виртуальный DOM - это концепция, основанная на том, что DOM-дерево представляется в вид обычных JS-объектов где-то
в глубине самого Реакта, а точнее в React DOM.

### Нахер оно вообще надо?

Вся проблема в том, что передовые алгоритмы преобразования деревьев имеют сложность порядка O(n**3). То есть при 1000
элементов в дереве нужно миллиард сравнений, если вести преобразование "в лоб", то есть сравнивать "каждый с каждым".

Конечно такую сложность никто не может себе позволить, поэтому Реакт выдвинул эвристический (помогающий находить, 
обнаруживать что-либо) алгоритм преобразования, который обладает сложностью О(n) и следует двум предположениям.

- Два элемента с разными типами произведут разные деревья.
- Разработчик может указать, какие дочерние элементы могут оставаться стабильными между разными рендерами с помощью пропа key.

Вот так вот просто, но эффективно.

В первую очередь Реакт сравнивает типы компонентов/узлов. Если типы разные, то сам компонент и ВСЁ дерево ниже будут
уничтожены и отрисованы заново. Поэтому если у нас есть 2 компонента, которые возвращают почти то же самое, но один 
оборачивает всё в div, а другой - в section, то это очень плохо. Вместо того чтобы поменять какой-нибудь текст внутри
за маленькое время, мы целиком перерисуем весь компонент и все его дочерние элементы. Производительность проседает,
так делать ен надо.

Дальше, при сравнениях, Реакт сверяет аттрибуты у одинаковых элементов/компонентов и меняет только их, не рисуя заново 
всё поддерево. То же самое происходит и со стилями элементов.

#### О ключах.

Если мы преобразуем список:
- Вася
- Петя

в список

- Вася
- Петя
- Серёжа

то преобразование выполнится эффективно. Ну а что, стандартный алгоритм работы с массивами, так? 

А вот если из:
- Вася
- Петя

делать 

- Серёжа
- Вася
- Петя

то преобразование проседает. Ну типа надо все элементы сравнить заново по очереди и потом отрисовать весь список с нуля.


Как решить? Добавить элементам уникальные ключи, а потом сравнивать при изменении. Элемент с ключом Х есть в списке?
Его индекс изменился? Ок, принято. Добавили новый элемент? Какой у него ключ? Какой индекс?

Это сильно упрощает работу, позволяя сравнивать только ключи и индексы, а не перебирать все элементы как будто их 
впервые видим.

Именно поэтому Реакт требует проп key при рендере списков, и того, чтобы ключ был уникальным и неизменным.
По той же причине, использовать индексы в качестве ключа - плохая затея, если элементы будут меняться местами, исчезать 
или добавляться НЕ в конец списка.


>Примечание: В 16 Реакте добавили приоритезацию, так что теперь он не просто помечает какие ноды надо перестроить, но
> ещё и высчитывает какие из них надо поменять в первую очередь, а какие уже потом.

### Shadow DOM и виртуальный DOM - это одно и тоже?

Нет. 

Shadow DOM - технология, обеспечивающая инкапсуляцию. Эта штука позволяет к любому элементу реального DOM-дерева
прикрепить ещё дополнительные элементы.

А виртуальный DOM - это копия реального, используемая для вычисления изменений.

### Недостатки

Виртуальный DOM - не панацея. Вообще есть неплохая [статья](https://svelte.dev/blog/virtual-dom-is-pure-overhead) 
от Svelte, которая чётко даёт понять в чём прикол. Скорость - не достоинство виртуального DOM.

Декларативность и скорость разработки - вот главное достоинство этой технологии. Не надо париться о стейтах,
ты точно знаешь что и когда будет обновлено. И в целом, производительность зачастую достаточно неплоха,
чтобы не париться о всяких мелочах типа расчётов при каждом рендере. 


<h3> Вывод: Виртуальный DOM сильно ускоряет работу Реакта и очень существенно повышает производительность, 
если знать как грамотно использовать его особенности. Конечно, всё будет работать и без этого всего, 
но медленно, а такой Реакт нам не нужен</h3>
