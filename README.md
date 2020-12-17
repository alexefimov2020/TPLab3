# TPLab3

### Лабораторная работа № 3

Добавьте в свой предыдущий проект возможность сохранения состояния в виде периодического сохранения либо в виде функций импорта/экспорта. Выбранный формат для сериализации должен иметь схему. В проекте должен присутствовать код валидации данных, либо в самой программе при загрузке данных, либо в юнит-тестах, проверяющих корректность сохранения состояния.



Схема:

```python
schema = {
    "type" : "object",
    "properties" : {
        "username" : {"type" : "string"},
        "message" : {"type" : "array", "items": {
            "type" : "string"
        }},
        "duration" : {"type" : "number"},
        "quit" : {"type" : "boolean"}
    }
}
```

Был изменен метод send предыдущей работы. После формирования сообщения для отправки, это сообщение сохраняется в формате json в файл, после чего из этого файла считывается с последующей валидацией данных в соответствии со схемой.

```python
    def send(self):
        if self.ready:
            message = model.Message(username=self.username, message=self.frames, duration=self.dur, quit=False)
            with open('data.txt', 'w') as outfile:
                my_details = {
                    'username': self.username,
                    'message': self.frames,
                    'duration': self.dur,
                    'quit': False,
                }
                json.dump(my_details, outfile)
            try:
                with open('data.txt') as outfile:
                    data = json.load(outfile)
                validate(data, schema)
                self.ui.show_message('message saved into data.txt and readed succesfuly')
            except jsonschema.exceptions.ValidationError as ve:
                self.ui.show_message('message into data.txt failed')
            try:
                self.sock.sendall(message.marshal())
                self.ui.show_message('Message sended!')
            except (ConnectionAbortedError, ConnectionResetError):
                if not self.closing:
                    self.ui.alert(messages.ERROR, messages.CONNECTION_ERROR)
```

