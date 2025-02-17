---
title: Про сброс паролей в Linux 
date: 2025-02-17 00:00:00 +0300
categories: [linux]
tags: [linux, os, password]
pin: false
author: spybull
math: true
---


Один из способов сбросить **root** пароль - это отредактировать в **grub** параметры загрузки и добавить в конец строки начинающейся на '**linux**', инструкцию **rd.break**, которая прерывает процесс загрузки.

![rd_break_example](/assets/img/linux-reset-root-password/grub_rdbreak.png)
_установка флага rd.break_

После нажатия **Ctrl-x**, чтобы загрузиться, система просто зависает и ничего не происходит.  
ОС на которой я сбрасывал пароль - **Oracle Linux 9**.
![boot_freeze](/assets/img/linux-reset-root-password/boot_freeze.png)
_зависание при загрузке_

Немного покопавшись я выяснил, что проблема кроется в строке **console=ttyS0m115200n8** и если ее убрать, то **rd.break** отрабатывает нормально. Вместо **rd.break** можно использовать **init=/bin/bash**, но строку с **console=** все равно придется удалить.

## Процесс сброса root пароля
Сброс пароля можно осуществить несколькими способами. Например через параметр **rd.break** или через параметр **init=/bin/bash**. Возможно существует еще каки-то способы, но этих двух вполне достаточно.

## Сброс через rd.break
### Выбор подходящего ядра
Загружаемся в систему, выбираем нужное ядро из списка доступных и нажимаем 'e'(edit) для редактирования параметров загрузки ядра.
![choose_kernel](/assets/img/linux-reset-root-password/choose_kernel.png)
_выбор ядра для редактирования параметров_

### Редактирование параметров ядра
Добавляем необходимые параметры в конец строки **linux...**
![add_rdbreak](/assets/img/linux-reset-root-password/add_rdbreak.png)
_добавляем опцию rd.break_

Нажимаем **Ctrl-x** и загружаем систему для дальнейшего сброса

### Сброс пароля
Процесс сброса в командах:
```shell
switch_root:/# mount -o remount,rw /sysroot  
switch_root:/# chroot /sysroot  
sh-5.1# passwd  
Changing password for user root.  
New password:  
Retype new password:  
passwd: all authentication tokens updated successfully.  
sh-5.1# touch /.autorelabel  
sh-5.1# exit  
switch_root:/# reboot  
```  

## Сброс через init=/bin/bash
### Выбор подходящего ядра
Загружаемся в систему, выбираем нужное ядро из списка доступных и нажимаем 'e'(edit) для редактирования параметров загрузки ядра.
![choose_kernel](/assets/img/linux-reset-root-password/choose_kernel.png)
_выбор ядра для редактирования параметров_

### Редактирование параметров ядра
Далее добавляем необходимые параметры в конец строки **linux...**
![add_rdbreak](/assets/img/linux-reset-root-password/add_rdbreak.png)
_добавляем опцию rd.break_

Нажимаем **Ctrl-x** и загружаем систему для дальнейшего сброса

### Сброс пароля
В данном случае различия минимальны:

```shell
bash-5.1:# mount -o remount,rw /  
bash-5.1:# passwd  
Changing password for user root.  
New password:  
Retype new password:  
passwd: all authentication tokens updated successfully.  
bash-5.1:# touch /.autorelabel  
bash-5.1:# /usr/sbin/reboot -f  
```

## autorelabel
После первого запуска системы, если включен **SELinux**, будет выполнен **autorelabeling** (т.к мы создали файл **/.autorelabel**).
Система автоматически перезагрузится два раза. При первом запуске вся файловая системя будет автоматически перемаркирована используя 
**fixfiles -F restore**.

## Ссылки
- [Про selinux и autorelabel](https://www.man7.org/linux/man-pages/man5/selinux_config.5.html)
- [Сброс пароля RHEL9](https://learn.redhat.com/t5/Platform-Linux/Reset-forgotten-root-password-in-RHEL9/m-p/34115#M1884)
- [Сброс пароля RHEL/CentOS ver 7](https://www.redhat.com/en/blog/recover-root-passwd)
- [Еще про сброс пароля](https://gcore.com/learning/how-to-reset-password-in-linux/)

