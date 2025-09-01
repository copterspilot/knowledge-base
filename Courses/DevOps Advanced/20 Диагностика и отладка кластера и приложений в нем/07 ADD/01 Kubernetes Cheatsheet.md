#### **1. Первичный осмотр кластера**

```bash
# Получить список всех ресурсов определенного типа во всех namespace'ах
kubectl get pods -A
kubectl get nodes
kubectl get services -A

# Получить список ресурсов в текущем namespace
kubectl get pods
kubectl get deployments

# Получить подробную информацию о ресурсе
kubectl describe pod <имя_пода>
kubectl describe node <имя_ноды>

# Посмотреть события кластера (очень важно для диагностики!)
kubectl get events --sort-by=.metadata.creationTimestamp
kubectl get events -A --sort-by=.metadata.creationTimestamp

# Посмотреть потребление ресурсов
kubectl top node
kubectl top pod
kubectl top pod <имя_пода> --containers
```

---

#### **2. Работа с логами**

```bash
# Посмотреть логи контейнера в поде
kubectl logs <имя_пода>
kubectl logs <имя_пода> -c <имя_контейнера>  # Для мультиконтейнерных подов

# Посмотреть логи предыдущего экземпляра контейнера (ключево для CrashLoopBackOff)
kubectl logs <имя_пода> --previous

# Следить за логами в реальном времени (follow)
kubectl logs <имя_пода> --follow

# Посмотреть последние N строк логов
kubectl logs <имя_пода> --tail=50
```

---

#### **3. Взаимодействие с контейнерами (exec, attach, cp)**

```bash
# Выполнить команду в контейнере
kubectl exec <имя_пода> -- env
kubectl exec <имя_пода> -- ps aux

# Открыть интерактивную оболочку (sh, bash)
kubectl exec -it <имя_пода> -- sh
kubectl exec -it <имя_пода> -- bash

# Подключиться к stdin/stdout основного процесса в контейнере
kubectl attach <имя_пода> -it

# Скопировать файл из пода на локальную машину
kubectl cp <namespace>/<имя_пода>:/путь/в_контейнере/файл ./локальный_файл

# Скопировать файл с локальной машины в под
kubectl cp ./локальный_файл <namespace>/<имя_пода>:/путь/в_контейнере/
```

---

#### **4. Удаленная отладка и диагностика**

```bash
# Пробросить порт с пода на локальную машину
kubectl port-forward pod/<имя_пода> 8080:80
kubectl port-forward service/<имя_сервиса> 8080:80

# Отладка в контейнерах без sh/bash (distroless, scratch)
# Убедитесь, что EphemeralContainers включены в API-сервере
kubectl debug -it <имя_пода> --image=nicolaka/netshoot -- sh

# Получить манифест "живого" объекта из кластера
kubectl get deployment <имя> -o yaml
kubectl get pod <имя> -o json
```

---

#### **5. Работа с конфигурацией и развертыванием**

```bash
# Применить манифест
kubectl apply -f манифест.yaml

# Проверить статус деплоя
kubectl rollout status deployment/<имя_деплоя>

# Откатить деплой
kubectl rollout undo deployment/<имя_деплоя>

# Посмотреть историю ревизий
kubectl rollout history deployment/<имя_деплоя>

# Обновить образ в деплое (быстрый способ)
kubectl set image deployment/<имя_деплоя> <контейнер>=<новый_образ:тег>
```

---

#### **6. Работа с namespace'ами**

```bash
# Посмотреть все namespace'ы
kubectl get namespaces

# Установить контекст (namespace) по умолчанию
kubectl config set-context --current --namespace=<имя_namespace>

# Создать namespace
kubectl create namespace <имя>
```

---

#### **7. Полезные команды и алиасы**

```bash
# Получить справку
kubectl --help
kubectl <команда> --help

# Получить информацию о кластере
kubectl cluster-info

# Проверить, кто вы и куда подключены
kubectl config current-context

# Установка плагинов через krew
kubectl krew install <имя_плагина>
kubectl krew list
kubectl krew upgrade
```

**Полезные алиасы для `~/.bashrc` или `~/.zshrc`:**
```bash
alias k='kubectl'
alias kgp='kubectl get pods'
alias kdp='kubectl describe pod'
alias klo='kubectl logs'
alias kex='kubectl exec -it'
```

---

#### **8. Чек-лист диагностики `CrashLoopBackOff`

1.  `kubectl get pods -A` → Найти проблемный под.
2.  `kubectl describe pod <имя>` → Проверить секцию `Events`.
3.  `kubectl logs <имя>` → Просмотреть логи.
4.  `kubectl logs <имя> --previous` → **Ключевой шаг!** Посмотреть логи упавшего контейнера.
5.  `kubectl debug -it <имя> --image=nicolaka/netshoot -- sh` → Интерактивная отладка.

---
