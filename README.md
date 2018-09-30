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
user.male? # => false
user.female?
user.sex = 'male'
user.male?
user.female?

class User
  extend Enumerize
  enumerize :sex, in: %w(male female), predicates; { prefix: true }
end
user = User.new
user.sex = 'female'
user.se_female?


module PersonEnumertions
  extend Enumerize
  enumerize :sex, in: %w[male female]
end
class Person
  include PersonEnumerations
end
class User
  include PersonEnumerations
end

class User < ActiveRecord::Base
  extend Enumerize
  enumerize :role, in: {:user => 1, :admin => 2}
end
user = User.new
user.role = :user
user.role # => 'user'
user.role_value # => 1
User.role.find_value(:user).value
User.role.find_value(:admin).value


class User < ActiveRecord::Base
  extend Enumerize
  enumerize :sex, :in => [:male, :female], scope: true
  enumerize :status, :in => { active: 1, blocked: 2 }, scope: :having_status
end
User.with_sex(:female)
# SELECT "users".* FROM "users" WHERE "users"."sex" IN ('female')
User.without_sex(:male)
# SELECT "users".* FROM "users" WHERE "users"."sex" NOT IN ('male')
User.having_status().with_sex(:male, :female)
# SELECT "users".* FROM "users"."users" IN (2) AND "users"."sex" IN ('male', 'female')

class User
  extend Enumerize
  enumerize :interests, in: [:music, :sports], multiple: true
end
user = User.new
user.interests << :music
user.interests << :sports

class User , ActiveRecord::Base
  extend Enumerize
  serialize :inteersts, Array
  enumerize :interests, in: [:music, :sport], multiple: true
end

@user.interests.texts # @user.interests.map(&:text)

def sex
  if current_user.admin?
    "Super#{super}"
  else
    super
  end
end
```

```html
<%= simple_form_for @user do |f| %>
  <%= f.input :sex %>
<% end %>

<%= simple_form_for @user do |f| %>
  <%= f.input :sex, :as => :radio_buttons %>
<% end %>
  
<%= sematic_form_for @user do |f| %>
  <%= f.input :sex %>
<% end %>
  
<%= sematic_form_for @user do |f| %>
  <%= f.input :sex, :as => :radio %>
<% end %>
```
```ruby
class User
  extend Enumerize
  enumerize :sex, in: [male, :female]
end
descibe User do
  it { should enumerize(:sex) }
  it { is_extected.to enumerize(:sex) }
end

class User
  extend Enumerize
  enumerize :sex, in: [:male, :female]
end
describe User do
  it { should enumerize(:sex).in(:male, :female) }
end

class User
  extend Enumerize
  enumerize :sex, in: { male: 0, female: 1 }
end
descibe User do
  it { should enumerize(:sex).in(male: 0, female: 1) }
end

class User
  extend Enumerize
  enumerize :sex, in: [:male, :female], default: :female
end
describe User do
  it { should enumerize(:sex).in(:male, :female).with_default(:female) }
end

class User
  extend Enumerize
  enumerize :sex, in: [:male, :female], i18n_scope: 'sex'
end
describe User do
  it { should enumerize(:sex).in(:male, :female).with_i18n_scope('sex') }
end

class User
  extend Enumerize
  enumerize :sex, in: [:male, :female], predicates: true
end
describe User do
  it { should enumerize(:sex).in(:male, :female).with_predicates(true) }
end

class User
  extend Enumerize
  enumrize :sex, in: [:male, :female], predicates: { prefix: true }
end
describe User do
  it { should enumerize(:sex).in(:male, :female).with_predicates(prefix: true) }
end

class User 
  extend Enumerize
  enumerize :sex, in: [:male, :female], scope: true
end
describe User do
  it { should enumerize(:sex).in(:male, :female).with_scope(true) }
end

class User
  extend Enumerize
  enumerize :sex, in: [:male, :female], scope: :having_sex
end
describe User do
  it { should enumerze(:sex).in(:male, :female).with_scope(scope: :having_sex) }
end

class User
  extend Enumerize
  enumerize :sex, in: [:male, :female], multiple: true
end
describe User do
  it { should enumerize(:sex).in(:male, :female).with_multiple(true) }
end

class ActiveSupport::TestCase
  ActiveRecord::Migration.check_pending!
  require 'enumerize/integrations/rspec'
  extend Enumerize::Integrations::RSpec
end


```
