**1. Сборка Docker образа**
   
    docker build -t todo-service:local .
   
**2. Создаем именованный том Docker для постоянного хранения данных**

    docker volume create todo_date
   
**3. Запускаем**
   
    docker run --rm -p 8000:80 -v todo_data:/app/data --name todo_service todo-service:local
   
**4. Можем сразу открыть страничку с документацией**
   
    start http://localhost:8000/docs
