Play 2.4 with MacWire
=====================

This is an example project of Play 2.4 with Macwire and Slick integration provided by [Play Slick](https://www.playframework.com/documentation/2.4.x/PlaySlick). It is based off of the slick introduction guide: http://slick.typesafe.com/doc/3.0.0/gettingstarted.html#quick-introduction

To run the project, just type 'activator run'

There are three sample endpoints:

* /supplier/all
* /coffee/all 
* /coffee/priced/9


Some important things to point out:

* Play 2.4 has recommended avoiding using [GlobalSettings](https://www.playframework.com/documentation/2.4.x/GlobalSettings) and to rely on the ApplicationLoader. There is a new class called ApplicationLifecycle, which is injected with BuiltInComponents. If you need provide cleanup on application shutdown, you can add a hook - [applicationLifecycle.addStopHook()](https://www.playframework.com/documentation/2.4.x/ScalaDependencyInjection#Stopping/cleaning-up).
* Avoid referencing the Application global state. Don't use play.api.Play.current or try to access 'lazy val application' in your module (the application isn't initialized yet and will cause an error). Configuration and Environment are mixed in from [BuiltInComponents](https://github.com/playframework/playframework/blob/2.4.x/framework/src/play/src/main/scala/play/api/Application.scala#L261) if you need access to them.
* The [Routes](https://www.playframework.com/documentation/2.4.x/ScalaCompileTimeDependencyInjection#Providing-a-router) class in router.Routes is generated by Play on compile, if you are interested in how it works. It accepts your controllers as parameters. wire[Routes] should be sufficient in wiring everything up, as long as you have defined instances of your controllers and all the dependencies. 
* There is a new trait for WS - NingWSComponents. This trait instantiates a wsClient automatically for you. You should mix this trait into your module and inject wsClient into your classes. Avoid using the global WS or WSClient.
* There is a new trait SlickComponents, which will create an instance of SlickApi. [Play Slick allows Slick to control its own connection pool](https://www.playframework.com/documentation/2.4.x/PlaySlickAdvancedTopics) which defaults to HikariCP in Slick 3, so there is no need to provide your own.
* Unit testing: if you don't need access to Play environment variables, you can mix in the modules directly into your tests (providing mocks as necessary). They will run much quicker, as an instance of FakeApplication won't need to be created. If you want to create integration tests and need access to an application (such as for testing endpoints), there is a new play specs2 helper - WithApplicationLoader. It's best to create a test ApplicationLoader with your external dependencies mocked.