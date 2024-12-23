
Проверка контейнеров на наличие уязвимостей является важной задачей для обеспечения безопасности ваших приложений и инфраструктуры. Docker Scout - это инструмент, который помогает автоматизировать процесс сканирования контейнеров на наличие известных уязвимостей.

## Установка Docker Scout

1. Переходим в раздел [релизов Docker Scout](https://github.com/docker/scout-cli/releases/) и выбираем последнюю версию.
2. Выбираем архив согласно архитектуре процессора (в примере amd64) и скачиваем его:
   ```
   wget -O docker-scout.tar https://github.com/docker/scout-cli/releases/download/v1.4.1/docker-scout_1.4.1_linux_amd64.tar.gz
   ```
3. Разархивируем архив:
   ```
   tar -xvf docker-scout.tar
   ```
4. Создаем каталог `~/.docker/cli-plugins` и переносим `docker-scout` в данный каталог:
   ```
   mv docker-scout ~/.docker/cli-plugins/
   ```
5. Для получения справки выполним команду:
   ```
   docker-scout
   ```

## Использование основных команд Docker Scout

### Docker scout quickview
