# ОТЧЕТ по лабораторной работе №1

**«Обзор Google Cloud и исследование основных сервисов»**

**Выполнил:** студент \[Ваша Фамилия И.О.]  
**Группа:** \[Ваша группа]  
**Дата выполнения:** 8 ноября 2026 г.

## 1\. Цель работы

Ознакомиться с основными возможностями и преимуществами облачной платформы Google Cloud, получить практические навыки работы с сервисами IAM (Identity and Access Management), Compute Engine и Cloud Storage.

## 2\. Ход работы

### 2.1. Создание сервисного аккаунта

Был создан сервисный аккаунт с именем `novikov-aa-lab1` для управления доступом к ресурсам Google Cloud. Сервисному аккаунту была назначена роль Storage Admin (Администратор хранилища), которая предоставляет полный контроль над ресурсами Cloud Storage.

**Параметры сервисного аккаунта**

|Параметр|Значение|
|-|-|
|Имя|`novikov-aa-lab1`|
|Email|`novikov-aa-lab1@cloud-platforms-as-the-basis.iam.gserviceaccount.com`|
|Unique ID|`113375894780597845301`|
|Статус|`Enabled`|

!\[Скриншот 1: Параметры и статус сервисного аккаунта novikov-aa-lab1](images/laba1\_1.png)

### 2.2. Создание виртуальной машины

Создан экземпляр Compute Engine со следующими параметрами.

**Конфигурация ВМ**

|Параметр|Значение|
|-|-|
|Имя|`novikov-aa-lab1`|
|Machine type|`e2-micro` (2 vCPU, 1 ГБ памяти)|
|Provisioning model|Spot VM (прерываемая ВМ)|
|Service account|`novikov-aa-lab1@cloud-platforms-as-the-basis.iam.gserviceaccount.com`|

!\[Скриншот 2: Конфигурация виртуальной машины novikov-aa-lab1 с типом e2-micro](images/laba1\_2.png)

### 2.3. Работа с Cloud Storage

С помощью утилиты `gcloud storage` выполнено копирование файлов из учебного бакета `lab1-bucket-itmo` на виртуальную машину.

**Выполненные команды**

```bash
# Создание директории для файлов
mkdir \~/lab1\_files
cd \~/lab1\_files

# Просмотр объектов в бакете
gcloud storage ls gs://lab1-bucket-itmo/

# Копирование файлов из бакета
gcloud storage cp gs://lab1-bucket-itmo/\* .

# Просмотр скопированных файлов
ls -lah
```

**Результат выполнения**

```text
Copying gs://lab1-bucket-itmo/pic1.jpg to file:///./pic1.jpg
Copying gs://lab1-bucket-itmo/pic2.jpg to file:///./pic2.jpg
Copying gs://lab1-bucket-itmo/pic3.jpeg to file:///./pic3.jpeg
Completed files 3/3 | 1.5MiB/1.5MiB
Average throughput: 6.9MiB/s
```

!\[Скриншот 3: Терминал с успешным копированием трёх файлов](images/laba1\_3.png)

**Вывод.** При наличии роли Storage Admin сервисный аккаунт имеет полный доступ к операциям чтения и записи в Cloud Storage.

### 2.4. Изменение прав доступа (IAM)

Роль сервисного аккаунта была изменена с Storage Admin на Compute Viewer (Просмотрщик Compute Engine).

**Характеристики роли Compute Viewer**

* Read-only access to get and list information about all Compute Engine resources.
* Allows getting and listing information about disks, images, and snapshots.
* Does NOT allow reading the data stored in Cloud Storage.

!\[Скриншот 4: Выбор роли Compute Viewer для сервисного аккаунта](images/laba1\_4.png)

### 2.5. Проверка применения новых прав

После изменения роли выполнены команды для проверки ограничений доступа.

#### Проверка 1. Копирование файлов

```bash
gsutil cp gs://lab1-bucket-itmo/\* .
```

**Результат.** Скопированы три объекта общим объёмом 1,5 МиБ.

#### Проверка 2. Получение IAM-политики бакета

```bash
gcloud storage buckets get-iam-policy gs://lab1-bucket-itmo
```

**Результат**

```text
ERROR: (gcloud.storage.buckets.get-iam-policy)
\[novikov-aa-lab1@cloud-platforms-as-the-basis.iam.gserviceaccount.com]
does not have permission to access b instance \[lab1-bucket-itmo]
(or it may not exist): Provided scope(s) are not authorized.
```

!\[Скриншот 5: Успешное копирование через gsutil и отказ при запросе IAM-политики бакета](images/laba1\_5.png)

**Анализ результатов**

Копирование объектов прошло успешно, а запрос IAM-политики бакета завершился ошибкой. В сообщении указано ограничение областей доступа (scopes). По этим результатам нельзя определить, какие разрешения позволили скопировать файлы и когда вступило в силу изменение роли.

### 2.6. Удаление ресурсов

После завершения работы все созданные ресурсы были удалены для предотвращения несанкционированного использования и экономии квот проекта.

**Удаленные ресурсы**

* **Виртуальная машина:** `novikov-aa-lab1`
* **Сервисный аккаунт:** `novikov-aa-lab1@cloud-platforms-as-the-basis.iam.gserviceaccount.com`



## 3\. Выводы

В ходе выполнения лабораторной работы были получены следующие практические навыки.

* **Управление сервисными аккаунтами**

  * Создание и настройка сервисных аккаунтов.
  * Назначение ролей на уровне проекта.
  * Понимание разницы между ролями разных уровней.
* **Работа с Compute Engine**

  * Создание виртуальных машин различных типов.
  * Настройка прерываемых (Spot) ВМ.
  * Привязка сервисных аккаунтов к ВМ.
* **Взаимодействие с Cloud Storage**

  * Использование утилит `gcloud storage` и `gsutil`.
  * Копирование объектов из бакетов.
  * Понимание иерархии прав доступа к хранилищу.
* **Система IAM (Identity and Access Management)**

  * Назначение и изменение ролей.
  * Принцип наименьших привилегий (Least Privilege).
  * Различие между ролями Storage Admin и Compute Viewer.
* **Управление ресурсами**

  * Важность очистки ресурсов после завершения работы.
  * Контроль квот и расходов в Google Cloud.

**Основные выводы по безопасности**

* Роль Storage Admin предоставляет полный контроль над Cloud Storage (чтение, запись, удаление, управление политиками).
* Роль Compute Viewer позволяет только просматривать информацию о ресурсах Compute Engine, но не дает доступа к данным в хранилище.
* Система IAM Google Cloud обеспечивает гибкое и безопасное управление доступом на основе ролей (RBAC — Role-Based Access Control).

## 

