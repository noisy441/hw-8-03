# Домашнее задание к занятию "`GitLab`" - `Дудин Сергей Васильевич`


### Инструкция по выполнению домашнего задания

   1. Сделайте `fork` данного репозитория к себе в Github и переименуйте его по названию или номеру занятия, например, https://github.com/имя-вашего-репозитория/git-hw или  https://github.com/имя-вашего-репозитория/7-1-ansible-hw).
   2. Выполните клонирование данного репозитория к себе на ПК с помощью команды `git clone`.
   3. Выполните домашнее задание и заполните у себя локально этот файл README.md:
      - впишите вверху название занятия и вашу фамилию и имя
      - в каждом задании добавьте решение в требуемом виде (текст/код/скриншоты/ссылка)
      - для корректного добавления скриншотов воспользуйтесь [инструкцией "Как вставить скриншот в шаблон с решением](https://github.com/netology-code/sys-pattern-homework/blob/main/screen-instruction.md)
      - при оформлении используйте возможности языка разметки md (коротко об этом можно посмотреть в [инструкции  по MarkDown](https://github.com/netology-code/sys-pattern-homework/blob/main/md-instruction.md))
   4. После завершения работы над домашним заданием сделайте коммит (`git commit -m "comment"`) и отправьте его на Github (`git push origin`);
   5. Для проверки домашнего задания преподавателем в личном кабинете прикрепите и отправьте ссылку на решение в виде md-файла в вашем Github.
   6. Любые вопросы по выполнению заданий спрашивайте в чате учебной группы и/или в разделе “Вопросы по заданию” в личном кабинете.
   
Желаем успехов в выполнении домашнего задания!
   
### Дополнительные материалы, которые могут быть полезны для выполнения задания

1. [Руководство по оформлению Markdown файлов](https://gist.github.com/Jekins/2bf2d0638163f1294637#Code)

---

### Задание 1

**Что нужно сделать:**

1. Разверните GitLab локально, используя Vagrantfile и инструкцию, описанные в [этом репозитории](https://github.com/netology-code/sdvps-materials/tree/main/gitlab).   
2. Создайте новый проект и пустой репозиторий в нём.
3. Зарегистрируйте gitlab-runner для этого проекта и запустите его в режиме Docker. Раннер можно регистрировать и запускать на той же виртуальной машине, на которой запущен GitLab.

В качестве ответа в репозиторий шаблона с решением добавьте скриншоты с настройками раннера в проекте.

---

### Решение 1

```
stages:
  - test
  - build

test:
  stage: test
  image: golang:1.17
  script: 
   - go test .
  tag: netology

build:
  stage: build
  image: docker:latest
  script:
   - docker build .
  tag: netology

```

![Скриншот 1](https://github.com/noisy441/hw-8-03/blob/main/img/img1.png)`

![Скриншот 2](https://github.com/noisy441/hw-8-03/blob/main/img/img2.png)`

---

### Задание 2

**Что нужно сделать:**

1. Запушьте [репозиторий](https://github.com/netology-code/sdvps-materials/tree/main/gitlab) на GitLab, изменив origin. Это изучалось на занятии по Git.
2. Создайте .gitlab-ci.yml, описав в нём все необходимые, на ваш взгляд, этапы.

В качестве ответа в шаблон с решением добавьте: 
   
 * файл gitlab-ci.yml для своего проекта или вставьте код в соответствующее поле в шаблоне; 
 * скриншоты с успешно собранными сборками.

--- 

### Решение 2

Pipeline 

```
stages:
  - test
  - build

test:
  stage: test
  image: golang:1.16
  script: 
   - go test .
  tags:
   - netology

sonarqube-check:
  stage: test
  image:
   name: sonarsource/sonar-scanner-cli
   entrypoint: [""]
  variables:
  script:
   - sonar-scanner -Dsonar.projectKey=my-test -Dsonar.sources=. -Dsonar.host.url=http://51.250.25.32:9000 -Dsonar.login=sqp_40c70b917ff9f206401c968d056a9d60ef6e1f87
  tags:
   - netology

build:
  stage: build
  image: docker:latest
  only:
    - master
  script:
   - docker build .
  tags:
    - netology

build:
  stage: build
  image: docker:latest
  when: manual
  except:
    - master
  script:
   - docker build .
  tags:
   - netology

```

![Скриншот 3](https://github.com/noisy441/hw-8-03/blob/main/img/img3.png)`

![Скриншот 4](https://github.com/noisy441/hw-8-03/blob/main/img/img4.png)`


---
## Дополнительные задания* (со звёздочкой)

Их выполнение необязательное и не влияет на получение зачёта по домашнему заданию. Можете их решить, если хотите лучше разобраться в материале.

---

### Задание 3*

Измените CI так, чтобы:

 - этап сборки запускался сразу, не дожидаясь результатов тестов;
 - тесты запускались только при изменении файлов с расширением *.go.

В качестве ответа добавьте в шаблон с решением файл gitlab-ci.yml своего проекта или вставьте код в соответсвующее поле в шаблоне.

--- 

### Решение 3

Блок отвечающий за сборку при любом статусе предыдущих стадий

```
build:
  stage: build
  image: docker:latest
  when: on_any_status
  except:
    - master
  script:
   - docker build .
  tags:
   - netology
```

Блок отвечающий за сборку при успешном завершении предыдущих шагов а параметр - "**/*.go" определяет, что блок буде запущен только при инзменении файлов *.go 

```
build:
  stage: build
  image: docker:latest
  when: on_success
  only:
    changes:
      - "**/*.go"
  except:
    - master
  script:
    - docker build .
  tags:
    - netology
```
---
