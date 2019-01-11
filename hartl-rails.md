### Hartl notes:

Ветвь dev:
```bash
git branch -d dev
```
Отправка:
```bash
git push
```

Phusion Passenger - модуль для Apache и Nginx

Heroku (production) - см. стр. 52:
```ruby
gem 'pg'
gem 'rails_12factor'
```

Bundle:
```ruby
bundle install --without-production
```
alias:
```bash
bwp
```

```bash
heroku login
```
```bash
heroku create # alias: herc
```
```bash
git push heroku master # alias: gphm
```
Rename:
```bash
heroku rename foo
```
Open:
```bash
heroku open # alias: hero
```

Отменить изменения:
```bash
git checkout -f
```

Добавить и отправить:
```bash
git remote add orign git@...

git push -u orign --all
```
1. Ветвление
2. Редактирование
3. Фиксация
4. Слияние

```bash
alias git co = git checkout
```

Ветвление:
```bash
git co -b dev # создание ветки
git branch # список веток
```

Фиксация:
```bash
git status # gs
git commit # gc "Foo"
```

Слияние:
```bash
git checkout master
git merge dev
```

Модель данных Users + интерфейс = ресурс Users

Scaffold:
```bash
rails generate scaffold User name:string email:string

rake db:migrate
```
Посмотреть список Rake-задач:
```bash
rake -T db # по БД
rake -T # все
```
#### CRUD:

REST - передача репрезентативного состояния (архитектурный стиль разработки распределённых сетевых систем). Рой Фолдинг, 2000

- C - POST - CREATE
- R - GET - READ
- U - PATCH - UPDATE
- D - DELETE - DESTROY

CRUD - операции БД и 4 метода HTTP-запроса.

- index - /users - GET
- show - /users/1 - GET
- new - /users/new - GET
- edit - /users/edit - GET
- create - /users - POST
- update - /users/1 - PATCH
- destroy - /users/1 - DELETE

Решить, какие контроллеры и методы необходимы: структур. приложение использ. ресурсы которые могут CRUD

controller:
```ruby
def index
  @users = User.all
end
```
view:
```ruby
<% @users.each do |user| %>
  <%= user.name %>
  <%= user.email %>

  <%= link_to 'Show', user %>
  <%= link_to 'Edit', edit_user_path(user) %>
  <%= link_to 'Destroy', user, method: :delete %>
<% end %>

<%= link_to 'New User', new_user_path %>
```
#### link_to

- show - user
- edit - edit_user_path(user)
- destroy - user, method: :delete
- new user - new_user_path
- users list - users_path

#### routes.rb

Маршрутное правило (направляет запросы к URL в методы контроллера по REST):
```ruby
resources :users
```
Валидатор длины /models/...
```ruby
validates :content, length: {maximum: 140}
```

user_id

В models:
```ruby
has_many :posts
belong_to :user
```

Heroku run:
```ruby
heroku run rake db:migrate
```
Rails REST: стандартный набор URL и методов действий контроллеров для взаимодействия с моделями данных.

Валидация наличия содержимого (в model):
```ruby
validates :content, presence: true
```

Gem-ы для test в Gemfile:
```ruby
'minitest-reporters'
'mini_backtrace'
'guard-minitest'
'guard'
```
```bash
heroku logs # hl
```
Если много статических страниц, то лучше использовать гем high_voltage

Default start page (routes.rb):
```ruby
root 'application#hello'
```

Генерировать контроллеры в верблюжьей нотации:
```bash
rails generate controller StaticPages home help

# alias rgc
```
Откат создания контроллера:
```bash
rails destroy controller StaticPages home
```
Откат создания моделил:
```bash
rails destroy model User
rake db:migrate
```
Откатить на 1 шаг миграции:
```bash
rake db:rollback
```
Откат к началу:
```bash
rake db:migrate VERSION=0
```
В некоторых средах перед rake надо писать:
```bash
bundle exec
```
Маршруты к методам в routes.rb:
```ruby
get 'static_pages/home'
```
#### Тестирование контроллеров:
```ruby
test "should get home" do
  get static_pages_home_url
  assert_response: success
end
```
```bash
bundle
```
Исслед. сообщ. об ошибке:
```bash
rake test
```
#### КРАСНЫЙ / ЗЕЛЁНЫЙ / РЕФАКТОРИНГ

Проверка заголовка (тест):
```ruby
assert_select "title", "Foo"
```
.erb:
```ruby
<%= provide(:title, "Home") %>
<%= yield(:title) %>
```
#### Автозапуск тестов:

```bash
bundle exec guard init # alias begi
bundle exec guard # alias beg
# Enter
```
Когда тормозит:
```bash
ps aux | grep spring # узнаем pid
kill -9 [pid] # или
spring stop # или
pkill -9f spring
```
Смотри мой репо на гитхаб - https://github.com/krdprog/hartl-ror-custom-guardfile

#### Код Ruby пишем в хелперах

/app/helpers/application-helper.rb

Можно создавать свои вспомогательные функции (методы)

```bash
a.class
a.class.superclass.superclass
# наследование классов
```

```ruby
class Word
  def palindrome?(string)
    string == string.reverse
  end
end

w = Word.new
w.palindrome?("Foo") #=> false
```
```ruby
class Word < String
  def palindrome?
    self == self.reverse
  end
end

s = Word.new("level")
s.palindrome? #=> true
s.size #=> 5
```
Изменение встроенных классов:
```ruby
class String
  def palindrome?
    self == self.reverse
  end
end

"level".palindrome? #=> true
```

```ruby
"".empty? # Ruby-метод
" ".blank? # Rails-метод
```

```ruby
class User
  # создадим методы доступа к атрибутам
  attr_accessor :name :email
  
  # автоматически вызывается при User.new, аргумент attributes со значением по умолчанию - пустой хеш (nil)
  def initialize(attributes = {})
    @name = attributes[:name]
    @email = attributes[:email]
  end
  
  # переменные экземпляра (в Rails автоматически доступны во views)
  def formatted_email
    "#{@name} - #{@email}"
  end
end
```

- getter - метод чтения
- setter - метод записи

```ruby
require './example_user'

example = User.new
example.name = "Foo"
example.email = "foo@bar.com"

example.formatted_email

# или так:
user = User.new(name: "Foo", email: "Bar")
```
Инициализация объектов передачей хешей в аргументах широко используется в Rails.
