---
title: Переменные
date: 2024-01-29 00:00:00 +0300
categories: [Programming, С]
tags: [c]
pin: false
author: spybull
---

## Что такое переменные
Переменные - это фундаментальные объекты данных в языке С, без них не пишется ни одна программа.  
Они предназначены для хранения данных и манипулирования этими данными.

Каждая переменная обязательно должна иметь:
- тип, позволяющий определить какие данные мы будем хранить;
- идентификатор, состоящий из буквенно-цифровых символов;

Переменную можно проинициализировать и присвоить ей начальное значение, в зависимости от типа.  
Еще мы можем добавить к объявлению переменной квалификаторы и классы памяти.

Сама по себе переменная - это некоторая ячейка памяти, которая хранит данные, которые мы туда помещаем,
а переменной она называется потому что данные могут меняться с течением времени и обстоятельств.

## Ключевые слова в языке C

В любом языке присутсвуют "ключевые слова", под ними я подразумеваю встроенные идентификаторы языка, т.е зарезервированные языком
наборы некоторых идентификаторов (имен). Например к таким идентификаторам могут относиться [типы данных]({% post_url 2023-12-15-types in c %}#типы-данных)
и другие конструкции языка:

```c
auto        enum        restrict    unsigned
break       extern      return      void
case        float       short       volatile
char        for         signed      while
const       goto        sizeof      _Bool
continue    if          static      _Complex
default     inline      struct      _Imaginary
do          int         switch
double      long        typedef
else        register    union
```
Данный набор взят из стандарта [ISO/IEC 9899:1999](https://www.dii.uchile.cl/~daespino/files/Iso_C_1999_definition.pdf)

> Данные ключевые слова нельзя использовать в качестве имен переменных, функций, стуктур и т.д
{: .prompt-warning }

Помимо ключевых слов языка, существуют также зарезервированные компилятором дополнительные команды препроцессора, ключевые слова и атрибуты.

Для GCC:
- [Keyword index](https://gcc.gnu.org/onlinedocs/gcc-4.1.2/gcc/Keyword-Index.html)
- [Function attributes](https://gcc.gnu.org/onlinedocs/gcc/Function-Attributes.html)

## Имена переменных

Имена переменных и других объектов в коде используются для идентификации, и для удобного использования объектов при обращении к ним.

"имя переменной" - это ее идентификатор, стандарт определяет некоторые правила для идентификаторов.
Разрешается использовать латинские буквы верхнего и нижнего регистра, нижнее подчеркивание
и цифры:
```
a b c d e f g h i j k l m
n o p q r s t u v w x y z
A B C D E F G H I J K L M
N O P Q R S T U V W X Y Z _

0 1 2 3 4 5 6 7 8 9
```

Данный набор символов относится не только к именам переменных, он относится к любым **идентификаторам** в коде, будь то
имена функций структур или констант!

> Имя идентификатора не может начинаться с цифры или состоять только из цифр
{: .prompt-warning }

Пример валидных имен переменных:

```c
int _name_;
char nAmEoFvAr;
int _1_2_3_4_5;
int my;
int a,b,c,d,e;
int var_name1;
```

Пример **НЕ** валидных имен переменных:

```c
int 1_name;
char 123;
int variable-name;
int if;
```

Также стоит отметить, что большинство идентификаторов начинающихся с нижнего подчеркивания '_', могут быть зарезервированы 
разными библиотеками и если вы не хотите нарваться на неприятности, то лучше не использовать нижнее подчеркивание в начале имени идентификатора.

Имена переменным, функциям, структурам и другим объектам стоит давать осознанные. Также не стоит делать имена слишком длинными
или слишком короткими. Есть некоторые соглашения об именах идентификаторов например [Snake case](https://en.wikipedia.org/wiki/Snake_case)
или [Camel case](https://en.wikipedia.org/wiki/Camel_case).

Я использую оба соглашения но в С - **Snake case**, в С++ - **Camel case**. Последний свой проект я разрабатывал согласно 
[GNU Coding Standards](https://www.gnu.org/prep/standards/standards.html) в котором есть отдельный [раздел про naming](https://www.gnu.org/prep/standards/standards.html#Names). При написании кода для ядра Linux существует свой стиль - [Linux kernel coding style](https://www.kernel.org/doc/html/v4.10/process/coding-style.html).


Подводя итог:
- Идентификаторы не могут начинаться с цифры;
- Идентификаторы могут состоять из цифр и нижнего подчеркивания;
- Идентификаторы могут состоять из латинских букв верхнего и нижнего регистра;
- В качестве имени идентификатора нельзя использовать зарезервированые ключевые слова;
- Идентификатор может начинаться с нижнего подчеркивания, но имя может быть зарезервировано;

## Область видимости (scope)

Область видимости (scope) - это область в коде программы, в которой видимость ограничина одним из четырех типов областей:

#### function scope
Каждая функция имеет свою область видимости ограниченную фигурными скобками '{}'. 

```c
int main()
{
    /* область видимости ограничена этим блоком */
    return 0;
}
```

Это говорит нам о том, что любые переменные(идентификаторы) объявленные внутри функции будут ограничены областью видимости данной функции.
Например, объявим две функции:
```c

int fn1() {
    int a = 10;
    return 0;
}

int fn2() {
    int b = 20;
    // переменная 'a' - недоступна в данной функции
    return 0;
}
```


В данном случае функция `fn1()` и `fn2()` имеют свои области видимости и все переменные внутри функций являются локальными по 
отношению к функции в которой они объявлены.   

> Время жизни таких переменных(идентификаторов) ограничено областью видимости функции.
{: .prompt-warning }

#### block scope
Область видимости на уровне блока представляет собой отдельную область видимости внутри пары фигурных скобок:

```c
#include <stdio.h>

int main()
{ /* Область видимости на уровне функции */
    int test = 10;

    { /* Область видимости на уровне блока 1 */
        printf("(block #1 (start)): %d\n", test);
        int test = 20;

        { /* Область видимости на уровне блока 2 */
            printf("(block #2 (start)): %d\n", test);
            int test = 30;
            printf("(block #2 (end)): %d\n", test);
        }

        printf("(block #1 (end)): %d\n", test);
    }

    printf("(main scope): %d\n", test);
    return 0;
}
```
На выходе мы получим следующий результат:
```terminal
(block #1 (start)): 10
(block #2 (start)): 20
(block #2 (end)): 30
(block #1 (end)): 20
(main scope): 10
```

При первом вызове `printf()` мы получаем число **10**, находясь внутри области видимости первого блока. Это говорит нам о том
что первый вложенный блок "видит" переменную `test` со значением **10** из области видимости функции `main()`. Это также было бы 
справедливо и для остальных вложенных блоков.

В первом блоке мы объявили еще одну переменную с таким же именем `test`, тем самым "перекрывая" переменную `test` из области
видимости функции `main`. Поэтому в выводе второго вложенного блока мы получили значение **20**. Тоже самое справедливо и для 
третьего вывода, где мы получили значение **30**.

При выходе из второго вложенного блока переменная `test` со значением **30** перестает существовать, но так как
мы находимся в первом вложенном блоке в выводе мы видим число **20**. Тоже самое справедливо и при выходе из
первого вложенного блока, поэтому при выходе из всех блоков на уровне области видимости функции у нас
по прежнему переменная `test` остается со значением **10**.

#### file scope
Область видимости на уровне файла относится к области видимости на уровне всего файла где пишется код.
Данную область видимости еще называют глобальной областью видимости, а переменные объявленные внутри
этой области - глобальными переменными.
```c
/* main.c */
#include <stdio.h>

int test = 100; /* Переменная имеет область видимости на уровне файла */

void fn() {
    printf("fn() test = %d\n", test);
}

int main() {
    fn();
    printf("main() test = %d\n", test);
    int test = 200;
    printf("main() test = %d\n", test);
    return 0;
}
```

В результате получаем:
```terminal
fn() test = 100
main() test = 100
main() test = 200
```

#### function prototype scope
Область видимости на уровне прототипа функции относится только лишь к прототипам функций и означает, что 
параметры функции в прототипе функции имеют область видимости до конца объявления этого прототипа.

```c
void func1 (int a, int b);
void func2 (int a, int b);
void func3 (int a, int b);
```

В данном случае параметры с одинаковыми именами **a** и **b** могут быть объявлены сколько угодно раз.

Другой пример:
```c
void func(int a, int a);
```

Выдаст соответствующую ошибку:
```c
s.c:4:20: error: redefinition of parameter 'a'
    4 | void f( int a, int a );
      |                ~~~~^
s.c:4:13: note: previous definition of 'a' with type int
    4 | void f( int a, int a );
      |         ~~~~^
```

Что говорит о том, что в области видимости прототипа функции мы попытались переопределить переменную **a** второй раз

## Классы памяти
Класс памяти (storage class) - определяет тип хранения объекта. **Только один класс памяти может быть определен для объекта**.
Если класс памяти не указан явно, то объект получит класс памяти по умолчанию, выбор определенного класса по умолчанию 
зависит от того где объект определен, внутри функции(**internal**) или за ее пределами (**external**). Для
объектов объявленных за пределами функций по умолчанию применяется класс памяти `extern`, а для объектов
объявленных внутри функции `auto`.

```c
/* main.c */

int z; /* класс памяти по умолчанию будет extern */

int main()
{
    int a; /* класс памяти по умолчанию будет auto */
    return 0;
}

```

### extern

Данный класс памяти может применяться как к переменным так и к функциям. Используется он обычно 
в контексте внешнего связывания и говорит компилятору о том, что переменная или функция
объявлены где-то в другом файле.

```c
/* functions.c */
int test = 100;
void println(const char *str) {
    printf("%s\n", str);
}
```

```c
/* main.c */
extern int test;
extern void println(const char *str);
```

Функции по умолчанию имею класс памяти `extern`, как и глобальные переменные, поэтому явного указания не требуется.
Однако, явное использование `extern` для функций может быть полезно для ясности кода.

Одна из интересных конструкций где еще может применяться класс памяти `extern`, это в коде на C++:
```cpp
extern "C" {
    unsigned void bump(int, char);
};
```

Данная конструкция применяется в языке C++ для того чтобы компилятор С++ не [манглировал](https://en.wikipedia.org/wiki/Name_mangling) 
ссылку на функцию bump.

Если например хочется использовать какие-то функции из кода на С++ в коде на С, то 
такая конструкция позволит не манглировать имена функций, что позволит использовать данные функции внутри кода на С.

```cpp
extern "C" {
   void p(int){
      /* не манглируется */
   }
};
```

При использовании `extern "C"` компилятор отключает механизм [манглирования](https://en.wikipedia.org/wiki/Name_mangling) имен, который применяется по умолчанию в C++, что позволяет функциям иметь "C-стиль" интерфейс, который легко использовать из кода на языке С.


### auto
Данный класс памяти применяется по умолчанию к локальным переменным внутри функции, явное указание
данного ключевого слова не имеет смысла. Продолжительность жизни таких переменных ограничена блоком.

Например:
```c
int main() {
    int a; /* класс памяти = auto */

    {
        int b; /* класс памяти = auto */
    } /* в конце блока память выделенная под b будет освобождена */

    return 0;
}/* в конце блока память выделенная под a будет освобождена */
```

### static
Данный класс памяти может применяться как переменным так и к функциям в зависимости от контекста использования
данный класс памяти ведет себя по разному. 

При использовании внутри функции перед объявлением переменной:
```c
void func() {
    static int a = 0;
    a++;
    printf("a = %d\n", a);
}

int main() {
    func();  /* Выведет a = 1 */
    func();  /* Выведет a = 2 */
    return 0;
}
```

Переменная 'a' имеет статическую продолжительность жизни. Это означает, что переменная будет создана
только один раз при первом запуске функции и в дальнейшем она будет сохранять свое значение между
другими вызовами данной функции.

При использовании ключевого параметра `static` для глобальных переменных или функций внутри текущего файла, 
переменные или функции становятся доступными только на уровне текущего файла, в других файлах получить
доступ к таким функциям или переменным не представляется возможным.

```c
/* main.c */
static int test = 10;
static void println(const char *str);
```

```c
/* functions.c */
test = 100; /* переменная test не доступна */
println(); /* функция println() не доступна */
```


### register
Данный класс памяти указывает компилятору на то, что переменная должна быть
размещена в регистре процессора по возможности. Это не жесткое требование
и компилятор может проигнорировать его.

Переменную с таким классом памяти можно объявлять только внутри блока.
**Адресация для данного класса памяти запрещена.** При объявлении массива 
с данным классом памяти, конвертировать такой массив в указатель не получится.

```c
int main()
{
    register int a = 500;
    return 0;
}
```

Данное ключевое слово используется редко, так как современные компиляторы хорошо
оптимизируют код и размещают переменные в регистрах более эффективно.


## Квалификаторы

### const
Объект объявленный с квалификатором `const` становится неизменяемым.
Важно заметить, что при использовании данного квалификатора необходимо
инициализировать объект начальным значением. Это отличает константы от обычных переменных, 
которые могут быть объявлены без начального значения и присвоены позже.

```c
const int FOO = 314; /* Правильно */

/* Неправильно */
const int FOO;
FOO = 100;
```

### volatile
К объекту объявленному с квалификатором `volatile` не будут применяться оптимизации компилятора, а также
объект не будет подвержен кэшированию.
Самый простой пример, который можно описать, связанный с данным квалификатором `volatile` - это обращение к памяти в цикле.

```c
int main()
{
    volatile int *p = (int *)0xfffffffa;
    while(1) {
        if (*p)
            break;
    }
}
```

В данном случае компилятор не будет производить оптимизации над указателем `*p`, что 
позволит в цикле производить постоянное обращение к памяти. Если бы мы не указали
квалификатор `volatile` то при оптимизации кода, адрес памяти в указателе `*p` был
бы сохранен и никаких обращений к памяти не происходило бы.


### restrict
Квалификатор `restrict` дает обещание компилятору о том, что программист гарантирует, что
доступ к объекту через указатель объявленный как `restrict` будет осуществляться только через этот 
указатель и этот указатель не будет пересекаться с памятью других указателей.
**Это позволяет компилятору лучше оптимизировать код.**

> Данный квалификатор может быть применен только к указателю.
{: .prompt-warning }

Хорошим примером могут служить две функции [memcpy](https://man7.org/linux/man-pages/man3/memcpy.3.html) и [memmove](https://man7.org/linux/man-pages/man3/memmove.3.html).

Прототип функции memcpy:
```c
void *memcpy(void dest[restrict .n], const void src[restrict .n], size_t n);
```
Прототип функции memmove:
```c
void *memmove(void dest[.n], const void src[.n], size_t n);
```

При использовании `memcpy` использовать пересекающиеся области памяти **нельзя**, но используя
`memmove` это делать **можно**:

```c
#include <string.h>

int main() {

    char src[] = "Hello, world!";
    char dest[20];

    /* Копируем строку в dest (в непересекающуюся область памяти) */
    memcpy(dest, src, strlen(src) + 1);  

    /* Перемещаем "world!" на место "Hello, " используя одну и туже область памяти*/
    memmove(src + 7, src, 7); 

    return 0;
}
```

## Заключение
В заключение можно привести пример прототипа объявления переменной:
```terminal
[квалификатор][класс хранения] тип имя [ = начальное значение]
```

Где:

- [квалификатор](#квалификаторы): Может быть `const`, `volatile`, `restrict` или отсутствовать.  
- [класс хранения](#классы-памяти): Может быть `auto`, `register`, `static`, `extern` или отсутствовать.  
- [тип]({% post_url 2023-12-15-types in c %}#типы-данных): Тип данных переменной (например, `int`, `double`, `char`, `struct`, `enum`, указатель и т.д.).  
- [имя](#имена-переменных): Имя переменной, используемое для обращения к ней в коде.  
- **начальное значение**: Необязательное начальное значение переменной.  


## Полезные ссылки

### Стандарты кодирования
- [GNU Coding Standards](https://www.gnu.org/prep/standards/standards.html)
- [Linux kernel coding style](https://www.kernel.org/doc/html/v4.10/process/coding-style.html)
- [ISO/IEC 9899](https://www.open-std.org/jtc1/sc22/wg14/www/docs/n2310.pdf)
- [C++ standards](https://www.open-std.org/jtc1/sc22/wg21/docs/standards)
  
### Функции стандартной библиотеки
- [memcpy](https://man7.org/linux/man-pages/man3/memcpy.3.html)
- [memmove](https://man7.org/linux/man-pages/man3/memmove.3.html)

### Соглашение об именовании
- [Snake case](https://en.wikipedia.org/wiki/Snake_case)
- [Camel case](https://en.wikipedia.org/wiki/Camel_case)
- [Kebab case](https://en.wikipedia.org/wiki/Letter_case#Kebab_case)

### Другое
- [The C Book](https://publications.gbdirect.co.uk/c_book/)
