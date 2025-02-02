**Important**
- `master` branch contains the bleeding edge development code.
- check `branches` or `tags` for the latest stable release or specific versions.

# Paloma
Page-specific javascript for Rails done right.

## Advantages
* Choose what specific javascript code to run per page.
* Easily make ruby variables available on your javascript files.
* Write in vanilla javascript, coffeescript, and anything that compiles to js.
* No external JS library dependency.
* Easy to understand (*because it is patterned after Rails controller*).

## Minimum Requirement
* Rails 4.0.2 or higher

## Quick Example

Paloma controller:
```javascript
Paloma.controller('Users', {
  new: function(){
    // Executes when Rails Users#new is executed.
    alert('Hello Sexy User!');
  }
});
```

Rails controller:
```ruby
def UsersController < ApplicationController
  def new
    # no special method to call
    @user = User.new
  end
end
```


## Install

1. Install gem.
  - Without bundler: `sudo gem install paloma`.
  - With bundler, add this to your Gemfile:
    ```ruby
    gem 'paloma', '~> 6.1.0'
    ```

2. Require `paloma` in your `application.js`:
```
//= require paloma
```

3. In your layouts, insert Paloma's hook. This is responsible for connecting your ruby code to your javascript code.

   `application.html.erb`
   ```html
   <html>
      <head>
      </head>

      <body>
         <%= yield %>
         <%= insert_paloma_hook %>
      </body>
   </html>
   ```

4. Start Paloma to initialize the appropriate controller and execute a certain action. Most of the time this will be inside `document.ready`.

  ```js
  $(document).ready(function(){
    Paloma.start();
  });
  ```

### Controller

Paloma controllers are javascript classes which will be mapped with your Rails controller. Basically, both Paloma and Rails controllers will share the same name.

It is created or accessed (if existing), using `Paloma.controller` method.

```js
var ArticlesController = Paloma.controller('Articles');
```

Note: Using `Paloma.controller` method, you can access the same controller across different files.

### Actions

To handle specific actions of your Rails controller, add methods to your Paloma controller's prototype.

```js
var ArticlesController = Paloma.controller('Articles');

ArticlesController.prototype.edit = function(){
  // Handle edit article
};
```

Or you can pass the prototype value as the 2nd argument of the `Paloma.controller` method.

```js
Paloma.controller('Articles', {
  edit: function(){
    // Handle edit article
  }
});
```

### Namespace

Namespaced controller should follow the format `namespace/controller`.

Rails controller:
```ruby
class Admin::UsersController < ApplicationController
  def new
    @user = User.new
  end
end
```

Paloma controller:
```js
Paloma.controller('Admin/Users', {
  new: function(){
    // Handle new admin user
  }
});
```

### Controller Inheritance

Controller inheritance is accomplished using the syntax `Controller < ParentController` *(same as ruby's syntax)*.

Parent:
```js
Paloma.controller('Application', {
  index: function(){
    alert('Application: Index');
  },

  new: function(){
    alert('Application: New');
  }
});
```

Child:
```js
Paloma.controller('Users < Application', {
  // Override Application's new action
  new: function(){
    alert('Users: New');
  }
});
```

## Before Callbacks

Executing a method before doing an action can be done using the `before` property of a controller.

```js
Paloma.controller('Articles', {
  before: ['show -> alert'],

  show: function(){
    // Handle show Article
  },

  alert: function(){
    alert("You are about to show an article.");
  }
});
```

Multiple actions and callbacks should be separated by spaces.
The callbacks order on the string will define the order of their execution.
So in this case, `alert` will be executed first before `log`.

```js
Paloma.controller('Articles', {
  before: ['show index -> alert log'],

  index: function(){},
  show: function(){},

  alert: function(){
    alert('Before index and show');
  },

  log: function(){
    console.log('Before index and show');
  }
});
```

### Multiple Before Entries

The order of execution is also based on the order of entries on the `before` array.

```js
Paloma.controller('Articles', {
  before: [
    'show -> beforeShow',
    'index -> beforeIndex',
    'show index -> beforeShowAndIndex'
  ],

  beforeShow: function(){ alert('Before Show'); },
  beforeShowAndIndex: function(){ alert('Before Show and Index'); }
});
```

When `show` is executed, the following callbacks will be called in this order: `beforeShow` then `beforeShowAndIndex`.

### Before All Actions

`all` is a special string that can be used to indicate a catch-all callback.

```js
Paloma.controller('Articles', {
  before: ['all -> initialize'],

  initialize: function(){
    alert('execute before every action');
  }
});
```

## Execution Details

You can access what `controller` and `action` Paloma is about to execute or already executed,
by accessing the `controller` and `action` property of a Paloma controller.

```js
Paloma.controller('Users', {
  before: ['all -> log'],

  log: function(){
    console.log('Controller: ' + this.controller);
    console.log('Action: ' + this.action);
  }
})
```

## Advanced Usage

You can manipulate what controller/action should Paloma execute by calling `js` method **before** rendering.

1. Changing controller.

  ```ruby
  class UsersController < ApplicationController
    def new
      @user = User.new

      # will use Accounts controller instead of Users controller
      js 'Accounts'
    end
  end
  ```

2. Changing action.

  You can use the symbol syntax:
  ```ruby
  def new
    @user = User.new

    # will execute register method instead of new
    js :register
  end
  ```

  Or the string syntax:
  ```ruby
  def new
    @user = User.new

    # will execute register method instead of new
    js '#register'
  end
  ```

3. Changing controller and action.

  ```ruby
  def new
    @user = User.new

    # will execute Accounts#register instead of Users#new
    js 'Accounts#register'
  end
  ```

4. Changing controller and action with namespace.

  ```ruby
  def new
    @user = User.new

    # will use Admin/Accounts instead of Users controller
    js `Admin/Accounts`
  end
  ```

  ```ruby
  def new
    @user = User.new

    # will execute Admin/Accounts#register instead of Users#new
    js 'Admin/Accounts#register'
  end
  ```


## Passing Parameters

You can pass parameters to your Paloma Controller in two ways.

1. Passing a hash. (*parameters only*)

  ```ruby
  def show
    user = User.find params[:id]

    js :id => user.id, :myParam => 'test'
  end
  ```

2. Passing a `namespace/controller#action` string and a hash.

  ```ruby
  def show
    user = User.find params[:id]

    js 'Admin/Users', :id => user.id, :myParam => 'test'
  end
  ```

You can access the passed parameters using the `params` property of your Paloma controller.

```js
Paloma.controller('Users', {
  show: function(){
    alert("User id: " + this.params.id);
    alert("String: " + this.params.myParam);
  }
});
```


## Preventing Paloma Execution

If you do not want Paloma to execute in a specific Rails Controller action you pass `false` to the `js` method.

```ruby
def edit
  @user = User.find params[:id]
  js false
end
```

## Controller-wide Setup

You can call `js` outside Rails controller actions for controller-wide settings.

**Example:**

```ruby
class UsersController < ApplicationController

  # use Accounts controller instead of Users for all actions.
  js 'Accounts'

end
```

Like `before_action` of Rails you can also pass `only` and `except` options.

```ruby
class UsersController < ApplicationController

  # Use Admin/Accounts except for show and destroy method
  js 'Admin/Accounts', :except => [:show, :destroy]

end
```


**IMPORTANT NOTE:**
If you are going to pass parameters for Controller-wide settings, pass a hash using the `:params` key.

```ruby
class UsersController < ApplicationController
  js 'Accounts', :params => {:x => 1, :y => 2, :z => 3}, :only => :show
end
```

### Overriding Controller-wide Setup

If you want to override the controller-wide setup call `js` again inside a controller action. From there, you can override the controller/action or pass additional parameters.

```ruby
class UsersController < ApplicationController
  js 'Accounts', :params => {:x => 1}

  def new
    @user = User.new

    # will execute Accounts#register with params {:x => 1, :y => 2}
    js :register, :y => 2
  end
end
```

## Hook
`insert_paloma_hook` is a helper method that you use in your views to insert Paloma's HTML hook. It is what connects your ruby code to your javascript code. Basically, it contains a javascript code that has embedded ruby in it. That javascript code will register the Rails controller and action to Paloma's engine.

Ideally, you just need to call `insert_paloma_hook` in your layouts, since the layout will always be included in every rendered view. But if you are rendering a view without a layout, make sure to call `insert_paloma_hook` in that view.

## Testing

A helper module `Paloma::TestHelper` is provided to help with testing parameters passed to javascript in a controller test.
This module exposes a `js_params` method which returns the set of parameters passed to paloma, which you can then make assertions on.

For example:
```ruby
class UsersController
  def show
    user = User.find params[:id]

    js :id => user.id, :myParam => 'test'
  end
end
```
Corresponding test (Minitest):
```ruby
# Require test helper
require "paloma/test_helper"

class UsersControler < ActiveSupport::TestCase
  # Include helper to access `js_params` method
  include Paloma::TestHelper

  test "should get show" do
    user = User.create!
    get :show, id: user.id
    
    expected_params = { id: user.id, myParam: 'test' }
    assert_equal(expected_params, js_params)
  end
end
```

## Starting Paloma
Once Paloma's HTML hook is already executed, you can now start Paloma by calling `Paloma.start()` in your javascript code. First, it will execute the HTML hook if not yet executed, then will initialize the correct Paloma controller, execute any before callbacks, and finally execute the correct action if available.


## AJAX
1. Make sure that the AJAX response contains the HTML hook. (use `insert_paloma_hook`)
2. Start Paloma on complete/success.

   ```js
   $.get('http://example.com', function(response){
      $('#result').html(response);
      Paloma.start();
   });
   ```

## Turbolinks Support

### Execute Paloma when user hits `Back` or `Forward` button.

Paloma executes page-specific javascript by adding a `<script>` tag to the response body. Turbolinks, by default, executes any inline javascript in the response body when you visit a page, so the `<script>` tag appended by Paloma will automatically be executed. However, when Turbolinks restores a page from cache (*this happens when a user hits `Back` or `Forward` button in his browser*) any **inline javascript will not be executed** anymore. This is the intentional behavior of Turbolinks, and it is not a bug. If you want to execute Paloma again when Turbolinks restores a page, do something like this:

```js
$(document).on('page:restore', function(){
  Paloma.start();
});
```
