# [Chapter 2 A toy app](/book/toy_app#cha-a_toy_app)
Trong chương này, chúng ta sẽ thử tạo một demo application để có thể thấy được sức mạnh của Rails. Với mục đích tiếp cận một cách khái quát nhất về lập trình với Ruby on Rails (cũng như lập trình Web nói chung), trong chương này chúng ta sẽ sử dụng _scaffold generators_, một câu lệnh cho phép tạo tự dộng một số lượng lớn các hàm có sẵn. Như đã đề cập ở [Box&nbsp;1.2](/book/beginning#aside-scaffolding), ngoài chương này ra, phần còn lại của cuốn sách này sẽ tiếp cận theo hướng ngược lại, phát triển một sample application đầy đủ, dần dần từng bước và sẽ giải thích cặn kẽ từng bước một trong quá trình phát triển. 
Mục đích của demo application chúng ta sẽ tạo trong chương này là một application cho phép chúng ta có thể thao tác, truy cập qua URLs, giúp ta hiểu về cấu trúc của một Rails Application, bao gồm cả ví dụ về cấu trúc REST - cấu trúc được ưa chuộng của Rails.

Demo Application sẽ bao gồm các _users_ và các _microposts_ liên quan (có thể tưởng tượng nó giống một app Twitter thu nhỏ).  Các chức năng sẽ được phát triển một cách cực kỳ đơn giản, và đôi khi có một số bước có vẻ hơi ảo diệu chút, nhưng đừng lo lắng nhé, các bạn sẽ được phát triển một app tương tự với nhiều tính năng hơn, đặc biệt là bắt đầu từ đầu, có giải thích rõ ràng từ [Chapter&nbsp;3](/book/static_pages#cha-static_pages). Đồng thời tôi sẽ cung cấp thêm nhiều tài liệu tham khảo để các bạn có thể tìm hiểu thêm.
Bây giờ, tôi cần bạn bình tĩnh, nhẫn nại một chút, mục tiêu của tutorials này là giúp bạn tiếp cận từ điểm khái quát nhất cho đến những vấn đề cặn kẽ, chi tiết để bạn có thể hiểu một cách sâu sắc về Rails.

## [2.1 Planning the application](/book/toy_app#sec-planning_the_application)
Trong phần này, chúng ta sẽ lên kế hoạch cho demo application của mình. Giống như ở [Section&nbsp;1.3](/book/beginning#sec-the_hello_application), chúng ta sẽ bắt đầu bằng việc tạo ra khung của application, sử dụng câu lệnh `rails new` với Rails version đã được định sẵn:

```
$ cd ~/workspace
$ rails _4.2.0_ new toy_app
$ cd toy_app/
```

Nếu câu lệnh trên trả về thông báo lỗi kiểu như “Could not find ’railties”, thì có nghĩa là bạn chưa cài đặt đúng version của Rails rồi, bạn nên kiểm tra lại xem bạn đã thực hiện đúng các bước như đã hướng dẫn ở mục [Listing&nbsp;1.1](/book/beginning#code-installing_rails) chưa nhé.
Nếu bạn sử dụng cloud IDE như đề xuất ở [Section&nbsp;1.2.1](/book/beginning#sec-development_environment), thì bạn lưu ý rằng có thể app thứ 2 này được tạo ở cùng workspace với app đầu tiên. Bạn không cần thiết phải tạo một workspace mới, bạn chỉ cần click vào icon bánh răng ở khu vực file navigator và chọn “Refresh File Tree” là các files mới của bạn sẽ xuất hiện.

Tiếp theo, chúng ta sẽ sử dụng text editor để update `Gemfile`, như [Listing&nbsp;2.1](/book/toy_app#code-demo_gemfile_sqlite_version_redux) dưới đây.

Listing 2.1:A `Gemfile` for the toy app.

```
source 'https://rubygems.org'

gem 'rails', '4.2.0'
gem 'sass-rails', '5.0.2'
gem 'uglifier', '2.5.3'
gem 'coffee-rails', '4.1.0'
gem 'jquery-rails', '4.0.3'
gem 'turbolinks', '2.3.0'
gem 'jbuilder', '2.2.3'
gem 'sdoc', '0.4.0', group: :doc

group :development, :test do
  gem 'sqlite3', '1.3.9'
  gem 'byebug', '3.4.0'
  gem 'web-console', '2.0.0.beta3'
  gem 'spring', '1.1.3'
end

group :production do
  gem 'pg', '0.17.1'
  gem 'rails_12factor', '0.0.2'
end
```

Lưu ý rằng [Listing&nbsp;2.1](/book/toy_app#code-demo_gemfile_sqlite_version_redux) giống hệt với [Listing&nbsp;1.14](/book/beginning#code-gemfile_pg_gem).

Như ở mục [Section&nbsp;1.5.1](/book/beginning#sec-heroku_setup), chúng ta sẽ install các local gems và không install các production gem bằng cách sử dụng option --without production

```
$ bundle install --without production
```

Cuối cùng, chúng ta sẽ quản lý version demo app bằng Git:

```
$ git init
$ git add -A
$ git commit -m "Initialize repository"
```

Bạn cũng nên [tạo một repository mới](https://bitbucket.org/repo/create) bằng cách click vào button “Create” ở Bitbucket ( [Figure&nbsp;2.1](/book/toy_app#fig-create_demo_repository)), và push code lên remote repository của bạn.

```
$ git remote add origin git@bitbucket.org:<username>/toy_app.git
$ git push -u origin --all # pushes up the repo and its refs for the first time
```

 ![images/figures/create_demo_repo_bitbucket](https://softcover.s3.amazonaws.com/636/ruby_on_rails_tutorial_3rd_edition/images/figures/create_demo_repo_bitbucket.png)

Figure 2.1: Creating the toy app repository at Bitbucket.

Cuối cùng, không bao giờ là quá sớm để deploy cả, và tôi khuyên bạn nên làm theo các bước hướng dẫn ở  [Listing&nbsp;1.8](/book/beginning#code-hello_action) và [Listing&nbsp;1.9](/book/beginning#code-default_root_route).<sup id="_cha-2_footnote-ref-1" class="footnote"><a href="#cha-2_footnote-1">1</a></sup>. Sau đó commit và push lên Heroku.

```
$ git commit -am "Add hello"
$ heroku create
$ git push heroku master
```

(Giống như ở [Section&nbsp;1.5](/book/beginning#sec-deploying), bạn có thể nhận được một vài thông báo warning, tạm thời bạn có thể bỏ qua. Chúng ta sẽ giải quyết chúng trong phần [Section&nbsp;7.5](/book/sign_up#sec-professional_grade_deployment)). Ngoài địa chỉ app Heroku thì các phần còn lại sẽ giống như kết quả ở [Figure&nbsp;1.18](/book/beginning#fig-heroku_app).

Bây giờ, chúng ta có thể sẵn sàng tạo demo app rồi.
Bước đầu tiên khi tạo một web application đó là tạo một _data model_, biểu diễn các cấu trúc cần thiết trong ứng dụng của chúng ta. Trong trường hợp này, chúng ta sẽ tạo nên một microblog, với yêu cầu đơn giản chỉ có các users và các microposts. Do vậy, chúng ta sẽ bắt đầu với việc định nghĩa model cho _users_ của app ( [Section&nbsp;2.1.1](/book/toy_app#sec-modeling_demo_users)), sau đó thêm model cho _microposts_ ( [Section&nbsp;2.1.2](/book/toy_app#sec-modeling_demo_microposts)).












### [2.1.1 A toy model for users](/book/toy_app#sec-modeling_demo_users)

There are as many choices for a user data model as there are different registration forms on the web; we’ll go with a distinctly minimalist approach. Users of our toy app will have a unique `integer` identifier called `id`, a publicly viewable `name` (of type `string`), and an `email` address (also a `string`) that will double as a username. A summary of the data model for users appears in [Figure&nbsp;2.2](/book/toy_app#fig-demo_user_model).

 ![demo_user_model](https://softcover.s3.amazonaws.com/636/ruby_on_rails_tutorial_3rd_edition/images/figures/demo_user_model.png)
Figure 2.2: The data model for users.

As we’ll see starting in [Section&nbsp;6.1.1](/book/modeling_users#sec-database_migrations), the label `users` in [Figure&nbsp;2.2](/book/toy_app#fig-demo_user_model) corresponds to a _table_ in a database, and the `id`, `name`, and `email` attributes are _columns_ in that table.

### [2.1.2 A toy model for microposts](/book/toy_app#sec-modeling_demo_microposts)

The core of the micropost data model is even simpler than the one for users: a micropost has only an `id` and a `content` field for the micropost’s text (of type `text`).<sup id="_cha-2_footnote-ref-2" class="footnote"><a href="#cha-2_footnote-2">2</a></sup> There’s an additional complication, though: we want to _associate_ each micropost with a particular user. We’ll accomplish this by recording the `user_id` of the owner of the post. The results are shown in [Figure&nbsp;2.3](/book/toy_app#fig-demo_micropost_model).

 ![demo_micropost_model](https://softcover.s3.amazonaws.com/636/ruby_on_rails_tutorial_3rd_edition/images/figures/demo_micropost_model.png)
Figure 2.3: The data model for microposts.

We’ll see in [Section&nbsp;2.3.3](/book/toy_app#sec-demo_user_has_many_microposts) (and more fully in [Chapter&nbsp;11](/book/user_microposts#cha-user_microposts)) how this `user_id` attribute allows us to succinctly express the notion that a user potentially has many associated microposts.

## [2.2 The Users resource](/book/toy_app#sec-demo_users_resource)

In this section, we’ll implement the users data model in [Section&nbsp;2.1.1](/book/toy_app#sec-modeling_demo_users), along with a web interface to that model. The combination will constitute a _Users resource_, which will allow us to think of users as objects that can be created, read, updated, and deleted through the web via the [HTTP protocol](http://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol). As promised in the introduction, our Users resource will be created by a scaffold generator program, which comes standard with each Rails project. I urge you not to look too closely at the generated code; at this stage, it will only serve to confuse you.

Rails scaffolding is generated by passing the `scaffold` command to the `rails generate` script. The argument of the `scaffold` command is the singular version of the resource name (in this case, `User`), together with optional parameters for the data model’s attributes:<sup id="_cha-2_footnote-ref-3" class="footnote"><a href="#cha-2_footnote-3">3</a></sup>

```
$ rails generate scaffold User name:string email:string
      invoke active_record
      create db/migrate/20140821011110_create_users.rb
      create app/models/user.rb
      invoke test_unit
      create test/models/user_test.rb
      create test/fixtures/users.yml
      invoke resource_route
       route resources :users
      invoke scaffold_controller
      create app/controllers/users_controller.rb
      invoke erb
      create app/views/users
      create app/views/users/index.html.erb
      create app/views/users/edit.html.erb
      create app/views/users/show.html.erb
      create app/views/users/new.html.erb
      create app/views/users/_form.html.erb
      invoke test_unit
      create test/controllers/users_controller_test.rb
      invoke helper
      create app/helpers/users_helper.rb
      invoke test_unit
      create test/helpers/users_helper_test.rb
      invoke jbuilder
      create app/views/users/index.json.jbuilder
      create app/views/users/show.json.jbuilder
      invoke assets
      invoke coffee
      create app/assets/javascripts/users.js.coffee
      invoke scss
      create app/assets/stylesheets/users.css.scss
      invoke scss
      create app/assets/stylesheets/scaffolds.css.scss
```

By including `name:string` and `email:string`, we have arranged for the User model to have the form shown in [Figure&nbsp;2.2](/book/toy_app#fig-demo_user_model). (Note that there is no need to include a parameter for&nbsp;`id`; it is created automatically by Rails for use as the _primary key_ in the database.)

To proceed with the toy application, we first need to _migrate_ the database using _Rake_ ( [Box&nbsp;2.1](/book/toy_app#aside-rake)):

```
$ bundle exec rake db:migrate
== CreateUsers: migrating ====================================================
-- create_table(:users)
   -> 0.0017s
== CreateUsers: migrated (0.0018s) ===========================================
```

This simply updates the database with our new `users` data model. (We’ll learn more about database migrations starting in [Section&nbsp;6.1.1](/book/modeling_users#sec-database_migrations).) Note that, in order to ensure that the command uses the version of Rake corresponding to our `Gemfile`, we need to run `rake` using `bundle exec`. On many systems, including the cloud IDE, you can omit `bundle exec`, but it is necessary on some systems, so I’ll include it for completeness.

With that, we can run the local web server in a separate tab ( [Figure&nbsp;1.7](/book/beginning#fig-rails_server_new_tab)) as follows:<sup id="_cha-2_footnote-ref-4" class="footnote"><a href="#cha-2_footnote-4">4</a></sup>

```
$ rails server -b $IP -p $PORT # Use only `rails server` if running locally
```

Now the toy application should be available on the local server as described in [Section&nbsp;1.3.2](/book/beginning#sec-rails_server). (If you’re using the cloud IDE, be sure to open the resulting development server in a new _browser_ tab, not inside the IDE itself.)

Box 2.1.Rake

In the Unix tradition, the [_make_](http://en.wikipedia.org/wiki/Make_(software)) utility has played an important role in building executable programs from source code; many a computer hacker has committed to muscle memory the line

```
$ ./configure && make && sudo make install
```

commonly used to compile code on Unix systems (including Linux and Mac OS&nbsp;X).

Rake is _Ruby make_, a make-like language written in Ruby. Rails uses Rake extensively, especially for the innumerable little administrative tasks necessary when developing database-backed web applications. The `rake db:migrate` command is probably the most common, but there are many others; you can see a list of database tasks using `-T db`:

```
$ bundle exec rake -T db
```

To see all the Rake tasks available, run

```
$ bundle exec rake -T
```

The list is likely to be overwhelming, but don’t worry, you don’t have to know all (or even most) of these commands. By the end of the _Rails Tutorial_, you’ll know all the most important ones.

### [2.2.1 A user tour](/book/toy_app#sec-a_user_tour)

If we visit the root URL at&nbsp;/ (read “slash”, as noted in [Section&nbsp;1.3.4](/book/beginning#sec-hello_world)), we get the same default Rails page shown in [Figure&nbsp;1.9](/book/beginning#fig-riding_rails), but in generating the Users resource scaffolding we have also created a large number of pages for manipulating users. For example, the page for listing all users is at [/users](http://localhost:3000/users), and the page for making a new user is at [/users/new](http://localhost:3000/users/new). The rest of this section is dedicated to taking a whirlwind tour through these user pages. As we proceed, it may help to refer to [Table&nbsp;2.1](/book/toy_app#table-user_urls), which shows the correspondence between pages and URLs.

| **URL** | **Action** | **Purpose** |
| [/users](http://localhost:3000/users) | `index` | page to list all users |
| [/users/1](http://localhost:3000/users/1) | `show` | page to show user with id `1` |
| [/users/new](http://localhost:3000/users/new) | `new` | page to make a new user |
| [/users/1/edit](http://localhost:3000/users/1/edit) | `edit` | page to edit user with id `1` |

Table 2.1: The correspondence between pages and URLs for the Users resource.

We start with the page to show all the users in our application, called [index](http://localhost:3000/users); as you might expect, initially there are no users at all ( [Figure&nbsp;2.4](/book/toy_app#fig-demo_blank_user_index_rails_3)).

 ![images/figures/demo_blank_user_index_3rd_edition](https://softcover.s3.amazonaws.com/636/ruby_on_rails_tutorial_3rd_edition/images/figures/demo_blank_user_index_3rd_edition.png)

Figure 2.4: The initial index page for the Users resource ( [/users](http://localhost:3000/users)).

To make a new user, we visit the [new](http://localhost:3000/users/new) page, as shown in [Figure&nbsp;2.5](/book/toy_app#fig-demo_new_user_rails_3). (Since the http://0.0.0.0:3000 or cloud IDE part of the address is implicit whenever we are developing locally, I’ll omit it from now on.) In [Chapter&nbsp;7](/book/sign_up#cha-sign_up), this will become the user signup page.

 ![images/figures/demo_new_user_3rd_edition](https://softcover.s3.amazonaws.com/636/ruby_on_rails_tutorial_3rd_edition/images/figures/demo_new_user_3rd_edition.png)

Figure 2.5: The new user page ( [/users/new](http://localhost:3000/users/new)).

We can create a user by entering name and email values in the text fields and then clicking the Create User button. The result is the user [show](http://localhost:3000/users/1) page, as seen in [Figure&nbsp;2.6](/book/toy_app#fig-demo_show_user_rails_3). (The green welcome message is accomplished using the _flash_, which we’ll learn about in [Section&nbsp;7.4.2](/book/sign_up#sec-the_flash).) Note that the URL is [/users/1](http://localhost:3000/users/1); as you might suspect, the number&nbsp;`1` is simply the user’s&nbsp;`id` attribute from [Figure&nbsp;2.2](/book/toy_app#fig-demo_user_model). In [Section&nbsp;7.1](/book/sign_up#sec-showing_users), this page will become the user’s profile.

 ![images/figures/demo_show_user_3rd_edition](https://softcover.s3.amazonaws.com/636/ruby_on_rails_tutorial_3rd_edition/images/figures/demo_show_user_3rd_edition.png)

Figure 2.6: The page to show a user ( [/users/1](http://localhost:3000/users/1)).

To change a user’s information, we visit the [edit](http://localhost:3000/users/1/edit) page ( [Figure&nbsp;2.7](/book/toy_app#fig-demo_edit_user_rails_3)). By modifying the user information and clicking the Update User button, we arrange to change the information for the user in the toy application ( [Figure&nbsp;2.8](/book/toy_app#fig-demo_update_user_rails_3)). (As we’ll see in detail starting in [Chapter&nbsp;6](/book/modeling_users#cha-modeling_users), this user data is stored in a database back-end.) We’ll add user edit/update functionality to the sample application in [Section&nbsp;9.1](/book/updating_and_deleting_users#sec-updating_users).

 ![images/figures/demo_edit_user_3rd_edition](https://softcover.s3.amazonaws.com/636/ruby_on_rails_tutorial_3rd_edition/images/figures/demo_edit_user_3rd_edition.png)

Figure 2.7: The user edit page ( [/users/1/edit](http://localhost:3000/users/1/edit)).

 ![images/figures/demo_update_user_3rd_edition](https://softcover.s3.amazonaws.com/636/ruby_on_rails_tutorial_3rd_edition/images/figures/demo_update_user_3rd_edition.png)

Figure 2.8: A user with updated information.

Now we’ll create a second user by revisiting the [new](http://localhost:3000/users/new) page and submitting a second set of user information; the resulting user [index](http://localhost:3000/users) is shown in [Figure&nbsp;2.9](/book/toy_app#fig-demo_user_index_two_rails_3). [Section&nbsp;7.1](/book/sign_up#sec-showing_users) will develop the user index into a more polished page for showing all users.

 ![images/figures/demo_user_index_two_3rd_edition](https://softcover.s3.amazonaws.com/636/ruby_on_rails_tutorial_3rd_edition/images/figures/demo_user_index_two_3rd_edition.png)

Figure 2.9: The user index page ( [/users](http://localhost:3000/users)) with a second user.

Having shown how to create, show, and edit users, we come finally to destroying them ( [Figure&nbsp;2.10](/book/toy_app#fig-demo_destroy_user)). You should verify that clicking on the link in [Figure&nbsp;2.10](/book/toy_app#fig-demo_destroy_user) destroys the second user, yielding an index page with only one user. (If it doesn’t work, be sure that JavaScript is enabled in your browser; Rails uses JavaScript to issue the request needed to destroy a user.) [Section&nbsp;9.4](/book/updating_and_deleting_users#sec-deleting_users) adds user deletion to the sample app, taking care to restrict its use to a special class of administrative users.

 ![images/figures/demo_destroy_user_3rd_edition](https://softcover.s3.amazonaws.com/636/ruby_on_rails_tutorial_3rd_edition/images/figures/demo_destroy_user_3rd_edition.png)

Figure 2.10: Destroying a user.

### [2.2.2 MVC in action](/book/toy_app#sec-mvc_in_action)

Now that we’ve completed a quick overview of the Users resource, let’s examine one particular part of it in the context of the Model-View-Controller (MVC) pattern introduced in [Section&nbsp;1.3.3](/book/beginning#sec-mvc). Our strategy will be to describe the results of a typical browser hit—a visit to the user index page at [/users](http://localhost:3000/users)—in terms of MVC ( [Figure&nbsp;2.11](/book/toy_app#fig-mvc_detailed)).

 ![images/figures/mvc_detailed](https://softcover.s3.amazonaws.com/636/ruby_on_rails_tutorial_3rd_edition/images/figures/mvc_detailed.png)

Figure 2.11: A detailed diagram of MVC in Rails.

Here is a summary of the steps shown in [Figure&nbsp;2.11](/book/toy_app#fig-mvc_detailed):

1. The browser issues a request for the /users URL.
2. Rails routes /users to the `index` action in the Users controller.
3. The `index` action asks the User model to retrieve all users (`User.all`).
4. The User model pulls all the users from the database.
5. The User model returns the list of users to the controller.
6. The controller captures the users in the `@users` variable, which is passed to the `index` view.
7. The view uses embedded Ruby to render the page as HTML.
8. The controller passes the HTML back to the browser.<sup id="_cha-2_footnote-ref-5" class="footnote"><a href="#cha-2_footnote-5">5</a></sup>

Now let’s take a look at the above steps in more detail. We start with a request issued from the browser—i.e., the result of typing a URL in the address bar or clicking on a link (Step&nbsp;1 in [Figure&nbsp;2.11](/book/toy_app#fig-mvc_detailed)). This request hits the _Rails router_ (Step&nbsp;2), which dispatches to the proper _controller action_ based on the URL (and, as we’ll see in [Box&nbsp;3.2](/book/static_pages#aside-get_etc), the type of request). The code to create the mapping of user URLs to controller actions for the Users resource appears in [Listing&nbsp;2.2](/book/toy_app#code-rails_routes); this code effectively sets up the table of URL/action pairs seen in [Table&nbsp;2.1](/book/toy_app#table-user_urls). (The strange notation `:users` is a _symbol_, which we’ll learn about in [Section&nbsp;4.3.3](/book/rails_flavored_ruby#sec-hashes_and_symbols).)

Listing 2.2:The Rails routes, with a rule for the Users resource. config/routes.rb

```
Rails.application.routes.draw do
  resources :users
  .
  .
  .
end
```

While we’re looking at the routes file, let’s take a moment to associate the root route with the users index, so that “slash” goes to /users. Recall from [Listing&nbsp;1.10](/book/beginning#code-hello_root_route) that we changed

```
# root 'welcome#index'
```

to read

```
root 'application#hello'
```

so that the root route went to the `hello` action in the Application controller. In the present case, we want to use the `index` action in the Users controller, which we can arrange using the code shown in [Listing&nbsp;2.3](/book/toy_app#code-rails_routes_root_route). (At this point, I also recommend removing the `hello` action from the Application controller if you added it at the beginning of this section.)

Listing 2.3:Adding a root route for users. config/routes.rb

```
Rails.application.routes.draw do
  resources :users
  root 'users#index'
  .
  .
  .
end
```

The pages from the tour in [Section&nbsp;2.2.1](/book/toy_app#sec-a_user_tour) correspond to _actions_ in the Users _controller_, which is a collection of related actions. The controller generated by the scaffolding is shown schematically in [Listing&nbsp;2.4](/book/toy_app#code-demo_users_controller). Note the notation `class UsersController < ApplicationController`, which is an example of a Ruby _class_ with _inheritance_. (We’ll discuss inheritance briefly in [Section&nbsp;2.3.4](/book/toy_app#sec-inheritance_hierarchies) and cover both subjects in more detail in [Section&nbsp;4.4](/book/rails_flavored_ruby#sec-ruby_classes).)

Listing 2.4:The Users controller in schematic form. app/controllers/users\_controller.rb

```
class UsersController < ApplicationController
  .
  .
  .
  def index
    .
    .
    .
  end

  def show
    .
    .
    .
  end

  def new
    .
    .
    .
  end

  def edit
    .
    .
    .
  end

  def create
    .
    .
    .
  end

  def update
    .
    .
    .
  end

  def destroy
    .
    .
    .
  end
end
```

You may notice that there are more actions than there are pages; the `index`, `show`, `new`, and `edit` actions all correspond to pages from [Section&nbsp;2.2.1](/book/toy_app#sec-a_user_tour), but there are additional `create`, `update`, and `destroy` actions as well. These actions don’t typically render pages (although they can); instead, their main purpose is to modify information about users in the database. This full suite of controller actions, summarized in [Table&nbsp;2.2](/book/toy_app#table-demo_RESTful_users), represents the implementation of the REST architecture in Rails ( [Box&nbsp;2.2](/book/toy_app#aside-REST)), which is based on the ideas of _representational state transfer_ identified and named by computer scientist [Roy Fielding](http://en.wikipedia.org/wiki/Roy_Fielding).<sup id="_cha-2_footnote-ref-6" class="footnote"><a href="#cha-2_footnote-6">6</a></sup> Note from [Table&nbsp;2.2](/book/toy_app#table-demo_RESTful_users) that there is some overlap in the URLs; for example, both the user `show` action and the `update` action correspond to the URL /users/1. The difference between them is the [HTTP request method](http://en.wikipedia.org/wiki/HTTP_request#Request_methods) they respond to. We’ll learn more about HTTP request methods starting in [Section&nbsp;3.3](/book/static_pages#sec-getting_started_with_testing).

| **HTTP request** | **URL** | **Action** | **Purpose** |
| GET | /users | `index` | page to list all users |
| GET | /users/1 | `show` | page to show user with id `1` |
| GET | /users/new | `new` | page to make a new user |
| POST | /users | `create` | create a new user |
| GET | /users/1/edit | `edit` | page to edit user with id `1` |
| PATCH | /users/1 | `update` | update user with id `1` |
| DELETE | /users/1 | `destroy` | delete user with id `1` |

Table 2.2: RESTful routes provided by the Users resource in [Listing&nbsp;2.2](/book/toy_app#code-rails_routes).

Box 2.2.REpresentational State Transfer (REST)

If you read much about Ruby on Rails web development, you’ll see a lot of references to “REST”, which is an acronym for REpresentational State Transfer. REST is an architectural style for developing distributed, networked systems and software applications such as the World Wide Web and web applications. Although REST theory is rather abstract, in the context of Rails applications REST means that most application components (such as users and microposts) are modeled as _resources_ that can be created, read, updated, and deleted—operations that correspond both to the [CRUD operations of relational databases](http://en.wikipedia.org/wiki/Create,_read,_update_and_delete) and to the four fundamental [HTTP request methods](http://en.wikipedia.org/wiki/HTTP_request#Request_methods): POST, GET, PATCH, and DELETE.<sup id="_cha-2_footnote-ref-7" class="footnote"><a href="#cha-2_footnote-7">7</a></sup> (We’ll learn more about HTTP requests in [Section&nbsp;3.3](/book/static_pages#sec-getting_started_with_testing) and especially [Box&nbsp;3.2](/book/static_pages#aside-get_etc).)

As a Rails application developer, the RESTful style of development helps you make choices about which controllers and actions to write: you simply structure the application using resources that get created, read, updated, and deleted. In the case of users and microposts, this process is straightforward, since they are naturally resources in their own right. In [Chapter&nbsp;12](/book/following_users#cha-following_users), we’ll see an example where REST principles allow us to model a subtler problem, “following users”, in a natural and convenient way.

To examine the relationship between the Users controller and the User model, let’s focus on a simplified version of the `index` action, shown in [Listing&nbsp;2.5](/book/toy_app#code-demo_index_action). (The scaffold code is ugly and confusing, so I’ve suppressed it.)

Listing 2.5:The simplified user `index` action for the toy application. app/controllers/users\_controller.rb

```
class UsersController < ApplicationController
  .
  .
  .
  def index
    @users = User.all
  end
  .
  .
  .
end
```

This `index` action has the line `@users = User.all` (Step&nbsp;3 in [Figure&nbsp;2.11](/book/toy_app#fig-mvc_detailed)), which asks the User model to retrieve a list of all the users from the database (Step&nbsp;4), and then places them in the variable `@users` (pronounced “at-users”) (Step&nbsp;5). The User model itself appears in [Listing&nbsp;2.6](/book/toy_app#code-demo_user_model); although it is rather plain, it comes equipped with a large amount of functionality because of inheritance ( [Section&nbsp;2.3.4](/book/toy_app#sec-inheritance_hierarchies) and [Section&nbsp;4.4](/book/rails_flavored_ruby#sec-ruby_classes)). In particular, by using the Rails library called _Active Record_, the code in [Listing&nbsp;2.6](/book/toy_app#code-demo_user_model) arranges for `User.all` to return all the users in the database.

Listing 2.6:The User model for the toy application. app/models/user.rb

```
class User < ActiveRecord::Base
end
```

Once the `@users` variable is defined, the controller calls the _view_ (Step&nbsp;6), shown in [Listing&nbsp;2.7](/book/toy_app#code-demo_index_view). Variables that start with the `@`&nbsp;sign, called _instance variables_, are automatically available in the views; in this case, the `index.html.erb` view in [Listing&nbsp;2.7](/book/toy_app#code-demo_index_view) iterates through the `@users` list and outputs a line of HTML for each one. (Remember, you aren’t supposed to understand this code right now. It is shown only for purposes of illustration.)

Listing 2.7:The view for the user index. app/views/users/index.html.erb

```
<h1>Listing users</h1>

<table>
  <thead>
    <tr>
      <th>Name</th>
      <th>Email</th>
      <th colspan="3"></th>
    </tr>
  </thead>

<% @users.each do |user| %>
  <tr>
    <td><%= user.name %></td>
    <td><%= user.email %></td>
    <td><%= link_to 'Show', user %></td>
    <td><%= link_to 'Edit', edit_user_path(user) %></td>
    <td><%= link_to 'Destroy', user, method: :delete,
                                     data: { confirm: 'Are you sure?' } %></td>
  </tr>
<% end %>
</table>

<br>

<%= link_to 'New User', new_user_path %>
```

The view converts its contents to HTML (Step&nbsp;7), which is then returned by the controller to the browser for display (Step&nbsp;8).

### [2.2.3 Weaknesses of this Users resource](/book/toy_app#sec-weaknesses_of_this_users_resource)

Though good for getting a general overview of Rails, the scaffold Users resource suffers from a number of severe weaknesses.

- **No data validations.** Our User model accepts data such as blank names and invalid email addresses without complaint.
- **No authentication.** We have no notion of logging in or out, and no way to prevent any user from performing any operation.
- **No tests.** This isn’t technically true—the scaffolding includes rudimentary tests—but the generated tests don’t test for data validation, authentication, or any other custom requirements.
- **No style or layout.** There is no consistent site styling or navigation.
- **No real understanding.** If you understand the scaffold code, you probably shouldn’t be reading this book.

## [2.3 The Microposts resource](/book/toy_app#sec-microposts_resource)

Having generated and explored the Users resource, we turn now to the associated Microposts resource. Throughout this section, I recommend comparing the elements of the Microposts resource with the analogous user elements from [Section&nbsp;2.2](/book/toy_app#sec-demo_users_resource); you should see that the two resources parallel each other in many ways. The RESTful structure of Rails applications is best absorbed by this sort of repetition of form—indeed, seeing the parallel structure of Users and Microposts even at this early stage is one of the prime motivations for this chapter.

### [2.3.1 A micropost microtour](/book/toy_app#sec-a_micropost_microtour)

As with the Users resource, we’ll generate scaffold code for the Microposts resource using `rails generate scaffold`, in this case implementing the data model from [Figure&nbsp;2.3](/book/toy_app#fig-demo_micropost_model):<sup id="_cha-2_footnote-ref-8" class="footnote"><a href="#cha-2_footnote-8">8</a></sup>

```
$ rails generate scaffold Micropost content:text user_id:integer
      invoke active_record
      create db/migrate/20140821012832_create_microposts.rb
      create app/models/micropost.rb
      invoke test_unit
      create test/models/micropost_test.rb
      create test/fixtures/microposts.yml
      invoke resource_route
       route resources :microposts
      invoke scaffold_controller
      create app/controllers/microposts_controller.rb
      invoke erb
      create app/views/microposts
      create app/views/microposts/index.html.erb
      create app/views/microposts/edit.html.erb
      create app/views/microposts/show.html.erb
      create app/views/microposts/new.html.erb
      create app/views/microposts/_form.html.erb
      invoke test_unit
      create test/controllers/microposts_controller_test.rb
      invoke helper
      create app/helpers/microposts_helper.rb
      invoke test_unit
      create test/helpers/microposts_helper_test.rb
      invoke jbuilder
      create app/views/microposts/index.json.jbuilder
      create app/views/microposts/show.json.jbuilder
      invoke assets
      invoke coffee
      create app/assets/javascripts/microposts.js.coffee
      invoke scss
      create app/assets/stylesheets/microposts.css.scss
      invoke scss
   identical app/assets/stylesheets/scaffolds.css.scss
```

(If you get an error related to Spring, just run the command again.) To update our database with the new data model, we need to run a migration as in [Section&nbsp;2.2](/book/toy_app#sec-demo_users_resource):

```
$ bundle exec rake db:migrate
== CreateMicroposts: migrating ===============================================
-- create_table(:microposts)
   -> 0.0023s
== CreateMicroposts: migrated (0.0026s) ======================================
```

Now we are in a position to create microposts in the same way we created users in [Section&nbsp;2.2.1](/book/toy_app#sec-a_user_tour). As you might guess, the scaffold generator has updated the Rails routes file with a rule for Microposts resource, as seen in [Listing&nbsp;2.8](/book/toy_app#code-demo_microposts_resource).<sup id="_cha-2_footnote-ref-9" class="footnote"><a href="#cha-2_footnote-9">9</a></sup> As with users, the `resources :microposts` routing rule maps micropost URLs to actions in the Microposts controller, as seen in [Table&nbsp;2.3](/book/toy_app#table-demo_RESTful_microposts).

Listing 2.8:The Rails routes, with a new rule for Microposts resources. config/routes.rb

```
Rails.application.routes.draw do
  resources :microposts
  resources :users
  .
  .
  .
end
```

| **HTTP request** | **URL** | **Action** | **Purpose** |
| GET | /microposts | `index` | page to list all microposts |
| GET | /microposts/1 | `show` | page to show micropost with id `1` |
| GET | /microposts/new | `new` | page to make a new micropost |
| POST | /microposts | `create` | create a new micropost |
| GET | /microposts/1/edit | `edit` | page to edit micropost with id `1` |
| PATCH | /microposts/1 | `update` | update micropost with id `1` |
| DELETE | /microposts/1 | `destroy` | delete micropost with id `1` |

Table 2.3: RESTful routes provided by the Microposts resource in [Listing&nbsp;2.8](/book/toy_app#code-demo_microposts_resource).

The Microposts controller itself appears in schematic form [Listing&nbsp;2.9](/book/toy_app#code-demo_microposts_controller). Note that, apart from having `MicropostsController` in place of `UsersController`, [Listing&nbsp;2.9](/book/toy_app#code-demo_microposts_controller) is _identical_ to the code in [Listing&nbsp;2.4](/book/toy_app#code-demo_users_controller). This is a reflection of the REST architecture common to both resources.

Listing 2.9:The Microposts controller in schematic form. app/controllers/microposts\_controller.rb

```
class MicropostsController < ApplicationController
  .
  .
  .
  def index
    .
    .
    .
  end

  def show
    .
    .
    .
  end

  def new
    .
    .
    .
  end

  def edit
    .
    .
    .
  end

  def create
    .
    .
    .
  end

  def update
    .
    .
    .
  end

  def destroy
    .
    .
    .
  end
end
```

To make some actual microposts, we enter information at the new microposts page, [/microposts/new](http://localhost:3000/microposts/new), as seen in [Figure&nbsp;2.12](/book/toy_app#fig-demo_new_micropost).

 ![images/figures/demo_new_micropost_3rd_edition](https://softcover.s3.amazonaws.com/636/ruby_on_rails_tutorial_3rd_edition/images/figures/demo_new_micropost_3rd_edition.png)

Figure 2.12: The new micropost page ( [/microposts/new](http://localhost:3000/microposts/new)).

At this point, go ahead and create a micropost or two, taking care to make sure that at least one has a `user_id` of&nbsp;`1` to match the id of the first user created in [Section&nbsp;2.2.1](/book/toy_app#sec-a_user_tour). The result should look something like [Figure&nbsp;2.13](/book/toy_app#fig-demo_micropost_index).

 ![images/figures/demo_micropost_index_3rd_edition](https://softcover.s3.amazonaws.com/636/ruby_on_rails_tutorial_3rd_edition/images/figures/demo_micropost_index_3rd_edition.png)

Figure 2.13: The micropost index page ( [/microposts](http://localhost:3000/microposts)).

### [2.3.2 Putting the _micro_ in microposts](/book/toy_app#sec-putting_the_micro_in_microposts)

Any _micro_post worthy of the name should have some means of enforcing the length of the post. Implementing this constraint in Rails is easy with _validations_; to accept microposts with at most 140 characters (à la Twitter), we use a _length_ validation. At this point, you should open the file `app/models/micropost.rb` in your text editor or IDE and fill it with the contents of [Listing&nbsp;2.10](/book/toy_app#code-demo_length_validation).

Listing 2.10:Constraining microposts to be at most 140 characters. app/models/micropost.rb

```
class Micropost < ActiveRecord::Base
  validates :content, length: { maximum: 140 }
end
```

The code in [Listing&nbsp;2.10](/book/toy_app#code-demo_length_validation) may look rather mysterious—we’ll cover validations more thoroughly starting in [Section&nbsp;6.2](/book/modeling_users#sec-user_validations)—but its effects are readily apparent if we go to the new micropost page and enter more than 140 characters for the content of the post. As seen in [Figure&nbsp;2.14](/book/toy_app#fig-micropost_length_error), Rails renders _error messages_ indicating that the micropost’s content is too long. (We’ll learn more about error messages in [Section&nbsp;7.3.3](/book/sign_up#sec-signup_error_messages).)

 ![images/figures/micropost_length_error_3rd_edition](https://softcover.s3.amazonaws.com/636/ruby_on_rails_tutorial_3rd_edition/images/figures/micropost_length_error_3rd_edition.png)

Figure 2.14: Error messages for a failed micropost creation.

### [2.3.3 A user has\_many microposts](/book/toy_app#sec-demo_user_has_many_microposts)

One of the most powerful features of Rails is the ability to form _associations_ between different data models. In the case of our User model, each user potentially has many microposts. We can express this in code by updating the User and Micropost models as in [Listing&nbsp;2.11](/book/toy_app#code-demo_user_has_many_microposts) and [Listing&nbsp;2.12](/book/toy_app#code-demo_micropost_belongs_to_user).

Listing 2.11:A user has many microposts. app/models/user.rb

```
class User < ActiveRecord::Base
  has_many :microposts
end
```

Listing 2.12:A micropost belongs to a user. app/models/micropost.rb

```
class Micropost < ActiveRecord::Base
  belongs_to :user
  validates :content, length: { maximum: 140 }
end
```

We can visualize the result of this association in [Figure&nbsp;2.15](/book/toy_app#fig-micropost_user_association). Because of the `user_id` column in the `microposts` table, Rails (using Active Record) can infer the microposts associated with each user.

 ![images/figures/micropost_user_association](https://softcover.s3.amazonaws.com/636/ruby_on_rails_tutorial_3rd_edition/images/figures/micropost_user_association.png)

Figure 2.15: The association between microposts and users.

In [Chapter&nbsp;11](/book/user_microposts#cha-user_microposts) and [Chapter&nbsp;12](/book/following_users#cha-following_users), we will use the association of users and microposts both to display all of a user’s microposts and to construct a Twitter-like micropost feed. For now, we can examine the implications of the user-micropost association by using the _console_, which is a useful tool for interacting with Rails applications. We first invoke the console with `rails console` at the command line, and then retrieve the first user from the database using `User.first` (putting the results in the variable `first_user`):<sup id="_cha-2_footnote-ref-10" class="footnote"><a href="#cha-2_footnote-10">10</a></sup>

```
$ rails console
>> first_user = User.first
=> #<User id: 1, name: "Michael Hartl", email: "michael@example.org",
created_at: "2014-07-21 02:01:31", updated_at: "2014-07-21 02:01:31">
>> first_user.microposts
=> [#<Micropost id: 1, content: "First micropost!", user_id: 1, created_at:
"2014-07-21 02:37:37", updated_at: "2014-07-21 02:37:37">, #<Micropost id: 2,
content: "Second micropost", user_id: 1, created_at: "2014-07-21 02:38:54",
updated_at: "2014-07-21 02:38:54">]
>> micropost = first_user.microposts.first # Micropost.first would also work.
=> #<Micropost id: 1, content: "First micropost!", user_id: 1, created_at:
"2014-07-21 02:37:37", updated_at: "2014-07-21 02:37:37">
>> micropost.user
=> #<User id: 1, name: "Michael Hartl", email: "michael@example.org",
created_at: "2014-07-21 02:01:31", updated_at: "2014-07-21 02:01:31">
>> exit
```

(I include `exit` in the last line just to demonstrate how to exit the console. On most systems, you can also use Ctrl-D for the same purpose.)<sup id="_cha-2_footnote-ref-11" class="footnote"><a href="#cha-2_footnote-11">11</a></sup> Here we have accessed the user’s microposts using the code `first_user.microposts`. With this code, Active Record automatically returns all the microposts with `user_id` equal to the id of `first_user` (in this case,&nbsp;`1`). We’ll learn much more about the association facilities in Active Record in [Chapter&nbsp;11](/book/user_microposts#cha-user_microposts) and [Chapter&nbsp;12](/book/following_users#cha-following_users).

### [2.3.4 Inheritance hierarchies](/book/toy_app#sec-inheritance_hierarchies)

We end our discussion of the toy application with a brief description of the controller and model class hierarchies in Rails. This discussion will only make much sense if you have some experience with object-oriented programming (OOP); if you haven’t studied OOP, feel free to skip this section. In particular, if you are unfamiliar with _classes_ (discussed in [Section&nbsp;4.4](/book/rails_flavored_ruby#sec-ruby_classes)), I suggest looping back to this section at a later time.

We start with the inheritance structure for models. Comparing [Listing&nbsp;2.13](/book/toy_app#code-demo_user_class) and [Listing&nbsp;2.14](/book/toy_app#code-demo_micropost_class), we see that both the User model and the Micropost model inherit (via the left angle bracket&nbsp;`<`) from `ActiveRecord::Base`, which is the base class for models provided by ActiveRecord; a diagram summarizing this relationship appears in [Figure&nbsp;2.16](/book/toy_app#fig-demo_model_inheritance). It is by inheriting from `ActiveRecord::Base` that our model objects gain the ability to communicate with the database, treat the database columns as Ruby attributes, and so&nbsp;on.

Listing 2.13:The `User` class, highlighting inheritance. app/models/user.rb

```
class User < ActiveRecord::Base
  .
  .
  .
end
```

Listing 2.14:The `Micropost` class, highlighting inheritance. app/models/micropost.rb

```
class Micropost < ActiveRecord::Base
  .
  .
  .
end
```

 ![images/figures/demo_model_inheritance](https://softcover.s3.amazonaws.com/636/ruby_on_rails_tutorial_3rd_edition/images/figures/demo_model_inheritance.png)

Figure 2.16: The inheritance hierarchy for the User and Micropost models.

The inheritance structure for controllers is only slightly more complicated. Comparing [Listing&nbsp;2.15](/book/toy_app#code-demo_users_controller_class) and [Listing&nbsp;2.16](/book/toy_app#code-demo_microposts_controller_class), we see that both the Users controller and the Microposts controller inherit from the Application controller. Examining [Listing&nbsp;2.17](/book/toy_app#code-toy_application_controller_class), we see that `ApplicationController` itself inherits from `ActionController::Base`; this is the base class for controllers provided by the Rails library Action Pack. The relationships between these classes is illustrated in [Figure&nbsp;2.17](/book/toy_app#fig-demo_controller_inheritance).

Listing 2.15:The `UsersController` class, highlighting inheritance. app/controllers/users\_controller.rb

```
class UsersController < ApplicationController
  .
  .
  .
end
```

Listing 2.16:The `MicropostsController` class, highlighting inheritance. app/controllers/microposts\_controller.rb

```
class MicropostsController < ApplicationController
  .
  .
  .
end
```

Listing 2.17:The `ApplicationController` class, highlighting inheritance. app/controllers/application\_controller.rb

```
class ApplicationController < ActionController::Base
  .
  .
  .
end
```

 ![images/figures/demo_controller_inheritance](https://softcover.s3.amazonaws.com/636/ruby_on_rails_tutorial_3rd_edition/images/figures/demo_controller_inheritance.png)

Figure 2.17: The inheritance hierarchy for the Users and Microposts controllers.

As with model inheritance, both the Users and Microposts controllers gain a large amount of functionality by inheriting from a base class (in this case, `ActionController::Base`), including the ability to manipulate model objects, filter inbound HTTP requests, and render views as HTML. Since all Rails controllers inherit from `ApplicationController`, rules defined in the Application controller automatically apply to every action in the application. For example, in [Section&nbsp;8.4](/book/log_in_log_out#sec-remember_me) we’ll see how to include helpers for logging in and logging out of all of the sample application’s controllers.

### [2.3.5 Deploying the toy app](/book/toy_app#sec-deploying_the_toy_app)

With the completion of the Microposts resource, now is a good time to push the repository up to Bitbucket:

```
$ git status
$ git add -A
$ git commit -m "Finish toy app"
$ git push
```

Ordinarily, you should make smaller, more frequent commits, but for the purposes of this chapter a single big commit at the end is fine.

At this point, you can also deploy the toy app to Heroku as in [Section&nbsp;1.5](/book/beginning#sec-deploying):

```
$ git push heroku
```

(This assumes you created the Heroku app in [Section&nbsp;2.1](/book/toy_app#sec-planning_the_application). Otherwise, you should run `heroku create` and then `git push heroku master`.)

To get the application’s database to work, you’ll also have to migrate the production database:

```
$ heroku run rake db:migrate
```

This updates the database at Heroku with the necessary user and micropost data models. After running the migration, you should be able to use the toy app in production, with a real PostgreSQL database back-end ( [Figure&nbsp;2.18](/book/toy_app#fig-toy_app_production)).

 ![images/figures/toy_app_production](https://softcover.s3.amazonaws.com/636/ruby_on_rails_tutorial_3rd_edition/images/figures/toy_app_production.png)

Figure 2.18: Running the toy app in production.

## [2.4 Conclusion](/book/toy_app#sec-toy_app_conclusion)

We’ve come now to the end of the high-level overview of a Rails application. The toy app developed in this chapter has several strengths and a host of weaknesses.

**Strengths**

- High-level overview of Rails
- Introduction to MVC
- First taste of the REST architecture
- Beginning data modeling
- A live, database-backed web application in production

**Weaknesses**

- No custom layout or styling
- No static pages (such as “Home” or “About”)
- No user passwords
- No user images
- No logging in
- No security
- No automatic user/micropost association
- No notion of “following” or “followed”
- No micropost feed
- No meaningful tests
- **No real understanding**

The rest of this tutorial is dedicated to building on the strengths and eliminating the weaknesses.

### [2.4.1 What we learned in this chapter](/book/toy_app#sec-toy_app_what_we_learned_in_this_chapter)

- Scaffolding automatically creates code to model data and interact with it through the web.
- Scaffolding is good for getting started quickly but is bad for understanding.
- Rails uses the Model-View-Controller (MVC) pattern for structuring web applications.
- As interpreted by Rails, the REST architecture includes a standard set of URLs and controller actions for interacting with data models.
- Rails supports data validations to place constraints on the values of data model attributes.
- Rails comes with built-in functions for defining associations between different data models.
- We can interact with Rails applications at the command line using the Rails console.

## [2.5 Exercises](/book/toy_app#sec-toy_app_exercises)

_Note_: The _Solutions Manual for Exercises_, with solutions to every exercise in the _Ruby on Rails Tutorial_ book, is included for free with every purchase at [www.railstutorial.org](http://www.railstutorial.org/).

1. The code in [Listing&nbsp;2.18](/book/toy_app#code-validates_content_presence) shows how to add a validation for the presence of micropost content in order to ensure that microposts can’t be blank. Verify that you get the behavior shown in [Figure&nbsp;2.19](/book/toy_app#fig-micropost_content_cant_be_blank).
2. Update [Listing&nbsp;2.19](/book/toy_app#code-toy_user_presence_valications) by replacing `FILL_IN` with the appropriate code to validate the presence of name and email attributes in the User model ( [Figure&nbsp;2.20](/book/toy_app#fig-user_presence_validations)).

Listing 2.18:Code to validate the presence of micropost content. app/models/micropost.rb

```
class Micropost < ActiveRecord::Base
  belongs_to :user
  validates :content, length: { maximum: 140 },
                      presence: true
end
```

 ![images/figures/micropost_content_cant_be_blank](https://softcover.s3.amazonaws.com/636/ruby_on_rails_tutorial_3rd_edition/images/figures/micropost_content_cant_be_blank.png)

Figure 2.19: The effect of a micropost presence validation.

Listing 2.19:Adding presence validations to the User model. app/models/user.rb

```
class User < ActiveRecord::Base
  has_many :microposts
  validates FILL_IN, presence: true
  validates FILL_IN, presence: true
end
```

 ![images/figures/user_presence_validations](https://softcover.s3.amazonaws.com/636/ruby_on_rails_tutorial_3rd_edition/images/figures/user_presence_validations.png)

Figure 2.20: The effect of presence validations on the User model.

1. The main reason for this is that the default Rails page typically breaks at Heroku, which makes it hard to tell if the deployment was successful or not.&nbsp;↑
2. Because microposts are short by design, the `string` type is actually big enough to contain them, but using `text` better expresses our intent, while also giving us greater flexibility should we ever wish to relax the length constraint.&nbsp;↑
3. The name of the scaffold follows the convention of _models_, which are singular, rather than resources and controllers, which are plural. Thus, we have `User` instead of `Users`.&nbsp;↑
4. The `rails` script is designed so that you don’t need to use `bundle exec`.&nbsp;↑
5. Some references indicate that the view returns the HTML directly to the browser (via a web server such as Apache or Nginx). Regardless of the implementation details, I prefer to think of the controller as a central hub through which all the application’s information flows.&nbsp;↑
6. Fielding, Roy Thomas. _Architectural Styles and the Design of Network-based Software Architectures_. Doctoral dissertation, University of California, Irvine, 2000.&nbsp;↑
7. Earlier versions of Rails used PUT for data updates, but PATCH is the more appropriate method according to the HTTP standard.&nbsp;↑
8. As with the User scaffold, the scaffold generator for microposts follows the singular convention of Rails models; thus, we have `generate Micropost`.&nbsp;↑
9. The scaffold code may have extra newlines compared to [Listing&nbsp;2.8](/book/toy_app#code-demo_microposts_resource). This is not a cause for concern, as Ruby ignores extra newlines.&nbsp;↑
10. Your console prompt might be something like 2.1.1 :001 >, but the examples use&nbsp;>> since Ruby versions will vary.&nbsp;↑
11. As with “Ctrl-C”, the convention is to write “Ctrl-D” even though it’s really “Ctrl-d”.&nbsp;↑


