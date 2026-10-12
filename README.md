# Домашнее задание к занятию 4 «Работа с roles» - Некрасов Александр

## Подготовка к выполнению

1. * Необязательно. Познакомьтесь с [LightHouse](https://youtu.be/ymlrNlaHzIY?t=929).
2. Создайте два пустых публичных репозитория в любом своём проекте: vector-role и lighthouse-role.
3. Добавьте публичную часть своего ключа к своему профилю на GitHub.

## Основная часть

Ваша цель — разбить ваш playbook на отдельные roles. 

Задача — сделать roles для ClickHouse, Vector и LightHouse и написать playbook для использования этих ролей. 

Ожидаемый результат — существуют три ваших репозитория: два с roles и один с playbook.

**Что нужно сделать**

1. Создайте в старой версии playbook файл `requirements.yml` и заполните его содержимым:

   ```yaml
   ---
     - src: git@github.com:AlexeySetevoi/ansible-clickhouse.git
       scm: git
       version: "1.13"
       name: clickhouse 
   ```

2. При помощи `ansible-galaxy` скачайте себе эту роль.
3. Создайте новый каталог с ролью при помощи `ansible-galaxy role init vector-role`.
4. На основе tasks из старого playbook заполните новую role. Разнесите переменные между `vars` и `default`. 
5. Перенести нужные шаблоны конфигов в `templates`.
6. Опишите в `README.md` обе роли и их параметры. Пример качественной документации ansible role [по ссылке](https://github.com/cloudalchemy/ansible-prometheus).
7. Повторите шаги 3–6 для LightHouse. Помните, что одна роль должна настраивать один продукт.
8. Выложите все roles в репозитории. Проставьте теги, используя семантическую нумерацию. Добавьте roles в `requirements.yml` в playbook.
9. Переработайте playbook на использование roles. Не забудьте про зависимости LightHouse и возможности совмещения `roles` с `tasks`.
10. Выложите playbook в репозиторий.
11. В ответе дайте ссылки на оба репозитория с roles и одну ссылку на репозиторий с playbook.

---

## Решение.
Playbook для развёртывания трёх сервисов (ClickHouse, Vector, LightHouse) с использованием **Ansible Roles**.

Проект разделён на три репозитория:

| Репозиторий | Описание |
|-------------|----------|
| [vector-role](https://github.com/AlexandrNekrasov/vector-role) | Роль для установки и настройки Vector |
| [lighthouse-role](https://github.com/AlexandrNekrasov/lighthouse-role) | Роль для установки и настройки LightHouse + Nginx |
| **ansible-04-roles** | Playbook, использующий роли |

### Структура

    ansible-04-roles/
    ├── .gitignore
    ├── requirements.yml          # Список ролей для скачивания
    └── playbook/
        ├── ansible.cfg           # Конфиг (roles_path)
        ├── .ansible-lint         # Исключение clickhouse из линта
        ├── site.yml              # Playbook с ролями
        └── inventory/
            └── prod.yml          # Inventory

### Роли

### 1. clickhouse

Готовая роль из Ansible Galaxy: [AlexeySetevoi/ansible-clickhouse](https://github.com/AlexeySetevoi/ansible-clickhouse)

- **Версия:** `1.13`
- **Источник:** `git@github.com:AlexeySetevoi/ansible-clickhouse.git`

### 2. vector-role

Своя роль для установки Vector.

- **Версия:** `1.0.0`
- **Репозиторий:** [AlexandrNekrasov/vector-role](https://github.com/AlexandrNekrasov/vector-role)
- **Что делает:**
  - Скачивает дистрибутив Vector
  - Распаковывает в `/opt/vector`
  - Создаёт `/var/lib/vector`
  - Деплоит конфиг `/etc/vector/vector.toml` через Jinja2
  - Запускает Vector

### 3. lighthouse-role

Своя роль для установки LightHouse + Nginx.

- **Версия:** `1.0.0`
- **Репозиторий:** [AlexandrNekrasov/lighthouse-role](https://github.com/AlexandrNekrasov/lighthouse-role)
- **Что делает:**
  - Устанавливает Nginx и unzip
  - Скачивает статику LightHouse с GitHub
  - Распаковывает в `/var/www/lighthouse`
  - Настраивает Nginx через Jinja2
  - Запускает Nginx

### Requirements

- Ansible 2.10+
- Ubuntu 20.04 / 22.04
- Доступ к GitHub по SSH (для скачивания ролей)

### Установка ролей

Из корня проекта выполнить:

```
ansible-galaxy install -r requirements.yml -p roles/
```
### Запуск
```cd playbook```

Проверка синтаксиса

```ansible-playbook -i inventory/prod.yml site.yml --syntax-check```

Линтинг

```ansible-lint site.yml```

Dry-run

```ansible-playbook -i inventory/prod.yml site.yml --check```

Реальный запуск

```ansible-playbook -i inventory/prod.yml site.yml```

С diff

```ansible-playbook -i inventory/prod.yml site.yml --diff```

### Ссылки:
[vector-role](https://github.com/AlexandrNekrasov/vector-role)

[lighthouse-role](https://github.com/AlexandrNekrasov/lighthouse-role)
