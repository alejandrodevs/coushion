# Coushion

This is a simple and powerful ODM (Object-Document Mapper) for [CouchDB](https://couchdb.apache.org) in Ruby.

## Installation

Add this line to your application's Gemfile:

```ruby
gem 'coushion'
```

And then execute:

    $ bundle install

Or install it yourself as:

    $ gem install coushion

## Usage

### Defining Documents

```ruby
class Person
  include Coushion::Document

  attribute :name, type: Types::String
  attribute :locale, type: Types::String, default: "en"
  attribute :followers, type: Types::Integer, default: 0
end

person = Person.new
# => #<Person ...>

person.locale
# => "en"

person.name = "Bart"
# => "Bart"

person.save
# => true
```

### Validations

Coushion includes [ActiveModel::Validations](https://api.rubyonrails.org/classes/ActiveModel/Validations.html) to supply the basic validation.

### Persistence

| Operation | Description |
| ------------- | ------------- |
| `Document.create` | Description |
| `Document.create!` | Description |
| `Document#save` | Description |
| `Document#save!` | Description |
| `Document#update` | Description |
| `Document#update!` | Description |
| `Document#touch` | Description |
| `Document#delete` | Description |
| `Document#destroy` | Description |
| `Document#new_record?` | Description |
| `Document#persisted?` | Description |

##### Examples

```ruby
class Post
  include Coushion::Document

  attribute :title, type: Types::String

  validates :title, presence: true
end

Post.create(title: "How to use CouchDB")
# => #<Post id: "", rev: "", title: "How to use CouchDB" ...>

Post.create!(title: nil)
# => raises Coushion::DocumentInvalid

post = Post.new(title: "My Second Post")
post.save
# => true

post.title = nil
post.save!
# => raises Coushion::DocumentInvalid

post = Post.find("")
post.update(title: "New title")
# => true

post.update!(title: nil)
# => raises Coushion::DocumentInvalid

post = Post.find("")
post.destroy
# => true
```

### Querying

| Operation | Description |
| ------------- | ------------- |
| `Document.find` | Description |
| `Document.where` | Description |

##### Examples

```ruby
Product.find("")
# => #<Product id: "" ...>

User.where(occupation: "Software Engineer")
# => [#<User id: "" ...>, #<User id: "" ...>, ...]
```

## Development

After checking out the repo, run `bin/setup` to install dependencies. Then, run `rake spec` to run the tests. You can also run `bin/console` for an interactive prompt that will allow you to experiment.

To install this gem onto your local machine, run `bundle exec rake install`. To release a new version, update the version number in `version.rb`, and then run `bundle exec rake release`, which will create a git tag for the version, push git commits and the created tag, and push the `.gem` file to [rubygems.org](https://rubygems.org).

## Contributing

Bug reports and pull requests are welcome on GitHub at https://github.com/alejandrodevs/coushion. This project is intended to be a safe, welcoming space for collaboration, and contributors are expected to adhere to the [code of conduct](https://github.com/alejandrodevs/coushion/blob/master/CODE_OF_CONDUCT.md).

## License

The gem is available as open source under the terms of the [MIT License](https://opensource.org/licenses/MIT).

## Code of Conduct

Everyone interacting in the Coushion project's codebases, issue trackers, chat rooms and mailing lists is expected to follow the [code of conduct](https://github.com/alejandrodevs/coushion/blob/master/CODE_OF_CONDUCT.md).
