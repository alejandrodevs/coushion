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

Just include `Coushion::Document` in your class and start defining your
document's attributes.

```ruby
class Person
  include Coushion::Document

  attribute :first_name, type: Types::String
  attribute :last_name, type: Types::String
  attribute :occupation, type: Types::String
  attribute :locale, type: Types::String, default: "en"
  attribute :preferences, type: Types::Hash, default: {}
end
```

Coushion uses `Sphene` for attributes definition. Please check [Sphene Documentation](https://github.com/alejandrodevs/sphene) for more information about its usage and the [types](https://github.com/alejandrodevs/sphene/blob/master/lib/sphene/types.rb) available.

### Validations

Coushion includes [ActiveModel::Validations](https://api.rubyonrails.org/classes/ActiveModel/Validations.html) to supply basic validation. Contexts for `create` and `update` actions are provided.

```ruby
class Movie
  include Coushion::Document

  attribute :name, type: Types::String
  attribute :genre, type: Types::String

  validates :name, presence: true
  validates :name, length: { maximum: 50 }, on: :create
  validates :genre, inclusion: { in: ["drama", "action"] }
end
```

### Callbacks

Coushion includes [ActiveModel::Callbacks](https://api.rubyonrails.org/classes/ActiveModel/Callbacks.html) to supply callbacks. Callbacks `before` and `after` are provided for `save`, `create` and `update` actions.

```ruby
class User
  include Coushion::Document

  attribute :email, type: Types::String
  attribute :username, type: Types::String

  after_create :send_welcome_email

  private

  def send_welcome_email
    UserMailer.welcome(id).deliver_later
  end
end
```

### Persistence

| Operation | Description |
| ------------- | ------------- |
| `Document.create` | Inserts a document into the database. Doesn't raise exceptions on validation errors but it still raises server errors. Returns the document no matter if it was persisted or not. |
| `Document.create!` | Inserts a document into the database, raising an error if a validation or server error occurs. Returns the document if there was not any error. |
| `Document#save` | Saves the document to the database, or creates it if new. Doesn't raise exceptions on validation errors but it still raises server errors. Will return true if the document was saved, false if not. |
| `Document#save!` | Saves the document to the database, or insert it if new. Raises an exception if validations failed or there was a server error. Returns true if the document was saved. |
| `Document#update` | Updates the document in the database. Doesn't raise exceptions on validation errors but it still raises server errors. Returns true if the document was updated, false if not. |
| `Document#update!` | Updates the document in the database and raise an error if validations failed or there was a server error. Returns true if the document was updated. |
| `Document#touch` | Updates the document's updated_at timestamp, optionally with one extra provided time field. This operation skips validations and callbacks. |
| `Document#delete` | Deletes the document from the database without running callbacks. It still raises server errors. Returns true if the document was deleted, false if not. |
| `Document#destroy` | Deletes the document from the database while running destroy callbacks. It still raises server errors. Returns true if the document was deleted, false if not. |
| `Document#new_record?` | Returns true if the model instance has not yet been saved to the database. Opposite of `persisted?` |
| `Document#persisted?` | Returns true if the model instance has been saved to the database. Opposite of `new_record?` |

##### Examples

```ruby
class Post
  include Coushion::Document

  attribute :title, type: Types::String

  validates :title, presence: true
  validates :title, length: { maximum: 50 }
end

Post.create(title: "How to use CouchDB")
# => #<Post id: "1601a...", rev: "1-b91b...", title: "How to use CouchDB" ...>

Post.create!(title: nil)
# => raises Coushion::DocumentInvalid

post = Post.new(title: "My Second Post")
post.save
# => true

post.title = nil
post.save!
# => raises Coushion::DocumentInvalid

post = Post.find("16011a5704ffe78e6de2afa4b3001d10")
post.update(title: nil)
# => false

post.update!(title: "New post's title")
# => true

post = Post.find("16011a5704ffe78e6de2afa4b3001d10")
post.destroy
# => true
```

### Querying

| Operation | Description |
| ------------- | ------------- |
| `Document.find` | Finds a document by a specific id. If the document cannot be found for the requested id, then `Coushion::DocumentNotFound` will be raised. |
| `Document.where` | Queries documents using Mango. Mango is a declarative JSON querying language for CouchDB databases. |

##### Examples

```ruby
Product.find("16011a5704ffe78e6de2afa4b3001d10")
# => #<Product id: "16011a..." ...>

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
