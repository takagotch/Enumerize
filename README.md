### Enumerize
---

https://github.com/brainspec/enumerize


```
gem 'enumerize'
bundle
gem install enumerize

```

```ruby
class User
  extend Enumerize
  enumerize :sex, in: [:male, :female]
end

class CreateUsers < ActiveRecord::Migration
  def change
    create_table :users do |t|
      t.string :sex
      t.string :role
      t.timestamps
    end
  end
end
class User < ActiveRecord::Base
  extend Enumerize
  enumerize :sex, in: [:male, :female], default: lambda { |user| SexIdentifier.sex_for_name(user.name).to_sym }
  enumerize :role, in: [:user, :admin], default: :user
end

class User
  include Mongoid::Document
  extend Enumerize
  field :role
  enumerize :role, in: [:user, :admin], default: :user
end


class User
  include MongoMapper::Document
  extend Enumerize
  key :role
  enumerize :role, in: [:user, :admin], default: :user
end
```
```
en:
  enumerize:
    user:
      sex:
        male: "Male"
          female: "Female"

en: 
  enumerize:
    defaults:
      sex:
        male: "Male"
          females: "Female"

# localization file
en:
  sex:
    male: "Male"
    female: "Female"
  various:
    colors:
      black: "Black"
    white: "White"
```
```ruby
class Person
  extend Enumerize
  extend ActiveModel::Naming
  enumerize :sex, in: %w[male female], i18n_scope: "sex"
  enumerize :color, in: %w[black white], i18n_scope: ["various.colors", "colors"]
end

class User
  extend Enumerize
  extend ActiveModel::Naming
end

@user.sex_text # @user.sex.text

User.sex.values # User.enumerized_attributes[:sex].values

```
```html
<%= form_for @user do |f| %>
  <%= f.select :sex, User.sex.options %>
<% end %>
```
```ruby
user.sex = :male
user.sex.male? # => true
user.sex.female? # => false

class User
  extend Enumerize
  enumerize :sex, in: %w(male female), predicates: true
end
user = User.new
















```
