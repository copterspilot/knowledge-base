Отлично! Ниже приведена **диаграмма деятельности (activity diagram)** в формате **PlantUML**, которая показывает **цепочку действий при применении политик Istio Service Mesh**.

---

## 🎯 Сценарий

- **Сервис A** вызывает **Сервис B**.
- Istio проверяет:
  - **mTLS**,
  - **JWT**,
  - **AuthorizationPolicy**.

---

## 🖼️ PlantUML: Диаграмма деятельности (Activity Diagram) для Istio

```plantuml
@startuml
skinparam backgroundColor #F9F9F9
skinparam activity {
    BackgroundColor #E1F5FE
    BorderColor #01579B
    FontSize 12
}
skinparam note {
    BackgroundColor #FFF3E0
    BorderColor #FF6F00
}

title Цепочка применения политик Istio Service Mesh

start

:Сервис A отправляет\nзапрос к Сервису B;

:Запрос перехватывается\nEnvoy Sidecar A;

:Установка mTLS-соединения\nс Envoy Sidecar B;

if (mTLS успешно установлена?) then (да)
  :Запрос поступает\nв Envoy Sidecar B;
  
  :Проверка PeerAuthentication\n(STRICT mode);
  
  if (mTLS активна?) then (да)
    :Проверка RequestAuthentication\n(валидация JWT);
    
    if (JWT валиден?) then (да)
      :Проверка AuthorizationPolicy\n(доступ по принципалу и пути);
      
      if (Доступ разрешён?) then (да)
        :Передача запроса\nв Сервис B;
        :Сервис B обрабатывает\nзапрос;
        :Ответ возвращается\nчерез Envoy B → Envoy A;
        :Сервис A получает\nответ 200 OK;
        stop
      else (нет)
        :403 Forbidden\n(политика запретила доступ);
        stop
      endif
    else (нет)
      :401 Unauthorized\n(невалидный JWT);
      stop
    endif
  else (нет)
    :403 Forbidden\n(mTLS не активна);
    stop
  endif
else (нет)
  :Ошибка установки mTLS;
  stop
endif

@enduml
```

> ---
> ---
> ---
> ---
> ---
> ---
> ---
> ---
> ---
> ---








```
note right of "Проверка PeerAuthentication\n(STRICT mode)"
  Проверяется:
  - Требование mTLS
  - Действительность сертификатов
end note

note right of "Проверка RequestAuthentication\n(валидация JWT)"
  Проверяется:
  - Наличие JWT
  - Валидность подписи
  - Issuer и audience
end note

note right of "Проверка AuthorizationPolicy\n(доступ по принципалу и пути)"
  Проверяется:
  - SPIFFE ID вызывающего
  - Метод и путь запроса
  - Соответствие политике
end note

```

---

## 📌 Что показывает диаграмма?

- **Последовательность проверок** при вызове между сервисами.
- **Условные переходы** (ветвления) на каждом этапе безопасности.
- **Результаты** — успешный вызов или одна из ошибок (401, 403).
- **Пояснения** к ключевым проверкам.

---

## 🛠 Как использовать

Вы можете:
- Вставить код в **PlantUML редактор** (например, [https://www.plantuml.com/plantuml](https://www.plantuml.com/plantuml)).
- Сгенерировать изображение и использовать в **презентации**, **документации** или **обучении**.
- Адаптировать под конкретные политики или требования.

---

Если хотите, могу:
- создать **аналогичную диаграмму для Vault**,
- объединить обе диаграммы в **одну комплексную**,
- или добавить **метрики, логирование, трассировку**.

Готов помочь!