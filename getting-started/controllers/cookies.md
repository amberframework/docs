# Cookies

Cookies are read and written through **Amber::Base::Controller\#cookies**.

The cookies being read are the ones received along with the request, the cookies being written will be sent out with the response. Reading a cookie does not get the cookie object itself back, just the value it holds.

It's advisable that you only store simple data \(strings and numbers\) in cookies. If you have to store complex objects, you would need to handle the conversion manually when reading the values on subsequent requests.

Amber also has an encrypted cookie jar for storing sensitive data. The encrypted cookie jar encrypts the values in addition to signing them, so that they cannot be read by the end user.

### Examples of writing:

```crystal
class CommentsController < ApplicationController
  def new
    # Auto-fill the commenter's name if it has been stored in a cookie
    @comment = Comment.new(author: cookies[:commenter_name])
  end

  def create
    @comment = Comment.new(params[:comment])
    if @comment.save
      flash[:notice] = "Thanks for your comment!"
      if params[:remember_name]
        # Remember the commenter's name.
        cookies[:commenter_name] = @comment.author
      else
        # Delete cookie for the commenter's name cookie, if any.
        cookies.delete(:commenter_name)
      end
      redirect_to @comment.article
    else
      render action: "new"
    end
  end
end
```

### Examples of Reading

```crystal
cookies[:user_name]           # => "david"
cookies.size                  # => 2
JSON.parse(cookies[:lat_lon]) # => [47.68, -122.37]
cookies.encrypted[:discount]  # => 45
```

Please note that if you specify a :domain when setting a cookie, you must also specify the domain when deleting the cookie:

```crystal
cookies[:name] = {
  value:   'a yummy cookie',
  expires: 1.year.from_now,
  domain:  'domain.com'
}
cookies.delete(:name, domain: 'domain.com')
```

The option symbols for setting cookies are:

```crystal
:value  - The cookies value.
:path   - The path for which this cookie applies. Defaults to the root of the application.
:domain - The domain for which this cookie applies so you can restrict to the domain level. 
          If you use a schema like www.example.com and want to share session with user.example.com 
          set :domain to :all. Make sure to specify the :domain option with :all or Array again 
          when deleting cookies.

domain: nil                           # Does not set cookie domain. (default)
domain: :all                          # Allow the cookie for the top most level domain and subdomains.
domain: %w(.example.com .example.org) # Allow the cookie for concrete domain names.

:tld_length - When using :domain => :all, this option can be used to explicitly set the TLD length 
              when using a short (<= 3 character) domain that is being interpreted as part of a TLD. 
              For example, to share cookies between user1.lvh.me and user2.lvh.me, set :tld_length to 1.

:expires    - The time at which this cookie expires, as a Time object.
:secure     - Whether this cookie is only transmitted to HTTPS servers. Default is false.
:httponly   - Whether this cookie is accessible via scripting or only HTTP. Defaults to false.
```



