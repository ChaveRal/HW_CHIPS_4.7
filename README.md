# WordGuesser on Rails

In a [previous assignment](https://github.com/saasbook/hw-sinatra-saas-wordguesser) you created a simple Web app that plays the Wordguesser game.

More specifically:

1. You wrote the app's code in its own class, `WordGuesserGame`, which knows nothing about being part of a Web app.

2. You used the Sinatra framework to "wrap" the game code by providing a set of RESTful actions that the player can take, with the following routes:

    * `GET  /new`-- default ("home") screen that allows player to start new game
    * `POST /create` -- actually creates the new game
    * `GET  /show` -- show current game status and let player enter a move
    * `POST /guess` -- player submits a letter guess
    * `GET  /win`   -- redirected here when `show` action detects game won
    * `GET  /lose`  -- redirected here when `show` action detects game lost

3. To maintain the state of the game between (stateless) HTTP requests, you stored a copy of the `WordGuesserGame` instance itself in the `session[]` hash provided by Sinatra, which is an abstraction for storing information in cookies passed back and forth between the app and the player's browser.

In this assignment, you'll reuse the *same* game code but "wrap" it in a simple Rails app instead of Sinatra.

## Learning Goals

Understand the differences between how Rails and Sinatra handle various aspects of constructing SaaS, including: 

* how routes are defined and mapped to actions; 
* the directory structure used by each framework;
* how an app is started and stopped; 
* how the app's behavior can be inspected by looking at logs or invoking a debugger. 

## 1. Run the App

**NOTE: You may find these [Rails guides](http://guides.rubyonrails.org/v4.2/) and the [Rails reference documentation](http://api.rubyonrails.org/v4.2.9/) helpful to have on hand.**

Like substantially all Rails apps, you can get this one running by doing these steps:

1. Clone or fork the [repo](https://github.com/saasbook/hw-rails-wordguesser)

2. Change into the app's root directory `hw-rails-wordguesser`

3. Run `bundle install --without production`

4. | Local Development                      	| Codio                                                     	|
    |----------------------------------------	|-----------------------------------------------------------	|
    | Run `rails server` to start the server 	| Run <br>`rails server -b 0.0.0.0`<br> to start the server 	|

### To view your site in Codio
Use the Preview button that says "Project Index" in the top tool bar. Click the drop down and select "Box URL" 

![.guides/img/BoxURLpreview](https://global.codio.com/content/BoxURLpreview.png)

For subsequent previews, you will not need to press the drop down -- your button should now read "Box URL".

**Q1.1.**  What is the goal of running `bundle install`? The goal of running `bundle install` is to set up files project to be ready to unpacked Gemfile

**Q1.2.**  Why is it good practice to specify `--without production` when running  it on your development computer? 1.Reduced Installation Time 2.Avoiding Unnecessary Components 3.Avoiding Potential Conflicts

**Q1.3.** 
(For most Rails apps you'd also have to create and seed the development database, but like the Sinatra app, this app doesn't use a database at all.) 
Play around with the game to convince yourself it works the same as the Sinatra version.

## 2. Where Things Are

Both apps have similar structure: the user triggers an action on a game via an HTTP request; a particular chunk of code is called to "handle" the request as appropriate; the `WordGuesserGame` class logic is called to handle the action; and usually, a view is rendered to show the result.  But the locations of the code corresponding to each of these tasks is slightly different between Sinatra and Rails.

**Q2.1.** Where in the Rails app directory structure is the code corresponding to the `WordGuesserGame` model? WordGuesserGame modle is in directory /app/model/word_guesser_game.rb

**Q2.2.** In what file is the code that most closely corresponds to the  logic in the Sinatra apps' `app.rb` file that handles incoming user actions? word_guesser_game.rb for storage input from user

**Q2.3.** What class contains that code? Class WordGuesserGame

**Q2.4.** From what other class (which is part of the Rails framework) does that class inherit? GameController use class inherit

**Q2.5.** In what directory is the code corresponding to the Sinatra app's views (`new.erb`, `show.erb`, etc.)?  /app/views/game work as new.erb, show.erb etc.

**Q2.6.** The filename suffixes for these views are different in Rails than they were in the Sinatra app.  What information does the rightmost suffix of the filename  (e.g.: in `foobar.abc.xyz`, the suffix `.xyz`) tell you about the file contents?  template engine is used in that file and views in rails mostly extention is .erb, .haml, .slim or .builder etc. That template engines in Rails supports 

**Q2.7.** What information does the  other suffix tell you about what Rails is being asked to do with the file? ไฟล์ที่มีextention เป็น .html.erb, .json.jbuilder, .js.erb นั้นบอกให้ Rails รู้ว่า format หรือ template engine ที่ใช้ในการ render

**Q2.8.** In what file is the information in the Rails app that maps routes (e.g. `GET /new`)  to controller actions? ใน Rails ข้อมูลที่เส้นทางของ Route (GET /new) ไปยัง controller ส่วนใหญ่ประกาศในไฟล์ config/routes.rb 

**Q2.9.** What is the role of the `:as => 'name'` option in the route declarations of `config/routes.rb`?  (Hint: look at the views.) ใช้ในการเรียก path ของ route ในไฟล์ cofig/route.rb ที่สามารถใช้งานได้ใน views และ controllers แทน URL

## 3. Session

Both apps ensure that the current game is loaded from the session before any controller action occurs, and that the (possibly modified) current game is replaced in the session after each action completes.

**Q3.1.** In the Sinatra version, `before do...end` and `after do...end` blocks are used for session management.  What is the closest equivalent in this Rails app, and in what file do we find the code that does it? before_action และ after_action callbacks in app/controllers/application_controller.rb file

**Q3.2.** A popular serialization format for exchanging data between Web apps is [JSON](https://en.wikipedia.org/wiki/JSON).  Why wouldn't it work to use JSON instead of YAML?  (Hint: try replacing `YAML.load()` with `JSON.parse()` and `.to_yaml` with `.to_json` to do this test.  You will have to clear out your cookies associated with `localhost:3000`, or restart your browser with a new Incognito/Private Browsing window, in order to clear out the `session[]`.  Based on the error messages you get when trying to use JSON serialization, you should be able to explain why YAML serialization works in this case but JSON doesn't.) ไฟล์ JSON ไม่สามารถใช้แทนYAMLได้เนื่องจาก JSONไม่รองรับ additional data type

## 4. Views

**Q4.1.** In the Sinatra version, each controller action ends with either `redirect` (which as you can see becomes `redirect_to` in Rails) to redirect the player to another action, or `erb` to render a view.  Why are there no explicit calls corresponding to `erb` in the Rails version? (Hint: Based on the code in the app, can you discern the Convention-over-Configuration rule that is at work here?) ใน Rails version ไม่มีการเรียก erb ที่ชัดเจนใน controller action เพราะว่า Rails ใช้ระบบ convention-over-configuration โดยจะจัดการให้ views นั้นจะอยู่ใน directoryที่ชัดเจนและชื่อก็ควรเหมือนกันกับ controller actions โดยไม่ให้ developer ต้องมาระบุ views ที่จะแสดงผล ในกรณีนี้คือ Convention-over-Configuration ทำให้ developer นั้นสร้าง web ได้เร็วและมีประสิทธิภาพด้วยการเลี่ยงงานที่ซ้ำซาก

**Q4.2.** In the Sinatra version, we directly coded an HTML form using the `<form>` tag, whereas in the Rails version we are using a Rails method `form_tag`, even though it would be perfectly legal to use raw HTML `<form>` tags in Rails.  Can you think of a reason Rails might introduce this "level of indirection"? โดยรวมแล้ว Rails ช่วยในเรื่องของ ความปลอดภัย การดูแลรักษาที่ง่ายขึ้น การใช้งานที่มากหลาย การทำให้มี productivity และรองรับ conventions

**Q4.3.** How are form elements such as text fields and buttons handled in Rails?  (Again, raw HTML would be legal, but what's the motivation behind the way Rails does it?) ใน Rails element และ ปุ่มนั้นถูกจัดการโดย html หลายตัวร่วมกับ Ruby code ซึ่ง Rails framework นั้นมี helper method ที่สร้าง html ที่จำเป็นต่อ form element นั้นๆ ผู้พัฒนาระบุตัวเลือกและattributesต่างๆโดยใช้ Ruby code ซึ่งข้อดีของ rails helper methods นั้นคือการทำform ให้ใช้งานได้กับ Rails conventions

**Q4.4.** In the Sinatra version, the `show`, `win` and `lose` views re-use the code in the `new` view that offers a button for starting a new game. What Rails mechanism allows those views to be re-used in the Rails version? ใน Rails Version ของ App ในส่วน Show win lose views นั้นสามารถนำ Code กลับมาใช้ซ้ำได้ใน view ใหม่ ที่มีปุ่ม เริ่มเกมใหม่โดยใช้ระบบ Rails rendering เรียกว่า "partials" ซึ่ง partial คือ view template ที่สามารถนำกลับมาใช้ใหม่ได้ สามารถ rendered ภายใน views อื่นๆ โดยการใช้ partial, ภายในส่วนของviews นั้นก็จะแบ่งกันใช้ common code 

## 5. Cucumber scenarios

The Cucumber scenarios and step definitions (everything under `features/`, including the support for Webmock in `webmock.rb`) was copied verbatim from the Sinatra version, with one exception: the `features/support/env.rb` file is simpler because the `cucumber-rails` gem automatically does some of the things we had to do explicitly in that file for the Sinatra version.

Verify the Cucumber scenarios run and pass by running `rake cucumber`.

**Q5.1.** What is a qualitative explanation for why the Cucumber scenarios and step definitions didn't need to be modified at all to work equally well with the Sinatra or Rails versions of the app? เนื่องจากCucumber เน้นไปที่การทำtest case แบบ BDD(behavior Drive Development)จากEnd User ทำให้ไม่ต้องการการปรับแต่งในการใช้งานทั้ง Sinitra และ Rails
