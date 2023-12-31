---
title: Введение в язык программирования Си
date: 2023-12-06 00:00:00 +0300
categories: [Programming, С]
tags: [c]
pin: true
author: spybull
---

## Настройка окружения

Все настройки и разработка будут производится под ОС **Fedora 39**.  
Для начала нужно установить необходимые утилиты, а именно компилятор, линкер и прочее для компиляции и сборки программ. Основная группа, в которой содержатся все необходимые утилиты, называется  
'**C Development Tools and Libraries**':
```shell
dnf groupinstall 'C Development Tools and Libraries' -y
```

Для тех кто будет использовать **vim** при разработке, сделаем небольшие настройки.

Сперва нужно установить **vim**:
```shell
dnf install vim -y
```

Выдумывать ничего сами не будем, а также накручивать что-то вроде плагинов [YouCompleteMe](https://github.com/ycm-core/YouCompleteMe) тоже не будем, для тех кто хочет, может самостоятельно его для себя настроить. А мы же просто возьмем готовый конфиг отсюда [.vimrc](https://gist.githubusercontent.com/rocarvaj/2513367/raw/626199ba537941208a479a0e7c93b9ed6fd42ac9/.vimrc) и сохраним его в **~/.vimrc**.

## Первая программа

В целом этого достаточно для написания первого 'hello, world':
```c
#include <stdio.h>

int main()
{
    printf("hello, world\n");
    return 0;
}
```

Сохраняем код в файл **hello.c** и компилируем с помощью компилятора **gcc**:
```shell
gcc hello.c
```

На выходе мы получаем исполняемый файл, который по дефолту именуются как **a.out**:
```terminal
root@fedora-develop:~/C/intro# ls
a.out  hello.c
root@fedora-develop:~/C/intro# file a.out 
a.out: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=289bd952ad072974c493e257e5ccb62a1b9a41f7, for GNU/Linux 3.2.0, not stripped
```

Имя выходного исполняемого файла можно переопределить через параметр **-o**:
```terminal
gcc hello.c -o hello
```

Файл сразу должен быть помечен на исполнение, поэтому его можно сразу же запустить:
```terminal
root@fedora-develop:~/C/intro# ./a.out 
hello, world
root@fedora-develop:~/C/intro# ./hello 
hello, world
```

На самом деле в данном процессе происходит не только компиляция, но также и другие этапы:

1) [Препроцессинг](#препроцессинг) (Preprocessing)  
2) [Компиляция](#компиляция) (Compilation)  
3) [Сборка](#сборка) (Assembling)  
4) [Линковка](#линковка) (Linking)  

## Препроцессинг

Первая стадия компиляции называется препроцессингом.  
На данном этапе происходит обработка команд которые начинаются с '#' - их и называют "команды препроцессора". Это макроопределения (макросы) со своей семантикой и синтаксисом для языка.  

Например, такие команды позволяют подключать заголовочные файлы с функциями, сообщать разные параметры компилятору, выполнять какие-то подготовительные условия перед компиляцией или записывать маленькие функции внутри макроопределений.

Препроцессор также выполняет первоначальную обработку данных строк удаляя строки заканчивающиеся символом "\\" и удаляет комментарии, кстати комментарии в Си можно написать вот так:
```c
/* Это комментарий!
   При том многострочный */
```

В С++ например есть еще и однострочные комментарии:
```c
// Это однострочный комментарий
```

**Данный вид однострочного комментария также есть и в Си, но начиная со стандарта С99**


C помощью **gcc**  и дополнительного флага "-E", мы можем получить отдельно данный этап и посмотреть  
что же там происходит внутри нашего примера при препроцессинге:

```terminal
gcc -E hello.c > hello.i
```

Файл **hello.i** получился довольно таки объемным, это произошло,
потому что препроцессор "раскрыл" содержимое заголовочного файла **stdio.h** в котором находятся другие заголовки,
а также прототипы-функций для работы с буферизированным вводом-выводом, что мы и использовали для вывода нашей строки **printf()**.

Итог:
- Препроцессор обрабатывает наш исходный код до фактической компиляции;
- Препроцессор раскрывает макросы, вставляет содержимое файлов через дерективы `#include` и другие;
- Получить содержимое препроцессора в **gcc** можно через флаг **-E**.

## Компиляция

На данном этапе происходит трансляция кода препроцессора в ассемблерные инструкции, специфичные для данного процессора и архитектуры.
Данный этап также позволяет иметь в Си коде встроенный ассемблер, потому что такой этап есть, а значит можно прямо в самом Си писать на ассемблере,
никто этого не запрещает, хотя и переносимость будет страдать.

Эту стадию также можно запустить отдельно, чтобы посмотреть на ассемблерный код, с помощью флага '**-S**':
```terminal
gcc -S hello.c
```

На выходе мы получаем файл с расширением hello.s, в котором содержится ассемблерный код:
```shell
	.file	"hello.c"
	.text
	.section	.rodata
.LC0:
	.string	"hello, world"
	.text
	.globl	main
	.type	main, @function
main:
.LFB0:
	.cfi_startproc
	pushq	%rbp
	.cfi_def_cfa_offset 16
	.cfi_offset 6, -16
	movq	%rsp, %rbp
	.cfi_def_cfa_register 6
	movl	$.LC0, %edi
	call	puts
	movl	$0, %eax
	popq	%rbp
	.cfi_def_cfa 7, 8
	ret
	.cfi_endproc
.LFE0:
	.size	main, .-main
	.ident	"GCC: (GNU) 13.2.1 20231011 (Red Hat 13.2.1-4)"
	.section	.note.GNU-stack,"",@progbits
```

В дальнейшем мы рассмотрим форматы **ELF/EXE** и ассемблерный код более подробно.

Итог:
- Компиляция осуществляет перевод исходного кода на высокоуровневом языке в промежуточный в более низкоуровневый язык более специфичный для данной архитектуры и процессора.
- Также некоторые компиляторы могут миновать данный этап генерируя машинный код напрямую избегая промежуточной стадии с генерацией ассемблерного кода
- Получить ассемблерный код в **gcc** можно через флаг **-S**

## Сборка

После препроцессинга и компиляции, нам необходимо преобразовать исходный код  
на языке ассемблера (ассемблер - это еще не то что понимает процессор) в исполняемый код  
для нашего процессора, который будет уже им обрабатываться.  
На этом этапе происходит преобразование ассемблерных инструкций в так называемый **объектный код**. 

Для сохранения такого кода используется флаг '**-c**':
```terminal
gcc -c hello.c 
```

На выходе мы получаем объектный файл **hello.o**, который содержит бинарные данные,
и посмотреть его можно только специальными утилитами, например через **objdump** или через **hexdump**:

```terminal
root@fedora-develop:~/C/intro# objdump -d hello.o
hello.o:     file format elf64-x86-64
Disassembly of section .text:
0000000000000000 <main>:
   0:	55                   	push   %rbp
   1:	48 89 e5             	mov    %rsp,%rbp
   4:	bf 00 00 00 00       	mov    $0x0,%edi
   9:	e8 00 00 00 00       	call   e <main+0xe>
   e:	b8 00 00 00 00       	mov    $0x0,%eax
  13:	5d                   	pop    %rbp
  14:	c3                   	ret

root@fedora-develop:~/C/intro# hexdump hello.o 
0000000 457f 464c 0102 0001 0000 0000 0000 0000
0000010 0001 003e 0001 0000 0000 0000 0000 0000
0000020 0000 0000 0000 0000 0268 0000 0000 0000
0000030 0000 0000 0040 0000 0000 0040 000e 000d
0000040 4855 e589 00bf 0000 e800 0000 0000 00b8
0000050 0000 5d00 68c3 6c65 6f6c 202c 6f77 6c72
...
```

Итог:
- При сборке мы получаем объектный файл с бинарными данными для процессора
- Для просмотра такого кода можно использовать **objdump**
- Получить объектный файл в **gcc** можно через флаг **-c**


## Линковка

Линковкой занимается линковщик или линкер.  
Он объединяет объектные файлы в один и на выходе мы и получаем наш исполняемый файл.

В его задачу входит следующее:

1) Создать таблицу символов;  
2) Разрешить символы функций (символы это имена функций например);  
3) Взять все необходимые объектные файлы и собрать их в один исполняемый файл.    

Для линковки нашего объектного файла в исполняемый запустим **gcc** без параметров:
```terminal
root@fedora-develop:~/C/intro# gcc hello.o -o hello
root@fedora-develop:~/C/intro# ./hello 
hello, world
root@fedora-develop:~/C/intro# file hello
hello: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=289bd952ad072974c493e257e5ccb62a1b9a41f7, for GNU/Linux 3.2.0, not stripped
root@fedora-develop:~/C/intro# 
```

Теперь посмотрим, что у нас внутри нашего исполняемого файла с помощью команды **nm**:

```terminal
root@fedora-develop:~/C/intro# nm hello
000000000040039c r __abi_tag
000000000040400c B __bss_start
000000000040400c b completed.0
0000000000404008 D __data_start
0000000000404008 W data_start
0000000000401080 t deregister_tm_clones
0000000000401070 T _dl_relocate_static_pie
00000000004010f0 t __do_global_dtors_aux
0000000000403e00 d __do_global_dtors_aux_fini_array_entry
0000000000402008 R __dso_handle
0000000000403e08 d _DYNAMIC
000000000040400c D _edata
0000000000404010 B _end
000000000040113c T _fini
0000000000401120 t frame_dummy
0000000000403df8 d __frame_dummy_init_array_entry
00000000004020d8 r __FRAME_END__
0000000000403fe8 d _GLOBAL_OFFSET_TABLE_
                 w __gmon_start__
0000000000402020 r __GNU_EH_FRAME_HDR
0000000000401000 T _init
0000000000402000 R _IO_stdin_used
                 U __libc_start_main@GLIBC_2.34
0000000000401126 T main
                 U puts@GLIBC_2.2.5
00000000004010b0 t register_tm_clones
0000000000401040 T _start
0000000000404010 D __TMC_END__
```

Команда **nm** используется как раз таки для вывода тех самых символов (symbols), о которых я говорил ранее, из объектных файлов или исполняемых.

**первый столбец** - это адрес символа, адреса указывают местоположение этих символов в памяти;  
**второй столбец** - это тип символа. Например 'T' означает текстовый код, 'D' - данные и т.д  
**третий столбец** - это имя символа  

Из нашего вывода, например:  
`000000000040400c B __bss_start` - Это начало сексии BSS в памяти  
`0000000000401040 T _start` - Это начальная точка исполнения программы  

и так далее, более подробно о секциях мы поговорим далее.  
Пока нужно просто понять какие этапы существуют и как примерно они выглядят.