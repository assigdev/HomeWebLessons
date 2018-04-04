## Homework-6

### Задание:

Создать две странички с двумя формами. Первая форма - отзыв , вторая заявка.

##### Форма отзыв:

    Отзыв не должен храниться в модели (то есть это обычный Form)
    Отзыв должен отправится на почту администрации.

поля:

    ФИО
    email
    Текст отзыва
   
##### Форма заявка:

    Отзыв должен храниться в модели (то есть это ModelForm)
    На почту пользователя, отправившего отзыв, должно прийти сообщение о том, что заявка принята

поля:  

    ФИО
    возраст
    email
    

### Подсказки:

для того чтобы отправлять сообщения на почту необходимо добавить следующие насройки
    
    EMAIL_USE_SSL = True
    EMAIL_HOST = 'smtp.yandex.ru'
    EMAIL_PORT = 465
    EMAIL_HOST_USER = 'noreply@bytehouse.ru'     # впишите свою яндекс почту
    EMAIL_HOST_PASSWORD = 'password'     # пароль от вашей яндекс почты
    DEFAULT_FROM_EMAIL = EMAIL_HOST_USER
    ADMINS = [('John', 'john@example.com'), ('Mary', 'mary@example.com')]  # напишите свою почту и имя
    

Чтоб отправить сообщение на почту админам добавьте этот метод к форме Отзыв:

        def send_mail(self):
            name = self.cleaned_data.get('name')
            email = self.cleaned_data.get('email')
            text = self.cleaned_data.get('text')
            subject = 'Новый отзыв'
            message = '''Отзыв от {0}:
            {1}
            email: {2}'''.format(name, text, email)
            mail_admins(subject, message,)
            
Потом во вьюшке вызываем этот метод если форма валидна: form.send_mail()

Для отправки сообщения пользователю, который оставил заявку , к форме для Заявки переопределите метод save() - Так как это ModelForm , у такой формы есть метод save() и мы его просто переопределим:
    
       def save(self, commit=True):
            name = self.cleaned_data.get('name')
            email = self.cleaned_data.get('email')
            subject = 'Ваша заявка принята'
            message = "{0} Заявка принята на рассмотрение".format(name)
            send_mail(subject, message, from_email=DEFAULT_FROM_EMAIL, recipient_list=[email])
            return super().save(commit)

Также вам понадобятся эти импорты:

    from django.core.mail import send_mail, mail_admins
    from django10.settings import DEFAULT_FROM_EMAIL
