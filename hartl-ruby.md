### Hartl Ruby in RoR

```ruby
"foo".empty? # пусто?

s.include?("foo") # включает?
```
```ruby
if s.empty?
  'Empty'
else
  'Not empty'
end
```
```ruby
&& # и
|| # или
!x.empty? # не
```
```ruby
puts 'Empty' if x.empty? || y.empty?
```
```ruby
def string_message(srt='')
  if str.empty?
    'Empty'
  else
    'Not empty'
  end
end

puts string_message('foo')
```
```ruby
def string_message(str='')
  return 'Empty' if str.empty?
  return 'Not empty'
end

puts string_message('foo')

# или

'foo'.string_message
```
```ruby
def full_title(page_title='')
  base_title = 'First App'
  
  if page_title.empty?
    base_title
  else
    page_title + '|' + base_title
  end
end

# стр. 138 - почему так, а не #{} - over-escape - чрезмерное экранирование
```
```ruby
def full_title(page_title='')
  base_title = 'First App'
  
  return base_title if page_title.empty?
  return "#{page_title} | #{base_title}"
end
```
В /app/helpers/application_helper.rb - хелперы приложения

### Массив

Массив - список элементов, следующих в определённом порядке.

```ruby
"foo bar baz".split #=> ["foo", "bar", "baz"] - преобразование в массив
```
```ruby
"fooxbarxbaz".split('x') #=> ["foo", "bar", "baz"]
```
```ruby
a[0]
a[1]
a[2]
a[-1]
a.first
a.second # только в RoR
a.last
a #=> [...]
a.last == a[-1]
a.length # размер
a.empty?
a.include?(42)
a.sort
a.reverse
a.shuffle # перемешать
```
Всё это **не меняет** исходный массив, чтобы поменять, надо использовать бэнг-методы (с ! знаком)

```ruby
a.sort!
```
Добавить в массив:
```ruby
a.push(6) # добавит 6
a << 7 # добавит 7
a << "foo" << "bar" # добавит foo, bar
```
Преобразует массив в строку:
```ruby
a.join

a.join(', ') # через запятую и пробел
```
### Диапазоны

```ruby
(0..9).to_a

a = %w[foo bar baz faz]
a[0..2] #=> ["foo", "bar", "baz", "faz"]

a = (0..9).to_a
a[2..(a.length - 1)] # явное использование длины массива
a[2..-1] # трюк с индексом -1

('a'..'e').to_a
```

### Блоки

```ruby
(1..5).each { |i| puts 2 * i }
```
```ruby
(1..5).each do |i|
  puts 2 * i
end
```
Блоки - это замыкания, объединяющие анонимные функции с внешними данными.

```ruby
3.times { puts 'Foo' }

(1..5).map { |i| i**2 }
```

### Символ &:

```ruby
%w[A B C].map { |i| i.downcase }

# равно такому:

%[A B C].map(&:downcase)
```
```ruby
('a'..'z').to_a.shuffle[0..7].join # перемешать и выбрать 8 букв
```

### Хеши {}

Хеши - это массивы не ограниченные целочисленными индексам (ассоциативные массивы). Вместо целочисленных индексов используются хеш-индексы (ключи).

```ruby
user = {}

user["first_name"] = "Foo"
user["first_name"]  # доступ

user # литеральное представление хеша
```

#### ключ => значение

```ruby
user = {"first_name" => "foo", "age" => 23 }
```
Чаще используются **символы**:

```ruby
:first_name
:age
```

**Символы** - специальый тип данных в Ruby.

При попытке получить значение для отсутствующего ключа, возвращается nil.

Хеш можно задать и так:

```ruby
user = { first_name: "Foo" }
```
И так:
```ruby
user = { :first_name => "Foo" }
```
Вложенные хеши:
```ruby
params = {}
params[:user] = { name: "Foo", email: "foo@bar.ru" }

params[:user][:email]
```

Так же как массивы и диапазоны у хеша есть метод each:

```ruby
flash = { success: "Work", danger: "Not work" }

flash.each do |key, value|
  puts "Key: #{key}, value: #{value}"
end
```
.inspect - возвращает строку с литеральным представлением своего объекта.

```ruby
[1, 2, 3, 4, 5] # литерал массива
```

p - возвращает объект, а puts - возвращает nil

```ruby
stylesheet_link_tag 'application', media: 'all', 'data-turbolinks-track' => true
```
**Если хеш - последний аргумент в вызове метода, то фигурные скобки можно опустить**

```ruby
'data-turbolinks-track' => true # с хеш-ракетой, т.к. в символе нельзя использовать дефис
```
### Конструкторы

```ruby
s = "foo" # литеральный конструктор строки

s = String.new("foo") # именованный конструктор строки

a = Array.new([1, 2, 3])

h = Hash.new
h[:foo] #=> nil

h = Hash.new(0) # принимает значение по-умолчанию
h[:foo] #=> 0
```
.new - метод класса (вызов к классу)
.length - метод экземпляра класса (вызов к экземпляру класса)

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
